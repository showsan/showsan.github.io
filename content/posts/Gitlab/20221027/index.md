---
title: "AWS上でGitlabRunnerを作った話(とりあえずRunnerだけ)"
date: 2022-10-27
categories: ["Gitlab"]
tags: ["AWS","Gitlab","CI/CD","GitlabRunner","Fargate","Nat"]
menu: main
---

## ざっと経緯説明

GitlabとGitlabRunnerをAWSで組むことになった。  
今回はアカウントAにGitlabを乗せ、アカウントBにGitlabRunnerを乗せる事になったのでその備忘録  

## 最終的な構成図

![Gitlab](gitlab.drawio.svg)  

ちょっとわかりづらいかもしれないんですが、大まかな構成はこんな感じ  
Gitlabの方の解説は後日やるので、一旦Runnerについて書いておきます。  

## Runnerの説明

### 前提条件

* GitlabにはセキュリティグループでIP制限をかけている。

### なぜPublicSubnet二つ？

そもそもこのGitlabRunnerはRunnerでJobを受けるとFargateSpotを起動してjobを処理するという物になります。  
構築に関しては下記公式を参照しています。  
[Autoscaling GitLab CI on AWS Fargate](https://docs.gitlab.com/runner/configuration/runner_autoscale_aws_fargate/)  
この構成でJobを処理する際、EC2のGitlabRunnerからGitlabにアクセスする際はElasticIPを割り当てれば問題なく通信できます。  
しかし、Jobを処理するcoordinatorはFargateSPOTで起動するのでElasticIPを割り当てるにはNatが必須になります。  
このときNatGatewayでももちろんいいのですが、いかんせん料金が高い為NatInstanceを利用しています。  
このNatインスタンスもIPを固定しなければならない通信先のみをルーティングし、それ以外はFargate自身で通信をさせているのでPublicSubnetは二つです。  

### 当たり前だけどNATに通したいルーティングを変えるときは

PublicSubnetBのルーティングテーブルに特定IPだけをNatInstanceに向けるようにルーティングする  

## まとめ

IP制限とかかかっていないGitlabにRunnerを単純に使うならこんなの要らないんですけど、自分で立てているGitlabとかに繋ぐときは一工夫必要と。  
今度具体的にこの辺のRunnerを立てたときのレシピとか、これにCodeBuildを連携させたりっていうのも記事にできればなと思います。  
