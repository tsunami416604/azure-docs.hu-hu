---
title: Támogatott erőforrástípusok az Azure Resource Health szolgáltatáson keresztül | Microsoft dokumentumok
description: Támogatott erőforrástípusok az Azure Resource állapota révén
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: 5cc80147730fdc97b1181690f6e70fc538d4afcc
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478901"
---
# <a name="resource-types-and-health-checks-in-azure-resource-health"></a>Erőforrástípusok és állapotellenőrzések az Azure-erőforrások állapotában
Az alábbi lista az erőforrás-állapotok erőforrás-típusonkénti összes ellenőrzését tartalmazza.

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/kiszolgálók
|Végrehajtott ellenőrzések|
|---|
|<ul><li>A szerver működik?</li><li>Elfogyott a kiszolgáló memóriája?</li><li>Elindul a kiszolgáló?</li><li>Helyreáll a kiszolgáló?</li></ul>|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Működik az Api Management szolgáltatás?</li></ul>|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts
|Végrehajtott ellenőrzések|
|---|
|<ul><li>A Batch fiók működik és működik?</li><li>Túllépte a készletkvótát ehhez a kötegfiókhoz?</li></ul>|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/Redis
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Az összes gyorsítótár-csomópont működik?</li><li>A gyorsítótár elérhető az adatközponton belülről?</li><li>Elérte a gyorsítótár a kapcsolatok maximális számát?</li><li> A gyorsítótár kimerítette a rendelkezésre álló memóriát? </li><li>A gyorsítótárban nagy számú laphiba tapasztalható?</li><li>A cache nagy terhelés alatt van?</li></ul>|

## <a name="microsoftcdnprofile"></a>Microsoft.CDN/profil
|Végrehajtott ellenőrzések|
|---|
|<ul> <li>A kiegészítő portál elérhető a CDN konfigurációs műveletek hez?</li><li>Vannak folyamatban lévő szállítási problémák a CDN-végpontokkal?</li><li>A felhasználók módosíthatják a CDN-erőforrások konfigurációját?</li><li>A konfigurációs módosítások a várt ütemben terjednek?</li><li>A felhasználók kezelhetik a CDN-konfigurációt az Azure Portalon, a PowerShellen vagy az API-n keresztül?</li> </ul>|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.classiccompute/virtualmachines
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Működik a gazdakiszolgáló?</li><li>Befejeződött a gazdaoperációs rendszer indítása?</li><li>Ki van építve és be van kapcsolva a virtuálisgép-tároló?</li><li>Van hálózati kapcsolat a gazdagép és a tárfiók között?</li><li>Befejeződött a vendég operációs rendszer indítása?</li><li>Van-e folyamatban lévő tervezett karbantartás?</li><li>A gazdagép hardvere leromlott, és az előrejelzések szerint hamarosan meghibásodik?</li></ul>|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.cognitiveservices/fiókok
|Végrehajtott ellenőrzések|
|---|
|<ul><li>A fiók elérhető az adatközponton belülről?</li><li>Elérhető a Cognitive Services erőforrás-szolgáltató?</li><li>A Cognitive Service elérhető a megfelelő régióban?</li><li>Az erőforrás metaadatait tároló tárfiókban olvasási műveletek hajthatók végre?</li><li>Elérte az API-hívási kvótát?</li><li>Elérte az API-hívás olvasási korlátját?</li></ul>|

## <a name="microsoftcomputehostgroupshosts"></a>Microsoft.compute/hostgroups/hosts
|Végrehajtott ellenőrzések|
|---|
|<ul><li>A gazdagép működik</li><li>A gazdahardver leromlott?</li><li>A gazdagép felvan osztva?</li><li>A gazdahardverszolgáltatás különböző hardverrel gyógyult?</li></ul>|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.compute/virtualmachines
|Végrehajtott ellenőrzések|
|---|
|<ul><li>A virtuális gépet üzemeltető kiszolgáló működik?</li><li>Befejeződött a gazdaoperációs rendszer indítása?</li><li>Ki van építve és be van kapcsolva a virtuálisgép-tároló?</li><li>Van hálózati kapcsolat a gazdagép és a tárfiók között?</li><li>Befejeződött a vendég operációs rendszer indítása?</li><li>Van-e folyamatban lévő tervezett karbantartás?</li><li>A gazdagép hardvere leromlott, és az előrejelzések szerint hamarosan meghibásodik?</li></ul>|

