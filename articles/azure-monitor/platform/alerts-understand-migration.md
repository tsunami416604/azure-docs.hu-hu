---
title: Az Azure Monitor riasztások az önkéntes áttelepítési eszköz működésének megismerése
description: A riasztások áttelepítési eszköz működésének megismerése, és a problémák elhárításához.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 9d872a6d753a206dcfb03761e50e5854db4f146e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67071594"
---
# <a name="understand-how-the-migration-tool-works"></a>Az áttelepítési eszköz működésének megismerése

Mint [azt korábban bejelentettük](monitoring-classic-retirement.md), klasszikus riasztások az Azure monitorban vannak vezetve az szeptember 2019 (eredetileg július 2019 volt). A migrálási eszköz érhető el az ügyfelek számára, akik klasszikus riasztási szabályok és ki szeretne aktiválása áttelepítési magukat az Azure Portalon.

Ez a cikk ismerteti, hogyan az önkéntes áttelepítési eszköz. Kártérítése a néhány gyakori problémákat is ismerteti.

> [!NOTE]
> Bevezetés az áttelepítési eszköz késéssel, klasszikus riasztások az áttelepítéshez a kivezetési dátum lett [2019. augusztus 31-edik terjeszteni](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) eredetileg közzétett dátumától számítva 2019. június 30.

## <a name="which-classic-alert-rules-can-be-migrated"></a>Melyik klasszikus riasztási szabályok áttelepíthetők?

Az eszköz szinte az összes klasszikus riasztási szabályok áttelepíthetők, bár vannak kivételek. A következő riasztási szabályok nem lesznek áttelepítve, az eszköz használatával (vagy a szeptember 2019 automatikus áttelepítése során):

