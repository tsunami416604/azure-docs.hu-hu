---
title: A Marathon felhasználói felület Azure DC/OS-fürt kezeléséhez
description: A cikk azt ismerteti, hogyan telepíthetők tárolók egy Azure tárolószolgáltatásba a Marathon webes felhasználói felület segítségével.
services: container-service
author: dlepow
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/04/2017
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 43407d40db0aab2772cb1baeab3471be68aee2ab
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="manage-an-azure-container-service-dcos-cluster-through-the-marathon-web-ui"></a>Azure Container Service DC/OS-fürt kezelése a Marathon webes felhasználói felületén

A DC/OS biztosítja a fürtözött feladatok telepítését és skálázását lehetővé tevő környezetet, ugyanakkor absztrakciós rétegként működik a hardver fölött. A DC/OS fölötti keretrendszer gondoskodik a számítási feladatok ütemezéséről és végrehajtásáról.

Számos népszerű számítási elérhetők keretrendszerek, ez a dokumentum ismerteti a marathon segítségével tárolók üzembe helyezésének első lépései. 


## <a name="prerequisites"></a>Előfeltételek
A példákban szereplő feladatok elvégzéséhez szüksége lesz egy az Azure tárolószolgáltatásban konfigurált DC/OS-fürtre, valamint távoli kapcsolatot kell tudnia létesíteni a fürttel. Ezekkel az elemekkel kapcsolatban a következő cikkekben talál további tájékoztatást:

* [Az Azure Container Service-fürt üzembe helyezése](container-service-deployment.md)
* [Csatlakozás Azure Container Service-fürthöz](../container-service-connect.md)

> [!NOTE]
> Ez a cikk feltételezi, hogy az alagutat a DC/OS-fürtre a helyi 80-as porton keresztül.
>

## <a name="explore-the-dcos-ui"></a>A DC/OS felhasználói felületének megnyitása
Az egy Secure Shell (SSH) alagút [létrehozott](../container-service-connect.md), keresse meg a http://localhost/. Ez betölti a DC/OS webes felhasználói felületét, és a fürtre vonatkozó információkat jelenít meg, például a felhasznált erőforrásokat, az aktív ügynököket és a futó szolgáltatásokat.

![A DC/OS UI felhasználói felülete](./media/container-service-mesos-marathon-ui/dcos2.png)

## <a name="explore-the-marathon-ui"></a>A Marathon felhasználói felület megnyitása
A Marathon felhasználói felület megtekintéséhez navigáljon a http://localhost/marathon. Ezen a képernyőn elindíthat egy új tárolót vagy más szolgáltatást az Azure tárolószolgáltatási DC/OS-fürtön. A futó tárolókkal és alkalmazásokkal kapcsolatos információkat is láthat.  

![Marathon felhasználói felület](./media/container-service-mesos-marathon-ui/dcos3.png)

## <a name="deploy-a-docker-formatted-container"></a>Docker-formátumú tároló üzembe helyezése
Ha új tárolót szeretne üzembe helyezni a Marathon segítségével, kattintson a **Create Application** (Alkalmazás létrehozása) gombra, és adja meg a következő információkat az űrlapon:

| Mező | Érték |
| --- | --- |
| ID (Azonosító) |nginx |
| Memory (Memória) | 32 |
| Image (Kép) |nginx |
| Network (Hálózat) |Bridged |
| Host Port (Gazdaport) |80 |
| Protocol (Protokoll) |TCP |

![New Application (Új alkalmazás) felhasználói felület – General (Általános)](./media/container-service-mesos-marathon-ui/dcos4.png)

![New Application (Új alkalmazás) felhasználói felület – Docker Container (Docker-tároló)](./media/container-service-mesos-marathon-ui/dcos5.png)

![New Application (Új alkalmazás) felhasználói felület – Ports and Service Discovery (Portok és szolgáltatások felderítése)](./media/container-service-mesos-marathon-ui/dcos6.png)

Ha statikusan le kívánja képezni a tárolóportot egy az ügynökön lévő portra, akkor a JSON üzemmódot kell használnia. Ehhez váltsa át a New Application (Új alkalmazás) varázslót erre az üzemmódra a **JSON Mode** (JSON üzemmód) váltógombbal. Ezután adja meg a következő beállítást az alkalmazás definíciójának `portMappings` szakaszában. A példa a tároló 80-as portját a DC/OS-ügynök 80-as portjához köti. Ennek a módosításnak az elvégzése után visszaválthat a varázsló JSON üzemmódjából.

```none
"hostPort": 80,
```

![New Application (Új alkalmazás) felhasználói felület – példa a 80-as portra](./media/container-service-mesos-marathon-ui/dcos13.png)

Ha engedélyezni szeretné az állapot-ellenőrzéseket, adjon meg egy útvonalat a **Health Checks** (Állapot-ellenőrzések) lapon.

![New Application (Új alkalmazás) felhasználói felület – állapot-ellenőrzések](./media/container-service-mesos-marathon-ui/dcos_healthcheck.png)

A DC/OS-fürt üzembe helyezésekor különféle privát és nyilvános ügynökök is települnek. Ahhoz, hogy a fürt elérhessen alkalmazásokat az internetről, nyilvános ügynökre kell telepíteni az alkalmazást. Ehhez válassza a New Application (Új alkalmazás) varázsló **Optional** (Választható) lapfülét, és írja be a **slave_public** értéket az **Accepted Resource Roles** (Elfogadott erőforrás-szerepkörök) mezőbe.

Ezután kattintson a **Create Application** (Alkalmazás létrehozása) elemre.

![New Application (Új alkalmazás) felhasználói felület – nyilvános ügynök beállítása](./media/container-service-mesos-marathon-ui/dcos14.png)

A Marathon főoldalára visszatérve láthatja a tároló üzembe helyezési állapotát. Kezdetben a **Deploying** (Üzembe helyezés) állapotot fogja látni. A sikeres üzembe helyezést követően az állapot **Running** (Fut) állapotra vált.

![A Marathon főoldala – a tároló üzembe helyezési állapota](./media/container-service-mesos-marathon-ui/dcos7.png)

Amikor visszavált a DC/OS webes felhasználói felülete (http://localhost/), láthatja, hogy a DC/OS fürtben fut egy feladat (ebben az esetben egy Docker-formázású tároló).

![DC/OS webes felhasználói felülete – a fürtön futó feladat](./media/container-service-mesos-marathon-ui/dcos8.png)

Ha szeretné megtekinteni azt a fürtcsomópontot, amelyen a feladat fut, kattintson a **Nodes** (Csomópontok) fülre.

![A DC/OS webes felhasználói felülete – a feladat fürtcsomópontja](./media/container-service-mesos-marathon-ui/dcos9.png)

## <a name="reach-the-container"></a>A tároló elérése

Ebben a példában az alkalmazás fut egy nyilvános ügynök csomóponton. Keresse meg azt az ügynöknek a fürt eléri az alkalmazást az internetről: `http://[DNSPREFIX]agents.[REGION].cloudapp.azure.com`, ahol:

* a **DNSPREFIX** a fürt telepítésekor megadott DNS-előtag.
* a **REGION** az a régió, ahol az erőforráscsoport megtalálható.

    ![Internetről érkező Nginx](./media/container-service-mesos-marathon-ui/nginx.png)


## <a name="next-steps"></a>További lépések
* [A DC/OS és a Marathon API használata](container-service-mesos-marathon-rest.md)

* A Mesost használó Azure Container Service részletes bemutatása

    > [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON203/player]
    > 
