---
title: A Windows biztonsági eseményeinek összekötése az Azure Sentinel szolgáltatással | Microsoft Docs
description: Ismerje meg, hogyan csatlakoztathatók a Windows biztonsági események az Azure Sentinel szolgáltatáshoz.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 70190eeb3adec239d1e52f51afcd173497d08e6a
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588008"
---
# <a name="connect-windows-security-events"></a>A Windows biztonsági eseményeinek csatlakoztatása 



Az Azure Sentinel-munkaterülethez csatlakoztatott Windows-kiszolgálókról minden biztonsági eseményt továbbíthat. Ez a kapcsolat lehetővé teszi az irányítópultok megtekintését, az egyéni riasztások létrehozását és a vizsgálat javítását. Ez nagyobb betekintést nyújt a szervezet hálózatára, és javítja a biztonsági műveletek képességeit.  Kiválaszthatja, hogy mely események legyenek adatfolyamként:

- **Minden esemény** – minden Windows-biztonsági és AppLocker-esemény.
- **Common (általános** ) – a naplózási célokra szolgáló szabványos események. Ebben a készletben a teljes felhasználói naplózási nyomvonal szerepel. Ez a készlet például felhasználói bejelentkezést és felhasználói kijelentkezési eseményeket tartalmaz (4634-es azonosítójú esemény). Például a biztonsági csoportok változásait, legfontosabb tartomány tartományvezérlő Kerberos műveleti és az eseményeket, amelyek a szervezetek által ajánlott műveletek naplózási tartalmazza.

Eseményeket, amelyek nagyon kevés a Rendszeríró a közös állítja be a fő motiváció kiválasztása az összes esemény keresztül csökkentése és a meghatározott események kiszűrésére.
- **Minimális** – a lehetséges fenyegetéseket jelző események kis halmaza. Ha engedélyezi ezt a beállítást, nem fog tudni teljes naplózási nyomvonalat bejelentkezni.  Ez a készlet csak olyan eseményekre vonatkozik, amelyek egy nagyon alacsony kötettel rendelkező sikeres jogsértést és fontos eseményeket jelezhetnek. Ez a készlet például a felhasználó sikeres és sikertelen bejelentkezését (4624, 4625) tartalmazza, de nem tartalmaz kijelentkezési információt, amely fontos a naplózáshoz, de nem releváns az észleléshez, és viszonylag nagy mennyiségű. A készlet adatmennyiségének nagy része a bejelentkezési események és a folyamat-létrehozási esemény (4688-es AZONOSÍTÓJÚ esemény).
- **Nincs** – nincsenek biztonsági vagy AppLocker-események.

> [!NOTE]
> 
> - Az Azure Sentinel-t futtató munkaterület földrajzi helye tárolja az adatmennyiséget.
> - Ha Azure Security Center és az Azure Sentinel ugyanazon a munkaterületen fut, akkor a biztonsági események összekötője csak Azure Security Center vagy az Azure Sentinel szolgáltatásból csatlakoztatható. Ha ezeket az eseményeket az Azure Sentinelből szeretné kezelni, azt javasoljuk, hogy a Azure Security Centerről válassza le, és csak az Azure Sentinelhez csatlakoztassa.


Az alábbi lista az egyes készletekhez tartozó biztonsági és alkalmazás-zárolási események azonosítóinak teljes részletezését tartalmazza:

| Adatszint | Összegyűjtött események mutatók |
| --- | --- |
| Minimális | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| Közös | 1, 299, 300, 324, 340, 403, 404, 410, 411, 412, 413, 431, 500, 501, 1100, 1102, 1107, 1108, 4608, 4610, 4611, 4614, 4622, |
| |  4624,4625,4634,4647,4648,4649,4657,4661,4662,4663,4665,4666,4667,4688,4670,4672,4673,4674,4675,4689,4697, |
| | 4700,4702,4704,4705,4716,4717,4718,4719,4720,4722,4723,4724,4725,4726,4727,4728,4729,4733,4732,4735,4737, |
| | 4738,4739,4740,4742,4744,4745,4746,4750,4751,4752,4754,4755,4756,4757,4760,4761,4762,4764,4767,4768,4771, |
| | 4774,4778,4779,4781,4793,4797,4798,4799,4800,4801,4802,4803,4825,4826,4870,4886,4887,4888,4893,4898,4902, |
| | 4904,4905,4907,4931,4932,4933,4946,4948,4956,4985,5024,5033,5059,5136,5137,5140,5145,5632,6144,6145,6272, |
| | 6273,6278,6416,6423,6424,8001,8002,8003,8004,8005,8006,8007,8222,26401,30004 |

## <a name="set-up-the-windows-security-events-connector"></a>A Windows biztonsági események összekötő beállítása

Windowsos biztonsági események teljes körű integrálása az Azure Sentinel használatával:

1. Az Azure Sentinel portálon válassza az **adatösszekötők** lehetőséget, majd kattintson a **biztonsági események** csempére. 
1. Válassza ki, hogy mely adattípusokat kívánja továbbítani.
1. Kattintson a **Frissítés** parancsra.
6. Ha a Windows biztonsági eseményeihez a Log Analytics vonatkozó sémát szeretné használni, keresse meg a **SecurityEvent**.

## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése

Előfordulhat, hogy körülbelül 20 percet vesz igénybe, amíg a naplók meg nem jelennek a Log Analyticsban. 



## <a name="next-steps"></a>Következő lépések
Ebből a dokumentumból megtanulta, hogyan csatlakoztathatók a Windows biztonsági eseményei az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).

