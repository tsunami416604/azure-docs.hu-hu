---
title: A Service Fabric és a tárolók áttekintése
description: A Service Fabric áttekintése és a mikroszolgáltatási alkalmazások üzembe helyezéséhez használt tárolók. Ez a cikk áttekintést nyújt a tárolók és a Service Fabric elérhető képességeinek áttekintését.
ms.topic: conceptual
ms.date: 8/8/2018
ms.openlocfilehash: 884cefa3d6a60f55269afac73c40b9f6b21518f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458222"
---
# <a name="service-fabric-and-containers"></a>Szolgáltatás fabric és konténerek

## <a name="introduction"></a>Bevezetés

Az Azure Service Fabric egy elosztott rendszerplatform, amely megkönnyíti a skálázható és megbízható mikroszolgáltatások és tárolók csomagolását, üzembe helyezését és kezelését.

A Service Fabric a Microsoft [tárolóvongontartója](service-fabric-cluster-resource-manager-introduction.md) a mikroszolgáltatások gépek fürtje közötti üzembe helyezéséhez. A Service Fabric a Microsoft nál a szolgáltatások tömeges futtatásával töltött évek során levont tanulságok előnyeit élvezi.

A mikroszolgáltatások számos különféle módon fejleszthetőek a [Service Fabric programozási modellektől](service-fabric-choose-framework.md) és az [ASP.NET Core-tól](service-fabric-reliable-services-communication-aspnetcore.md)[tetszőleges kód üzembe helyezéséig](service-fabric-guest-executables-introduction.md). Vagy ha csak telepíteni és kezelni szeretné a [tárolókat,](service-fabric-containers-overview.md)a Service Fabric is nagyszerű választás.

Alapértelmezés szerint a Service Fabric telepíti és aktiválja ezeket a szolgáltatásokat folyamatokként. A folyamatok biztosítják a fürt erőforrásainak leggyorsabb aktiválását és legnagyobb sűrűségű használatát. A Service Fabric is üzembe helyezheti a szolgáltatásokat a tárolórendszerképeken. A szolgáltatások a folyamatokban és a szolgáltatások tárolókban, ugyanabban az alkalmazásban is keverheti.

Ha azonnal be szeretne ugrani, és ki szeretné próbálni a tárolókat a Service Fabric-en, próbálkozzon egy rövid útmutatóval, oktatóanyaggal vagy mintával:  

