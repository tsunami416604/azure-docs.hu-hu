---
title: Az AMQP 1.0 áttekintése az Azure Service Busban
description: Ismerje meg, hogy az Azure Service Bus hogyan támogatja az Advanced Message Queuing Protocol (AMQP) nyílt szabványprotokollt.
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
ms.openlocfilehash: 50d21cfe8136b9c794eae5104bbb34e28f7c1661
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759312"
---
# <a name="amqp-10-support-in-service-bus"></a>AMQP 1.0 támogatás a Service Busban
Az Azure Service Bus felhőszolgáltatása és a [Windows Server (Service Bus 1.1) helyszíni service busszolgáltatása](https://msdn.microsoft.com/library/dn282144.aspx) egyaránt támogatja az AMQP 1.0-s (Advanced Message Queueing Protocol) 1.0 protokollt. Az AMQP lehetővé teszi, hogy platformfüggetlen, hibrid alkalmazásokat hozzon létre egy nyílt szabványú protokoll használatával. Alkalmazásokat különböző nyelvek és keretrendszerek felhasználásával, valamint különböző operációs rendszereken futó összetevők használatával hozhat létre. Mindezek az összetevők csatlakozhatnak a Service Bus-hoz, és zökkenőmentesen és zökkenőmentesen válthatnak strukturált üzleti üzeneteket.

## <a name="introduction-what-is-amqp-10-and-why-is-it-important"></a>Bevezetés: Mi az AMQP 1.0, és miért fontos?
Hagyományosan, üzenet-orientált middleware termékek használt saját protokollok közötti kommunikáció ügyfélalkalmazások és brókerek. Ez azt jelenti, hogy miután kiválasztotta egy adott szállító üzenetküldő bróker, kell használnia, hogy a szállító kódtárak csatlakozni az ügyfélalkalmazások, hogy a közvetítő. Ez bizonyos fokú függőséget eredményez az adott szállítótól, mivel egy alkalmazás másik termékre történő portolásához kódmódosításokra van szükség az összes csatlakoztatott alkalmazásban. 

Továbbá, összekötő üzenetküldő brókerek a különböző gyártók trükkös. Ehhez általában alkalmazásszintű áthidalásra van szükség az üzenetek egyik rendszerből a másikba történő áthelyezéséhez és a saját üzenetformátumok közötti fordításhoz. Ez egy közös követelmény; például, ha új, egységes felületet kell biztosítania a régebbi különböző rendszerekhez, vagy integrálnia kell az informatikai rendszereket az egyesülést követően.

A szoftveripar gyorsan változó üzlet; az új programozási nyelvek és alkalmazási keretek néha zavarba ejtő ütemben kerülnek bevezetésre. Hasonlóképpen, az informatikai rendszerek követelményei idővel fejlődnek, és a fejlesztők szeretnék kihasználni a legújabb platform funkciókat. Néha azonban a kijelölt üzenetküldő szolgáltató nem támogatja ezeket a platformokat. Mivel az üzenetkezelési protokollok védettek, mások nem tudnak könyvtárakat biztosítani ezekhez az új platformokhoz. Ezért olyan megközelítéseket kell használnia, mint például átjárók vagy hidak létrehozása, amelyek lehetővé teszik az üzenetküldő termék további használatát.

Az Advanced Message Queuing Protocol (AMQP) 1.0 fejlesztését ezek a problémák motiválták. Ez eredetileg a JP Morgan Chase, aki, mint a legtöbb pénzügyi szolgáltató cégek, a nehéz felhasználók üzenet-orientált middleware. A cél egyszerű volt: létrehozni egy nyílt szabványú üzenetküldő protokollt, amely lehetővé tette, hogy üzenetalapú alkalmazásokat hozzunk létre különböző nyelvek, keretrendszerek és operációs rendszerek felhasználásával készült összetevők felhasználásával, amelyek mindegyike a legkiválóbb összetevőket használja egy sor Szállítók.

## <a name="amqp-10-technical-features"></a>AMQP 1.0 műszaki funkciók
Az AMQP 1.0 egy hatékony, megbízható, vezetékes szintű üzenetkezelési protokoll, amelynek segítségével robusztus, platformfüggetlen üzenetküldő alkalmazásokat hozhat létre. A protokoll nak van egy egyszerű célja: meghatározni a mechanika a biztonságos, megbízható és hatékony üzenetek átadása két fél között. Maguk az üzenetek hordozható adatábrázolással vannak kódolva, amely lehetővé teszi a heterogén feladók és fogadók számára, hogy strukturált üzleti üzeneteket cseréljenek teljes hűséggel. Az alábbiakban összefoglaljuk a legfontosabb jellemzőket:

* **Hatékony**: Az AMQP 1.0 egy kapcsolatorientált protokoll, amely bináris kódolást használ a protokollutasításokhoz és a rajta átvitt üzleti üzenetekhez. Kifinomult áramlásvezérlési sémákat tartalmaz a hálózat és a csatlakoztatott összetevők kihasználtságának maximalizálása érdekében. Igaz, a protokoll célja az volt, hogy egyensúlyt teremtsen a hatékonyság, a rugalmasság és az interoperabilitás között.
* **Megbízható**: Az AMQP 1.0 protokoll lehetővé teszi az üzenetek cseréjét számos megbízhatósági garanciával, a tűz-és felejtéstől a megbízható, pontosan egyszer nyugtázott kézbesítésig.
* **Rugalmas**: Az AMQP 1.0 egy rugalmas protokoll, amely különböző topológiák támogatására használható. Ugyanez a protokoll használható az ügyfél-ügyfél, az ügyfél-közvetítő és a közvetítő-közvetítő közötti kommunikációhoz.
* **Bróker-modell független:** Az AMQP 1.0 specifikáció nem tesz semmilyen követelményt a bróker által használt üzenetkezelési modellre vonatkozóan. Ez azt jelenti, hogy könnyen hozzáadhat AMQP 1.0 támogatást a meglévő üzenetküldő brókerekhez.

## <a name="amqp-10-is-a-standard-with-a-capital-s"></a>Az AMQP 1.0 egy standard (nagy "S" betűvel)
Az AMQP 1.0 egy nemzetközi szabvány, amelyet az ISO és az IEC ISO/IEC 19464:2014 néven jóváhagyott.

Az AMQP 1.0 2008 óta fejlesztés alatt áll egy több mint 20 vállalatból álló központi csoportnál, mind technológiai beszállítók, mind végfelhasználócégek nél. Ez idő alatt, a felhasználói cégek hozzájárultak a valós üzleti követelmények és a technológia gyártók fejlődött a protokoll, hogy megfeleljen ezeknek a követelményeknek. A folyamat során a szállítók olyan workshopokon vettek részt, amelyekben együttműködtek a megvalósításaik közötti interoperabilitás ellenőrzésében.

2011 októberében a fejlesztési munka a Strukturált Információs Szabványok Fejlesztéséért Szervezet (OASIS) technikai bizottságává alakult át, és az OASIS AMQP 1.0 Standard 2012 októberében jelent meg. A szabvány kidolgozása során a következő cégek vettek részt a műszaki bizottságban:

* **Technológiai gyártók:** Axway Software, Huawei Technologies, IIT Software, INETCO Systems, Kaazing, Microsoft, Mitre Corporation, Primeton Technologies, Progress Software, Red Hat, SITA, Software AG, Solace Systems, VMware, WSO2, Zenika.
* **Felhasználói cégek**: Bank of America, Credit Suisse, Deutsche Boerse, Goldman Sachs, JPMorgan Chase.

A nyílt szabványok által leggyakrabban idézett előnyök közé tartoznak a következők:

* Kisebb az esélye a szállítói zárolásnak
* Együttműködési lehetőség
* A könyvtárak és az eszközök széles körű elérhetősége
* Az elavulás elleni védelem
* Hozzáértő személyzet rendelkezésre állása
* Alacsonyabb és kezelhető kockázat

## <a name="amqp-10-and-service-bus"></a>AMQP 1.0 és Service Bus
Az AMQP 1.0 támogatása az Azure Service Busban azt jelenti, hogy most már használhatja a Service Bus-sorban állásszolgáltatást, és számos platformról közzétehet/előfizethet közvetített üzenetkezelési funkciókat egy hatékony bináris protokoll használatával. Ezenkívül olyan alkalmazásokat is létrehozhat, amelyek nyelvek, keretrendszerek és operációs rendszerek kombinációjával készült összetevőkből állnak.

Az alábbi ábra egy példa központi telepítés, amelyben a Linuxon futó Java-ügyfelek, a szabványos Java Message Service (JMS) API és a .NET ügyfelek Windows futó, üzeneteket váltanak a Service Bus segítségével AMQP 1.0.

![][0]

**1. ábra: Példa üzembe helyezési forgatókönyvre, amely a Service Bus és az AMQP 1.0 használatával jeleníti meg a platformok közötti üzenetküldést**

Jelenleg a következő ügyfélkódtárak működnek együtt a Service Bus szolgáltatással:

| Nyelv | Erőforrástár |
| --- | --- |
| Java |Apache Qpid Java Message Service (JMS) ügyfél<br/>IIT Software SwiftMQ Java kliens |
| C# |Apache Qpid Proton-C |
| PHP |Apache Qpid Proton-PHP |
| Python |Apache Qpid Proton-Python |
| C# |AMQP .NET Lite |

**2. ábra: Az AMQP 1.0 ügyfélkódtárak táblázata**

## <a name="summary"></a>Összefoglalás
* Az AMQP 1.0 egy nyílt, megbízható üzenetkezelési protokoll, amelynek segítségével platformfüggetlen, hibrid alkalmazásokat hozhat létre. Az AMQP 1.0 egy OASIS szabvány.
* Az AMQP 1.0 támogatása már elérhető az Azure Service Bus ban, valamint a Service Bus for Windows Server (Service Bus 1.1) szolgáltatásban. Az árképzés megegyezik a meglévő protokollok árával.

## <a name="next-steps"></a>További lépések
Készen áll a tanulásra? Látogasson el az alábbi linkekre:

* [A .NET szolgáltatásból származó Service Bus használata amqp-val]
* [A Java-ból származó Service Bus használata amqp-val]
* [Az Apache Qpid Proton-C telepítése Azure Linux os virtuális gépre]
* [AMQP a Windows Server service busszolgáltatásban]

[0]: ./media/service-bus-amqp-overview/service-bus-amqp-1.png
[A .NET szolgáltatásból származó Service Bus használata amqp-val]: service-bus-amqp-dotnet.md
[A Java-ból származó Service Bus használata amqp-val]: service-bus-amqp-java.md
[Az Apache Qpid Proton-C telepítése Azure Linux os virtuális gépre]: service-bus-amqp-apache.md
[AMQP a Windows Server service busszolgáltatásban]: https://msdn.microsoft.com/library/dn574799.aspx
