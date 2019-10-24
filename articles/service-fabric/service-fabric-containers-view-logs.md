---
title: Tárolók naplóinak megtekintése az Azure Service Fabricban | Microsoft Docs
description: Ismerteti, hogyan lehet megtekinteni egy futó Service Fabric Container Services tároló-naplóit Service Fabric Explorer használatával.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/15/2018
ms.author: atsenthi
ms.openlocfilehash: 3fa40d794d02da08d29b6cac652edf493977f8e1
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599733"
---
# <a name="view-logs-for-a-service-fabric-container-service"></a>Service Fabric tároló szolgáltatás naplófájljainak megtekintése
Az Azure Service Fabric egy tároló-Orchestrator, és támogatja a [Linux-és Windows-tárolókat](service-fabric-containers-overview.md)is.  Ez a cikk azt ismerteti, hogyan lehet megtekinteni egy futó tároló szolgáltatás vagy egy elhalt tároló tároló naplóit, így diagnosztizálhatja és elháríthatja a problémákat.

## <a name="access-the-logs-of-a-running-container"></a>Egy futó tároló naplóihoz való hozzáférés
A tároló naplói [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)használatával érhetők el.  Egy webböngészőben nyissa meg Service Fabric Explorer a fürt felügyeleti végpontján, ehhez [http://mycluster.region.cloudapp.azure.com:19080/Explorer](http://mycluster.region.cloudapp.azure.com:19080/Explorer)navigáljon a következőre:.  

A tároló-naplók azon a fürtcsomóponton találhatók, amelyen a Container Service-példány fut. Tegyük fel például, hogy beolvassa a [linuxos szavazási minta alkalmazás](service-fabric-quickstart-containers-linux.md)webes kezelőfelületének naplóit. A fanézetben bontsa ki a **cluster**>**Applications**>**VotingType**>**Fabric:/szavazás/azurevotefront**elemet.  Ezután bontsa ki a partíciót (ebben a példában a d1aa737e-f22a-e347-be16-eec90be24bc1), és ellenőrizze, hogy a tároló fut-e a fürtcsomópont *_lnxvm_0*.

A fanézetben keresse meg a *_lnxvm_0* csomóponton a **_lnxvm_0**>**Fabric:/szavazó**>**azurevotfrontPkg**>kód **csomópontok**>kibontásával.Csomagok>**kódja**.  Ezután válassza a **tárolói naplók** lehetőséget a tároló naplófájljainak megjelenítéséhez.

![Service Fabric platform][Image1]

## <a name="access-the-logs-of-a-dead-or-crashed-container"></a>Egy halott vagy összeomlott tároló naplófájljainak elérése
A v 6.2-es verziótól kezdődően a [REST API](/rest/api/servicefabric/sfclient-index) -k vagy a [Service FABRIC CLI (SFCTL)](service-fabric-cli.md) parancsok használatával is beolvashatja a halott vagy összeomlott tároló naplóit.

### <a name="set-container-retention-policy"></a>Tárolómegőrzési szabályzat megadása
A tárolóindítási hibák diagnosztizálásának elősegítése céljából a Service Fabric (6.1-es vagy újabb verzió esetén) támogatja a megszakadt működésű vagy el sem induló tárolók megőrzését. Ez a szabályzat az **ApplicationManifest.xml** fájlban állítható be az alábbi kódrészletben látható módon:
```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
 ```

A **ContainersRetentionCount** beállítása megadja a hiba esetén megőrzendő tárolók számát. Ha negatív érték van megadva, a rendszer minden olyan tárolót megőriz, amelyen hiba jelentkezik. Ha a **ContainersRetentionCount** attribútum nincs megadva, a rendszer nem őrzi meg a tárolókat. A **ContainersRetentionCount** attribútum az Alkalmazásparamétereket is támogatja, így a felhasználók különböző értékeket adhatnak meg a tesztelési és az éles fürtökön. A funkció használatakor alkalmazzon elhelyezési korlátozásokat, hogy a tárolószolgáltatás egy adott csomóponton maradjon, és a rendszer ne kerüljön át más csomópontokra. Az ezzel a funkcióval megőrzött tárolókat manuálisan kell eltávolítani.

A **RunInteractive** beállítás a Docker és `--interactive` `tty` a [Flags](https://docs.docker.com/engine/reference/commandline/run/#options)értéknek felel meg. Ha a jegyzékfájl értéke TRUE (igaz), akkor ezek a jelzők a tároló elindítására szolgálnak.  

### <a name="rest"></a>REST
A csomópont-művelethez tartozó [tároló-naplók](/rest/api/servicefabric/sfclient-api-getcontainerlogsdeployedonnode) beolvasása paranccsal lekérheti egy összeomlott tároló naplóit. Adja meg annak a csomópontnak a nevét, amelyen a tároló fut, az alkalmazás neve, a szolgáltatás jegyzékfájljának neve és a kód csomag neve.  Meg `&Previous=true`kell adni. A válasz tartalmazni fogja a kód csomag példányának Holt tárolójának tároló naplóit.

A kérelem URI-ja a következő formátumú:

```
/Nodes/{nodeName}/$/GetApplications/{applicationId}/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName={ServiceManifestName}&CodePackageName={CodePackageName}&Previous={Previous}
```

Példa kérésre:
```
GET http://localhost:19080/Nodes/_Node_0/$/GetApplications/SimpleHttpServerApp/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName=SimpleHttpServerSvcPkg&CodePackageName=Code&Previous=true  
```

200 válasz törzse:
```json
{   "Content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" } 
```

### <a name="service-fabric-sfctl"></a>Service Fabric (SFCTL)
Az összeomlott tároló naplófájljainak beolvasásához használja a [Get-Container-logs](service-fabric-sfctl-service.md) parancsot a sfctl szolgáltatásban.  Adja meg annak a csomópontnak a nevét, amelyen a tároló fut, az alkalmazás neve, a szolgáltatás jegyzékfájljának neve és a kód csomag neve. Határozza meg `--previous` a jelzőt.  A válasz tartalmazni fogja a kód csomag példányának Holt tárolójának tároló naplóit.

```
sfctl service get-container-logs --node-name _Node_0 --application-id SimpleHttpServerApp --service-manifest-name SimpleHttpServerSvcPkg --code-package-name Code –-previous
```
Válasz:
```json
{   "content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" }
```

## <a name="next-steps"></a>További lépések
- A [Linux Container Application alkalmazás létrehozásával](service-fabric-tutorial-create-container-images.md)foglalkozó oktatóanyagban dolgozhat.
- További információ a [Service Fabric és](service-fabric-containers-overview.md) a tárolók használatáról

[Image1]: media/service-fabric-containers-view-logs/view-container-logs-sfx.png
