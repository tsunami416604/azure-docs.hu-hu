---
title: Az IoT hub IP-címének ismertetése | Microsoft dokumentumok
description: Ismerje meg, hogyan lehet lekérdezni az IoT hub IP-címét és tulajdonságait. Az IoT hub IP-címe bizonyos forgatókönyvek, például a vész-helyreállítási vagy regionális feladatátvétel során változhat.
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: c609f2a3843481442e97061739a806de60a680b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367567"
---
# <a name="iot-hub-ip-addresses"></a>IoT Hub IP-címei

Az IoT Hub nyilvános végpontjainak IP-címelőegyesei rendszeresidőközönként közzévannak téve az _AzureIoTHub_ [szolgáltatáscímke](../virtual-network/service-tags-overview.md)alatt.

> [!NOTE]
> A helyszíni hálózatokon belül üzembe helyezett eszközök esetében az Azure IoT Hub támogatja a virtuális hálózat és a magánvégpontok virtuális hálózati kapcsolatának integrálását. További információkért tekintse meg [az IoT Hub támogatását](./virtual-network-support.md#ingress-connectivity-to-iot-hub-using-private-endpoints) a Virtuális hálózatokhoz.


Ezekkel az IP-címelőtagokkal vezérelheti az IoT Hub és az eszközök vagy hálózati eszközök közötti kapcsolatot a különböző hálózati elkülönítési célok megvalósítása érdekében:

| Cél | Alkalmazható forgatókönyvek | Módszer |
|------|-----------|----------|
| Győződjön meg arról, hogy az eszközök és szolgáltatások csak az IoT Hub-végpontokkal kommunikálnak | [Eszközről-felhőre](./iot-hub-devguide-messaging.md), és [felhőből az eszközre](./iot-hub-devguide-messages-c2d.md) üzenetek, [közvetlen módszerek,](./iot-hub-devguide-direct-methods.md) [eszköz és modul ikrek](./iot-hub-devguide-device-twins.md) és eszköz [streamek](./iot-hub-device-streams-overview.md) | Az _AzureIoTHub_ és _az EventHub_ szolgáltatáscímkék használatával felderítheti az IoT Hub és az Event Hub IP-címének előtagjait, és konfigurálhatja az ALLOW-szabályokat az eszközök és a szolgáltatások tűzfalbeállításaiban az IP-címelőtagokhoz; forgalmat más cél IP-címekre, amelyekkel nem szeretné, hogy az eszközök vagy szolgáltatások kommunikáljanak. |
| Győződjön meg arról, hogy az IoT Hub-eszközvégpont csak az eszközökről és a hálózati eszközökről kap kapcsolatokat | [Eszközről-felhőre](./iot-hub-devguide-messaging.md), és [felhőből az eszközre](./iot-hub-devguide-messages-c2d.md) üzenetek, [közvetlen módszerek,](./iot-hub-devguide-direct-methods.md) [eszköz és modul ikrek](./iot-hub-devguide-device-twins.md) és eszköz [streamek](./iot-hub-device-streams-overview.md) | Az IoT Hub [IP-szűrőszolgáltatásával](iot-hub-ip-filtering.md) engedélyezheti az eszközökről és a hálózati eszköz IP-címeiből érkező kapcsolatokat (lásd a [korlátozásokat).](#limitations-and-workarounds) | 
| Győződjön meg arról, hogy az útvonalak egyéni végponterőforrásai (tárfiókok, szolgáltatásbusz és eseményközpontok) csak a hálózati eszközökről érhetőek el | [Üzenetek útválasztása](./iot-hub-devguide-messages-d2c.md) | Kövesse az erőforrás nak a kapcsolat korlátozására vonatkozó útmutatását (például [tűzfalszabályok,](../storage/common/storage-network-security.md) [privát kapcsolatok](../private-link/private-endpoint-overview.md)vagy [szolgáltatásvégpontok segítségével);](../virtual-network/virtual-network-service-endpoints-overview.md) _az AzureIoTHub_ szolgáltatáscímkék használatával felderítheti az IoT Hub IP-címelőegyeseit, és allow-szabályokat adhat hozzá az erőforrás tűzfalkonfigurációjában lévő IP-előtagokhoz (lásd a [korlátozásokat).](#limitations-and-workarounds) |



## <a name="best-practices"></a>Ajánlott eljárások

* Ha allow szabályokat ad hozzá az eszközök tűzfalának konfigurációjában, a legjobb, ha a [megfelelő protokollok által használt portokat](./iot-hub-devguide-protocols.md#port-numbers)biztosít.

* Az IoT hub IP-cím előtagai változhatnak. Ezeket a módosításokat a rendszer a hatálybalépés előtt rendszeresen közzéteszi a szolgáltatáscímkéken keresztül. Ezért fontos, hogy olyan folyamatokat dolgozzon ki, amelyek rendszeresen lekérik és használják a legújabb szolgáltatáscímkéket. Ez a folyamat automatizálható a [szolgáltatáscímkék felderítési API-n](../virtual-network/service-tags-overview.md#service-tags-on-premises)keresztül. Vegye figyelembe, hogy a Szolgáltatás címkék felderítési API továbbra is előzetes verzióban, és bizonyos esetekben nem termela címkék és IP-címek teljes listáját. Amíg a felderítési API általánosan elérhető, fontolja meg a [szolgáltatáscímkék letölthető JSON formátumban](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files). 

* Használja az *AzureIoTHub.[ régió név]* címke az IoT hub végpontjai által egy adott régióban használt IP-előtagok azonosítására. Az adatközpont vész-helyreállítási vagy [regionális feladatátvételi](iot-hub-ha-dr.md) győződjön meg arról, hogy az IoT Hub földrajzi pár régiójának IP-előtagokkal való kapcsolata is engedélyezve van.

* Tűzfalszabályok beállítása az IoT Hub ban blokkolhatja az Azure CLI és a PowerShell-parancsok ioT Hub on-t futtatni. Ennek elkerülése érdekében az ügyfelek IP-címelőtagjaihoz adhat hozzá ALLOW-szabályokat, hogy újra engedélyezze a CLI- vagy PowerShell-ügyfelek et az IoT Hubbal való kommunikációhoz.  


## <a name="limitations-and-workarounds"></a>Korlátozások és kerülő megoldások

* Az IoT Hub IP-szűrőszolgáltatása legfeljebb 10 szabály. Ez a korlát az Azure ügyfélszolgálatán keresztül kérésekkel emelhető ki. 

* A konfigurált [IP-szűrési szabályok](iot-hub-ip-filtering.md) csak az IoT Hub IP-végpontokon, és nem az IoT hub beépített Event Hub-végpont. Ha azt is megköveteli, hogy az IP-szűrés az üzeneteket tároló eseményközpontban legyen alkalmazva, akkor a saját Event Hub-erőforrást is létrehozhatja, ahol közvetlenül konfigurálhatja a kívánt IP-szűrési szabályokat. Ehhez ki kell építenie a saját Event Hub-erőforrást, és be kell állítania [az üzenet-útválasztást,](./iot-hub-devguide-messages-d2c.md) hogy az IoT Hub beépített Event Hub-ja helyett az adott erőforrásnak küldhesse az üzeneteket. Végül, amint azt a fenti táblázatban, az üzenet-útválasztási funkció engedélyezéséhez is engedélyeznie kell a kapcsolatot az IoT Hub IP-címelőtagai a kiépített Event Hub erőforrás.

* Ha egy tárfiókba, az IoT Hub IP-címelőtagjaiból érkező forgalom engedélyezése csak akkor lehetséges, ha a tárfiók egy másik régióban, mint az IoT Hub.

## <a name="support-for-ipv6"></a>Az IPv6 támogatása 

Az IoT Hub jelenleg nem támogatott az IPv6.
