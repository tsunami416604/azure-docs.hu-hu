---
title: Ismerje meg, hogy az önkéntes áttelepítési eszköz hogyan működik Azure Monitor riasztások esetén
description: Ismerje meg, hogy a riasztások áttelepítési eszköze hogyan működik és hibaelhárítási problémákkal rendelkezik.
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: yalavi
author: yalavi
ms.subservice: alerts
ms.openlocfilehash: 8cc77d13567910797cd519ac193b848f3ea434da
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79274813"
---
# <a name="understand-how-the-migration-tool-works"></a>Az áttelepítési eszköz működésének megismerése

Amint azt [korábban bejelentettük](monitoring-classic-retirement.md), a klasszikus riasztások Azure monitor 2019 (eredetileg a 2019. június 30-ig) megszűnnek. Az áttelepítési eszköz a Azure Portalban olyan ügyfelek számára érhető el, akik klasszikus riasztási szabályokat használnak, és magukat a migrációt szeretnék elindítani.

Ez a cikk azt ismerteti, hogyan működik az önkéntes áttelepítési eszköz. Emellett ismerteti a gyakori problémákra vonatkozó jogorvoslatokat is.

> [!NOTE]
> Az áttelepítési eszköz késése miatt a klasszikus riasztások áttelepítésének lejárati dátuma [2019 augusztus 31-ig](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) , az eredetileg bejelentett, 2019. június 30-ig.

## <a name="classic-alert-rules-that-will-not-be-migrated"></a>Klasszikus riasztási szabályok, amelyek nem lesznek áttelepítve

> [!IMPORTANT]
> Az áttelepítés nem érinti a tevékenységek naplójának riasztásait (a szolgáltatás állapotával kapcsolatos riasztásokat is beleértve) és a naplózási riasztásokat. Az áttelepítés csak az [itt](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform)ismertetett klasszikus riasztási szabályokra vonatkozik.

Bár az eszköz szinte az összes [klasszikus riasztási szabályt](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform)át tudja telepíteni, bizonyos kivételek vannak. A következő riasztási szabályok nem lesznek áttelepítve az eszköz használatával (vagy az automatikus áttelepítés 2019. szeptemberének indításakor):

