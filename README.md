<h1 align=center><code>Anti-Aimbot</code></h1>

<div align=center>
  <img src=https://img.shields.io/badge/Counter--Strike%202-Metamod%202.0%20(KHook)-orange?style=flat-square alt=metamod>
  <a href=https://github.com/Cookies2122/cs2-antiaimbot/commits><img src=https://img.shields.io/github/last-commit/Cookies2122/cs2-antiaimbot?style=flat-square alt="last commit"></a>
  <a href=LICENSE><img src=https://img.shields.io/github/license/Cookies2122/cs2-antiaimbot?style=flat-square alt=license></a>
</div>
<br>

Ищет anti-aim, silent-aim и aimbot по движению прицела вокруг убийства и наказывает. Нагрузка на сервер почти нулевая. Отдельные виды aimbot могут не ловиться.

> Автор плагина: [ABKAM](https://github.com/ABKAM2023). Здесь версия 1.0.2 под новый SDK и Metamod 2.0, оригинал: [ABKAM2023/cs2-antiaimbot](https://github.com/ABKAM2023/cs2-antiaimbot).

## Что нужно

- [Metamod:Source](https://github.com/alliedmodders/metamod-source) 2.0 (KHook)
- [Utils](https://github.com/Pisex/cs2-menus)
- [Admin System](https://github.com/Pisex/cs2-admin_system) (нужен для `ban_action admin`)

## Установка

Соберите плагин (раздел [Сборка](#сборка)) и скопируйте содержимое `build/package` в папку `csgo` на сервере. В пакете лежит:

| Файл | Куда |
| --- | --- |
| `AntiAimbot.so` | `addons/AntiAimbot/` |
| `AntiAimbot.vdf` | `addons/metamod/` |
| `settings.ini` | `addons/configs/AntiAimbot/` |

После этого перезапустите сервер или смените карту.

## Настройки

### settings.ini

`addons/configs/AntiAimbot/settings.ini`

| Ключ | По умолчанию | Что делает |
| --- | --- | --- |
| `ban_enabled` | `1` | 1 наказывать, 0 только писать в лог |
| `ban_action` | `admin` | как наказывать: `admin` бан через Admin System, `disconnect` кик, `command` своя команда, `none` ничего |
| `ban_command` | `css_ban {steamid64} {minutes} {reason_q}` | команда для `ban_action command`, подстановки `{steamid64}`, `{userid}`, `{slot}`, `{minutes}`, `{reason}`, `{reason_q}` (причина в кавычках) |
| `ban_time_minutes` | `0` | время бана в минутах, 0 навсегда |
| `reason` | `Использование Aimbot` | причина бана |
| `exclude_warmup` | `0` | не проверять в разминку, 1 да |
| `debug` | `1` | подробный лог в консоль, 1 включить |
| `threshold` | `24.0` | сколько очков подозрения нужно для наказания |

Остальные ключи это тонкая настройка детекта: пороги углов, веса сигналов и окна анализа. У каждого есть пояснение в конфиге, без понимания их лучше не трогать.

<details>
<summary>Весь файл settings.ini</summary>

```
"AntiAimbot"
{
    "debug"                       "1"      // логи в консоль (0/1)
    "sample_interval"             "0.03"   // период записи прицела, сек
    "analysis_window_s"           "0.60"   // окно анализа вокруг килла, сек

    "fov_lock_deg"                "4.0"    // FOV «в цель», °
    "lock_var_deg"                "0.50"   // доп. дрожь в lock, °/шаг
    "lock_hold_s"                 "0.25"   // мин. длительность lock, сек

    "snap_delta_deg"              "12.0"   // порог флика, °
    "pre_snap_min_fov"            "10.0"   // мин. FOV до флика, °

    "silent_fov_min"              "20.0"   // FOV до жертвы для silent, °
    "silent_weight"               "18.0"   // вес silent-сигнала
    "max_shot_age"                "0.35"   // макс. время с выстрела до килла, сек
    "head_bonus"                  "2.0"    // множитель за хедшот (silent)

    "lock_weight"                 "8.0"   // вес lock-сигнала
    "snap_weight"                 "10.0"   // вес snap-сигнала
    "threshold"                   "24.0"   // порог бана (очки)

    "close_range_m"               "3.0"    // ближняя дистанция, м
    "close_range_mult"            "0.50"   // ослабление очков на ближней
    "decay_per_second"            "1.0"    // спад подозрения, очк/сек

    "approach_window_s"           "0.40"   // окно «человеч. подвода», сек
    "approach_need_frac"          "0.60"   // доля времени с уменьшением FOV
    "approach_min_time"           "0.12"   // мин. активного времени, сек
    "approach_bonus"              "8.0"    // сколько снять при естеств. подводе, очков

    "continuity_window_s"         "0.50"   // окно «флик→лок», сек
    "continuity_weight"           "6.0"    // вес непрерывности
    "continuity_max_spike_deg"    "9.0"    // порог разового всплеска Δ, °
    "continuity_min_avg_delta"    "0.20"   // верхний предел средней Δ (≤ °)

    "buf_hard_cap"                "9000"   // кап буфера сэмплов, шт
    "buf_decimate_factor"         "2"      // прореживание старой половины

    "crouch_eyez_delta_min"       "14.0"   // мин. Δ по eyeZ для приседа/спуска, юниты
    "vertical_dom_ratio"          "0.75"   // доля вертикальных движений для «верт. доминанты»
    "snap_near_boost_per_m"       "8.0"    // +° к порогу снапа за метр до close_range_m
    "lock_close_mult"             "0.50"   // ослабление lock на ближней
    "snap_vertical_boost_per_deg" "0.5"    // +° к порогу снапа за градус вертик. угла
    "headshot_elev_no_bonus_deg"  "15.0"   // ≥ этого угла хедшот без бонуса

    "lock_acq_lookback_s"         "0.25"   // сколько смотреть назад перед входом, сек
    "lock_acq_min_delta_deg"      "2.5"    // мин. сумм./пиковое Δ до входа, °
    "lock_enter_fov_margin_deg"   "1.0"    // запас к fov_lock для «входа из-вне», °
    "lock_angle_hold_reduce"      "0.15"   // срез веса lock при angle-hold, ×
    "angle_hold_prefov_deg"       "5.0"    // если prevFOV ≤ … считаем «держал угол», °
    "angle_hold_maxdelta_deg"     "2.5"    // и preMaxΔ ≤ … - обнуляем lock, °

    "exposure_window_s"           "0.25"   // окно анализа до килла, сек
    "exposure_cone_deg"           "15.0"   // жертва «в прицеле», если FOV ≤ 15°
    "exposure_need_s"             "0.10"   // нужно ≥100 мс экспозиции до наказаний
    "exposure_soft_scale"         "0.50"   // при нехватке экспозиции: lock ×0.5
    "far_range_m"                 "12.0"   // на дистанции ≥12 м гейт мягче

    "ban_enabled"       "1"                // 0 - оповещать/логировать без наказания
    "ban_action"        "admin"            // admin | disconnect | command | none
    "ban_command"       "css_ban {steamid64} {minutes} {reason_q}" // ban_action = command, чтобы отправлял команду

    "exclude_warmup"              "0"      // игнорировать разминку (0/1)
    "ban_time_minutes"            "0"      // время бана (0=перма)
    "reason"                      "Использование Aimbot" // причина бана
}
```

</details>

## Что изменено

По сравнению с оригиналом:

- Переведено на новый SDK ([Wend4r/sourcesdk](https://github.com/Wend4r/sourcesdk)), старый после обновления CS2 от 12.11.2026 уже не подходит.
- Работает только на Metamod 2.0 (KHook), старый Metamod на SourceHook больше не поддерживается.
- Заголовки Utils обновлены под текущую версию, имена интерфейсов теперь `UTILS_INTERFACE` и `MENUS_INTERFACE`.
- Своя функция `GameEntitySystem()` убрана, в новом SDK entity system берётся через `IGameResourceService`.
- SchemaEntity обновлена: актуальные оффсеты виртуальных функций.
- При выгрузке плагин проверяет, что Utils ещё загружен. Раньше `meta unload` после выгрузки Utils мог уронить сервер.
- `ban_time_minutes` теперь переводится в секунды перед баном через Admin System. Раньше 60 минут превращались в бан на 60 секунд.

## Сборка

Собирается на Linux. Нужны clang, python3, cmake и [AMBuild](https://github.com/alliedmodders/ambuild). Все команды ниже выполняются из одной папки.

**1. SDK.** Нужен [sourcesdk](https://github.com/Wend4r/sourcesdk) не старее обновления CS2 от 12.11.2026. Собирается один раз:

```bash
git clone --recursive https://github.com/Wend4r/sourcesdk hl2sdk-cs2
cmake -S hl2sdk-cs2 -B hl2sdk-cs2/build -DCMAKE_BUILD_TYPE=Release -DCMAKE_C_COMPILER=clang -DCMAKE_CXX_COMPILER=clang++ -DSOURCESDK_AM_DEFINES=ON -DSOURCESDK_USE_MINIMAL_PROTOS=OFF
cmake --build hl2sdk-cs2/build -j$(nproc)
export HL2SDKCS2=$PWD/hl2sdk-cs2
```

**2. Metamod.** Нужен Metamod 2.0 с KHook, ветка `master`. Со старым Metamod на SourceHook сборка остановится с ошибкой.

```bash
git clone --recursive https://github.com/alliedmodders/metamod-source
```

**3. Плагин.** Папка SchemaEntity должна лежать рядом с папкой плагина.

```bash
git clone https://github.com/{o}/SchemaEntity
git clone https://github.com/Cookies2122/cs2-antiaimbot
cd cs2-antiaimbot
mkdir build && cd build
python3 ../configure.py --enable-optimize --sdks=cs2 --targets=x86_64 --mms_path=$PWD/../../metamod-source --hl2sdk-manifests=../hl2sdk-manifests
ambuild
```

Готовые файлы появятся в `build/package`.

## Автор

Плагин написал [ABKAM](https://github.com/ABKAM2023), оригинал: [ABKAM2023/cs2-antiaimbot](https://github.com/ABKAM2023/cs2-antiaimbot).
Перенос на новый SDK и Metamod 2.0, исправления: \_ded\_cookies.

## Лицензия

GPL-3.0, текст в файле [LICENSE](LICENSE). В коде плагина лицензия тоже указана как GPL. Авторские права на оригинальный код у ABKAM.
