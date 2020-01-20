---
title: ELAVULT Azure Kubernetes-fürt kezelése webes felhasználói felülettel
description: A Kubernetes webes felhasználói felületének használata a Azure Container Serviceban
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 02/21/2017
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 6ce78ca19458b497980cf2cfc374f787d3a5d9f5
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76276979"
---
# <a name="deprecated-using-the-kubernetes-web-ui-with-azure-container-service"></a>ELAVULT A Kubernetes webes felhasználói felületének használata a Azure Container Service

> [!TIP]
> Az Azure Kubernetes Service-t használó cikk frissített verziója: [hozzáférés a Kubernetes webes irányítópulthoz az Azure Kubernetes Service-ben (ak)](../../aks/kubernetes-dashboard.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Előfeltételek
Ez a bemutató azt feltételezi, hogy [Azure Container Service használatával hozott létre egy Kubernetes-fürtöt](container-service-kubernetes-walkthrough.md).


Azt is feltételezi, hogy telepítve van az Azure CLI és a `kubectl` eszköz.

A futtatásával tesztelheti, hogy telepítve van-e a `az` eszköz:

```console
$ az --version
```

Ha nincs telepítve a `az` eszköz, [itt](https://github.com/azure/azure-cli#installation)talál útmutatást.

A futtatásával tesztelheti, hogy telepítve van-e a `kubectl` eszköz:

```console
$ kubectl version
```

Ha nincs `kubectl` telepítve, akkor a következőket futtathatja:

```console
$ az acs kubernetes install-cli
```

## <a name="overview"></a>Áttekintés

### <a name="connect-to-the-web-ui"></a>Kapcsolódás a webes felhasználói felülethez
A Kubernetes webes felhasználói felületét a következő futtatásával indíthatja el:

```console
$ az acs kubernetes browse -g [Resource Group] -n [Container service instance name]
```

Ekkor meg kell nyitnia egy webböngészőt, amely úgy van konfigurálva, hogy a helyi gépet a Kubernetes webes felhasználói felületéhez csatlakozó biztonságos proxyval beszéljen.

### <a name="create-and-expose-a-service"></a>Szolgáltatás létrehozása és közzététele
1. A Kubernetes webes felhasználói felületén kattintson a jobb felső ablakban található **Létrehozás** gombra.

    ![Kubernetes felhasználói felület létrehozása](./media/container-service-kubernetes-ui/create.png)

    Megnyílik egy párbeszédpanel, ahol megkezdheti az alkalmazás létrehozását.

2. Adja meg `hello-nginx`nevét. Használja a [docker`nginx` tárolóját](https://hub.docker.com/_/nginx/) , és telepítse a webszolgáltatás három replikáját.

    ![Kubernetes Pod létrehozás párbeszédpanel](./media/container-service-kubernetes-ui/nginx.png)

3. A **szolgáltatás**alatt válassza a **külső** lehetőséget, és adja meg a 80-as portot.

    Ezzel a beállítással a három replikára osztja a forgalmat.

    ![Kubernetes szolgáltatás létrehozása párbeszédpanel](./media/container-service-kubernetes-ui/service.png)

4. A tárolók és szolgáltatások telepítéséhez kattintson a **telepítés** elemre.

    ![Kubernetes üzembe helyezése](./media/container-service-kubernetes-ui/deploy.png)

### <a name="view-your-containers"></a>Tárolók megtekintése
Miután rákattintott a **telepítés**gombra, a felhasználói felület a szolgáltatás központi telepítésének nézetét jeleníti meg:

![Kubernetes állapota](./media/container-service-kubernetes-ui/status.png)

Az egyes Kubernetes objektumok állapotát a felhasználói felület bal oldalán, a **hüvelyek**területen tekintheti meg. Ha ez egy részben teljes kör, akkor a rendszer még mindig üzembe helyezi az objektumot. Ha egy objektum teljesen telepítve van, egy zöld pipa jelenik meg:

![Kubernetes központilag telepítve](./media/container-service-kubernetes-ui/deployed.png)

Ha minden fut, kattintson az egyik hüvelyre a futó webszolgáltatás részleteinek megtekintéséhez.

![Kubernetes hüvelyek](./media/container-service-kubernetes-ui/pods.png)

A **hüvelyek** nézetben megtekintheti a pod tárolók adatait, valamint a tárolók által használt CPU-és memória-erőforrásokat:

![Kubernetes-erőforrások](./media/container-service-kubernetes-ui/resources.png)

Ha nem látja az erőforrásokat, előfordulhat, hogy néhány percet várnia kell, amíg a figyelési adat propagálva lesz.

A tároló naplóinak megtekintéséhez kattintson a **naplók megtekintése**elemre.

![Kubernetes-naplók](./media/container-service-kubernetes-ui/logs.png)

### <a name="viewing-your-service"></a>A szolgáltatás megtekintése
A tárolók futtatásán kívül a Kubernetes felhasználói felületén egy külső `Service` is létrehozta, amely egy terheléselosztó használatával helyezi át a forgalmat a fürtben lévő tárolóba.

A bal oldali navigációs panelen kattintson a **szolgáltatások** elemre az összes szolgáltatás megtekintéséhez (csak egy).

![Kubernetes szolgáltatások](./media/container-service-kubernetes-ui/service-deployed.png)

Ebben a nézetben egy külső végpontot (IP-címet) kell látnia, amelyet a szolgáltatáshoz rendeltek.
Ha erre az IP-címére kattint, megjelenik a terheléselosztó mögött futó Nginx-tároló.

![Nginx-nézet](./media/container-service-kubernetes-ui/nginx-page.png)

### <a name="resizing-your-service"></a>A szolgáltatás átméretezése
Az objektumok felhasználói felületen való megtekintése mellett módosíthatja és frissítheti a Kubernetes API-objektumokat.

Először kattintson a **központi telepítések** elemre a bal oldali navigációs panelen a szolgáltatás üzembe helyezésének megtekintéséhez.

Ha ezt a nézetet választotta, kattintson a másodpéldányra, majd a felső navigációs sávon a **Szerkesztés** elemre:

![Kubernetes szerkesztése](./media/container-service-kubernetes-ui/edit.png)

Szerkessze a `spec.replicas` mezőt `2`, majd kattintson a **frissítés**gombra.

Ez azt eredményezi, hogy a replikák száma az egyik hüvely törlésével kettőre csökken.

 

