---
title: "Az Azure verem terv, ajánlat, kvóta és előfizetés áttekintése |} Microsoft Docs"
description: "Felhő operátorként kívánt Azure verem terveket, ajánlatokat, kvóták és előfizetések ismertetése."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 8/22/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: 7c395dfcdfcd509d8b32028badd1c1310ea67657
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="plan-offer-quota-and-subscription-overview"></a>Csomag, ajánlat, kvóta és előfizetés áttekintése

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

[Azure verem](azure-stack-poc.md) megadható, hogy számos átadná szolgáltatások, például a virtuális gépeket, SQL Server-adatbázisok, SharePoint, Exchange, sőt akár [Azure piactéren elemek](azure-stack-marketplace-azure-items.md). Azure verem kezelőként konfigurálása, és ilyen szolgáltatások biztosításához Azure verem terveket, a ajánlatok és a kvóták segítségével.

Ajánlatok tartalmazhat egy vagy több terveket, és minden csomag tartalmaz egy vagy több szolgáltatás. Tervek létrehozásával, és azokat a különböző ajánlatok kombinálásával, szabályozása
- mely szolgáltatások és erőforrások a felhasználók
- azokat az erőforrásokat, a felhasználók is felhasználhatnak mennyisége
- mely régiókat férhetnek hozzá az erőforrásokhoz

Ha valamilyen szolgáltatás biztosításához fogjuk lépések magas szintű:

1. Adjon hozzá egy szolgáltatás, amelyet telepíthet a felhasználók számára.
2. Egy vagy több szolgáltatást tartalmazó terv létrehozása. Terv létrehozása, amikor válasszon, vagy hozzon létre a csomagban az erőforrás korlátai által megszabott minden egyes szolgáltatás meghatározó kvótákat.
3. Hozzon létre egy ajánlatot (például alap tervek és választható bővítmény tervek) egy vagy több terveket tartalmazó.

Miután létrehozta a kért ajánlat, a felhasználók előfizethetnek hozzáférjen a szolgáltatásokat és erőforrásokat biztosít. Felhasználók előfizethetnek annyi ajánlatok szerint. Az alábbi ábrán egy egyszerű példa egy felhasználó két ajánlatok fizet elő. Minden egyes ajánlat rendelkezik a szolgáltatáscsomag vagy a kettő, és minden terv hozzáférést biztosít szolgáltatások.

![](media/azure-stack-key-features/image4.png)

## <a name="plans"></a>Tervek

Tervek csoportosítását egy vagy több szolgáltatást is. Azure verem kezelőként Ön [tervek létrehozása](azure-stack-create-plan.md) történő szinkronizálásával. A felhasználók, a csomagok és szolgáltatások tartoznak a ajánlatok előfizetni. Tervek létrehozásakor győződjön meg arról, hogy a kvótákat, az alap tervek meghatározása, és nem kötelező bővítmény terveket is.

### <a name="quotas"></a>Kvóták

Kezeléséhez nyújt segítséget a felhő kapacitása, válassza ki, vagy hozzon létre egy kvótát minden egyes szolgáltatás egy csomagban. Kvóták határait felső erőforrás felhasználói előfizetés kiépítése vagy felhasználását. Például a kvóta lehetővé teheti a felhasználó legfeljebb öt virtuális gépek létrehozásához. Kvóták korlátozhatja a különböző erőforrások, például a virtuális gépek, memória és CPU korlátok.

Kvóták régió konfigurálhatja. Például egy tervet tartalmazó terület A számítási szolgáltatásai eredményezhet. a kvóta két virtuális gép, 4 GB RAM és 10 CPU-magokat. A csomagban Azure verem, csak egy régió tartozik (nevű *helyi*) érhető el.

### <a name="base-plan"></a>Alapcsomag

Az ajánlat létrehozásakor a szolgáltatás-rendszergazda tartalmazhat egy referenciaterv. E alap tervek szerepelnek alapértelmezés szerint, amikor a felhasználó adott ajánlatra. Amikor a felhasználó, hozzáférhetnek az e alap terveket (megfelelő kvótával) megadott összes erőforrás-szolgáltató.

### <a name="add-on-plans"></a>Kiegészítő csomagok

Nem kötelező bővítmény tervek ajánlatot is felvehető. Az előfizetés alapértelmezés szerint nem szerepelnek a bővítmény tervek. Bővítmény tervek további terveket (kvóták), amely az előfizető adhat hozzá az előfizetések ajánlatot érhető el. Például kínálhat egy referenciaterv próbaverziójára korlátozott erőforrásokkal, és nagyobb mértékű erőforrásokkal bővítmény tervének az ügyfelek, akik úgy dönt, hogy elfogadja a szolgáltatást.

## <a name="offers"></a>Ajánlatok

Ajánlatok olyan beállításcsoportok, egy vagy több tervek az Ön által létrehozott, hogy a felhasználók előfizethetnek őket. Alpha kínálnak például tartalmazhat terv A számítási szolgáltatások és a tárolási és hálózati szolgáltatások tartalmazó megtervezése B halmazát tartalmazza. 

Ha Ön [hozzon létre egy ajánlatot](azure-stack-create-offer.md), meg kell adni legalább egy referenciaterv, de bővítmény terveket tartalmazhatnak, amelyek felhasználókat adhat hozzá az előfizetés is létrehozhat.


## <a name="subscriptions"></a>Előfizetések

Az előfizetés egy, a felhasználók miként csatlakoznak a ajánlatokat. Ha egy szolgáltató Azure verem operátort, a felhasználók (bérlőkkel) vásároljon a szolgáltatások a ajánlatok való feliratkozással. Ha egy szervezet Azure verem operátort, a felhasználók (alkalmazottak) fizethet nélkül fizető kínált szolgáltatásokat. Ajánlattal felhasználó minden értékkombinációja egyedi előfizetés. Így a felhasználó rendelkezhet több ajánlatokat az előfizetések, de minden előfizetéshez csak egy ajánlat vonatkozik. Tervek ajánlatok és kvóták alkalmazása csak egyes egyedi előfizetések – nem osztható előfizetések között. Az egyes erőforrások egy felhasználó által létrehozott egy előfizetés tartozik.


### <a name="default-provider-subscription"></a>Alapértelmezett szolgáltató előfizetés

Az alapértelmezett szolgáltató előfizetés automatikusan létrejön a Azure verem csomag telepítésekor. Ez az előfizetés kezelése az Azure-vermet, további erőforrás-szolgáltatók telepítéséhez és tervek és a felhasználók ajánlatok létrehozása használható. A biztonsági és licencelési okok azt nem használandó felhasználói munkaterhelések és alkalmazások futtatásához. 

## <a name="next-steps"></a>További lépések

[Csomag létrehozása](azure-stack-create-plan.md)
