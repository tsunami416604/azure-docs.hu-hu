---
title: A fürt Azure Service Fabric Explorerrel megjelenítése |} Microsoft Docs
description: Service Fabric Explorer alkalmazás ellenőrzése és kezelése a felhőalapú alkalmazások és a Microsoft Azure Service Fabric-fürt csomópontjának.
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.assetid: c875b993-b4eb-494b-94b5-e02f5eddbd6a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2018
ms.author: mikhegn
ms.openlocfilehash: 916742d89447af4097d37b5d78e97ff86c12834c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="visualize-your-cluster-with-service-fabric-explorer"></a>A fürt megjelenítése a Service Fabric Explorerrel

Service Fabric Explorer (SFX) egy olyan nyílt forráskódú eszköz ellenőrzi, és az Azure Service Fabric-fürtök kezelése. Service Fabric Explorer egy asztali alkalmazás Windows és Linux. MacOS támogatása hamarosan elérhető.

## <a name="service-fabric-explorer-download"></a>Service Fabric Explorer letöltése

Az alábbi hivatkozásokat követve töltse le a Service Fabric Explorer egy asztali alkalmazás:

- Windows
  - https://aka.ms/sfx-windows

- Linux
  - https://aka.ms/sfx-linux-x86
  - https://aka.ms/sfx-linux-x64

- macOS
  - https://aka.ms/sfx-macos

> [!NOTE]
> A Service Fabric Explorer asztali verzióját rendelkezhet több vagy kevesebb, mint a fürt támogatása szolgáltatások. Ön visszatérhet a teljes szolgáltatás a kompatibilitás érdekében a fürt telepített Service Fabric Explorer verziója.
>
>

### <a name="running-service-fabric-explorer-from-the-cluster"></a>Service Fabric Explorer fut a fürtön

Service Fabric Explorer is található a Service Fabric-fürt HTTP felügyeleti végpontja. Indítsa el a SFX webböngészőben, navigáljon a HTTP-felügyeleti végpont a fürt minden böngészőből - például https://clusterFQDN:19080.

A fejlesztő munkaállomás telepítő, elindíthatja a Service Fabric Explorer a helyi fürtön lévő útvonalon https://localhost:19080/Explorer. Tekintse meg jelen cikk [a fejlesztőkörnyezet előkészítése](service-fabric-get-started.md).

## <a name="connect-to-a-service-fabric-cluster"></a>Csatlakozás a Service Fabric-fürt
A Service Fabric-fürt csatlakozni kell a a fürtök felügyeleti végpont (FQDN vagy IP-) és a HTTP felügyeleti végpont portja (alapértelmezés szerint 19080). Például: https://mysfcluster.westus.cloudapp.azure.com:19080. A "Localhost csatlakozás" jelölőnégyzet segítségével csatlakozzon a helyi fürthöz a munkaállomáson.

### <a name="connect-to-a-secure-cluster"></a>Csatlakozás biztonságos fürthöz
A Service Fabric fürt tanúsítványok, valamint az Azure Active Directory (AAD) használó ügyfél-hozzáférési szabályozhatja.

Ha egy biztonságos fürt csatlakozni próbál, majd függően a fürtkonfiguráció meg kell ügyfél-tanúsítvány létezik, vagy jelentkezzen be az AAD.

## <a name="video-tutorial"></a>Oktatóvideó

Megtudhatja, hogyan használhatja a Service Fabric Explorer, a következő Microsoft Virtual Academy videót:

> [!NOTE]
> Ez a videó bemutatja, hogy a Service Fabric-fürt, nem asztali verzióját tárolt Service Fabric Explorerben talál.
>
>

[<center><img src="./media/service-fabric-visualizing-your-cluster/SfxVideo.png" WIDTH="360" HEIGHT="244"></center>](https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=bBTFg46yC_9806218965)

## <a name="understand-the-service-fabric-explorer-layout"></a>A Service Fabric Explorer elrendezés ismertetése
Service Fabric Explorer navigálhat a bal oldali fában használatával. A fa gyökerében a fürt irányítópult áttekintése a fürt, beleértve az alkalmazás és a csomópont állapotának összegzését.

![Service Fabric Explorer fürt irányítópult][sfx-cluster-dashboard]

