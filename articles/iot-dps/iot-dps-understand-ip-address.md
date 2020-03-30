---
title: Az IoT-eszközlétesítési szolgáltatás (DPS) példányának IP-címének ismertetése | Microsoft dokumentumok
description: Ismerje meg, hogyan lehet lekérdezni az IoT-eszköz létesítési szolgáltatás (DPS) címét és tulajdonságait. A DPS-példány IP-címe bizonyos forgatókönyvek, például a vész-helyreállítási vagy regionális feladatátvétel során változhat.
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: f6afd5c4cc5aa0215f943979ae91389b39d449f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284927"
---
# <a name="iot-hub-dps-ip-addresses"></a>IoT Hub DPS IP-címek

Az IoT Hub-eszközlétesítési szolgáltatás (DPS) nyilvános végpontjainak IP-címelőegyesei rendszeres időközönként közzé vannak téve az _AzureIoTHub_ [szolgáltatáscímke](../virtual-network/service-tags-overview.md)alatt. Ezekkel az IP-címelőtagokkal szabályozhatja az IoT DPS-példányok és az eszközök vagy hálózati eszközök közötti kapcsolatot a különböző hálózati elkülönítési célok megvalósítása érdekében:

| Cél | Módszer |
|------|----------|
| Győződjön meg arról, hogy az eszközök és szolgáltatások csak az IoT Hub DPS-végpontokkal kommunikálnak | Az _AzureIoTHub_ szolgáltatáscímke segítségével fedezze fel az IoT Hub DPS-példányokat. Konfigurálja az ALLOW-szabályokat az eszközök és a szolgáltatások tűzfal-beállításán az IP-címelőtagokhoz. Állítsa be a szabályokat úgy, hogy a forgalmat más cél IP-címekre bonyolítsa, amelyekkel nem szeretné, hogy az eszközök vagy szolgáltatások kommunikáljanak. |
| Győződjön meg arról, hogy az IoT Hub DPS-végpontja csak az eszközökről és a hálózati eszközökről kap kapcsolatokat | Az IoT DPS [IP-szűrő szolgáltatás használatával](iot-dps-ip-filtering.md) szűrőszabályokat hozhat létre az eszközhöz és a DPS-szolgáltatás API-ihoz. Ezek a szűrőszabályok csak az eszközökről és a hálózati eszköz IP-címeiről engedélyezik a kapcsolatokat (lásd a [korlátozásokat).](#limitations-and-workarounds) | 




## <a name="best-practices"></a>Ajánlott eljárások

* Ha allow szabályokat ad hozzá az eszközök tűzfalának konfigurációjában, a legjobb, ha a [megfelelő protokollok által használt portokat](../iot-hub/iot-hub-devguide-protocols.md#port-numbers)biztosít.

* Az IoT DPS-példányok IP-címelőbiztosei változhatnak. Ezeket a módosításokat a rendszer a hatálybalépés előtt rendszeresen közzéteszi a szolgáltatáscímkéken keresztül. Ezért fontos, hogy olyan folyamatokat dolgozzon ki, amelyek rendszeresen lekérik és használják a legújabb szolgáltatáscímkéket. Ez a folyamat automatizálható a [szolgáltatáscímkék felderítési API-n](../virtual-network/service-tags-overview.md#service-tags-on-premises)keresztül. A Szolgáltatás címkék felderítési API továbbra is előzetes verzióban, és bizonyos esetekben előfordulhat, hogy nem hozza létre a címkék és IP-címek teljes listáját. Amíg a felderítési API általánosan elérhető, fontolja meg a [szolgáltatáscímkék letölthető JSON formátumban](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files). 

* Használja az *AzureIoTHub.[ régió név]* címke a DPS-végpontok által egy adott régióban használt IP-előtagok azonosítására. Az adatközpontvész-helyreállítási vagy [regionális feladatátvételi](../iot-hub/iot-hub-ha-dr.md)figyelembe vétele érdekében győződjön meg arról, hogy a DPS-példány földrajzi párrégiójának IP-előtagokkal való kapcsolata is engedélyezve van.

* A DPS-példány okai között tűzfalszabályok beállítása megakadályozhatja az Azure CLI és a PowerShell-parancsok futtatásához szükséges kapcsolatot. Ezek a kapcsolódási problémák elkerülése érdekében az ügyfelek IP-címelőtagaival engedélyezheti az ALLOW-szabályokat, hogy újra engedélyezze a CLI- vagy PowerShell-ügyfelek számára a DPS-példányokkal való kommunikációt.  


## <a name="limitations-and-workarounds"></a>Korlátozások és kerülő megoldások

* A DPS IP-szűrő szolgáltatás a legfeljebb 100 szabályt. Ez a korlát az Azure ügyfélszolgálatán keresztül kérésekkel emelhető ki. 

* A konfigurált [IP-szűrési szabályok](iot-dps-ip-filtering.md) csak a DPS-végpontokon vannak alkalmazva, a csatolt IoT Hub-végpontokon nem. A csatolt IoT-központok IP-szűrését külön kell konfigurálni. További információ: [IoT Hub IP-szűrési szabályok.](../iot-hub/iot-hub-ip-filtering.md)

## <a name="support-for-ipv6"></a>Az IPv6 támogatása 

Az IoT Hub vagy a DPS jelenleg nem támogatott Az IPv6 jelenleg nem támogatott.

## <a name="next-steps"></a>További lépések

Ha többet szeretne tudni a DPS protokollal kapcsolatos IP-címkonfigurációkról, olvassa el a következő témaköröket:

* [IP-szűrés konfigurálása](iot-dps-ip-filtering.md)
