---
title: "Az Azure tárolószolgáltatás tárolóinak kezelése a webes felhasználói felületen | Microsoft Docs"
description: "A cikk azt ismerteti, hogyan telepíthetők tárolók egy Azure tárolószolgáltatásba a Marathon webes felhasználói felület segítségével."
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, tárolók, mikroszolgáltatások, Mesos, Azure"
ms.assetid: d148ed1e-b582-4d51-944f-1ac7ae3c4fd6
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2016
ms.author: timlt
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ae2945b0025da3bc933a1e4a8f10f21fd35cb51c


---
# <a name="container-management-through-the-web-ui"></a>Tárolókezelés a webes felhasználói felületen
A DC/OS biztosítja a fürtözött feladatok telepítését és skálázását lehetővé tevő környezetet, ugyanakkor absztrakciós rétegként működik a hardver fölött. A DC/OS fölötti keretrendszer gondoskodik a számítási feladatok ütemezéséről és végrehajtásáról.

Számos népszerű alkalmazáshoz és szolgáltatáshoz elérhetők keretrendszerek, ez a dokumentum azonban azt ismerteti, hogyan lehet üzemelő tárolópéldányokat létrehozni, illetve skálázni őket a Marathon segítségével. Mielőtt végighalad a példákon, szüksége van egy az Azure tárolószolgáltatásban konfigurált DC/OS-fürtre. Ezen kívül távoli kapcsolatot kell tudnia létesíteni ezzel a fürttel. Ezekkel az elemekkel kapcsolatban a következő cikkekben talál további tájékoztatást:

* [Az Azure Container Service-fürt üzembe helyezése](container-service-deployment.md)
* [Csatlakozás Azure Container Service-fürthöz](container-service-connect.md)

## <a name="explore-the-dcos-ui"></a>A DC/OS felhasználói felületének megnyitása
Alakítson ki  Secure Shell-alagutat (SSH-alagutat), és nyissa meg böngészőben a http://localhost/ címet. Ez betölti a DC/OS webes felhasználói felületét, és a fürtre vonatkozó információkat jelenít meg, például a felhasznált erőforrásokat, az aktív ügynököket és a futó szolgáltatásokat.

![A DC/OS UI felhasználói felülete](media/dcos/dcos2.png)

## <a name="explore-the-marathon-ui"></a>A Marathon felhasználói felület megnyitása
A Marathon felhasználói felületet a http://localhost/Marathon URL-cím megnyitásával jelenítheti meg. Ezen a képernyőn elindíthat egy új tárolót vagy más szolgáltatást az Azure tárolószolgáltatási DC/OS-fürtön. A futó tárolókkal és alkalmazásokkal kapcsolatos információkat is láthat.  

![Marathon felhasználói felület](media/dcos/dcos3.png)

## <a name="deploy-a-dockerformatted-container"></a>Docker-formázású tároló üzembe helyezése
Ha új tárolót kíván üzembe helyezni a Marathon segítségével, kattintson a **Create Application** (Alkalmazás létrehozása) gombra, és adja meg a következő információkat az űrlapon:

| Mező | Érték |
| --- | --- |
| ID (Azonosító) |nginx |
| Image (Kép) |nginx |
| Network (Hálózat) |Bridged |
| Host Port (Gazdaport) |80 |
| Protocol (Protokoll) |TCP |

![New Application (Új alkalmazás) felhasználói felület – General (Általános)](media/dcos/dcos4.png)

![New Application (Új alkalmazás) felhasználói felület – Docker Container (Docker-tároló)](media/dcos/dcos5.png)

![New Application (Új alkalmazás) felhasználói felület – Ports and Service Discovery (Portok és szolgáltatások felderítése)](media/dcos/dcos6.png)

Ha statikusan le kívánja képezni a tárolóportot egy az ügynökön lévő portra, akkor a JSON üzemmódot kell használnia. Ehhez váltsa át a New Application (Új alkalmazás) varázslót erre az üzemmódra a **JSON Mode** (JSON üzemmód) váltógombbal. Ezután írja be a következőt az alkalmazás definíciójának `portMappings` szakaszába. A példa a tároló 80-as portját a DC/OS-ügynök 80-as portjához köti. Ennek a módosításnak az elvégzése után visszaválthat a varázsló JSON üzemmódjából.

```none
"hostPort": 80,
```

![New Application (Új alkalmazás) felhasználói felület – példa a 80-as portra](media/dcos/dcos13.png)

A DC/OS-fürt üzembe helyezésekor különféle privát és nyilvános ügynökök is települnek. Ahhoz, hogy a fürt elérhessen alkalmazásokat az internetről, nyilvános ügynökre kell telepíteni az alkalmazást. Ehhez válassza a New Application (Új alkalmazás) varázsló **Optional** (Választható) lapfülét, és írja be a **slave_public** értéket az **Accepted Resource Roles** (Elfogadott erőforrás-szerepkörök) mezőbe.

![New Application (Új alkalmazás) felhasználói felület – nyilvános ügynök beállítása](media/dcos/dcos14.png)

A Marathon főoldalára visszatérve láthatja a tároló üzembe helyezési állapotát.

![A Marathon főoldala – a tároló üzembe helyezési állapota](media/dcos/dcos7.png)

Amikor visszavált a DC/OS webes felhasználói felületére (http://localhost/), láthatja, hogy a DC/OS-fürtön fut egy feladat (ez esetben egy Docker-formázású tároló).

![DC/OS webes felhasználói felülete – a fürtön futó feladat](media/dcos/dcos8.png)

Azt is láthatja, hogy a fürt melyik csomópontján fut a feladat.

![A DC/OS webes felhasználói felülete – a feladat fürtcsomópontja](media/dcos/dcos9.png)

## <a name="scale-your-containers"></a>A tárolók skálázása
A Marathon felhasználói felület segítségével módosítható a tárolók példányainak száma. Ehhez navigáljon a **Marathon** lapra, jelölje be a skálázni kívánt tárolót, és kattintson a **Scale** (Skálázás) gombra. Adja meg a **Scale Application** (Alkalmazás skálázása) párbeszédpanelen, hogy hány tárolópéldányt szeretne, majd válassza a **Scale Application** (Alkalmazás skálázása) gombot.

![Marathon felhasználói felület – Scale Application (Alkalmazás skálázása) párbeszédpanel](media/dcos/dcos10.png)

A skálázási művelet befejezése után több példányt fog látni ugyanabból a feladatból, elosztva a DC/OS-ügynökök között.

![A DC/OS webes felhasználói felületének irányítópultja – több ügynök között elosztott feladat](media/dcos/dcos11.png)

![A DC/OS webes felhasználói felülete – csomópontok](media/dcos/dcos12.png)

## <a name="next-steps"></a>Következő lépések
* [A DC/OS és a Marathon API használata](container-service-mesos-marathon-rest.md)

A Mesost használó Azure Container Service részletes bemutatása

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON203/player]
> 
> 




<!--HONumber=Nov16_HO2-->


