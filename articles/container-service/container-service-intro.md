---
title: "Docker-tárolófürtök az Azure-felhőben | Microsoft Docs"
description: "Az Azure Container Service lehetőséget biztosít arra, hogy egyszerűbben lehessen létrehozni, konfigurálni és kezelni a virtuális gépeknek egy olyan fürtjét, amely tárolóalapú alkalmazások futtatására lett konfigurálva."
services: container-service
documentationcenter: 
author: rgardler
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, tárolók, mikroszolgáltatások, Mesos, Azure"
ms.assetid: 52da4163-1182-4b2e-be00-4951e5c1da16
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2017
ms.author: rogardle
translationtype: Human Translation
ms.sourcegitcommit: cc3cc2dce324942f184b6a520dc4db28518a3091
ms.openlocfilehash: 0521fbd689233c0f2359a10006e24c75c8997732


---
# <a name="azure-container-service-introduction"></a>Az Azure Container Service bemutatása
Az Azure Container Service elősegíti azt, hogy egyszerűbben lehessen létrehozni, konfigurálni és kezelni a virtuális gépeknek egy olyan fürtjét, amely tárolóalapú alkalmazások futtatására lett konfigurálva. Ezt nyílt forráskódú ütemezési és vezénylési eszközök optimalizált konfigurációját igénybe véve teszi lehetővé. Így lehetővé válik, hogy a meglévő ismereteit használja, vagy egy nagy és egyre növekvő közösségi szakértelmet vegyen igénybe a tárolóalapú alkalmazások Microsoft Azure-on történő üzembe helyezéséhez és kezeléséhez.

![Az Azure Container Service lehetővé teszi, hogy az Azure-on több gazdagépen kezelhesse a tárolóalapú alkalmazásokat.](./media/acs-intro/acs-cluster-new.png)

Az Azure Container Service a Docker tárolóformátumot használja, így biztosítva az alkalmazástárolók hordozhatóságát. Ezenkívül támogatja a Marathon, a DC/OS, a Docker Swarm és a Kubernetes használatát, így tárolók ezreire vagy akár tízezreire is méretezheti ezeket az alkalmazásokat.

>[!NOTE]
> A Kubernetes támogatása az Azure Container Service-ben jelenleg előzetes verzióban van.
>

Az Azure Container Service használatával igénybe veheti az Azure nagyvállalati szintű funkcióit, miközben továbbra is fenntartja az alkalmazás-hordozhatóságot, beleértve a vezénylési rétegek hordozhatóságát is.

## <a name="using-azure-container-service"></a>Az Azure Container Service használata
Célunk az Azure Container Service keretében az, hogy a manapság az ügyfelek körében népszerű nyílt forráskódú eszközök és technológiák használatával nyújtsunk tárolóüzemeltetési környezetet. E célból a választott vezénylő (DC/OS, Docker Swarm vagy Kubernetes) számára elérhetővé tesszük a standard API-végpontokat. E végpontokkal bármely olyan szoftvert használhat, amely képes a végpontokkal folytatott kommunikációra. Például a Docker Swarm-végpont esetében választhatja a Docker parancssori felületének használatát. A DC/OS esetében pedig használhatja a DCOS parancssori felületét. A Kubernetes esetében a pkubectl használatát is választhatja.

## <a name="creating-a-docker-cluster-by-using-azure-container-service"></a>Egy Docker-fürt létrehozása az Azure Container Service használatával
Az Azure Container Service használatához üzembe kell helyeznie egy Azure Container Service-fürtöt a portálon keresztül (keressen az „Azure Container Service” kifejezésre), illetve egy Azure Resource Manager-sablon ([Docker Swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm), [DC/OS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos) vagy [Kubernetes](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes) esetén), vagy a [parancssori felület](/articles/xplat-cli-install.md) segítségével. A megadott gyorsindítási sablonok módosíthatók további vagy speciális Azure-konfigurációk belefoglalásával. Az Azure Container Service-fürt üzembe helyezésével kapcsolatos információkért lásd az [Azure Container Service-fürt üzembe helyezését](container-service-deployment.md) ismertető cikket.

## <a name="deploying-an-application"></a>Alkalmazás üzembe helyezése
Az Azure Container Service a vezényléshez lehetővé teszi a választást a Docker Swarm, a DC/OS és a Kubernetes között. A kiválasztott vezénylőtől függ, hogyan helyezi üzembe az alkalmazást.

