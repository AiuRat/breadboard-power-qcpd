<p align="center">
  <b>Русский</b> | <a href="README.en.md">English</a> | <a href="README.de.md">Deutsch</a>
</p>

# Разработка аппаратной части (Electronics)

Здесь содержатся все материалы, связанные с проектированием, расчетом и трассировкой печатной платы модуля комбинированного питания.

## Структура директории

* **[`easyeda`](/electronics/easyeda)** — исходные файлы проекта в формате `.json` для импорта в САПР EasyEDA (принципиальная схема и топология печатной платы).
* **[`verification`](/electronics/verification)** — папка с подробными инженерными расчетами обвязки микросхем, формулами и осциллограммами физических испытаний готового макета.
* **[`images`](/electronics/images)** — графические материалы проекта (скриншоты схемы, слоев трассировки и 3D-модели платы).

---

## Принципиальная схема устройства

Полная принципиальная электрическая схема модуля, выполненная в EasyEDA:

<p align="center">
  <img src="/electronics/images/hv-test-stand-schematic.png" width="1000" alt="Принципиальная схема устройства">
</p>

---

## Топология печатной платы (PCB)

Двухслойная печатная плата с оптимизированной разводкой силовых дорожек и полигонов заземления (без отображения компонентов):

<p align="center">
  <img src="/electronics/images/hv-test-stand-pcb(1).png" width="380" alt="Топология платы - вид сверху">
  &nbsp;&nbsp;&nbsp;&nbsp;
  <img src="/electronics/images/hv-test-stand-pcb(2).png" width="380" alt="Топология платы - вид снизу">
</p>

---

## Трехмерная визуализация (3D Render)

Реалистичные 3D-рендеры печатной платы с установленной элементной базой:

<p align="center">
  <img src="/electronics/images/hv-test-stand-pcb(3).png" width="380" alt="3D модель платы - ракурс 1">
  &nbsp;&nbsp;&nbsp;&nbsp;
  <img src="/electronics/images/hv-test-stand-pcb(4).png" width="380" alt="3D модель платы - ракурс 2">
</p>