## <a name="microsoftdatafactoryfactories"></a>Microsoft.datafactory/gyárak
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Voltak-e folyamatfuttatási hibák?</li><li>A Data Factory-t üzemeltető fürt kifogástalan állapotú?</li></ul>|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.datalakeanalytics/fiókok
|Végrehajtott ellenőrzések|
|---|
|<ul><li>A felhasználók tapasztalták-e a Data Lake Analytics-feladataik elküldésével vagy listázásával kapcsolatos problémákat?</li><li>A Data Lake Analytics-feladatok rendszerhibák miatt nem hajthatók végre?</li></ul>|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.datalakestore/fiókok
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Problémát tapasztaltak a felhasználók az adatok Data Lake Store-ba való feltöltésével kapcsolatban?</li><li>Problémákat tapasztaltak a felhasználók az adatok Data Lake Store-ból való letöltésével kapcsolatban?</li></ul>|

## <a name="microsoftdatamigrationservices"></a>Microsoft.datamigration/services
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Nem sikerült az adatbázis-áttelepítési szolgáltatás kiépítése?</li><li>Inaktivitás vagy felhasználói kérelem miatt leállt az adatbázis-áttelepítési szolgáltatás?</li></ul>|

## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/fiókok
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Az adatmegosztási fiók működik?</li><li>Elérhető az adatmegosztást üzemeltető fürt?</li></ul>|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/kiszolgálók
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Karbantartás miatt nem érhető el a kiszolgáló?</li><li>Az újrakonfigurálás miatt nem érhető el a kiszolgáló?</li></ul>|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/kiszolgálók
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Karbantartás miatt nem érhető el a kiszolgáló?</li><li>Az újrakonfigurálás miatt nem érhető el a kiszolgáló?</li></ul>|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/szerverek
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Karbantartás miatt nem érhető el a kiszolgáló?</li><li>Az újrakonfigurálás miatt nem érhető el a kiszolgáló?</li></ul>|

## <a name="microsoftdevicesiothubs"></a>Microsoft.devices/iothubs
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Az IoT hub működik?</li></ul>|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.documentdb/databaseAccounts
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Voltak olyan adatbázis- vagy gyűjteménykérelmek, amelyeket nem szolgáltak ki az Azure Cosmos DB-szolgáltatás elérhetetlensége miatt?</li><li>Voltak olyan dokumentumkérelmek nem kézbesített miatt az Azure Cosmos DB szolgáltatás elérhetetlensége miatt?</li></ul>|

## <a name="microsofteventhubnamespaces"></a>Microsoft.eventhub/névterek
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Az Event Hubs névtér felhasználói generált hibákat tapasztal?</li><li>Az Event Hubs névtér frissítése folyamatban van?</li></ul>|

## <a name="microsofthdinsightclusters"></a>Microsoft.hdinsight/clusters
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Elérhetők az alapvető szolgáltatások a HDInsight-fürtön?</li><li>A HDInsight-fürt hozzáférhet a BYOK titkosítási kulcshoz inaktív állapotban?</li></ul>|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults
|Végrehajtott ellenőrzések|
|---|
|<ul><li>A key vault-kérelmek az Azure KeyVault platformproblémái miatt sikertelenek?</li><li>A key vault-kérelmek szabályozása az ügyfél által küldött túl sok kérés miatt?</li></ul>|

## <a name="microsoftmachinelearningwebservices"></a>Microsoft.MachineLearning/webServices
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Működik és működik a webszolgáltatás?</li></ul>|

## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Működik és működik a médiaszolgáltatás?</li></ul>|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.network/applicationgateways
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Az Alkalmazásátjáró teljesítménye leromlott?</li><li>Elérhető az alkalmazásátjáró?</li></ul>|

## <a name="microsoftnetworkconnections"></a>Microsoft.network/connections
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Csatlakoztatva van a VPN-alagút?</li><li>Vannak konfigurációs ütközések a kapcsolatban?</li><li>Megfelelően vannak konfigurálva az előmegosztott kulcsok?</li><li>A VPN helyszíni eszköz elérhető?</li><li>Vannak eltérések az IPSec/IKE biztonsági házirendben?</li><li>Az S2S VPN-kapcsolat megfelelően ki van építve, vagy hibás állapotban van?</li><li>A VNET-vNET-kapcsolat megfelelően ki van építve, vagy hibás állapotban van?</li></ul>|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.network/expressroutecircuits
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Az ExpressRoute-kapcsolat kifogástalan?</li></ul>|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft.network/frontdoors
|Végrehajtott ellenőrzések|
|---|
|<ul><li>A Bejárati ajtó háttérrendszerei hibákkal válaszolnak az állapotminta-vizsgálatokra?</li><li>Késnek a konfigurációs módosítások?</li></ul>|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.network/LoadBalancers
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Elérhetők a terheléselosztási végpontok?</li></ul>|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.network/virtualNetworkGateways
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Elérhető-e a VPN-átjáró az internetről?</li><li>A VPN-átjáró készenléti állapotban van?</li><li>Fut a VPN-szolgáltatás az átjárón?</li></ul>|

