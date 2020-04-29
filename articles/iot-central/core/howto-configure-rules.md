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
ms.openlocfilehash: 509f9557a8128df12353ad02a7c7db02b7b42631
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80158459"
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

## <a name="use-aggregate-windowing"></a>Összesítő ablak használata

A szabályok kiértékelik a Windows összesített időkorlátját. Az alábbi képernyőképen az időablak öt perc. A szabály 5 percenként kiértékeli az adatmennyiséget az utolsó öt percben. Az adatmennyiség csak egyszer lesz kiértékelve abban az ablakban, amelyhez megfelel.

![Ablakok kiesése](media/howto-configure-rules/tumbling-window.png)

## <a name="use-rules-with-iot-edge-modules"></a>Szabályok használata IoT Edge modulokkal

Korlátozás vonatkozik a IoT Edge modulokra alkalmazott szabályokra. A különböző modulok telemetria vonatkozó szabályok nem érvényes szabályokként vannak kiértékelve. Példaként végezze el az alábbiakat. A szabály első feltétele az A modul egy hőmérsékleti telemetria. A szabály második feltétele a B modul nedvességtartalmának telemetria. Mivel a két feltétel különböző modulokból származik, ez a feltételek érvénytelenek. A szabály nem érvényes, és a rendszer hibát jelez a szabály mentésekor.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte, hogyan konfigurálhat egy szabályt az Azure IoT Central alkalmazásban, a következőket teheti:

> [!div class="nextstepaction"]
> [Az adatai menet közbeni elemzése](howto-create-analytics.md)
