# Suzuka‘s LAS-16 Boost v1.0.0-preview1

只针对 LAS-16 镰刀的独立强化 mod。预览版。

**不要与 Suzuka‘s LAS Boost 同时启用。** 两者都修改 LAS-16；若同时加载，后加载的一方会检测到另一个并完全不写入（日志 `SuzukasLAS16Boost.log` 中 `STATUS=CONFLICT_OTHER_LAS_PACKAGE_NO_WRITE`）。

## 选项（Mod Options Menu → Suzuka LAS-16 Boost）

| 选项 | 默认 | 内容 |
| --- | --- | --- |
| LAS-16 PROJECTILE BOOST | ON | 弹丸伤害 60/6 → 72/9（普通/耐久）。**会同时影响 LAS-17 双刃镰刀**：两把枪的基础弹丸共用同一条伤害记录（LAS-17 过热阶段的弹丸不变）。 |
| LAS-16 RECOIL BOOST | ON | 水平/垂直后坐力倍率 1 → 0.7 |
| LAS-16 SPREAD BOOST | ON | 初始散布 水平 20 → 1、垂直 20 → 14 |
| LAS-16 CROSSHAIR | TRIANGLE | TRIANGLE 原版激光三角准星；STANDARD 步枪通用准星 |
| LAS-16 COOLING BOOST | ON | 散热 8 → 28 热量/s（×3.5）；其他武器不变 |
| LAS-16 FIRING MODE | PROJECTILE | 实验：TRIDENT BEAM 改为发射原生 LAS-13 三叉戟光束（详见 TRIDENT_EXPERIMENT.md） |
| LAS-16 BEAM COUNT | 6 | 光束模式每次开火 2 / 4 / 6 道 |

固定开启（无菜单项）：普通/开镜摇晃 ×0.7。

生效时机：弹丸伤害与散热即时写入；后坐力、散布、准星可能需要重新装备；射击模式与光束数量只在场上没有 LAS-16 时切换（在飞船里装备前选好，或重启）。退出游戏时恢复原版数值（光束模式若有镰刀存在则保留到进程结束）。

## 安装

关闭游戏，导入 `Suzukas-LAS16-Boost-v1.0.0-preview1.zip` 并部署；先停用 Suzuka‘s LAS Boost 和含 LAS 的 Gun Boost 合包。需要 Bingus Shared Loader v15+ / API 1；菜单需要 Mod Options Menu API 1（无菜单时使用上表默认值）。包内含镰刀装备包与三叉戟光束资源补丁，与其他修改 laser_rifle_long 装备包的 mod 冲突。

仅支持 game.dll SHA256 2e2c3b7c2500646dadd5f2b4c6e0504dbb7e7896139f64cddc0d1813c718f51e；所有写入先核对原值、写后回读。系统调用经 GetProcAddress 绑定，与其他插件声明的 C 原型互不影响。

## 验证

离线（游戏 LuaJIT 独立状态）：test_las16_package.py（完整入口、互斥检测、菜单分类与选项 ID）、test_sickle_projectile.py、test_las16_options.py、test_las_cooling.py、test_trident_backend.py、test_compat.py。**尚未实机验证。**
