---
title: ELAVULT Azure DC/OS-fürt kezelése Marathon felhasználói felülettel
description: A cikk azt ismerteti, hogyan telepíthetők tárolók egy Azure tárolószolgáltatásba a Marathon webes felhasználói felület segítségével.
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 04/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: b251096915506c3c7a4eebf45b6a03e24779a3d8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "76277806"
---
# <a name="deprecated-manage-an-azure-container-service-dcos-cluster-through-the-marathon-web-ui"></a>ELAVULT Azure Container Service DC/OS-fürt kezelése a Marathon webes felhasználói felületén

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

A DC/OS biztosítja a fürtözött feladatok telepítését és skálázását lehetővé tevő környezetet, ugyanakkor absztrakciós rétegként működik a hardver fölött. A DC/OS fölötti keretrendszer gondoskodik a számítási feladatok ütemezéséről és végrehajtásáról.

Míg a keretrendszerek számos népszerű munkaterheléshez elérhetők, ez a dokumentum azt ismerteti, hogyan lehet megkezdeni a tárolók üzembe helyezését a Marathon használatával. 


## <a name="prerequisites"></a>Előfeltételek
A példákban szereplő feladatok elvégzéséhez szüksége lesz egy az Azure tárolószolgáltatásban konfigurált DC/OS-fürtre, valamint távoli kapcsolatot kell tudnia létesíteni a fürttel. Ezekkel az elemekkel kapcsolatban a következő cikkekben talál további tájékoztatást:

* [Azure  tárolószolgáltatás-fürt üzembe helyezése](container-service-deployment.md)
* [Csatlakozás Azure tárolószolgáltatási fürthöz](../container-service-connect.md)

> [!NOTE]
> Ez a cikk azt feltételezi, hogy a DC/OS-fürtöt a 80-as helyi porton keresztül bújtatással látja el.
>

## <a name="explore-the-dcos-ui"></a>A DC/OS felhasználói felületének megnyitása
Ha a rendszer [létrehoz](../container-service-connect.md)egy Secure Shell-(SSH-) alagutat, keresse meg a http: \/ /localhost/. Ez betölti a DC/OS webes felhasználói felületét, és a fürtre vonatkozó információkat jelenít meg, például a felhasznált erőforrásokat, az aktív ügynököket és a futó szolgáltatásokat.

![A DC/OS UI felhasználói felülete](./media/container-service-mesos-marathon-ui/dcos2.png)

## <a name="explore-the-marathon-ui"></a>A Marathon felhasználói felület megnyitása
A Marathon felhasználói felületének megtekintéséhez keresse fel a http: \/ /localhost/Marathon. Ezen a képernyőn elindíthat egy új tárolót vagy más szolgáltatást az Azure tárolószolgáltatási DC/OS-fürtön. A futó tárolókkal és alkalmazásokkal kapcsolatos információkat is láthat.  

![Marathon felhasználói felület](./media/container-service-mesos-marathon-ui/dcos3.png)

## <a name="deploy-a-docker-formatted-container"></a>Docker-formátumú tároló üzembe helyezése
Ha új tárolót szeretne üzembe helyezni a Marathon segítségével, kattintson a **Create Application** (Alkalmazás létrehozása) gombra, és adja meg a következő információkat az űrlapon:

| Mező | Érték |
| --- | --- |
| ID |nginx |
| Memory (Memória) | 32 |
| Kép |nginx |
| Network (Hálózat) |Bridged |
| Host Port (Gazdaport) |80 |
| Protokoll |TCP |

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

Amikor visszavált a DC/OS webes felhasználói felületére (http: \/ /localhost/), láthatja, hogy a DC/os-fürtön fut egy feladat (ebben az esetben egy Docker-formázott tároló).

![DC/OS webes felhasználói felülete – a fürtön futó feladat](./media/container-service-mesos-marathon-ui/dcos8.png)

Ha szeretné megtekinteni azt a fürtcsomópontot, amelyen a feladat fut, kattintson a **Nodes** (Csomópontok) fülre.

![A DC/OS webes felhasználói felülete – a feladat fürtcsomópontja](./media/container-service-mesos-marathon-ui/dcos9.png)

## <a name="reach-the-container"></a>A tároló elérése

Ebben a példában az alkalmazás egy nyilvános ügynök-csomóponton fut. Az alkalmazást az internetről érheti el, ha megkeresi a fürt teljes tartománynevét: `http://[DNSPREFIX]agents.[REGION].cloudapp.azure.com` , ahol:

* a **DNSPREFIX** a fürt telepítésekor megadott DNS-előtag.
* a **REGION** az a régió, ahol az erőforráscsoport megtalálható.

    ![Nginx az internetről](./media/container-service-mesos-marathon-ui/nginx.png)


## <a name="next-steps"></a>További lépések
* [A DC/OS és a Marathon API használata](container-service-mesos-marathon-rest.md)

* A Mesost használó Azure Container Service részletes bemutatása

    > [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON203/player]
    > 