### <a name="using-dcos"></a>A DC/OS használata
A DC/OS egy olyan elosztott operációs rendszer, amely az Apache Mesos elosztott rendszer kernelén alapszik. Az Apache Mesos az Apache Software Foundation részeként érhető el, és felhasználói és közreműködői között megtalálhatók [az informatikai piac legnagyobb nevei](http://mesos.apache.org/documentation/latest/powered-by-mesos/) is.

![Swarmhoz konfigurált Azure Container Service, ügynökökkel és főkiszolgálókkal.](media/acs-intro/dcos.png)

A DC/OS és az Apache Mesos lenyűgöző szolgáltatáskészletet tesz elérhetővé:

* Bizonyított méretezhetőség
* Apache Zookeepert használó, nagy hibatűrésű replikált főkiszolgáló és alkiszolgálók
* Docker formátumú tárolók támogatása
* Feladatok közötti natív elkülönítés Linux-tárolókkal
* Többforrású ütemezés (memória, processzor, lemez és portok)
* Java, Python és C++ API-k új párhuzamos alkalmazások fejlesztéséhez
* Webes felhasználói felület a fürtállapot áttekintésére

Alapértelmezés szerint az Azure Container Service-en futó DC/OS tartalmazza az ütemezési feladatokhoz szükséges Marathon vezénylési platformot. Azonban az ACS DC/OS rendszerű környezetét részét képező Mesosphere Universe olyan szolgáltatásokat tartalmaz, amelyeket hozzáadhat saját szolgáltatásához. Ilyen többek között például a Spark, a Hadoop és a Cassandra.

![A DC/OS Universe az Azure Container Service-ben](media/dcos/universe.png)

#### <a name="using-marathon"></a>A Marathon használata
A Marathon egy egész fürtre kiterjedő inicializáló és vezérlő rendszer cgroupokban található szolgáltatások vagy – az Azure Container Service esetében – Docker formátumú tárolók számára. A Marathon egy olyan webes felhasználói felületet biztosít, ahonnan telepítheti az alkalmazásokat. Ezt a felületet egy, a következőhöz hasonló URL-címmel érheti el: `http://DNS_PREFIX.REGION.cloudapp.azure.com`, ahol mind a DNS\_PREFIX, mind a REGION az üzembe helyezéskor van meghatározva. Természetesen megadhatja a saját DNS-nevét is. A Marathon webes felhasználói felülete segítségével futtatott tárolóhoz kapcsolódó további információért lásd a [Tárolókezelés a webes felhasználói felületen](container-service-mesos-marathon-ui.md) című cikket.

![A Marathon alkalmazáslistája](media/dcos/marathon-applications-list.png)

REST API-kat is használhat a Marathonnal folytatott kommunikációhoz. Számos ügyfélkódtár létezik, amelyek elérhetők minden egyes eszköz számára. Számos nyelvet támogatnak, és persze a HTTP protokollt is használhatja bármilyen nyelven. Továbbá sok népszerű DevOps-eszköz is támogatja a Marathont. Ez maximális rugalmasságot biztosít a műveleti csapatnak, amikor egy Azure Container Service-fürttel dolgozik. A Marathon REST API-k segítségével futtatott tárolóhoz kapcsolódó további információért lásd a [Tárolókezelés a REST API-val](container-service-mesos-marathon-rest.md) című cikket.

### <a name="using-docker-swarm"></a>A Docker Swarm használata
A Docker Swarm natív fürtszolgáltatást biztosít a Docker számára. Mivel a Docker Swarm a standard Docker API-t szolgálja ki, minden más eszköz, amely már kommunikál a Docker-démonnal használhatja a Swarmot ahhoz, hogy több gazdagépre transzparens módon végezzen átméretezést az Azure Container Service-ben.

![DC OS használatához konfigurált Azure Container Service, jumpboxszal, ügynökökkel és főkiszolgálókkal.](media/acs-intro/acs-swarm2.png)

A Swarm-fürtön való tárolókezeléseket támogató eszközök közé tartoznak többek között az alábbiak:

* Dokku
* Docker parancssori felület (CLI) és Docker Compose
* Krane
* Jenkins

### <a name="using-kubernetes"></a>A Kubernetes használata
A Kubernetes az automatizált tárolókörnyezethez, méretezéshez és tárolóalapú alkalmazások felügyeletéhez készült népszerű, nyílt forráskódú, termelési szintű tárolóvezénylő eszköz. Mivel ez egy nyílt forráskódú megoldás, amelyet a felhasználói közösség fejleszt, problémamentesen fut az Azure Container Service-en, és használható tárolók méretezhető való üzembe helyezésére az Azure Container Service-ben.

![Kubernetes használatához konfigurált Azure Container Service, ügynökökkel és főkiszolgálókkal.](media/acs-intro/kubernetes.png)

Többek között a következő funkciókat tartalmazza:
* Vízszintes méretezés
* Szolgáltatásészlelés és terheléselosztás
* Titkos kódok és konfigurációk kezelése
* API-alapú automatizált kibocsátások és visszaállítások
* Önjavítás
* és még sok más.



## <a name="videos"></a>Videók
Bevezetés az Azure Container Service használatába (101):  

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-Container-Service-101/player]
>
>

Alkalmazások létrehozása az Azure Container Service (2016-os build) használatával

> [!VIDEO https://channel9.msdn.com/Events/Build/2016/B822/player]
>
>



<!--HONumber=Feb17_HO1-->


