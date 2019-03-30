---
title: A Cloud Services és a Service Fabric közötti különbségek |} A Microsoft Docs
description: Áttelepítés fogalmi áttekintése alkalmazások a Cloud Servicesből a Service Fabric.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 0b87b1d3-88ad-4658-a465-9f05a3376dee
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 4682e47e664384a6869e1a74e3de6d9083db082b
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58669452"
---
# <a name="learn-about-the-differences-between-cloud-services-and-service-fabric-before-migrating-applications"></a>További tudnivalók a Cloud Services és a Service Fabric közötti különbségekről való migrálás előtt alkalmazásokat.
A Microsoft Azure Service Fabric az új generációs felhőalapú alkalmazás platform, rugalmasan méretezhető, magas megbízhatóságú elosztott alkalmazásokhoz. Azt mutatja be a csomagolása, üzembe helyezésével, frissítésével és elosztott felhőalkalmazások kezelése frissítés számos új szolgáltatást. 

Egy bevezető útmutató az alkalmazások a Cloud Servicesből a Service Fabric. Azt architekturális elsősorban a összpontosít, és a Cloud Services és a Service Fabric közötti különbségek kialakítása.

## <a name="applications-and-infrastructure"></a>Alkalmazások és infrastruktúra
Alapvető különbség a Cloud Services és a Service Fabric kapcsolódik egymáshoz a virtuális gépek, a számítási feladatok és az alkalmazások. Itt egy számítási feladat számít, ha az adott feladat végrehajtására, vagy adjon meg egy szolgáltatás írt kódot.

* **A cloud Services van a virtuális alkalmazások központi telepítésével kapcsolatos.** Írt szorosan összekapcsolt, például egy webes vagy feldolgozói szerepkör egy Virtuálisgép-példányhoz. A Cloud Services számítási feladatok üzembe helyezéséhez van, amely a számítási feladatok futtatásához egy vagy több Virtuálisgép-példányok üzembe helyezéséhez. Alkalmazások és a virtuális gépek nem szétválasztása van, és itt is van az alkalmazás nem hivatalos definíció nem. Egy alkalmazás tekinthető webes vagy feldolgozói szerepkör példányok egy Cloud Services-környezetben, vagy egy teljes Cloud Services-környezetben. Ebben a példában egy alkalmazás jelenik meg a szerepkörpéldányok készletként.

![Cloud Services-alkalmazások és a topológia][1]

* **Service Fabric a meglévő virtuális gépekhez vagy a Service Fabric futó Windows vagy Linux rendszerű gépek alkalmazások központi telepítésével kapcsolatos.** A szolgáltatások ír teljesen leválasztott származó az alapul szolgáló infrastruktúrát, amelyeket azonnal a Service Fabric application platform, így egy alkalmazás több környezethez is telepíthető. A Service Fabric egy számítási feladat "szolgáltatás" nevezzük, és a egy korábbi által definiált alkalmazás, amely a Service Fabric-alkalmazás platformon fut, egy vagy több szolgáltatás szerint vannak csoportosítva. Több alkalmazást is üzembe helyezhetők a Service Fabric-fürtön.

![Service Fabric-alkalmazásokat és topológia][2]

Maga a Service Fabric, amely egy application platform Windows vagy Linux-alapú futtató, mivel a Cloud Services egy rendszer üzembe helyezéséhez az Azure által felügyelt virtuális gépeket csatolt számítási feladatokkal.
A Service Fabric-alkalmazásmodell rendelkezik számos előnnyel jár:

* Gyors üzembe helyezéshez szükséges idő. Virtuálisgép-példányok létrehozása időigényes lehet. A Service Fabric segítségével a virtuális gépek csak telepítése után a fürtöt, amely üzemelteti a Service Fabric application platform. Az ettől az alkalmazáscsomagok telepíthetők a fürt nagyon gyorsan.
* Nagy sűrűségű üzemeltetés. A Cloud Services a feldolgozói szerepkör virtuális gép, amelyen egy számítási feladat. A Service Fabric, az alkalmazások elkülönülnek futtató virtuális gépek őket, ami azt jelenti, nagy számú kis számú virtuális gépeket, ami csökkentheti a teljes költség, nagyobb telepítések alkalmazásokat telepíthet.
* A Service Fabric platformot bárhol futtathatók, amely rendelkezik a Windows Server vagy Linux rendszerű gépek Azure-ban vagy a helyszíni e. A platform biztosítja az alapul szolgáló infrastruktúra feletti egy olyan absztrakciós réteget, így az alkalmazás futtatható a különböző környezetekben. 
* Elosztott alkalmazás kezelése. A Service Fabric az, hogy nem csak gazdagépek elosztott alkalmazásokhoz, de is segít kezelése életciklusukon függetlenül, a üzemeltetési virtuális gép vagy gépek életciklusa platform.

## <a name="application-architecture"></a>Alkalmazásarchitektúra
A Cloud Services-alkalmazás architektúrájának általában magában foglalja számos olyan külső függőségei, mint a Service Bus, az Azure Table és a Blob Storage, SQL, a Redis és mások állapota és adatai egy alkalmazás és webhely közötti kommunikáció kezelése és A központi telepítés a Cloud Services feldolgozói szerepkörei. A teljes Cloud Services-alkalmazás például ehhez hasonló lehet:  

![Cloud Services architektúrája][9]

