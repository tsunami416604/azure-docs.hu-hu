---
title: Az Azure verem terv, ajánlat, kvóta és előfizetés áttekintése |} Microsoft Docs
description: Felhő operátorként kívánt Azure verem terveket, ajánlatokat, kvóták és előfizetések ismertetése.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/07/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: d8aef778807d3a8a61cf9eedaae24abce84a19ab
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248758"
---
# <a name="plan-offer-quota-and-subscription-overview"></a>Csomag, ajánlat, kvóta és előfizetés áttekintése

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

[Azure verem](azure-stack-poc.md) megadható, hogy számos átadná szolgáltatások, például a virtuális gépeket, SQL Server-adatbázisok, SharePoint, Exchange, sőt akár [Azure piactéren elemek](azure-stack-marketplace-azure-items.md). Azure verem kezelőként konfigurálása, és ilyen szolgáltatások biztosításához Azure verem terveket, a ajánlatok és a kvóták segítségével.

Ajánlatok tartalmazhat egy vagy több terveket, és minden csomag tartalmaz egy vagy több szolgáltatás. Tervek létrehozásával, és azokat a különböző ajánlatok kombinálásával, kezelheti:

- Mely szolgáltatások és erőforrások a felhasználók férhetnek hozzá.
- A mérete, hogy a felhasználók is felhasználhatnak.
- Mely régiókat férhetnek hozzá az erőforrásokhoz.

Ha valamilyen szolgáltatás biztosításához kövesse az alábbi magas szintű lépéseket:

1. Adjon hozzá egy szolgáltatás, amelyet telepíthet a felhasználók számára.
2. Hozzon létre egy tervet, amely rendelkezik egy vagy több szolgáltatást. Terv létrehozása, amikor válassza ki, vagy a csomagban az erőforrás korlátai által megszabott minden egyes szolgáltatás meghatározó kvóták létrehozása.
3. Hozzon létre egy vagy több terveket tartalmazó ajánlatot. Az ajánlat alap tervek és választható bővítmény terveket tartalmazhatnak.

Az ajánlat létrehozása után a felhasználók előfizethetnek a szolgáltatásokat és erőforrásokat biztosít a ajánlat elérésére. Felhasználók előfizethetnek annyi ajánlatok szerint. Az alábbi ábrán egy egyszerű példa egy felhasználó két ajánlatok fizet elő. Minden egyes ajánlat rendelkezik a szolgáltatáscsomag vagy a kettő, és minden terv hozzáférést biztosít szolgáltatások.

![Bérlői előfizetéshez ajánlatok és tervek](media/azure-stack-key-features/image4.png)

## <a name="plans"></a>Tervek

Tervek csoportosítását egy vagy több szolgáltatást is. Azure verem kezelőként Ön [tervek létrehozása](azure-stack-create-plan.md) történő szinkronizálásával. A felhasználók, a csomagok és szolgáltatások tartoznak a ajánlatok előfizetni. Tervek létrehozásakor győződjön meg arról, hogy a kvótákat, az alap tervek meghatározása, és nem kötelező bővítmény terveket is.

### <a name="quotas"></a>Kvóták

Kezelheti a felhő kapacitása, előre konfigurált kvótákat használni, vagy létrehozhat egy új kvótát minden egyes szolgáltatás egy csomagban. Kvóták határait felső erőforrás felhasználói előfizetés kiépítése vagy felhasználását. Például a kvóta lehetővé teheti a felhasználó legfeljebb öt virtuális gépek (VM) létrehozásához. A virtuális gépeken, például a memória és CPU magok beállíthatja a további kvótákról.

Régiónként kvóták is rendelhetők. Például egy számítási szolgáltatásokat biztosít a terület A terv eredményezhet. a kvóta a 4 GB RAM és 8 processzormag két virtuális gépek.

>[!NOTE]
>A csomagban Azure verem, csak egy régió tartozik (nevű *helyi*) érhető el.

További információ [kvóta típusok Azure verem](azure-stack-quota-types.md).

### <a name="base-plan"></a>Alapcsomag

Az ajánlat létrehozásakor a szolgáltatás-rendszergazda tartalmazhat egy referenciaterv. E alap tervek szerepelnek alapértelmezés szerint, amikor a felhasználó adott ajánlatra. Amikor a felhasználó, hozzáférhetnek az e alap terveket (és a megfelelő kvóták.) a megadott összes erőforrás-szolgáltató

### <a name="add-on-plans"></a>Kiegészítő csomagok

Bővítmény tervek tervekben választható ajánlatot ad hozzá. Bővítmény tervek alapértelmezés szerint az előfizetés a nem tartalmaz. Bővítmény tervek további terveket (kvóták), amely az előfizető adhat hozzá az előfizetések ajánlatot érhető el. Például kínálhat egy referenciaterv próbaverziójára korlátozott erőforrásokkal, és nagyobb mértékű erőforrásokkal bővítmény tervének az ügyfelek, akik úgy dönt, hogy elfogadja a szolgáltatást.

## <a name="offers"></a>Ajánlatok

Ajánlatok olyan beállításcsoportok, egy vagy több tervek az Ön által létrehozott, hogy a felhasználók előfizethetnek őket. Például kínálnak Alpha terv A, amely biztosít számítási szolgáltatások és tervezze meg a B kiszolgálóra, tárolási és hálózati szolgáltatásokat biztosít, amely tartalmazhat.

Ha Ön [hozzon létre egy ajánlatot](azure-stack-create-offer.md), meg kell adni legalább egy referenciaterv, de bővítmény terveket tartalmazhatnak, amelyek felhasználókat adhat hozzá az előfizetés is létrehozhat.

## <a name="subscriptions"></a>Előfizetések

Az előfizetés egy, a felhasználók miként csatlakoznak a ajánlatokat. Ha Ön Azure verem operátor szolgáltató, a felhasználók (bérlőkkel) vásároljon a szolgáltatások a ajánlatok való feliratkozással. Ha egy szervezet Azure verem operátort, a felhasználók (alkalmazottak) fizethet nélkül fizető kínált szolgáltatásokat.

Ajánlattal felhasználó minden értékkombinációja egyedi előfizetés. Egy felhasználó rendelkezhet több ajánlatokat az előfizetések, de az egyes előfizetések csak egy ajánlat vonatkozik. Tervek ajánlatok és kvóták csak vonatkozik egy egyedi – nem osztható előfizetések között. Az egyes erőforrások egy felhasználó által létrehozott egy előfizetés tartozik.

### <a name="default-provider-subscription"></a>Alapértelmezett szolgáltató előfizetés

Az alapértelmezett szolgáltató előfizetés automatikusan létrejön a Azure verem csomag telepítésekor. Ez az előfizetés kezelése az Azure-vermet, további erőforrás-szolgáltatók telepítéséhez és tervek és a felhasználók ajánlatok létrehozása használható. A biztonsági és licencelési okok azt nem használja ügyfél számítási feladatok és alkalmazások futtatásához.

## <a name="next-steps"></a>További lépések

[Csomag létrehozása](azure-stack-create-plan.md)
