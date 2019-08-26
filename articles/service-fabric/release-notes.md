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
ms.openlocfilehash: 636518688aec1f514b551e34219acfc1c9e81aec
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68965381"
---
# <a name="service-fabric-releases"></a>Service Fabric kiadások

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">Hibaelhárítási útmutatók</a> 
| a<a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">támogatási lehetőségek</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">támogatott verziók</a><a href="https://github.com/Azure/service-fabric-issues" target="blank"></a><a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">kód minták</a> követéséhez 
|  
| 

Ez a cikk további információkat tartalmaz a Service Fabric futtatókörnyezet és SDK-k legújabb kiadásairól és frissítéseiről.

## <a name="whats-new-in-service-fabric"></a>A Service Fabric újdonságai

### <a name="service-fabric-65"></a>Service Fabric 6,5

A legújabb Service Fabric kiadás magában foglalja a támogatás, a megbízhatóság és a teljesítmény javítását, az új funkciókat, hibajavításokat és fejlesztéseket a fürt és az alkalmazások életciklus-felügyeletének megkönnyítéséhez.

> [!IMPORTANT]
> A Service Fabric 6,5 a Visual Studio 2015 Service Fabric-eszközök támogatásának végső kiadása. Javasoljuk, hogy a [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) -es verzióra lépjen tovább.

A Service Fabric 6,5 újdonságai:

- Service Fabric Explorer tartalmaz egy [lemezképtároló](service-fabric-visualizing-your-cluster.md#image-store-viewer) -megjelenítőt a rendszerkép-áruházba feltöltött alkalmazások vizsgálatához.

- A [patch-előkészítési alkalmazás (Poa)](service-fabric-patch-orchestration-application.md) [1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) verziója számos öndiagnosztika fejlesztést tartalmaz. Javasoljuk, hogy erre a verzióra térjen át a POA ügyfeleinek.

- A [EventStore szolgáltatás alapértelmezés szerint engedélyezve van](service-fabric-visualizing-your-cluster.md#event-store) Service Fabric 6,5-fürtökön, hacsak nem választotta ki.

- Az állapot-nyilvántartó szolgáltatásokhoz hozzáadott [replika-Életciklus eseményei](service-fabric-diagnostics-event-generation-operational.md#replica-events) .

- [A vetőmag-csomópontok állapotának jobb láthatósága](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status), beleértve a fürtre vonatkozó figyelmeztetéseket, ha avetőmag-csomópontok állapota nem megfelelő (leállítva, *eltávolítva* vagy *ismeretlen*).

- [Service Fabric alkalmazás vész-helyreállítási eszköze](https://github.com/Microsoft/Service-Fabric-AppDRTool) lehetővé teszi, hogy Service Fabric állapot-nyilvántartó szolgáltatások gyorsan helyreállíthatók, amikor az elsődleges fürt vészhelyzetet tapasztal. Az elsődleges fürtből származó adatok folyamatos szinkronizálása a másodlagos készenléti alkalmazásban rendszeres biztonsági mentés és visszaállítás használatával történik.

- A Visual Studio támogatja a [.net Core-alkalmazások Linux-alapú fürtökre való közzétételét](service-fabric-how-to-publish-linux-app-vs.md).

- Az [azure Service FABRIC CLI (SFCTL)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) automatikusan települ a Service Fabric 6,5 (és újabb verziók esetében), amikor új Linux-fürtöt frissít vagy hoz létre az Azure-ban.

- A [SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) a MacOS/Linux beépített-fürtökön alapértelmezés szerint telepítve van.

További részletekért tekintse meg a [Service Fabric 6,5 kibocsátási megjegyzéseit](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf).

### <a name="service-fabric-65-releases"></a>Service Fabric 6,5 kiadás

| Kiadás dátuma | Kiadás | További információ |
|---|---|---|
| Június 11.2019 | [Azure Service Fabric 6,5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/06/11/azure-service-fabric-6-5-release/)  | [Kibocsátási megjegyzések](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2019. július 2. | [Azure Service Fabric 6,5 frissítési kiadás](https://blogs.msdn.microsoft.com/azureservicefabric/2019/07/04/azure-service-fabric-6-5-refresh-release/)  | [Kibocsátási megjegyzések](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| Július 29., 2019 | [Azure Service Fabric 6,5 frissítési kiadás](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [Kibocsátási megjegyzések](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |


## <a name="previous-versions"></a>Korábbi verziók

### <a name="service-fabric-64-releases"></a>Service Fabric 6,4 kiadás

| Kiadás dátuma | Kiadás | További információ |
|---|---|---|
| November 30., 2018 | [Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [Kibocsátási megjegyzések](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 2018. december 12. | [Azure Service Fabric 6,4 Windows-fürtök frissítési kiadása](https://blogs.msdn.microsoft.com/azureservicefabric/2018/12/12/azure-service-fabric-6-4-refresh-for-windows-clusters/)  | [Kibocsátási megjegyzések](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 2019. február 4. | [Azure Service Fabric 6,4 frissítési kiadás](https://blogs.msdn.microsoft.com/azureservicefabric/2019/02/04/azure-service-fabric-6-4-refresh-release/) | [Kibocsátási megjegyzések](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 2019. március 4. | [Azure Service Fabric 6,4 frissítési kiadás](https://blogs.msdn.microsoft.com/azureservicefabric/2019/03/12/azure-service-fabric-6-4-refresh-release-2/) | [Kibocsátási megjegyzések](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 2019. április 8. | [Azure Service Fabric 6,4 frissítési kiadás](https://blogs.msdn.microsoft.com/azureservicefabric/2019/04/08/azure-service-fabric-6-4-refresh-release-5/) | [Kibocsátási megjegyzések](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2019. május 2. | [Azure Service Fabric 6,4 frissítési kiadás](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/02/azure-service-fabric-6-4-refresh-release-3/) | [Kibocsátási megjegyzések](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 2019. május 28. | [Azure Service Fabric 6,4 frissítési kiadás](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/28/azure-service-fabric-6-4-refresh-release-4/) | [Kibocsátási megjegyzések](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
