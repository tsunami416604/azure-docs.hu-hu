---
title: A Service Fabric és a tárolók áttekintése |} A Microsoft Docs
description: Mikroszolgáltatás-alkalmazások üzembe helyezése a Service Fabric és a tárolók használatának áttekintése. A cikk ismerteti, hogyan használhatók tárolók áttekintése és az elérhető képességek a Service Fabricben.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: c98b3fcb-c992-4dd9-b67d-2598a9bf8aab
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/8/2018
ms.author: msfussell
ms.openlocfilehash: 6715142be7f40955861afa634bf6e2472c9f7294
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005775"
---
# <a name="service-fabric-and-containers"></a>A Service Fabric és a tárolók

## <a name="introduction"></a>Bevezetés

Az Azure Service Fabric egy elosztott rendszerplatform, amely megkönnyíti a skálázható és megbízható mikroszolgáltatások és tárolók csomagolását, üzembe helyezését és kezelését.

Service Fabric a Microsoft [tárolóvezénylő](service-fabric-cluster-resource-manager-introduction.md) üzembe helyezésének mikroszolgáltatásokat Vezényel számítógépfürtökön. A Microsoft-szolgáltatásokat futtató nagy mennyiségben év során szerzett tapasztalatokat a Service Fabric előnyei.

A mikroszolgáltatások számos különféle módon fejleszthetőek a [Service Fabric programozási modellektől](service-fabric-choose-framework.md) és az [ASP.NET Core-tól](service-fabric-reliable-services-communication-aspnetcore.md) [tetszőleges kód üzembe helyezéséig](service-fabric-guest-executables-introduction.md). Vagy, ha csak át szeretné [tárolók telepítése és felügyelete](service-fabric-containers-overview.md), Service Fabric is, amely nagyszerű választás.

Alapértelmezés szerint a Service Fabric üzembe helyezi, és ezek a szolgáltatások, folyamatok aktiválja. Folyamatok adja meg, a leggyorsabb aktiválási és a legnagyobb kapacitású használatát egy fürtön. A Service Fabric is szolgáltatást is üzembe helyezhet tárolórendszerképeket a. A folyamatok szolgáltatások és szolgáltatások a tárolókban, ugyanabban az alkalmazásban is kombinálhatók.

Ugrani, és próbálja ki a Service Fabric tárolók, próbálja meg egy rövid, az oktatóanyag vagy minta:  