[Rövid útmutató: Linux-tárolóalkalmazás üzembe helyezése a Service Fabric ben](service-fabric-quickstart-containers-linux.md)  
[Rövid útmutató: Windows-tárolóalkalmazás telepítése a Service Fabric szolgáltatásba](service-fabric-quickstart-containers.md)  
[Meglévő .NET-alkalmazás tárolóba helyezése](service-fabric-host-app-in-a-container.md)  
[Service Fabric-tárolóminták](https://azure.microsoft.com/resources/samples/service-fabric-containers/)  

## <a name="what-are-containers"></a>Mik azok a konténerek

A tárolók úgy oldják meg az alkalmazások megbízható futtatásának problémáját a különböző számítástechnikai környezetekben, hogy nem módosítható környezetet biztosítanak az alkalmazás futtatásához. A tárolók egy alkalmazást és annak összes függőségét, például a könyvtárakat és a konfigurációs fájlokat saját elkülönített "dobozába" csomagolják, amely tartalmazza a szoftver tárolón belüli futtatásához szükséges összes tartalmat. Bárhol is fut a tároló, a benne lévő alkalmazás mindig mindent tartalmaz, amelyre szüksége van, például a függő kódtárak megfelelő verzióival, a konfigurációs fájlokkal és minden mással, amelyre szüksége van.

A tárolók közvetlenül a kernel tetején futnak, és elkülönített nézetben rendelkeznek a fájlrendszerről és más erőforrásokról. A tárolóban lévő alkalmazások nem ismerik a tárolón kívüli más alkalmazásokat vagy folyamatokat. Minden alkalmazás és a futásidejű, függőségek és rendszerkönyvtárak egy tárolóban futnak, amely teljes körű, privát hozzáférést biztosít a tároló operációs rendszer saját elkülönített nézetéhez. Amellett, hogy megkönnyíti az alkalmazás függőségeinek biztosítását, amelyeket különböző számítástechnikai környezetekben kell futtatnia, a biztonság és az erőforrások elkülönítése fontos előnyöket jelent a tárolók Service Fabric használatával , amely egyébként szolgáltatásokat futtat egy Folyamat.

A virtuális gépekkel összehasonlítva a tárolók a következő előnyökkel rendelkeznek:

* **Kis**: A tárolók egyetlen tárolóhelyet és rétegverziót és frissítéseket használnak a hatékonyság növelése érdekében.
* **Gyors:** A tárolóknak nem kell egy teljes operációs rendszert indítaniuk, így sokkal gyorsabban indulhatnak el – általában másodpercek alatt.
* **Hordozhatóság:** A tárolóba épített alkalmazásrendszerkép a felhőben, a helyszínen, a virtuális gépeken vagy közvetlenül a fizikai gépeken való futtatásra is hordozható.
* **Erőforrás-szabályozás:** A tároló korlátozhatja a fizikai erőforrásokat, amelyeket felhasználhat a gazdagépen.

### <a name="container-types-and-supported-environments"></a>Tárolótípusok és támogatott környezetek

A Service Fabric támogatja a linuxos és a windowsos tárolókat, és támogatja a Hyper-V elkülönítési módot a Windows rendszeren.

#### <a name="docker-containers-on-linux"></a>Docker-tárolók Linuxon

A Docker API-kat biztosít a Linux kerneltárolókon lévő tárolók létrehozásához és kezeléséhez. A Docker Hub egy központi tárházat biztosít a tárolórendszerképek tárolásához és lekéréséhez.
Linux-alapú oktatóanyagról az [Első Service Fabric-tárolóalkalmazás létrehozása Linux on című témakörben](service-fabric-get-started-containers-linux.md)található.

#### <a name="windows-server-containers"></a>Windows Server-tárolók

A Windows Server 2016 két különböző típusú tárolót kínál, amelyek elkülönítési szintszerint különböznek. A Windows Server-tárolók és a Docker-tárolók hasonlóak, mivel mindkettő névtér- és fájlrendszer-elkülönítéssel rendelkezik, miközben megosztja a rendszermagot azzal az állomással, amelyen futnak. Linuxon ezt az elkülönítést hagyományosan cgroups és névterek biztosítják, és a Windows Server-tárolók hasonlóan viselkednek.

A Hyper-V támogatással rendelkező Windows-tárolók nagyobb elkülönítést és biztonságot nyújtanak, mivel egyetlen tároló sem osztja meg az operációs rendszer kernelét más tárolóval vagy az állomással. Ezzel a magasabb szintű biztonsági elkülönítéssel a Hyper-V-kompatibilis tárolók potenciálisan ellenséges, több-bérlős forgatókönyveket céloznak meg.
Windows-alapú oktatóanyag: [Az első Service Fabric-tárolóalkalmazás létrehozása Windows rendszeren](service-fabric-get-started-containers.md)című témakörben.

Az alábbi ábra a rendelkezésre álló virtualizációs és elkülönítési szintek különböző típusait mutatja be.
![Service Fabric platform][Image1]

## <a name="scenarios-for-using-containers"></a>Tárolók használatának forgatókönyvei

Íme néhány tipikus példa arra, hogy egy tároló jó választás:

* **IIS-emelés és -váltás:** Egy meglévő [ASP.NET MVC](https://www.asp.net/mvc) alkalmazást helyezhet el egy tárolóban, ahelyett, hogy áttelepülne ASP.NET Core-ba. Ezek ASP.NET MVC-alkalmazások az Internet Information Services (IIS) szolgáltatástól függenek. Ezeket az alkalmazásokat az előre létrehozott IIS-lemezképből tárolórendszerképekbe csomagolhatja, és üzembe helyezheti őket a Service Fabric segítségével. A Windows-tárolókról a Windows-tárolókról a [Tárolóképek a Windows Server rendszeren](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-server) című témakörben talál információt.

* **Tárolók és a Service Fabric mikroszolgáltatások keverése:** Az alkalmazás egy részéhez használjon egy meglévő tárolórendszerképet. Például használhatja az [NGINX-tárolót](https://hub.docker.com/_/nginx/) az alkalmazás webes előtér-kiszolgálójának és az állapotalapú szolgáltatásoknak az intenzívebb háttér-számításhoz.

* **A "zajos szomszédok" szolgáltatások hatásának csökkentése:** A tárolók erőforrás-irányítási képességével korlátozhatja a szolgáltatás által a gazdagépen használt erőforrásokat. Ha a szolgáltatások sok erőforrást használnak fel, és hatással vannak mások teljesítményére (például egy hosszú ideig futó, lekérdezésszerű művelet), fontolja meg, hogy ezeket a szolgáltatásokat erőforrás-irányítással ellátott tárolókba helyezi.

## <a name="service-fabric-support-for-containers"></a>Service Fabric-támogatás tárolókhoz

A Service Fabric támogatja a Docker-tárolók linuxos és Windows Server-tárolók telepítését A Windows Server 2016 rendszeren, valamint a Hyper-V elkülönítési mód támogatását. 

A Service Fabric egy [alkalmazásmodellt](service-fabric-application-model.md) biztosít, amelyben egy tároló egy olyan alkalmazásgazdat jelöl, amelyben több szolgáltatásreplikák vannak elhelyezve. A Service Fabric is támogatja a [vendég végrehajtható forgatókönyv,](service-fabric-guest-executables-introduction.md) amelyben nem használja a beépített Service Fabric programozási modellek, hanem a csomag egy meglévő alkalmazás, bármilyen nyelven vagy keretrendszerhasználatával írt, egy tárolóban. Ez a forgatókönyv a tárolók gyakori használati esete.

A Service [Fabric-szolgáltatások egy tárolón belül](service-fabric-services-inside-containers.md)is futtathatók. A Service Fabric-szolgáltatások tárolókban való futtatásának támogatása jelenleg korlátozott.

A Service Fabric számos tárolóképességet biztosít, amelyek segítségével tárolóba épített mikroszolgáltatásokból álló alkalmazásokat hozhat létre, például:

* Tárolórendszerkép telepítése és aktiválása.
* Erőforrás-szabályozás, beleértve az erőforrás-értékek beállítása alapértelmezés szerint az Azure-fürtök.
* Tárház hitelesítése.
* Tárolóport a gazdaport-hozzárendeléshez.
* Tárolók közötti felderítés és kommunikáció.
* Környezeti változók konfigurálása és beállítása.
* A biztonsági hitelesítő adatok beállítása a tárolón.
* Különböző hálózati módok a tárolókhoz.

Az Azure-beli tárolók támogatásának átfogó áttekintését, például a Kubernetes-fürt azure Kubernetes-szolgáltatással való létrehozását, a privát Docker-beállításjegyzék létrehozásának módját az Azure Container Registry alkalmazásban, és egyebek az [Azure for Containers](https://docs.microsoft.com/azure/containers/)című témakört.

## <a name="next-steps"></a>További lépések

Ebben a cikkben a Service Fabric támogatja a tárolók futtatását. Ezután átmegyünk az egyes funkciók példáira, hogy megmutassuk, hogyan kell használni őket.

[Az első Service Fabric-tárolóalkalmazás létrehozása Linux rendszeren](service-fabric-get-started-containers-linux.md)  
[Az első Service Fabric-tárolóalkalmazás létrehozása Windows rendszeren](service-fabric-get-started-containers.md)  
[További információ a Windows-tárolókról](https://docs.microsoft.com/virtualization/windowscontainers/about/)

[Image1]: media/service-fabric-containers/Service-Fabric-Types-of-Isolation.png