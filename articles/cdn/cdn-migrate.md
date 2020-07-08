---
title: Azure CDN profil migrálása a Verizon standardból a Verizon Premiumba
description: Ismerje meg, hogy miként telepíthet át egy profilt a Verizon standard és a Verizon Premium rendszerbe.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/21/2018
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: c1302822715a37ef1b85007130067ac02a8fd17a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84887596"
---
# <a name="migrate-an-azure-cdn-profile-from-standard-verizon-to-premium-verizon"></a>Azure CDN profil migrálása a standard Verizonból a Premium Verizonba

Ha Azure Content Delivery Network-(CDN-) profilt hoz létre a végpontok kezeléséhez, a Azure CDN négy különböző terméket kínál, amelyek közül választhat. További információ a különböző termékekről és azok elérhető funkcióiról: [Azure CDN termék funkcióinak összehasonlítása](cdn-features.md).

Ha létrehozott egy **Azure CDN standardot a Verizon** -profilból, és a használatával felügyeli a CDN-végpontokat, lehetősége van arra, hogy frissítse azt a Verizon-profilból **származó, Azure CDN Premiumra** . A frissítéskor a CDN-végpontokat és az összes adatait megőrzi a rendszer. 

> [!IMPORTANT]
> Ha a Verizon-profilból **Azure CDN Premiumra** frissített, később nem konvertálhatja a Verizon-profilból a **Azure CDN standard** szintre.
> 

**Azure CDN standard Verizon** -profilból való frissítéséhez forduljon a [Microsoft ügyfélszolgálatahoz](https://azure.microsoft.com/support/options/).

## <a name="profile-comparison"></a>Profil összehasonlítása
A **Verizon-profilokból Azure CDN Premium** a következő lényeges különbségeket tartalmazhatja a Verizon-profilokból **származó Azure CDN standardtól** :
- Bizonyos Azure CDN-funkciók, például a [tömörítés](cdn-improve-performance.md), a [gyorsítótárazási szabályok](cdn-caching-rules.md)és a [földrajzi szűrés](cdn-restrict-access-by-country.md)esetében nem használhatja a Azure CDN felületet, a Verizon portált a **kezelés** gomb segítségével kell használnia.
- API: a standard Verizontól eltérően nem használhatja az API-t a prémium Verizon-portálon elért szolgáltatások szabályozására. Az API-val azonban más gyakori funkciókat is vezérelheti, például egy végpont létrehozását vagy törlését, a gyorsítótárazott eszközök kiürítését és betöltését, valamint egy egyéni tartomány engedélyezését vagy letiltását.
- Díjszabás: a prémium Verizon a standard Verizontól eltérő díjszabási struktúrával rendelkezik. További információ: [Content Delivery Network díjszabása](https://azure.microsoft.com/pricing/details/cdn/).

A **Verizon-profilokból Azure CDN Premium** a következő funkciókat tartalmazza:
- [Jogkivonat-hitelesítés](cdn-token-auth.md): lehetővé teszi a felhasználók számára, hogy a biztonságos erőforrások beolvasásához jogkivonatot kérjenek és használjanak.
- [Szabályok motorja](cdn-rules-engine.md): lehetővé teszi a HTTP-kérések kezelésének testreszabását.
- Speciális elemzési eszközök:
   - [Részletes HTTP-elemzés](cdn-advanced-http-reports.md)
   - [Edge Performance Analytics](cdn-edge-performance.md)
   - [Valós idejű elemzések](cdn-real-time-alerts.md)


## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni a szabályok motorról, tekintse meg a [Azure CDN Rules Engine-referenciát](cdn-rules-engine-reference.md).

