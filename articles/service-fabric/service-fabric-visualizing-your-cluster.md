---
title: "A Service Fabric Explorerrel fürt megjelenítése |} Microsoft Docs"
description: "Service Fabric Explorerben talál egy olyan webalapú eszköz ellenőrzi, és a felhőalapú alkalmazások és a Microsoft Azure Service Fabric-fürt csomópontjának kezelése."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: c875b993-b4eb-494b-94b5-e02f5eddbd6a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2017
ms.author: ryanwi
ms.openlocfilehash: 965ffc0f8cec26cccbe6e6459731afc234111f4d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="visualize-your-cluster-with-service-fabric-explorer"></a>A fürt megjelenítése a Service Fabric Explorerrel
Service Fabric Explorerben talál egy olyan webalapú eszköz ellenőrzi, és az alkalmazások és az Azure Service Fabric-fürt csomópontjának kezelése. Service Fabric Explorer gazdája közvetlenül a fürthöz, így az mindig elérhető, függetlenül attól, amelyen fut a fürtön.

## <a name="video-tutorial"></a>Oktatóvideó

Megtudhatja, hogyan használhatja a Service Fabric Explorer, a következő Microsoft Virtual Academy videót:

[<center><img src="./media/service-fabric-visualizing-your-cluster/SfxVideo.png" WIDTH="360" HEIGHT="244"></center>](https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=bBTFg46yC_9806218965)

## <a name="connect-to-service-fabric-explorer"></a>Kapcsolódás a Service Fabric Explorerrel
Ha már elvégezte az utasításokat [a fejlesztőkörnyezet előkészítése](service-fabric-get-started.md), elindíthatja a Service Fabric Explorer a helyi fürtön lévő 19080/Explorer útvonalon.

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
>

A következő táblázat felsorolja az egyes entitások esetében elérhető műveleteket:

| **Entitás** | **Művelet** | **Leírás** |
| --- | --- | --- |
| Alkalmazás típusa |Típus leépítése |Eltávolítja az alkalmazáscsomagot a fürt lemezképtárolóhoz. Először el kell távolítani az adott típusú összes alkalmazásra van szükség. |
| Alkalmazás |Alkalmazás törlése |Törli az alkalmazásból, beleértve a hozzá tartozó szolgáltatások és azok állapota (ha van ilyen). |
| Szolgáltatás |Szolgáltatás törlése |(Ha van ilyen), törölje a szolgáltatást és annak állapotát. |
| Csomópont |Aktiválás |A csomópont aktiválása. |
| Csomópont | (Szünet) inaktiválása | A csomópont jelenlegi állapotában felfüggesztése. Szolgáltatások tovább fut, de a Service Fabric proaktív helyezi át a semmit, vagy ki, kivéve, ha arra szükség van kimaradás vagy adatok inkonzisztenciája megelőzése érdekében. Ez a művelet általában segítségével engedélyezheti a hibakeresési szolgáltatásokat annak érdekében, hogy azok helyezi át az ellenőrzés során adott csomóponton. | |
| Csomópont | Inaktiválás (újraindítás) | Biztonságosan haladhat minden memórián belüli szolgáltatás ki olyan csomópont, és zárja be az állandó szolgáltatásokban. Általában használhatók, ha a gazdagép folyamatainak vagy a gép újra kell indítani. | |
| Csomópont | (Az adatok eltávolítása) inaktiválása | Biztonságosan zárja be a megfelelő tartalék replikák létrehozása után a csomóponton futó összes szolgáltatás. Jellemzően akkor csomópont (vagy legalább a tárhely) kívül Bizottság véglegesen foglalja. | |
| Csomópont | Távolítsa el a csomópont állapota | Távolítsa el a fürt egy csomópont replikák ismerete. Általában akkor használható, ha egy már leállt csomópont helyreállíthatatlan tekintendő. | |
| Csomópont | Újraindítás | Csomópont hiba szimulálása a csomópont újraindításával. További információ [Itt](/powershell/module/servicefabric/restart-servicefabricnode?view=azureservicefabricps) | |

Mivel számos műveletet károsak, a lehetséges, hogy a művelet befejezése előtt, győződjön meg arról, hogy a leképezés kéri.

> [!TIP]
> Minden művelet, amely a Service Fabric Explorer segítségével végezheti el is PowerShell vagy a REST API-t automatizálására végezhető el.
>
>

Service Fabric Explorer használatával hozzon létre egy adott alkalmazás típusa vagy a termékverzió alkalmazáspéldányok. Az alkalmazástípus válassza a faszerkezetes nézetben, majd kattintson a **app-példány létrehozása** mellett a verzióra szeretné, hogy a jobb oldali ablaktáblán látható hivatkozásra.

![Az alkalmazáspéldány létrehozása a Service Fabric Explorerben][sfx-create-app-instance]

> [!NOTE]
> Jelenleg nem paraméterezett Service Fabric Explorer használatával létrehozott alkalmazás-példányokon. Létrehozásuk alapértelmezett paraméterértékek használata.
>
>

## <a name="connect-to-a-remote-service-fabric-cluster"></a>Csatlakozzon a távoli Service Fabric-fürt
Ha a fürt végpontja tudja, és rendelkezik a szükséges engedélyekkel a Service Fabric Explorer minden böngészőből végezheti el. Ez azért, mert Service Fabric Explorer egy másik szolgáltatás, amely a fürt futtatja.

### <a name="discover-the-service-fabric-explorer-endpoint-for-a-remote-cluster"></a>A távoli fürt Service Fabric Explorer-végpont felderítése
Service Fabric Explorer egy adott fürt eléréséhez a böngészőben a pont:

http://&lt;a fürt végpontja&gt;: 19080/Explorer

Az Azure-fürtök esetén a teljes URL-címet is rendelkezésre áll a fürt essentials ablaktábláján az Azure-portálon.

### <a name="connect-to-a-secure-cluster"></a>Csatlakozás biztonságos fürthöz
A Service Fabric fürt tanúsítványok, valamint az Azure Active Directory (AAD) használó ügyfél-hozzáférési szabályozhatja.

Ha próbál csatlakozni a Service Fabric Explorer egy biztonságos fürtön, majd függően a fürtkonfiguráció akkor lesz kell ügyféltanúsítványt jelen, vagy jelentkezzen be az AAD használja.

## <a name="next-steps"></a>Következő lépések
* [Tesztelhetőségi áttekintése](service-fabric-testability-overview.md)
* [A Visual Studio a Service Fabric-alkalmazások kezelése](service-fabric-manage-application-in-visual-studio.md)
* [A Service Fabric-alkalmazás központi telepítésének PowerShell használatával](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[sfx-cluster-dashboard]: ./media/service-fabric-visualizing-your-cluster/SfxClusterDashboard.png
[sfx-cluster-map]: ./media/service-fabric-visualizing-your-cluster/SfxClusterMap.png
[sfx-application-tree]: ./media/service-fabric-visualizing-your-cluster/SfxApplicationTree.png
[sfx-service-essentials]: ./media/service-fabric-visualizing-your-cluster/SfxServiceEssentials.png
[sfx-delete-application]: ./media/service-fabric-visualizing-your-cluster/SfxDeleteApplication.png
[sfx-create-app-instance]: ./media/service-fabric-visualizing-your-cluster/SfxCreateAppInstance.png
