---
title: Azure Service Fabric-frissítések
description: Kibocsátási megjegyzések a legújabb funkciók és fejlesztések a Service Fabricben.
author: athinanthny
manager: chackdan
ms.author: atsenthi
ms.date: 6/10/2019
ms.topic: conceptual
ms.service: service-fabric
hide_comments: true
hideEdit: true
ms.openlocfilehash: 78fb96cae3d3d128da608183f37771b2ecf66dcf
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165355"
---
# <a name="service-fabric-releases"></a>A Service Fabric-kiadások

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">Hibaelhárítási útmutatók</a> 
| <a href="https://github.com/Azure/service-fabric-issues" target="blank">Problémakövetés</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">-támogatással kapcsolatos lehetőségekről</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">támogatott verziók</a>  
|  <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">-Kódminták</a>

Ez a cikk további információkkal szolgál a legújabb kiadásainak listáját és a frissítések a Service Fabric-futtatókörnyezet és az SDK-k.

## <a name="whats-new-in-service-fabric"></a>**Újdonságok a Service Fabric**

### <a name="service-fabric-65"></a>A Service Fabric 6.5-ös

A legújabb Service Fabric-kiadás tartalmazza a támogatási lehetőségek, a megbízhatóság és a teljesítménnyel kapcsolatos fejlesztések, új funkciókat, a hibajavítások és a továbbfejlesztett megkönnyítése érdekében a fürt és alkalmazáséletciklus-kezelés.

> [!IMPORTANT]
> Service Fabric 6.5-ös végleges a Service Fabric tools támogatja a Visual Studio 2015. Ügyfelek javasolja, hogy helyezze át [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) a jövőben.

Újdonságok a Service Fabric 6.5-ös itt látható:

- A Service Fabric Explorer tartalmaz egy [Image Store Viewer](service-fabric-visualizing-your-cluster.md#image-store-viewer) vizsgálatával alkalmazásokat az Ön által feltöltött lemezképtárolójába.

- [Patch Orchestration Application (POA)](service-fabric-patch-orchestration-application.md) verzió [1.4.0-s](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) self-diagnostic számos fejlesztést tartalmaz. POA ügyfelei számára ajánlott, hogy helyezze át erre a verzióra.

- [Az EventStore szolgáltatás alapértelmezés szerint engedélyezve van](service-fabric-visualizing-your-cluster.md#event-store) 6.5 a Service Fabric-fürtök, kivéve, ha azt választotta, hogy.

- Hozzáadott [replika életciklusesemények](service-fabric-diagnostics-event-generation-operational.md#replica-events) az állapotalapú szolgáltatások esetében.

- [Kezdőérték csomópont állapota látható-e jobb](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status), beleértve a fürt szintű figyelmeztetések, ha a kezdőérték csomópont állapota nem megfelelő (*le*, *el lett távolítva* vagy *ismeretlen*).

- [Service Fabric Application vész helyreállítási eszköz](https://github.com/Microsoft/Service-Fabric-AppDRTool) lehetővé teszi, hogy a Service Fabric állapotalapú szolgáltatások a gyors helyreállításra, ha az elsődleges fürt fordul elő egy vészhelyreállítási. Elsődleges-fürtből származó adatokat a rendszer folyamatosan szinkronizálja a másodlagos készenléti alkalmazás rendszeres biztonsági mentés és visszaállítás használatával.

- A Visual Studio támogatása [közzététele a .NET Core alkalmazásokat a Linux-alapú fürtök](service-fabric-how-to-publish-linux-app-vs.md).

- [Az Azure Service Fabric parancssori felület (SFCTL)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) automatikusan települ a Service Fabric 6.5-ös (és újabb verziók) Ha frissíti vagy egy új Linux-fürt létrehozása az Azure-ban.

- [SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) beépített MacOS és Linux-fürtökön alapértelmezés szerint telepítve van.

További részletekért tekintse meg a [Service Fabric 6.5-ös kiadási megjegyzései](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf).

## <a name="previous-versions"></a>Korábbi verziók

### <a name="service-fabric-64-releases"></a>Service Fabric 6.4 kiadások

| Kiadási dátum | Kiadás | További információ |
|---|---|---|
| 2018. november 30. | [Azure Service Fabric 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [Kibocsátási megjegyzések](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 2018. december 12. | [Az Azure Service Fabric 6.4 frissítése a kiadásban a Windows-fürtök](https://blogs.msdn.microsoft.com/azureservicefabric/2018/12/12/azure-service-fabric-6-4-refresh-for-windows-clusters/)  | [Kibocsátási megjegyzések](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 2019. február 4. | [Az Azure Service Fabric 6.4 frissítési kiadás](https://blogs.msdn.microsoft.com/azureservicefabric/2019/02/04/azure-service-fabric-6-4-refresh-release/) | [Kibocsátási megjegyzések](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 2019. március 4. | [Az Azure Service Fabric 6.4 frissítési kiadás](https://blogs.msdn.microsoft.com/azureservicefabric/2019/03/12/azure-service-fabric-6-4-refresh-release-2/) | [Kibocsátási megjegyzések](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 2019. április 8. | [Az Azure Service Fabric 6.4 frissítési kiadás](https://blogs.msdn.microsoft.com/azureservicefabric/2019/04/08/azure-service-fabric-6-4-refresh-release-5/) | [Kibocsátási megjegyzések](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2019. május 2. | [Az Azure Service Fabric 6.4 frissítési kiadás](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/02/azure-service-fabric-6-4-refresh-release-3/) | [Kibocsátási megjegyzések](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 2019. május 28. | [Az Azure Service Fabric 6.4 frissítési kiadás](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/28/azure-service-fabric-6-4-refresh-release-4/) | [Kibocsátási megjegyzések](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
