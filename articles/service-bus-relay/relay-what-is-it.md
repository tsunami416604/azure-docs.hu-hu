---
title: "Mi az Azure Relay, és mire használható? – áttekintés | Microsoft Docs"
description: "Az Azure Relay áttekintése"
services: service-bus-relay
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 1e3e971d-2a24-4f96-a88a-ce3ea2b1a1cd
ms.service: service-bus-relay
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: get-started-article
ms.date: 03/09/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 9fd40892c77630bd4f0b7abf5c3458a6dc200402
ms.contentlocale: hu-hu
ms.lasthandoff: 03/09/2017


---
# <a name="what-is-azure-relay"></a>Mi az az Azure Relay?
Az Azure Relay szolgáltatás úgy segíti a hibrid alkalmazásokat, hogy biztonságosan lehetővé teszi a vállalati hálózaton belüli szolgáltatások közzétételét a nyilvános felhőben anélkül, hogy meg kellene nyitni egy tűzfalkapcsolatot, vagy zavaró módosításokat kellene végrehajtani a vállalati hálózati infrastruktúrában. A Relay számos különböző átviteli protokollt és webszolgáltatási szabványt támogat.

A továbbítási szolgáltatás támogatja a hagyományos egyirányú, a kérelem/válasz típusú és a társközi forgalmat. Támogatja az események terjesztését is az internetes tartományban, így a pontok közötti nagyobb hatékonyság érdekében lehetővé válnak a közzétételi/előfizetési forgatókönyvek és a kétirányú szoftvercsatornás kommunikáció. 

A továbbítón keresztüli adatátviteli mintában a helyszíni szolgáltatás csatlakozik a továbbítási szolgáltatáshoz egy kimenő porton keresztül, majd létrehoz egy adott szinkronizálási címhez kötött kétirányú szoftvercsatornát a kommunikációhoz. Az ügyfél ezután kommunikálhat a helyszíni szolgáltatással, ha a szinkronizálási címet megcélozva forgalmat küld a továbbítási szolgáltatásnak. A továbbítási szolgáltatás ezután továbbítja az adatokat a helyszíni szolgáltatásba az egyes ügyfelek számára kijelölt kétirányú szoftvercsatornán keresztül. Az ügyfélnek nincs szüksége közvetlen kapcsolatra a helyszíni szolgáltatással, és azt sem kell tudnia, hol található, a helyszíni szolgáltatásnak pedig nincs szüksége megnyitott bejövő portra a tűzfalon.

A Relay által biztosított kulcsfontosságú képesség a TPC-szerű szabályozással, végpontészleléssel, kapcsolati állapottal és átfedő végpontvédelemmel rendelkező, hálózati határok között történő kétirányú, nem pufferelt kommunikációban rejlik. A továbbítási képességek abban térnek el az olyan hálózati szintű integrációs technológiáktól mint például a VPN, hogy a továbbítás egyetlen alkalmazásvégpontra is fókuszálható a gépen, míg a VPN technológia ennél jóval agresszívabb, hiszen a hálózati környezet módosítására támaszkodik.

Az Azure Relay két funkciója:

1. [Hibrid kapcsolatok](#hybrid-connections) – A többplatformos forgatókönyveket lehetővé tevő WebSockets nyílt szabványt használja.
2. [WCF-továbbítók](#wcf-relays) – A Windows Communication Foundation (WCF) használatával engedélyezi a távoli eljáráshívásokat. A WCF-továbbító egy olyan örökölt Relay-ajánlat, amelyet számos ügyfél használhat már a WCF programozási modelljeivel.

A Hibrid kapcsolatok és a WCF-továbbítók egyaránt lehetővé teszik a vállalati hálózaton belüli eszközökhöz való biztonságos csatlakozást. Az Ön igényein múlik, hogy melyik lehetőséget választja, ahogy az az alábbi táblázatban látható:

|  | WCF-továbbító | Hibrid kapcsolatok |
| --- |:---:|:---:|
| **WCF** |x | |
| **.NET Core** | |x |
| **.NET-keretrendszer** |x |x |
| **JavaScript/NodeJS** | |x |
| **Szabvány-alapú nyílt protokoll** | |x |
| **Többszörös RPC programozási modellek** | |x |

## <a name="hybrid-connections"></a>Hibrid kapcsolatok
Az [Azure Relay Hibrid kapcsolatok képesség](relay-hybrid-connections-protocol.md) egy biztonságos, nyílt protokoll szerinti továbbfejlesztése a létező továbbítófunkcióknak, amely bármely platformon, illetve bármely olyan nyelvben is alkalmazható, amely rendelkezik alapszintű WebSocket képességgel, ha kifejezetten tartalmazza a WebSocket API-t a gyakori internetes böngészőkben. A Hibrid kapcsolatok a HTTP-n és a WebSockets szabványon alapul.

## <a name="wcf-relays"></a>WCF-továbbítók
A WCF-továbbító a teljes .NET-keretrendszer (NETFEX) és a WCF esetén működik. A kapcsolatot a helyszíni és a továbbítási szolgáltatás között kezdeményezheti egy WCF továbbító kötéskészlet használatával. A színfalak mögött a továbbítási kötéseket a rendszer a Service Busszal a felhőben integrálódó WCF-csatornaösszetevők létrehozására tervezett új átviteli kötőelemekké képezi le.

## <a name="service-history"></a>Szolgáltatási előzmények
A Hibrid kapcsolatok pótolja a korábbi, hasonló nevű „BizTalk Services” funkciót, amely az Azure Service Bus WCF-továbbítóra épült. Az új Hibrid kapcsolatok képesség kiegészíti a már meglévő WCF-továbbítót, és a belátható jövőben a két szolgáltatási képesség egymás mellett fog működni a Relay szolgáltatásban. E szolgáltatások közös átjáróval rendelkeznek, de ettől eltekintve különböző megvalósításról van szó.

## <a name="next-steps"></a>Következő lépések:
* [Relay – gyakori kérdések](relay-faq.md)
* [Névtér létrehozása](relay-create-namespace-portal.md)
* [Ismerkedés a .NET-tel](relay-hybrid-connections-dotnet-get-started.md)
* [Bevezetés a Node használatába](relay-hybrid-connections-node-get-started.md)


