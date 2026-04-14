---
layout: post
title:  "Nucleo C542RC Pierwszy test - ustawiamy TIM1 oraz migamy LED1"
date:   2026-04-12
categories: STM32
---

W tym artykule przyjrzymy się nowemu ekosystemowi **HAL2** od STMicroelectronics. Pokażę Ci, jak skonfigurować Timer w taki sposób, aby w regularnych odstępach czasu generował przerwanie. Wykorzystamy je do cyklicznej zmiany stanu wyjścia, do którego podłączona jest dioda LED.

## Sprzęt i środowisko

Ze względu na to, że biblioteka **HAL2** została zaprojektowana z myślą o najnowszych rodzinach procesorów, w tym projekcie wykorzystamy płytkę **Nucleo-C542RC**.

![Widok płytki Nucleo-C542RC](/assets/images/wpis_01/nucleo_c5.jpg)
*Rys 1. Płytka Nucleo-C542RC wykorzystana w projekcie.*

Do pracy wykorzystamy zestaw narzędzi rekomendowany przez ST:
*   **Visual Studio Code** z zainstalowaną wtyczką „STM32CubeIDE for VS Code”.
![Wtyczka STM32CubeIDE for VSCode](/assets/images/wpis_01/wtyczka_STM32CubeIDE.png)
*   **STM32CubeMX2** – serce konfiguracji, które możesz uruchomić bezpośrednio z poziomu VS Code (opcja *Launch STM32CubeMX2*).
*   **Dokumentacja**: Warto mieć pod ręką Reference Manual o oznaczeniu **RM0522**.

## Analiza schematu: Dioda LED

Zanim przejdziemy do kodu, spójrzmy na schemat elektryczny (*MB2213-C542RC-B02*). Dioda LED jest podłączona do pinu **PA5**.

![Schemat podłączenia diody LED](/assets/images/wpis_01/schemat_LD1.png)
*Rys 2. Wycinek schematu przedstawiający sterowanie diodą przez MOSFET.*

Układ działa w następujący sposób:
1. Pin **PA5** steruje bramką tranzystora MOSFET.
2. Stan **niski** na bramce: kanał tranzystora jest zamknięty, prąd nie płynie – dioda nie świeci.
3. Stan **wysoki (3.3V)**: napięcie bramka-źródło przekracza próg włączenia, tranzystor przewodzi, a dioda LED zaczyna świecić.

## Konfiguracja w STM32CubeMX2

Uruchamiamy CubeMX2 z zakładki ST w VS Code.
![Opcja Launch dostępna bezpośrednio w oknie VSCode](/assets/images/wpis_01/vscode_launch cubeMX2.png)

W sekcji *Home* wybieramy *Board*, wyszukujemy nasze Nucleo i tworzymy projekt, pamiętając o zaznaczeniu opcji „Automatically Download, Install & Create Project”.
![Konfiguracja Projektu](/assets/images/wpis_01/cubeMX2 Board.png)

### 1. Konfiguracja GPIO
Ustawiamy pin **PA5** jako **Output** i nadajemy mu etykietę `LED1`. Pamiętaj, aby zmienić status pinu z *Reserved* na *Configured*. Ciekawostką w nowym CubeMX2 jest funkcja **Code Preview**, która pozwala podejrzeć generowany plik `mx_gpio_default.c` jeszcze przed pełnym eksportem projektu.
![Nadanie etykiety dla PA5](/assets/images/wpis_01/label_led1.png)

### 2. Konfiguracja Timera (TIM1)
Zgodnie z Reference Manual, **TIM1** znajduje się na szynie **APB2** (taktowanej domyślnie 144 MHz). 

*   W zakładce *Peripherals* aktywujemy **TIM1**.
![Aktywacja TIM1](/assets/images/wpis_01/TIM1_activate.png)
*   **Prescaler**: 1439 (dla wartości Integer).
*   **Counter Period**: 65535 (dla wartości Integer).
*   Taka konfiguracja pozwoli nam uzyskać taktowanie o częstotliwości ok. **1 Hz**.

![Konfiguracja TIM1 w CubeMX2](/assets/images/wpis_01/parametry_TIM1.png)
*Rys 3. Parametry pracy Timera 1.*

**Ważne:** W sekcji `System -> NVIC` musimy zaznaczyć **Update interrupt** oraz **Interrupt handler generation**. Dzięki temu procesor wywoła odpowiednią funkcję za każdym razem, gdy licznik osiągnie zadaną wartość.
![Aktywacja obsługi przerwań](/assets/images/wpis_01/enable_Nvic.png)

## Generowanie projektu i kod

Zanim klikniesz *Generate Code*, upewnij się, że w *Project Settings -> General Setup* wybrano:
*   **Format**: CMake
*   **Toolchain**: GCC dla CMake
![Toolchain](/assets/images/wpis_01/toolchain.png)

### Uruchomienie Timera w `main.c`
W pliku `main.c` musimy wystartować nasz timer w trybie przerwaniami:

```c
/* Start Timer z obsługą przerwań */
HAL_TIM_Base_Start_IT(&htim1);
