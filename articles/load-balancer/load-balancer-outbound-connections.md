---
title: Kimenő proxy Azure Load Balancer
description: Ismerteti, hogyan használható a Azure Load Balancer proxyként a kimenő internetkapcsolathoz
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperfq1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: 422f8106ac52c85f0680d54e420d0f1b4d326910
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/14/2020
ms.locfileid: "92017692"
---
# <a name="outbound-proxy-azure-load-balancer"></a>Kimenő proxy Azure Load Balancer

Az Azure Load Balancer proxyként is használható a kimenő internetkapcsolathoz. A terheléselosztó biztosítja a kimenő kapcsolatot a háttérbeli példányok számára. 

Ez a konfiguráció a **forrás hálózati címfordítást (SNAT)** használja. A SNAT újraírja a háttér IP-címét a terheléselosztó nyilvános IP-címére. 

A SNAT lehetővé teszi a háttér **-példány IP-címének maszkolását** . Ez a maszkolás megakadályozza, hogy a külső források közvetlenül a háttérbeli példányokhoz legyenek letiltva. 

A háttérbeli példányok közötti IP-cím megosztása csökkenti a statikus nyilvános IP-címek költségeit, és olyan forgatókönyveket támogat, mint például az IP-címek engedélyezése az ismert nyilvános IP-címekről érkező forgalomhoz. 

## <a name="sharing-ports-across-resources"></a><a name ="snat"></a> Portok megosztása az erőforrások között

Ha egy terheléselosztó háttér-erőforrásai nem rendelkeznek ILPIP-címmel, a nyilvános terheléselosztó előtérbeli IP-címén keresztül hozhatnak létre kimenő kapcsolatokat.

A portok a különböző folyamatok fenntartásához használt egyedi azonosítók előállítására szolgálnak. Az Internet egy öt rekord használatával biztosítja ezt a különbséget.

Az öt rekord a következőkből áll:

* Cél IP-címe
* Célport
* Forrás IP-címe
* A forrás portszáma és a protokoll a különbségtétel biztosításához.

Ha a bejövő kapcsolatokhoz portot használ, akkor a bejövő kapcsolatokra vonatkozó kérelmeket **figyelő** a porton, és nem használható kimenő kapcsolatokhoz. 

A kimenő kapcsolatok létrehozásához egy **ideiglenes portot** kell használni, amely lehetővé teszi, hogy a cél egy olyan porton keresztül legyen elérhető, amelyen keresztül kommunikálni és karbantartani kell a különböző forgalmi folyamatokat. 

Minden IP-címnek 65 535 porttal rendelkezik. Az első 1024-portok **rendszerportként**vannak lefoglalva. Az egyes portok a TCP és az UDP bejövő vagy kimenő kapcsolataihoz is használhatók. 

A fennmaradó portok közül az Azure a 64 000-as időszakos **portok**használatát teszi lehetővé. Amikor IP-címet ad hozzá az előtérbeli IP-konfigurációként, ezek az ideiglenes portok a SNAT használhatók.

A kimenő szabályok révén ezek a SNAT-portok kiterjeszthetők a háttérbeli példányokra, így a kimenő kapcsolatok esetében a terheléselosztó nyilvános IP-címe (i) is megoszthatók.

A gazdagépen az egyes backend-példányokhoz tartozó hálózatkezelés a kimenő kapcsolat részét képező csomagokra SNAT. A gazdagép újraírja a forrás IP-címet az egyik nyilvános IP-címhez. A gazdagép újraírja az egyes kimenő csomagok forrás portját az egyik SNAT-portra.

## <a name="exhausting-ports"></a><a name="scenarios"></a> Kimerített portok

A célként megadott IP-címhez és célporthoz való minden kapcsolódás SNAT-portot fog használni. Ez a kapcsolódás külön **forgalmat** tart fenn a háttér-példány vagy az **ügyfél** között egy **kiszolgálóra**. Ez a folyamat egy különálló portot ad a kiszolgálónak, amely a forgalom kezelésére szolgál. A folyamat nélkül az ügyfélszámítógép nem ismeri a csomag részét képező adatfolyamot.

Képzelje el, hogy több böngésző https://www.microsoft.com is van, ami a következő:

* Cél IP-címe = 23.53.254.142
* Célport = 443
* Protokoll = TCP

Különböző célport nélkül a visszaadott forgalomhoz (a kapcsolat létrehozásához használt SNAT-port) az ügyfélnek nincs lehetősége arra, hogy egy lekérdezési eredményt válasszon egy másikból.

A kimenő kapcsolatok feltörtek. A háttér-példányok nem rendelkeznek elegendő porttal. Ha nincs engedélyezve a **kapcsolatok újrafelhasználása** , a SNAT- **portok kimerülésének** kockázata megnő.

