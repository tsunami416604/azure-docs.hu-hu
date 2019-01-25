---
title: A fürt az Azure Service Fabric Explorerrel megjelenítése |} A Microsoft Docs
description: A Service Fabric Explorer vizsgálatához és kezeléséhez a felhőalapú alkalmazások és a egy Microsoft Azure Service Fabric-fürtben található csomópontok alkalmazásként jelenik meg.
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
ms.date: 01/24/2019
ms.author: mikhegn
ms.openlocfilehash: dc979e4aa9882960b86d902266793910ce6fe8b3
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2019
ms.locfileid: "54902958"
---
# <a name="visualize-your-cluster-with-service-fabric-explorer"></a>A fürt megjelenítése a Service Fabric Explorerrel

Service Fabric Explorer (SFX) egy olyan nyílt forráskódú eszköz, vizsgálatához és az Azure Service Fabric-fürtök kezeléséhez. A Service Fabric Explorer asztali alkalmazások Windows, macOS és Linux.

## <a name="service-fabric-explorer-download"></a>A Service Fabric Explorer letöltése

Az alábbi hivatkozások segítségével töltheti le a Service Fabric Explorer asztali alkalmazás:

- Windows
  - https://aka.ms/sfx-windows

- Linux
  - https://aka.ms/sfx-linux-x86
  - https://aka.ms/sfx-linux-x64

- macOS
  - https://aka.ms/sfx-macos

> [!NOTE]
> A Service Fabric Explorer asztali verzióját rendelkezhet több vagy kevesebb funkciókat, mint a fürtök támogatása. Visszatérhet a Service Fabric Explorer verziója a fürt összes funkciója a kompatibilitás érdekében.
>
>

### <a name="running-service-fabric-explorer-from-the-cluster"></a>A fürtön futó Service Fabric Explorerben

A Service Fabric Explorer egy Service Fabric-fürt HTTP-felügyeleti végpont is üzemel. SFX elindíthatja egy webböngészőben, keresse meg a HTTP-felügyeleti végpont a fürt minden böngészőből – például https://clusterFQDN:19080.

A fejlesztő munkaállomás telepítő, elindíthatja a Service Fabric Explorer a helyi fürtön lévő lépve https://localhost:19080/Explorer. Tekintse meg ebben a cikkben [a fejlesztési környezet előkészítését](service-fabric-get-started.md).

> [!NOTE]
> Ha hibaüzenetet kap a webböngészőből önaláírt tanúsítvány védi a fürt "ezen a helyen, nem biztonságos". Egyszerűen végrehajtható a legtöbb modern böngésző keresztül letiltásával a figyelmeztetést. Éles környezetben a fürt védelméhez köznapi név és a egy tanúsítvány kibocsátott tanúsítvány használatával. 
>
>

## <a name="connect-to-a-service-fabric-cluster"></a>Kapcsolódás a Service Fabric-fürt
A Service Fabric-fürthöz csatlakozik, szüksége van a fürt felügyeleti végpontja (FQDN vagy IP) és a HTTP felügyeleti végpont portjára (19080 alapértelmezés szerint). Például: https://mysfcluster.westus.cloudapp.azure.com:19080. A "Connect to localhost" jelölőnégyzetet ezen a munkaállomáson a helyi fürthöz való csatlakozáshoz.

### <a name="connect-to-a-secure-cluster"></a>Csatlakozás biztonságos fürthöz
A Service Fabric-fürthöz, tanúsítványok vagy Azure Active Directory (AAD) használatával való ügyfél-hozzáférési szabályozhatja.

Ha megpróbálja egy biztonságos fürthöz való csatlakozáshoz, majd a fürt konfigurációjától függően kell adnia ügyféltanúsítvány jelen, vagy jelentkezzen be aad-ben való.

## <a name="video-tutorial"></a>Oktatóvideó

Megtudhatja, hogyan használható a Service Fabric Explorer, az alábbi Microsoft Virtual Academy-videó megtekintése:

> [!NOTE]
> Ez a videó bemutatja a Service Fabric Explorer üzemeltetett Service Fabric-fürt, az asztali verziót.
>
>

[<center><img src="./media/service-fabric-visualizing-your-cluster/SfxVideo.png" WIDTH="360" HEIGHT="244"></center>](https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=bBTFg46yC_9806218965)

## <a name="understand-the-service-fabric-explorer-layout"></a>A Service Fabric Explorer elrendezésének ismertetése
A fa használatával a bal oldali navigációs sávja a Service Fabric Explorer. A fa gyökérmappájában a fürt irányítópultja áttekintést a fürtről, beleértve az alkalmazás és a csomópontok állapotának összefoglalását.

![A Service Fabric Explorer fürt irányítópultja][sfx-cluster-dashboard]

### <a name="view-the-clusters-layout"></a>A fürt Elrendezés megtekintése
Egy Service Fabric-fürtben található csomópontok egy kétdimenziós rácsot a tartalék tartományok között vannak elhelyezve, és frissítési tartományokban tárolja. Az elhelyezési biztosítja, hogy az alkalmazások hardver-meghibásodásokkal és alkalmazásfrissítések elérhető marad. Hogyan az aktuális fürt van leírva a fürttérkép használatával tekintheti meg.

