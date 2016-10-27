<properties
    pageTitle="A Service Bus Relay áttekintése | Microsoft Azure"
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
    ms.date="09/01/2016"
    ms.author="sethm"/>



# <a name="overview-of-service-bus-relay"></a>A Service Bus Relay áttekintése

A Service Bus fő összetevője egy központosított (de erősen elosztott terhelésű) *továbbítási* szolgáltatás, amely lehetővé teszi olyan hibrid alkalmazások összeállítását, amelyek egy Azure-adatközpontban és a saját helyszíni vállalati környezetben is futnak.  A Service Bus Relay számos különböző átviteli protokollt és webszolgáltatási szabványt támogat. Ezek közé tartozik például a SOAP, a WS-* és a REST is. A továbbítási szolgáltatás úgy segíti a hibrid alkalmazásait, hogy biztonságosan lehetővé teszi a vállalati hálózaton belüli Windows Communication Foundation (WCF-) szolgáltatások közzétételét a nyilvános felhőben anélkül, hogy meg kellene nyitni egy tűzfalkapcsolatot, vagy zavaró módosításokat kellene végrehajtani a vállalati hálózati infrastruktúrában. 

![A Relay szolgáltatással kapcsolatos fogalmak](./media/service-bus-relay-overview/sb-relay-01.png)

A továbbítási szolgáltatás támogatja a hagyományos egyirányú, a kérelem/válasz típusú és a társközi üzenetkezelést. Támogatja az események terjesztését is az internetes tartományban, így a pontok közötti nagyobb hatékonyság érdekében lehetővé válnak a közzétételi/előfizetési forgatókönyvek és a kétirányú szoftvercsatornás kommunikáció. 

A továbbítón keresztüli üzenetcsere mintában a helyszíni szolgáltatás csatlakozik a továbbítási szolgáltatáshoz egy kimenő porton keresztül, majd létrehoz egy adott szinkronizálási címhez kötött kétirányú szoftvercsatornát a kommunikációhoz. Az ügyfél ezután kommunikálhat a helyszíni szolgáltatással, ha a szinkronizálási címet megcélozva üzeneteket küld a továbbítási szolgáltatásnak. A továbbítási szolgáltatás ezután továbbítja az üzeneteket a helyszíni szolgáltatásra a már létrejött kétirányú szoftvercsatornán keresztül. Az ügyfélnek nincs szüksége közvetlen kapcsolatra a helyszíni szolgáltatással, és azt sem kell tudnia, hol található, a helyszíni szolgáltatásnak pedig nincs szüksége megnyitott bejövő portra a tűzfalon.

A kapcsolatot a helyszíni és a továbbítási szolgáltatás között kezdeményezheti egy WCF továbbító kötéskészlet használatával. A színfalak mögött a továbbítási kötéseket a rendszer a Service Busszal a felhőben integrálódó WCF-csatornaösszetevők létrehozására tervezett új átviteli kötőelemekké képezi le. 

## <a name="next-steps"></a>Következő lépések

A Service Bus Relayjel kapcsolatos részletekért lásd az alábbi témaköröket.

- [Azure Service Bus architectural overview](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md) (Az Azure Service Bus architektúrájának áttekintése)
- [How to use the Service Bus Relay service](service-bus-dotnet-how-to-use-relay.md) (A Service Bus Relay szolgáltatás használata)

 


<!--HONumber=Oct16_HO3-->


