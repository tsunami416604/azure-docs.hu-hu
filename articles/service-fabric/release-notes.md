---
title: Azure Service Fabric kiadások
description: A Service Fabric legújabb funkcióinak és tökéletesítésének kibocsátási megjegyzései.
author: athinanthny
manager: chackdan
ms.author: atsenthi
ms.date: 6/10/2019
ms.topic: conceptual
ms.service: service-fabric
hide_comments: true
hideEdit: true
ms.openlocfilehash: 4a681b3a09def3a7b27b603cf5201aebdbf2e4bf
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72386212"
---
# <a name="service-fabric-releases"></a>Service Fabric kiadások

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">hibaelhárítási útmutatók</a> 
| <a href="https://github.com/Azure/service-fabric-issues" target="blank">probléma követése</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">támogatási lehetőség</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">támogatott verziók</a> 
| <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">kód minták</a>

Ez a cikk további információkat tartalmaz a Service Fabric futtatókörnyezet és SDK-k legújabb kiadásairól és frissítéseiről.

## <a name="whats-new-in-service-fabric"></a>A Service Fabric újdonságai

### <a name="service-fabric-65"></a>Service Fabric 6,5

A legújabb Service Fabric kiadás magában foglalja a támogatás, a megbízhatóság és a teljesítmény javítását, az új funkciókat, hibajavításokat és fejlesztéseket a fürt és az alkalmazások életciklus-felügyeletének megkönnyítéséhez.

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
