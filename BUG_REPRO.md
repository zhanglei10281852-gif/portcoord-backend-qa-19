# Bug Reproduction

## 包的性质

当前 test_model_fix 保存的是被测模型修复后的结果源码，不是初始含 Bug 源码。要复现原始缺陷，必须检出下面固定的 parent SHA；不要在当前修复结果源码上期待重新出现修复前失败。生成系统使用的可信验证补丁和完整验证日志仅在本地留存，不提交到结果分支。

## 问题现象

批量分配两个泊位窗口时接口只返回并持久化第一个窗口。请修复批处理过程中结果集合被截断的问题。

## 含 Bug 版本

- 仓库：zhanglei10281852-gif/portcoord-backend-qa-19
- 仓库地址：https://github.com/zhanglei10281852-gif/portcoord-backend-qa-19.git
- parent SHA：1b5284604a82e30e9a5992d3c31f2d3264f4653b

## 复现步骤

```bash
git clone -- https://github.com/zhanglei10281852-gif/portcoord-backend-qa-19.git bug-repro
cd bug-repro
git checkout --detach 1b5284604a82e30e9a5992d3c31f2d3264f4653b
go test ./internal/berthing -run "^TestBerthing_BatchAllocate$" -count=1
```

## 双架构完整错误信息

### linux/amd64

- 容器内复现预期退出码：1
- 容器内复现实际退出码：1

stdout：

```text
$ go test ./internal/berthing -run "^TestBerthing_BatchAllocate$" -count=1
--- FAIL: TestBerthing_BatchAllocate (0.02s)
    berthing_test.go:91: expected 2 windows, got 1
FAIL
FAIL	portcoord/internal/berthing	0.028s
FAIL

```

stderr：

```text
warning: internal/berthing/berthing_test.go has type 100755, expected 100644
warning: internal/berthing/berthing_test.go has type 100755, expected 100644

```

### linux/arm64

- 容器内复现预期退出码：1
- 容器内复现实际退出码：1

stdout：

```text
$ go test ./internal/berthing -run "^TestBerthing_BatchAllocate$" -count=1
--- FAIL: TestBerthing_BatchAllocate (0.32s)
    berthing_test.go:91: expected 2 windows, got 1
FAIL
FAIL	portcoord/internal/berthing	0.575s
FAIL

```

stderr：

```text
warning: internal/berthing/berthing_test.go has type 100755, expected 100644
warning: internal/berthing/berthing_test.go has type 100755, expected 100644

```

## 通过条件

在题面触发条件下，公开行为必须恢复且原始异常不再出现；定向命令 go test ./internal/berthing -run ^TestBerthing_BatchAllocate$ -count=1、相关包测试、全量测试、race、vet 和 build 必须通过；不得删除或跳过测试，也不得绕过目标逻辑。
