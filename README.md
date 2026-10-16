# Suzuka‘s LAS-16 Boost v1.0.1

只针对 LAS-16 镰刀的独立强化 mod：弹丸、操控、准星、散热，以及可切换的三叉戟光束射击模式。所有选项都在 Mod Options Menu 中设置。

**不要与 Suzuka‘s LAS Boost 同时启用。** 两者都修改 LAS-16；同时加载时，后加载的一方会检测到另一个并完全不写入（日志 `SuzukasLAS16Boost.log` 中 `STATUS=CONFLICT_OTHER_LAS_PACKAGE_NO_WRITE`）。

## 选项（Mod Options Menu → Suzuka LAS-16 Boost）

| 选项 | 默认 | 内容 |
| --- | --- | --- |
| LAS-16 PROJECTILE BOOST | ON | 弹丸伤害 60/6 → 72/18（普通/耐久）。**会同时影响 LAS-17 双刃镰刀**：两把枪的基础弹丸共用同一条伤害记录（LAS-17 过热阶段的弹丸不变）。 |
| LAS-16 PROJECTILE AP +1 | ON | 弹丸穿甲 +1：AP 2/2/2/0 → 3/3/3/0（轻甲 → 中甲）。**同样会影响 LAS-17 双刃镰刀**的基础弹丸（LAS-17 过热阶段的弹丸不变） |
| LAS-16 RECOIL BOOST | ON | 水平/垂直后坐力倍率 1 → 0.7 |
| LAS-16 SPREAD BOOST | ON | 初始散布 水平 20 → 1、垂直 20 → 14 |
| LAS-16 CROSSHAIR | TRIANGLE | TRIANGLE 原版激光三角准星；STANDARD 步枪通用准星 |
| LAS-16 COOLING BOOST | 28/s | 三档：OFF 原版 8 热量/s、28/s、50/s；其他武器不变 |
| LAS-16 FIRING MODE | PROJECTILE | TRIDENT BEAM：镰刀改为发射原生 LAS-13 三叉戟光束，不再发射射弹 |
| LAS-16 BEAM COUNT | 6 | 光束模式每次开火 2 / 4 / 6 道 |
| LAS-16 BEAM MEDIUM PEN | OFF | 光束穿甲 AP 2（轻甲）→ 3（中甲），伤害 60/6 不变。**会同时影响 LAS-13 三叉戟本体**：光束伤害记录由两者共用 |

固定开启（无菜单项）：普通/开镜摇晃 ×0.7。

## 三叉戟光束模式

- 镰刀的射弹组件换成三叉戟的原生光束组件，持续射击、伤害和特效都由游戏自己的光束系统处理；镰刀模型、热量与换散热器流程不变。两种射击方式互斥。
- 每次开火的光束数 = 发射点数 × 2。三叉戟有 3 个发射点；镰刀只有 1 个枪口，4 道/6 道时把枪口复用为 2/3 个发射点并同时开火。
- 光束模式每次开火 +4 热量（射弹为每发 1.15）。按每秒开火 5 次，原版散热约 8 秒过热；散热 28/s 及以上时不会过热。
- **切换射击模式与光束数量只在场上没有 LAS-16 时生效**：在飞船里装备镰刀之前选好，或者选好后重启游戏。若已有镰刀存在，日志 `SuzukasLASFiringMode.log` 显示 `PENDING_LAS16_EXISTS`，镰刀消失后自动生效。
- 不支持局内切换。退出游戏时若光束模式的镰刀仍在场上，这项修改保留到进程结束（引擎清理时组件保持一致）；下次启动从原版开始。

## 生效时机

弹丸伤害与穿甲、散热、光束穿甲即时写入；后坐力、散布、准星可能需要重新装备。退出游戏时所有数值恢复原版。

## 安装

关闭游戏，导入 `Suzukas-LAS16-Boost-v1.0.1.zip` 并部署（同 GUID 替换各个 preview 版）；先停用 Suzuka‘s LAS Boost 和含 LAS 的 Gun Boost 合包。

需要 Bingus Shared Loader v15+ / API 1；菜单需要 Mod Options Menu API 1（无菜单时使用上表默认值）。

包内含镰刀装备包与只含 6 项三叉戟光束资源（两个粒子、两个材质、三叉戟音频库）的补丁，不复制任何原版镰刀/三叉戟模型、瞄具或贴图，因此与改镰刀枪口烟雾、瞄具等的 mod 没有资源重叠；仍与其他替换 laser_rifle_long 装备包本身的 mod 冲突。

## 兼容性与性能

- 仅支持 game.dll SHA256 `2e2c3b7c2500646dadd5f2b4c6e0504dbb7e7896139f64cddc0d1813c718f51e`。游戏更新后会拒绝写入而不是乱写。
- 所有系统调用经 GetProcAddress 绑定为本 mod 自己的函数签名，不受其他插件先声明的 C 原型影响；入口不创建任何多余的全局变量，只读取 Lua 标准库。
- 加载：game.dll 按 PE 头指纹识别，不对 15 MB 文件做 SHA-256；与其他 Suzuka 插件共享校验结果。数据表通过已知地址直接定位，不扫描整个游戏镜像。
- 运行：每帧不分配内存、不读内存；各组件的周期检查错开在不同帧，设置稳定后只读菜单（纯 Lua）。读取只用 ReadProcessMemory。
- 所有写入先核对原值、写后回读；数据与预期不符时拒绝写入并在日志注明原因。

## 日志

`%LOCALAPPDATA%\CowboyBingus\Helldivers2\Logs\`：`SuzukasLAS16Boost.log`（加载耗时 LOAD_MS、识别方式 IDENTITY；退出时 FRAMES/RPM/VQ 读取统计）、`SuzukasLAS16Projectile.log`、`SuzukasLAS16Options.log`、`SuzukasLASCooling.log`、`SuzukasLASFiringMode.log`、`SuzukasBeamPenetration.log`。

## 验证

## 更新记录

- v1.0.1：弹丸强化耐久伤害 9 → 18；新增 `LAS-16 PROJECTILE AP +1`（弹丸穿甲 +1，同时影响 LAS-17）。
- v1.0.0：首个正式版。

## 验证（续）

实机 v1.0.0（2026-10-15）：加载 2 ms（IDENTITY=CACHE），整局 26783 帧共 145 次内存读取、17 次区域查询；无冲突无错误。

实机（v1.0.0-preview6，2026-10-15）：全部组件加载、无冲突无错误；光束模式（4 道）、50/s 散热、后坐力/散布/准星、弹丸强化与光束中穿（伤害记录 508）均生效，退出时正确恢复。

离线（游戏 LuaJIT 独立状态）：test_las16_package.py（完整入口、互斥检测、无多余全局变量、日志统计）、test_sickle_projectile.py、test_las16_options.py、test_las_cooling.py、test_beam_penetration.py、test_trident_backend.py、test_compat.py（冲突原型）、test_trident_beam_assets.py（6 项资源与原版逐字节一致）、check_globals.py（字节码中无全局写入）。
