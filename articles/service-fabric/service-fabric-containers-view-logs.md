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
ms.openlocfilehash: b2b3562f65e7e861b7e4dff7b7c26d58081ff29e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
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
