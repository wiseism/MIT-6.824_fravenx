# 修复记录：Go CI 脚本权限与缺参数失败（2026-11-08）

## 失败信息
- 仓库：wiseism/MIT-6.824_fravenx
- Workflow：Go
- 失败 run：[42426858006](https://github.com/wiseism/MIT-6.824_fravenx/actions/runs/42426858006)（2026-11-07 exit 1）、[41939638110](https://github.com/wiseism/MIT-6.824_fravenx/actions/runs/41939638110)（2026-11-05 exit 126）

## 错误日志摘录
```
# 41939638110 (exit 126)
./test-mr-many.sh: Permission denied
# 42426858006 (exit 1)
Usage: ./test-mr-many.sh numTrials
##[error]Process completed with exit code 1.
```

## 原因分析
1. `src/main/test-mr-many.sh` 缺乏可执行位，早期 run 以 `Permission denied`（126）失败；
2. workflow 直接 `./test-mr-many.sh` 不传参赛，脚本对参数校验 `$# -ne 1` 打印 Usage 后 `exit 1`。

## 修复内容
1. `src/main/test-mr-many.sh`：git mode +x（`chmod +x` + `update-index --chmod=+x`）。
2. `.github/workflows/go.yml`：`./test-mr-many.sh 1`（CI 跑 1 次 trial）。

## 验证
| run id | 结果 |
| --- | --- |
| 42442516777 (CI) / 42442516778 (Go) | ✅ success |