- Klasszikus riasztási szabályok a virtuális gépek vendég metrikái (Windows és Linux rendszereken egyaránt). A [riasztási szabályok újbóli létrehozásával kapcsolatos útmutatásért](#guest-metrics-on-virtual-machines) lásd a jelen cikk későbbi, új metrikai riasztások című részében.
- Klasszikus riasztási szabályok klasszikus tárolási mérőszámokhoz. Tekintse [meg a klasszikus Storage-fiókok figyelésével kapcsolatos útmutatót](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/).
- Klasszikus riasztási szabályok néhány Storage-fiók metrikáján. A [részleteket](#storage-account-metrics) a cikk későbbi részében találja.
- A klasszikus riasztási szabályok egyes Cosmos DB metrikák esetében. A [részleteket](#cosmos-db-metrics) a cikk későbbi részében találja.
- Klasszikus riasztási szabályok a klasszikus virtuális gépek és a Cloud Services-metrikák (Microsoft. ClassicCompute/virtualMachines és Microsoft. ClassicCompute/tartománynév/bővítőhely/szerepkörök) esetében. A [részleteket](#classic-compute-metrics) a cikk későbbi részében találja.

Ha az előfizetése klasszikus szabályokkal rendelkezik, azokat manuálisan kell áttelepítenie. Mivel nem tudunk automatikus áttelepítést biztosítani, az ilyen típusú meglévő, klasszikus metrikai riasztások továbbra is a 2020 júniusáig fognak működni. Ez a bővítmény időt biztosít az új riasztásokra való áttérésre. Továbbra is létrehozhat új klasszikus riasztásokat a fentiekben felsorolt kivételek szerint, 2020-ig. Azonban minden más esetben nem hozhatók létre új klasszikus riasztások augusztus 2019. után.

> [!NOTE]
> A fent felsorolt kivételek mellett, ha a klasszikus riasztási szabályok érvénytelenek, azaz [elavult metrikákkal](#classic-alert-rules-on-deprecated-metrics) vagy törölt erőforrásokkal rendelkeznek, nem lesznek áttelepítve, és a szolgáltatás kivonása után nem lesznek elérhetők.

### <a name="guest-metrics-on-virtual-machines"></a>Vendég metrikák a virtuális gépeken

Ahhoz, hogy új metrikai riasztásokat hozzon létre a vendég metrikák esetében, a vendég metrikákat a Azure Monitor egyéni metrikák tárolójába kell elküldeni. Kövesse ezeket az utasításokat a Azure Monitor fogadó diagnosztikai beállításokban való engedélyezéséhez:

- [Vendég metrikák engedélyezése Windows rendszerű virtuális gépekhez](collect-custom-metrics-guestos-resource-manager-vm.md)
- [Vendég mérőszámok engedélyezése Linux rendszerű virtuális gépekhez](collect-custom-metrics-linux-telegraf.md)

A lépések elvégzése után létrehozhat új metrikai riasztásokat a vendég metrikák használatával. Az új metrikai riasztások létrehozása után törölheti a klasszikus riasztásokat is.

### <a name="storage-account-metrics"></a>Tárfiókok mérőszámai

A rendszer minden klasszikus riasztást áttelepíthet a tárolási fiókokon, kivéve a riasztásokat a következő metrikák esetén:

- PercentAuthorizationError
- PercentClientOtherError
- Percentnetworkerror értéket mutatnak
- PercentServerOtherError
- PercentSuccess
- Percentthrottlingerror értéket mutatnak
- Percenttimeouterror értéket mutatnak
- AnonymousThrottlingError
- SASThrottlingError
- ThrottlingError

A rendszer a klasszikus riasztási szabályokat a [régi és az új tárolási mérőszámok közötti leképezés](https://docs.microsoft.com/azure/storage/common/storage-metrics-migration#metrics-mapping-between-old-metrics-and-new-metrics)alapján kell áttelepíteni. A küszöbértékeket megfelelően módosítani kell, mert az elérhető új metrika abszolút.

A AnonymousThrottlingError, a SASThrottlingError és a ThrottlingError klasszikus riasztási szabályait két új riasztásra kell bontani, mert nincs olyan kombinált metrika, amely ugyanazokat a funkciókat biztosítja. A küszöbértékeket megfelelően ki kell igazítani.

### <a name="cosmos-db-metrics"></a>Cosmos DB-metrikák

Cosmos DB metrikákkal kapcsolatos klasszikus riasztások áttelepíthetők, kivéve a következő metrikákkal kapcsolatos riasztásokat:

- Másodpercenkénti átlagos kérelmek
- Konzisztenciaszint
- Http 2xx
- Http-3xx
- Http 400
- HTTP 401
- Belső kiszolgálóhiba
- Percenként felhasznált maximális RUPM
- Maximális RUs másodpercenként
- Sikertelen kérelmek Mongo száma
- Sikertelen kérelmek Mongo törlése
- Mongo – sikertelen kérelmek beszúrása
- Egyéb sikertelen kérelmek Mongo
- Mongo egyéb kérések díja
- Mongo egyéb kérelmek gyakorisága
- Mongo-lekérdezés sikertelen kérelmei
- Mongo-frissítési sikertelen kérelmek
- Megfigyelt olvasási késés
- Megfigyelt írási késés
- Szolgáltatás rendelkezésre állása
- Tárkapacitás
- Szabályozott kérelmek
- Összes kérelem

Másodpercenkénti kérelmek másodpercenkénti száma, a konzisztencia szintje, a percenként felhasznált maximális RUPM, a másodpercenkénti maximális mennyiség, a megfigyelt olvasási késés, a megfigyelt írási késés, a tárolási kapacitás jelenleg nem érhető el az [új rendszeren](metrics-supported.md#microsoftdocumentdbdatabaseaccounts).

A kérelmek metrikái, például a http-2xx, a http-3xx, a http 400, a HTTP 401, a belső kiszolgálóhiba, a szolgáltatás rendelkezésre állása, a szabályozott kérelmek és az összes kérelem nem települ át, mert a kérések számlálása eltérő a klasszikus metrikák és az új metrikák között. Ezeket a riasztásokat manuálisan újra létre kell hozni a küszöbértékekkel korrigálva.

A sikertelen kérelmek Mongo tartozó riasztásokat több riasztásra kell bontani, mert nincs olyan kombinált metrika, amely ugyanazt a funkciót biztosítja. A küszöbértékeket megfelelően ki kell igazítani.

### <a name="classic-compute-metrics"></a>Klasszikus számítási mérőszámok

A klasszikus számítási metrikákkal kapcsolatos riasztásokat a rendszer nem telepíti át az áttelepítési eszköz használatával, mivel a klasszikus számítási erőforrások még nem támogatottak az új riasztásokkal. A jövőben új riasztások jelennek meg az adott erőforrástípusok esetében. Ha ez elérhető, az ügyfeleknek új, egyenértékű riasztási szabályokat kell létrehozniuk a klasszikus riasztási szabályok alapján a 2020. június előtt.

### <a name="classic-alert-rules-on-deprecated-metrics"></a>Klasszikus riasztási szabályok elavult metrikák esetén

Ezek a klasszikus riasztási szabályok a korábban támogatott metrikák esetében, de végül elavultak voltak. Előfordulhat, hogy az ügyfél kis hányada érvénytelen klasszikus riasztási szabályokat tartalmaz az ilyen mérőszámokra vonatkozóan. Mivel ezek a riasztási szabályok érvénytelenek, nem lesznek áttelepítve.

| Erőforrás típusa| Elavult metrika (ek) |
|-------------|----------------- |
| Microsoft.DBforMySQL/servers | compute_consumption_percent, compute_limit |
| Microsoft.DBforPostgreSQL/servers | compute_consumption_percent, compute_limit |
| Microsoft.Network/publicIPAddresses | defaultddostriggerrate |
| Microsoft.SQL/servers/databases | service_level_objective, storage_limit, storage_used, throttling, dtu_consumption_percent, storage_used |
| Microsoft.Web/hostingEnvironments/multirolepools | averagememoryworkingset |
| Microsoft.Web/hostingEnvironments/workerpools | BytesReceived, httpqueuelength |

## <a name="how-equivalent-new-alert-rules-and-action-groups-are-created"></a>Hogyan hozhatók létre egyenértékű új riasztási szabályok és műveleti csoportok

Az áttelepítési eszköz a klasszikus riasztási szabályokat egyenértékű új riasztási szabályokkal és műveleti csoportokkal alakítja át. A klasszikus riasztási szabályok többsége esetében az egyenértékű új riasztási szabályok ugyanazon a metrikán vannak, mint a `windowSize` és az `aggregationType`. Van azonban néhány klasszikus riasztási szabály olyan metrikák esetében, amelyek eltérő, egyenértékű metrikával rendelkeznek az új rendszeren. A klasszikus riasztások áttelepítésére az alábbi alapelvek érvényesek, kivéve, ha az alábbi szakaszban szerepel:

- **Gyakoriság**: azt határozza meg, hogy egy klasszikus vagy új riasztási szabály milyen gyakran ellenőrizze a feltételt. A klasszikus riasztási szabályok `frequency` a felhasználó nem konfigurálható, és mindig 5 perc az összes erőforrástípus számára, kivéve azokat a Application Insights összetevőket, amelyekhez 1 perc volt. Ezért az egyenértékű szabályok gyakorisága 5 perc és 1 perc is lehet.
- **Összesítés típusa**: meghatározza, hogy a metrika hogyan legyen összesítve az adott ablakban. A `aggregationType` a klasszikus riasztások és a legtöbb mérőszámhoz tartozó új riasztások között is megegyeznek. Bizonyos esetekben, mivel a metrika különbözik a klasszikus riasztások és az új riasztások, az egyenértékű `aggregationType` vagy a metrikához definiált `primary Aggregation Type` használatával.
- **Units**: a riasztást létrehozó metrika tulajdonsága. Egyes egyenértékű mérőszámok eltérő egységekkel rendelkeznek. A küszöbértéket szükség szerint korrigálni kell. Ha például az eredeti metrika másodperceket tartalmaz egységként, de az egyenértékű új metrika egységként Ezredmásodperctel rendelkezik, az eredeti küszöbértéket a 1000-es értékkel kell megszorozni, hogy az azonos viselkedést biztosítson.
- **Ablak mérete**: meghatározza azt az ablakot, amely alatt a metrikai adatok összesítése történik a küszöbértékkel való összehasonlításhoz. A standard `windowSize` értékek, például a 5mins, a 15mins, a 30mins, a 1óra, a 3hours, a 6 óra, a 12 óra, 1 nap, az egyenértékű új riasztási szabály esetében nem történt változás. Más értékek esetén a legközelebbi `windowSize` lesz kiválasztva. A legtöbb ügyfél esetében ez a változás nem befolyásolja a változást. Az ügyfelek kis hányada esetében előfordulhat, hogy a küszöbértéket úgy kell megcsípni, hogy pontosan ugyanazt a viselkedést kapja.

A következő fejezetekben részletesen ismertetjük az új rendszer különböző, egyenértékű metrikával rendelkező mérőszámait. A klasszikus és az új riasztási szabályoknál változatlan maradó mérőszámok nem szerepelnek a felsorolásban. [Itt](metrics-supported.md)megtalálhatja az új rendszer által támogatott mérőszámok listáját.

### <a name="microsoftstorageaccountsservices"></a>Microsoft.StorageAccounts/services

A Storage-fiókokhoz, például a blobhoz, a táblához, a fájlokhoz és a várólistákhoz a következő metrikák az alábbi módon vannak leképezve az egyenértékű mérőszámokra:

| Metrika klasszikus riasztásokban | Egyenértékű metrika az új riasztásokban | Megjegyzések|
|--------------------------|---------------------------------|---------|
| AnonymousAuthorizationError| Tranzakciós metrika dimenziókkal "ResponseType" = "AuthorizationError" és "hitelesítés" = "névtelen"| |
| AnonymousClientOtherError | Tranzakciós metrika dimenziókkal "ResponseType" = "ClientOtherError" és "hitelesítés" = "névtelen" | |
| AnonymousClientTimeOutError| Tranzakciós metrika dimenziókkal "ResponseType" = "ClientTimeOutError" és "hitelesítés" = "névtelen" | |
| AnonymousNetworkError | Tranzakciós metrika dimenziókkal "ResponseType" = "NetworkError" és "hitelesítés" = "névtelen" | |
| AnonymousServerOtherError | Tranzakciós metrika dimenziókkal "ResponseType" = "ServerOtherError" és "hitelesítés" = "névtelen" | |
| AnonymousServerTimeOutError | Tranzakciós metrika dimenziókkal "ResponseType" = "ServerTimeOutError" és "hitelesítés" = "névtelen" | |
| AnonymousSuccess | Tranzakciós metrika dimenziókkal "ResponseType" = "sikeres" és "hitelesítés" = "névtelen" | |
| AuthorizationError | Tranzakciós metrika a következő dimenziókkal: "ResponseType" = "AuthorizationError" | |
| AverageE2ELatency | SuccessE2ELatency | |
| AverageServerLatency | SuccessServerLatency | |
| Kapacitás | BlobCapacity | Használja az "átlag" `aggregationType` a "Last" helyett. A metrika csak a blob-szolgáltatásokra vonatkozik |
| ClientOtherError | Tranzakciós metrika a következő dimenziókkal: "ResponseType" = "ClientOtherError"  | |
| ClientTimeoutError | Tranzakciós metrika a következő dimenziókkal: "ResponseType" = "ClientTimeOutError" | |
| ContainerCount | ContainerCount | Használja az "átlag" `aggregationType` a "Last" helyett. A metrika csak a blob-szolgáltatásokra vonatkozik |
| NetworkError | Tranzakciós metrika a következő dimenziókkal: "ResponseType" = "NetworkError" | |
| ObjectCount | BlobCount| Használja az "átlag" `aggregationType` a "Last" helyett. A metrika csak a blob-szolgáltatásokra vonatkozik |
| SASAuthorizationError | Tranzakciós metrika a következő dimenziókkal: "ResponseType" = "AuthorizationError" és "Authentication" = "SAS" | |
| SASClientOtherError | Tranzakciós metrika a következő dimenziókkal: "ResponseType" = "ClientOtherError" és "Authentication" = "SAS" | |
| SASClientTimeOutError | Tranzakciós metrika a következő dimenziókkal: "ResponseType" = "ClientTimeOutError" és "Authentication" = "SAS" | |
| SASNetworkError | Tranzakciós metrika a következő dimenziókkal: "ResponseType" = "NetworkError" és "Authentication" = "SAS" | |
| SASServerOtherError | Tranzakciós metrika a következő dimenziókkal: "ResponseType" = "ServerOtherError" és "Authentication" = "SAS" | |
| SASServerTimeOutError | Tranzakciós metrika a következő dimenziókkal: "ResponseType" = "ServerTimeOutError" és "Authentication" = "SAS" | |
| SASSuccess | Tranzakciós metrika dimenziókkal "ResponseType" = "sikeres" és "hitelesítés" = "SAS" | |
| ServerOtherError | Tranzakciós metrika a következő dimenziókkal: "ResponseType" = "ServerOtherError" | |
| ServerTimeOutError | Tranzakciós metrika a következő dimenziókkal: "ResponseType" = "ServerTimeOutError"  | |
| Sikeres | Tranzakciós metrika a következő dimenziókkal: "ResponseType" = "sikeres" | |
| TotalBillableRequests| Tranzakciók | |
| TotalEgress | Kimenő forgalom | |
| TotalIngress | Bejövő forgalom | |
| TotalRequests | Tranzakciók | |

### <a name="microsoftinsightscomponents"></a>Microsoft.insights/components

Application Insights esetén az egyenértékű mérőszámok az alábbiak szerint jelennek meg:

| Metrika klasszikus riasztásokban | Egyenértékű metrika az új riasztásokban | Megjegyzések|
|--------------------------|---------------------------------|---------|
| rendelkezésre állás. availabilityMetric. Value | availabilityResults/availabilityPercentage|   |
| rendelkezésre állás. durationMetric. Value | availabilityResults/időtartam| Az eredeti küszöbértéket 1000-ként szorozzuk meg, mivel a klasszikus metrika egysége másodpercben van, és az új érték ezredmásodpercben van.  |
| basicExceptionBrowser.count | kivételek/böngésző|  A "Sum" helyett használja a "Count" `aggregationType`. |
| basicExceptionServer.count | kivételek/kiszolgáló| A "Sum" helyett használja a "Count" `aggregationType`.  |
| clientPerformance. clientProcess. Value | browserTimings/processingDuration| Az eredeti küszöbértéket 1000-ként szorozzuk meg, mivel a klasszikus metrika egysége másodpercben van, és az új érték ezredmásodpercben van.  |
| clientPerformance.networkConnection.value | browserTimings/networkDuration|  Az eredeti küszöbértéket 1000-ként szorozzuk meg, mivel a klasszikus metrika egysége másodpercben van, és az új érték ezredmásodpercben van. |
| clientPerformance.receiveRequest.value | browserTimings/receiveDuration| Az eredeti küszöbértéket 1000-ként szorozzuk meg, mivel a klasszikus metrika egysége másodpercben van, és az új érték ezredmásodpercben van.  |
| clientPerformance.sendRequest.value | browserTimings/sendDuration| Az eredeti küszöbértéket 1000-ként szorozzuk meg, mivel a klasszikus metrika egysége másodpercben van, és az új érték ezredmásodpercben van.  |
| clientPerformance. Total. Value | browserTimings/totalDuration| Az eredeti küszöbértéket 1000-ként szorozzuk meg, mivel a klasszikus metrika egysége másodpercben van, és az új érték ezredmásodpercben van.  |
| performanceCounter. available_bytes. Value | performanceCounters/memoryAvailableBytes|   |
| performanceCounter.io_data_bytes_per_sec.value | performanceCounters/processIOBytesPerSecond|   |
| performanceCounter.number_of_exceps_thrown_per_sec.value | performanceCounters/exceptionsPerSecond|   |
| performanceCounter.percentage_processor_time_normalized.value | performanceCounters/processCpuPercentage|   |
| performanceCounter.percentage_processor_time.value | performanceCounters/processCpuPercentage| A küszöbértéket megfelelő módon módosítani kell, mivel az eredeti metrika az összes mag esetében módosult, és az új metrika egy mag szerint van normalizálva. Az áttelepítési eszköz nem változtatja meg a küszöbértékeket.  |
| performanceCounter.percentage_processor_total.value | performanceCounters/processorCpuPercentage|   |
| performanceCounter. process_private_bytes. Value | performanceCounters/processPrivateBytes|   |
| performanceCounter.request_execution_time.value | performanceCounters/requestExecutionTime|   |
| performanceCounter.requests_in_application_queue.value | performanceCounters/requestsInQueue|   |
| performanceCounter. requests_per_sec. Value | performanceCounters/requestsPerSecond|   |
| kérelem. időtartam | kérelmek/időtartam| Az eredeti küszöbértéket 1000-ként szorozzuk meg, mivel a klasszikus metrika egysége másodpercben van, és az új érték ezredmásodpercben van.  |
| kérelem. arány | kérelmek/díjszabás|   |
| requestFailed. Count | kérelmek/sikertelen| A "Sum" helyett használja a "Count" `aggregationType`.   |
| megtekintés. darabszám | Oldalmegtekintések/darabszám| A "Sum" helyett használja a "Count" `aggregationType`.   |

### <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

Cosmos DB esetén az egyenértékű mérőszámok az alábbiak szerint jelennek meg:

| Metrika klasszikus riasztásokban | Egyenértékű metrika az új riasztásokban | Megjegyzések|
|--------------------------|---------------------------------|---------|
| AvailableStorage     |AvailableStorage|   |
| Adatméret | DataUsage| |
| Dokumentumok száma | DocumentCount||
| Index mérete | IndexUsage||
| Mongo száma – kérelem díja| MongoRequestCharge a "CommandName" = "Count" dimenzióval||
| Mongo száma | MongoRequestsCount a "CommandName" = "Count" dimenzióval||
| Mongo-törlési kérés díja | MongoRequestCharge a "CommandName" = "Delete" dimenzióval||
| Mongo-törlési kérelmek gyakorisága | MongoRequestsCount a "CommandName" = "Delete" dimenzióval||
| Mongo-beszúrási kérelem díja | MongoRequestCharge a "CommandName" = "Insert" dimenzióval||
| Mongo-beszúrási kérelmek gyakorisága | MongoRequestsCount a "CommandName" = "Insert" dimenzióval||
| Mongo-lekérdezési kérelem díja | MongoRequestCharge a "CommandName" = "Find" dimenzióval||
| Mongo-lekérdezési kérelmek gyakorisága | MongoRequestsCount a "CommandName" = "Find" dimenzióval||
| Mongo frissítési kérelmének díja | MongoRequestCharge a "CommandName" = "Update" dimenzióval||
| A szolgáltatás nem érhető el| ServiceAvailability||
| TotalRequestUnits | TotalRequestUnits||

### <a name="how-equivalent-action-groups-are-created"></a>Az egyenértékű műveleti csoportok létrehozása

A klasszikus riasztási szabályok a riasztási szabályhoz kötött e-mail-, webhook-, logikai alkalmazás-és runbook-műveletekkel rendelkeznek. Az új riasztási szabályok olyan műveleti csoportokat használnak, amelyek többször is felhasználhatók több riasztási szabályban. Az áttelepítési eszköz egyetlen műveleti csoportot hoz létre ugyanahhoz a műveletekhez, függetlenül attól, hogy hány riasztási szabály használja a műveletet. Az áttelepítési eszköz által létrehozott műveleti csoportok a következő elnevezési formátumot használják: "Migrated_AG *".

> [!NOTE]
> A klasszikus riasztások honosított e-maileket küldenek a klasszikus rendszergazdák helyi beállításai alapján, ha a klasszikus rendszergazdai szerepkörök bejelentésére szolgálnak. Az új riasztási e-maileket műveleti csoportokon keresztül küldik el, és csak angol nyelven érhetők el.

## <a name="rollout-phases"></a>Bevezetési fázisok

Az áttelepítési eszköz fázisokban van a klasszikus riasztási szabályokat használó ügyfeleknek. Az előfizetés tulajdonosai e-mailt kapnak, amikor az előfizetés készen áll az áttelepítésre az eszköz használatával.

> [!NOTE]
> Mivel az eszköz fázisokban van, láthatja, hogy néhány előfizetés még nem áll készen a korai fázisokban való áttelepítésre.

Az előfizetések többsége jelenleg készként van megjelölve az áttelepítésre. Csak azok az előfizetések állnak készen az áttelepítésre, amelyek klasszikus riasztásokkal rendelkeznek a következő erőforrás-típusokon.

- Microsoft.classicCompute/domainNames/slots/roles
- Microsoft.insights/components

## <a name="who-can-trigger-the-migration"></a>Kik indíthatják el az áttelepítést?

Minden olyan felhasználó, aki rendelkezik az előfizetés szintjén a figyelési közreműködő beépített szerepkörével, aktiválhatja az áttelepítést. Azok a felhasználók, akik egyéni szerepkörrel rendelkeznek a következő engedélyekkel, az áttelepítést is aktiválhatja:

- */read
- Microsoft. bepillantások/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft. bepillantások/metricAlerts/*
- Microsoft. AlertsManagement/smartDetectorAlertRules/*

> [!NOTE]
> A fenti engedélyek mellett az előfizetését emellett regisztrálni kell a Microsoft. AlertsManagement erőforrás-szolgáltatóban. Erre azért van szükség, hogy sikeresen áttelepítse a hiba-anomáliák riasztásait Application Insightson. 

## <a name="common-problems-and-remedies"></a>Gyakori problémák és jogorvoslatok

Miután [elindította az áttelepítést](alerts-using-migration-tool.md), e-mailt fog kapni a megadott címekről, hogy értesítést kapjon arról, hogy a Migrálás befejeződött, vagy ha bármilyen műveletre szükség van. Ez a szakasz néhány gyakori problémát és azok kezelését ismerteti.

### <a name="validation-failed"></a>Az érvényesítés sikertelen

Az előfizetés klasszikus riasztási szabályainak legutóbbi változásai miatt az előfizetés nem telepíthető át. Ez a probléma ideiglenes. Újraindíthatja az áttelepítést, miután az áttelepítés állapota néhány nap múlva újra **készen áll az áttelepítésre** .

### <a name="scope-lock-preventing-us-from-migrating-your-rules"></a>A hatókör-zárolás megakadályozza a szabályok áttelepítését

A Migrálás részeként új metrikai riasztások és új műveleti csoportok jönnek létre, és a rendszer törli a klasszikus riasztási szabályokat. A hatókör-zárolás azonban megakadályozhatja az erőforrások létrehozását és törlését. A hatókör-zárolástól függően előfordulhat, hogy egy vagy több szabály nem telepíthető át. A probléma megoldásához távolítsa el az [áttelepítési eszközben](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)felsorolt előfizetés, erőforráscsoport vagy erőforrás hatókör-zárolását, és indítsa el újra az áttelepítést. A hatókör-zárolás nem tiltható le, és az áttelepítési folyamat időtartama alatt el kell távolítani. [További információ a hatóköri zárolások kezeléséről](../../azure-resource-manager/management/lock-resources.md#portal).

### <a name="policy-with-deny-effect-preventing-us-from-migrating-your-rules"></a>A "megtagadás" hatású szabályzat megakadályozza a szabályok áttelepítését

A Migrálás részeként új metrikai riasztások és új műveleti csoportok jönnek létre, és a rendszer törli a klasszikus riasztási szabályokat. Egy házirend azonban megakadályozza, hogy erőforrásokat hozzon létre. A házirendtől függően előfordulhat, hogy egy vagy több szabályt nem lehetett migrálni. A folyamatot blokkoló házirendek megjelennek az [áttelepítési eszközben](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel). Oldja meg a problémát a következők bármelyikével:

- Az előfizetések vagy erőforráscsoportok kizárása az áttelepítési folyamat időtartamára a szabályzat-hozzárendelésből. [További információ a házirendek Kizárási hatókörének kezeléséről](../../governance/policy/tutorials/create-and-manage.md#exempt-a-non-compliant-or-denied-resource-using-exclusion).
- A "naplózás" vagy a "Hozzáfűzés" effektus eltávolítása vagy módosítása (ami például a hiányzó címkékkel kapcsolatos problémák megoldására szolgál). [További információ a házirendek hatásának kezeléséről](../../governance/policy/concepts/definition-structure.md#policy-rule).

## <a name="next-steps"></a>Következő lépések

- [A migrálási eszköz használata](alerts-using-migration-tool.md)
- [Felkészülés az áttelepítésre](alerts-prepare-migration.md)
