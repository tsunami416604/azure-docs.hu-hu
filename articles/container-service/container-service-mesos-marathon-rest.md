---
title: "Azure tárolószolgáltatás-tárolók kezelése a REST API-val | Microsoft Docs"
description: "Tárolók üzembe helyezése Azure tárolószolgáltatásbeli Mesos fürtben a Marathon REST API használatával."
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, tárolók, mikroszolgáltatások, Mesos, Azure"
ms.assetid: c7175446-4507-4a33-a7a2-63583e5996e3
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 54832afbc9a7bf1d660de3fd898ad5c97715ca5d
ms.openlocfilehash: a01993eb01b9e05b4848d5a81b841fe10ccae035


---
# <a name="container-management-through-the-rest-api"></a>Tárolókezelés a REST API használatával
A DC/OS biztosítja a fürtözött feladatok telepítését és skálázását lehetővé tevő környezetet, ugyanakkor absztrakciós rétegként működik a hardver fölött. A DC/OS fölötti keretrendszer gondoskodik a számítási feladatok ütemezéséről és végrehajtásáról.

Számos népszerű számítási feladathoz érhetők el keretrendszerek. Ez a dokumentum azt ismerteti, hogy miként hozhat létre és hogyan méretezhet üzemelő tárolópéldányokat a Marathon használatával. A példákban szereplő feladatok elvégzéséhez szüksége lesz egy az Azure tárolószolgáltatásban konfigurált DC/OS-fürtre, valamint távoli kapcsolatot kell tudnia létesíteni a fürttel. Ezekkel az elemekkel kapcsolatban a következő cikkekben talál további tájékoztatást:

* [Azure Container Service-fürt üzembe helyezése](container-service-deployment.md)
* [Csatlakozás Azure Container Service-fürthöz](container-service-connect.md)

Miután csatlakozott az Azure tárolószolgáltatás-fürthöz, a DC/OS-t és a megfelelő REST API-kat a http://localhost:local-port címen érheti el. Az ebben a dokumentumban szereplő példák azt feltételezik, hogy az alagutat a 80-as porton keresztül hozta létre. A Marathon-végpont például a `http://localhost/marathon/v2/` címen érhető el. A [Marathon API-ról](https://mesosphere.github.io/marathon/docs/rest-api.html) és a [Chronos API-ról](https://mesos.github.io/chronos/docs/api.html) a Mesosphere dokumentációjában, a [Mesos Scheduler API-ról](http://mesos.apache.org/documentation/latest/scheduler-http-api/) pedig az Apache dokumentációjában talál további információt.

## <a name="gather-information-from-dcos-and-marathon"></a>Információgyűjtés a DC/OS-ről és a Marathonról
Mielőtt tárolókat helyezne üzembe a DC/OS fürtben, gyűjtsön össze néhány olyan információt a DC/OS-fürtről, mint például a DC/OS-ügynökök neve és aktuális állapota. Ehhez kérdezze le a DC/OS REST API fő- és alárendelt kiszolgálóinak (`master/slaves`) végpontját. Ha minden megfelelően működik, a lekérdezés a DC/OS-ügynökök listáját és az ügynökök különböző tulajdonságait adja vissza.

```bash
curl http://localhost/mesos/master/slaves
```

A DC/OS-fürtben üzembe helyezett alkalmazások lekérdezéséhez használja a Marathon `/apps` végpontot. Ha ez egy új fürt, akkor az alkalmazásoknál egy üres tömb jelenik meg.

```
curl localhost/marathon/v2/apps

{"apps":[]}
```

## <a name="deploy-a-docker-formatted-container"></a>Docker-formázású tároló üzembe helyezése
A Docker-formátumú tárolók Marathon segítségével való üzembe helyezéséhez egy olyan JSON-fájlt kell használnia, amelyben megadhatja a kívánt üzembe helyezéssel kapcsolatos információkat. Az alábbi példában, amely egy Nginx-tároló üzembe helyezését szemlélteti, a DC/OS-ügynök 80-as portja a tároló 80-as portjával van összekötve. Figyelje meg, hogy az „acceptedResourceRoles” tulajdonság értéke „slave_public”. Ezáltal a tároló egy nyilvános ügynökskálázási készletben lévő ügynökön lesz üzembe helyezve.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 16.0,
  "instances": 1,
    "acceptedResourceRoles": [
    "slave_public"
  ],
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "hostPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Docker-formátumú tároló üzembe helyezéséhez hozzon létre egy saját JSON-fájlt, vagy használja az [Azure tárolószolgáltatás bemutatójában](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json) elérhető mintát. A fájlt tárolja elérhető helyen. Ezt követően a tároló üzembe helyezéséhez futtassa az alábbi parancsot. Adja meg a JSON-fájl nevét.

