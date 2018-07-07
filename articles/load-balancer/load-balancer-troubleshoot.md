---
title: Az Azure Load Balancer hibaelhárítása |} A Microsoft Docs
description: Az Azure Load Balancer szolgáltatással kapcsolatos ismert problémák elhárítása
services: load-balancer
documentationcenter: na
author: chadmath
manager: cshepard
editor: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/06/2018
ms.author: genli
ms.openlocfilehash: 6777842f3ca336eb4ae0d134cbc7ffd062bc6f29
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/06/2018
ms.locfileid: "37889588"
---
# <a name="troubleshoot-azure-load-balancer"></a>Az Azure Load Balancer hibaelhárítása

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Ez az oldal nyújt információkat talál az Azure Load Balancer gyakori kérdésekre. A Load Balancer internetkapcsolat esetén nem érhető el, a leggyakoribb jelenségeket a következők: 
- A terheléselosztó mögötti virtuális gépek nem válaszolnak a állapotadat-mintavételek 
- A terheléselosztó mögötti virtuális gépek nem reagálnak a forgalmat a konfigurált port

## <a name="symptom-vms-behind-the-load-balancer-are-not-responding-to-health-probes"></a>Jelenség: A terheléselosztó mögötti virtuális gépek nem válaszolnak a állapotadat-mintavételek
A háttérkiszolgálókhoz, hogy részt vesz a terheléselosztó készlethez át kell adniuk a mintavétel-ellenőrzés. Állapot-mintavételei kapcsolatos további információkért lásd: [ismertetése Load Balancer mintavételek](load-balancer-custom-probe-overview.md). 

A Load Balancer háttérkészletének virtuális gépek nem válaszolnak, az alábbi okok bármelyike miatt a mintavételezők: 
- A terheléselosztó háttérkészletének virtuális gép állapota nem megfelelő 
- Terheléselosztó háttérkészletének virtuális gép nem a mintavételi portot figyel. 
- Tűzfal vagy egy hálózati biztonsági csoport blokkolja az a Load Balancer háttérkészletének virtuális gépek portja 
- A Load Balancer más konfigurációs hibák

### <a name="cause-1-load-balancer-backend-pool-vm-is-unhealthy"></a>1. ok: A terheléselosztó háttérkészletének virtuális Gépén állapota nem megfelelő 

**Érvényesítési és megoldás szerint**

A probléma megoldásához jelentkezzen be a programban részt vevő virtuális gépekre, és ellenőrizze, hogy a virtuális gép állapota kifogástalan, és válaszolhat **PsPing** vagy **TCPing** a készletben lévő másik virtuális gépről. Ha a virtuális gép állapota nem megfelelő, vagy nem válaszol a mintavétel, hibanaplóit a probléma megoldásához, és előtt, hogy részt vehessen a terheléselosztást, szerezze be a virtuális Gépet egy Kifogástalan állapotba.

### <a name="cause-2-load-balancer-backend-pool-vm-is-not-listening-on-the-probe-port"></a>2. ok: Load Balancer háttérkészletének virtuális Gépén nem figyel a mintavételi portot
Ha a virtuális gép állapota megfelelő, de nem válaszol a mintavétel a, majd egyik lehetséges oka az lehet, hogy a mintavételi portot ne legyen meg van nyitva a virtuális gép vagy a virtuális gép résztvevő nem adott portot figyel.

**Érvényesítési és megoldás szerint**

1. Jelentkezzen be a háttérbeli virtuális Gépről. 
2. Nyisson meg egy parancssort, és futtassa a következő parancsot hiba ellenőrzése az alkalmazás figyelését a mintavételi portot:   
            netstat - an
3. Ha a port állapota nem szerepel az **FIGYELŐ**, konfigurálja a megfelelő portot. 
4. Jelölje ki, hogy van egy másik portra **FIGYELŐ**, és ennek megfelelően betölteni a terheléselosztó-konfiguráció frissítése.              

### <a name="cause-3-firewall-or-a-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vms"></a>3. ok: Tűzfal vagy egy hálózati biztonsági csoport blokkolja a terheléselosztó háttérkészletének virtuális gépek az portja  
Ha a virtuális gép tűzfala blokkolja a mintavételi portot, vagy legalább egy hálózati biztonsági csoportok konfigurálása az alhálózaton, vagy a virtuális gépen, a mintavétel elérni a port nem engedélyezett, a virtuális gép nem válaszol az állapotvizsgálatra.          

**Érvényesítési és megoldás szerint**