## <a name="microsoftnotificationhubsnamespace"></a>Microsoft.NotificationHubs/névtér
|Végrehajtott ellenőrzések|
|---|
|<ul><li>A futásidejű műveletek, például a regisztráció, a telepítés vagy a küldés a névtéren hajthatók végre?</li></ul>|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.operationalinsights/workspaces
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Vannak indexelési késések a munkaterületen?</li></ul>|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedikált/Kapacitások
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Működik és működik a kapacitáserőforrás?</li><li>Minden számítási feladat működik?</li></ul>|

## <a name="microsoftpowerbiworkspacecollections"></a>Microsoft.PowerBI/workspaceGyűjtemények
|Végrehajtott ellenőrzések|
|---|
|<ul><li>A gazdaoperációs rendszer működik?</li><li>A munkaterületgyűjtemény elérhető az adatközponton kívülről?</li><li>Elérhető a Power BI erőforrás-szolgáltató?</li><li>Elérhető a Power BI szolgáltatás a megfelelő régióban?</li></ul>|

## <a name="microsoftsearchsearchservices"></a>Microsoft.search/searchServices
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Végezhetők-e diagnosztikai műveletek a fürtön?</li></ul>|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/névterek
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Az ügyfelek felhasználói által generált Service Bus-hibákat tapasztalnak?</li><li>A felhasználók átmeneti hibák növekedését tapasztalják a Service Bus névtér frissítése miatt?</li></ul>|

## <a name="microsoftservicefabricclusters"></a>Microsoft.ServiceFabric/fürtök
|Végrehajtott ellenőrzések|
|---|
|<ul><li>A Service Fabric-fürt működik?</li><li>A Service Fabric-fürt kezelhető az Azure Resource Manageren keresztül?</li></ul>|

## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft.SQL/managedInstances/databases
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Működik és működik az adatbázis?</li></ul>|

## <a name="microsoftsqlserverdatabases"></a>Microsoft.SQL/Server/Databases
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Voltak bejelentkezések az adatbázisba?</li></ul>|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Az Azure Storage platformproblémái miatt nem sikerül adatokat olvasni a Storage-fiókból?</li><li>Az Azure Storage platformproblémái miatt nem sikerül adatokat írni a Storage-fiókba?</li><li>Nem érhető el a Storage-fürt, ahol a storage-fiók található?</li></ul>|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Az összes olyan állomás, ahol a feladat fut és fut?</li><li>Nem sikerült elindítani a feladatot?</li><li>Vannak folyamatban lévő futásidejű frissítések?</li><li>A feladat várt állapotban van (például fut vagy leállítja az ügyfél)?</li><li>A feladat memóriakivételekkel rendelkezik?</li><li>Vannak folyamatban lévő ütemezett számítási frissítések?</li><li>Elérhető a Végrehajtáskezelő (vezérlőterv) ?</li></ul>|

## <a name="microsoftwebserverfarms"></a>Microsoft.web/serverFarms
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Működik a gazdakiszolgáló?</li><li>Fut az Internet Information Services?</li><li>Működik a terheléselosztó?</li><li>Elérhető-e az App Service-csomag az adatközponton belülről?</li><li>A tárfiók otthont a helyszínek tartalmát a serverFarm elérhető?</li></ul>|

## <a name="microsoftwebsites"></a>Microsoft.web/webhelyek
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Működik a gazdakiszolgáló?</li><li>Fut az Internet Information kiszolgáló?</li><li>Működik a terheléselosztó?</li><li>Elérhető a webalkalmazás az adatközponton belülről?</li><li>Elérhető a webhely tartalmát tároló tárfiók?</li></ul>|

## <a name="next-steps"></a>Következő lépések
-  [Az Azure Service Health irányítópultjának bemutatása](service-health-overview.md) és az Azure Resource Health [bemutatása](resource-health-overview.md) című témakörben további tudnivalókat. 
-  [Gyakori kérdések az Azure Resource Health szolgáltatásról](resource-health-faq.md)
- Állítsa be a riasztásokat, hogy értesítést kapjon az egészségügyi problémákról. További információ: [Riasztások konfigurálása szolgáltatásállapot-eseményekhez](../azure-monitor/platform/alerts-activity-log-service-notifications.md)című témakörben talál. 
