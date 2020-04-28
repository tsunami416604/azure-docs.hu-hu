---
title: Megosztott felelősség a felhőben – Microsoft Azure
description: Ismerje meg a megosztott felelősségi modellt, valamint azt, hogy mely biztonsági feladatokat kezeli a felhőalapú szolgáltató, és hogy mely feladatokat kezeli.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "72518368"
---
# <a name="shared-responsibility-in-the-cloud"></a>Megosztott feladatkörök a felhőben

A nyilvános Cloud Services megítélése és kiértékelése során fontos megérteni a megosztott felelősségi modellt, és azt, hogy mely biztonsági feladatokat kezeli a felhőalapú szolgáltató, és mely feladatokat kezelheti Önnek. A számítási feladatok feladatai attól függően változnak, hogy a munkaterhelést a szolgáltatott szoftver (SaaS), a platform szolgáltatásként (IaaS), vagy egy helyszíni adatközpontban üzemeltetik-e.

## <a name="division-of-responsibility"></a>A felelősség megosztása
Egy helyszíni adatközpontban a teljes verem tulajdonosa. A felhőbe való áttérés során bizonyos kötelezettségek a Microsoftnak való átirányítással foglalkoznak. A következő ábra az Ön és a Microsoft közötti felelősség területeit mutatja be a verem központi telepítési típusának megfelelően.

![Felelősségi zónák](./media/shared-responsibility/shared-responsibility.png)

A teljesen felhőalapú központi telepítési típusoknál Ön rendelkezik minden adattal és identitással. Ön felelős az adatok és az identitások, a helyszíni erőforrások, valamint az Ön által felügyelt felhő-összetevők biztonságának védelméért (amely a szolgáltatás típusától függően változik).

Az üzembe helyezés típusától függetlenül a következő feladatokat mindig megőrzi:

- Adatok
- Végpontok
- Fiók
- Hozzáférés-kezelés

## <a name="cloud-security-advantages"></a>Felhőbeli biztonsági előnyök
A felhő jelentős előnyöket kínál a hosszú távú biztonsági kihívások megoldásához. A helyszíni környezetben a szervezetek valószínűleg kielégítetlen felelősséggel rendelkeznek, és korlátozott erőforrásokkal ruházzák fel a biztonságot, ami olyan környezetet hoz létre, amelyben a támadók kihasználhatják a biztonsági réseket minden rétegben.

Az alábbi ábrán egy hagyományos megközelítés látható, ahol a korlátozott erőforrások miatt számos biztonsági feladat nem teljesül. A felhőalapú megközelítésben a napi biztonsági felelősségeket a Felhőbeli szolgáltatónak kell elváltania, és újra hozzá kell rendelnie az erőforrásokat.

![A Cloud Era biztonsági előnyei](./media/shared-responsibility/cloud-enabled-security.png)

A felhőalapú megközelítés lehetővé teszi, hogy a felhőalapú biztonsági funkciókat hatékonyabban hasznosítsa, és a Felhőbeli intelligenciával javítsa a fenyegetések észlelését és a válaszadási időt. A felelősségi körök felhőbe való áthelyezésével a szervezetek nagyobb biztonsági lefedettséggel rendelkezhetnek, ami lehetővé teszi, hogy a biztonsági erőforrásokat és a költségvetést más üzleti prioritásokhoz lehessen rendelni.

## <a name="next-steps"></a>További lépések
Az Ön és a Microsoft közötti, SaaS-, Pásti-és IaaS-alapú üzembe helyezéssel kapcsolatos további információkért lásd: [a felhőalapú számítástechnika megosztott feladatai](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91).
