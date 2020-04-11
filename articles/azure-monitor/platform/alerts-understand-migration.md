---
title: Az Azure Monitor riasztásaiáttelepítési eszközének ismertetése
description: Ismerje meg, hogyan működik a riasztások áttelepítési eszköz, és elháríthatja a problémákat.
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: yalavi
author: yalavi
ms.subservice: alerts
ms.openlocfilehash: d31c856e17348c23ad61130869af6ae440d3050d
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114305"
---
# <a name="understand-how-the-migration-tool-works"></a>A migrálási eszköz működésének ismertetése

Ahogy [azt korábban bejelentettük,](monitoring-classic-retirement.md)az Azure Monitor klasszikus riasztásait 2019. augusztus 31-ig (eredetileg 2019. június 30-ig) vonják ki. Az Azure Portalon elérhető egy áttelepítési eszköz azoknak az ügyfeleknek, akik klasszikus riasztási szabályokat használnak, és akik maguk szeretnék elindítani az áttelepítést.

Ez a cikk bemutatja, hogyan működik az önkéntes áttelepítési eszköz. Azt is leírja, jogorvoslati néhány gyakori probléma.

> [!NOTE]
> A migrációs eszköz bevezetésének késedelmi ideje miatt a klasszikus riasztások áttelepítésének nyugdíjazási dátumát 2019. június 30-án, az eredetileg bejelentett időponttól [2019. augusztus 31-ig meghosszabbították.](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/)

## <a name="classic-alert-rules-that-will-not-be-migrated"></a>Nem migrált klasszikus riasztási szabályok

> [!IMPORTANT]
> A tevékenységnapló-riasztásokat (beleértve a szolgáltatásállapot-riasztásokat) és a naplóriasztásokat az áttelepítés nem érinti. Az áttelepítés csak az [itt](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform)leírt klasszikus riasztási szabályokra vonatkozik.

Bár az eszköz szinte az összes [klasszikus riasztási szabályt](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform)áttelepítheti, vannak kivételek. A következő riasztási szabályok nem kerülnek áttelepítésre az eszköz használatával (vagy a 2019 szeptemberétől kezdődő automatikus áttelepítés során):

