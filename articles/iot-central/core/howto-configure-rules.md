---
title: Szabályok és műveletek konfigurálása az Azure IoT Centralban | Microsoft Docs
description: Ez az útmutató cikk bemutatja, mint szerkesztő, hogyan konfigurálhatja a telemetriai alapú szabályok és műveletek az Azure IoT Central-alkalmazásban.
author: vavilla
ms.author: vavilla
ms.date: 11/27/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 509f9557a8128df12353ad02a7c7db02b7b42631
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158459"
---
# <a name="configure-rules"></a>Szabályok konfigurálása



*Ez a cikk operátorokra, fejlesztőkre és rendszergazdákra vonatkozik.*

Az IoT Central szabályai testre szabható válaszeszközként szolgálnak, amely aktívan figyelt eseményeket vált ki a csatlakoztatott eszközökről. A következő szakaszok a szabályok kiértékelésének módját ismertetik.

## <a name="select-target-devices"></a>Céleszközök kiválasztása

A céleszközök szakaszban válassza ki, hogy milyen típusú eszközök ezt a szabályt fogja alkalmazni. A szűrők lehetővé teszik a további finomítást, hogy milyen eszközöket kell felvenni. A szűrők az eszközsablon tulajdonságaival szűrik le az eszközök készletét. Maguk a szűrők nem indítanak el műveletet. A következő képernyőképen a megcélzott eszközök a Hűtőszekrény eszközsablon-típusba **tartoznak.** A szűrő kimondja, hogy a szabály csak akkor tartalmazza **a hűtőszekrények,** ahol a **gyártott állami** tulajdonság egyenlő **Washington**.

![Feltételek](media/howto-configure-rules/filters.png)

## <a name="use-multiple-conditions"></a>Több feltétel használata

A feltételek azok, amelyeket a szabályok kiváltanak. Jelenleg, ha több feltételt ad hozzá egy szabályhoz, azok logikailag ÉS együtt vannak. Más szóval, minden feltételnek teljesülnie kell ahhoz, hogy a szabály igaznak minősüljen.  

A következő képernyőképen a feltételek ellenőrzik, hogy&deg; a hőmérséklet-e meghaladja a 70 F-ot, és a páratartalom kevesebb, mint 10. Ha mindkét utasítás igaz, a szabály igaz értéket ad ki, és műveletet indít el.

![Feltételek](media/howto-configure-rules/conditions.png)

## <a name="use-aggregate-windowing"></a>Összesítő ablakolás használata

A szabályok az összesített időablakokat bukdácsoló ablakokként értékelik. Az alábbi képernyőképen az időablak öt perc. A szabály öt percenként értékeli ki az adatok utolsó öt percét. Az adatok kiértékelése csak egyszer jelenik meg abban az ablakban, amelynek megfelel.

![A Windows bukdácsolása](media/howto-configure-rules/tumbling-window.png)

## <a name="use-rules-with-iot-edge-modules"></a>Szabályok használata IoT Edge-modulokkal

A korlátozás az IoT Edge-modulokra alkalmazott szabályokra vonatkozik. A különböző modulok telemetriai adatai nem minősülnek érvényes szabálynak. Vegyük például a következőket. A szabály első feltétele az A modul hőmérsékleti telemetria. A szabály második feltétele a B modul páratartalom-telemetriai adatai. Mivel a két feltétel különböző modulokból származik, ez érvénytelen feltételkészlet. A szabály nem érvényes, és hibaüzenetet ad a szabály mentésének megkísérlésektől.

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta, hogyan konfigurálhat egy szabályt az Azure IoT Central alkalmazásban, a következőket teheti:

> [!div class="nextstepaction"]
> [Elemezze adatait menet közben](howto-create-analytics.md)
