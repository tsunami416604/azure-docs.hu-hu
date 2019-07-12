---
title: Windows biztonsági események adatainak csatlakozhat az Azure-on Előzetesben Sentinel-|} A Microsoft Docs
description: Ismerje meg, hogyan kell csatlakozni a Windows biztonsági események adatainak Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2019
ms.author: rkarlin
ms.openlocfilehash: 188febf090ddb3f685f9d3c3b94d822f15bbcfcb
ms.sourcegitcommit: 80aaf27e3ad2cc4a6599a3b6af0196c6239e6918
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673767"
---
# <a name="connect-windows-security-events"></a>A Windows biztonsági eseményeinek csatlakoztatása 

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A Windows-kiszolgálók az Azure Sentinel-munkaterülethez kapcsolódó összes biztonsági eseményt streamelheti. Ez a kapcsolat lehetővé teszi megjelenítheti az irányítópultokat, egyéni riasztásokat is létrehozhat és vizsgálat javítása. Ez nagyobb betekintést kaphat a szervezet hálózati biztosít, és javítja a biztonsági művelet képességeket.  Események streamelése választhatja ki:

- **Az összes esemény** – az összes Windows biztonsági és AppLocker-eseményeket.
- **Közös** -eseményeinek naplózási célokra szabványos készletét. Ez egy teljes felhasználói auditnapló tartalmazza. A készlet például felhasználói bejelentkezési és felhasználói kijelentkezési események (event ID 4634) is tartalmaz. Például a biztonsági csoportok változásait, legfontosabb tartomány tartományvezérlő Kerberos műveleti és az eseményeket, amelyek a szervezetek által ajánlott műveletek naplózási tartalmazza.

Eseményeket, amelyek nagyon kevés a Rendszeríró a közös állítja be a fő motiváció kiválasztása az összes esemény keresztül csökkentése és a meghatározott események kiszűrésére.
- **Minimális** -eseményeket, amelyek esetleg jelzik a potenciális fenyegetések egy kis készletét. Ha engedélyezi ezt a beállítást, akkor tudja a teljes auditnaplót.  Ez csak olyan eseményeket, amelyek esetleg jelzik a sikeres biztonsági incidenseinek és a fontos eseményekről, amelyek nagyon kevés üzenettel rendelkező vonatkozik. Például a felhasználó sikeres és sikertelen bejelentkezés (esemény azonosítók 4624, 4625-ös számú) tartalmazza, de nem tartalmaz információt, amely a naplózás fontos, de nem értelmezhető az észlelést, és viszonylag nagy mennyiségű kijelentkezést. Ezen adatok mennyisége a legtöbb a bejelentkezési események és folyamat létrehozása event (esemény azonosítója 4688).
- **Nincs** – nincs biztonsági és AppLocker-eseményeket.

> [!NOTE]
> 
> - Adatokat a munkaterület, amely futtatja az Azure-Sentinel a földrajzi helyen kell tárolni.

Az alábbi lista eseményazonosítók nyújt a biztonsági és AppLocker teljes körű információkat az egyes:

| Adatszint | Összegyűjtött események mutatók |
| --- | --- |
| Minimális | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| Közös | 1,299,300,324,340,403,404,410,411,412,413,431,500,501,1100,1102,1107,1108,4608,4610,4611,4614,4622, |
| |  4624,4625,4634,4647,4648,4649,4657,4661,4662,4663,4665,4666,4667,4688,4670,4672,4673,4674,4675,4689,4697, |
| | 4700,4702,4704,4705,4716,4717,4718,4719,4720,4722,4723,4724,4725,4726,4727,4728,4729,4733,4732,4735,4737, |
| | 4738,4739,4740,4742,4744,4745,4746,4750,4751,4752,4754,4755,4756,4757,4760,4761,4762,4764,4767,4768,4771, |
| | 4774,4778,4779,4781,4793,4797,4798,4799,4800,4801,4802,4803,4825,4826,4870,4886,4887,4888,4893,4898,4902, |
| | 4904,4905,4907,4931,4932,4933,4946,4948,4956,4985,5024,5033,5059,5136,5137,5140,5145,5632,6144,6145,6272, |
| | 6273,6278,6416,6423,6424,8001,8002,8003,8004,8005,8006,8007,8222,26401,30004 |

## <a name="set-up-the-windows-security-events-connector"></a>A Windows biztonsági eseményeinek connector telepítése

Teljes mértékben a Windows biztonsági események integrálása az Azure-Sentinel:

1. Az Azure-Sentinel-portálon válassza **adatösszekötők** majd kattintson a a **Windows biztonsági eseményeinek** csempére. 
1. Válassza ki a kívánt adatfolyam adattípusokat.
1. Kattintson az **Update** (Frissítés) elemre.
6. A megfelelő sémát a Log Analytics használata a Windows biztonsági eseményeket, keresse meg **SecurityEvent**.

## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése

A naplók elindítani, megjelenik a Log Analytics körülbelül 20 percet igénybe vehet. 



## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtudhatta, hogyan szeretne csatlakozni, hogy a Windows biztonsági eseményeinek Azure Sentinel. Azure-Sentinel kapcsolatos további információkért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan [betekintést nyerhet az adatok és a potenciális fenyegetések](quickstart-get-visibility.md).
- Első lépések [Azure Sentinel-fenyegetések észlelése](tutorial-detect-threats.md).

