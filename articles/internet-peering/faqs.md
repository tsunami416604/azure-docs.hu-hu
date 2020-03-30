---
title: Internetes társviszony-létesítés - gyakori kérdések
titleSuffix: Azure
description: Internetes társviszony-létesítés - gyakori kérdések
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: reference
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 9b0b2b08e01c99fc918c4bc5649197c9caa4978a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775497"
---
# <a name="internet-peering---faqs"></a>Internetes társviszony-létesítés - gyakori kérdések

Az általános kérdésekért tekintse át az alábbi információkat.

**Mi a különbség az internetes társviszony-létesítés és a társviszony-létesítési szolgáltatás között?**

A társviszony-létesítési szolgáltatás olyan szolgáltatás, amely vállalati szintű nyilvános IP-kapcsolatot kíván biztosítani a Microsoft számára a vállalati ügyfelek számára. A nagyvállalati szintű internetkapcsolat olyan internetszolgáltatókon keresztül izolált kapcsolatot is magában foglal, amelyek nagy átviteli sebességű kapcsolattal rendelkeznek a Microsofttal, és redundanciát biztosítanak a HA-kapcsolathoz. Emellett a felhasználói forgalom van optimalizálva a késés a legközelebbi Microsoft Edge. A társviszony-létesítési szolgáltatás a partnerszolgáltatóval való társviszonyra épül. A partnerrel létesítési kapcsolatnak közvetlen társviszony-létesítésnek kell lennie, nem pedig exchange-társviszony-létesítésnek. A közvetlen társviszony-létesítésnek helyi és georedundanciával kell rendelkeznie.

**Mi az örökölt társviszony-létesítés?**

Az Azure PowerShell használatával beállított társviszony-létesítési kapcsolat Azure-erőforrásként van kezelve. A múltban beállított társviszony-létesítési kapcsolatok a rendszerünkben örökölt társviszony-létesítésként tárolódnak, amelyet azure-erőforrásként kezelhet.

**A New-AzPeeringDirectConnectionObject elnevezésekénél milyen IP-címeket kapnak a Microsoft és a Társeszközök?**

A New-AzPeeringDirectConnectionObject parancsmag hívásakor egy /31-es címet (a.b.c.d/31) vagy /30-as címet (a.b.c.d/30) kell megadni. Az első IP-cím (a.b.c.d+0) a Társ eszközének, a második IP-címe (a.b.c.d+1) pedig a Microsoft-eszköznek van megadva.

**Mi a MaxPrefixesAdvertisedIPv4 és A MaxPrefixesAdvertisedIPv6 paraméter e New-AzPeeringDirectConnectionObject parancsmagban?**

A MaxPrefixesAdvertisedIPv4 és a MaxPrefixesAdvertisedIPv6 paraméterek az IPv4 és az IPv6-előtagok maximális számát jelentik, amelyeket a Társ a Microsoftáltal el kell fogadnia. Ezek a paraméterek bármikor módosíthatók.