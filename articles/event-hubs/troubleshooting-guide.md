---
title: Hibaelhárítási útmutató – Azure Event Hubs | Microsoft Docs
description: Ez a cikk az Azure Event Hubs üzenetkezelési kivételek és a javasolt műveletek listáját tartalmazza.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 01/16/2020
ms.author: shvija
ms.openlocfilehash: ab3cbdf938409f4eacffa10ae5cb20f8c36b5856
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124669"
---
# <a name="azure-event-hubs---troubleshooting-guide"></a>Azure Event Hubs – hibaelhárítási útmutató
Ez a cikk az Azure EventHubs használata során esetlegesen felmerülő problémákkal kapcsolatos hibaelhárítási tippeket és javaslatokat tartalmaz.

## <a name="connectivity-certificate-or-timeout-issues"></a>Kapcsolati, tanúsítvány-vagy időtúllépési problémák
A következő lépések segítséget nyújthatnak a kapcsolat/tanúsítvány/időtúllépési problémák hibaelhárításához a *. servicebus.windows.net alatti összes szolgáltatáshoz. 

- Tallózással keresse meg a következőt: vagy a [wget](https://www.gnu.org/software/wget/) `https://<yournamespacename>.servicebus.windows.net/` . Segít ellenőrizni, hogy rendelkezik-e IP-szűréssel, illetve virtuális hálózati vagy tanúsítványlánc-problémákkal (a Java SDK használatakor leggyakrabban).

    Példa a sikeres üzenetre:
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    Egy példa a hiba hibaüzenetére:

    ```json
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- A következő parancs futtatásával ellenőrizze, hogy a tűzfal blokkolja-e a portokat. A használt portok a következők: 443 (HTTPS), 5671 (AMQP) és 9093 (Kafka). A használt könyvtártól függően más portok is használatban vannak. Itt látható a minta parancs, amely azt vizsgálja, hogy a 5671-es port blokkolva van-e.

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    Linux rendszeren:

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- Időnkénti kapcsolódási problémák esetén futtassa az alábbi parancsot, és ellenőrizze, hogy vannak-e eldobott csomagok. Ezzel a paranccsal a szolgáltatással 1 másodpercenként 25 különböző TCP-kapcsolatot kell létrehozni. Ezt követően megtekintheti, hogy a sikeres és sikertelen volt-e a TCP-kapcsolatok késése. Az `psping` eszközt [innen](/sysinternals/downloads/psping)töltheti le.

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespacename>.servicebus.windows.net:5671 -nobanner     
    ```
    Ha más eszközöket (például `tnc` , stb.) használ, használhatja az egyenértékű parancsokat `ping` . 
- Szerezze be a hálózati nyomkövetést, ha az előző lépések nem segítenek és nem elemzik olyan eszközökkel, mint például a [Wireshark](https://www.wireshark.org/). Ha szükséges, forduljon a [Microsoft ügyfélszolgálatahoz](https://support.microsoft.com/) . 

## <a name="issues-that-may-occur-with-service-upgradesrestarts"></a>A szolgáltatás verziófrissítése/újraindítása esetén felmerülő problémák
A háttérbeli szolgáltatás verziófrissítése és újraindítása a következő hatással lehet az alkalmazásokra:

- Előfordulhat, hogy a kérelmek egy pillanatra szabályozva vannak.
- Lehet, hogy elvesznek a bejövő üzenetek/kérelmek.
- A naplófájl hibaüzeneteket tartalmazhat.
- Előfordulhat, hogy az alkalmazások néhány másodpercig le lesznek választva a szolgáltatástól.

Ha az alkalmazás kódja az SDK-t használja, az újrapróbálkozási házirend már be van építve és aktív. Az alkalmazás az alkalmazás/munkafolyamat jelentős hatása nélkül újra csatlakozik.


## <a name="next-steps"></a>További lépések

Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Event Hubs áttekintése](event-hubs-what-is-event-hubs.md)
* [Event hub létrehozása](event-hubs-create.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)
