---
title: 'Gyors útmutató: egyéni riasztások létrehozása'
description: Ismerje meg, hozza létre és rendelje hozzá az egyéni eszközökhöz tartozó riasztásokat a IoT biztonsági szolgáltatás Azure Security Centerához.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: d1757868-da3d-4453-803a-7e3a309c8ce8
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/04/2020
ms.author: mlottner
ms.openlocfilehash: 7638ad070e8ac8bd99cbfb49b99bbb347a243a21
ms.sourcegitcommit: 59ea8436d7f23bee75e04a84ee6ec24702fb2e61
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2020
ms.locfileid: "89505437"
---
# <a name="quickstart-create-custom-alerts"></a>Gyors útmutató: egyéni riasztások létrehozása

Az egyéni biztonsági csoportok és riasztások használata révén teljes mértékben kihasználhatja a teljes körű biztonsági információkat és a kategorikus eszköz ismereteit, így biztosítva a jobb biztonságot a IoT-megoldáson belül.

## <a name="why-use-custom-alerts"></a>Miért érdemes egyéni riasztásokat használni?

Ismeri a IoT-eszközeit a legjobban.

Azon ügyfelek esetében, akik teljes mértékben értik a várt eszköz viselkedését, Azure Security Center a IoT lehetővé teszi, hogy az eszköz viselkedési házirendjében és a várttól, a normál működéstől való eltéréstől függően lefordítsa ezt az értelmezést.

## <a name="security-groups"></a>Biztonsági csoportok

A biztonsági csoportok lehetővé teszik, hogy az eszközök logikai csoportjait definiálja, és központi módon kezelhesse a biztonsági állapotukat.

Ezek a csoportok az adott hardverrel rendelkező eszközöket, az adott helyen üzembe helyezett eszközöket, vagy bármely más, az adott igényeknek megfelelő csoportot jelenthetnek.

A biztonsági csoportokat a **SecurityGroup**nevű Device Twin tag tulajdonság határozza meg. Alapértelmezés szerint a IoT Hub minden IoT-megoldása egy **alapértelmezett**nevű biztonsági csoporttal rendelkezik. Módosítsa a **SecurityGroup** tulajdonság értékét egy eszköz biztonsági csoportjának megváltoztatásához.

Például:

```
{
  "deviceId": "VM-Contoso12",
  "etag": "AAAAAAAAAAM=",
  "deviceEtag": "ODA1BzA5QjM2",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00",
  "connectionState": "Disconnected",
  "lastActivityTime": "0001-01-01T00:00:00",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "version": 4,
  "tags": {
    "SecurityGroup": "default"
  },
```

Biztonsági csoportok használatával csoportosíthatja az eszközöket logikai kategóriákba. A csoportok létrehozása után hozzárendelheti azokat a választott egyéni riasztásokhoz a leghatékonyabb végpontok közötti IoT biztonsági megoldáshoz.

## <a name="customize-an-alert"></a>Riasztás testreszabása

1. Nyissa meg a IoT Hub, és válassza a **Beállítások** lehetőséget a **Biztonság** menüben. 
1. Kattintson az **egyéni riasztások**elemre.
1. Válassza ki azt a biztonsági csoportot, amelyre alkalmazni kívánja a testreszabást.
1. Kattintson **az egyéni riasztás hozzáadása**lehetőségre.
1. Válasszon ki egy egyéni riasztást a legördülő listából.
1. Szerkessze a szükséges tulajdonságokat, majd kattintson **az OK**gombra.
1. Győződjön meg arról, hogy a **Mentés**gombra kattint. Az új riasztás mentése nélkül a rendszer törli a riasztást, amikor legközelebb IoT Hub.

## <a name="alerts-available-for-customization"></a>Testreszabáshoz elérhető riasztások

A IoT-hez készült Azure Security Center nagy számú riasztást biztosít, amelyek az adott igényeknek megfelelően testreszabhatók. Tekintse át a riasztás súlyosságát, az adatforrást, a leírást és a javasolt szervizelési lépéseket, ha az egyes riasztások fogadásakor és időpontjában a [testre szabható riasztási táblázat](concept-customizable-security-alerts.md)

## <a name="next-steps"></a>Következő lépések

A következő cikkből megtudhatja, hogyan telepíthet biztonsági ügynököt...

> [!div class="nextstepaction"]
> [Biztonsági ügynök üzembe helyezése](how-to-deploy-agent.md)
