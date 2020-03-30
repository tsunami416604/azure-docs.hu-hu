---
title: Megosztott felelősség a felhőben – Microsoft Azure
description: Ismerje meg a megosztott felelősség modelljét, és hogy a felhőszolgáltató mely biztonsági feladatokat kezeli, és mely feladatokat kezeli.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: na
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/16/2019
ms.author: terrylan
ms.openlocfilehash: 8f16105d6bda1798828bc423ec8a158d49e0cf2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72518368"
---
# <a name="shared-responsibility-in-the-cloud"></a>Megosztott feladatkörök a felhőben

A nyilvános felhőszolgáltatások mérlegelése és kiértékelése során fontos, hogy megértse a megosztott felelősségi modellt, és hogy mely biztonsági feladatokat kezeli a felhőszolgáltató, és mely feladatokat kezeli Ön. A számítási feladatok attól függően változnak, hogy a számítási feladatok a Szoftver szolgáltatásként (SaaS), a Platform as a Service (PaaS), az Infrastruktúra szolgáltatásként (IaaS) vagy egy helyszíni adatközpontban találhatók- e.

## <a name="division-of-responsibility"></a>A felelősség megosztása
Egy helyszíni adatközpontban a teljes verem a teljes. Ahogy a felhőbe költözik, bizonyos feladatok átvihetők a Microsoftra. Az alábbi ábra bemutatja az Ön és a Microsoft közötti felelősségi területeket, a verem központi telepítésének típusától függően.

![Felelősségi zónák](./media/shared-responsibility/shared-responsibility.png)

A teljesen felhőalapú központi telepítési típusoknál Ön rendelkezik minden adattal és identitással. Ön felelős az adatok és identitások, a helyszíni erőforrások és a felhőbeli összetevők biztonságának védelméért (amely szolgáltatástípusonként változik).

A telepítés típusától függetlenül a következő feladatokat mindig ön tartja meg:

- Adatok
- Végpontok
- Fiók
- Hozzáférés-kezelés

## <a name="cloud-security-advantages"></a>Felhőalapú biztonsági előnyök
A felhő jelentős előnyöket kínál a régóta fennálló információbiztonsági kihívások megoldásához. A helyszíni környezetben a szervezetek valószínűleg kielégítetlen felelősségi körökkel és korlátozott erőforrásokkal rendelkeznek a biztonságba való befektetéshez, ami olyan környezetet hoz létre, ahol a támadók minden rétegbiztonsági biztonsági réseit ki tudják használni.

Az alábbi ábrán egy hagyományos megközelítés látható, amelyben számos biztonsági felelősség nem teljesül a korlátozott erőforrások miatt. A felhőalapú megközelítésben a napról a napra átruházhatja a felhőszolgáltatóra a biztonsági feladatokat, és újra kioszthatja az erőforrásokat.

![A felhőkorszak biztonsági előnyei](./media/shared-responsibility/cloud-enabled-security.png)

A felhőalapú megközelítésben a felhőalapú biztonsági képességeket is kihasználhatja a hatékonyabbá, és a felhőintelligencia használatával javíthatja a fenyegetésészlelési és -reagálási időt. Azáltal, hogy a feladatokat a felhőszolgáltatóra helyezi át, a szervezetek nagyobb biztonsági lefedettséget kaphatnak, ami lehetővé teszi számukra, hogy a biztonsági erőforrásokat és a költségvetést más üzleti prioritásokhoz csoportosítsák át.

## <a name="next-steps"></a>További lépések
A SaaS, a PaaS és az IaaS központi telepítésében ön és a Microsoft közötti felelősségmegosztásról a [Megosztott felelősségek a felhőalapú számítástechnikáért](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)című témakörben talál további információt.
