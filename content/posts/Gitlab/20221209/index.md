---
title: "GitlabCIの参考資料とTIPS"
date: 2022-12-09
categories: ["Gitlab"]
tags: ["Gitlab","GitlabCI","CI/CD"]
---

## まず読むべき公式

まずわからなくなったりやり方を知りたければここを読むこと  
[GitLabを使ったCI/CD入門](https://gitlab-docs.creationline.com/ee/ci/introduction/)  
[GitLab CI/CD パイプライン設定リファレンス](https://gitlab-docs.creationline.com/ee/ci/yaml/README.html)  

## 複数Jobにまたがって同じ変数を扱いたい場合

例としてはjob1で生成したDockerImageのタグを後続のJob2でも利用したい場合  
基本的にはJobIDとかJobが終わったら変わってしまうようなIDでなく、パイプラインIDやマージリクエストIDを使う事を推奨  
特にマージリクエストのタイミングとマージのタイミングのパイプラインはIDが異なるので、共通させたいならマージリクエストID等を使用する事が多い  

## Jobをパラレルに実行したいときのはまりどころ

Jobをパラレルに実行したいときは、Jobを同じステージに置くかParallerの設定を追加して多重度を決める。  
このときのはまりどころは二点  

### パラレル実行するときに各JOBの変数の扱い

Jobを同じステージに置く場合はそれぞれのJobに変数や処理を書けば違う処理が走るが、Parallerの場合は同じ処理が多重で動く。  
そういった際はGitlabのプロジェクトの変数をtest_1,test_2のように連番で設定しておき、Jobの中でtest_${CI_NODE_INDEX}みたいな感じで表現することで同じJobの中で変数を変えることができる。  
最近自分はRspecの実行やCloudFormationの更新、Dockerイメージを複数作らないと行けない場合といった、コマンドはほぼ同じだが変数が変わるだけで済む処理みたいな物を並列させている。  

### enviromentで環境情報を設定していると並列はできない  

enviromentというのはGitlab上でデプロイ先の環境を定義するオプションです。  
これをつけているとJobが成功する度にGitのSHA渡韓共鳴とともにデプロイが記録されます。  
[環境とデプロイメント](https://gitlab-docs.creationline.com/ee/ci/environments/#configuring-environments)  

しかしこのオプションをつけていると、並列でJobを動かすことができません。  
理由はGitlabのセーフティとして一つの環境に同時に走らせられるJobは一つだけどいう制限がかかっているためです。  
つまり複数のCloudFormationを更新したいという場合は、並列でDockerImageをBuildしたい！という場合はこのオプションは使用しない用にしなければなりません。  

## その他よく読む記事

[GitLab での 並列 CI パターン](https://qiita.com/utrhira/items/0227a0dde84d193132c2)  
[.gitlab-ci.yml を書くときに知っておくといいことあるかも](https://qiita.com/masakura/items/4ae0a2049608722c1dea)  
