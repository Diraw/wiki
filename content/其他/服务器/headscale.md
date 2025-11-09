创建新用户：

```bash
docker exec -it headscale headscale users create <用户名>
```

查看所有用户：

```shell
docker exec -it headscale headscale users list
```

为用户生成预认证密钥：

```bash
docker exec -it headscale headscale preauthkeys create --user <user_id> [--reusable] [--ephemeral] [--expiration <duration>] [--tags <tag1,tag2,...>]
```

- `--reusable`：可以使用同一个密钥注册多个设备
- `--expiration`：不添加就创建一个永不过期的新密钥
	- `--expiration 30d`：30天之后过期
- `--ephemeral`：临时密钥。当临时设备离线一段时间后（默认是 24 小时，但 Headscale 的配置可以修改这个时间），Headscale 会自动将其从网络中删除
	-  如果不使用 `--ephemeral`，设备将被视为永久设备，即使预认证密钥过期了，设备也还是会在组网里面

查看预认证密钥和过期时间：

```shell
docker exec -it headscale headscale preauthkeys list --user <user_id>
```

客户端认证：

```bash
tailscale up --login-server=<headscale地址> --authkey=<你的预认证密钥>
```

查看所有设备：

```shell
docker exec -it headscale headscale nodes list
```

```shell
docker exec -it headscale headscale nodes list --output json
```

修改设备tag：

```shell
docker exec -it headscale headscale nodes tag --identifier 1 --tags tag:test
```

