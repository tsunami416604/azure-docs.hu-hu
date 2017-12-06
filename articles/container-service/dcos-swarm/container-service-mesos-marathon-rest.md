---
title: "A Marathon REST API-t Azure DC/OS-fürt kezeléséhez"
description: "Tárolók telepítése egy Azure tároló szolgáltatás DC/OS fürtben a Marathon REST API használatával."
services: container-service
author: dlepow
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 04/04/2017
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: c9322756c30011305ebe6f4f2fd38554f275a1b3
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2017
---
# <a name="dcos-container-management-through-the-marathon-rest-api"></a>A Marathon REST API-t a DC/OS-tárolók kezelése

A DC/OS biztosítja a fürtözött feladatok telepítését és skálázását lehetővé tevő környezetet, ugyanakkor absztrakciós rétegként működik a hardver fölött. A DC/OS fölötti keretrendszer gondoskodik a számítási feladatok ütemezéséről és végrehajtásáról. Bár számos népszerű számítási elérhetők keretrendszerek, ez a dokumentum beolvasása elkezdésének létrehozásán és skálázásán üzemelő tárolópéldányokat a Marathon REST API használatával. 

## <a name="prerequisites"></a>Előfeltételek

A példákban szereplő feladatok elvégzéséhez szüksége lesz egy az Azure tárolószolgáltatásban konfigurált DC/OS-fürtre, valamint távoli kapcsolatot kell tudnia létesíteni a fürttel. Ezekkel az elemekkel kapcsolatban a következő cikkekben talál további tájékoztatást:

* [Azure Container Service-fürt üzembe helyezése](container-service-deployment.md)
* [Csatlakozás Azure Container Service-fürthöz](../container-service-connect.md)

## <a name="access-the-dcos-apis"></a>Hozzáférés a DC/OS API-k
Miután csatlakozott az Azure tárolószolgáltatás-fürthöz, a DC/OS-t és a megfelelő REST API-kat a http://localhost:local-port címen érheti el. Az ebben a dokumentumban szereplő példák azt feltételezik, hogy az alagutat a 80-as porton keresztül hozta létre. Például a Marathon végpontok címen érhető el URI-azonosítók kezdve `http://localhost/marathon/v2/`. 

