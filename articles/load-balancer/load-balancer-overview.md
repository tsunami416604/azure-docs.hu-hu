---
title: "Az Azure Load Balancer áttekintése |} Microsoft Docs"
description: "Azure Load Balancer funkciók, architektúrájának és megvalósítási áttekintése. Ismerje meg, hogyan működik, a terheléselosztó és a felhőben kihasználja azt."
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
editor: 
ms.assetid: 0f313dc0-f007-4cee-b2b9-f542357925a3
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: ecf1fc38d2b9fd54fe5b00db616224a0848179fe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-load-balancer-overview"></a>Az Azure Load Balancer áttekintése

Az Azure Load Balancer magas rendelkezésre állást és hálózati teljesítményt biztosít alkalmazásai számára. A réteg 4 (TCP, UDP) terheléselosztó bejövő forgalmat egy elosztott terhelésű készlet definiált kifogástalan szolgáltatáspéldányok között ellátó.

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Az Azure Load Balancer beállítható úgy, hogy:

* Egyenleg bejövő internetes forgalmat a virtuális gépek betölteni. Ez a konfiguráció az úgynevezett [internetre terheléselosztási](load-balancer-internet-overview.md).
* Betöltési oszthatja el a forgalmat egy virtuális hálózatban lévő virtuális gépek, virtuális gépek felhőszolgáltatások közötti vagy a helyszíni számítógépek és a létesítmények közötti virtuális hálózatban lévő virtuális gépek között. Ez a konfiguráció az úgynevezett [belső terheléselosztás](load-balancer-internal-overview.md).
* Egy adott virtuális gép a külső forgalom továbbítására.

A felhőben található összes erőforrást egy nyilvános IP-címet az internetről elérhetőnek kell lennie kell. A felhőalapú infrastruktúra az Azure-ban nem elérhető IP-címet használ az erőforrások. Azure hálózati címfordítás (NAT) használ a kommunikálnak az interneten a nyilvános IP-címeket.

## <a name="load-balancer-features"></a>A terheléselosztó funkció betöltése

* Kivonat-alapú terjesztési

    Az Azure Load Balancer kivonat-alapú elosztási algoritmust használ. Alapértelmezés szerint 5 rekordos kivonatot forrás IP-cím, a forrásport, a cél IP-cím, a célport és a protokolltípus álló forgalom hozzárendelése elérhető kiszolgálók használ. Csak tölcsérútvonalak biztosít *belül* egy átviteli munkamenet. Az elosztott terhelésű végpont mögött ugyanazon ugyanabban a TCP vagy UDP-munkamenetben csomagokat a rendszer kéri. Ha az ügyfél bezárja és újra megnyitja a kapcsolatot, vagy új munkamenet indítása a azonos forrás IP-címről, a forrásport változik. Emiatt a forgalom egy másik végpont egy ugyanabban az adatközpontban ugorhat.

    További részletekért lásd: [terheléselosztó terheléselosztási mód](load-balancer-distribution-mode.md). A következő ábra a kivonat-alapú eloszlását mutatja be:

    ![Kivonat-alapú terjesztési](./media/load-balancer-overview/load-balancer-distribution.png)

    Ábra - kivonat-alapú terjesztési

* Port átirányítása

    Az Azure Load Balancer állítható be, hogyan bejövő kommunikáció alatt áll. Ez a kommunikáció internetes gazdagépek, virtuális gépek más felhőalapú szolgáltatások, vagy a virtuális hálózatok kezdeményezett forgalmat foglalja magában. Ez a vezérlő végpont (más néven bemeneti végpontja) jelöl.

    Bemeneti végpontja egy nyilvános portot figyeli, és a belső portra forgalmát. Ugyanazokat a portokat, belső vagy külső végpont hozzárendelését, vagy egy másik portot használ. Például hogy egy webkiszolgálón, míg a nyilvános végpontot leképezés 80-as porton figyeli 81-es porthoz. Egy nyilvános végpontot létrehozásának elindítja a terhelés terheléselosztó példány létrehozása.

    Létrehozásakor az Azure portál használatával, a portál automatikusan létrehoz végpontot a virtuális géphez a távoli asztal protokoll (RDP) és a Windows PowerShell távoli munkamenet-forgalmat. Ezeket a végpontokat segítségével távoli felügyeletéhez a virtuális gép az interneten keresztül.

* Automatikus újrakonfigurálása

    Az Azure Load Balancer azonnal újrakonfigurálása akkor példányok felfelé vagy lefelé. Például az újrakonfigurálás történik, a példányok száma a felhőszolgáltatásban a webes/munkavégző szerepkörök növelésével vagy elosztott terhelésű ugyanazokat a további virtuális gépek hozzáadásakor.

