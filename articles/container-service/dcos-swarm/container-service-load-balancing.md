---
title: Tárolók terheléselosztása az Azure DC/OS-fürtben
description: Több tárolóra kiterjedő terheléselosztás egy Azure Container Service DC/OS-fürtben.
services: container-service
author: rgardler
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 06/02/2017
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: 8e8ad913c6e5b8e947b17ed8920285de3fcfd8ae
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46964360"
---
# <a name="load-balance-containers-in-an-azure-container-service-dcos-cluster"></a>Tárolók terheléselosztása egy Azure Container Service DC/OS-fürtben

Ebben a cikkben azt vizsgáljuk meg, hogyan lehet belső terheléselosztót létrehozni a Marathon-LB-vel egy, a DC/OS által kezelt Azure Container Service szolgáltatásban. Ez a konfiguráció lehetőséget nyújt az alkalmazások horizontális skálázására. Ezenkívül lehetővé teszi a nyilvános és a privát ügynökös fürtök használatát. Ehhez a terheléselosztókat a nyilvános fürtbe kell helyezni, az alkalmazástárolókat pedig a privát fürtbe. Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Egy Marathon Load Balancer konfigurálása
> * Alkalmazás üzembe helyezése a terheléselosztóval
> * Az Azure Load Balancer konfigurálása

Az oktatóanyagban ismertetett lépések végrehajtásához szüksége lesz egy ACS DC/OS-fürtre. Amennyiben szükséges, [ezzel a mintaszkripttel](./../kubernetes/scripts/container-service-cli-deploy-dcos.md) létrehozhat egyet.

Az oktatóanyaghoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="load-balancing-overview"></a>Terheléselosztás – áttekintés

Az Azure Container Service DC/OS-fürtön két terheléselosztó réteg található: 

Az **Azure Load Balancer** nyilvános belépési pontokat biztosít (amelyekhez a végfelhasználók hozzáférnek). Az Azure LB-t az Azure Container Service automatikusan biztosítja, és alapértelmezés szerint a 80-as, 443-as és 8080-as portok használatára van konfigurálva.

**A Marathon Load Balancer (Marathon-LB)** a bejövő kéréseket olyan tárolópéldányokhoz irányítja, amelyek ezeket a kéréseket kiszolgálják. A webszolgáltatás biztosítás közben a Marathon-LB dinamikusan alkalmazkodik a tárolók méretezéséhez. Ez a terheléselosztó nincs alapértelmezés szerint megadva a Container Service-ben, de könnyű telepíteni.

## <a name="configure-marathon-load-balancer"></a>A Marathon Load Balancer konfigurálása

A Marathon Load Balancer dinamikusan újrakonfigurálja magát az üzembe helyezett tárolók alapján. A tárolók és az ügynökök elvesztése sem zavarja meg a működését. Ilyen esetekben az Apache Mesos máshol indítja újra a tárolót, és a Marathon-LB alkalmazkodik a változáshoz.

A Marathon Load Balancer a nyilvános ügynök fürtjén való telepítéséhez futtassa a következő parancsot.

```azurecli-interactive
dcos package install marathon-lb
```

## <a name="deploy-load-balanced-application"></a>Elosztott terhelésű alkalmazás üzembe helyezése

Most, hogy már rendelkezésre áll a marathon-lb csomag, üzembe helyezhetünk egy alkalmazástárolót, amelynek el kívánjuk osztani a terhelését. 

Első lépésként kérje le a nyilvános ügynökök FQDN-jét.

```azurecli-interactive
az acs list --resource-group myResourceGroup --query "[0].agentPoolProfiles[0].fqdn" --output tsv
```

Ezután hozzon létre egy fájlt *hello-web.json* néven, és másolja bele a következő tartalmat. A `HAPROXY_0_VHOST` címkét a DC/OS-ügynökök FQDN-jével kell frissíteni. 

```json
{
  "id": "web",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "yeasy/simple-web",
      "network": "BRIDGE",
      "portMappings": [
        { "hostPort": 0, "containerPort": 80, "servicePort": 10000 }
      ],
      "forcePullImage":true
    }
  },
  "instances": 3,
  "cpus": 0.1,
  "mem": 65,
  "healthChecks": [{
      "protocol": "HTTP",
      "path": "/",
      "portIndex": 0,
      "timeoutSeconds": 10,
      "gracePeriodSeconds": 10,
      "intervalSeconds": 2,
      "maxConsecutiveFailures": 10
  }],
  "labels":{
    "HAPROXY_GROUP":"external",
    "HAPROXY_0_VHOST":"YOUR FQDN",
    "HAPROXY_0_MODE":"http"
  }
}
```

A DC/OS CLI-vel futtassa az alkalmazást. A Marathon alapértelmezés szerint a privát fürtön helyezi üzembe az alkalmazást. Ez azt jelenti, hogy a fenti üzembe helyezés csak a terheléselosztóról érhető el, ami általában a kívánt viselkedés.

```azurecli-interactive
dcos marathon app add hello-web.json
```

Az alkalmazás üzembe helyezése után keresse meg az ügynökfürt FQDN-jét, és tekintse meg az elosztott terhelésű alkalmazást.

![Kép az elosztott terhelésű alkalmazásról](./media/container-service-load-balancing/lb-app.png)

## <a name="configure-azure-load-balancer"></a>Az Azure Load Balancer konfigurálása

Alapértelmezés szerint az Azure Load Balancer a 80-as, 8080-as és 443-as portokat teszi elérhetővé. Ha ezen portok egyikét használja (ahogyan a fenti példában is), akkor semmit nem kell tennie. Képesnek kell lennie arra, hogy rákattintson az ügynöke terheléselosztójának FQDN-jére, és minden frissítéskor ciklikusan fogja elérni a három webkiszolgáló egyikét. 

De ha másik portot használ, ciklikus szabályt és egy hálózatfigyelőt kell hozzáadnia a terheléselosztón a használt porthoz. Ezt az [Azure parancssori felületén](../../azure-resource-manager/xplat-cli-azure-resource-manager.md) teheti meg az `azure network lb rule create` és `azure network lb probe create` parancsokkal.

## <a name="next-steps"></a>További lépések

Ebben az oktatóprogramban megismerhette az ACS terheléselosztását a Marathon és az Azure Load Balancer segítségével, és a következő műveleteket végezte el:

> [!div class="checklist"]
> * Egy Marathon Load Balancer konfigurálása
> * Alkalmazás üzembe helyezése a terheléselosztóval
> * Az Azure Load Balancer konfigurálása

A következő oktatóanyagban megtudhatja, hogyan integrálhatja az Azure Storage szolgáltatást a DC/OS szolgáltatással az Azure-ban.

> [!div class="nextstepaction"]
> [Azure-fájlmegosztás egy DC/OS-fürtben](container-service-dcos-fileshare.md)