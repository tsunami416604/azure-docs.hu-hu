<properties
    pageTitle="A Service Bus továbbítón keresztüli üzenetcsere áttekintése | Microsoft Azure"
    description="A Service Bus Relay áttekintése."
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="get-started-article"
    ms.date="05/06/2016"
    ms.author="sethm"/>


# A Service Bus továbbítón keresztüli üzenetcsere

A Service Bus központi összetevője egy központosított (de erősen elosztott terhelésű) továbbítási szolgáltatás, amely lehetővé teszi hibrid alkalmazások összeállítását, amelyek egy Azure-adatközpontban és a saját helyszíni vállalati környezetben is futnak.  A továbbítási szolgáltatás számos különböző átviteli protokollt és webszolgáltatási szabványt támogat. Ezek közé tartozik például a SOAP, a WS-* és a REST is. A Service Bus Relay úgy segíti a hibrid alkalmazásait, hogy biztonságosan lehetővé teszi a vállalati hálózaton belüli Windows Communication Foundation (WCF-) szolgáltatások közzétételét a nyilvános felhőben anélkül, hogy meg kellene nyitni egy tűzfalkapcsolatot, vagy zavaró módosításokat kellene végrehajtani a vállalati hálózati infrastruktúrában. 

![A Relay szolgáltatással kapcsolatos fogalmak](./media/service-bus-relay-overview/sb-relay-01.png)

A továbbítási szolgáltatás támogatja a hagyományos egyirányú, a kérelem/válasz típusú és a társközi üzenetkezelést. Támogatja az események terjesztését is az internetes tartományban, így a pontok közötti nagyobb hatékonyság érdekében lehetővé válnak a közzétételi/előfizetési forgatókönyvek és a kétirányú szoftvercsatornás kommunikáció. 

A továbbítón keresztüli üzenetcsere mintában a helyszíni szolgáltatás csatlakozik a továbbítási szolgáltatáshoz egy kimenő porton keresztül, majd létrehoz egy adott szinkronizálási címhez kötött kétirányú szoftvercsatornát a kommunikációhoz. Az ügyfél ezután kommunikálhat a helyszíni szolgáltatással, ha a szinkronizálási címet megcélozva üzeneteket küld a továbbítási szolgáltatásnak. A továbbítási szolgáltatás ezután továbbítja az üzeneteket a helyszíni szolgáltatásra a már létrejött kétirányú szoftvercsatornán keresztül. Az ügyfélnek nincs szüksége közvetlen kapcsolatra a helyszíni szolgáltatással, és azt sem kell tudnia, hol található, a helyszíni szolgáltatásnak pedig nincs szüksége megnyitott bejövő portra a tűzfalon.

A kapcsolatot a helyszíni és a továbbítási szolgáltatás között kezdeményezheti egy WCF továbbító kötéskészlet használatával. A színfalak mögött a továbbítási kötéseket a rendszer a Service Busszal a felhőben integrálódó WCF-csatornaösszetevők létrehozására tervezett új átviteli kötőelemekké képezi le. 

## Következő lépések

A Service Bus Relayjel kapcsolatos részletekért lásd az alábbi témaköröket.

- [Azure Service Bus architectural overview (Az Azure Service Bus architektúrájának áttekintése)](service-bus-fundamentals-hybrid-solutions.md)
- [A Service Bus Relay szolgáltatás használata](service-bus-dotnet-how-to-use-relay.md)

 


<!--HONumber=Jun16_HO2-->


