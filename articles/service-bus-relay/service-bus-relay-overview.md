---
title: "A Service Bus Relay áttekintése | Microsoft Docs"
description: "A Service Bus Relay áttekintése."
services: service-bus
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 1038a2d8-5def-4f48-8703-cb0070fc5f10
ms.service: service-bus
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: get-started-article
ms.date: 09/01/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0482096cbec6a5e4b7b13ea662a180cd9b96e85f


---
# <a name="overview-of-service-bus-relay"></a>A Service Bus Relay áttekintése
A Service Bus fő összetevője egy központosított (de erősen elosztott terhelésű) *továbbítási* szolgáltatás, amely lehetővé teszi olyan hibrid alkalmazások összeállítását, amelyek egy Azure-adatközpontban és a saját helyszíni vállalati környezetben is futnak.  A Service Bus Relay számos különböző átviteli protokollt és webszolgáltatási szabványt támogat. Ezek közé tartozik például a SOAP, a WS-* és a REST is. A továbbítási szolgáltatás úgy segíti a hibrid alkalmazásait, hogy biztonságosan lehetővé teszi a vállalati hálózaton belüli Windows Communication Foundation (WCF-) szolgáltatások közzétételét a nyilvános felhőben anélkül, hogy meg kellene nyitni egy tűzfalkapcsolatot, vagy zavaró módosításokat kellene végrehajtani a vállalati hálózati infrastruktúrában. 

![A WCF Relay szolgáltatással kapcsolatos fogalmak](./media/service-bus-relay-overview/sb-relay-01.png)

A továbbítási szolgáltatás támogatja a hagyományos egyirányú, a kérelem/válasz típusú és a társközi üzenetkezelést. Támogatja az események terjesztését is az internetes tartományban, így a pontok közötti nagyobb hatékonyság érdekében lehetővé válnak a közzétételi/előfizetési forgatókönyvek és a kétirányú szoftvercsatornás kommunikáció. 

A továbbítón keresztüli üzenetcsere mintában a helyszíni szolgáltatás csatlakozik a továbbítási szolgáltatáshoz egy kimenő porton keresztül, majd létrehoz egy adott szinkronizálási címhez kötött kétirányú szoftvercsatornát a kommunikációhoz. Az ügyfél ezután kommunikálhat a helyszíni szolgáltatással, ha a szinkronizálási címet megcélozva üzeneteket küld a továbbítási szolgáltatásnak. A továbbítási szolgáltatás ezután továbbítja az üzeneteket a helyszíni szolgáltatásra a már létrejött kétirányú szoftvercsatornán keresztül. Az ügyfélnek nincs szüksége közvetlen kapcsolatra a helyszíni szolgáltatással, és azt sem kell tudnia, hol található, a helyszíni szolgáltatásnak pedig nincs szüksége megnyitott bejövő portra a tűzfalon.

A kapcsolatot a helyszíni és a továbbítási szolgáltatás között kezdeményezheti egy WCF továbbító kötéskészlet használatával. A színfalak mögött a továbbítási kötéseket a rendszer a Service Busszal a felhőben integrálódó WCF-csatornaösszetevők létrehozására tervezett új átviteli kötőelemekké képezi le. 

## <a name="next-steps"></a>Következő lépések
A Service Bus Relayjel kapcsolatos részletekért lásd az alábbi témaköröket.

* [Azure Service Bus architectural overview](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md) (Az Azure Service Bus architektúrájának áttekintése)
* [How to use the Service Bus WCF Relay service](service-bus-dotnet-how-to-use-relay.md) (A Service Bus WCF Relay szolgáltatás használata)




<!--HONumber=Nov16_HO2-->


