---
title: Tárolók naplók megtekintése az Azure Service Fabric |} Microsoft Docs
description: Ismerteti a Service Fabric Explorerrel futó Service Fabric tároló Services tároló naplók megtekintéséhez.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/15/2018
ms.author: ryanwi
ms.openlocfilehash: c8b6bc791700e6811f5681ee70329e4d2ac05991
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2018
ms.locfileid: "34824611"
---
# <a name="view-logs-for-a-service-fabric-container-service"></a>A Service Fabric-tároló szolgáltatás naplók megtekintése
Az Azure Service Fabric egy tároló orchestrator és egyaránt támogat [Linux-és Windows](service-fabric-containers-overview.md).  Ez a cikk ismerteti, hogyan futó tárolószolgáltatás vagy egy inaktív tároló tároló naplók megtekintéséhez, hogy diagnosztizálása és elhárítása.

## <a name="access-the-logs-of-a-running-container"></a>A naplók a futó tároló hozzáférés
Tároló naplók segítségével férhetők el [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).  Egy webböngészőben nyissa meg a Service Fabric Explorerben talál a fürt felügyeleti végpont a navigáljon [ http://mycluster.region.cloudapp.azure.com:19080/Explorer ](http://mycluster.region.cloudapp.azure.com:19080/Explorer).  

Tároló naplók az, hogy a tároló szolgáltatáspéldány fut csomóponton találhatók. Tegyük fel, a naplókat web előtér-tárolójának beolvasása a [mintaalkalmazás Linux szavazás](service-fabric-quickstart-containers-linux.md). Bontsa ki a fa nézetben **fürt**>**alkalmazások**>**VotingType**>**fabric: / Voting / azurevotefront**.  Ezután bontsa ki a partíció (d1aa737e-f22a-e347-be16-eec90be24bc1, ebben a példában), és a fürt csomópontján fut-e a tároló *_lnxvm_0*.

A fa nézetben található a kódcsomag a *_lnxvm_0* csomópont kibontásával **csomópontok**>**_lnxvm_0**>**fabric: / szavazás**  > **azurevotfrontPkg**>**kód csomagok**>**kód**.  Válassza ki a **tároló naplók** lehetőséget a tároló naplók megjelenítéséhez.

![Service Fabric platform][Image1]

## <a name="access-the-logs-of-a-dead-or-crashed-container"></a>A naplók a inaktív vagy lefagyott tároló hozzáférés
6.2-es kezdődően akkor is is beolvasása a naplók egy inaktív vagy lefagyott tároló használatára vonatkozó [REST API-k](/rest/api/servicefabric/sfclient-index) vagy [Service Fabric CLI (SFCTL)](service-fabric-cli.md) parancsok.

### <a name="set-container-retention-policy"></a>Tárolómegőrzési szabályzat megadása
A tárolóindítási hibák diagnosztizálásának elősegítése céljából a Service Fabric (6.1-es vagy újabb verzió esetén) támogatja a megszakadt működésű vagy el sem induló tárolók megőrzését. Ez a szabályzat az **ApplicationManifest.xml** fájlban állítható be az alábbi kódrészletben látható módon:
```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
 ```

A **ContainersRetentionCount** beállítása megadja a hiba esetén megőrzendő tárolók számát. Ha negatív érték van megadva, a rendszer minden olyan tárolót megőriz, amelyen hiba jelentkezik. Ha a **ContainersRetentionCount** attribútum nincs megadva, nincs tárolók megmaradnak. A **ContainersRetentionCount** attribútum az Alkalmazásparamétereket is támogatja, így a felhasználók különböző értékeket adhatnak meg a tesztelési és az éles fürtökön. A funkció használatakor alkalmazzon elhelyezési korlátozásokat, hogy a tárolószolgáltatás egy adott csomóponton maradjon, és a rendszer ne kerüljön át más csomópontokra. Az ezzel a funkcióval megőrzött tárolókat manuálisan kell eltávolítani.

### <a name="rest"></a>REST
Használja a [beolvasása tároló telepítve a szolgáltatók](/rest/api/servicefabric/sfclient-api-getcontainerlogsdeployedonnode) lekérdezése a naplók lefagyott a tárolóhoz. Adja meg, hogy a tároló kiszolgálón már futott a csomópont neve, alkalmazásnevet, service manifest nevét és a csomag neve.  Adja meg `&Previous=true`. A válasz tartalmazza a tároló keresse meg a halott tároló kód csomag példány.

A kérelem URI formátuma a következő:

```
/Nodes/{nodeName}/$/GetApplications/{applicationId}/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName={ServiceManifestName}&CodePackageName={CodePackageName}&Previous={Previous}
```

Példa egy kérelem:
```
GET http://localhost:19080/Nodes/_Node_0/$/GetApplications/SimpleHttpServerApp/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName=SimpleHttpServerSvcPkg&CodePackageName=Code&Previous=true  
```

200 adott válasz törzse:
```json
{   "Content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" } 
```

### <a name="service-fabric-sfctl"></a>A Service Fabric (SFCTL)
Használja a [sfctl szolgáltatás get-tároló-naplók](service-fabric-sfctl-service.md) parancs lefagyott tároló naplók beolvasása.  Adja meg, hogy a tároló kiszolgálón már futott a csomópont neve, alkalmazásnevet, service manifest nevét és a csomag neve. Adja meg a `-previous` jelzőt.  A válasz tartalmazza a tároló keresse meg a halott tároló kód csomag példány.

```
sfctl service get-container-logs --node-name _Node_0 --application-id SimpleHttpServerApp --service-manifest-name SimpleHttpServerSvcPkg --code-package-name Code –previous
```
Válasz:
```json
{   "content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" }
```

## <a name="next-steps"></a>További lépések
- Munka – a [hozzon létre egy Linux tároló oktatóanyag](service-fabric-tutorial-create-container-images.md).
- További információ [Service Fabric és a tárolók](service-fabric-containers-overview.md)

[Image1]: media/service-fabric-containers-view-logs/view-container-logs-sfx.png
