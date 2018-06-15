---
title: Támogatott Azure-erőforrás állapotának keresztül erőforrástípusok |} Microsoft Docs
description: Támogatott erőforrástípus keresztül Azure-erőforrás állapota
services: Resource health
documentationcenter: ''
author: BernardoAMunoz
manager: ''
editor: ''
ms.assetid: 85cc88a4-80fd-4b9b-a30a-34ff3782855f
ms.service: service-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 10/09/2017
ms.author: BernardoAMunoz
ms.openlocfilehash: 3b4d99fe883cf52ca7f1ef98e70b7f3a1bccd5ae
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31795945"
---
# <a name="resource-types-and-health-checks-in-azure-resource-health"></a>Erőforrástípusok és állapotát ellenőrzi az Azure-erőforrás állapota
Alább az erőforrás állapota keresztül erőforrástípusok által végrehajtott egyetlen ellenőrzés sem teljes listáját.

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Az Api Management szolgáltatás működik-e és fut?</li></ul>|

## <a name="microsoftcacheredisredis"></a>Microsoft.CacheRedis/Redis
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Megfelelően a gyorsítótár-csomópontok működik?</li><li>A gyorsítótár elérhető a az adatközponton belül?</li><li>A gyorsítótár elérte a kapcsolatok maximális száma?</li><li> A gyorsítótár kimerítette a rendelkezésre álló memória? </li><li>A gyorsítótár nagy mennyiségű laphibát tapasztal?</li><li>A gyorsítótár van terhelve?</li></ul>|

## <a name="microsoftcdnprofile"></a>Microsoft.CDN/profile
|Végrehajtott ellenőrzések|
|---|
|<ul> <li>A végpontok bármelyikét leállt, eltávolított vagy helytelenül konfigurált?</li><li>A kiegészítő portálon érhető el a CDN a konfigurálási műveletek?</li><li>A CDN-végpontokat a folyamatban lévő kézbesítési problémái vannak?</li><li>Módosíthatja a felhasználók a CDN-erőforrások konfigurációs?</li><li>Konfigurációs módosítások az várt díj propagálása vannak?</li><li>Kezelheti a felhasználók a CDN-konfiguráció az Azure-portálon, PowerShell vagy az API-t?</li> </ul>|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.classiccompute/virtualmachines
|Végrehajtott ellenőrzések|
|---|
|<ul><li>A gazdakiszolgáló megfelelően működik, és?</li><li>Befejeződött a gazdagép operációs rendszer rendszerindítást?</li><li>A virtuális gépet tároló kiosztásakor és kapcsolt?</li><li>A gazdagép és a tárfiók közötti hálózati kapcsolat van?</li><li>Befejeződött a vendég operációs rendszer rendszerindítást?</li><li>Nem tervezett karbantartást?</li></ul>|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.cognitiveservices/accounts
|Végrehajtott ellenőrzések|
|---|
|<ul><li>A fiók elérhető a az adatközponton belül?</li><li>A szolgáltatások kognitív erőforrás-szolgáltató érhető el?</li><li>Érhető el a kognitív szolgáltatás a megfelelő régióban?</li><li>Elolvashatják műveletek hajthatók végre a tárfiók, az erőforrás-metaadatok okozó?</li><li>Elérte az API-hívás kvóta?</li><li>Elérte az API hívása olvasás-korlátot?</li></ul>|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.compute/virtualmachines
|Végrehajtott ellenőrzések|
|---|
|<ul><li>A kiszolgáló mentése a virtuális gépet szolgáltató, és fut?</li><li>Befejeződött a gazdagép operációs rendszer rendszerindítást?</li><li>A virtuális gépet tároló kiosztásakor és kapcsolt?</li><li>A gazdagép és a tárfiók közötti hálózati kapcsolat van?</li><li>Befejeződött a vendég operációs rendszer rendszerindítást?</li><li>Nem tervezett karbantartást?</li></ul>|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.datalakeanalytics/accounts
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Felhasználók elküldés feladatok Data Lake Analytics régióban is?</li><li>Hajtsa végre az alapvető feladatok futtatása, és sikeresen régióban?</li><li>Felhasználók készíthetünk a szolgáltatáskatalógusban található elemek a régióban?</li>|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.datalakestore/accounts
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Felhasználók adatait feltöltheti Data Lake Store régióban?</li><li>Felhasználók is a Data Lake Store régióban le adatokat?</li></ul>|

## <a name="microsoftdevicesiothubs"></a>Microsoft.devices/iothubs

