---
title: Tárolónaplók megtekintése az Azure Service Fabricben
description: Ez a témakör azt ismerteti, hogy miként tekintheti meg a Service Fabric-tárolószolgáltatások tárolónaplóit a Service Fabric-kezelő használatával.
ms.topic: conceptual
ms.date: 05/15/2018
ms.openlocfilehash: c47a408b272f95dbfcf3d791c644bfeb52254a72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458184"
---
# <a name="view-logs-for-a-service-fabric-container-service"></a>Service Fabric-tárolószolgáltatás naplóinak megtekintése
Az Azure Service Fabric egy tárolóvonó, és támogatja a [Linux és a Windows tárolók.](service-fabric-containers-overview.md)  Ez a cikk ismerteti, hogyan tekintheti meg a tárolónaplók egy futó tároló szolgáltatás vagy egy halott tároló, így diagnosztizálhatja és elháríthatja a problémákat.

## <a name="access-the-logs-of-a-running-container"></a>Egy futó tároló naplóinak elérése
A tárolónaplók a [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)használatával érhetők el.  Webböngészőben nyissa meg a Service Fabric Explorer t a `http://mycluster.region.cloudapp.azure.com:19080/Explorer`fürt felügyeleti végpontjáról a rendszerre navigálva.  

A tárolónaplók azon a fürtcsomóponton találhatók, amelyen a tárolószolgáltatás-példány fut. Például a [Linux voting mintaalkalmazás](service-fabric-quickstart-containers-linux.md)webes előtér-tárolójának naplóit. A fa nézetben bontsa ki a **Cluster**>**Applications**>**VotingType**>**fabric:/Voting/azurevotefront csomópontot.**  Ezután bontsa ki a partíciót (d1aa737e-f22a-e347-be16-eec90be24bc1, ebben a példában), és nézze meg, hogy a tároló fut a fürtcsomóponton *_lnxvm_0*.

A fa nézetben keresse meg a kódcsomagot a *_lnxvm_0* csomóponton a **csomópontok**>**_lnxvm_0**>**fabric:/Voting**>**azurevotfrontPkg**>**code csomagok kódjának**>**code**bővítésével.  Ezután válassza a **Tárolónaplók** lehetőséget a tárolónaplók megjelenítéséhez.

![Service Fabric platform][Image1]

## <a name="access-the-logs-of-a-dead-or-crashed-container"></a>Hozzáférés egy halott vagy lezuhant konténer naplóihoz
A 6.2-es vagy a 6.2-es paranccsal kezdődően egy halott vagy összeomlott tároló naplóit is lehívhatja [REST API-k](/rest/api/servicefabric/sfclient-index) vagy [Service Fabric CLI (SFCTL)](service-fabric-cli.md) parancsok használatával.

### <a name="set-container-retention-policy"></a>Tárolómegőrzési szabályzat megadása
A tárolóindítási hibák diagnosztizálásának elősegítése céljából a Service Fabric (6.1-es vagy újabb verzió esetén) támogatja a megszakadt működésű vagy el sem induló tárolók megőrzését. Ez a szabályzat az **ApplicationManifest.xml** fájlban állítható be az alábbi kódrészletben látható módon:
```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
 ```

A **ContainersRetentionCount** beállítása megadja a hiba esetén megőrzendő tárolók számát. Ha negatív érték van megadva, a rendszer minden olyan tárolót megőriz, amelyen hiba jelentkezik. Ha a **ContainersRetentionCount** attribútum nincs megadva, nem tárolók maradnak meg. A **ContainersRetentionCount** attribútum az Alkalmazásparamétereket is támogatja, így a felhasználók különböző értékeket adhatnak meg a tesztelési és az éles fürtökön. A funkció használatakor alkalmazzon elhelyezési korlátozásokat, hogy a tárolószolgáltatás egy adott csomóponton maradjon, és a rendszer ne kerüljön át más csomópontokra. Az ezzel a funkcióval megőrzött tárolókat manuálisan kell eltávolítani.

A **RunInteractive** beállítás megfelel a `--interactive` Docker-nek és `tty` [a jelzőknek.](https://docs.docker.com/engine/reference/commandline/run/#options) Ha ez a beállítás igaz értékre van állítva a jegyzékfájlban, ezek a jelzők a tároló indításához használatosak.  

### <a name="rest"></a>REST
A [csomóponton üzembe helyezett tárolónaplók beszerezhető tárolónaplók](/rest/api/servicefabric/sfclient-api-getcontainerlogsdeployedonnode) lekérni egy összeomlott tároló naplók. Adja meg annak a csomópontnak a nevét, amelyen a tároló futott, az alkalmazás nevét, a szolgáltatásjegyzék nevét és a kódcsomag nevét.  Adja meg a következőt: `&Previous=true`. A válasz tartalmazza a kódcsomag példány halott tárolójának tárolónaplóit.

A kérelem URI-ja a következő űrlappal rendelkezik:

```
/Nodes/{nodeName}/$/GetApplications/{applicationId}/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName={ServiceManifestName}&CodePackageName={CodePackageName}&Previous={Previous}
```

Példa kérelem:
```
GET http://localhost:19080/Nodes/_Node_0/$/GetApplications/SimpleHttpServerApp/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName=SimpleHttpServerSvcPkg&CodePackageName=Code&Previous=true  
```

200 Válasz testület:
```json
{   "Content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" } 
```

### <a name="service-fabric-sfctl"></a>Szolgáltatás háló (SFCTL)
Az [sfctl szolgáltatás get-container-logs](service-fabric-sfctl-service.md) parancs használatával lekéri a naplókat egy összeomlott tároló.  Adja meg annak a csomópontnak a nevét, amelyen a tároló futott, az alkalmazás nevét, a szolgáltatásjegyzék nevét és a kódcsomag nevét. Adja `--previous` meg a jelzőt.  A válasz tartalmazza a kódcsomag példány halott tárolójának tárolónaplóit.

```
sfctl service get-container-logs --node-name _Node_0 --application-id SimpleHttpServerApp --service-manifest-name SimpleHttpServerSvcPkg --code-package-name Code –-previous
```
Válasz:
```json
{   "content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" }
```

## <a name="next-steps"></a>További lépések
- A [Linux-tárolóalkalmazás létrehozása oktatóanyag](service-fabric-tutorial-create-container-images.md)a munka során.
- További információ a [Service Fabricről és a tárolókról](service-fabric-containers-overview.md)

[Image1]: media/service-fabric-containers-view-logs/view-container-logs-sfx.png
