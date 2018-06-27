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
ms.openlocfilehash: 074dbb094e7ae2cd1f1719016928bd7348da3949
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/26/2018
ms.locfileid: "36958054"
---
# <a name="migrate-an-azure-cdn-profile-from-standard-verizon-to-premium-verizon"></a>Az Azure CDN-profil át Standard Verizon Premium Verizon

A végpontok kezelése az Azure Content Delivery Network (CDN) profil létrehozásakor az Azure CDN választhat négy különböző termékek kínál. A különböző termékek és azok elérhető funkciókkal kapcsolatos információkért lásd: [összehasonlítása Azure CDN termék funkcióiról](cdn-features.md).

Ha már létre egy **Azure CDN Standard verizon** profilhoz, és azt használja a CDN-végpontokat kezelheti a lehetőséggel frissítheti úgy, hogy rendelkezik egy **verizon Azure CDN Premium** profil. Amikor frissít, a CDN-végpontokat és az összes adat megmarad. 

> [!IMPORTANT]
> Miután végzett korábban is egy **verizon Azure CDN Premium** profil, már nem később módosíthatja vissza egy **Azure CDN Standard verizon** profil.
> 

Frissítése az **Azure CDN Standard verizon** profil, kapcsolattartási [Microsoft Support](https://azure.microsoft.com/support/options/).

## <a name="profile-comparison"></a>Profil összehasonlítása
**Verizon Azure CDN Premium** profilok rendelkezik a következő kulcs különbségeket **Azure CDN Standard verizon** profilokat:
- Az egyes Azure CDN szolgáltatások, például a [tömörítés](cdn-improve-performance.md), [szabályok gyorsítótárazás](cdn-caching-rules.md), és [földrajzi szűrés](cdn-restrict-access-by-country.md), nem használhatja az Azure CDN felület, a Verizon portálon keresztül kell használnia a **Kezelése** gombra.
- Alkalmazásprogramozási felületek: Eltérően a Standard Verizon, nem használhatja az API-t ezek a Funkciók, amelyek a Premium Verizon portálról érhetők el. Az API segítségével azonban más közös a Funkciók, például egy végpont létrehozása/törlése, gyorsítótárazott eszközök kiürítése/betöltése, és az egyéni tartománynév engedélyezése vagy tiltása.
- Árképzési: Premium Verizon adatátviteli mint Standard Verizon rendelkezik különböző árképzési struktúra. További információkért lásd: [Content Delivery Network árképzési](https://azure.microsoft.com/pricing/details/cdn/).

**Verizon Azure CDN Premium** profilok az alábbi további funkciókkal rendelkeznek:
- [Jogkivonat hitelesítési](cdn-token-auth.md): lehetővé teszi a felhasználóknak, hogy megszerezze és használja a jogkivonatot beolvasni a biztonságos erőforrások.
- [Szabálymotor](cdn-rules-engine.md): segítségével testre szabhatja a HTTP-kérések kezelésének módja.
- Bővített analitikát eszközök:
   - [Részletes HTTP elemzés](cdn-advanced-http-reports.md)
   - [Peremhálózati teljesítmény elemzés](cdn-edge-performance.md)
   - [Valós idejű elemzési](cdn-real-time-alerts.md)


## <a name="next-steps"></a>További lépések
A szabályok motor kapcsolatos további információkért lásd: [Azure CDN szabályok motor hivatkozás](cdn-rules-engine-reference.md).

