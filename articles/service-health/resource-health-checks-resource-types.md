---
title: Az Azure Resource Health segítségével erőforrástípusok támogatott |} A Microsoft Docs
description: Az Azure Resource health segítségével támogatott erőforrástípusok
author: stephbaron
ms.author: stbaron
ms.topic: how-to
ms.service: service-health
ms.date: 01/29/2019
ms.openlocfilehash: 6945573fffa5a8398a61bfa44737bec798e16be2
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55881440"
---
# <a name="resource-types-and-health-checks-in-azure-resource-health"></a>Erőforrástípusok és állapot-ellenőrzések a az Azure resource health segítségével elérhető
Alább az erőforrástípusok szerint resource health segítségével végrehajtott összes ellenőrzés teljes listája.

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Az a kiszolgáló üzembe helyezéséig?</li><li>A kiszolgáló elfogyott a memória?</li><li>A kiszolgáló indítása folyamatban van?</li><li>A kiszolgáló helyreállítása folyamatban van?</li></ul>|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Az Api Management-szolgáltatás működik és fut?</li></ul>|

## <a name="microsoftcacheredisredis"></a>Microsoft.CacheRedis/Redis
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Helyezheti üzembe, amelyek a gyorsítótár-csomópontok?</li><li>A gyorsítótár elérhető, az az adatközpontban?</li><li>A gyorsítótár elérte a kapcsolatok maximális számát?</li><li> Már a gyorsítótár kimerítette a rendelkezésre álló memória? </li><li>A gyorsítótár oldalhibák nagy számú tapasztal?</li><li>A gyorsítótár nagy terhelés alatt áll?</li></ul>|

## <a name="microsoftcdnprofile"></a>Microsoft.CDN/profile
|Végrehajtott ellenőrzések|
|---|
|<ul> <li>A kiegészítő portál érhető el a CDN a konfigurálási műveletek?</li><li>A CDN-végpontok folyamatos kézbesítési problémái vannak?</li><li>Felhasználók módosíthatja a konfigurációt a CDN-erőforrások?</li><li>A várt sebességgel terjesztenek konfigurációs módosításokat?</li><li>Kezelheti a felhasználókat a CDN-konfiguráció az Azure portal, PowerShell vagy az API használatával?</li> </ul>|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.classiccompute/virtualmachines
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Működik-e a gazdagép-kiszolgálón?</li><li>Befejeződött a gazdagép operációs rendszer rendszerindítást?</li><li>A virtuálisgép-tároló üzembe helyezett és kapcsolt be?</li><li>Van-e hálózati kapcsolat a gazdagép és a storage-fiók között?</li><li>Befejeződött a vendég operációs rendszerben indul el?</li><li>Nem tervezett karbantartást?</li></ul>|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.cognitiveservices/accounts
|Végrehajtott ellenőrzések|
|---|
|<ul><li>A fiókhoz elérhető, az az adatközpontban?</li><li>A Cognitive Services-Erőforrásszolgáltatót érhető el?</li><li>Érhető el a Cognitive Services-szolgáltatás a megfelelő régióban?</li><li>Olvashatja az műveletek hajthatók végre a az erőforrás-metaadatok birtokló storage-fiók?</li><li>Elérte az API-hívási kvótát?</li><li>Elérte az API hívása olvasási-korlátot?</li></ul>|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.compute/virtualmachines
|Végrehajtott ellenőrzések|
|---|
|<ul><li>A kiszolgáló a virtuális gépet üzemeltető fel és futó?</li><li>Befejeződött a gazdagép operációs rendszer rendszerindítást?</li><li>A virtuálisgép-tároló üzembe helyezett és kapcsolt be?</li><li>Van-e hálózati kapcsolat a gazdagép és a storage-fiók között?</li><li>Befejeződött a vendég operációs rendszerben indul el?</li><li>Nem tervezett karbantartást?</li></ul>|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.datalakeanalytics/accounts
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Felhasználók problémákat tapasztal vagy a listázása a Data Lake Analytics-feladatok elküldése van?</li><li>Data Lake Analytics-feladatok nem lehet végrehajtani a rendszerhibák vannak?</li></ul>|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.datalakestore/accounts
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Tapasztaltak a felhasználóknak a problémák adatok feltöltése a Data Lake Store?</li><li>Tapasztaltak a felhasználók adatokat tölti le a Data Lake Store problémákat?</li></ul>|

## <a name="microsoftdevicesiothubs"></a>Microsoft.devices/iothubs

|Végrehajtott ellenőrzések|
|---|
|<ul><li>Működik-e az IoT hub?</li></ul>|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.documentdb/databaseAccounts
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Ott volt bármilyen adatbázis és gyűjtemény kérelmek nem szolgálja ki egy Azure Cosmos DB szolgáltatás elérhetetlensége miatt?</li><li>Van már nem szolgálja ki egy Azure Cosmos DB szolgáltatás elérhetetlensége miatt dokumentum kérések?</li></ul>|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Az Azure KeyVault platformmal kapcsolatos problémák miatt sikertelen kéréseket a key vault?</li><li>Vannak a key vaulttal kérelmek miatt szabályozás alatt áll ügyfél által végrehajtott túl sok kérelmet?</li></ul>|

