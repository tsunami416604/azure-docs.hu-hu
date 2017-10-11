---
title: "Betöltése Azure DC/OS-fürtről a tárolók elosztása |} Microsoft Docs"
description: "Egy Azure tároló szolgáltatás DC/OS-fürtben több tároló terheléselosztása."
services: container-service
documentationcenter: 
author: rgardler
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Tárolók, mikroszolgáltatások, DC/OS, Azure"
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/02/2017
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: 78725c9d23e13d307821a188028ef573d1def038
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="load-balance-containers-in-an-azure-container-service-dcos-cluster"></a>Betöltési egyenleg tárolók egy Azure tároló szolgáltatás DC/OS-fürtben
Ebben a cikkben megismerkedhet azt egy belső terheléselosztó létrehozása a DC/OS felügyelete az Azure Tárolószolgáltatás Marathon-LB használatával. Ez a konfiguráció lehetővé teszi az alkalmazások horizontálisan méretezhető. Azt is lehetővé teszi, hogy kihasználhatja a nyilvános és titkos ügynök fürtök úgy, hogy a terheléselosztó a nyilvános és titkos fürtön alkalmazás tárolók skálázása. Ebben az oktatóanyagban az alábbiakat végezte el:

> [!div class="checklist"]
> * A Marathon terheléselosztó konfigurálása
> * Olyan központi telepítésű alkalmazást használ a load balancer
> * Konfigurálja és Azure terheléselosztó

Az ACS DC/OS-fürt az oktatóanyag lépéseinek végrehajtásához van szüksége. Ha szükséges, [a parancsfájl minta](./../kubernetes/scripts/container-service-cli-deploy-dcos.md) hozhat létre egyet.

Az oktatóanyaghoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="load-balancing-overview"></a>Terheléselosztás – áttekintés

Az alábbi két terheléselosztási réteg Azure tároló szolgáltatás DC/OS-fürtben lévő: 

**Az Azure Load Balancer** biztosít a nyilvános belépési pontok (a végfelhasználók hozzáférhetnének az megfelelően). Az Azure LB Azure Tárolószolgáltatás automatikusan biztosítja, és alapértelmezés szerint a 80-as, a 443-as és a 8080-as portot teszi közzé konfigurálva van.

**A Marathon terheléselosztó (marathon-lb)** útvonalak bejövő kérelmek ezen érkező kérelmek kiszolgálása során tárolópéldányt. A webszolgáltatás nyújtó tárolók skálázásához, a marathon-lb dinamikusan alkalmazkodik. Ez a terheléselosztó nem szerepel a Tárolószolgáltatás alapértelmezés szerint, de egyszerű telepítéséhez.

## <a name="configure-marathon-load-balancer"></a>A Marathon terheléselosztó konfigurálása

A Marathon Load Balancer dinamikusan újrakonfigurálja magát az üzembe helyezett tárolók alapján. Akkor is rugalmas elvész egy tároló vagy ügynök - Ha ez történik, Apache Mesos újraindítja a tárolót máshol, és a marathon-lb alkalmazkodik.

A következő parancsot a marathon terheléselosztó a nyilvános ügynököt a fürtön telepíteni.

```azurecli-interactive
dcos package install marathon-lb
```

## <a name="deploy-load-balanced-application"></a>Elosztott terhelésű alkalmazás központi telepítése

Most, hogy már rendelkezésre áll a marathon-lb csomag, üzembe helyezhetünk egy alkalmazástárolót, amelynek el kívánjuk osztani a terhelését. 

Első lépésként beolvasása a nyilvánosan elérhetővé ügynökök teljes Tartományneve.

```azurecli-interactive
az acs list --resource-group myResourceGroup --query "[0].agentPoolProfiles[0].fqdn" --output tsv
```

Következő lépésként hozzon létre egy fájlt *hello-web.json* , és másolja a következő tartalmában. A `HAPROXY_0_VHOST` címke a DC/OS-ügynökök a teljes tartománynévvel frissíteni kell. 

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

A DC/OS parancssori felület használatával futtassa az alkalmazást. Alapértelmezés szerint a Marathon telepíti a titkos fürtre alkalmazástelepítések. Ez azt jelenti, hogy a fenti központi telepítés csak a terheléselosztó keresztül érhető el ez általában a kívánt viselkedés.

```azurecli-interactive
dcos marathon app add hello-web.json
```

Miután az alkalmazás telepítve van, tallózással keresse meg az ügynök fürt terhelésű alkalmazást teljesen minősített Tartománynevét.

![Elosztott terhelésű alkalmazások képe](./media/container-service-load-balancing/lb-app.png)

## <a name="configure-azure-load-balancer"></a>Az Azure terheléselosztó konfigurálása

Alapértelmezés szerint az Azure Load Balancer a 80-as, 8080-as és 443-as portokat teszi elérhetővé. Ha ezen portok egyikét használja (ahogyan a fenti példában is), akkor semmit nem kell tennie. Kell tudnia érni az ügynök terheléselosztó FQDN, és minden alkalommal, amikor frissíti, lesz elérte a ciklikus multiplexelés webkiszolgálót egyikét. 

Ha másik portot használ, adja hozzá a port, amelyet akkor használ a load balancer egy ciklikus multiplexelési szabályt és egy mintavételt szeretné. Ezt az [Azure parancssori felületén](../../azure-resource-manager/xplat-cli-azure-resource-manager.md) teheti meg az `azure network lb rule create` és `azure network lb probe create` parancsokkal.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóprogramban megismerte terheléselosztás az ACS és a Marathon és az Azure terheléselosztó többek között a következő műveleteket:

> [!div class="checklist"]
> * A Marathon terheléselosztó konfigurálása
> * Olyan központi telepítésű alkalmazást használ a load balancer
> * Konfigurálja és Azure terheléselosztó

A következő oktatóanyag további információt az Azure storage integrálása az Azure-ban a DC/OS továbblépés.

> [!div class="nextstepaction"]
> [A DC/OS-fürt csatlakoztatási Azure fájlmegosztás](container-service-dcos-fileshare.md)