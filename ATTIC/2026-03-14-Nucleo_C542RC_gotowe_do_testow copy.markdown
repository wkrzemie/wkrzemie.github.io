---
layout: post
title:  "Nucleo C542RC gotowe do testów"
date:   2026-03-14
categories: STM32
---

Właśnie dotarła do mnie płytka z procesorem z nowej rodziny STM32 zamontowanym na płytce **Nucleo-C542RC**. Poniżej zamieszczam zdjęcie płytki, którą właśnie rozpakowałem.

![Nucleo-C542RC](/assets/images/nucleo_c5.jpg)

Niestety nowa rodzina C5 wymaga nowej wersji **CubeMX2**, na którego odsłonę musimy zaczekać do najbliższego poniedziałku, gdyż w chwili obecnej jest ona jeszcze niedostępna.

### Co drzemie w nowym Nucleo-C542RC?

Mimo że na pierwsze testy kodu musimy chwilę poczekać, specyfikacja nowej jednostki wygląda obiecująco. Oto najważniejsze cechy i nowości:

*   **Rdzeń nowej generacji:** Płytka bazuje na zoptymalizowanym rdzeniu ARM Cortex, oferującym jeszcze lepszy stosunek wydajności do poboru mocy.
*   **Nowe złącze CAN:** Ważną nowością na płytce jest dedykowane złącze dla magistrali **CAN**, co znacznie ułatwia implementację komunikacji przemysłowej i motoryzacyjnej bez konieczności stosowania wielu dodatkowych nakładek.
*   **Zaawansowane zarządzanie energią:** Rodzina C5 wprowadza nowe tryby *Deep Sleep* z błyskawicznym czasem wybudzania, idealne do projektów IoT.
*   **Zintegrowane bezpieczeństwo:** Sprzętowe wsparcie dla kryptografii oraz bezpieczny system aktualizacji firmware'u (**Secure Boot**).
*   **Szybsze peryferia:** Odświeżone kontrolery DMA oraz ulepszone przetworniki ADC i DAC o wyższej rozdzielczości.
*   **Złącze USB-C:** W standardzie Nucleo otrzymujemy w końcu natywne złącze **USB typu C** do programowania i zasilania.

To ewolucja, która stawia na wydajność energetyczną i nowoczesne standardy łączności. Już w przyszłym tygodniu, gdy tylko **CubeMX2** pojawi się na serwerach, wrzucę pierwszy tutorial z uruchomienia projektu.



