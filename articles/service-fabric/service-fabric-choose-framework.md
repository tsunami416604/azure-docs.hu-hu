---
title: A Service Fabric programozási modell áttekintése |} A Microsoft Docs
description: 'A Service Fabric biztosít két keretrendszerek, a szolgáltatások: az actors keretrendszerben, és a szolgáltatások keretében. Különböző kompromisszummal az egyszerűség és a vezérlő kínálnak.'
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: vturecek
ms.assetid: 974b2614-014e-4587-a947-28fcef28b382
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: d791148e6ee023239b0a6bf49c705c2da4eea9a2
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56881872"
---
# <a name="service-fabric-programming-model-overview"></a>A Service Fabric programozási modell áttekintése
A Service Fabric írni, és a szolgáltatások kezeléséhez több lehetőséget is kínál. A Service Fabric API-k használatával teljes mértékben kihasználhatja a platform szolgáltatásai és alkalmazás-keretrendszerek szolgáltatások választhat. Szolgáltatások írt bármilyen nyelven vagy a Service Fabric-fürt által futtatott tárolóban futó bármely lefordított végrehajtható programot is lehet.

## <a name="guest-executables"></a>Vendég végrehajtható fájlok
A [futtatható vendégalkalmazás](service-fabric-guest-executables-introduction.md) egy meglévő, tetszőleges végrehajtható fájl (bármilyen nyelven fejlesztett), amely az alkalmazás szolgáltatásként is futtathatók. Futtatható vendégalkalmazás hívja meg a Service Fabric SDK API-k közvetlenül. Azonban azok továbbra is élvezhetik a platformot kínál, például service észlelhetőség, egyéni állapotfigyelő szolgáltatásokat, és töltse be a reporting Service Fabric által elérhetővé tett REST API-jainak meghívásával. Alkalmazás teljes életciklus-támogatása is rendelkeznek.

Ismerkedés a futtatható vendégalkalmazás üzembe az első [futtatható vendégalkalmazás](service-fabric-deploy-existing-app.md).

## <a name="containers"></a>Containers
Alapértelmezés szerint a Service Fabric üzembe helyezi, és aktiválja a szolgáltatást folyamatokat is. A Service Fabric is üzembe helyezheti a szolgáltatások [tárolók](service-fabric-containers-overview.md). Service Fabric Linux-tárolók és a Windows-tárolók üzembe helyezése, támogatja a Windows Server 2016 rendszeren. Tárolórendszerképek is bármely tárolóadattárral lekért és telepíteni kell a a gépet. Futtatható vendégalkalmazás, Service Fabric állapotmentes vagy állapotalapú Reliable services és Reliable Actors-tárolókban, meglévő alkalmazásokat helyezhet üzembe, és kombinálhatja folyamatokon belüli és tárolókon belüli ugyanazt az alkalmazást szolgáltatásokat.

[További információ a Windows vagy Linux-alapú szolgáltatások bontását](service-fabric-deploy-container.md)

## <a name="reliable-services"></a>Reliable Services
A Reliable Services egy olyan egyszerűsített keretrendszer, szolgáltatások, amelyek integrálása a Service Fabric platformot és a platform funkciók teljes készletét írásához. A Reliable Services API-k, amelyek engedélyezik a Service Fabric-futtatókörnyezet az életciklus-szolgáltatását kezelheti és, amelyek engedélyezik a modul kommunikál a szolgáltatások minimális tárházát biztosítja. Az alkalmazás-keretrendszer minimális, így tervezési és megvalósítási lehetőségek teljes körű irányítását, és üzemeltetéséhez, minden más alkalmazás-keretrendszert, például az ASP.NET Core segítségével.

A Reliable Services lehet állapot nélküli, hasonlóan ahhoz, hogy a legtöbb szolgáltatás platformok, például a webkiszolgálók, amelyben a szolgáltatás mindegyik példánya nem jön létre, és állapot-külső megoldások, például az Azure DB vagy az Azure Table Storage van rögzítve.

A Reliable Services is lehet állapotalapú, kizárólagos a Service Fabric, ahol állapotban van megőrzött közvetlenül a szolgáltatásban magát a Reliable Collections használata. Állapot magas rendelkezésre állású replikáció útján létrejötte és particionálása, minden felügyelt automatikusan, a Service Fabric keresztül terjesztett.

[További tudnivalók a Reliable Services](service-fabric-reliable-services-introduction.md) vagy történő használatának első lépései [írása az első megbízható szolgáltatás](service-fabric-reliable-services-quick-start.md).

## <a name="aspnet-core"></a>ASP.NET-mag
Az ASP.NET Core egy új nyílt forráskódú és platformfüggetlen keretrendszer, amellyel modern felhőalapú internetkapcsolattal rendelkező alkalmazások, például a web apps, az IoT-alkalmazások és a mobilalkalmazások háttérkomponensei a. A Service Fabric ASP.NET Core integrálható, így írhat is állapot nélküli és állapotalapú ASP.NET Core alkalmazásokat, amelyek a Reliable Collections és a Service Fabric vezénylési speciális képességek előnyeit.

[További információ a Service Fabric ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) vagy történő használatának első lépései [az első ASP.NET Core Service Fabric-alkalmazás írása](service-fabric-tutorial-create-dotnet-app.md).

## <a name="reliable-actors"></a>Reliable Actors
A Reliable Actors keretrendszerben a Reliable Services-ra épülő, alkalmazás-keretrendszer, amely megvalósítja a virtuális Aktor mintában szereplő tervezési minta alapján. A Reliable Actors keretrendszerben független egység a számítási műveletek és az állapot nevű actors egyszálas végrehajtási használ. A Reliable Actors keretrendszerben aktorok és előre beállított állapot megőrzését és a horizontális felskálázást konfigurációk beépített kommunikációt biztosít.

Mivel a Reliable Actors, Reliable Services épülő alkalmazás-keretrendszer teljesen integrálva van a Service Fabric platformot és a teljes körű a platform által kínált szolgáltatások előnyeit.

[További tudnivalók a Reliable Actors](service-fabric-reliable-actors-introduction.md) vagy történő használatának első lépései [írása az első Reliable Actors-szolgáltatás](service-fabric-reliable-actors-get-started.md)


[ASP.NET Core használatával előtér-szolgáltatás készítése](service-fabric-reliable-services-communication-aspnetcore.md)

## <a name="next-steps"></a>További lépések
[A Service Fabric és a tárolók áttekintése](service-fabric-containers-overview.md)

[A Reliable Services áttekintése](service-fabric-reliable-services-introduction.md)

[A Reliable Actors áttekintése](service-fabric-reliable-actors-introduction.md)

[A Service Fabric és az ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)




