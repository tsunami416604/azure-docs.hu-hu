---
title: A AMQP 1,0-es verziójának áttekintése Azure Service Bus
description: Ismerje meg, hogyan támogatja a Azure Service Bus a Advanced Message Queueing Protocol (AMQP), egy nyílt szabványú protokollt.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 1113ffd268c991ba8a9c8f574b90d555a81ae840
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87083871"
---
# <a name="amqp-10-support-in-service-bus"></a>AMQP 1,0-támogatás Service Bus
A Windows Server Azure Service Bus Cloud Service és a helyszíni [Service Bus (Service Bus 1,1)](https://msdn.microsoft.com/library/dn282144.aspx) egyaránt támogatja a speciális Message Queuing PROTOKOLLT (AMQP) 1,0. A AMQP lehetővé teszi többplatformos hibrid alkalmazások készítését egy nyílt szabványú protokoll használatával. Az alkalmazásokat a különböző nyelvekkel és keretrendszerekkel, valamint a különböző operációs rendszereken futó összetevőkkel építheti fel. Ezek az összetevők kapcsolódhatnak a Service Bushoz, és zökkenőmentesen cserélhetik fel a strukturált üzleti üzeneteket hatékonyan és teljes hűséggel.

## <a name="introduction-what-is-amqp-10-and-why-is-it-important"></a>Bevezetés: mi a AMQP 1,0, és miért fontos?
A hagyományosan az üzenetsor-orientált middleware-termékek saját protokollokat használnak az ügyfélalkalmazások és a brókerek közötti kommunikációhoz. Ez azt jelenti, hogy ha kiválasztotta egy adott szállító üzenetkezelési közvetítőjét, akkor a gyártó kódtárait kell használnia az ügyfélalkalmazások ehhez a közvetítőhöz való összekapcsolásához. Ez az adott gyártótól való függőséget eredményez, mivel az alkalmazások egy másik termékhez való portolása minden csatlakoztatott alkalmazásban kód módosítását igényli. 

Emellett a különböző gyártóktól származó üzenetkezelési közvetítők összekapcsolása bonyolult. Ez általában az alkalmazás-szintű áthidalót igényli az üzenetek egyik rendszerről egy másikra való áthelyezéséhez, illetve a saját üzenetek formátumának lefordításához. Ez egy gyakori követelmény; Ha például egy új egységesített felületet kell megadnia a régebbi, különálló rendszerekhez, vagy integrálnia kell az informatikai rendszereket az egyesítést követően.

A szoftveres iparág egy gyorsan változó üzleti tevékenység; az új programozási nyelvek és alkalmazás-keretrendszerek időnként megzavarják a lépést. Hasonlóképpen, az informatikai rendszerek követelményei idővel fejlődnek, és a fejlesztők a platform legújabb funkcióit szeretnék kihasználni. Előfordulhat azonban, hogy a kiválasztott üzenetküldési szállító nem támogatja ezeket a platformokat. Mivel az üzenetkezelési protokollok védettek, nem lehetséges, hogy mások is biztosítanak könyvtárakat ezekhez az új platformokhoz. Ezért olyan megközelítéseket kell használnia, mint például az átjárók vagy hidak létrehozása, amelyek lehetővé teszik az üzenetkezelési termék használatának folytatását.

A Advanced Message Queueing Protocol (AMQP) 1,0 fejlesztését ezen problémák indokolták. A JP Morgan Chasetől származik, akik a legtöbb pénzügyi szolgáltatóhoz hasonlóan nagy mennyiségű, az üzenetsor-orientált middleware-t használó felhasználók. A cél egyszerű volt: egy nyílt szabványú üzenetküldési protokoll létrehozásához, amely a különböző nyelvekkel, keretrendszerekkel és operációs rendszerekkel létrehozott összetevők használatával teszi elérhetővé az üzenetkezelési alkalmazásokat, mindezt a legjobb összetevők széles választékával.

## <a name="amqp-10-technical-features"></a>AMQP 1,0 – technikai funkciók
A AMQP 1,0 egy hatékony, megbízható, vezetékes üzenetkezelési protokoll, amellyel robusztus, platformfüggetlen, üzenetkezelési alkalmazásokat hozhat létre. A protokoll egyszerű célja: az üzenetek biztonságos, megbízható és hatékony átvitelének mechanikája két fél között. Maguk az üzenetek olyan hordozható adatábrázolással vannak kódolva, amely lehetővé teszi a heterogén küldők és fogadók számára a strukturált üzleti üzenetek teljes megbízhatóságú cseréjét. Az alábbiakban a legfontosabb funkciók összegzését ismertetjük:

* **Hatékony**: a AMQP 1,0 egy kapcsolaton alapuló protokoll, amely bináris kódolást használ a protokoll utasításainak és a rajta továbbított üzleti üzeneteknek. Kifinomult folyamat-ellenőrzési sémákat tartalmaz a hálózat és a csatlakoztatott összetevők kihasználtságának maximalizálása érdekében. Ez azt jelenti, hogy a protokoll úgy lett kialakítva, hogy egyensúlyt teremteni a hatékonyság, a rugalmasság és az együttműködési képesség között.
* **Megbízható**: a AMQP 1,0 protokoll lehetővé teszi, hogy az üzenetek számos megbízhatósági garanciával legyenek kicserélve, a tűz és a felejtsd el a megbízható, pontosan egyszeri nyugtázott kézbesítést.
* **Rugalmas**: a AMQP 1,0 egy rugalmas protokoll, amely különböző topológiák támogatásához használható. Ugyanez a protokoll használható az ügyfél és az ügyfél, az ügyfelek és a közvetítők közötti kommunikációhoz is.
* **Közvetítő – modell független**: a AMQP 1,0 specifikáció nem tesz követelményt a közvetítő által használt üzenetkezelési modellre vonatkozóan. Ez azt jelenti, hogy könnyen hozzáadhat AMQP 1,0-támogatást a meglévő üzenetkezelési közvetítőknek.

## <a name="amqp-10-is-a-standard-with-a-capital-s"></a>A AMQP 1,0 egy standard (Capital 'S)
A AMQP 1,0 egy nemzetközi szabvány, amelyet az ISO és az IEC is jóváhagy ISO/IEC 19464:2014 néven.

A AMQP 1,0 a 2008-es, több mint 20 vállalat, a technológiai beszállítók és a végfelhasználói vállalatok egyik fő csoportjának fejlesztései óta. Ebben az időszakban a felhasználói cégek járultak hozzá a valós üzleti követelményekhez, és a technológiai szolgáltatók kifejlődtek a protokollon, hogy megfeleljenek a követelményeknek. A folyamat során a szállítók részt vettek a workshopokban, amelyekben együttműködtek a megvalósításuk közötti együttműködés ellenőrzéséhez.

Október 2011-án a fejlesztési munka a szervezeten belüli technikai bizottságba került át a strukturált információs szabványok (OASIS) és az OASIS AMQP 1,0 standard 2012 kiadásában. A következő cégek részt vettek a technikai bizottságban a standard fejlesztése során:

* **Technológiai szállítók**: Axway szoftver, Huawei Technologies, IIT szoftver, INETCO-rendszerek, Kaazing, Microsoft, Mitre Corporation, Primeton Technologies, folyamatjelző szoftver, Red Hat, a, a Software AG, a vigaszrendszer, a VMware, a WSO2 és a Zenika.
* **Felhasználói cégek**: Bank of America, Credit Suisse, Deutsche Boerse, Goldman Sachs, JPMorgan Chase.

A nyílt szabványok általánosan idézett előnyei többek között a következők:

* A gyártó zárolásának kisebb esélye
* Együttműködési lehetőség
* Könyvtárak és eszközök széles körű rendelkezésre állása
* Az elavultság elleni védelem
* A hozzáértő munkatársak rendelkezésre állása
* Alacsonyabb és kezelhető kockázat

## <a name="amqp-10-and-service-bus"></a>AMQP 1,0 és Service Bus
A AMQP 1,0-es verziójának támogatása a Azure Service Bus azt jelenti, hogy mostantól kihasználhatja a Service Bus Queuing szolgáltatást, és a felügyelt és előfizetéseket számos platformról kihasználhatja egy hatékony bináris protokoll használatával. Emellett a különböző nyelvek, keretrendszerek és operációs rendszerek együttes használatával létrehozott összetevőkből álló alkalmazásokat is készíthet.

Az alábbi ábra egy példát mutat be egy olyan központi telepítésre, amelyben a Linux rendszeren futó Java-ügyfelek a standard Java Message Service (JMS) API-val és a Windows rendszeren futó .NET-ügyfelekkel, az AMQP 1,0-es verzióját használva Exchange-Service Bus üzeneteket küldenek.

![Diagram, amely egy Service Bus két Linux-környezettel és két Windows-környezettel rendelkező üzeneteket cserél.][0]

**1. ábra: példa a többplatformos üzenetküldést Service Bus és AMQP 1,0-et használó üzembe helyezési forgatókönyvre**

Jelenleg a következő ügyféloldali függvénytárak ismertek a Service Bus-vel való együttműködésre:

| Nyelv | Kódtár |
| --- | --- |
| Java |Apache csontos Java Message Service (JMS) ügyfél<br/>IIT szoftver SwiftMQ Java-ügyfél |
| C |Apache csontos-proton – C |
| PHP |Apache csontos proton – PHP |
| Python |Apache csontos proton – Python |
| C# |AMQP .NET Lite |

**2. ábra: AMQP 1,0 ügyféloldali kódtárak táblázata**

## <a name="summary"></a>Összegzés
* A AMQP 1,0 egy nyílt, megbízható üzenetkezelési protokoll, amellyel többplatformos hibrid alkalmazások hozhatók létre. A AMQP 1,0 egy oázis standard.
* A AMQP 1,0 támogatás mostantól elérhető Azure Service Busban, valamint Service Bus a Windows Serverhez (Service Bus 1,1). A díjszabás megegyezik a meglévő protokollokkal.

## <a name="next-steps"></a>További lépések
Készen áll a tanulásra? Látogasson el a következő hivatkozásokra:

* [Service Bus használata a .NET-ről a AMQP használatával]
* [Service Bus használata a Java és a AMQP használatával]
* [Az Apache csontos proton-C telepítése Azure Linux rendszerű virtuális gépen]
* [A Windows Server Service Bus AMQP]

[0]: ./media/service-bus-amqp-overview/service-bus-amqp-1.png
[Service Bus használata a .NET-ről a AMQP használatával]: service-bus-amqp-dotnet.md
[Service Bus használata a Java és a AMQP használatával]: service-bus-amqp-java.md
[Az Apache csontos proton-C telepítése Azure Linux rendszerű virtuális gépen]: service-bus-amqp-apache.md
[A Windows Server Service Bus AMQP]: https://msdn.microsoft.com/library/dn574799.aspx
