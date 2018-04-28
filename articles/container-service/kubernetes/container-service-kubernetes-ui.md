---
title: Webes felhasználói felület Azure Kubernetes fürt kezelése
description: Az Azure Tárolószolgáltatásban a Kubernetes webes felhasználói felület használatával
services: container-service
author: bburns
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 02/21/2017
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 0680551d3a87c942574a4eac70fa380cc1e9b5d9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="using-the-kubernetes-web-ui-with-azure-container-service"></a>Azure Tárolószolgáltatás a Kubernetes webes felhasználói felület használatával

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató feltételezi, hogy rendelkezik [a Kubernetes Azure Tárolószolgáltatási fürt létrehozott](container-service-kubernetes-walkthrough.md).


Azt is feltételezi, hogy rendelkezik-e az Azure CLI 2.0 és `kubectl` eszközök vannak telepítve.

Ha tesztelheti a `az` eszköz futtatásával telepítve:

```console
$ az --version
```

Ha nem rendelkezik a `az` eszköz telepítve, az e-mail utasításokat is [Itt](https://github.com/azure/azure-cli#installation).

Ha tesztelheti a `kubectl` eszköz futtatásával telepítve:

```console
$ kubectl version
```

Ha nem rendelkezik `kubectl` telepítve, futtatható:

```console
$ az acs kubernetes install-cli
```

## <a name="overview"></a>Áttekintés

### <a name="connect-to-the-web-ui"></a>A webes felhasználói felület csatlakozik
A Kubernetes webes felhasználói felület futtatásával indíthatja el:

```console
$ az acs kubernetes browse -g [Resource Group] -n [Container service instance name]
```

Ez kell nyisson meg egy webböngészőt, kérdezze meg a helyi számítógép csatlakozik a Kubernetes webes felhasználói felület biztonságos proxy konfigurálva.

### <a name="create-and-expose-a-service"></a>Hozzon létre, és tegye elérhetővé a szolgáltatás
1. A Kubernetes webes felhasználói felületen kattintson **létrehozása** gombra a jobb felső ablakban.

    ![Kubernetes felhasználói Felületüket létrehozni.](./media/container-service-kubernetes-ui/create.png)

    Egy adott elindíthatja az alkalmazás létrehozása párbeszédpanel megnyílik.

2. A nevet `hello-nginx`. Használja a [ `nginx` tárolót a Docker](https://hub.docker.com/_/nginx/) és három replikák webszolgáltatás telepítése.

    ![Kubernetes Pod létrehozása párbeszédpanel](./media/container-service-kubernetes-ui/nginx.png)

3. A **szolgáltatás**, jelölje be **külső** , és írja be a 80-as porton.

    Ez a beállítás kiegyenlíti forgalom a három replikához.

    ![Kubernetes Service létrehozás párbeszédpanel](./media/container-service-kubernetes-ui/service.png)

4. Kattintson a **telepítés** ezek a tárolók és szolgáltatások telepítéséhez.

    ![Kubernetes központi telepítése](./media/container-service-kubernetes-ui/deploy.png)

### <a name="view-your-containers"></a>A tárolók megtekintése
Miután rákattintott **telepítés**, a felhasználói felületen jeleníti meg, a szolgáltatás módon telepíti:

![Kubernetes állapota](./media/container-service-kubernetes-ui/status.png)

Megtekintheti a kör Kubernetes objektumok állapotának a felhasználói felület, a bal oldalon **három munkaállomás-csoporttal**. Ha egy részben teljes kör, az objektum még telepítés. Az objektum teljes mértékben telepítve, egy zöld pipa jeleníti meg:

![Telepített Kubernetes](./media/container-service-kubernetes-ui/deployed.png)

Ha minden fut, kattintson a három munkaállomás-csoporttal a futó webes szolgáltatás kapcsolatos részletek megtekintéséhez.

![Kubernetes három munkaállomás-csoporttal](./media/container-service-kubernetes-ui/pods.png)

Az a **három munkaállomás-csoporttal** nézet, a tárolók fogyasztanak, valamint a CPU és memória-erőforrások összes blobhoz által használt információ látható:

![Kubernetes erőforrások](./media/container-service-kubernetes-ui/resources.png)

Ha nem látja az erőforrásokat, esetleg Várjon néhány percet a figyelési adatok továbbítására.

A tároló a naplók megtekintéséhez kattintson **naplók megtekintése**.

![Kubernetes naplók](./media/container-service-kubernetes-ui/logs.png)

### <a name="viewing-your-service"></a>A szolgáltatás megtekintése
A Kubernetes felhasználói felület mellett futó a tárolók, külső hozott létre `Service` amely látja el egy terhelés-kiegyenlítő forgalom kerüljön a tárolók a fürtön.

A bal oldali navigációs ablaktáblán kattintson **szolgáltatások** megtekintéséhez az összes szolgáltatást (kell csak egyet).

![Kubernetes szolgáltatások](./media/container-service-kubernetes-ui/service-deployed.png)

A fenti nézetben megjelenik a szolgáltatás felosztott külső végpont (IP-cím).
Ha az IP-címet gombra kattint, megtekintheti a terheléselosztó mögött futó Nginx tároló.

![nginx megtekintése](./media/container-service-kubernetes-ui/nginx-page.png)

### <a name="resizing-your-service"></a>A szolgáltatás átméretezése
Is megtekintheti az objektumok a felhasználói felület, szerkesztheti, és frissítse az Kubernetes API objektumokat.

Első lépésként kattintson **központi telepítések** a bal oldali navigációs panelen, hogy a szolgáltatás központi telepítését.

Miután a fenti nézetben, kattintson a replikakészlethez a, majd **szerkesztése** a felső navigációs sávján látható:

![Kubernetes Edit](./media/container-service-kubernetes-ui/edit.png)

Szerkessze a `spec.replicas` mezőre, amely `2`, és kattintson a **frissítés**.

Ennek hatására két egy a három munkaállomás-csoporttal eldobása replikák száma.

 

