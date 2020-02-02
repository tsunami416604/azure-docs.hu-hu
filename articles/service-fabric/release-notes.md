---
title: Azure Service Fabric kiadások
description: Az Azure Service Fabric kibocsátási megjegyzései. A Service Fabric legújabb szolgáltatásairól és tökéletesítéséről tartalmaz információkat.
ms.date: 06/10/2019
ms.topic: conceptual
hide_comments: true
hideEdit: true
ms.openlocfilehash: 73624df3cb7b04c3c442a6654f1388017482b2ba
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2020
ms.locfileid: "76935438"
---
# <a name="service-fabric-releases"></a>Service Fabric kiadások

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">hibaelhárítási útmutatók</a> 
| <a href="https://github.com/Azure/service-fabric-issues" target="blank">probléma követése</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">támogatási lehetőségek</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">támogatott verziók</a> 
| <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">kód minták</a>

Ez a cikk további információkat tartalmaz a Service Fabric futtatókörnyezet és SDK-k legújabb kiadásairól és frissítéseiről.

## <a name="whats-new-in-service-fabric"></a>A Service Fabric újdonságai

### <a name="service-fabric-70"></a>Service Fabric 7,0

Az Azure Service Fabric 7,0 már elérhető! A 7,0-re a Azure Portal vagy egy Azure Resource Manager üzemelő példányon keresztül frissítheti. Az üdülési időszakon kívüli kiadásokra vonatkozó vásárlói visszajelzések miatt a rendszer nem kezdi automatikusan frissíteni a fürtöket, hogy az automatikus frissítéseket a januárig kapják meg.
Januárban folytatjuk a normál kiindulási eljárást, és az automatikus frissítésekkel rendelkező fürtök automatikusan megkapják az 7,0-es frissítést. A kiindulási folyamat megkezdése előtt egy újabb bejelentést fogunk benyújtani.
A tervezett kiadási dátumokat is frissítjük, jelezve, hogy figyelembe vesszük ezt a szabályzatot. A jövőbeli [kiadási ütemtervekkel](https://github.com/Microsoft/service-fabric/#service-fabric-release-schedule)kapcsolatos frissítéseket itt találja.
 
Ez a Service Fabric legújabb kiadása, amely kulcsfontosságú funkciókkal és újdonságokkal van betöltve.

### <a name="key-announcements"></a>Legfontosabb közlemények
 - [**Az KeyVaultReference támogatása (előzetes verzió)** ](https://docs.microsoft.com/azure/service-fabric/service-fabric-keyvault-references): Service Fabric [felügyelt identitásokat](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity) engedélyező alkalmazások mostantól közvetlenül hivatkozhatnak Key Vault titkos URL-címre környezeti változóként, Application paraméterként vagy Container adattárbeli hitelesítő adatokként. Service Fabric automatikusan feloldja a titkot az alkalmazás felügyelt identitásával. 
     
- **Javított biztonság az állapot nélküli szolgáltatások esetében**: az alkalmazások frissítése során a rendelkezésre állás biztosítása érdekében új konfigurációkat vezettünk be, amelyekkel meghatározható, hogy az [állapot nélküli szolgáltatások milyen számú példányban](https://docs.microsoft.com/dotnet/api/system.fabric.description.statelessservicedescription?view=azure-dotnet) legyenek elérhetők. Korábban ez az érték 1 volt az összes szolgáltatás esetében, és nem módosítható. Ezzel az új, szolgáltatásként nyújtott biztonsági ellenőrzéssel biztosíthatja, hogy a szolgáltatások az alkalmazások frissítése, a fürtök frissítése és a Service Fabric állapotának és biztonsági ellenőrzésének egyéb karbantartása során minimális számú példányban maradjanak.
  
- [**Felhasználói szolgáltatások erőforrás-korlátai**](https://docs.microsoft.com/azure/service-fabric/service-fabric-resource-governance#enforcing-the-resource-limits-for-user-services): a felhasználók erőforrás-korlátozásokat állíthatnak be a csomópont felhasználói szolgáltatásaihoz, így elkerülhetők például az Service Fabric rendszerszolgáltatások erőforrás-kimerülése. 
  
- A replika típusának [**nagyon magas szolgáltatási mozgatási díja**](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-movement-cost) . A nagyon magas áthelyezési költségeket tartalmazó replikák csak akkor lesznek áthelyezve, ha a fürtben nem lehet más módon megállapítani a korlátozás megsértését. A "nagyon magas" áthelyezési díjak használata esetén a dokumentációban talál további információkat, és további szempontokat is figyelembe kell vennie.
  
-  **További fürt biztonsági ellenőrzései**: ebben a kiadásban egy konfigurálható vetőmag-csomópont Kvórumának biztonsági ellenőrzését ismertetjük. Ez lehetővé teszi annak testreszabását, hogy hány mag-csomópontnak kell elérhetőnek lennie a fürt életciklusa és kezelési forgatókönyvei során. Azok a műveletek, amelyeken a fürt a konfigurált érték alá kerülne, le vannak tiltva. Napjainkban az alapértelmezett érték mindig a vetőmag-csomópontok kvóruma, például ha 7 vetőmag-csomóponttal rendelkezik, a rendszer alapértelmezés szerint letilt egy műveletet, amely 5 mag csomópont alatt lenne. Ezzel a módosítással megteheti a minimálisan szükséges 6 értéket, ami lehetővé tenné, hogy egyszerre csak egy mag-csomópont legyen letiltva.
   
- [**A Service Fabric Explorer-ben a biztonsági mentési és visszaállítási szolgáltatás felügyeletének**](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-quickstart-azurecluster)támogatása. Így a következő tevékenységek közvetlenül elérhetővé tehetők az SFX környezetből: a biztonsági mentési és visszaállítási szolgáltatás, a biztonsági mentési szabályzat létrehozása, az automatikus biztonsági mentések, az alkalmi biztonsági másolatok, a visszaállítási műveletek elindítása és a meglévő biztonsági másolatok tallózása.

- A [**ReliableCollectionsMissingTypesTool**](https://github.com/hiadusum/ReliableCollectionsMissingTypesTool)rendelkezésre állásának bejelentése: ez az eszköz segít ellenőrizni, hogy a megbízható gyűjteményekben használt típusok továbbítva és visszamenőlegesen kompatibilisek-e a működés közbeni alkalmazások frissítésekor. Ez segít megelőzni a frissítési hibákat, illetve az adatvesztést és az adatsérülést a hiányzó vagy inkompatibilis típusok miatt.

- [**Stabil olvasások engedélyezése a másodlagos replikák**](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-configuration#configuration-names-1)esetében: a stabil olvasások a kvórum nyugtázva tartozó értékek visszaadása érdekében korlátozzák a másodlagos replikákat.

Emellett ez a kiadás más új funkciókat, hibajavításokat, valamint a támogatás, a megbízhatóság és a teljesítmény javítását is tartalmazza. A módosítások teljes listájáért tekintse meg a [kibocsátási megjegyzéseket](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md).

### <a name="service-fabric-70-releases"></a>Service Fabric 7,0 kiadás

| Kiadási dátum | Kiadás | További információ |
|---|---|---|
| November 18., 2019 | [Azure Service Fabric 7,0](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Service-Fabric-7-0-Release/ba-p/1015482)  | [Kiadási megjegyzések](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)|
| 2020. január 30-ig | [Azure Service Fabric 7,0 frissítési kiadás](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-second-refresh-release/ba-p/1137690)  | [Kiadási megjegyzések](https://github.com/Azure/service-fabric/blob/master/release_notes/Service-Fabric-70CU2-releasenotes.md)|


### <a name="service-fabric-65"></a>Service Fabric 6,5

Ez a kiadás támogatja a támogatás, a megbízhatóság és a teljesítmény fejlesztését, az új funkciókat, hibajavításokat és fejlesztéseket a fürt és az alkalmazások életciklus-felügyeletének megkönnyítéséhez.

> [!IMPORTANT]
> A Service Fabric 6,5 a Visual Studio 2015 Service Fabric-eszközök támogatásának végső kiadása. Javasoljuk, hogy a [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) -es verzióra lépjen tovább.

A Service Fabric 6,5 újdonságai:

- Service Fabric Explorer tartalmaz egy [lemezképtároló-megjelenítőt](service-fabric-visualizing-your-cluster.md#image-store-viewer) a rendszerkép-áruházba feltöltött alkalmazások vizsgálatához.

- A [patch-előkészítési alkalmazás (Poa)](service-fabric-patch-orchestration-application.md) [1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) verziója számos öndiagnosztika fejlesztést tartalmaz. Javasoljuk, hogy erre a verzióra térjen át a POA ügyfeleinek.

- A [EventStore szolgáltatás alapértelmezés szerint engedélyezve van](service-fabric-visualizing-your-cluster.md#event-store) Service Fabric 6,5-fürtökön, hacsak nem választotta ki.

- Az állapot-nyilvántartó szolgáltatásokhoz hozzáadott [replika-Életciklus eseményei](service-fabric-diagnostics-event-generation-operational.md#replica-events) .

- [A vetőmag-csomópontok állapotának jobb láthatósága](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status), beleértve a fürtre vonatkozó figyelmeztetéseket, ha a vetőmag-csomópontok állapota nem*megfelelő (* leállítva, *eltávolítva* vagy *ismeretlen*).

- [Service Fabric alkalmazás vész-helyreállítási eszköze](https://github.com/Microsoft/Service-Fabric-AppDRTool) lehetővé teszi, hogy Service Fabric állapot-nyilvántartó szolgáltatások gyorsan helyreállíthatók, amikor az elsődleges fürt vészhelyzetet tapasztal. Az elsődleges fürtből származó adatok folyamatos szinkronizálása a másodlagos készenléti alkalmazásban rendszeres biztonsági mentés és visszaállítás használatával történik.

- A Visual Studio támogatja a [.net Core-alkalmazások Linux-alapú fürtökre való közzétételét](service-fabric-how-to-publish-linux-app-vs.md).

- Az [azure Service FABRIC CLI (SFCTL)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) automatikusan települ a Service Fabric 6,5 (és újabb verziók esetében), amikor új Linux-fürtöt frissít vagy hoz létre az Azure-ban.

- A [SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) a MacOS/Linux beépített-fürtökön alapértelmezés szerint telepítve van.

További részletekért tekintse meg a [Service Fabric 6,5 kibocsátási megjegyzéseit](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf).

### <a name="service-fabric-65-releases"></a>Service Fabric 6,5 kiadás

| Kiadási dátum | Kiadás | További információ |
|---|---|---|
| Június 11.2019 | [Azure Service Fabric 6,5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/06/11/azure-service-fabric-6-5-release/)  | [Kiadási megjegyzések](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2019. július 2. | [Azure Service Fabric 6,5 frissítési kiadás](https://blogs.msdn.microsoft.com/azureservicefabric/2019/07/04/azure-service-fabric-6-5-refresh-release/)  | [Kiadási megjegyzések](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| Július 29., 2019 | [Azure Service Fabric 6,5 frissítési kiadás](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [Kiadási megjegyzések](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| Augusztus 23., 2019 | [Azure Service Fabric 6,5 frissítési kiadás](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [Kiadási megjegyzések](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |
| Október 14., 2019 | [Azure Service Fabric 6,5 frissítési kiadás](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Fifth-Refresh-Release/ba-p/913296)  | [Kiadási megjegyzések](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU5.md)  |


## <a name="previous-versions"></a>Korábbi verziók

### <a name="service-fabric-64-releases"></a>Service Fabric 6,4 kiadás

| Kiadási dátum | Kiadás | További információ |
|---|---|---|
| November 30., 2018 | [Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [Kiadási megjegyzések](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 2018. december 12. | [Azure Service Fabric 6,4 Windows-fürtök frissítési kiadása](https://blogs.msdn.microsoft.com/azureservicefabric/2018/12/12/azure-service-fabric-6-4-refresh-for-windows-clusters/)  | [Kiadási megjegyzések](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 2019. február 4. | [Azure Service Fabric 6,4 frissítési kiadás](https://blogs.msdn.microsoft.com/azureservicefabric/2019/02/04/azure-service-fabric-6-4-refresh-release/) | [Kiadási megjegyzések](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 2019. március 4. | [Azure Service Fabric 6,4 frissítési kiadás](https://blogs.msdn.microsoft.com/azureservicefabric/2019/03/12/azure-service-fabric-6-4-refresh-release-2/) | [Kiadási megjegyzések](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 2019. április 8. | [Azure Service Fabric 6,4 frissítési kiadás](https://blogs.msdn.microsoft.com/azureservicefabric/2019/04/08/azure-service-fabric-6-4-refresh-release-5/) | [Kiadási megjegyzések](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2019. május 2. | [Azure Service Fabric 6,4 frissítési kiadás](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/02/azure-service-fabric-6-4-refresh-release-3/) | [Kiadási megjegyzések](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 2019. május 28. | [Azure Service Fabric 6,4 frissítési kiadás](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/28/azure-service-fabric-6-4-refresh-release-4/) | [Kiadási megjegyzések](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
