---
title: Tároló naplóinak megtekintése az Azure Service Fabricben |} A Microsoft Docs
description: Ismerteti a Service Fabric Explorerrel futó Service Fabric container Services tárolónaplók megtekintése.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/15/2018
ms.author: aljo
ms.openlocfilehash: 0408010a49b8ec83aa02c74887139f663788ad80
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58662822"
---
# <a name="view-logs-for-a-service-fabric-container-service"></a>A Service Fabric-tároló szolgáltatás naplók megtekintése
Az Azure Service Fabric egy tárolóvezénylő, és mindkettő támogatja [Linux és Windows-tárolók](service-fabric-containers-overview.md).  Ez a cikk azt ismerteti, hogy a futó tároló szolgáltatás vagy a kézbesíthetetlen tároló tárolónaplók megtekintése, így diagnosztizálhatja és elháríthatja.

## <a name="access-the-logs-of-a-running-container"></a>A futó tárolót, a naplók elérése
Tárolónaplók használatával lehet elérni [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).  Egy webböngészőben nyissa meg a Service Fabric Explorert a a fürt felügyeleti végpontja az [ http://mycluster.region.cloudapp.azure.com:19080/Explorer ](http://mycluster.region.cloudapp.azure.com:19080/Explorer).  

Tárolónaplók találhatók, amely a container service-példány fut fürtcsomóponton. Tegyük fel, a webes előtér-tároló, a naplók lekérése az [mintaalkalmazás Linux szavazási](service-fabric-quickstart-containers-linux.md). A fanézetben bontsa ki a **fürt**>**alkalmazások**>**VotingType**>**fabric: / Voting / azurevotefront**.  Ezután bontsa ki a partíció (d1aa737e-f22a-e347-be16-eec90be24bc1, ebben a példában), és tekintse meg, hogy a tároló fut-e a fürtcsomópont *_lnxvm_0*.

A faszerkezetes nézetben található a kódcsomag a *_lnxvm_0* csomópont kibontásával **csomópontok**>**_lnxvm_0**>**fabric: / Voting**  > **azurevotfrontPkg**>**Kódcsomagok**>**kód**.  Válassza ki a **Tárolónaplók** beállítást a tároló naplóinak megjelenítése.

![Service Fabric platform][Image1]

## <a name="access-the-logs-of-a-dead-or-crashed-container"></a>Halott vagy összeomlott tároló a naplók elérése
6.2-es kezdődően lehet is beolvasni a naplókat a kézbesíthetetlen vagy összeomlott tároló használatával [REST API-k](/rest/api/servicefabric/sfclient-index) vagy [Service Fabric parancssori felület (SFCTL)](service-fabric-cli.md) parancsokat.

### <a name="set-container-retention-policy"></a>Tárolómegőrzési szabályzat megadása
A tárolóindítási hibák diagnosztizálásának elősegítése céljából a Service Fabric (6.1-es vagy újabb verzió esetén) támogatja a megszakadt működésű vagy el sem induló tárolók megőrzését. Ez a szabályzat az **ApplicationManifest.xml** fájlban állítható be az alábbi kódrészletben látható módon:
```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
 ```

A **ContainersRetentionCount** beállítása megadja a hiba esetén megőrzendő tárolók számát. Ha negatív érték van megadva, a rendszer minden olyan tárolót megőriz, amelyen hiba jelentkezik. Ha a **ContainersRetentionCount** attribútum nincs megadva, nincs olyan tárolót megőriz. A **ContainersRetentionCount** attribútum az Alkalmazásparamétereket is támogatja, így a felhasználók különböző értékeket adhatnak meg a tesztelési és az éles fürtökön. A funkció használatakor alkalmazzon elhelyezési korlátozásokat, hogy a tárolószolgáltatás egy adott csomóponton maradjon, és a rendszer ne kerüljön át más csomópontokra. Az ezzel a funkcióval megőrzött tárolókat manuálisan kell eltávolítani.

A beállítás **RunInteractive** felel meg a Docker `--interactive` és `tty` [jelzők](https://docs.docker.com/engine/reference/commandline/run/#options). Ha ez a beállítás értéke igaz a jegyzékfájlban, ezek a jelölők szolgálnak a tárolót.  

### <a name="rest"></a>REST
Használja a [első tároló naplóinak üzembe helyezett a csomópont](/rest/api/servicefabric/sfclient-api-getcontainerlogsdeployedonnode) művelet a naplók lekérése az összeomlott a tárolóhoz. Adja meg a tárolót futtató csomópont nevét, alkalmazásnevet, szolgáltatás jegyzékfájl neve és a kód csomag nevét.  Adja meg `&Previous=true`. A válasz tartalmazni fogja a tároló naplóit a kézbesíthetetlen tároló kód csomag-példány.

A kérelem URI formátuma a következő:

```
/Nodes/{nodeName}/$/GetApplications/{applicationId}/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName={ServiceManifestName}&CodePackageName={CodePackageName}&Previous={Previous}
```

Kérelem (példa):
```
GET http://localhost:19080/Nodes/_Node_0/$/GetApplications/SimpleHttpServerApp/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName=SimpleHttpServerSvcPkg&CodePackageName=Code&Previous=true  
```

200 válasz törzsében:
```json
{   "Content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" } 
```

### <a name="service-fabric-sfctl"></a>Service Fabric (SFCTL)
Használja a [sfctl szolgáltatás get-tároló-naplók](service-fabric-sfctl-service.md) parancs beolvassa a naplókat összeomlott a tárolóhoz.  Adja meg a tárolót futtató csomópont nevét, alkalmazásnevet, szolgáltatás jegyzékfájl neve és a kód csomag nevét. Adja meg a `--previous` jelzőt.  A válasz tartalmazni fogja a tároló naplóit a kézbesíthetetlen tároló kód csomag-példány.

```
sfctl service get-container-logs --node-name _Node_0 --application-id SimpleHttpServerApp --service-manifest-name SimpleHttpServerSvcPkg --code-package-name Code –-previous
```
Válasz:
```json
{   "content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" }
```

## <a name="next-steps"></a>További lépések
- Haladjon végig a [hozzon létre egy Linux-tároló alkalmazás oktatóanyag](service-fabric-tutorial-create-container-images.md).
- Tudjon meg többet [Service Fabric és a tárolók](service-fabric-containers-overview.md)

[Image1]: media/service-fabric-containers-view-logs/view-container-logs-sfx.png