* Szolgáltatásfigyelés

    Az Azure Load Balancer is mintavételi a különböző kiszolgálópéldányok állapotát. Egy mintavételt nem válaszol, amikor a terheléselosztó leállítja az új kapcsolatok küld a nem megfelelő példányok. A meglévő kapcsolatok nem érintettek.

    Három típusú mintavételt támogatja:

    + **Vendég ügynök mintavétele (platformon, a szolgáltatási célú virtuális gépek csak):** a terheléselosztót használja a vendégügynököt a virtuális gépen belül. A vendégügynök figyeli, és válaszol egy HTTP 200 OK választ, csak akkor, ha a példány (azaz a példány nem állapotban van egy foglalt, például újrahasznosítási, vagy leállítása) kész állapotban van. Ha az ügynök nem válaszol egy HTTP 200 OK, a terheléselosztó jelöli meg a példány válaszol, és leállítja a forgalom küldése annak a példánynak. A load balancer továbbra is fennáll, Pingelje meg a példány. Ha a vendégügynök válaszol egy HTTP 200, a terheléselosztó fog forgalmat küldeni annak a példánynak újra. Ha a webes szerepkör használata esetén a webhely kódot általában futtat w3wp.exe, nem figyelt az Azure fabric vagy Vendég ügynök. Ez azt jelenti, hogy a vendégügynök nem jelentett hibák w3wp.exe (pl. HTTP 500 válaszok), és a terheléselosztó nem fogja tudni kívül Elforgatás példánynak érvénybe.
    + **Egyéni HTTP-vizsgálatot:** Ez a Hálózatfigyelő felülbírálja az alapértelmezett (vendégügynök) mintavétel. Használhatja a saját egyéni logika állapotának a szerepkör-példány létrehozásához. A load balancer rendszeresen fog mintavételi a végpont (alapértelmezés szerint minden 15 másodperc). A példány Elforgatás találhatók, ha a 200-as HTTP vagy TCP nyugtát KAPNI a határidőn (alapértelmezett 31 másodperc) belül reagáljon tekinthető meg. Ez akkor hasznos, a saját logikai példány eltávolítása a terheléselosztó Elforgatás megvalósításához. Konfigurálhatja például, a példány visszatérési nem 200 állapotot, ha a példány CPU 90 % feletti. A webes szerepkörök w3wp.exe használó, is kap automatikus figyelési a webhely, mivel a webhely kódban hibák nem 200 állapot térjen vissza a mintavétel.
    + **Egyéni TCP-Hálózatfigyelővel:** Ez a Hálózatfigyelő sikeres TCP-munkamenet létrehozása egy meghatározott mintavételi port támaszkodik.

    További információkért lásd: a [LoadBalancerProbe séma](https://msdn.microsoft.com/library/azure/jj151530.aspx).

* Forrás NAT

    Minden kimenő forgalom az internethez, a szolgáltatásból származó forrás NAT (SNAT) a bejövő forgalmat a VIP-címmel segítségével megy keresztül. SNAT fontos előnyökkel jár:

    + Egyszerű frissítés és katasztrófa-helyreállítás szolgáltatások, mivel a VIP dinamikusan képezhető le a szolgáltatást egy másik példánya lehetővé teszi.
    + Ez egyszerűbbé teszi a hozzáférés-vezérlési lista (ACL) felügyeletet. Virtuális IP-címek kifejezett hozzáférés-vezérlési listák nem módosítása szolgáltatások felskálázott, mint lefelé vagy újratelepítése beolvasása.

    A terheléselosztó-konfigurációja teljes amerikai NAT támogatja az UDP-hez. Teljes amerikai NAT NAT, ahol a port lehetővé teszi a bejövő kapcsolatok származó bármely külső állomás (a egy kimenő irányuló kérelemre adott válasz) típusú.

    A terheléselosztó által is kapnak, minden egyes új kimenő kapcsolat indít el egy virtuális gép, egy kimenő portot. A külső gazdagép látja a forgalom virtuális IP-Címek VIP-hozzárendelt portszámmal együtt. Kimenő kapcsolatok nagy számú igénylő forgatókönyvek esetén ajánlott használni [példányszintű nyilvános IP-cím](../virtual-network/virtual-networks-instance-level-public-ip.md) szünteti meg, hogy a virtuális gépek SNAT egy dedikált kimenő IP-címet beállítani. Ez csökkenti a port Erőforrásfogyás kockázatát.

    Ellenőrizze a [kimenő kapcsolatok](load-balancer-outbound-connections.md) cikket ebben a témakörben olvashat.

### <a name="support-for-multiple-load-balanced-ip-addresses-for-virtual-machines"></a>Több-az elosztott terhelésű IP-címet a virtuális gépek támogatása
Egynél több terhelésű nyilvános IP-cím rendelhet a virtuális gépek halmazát jelenti. Ez a lehetőség a gazdagép több SSL-webhely és/vagy az SQL Server AlwaysOn rendelkezésre állási csoport több figyelőinek ugyanazokat a virtuális gépek. További információkért lásd: [több virtuális IP-címek egy felhőalapú szolgáltatás](load-balancer-multivip.md).

[!INCLUDE [load-balancer-compare-tm-ag-lb-include.md](../../includes/load-balancer-compare-tm-ag-lb-include.md)]

## <a name="limitations"></a>Korlátozások

Load Balancer háttérkészletek bármely virtuális gép SKU, kivéve az alapszintű csomag is tartalmazhat.

## <a name="next-steps"></a>Következő lépések

- További információ [internetre terheléselosztó](load-balancer-internet-overview.md)

- További információ [belső load balancer áttekintése](load-balancer-internal-overview.md)

- Hozzon létre egy [internetre terheléselosztó](load-balancer-get-started-internet-portal.md)

- Ismerje meg, azzal kapcsolatban, a másik kulccsal [hálózati lehetőségeket](../networking/networking-overview.md) Azure