A [Marathon API-ról](https://mesosphere.github.io/marathon/docs/rest-api.html) és a [Chronos API-ról](https://mesos.github.io/chronos/docs/api.html) a Mesosphere dokumentációjában, a [Mesos Scheduler API-ról](http://mesos.apache.org/documentation/latest/scheduler-http-api/) pedig az Apache dokumentációjában talál további információt.

## <a name="gather-information-from-dcos-and-marathon"></a>Információgyűjtés a DC/OS-ről és a Marathonról
Mielőtt tárolókat a DC/OS-fürtről telepít, a DC/OS-fürtről, például nevét és a DC/OS-ügynökök állapotának néhány információt gyűjteni. Ehhez kérdezze le a DC/OS REST API fő- és alárendelt kiszolgálóinak (`master/slaves`) végpontját. Ha minden megfelelően működik, a lekérdezés a DC/OS-ügynökök listáját és az ügynökök különböző tulajdonságait adja vissza.

```bash
curl http://localhost/mesos/master/slaves
```

A DC/OS-fürtben üzembe helyezett alkalmazások lekérdezéséhez használja a Marathon `/apps` végpontot. Ha ez egy új fürt, akkor az alkalmazásoknál egy üres tömb jelenik meg.

```bash
curl localhost/marathon/v2/apps

{"apps":[]}
```

## <a name="deploy-a-docker-formatted-container"></a>Docker-formázású tároló üzembe helyezése
A JSON-fájl, amely leírja a kívánt üzembe helyezéssel segítségével telepítheti Docker-formátumú tárolók Marathon REST API-n keresztül. Az alábbi minta egy titkos ügynököt a fürt egy Nginx tároló telepíti. 

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

Egy Docker-formátumú tároló üzembe helyezéséhez tárolja elérhető helyen a JSON-fájlt. Ezt követően a tároló üzembe helyezéséhez futtassa az alábbi parancsot. Adja meg a JSON-fájl nevét (`marathon.json` ebben a példában).

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

Ellenőrizheti, hogy a Nginx tároló fut. a titkos ügynököket a fürt egyik. A gazdagép és a port, amelyen fut a tárolóban található, marathonban a futó feladatok: 

```bash
curl localhost/marathon/v2/tasks
```

Keresse meg az értéket a `host` kimenet (hasonló IP-cím `10.32.0.x`), és az értéke `ports`.


Egy terminál SSH-kapcsolat (nem bújtatott kapcsolat) Ellenőrizze a fürt FQDN-felügyelet. A csatlakozás után ellenőrizze a következő kérelmet, és a megfelelő értékeket `host` és `ports`:

```bash
curl http://host:ports
```

A Nginx server kimenete az alábbihoz hasonló:

![Nginx-tárolójából.](./media/container-service-mesos-marathon-rest/nginx.png)




## <a name="scale-your-containers"></a>A tárolók skálázása
A Marathon API segítségével horizontális felskálázás vagy méretezni az alkalmazások központi telepítéseit. Az előző példában üzembe helyezett egy alkalmazáspéldányt. Ezt most skálázhatja három alkalmazáspéldányra. Ehhez hozzon létre egy JSON-fájlt az alábbi JSON-szöveg használatával, és tárolja elérhető helyen.

```json
{ "instances": 3 }
```

A bújtatott kapcsolat létrehozásakor futtassa a következő parancsot az alkalmazás horizontális.

> [!NOTE]
> Az URI a http://localhost/marathon/v2/apps/ cím, amelyet a skálázandó alkalmazás azonosítója követ. Ha az itt szerepelő Nginx mintát használja, akkor az URI a http://localhost/marathon/v2/apps/nginx cím lesz.
> 
> 

```bash
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

Végül kérdezze le az alkalmazásokat a Marathon végponton. Láthatja majd, hogy most már három Nginx-tároló létezik.

```bash
curl localhost/marathon/v2/apps
```

## <a name="equivalent-powershell-commands"></a>Egyenértékű PowerShell-parancsok
Ugyanezeket a műveleteket elvégezheti Windows rendszerben is a PowerShell-parancsok használatával.

Gyűjtsön információt a DC/OS fürtben, mint az ügynökök nevét és állapotát, a következő parancsot:

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

Egy Docker-formátumú tároló üzembe helyezéséhez tárolja elérhető helyen a JSON-fájlt. Ezt követően a tároló üzembe helyezéséhez futtassa az alábbi parancsot. Adja meg a JSON-fájl elérési útját (`marathon.json` ebben a példában).

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

A Marathon API-t az üzemelő alkalmazáspéldányok horizontális skálázására is használhatja. Az előző példában üzembe helyezett egy alkalmazáspéldányt. Ezt most skálázhatja három alkalmazáspéldányra. Ehhez hozzon létre egy JSON-fájlt az alábbi JSON-szöveg használatával, és tárolja elérhető helyen.

```json
{ "instances": 3 }
```

A következő parancsot az alkalmazás horizontális:

> [!NOTE]
> Az URI a http://localhost/marathon/v2/apps/ cím, amelyet a skálázandó alkalmazás azonosítója követ. Ha az Nginx-mintát használja, akkor az URI a http://localhost/marathon/v2/apps/nginx cím lesz.
> 
> 

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

## <a name="next-steps"></a>Következő lépések
* [További tudnivalók a Mesos HTTP-végpontokról](http://mesos.apache.org/documentation/latest/endpoints/)
* [További tudnivalók a Marathon REST API](https://mesosphere.github.io/marathon/docs/rest-api.html)