- Klasszikus riasztási szabályok a virtuális gép vendég metrikái (windowsos és Linux egyaránt). Tekintse meg az ilyen riasztási szabályok újbóli létrehozásához a cikk későbbi részében található [új metrikariasztásokban való újbóli létrehozásához.](#guest-metrics-on-virtual-machines)
- Klasszikus riasztási szabályok a klasszikus tárolási metrikák. Tekintse meg a [klasszikus tárfiókok figyeléséhez vezető útmutatót.](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/)
- Klasszikus riasztási szabályok egyes tárfiók-metrikák. A cikk későbbi részében [további részleteket](#storage-account-metrics) olvashat.
- Klasszikus riasztási szabályok néhány Cosmos DB-metrikák. A cikk későbbi részében [további részleteket](#cosmos-db-metrics) olvashat.
- Klasszikus riasztási szabályok az összes klasszikus virtuális gépek és a felhőszolgáltatások metrikák (Microsoft.ClassicCompute/virtualMachines és A Microsoft.ClassicCompute/domainNames/slots/roles). A cikk későbbi részében [további részleteket](#classic-compute-metrics) olvashat.

Ha az előfizetés rendelkezik ilyen klasszikus szabályokkal, manuálisan kell áttelepítenie őket. Mivel nem tudunk automatikus áttelepítést biztosítani, az ilyen típusú meglévő, klasszikus metrikariasztások 2020 júniusáig továbbra is működni fognak. Ez a bővítmény időt ad az új riasztásokra való áttérésre. 2020 júniusáig a fent felsorolt kivételeken is létrehozhat új klasszikus riasztásokat. Minden máshoz azonban 2019 augusztusa után nem lehet új klasszikus riasztásokat létrehozni.

> [!NOTE]
> A fent felsorolt kivételek mellett, ha a klasszikus riasztási szabályok érvénytelenek, azaz [elavult metrikákon](#classic-alert-rules-on-deprecated-metrics) vagy törölt erőforrásokon vannak, akkor nem lesznek áttelepítve, és nem lesznek elérhetők a szolgáltatás kivonása után.

### <a name="guest-metrics-on-virtual-machines"></a>Vendégmetrikák virtuális gépeken

Mielőtt új metrikariasztásokat hozhat létre a vendégmetrikák, a vendég metrikák at kell küldeni az Azure Monitor egyéni metrikák tárolására. Az alábbi utasításokat követve engedélyezze az Azure Monitor fogadóját a diagnosztikai beállításokban:

- [Vendégmérő számok engedélyezése Windows virtuális gépekhez](collect-custom-metrics-guestos-resource-manager-vm.md)
- [Vendégmetrikák engedélyezése Linuxos virtuális gépekhez](collect-custom-metrics-linux-telegraf.md)

Miután ezeket a lépéseket elvégezte, új metrikariasztásokat hozhat létre a vendégmetrikákon. És miután új metrikariasztásokat hozott létre, törölheti a klasszikus riasztásokat.

### <a name="storage-account-metrics"></a>Tárfiókok mérőszámai

A tárfiókok összes klasszikus riasztása áttelepíthető, kivéve az alábbi metrikákra vonatkozó riasztásokat:

- PercentAuthorizationError (PercentAuthorizationError)
- PercentClientOtherhiba
- PercentNetworkError (Százalékhálózati hiba)
- PercentServerOtherhiba
- Százalékos siker
- Százalékthrottlingerror
- PercentTimeouterror (Idő-idő hiba)
- AnonymousThrottlingError
- SASThrottling Hiba
- ThrottlingError

A százalékos metrikák klasszikus riasztási szabályait [a régi és az új tárolási metrikák közötti leképezés](https://docs.microsoft.com/azure/storage/common/storage-metrics-migration#metrics-mapping-between-old-metrics-and-new-metrics)alapján kell áttelepíteni. Küszöbértékek megfelelően módosítani kell, mert az új metrika elérhető abszolút egy.

Klasszikus riasztási szabályok AnonymousThrottlingError, SASThrottlingError és throttlingError két új riasztást kell felosztani, mert nincs kombinált metrika, amely ugyanazt a funkciót. A küszöbértékeket megfelelően ki kell igazítani.

### <a name="cosmos-db-metrics"></a>Cosmos DB-metrikák

A Cosmos DB-metrikákon lévő összes klasszikus riasztás áttelepíthető, kivéve az alábbi mérőszámokra vonatkozó riasztásokat:

- Átlagos kérelmek másodpercenként
- Konzisztenciaszint
- Http 2xx
- Http 3xx
- Http 400
- Http 401
- Belső kiszolgálóhiba
- Maximális RUPM percenkénti felhasznált
- Maximális rus másodpercenként
- Mongo sikertelen kérelmek száma
- Sikertelen kérelmek törlése mongo
- Sikertelen kérelmek beszúrása mongo
- Mongo egyéb sikertelen kérelmek
- Mongo egyéb kérelem díj
- Mongo egyéb kérelem aránya
- Sikertelen mongo lekérdezési kérelmek
- Sikertelen mongofrissítési kérelmek
- Megfigyelt olvasási késleltetés
- Megfigyelt írási késleltetés
- A szolgáltatás elérhetősége
- Tárkapacitás
- Szabályozott kérelmek
- Összes kérelem

Az átlagos kérelmek másodpercenként, a konzisztenciaszint, a percenként felhasznált maximális RUPM, a másodpercenkénti maximális kérelemegység, a megfigyelt olvasási késleltetés, a megfigyelt írási késleltetés, a tárolási kapacitás jelenleg nem érhető el az [új rendszerben.](metrics-supported.md#microsoftdocumentdbdatabaseaccounts)

Kérésre riasztások metrikák, például http 2xx, http 3xx, http 400, http 401, belső kiszolgálóhiba, szolgáltatás rendelkezésre állása, szabályozott kérelmek és az összes kérelem nem költözött, mert a kérelmek számlálása eltér a klasszikus metrikák és az új metrikák. Az ezekre vonatkozó riasztásokat manuálisan kell újra létrehozni a küszöbértékek beállításával.

Riasztások a Mongo sikertelen kérelmek metrikák kell osztani több riasztást, mert nincs kombinált metrika, amely ugyanazt a funkciót. A küszöbértékeket megfelelően ki kell igazítani.

### <a name="classic-compute-metrics"></a>Klasszikus számítási mutatók

A klasszikus számítási metrikákra vonatkozó riasztások nem lesznek áttelepítve az áttelepítési eszközzel, mivel a klasszikus számítási erőforrások at még nem támogatják az új riasztások. Az ezekre az erőforrástípusokra vonatkozó új riasztások támogatása a jövőben hozzáadódik. Ha ez elérhetővé válik, az ügyfeleknek 2020 júniusa előtt újra létre kell hozniuk az új egyenértékű riasztási szabályokat a klasszikus riasztási szabályaik alapján.

### <a name="classic-alert-rules-on-deprecated-metrics"></a>Klasszikus riasztási szabályok elavult metrikák

Ezek klasszikus riasztási szabályok metrikák, amelyek korábban támogatott, de végül elavult. Előfordulhat, hogy az ügyfél egy kis százaléka érvénytelen klasszikus riasztási szabályokat az ilyen metrikák. Mivel ezek a riasztási szabályok érvénytelenek, nem lesznek áttelepítve.

| Erőforrás típusa| Elavult metrika(k) |
|-------------|----------------- |
| Microsoft.DBforMySQL/kiszolgálók | compute_consumption_percent, compute_limit |
| Microsoft.DBforPostgreSQL/szerverek | compute_consumption_percent, compute_limit |
| Microsoft.Network/publicIPAddresses | defaultddostriggerrate |
| Microsoft.SQL/servers/databases | service_level_objective, storage_limit, storage_used, fojtás, dtu_consumption_percent, storage_used |
| Microsoft.Web/hostingEnvironments/multirolepools | átlagosmemória-megmunkálókészlet |
| Microsoft.Web/hostingEnvironments/workerpools | fogadott bájtok, httpqueuelength |

## <a name="how-equivalent-new-alert-rules-and-action-groups-are-created"></a>Az egyenértékű új riasztási szabályok és műveletcsoportok létrehozásának lépései

Az áttelepítési eszköz a klasszikus riasztási szabályokat egyenértékű új riasztási szabályokká és műveletcsoportokká alakítja. A legtöbb klasszikus riasztási szabályok, egyenértékű új riasztási szabályok `windowSize` ugyanazon `aggregationType`a metrika az azonos tulajdonságokkal, például és. Azonban vannak klasszikus riasztási szabályok metrikák, amelyek egy másik, egyenértékű metrika az új rendszerben. A klasszikus riasztások áttelepítésére a következő elvek vonatkoznak, kivéve, ha az alábbi szakaszban szerepel:

- **Gyakoriság:** Azt határozza meg, hogy egy klasszikus vagy új riasztási szabály milyen gyakran ellenőrizze a feltételt. A `frequency` klasszikus riasztási szabályok nem konfigurálható a felhasználó, és mindig 5 mins minden erőforrás-típusok, kivéve az Application Insights-összetevők, amelyek 1 min volt. Tehát az egyenértékű szabályok gyakorisága is 5 min, illetve 1 min.
- **Összesítés típusa:** Azt határozza meg, hogy a mérőszám hogyan lesz összesítve az érdeklődési ablakon keresztül. A `aggregationType` is megegyezik a klasszikus riasztások és az új riasztások a legtöbb metrikák. Bizonyos esetekben, mivel a metrika eltér a `aggregationType` klasszikus `primary Aggregation Type` riasztások és az új riasztások, egyenértékű vagy a metrika meghatározott használatos.
- **Egységek**: Annak a metrikának a tulajdonsága, amelyen a riasztás létrejön. Egyes egyenértékű metrikák különböző mértékegységekkel rendelkeznek. A küszöbértéket szükség szerint megfelelően kiigazítják. Ha például az eredeti metrika másodperceket egységként, de egyenértékű új metrika milliszekundum egységként, az eredeti küszöbérték et megszorozzuk 1000-rel, hogy biztosítsa ugyanazt a viselkedést.
- **Ablakméret:** Azt az ablakot határozza meg, amelyen a mérőszámok összesített adatait a rendszer a küszöbértékhez hasonlítja. A `windowSize` standard értékek, mint a 5mins, 15mins, 30mins, 1hour, 3hours, 6 óra, 12 óra, 1 nap, nincs változás az egyenértékű új riasztási szabály. Más értékek esetén a `windowSize` legközelebbi t választja ki a program. A legtöbb ügyfél számára nincs hatása ezzel a változással. Az ügyfelek kis százaléka, előfordulhat, hogy szükség van a csípés a küszöbértéket, hogy pontosan ugyanazt a viselkedést.

A következő szakaszokban részletesen ismertetik azokat a mutatókat, amelyek az új rendszerben egy másik, egyenértékű metrikával rendelkeznek. Minden metrika, amely ugyanaz marad a klasszikus és az új riasztási szabályok nem szerepel. Az új rendszerben támogatott mérőszámok listáját [itt](metrics-supported.md)találja.

### <a name="microsoftstorageaccountsservices"></a>Microsoft.StorageAccounts/services

A storage-fiók szolgáltatások, például blob, tábla, fájl és várólista, a következő metrikák vannak leképezve egyenértékű metrikák az alábbiak szerint:

| A klasszikus riasztások metrikája | Egyenértékű mutató az új riasztásokban | Megjegyzések|
|--------------------------|---------------------------------|---------|
| AnonymousAuthorizationError (Névtelen engedélyezési hiba)| Tranzakciók metrikája "ResponseType"="AuthorizationError" és "Authentication" = "Anonymous" dimenziókkal| |
| AnonymousClientOtherhiba | Tranzakciók metrikája "ResponseType"="ClientOtherError" és "Authentication" = "Anonymous" dimenziókkal | |
| AnonymousClientTimeOuthiba| Tranzakciók metrikája "ResponseType"="ClientTimeOutError" és "Authentication" = "Névtelen" dimenziókkal | |
| AnonymousNetworkError | Tranzakciók metrikája "ResponseType"="NetworkError" és "Authentication" = "Anonymous" dimenziókkal | |
| AnonymousServerOtherhiba | Tranzakciók metrikája "ResponseType"="ServerOtherError" és "Authentication" = "Anonymous" dimenziókkal | |
| AnonymousServerTimeOuthiba | Tranzakciók metrikája "ResponseType"="ServerTimeOutError" és "Authentication" = "Névtelen" dimenziókkal | |
| AnonymousSuccess | Tranzakciók mérőszáma "ResponseType"="Success" és "Authentication" = "Anonymous" dimenziókkal | |
| Engedélyezési hiba | Tranzakciók metrikája "ResponseType"="AuthorizationError" dimenziókkal | |
| AverageE2ELatency | SuccessE2ELatency (SuccessE2ELatency) | |
| AverageServerLatency | SuccessServerLatency (Sikereskiszolgálók átkának) | |
| Kapacitás | BlobCapacity kapacitás | Az `aggregationType` "utolsó" helyett használja az "átlag" lehetőséget. A metrika csak a Blob-szolgáltatásokra vonatkozik |
| ClientOtherhiba | Tranzakciók metrikája "ResponseType"="ClientOtherError" dimenziókkal  | |
| Ügyfélidő-hiba | Tranzakciók metrikája "ResponseType"="ClientTimeOutError" dimenziókkal | |
| Konténerszám | Konténerszám | Az `aggregationType` "utolsó" helyett használja az "átlag" lehetőséget. A metrika csak a Blob-szolgáltatásokra vonatkozik |
| NetworkError | Tranzakciók metrikája "ResponseType"="NetworkError" dimenziókkal | |
| Objektumszám | BlobCount (BlobCount)| Az `aggregationType` "utolsó" helyett használja az "átlag" lehetőséget. A metrika csak a Blob-szolgáltatásokra vonatkozik |
| SASAuthorizationError | Tranzakciók metrikája a "ResponseType"="AuthorizationError" és a "Authentication" = "SAS" dimenziókkal | |
| SASClientOtherError | Tranzakciók metrikája "ResponseType"="ClientOtherError" és "Authentication" = "SAS" dimenziókkal | |
| SASClientTimeOuterror | Tranzakciók metrikája "ResponseType"="ClientTimeOutError" és "Authentication" = "SAS" dimenziókkal | |
| SASNetworkHiba | Tranzakciók metrikája "ResponseType"="NetworkError" és "Authentication" = "SAS" dimenziókkal | |
| SASServerOtherError | Tranzakciók metrikája a "ResponseType"="ServerOtherError" és a "Authentication" = "SAS" dimenziókkal | |
| SASServerTimeOutError | Tranzakciók metrikája "ResponseType"="ServerTimeOutError" és "Authentication" = "SAS" dimenziókkal | |
| SASSiker | Tranzakciók metrikája "ResponseType"="Success" és "Authentication" = "SAS" dimenziókkal | |
| ServerOtherHiba | Tranzakciók metrikája "ResponseType"="ServerOtherError" dimenziókkal | |
| ServerTimeOuthiba | Tranzakciók metrikája "ResponseType"="ServerTimeOutError" dimenziókkal  | |
| Sikeres | Tranzakciók mérőszáma "ResponseType"="Sikeres" dimenziókkal | |
| Összesszámlázható kérések| Tranzakciók | |
| TotalEgress | Kimenő forgalom | |
| TotalIngress | Bejövő forgalom | |
| TotalRequests | Tranzakciók | |

### <a name="microsoftinsightscomponents"></a>Microsoft.insights/components

Az Application Insights esetében az egyenértékű mutatók az alábbiak szerint értik a következőket:

| A klasszikus riasztások metrikája | Egyenértékű mutató az új riasztásokban | Megjegyzések|
|--------------------------|---------------------------------|---------|
| availability.availabilityMetric.value | availabilityResults/availabilitySzázalék|   |
| availability.durationMetric.érték | availabilityEredmények/időtartam| Szorozza meg az eredeti küszöbértéket 1000 egység a klasszikus metrika másodpercben, és az új egy milliseconds.  |
| basicExceptionBrowser.count | kivételek/böngésző|  Az `aggregationType` "összeg" helyett használja a "count" (szám' lehetőséget. |
| basicExceptionServer.count | kivételek/kiszolgáló| Az `aggregationType` "összeg" helyett használja a "count" (szám' lehetőséget.  |
| clientPerformance.clientProcess.value | browserTimings/processingDuration| Szorozza meg az eredeti küszöbértéket 1000 egység a klasszikus metrika másodpercben, és az új egy milliseconds.  |
| clientPerformance.networkConnection.value | browserTimings/networkDuration|  Szorozza meg az eredeti küszöbértéket 1000 egység a klasszikus metrika másodpercben, és az új egy milliseconds. |
| clientPerformance.receiveRequest.value | browserTimings/receiveDuration| Szorozza meg az eredeti küszöbértéket 1000 egység a klasszikus metrika másodpercben, és az új egy milliseconds.  |
| clientPerformance.sendRequest.value | browserTimings/sendDuration| Szorozza meg az eredeti küszöbértéket 1000 egység a klasszikus metrika másodpercben, és az új egy milliseconds.  |
| clientPerformance.total.érték | browserTimings/totalDuration| Szorozza meg az eredeti küszöbértéket 1000 egység a klasszikus metrika másodpercben, és az új egy milliseconds.  |
| performanceCounter.available_bytes.érték | performanceCounters/memoryAvailableBytes|   |
| performanceCounter.io_data_bytes_per_sec.érték | performanceCounters/processIOBytesPerSecond|   |
| performanceCounter.number_of_exceps_thrown_per_sec.érték | performanceCounters/exceptionsPerSecond|   |
| performanceCounter.percentage_processor_time_normalized.value | performanceCounters/processCpuPercentage|   |
| performanceCounter.percentage_processor_time.érték | performanceCounters/processCpuPercentage| Küszöbértéket kell megfelelően módosítani, mint az eredeti metrika volt az összes mag, és az új metrika egy magra normalizálódik. Az áttelepítési eszköz nem módosítja a küszöbértékeket.  |
| performanceCounter.percentage_processor_total.value | performanceCounters/processorCpuPercentage|   |
| performanceCounter.process_private_bytes.érték | performanceCounters/processPrivateBytes|   |
| performanceCounter.request_execution_time.érték | performanceCounters/requestExecutionTime|   |
| performanceCounter.requests_in_application_queue.érték | performanceCounters/requestsInQueue|   |
| performanceCounter.requests_per_sec.érték | performanceCounters/requestsPerSecond|   |
| kérés.időtartam | kérések/időtartam| Szorozza meg az eredeti küszöbértéket 1000 egység a klasszikus metrika másodpercben, és az új egy milliseconds.  |
| kérés.ráta | kérések/díj|   |
| requestFailed.count | kérések/sikertelen| Az `aggregationType` "összeg" helyett használja a "count" (szám' lehetőséget.   |
| view.count | oldalmegtekintések/számlálók| Az `aggregationType` "összeg" helyett használja a "count" (szám' lehetőséget.   |

### <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

A Cosmos DB esetében az egyenértékű mutatók az alábbiak szerint jelennek meg:

| A klasszikus riasztások metrikája | Egyenértékű mutató az új riasztásokban | Megjegyzések|
|--------------------------|---------------------------------|---------|
| AvailableStorage (Elérhető tárhely)     |AvailableStorage (Elérhető tárhely)|   |
| Adatméret | Adathasználat| |
| Dokumentumok száma | DocumentCount (Dokumentumdarab)||
| Index mérete | Indexhasználat||
| Mongo-számlálási kérelem díja| MongoRequestCharge "CommandName" dimenzióval = "gróf"||
| Mongo-számlálási kérelmek aránya | MongoRequestsCount a "CommandName" dimenzióval = "gróf"||
| Mongo törlési kérelem díja | MongoRequestCharge a "CommandName" dimenzióval = "törlés"||
| Mongo törlési kérelem aránya | MongoRequestsCount a "CommandName" dimenzióval = "delete"||
| Mongo request charge behelyezése | MongoRequestCharge "CommandName" dimenzióval = "insert"||
| Mongo kérelembehelyezési arány | MongoRequestsCount a "CommandName" = "insert" dimenzióval||
| Mongo lekérdezéskérelem-terhelés | MongoRequestCharge a "CommandName" dimenzióval = "find"||
| Mongo lekérdezési kérelmek aránya | MongoRequestsCount a "CommandName" dimenzióval = "find"||
| Mongo frissítési kérelem díja | MongoRequestCharge a "CommandName" dimenzióval = "update"||
| A szolgáltatás nem érhető el| Szolgáltatáselérhetősége||
| TotalRequestUnits | TotalRequestUnits||

### <a name="how-equivalent-action-groups-are-created"></a>Az egyenértékű műveletcsoportok létrehozásának lépései

Klasszikus riasztási szabályok e-mail, webhook, logikai alkalmazás és runbook műveletek et a riasztási szabály maga. Az új riasztási szabályok műveletcsoportokat használnak, amelyek több riasztási szabályban is felhasználhatók. Az áttelepítési eszköz egyetlen műveletcsoportot hoz létre ugyanazokhoz a műveletekhez, függetlenül attól, hogy hány riasztási szabály használja a műveletet. Az áttelepítési eszköz által létrehozott műveletcsoportok a "Migrated_AG*" elnevezési formátumot használják.

> [!NOTE]
> Klasszikus riasztások küldött honosított e-maileket a területi beállítás alapján a klasszikus rendszergazda, ha a klasszikus rendszergazdai szerepkörök értesítésére használják. Az új figyelmeztető e-maileket a műveletcsoportokon keresztül küldjük el, és csak angol nyelven.

## <a name="rollout-phases"></a>Bevezetési fázisok

Az áttelepítési eszköz fokozatosan bevezetésre kerül a klasszikus riasztási szabályokat használó ügyfelek számára. Az előfizetés-tulajdonosok e-mailt kapnak, amikor az előfizetés készen áll az eszköz használatával történő áttelepítésre.

> [!NOTE]
> Mivel az eszköz fázisokban van kivezetve, előfordulhat, hogy egyes előfizetések még nem állnak készen a korai fázisokban való áttelepítésre.

A legtöbb előfizetés ek jelenleg áttelepítésre készként vannak megjelölve. Csak a következő erőforrástípusokklasszikus riasztásokkal rendelkező előfizetések továbbra sem állnak készen az áttelepítésre.

- Microsoft.classicCompute/domainNames/slots/roles
- Microsoft.insights/components

## <a name="who-can-trigger-the-migration"></a>Ki indíthatja el az áttelepítést?

Minden olyan felhasználó, aki az előfizetés szintjén rendelkezik a közreműködő figyelése beépített szerepkörével, aktiválhatja az áttelepítést. Azok a felhasználók, akik egyéni szerepkörrel rendelkeznek a következő engedélyekkel, szintén elindíthatják az áttelepítést:

- */olvasás
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*
- Microsoft.AlertsManagement/smartDetectorAlertRules/*

> [!NOTE]
> A fenti engedélyek mellett az előfizetést is regisztrálni kell a Microsoft.AlertsManagement erőforrás-szolgáltatónál. Ez szükséges a hibaanomaly riasztások sikeres áttelepítéséhez az Application Insights. 

## <a name="common-problems-and-remedies"></a>Gyakori problémák és jogorvoslatok

Az [áttelepítés aktiválása](alerts-using-migration-tool.md)után e-mailben értesíti, hogy az áttelepítés befejeződött, vagy ha bármilyen műveletre van szükség. Ez a rész néhány gyakori problémát és azok kezelésének módját ismerteti.

### <a name="validation-failed"></a>Az érvényesítés nem sikerült

A klasszikus riasztási szabályok néhány legutóbbi módosítása miatt az előfizetésben az előfizetés nem telepíthető át. Ez a probléma átmeneti. Az áttelepítés újraindítása után az áttelepítési állapot néhány napon belül **újraindul Az áttelepítésre való felkészülés.**

### <a name="scope-lock-preventing-us-from-migrating-your-rules"></a>A hatókör zárolása megakadályozza, hogy áttelepítsék a szabályokat

Az áttelepítés részeként új metrikariasztások és új műveletcsoportok jönnek létre, majd a klasszikus riasztási szabályok törlődnek. A hatókörzárolás azonban megakadályozhatja, hogy erőforrásokat hozzunk létre vagy távolítsunk el. A hatókörzárolástól függően néhány vagy az összes szabályt nem lehetett áttelepíteni. A probléma az [áttelepítési eszközben](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)felsorolt előfizetés, erőforráscsoport vagy erőforrás hatókörzárolásának eltávolításával és az áttelepítés újbóli elindításával oldható meg. A hatókörzárolás nem tiltható le, ezért az áttelepítési folyamat időtartama alatt el kell távolítani. [További információ a hatókörzárolások kezeléséről.](../../azure-resource-manager/management/lock-resources.md#portal)

### <a name="policy-with-deny-effect-preventing-us-from-migrating-your-rules"></a>A "Megtagadás" hatású házirend megakadályozza, hogy áttelepítsék a szabályokat

Az áttelepítés részeként új metrikariasztások és új műveletcsoportok jönnek létre, majd a klasszikus riasztási szabályok törlődnek. Egy házirend azonban megakadályozhatja, hogy erőforrásokat hozzunk létre. A házirendtől függően néhány vagy az összes szabályt nem lehetett áttelepíteni. A folyamatot letiltó házirendek az [áttelepítési eszközben](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)jelennek meg. A probléma megoldása:

- Az előfizetések vagy erőforráscsoportok kizárása az áttelepítési folyamat időtartama alatt a házirend-hozzárendelésből. [További információ a házirendek kizárási hatókörének kezeléséről.](../../governance/policy/tutorials/create-and-manage.md#exempt-a-non-compliant-or-denied-resource-using-exclusion)
- A hatás eltávolítása vagy módosítása "audit" vagy "hozzáfűzés" (amely például megoldhatja a hiányzó címkékkel kapcsolatos problémákat). [További információ a házirendek hatásának kezeléséről.](../../governance/policy/concepts/definition-structure.md#policy-rule)

## <a name="next-steps"></a>További lépések

- [A migrálási eszköz használata](alerts-using-migration-tool.md)
- [Előkészületek a migráláshoz](alerts-prepare-migration.md)