|Végrehajtott ellenőrzések|
|---|
|<ul><li>Az IoT hub megfelelően működik, és?</li></ul>|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.documentdb/databaseAccounts
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Minden adatbázis vagy a gyűjtemény kérést, nem szolgálható ki egy Azure Cosmos DB szolgáltatás elérhetetlensége miatt nem lett volna?</li><li>A dokumentum kéréseit nem szolgálható ki egy Azure Cosmos DB szolgáltatás elérhetetlensége miatt nem lett volna?</li></ul>|

## <a name="microsoftnetworkconnections"></a>Microsoft.network/connections
|Végrehajtott ellenőrzések|
|---|
|<ul><li>A VPN-alagút csatlakoztatva van?</li><li>Vannak-e konfigurációjának ütközései a kapcsolatot?</li><li>Az előmegosztott kulcs megfelelően legyenek konfigurálva?</li><li>A helyszíni VPN-eszköz érhető el?</li><li>Vannak eltérések a IPSec/IKE biztonsági házirendben?</li><li>Az az S2S VPN-kapcsolat megfelelően kiosztott vagy hibás állapotban lévő?</li><li>Megfelelően kiosztott vagy hibás állapotban, az a VNET – VNET-kapcsolatot?</li></ul>|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.network/virtualNetworkGateways
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Az internetről elérhető a VPN-átjáró?</li><li>A VPN-átjáró készenléti üzemmódban van?</li><li>A VPN szolgáltatást az átjáró fut?</li></ul>|

## <a name="microsoftnotificationhubsnamespace"></a>Microsoft.NotificationHubs/namespace
|Végrehajtott ellenőrzések|
|---|
|<ul><li> A névtér elvégezhető futásidejű műveletek tartoznak, mint a regisztráció, a telepítés vagy a küldési?</li></ul>|

## <a name="microsoftpowerbiworkspacecollections"></a>Microsoft.PowerBI/workspaceCollections
|Végrehajtott ellenőrzések|
|---|
|<ul><li>A gazda operációs rendszer megfelelően működik, és?</li><li>Az elérhető az adatközponton kívülről a workspaceCollection?</li><li>A Power bi erőforrás-szolgáltató érhető el?</li><li>Érhető el a Power bi szolgáltatás a megfelelő régióban?</li></ul>|

## <a name="microsoftsearchsearchservices"></a>Microsoft.search/searchServices
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Diagnosztikai műveletek végezhetők a fürt?</li></ul>|

## <a name="microsoftsqlserverdatabase"></a>Microsoft.SQL/Server/database
|Végrehajtott ellenőrzések|
|---|
|<ul><li> Nem volt az adatbázis-felhasználónevekhez?</li></ul>|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs
|Végrehajtott ellenőrzések|
|---|
|<ul><li>Ha a feladat végrehajtása be, és fut minden gazdagép vannak?</li><li>Nem lehetett elindítani a feladatot?</li><li>Vannak-e folyamatban lévő futásidejű frissítéseket?</li><li>A feladat várt állapota (például fut vagy leállt ügyfél)?</li><li>A feladat észlelt kimenő memória kivételek?</li><li>Vannak-e folyamatban lévő ütemezett számítási frissítéseket?</li><li>A végrehajtás-kezelője (vezérlési tervek) érhető el?</li></ul>|

## <a name="microsoftwebserverfarms"></a>Microsoft.web/serverFarms
|Végrehajtott ellenőrzések|
|---|
|<ul><li>A gazdakiszolgáló megfelelően működik, és?</li><li>Az Internet Information Services fut?</li><li>A Load balancer fut?</li><li>Az App Service-csomag elérhető a az adatközponton belül?</li><li>A tárfiók üzemelteti a kiszolgálófarm elérhető webhelyek tartalmának??</li></ul>|

## <a name="microsoftwebsites"></a>Microsoft.web/sites
|Végrehajtott ellenőrzések|
|---|
|<ul><li>A gazdakiszolgáló megfelelően működik, és?</li><li>Internet Information server fut?</li><li>A Load balancer fut?</li><li>A webalkalmazás elérhetőségének a az adatközponton belül?</li><li>A tárfiók üzemeltető érhető el a webhely tartalmát?</li></ul>|

# <a name="next-steps"></a>További lépések
-  Lásd: [Bevezetés az Azure szolgáltatás állapota irányítópult](service-health-overview.md) és [Bevezetés az Azure Resource Health](resource-health-overview.md) bővebb információt őket. 
-  [Az Azure Resource Health kapcsolatos gyakori kérdések](resource-health-faq.md)
- Riasztások beállítása, így ügynökállapottal kapcsolatos hibákkal értesítést kap. További információkért lásd: [riasztások konfigurálása a szolgáltatás állapotával kapcsolatos események](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md). 
