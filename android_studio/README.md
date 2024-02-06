## Andriod Studio 実行用

- fonts  
  Android Studio で利用するフォント等いれておく
- keys  
  ssh 用の鍵
- resources  
  日本語化プラグインなど、使用したいファイルを入れておく

<br/>

- イメージビルド

```
docker build ./ -t android_studio:230101
```

- コンテナ作成

```
docker container create --name android_studio_230101 -it --mount src=workspaces,dst=/tmp/workspaces -p 50122:22 android_studio:230101
```