```
curl -X POST http://localhost/marathon/v2/apps -d @marathon.json -H "Content-type: application/json"
```

A kimenet az alábbihoz hasonló lesz:

```json
{"version":"2015-11-20T18:59:00.494Z","deploymentId":"b12f8a73-f56a-4eb1-9375-4ac026d6cdec"}
```

Ha ezt követően lekérdezi az alkalmazásokat a Marathonban, az eredmények között megjelenik az új alkalmazás is.

```
curl localhost/marathon/v2/apps
```

## <a name="scale-your-containers"></a>A tárolók skálázása
A Marathon API-t az üzemelő alkalmazáspéldányok horizontális skálázására is használhatja. Az előző példában üzembe helyezett egy alkalmazáspéldányt. Ezt most skálázhatja három alkalmazáspéldányra. Ehhez hozzon létre egy JSON-fájlt az alábbi JSON-szöveg használatával, és tárolja elérhető helyen.

```json
{ "instances": 3 }
```

Az alkalmazás horizontális skálázásához használja az alábbi parancsot.

> [!NOTE]
> Az URI a http://localhost/marathon/v2/apps/ cím, amelyet a skálázandó alkalmazás azonosítója követ. Ha az itt szerepelő Nginx mintát használja, akkor az URI a http://localhost/marathon/v2/apps/nginx cím lesz.
> 
> 

```json
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

Végül kérdezze le az alkalmazásokat a Marathon végponton. Láthatja majd, hogy most már három Nginx-tároló létezik.

```
curl localhost/marathon/v2/apps
```

## <a name="use-powershell-for-this-exercise-marathon-rest-api-interaction-with-powershell"></a>Ehhez a gyakorlathoz használja a PowerShellt: Marathon REST API – kommunikáció a PowerShell-lel.
Ugyanezeket a műveleteket elvégezheti Windows rendszerben is a PowerShell-parancsok használatával.

Ha információt szeretne gyűjteni a DC/OS-fürtről, például szeretné beolvasni az ügynökök nevét és állapotát, használja az alábbi parancsot.

```powershell
Invoke-WebRequest -Uri http://localhost/mesos/master/slaves
```

A Docker-formátumú tárolók Marathon segítségével való üzembe helyezéséhez egy olyan JSON-fájlt kell használnia, amelyben megadhatja a kívánt üzembe helyezéssel kapcsolatos információkat. Az alábbi példában, amely egy Nginx-tároló üzembe helyezését szemlélteti, a DC/OS-ügynök 80-as portja a tároló 80-as portjával van összekötve.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 16.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "hostPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Hozzon létre egy saját JSON-fájlt, vagy használja az [Azure tárolószolgáltatás bemutatójában](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json) elérhető mintát. A fájlt tárolja elérhető helyen. Ezt követően a tároló üzembe helyezéséhez futtassa az alábbi parancsot. Adja meg a JSON-fájl nevét.

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

A Marathon API-t az üzemelő alkalmazáspéldányok horizontális skálázására is használhatja. Az előző példában üzembe helyezett egy alkalmazáspéldányt. Ezt most skálázhatja három alkalmazáspéldányra. Ehhez hozzon létre egy JSON-fájlt az alábbi JSON-szöveg használatával, és tárolja elérhető helyen.

```json
{ "instances": 3 }
```

Az alkalmazás horizontális skálázásához használja az alábbi parancsot.

> [!NOTE]
> Az URI a http://localhost/marathon/v2/apps/ cím, amelyet a skálázandó alkalmazás azonosítója követ. Ha az Nginx-mintát használja, akkor az URI a http://localhost/marathon/v2/apps/nginx cím lesz.
> 
> 

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

## <a name="next-steps"></a>Következő lépések
* [További információk a Mesos HTTP-végpontokról](http://mesos.apache.org/documentation/latest/endpoints/).
* [További információk a Marathon REST API-ról](https://mesosphere.github.io/marathon/docs/rest-api.html).




<!--HONumber=Dec16_HO2-->


