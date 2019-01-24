---
title: Az AMQP 1.0-t az Azure Service Bus – áttekintés |} A Microsoft Docs
description: További információ a speciális Message Queuing AMQP protokoll 1.0-s az Azure-ban.
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 0e8d19cc-de36-478e-84ae-e089bbc2d515
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 70a0463094f98612169e78e4bcdd4eac9c8ebf24
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54844704"
---
# <a name="amqp-10-support-in-service-bus"></a>A Service Bus AMQP 1.0-támogatás
Az Azure Service Bus felhőalapú szolgáltatás és a helyszíni [Service Bus Windows Server (Service Bus 1.1)](https://msdn.microsoft.com/library/dn282144.aspx) támogatja a speciális üzenet sorba rendezését AMQP protokoll 1.0-t. Az AMQP lehetővé teszi többplatformos hibrid alkalmazások egy nyílt szabványú protokoll használatával hozhat létre. Alkalmazások, amelyek különböző nyelv és keretrendszer használatával készült, és a különböző operációs rendszereket futtató összetevők használatával hozhatnak létre. Ezeket az összetevőket a Service Bus, és zökkenőmentesen kapcsolódik az összes alkalmazásközi strukturált üzleti hatékonyan és teljes visszaadása.

## <a name="introduction-what-is-amqp-10-and-why-is-it-important"></a>Bemutatása: Mi az AMQP 1.0-t, és miért fontos?
Hagyományosan üzenetorientált termékeket használták saját protokoll ügyfélalkalmazások és közvetítők közötti kommunikációhoz. Ez azt jelenti, hogy egy adott gyártó üzenetközvetítő kiválasztása után kell használnia gyártója által biztosított kódtárak csatlakoztatása az ügyfélalkalmazások számára, hogy a közvetítőn. Az eredmény mértékű függés szállítót, mivel egy másik termékkel, egy alkalmazás portolása az összes csatlakoztatott alkalmazások kódmódosítás szükséges. 

Emellett két üzenetkezelő közvetítők különböző szállítóktól származó nagyon bonyolult feladat. Ehhez általában alkalmazásszintű adatközponthíd-képzés üzenetek áthelyezése a rendszer a másikra, és az átalakításra a szellemi tulajdont képező üzenetformátumok között. Ez az általános követelmény; például, amikor meg kell egy új, egységes felületet biztosítanak a régebbi különálló rendszerek vagy egyesülés következő informatikai rendszerek integrálását.

A szoftvergyártói ágazat egy gyorsan változó üzleti; új programozási nyelvet és alkalmazás-keretrendszerek néha bewildering ütemben jelennek meg. Ehhez hasonlóan időbeli alakulásával az informatikai rendszerek követelményeinek, és a fejlesztők szeretne a legújabb platform funkcióinak kihasználása. Azonban néha a kiválasztott üzenetkezelési szállító nepodporuje ezekről a platformokról. Mivel jogvédett üzenetküldési protokollokat, nincs lehetőség mások kódtárakat biztosít ezen új platformokat. Ezért módszerekkel, például átjárókat vagy hidak, amelyek lehetővé teszik, hogy továbbra is használhatja az üzenetkezelési termék létrehozásához kell használnia.

A fejlesztés, a speciális Message Queuing protokoll AMQP 1.0 indokolta, hogy ezeket a problémákat. Azt adja meg, a JP Morgan belőlük az ember, akik, például a pénzügyi szolgáltatásokat nyújtó cégek az üzenetorientált (nagy erőforrásigényű) felhasználói. Egyszerű volt a cél: egy nyílt szabványú üzenetkezelő protokoll, amely lehetségessé vált összetevővel felhasználásával különböző nyelv, keretrendszer és operációs rendszerek esetén üzenet-alapú alkalmazások létrehozásához minden használata a lehető legjobb videóterjesztési-összetevőt számos Szállítók.

## <a name="amqp-10-technical-features"></a>Az AMQP 1.0-s műszaki szolgáltatások
Az AMQP 1.0 egy hatékony, megbízható, alkalmazásszintű üzenetkezelő protokoll, amely segítségével hozhat létre nagy teherbírású, többplatformos üzenetkezelési alkalmazások. A protokoll egy egyszerű célja van: a idejéről biztonságos, megbízható és hatékony üzenetek két fél közötti meghatározásához. Maguk az üzenetek kódolt használatával, amely lehetővé teszi a heterogén küldők és fogadók strukturált üzleti üzeneteket teljes visszaadása, hordozható adatok jelképeként. Az alábbiakban a legfontosabb funkcióit:

* **Hatékony**: Az AMQP 1.0-s egy kapcsolat alapú protokoll, amely használja a bináris kódolás protokoll utasításokat és az üzleti üzenetek továbbítja azt. Kifinomult forgalomszabályozás rendszerek maximalizálhatja a kihasználtság a hálózat és a kapcsolódó összetevőket tartalmazza. Mindemellett a protokoll úgy lett kialakítva, hogy hatékonyságának, rugalmasságának és együttműködési közötti egyensúlyt.
* **Megbízható**: Az AMQP 1.0 protokoll lehetővé teszi, hogy az üzenetek küldését megbízhatóságot garantálja a tűz-és-felejtse el megbízható, számos pontosan – egyszer arra vonatkozik, szállítási.
* **Rugalmas**: Az AMQP 1.0-s egy rugalmas protokoll, amely támogatja a különböző topológiák használható. Ugyanazt a protokollt ügyfél-ügyfél, ügyfél-közvetítő és broker átvitelszervező kommunikációhoz használható.
* **Munkamenet-átvitelszervező-modell független**: Az AMQP 1.0 specifikáció nem derül kapcsolatos követelmények egy közvetítő által használt üzenetküldési modelljével. Ez azt jelenti, hogy könnyen hozzá lehet adni az AMQP 1.0 támogatása meglévő üzenetkezelő közvetítők.

## <a name="amqp-10-is-a-standard-with-a-capital-s"></a>Az AMQP 1.0 egy olyan szabvány (egy tőkével ")
Az AMQP 1.0-t az nemzetközi szabványa, ISO és IEC, ISO/IEC 19464:2014 jóvá.

Az AMQP 1.0-s lett a vállalatok több mint 20 core csoportja által 2008 óta fejlesztési technológia szállítók és a végfelhasználói cégek. Ez idő alatt felhasználói cégek által a való életből vett üzleti követelmények és a technológia-szállítók fejlődtek, a protokoll erőforrásigények kielégítéséhez. A folyamat során szállítók közreműködő ellenőrzése saját megvalósításaikban együttműködésével workshopokat vett részt.

A 2011. október a fejlesztési munkálatok során, a szervezet a előrelépésnek számít, strukturált adatokat szabványok (FEJLESZTÉSÉÉRT) és az OASIS AMQP 1.0-s standard szintű technikai bizottság továbbítjuk 2012. októberi jelent meg. A standard fejlesztése során az alábbi vállalkozások részt vett a technikai bizottság:

* **Technológiai szállítók**: Axway szoftver, Huawei technológiákat, IIT szoftver, INETCO rendszerek, Kaazing, Microsoft, Mitre Corporation, Primeton technológiákat, folyamatban szoftver, Red Hat, SITA, szoftver rendelkezésre állási csoport, Solace rendszerek, a VMware, WSO2, Zenika.
* **Felhasználói cégek**: Kredit Suisse, az Amerikai Deutsche Boerse, Goldman Bank Sachs, JPMorgan belőlük az ember.

Gyakran idézett előnyeit nyílt szabványokat, többek között:

* A szállítói zárolása a kevesebb alkalommal
* Együttműködési lehetőség
* Kódtárak és eszközök széles körű elérhetősége
* Elavulás elleni védelem
* A tapasztalt személyzet rendelkezésre állása
* Alacsonyabb és kezelhető a kockázat

## <a name="amqp-10-and-service-bus"></a>Az AMQP 1.0-ás és Service Bus
AMQP 1.0-támogatás az Azure Service Busban azt jelenti, hogy mostantól használhatja a Service Bus üzenetsor-kezelési és közzétételi/előfizetési közvetítőalapú üzenettovábbítási szolgáltatások egy hatékony bináris protokoll használatával platformok listája. Emellett hozhat létre olyan alkalmazásokat, használja a programozási nyelvek, keretrendszerek és operációs rendszerek-összetevői áll.

Az alábbi ábra telepítését bemutató példát, amelyben írt a szabványos Java Message Service (JMS) API és .NET-ügyfelek futó Windows, Linux rendszeren futó Java-ügyfelek keresztül váltanak üzeneteket a Service Bus AMQP 1.0-val.

![][0]

**1. ábra: Többplatformos üzenetkezelés a Service Bus és az AMQP 1.0 használatával bemutató példa üzembe helyezési forgatókönyv**

Jelenleg a következő ügyfélkódtárak ismert, hogy a Service Bus használata:

| Nyelv | Kódtár |
| --- | --- |
| Java |Az Apache Qpid Java Message Service (JMS) ügyfél<br/>IIT szoftver SwiftMQ Java ügyfél |
| C |Az Apache Qpid Proton-C |
| PHP |Az Apache Qpid Proton – PHP |
| Python |Az Apache Qpid Proton – Python |
| C# |Az AMQP .net Lite |

**2. ábra: Az AMQP 1.0-s ügyfél kódtárak táblázatát**

## <a name="summary"></a>Összegzés
* Az AMQP 1.0-s egy nyitott, megbízható üzenetkezelő protokoll, amely segítségével többplatformos, a hibrid alkalmazások készítését. Az AMQP 1.0-t az OASIS szabványa.
* Az AMQP 1.0 támogatása már elérhető az Azure Service Bus, valamint a Service Bus Windows Server (Service Bus 1.1-es). Díjszabás ugyanúgy történik, mint a meglévő protokollokat.

## <a name="next-steps"></a>További lépések
Készen áll további? Látogasson el az alábbi hivatkozásokat:

* [Az AMQP használatával a Service Bus, a .NET használatával]
* [Az AMQP használatával a Service Bus, a Java használatával]
* [Az Azure Linux rendszerű virtuális gépen Apache Qpid Proton-C telepítése]
* [A Windows Server a Service Bus AMQP]

[0]: ./media/service-bus-amqp-overview/service-bus-amqp-1.png
[Az AMQP használatával a Service Bus, a .NET használatával]: service-bus-amqp-dotnet.md
[Az AMQP használatával a Service Bus, a Java használatával]: service-bus-amqp-java.md
[Az Azure Linux rendszerű virtuális gépen Apache Qpid Proton-C telepítése]: service-bus-amqp-apache.md
[A Windows Server a Service Bus AMQP]: https://msdn.microsoft.com/library/dn574799.aspx
