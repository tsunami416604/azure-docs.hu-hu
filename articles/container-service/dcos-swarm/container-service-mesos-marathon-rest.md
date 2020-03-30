---
title: (ELAVULT) Az Azure DC/OS fürt kezelése a Marathon REST API-val
description: Telepítse a tárolókat egy Azure Container Service DC/OS-fürtre a Marathon REST API használatával.
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 04/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 3492f35d54dd3ee61ab8d29a3af06e4998bbd477
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277786"
---
# <a name="deprecated-dcos-container-management-through-the-marathon-rest-api"></a>(ELAVULT) DC/OS tárolókezelés a Marathon REST API-n keresztül

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

A DC/OS biztosítja a fürtözött feladatok telepítését és skálázását lehetővé tevő környezetet, ugyanakkor absztrakciós rétegként működik a hardver fölött. A DC/OS fölötti keretrendszer gondoskodik a számítási feladatok ütemezéséről és végrehajtásáról. Bár a keretrendszerek számos népszerű számítási feladathoz érhetők el, ez a dokumentum a Marathon REST API használatával megkezdheti a tárolók üzembe helyezésének megkezdését és méretezését. 

## <a name="prerequisites"></a>Előfeltételek

A példákban szereplő feladatok elvégzéséhez szüksége lesz egy az Azure tárolószolgáltatásban konfigurált DC/OS-fürtre, valamint távoli kapcsolatot kell tudnia létesíteni a fürttel. Ezekkel az elemekkel kapcsolatban a következő cikkekben talál további tájékoztatást:

* [Azure tárolószolgáltatás-fürt üzembe helyezése](container-service-deployment.md)
* [Csatlakozás Azure tárolószolgáltatás-fürthöz](../container-service-connect.md)

## <a name="access-the-dcos-apis"></a>Hozzáférés a DC/OS API-khoz
Miután csatlakozott az Azure Container Service-fürthöz, a DC/OS és a\/kapcsolódó REST API-k http: /localhost:local-port keresztül érhető el. Az ebben a dokumentumban szereplő példák azt feltételezik, hogy az alagutat a 80-as porton keresztül hozta létre. Például a maratoni végpontok a http:\//localhost/marathon/v2/ kezdetű URI-kon érhetőel el. 

A [Marathon API-ról](https://mesosphere.github.io/marathon/docs/rest-api.html) és a [Chronos API-ról](https://mesos.github.io/chronos/docs/api.html) a Mesosphere dokumentációjában, a [Mesos Scheduler API-ról](https://mesos.apache.org/documentation/latest/scheduler-http-api/) pedig az Apache dokumentációjában talál további információt.

## <a name="gather-information-from-dcos-and-marathon"></a>Információgyűjtés a DC/OS-ről és a Marathonról
Mielőtt telepítene tárolókat a DC/OS fürtre, gyűjtsön néhány információt a DC/OS fürtről, például a DC/OS ügynökök nevét és állapotát. Ehhez kérdezze le a DC/OS REST API fő- és alárendelt kiszolgálóinak (`master/slaves`) végpontját. Ha minden megfelelően működik, a lekérdezés a DC/OS-ügynökök listáját és az ügynökök különböző tulajdonságait adja vissza.

```bash
curl http://localhost/mesos/master/slaves
```

A DC/OS-fürtben üzembe helyezett alkalmazások lekérdezéséhez használja a Marathon `/apps` végpontot. Ha ez egy új fürt, akkor az alkalmazásoknál egy üres tömb jelenik meg.

```bash
curl localhost/marathon/v2/apps

{"apps":[]}
```

## <a name="deploy-a-docker-formatted-container"></a>Docker-formátumú tároló üzembe helyezése
A Docker által formázott tárolókat a Marathon REST API-n keresztül telepíti egy JSON-fájl használatával, amely leírja a tervezett üzembe helyezést. A következő minta egy Nginx-tárolót telepít a fürt egy magánügynökére. 

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

## <a name="reach-the-container"></a>Érje el a tartályt

Ellenőrizheti, hogy a Nginx a fürt egyik magánügynökén egy tárolóban fut-e. A tároló takarásában és portjának megkereséséhez kérdezze le marathoni a futó feladatokat: 

```bash
curl localhost/marathon/v2/tasks
```

Keresse meg `host` a kimenet értékét (a `10.32.0.x`hozzá hasonló IP-címet) és a `ports`értékét.


Most, hogy egy SSH terminál kapcsolat (nem bújt at) a felügyeleti fqdn a fürt. A csatlakozást követően tegye meg a következő `host` kérelmet, helyettesítve a helyes `ports`értékeket:

```bash
curl http://host:ports
```

A Nginx kiszolgáló kimenete hasonló a következőhöz:

![Nginx tartályból](./media/container-service-mesos-marathon-rest/nginx.png)




## <a name="scale-your-containers"></a>A tárolók skálázása
A Marathon API-val horizontális felskálázhatja vagy skálázhatja az alkalmazás-üzembe helyezéseket. Az előző példában üzembe helyezett egy alkalmazáspéldányt. Ezt most skálázhatja három alkalmazáspéldányra. Ehhez hozzon létre egy JSON-fájlt az alábbi JSON-szöveg használatával, és tárolja elérhető helyen.

```json
{ "instances": 3 }
```

A bújtatott kapcsolaton futtassa a következő parancsot az alkalmazás horizontális felskálázásához.

> [!NOTE]
> Az URI http:\//localhost/marathon/v2/apps/ amelyet az alkalmazás méretezési azonosítója követ. Ha az itt megadott Nginx-mintát használja, az URI\/a következő: /localhost/marathon/v2/apps/nginx.

```bash
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

Végül kérdezze le az alkalmazásokat a Marathon végponton. Láthatja majd, hogy most már három Nginx-tároló létezik.

```bash
curl localhost/marathon/v2/apps
```

## <a name="equivalent-powershell-commands"></a>Egyenértékű PowerShell-parancsok
Ugyanezeket a műveleteket elvégezheti Windows rendszerben is a PowerShell-parancsok használatával.

A DC/OS fürttel kapcsolatos információk, például az ügynöknevek és az ügynökállapot összegyűjtéséhez futtassa a következő parancsot:

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

Docker-formátumú tároló üzembe helyezéséhez tárolja a JSON-fájlt egy elérhető helyen. Ezt követően a tároló üzembe helyezéséhez futtassa az alábbi parancsot. Adja meg a JSON-fájl elérési útját (`marathon.json` ebben a példában).

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

A Marathon API-t az üzemelő alkalmazáspéldányok horizontális skálázására is használhatja. Az előző példában üzembe helyezett egy alkalmazáspéldányt. Ezt most skálázhatja három alkalmazáspéldányra. Ehhez hozzon létre egy JSON-fájlt az alábbi JSON-szöveg használatával, és tárolja elérhető helyen.

```json
{ "instances": 3 }
```

Az alkalmazás horizontális felskálázásához futtassa a következő parancsot:

> [!NOTE]
> Az URI http:\//localhost/marathon/v2/apps/ amelyet az alkalmazás méretezési azonosítója követ. Ha az itt megadott Nginx-mintát használja, az\/URI a következő: /localhost/marathon/v2/apps/nginx.

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

## <a name="next-steps"></a>További lépések
* [További információ a Mesos HTTP-végpontjairól](https://mesos.apache.org/documentation/latest/endpoints/)
* [További információ a Marathon REST API-ról](https://mesosphere.github.io/marathon/docs/rest-api.html)

