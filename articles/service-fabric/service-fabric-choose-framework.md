---
title: A Service Fabric programozási modelljének áttekintése
description: 'A Service Fabric két keretrendszert kínál az építési szolgáltatásokhoz: az aktor keretrendszer és a szolgáltatási keretrendszer. Kínálnak különböző kompromisszumokat az egyszerűség és az ellenőrzés.'
author: vturecek
ms.topic: conceptual
ms.date: 01/07/2020
ms.custom: sfrev
ms.openlocfilehash: 11e32c9d1290227e638a314ed8417b1bed906842
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75749529"
---
# <a name="service-fabric-programming-model-overview"></a>A Service Fabric programozási modelljének áttekintése

A Service Fabric többféle módon írhatja és kezelheti a szolgáltatásokat. A szolgáltatások a Service Fabric API-k használatával teljes mértékben kihasználhatják a platform szolgáltatásait és alkalmazáskeretrendszerét. A szolgáltatások bármely lefordított végrehajtható program, amely bármely nyelven vagy kódon fut egy Service Fabric-fürtön tárolt tárolóban.

## <a name="guest-executables"></a>Vendég végrehajtható fájlok

A [vendég végrehajtható fájl](service-fabric-guest-executables-introduction.md) egy meglévő, tetszőleges végrehajtható fájl (bármilyen nyelven írott), amely szolgáltatásként futtatható az alkalmazásban. Vendég végrehajtható fájlok nem hívja meg a Service Fabric SDK API-k közvetlenül. Azonban továbbra is kihasználják a platform által nyújtott funkciók, például a szolgáltatás felderíthetőségét, az egyéni állapotés a terhelésjelentés a Service Fabric által elérhetővé tett REST API-k hívásával. Ők is teljes körű alkalmazás életciklus-támogatás.

Az első [vendég végrehajtható alkalmazás](service-fabric-deploy-existing-app.md)telepítésével ismerkedhet meg a vendég végrehajtható fájlokkal.

## <a name="containers"></a>Containers

Alapértelmezés szerint a Service Fabric folyamatokként telepíti és aktiválja a szolgáltatásokat. A Service Fabric [tárolókban](service-fabric-containers-overview.md)is üzembe helyezhetszolgáltatásokat. A Service Fabric támogatja a Linux-tárolók és a Windows-tárolók telepítését Windows Server 2016-ban és újabb rendszereken. A tárolórendszerképek bármely tárolótárból lehúzhatók, és üzembe helyezhetők a gépre. Meglévő alkalmazásokat vendég végrehajtható fájlként, a Service Fabric állapotmentes vagy állapotalapú megbízható szolgáltatások vagy megbízható szereplők tárolókban, és a szolgáltatások keverése a folyamatok és szolgáltatások tárolókugyanabban az alkalmazásban.

[További információ a szolgáltatások Windows vagy Linux rendszerben való konténerezéséről](service-fabric-deploy-container.md)

## <a name="reliable-services"></a>Reliable Services

A Reliable Services egy könnyű keretrendszer olyan szolgáltatások írásához, amelyek integrálhatók a Service Fabric platformmal, és a platform összes szolgáltatásának előnyeit élvezhetik. A Megbízható szolgáltatások minimális API-készletet biztosítanak, amelyek lehetővé teszik a Service Fabric futásidejű a szolgáltatások életciklusának kezelésére, és amelyek lehetővé teszik a szolgáltatások számára a futásidejű együttműködését. Az alkalmazáskeretrendszer minimális, így teljes mértékben szabályozhatja a tervezési és megvalósítási lehetőségeket, és bármely más alkalmazáskeretrendszer, például ASP.NET Core üzemeltetésére használható.

Megbízható szolgáltatások állapot nélküli lehet, hasonlóan a legtöbb szolgáltatás platformok, például a webkiszolgálók, amelyben a szolgáltatás minden példánya egyenlőnek jön létre, és állapot egy külső megoldás, például az Azure DB vagy az Azure Table Storage.

Kizárólag a Service Fabric, megbízható szolgáltatások is állapotalapú, ahol az állapot közvetlenül a szolgáltatás ban is megmarad megbízható gyűjtemények használatával. Az állapot a replikáció révén magas szintű elérhetővé válik, és particionálással van elosztva, és a Service Fabric automatikusan kezeli.

[Tudjon meg többet a Megbízható szolgáltatásokról,](service-fabric-reliable-services-introduction.md) vagy az [első Megbízható szolgáltatás megírásával.](service-fabric-reliable-services-quick-start.md)

## <a name="aspnet-core"></a>ASP.NET-mag

ASP.NET Core egy nyílt forráskódú, platformfüggetlen keretrendszer modern felhőalapú internetkapcsolattal rendelkező alkalmazások, például webalkalmazások, IoT-alkalmazások és mobil háttérrendszerek létrehozásához. A Service Fabric integrálható ASP.NET Core- val, így állapot nélküli és állapotalapú ASP.NET Core alkalmazásokat is írhat, amelyek kihasználják a Megbízható gyűjtemények és a Service Fabric fejlett vezénylési képességeit.

[Tudjon meg többet ASP.NET Core a Service Fabricben,](service-fabric-reliable-services-communication-aspnetcore.md) vagy első lépések [írása az első ASP.NET Core Service Fabric-alkalmazás.](service-fabric-tutorial-create-dotnet-app.md)

## <a name="reliable-actors"></a>Reliable Actors

A megbízható szolgáltatásokra épülő Reliable Actor keretrendszer egy alkalmazáskeretrendszer, amely megvalósítja a [virtuális aktor](https://research.microsoft.com/en-us/projects/orleans/) minta, a számítási [aktor modell](https://en.wikipedia.org/wiki/Actor_model)alapján. A Megbízható szereplő keretrendszer független számítási és állapotegységeket használ az egyszálas *végrehajtással, amelyet szereplőknek neveznek.* A Megbízható szereplő keretrendszer beépített kommunikációt biztosít a szereplők és az előre beállított állapotmegőrzési és horizontális felskálázási konfigurációk számára.

Mivel a Reliable Actors egy megbízható szolgáltatásokra épülő alkalmazáskeretrendszer, teljes mértékben integrálva van a Service Fabric platformmal, és a platform által kínált funkciók teljes készletének előnyeit élvezi.

[Tudjon meg többet a Megbízható szereplőkről,](service-fabric-reliable-actors-introduction.md) vagy az [első Megbízható szereplő szolgáltatás megírásával](service-fabric-reliable-actors-get-started.md)

[Front-end szolgáltatás létrehozása ASP.NET Core használatával](service-fabric-reliable-services-communication-aspnetcore.md)

## <a name="next-steps"></a>További lépések

[Szolgáltatásfabric és tárolók – áttekintés](service-fabric-containers-overview.md)

[A Reliable Services áttekintése](service-fabric-reliable-services-introduction.md)

[A Reliable Actors áttekintése](service-fabric-reliable-actors-introduction.md)

[Service Fabric és ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)