### <a name="view-the-clusters-layout"></a>A fürt Elrendezés megtekintése
A Service Fabric-fürt csomópontjának kerülnek, egy kétdimenziós rácsot a tartalék tartományok között, és a frissítési tartományt. Az elhelyezési biztosítja, hogy az alkalmazások továbbra is elérhető a hardver meghibásodása és alkalmazásfrissítések. A jelenlegi fürthöz van elrendezését a betűcsoport-leképezés használatával tekintheti meg.

![Service Fabric Explorer betűcsoport-leképezés][sfx-cluster-map]

### <a name="view-applications-and-services"></a>Nézet alkalmazások és szolgáltatások
A fürt két részfák tartalmazza: egyet az alkalmazások és más csomópontok.

Az alkalmazás nézet segítségével haladjon végig a Service Fabric logikai hierarchia: alkalmazások, szolgáltatások, partíciók és replikáit.

Az alkalmazás az alábbi példában a **MyApp** két szolgáltatásból áll **MyStatefulService** és **WebService**. Mivel a **MyStatefulService** állapotalapú, egy elsődleges és két másodlagos replika partíciót tartalmaz. Ezzel szemben WebSvcService állapot nélküli, és egyetlen példányt tartalmaz.

![Service Fabric Explorer alkalmazás megtekintése][sfx-application-tree]

A fa egyes szintjein a fő ablaktáblán a cikk vonatkozó információkat jeleníti meg. Például láthatja a állapotát és egy adott szolgáltatáshoz verziója.

![Service Fabric Explorer essentials ablaktábla][sfx-service-essentials]

### <a name="view-the-clusters-nodes"></a>A fürtcsomópontok megtekintése
A csomópontnézet a fürt fizikai elrendezését mutatja. Az egyes csomópontoknál megtekintheti, hogy melyik alkalmazások kódja üzemel az adott csomóponton. Pontosabban láthatja, melyik replikák futó van.

## <a name="actions"></a>Műveletek
Service Fabric Explorer műveleteket az egyes csomópontok, alkalmazások és szolgáltatások a fürtön belüli meghívása gyors lehetőséget kínál.

Például egy alkalmazáspéldányt törléséhez válassza ki az alkalmazást a a bal oldali fában, és válassza **műveletek** > **alkalmazás törlése**.

![A Service Fabric Explorerben alkalmazás törlése][sfx-delete-application]

> [!TIP]
> Minden elem jelre kattintva ugyanazokat a műveleteket végezheti el.
>
> Minden művelet, amely a Service Fabric Explorer segítségével végezheti el is PowerShell vagy a REST API-t automatizálására végezhető el.
>
>

Service Fabric Explorer használatával hozzon létre egy adott alkalmazás típusa vagy a termékverzió alkalmazáspéldányok. Az alkalmazástípus válassza a faszerkezetes nézetben, majd kattintson a **app-példány létrehozása** mellett a verzióra szeretné, hogy a jobb oldali ablaktáblán látható hivatkozásra.

![Az alkalmazáspéldány létrehozása a Service Fabric Explorerben][sfx-create-app-instance]

> [!NOTE]
> Service Fabric Explorer nem támogatja a paraméterek alkalmazáspéldányok létrehozásakor. Az alkalmazáspéldányok alapértelmezett paraméterértékek használata.
>
>

## <a name="next-steps"></a>További lépések
* [A Visual Studio a Service Fabric-alkalmazások kezelése](service-fabric-manage-application-in-visual-studio.md)
* [A Service Fabric-alkalmazás központi telepítésének PowerShell használatával](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[sfx-cluster-dashboard]: ./media/service-fabric-visualizing-your-cluster/SfxClusterDashboard.png
[sfx-cluster-map]: ./media/service-fabric-visualizing-your-cluster/SfxClusterMap.png
[sfx-application-tree]: ./media/service-fabric-visualizing-your-cluster/SfxApplicationTree.png
[sfx-service-essentials]: ./media/service-fabric-visualizing-your-cluster/SfxServiceEssentials.png
[sfx-delete-application]: ./media/service-fabric-visualizing-your-cluster/SfxDeleteApplication.png
[sfx-create-app-instance]: ./media/service-fabric-visualizing-your-cluster/SfxCreateAppInstance.png