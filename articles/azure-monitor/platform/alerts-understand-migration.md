---
title: Az Azure Monitor riasztások az önkéntes áttelepítési eszköz működésének megismerése
description: A riasztások áttelepítési eszköz működésének megismerése, és a problémák elhárításához.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 015000388c5629dbd8ed8833931a809ebd738bd6
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295532"
---
# <a name="understand-how-the-migration-tool-works"></a>Az áttelepítési eszköz működésének megismerése

Mint [azt korábban bejelentettük](monitoring-classic-retirement.md), klasszikus riasztások az Azure monitorban vannak vezetve által 2019. augusztus 31-ig (eredetileg. június 30 2019 volt). A migrálási eszköz érhető el az ügyfelek számára, akik klasszikus riasztási szabályok és ki szeretne aktiválása áttelepítési magukat az Azure Portalon.

Ez a cikk ismerteti, hogyan az önkéntes áttelepítési eszköz. Kártérítése a néhány gyakori problémákat is ismerteti.

> [!NOTE]
> Bevezetés az áttelepítési eszköz késéssel, klasszikus riasztások az áttelepítéshez a kivezetési dátum lett [2019. augusztus 31-edik terjeszteni](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) eredetileg közzétett dátumától számítva 2019. június 30.

## <a name="classic-alert-rules-that-will-not-be-migrated"></a>Klasszikus riasztási szabályok, amelyek nem lesznek áttelepítve

> [!IMPORTANT]
> Az áttelepítés nem befolyásolják a tevékenységnapló-riasztások (például Service health riasztások) és a riasztások. A migrálás csak meghatározott klasszikus riasztási szabályokat vonatkozik [Itt](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform).

Bár az eszköz szinte az összes áttelepíthető [klasszikus riasztási szabályok](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform), vannak kivételek. A következő riasztási szabályok nem lesznek áttelepítve, az eszköz használatával (vagy az automatikus áttelepítés szeptember 2019 indítása során):