![A Service Fabric Explorer fürttérkép][sfx-cluster-map]

### <a name="view-applications-and-services"></a>Az alkalmazás- és szolgáltatások
A fürt két részfából tartalmaz: egyet az alkalmazások és a egy másik a csomópontok.

Az alkalmazás nézet segítségével keresse meg a Service Fabric logikai hierarchián keresztül: alkalmazások, szolgáltatások, a partíciók és replikáit.

Az alkalmazás az alábbi példában a **MyApp** két szolgáltatásból áll **MyStatefulService** és **webszolgáltatás**. Mivel **MyStatefulService** állapotalapú, az egy elsődleges és két másodlagos replika egy partíciót tartalmaz. Ezzel szemben az WebSvcService állapot nélküli, és egyetlen példányt tartalmaz.

![A Service Fabric Explorer alkalmazás nézet][sfx-application-tree]

A fa minden szinten a fő panelen a elemről a profiljával kapcsolatos információkat jeleníti meg. Például megtekintheti az állapotát és a egy adott szolgáltatáshoz készült.

![A Service Fabric Explorer alapvető erőforrások panelje][sfx-service-essentials]

### <a name="view-the-clusters-nodes"></a>A fürtcsomópontok megtekintése
A csomópontnézet a fürt fizikai elrendezését mutatja. Az egyes csomópontoknál megtekintheti, hogy melyik alkalmazások kódja üzemel az adott csomóponton. Pontosabban láthatja, melyik replika jelenleg futnak van.

## <a name="actions"></a>Műveletek
Service Fabric Explorert a tevékenységeket a csomópontokon, alkalmazások és szolgáltatások a fürtben lévő gyors módszert kínál.

Például az alkalmazáspéldány törléséhez válassza a bal oldali fában az alkalmazást, és válassza **műveletek** > **alkalmazás törlése**.

![A Service Fabric Explorerben alkalmazás törlése][sfx-delete-application]

> [!TIP]
> Minden elem melletti három pontra kattintva ugyanazokat a műveleteket hajthat végre.
>
> Minden művelet, amely a Service Fabric Explorer segítségével végezheti el is végrehajtható-automatizálás engedélyezése a PowerShell vagy a REST API használatával.
>
>

A Service Fabric Explorer segítségével alkalmazáspéldány számára egy adott alkalmazás típusát és verzióját. A faszerkezetes nézetben válassza ki az alkalmazás típusát, majd kattintson a **app-példány létrehozása** mellett a verzióra szeretné, hogy a jobb oldali ablaktáblán látható hivatkozásra.

![Hozzon létre egy alkalmazás-példányt a Service Fabric Explorerben][sfx-create-app-instance]

> [!NOTE]
> A Service Fabric Explorer nem támogatja a paraméterek alkalmazáspéldányok létrehozásakor. Az alkalmazáspéldányok alapértelmezett paraméterértékek használata.
>
>

## <a name="event-store"></a>Esemény Store
Az EventStore a platform által biztosított Service Fabric platform eseményeket a Service Fabric Explorert, és a REST API-n keresztül elérhető funkciója. Láthatja, mi történik a fürtben az egyes entitásokhoz például csomópont, service, alkalmazás és az esemény időpontjában a lekérdezéshez egy pillanatképnézet. Emellett az EventStore található további információ a [az EventStore áttekintése](service-fabric-diagnostics-eventstore.md).   

![EventStore][sfx-eventstore]

>[!NOTE]
>Kezdődően a Service Fabric verziója 6.4. Az EventStore alapértelmezés szerint nincs engedélyezve, és a resource manager-sablonban engedélyezve kell lennie

>[!NOTE]
>Kezdődően a Service Fabric verziója 6.4. az EventStore API-k csak Azure-on futó Windows-fürtök csak érhetők el. Ez a funkció a Linux, valamint az önálló fürtök portolása dolgozunk.


## <a name="next-steps"></a>További lépések
* [A Visual Studióban a Service Fabric-alkalmazások kezelése](service-fabric-manage-application-in-visual-studio.md)
* [A Service Fabric-alkalmazás központi telepítésének PowerShell-lel](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[sfx-cluster-dashboard]: ./media/service-fabric-visualizing-your-cluster/SfxClusterDashboard.png
[sfx-cluster-map]: ./media/service-fabric-visualizing-your-cluster/SfxClusterMap.png
[sfx-application-tree]: ./media/service-fabric-visualizing-your-cluster/SfxApplicationTree.png
[sfx-service-essentials]: ./media/service-fabric-visualizing-your-cluster/SfxServiceEssentials.png
[sfx-delete-application]: ./media/service-fabric-visualizing-your-cluster/SfxDeleteApplication.png
[sfx-create-app-instance]: ./media/service-fabric-visualizing-your-cluster/SfxCreateAppInstance.png
[sfx-eventstore]: ./media/service-fabric-diagnostics-eventstore/eventstore.png
