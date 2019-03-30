---
title: Megismerheti, hogyan önkéntes riasztások áttelepítési eszköz az Azure Monitor működése
description: A riasztási áttelepítési eszköz működésének megismerése, és ha a felmerülő hibák elhárítása.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: a45a0cff606bc854924d5da0841b26e1cb9031bb
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632320"
---
# <a name="understand-how-the-migration-tool-works"></a>Az áttelepítési eszköz működésének megismerése

Mint [azt korábban bejelentettük](monitoring-classic-retirement.md), klasszikus riasztások az Azure monitorban vannak vezetve a július 2019. Az áttelepítési eszköz való áttelepítés önkéntesen érhető el az Azure Portalon, és ügyfeleink, akik klasszikus riasztási szabályok jelennek meg.

Ez a cikk végigvezeti az önkéntes migrálást eszköz működését. Szervizelés a gyakori problémákat is ismerteti.

## <a name="which-classic-alert-rules-can-be-migrated"></a>Melyik klasszikus riasztási szabályok áttelepíthetők?

Szinte összes klasszikus riasztási szabályt az eszköz használatával telepíthetők át, bár vannak kivételek. A következő riasztási szabályok nem lesznek áttelepítve az eszközzel (vagy a július 2019 automatikus áttelepítése során)

- Klasszikus riasztási szabályok a virtuális gép vendég mérőszámok (Windows és Linux). [Hozza létre újra a e riasztási szabályok az új metrikákhoz kapcsolódó riasztások útmutatást talál](#guest-metrics-on-virtual-machines)
- Klasszikus riasztási szabályok klasszikus tárnak metrikákra vonatkozóan. [Útmutatást talál a monitorozása a klasszikus tárfiókok](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/)
- Klasszikus riasztási szabályok az egyes tárfiókok mérőszámai. [Alább a részletekre](#storage-account-metrics)

Ha az előfizetés bármely ilyen klasszikus szabályok, a szabályok rest át lesz telepítve, de ezeket a szabályokat kell manuálisan kell áttelepíteni. Mivel a Microsoft nem biztosít automatikus áttelepítése, bármely ilyen meglévő klasszikus metrikariasztásokat továbbra is dolgozunk azon, hogy június 2020-ra, hogy új riasztások átvitele időt adjon. Azonban nincsenek új klasszikus riasztások hozható létre post június 2019.

### <a name="guest-metrics-on-virtual-machines"></a>A virtuális gépek Vendég mérőszámok

A Vendég mérőszámok új metrikákhoz kapcsolódó riasztások létrehozására, a Vendég mérőszámok kell kell küldeni az Azure Monitor egyéni metrikákat tároló. Kövesse az alábbi utasításokat az Azure Monitor-fogadó a diagnosztikai beállítások engedélyezése.

- [Windows virtuális gépek Vendég mérőszámainak engedélyezése](collect-custom-metrics-guestos-resource-manager-vm.md)
- [Linux rendszerű virtuális gépek Vendég mérőszámainak engedélyezése](https://docs.microsoft.com/azure/azure-monitor/platform/collect-custom-metrics-linux-telegraf)

Miután a fenti lépéseket kell elvégezni, a Vendég mérőszámok hozható létre új metrikákhoz kapcsolódó riasztások. Új metrikákhoz kapcsolódó riasztások kell újból, klasszikus riasztások törölhetők.

### <a name="storage-account-metrics"></a>Tárfiókok mérőszámai

A storage-fiókok összes klasszikus riasztások ezeket a riasztásokat, a következő metrikákra vonatkozóan kivételével telepíthető át:

- PercentAuthorizationError
- PercentClientOtherError
- Percentnetworkerror értéket mutatnak
- PercentServerOtherError
- PercentSuccess
- PercentThrottlingError
- Percenttimeouterror értéket mutatnak
- AnonymousThrottlingError
- SASThrottlingError

A százalékos metrikák klasszikus riasztási szabályok kell áttelepíteni kívánt alapján [korábbi és új storage-mérőszámok közötti leképezést](https://docs.microsoft.com/azure/storage/common/storage-metrics-migration#metrics-mapping-between-old-metrics-and-new-metrics). Küszöbértékek megfelelően módosítható, mert a rendelkezésre álló új metrika egy abszolút kell.

Két új riasztásokat is vannak felosztva kell AnonymousThrottlingError és SASThrottlingError klasszikus riasztási szabályok nincs kombinált metrika, amely ugyanazokat a funkciókat biztosít. Küszöbértékek kell megfelelően adaptálhassa.

## <a name="roll-out-phases"></a>Bevezetési fázisok

Az áttelepítési eszköz bevezetéséről szakaszában, az ügyfelek számára, amely a klasszikus riasztási szabályok használata. **Az előfizetés-tulajdonosokat** egy e-mailt fog kapni, ha az előfizetés rendelkezésre áll az eszközzel kell áttelepíteni.

> [!NOTE]
> Az eszköz fázisban történik, a korai fázisban tesszük elérhetővé, láthatja, hogy az előfizetések többsége még nem állnak készen kell áttelepíteni.

Jelenleg egy **részhalmazát** -előfizetést, amely **csak** klasszikus riasztási szabályok típus szerint készen áll az áttelepítéshez vannak megjelölve az alábbi erőforrás rendelkezik. További erőforrástípusok fogja támogatni a későbbi fázisokban.

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

Minden olyan felhasználó, aki a beépített szerepkör rendelkezik a **közreműködő figyelése** az előfizetés szintű fogja tudni elindítani a migrálás. Egy egyéni biztonsági szerepkört a következő engedélyekkel rendelkező felhasználók is kiválthatják az áttelepítés:

- */read
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*

## <a name="common-issues-and-remediations"></a>Gyakori problémák és szervizelések

Egyszer, [aktiválása az áttelepítés](alerts-using-migration-tool.md), használjuk fel Önt a migrálás befejezése után a megadott e-mail-címeit, vagy ha nincs szükség művelet. Az alábbi szakasz néhány olyan gyakori problémát és azok javítása

### <a name="validation-failed"></a>Az érvényesítés nem sikerült

Néhány legutóbbi módosításainak klasszikus riasztási szabályok az előfizetésében, mert az előfizetés nem telepíthetők át. Ez az ideiglenes probléma. Az áttelepítés után a migrálás állapota Visszalépés újraindíthatja **migrálásra kész** néhány nap alatt.

### <a name="policyscope-lock-preventing-us-from-migrating-your-rules"></a>A szabályok áttelepítés megakadályozza szabályzathatókörnek/zárolása

Az áttelepítés részeként jön létre új metrikákhoz kapcsolódó riasztások és Műveletcsoportok új és klasszikus riasztási szabályok (új szabályok létrehozása) után törlődnek. Van azonban egy házirend vagy a hatókör zárolása megakadályozza-erőforrások létrehozását. A szabályzat vagy a hatókör zárolást függően néhány vagy összes szabályt meghajtóprogram nem telepíthető át. A probléma megoldásához a hatókör-zárolási és házirend ideiglenesen eltávolításával, és aktiválja újra a migrálás.

## <a name="next-steps"></a>További lépések

- [Az áttelepítési eszköz használata](alerts-using-migration-tool.md)
- [Az áttelepítés előkészítése](alerts-prepare-migration.md)
