# VelaWear

> 基于 openvela 与 SF32LB52 的低功耗多模态 AI 智能穿戴 Agent 原型

VelaWear 是面向 SF32LB52 黄山派开发板的 AI 穿戴设备原型，围绕传感器事件、Agent 决策、设备动作和手机协同组织代码。本仓库是 2026 openvela AI 硬件产品创新赛道的作品提交仓。

本 README 以 PR #6 已提交的代码和证据为准。本次提交整理只收口说明文档，不新增功能，不继续 PAN/Internet 调试，也不把未闭环能力写成已完成。

## 作品信息

- 赛道：AI 硬件产品创新
- 硬件：SF32LB52 黄山派
- 软件基础：openvela、ai_agent、BLE/GATT、XiaoZhi 传输接口
- 主要代码：**app/velawear_agent/**
- AI Coding 日志：**logs/**
- 验收清单：**docs/contest2026_submission_checklist.md**

## 已实现并有提交证据的内容

- VelaWear Agent 的事件、状态、决策和动作管理链路。
- IMU、音频、显示、BLE/GATT 和触摸相关接口的工程集成。
- Wellness Skill 文件、Agent 协议测试和 XiaoZhi/PAN 传输路径。
- SF32LB52 黄山派固件的构建、烧录、冷启动和部分无线/GATT HIL 记录。
- AI Coding JSONL 日志和 manifest.json 已保留在 logs/，历史日志不在本次整理中改写。

### 已有验证记录

这些是 PR #6 中已有的记录；本次没有重新构建、烧录或宣称新增硬件结果。

| 项目 | 已记录结果 | 证据边界 |
| --- | --- | --- |
| 构建 | Ninja 40/40，退出码 0 | 证明该基线可构建，不等于所有功能真机验收 |
| 烧录与启动 | 1,000,000 bps、地址 0x12010000、完整镜像 --verify 通过并完成冷启动 | 证明基线镜像写入和启动，不等于联网 |
| BLE/GATT | 独立 WinRT 扫描观察到 VelaWear；3 个服务、5 个特征、CCCD 和通知回归通过 | 证明无线/GATT 证据，不等于 PAN/Internet |
| 主机与协议测试 | 30 个 Python 主机测试及 Agent 协议测试通过 | 证明主机/协议回归，不等于 LCD、扬声器或语音的人眼/人耳验收 |
| 退出回归 | network watcher、Cron、Heartbeat 停止，出现 Shutdown complete | 证明退出路径记录，不等于在线 Agent 对话 |

## 尚未闭环的能力

以下项目在提交资料中明确保持未完成或未证明状态：

- Classic PAN/BNEP、DHCP、IP、路由和 Internet 的完整链路。
- 有效网络、真实 LLM/团队服务端凭据和一轮在线基础对话。
- Wellness Skill 的板端实际执行、主动提醒触发以及 LCD/震动/扬声器的可观察物理反馈。
- 语音唤醒词的真机验收。
- 不超过 5 分钟的最终演示视频仍需人工录制；现有脚本位于 docs/submission/velawear_demo_script.md。

因此，本作品的准确定位是：

> 基于 openvela 与 SF32LB52 的低功耗多模态 AI 智能穿戴 Agent 原型，已完成代码集成及部分构建、启动、无线和协议验证；在线 Agent、PAN/Internet 和完整物理交互仍未闭环。

## 目录

~~~text
app/velawear_agent/       VelaWear Agent 源码、驱动、Skill 和协议测试
docs/                     架构、证据、提交说明和验收清单
logs/                     AI Coding JSONL 日志及 manifest
patches/                  黄山派配置补丁
contest2026_329_dela.xml  repo manifest
README.md                 作品说明
~~~

## 编译、烧录与运行

以下命令是 PR #6 记录的复现入口，路径按本地 openvela 工作区调整。

~~~bash
cd /path/to/openvela

cmake -B cmake_out/lckfb_huangshan_pi -S "$PWD/nuttx" -GNinja \
  -DBOARD_CONFIG=../vendor/sifli/boards/sf32lb52/lckfb_huangshan_pi/configs/nsh \
  -DEXTRA_FLAGS="-Wno-cpp -Wno-deprecated-declarations"

ninja -C cmake_out/lckfb_huangshan_pi -j4
~~~

~~~bash
sftool -c SF32LB52 -p /dev/ttyUSB0 -b 1000000 \
  --compat true --verify \
  write_flash cmake_out/lckfb_huangshan_pi/nuttx.bin@0x12010000
~~~

~~~bash
picocom -b 1000000 --noreset --lower-rts --lower-dtr /dev/ttyUSB0
~~~

在 nsh> 下启动：

~~~text
nsh> velawear
~~~

## AI Coding 说明

本项目使用 AI 辅助需求分析、架构设计、代码实现、调试、测试和文档整理。可复核的对话日志保留在 logs/；本次只检查其存在性和提交路径，不修改历史 JSONL 内容，也不虚构未发生的验证。

最终提交边界以代码、日志和证据文件为准；未闭环项目继续保持明确标注，后续如需补齐联网、在线对话或真机物理反馈，应另行验证。