* Ha a tűzfal engedélyezve van, ellenőrizze, hogy van-e be, hogy a mintavételi portot. Ha nem, akkor konfigurálja a tűzfalat, hogy engedélyezik a forgalmat a mintavételi portot, majd tesztelje újból. 
* A hálózati biztonsági csoportok listában ellenőrzi, hogy a bejövő vagy kimenő forgalmat a mintavételi portot zavaró tényező tartalmazza-e. 
* Ellenőrizze továbbá, ha egy **az összes elutasítása** hálózati biztonsági csoportok szabály a hálózati adapteren a virtuális gép vagy az alhálózatot, amelyet a magasabb prioritású, mint az alapértelmezett szabályt, amely lehetővé teszi az LB-mintavételek & forgalmat (hálózati biztonsági csoportok engedélyeznie kell a terheléselosztó IP 168.63.129.16). 
* Ha ezek a szabályok bármelyikét forgalomszűrők blokkolják a mintavétel forgalmát, távolítsa el, és konfigurálja újra a szabályok, mintavétel adatforgalom engedélyezésére.  
* Ha a virtuális gép most már megkezdődött válaszol az állapotmintákat a teszteléséhez. 

### <a name="cause-4-other-misconfigurations-in-load-balancer"></a>4. ok: Egyéb sablonengedélyek Load Balancer
Ha az előző okok úgy tűnik, ellenőrizni kell, és helyesen feloldani, és a háttérbeli virtuális gép továbbra is válaszol az állapotvizsgálatra, akkor manuálisan ellenőrizze a hálózati kapcsolatot, és tudni, hogy a kapcsolat néhány nyomkövetések gyűjtéséhez.

**Érvényesítési és megoldás szerint**

* Használat **Psping** a mintavételi portot válasz tesztelése a virtuális hálózaton belüli más virtuális gépek közül (Példa:.\psping.exe -t 10.0.0.4:3389), és jegyezze fel az eredményeket. 
* Használat **TCPing** a mintavételi portot válasz tesztelése a virtuális hálózaton belüli más virtuális gépek közül (Példa:.\tcping.exe 10.0.0.4 3389-es), és jegyezze fel az eredményeket. 
* Ha nem érkezik válasz ping a vizsgálatok, majd
    - Futtassa egy egyidejű Netsh trace, a cél háttérkészletének virtuális Gépén futó és a egy másik tesztelési virtuális gép ugyanazon a Vneten. Most futtassa a PsPing tesztet egy kis ideig, bizonyos hálózati nyomkövetések gyűjtéséhez és majd leállítja a tesztet. 
    - A hálózati rögzítés elemzése, és tekintse meg, hogy vannak-e a ping lekérdezés kapcsolódó bejövő és kimenő csomagok. 
        - Nincs bejövő csomagok a háttérkészletének virtuális Gépén futó jelennek meg, van-e vélhetően egy hálózati biztonsági csoportok vagy UDR konfiguráció blokkolja a forgalmat. 
        - Nincs kimenő csomagok háttérkészletének virtuális Gépén futó jelennek meg, ha a virtuális Gépet kell ellenőrizni kell a nem kapcsolódó problémákat (a szolgáltatást, az alkalmazás blokkolja-e a mintavételi portot). 
    - Győződjön meg arról, ha a mintavételi csomagok folyamatban van kényszerítve (udr-t beállításainál valószínűleg) egy másik cél a terheléselosztó elérése előtt. Emiatt a forgalmat a háttérbeli virtuális gép soha nem érkezik. 
