## Andriod Studio 実行用

- イメージビルド

```
docker build ./ -t android_studio:230101
```

- コンテナ作成

```
docker container create --name android_studio_230101 -it --mount src=workspaces,dst=/tmp/workspaces -p 50122:22 android_studio:230101
```
