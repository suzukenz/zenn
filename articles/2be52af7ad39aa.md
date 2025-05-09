---
title: "Google CloudのContainer-Optimized OSでコンテナにディスクをマウントしようとしてハマった"
emoji: "📦"
type: "tech"
topics:
  - "docker"
  - "googlecloud"
published: true
published_at: "2024-07-18 00:11"
---

## 背景

以下のような構成を取ろうとしたら、ディスクのマウントが完了する前にコンテナが起動してしまい意図する動作をしないという問題にぶつかったのでメモ。

- Google Compute EngineでContainer-Optimized OSを利用。
- データを永続化するため追加ディスクを作成し、インスタンスにアタッチ。
- bootcmdを利用してディスクをインスタンスにマウント([参考](https://cloud.google.com/container-optimized-os/docs/concepts/disks-and-filesystem?hl=ja#mounting_and_formatting_disks))、およびコンテナのボリュームとして利用。

## 対応策

インスタンスの設定としてコンテナをデプロイするのではなく、[cloud-init の例](https://cloud.google.com/container-optimized-os/docs/how-to/run-container-instance?hl=ja#using_cloud-init_with) についてのドキュメントを参考に、自身でコンテナをサービスとして起動するように設定する。

```plaintext
#cloud-config

write_files:
- path: /etc/systemd/system/cloudservice.service
  permissions: 0644
  owner: root
  content: |
    [Unit]
    Description=Cloud Service Docker Container
    Wants=gcr-online.target
    After=gcr-online.target

    [Service]
    Environment="HOME=/home/cloudservice"
    ExecStartPre=/usr/bin/docker-credential-gcr configure-docker
    ExecStart=/usr/bin/docker run --rm --name=mycloudservice \
              -v /mnt/disks/service-data:/data \
              CONTAINER_IMAGE_NAME
    ExecStop=/usr/bin/docker stop mycloudservice
    ExecStopPost=/usr/bin/docker rm mycloudservice

runcmd:
  - systemctl daemon-reload
  - systemctl start cloudservice.service

bootcmd:
  - fsck.ext4 -tvy /dev/disk/by-id/google-ATTACHED_DISK_NAME
  - mkdir -p /mnt/disks/service-data
  - mount -t ext4 -o discard,defaults /dev/disk/by-id/google-ATTACHED_DISK_NAME /mnt/disks/service-data
```

こうすると runcmd が bootcmd　より後に動くので、問題を解消できた（多分。。。ディスクが非常に大きかったりすると、きちんとExecStartPreなどでマウントチェックしないとダメかもしれない）