* Módosítsa a mintavétel típusát (például HTTP-TCP-), és a megfelelő portot konfigurálja a hálózati biztonsági csoportok hozzáférés-vezérlési listák és a tűzfal ellenőrizheti, hogy a probléma van a mintavétel válasz konfigurációval. Állapot-mintavételi konfigurációban kapcsolatos további információkért lásd: [Endpoint Load Balancing egészségügyi mintavételi konfiguráció](https://blogs.msdn.microsoft.com/mast/2016/01/26/endpoint-load-balancing-heath-probe-configuration-details/).

## <a name="symptom-vms-behind-load-balancer-are-not-responding-to-traffic-on-the-configured-data-port"></a>Jelenség: Terheléselosztó mögötti virtuális gépek nem válaszolnak a beállított port forgalmát a

Ha a virtuális gép kifogástalan állapotúként szerepel és az állapot-mintavételei válaszol, de még nem vesz részt a terheléselosztást, vagy nem válaszol az adatforgalmat a háttérkészlet, az alábbi okok bármelyike miatt lehet: 
* A terheléselosztó háttérkészletének virtuális gép nem figyel az adatportot 
* Hálózati biztonsági csoport nem blokkolja a portot a Load Balancer háttérkészletének virtuális Gépén futó  
* A virtuális gép és a hálózati Adaptert a terheléselosztó elérése 
* A programban részt vevő Load Balancer háttérkészletének virtuális Gépén az Internet Terheléselosztói VIP elérése 

### <a name="cause-1-load-balancer-backend-pool-vm-is-not-listening-on-the-data-port"></a>1. ok: Load Balancer háttérkészletének virtuális Gépén nem figyel a következőn: az adatok port 
Ha a virtuális gép nem válaszol az adatforgalmat, mert nem nyissa meg a programban részt vevő virtuális Gépen, vagy a célként megadott port lehet vagy a virtuális gép nem adott portot figyel. 

**Érvényesítési és megoldás szerint**

1. Jelentkezzen be a háttérbeli virtuális Gépről. 
2. Nyisson meg egy parancssort, és futtassa a következő parancsot hiba ellenőrzése az alkalmazás figyelését az adatportot:  
            netstat - an 
3. Ha a port nem szerepel az állapot "FIGYELŐ", a megfelelő figyelőport konfigurálása 
4. Ha a porton figyelő van megjelölve, majd ellenőrizze a célalkalmazás a adott porton figyeli a lehetséges problémákat. 

### <a name="cause-2-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vm"></a>2. ok: A hálózati biztonsági csoport nem blokkolja a portot a Load Balancer háttérkészletének virtuális Gépén futó  

Ha egy vagy több hálózati biztonsági csoport konfigurálva az alhálózaton, vagy a virtuális gépen, blokkolja a forrás IP-cím vagy a port, majd a virtuális gép nem tud válaszolni.

* A konfigurált virtuális gép háttérbeli hálózati biztonsági csoportok listája. További információkért lásd: [hálózati biztonsági csoportok kezelése](../virtual-network/manage-network-security-group.md).
* A hálózati biztonsági csoportok listában ellenőrizze, hogy:
    - a bejövő vagy kimenő forgalmat az adatportot zavaró tényező rendelkezik. 
    - egy **az összes elutasítása** adatforgalom és a hálózati biztonsági csoportra vonatkozó szabályt a hálózati adapteren a virtuális gép vagy az alhálózatot, amely az alapértelmezett szabályt, amely lehetővé teszi, hogy a terheléselosztó a mintavételezők magasabb prioritású (hálózati biztonsági csoportok engedélyeznie kell a terheléselosztó IP-168.63.129.16, Ez a mintavételi portot) 
* Ha a szabály forgalomszűrők blokkolják a forgalmat, távolítsa el, és konfigurálja újra ezeket a szabályokat, hogy az adatforgalom.  
* Tesztelése amennyiben a virtuális gép most már elindult az állapot-mintavételei válaszolni.

### <a name="cause-3-accessing-the-load-balancer-from-the-same-vm-and-network-interface"></a>3. ok: A terheléselosztó az azonos virtuális gépek és a hálózati adapter elérése 

Ha az alkalmazás a virtuális Gépet egy terheléselosztó lévő üzemeltetett próbál meg hozzáférni egy másik alkalmazás ugyanarra a háttérrendszerre virtuális gép üzemeltetett keresztül ugyanazon hálózati adapterre, nem támogatott forgatókönyv, és sikertelen lesz. 

**Feloldási** fel tudja oldani a problémát a következő módszerek egyikét:
* Alkalmazásonkénti külön háttérkészletének virtuális gépek konfigurálása. 
* Az alkalmazás konfigurálása kettős NIC-beli virtuális gépeken, így mindegyik alkalmazás használta a szolgáltatást, a saját hálózati adapter és IP-címet. 

### <a name="cause-4-accessing-the-internal-load-balancer-vip-from-the-participating-load-balancer-backend-pool-vm"></a>4. ok: A belső Terheléselosztói VIP elérése a programban részt vevő Load Balancer háttérkészletének virtuális Gépén

Ha egy ILB virtuális IP-CÍMEK egy virtuális hálózaton belül van konfigurálva, és a résztvevők háttérbeli virtuális gépek egyik próbál meg hozzáférni a belső Terheléselosztói VIP, a hibát az eredmények. Ez egy nem támogatott eset.
**Feloldási** Application Gateway kiértékelése vagy más proxyk (például a nginx-et vagy a haproxy) ezt a fajta forgatókönyv támogatásához. Az Application Gateway kapcsolatos további információkért lásd: [Application Gateway áttekintése](../application-gateway/application-gateway-introduction.md)

## <a name="additional-network-captures"></a>További hálózati rögzíti.
Ha úgy dönt, hogy nyisson meg egy támogatási esetet, gyorsabb megoldást a következő adatainak összegyűjtése. Válassza ki az egyetlen olyan háttérszolgáltatással virtuális Gépet a következő ellenőrzés végrehajtásához:
- A háttérrendszer a virtuális hálózaton belüli virtuális gépek közül a Psping eszköz használata a mintavételi portot válasz tesztelése (Példa: psping 10.0.0.4:3389), és jegyezze fel az eredményeket. 
- Ha nem érkezik válasz ping a vizsgálatok, futtassa egy egyidejű Netsh trace a háttérbeli virtuális Gépről, és a VNet tesztelési virtuális gép során, futtassa a PsPing, majd állítsa le a Netsh trace. 
  
## <a name="next-steps"></a>További lépések

Ha a fenti lépések nem a probléma megoldásához nyissa meg a [támogatási jegyet](https://azure.microsoft.com/support/options/).

