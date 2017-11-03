---
title: "Az AMQP 1.0 az Azure Service Bus áttekintése |} Microsoft Docs"
description: "További információ a speciális Message Queuing protokoll (AMQP) 1.0 Azure használatáról."
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 0e8d19cc-de36-478e-84ae-e089bbc2d515
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/06/2017
ms.author: sethm
ms.openlocfilehash: e2d190dddc3b84eb07eaf2ffea1af48bba0ea13a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="amqp-10-support-in-service-bus"></a>A Service Bus AMQP 1.0-támogatás
Az Azure Service Bus cloud service és a helyszíni [Service Bus a Windows Server (Service Bus 1.1)](https://msdn.microsoft.com/library/dn282144.aspx) támogatja a speciális üzenet Várólistázást protokoll (AMQP) 1.0-s. AMQP platformok közötti, egy megnyitott szabványos protokollt használó hibrid alkalmazások létrehozását teszi lehetővé. Alkalmazás-összetevők, amelyek különböző nyelv és keretrendszer használatával készített, és a különböző operációs rendszereken futó, hogyan hozhat létre. Ezeket az összetevőket a Service Bus és zökkenőmentesen csatlakozni tud az összes üzenetváltásra strukturált üzleti hatékonyan és teljes visszaadása.

## <a name="introduction-what-is-amqp-10-and-why-is-it-important"></a>Bemutatása: Mi az az AMQP 1.0-s, és miért fontos?
Hagyományosan üzenet célú termékekre használt saját protokoll ügyfél-alkalmazások és a brókerek közötti kommunikációhoz. Ez azt jelenti, hogy Miután kijelölt egy adott szállító üzenetkezelési broker, kell gyártója által biztosított szalagtárak való csatlakozásra használja, hogy broker az ügyfél alkalmazások. Az eredmény egy bizonyos fokú függés forgalmazóval, az eljárás egy alkalmazás egy másik termékhez szükséges az egymáshoz kapcsolódó alkalmazások a kód módosítására. 

Ezenkívül üzenetkezelő közvetítők csatlakozás a különböző szállítóktól származó legbonyolultabb. Ehhez általában alkalmazásszintű adatközponthíd-képzés üzenetek áthelyezése egy olyan rendszert másik és a szellemi tulajdont képező üzenetformátumok közötti lefordítani. Ez feltétele közös; például amikor kell egy új egységes felületet biztosít a régebbi különböző rendszerek, vagy az informatikai rendszerek, a következő egyesülés integrálni.

A szoftvergyártói ágazat a gyorsan változó üzleti; néha bewildering ütemben bevezetett új programozási nyelveket és alkalmazás-keretrendszerek számára. Ehhez hasonlóan az informatikai rendszerek követelményeinek verzióinformációk, és a fejlesztők szeretne a legújabb platform szolgáltatásait. Azonban néha a kiválasztott üzenetkezelési szállító nem támogatja a ezekről a platformokról. Mert üzenetküldési protokoll saját fejlesztésű, nincs lehetőség szalagtárak biztosítanak ezekről a platformokról másoknak. Ezért például az átjárók vagy hidak, amelyek lehetővé teszik az üzenetkezelési termék használatának folytatásához megközelítések kell használnia.

A fejlesztői, a speciális Message Queuing protokoll (AMQP) 1.0 indokolta, hogy ezeket a problémákat. A JP Morgan Chase, akik, például a pénzügyi szolgáltatásokat cégek, nehéz felhasználók üzenet célú köztes származik. Egyszerű volt a cél: egy nyílt szabvány szerinti üzenetküldési protokoll, amely szerint több különböző nyelvet, keretrendszerek és operációs rendszerek használatával készített összetevők üzenet-alapú alkalmazások létrehozásához minden használata ajánlott a fajta-összetevőt számos a szállítók.

## <a name="amqp-10-technical-features"></a>Műszaki AMQP 1.0-szolgáltatásokat
AMQP 1.0 egy olyan hatékony, megbízható, vezetékes szintű üzenetkezelési protokoll, amely segítségével hatékony, platformfüggetlen, build üzenetküldő alkalmazások. A protokoll egy egyszerű célja van: a idejéről biztonságos, megbízható és hatékony üzenetek két fél közötti meghatározásához. Maguk az üzenetek kódolt használatával a hordozható adatok megjelenítése, amely lehetővé teszi a heterogén küldő és a helyszíni Exchange strukturált üzleti üzenetekben teljes visszaadása. Az alábbiakban áttekintjük a legfontosabb funkciókról összefoglalása:

* **Hatékony**: AMQP 1.0 kapcsolat alapú protokoll, amelyben a bináris kódolás protokoll utasításokat és az üzleti üzenetek továbbítja. A szerződés magában foglalja a hálózat és a kapcsolódó összetevők felhasználás kifinomult forgalomszabályozás rendszerek. Említett, a protokoll úgy lett kialakítva, hogy egyensúlyt hatékonyságát, a rugalmasság és a kölcsönös átjárhatóságot között.
* **Megbízható**: az AMQP 1.0 protokoll lehetővé teszi az üzenetek küldését megbízhatóság garanciák tűz-és-feledje megbízható, a tartománnyal pontosan-egyszer nyugtázott kézbesítését.
* **Rugalmas**: AMQP 1.0 egy rugalmas protokoll, amely támogatja a különböző topológiák is használható. A rendszer ugyanazt a protokollt az ügyfél-ügyfél, ügyfél-átvitelszervező és broker-broker kommunikáció használható.
* **Munkamenet-átvitelszervező-modell független**: az AMQP 1.0-specifikációja nem teszi szemben támasztott követelményeket az ügynök által használt üzenetkezelési modellt. Ez azt jelenti, hogy könnyen hozzá lehet adni AMQP 1.0 támogatása meglévő üzenetkezelő közvetítők.

## <a name="amqp-10-is-a-standard-with-a-capital-s"></a>AMQP 1.0-s szabvány (az egy beruházási ")
AMQP 1.0 az nemzetközi szabványa, ISO és IEC, ISO/IEC 19464:2014 jóvá.

AMQP 1.0 fejlesztési több mint 20 vállalatok core csoportja által 2008 óta nem történt technológia szállítók és a végfelhasználói vállalkozások. Ebben az időszakban felhasználói vállalkozások hozzájárultak a valós üzleti követelményeknek, és a technológia szállítók erőforrásigények kielégítéséhez protokoll lett hatékonyabb. A folyamat során a szállítók közreműködő azok közötti együttműködés érvényesítéséhez műhelyek részt vett.

A 2011. októberi 2012. októberi a fejlesztési munkálatok során, a fizetési a strukturált adatokat szabványok (OASIS) és a OASIS AMQP 1.0-s szabvány a szervezeten belül technikai bizottság át lett alakítva jelent meg. A következő vállalkozások részt a Technikai Bizottság szabványos fejlesztése során:

* **Technológia szállítók**: Axway szoftver, Huawei technológiákat, IIT szoftver, INETCO rendszerek, Kaazing, a Microsoft, Mitre Corporation, Primeton technológiákat, folyamatban lévő szoftver, Red Hat, SITA, szoftver rendelkezésre állási csoport, Solace rendszerek, VMware, WSO2, Zenika.
* **Felhasználói vállalkozások**: amerikai banki, jóváírás Suisse, német Boerse, Goldman Sachs, JPMorgan Chase.

Nyissa meg szabványok gyakran idézett előnyei a következők:

* Kisebb a szállító zárolás a valószínűsége.
* Együttműködési lehetőség
* Széles körű rendelkezésre állását, valamint a szalagtárak tooling
* Elavulás elleni védelem
* Tájékozott személyzet rendelkezésre állása
* Alacsonyabb és kezelhető is legyen kockázata

## <a name="amqp-10-and-service-bus"></a>AMQP 1.0-ás és Service Bus
Azure Service Bus AMQP 1.0 támogatást azt jelenti, hogy mostantól kihasználhatja a Service Bus queuing és közzétételi/előfizetési közvetítőalapú üzenettovábbítás szolgáltatások egy hatékony bináris protokollal platformok közötti. Ezenkívül összetevőket használja a nyelveket, keretrendszerek és operációs rendszerek áll alkalmazásokat hozhat létre.

A következőképpen ábrázolható telepítését bemutató példát, amelyben Java ügyfelek Linux, a standard Java üzenet szolgáltatás (JMS) API és a Windows rendszeren futó .NET-ügyfelekből használatával készítettek futó exchange-AMQP 1.0 használatával a Service Bus keresztül.

![][0]

**1. ábra: Példa telepítési forgatókönyv megjelenítő platformok közötti Service Bus és AMQP 1.0 használatával**

Jelenleg a következő klienskódtárak segítségével ismert, hogy a Service Bus használata:

| Nyelv | Részletes ismertetés |
| --- | --- |
| Java |Apache Qpid Java üzenet szolgáltatás (JMS) ügyfél<br/>IIT szoftver SwiftMQ Java-ügyfél |
| C# |Apache Qpid Proton-C |
| PHP |Apache Qpid Proton-PHP |
| Python |Apache Qpid Proton-Python |
| C# |AMQP .net Lite |

**2. ábra: Tábla AMQP 1.0-ügyfél szalagtárak**

## <a name="summary"></a>Összefoglalás
* AMQP 1.0 egy olyan nyitott, megbízható üzenetkezelési protokoll, platformfüggetlen, hibrid alkalmazások létrehozásához használhat. AMQP 1.0 az OASIS szabványa.
* AMQP 1.0 a rendszer támogatja az Azure Service Bus, valamint a Service Bus a Windows Server (Service Bus 1.1). Árképzési ugyanúgy történik, mint a meglévő protokollokat.

## <a name="next-steps"></a>Következő lépések
Készen áll a további? Látogasson el a következő hivatkozásokra:

* [Az AMQP, a Service Bus a .NET használatával]
* [Az AMQP, a Service Bus a Java használatával]
* [Apache Qpid Proton-C az Azure Linux virtuális gép telepítése]
* [A Service Bus a Windows Server AMQP]

[0]: ./media/service-bus-amqp-overview/service-bus-amqp-1.png
[Az AMQP, a Service Bus a .NET használatával]: service-bus-amqp-dotnet.md
[Az AMQP, a Service Bus a Java használatával]: service-bus-amqp-java.md
[Apache Qpid Proton-C az Azure Linux virtuális gép telepítése]: service-bus-amqp-apache.md
[A Service Bus a Windows Server AMQP]: https://msdn.microsoft.com/library/dn574799.aspx
