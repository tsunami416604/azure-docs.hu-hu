---
title: 'Rövid útmutató: Egyéni riasztások létrehozása'
description: Ismerje meg, hozzon létre és rendeljen hozzá egyéni eszközriasztásokat az Azure Security Center for IoT biztonsági szolgáltatáshoz.
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
ms.date: 1/27/2020
ms.author: mlottner
ms.openlocfilehash: 8ba81b669c25d91efa8ad1f07b1a7835b42c5cf4
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310836"
---
# <a name="quickstart-create-custom-alerts"></a>Rövid útmutató: Egyéni riasztások létrehozása

Egyéni biztonsági csoportok és riasztások használatával teljes mértékben kihasználja a teljes körű biztonsági információkat és a kategorikus eszközismereteket az IoT-megoldás nagyobb biztonságának biztosítása érdekében.

## <a name="why-use-custom-alerts"></a>Miért érdemes egyéni riasztásokat használni?

Ön ismeri a legjobban az IoT-eszközeit.

Azok nak az ügyfeleknek, akik teljes mértékben tisztában vannak a várt eszközviselkedésükkel, az Azure Security Center for IoT lehetővé teszi, hogy ezt a megértést egy eszköz viselkedési szabályzatává alakítsa le, és riasztást kapjon a várható, normál viselkedéstől való eltérésről.

## <a name="security-groups"></a>Biztonsági csoportok

A biztonsági csoportok lehetővé teszik az eszközök logikai csoportjainak meghatározását és biztonsági állapotuk központosított kezelését.

Ezek a csoportok egy adott hardverrel rendelkező eszközöket, egy adott helyen telepített eszközöket vagy az Ön igényeinek megfelelő bármely más csoportot képviselhetnek.

A biztonsági csoportokat a **SecurityGroup**nevű ikercímke-tulajdonság határozza meg. Alapértelmezés szerint az IoT Hub minden IoT-megoldásának van egy **alapértelmezett**nevű biztonsági csoportja. Az eszköz biztonsági csoportjának módosításához módosítsa a **SecurityGroup** tulajdonság értékét.

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

A biztonsági csoportok segítségével az eszközöket logikai kategóriákba csoportosíthatja. A csoportok létrehozása után rendelje hozzá őket az Ön által választott egyéni riasztásokhoz a leghatékonyabb end-to-end IoT biztonsági megoldás érdekében.

## <a name="customize-an-alert"></a>Riasztás testreszabása

1. Nyissa meg az IoT Hubot.
1. Kattintson az **Egyéni riasztások elemre** a **Biztonság** szakaszban.
1. Válassza ki azt a biztonsági csoportot, amelyre alkalmazni szeretné a testreszabást.
1. Kattintson **az Egyéni riasztás hozzáadása gombra.**
1. Válasszon egyéni riasztást a legördülő listából.
1. Szerkesztsd a szükséges tulajdonságokat, kattintson az **OK gombra.**
1. Győződjön meg arról, hogy a **MENTÉS gombra**kattint. Az új riasztás mentése nélkül a riasztás törlődik a következő, amikor bezárja az IoT Hub.

## <a name="alerts-available-for-customization"></a>Testreszabásra rendelkezésre álló értesítések

Az Azure Security Center for IoT számos riasztást kínál, amelyek az Ön egyedi igényeinek megfelelően testreszabhatók. Tekintse át a [testreszabható riasztási táblázat](concept-customizable-security-alerts.md) riasztás súlyossága, adatforrás, leírás és a javasolt javítási lépéseket, ha és amikor az egyes riasztások érkeznek.

## <a name="next-steps"></a>További lépések

A következő cikkhez, hogy megtudja, hogyan kell telepíteni egy biztonsági ügynök ...

> [!div class="nextstepaction"]
> [Biztonsági ügynök telepítése](how-to-deploy-agent.md)
