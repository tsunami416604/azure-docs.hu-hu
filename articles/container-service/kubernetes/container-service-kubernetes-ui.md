---
title: (ELAVULT) Az Azure Kubernetes-fürt kezelése webes felhasználói felülettel
description: A Kubernetes webes felhasználói felületének használata az Azure Container Service-ben
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 02/21/2017
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 01abcc961d1c2ad9d3e2cf35f82e62929bc2fb89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371137"
---
# <a name="deprecated-using-the-kubernetes-web-ui-with-azure-container-service"></a>(ELAVULT) A Kubernetes webes felhasználói felületének használata az Azure Container Service szolgáltatással

> [!TIP]
> Az Azure Kubernetes-szolgáltatást használó cikk frissített verziójáról az [Access the Kubernetes webirányítópult jatt az Azure Kubernetes-szolgáltatás (AKS)](../../aks/kubernetes-dashboard.md)című témakörben talál.

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Előfeltételek
Ez a forgatókönyv feltételezi, hogy [létrehozott egy Kubernetes-fürtöt az Azure Container Service használatával.](container-service-kubernetes-walkthrough.md)


Azt is feltételezi, hogy az Azure `kubectl` CLI és az eszközök telepítve van.

Tesztelheti, hogy telepítve `az` van-e az eszköz a következő futtatásával:

```azurecli
az --version
```

Ha nincs telepítve az `az` eszköz, [itt](https://github.com/azure/azure-cli#installation)utasítások találhatók.

Tesztelheti, hogy telepítve `kubectl` van-e az eszköz a következő futtatásával:

```console
kubectl version
```

Ha nincs `kubectl` telepítve, futtathatja a következőket:

```azurecli
az acs kubernetes install-cli
```

## <a name="overview"></a>Áttekintés

### <a name="connect-to-the-web-ui"></a>Csatlakozás a webes felhasználói felülethez
A Kubernetes webes felhasználói felületét a következő futtatásával indíthatja el:

```azurecli
az acs kubernetes browse -g [Resource Group] -n [Container service instance name]
```

Ezzel meg kell nyitnia egy olyan webböngészőt, amely úgy van beállítva, hogy a helyi gépet a Kubernetes webes felhasználói felületéhez összekötő biztonságos proxyval beszéljen.

### <a name="create-and-expose-a-service"></a>Szolgáltatás létrehozása és kisajátítása
1. A Kubernetes webes felhasználói felületén kattintson a Jobb felső ablak **Létrehozás** gombjára.

    ![Kubernetes felhasználói felület létrehozása](./media/container-service-kubernetes-ui/create.png)

    Megnyílik egy párbeszédpanel, ahol megkezdheti az alkalmazás létrehozását.

2. Adja meg `hello-nginx`a nevét . Használja a [ `nginx` Docker-tárolót,](https://hub.docker.com/_/nginx/) és telepítse a webszolgáltatás három replikáját.

    ![Kubernetes pod létrehozási párbeszédpanel](./media/container-service-kubernetes-ui/nginx.png)

3. A **Szolgáltatás**csoportban válassza **a Külső** lehetőséget, és adja meg a 80-as portot.

    Ez a beállítás terhelési kiegyensúlyozza a forgalmat a három replikák.

    ![Kubernetes szolgáltatás létrehozása párbeszédpanel](./media/container-service-kubernetes-ui/service.png)

4. Kattintson **a Telepítés** gombra a tárolók és szolgáltatások üzembe helyezéséhez.

    ![Kubernetes – üzembe helyezés](./media/container-service-kubernetes-ui/deploy.png)

### <a name="view-your-containers"></a>A tárolók megtekintése
Miután a **Telepítés gombra kattintott,** a felhasználói felület megjeleníti a szolgáltatás központi telepítésének nézetét:

![Kubernetes-állapot](./media/container-service-kubernetes-ui/status.png)

A felhasználói felület bal oldalán, a **Pods**csoportban látható az egyes Kubernetes-objektumok állapota a felhasználói felület bal oldalán lévő körben. Ha ez egy részben teljes kör, akkor az objektum még mindig telepítve van. Ha egy objektum teljesen telepítve van, zöld pipa jelenik meg:

![Kubernetes telepítve](./media/container-service-kubernetes-ui/deployed.png)

Miután minden fut, kattintson az egyik podok a futó webszolgáltatás részleteinek megtekintéséhez.

![Kubernetes hüvelyek](./media/container-service-kubernetes-ui/pods.png)

A **Pods** nézetben a podban lévő tárolókra, valamint a tárolók által használt PROCESSZOR- és memóriaerőforrásokra vonatkozó információk láthatók:

![Kubernetes erőforrások](./media/container-service-kubernetes-ui/resources.png)

Ha nem látja az erőforrásokat, előfordulhat, hogy várnia kell néhány percet, amíg a figyelési adatok propagálása propagál.

A tároló naplóinak megtekintéséhez kattintson a **Naplók megtekintése gombra.**

![Kubernetes naplók](./media/container-service-kubernetes-ui/logs.png)

### <a name="viewing-your-service"></a>A szolgáltatás megtekintése
A tárolók futtatása mellett a Kubernetes felhasználói `Service` felülete létrehozott egy külsőt, amely egy terheléselosztót is kiír a fürt tárolóihoz való forgalom hozására.

A bal oldali navigációs ablakban kattintson a **Szolgáltatások** elemre az összes szolgáltatás megtekintéséhez (csak egy lehet).

![Kubernetes-szolgáltatások](./media/container-service-kubernetes-ui/service-deployed.png)

Ebben a nézetben egy külső végpontot (IP-címet) kell látnia, amely a szolgáltatáshoz van rendelve.
Ha rákattint az IP-címre, látnia kell a Nginx-tárolót a terheléselosztó mögött.

![nginx nézet](./media/container-service-kubernetes-ui/nginx-page.png)

### <a name="resizing-your-service"></a>A szolgáltatás átméretezése
Az objektumok felhasználói felületen való megtekintése mellett szerkesztheti és frissítheti a Kubernetes API-objektumokat is.

Először kattintson **a központi telepítések** a bal oldali navigációs ablakban a szolgáltatás központi telepítésének megtekintéséhez.

Miután ebben a nézetben vagy, kattintson a replikakészletre, majd a felső navigációs sáv **szerkesztés gombjára:**

![Kubernetes szerkesztés](./media/container-service-kubernetes-ui/edit.png)

Szerkesztheti `spec.replicas` a mezőt, `2`majd kattintson a **Frissítés gombra.**

Ennek hatására a replikák száma kettőre csökken az egyik podtörlésével.

 

