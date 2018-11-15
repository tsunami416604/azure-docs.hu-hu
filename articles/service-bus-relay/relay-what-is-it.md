---
title: Mi az az Azure Relay? | Microsoft Docs
description: Ez a cikk az Azure Relay szolgáltatásról nyújt áttekintést, amely a vállalati hálózaton futó, helyszíni szolgáltatásokat használó felhőalkalmazások fejlesztését teszi lehetővé anélkül, hogy meg kellene nyitni egy tűzfalkapcsolatot, vagy zavaró módosításokat kellene végrehajtani a hálózati infrastruktúrán.
services: service-bus-relay
author: spelluru
manager: ''
editor: ''
ms.assetid: 1e3e971d-2a24-4f96-a88a-ce3ea2b1a1cd
ms.service: service-bus-relay
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: get-started-article
ms.date: 11/13/2018
ms.author: spelluru
ms.openlocfilehash: 132fca9c8904c4f64fffe167e3a3f2c6bb0d2cfe
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51621027"
---
# <a name="what-is-azure-relay"></a>Mi az az Azure Relay?
Az Azure Relay szolgáltatás biztonságosan lehetővé teszi a vállalati hálózaton futó szolgáltatások közzétételét a nyilvános felhőben. Ezt a port, a tűzfal megnyitása vagy zavaró módosításokat kellene végrehajtani gondoskodik a vállalati hálózati infrastruktúrában anélkül teheti meg. 

A Relay szolgáltatás az alábbi, helyszíni szolgáltatások és felhőben vagy más helyszíni környezetben futtatott alkalmazások közötti forgatókönyveket támogatja. 

- Hagyományos egyirányú, kérelem/válasz típusú és társközi kommunikáció; 
- Eseményterjesztés az internetes tartományban, lehetővé téve különböző közzétételi/előfizetési forgatókönyveket; 
- Hálózathatárokon átívelő, kétirányú és nem pufferelt szoftvercsatornás kommunikáció.

Az Azure Relay abban tér el a hálózati szintű integrációs technológiáktól (például a VPN-től), hogy egyetlen gép adott alkalmazásvégpontjára is fókuszálható. A VPN technológia ennél jóval agresszívabb, hiszen a hálózati környezet módosítására támaszkodik. 

## <a name="basic-flow"></a>Alapszintű folyamat
A továbbítón keresztüli adatátviteli minta alapszintű lépései a következők:

1. Egy helyszíni szolgáltatás csatlakozik a továbbítási szolgáltatáshoz egy kimenő porton keresztül. 
2. Létrehoz egy adott címhez kötött kétirányú szoftvercsatornát a kommunikációhoz. 
3. Az ügyfél ezután kommunikálhat a helyszíni szolgáltatással, ha ezt a címet megcélozva forgalmat küld a továbbítási szolgáltatásnak. 
4. A továbbítási szolgáltatás ezután *továbbítja* az adatokat a helyszíni szolgáltatásba az ügyfél számára kijelölt kétirányú szoftvercsatornán keresztül. Az ügyfélnek nincs szüksége közvetlen kapcsolatra a helyszíni szolgáltatással, nem kell ismernie a szolgáltatás helyét sem, a helyszíni szolgáltatásnak pedig nincs szüksége megnyitott bejövő portra a tűzfalon.


## <a name="features"></a>Szolgáltatások 
Az Azure Relay két funkciója:

