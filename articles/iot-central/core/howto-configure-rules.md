---
title: Szabályok és műveletek konfigurálása az Azure IoT Centralban | Microsoft Docs
description: Ez a cikk bemutatja, hogyan konfigurálhatja a telemetria szabályokat és műveleteket az Azure IoT Central alkalmazásban.
author: vavilla
ms.author: vavilla
ms.date: 11/27/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: f6e85ba5aafaad973d28f799a251b6f3aae548e3
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871939"
---
# <a name="configure-rules"></a>Szabályok konfigurálása

*Ez a cikk operátorokra, fejlesztőkre és rendszergazdákra vonatkozik.*

A IoT Central szabályai testreszabható válasz eszközként szolgálnak, amely a csatlakoztatott eszközökről aktívan figyelt eseményeket indít el. A következő szakaszok ismertetik a szabályok kiértékelésének módját.

## <a name="select-target-devices"></a>Megcélzott eszközök kiválasztása

Az eszközök megcélzása szakaszban kiválaszthatja, hogy milyen típusú eszközökre lesz alkalmazva a szabály. A szűrők segítségével tovább pontosíthatja, hogy mely eszközöket kell tartalmaznia. A szűrők az eszköz tulajdonságainak használatával szűrhetik az eszközök készletét. Maguk a szűrők nem indítják el a műveletet. A következő képernyőképen a megcélozni kívánt eszközök típusa **hűtőszekrény**. A szűrő azt jelzi, hogy a szabály csak olyan **hűtőszekrényeket** tartalmazhat, amelyekben a **gyártott állapot** értéke **Washington**.

![Feltételek](media/howto-configure-rules/filters.png)

## <a name="use-multiple-conditions"></a>Több feltétel használata

A feltételek alapján a szabályok aktiválva lesznek. Jelenleg, ha több feltételt ad hozzá egy szabályhoz, akkor logikailag és együtt. Más szóval minden feltételnek teljesülnie kell ahhoz, hogy a szabály kiértékelése igaz legyen.  

A következő képernyőképen a feltételek azt állapítják meg, hogy a hőmérséklet nagyobb&deg; -e, mint 70 F, a páratartalom pedig kevesebb, mint 10. Ha mindkét utasítás igaz, a szabály igaz értéket ad vissza, és elindítja a műveletet.

![Feltételek](media/howto-configure-rules/conditions.png)

### <a name="use-a-cloud-property-in-a-value-field"></a>Felhőbeli tulajdonság használata érték mezőben

Egy Felhőbeli tulajdonságra hivatkozhat egy feltétel **Value (érték** ) mezőjében található eszköz sablonjában. A Cloud Property és a telemetria értékének hasonló típusúnak kell lennie. Ha például a **hőmérséklet** egy dupla, akkor a **Value** legördülő menüben csak a dupla megjelenítés típusú felhő tulajdonságai érhetők el.

Ha egy eseménytípus telemetria értéket választ, az **érték** legördülő menü tartalmazza a **bármely**lehetőséget. A **bármely** beállítás azt jelenti, hogy a szabály akkor következik be, amikor az alkalmazás egy ilyen típusú eseményt kap, függetlenül a hasznos adatoktól.

## <a name="use-aggregate-windowing"></a>Összesítő ablak használata

A szabályok kiértékelik a Windows összesített időkorlátját. Az alábbi képernyőképen az időablak öt perc. A szabály 5 percenként kiértékeli az adatmennyiséget az utolsó öt percben. Az adatmennyiség csak egyszer lesz kiértékelve abban az ablakban, amelyhez megfelel.

![Ablakok kiesése](media/howto-configure-rules/tumbling-window.png)

## <a name="use-rules-with-iot-edge-modules"></a>Szabályok használata IoT Edge modulokkal

Korlátozás vonatkozik a IoT Edge modulokra alkalmazott szabályokra. A különböző modulok telemetria vonatkozó szabályok nem érvényes szabályokként vannak kiértékelve. Példaként végezze el az alábbiakat. A szabály első feltétele az A modul egy hőmérsékleti telemetria. A szabály második feltétele a B modul nedvességtartalmának telemetria. Mivel a két feltétel különböző modulokból származik, ez a feltételek érvénytelenek. A szabály nem érvényes, és a rendszer hibát jelez a szabály mentésekor.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte, hogyan konfigurálhat egy szabályt az Azure IoT Central alkalmazásban, a következőket teheti:

> [!div class="nextstepaction"]
> [Az adatai menet közbeni elemzése](howto-create-analytics.md)
