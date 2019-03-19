---
title: (ELAVULT) A Marathon REST API-val Azure DC/OS fürt kezelése
description: Tárolók üzembe helyezése egy Azure Container Service DC/OS-fürtön a Marathon REST API-val.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 567890f3beec1eff30effeec0ce23284c5fee141
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58109290"
---
# <a name="deprecated-dcos-container-management-through-the-marathon-rest-api"></a>(ELAVULT) DC/OS-tárolók kezelése a Marathon REST API

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

A DC/OS biztosítja a fürtözött feladatok telepítését és skálázását lehetővé tevő környezetet, ugyanakkor absztrakciós rétegként működik a hardver fölött. A DC/OS fölötti keretrendszer gondoskodik a számítási feladatok ütemezéséről és végrehajtásáról. Számos népszerű számítási feladathoz érhetők el a keretrendszerek, ez a dokumentum megkönnyíti első lépéseit létrehozásán és skálázásán üzemelő tárolópéldányokat a Marathon REST API-val. 

## <a name="prerequisites"></a>Előfeltételek

A példákban szereplő feladatok elvégzéséhez szüksége lesz egy az Azure tárolószolgáltatásban konfigurált DC/OS-fürtre, valamint távoli kapcsolatot kell tudnia létesíteni a fürttel. Ezekkel az elemekkel kapcsolatban a következő cikkekben talál további tájékoztatást:

* [Azure Container Service-fürt üzembe helyezése](container-service-deployment.md)
* [Csatlakozás Azure Container Service-fürthöz](../container-service-connect.md)

## <a name="access-the-dcos-apis"></a>A DC/OS API-k elérése
Miután csatlakozott az Azure Container Service-fürthöz, és hozzáférhet, a DC/OS keresztül kapcsolódó REST API-k http://localhost:local-port. Az ebben a dokumentumban szereplő példák azt feltételezik, hogy az alagutat a 80-as porton keresztül hozta létre. Ha például a Marathon végpontok címen érhető el URI-k kezdve `http://localhost/marathon/v2/`. 

A [Marathon API-ról](https://mesosphere.github.io/marathon/docs/rest-api.html) és a [Chronos API-ról](https://mesos.github.io/chronos/docs/api.html) a Mesosphere dokumentációjában, a [Mesos Scheduler API-ról](http://mesos.apache.org/documentation/latest/scheduler-http-api/) pedig az Apache dokumentációjában talál további információt.

## <a name="gather-information-from-dcos-and-marathon"></a>Információgyűjtés a DC/OS-ről és a Marathonról
Tárolók a DC/OS fürt üzembe helyezése előtt gyűjtsön össze néhány információt a DC/OS fürt, például a neveket és a DC/OS-ügynökök állapotát. Ehhez kérdezze le a DC/OS REST API fő- és alárendelt kiszolgálóinak (`master/slaves`) végpontját. Ha minden megfelelően működik, a lekérdezés a DC/OS-ügynökök listáját és az ügynökök különböző tulajdonságait adja vissza.

```bash
curl http://localhost/mesos/master/slaves
```

A DC/OS-fürtben üzembe helyezett alkalmazások lekérdezéséhez használja a Marathon `/apps` végpontot. Ha ez egy új fürt, akkor az alkalmazásoknál egy üres tömb jelenik meg.

```bash
curl localhost/marathon/v2/apps

{"apps":[]}
```

## <a name="deploy-a-docker-formatted-container"></a>Docker-formátumú tároló üzembe helyezése
Docker-formátumú tárolók Marathon REST API-val egy JSON-fájlt, amely leírja a kívánt központi telepítés segítségével telepítheti. Az alábbi minta egy privát ügynök a fürt telepíti az Nginx-tároló. 

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

Docker-formátumú tároló üzembe helyezéséhez a JSON-fájlt tárolja elérhető helyen. Ezt követően a tároló üzembe helyezéséhez futtassa az alábbi parancsot. Adja meg a JSON-fájl nevét (`marathon.json` ebben a példában).

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

## <a name="reach-the-container"></a>A tároló eléréséhez

Ellenőrizheti, hogy az Nginx-tárolóban fut a privát ügynökök, a fürt egyik. Az állomás és port, ahol a tároló fut-e megkereséséhez marathonban a futó feladatok: 

```bash
curl localhost/marathon/v2/tasks
```

Keresse meg az értéket a `host` a kimenetben (IP-cím hasonló `10.32.0.x`), és az értéke `ports`.


Egy terminál SSH-kapcsolatot (nem bújtatott kapcsolat) Ellenőrizze a fürt FQDN-felügyelet. Ha csatlakoztatva van, győződjön meg a következő kérelmet, a megfelelő értékeit helyettesítse `host` és `ports`:

```bash
curl http://host:ports
```

Az nginx-et kiszolgáló kimenete az alábbihoz hasonló:

![Az Nginx-tárolóból](./media/container-service-mesos-marathon-rest/nginx.png)




## <a name="scale-your-containers"></a>A tárolók skálázása
A Marathon API segítségével horizontálisan felskálázhatja vagy leskálázhatja az alkalmazások központi telepítéseit. Az előző példában üzembe helyezett egy alkalmazáspéldányt. Ezt most skálázhatja három alkalmazáspéldányra. Ehhez hozzon létre egy JSON-fájlt az alábbi JSON-szöveg használatával, és tárolja elérhető helyen.

```json
{ "instances": 3 }
```

Futtassa a következő parancsot az alkalmazás horizontális felskálázása a bújtatott kapcsolat.

> [!NOTE]
> Az URI-ja http://localhost/marathon/v2/apps/ méretezhető az alkalmazás azonosítója követ. Ha az Nginx-mintát, hogy melyik itt, az URI lesz az http://localhost/marathon/v2/apps/nginx.

```bash
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

Végül kérdezze le az alkalmazásokat a Marathon végponton. Láthatja majd, hogy most már három Nginx-tároló létezik.

```bash
curl localhost/marathon/v2/apps
```

## <a name="equivalent-powershell-commands"></a>Egyenértékű PowerShell-parancsok
Ugyanezeket a műveleteket elvégezheti Windows rendszerben is a PowerShell-parancsok használatával.

A DC/OS fürt, például az ügynökök nevét és állapotát, információkat gyűjthet a következő parancsot:

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

Docker-formátumú tároló üzembe helyezéséhez a JSON-fájlt tárolja elérhető helyen. Ezt követően a tároló üzembe helyezéséhez futtassa az alábbi parancsot. A JSON-fájl elérési útját (`marathon.json` ebben a példában).

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

A Marathon API-t az üzemelő alkalmazáspéldányok horizontális skálázására is használhatja. Az előző példában üzembe helyezett egy alkalmazáspéldányt. Ezt most skálázhatja három alkalmazáspéldányra. Ehhez hozzon létre egy JSON-fájlt az alábbi JSON-szöveg használatával, és tárolja elérhető helyen.

```json
{ "instances": 3 }
```

A következő parancsot az alkalmazás horizontális felskálázása:

> [!NOTE]
> Az URI-ja http://localhost/marathon/v2/apps/ méretezhető az alkalmazás azonosítója követ. Ha itt az Nginx-mintát használ, az URI lesz http://localhost/marathon/v2/apps/nginx.

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

## <a name="next-steps"></a>További lépések
* [További információ a Mesos HTTP-végpontokat](https://mesos.apache.org/documentation/latest/endpoints/)
* [További információ a Marathon REST API](https://mesosphere.github.io/marathon/docs/rest-api.html)

