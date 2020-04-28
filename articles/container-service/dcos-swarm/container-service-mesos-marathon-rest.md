---
title: ELAVULT Azure DC/OS-fürt kezelése Marathon REST API
description: Tárolók üzembe helyezése Azure Container Service DC/OS-fürtön a Marathon REST API használatával.
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 04/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 3492f35d54dd3ee61ab8d29a3af06e4998bbd477
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "76277786"
---
# <a name="deprecated-dcos-container-management-through-the-marathon-rest-api"></a>ELAVULT DC/OS-tárolók kezelése a Marathon REST API

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

A DC/OS biztosítja a fürtözött feladatok telepítését és skálázását lehetővé tevő környezetet, ugyanakkor absztrakciós rétegként működik a hardver fölött. A DC/OS fölötti keretrendszer gondoskodik a számítási feladatok ütemezéséről és végrehajtásáról. Bár a keretrendszerek számos népszerű munkaterheléshez elérhetők, ez a dokumentum a Marathon REST API használatával megkezdi a tárolók üzembe helyezésének létrehozását és méretezését. 

## <a name="prerequisites"></a>Előfeltételek

A példákban szereplő feladatok elvégzéséhez szüksége lesz egy az Azure tárolószolgáltatásban konfigurált DC/OS-fürtre, valamint távoli kapcsolatot kell tudnia létesíteni a fürttel. Ezekkel az elemekkel kapcsolatban a következő cikkekben talál további tájékoztatást:

* [Azure tárolószolgáltatás-fürt üzembe helyezése](container-service-deployment.md)
* [Csatlakozás Azure tárolószolgáltatás-fürthöz](../container-service-connect.md)

## <a name="access-the-dcos-apis"></a>A DC/OS API-k elérése
Miután csatlakozott a Azure Container Service-fürthöz, a DC/OS-t és a kapcsolódó REST API-kat a http\/:/localhost: Local-Port használatával érheti el. Az ebben a dokumentumban szereplő példák azt feltételezik, hogy az alagutat a 80-as porton keresztül hozta létre. A Marathon-végpontok például a http:/localhost/Marathon/v2/. értékkel kezdődő URI-k\/esetében érhetők el. 