## <a name="microsoftnetworkconnections"></a>Microsoft.network/connections
|Végrehajtott ellenőrzések|
|---|
|<ul><li>A VPN-alagút csatlakozik?</li><li>Vannak-e konfigurációs ütközések a kapcsolatot?</li><li>Az előmegosztott kulcsok megfelelően legyenek konfigurálva?</li><li>A helyszíni VPN-eszköz érhető el?</li><li>Vannak-e eltérést az IPSec/IKE-biztonsági házirend?</li><li>Megfelelően kiépített vagy hibás állapotban van az S2S VPN-kapcsolat?</li><li>Megfelelően kiépített vagy hibás állapotban van a VNET – VNET kapcsolat?</li></ul>|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.network/virtualNetworkGateways
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Az internetről elérhető, a VPN-átjáró?</li><li>A VPN-átjáró készenléti üzemmódban van?</li><li>A VPN-szolgáltatás fut az átjárón?</li></ul>|

## <a name="microsoftnotificationhubsnamespace"></a>Microsoft.NotificationHubs/namespace
|Végrehajtott ellenőrzések|
|---|
|<ul><li> Végrehajtható futásidejű műveletek, mint a regisztráció, a telepítéshez vagy a Küldés a névtér?</li></ul>|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/Capacities
|Végrehajtott ellenőrzések|
|---|
|<ul><li>A kapacitás erőforrás működik és elérhető?</li><li>Azok a számítási feladatokat, és?</li></ul>|

## <a name="microsoftpowerbiworkspacecollections"></a>Microsoft.PowerBI/workspaceCollections
|Végrehajtott ellenőrzések|
|---|
|<ul><li>A gazda operációs rendszer működik és elérhető?</li><li>Az a workspaceCollection az adatközponton kívülről elérhető?</li><li>A Power bi erőforrás-szolgáltató érhető el?</li><li>Érhető el a Power bi szolgáltatás a megfelelő régióban?</li></ul>|

## <a name="microsoftsearchsearchservices"></a>Microsoft.search/searchServices
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Diagnosztikai műveletek végrehajtható a fürtön?</li></ul>|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Ügyfelek felhasználó által a Service Bus hibákat tapasztal?</li><li>Az átmeneti hibák egy Service Bus-névtér frissítése miatt növelését tapasztalt felhasználók?</li></ul>|

## <a name="microsoftsqlserverdatabase"></a>Microsoft.SQL/Server/database
|Végrehajtott ellenőrzések|
|---|
|<ul><li> Az adatbázis-felhasználónevekhez nem voltak?</li></ul>|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Azure Storage platformmal kapcsolatos problémák miatt sikertelen kérelmeket a tárfiókból?</li><li>Azure Storage platformmal kapcsolatos problémák miatt sikertelen kérelmek adatokat írni a Storage-fiókban?</li><li>Nem érhető el a tárolófürtön, ahol a Storage-fiókban található?</li></ul>|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Ha a feladat végrehajtása fel és futtató összes gazdagép vannak?</li><li>Nem lehetett elindítani a feladatot?</li><li>Vannak-e folyamatban lévő futásidejű frissítéseket?</li><li>A feladat várt (például fut, vagy ügyfél által leállítva) állapotban van?</li><li>A feladat észlelt a memória kivételek?</li><li>Ütemezett számítási folyamatban lévő frissítések vannak?</li><li>A végrehajtás-kezelője (vezérlő csomag) érhető el?</li></ul>|

## <a name="microsoftwebserverfarms"></a>Microsoft.web/serverFarms
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Működik-e a gazdagép-kiszolgálón?</li><li>Fut az Internet Information Services?</li><li>Fut-e a Load balancer?</li><li>Az App Service-csomag elérhető a az adatközpontban?</li><li>A tárfiók üzemelteti a kiszolgálófarm érhető el: a webhelyek tartalmát??</li></ul>|

## <a name="microsoftwebsites"></a>Microsoft.web/sites
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Működik-e a gazdagép-kiszolgálón?</li><li>Az Internet Information server fut?</li><li>Fut-e a Load balancer?</li><li>A webes alkalmazás elérhető, az az adatközpontban?</li><li>A storage-fiók futtató érhető el a webhely tartalmát?</li></ul>|

# <a name="next-steps"></a>További lépések
-  Lásd: [bemutatása az Azure Service Health dashboard](service-health-overview.md) és [bemutatása az Azure Resource Health](resource-health-overview.md) , hogy többet őket. 
-  [Az Azure Resource Health kapcsolatos gyakori kérdések](resource-health-faq.md)
- Riasztások beállítása, így állapotbeli probléma értesítést kap. További információkért lásd: [riasztásainak konfigurálása a Szolgáltatásállapot-események](../azure-monitor/platform/alerts-activity-log-service-notifications.md). 
