---
title: "A Service Fabric és a tárolók áttekintése |} Microsoft Docs"
description: "Mikroszolgáltatási alkalmazások központi telepítése a Service Fabric és tárolók használatának áttekintése. Ez a cikk ismerteti, hogyan használható a tárolók áttekintése és a Service Fabric elérhető képességek."
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: c98b3fcb-c992-4dd9-b67d-2598a9bf8aab
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 9/20/2017
ms.author: msfussell
ms.openlocfilehash: 412107db2dc446eb5a6a433bfb7fc3bc5e760c27
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/13/2018
---
# <a name="service-fabric-and-containers"></a>A Service Fabric és a tárolók
> [!NOTE]
> Tárolók telepítése a Windows 10- vagy Docker CE Service Fabric-fürt nem támogatott. 
>   

> [!NOTE]
> A Service Fabric 6.1-es verziójú Windows Server verzió 1709 előzetes támogatása rendelkezik. Nyissa meg a hálózat és a Service Fabric DNS-szolgáltatás nem működnek a Windows Server verzió 1709. 
> 

## <a name="introduction"></a>Bevezetés
Az Azure Service fabric egy [orchestrator](service-fabric-cluster-resource-manager-introduction.md) szolgáltatások gépet fürtön belül, használatát és az optimalizálást a jelentős mértékű évnyi szolgáltatásokat, amely Microsoft. Szolgáltatások használatával számos módon fejleszthetők a [programozási modellek Service Fabric](service-fabric-choose-framework.md) telepítésével [Vendég végrehajtható fájlok](service-fabric-deploy-existing-app.md). Alapértelmezés szerint a Service Fabric telepíti, és ezek a szolgáltatások, folyamatok aktiválja. Folyamatok adja meg a leggyorsabb aktiválás és a legmagasabb sűrűség használatát egy fürtön. A Service Fabric szolgáltatások tároló képek is telepítheti. Fontos kombinálhatja a folyamatokat a szolgáltatások és szolgáltatások tárolókban ugyanabban az alkalmazásban.   

## <a name="what-are-containers"></a>Mik azok a tárolók?
Tárolók összetevői beágyazott, külön-külön telepíthető, amely a azonos kernel előnyeit, amely az operációs rendszer biztosít a virtualizálási elszigetelt példányai futtassa. Így minden alkalmazás és a futtatókörnyezet, a függőségeket és a rendszer tárak futhat egy tároló teljes, privát hozzáférést az operációs rendszer megoldások tároló saját elkülönített nézethez. Hordozhatósága, valamint biztonsági és erőforrás-elkülönítést az ilyen mértékű tárolók használata a Service Fabric, amelyek egyébként szolgáltatást futtat folyamatokat a fő juttatása.

Tárolók egy virtualizációs technológia, amely Virtualizálja az alapul szolgáló operációs rendszer alkalmazások. Tárolók különböző mértékben elkülönítési futó alkalmazások nem módosítható környezetet biztosíthat. Tárolók közvetlenül a kernel fut, és a fájlrendszerben és egyéb erőforrások elkülönített nézetet. Virtuális gépek képest, tárolók rendelkezik a következő előnyökkel jár:

* **Kis**: tárolók használata egyetlen tárolóhely és a réteg verziója és a frissítések a hatékonyság növelése érdekében.
* **Gyors**: tárolók nem rendelkezik teljes operációs rendszert, rendszerindító, így indításához sokkal gyorsabb, általában másodpercben.
* **Hordozhatósága**: egy indexelése alkalmazás-lemezképet is tartalomfájlokat a helyszíni virtuális gépeken belül, vagy közvetlenül a fizikai gép a felhőben való futtatásra.
* **Erőforrás-irányítás**: A tárolók korlátozhatja a fizikai erőforrásokat vehet igénybe, a gazdagépen.

## <a name="container-types-and-supported-environments"></a>Támogatott környezetek és a tároló esetében
A Service Fabric tárolók támogatja a Linux és a Windows, és is támogatja az utóbbi a Hyper-V elkülönítési üzemmódját. 

> [!NOTE]
> A Service Fabric-fürt Windows 10-tárolók telepítése jelenleg nem támogatott. 
> 

### <a name="docker-containers-on-linux"></a>Linux docker-tároló
Docker biztosít magas szintű API-k létrehozásához és kezeléséhez tárolók Linux kernel tárolók felett. Docker egy központi tárházban tárolásához és lekéréséhez tároló képek is.
Az oktatóanyagok esetén lásd: [egy Docker-tároló telepíteni a Service Fabric](service-fabric-get-started-containers-linux.md).

### <a name="windows-server-containers"></a>Windows Server-tárolók
Windows Server 2016 biztosít két különböző típusú tárolókat, amely nem egyezik a megadott elkülönítési szintjét. Windows Server-tárolók és a Docker-tárolók hasonlítanak, mivel mindkét rendszer elkülönítési névtér és a fájl rendelkezik, de a kernel megosztása gazdagépen futnak a gazdagép. Linux, elkülönítés hagyományosan által szolgáltatott `cgroups` és `namespaces`, és a Windows Server-tárolók hasonlóan viselkednek.