Service Fabric-alkalmazásokat is beállíthatja a teljes alkalmazás ugyanazok a külső szolgáltatások használata. Ebben a példában a Cloud Services-architektúra használatával, a legegyszerűbb áttelepítési út a Cloud Servicesből a Service Fabric az csak a Cloud Services üzembe helyezési cserélje le a Service Fabric-alkalmazás, ugyanazt az általános architektúrát tartja. A webes és feldolgozói szerepkörök már Service Fabric állapotmentes szolgáltatások révén minimális kódmódosítással is lehet.

![Egyszerű áttelepítés után a Service Fabric-architektúra][10]

Ezen a ponton a rendszer továbbra is a korábbiaknak megfelelően működjön. Kihasználni a Service Fabric állapotalapú szolgáltatások, külső állapotuk tárolók is lehet internalized, ahogy a állapotalapú szolgáltatások, ha vannak ilyenek. Ez a bonyolultabb, mint a Service Fabric állapotmentes szolgáltatások, webes és feldolgozói szerepkörök egyszerű áttelepítést, az egyéni szolgáltatásokról, amelyek az alkalmazás egyenértékű funkciókat biztosít, mint a külső szolgáltatások előtt írása szükséges. Ennek előnyei a következők: 

* Külső függőségek eltávolítása 
* A központi telepítési, kezelési és frissítési modellek egységes. 

Ezek a szolgáltatások internalizing eredményül kapott példa architektúrát nézhet ki:

![A teljes áttelepítés után a Service Fabric-architektúra][11]

## <a name="communication-and-workflow"></a>Kommunikációs és munkafolyamat
A legtöbb Cloud Service-alkalmazások egynél több szinten állnak. Ehhez hasonlóan a Service Fabric-alkalmazás több szolgáltatás (általában számos szolgáltatás) áll. Két gyakori kommunikációs modellt a közvetlen kommunikációt és a egy külső tartós tárolási keresztül.

### <a name="direct-communication"></a>Közvetlen kommunikáció
Közvetlen kommunikációt az rétegek közvetlenül minden egyes réteg által elérhető végponton keresztüli kommunikációt. Állapot nélküli környezetekben, például a Cloud Services, ez azt jelenti, hogy kiválasztja egy példányt egy Virtuálisgép-szerepkör, vagy véletlenszerűen vagy Ciklikus időszeleteléses egyenleg betölteni, és közvetlenül csatlakozik a végpont.

![A közvetlen kommunikációt a cloud Services][5]

 Közvetlen kommunikációra a Service Fabric egy általános kommunikációs modellt. A Service Fabric és Felhőszolgáltatások közötti fő különbség, hogy a Cloud Services csatlakoztat egy virtuális géphez, mivel a Service Fabric csatlakozik a szolgáltatáshoz. Ez fontos különbség néhány okok miatt:

* A Service Fabric Services nincs kötve a; üzemeltető virtuális gépek szolgáltatások előfordulhat, hogy Navigálás a fürt, és tulajdonképpen várhatóan Navigálás különböző okok miatt: Erőforrás terheléselosztás, feladatátvétel, alkalmazás és -infrastruktúra frissítések és elhelyezési vagy a betöltés korlátozása. Ez azt jelenti, hogy egy szolgáltatáspéldány címet bármikor módosíthatja. 
* Egy virtuális gép a Service Fabric több szolgáltatást, amelyek mindegyike egyedi végpontok is üzemeltethet.

A Service Fabric lehetővé teszi a szolgáltatás felderítési, neve az elnevezési szolgáltatás, amely szolgáltatások végpontcímeket megoldásához használható. 

![A Service Fabric közvetlen kommunikáció][6]

### <a name="queues"></a>Üzenetsorok
Egy általános kommunikációs mechanizmus állapot nélküli környezetekben, például a Cloud Services réteg között, hogy egy külső storage-üzenetsor tartósan tárolja a munkahelyi feladatokat az egyik rétegről a másikra. Egy gyakori forgatókönyv, amely egy Azure-üzenetsor vagy ahol feldolgozói szerepkör példányai is a sorból, és a feladatok feldolgozásához a Service Bus feladatok küld a webes szint.

![Cloud Services várólista kommunikáció][7]

A Service Fabric ugyanazt a kommunikációs modellt is használható. Ez akkor hasznos, ha egy meglévő alkalmazást a Cloud Services Service Fabric áttelepítése. 

![A Service Fabric közvetlen kommunikáció][8]

## <a name="next-steps"></a>További lépések
A legegyszerűbb áttelepítési út a Cloud Servicesből a Service Fabric, hogy csak a Cloud Services üzembe helyezési cserélje le a Service Fabric-alkalmazás, így az általános architektúrát, az alkalmazás nagyjából azonos. A következő cikk útmutató egy webes vagy feldolgozói szerepkör átalakítása egy Service Fabric állapotmentes szolgáltatás.

* [Egyszerű migrálás: a webes vagy feldolgozói szerepkör átalakítása egy Service Fabric állapotmentes szolgáltatás](service-fabric-cloud-services-migration-worker-role-stateless-service.md)

<!--Image references-->
[1]: ./media/service-fabric-cloud-services-migration-differences/topology-cloud-services.png
[2]: ./media/service-fabric-cloud-services-migration-differences/topology-service-fabric.png
[5]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-direct.png
[6]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-direct.png
[7]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-queues.png
[8]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-queues.png
[9]: ./media/service-fabric-cloud-services-migration-differences/cloud-services-architecture.png
[10]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-simple.png
[11]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-full.png