A [Marathon API-ról](https://mesosphere.github.io/marathon/docs/rest-api.html) és a [Chronos API-ról](https://mesos.github.io/chronos/docs/api.html) a Mesosphere dokumentációjában, a [Mesos Scheduler API-ról](https://mesos.apache.org/documentation/latest/scheduler-http-api/) pedig az Apache dokumentációjában talál további információt.

## <a name="gather-information-from-dcos-and-marathon"></a>Információgyűjtés a DC/OS-ről és a Marathonról
Mielőtt üzembe helyezi a tárolókat a DC/OS-fürtön, gyűjtsön össze néhány információt a DC/OS-fürtről, például a DC/os-ügynökök nevét és állapotát. Ehhez kérdezze le a DC/OS REST API fő- és alárendelt kiszolgálóinak (`master/slaves`) végpontját. Ha minden megfelelően működik, a lekérdezés a DC/OS-ügynökök listáját és az ügynökök különböző tulajdonságait adja vissza.

```bash
curl http://localhost/mesos/master/slaves
```

A DC/OS-fürtben üzembe helyezett alkalmazások lekérdezéséhez használja a Marathon `/apps` végpontot. Ha ez egy új fürt, akkor az alkalmazásoknál egy üres tömb jelenik meg.

```bash
curl localhost/marathon/v2/apps

{"apps":[]}
```

## <a name="deploy-a-docker-formatted-container"></a>Docker-formátumú tároló üzembe helyezése
Docker-formázott tárolók üzembe helyezése a Marathon REST API egy olyan JSON-fájl használatával, amely leírja a kívánt telepítést. A következő minta egy Nginx-tárolót helyez üzembe egy privát ügynökhöz a fürtben. 

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 32.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Docker-formátumú tároló üzembe helyezéséhez tárolja a JSON-fájlt egy elérhető helyen. Ezt követően a tároló üzembe helyezéséhez futtassa az alábbi parancsot. Adja meg a JSON-fájl nevét (`marathon.json` ebben a példában).

```bash
curl -X POST http://localhost/marathon/v2/apps -d @marathon.json -H "Content-type: application/json"
```

A kimenet a következő példához hasonló:

```json
{"version":"2015-11-20T18:59:00.494Z","deploymentId":"b12f8a73-f56a-4eb1-9375-4ac026d6cdec"}
```

Ha ezt követően lekérdezi az alkalmazásokat a Marathonban, az eredmények között megjelenik az új alkalmazás is.

```bash
curl localhost/marathon/v2/apps
```

## <a name="reach-the-container"></a>A tároló elérése

Ellenőrizheti, hogy az Nginx fut-e egy tárolóban a fürt egyik privát ügynökén. Annak a gazdagépnek és portnak a megkereséséhez, ahol a tároló fut, lekérdezési maraton a futó feladatokhoz: 

```bash
curl localhost/marathon/v2/tasks
```

`host` A kimenetben (a `10.32.0.x`következőhöz hasonló IP-cím) és a értékének megkeresése `ports`.


Most hozzon ki egy SSH-terminál-kapcsolódást (nem bújtatott kapcsolatban) a fürt felügyeleti teljes tartománynevére. A csatlakozás után végezze el `host` a következő kérelmet, és `ports`helyettesítse be a megfelelő értékeit:

```bash
curl http://host:ports
```

Az Nginx-kiszolgáló kimenete a következőhöz hasonló:

![Nginx a tárolóból](./media/container-service-mesos-marathon-rest/nginx.png)




## <a name="scale-your-containers"></a>A tárolók skálázása
A Marathon API használatával kibővítheti vagy méretezheti az alkalmazások központi telepítéseit. Az előző példában üzembe helyezett egy alkalmazáspéldányt. Ezt most skálázhatja három alkalmazáspéldányra. Ehhez hozzon létre egy JSON-fájlt az alábbi JSON-szöveg használatával, és tárolja elérhető helyen.

```json
{ "instances": 3 }
```

A bújtatási kapcsolatban futtassa az alábbi parancsot az alkalmazás skálázásához.

> [!NOTE]
> Az URI a http:\//localhost/Marathon/v2/apps/, amelyet a skálázási alkalmazás azonosítója követ. Ha az itt megadott Nginx-mintát használja, az URI http:\//localhost/Marathon/v2/apps/Nginx.

```bash
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

Végül kérdezze le az alkalmazásokat a Marathon végponton. Láthatja majd, hogy most már három Nginx-tároló létezik.

```bash
curl localhost/marathon/v2/apps
```

## <a name="equivalent-powershell-commands"></a>Egyenértékű PowerShell-parancsok
Ugyanezeket a műveleteket elvégezheti Windows rendszerben is a PowerShell-parancsok használatával.

A DC/OS-fürtről, például az ügynökök nevére és az ügynök állapotára vonatkozó információk összegyűjtéséhez futtassa a következő parancsot:

```powershell
Invoke-WebRequest -Uri http://localhost/mesos/master/slaves
```

A Docker-formátumú tárolók Marathon segítségével való üzembe helyezéséhez egy olyan JSON-fájlt kell használnia, amelyben megadhatja a kívánt üzembe helyezéssel kapcsolatos információkat. Az alábbi példában, amely egy Nginx-tároló üzembe helyezését szemlélteti, a DC/OS-ügynök 80-as portja a tároló 80-as portjával van összekötve.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 32.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Docker-formátumú tároló üzembe helyezéséhez tárolja a JSON-fájlt egy elérhető helyen. Ezt követően a tároló üzembe helyezéséhez futtassa az alábbi parancsot. Itt adhatja meg a JSON-fájl elérési útját (`marathon.json` ebben a példában).

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

A Marathon API-t az üzemelő alkalmazáspéldányok horizontális skálázására is használhatja. Az előző példában üzembe helyezett egy alkalmazáspéldányt. Ezt most skálázhatja három alkalmazáspéldányra. Ehhez hozzon létre egy JSON-fájlt az alábbi JSON-szöveg használatával, és tárolja elérhető helyen.

```json
{ "instances": 3 }
```

A következő parancs futtatásával bővítheti az alkalmazást:

> [!NOTE]
> Az URI a http:\//localhost/Marathon/v2/apps/, amelyet a skálázási alkalmazás azonosítója követ. Ha az itt megadott Nginx-mintát használja, az URI http:\//localhost/Marathon/v2/apps/Nginx.

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

## <a name="next-steps"></a>További lépések
* [További információ a Mezos HTTP-végpontokról](https://mesos.apache.org/documentation/latest/endpoints/)
* [További információ a Marathon REST APIról](https://mesosphere.github.io/marathon/docs/rest-api.html)

