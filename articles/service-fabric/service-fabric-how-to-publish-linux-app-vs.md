---
title: További információ a létrehozása és közzététele a .net Core-alkalmazások egy távoli Azure Service Fabric Linux-fürtön |} A Microsoft Docs
description: Létrehozása és közzététele a .net Core alkalmazásoknál egy távoli Linux-fürtön a Visual Studióból
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 5/20/2019
ms.author: pepogors
ms.openlocfilehash: 46d76edbe8cede12e8c7811f43c28a65c1ebaed0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67078663"
---
# <a name="use-visual-studio-to-create-and-publish-net-core-applications-targeting-a-remote-linux-service-fabric-cluster"></a>A Visual Studio használatával létrehozása és közzététele a .net Core-alkalmazások egy távoli Linux Service Fabric-fürt
A Visual Studio eszközök, fejlesztheti és közzététele a Service Fabric .net Core alkalmazásokat célzó Linux Service Fabric-fürt. Az SDK-verziót kell 3.4-es vagy újabb telepítése egy .net Core-alkalmazás célzó Linux Service Fabric-fürtök a Visual Studióból.

> [!Note]
> A Visual Studio hibakeresési Service Fabric-alkalmazásokat, amelyek Linux cél nem támogatja.
>

## <a name="create-a-service-fabric-application-targeting-net-core"></a>Hozzon létre egy célcsoport-kezelési .net Service Fabric-alkalmazás Core
1. Indítsa el a Visual Studiót **rendszergazdaként**.
2. Hozzon létre egy projektet a **File -> New Project ->** .
3. Az a **új projekt** párbeszédpanelen válasszon **Cloud Service Fabric-alkalmazás ->** .
![create-application]
4. Nevezze el az alkalmazást, és kattintson a **Ok**.
5. Az a **új Service Fabric-szolgáltatás** lapon, válassza ki a szolgáltatás létrehozása alatt szeretné a **.Net Core szakasz**.
![create-service]

## <a name="deploy-to-a-remote-linux-cluster"></a>Egy távoli Linux-fürt üzembe helyezése
1. A megoldáskezelőben kattintson a jobb gombbal a alkalmazást, és válasszon **összeállítása**.
![build-application]
2. Az alkalmazás a létrehozási folyamat befejezését követően a szolgáltatásban kattintson a jobb gombbal, és válassza a Szerkesztés a **csproj-fájl**.
![edit-csproj]
3. Szerkessze a UpdateServiceFabricManifestEnabled tulajdonság True **hamis** Ha a szolgáltatás egy **szereplőtípus projekt**. Ha az alkalmazás nem rendelkezik az aktorszolgáltatás, ugorjon a 4. lépés.
```xml
    <UpdateServiceFabricManifestEnabled>False</UpdateServiceFabricManifestEnabled>
```
> [!Note]
> UpdateServiceFabricManifestEnabled FALSE, letiltja a ServiceManifest.xml a frissítések során egy. Minden olyan változás, ilyen módon hozzá, távolíthatja el vagy nevezze át a szolgáltatásnak nem megjelennek a ServiceManifest.xml. Ha módosítás történik, akkor kell egyikével frissítheti a servicemanifest elemben manuálisan, vagy átmenetileg állítsa igaz értékre, és hozhat létre a szolgáltatás, amely frissíti a ServiceManifest.xml, majd visszatér UpdateServiceFabricManifestEnabled biztonsági hamis értékre.
>

4. Frissítse a RuntimeIndetifier win7-x64 származó, a szolgáltatási projektben a célplatformot.
```xml
    <RuntimeIdentifier>ubuntu.16.04-x64</RuntimeIdentifier>
```
5. Rendszerképnevet a servicemanifest elemben frissítse a program a belépési pont .exe eltávolítása. 
```xml
    <EntryPoint> 
    <ExeHost> 
        <Program>Actor1</Program> 
    </ExeHost> 
    </EntryPoint>
```
6. A Megoldáskezelőben kattintson a jobb gombbal az alkalmazást, majd válassza **közzététel**. Megjelenik a **Publish** (Közzététel) párbeszédpanel.
7. A **kapcsolati végpont**, válassza ki a végpont a távoli Service Fabric Linux-fürt, amelyekre szeretné.
![publish-application]

<!--Image references-->
[create-application]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-application-remote-linux.png
[create-service]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-service-remote-linux.png
[build-application]:./media/service-fabric-how-to-vs-remote-linux-cluster/build-application-remote-linux.png
[edit-csproj]:./media/service-fabric-how-to-vs-remote-linux-cluster/edit-csproj-remote-linux.png
[publish-application]:./media/service-fabric-how-to-vs-remote-linux-cluster/publish-remote-linux.png

## <a name="next-steps"></a>További lépések
* Ismerje meg [Ismerkedés a Service Fabric és a .net Core](https://azure.microsoft.com/resources/samples/service-fabric-dotnet-core-getting-started/)