A célként megadott IP-címhez tartozó új kimenő kapcsolatok sikertelenek lesznek, ha a portok kimerülése történik. Ha egy port elérhetővé válik, a kapcsolatok sikeresek lesznek. Ez a kimerültség akkor fordul elő, ha az IP-címről érkező 64 000-portok a különböző háttérbeli példányok között vékonyra vannak osztva. A SNAT-portok kimerülésének mérséklésével kapcsolatos útmutatásért lásd a [hibaelhárítási útmutatót](https://docs.microsoft.com/azure/load-balancer/troubleshoot-outbound-connection).  

A TCP-kapcsolatok esetében a terheléselosztó egyetlen SNAT-portot fog használni minden cél IP-címhez és porthoz. Ez a multiuse több kapcsolatot engedélyez ugyanahhoz a cél IP-címhez ugyanahhoz a SNAT-porthoz. Ez a multiuse korlátozott, ha a kapcsolódás nem különböző célport.

Az UDP-kapcsolatok esetében a terheléselosztó egy **portra korlátozott kúp NAT** -algoritmust használ, amely a célként megadott IP-címenként egy SNAT portot használ. 

A portok korlátlan számú kapcsolatra vannak újra felhasználva. A portot csak akkor használja a rendszer, ha a célként megadott IP-cím vagy port eltér.

## <a name="port-allocation"></a><a name="preallocatedports"></a> Port kiosztása

A terheléselosztó előtér-IP-címéhez hozzárendelt nyilvános IP-címek 64 000 SNAT-portot kapnak a háttérbeli készlet tagjai számára. A portok nem oszthatók meg a háttérbeli készlet tagjaival. Egy SNAT-portot csak egyetlen backend-példány használhat, így biztosítva, hogy a visszatérési csomagok megfelelően legyenek irányítva. 

Ajánlott explicit kimenő szabályt használni a SNAT-portok kiosztásának konfigurálásához. Ez a szabály maximalizálja azon SNAT-portok számát, amelyek esetében az egyes backend-példányok elérhetők a kimenő kapcsolatok számára. 

Ha a kimenő SNAT automatikus kiosztását egy terheléselosztási szabályon keresztül használja, a foglalási tábla meghatározza a portok kiosztását.

A következő <a name="snatporttable"></a> táblázat a SNAT portok előfoglalásait mutatja be a háttérbeli készlet méreteihez:

| Készlet mérete (VM-példányok) | Az előlefoglalt SNAT-portok száma IP-konfiguráció alapján |
| --- | --- |
| 1-50 | 1 024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1000 | 32 | 

>[!NOTE]
> Ha a háttér-készlet max. 6-os mérettel rendelkezik, az egyes példányok esetében a 64000/10 = 6 400 portok is rendelkezhetnek, ha explicit kimenő szabályt ad meg. A fenti táblázatnak megfelelően a rendszer csak akkor 1 024, ha az automatikus kiosztást választja.

## <a name="outbound-rules-and-virtual-network-nat"></a><a name="outboundrules"></a> Kimenő szabályok és Virtual Network NAT

Azure Load Balancer a kimenő szabályok és a Virtual Network NAT a virtuális hálózatból való kilépéshez elérhető lehetőségek.

További információ a kimenő szabályokról: [Kimenő szabályok](outbound-rules.md).

További információ az Azure Virtual Network NAT-ról: [Mi az az azure Virtual Network NAT](../virtual-network/nat-overview.md).

## <a name="constraints"></a>Korlátozások

*   A portok 15 másodperc után lesznek felszabadítva, ha az **első TCP** -t fogadják vagy elküldik
*   A portok 240 másodperc után lesznek felszabadítva, ha **FINACK** érkezik vagy elküldve
*   Ha a kapcsolatok nem kapnak új csomagokat, a portok 4 – 120 percet követően lesznek felszabadítva.
  * Ez a küszöbérték a kimenő szabályok használatával konfigurálható.
*   Minden IP-cím 64 000 portot biztosít, amely a SNAT használható.
*   Minden port használható TCP-és UDP-kapcsolatokhoz egy cél IP-címhez is
  * Egy UDP SNAT portra van szükség, hogy a célport egyedi-e, vagy sem. A cél IP-címhez minden UDP-kapcsolatban egy UDP-SNAT portot használ a rendszer.
  * A TCP SNAT-portok több kapcsolathoz is használhatók ugyanahhoz a cél IP-címhez, ha a célként megadott portok eltérőek.
*   A SNAT kimerültség akkor következik be, amikor egy háttérbeli példány kifogyott a megadott SNAT-portok közül. A terheléselosztó továbbra is használhat fel nem használt SNAT-portokat. Ha a háttérbeli példány SNAT-portjai meghaladják a megadott SNAT-portokat, nem fog tudni új kimenő kapcsolatokat létesíteni.

## <a name="next-steps"></a>Következő lépések

*   [A kimenő kapcsolatok hibáinak elhárítása a SNAT kimerülése miatt](https://docs.microsoft.com/azure/load-balancer/troubleshoot-outbound-connection)
*   [Tekintse át az SNAT mérőszámait](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#how-do-i-check-my-snat-port-usage-and-allocation) , és ismerkedjen meg a megfelelő szűrési, felosztási és megtekintési módszerekkel.