- [Hibrid kapcsolatok](#hybrid-connections) – A többplatformos forgatókönyveket lehetővé tevő WebSockets nyílt szabványt használja.
- [WCF-továbbítók](#wcf-relays) – A Windows Communication Foundation (WCF) használatával engedélyezi a távoli eljáráshívásokat. A WCF-továbbító egy olyan örökölt Relay-ajánlat, amelyet számos ügyfél már használ a WCF-programozási modelljeivel.

## <a name="hybrid-connections"></a>Hibrid kapcsolatok

Az Azure Relay Hibrid kapcsolatok szolgáltatása egy biztonságos, nyílt protokoll szerinti továbbfejlesztése a már létező Relay-funkcióknak, amely bármely platformon, illetve bármely nyelvben alkalmazható. Az Azure Relay Hibrid kapcsolatok szolgáltatása HTTP- és WebSockets-protokollokon alapul. Kérések küldését és a válaszok fogadását teszi Lehetővé WebSockets- vagy HTTP(S)-protokollon keresztül. A szolgáltatás kompatibilis a népszerű böngészők WebSocket API-jaival. 

A Hibrid kapcsolatok protokolljáról részletes információkat a [hibrid kapcsolatok protokoll-útmutatójában](relay-hybrid-connections-protocol.md) talál. A Hibrid kapcsolatok szolgáltatás az összes futtatókörnyezet és nyelv WebSockets-kódtárával kompatibilis.

> [!NOTE]
> Az Azure Relay Hibrid kapcsolatok szolgáltatása a BizTalk Services hasonló nevű korábbi szolgáltatását váltja fel, amely az Azure Service Bus WCF Relay szolgáltatására épült. Az Azure Relay Hibrid kapcsolatok képessége kiegészíti a már meglévő WCF Relay szolgáltatást. E két szolgáltatási képesség (WCF Relay és Hibrid kapcsolatok) a továbbiakban egymás mellett fog működni az Azure Relay szolgáltatásban. E szolgáltatások közös átjáróval rendelkeznek, de ettől eltekintve különböző megvalósításról van szó.

## <a name="wcf-relay"></a>WCF-továbbító
A WCF Relay szolgáltatás a teljes .NET-keretrendszerrel és WCF-fel is használható. A helyszíni és a továbbítási szolgáltatás közötti kapcsolatot egy WCF továbbító kötéskészlet használatával hozhatja létre. A továbbítási kötéseket a Service busszal a felhőben integrálódó WCF-csatornaösszetevők létrehozására tervezett új átviteli kötőelemekké leképezése. További információ: [Ismerkedés a WCF-továbbító szolgáltatással](relay-wcf-dotnet-get-started.md).

## <a name="hybrid-connections-vs-wcf-relay"></a>A Hibrid kapcsolatok és a WCF-továbbító
A Hibrid kapcsolatok és a WCF Relay szolgáltatás egyaránt lehetővé teszi a vállalati hálózaton belüli eszközökhöz való biztonságos csatlakozást. Az Ön igényein múlik, hogy melyik lehetőséget választja, ahogy az az alábbi táblázatban látható:

|  | WCF-továbbító | Hibrid kapcsolatok |
| --- |:---:|:---:|
| **WCF** |x | |
| **.NET Core** | |x |
| **.NET-keretrendszer** |x |x |
| **JavaScript/Node.JS** | |x |
| **Szabványokon alapuló nyílt protokoll** | |x |
| **RPC programozási modellek** | |x |

## <a name="architecture-processing-of-incoming-relay-requests"></a>Architektúra: Bejövő továbbítási kérelmek feldolgozása
Az alábbi ábra azt mutatja be, hogyan kezeli az Azure Relay szolgáltatás a bejövő továbbítási kérelmeket:

![Bejövő WCF Relay továbbítási kérelmek feldolgozása](./media/relay-what-is-it/ic690645.png)

1. A figyelő ügyfél figyelési kérelmet küld az Azure Relay szolgáltatás számára. Az Azure Load Balancer továbbítja a kérelmet valamelyik átjárócsomópontnak. 
2. Az Azure Relay szolgáltatás létrehoz egy továbbítót az átjárótárolóban. 
3. A küldő ügyfél elküld egy kérelmet a figyelő szolgáltatáshoz való csatlakozásra vonatkozóan. 
4. A kérelmet fogadó átjáró megkeresi a továbbítót az átjárótárolóban. 
5. Az átjáró továbbítja a kapcsolódási kérelmet az átjárótárolóban található, megfelelő átjárónak. 
6. Az átjáró kérelmet küld a figyelő ügyfélnek, hogy hozzon létre egy ideiglenes csatornát a küldő ügyfélhez legközelebbi átjárócsomóponthoz. 
7. A figyelő ügyfél létrehozza az ideiglenes csatornát a küldő ügyfélhez legközelebbi átjáróhoz. Most, hogy az ügyfelek között létrejött egy átjárókapcsolat, üzeneteket válthatnak egymással. 
8. Az átjáró továbbítja az üzeneteket a figyelő ügyféltől a küldő ügyfél felé. 
9. Az átjáró továbbítja az üzeneteket a küldő ügyféltől a figyelő ügyfél felé.  

## <a name="next-steps"></a>További lépések
* [Ismerkedés a .NET Websocketek](relay-hybrid-connections-dotnet-get-started.md)
* [Ismerkedés a .NET-HTTP-kérésekkel](relay-hybrid-connections-http-requests-dotnet-get-started.md)
* [Ismerkedés a node.js Websocketek](relay-hybrid-connections-node-get-started.md)
* [Ismerkedés a Node-HTTP-kérésekkel](relay-hybrid-connections-http-requests-node-get-started.md)
* [Relay – gyakori kérdések](relay-faq.md)