Hyper-támogatással rendelkező Windows-tárolók további elkülönítési és biztonsági adja meg, mert minden egyes tároló nem osztja meg az operációs rendszer kernel más tárolók vagy a gazdagépen. A magasabb szintű biztonsági elkülönítés engedélyezve van a Hyper-V tárolók rosszindulatú, több-bérlős forgatókönyvek célozzák.
Az oktatóanyagok esetén lásd: [egy Windows-tároló üzembe a Service Fabric](service-fabric-get-started-containers.md).

Az alábbi ábrán látható a különböző típusú virtualizálás és az elkülönítési szint érhető el az operációs rendszerben.
![Service Fabric-platformról][Image1]

## <a name="scenarios-for-using-containers"></a>Tárolók használatára vonatkozó forgatókönyvek
Íme jellemző példa arra, ahol a tároló, akkor a:

* **IIS növekedési, és az eltolás mértékét megadó**: Ha rendelkezik meglévő [ASP.NET MVC](https://www.asp.net/mvc) továbbra is használhatja, kívánt alkalmazások helyezze őket egy tároló áttelepítése helyett az ASP.NET Core azokat. ASP.NET MVC alkalmazások az Internet Information Services (IIS) határozza meg. Ezeket az alkalmazásokat a tároló képek a precreated IIS lemezképből csomag, és a Service Fabric telepíteni őket. Lásd: [tároló lemezképek a Windows Server](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-server) Windows tárolók kapcsolatos információkat.
* **Tárolók és a Service Fabric mikroszolgáltatások**: meglévő tároló lemezképet használja az alkalmazás a része. Használhatja például a [NGINX tároló](https://hub.docker.com/_/nginx/) a webalkalmazás első végén az alkalmazás- és állapot-nyilvántartó a intenzívebb háttér-számításhoz.
* **"Zajos szomszédok" szolgáltatások hatásának csökkentéséhez**: tárolók erőforrás irányítás képességét segítségével korlátozni az erőforrások, a szolgáltatás által használt egy gazdagépen. Ha szolgáltatások előfordulhat, hogy sok erőforrást és teljesítményét befolyásoló mások (például egy hosszan futó, a lekérdezés-szerű művelet), fontolja meg, ezek a szolgáltatások üzembe erőforrás irányítás tárolókat.

## <a name="service-fabric-support-for-containers"></a>A Service Fabric tárolók támogatása
A Service Fabric a Docker-tároló központi telepítését támogatja a Linux és Windows Server-tárolókra vonatkozóan a Windows Server 2016 működési feltételeit, támogatja a Hyper-V elkülönítési üzemmódját. 

A Service Fabric a [alkalmazásmodell](service-fabric-application-model.md), a tároló jelöli egy alkalmazásgazda, mely több szolgáltatásban replikák kerülnek. A Service Fabric tárolókkal futtathatja, és a forgatókönyv hasonlít a [Vendég végrehajtható forgatókönyv](service-fabric-deploy-existing-app.md), ahol a becsomagolt belül egy tárolót egy meglévő alkalmazást. Ebben a forgatókönyvben a gyakori használati eset az tárolókat, és példák bármilyen nyelvet vagy keretrendszert használja, de nem használ a beépített Service Fabric programozási modellek írása egy alkalmazást futtat.

Ezenkívül futtatása [Service Fabric szolgáltatások tárolókba](service-fabric-services-inside-containers.md) is. Tárolók belül futó Service Fabric-szolgáltatások támogatása jelenleg korlátozott, és a jövőbeli kiadásokban javítani kell.

A Service Fabric van több tárolóhoz képességeket, amelyek segítenek a mikroszolgáltatások létrehozására, amelyek indexelése épülnek alkalmazásokat hozhatnak létre. A Service Fabric indexelése szolgáltatások a következő szolgáltatásokat biztosítja:

* Tároló lemezkép-telepítés és az aktiválás.
* Erőforrás irányítás, beleértve a beállítás erőforrás értéke alapértelmezés szerint az Azure-fürtökön.
* Tárház-hitelesítés.
* Host port hozzárendelését tároló portot.
* Tároló-tároló felderítése és a kommunikáció.
* Konfigurálja, és állítsa be a környezeti változók lehetősége.
* Lehetővé teszi biztonsági hitelesítő adatok beállítása a tárolón.
* A tárolók hálózati mód választható.

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, tárolók, kapcsolatban, hogy a Service Fabric egy tárolót az orchestrator, és a Service Fabric tárolókat támogató szolgáltatások. A következő lépésben azt ismerteti példák az egyes mutatjuk be, hogyan használhatja ezeket a szolgáltatásokat.

[Az első Service Fabric tároló alkalmazás létrehozása Windows rendszeren](service-fabric-get-started-containers.md)

[Az első Service Fabric-tároló alkalmazás létrehozása Linux rendszeren](service-fabric-get-started-containers-linux.md)

[További információ a Windows-tárolók](https://docs.microsoft.com/virtualization/windowscontainers/about/)

[Image1]: media/service-fabric-containers/Service-Fabric-Types-of-Isolation.png
