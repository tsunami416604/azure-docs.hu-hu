---
title: "A Service Fabric programozási modell áttekintése |} Microsoft Docs"
description: "A Service Fabric szolgáltatások felépítéséhez két keretrendszerek kínál: a szereplő keretrendszer és a szolgáltatások keretében. Különböző kompromisszumot alakítson ki az egyszerűség és vezérlő kínálnak."
services: service-fabric
documentationcenter: .net
author: seanmck
manager: timlt
editor: vturecek
ms.assetid: 974b2614-014e-4587-a947-28fcef28b382
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: a68db62f87bca5c641db310823588df6fb74f75e
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="service-fabric-programming-model-overview"></a>A Service Fabric programozási modell áttekintése
A Service Fabric és a szolgáltatások kezeléséhez több lehetőséget is kínál. Szolgáltatások használhat a Service Fabric API-k a platform szolgáltatásai és alkalmazás-keretrendszerek számára teljes körű kihasználása érdekében. Szolgáltatások bármilyen nyelven vagy egyszerűen csak a Service Fabric fürt által futtatott tároló futó lefordított végrehajtható programok is lehet.

## <a name="guest-executables"></a>Vendég végrehajtható fájlok
A [Vendég végrehajtható](service-fabric-deploy-existing-app.md) -e egy meglévő, tetszőleges végrehajtható fájl (bármilyen nyelven írt) az alkalmazás szolgáltatásként futtatható. Vendég végrehajtható fájlokat ne hívja közvetlenül a Service Fabric SDK API-k. De azok továbbra is kihasználhassa a platformot kínál, például a szolgáltatás felderíthetőség, egyéni állapotfigyelő szolgáltatások, és betölteni a Service Fabric által elérhetővé tett REST API-k hívásával reporting. Teljes alkalmazás életciklusa támogatási is rendelkeznek.

Ismerkedés a Vendég végrehajtható fájlok az első üzembe helyezésével [Vendég futtatható alkalmazás](service-fabric-deploy-existing-app.md).

## <a name="containers"></a>Tárolók
Alapértelmezés szerint a Service Fabric telepíti, és aktiválja a szolgáltatást is folyamatokat. A Service Fabric is telepítheti a szolgáltatások [tárolók](service-fabric-containers-overview.md). A Service Fabric Windows Server 2016 Linux-tárolók és a Windows-tárolók központi telepítését támogatja. Tároló lemezképek bármely tároló tárház lekért, és a gépre telepítve. A meglévő alkalmazások Vendég exectuables, a Service Fabric állapot nélküli és állapotalapú Reliable services és Reliable Actors tárolókban lévő telepítheti, és kombinálhatja a folyamatokat a szolgáltatások és szolgáltatások tárolókban ugyanabban az alkalmazásban.

[További információk a szolgáltatások a Windows vagy Linux containerizing](service-fabric-deploy-container.md)

## <a name="reliable-services"></a>Reliable Services
Megbízható szolgáltatások az passzív keretrendszere, amely integrálható a Service Fabric-platformról és igénybe vehesse az platform funkciói a teljes készletének szolgáltatások írása. Megbízható szolgáltatások adjon meg egy minimális API-k, amelyek lehetővé teszik, hogy a Service Fabric-futtatókörnyezet a szolgáltatások életciklusának kezelését és, amelyek lehetővé teszik a szolgáltatásokat, hogy a futtatókörnyezet kommunikál. Az alkalmazás-keretrendszer minimális, felkínálva tervezési és megvalósítási lehetőségek teljes hozzáféréssel, és minden más alkalmazás-keretrendszert, például az ASP.NET Core üzemeltetéséhez használható.

Megbízható szolgáltatások állapot nélküli alkalmazások, a legtöbb service platformokat, például a webkiszolgálók, ahol a szolgáltatás minden példányának egyenlő jön létre, és állapota is megőrződjenek a külső megoldás például Azure DB vagy az Azure Table Storage hasonló lehet.

Megbízható szolgáltatásokat is lehet állapotalapú, érhető el a Service Fabric, ahol állapot megőrződjenek közvetlenül a megbízható gyűjtemények használatával a szolgáltatás. Állapot magas rendelkezésre állású-replikáció útján, és a particionálás, az összes kezelt Service Fabric által automatikusan keresztül terjesztett.

[További tudnivalók a Reliable Services](service-fabric-reliable-services-introduction.md) vagy Kezdésként [írása az első megbízható szolgáltatás](service-fabric-reliable-services-quick-start.md).

## <a name="aspnet-core"></a>ASP.NET-mag
Az ASP.NET Core egy új nyílt forráskódú és platformfüggetlen keretrendszer modern felhőalapú internetre kapcsolódó alkalmazások, például webalkalmazások, az IoT-alkalmazásokhoz és a mobil háttérkiszolgálókon. Service Fabric ASP.NET Core integrálható, így írhat az ASP.NET Core mind az állapotmentes és állapotalapú alkalmazások megbízható gyűjtemények, valamint a Service Fabric speciális vezénylési képességek előnyeit.

[További tudnivalók az ASP.NET Core a Service Fabric](service-fabric-reliable-services-communication-aspnetcore.md) vagy Kezdésként [az első ASP.NET Core Service Fabric-alkalmazás írása](service-fabric-reliable-services-communication-aspnetcore.md).

## <a name="reliable-actors"></a>Reliable Actors
Reliable Services platformra épül, a megbízható szereplő keretrendszer egy alkalmazás-keretrendszer, amely a virtuális szereplő mintában szereplő kialakítási minta alapján. A megbízható szereplő keretrendszer a számítási műveletek és állapot független egység szereplője nevű egyszálas végrehajtási használ. A megbízható szereplő keretrendszer szereplője és előre beállított állapot megőrzését és kibővített konfigurációk beépített kommunikációt biztosít.

Mivel a Reliable Actors magát a Reliable Services épülő alkalmazás-keretrendszer, teljesen integrálva van a Service Fabric-platformról és a teljes készletét a platform által nyújtott szolgáltatások előnyeit.

[További tudnivalók a Reliable Actors](service-fabric-reliable-actors-introduction.md) vagy Kezdésként [az első megbízható szereplő szolgáltatás írása](service-fabric-reliable-actors-get-started.md)


[ASP.NET Core segítségével egy előtér-szolgáltatás létrehozása](service-fabric-reliable-services-communication-aspnetcore.md)

## <a name="next-steps"></a>További lépések
[A Service Fabric és a tárolók – áttekintés](service-fabric-containers-overview.md)

[Megbízható szolgáltatások – áttekintés](service-fabric-reliable-services-introduction.md)

[Megbízható szereplője áttekintése](service-fabric-reliable-actors-introduction.md)

[A Service Fabric és az ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)




