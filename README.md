# Синтезатор на двух Si5351 для КВ трансивера с CAT

Готовые сборки и исходники прошивки HF-синтезатора на двух Si5351
(VFO + BFO), 9 КВ-диапазонов, ATmega168.

<p align="center">
  <img src="pics/step-ii.png" alt="Трансивер STEP-II SMD R9OFG с синтезатором на двух Si5351" width="72%">
</p>

<p align="center">
  <img src="pics/Pcb-1.png" alt="Плата синтезатора с установленным LCD1602" width="48%">
  <img src="pics/Pcb-2.png" alt="Плата синтезатора Rev.-2.1, вид сверху" width="48%">
</p>

## Возможности

* Два Si5351: VFO (Si5351-1) и BFO (Si5351-2), выбор микросхемы по шине I2C.
* 9 КВ-диапазонов (160…10 м) со своей частотой, шагом и ATT/PRE.
* Режимы LSB, USB, CWL, CWU; преобразование «классика» или «всегда вверх».
* CAT: Kenwood-совместимый набор команд и готовое описание Rig для OmniRig.
* S-метр: 10-битный АЦП, сглаживание, значок антенны на LCD и 7 кубиков уровней S1/3/5/7/9/9+10/9+20
* Меню FUNC: шаг, четыре BFO, калибровка XTALL-1/2, ток CLK0, вид
  преобразования, смена VFO/BFO при TX, включение/выключение диапазонов.
* Все настройки хранятся в EEPROM и сохраняются при обновлении прошивки.

| Компонент | Версия |
|---|---|
| Прошивка | **3.0.0** |
| Приложение | **3.1.0** |

## Скачать

### Прошивка

| Файл | Что это | Размер |
|---|---|---|
| [Synt_Si5351x2_R9OFG_3.0.0.hex](firmware/Synt_Si5351x2_R9OFG_3.0.0.hex) | прошивка 3.0.0 для ATmega168 | 40 КБ |
| [lfuse.hex](firmware/lfuse.hex) | LFUSE (0xCF) | 1 Б |
| [hfuse.hex](firmware/hfuse.hex) | HFUSE (0xD4) | 1 Б |
| [efuse.hex](firmware/efuse.hex) | EFUSE (0x01) | 1 Б |
| [lock.hex](firmware/lock.hex) | LOCK (0x3F) | 1 Б |

<p align="center">
  <img src="pics/fuses-avrisp-mkii.png" alt="Фьюзы ATmega168 в Atmel Studio 7 для AVRISP mkII" width="48%">
  <img src="pics/fuses-usbasp.png" alt="Фьюзы ATmega168 для USBasp (AVRDUDEPROG)" width="48%">
</p>

### Исходники прошивки

| Файл | Что это | Размер |
|---|---|---|
| [Synt_Si5351x2_R9OFG_3.0.0_src.zip](sources/Synt_Si5351x2_R9OFG_3.0.0_src.zip) | исходники прошивки и проект Atmel Studio 7 (без приложения) | 39 КБ |

### Приложение

| Файл | Что это | Размер |
|---|---|---|
| [CAT-R9OFG-OmniRig-Client.exe](app/CAT-R9OFG-OmniRig-Client.exe) | один exe, все библиотеки и шрифт внутри | 1,1 МБ |
| [CAT-R9OFG-OmniRig-Client_3.1.0.msi](app/CAT-R9OFG-OmniRig-Client_3.1.0.msi) | установщик MSI | 656 КБ |

### Документация

| Файл | Что это | Размер |
|---|---|---|
| [Synt_si5351_avr_5v_2.1.pdf](docs/Synt_si5351_avr_5v_2.1.pdf) | принципиальная схема | 202 КБ |
| [CAT_R9OFG.ini](docs/CAT_R9OFG.ini) | описание Rig для OmniRig | 2 КБ |
| [CHANGELOG_3.0.txt](docs/CHANGELOG_3.0.txt) | список изменений 3.0 | 39 КБ |

## Прошивка

ATmega168, кварц 25 МГц. Фьюзы: **LFUSE 0xCF, HFUSE 0xD4, EFUSE 0x01,
LOCK 0x3F** (файлы `*.hex` лежат рядом с прошивкой).

```
avrdude -c usbasp -p m168 -B 4 -U lfuse:w:0xCF:m -U hfuse:w:0xD4:m ^
  -U efuse:w:0x01:m -U lock:w:0x3F:m ^
  -U flash:w:Synt_Si5351x2_R9OFG_3.0.0.hex:i
```

Скриншоты настроек для AVRISP mkII и USBasp — в основном репозитории (`Prog/`).

**Первое включение после 3.0.** Формат настроек в EEPROM изменился, поэтому
при первом запуске они один раз сбрасываются на заводские. Далее нужно
выставить BFO и откалибровать XTALL-1/XTALL-2 в меню FUNC — подробности в
[CHANGELOG_3.0.txt](docs/CHANGELOG_3.0.txt).

Сборка из исходников: открыть `Synt_si5351.atsln` в Atmel Studio 7,
конфигурация **Release**, Build. После сборки `Tools/MakeRelease.ps1` кладёт
hex в `Release/`.

## Приложение

`CAT-R9OFG-OmniRig-Client` — окно управления синтезатором по CAT из OmniRig:
частота, режимы, ATT/PRE, тональник, менеджер частот, настройки синтезатора.

Требуется только .NET Framework 4.8 (входит в Windows 10/11) и OmniRig 1.1+.
`CAT_R9OFG.ini` копируется в `Rigs` OmniRig
(`C:\Program Files (x86)\Afreet\OmniRig\Rigs`), после чего OmniRig нужно
перезапустить — иначе `CAT-R9OFG` не появится в списке Rig 1/2.

## Состав репозитория

```
firmware/   прошивка (hex) и файлы фьюзов
sources/    архив исходников прошивки
app/        приложение: один exe и установщик MSI
docs/       схема, CAT_R9OFG.ini, changelog
```

## Обновление релиза

1. Собрать прошивку (Release) и приложение.
2. Заменить hex в `firmware/` (имя — `Synt_Si5351x2_R9OFG_<версия>.hex`).
3. Пересобрать `sources/Synt_Si5351x2_R9OFG_<версия>_src.zip` — только
   исходники и проект, без `APP/`, hex и txt.
4. Положить новый exe/MSI в `app/`, обновить версии и таблицы в этом README.
