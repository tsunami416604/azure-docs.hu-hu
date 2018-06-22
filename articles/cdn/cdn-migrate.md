---
title: Az Azure CDN-profil át Verizon Standard Verizon Premium |} Microsoft Docs
description: Ismerje meg a profil áttelepítése Verizon Standard Verizon Premium részleteit.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2018
ms.author: v-deasim
ms.custom: ''
ms.openlocfilehash: 615f35c602f9a086bdc5c32b04a97efa368411a3
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36308125"
---
# <a name="migrate-an-azure-cdn-profile-from-standard-verizon-to-premium-verizon"></a>Az Azure CDN-profil át Standard Verizon Premium Verizon

Az Azure Content Delivery Network (CDN) ajánlatok négy különböző termékek, amelyek mindegyike különböző funkciókat nyújtja: 
- **A Microsoft Azure CDN Standard**
- **Akamai Azure CDN Standard**
- **Verizon Azure CDN Standard**
- **Verizon Azure CDN Premium**.

Használata esetén egy **Azure CDN Standard verizon** profilhoz, a CDN-végpontokat kezelheti, lehetősége van a frissítés úgy, hogy egy **verizon Azure CDN Premium** profilt, és őrizze meg az összes adat.

Frissítése az **Azure CDN Standard verizon** profil, kapcsolattartási [Microsoft Support](https://azure.microsoft.com/support/options/).

> [!IMPORTANT]
> Miután végzett korábban is egy **verizon Azure CDN Premium** profil, már nem később módosíthatja vissza egy **Azure CDN Standard verizon** profil.
> 

## <a name="profile-comparison"></a>Profil összehasonlítása
**Verizon Azure CDN Premium** profilok rendelkezik a következő kulcs különbségeket **Azure CDN Standard verizon** profilokat:
- Az egyes Azure CDN szolgáltatások, például a [tömörítés](cdn-improve-performance.md), szabályok, gyorsítótárazás és [földrajzi szűrés](cdn-restrict-access-by-country.md), nem használhatja az Azure CDN felület, a Verizon portálon keresztül kell használnia a **kezelése**gombra.
- Alkalmazásprogramozási felületek: Standard Verizon ellentétben ezek a Funkciók, amelyek a Premium Verizon portálról érhetők el az API nem használható. Az API segítségével azonban más közös a Funkciók, például egy végpont létrehozása/törlése, gyorsítótárazott eszközök kiürítése/betöltése, és az egyéni tartománynév engedélyezése vagy tiltása.
- Árképzési: Premium Verizon adatátviteli mint Standard Verizon rendelkezik különböző árképzési struktúra. További információkért lásd: [Content Delivery Network árképzési](https://azure.microsoft.com/pricing/details/cdn/).

**Verizon Azure CDN Premium** profilok az alábbi további funkciókkal rendelkeznek:
- [Jogkivonat hitelesítési](cdn-token-auth.md): lehetővé teszi a felhasználóknak, hogy megszerezze és használja a jogkivonatot beolvasni a biztonságos erőforrások.
- [Szabálymotor](cdn-rules-engine.md): segítségével testre szabhatja a HTTP-kérések kezelésének módja.
- Bővített analitikát eszközök:
   - [Részletes HTTP elemzés](cdn-advanced-http-reports.md)
   - [Peremhálózati teljesítmény elemzés](cdn-edge-performance.md)
   - [Valós idejű elemzési](cdn-real-time-alerts.md)


## <a name="next-steps"></a>További lépések
A részletes Azure CDN termék funkcióiról, lásd: [Azure CDN termék funkcióiról](Compare Azure CDN product features).

A szabályok motor kapcsolatos további információkért lásd: [Azure CDN szabályok motor hivatkozás](cdn-rules-engine-reference.md).