[Gyors útmutató: A Service Fabric egy Linux-alapú tárolóalkalmazás üzembe helyezése](service-fabric-quickstart-containers-linux.md)  
[Gyors útmutató: A Service Fabric Windows-tároló alkalmazás üzembe helyezése](service-fabric-quickstart-containers.md)  
[Meglévő .NET-alkalmazás tárolóalapúvá](service-fabric-host-app-in-a-container.md)  
[Service Fabric-tárolóminták](https://azure.microsoft.com/resources/samples/service-fabric-containers/)  

## <a name="what-are-containers"></a>Mik a tárolók

Tárolók megoldani a problémát futó alkalmazások megbízhatóan különböző számítástechnikai környezetekben azáltal, hogy az alkalmazás futtatása a környezet nem módosítható. Tárolók burkolhatja az alkalmazást és annak minden függőségét, kódtárak és konfigurációs fájlok, például szöveget a saját elkülönített' ", amely tartalmaz mindent, a tárolóban a szoftver futtatásához szükséges. A tárolóban fut, ahol az alkalmazás mindig azon belül mindent tartalmaz, például a függő kódtárak és konfigurációs fájlok bármi más futtatásához megfelelő verzióinak futtatásához.

A tárolók közvetlenül a kernel fut, és egy elkülönített nézetnek a fájlrendszer és egyéb erőforrásokat. Egy alkalmazás egy tárolóban nem rendelkezik más alkalmazások vagy a tárolón kívüli folyamatokat. Egyes alkalmazások és a futtatókörnyezet, a függőségeket és a rendszer könyvtárak futtassa a tárolókon belül a tároló saját elkülönített nézetet, az operációs rendszer teljes, privát hozzáférést. Amellett, hogy egyszerűen adja meg az összes futtatásához a különböző számítási környezeteket, az alkalmazás-függőségek biztonsági és erőforrás-elkülönítést is fontos előnyei a tárolók a Service Fabric – a szolgáltatásai milyen más módon fut egy a folyamat.

A virtuális gépekhez képest a tárolók rendelkezik a következő előnyökkel jár:

* **Kis**: tárolók használata egyetlen tárhely és a réteg verziók és a frissítés a hatékonyság növelése érdekében.
* **Gyors**: tárolók nem rendelkezik egy teljes operációs rendszer rendszerindító, így elindíthatják sokkal gyorsabban – általában másodpercek alatt.
* **Hordozhatóságot**: egy tárolóba helyezett alkalmazást rendszerképet is már futtathat a felhőben, a helyszínen, a virtuális gépeken belül, vagy közvetlenül a fizikai gépek.
* **Erőforrás-szabályozás**: tároló korlátozhatja a gazdagépen lefoglalhatja a fizikai erőforrásokat.

### <a name="container-types-and-supported-environments"></a>Tároló-típusok és a támogatott környezetek

Service Fabric támogatja a tárolók a Linux és a Windows és a Hyper-V elkülönítési módban támogatja a Windows.

#### <a name="docker-containers-on-linux"></a>Docker-tárolók linuxon

Docker API-kat hozhat létre és felügyelhet tárolókat épülő Linux-kernel tárolókat biztosít. A docker Hub biztosít egy központi tárházban tárolására és beolvasására a tárolórendszerképeket.
Linux-alapú oktatóanyagért lásd: [az első Service Fabric-tárolóalkalmazás létrehozása linuxon](service-fabric-get-started-containers-linux.md).

#### <a name="windows-server-containers"></a>A Windows Server-tárolók

A Windows Server 2016-ban két különböző típusú tárolók két elkülönítési szintet biztosít. A Windows Server-tárolók és a Docker-tárolók hasonlóak, mert is rendelkezik a névtér és a fájl rendszer elkülönítést, a kernel osztanak meg a gazdagép, amelyen futnak. Linuxon Ez az elkülönítés hagyományosan megadott cgroupokban és a névterek, és a Windows Server-tárolók hasonlóan viselkednek.

Hyper-V-támogatással rendelkező Windows-tárolók további elkülönítési és biztonsági adja meg, mert nincs tároló megosztja az operációs rendszer kernelén bármilyen másik tároló vagy a gazdagép. Ez magasabb szintű biztonsági elkülönítés engedélyezve van a Hyper-V-tárolók esetleg rosszindulatú, több-bérlős forgatókönyvek célozzák meg.
Windows-alapú oktatóanyagért lásd: [az első Service Fabric-tárolóalkalmazás létrehozása Windows](service-fabric-get-started-containers.md).

A következő ábrán látható a különböző típusú virtualizálás és elkülönítési szinten érhető el.
![Service Fabric platformot][Image1]

## <a name="scenarios-for-using-containers"></a>Tárolók használatára vonatkozó forgatókönyvek

Az alábbiakban tipikus példák egy tároló esetén a jó választás:

* **IIS lift- and -shift**: egy meglévő helyezheti [ASP.NET MVC](https://www.asp.net/mvc) alkalmazás áttelepítése helyett egy tárolóban, az ASP.NET Core. Az ASP.NET MVC-alkalmazások attól függnek, hogy az Internet Information Services (IIS). Az alkalmazások a precreated IIS rendszerkép tárolórendszerképekbe csomagot, és a Service Fabric segítségével telepíthet. Lásd: [Tárolórendszerképeket a Windows Server](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-server) Windows-tárolókkal kapcsolatos információkat.

* **Tárolók és mikroszolgáltatások Service Fabric vegyesen**: egy meglévő tároló-rendszerkép használata a alkalmazás részeként. Például előfordulhat, hogy használja a [NGINX-tároló](https://hub.docker.com/_/nginx/) a webes kezelőfelület az alkalmazás és a háttér-intenzívebb számítási az állapotalapú szolgáltatások esetében.

* **A "zajos szomszédok" szolgáltatások gyakorolt hatást**: a tárolók erőforrás cégirányítási képessége segítségével korlátozhatja az erőforrások, amelyeket a szolgáltatás használ egy gazdagépen. Ha a szolgáltatások előfordulhat, hogy sok erőforrást igényelnek, és teljesítményét befolyásoló mások (például egy hosszan futó, lekérdezés-szerű művelet), fontolja meg, ezek a szolgáltatások üzembe helyezése erőforrás-szabályozás rendelkező tárolókat.

## <a name="service-fabric-support-for-containers"></a>A Service Fabric-tárolók támogatása

A Service Fabric Docker-tárolókat, Linux és Windows Server-tárolók telepítését támogatja a Windows Server 2016 esetében támogatja a Hyper-V elkülönítési módban. 

A Service Fabric biztosít egy [alkalmazásmodell](service-fabric-application-model.md) található, amely egy tároló jelöli egy alkalmazásgazda, mely több szolgáltatásban replikák kerülnek. A Service Fabric emellett támogatja a [Vendég végrehajtható forgatókönyv](service-fabric-guest-executables-introduction.md) amely, ne használja a beépített Service Fabric programozási modelleket, de ehelyett csomag belül bármilyen nyelvet vagy keretrendszert, használatával írt, meglévő alkalmazást egy a tároló. Ebben a forgatókönyvben a gyakori használati tárolókhoz.

Futtathat [Service Fabric-szolgáltatások a tárolókon belül](service-fabric-services-inside-containers.md). Tárolókon belül futó Service Fabric-szolgáltatások támogatása jelenleg korlátozódik.

A Service Fabric biztosít, amelyekkel különböző lehetőségek alkalmazások összeállítását, amelyek tárolóalapú mikroszolgáltatások például alkotják:

* Tároló-lemezkép központi telepítéséhez és az aktiválás.
* Erőforrás-szabályozás beállítása a erőforrás értékek alapértelmezés szerint az Azure-fürtökön.
* Adattár-hitelesítést.
* Port kontejneru host port hozzárendelését.
* Tároló – tároló felderítés és a kommunikáció.
* Lehetővé teszi konfigurálása és a környezeti változókat.
* Lehetőség biztonsági hitelesítő adatok beállítása a tárolón.
* Tárolók hálózati mód közül választhat.

Átfogó tájékoztatást tároló támogatja az Azure-ban, például a Kubernetes-fürt létrehozása az Azure Kubernetes Service szolgáltatással, hogyan privát Docker-tárolójegyzék létrehozása az Azure Container Registrybe, és további: [Azure tárolókhoz](https://docs.microsoft.com/azure/containers/).

## <a name="next-steps"></a>További lépések

Ebben a cikkben megismerkedett a Service Fabric biztosít a futó tárolók támogatása. Ezután fogja meg is vagyunk példák bemutatják, hogyan használhatja azokat a szolgáltatásokat az egyes keresztül.

[Az első Service Fabric-tárolóalkalmazás létrehozása linuxon](service-fabric-get-started-containers-linux.md)  
[Az első Service Fabric-tárolóalkalmazás létrehozása Windows](service-fabric-get-started-containers.md)  
[További tudnivalók a Windows-tárolók](https://docs.microsoft.com/virtualization/windowscontainers/about/)

[Image1]: media/service-fabric-containers/Service-Fabric-Types-of-Isolation.png