- Klasszikus riasztási szabályok (Windows és Linux) Vendég virtuális gépen metrikákra vonatkozóan. Tekintse meg a [útmutatást az ilyen riasztási szabályok az új metrikákhoz kapcsolódó riasztások könyvnyomtatásban](#guest-metrics-on-virtual-machines) a cikk későbbi részében.
- Klasszikus riasztási szabályok klasszikus tárnak metrikákra vonatkozóan. Tekintse meg a [útmutatást a klasszikus tárfiókok figyeléséhez](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/).
- Klasszikus riasztási szabályok az egyes tárfiókok mérőszámai. Lásd: [részletek](#storage-account-metrics) a cikk későbbi részében.

Ha az előfizetés bármely ilyen klasszikus szabályok, át kell őket manuálisan. Mivel a Microsoft nem biztosít automatikus áttelepítése, bármely meglévő, a klasszikus metrikariasztásokat az ilyen jellegű továbbra is működni fog június 2020-ig. Ez a bővítmény új riasztások átvitele időt biztosít. Nincsenek új klasszikus riasztások azonban augusztus 2019 után hozható létre.

### <a name="guest-metrics-on-virtual-machines"></a>A virtuális gépek Vendég mérőszámok

A Vendég mérőszámok új metrikákhoz kapcsolódó riasztások létrehozásához, a Vendég mérőszámok az Azure Monitor egyéni metrikákat tároló el kell küldeni. Kövesse ezeket az utasításokat az Azure Monitor-fogadó a diagnosztikai beállítások engedélyezése:

- [Windows virtuális gépek Vendég mérőszámainak engedélyezése](collect-custom-metrics-guestos-resource-manager-vm.md)
- [Linux rendszerű virtuális gépek Vendég mérőszámainak engedélyezése](collect-custom-metrics-linux-telegraf.md)

Ezeket a lépéseket kell elvégezni, miután a Vendég mérőszámok új metrikariasztásokat is létrehozhat. És Miután létrehozta az új metrikákhoz kapcsolódó riasztások, törölheti a klasszikus riasztások.

### <a name="storage-account-metrics"></a>Tárfiókok mérőszámai

A storage-fiókok összes klasszikus riasztások riasztások a metrikák kivételével telepíthető át:

- PercentAuthorizationError
- PercentClientOtherError
- Percentnetworkerror értéket mutatnak
- PercentServerOtherError
- PercentSuccess
- PercentThrottlingError
- Percenttimeouterror értéket mutatnak
- AnonymousThrottlingError
- SASThrottlingError
- ThrottlingError

A szabályok százalékban kifejezett metrikákra vonatkozóan kell áttelepíteni a klasszikus riasztás alapján [korábbi és új storage-mérőszámok közötti leképezést](https://docs.microsoft.com/azure/storage/common/storage-metrics-migration#metrics-mapping-between-old-metrics-and-new-metrics). Küszöbértékek megfelelően módosítható, mert a rendelkezésre álló új metrika egy abszolút kell.

Klasszikus riasztási szabályok AnonymousThrottlingError, SASThrottlingError és ThrottlingError kell osztani két új riasztásokat, mert nincs kombinált metrika, amely ugyanazokat a funkciókat biztosít. Küszöbértékek kell megfelelően adaptálhassa.

## <a name="rollout-phases"></a>Bevezetési fázisok

Az áttelepítési eszköz bevezetéséről szakaszában, az ügyfelek számára, amely a klasszikus riasztási szabályok használata. Az előfizetés-tulajdonosokat egy e-mailt fog kapni, ha az előfizetés rendelkezésre áll a eszközzel kell áttelepíteni.

> [!NOTE]
> Mivel az eszköz fázisban tesszük elérhetővé, láthatja, hogy az előfizetések többsége még nem állnak készen a korai fázisban kell áttelepíteni.

Előfizetések egy részét jelenleg meg van jelölve, migrálásra kész. A következő részhalmazt tartalmazza, amelyeken az klasszikus riasztási szabályok csak a következő erőforrástípusok ezen előfizetések. További erőforrástípusok fogja támogatni a későbbi fázisokban.

- Microsoft.apimanagement/service
- Microsoft.Batch/batchaccounts
- Microsoft.cache/redis
- Microsoft.DataFactory/datafactories
- Microsoft.dbformysql/servers
- Microsoft.dbforpostgresql/Servers
- Microsoft.eventhub/namespaces
- Microsoft.Logic/workflows
- Microsoft.network/applicationgateways
- Microsoft.network/dnszones
- Microsoft.network/expressroutecircuits
- Microsoft.network/loadbalancers
- Microsoft.network/networkwatchers/connectionmonitors
- Microsoft.network/publicipaddresses
- Microsoft.network/trafficmanagerprofiles
- Microsoft.network/virtualnetworkgateways
- Microsoft.search/searchservices
- Microsoft.servicebus/Namespaces
- Microsoft.streamanalytics/streamingjobs
- Microsoft.timeseriesinsights/Environments
- Microsoft.web/hostingenvironments/workerpools
- Microsoft.web/serverfarms
- Microsoft.web/sites

## <a name="who-can-trigger-the-migration"></a>Ki is aktiválhatja a migrálás?

Bármely felhasználó, aki közreműködői figyelés, a beépített szerepkör rendelkezik az előfizetés szintjén is aktiválhatja az áttelepítés. Egy egyéni biztonsági szerepkört a következő engedélyekkel rendelkező felhasználók is kiválthatják az áttelepítés:

- */read
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*

## <a name="common-problems-and-remedies"></a>Gyakori problémák és kártérítése

Miután [aktiválása az áttelepítés](alerts-using-migration-tool.md), úgy, hogy az áttelepítés akkor fejeződött be, vagy ha semmilyen teendője, a megadott címre e-mailt fog kapni. Ez a szakasz néhány gyakori problémát és azok kezelésére ismerteti.

### <a name="validation-failed"></a>Nem sikerült ellenőrizni a

Néhány legutóbbi módosításainak klasszikus riasztási szabályok az előfizetésében, mert az előfizetés nem telepíthetők át. Ez a probléma csak átmenetileg létezik. Az áttelepítés után a migrálás állapota Visszalépés újraindíthatja **migrálásra kész** néhány nap alatt.

### <a name="policy-or-scope-lock-preventing-us-from-migrating-your-rules"></a>A szabályok áttelepítés megakadályozza a házirend vagy -hatókörön zárolása

Az áttelepítés részeként jön létre új metrikákhoz kapcsolódó riasztások és Műveletcsoportok új, és majd a klasszikus riasztási szabály törölve lesz. Van azonban egy házirend vagy a hatókör zárolása megakadályozza-erőforrások létrehozását. A szabályzat vagy a hatókör zárolást függően néhány vagy összes szabályt meghajtóprogram nem telepíthető át. Ez a probléma megoldható a hatókör zárolása vagy a házirend eltávolítása ideiglenesen és aktiválása megismételni az áttelepítést.

## <a name="next-steps"></a>További lépések

- [Az áttelepítési eszköz használata](alerts-using-migration-tool.md)
- [Az áttelepítés előkészítése](alerts-prepare-migration.md)