- Klasszikus riasztási szabályok (Windows és Linux) Vendég virtuális gépen metrikákra vonatkozóan. Tekintse meg a [útmutatást az ilyen riasztási szabályok az új metrikákhoz kapcsolódó riasztások könyvnyomtatásban](#guest-metrics-on-virtual-machines) a cikk későbbi részében.
- Klasszikus riasztási szabályok klasszikus tárnak metrikákra vonatkozóan. Tekintse meg a [útmutatást a klasszikus tárfiókok figyeléséhez](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/).
- Klasszikus riasztási szabályok az egyes tárfiókok mérőszámai. Lásd: [részletek](#storage-account-metrics) a cikk későbbi részében.
- Klasszikus riasztási szabályok néhány Cosmos DB metrikákra vonatkozóan. Részletek egy következő frissítés fogja tartalmazni.

Ha az előfizetés bármely ilyen klasszikus szabályok, át kell őket manuálisan. Mivel a Microsoft nem biztosít automatikus áttelepítése, bármely meglévő, a klasszikus metrikariasztásokat az ilyen jellegű továbbra is működni fog június 2020-ig. Ez a bővítmény új riasztások átvitele időt biztosít. Nincsenek új klasszikus riasztások azonban augusztus 2019 után hozható létre.

> [!NOTE]
> Amellett, hogy a fent felsorolt kivételeket, ha a klasszikus riasztási szabályok érvénytelen azaz legyenek a [elavult metrikák](#classic-alert-rules-on-deprecated-metrics) vagy erőforrások, amelyek törölve lett, ezek nem telepíthetők át önkéntes áttelepítés során. Ilyen érvénytelen klasszikus riasztási szabályt törlődik, ha automatikus áttelepítése történik.

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

### <a name="classic-alert-rules-on-deprecated-metrics"></a>Klasszikus riasztási szabályok az elavult metrikák

Ezek a klasszikus riasztási szabályokat metrikákhoz, ami korábban is támogatott, de végül is elavult. Az ügyfél egy kis lehet érvénytelen klasszikus riasztási szabályok az ilyen metrikákra vonatkozóan. Ezek a riasztási szabályok érvénytelenek, mert ezek nem telepíthetők át.

| Erőforrás típusa| Elavult metric(s) |
|-------------|----------------- |
| Microsoft.DBforMySQL/servers | compute_consumption_percent, compute_limit |
| Microsoft.DBforPostgreSQL/servers | compute_consumption_percent, compute_limit |
| Microsoft.Network/publicIPAddresses | defaultddostriggerrate |
| Microsoft.SQL/servers/databases | service_level_objective, storage_limit, storage_used, throttling, dtu_consumption_percent, storage_used |
| Microsoft.Web/hostingEnvironments/multirolepools | averagememoryworkingset |
| Microsoft.Web/hostingEnvironments/workerpools | bytesreceived, httpqueuelength |

## <a name="how-equivalent-new-alert-rules-and-action-groups-are-created"></a>Hogyan egyenértékű új riasztási szabályok és Műveletcsoportok jönnek létre

Az áttelepítési eszköz megfelelő új riasztási szabályok és Műveletcsoportok alakítja át a klasszikus riasztási szabályok. A legtöbb klasszikus riasztási szabályok egyenértékű új riasztási szabályok is az ugyanazon a metrika, az azonos tulajdonságokkal rendelkező például `windowSize` és `aggregationType`. Vannak azonban bizonyos klasszikus riasztás szabályok vannak, amelyek egy másik, egyenértékű a metrika az új rendszer metrikákra vonatkozóan. A következő elvek vonatkoznak a migrálás a klasszikus riasztások, hacsak nincs megadva az alábbi szakaszban:

- **Gyakoriság**: Határozza meg, milyen gyakran ellenőrzi a klasszikus vagy új riasztási szabály a feltétel. A `frequency` klasszikus riasztási szabályok nem volt a felhasználó által konfigurálható, és mindig kivételével, amelyhez 1 perc volt az Application Insights-összetevők minden erőforrás esetében 5 perc volt. Ezért egyenértékű szabályok gyakoriságát is értéke 5 perc és 1 perc jelölik.
- **Összesítés típusa**: Határozza meg, hogyan a metrika összesített értéket jelenít meg az ablak a lényeges. A `aggregationType` ugyanazt klasszikus riasztások és a legtöbb metrika az új riasztások között is van. Bizonyos esetekben, mivel a metrika eltér klasszikus riasztások és új riasztásokat, egyenértékű `aggregationType` vagy a `primary Aggregation Type` definiálva a metrikával.
- **Egységek**: Vlastnost a mérőszám, amelyen a riasztás jön létre. Néhány egyenértékű metrikákhoz eltérő egységek. A küszöbérték igény szerint megfelelően módosul. Például ha az eredeti metrika másodpercből, egységek, de egyenértékű új metrika egységként ezredmásodperc, az eredeti küszöbértéket a rendszer megszorozza 1000 ugyanez a viselkedés biztosításához.
- **Ablakméret**: Az ablak, mely metrika keresztül adatokat összesíteni és a küszöbérték összehasonlításához határozza meg. A standard szintű `windowSize` értékek 5mins, 15mins, 30mins, mint 1 óra, 3 óra, 6 óra, 12 óra, 1 nap, nem történik változás arról, hogy a megfelelő új riasztási szabály. A más értékek, amelyik a legközelebb esik `windowSize` használandó van kiválasztva. A legtöbb ügyfél számára nem ez a változás nincs hatással. Az ügyfelek egy kis is a küszöbérték beolvasni a pontos viselkedést módosítania kell.

A következő szakaszokban azt részletezik, hogy rendelkezik egy másik, egyenértékű a metrika az új rendszer. Nem szerepel a bármilyen mérőszám, amely a klasszikus és az Új riasztási szabályok változatlan marad. Az új rendszer támogatott mérőszámok listája annak [Itt](metrics-supported.md).

### <a name="microsoftstorageaccountsservices"></a>Microsoft.StorageAccounts/services

A tárolási fiók szolgáltatások, például blob, table, fájl- és várólista a következő metrikák vannak leképezve egyenértékű metrikák alább látható módon:

| A klasszikus riasztások metrika | Az új riasztások egyenértékű metrika | Megjegyzések|
|--------------------------|---------------------------------|---------|
| AnonymousAuthorizationError| Tranzakciók metrika méretei "ResponseType" = "AuthorizationError" és "Hitelesítés" = "Névtelen"| |
| AnonymousClientOtherError | Tranzakciók metrika méretei "ResponseType" = "ClientOtherError" és "Hitelesítés" = "Névtelen" | |
| AnonymousClientTimeOutError| Tranzakciók metrika méretei "ResponseType" = "ClientTimeOutError" és "Hitelesítés" = "Névtelen" | |
| AnonymousNetworkError | Tranzakciók metrika méretei "ResponseType" = "NetworkError" és "Hitelesítés" = "Névtelen" | |
| AnonymousServerOtherError | Tranzakciók metrika méretei "ResponseType" = "ServerOtherError" és "Hitelesítés" = "Névtelen" | |
| AnonymousServerTimeOutError | Tranzakciók metrika méretei "ResponseType" = "ServerTimeOutError" és "Hitelesítés" = "Névtelen" | |
| AnonymousSuccess | Tranzakciók metrika méretei "ResponseType" = "Sikeres" és "Hitelesítés" = "Névtelen" | |
| AuthorizationError | Tranzakciók metrika méretei "ResponseType" = "AuthorizationError" | |
| AverageE2ELatency | SuccessE2ELatency | |
| AverageServerLatency | SuccessServerLatency | |
| Kapacitás | BlobCapacity | Használat `aggregationType` "átlagos", "last" helyett. A metrika csak érvényes Blob szolgáltatás |
| ClientOtherError | Tranzakciók metrika méretei "ResponseType" = "ClientOtherError"  | |
| ClientTimeoutError | Tranzakciók metrika méretei "ResponseType" = "ClientTimeOutError" | |
| ContainerCount | ContainerCount | Használat `aggregationType` "átlagos", "last" helyett. A metrika csak érvényes Blob szolgáltatás |
| NetworkError | Tranzakciók metrika méretei "ResponseType" = "NetworkError" | |
| ObjectCount | BlobCount| Használat `aggregationType` "átlagos", "last" helyett. A metrika csak érvényes Blob szolgáltatás |
| SASAuthorizationError | Tranzakciók metrika méretei "ResponseType" = "AuthorizationError" és "Hitelesítés" = "SAS" | |
| SASClientOtherError | Tranzakciók metrika méretei "ResponseType" = "ClientOtherError" és "Hitelesítés" = "SAS" | |
| SASClientTimeOutError | Tranzakciók metrika méretei "ResponseType" = "ClientTimeOutError" és "Hitelesítés" = "SAS" | |
| SASNetworkError | Tranzakciók metrika méretei "ResponseType" = "NetworkError" és "Hitelesítés" = "SAS" | |
| SASServerOtherError | Tranzakciók metrika méretei "ResponseType" = "ServerOtherError" és "Hitelesítés" = "SAS" | |
| SASServerTimeOutError | Tranzakciók metrika méretei "ResponseType" = "ServerTimeOutError" és "Hitelesítés" = "SAS" | |
| SASSuccess | Tranzakciók metrika méretei "ResponseType" = "Sikeres" és "Hitelesítés" = "SAS" | |
| ServerOtherError | Tranzakciók metrika méretei "ResponseType" = "ServerOtherError" | |
| ServerTimeOutError | Tranzakciók metrika méretei "ResponseType" = "ServerTimeOutError"  | |
| Siker | Tranzakciók metrika méretei "ResponseType" = "Success" | |
| TotalBillableRequests| Tranzakciók | |
| TotalEgress | Kimenő forgalom | |
| TotalIngress | Bejövő forgalom | |
| TotalRequests | Tranzakciók | |

### <a name="microsoftinsightscomponents"></a>Microsoft.insights/components

Az Application Insights az egyenértékű mérőszám játszik alább látható módon:

| A klasszikus riasztások metrika | Az új riasztások egyenértékű metrika | Megjegyzések|
|--------------------------|---------------------------------|---------|
| availability.availabilityMetric.value | availabilityResults/availabilityPercentage|   |
| availability.durationMetric.value | availabilityResults/duration| Eredeti küszöbérték szorozza 1000, klasszikus metrikus egység másodpercek alatt, és az új egyik ezredmásodpercet fejez ki.  |
| basicExceptionBrowser.count | kivételek és böngésző|  Használat `aggregationType` "count" helyett "sum". |
| basicExceptionServer.count | kivételek és a kiszolgáló| Használat `aggregationType` "count" helyett "sum".  |
| clientPerformance.clientProcess.value | browserTimings/processingDuration| Eredeti küszöbérték szorozza 1000, klasszikus metrikus egység másodpercek alatt, és az új egyik ezredmásodpercet fejez ki.  |
| clientPerformance.networkConnection.value | browserTimings/networkDuration|  Eredeti küszöbérték szorozza 1000, klasszikus metrikus egység másodpercek alatt, és az új egyik ezredmásodpercet fejez ki. |
| clientPerformance.receiveRequest.value | browserTimings/receiveDuration| Eredeti küszöbérték szorozza 1000, klasszikus metrikus egység másodpercek alatt, és az új egyik ezredmásodpercet fejez ki.  |
| clientPerformance.sendRequest.value | browserTimings/sendDuration| Eredeti küszöbérték szorozza 1000, klasszikus metrikus egység másodpercek alatt, és az új egyik ezredmásodpercet fejez ki.  |
| clientPerformance.total.value | browserTimings/totalDuration| Eredeti küszöbérték szorozza 1000, klasszikus metrikus egység másodpercek alatt, és az új egyik ezredmásodpercet fejez ki.  |
| performanceCounter.available_bytes.value | performanceCounters/memoryAvailableBytes|   |
| performanceCounter.io_data_bytes_per_sec.value | performanceCounters/processIOBytesPerSecond|   |
| performanceCounter.number_of_exceps_thrown_per_sec.value | performanceCounters/exceptionsPerSecond|   |
| performanceCounter.percentage_processor_time_normalized.value | performanceCounters/processCpuPercentage|   |
| performanceCounter.percentage_processor_time.value | performanceCounters/processCpuPercentage| Küszöbérték eredeti metrika az összes magon volt, és új metrika egy magot van normalizálva megfelelően módosítani kell. Áttelepítési eszköz küszöbértékek nem változik.  |
| performanceCounter.percentage_processor_total.value | performanceCounters/processorCpuPercentage|   |
| performanceCounter.process_private_bytes.value | performanceCounters/processPrivateBytes|   |
| performanceCounter.request_execution_time.value | performanceCounters/requestExecutionTime|   |
| performanceCounter.requests_in_application_queue.value | performanceCounters/requestsInQueue|   |
| performanceCounter.requests_per_sec.value | performanceCounters/requestsPerSecond|   |
| Request.DURATION | kérések/időtartama| Eredeti küszöbérték szorozza 1000, klasszikus metrikus egység másodpercek alatt, és az új egyik ezredmásodpercet fejez ki.  |
| Request.rate | kérések/arány|   |
| requestFailed.count | sikertelen kérések /| Használat `aggregationType` "count" helyett "sum".   |
| View.Count | pageViews/count| Használat `aggregationType` "count" helyett "sum".   |

### <a name="how-equivalent-action-groups-are-created"></a>Hogyan megfelelő Műveletcsoportok jönnek létre.

Klasszikus riasztási szabályok volt-e-mailt, webhookot, az logic app és a runbook-műveletek kapcsolódik a riasztás a szabály maga. Új riasztási szabályok Műveletcsoportok, amelyek felhasználhatók több riasztási szabályok használata. Az áttelepítési eszköz ugyanazokat a műveleteket attól függetlenül, hány riasztási szabályokat használ a művelet egyetlen művelettel csoportot hoz létre. Az áttelepítési eszköz által létrehozott Műveletcsoportok "Migrated_AG *" elnevezési formátumot használja.

## <a name="rollout-phases"></a>Bevezetési fázisok

Az áttelepítési eszköz bevezetéséről szakaszában, az ügyfelek számára, amely a klasszikus riasztási szabályok használata. Az előfizetés-tulajdonosokat egy e-mailt fog kapni, ha az előfizetés rendelkezésre áll a eszközzel kell áttelepíteni.

> [!NOTE]
> Az eszköz fázisban tesszük elérhetővé, mivel láthatja, hogy néhány előfizetéséhez még nem kész korai szakaszaiban kell áttelepíteni.

Az előfizetések többsége jelenleg jelölt készen áll az áttelepítéshez. Csak olyan előfizetéseket, amelyek klasszikus riasztások a következő erőforrástípusok még nem áll készen a migrálás.

- Microsoft.classicCompute/domainNames/slots/roles
- Microsoft.documentDB/databases
- Microsoft.insights/components

## <a name="who-can-trigger-the-migration"></a>Ki is aktiválhatja a migrálás?

Bármely felhasználó, aki közreműködői figyelés, a beépített szerepkör rendelkezik az előfizetés szintjén is aktiválhatja az áttelepítés. Egy egyéni biztonsági szerepkört a következő engedélyekkel rendelkező felhasználók is kiválthatják az áttelepítés:

- */read
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*

> [!NOTE]
> Mellett engedélyek fent, az előfizetés emellett Microsoft.AlertsManagement erőforrás-szolgáltatónál kell regisztrálni. Ez a hiba Anomáliadetektálási riasztás az Application Insights sikeres áttelepítéséhez szükséges. 

## <a name="common-problems-and-remedies"></a>Gyakori problémák és kártérítése

Miután [aktiválása az áttelepítés](alerts-using-migration-tool.md), úgy, hogy az áttelepítés akkor fejeződött be, vagy ha semmilyen teendője, a megadott címre e-mailt fog kapni. Ez a szakasz néhány gyakori problémát és azok kezelésére ismerteti.

### <a name="validation-failed"></a>Nem sikerült ellenőrizni a

Néhány legutóbbi módosításainak klasszikus riasztási szabályok az előfizetésében, mert az előfizetés nem telepíthetők át. Ez a probléma csak átmenetileg létezik. Az áttelepítés után a migrálás állapota Visszalépés újraindíthatja **migrálásra kész** néhány nap alatt.

### <a name="policy-or-scope-lock-preventing-us-from-migrating-your-rules"></a>A szabályok áttelepítés megakadályozza a házirend vagy -hatókörön zárolása

Az áttelepítés részeként jön létre új metrikákhoz kapcsolódó riasztások és Műveletcsoportok új, és majd a klasszikus riasztási szabály törölve lesz. Van azonban egy házirend vagy a hatókör zárolása megakadályozza-erőforrások létrehozását. A szabályzat vagy a hatókör zárolást függően néhány vagy összes szabályt meghajtóprogram nem telepíthető át. Ez a probléma megoldható a hatókör zárolása vagy a házirend eltávolítása ideiglenesen és aktiválása megismételni az áttelepítést.

## <a name="next-steps"></a>További lépések

- [Az áttelepítési eszköz használata](alerts-using-migration-tool.md)
- [Az áttelepítés előkészítése](alerts-prepare-migration.md)
