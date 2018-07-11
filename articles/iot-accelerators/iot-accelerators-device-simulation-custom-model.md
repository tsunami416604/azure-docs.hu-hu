---
title: Konfigurálja az egyéni modell – Azure |} A Microsoft Docs
description: Ez a cikk ismerteti egy egyéni eszköz modellje konfigurálása az Eszközszimuláció megoldásgyorsító a.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 07/06/2018
ms.topic: conceptual
ms.openlocfilehash: 61a7c5314df541b4b44a286efe982f4bf93256e3
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37967529"
---
# <a name="device-models"></a>Eszközmodell

Amikor konfigurál egy szimuláció, ha szeretné használni egy meglévő eszközt modell az előre definiált érzékelők beállítása, vagy hozzon létre egy egyéni eszköz modellje az Ön által választott szimulált érzékelők. Egyéni érzékelőkkel lehetővé teszik több szorosan modellezheti a valódi eszközön.

## <a name="pre-configured-device-models"></a>Előre konfigurált eszközök

Az Eszközszimuláció megoldásgyorsító három előre konfigurált eszköz modellt kínál: hőmérsékletű Elevator és látható.

Előre konfigurált eszközök egy JavaScript-fájlban definiált speciális viselkedése többféle rendelkezik. A webes felhasználói felület ezen viselkedés nem lehet konfigurálni.

Az alábbi táblázat az egyes előre konfigurált eszközmodell konfigurációk:

| Eszközmodell  | Érzékelő           | Unit (Egység)  |
| ------------- | ---------------- | ----- |
| Hűtő       | páratartalom         | %     |
|               | pressure         | psig  |
|               | hőmérséklet      | F     |
| Vezérigazgatója      | Emelet            |       |
|               | Rezgés        | mm    |
|               | Hőmérséklet      | F     |
| Teherautó         | Szélesség         |       |
|               | Hosszúság        |       |
|               | sebesség            | mph   |
|               | cargotemperature | F     |

## <a name="custom-device-models"></a>Egyéni modellek

Egyéni modellek lehetővé teszik, amelyek jobban a saját eszközök érzékelők modell. Egyéni eszköz legfeljebb 10 egyéni érzékelőkkel rendelkezhet.

Amikor kiválasztja az egyéni modell eszköztípus, kattintva adjon hozzá új érzékelők **+ Hozzáadás érzékelő**.

[![Érzékelő konfigurálása](./media/iot-accelerators-device-simulation-custom-model/configuresensors-inline.png)](./media/iot-accelerators-device-simulation-custom-model/configuresensors-expanded.png#lightbox)

Egyéni érzékelőkkel a következő tulajdonságokkal rendelkeznek:

| Mező     | Leírás |
| --------- | ----------- |
| Érzékelő neve | Egy rövid nevet, mint például a érzékelő **hőmérséklet** vagy **sebesség**.  |
| Viselkedés  | Viselkedés engedélyezéséhez eltérhet az üzenet egy való életből vett adatok szimulálása a következő telemetriai adatokat. **Növekmény** eggyel-eggyel mindegyik üzenet elküldve a minimális értékkel kezdődő növeli az értéket. Ha teljesül a maximális érték, majd elindítja keresztül újra, a minimális érték. **Csökkentési** hasonlóan viselkedik **növekmény** , de számolja. A **véletlenszerű** viselkedés a minimális és maximális értékeket közötti véletlenszerű értéket állít be. |
| Minimális érték | A szabályzatbeállítás a elfogadható tartományban. |
| A maximális érték | A legnagyobb száma a tartományon. |
| Unit (Egység)      | A mértékegység meghatározásáért ° F vagy MPH például az érzékelő. |

## <a name="next-steps"></a>További lépések

Ez az útmutató útmutatóban bemutattuk, egy egyéni modell-szimuláció konfigurálása. Következő lépésként érdemes felfedezheti a többi [IoT-megoldásgyorsítók](about-iot-accelerators.md).