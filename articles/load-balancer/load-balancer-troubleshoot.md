---
title: Azure Load Balancer hibaelhárítása |} Microsoft Docs
description: Azure Load Balancer szolgáltatással kapcsolatos ismert problémák elhárítása
services: load-balancer
documentationcenter: na
author: RamanDhillon
manager: timlt
editor: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 560e2ed7cafd1018f4b64ac6d0e45134560082b6
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="troubleshoot-azure-load-balancer"></a>Azure Load Balancer hibaelhárítása

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Ezen a lapon elhárításával kapcsolatban biztosít információkat Azure Load Balancer gyakori kérdésekre. Ha a terheléselosztó kapcsolat nem érhető el, a leggyakrabban használt tüneteket a következők: 
- A terheléselosztó mögött virtuális gépek nem válaszolnak a állapotteljesítmény 
- A terheléselosztó mögött virtuális gépek nem válaszolnak a forgalmat a konfigurált port

## <a name="symptom-vms-behind-the-load-balancer-are-not-responding-to-health-probes"></a>Jelenség: A terheléselosztó mögött virtuális gépek nem válaszolnak a állapotteljesítmény
A háttérkiszolgálók részt venni a terhelés terheléselosztó-készlet akkor meg kell felelnie a mintavétel-ellenőrzés. Állapotteljesítmény kapcsolatos további információkért lásd: [ismertetése Load Balancer vizsgálat](load-balancer-custom-probe-overview.md). 

Előfordulhat, hogy a terheléselosztó háttérkészletéből virtuális gépek nem válaszolnak a mintavételt a következő okok valamelyike miatt: 
- A terheléselosztó háttérkészletének virtuális gép állapota nem megfelelő 
- Virtuális gép nem figyeli a mintavételi portot a terheléselosztó háttérkészletéből betöltése 
- Tűzfal- vagy hálózati biztonsági csoport blokkolja az a terheléselosztó háttérkészletéből virtuális gépek portja 
- Más hibás konfigurációi által a terheléselosztó

### <a name="cause-1-load-balancer-backend-pool-vm-is-unhealthy"></a>1. ok: A terheléselosztó háttérkészletéből virtuális gép állapota nem megfelelő 

**Érvényesítési és megoldás szerint**

A probléma megoldásához, jelentkezzen be a programban részt vevő virtuális gépeket, és ellenőrizze, hogy a virtuális gép állapota kifogástalan, és válaszolhat **PsPing** vagy **TCPing** a készlet egy másik virtuális gépről. Ha a virtuális gép állapota nem megfelelő, vagy nem válaszol a mintavétel, akkor a probléma kijavításához és beolvasása a virtuális Gépet kifogástalan állapotra, mielőtt részt vehetne az terheléselosztás.

### <a name="cause-2-load-balancer-backend-pool-vm-is-not-listening-on-the-probe-port"></a>2. ok: Terheléselosztó háttérkészletének virtuális gép nem a mintavételi portot figyel
Ha a virtuális gép működik megfelelően, de a mintavétel nem válaszol, majd egyik oka lehet, hogy a mintavétel port nincs nyitva a részt vevő virtuális gép vagy a virtuális gép nem adott portot figyel.

**Érvényesítési és megoldás szerint**

1. Jelentkezzen be a háttérkiszolgáló virtuális gép. 
2. Nyisson meg egy parancssort, és érvényesítése nem található a következő parancsot egy alkalmazás a mintavételi portot figyel:   
            netstat - a
3. Ha a port állapota nem szerepel az **FIGYELŐ**, konfigurálja a megfelelő portot. 
4. Azt is megteheti, válasszon egy másik portra, amely blokkolandóként **FIGYELŐ**, és ennek megfelelően betölteni a terheléselosztó-konfiguráció frissítése.              

### <a name="cause-3-firewall-or-a-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vms"></a>3. ok: Tűzfal vagy egy hálózati biztonsági csoport blokkolja az a terheléselosztó háttérkészletéből virtuális gépek portja  
Ha a virtuális Gépen a tűzfal blokkolja a mintavételi portot, vagy egy vagy több hálózati az alhálózaton lévő vagy a virtuális Gépen konfigurált biztonsági csoportok, nem engedélyezi, hogy a mintavétel elérni a portot, a virtuális gép nem tudja válaszolni a állapotmintáihoz.          

**Érvényesítési és megoldás szerint**

* Ha a tűzfal engedélyezve van, ellenőrizze, ha beállítják, hogy engedélyezze a mintavételi portot. Ha nem, a tűzfalat engedélyezik a forgalmat a mintavételi portot, majd tesztelje újból. 
* Hálózati biztonsági csoportok listában ellenőrizze, hogy a mintavételi portot a bejövő vagy kimenő forgalmat zavaró tényező rendelkezik-e. 
* Emellett ellenőrizze, hogy egy **megtagadása az összes** hálózati biztonsági csoportok-szabályok a hálózati Adaptert a virtuális gép vagy az alhálózatot, amely a magasabb prioritású, mint az alapértelmezett szabály, amely lehetővé teszi, hogy a Terheléselosztó mintavételt & forgalmat (hálózati biztonsági csoportok engedélyeznie kell a Load Balancer IP-címe 168.63.129.16). 
* Ha ezek a szabályok bármelyikét blokkolják a mintavételi forgalmát, távolítsa el, és konfigurálja újra a szabályokat, hogy a mintavétel forgalmat.  
* Ha a virtuális gép most már megkezdődött válaszol a health mintavételt a teszteléséhez. 

### <a name="cause-4-other-misconfigurations-in-load-balancer"></a>4. ok: Más hibás konfigurációi által a terheléselosztó
Ha az előző okok úgy tűnik, ellenőrizni kell, és helyesen feloldani, és a háttér virtuális gép továbbra is válaszol a állapotmintáihoz, majd manuálisan ellenőrizze a hálózati kapcsolatot, és néhány nyomkövetési tudni, hogy a kapcsolat.

**Érvényesítési és megoldás szerint**

* Használja **Psping** a mintavételi portot válasz tesztelése a Vneten belül más virtuális gépek egyikét (Példa:.\psping.exe -t 10.0.0.4:3389), és jegyezze fel eredmények. 
* Használja **TCPing** a mintavételi portot válasz tesztelése a Vneten belül más virtuális gépek egyikét (Példa:.\tcping.exe 10.0.0.4 3389-es), és jegyezze fel eredmények. 
* Ha nem érkezik válasz ping a vizsgálatok, majd
    - A virtuális gép cél háttérkészlet egyidejű Netsh nyomkövetés és a teszteléshez használt virtuális gép egy másik futtassa ugyanazt a virtuális hálózatot. Most egy PsPing teszt egy kis ideig, bizonyos hálózati nyomkövetési, és állítsa le a vizsgálat. 
    - A hálózati rögzítési elemzéséhez, és van-e a ping lekérdezés kapcsolódik a bejövő és kimenő csomagok. 
        - Ha nincs bejövő csomagok figyelhetők a virtuális gép háttérkészlet, nincs potenciálisan a hálózati biztonsági csoportok vagy UDR nem megfelelő konfiguráció forgalmát blokkolja az. 
        - Ha nincs kimenő csomagok figyelhetők a virtuális gép háttérkészlet, a virtuális gép kell ellenőrizni kell a nem kapcsolódó probléma merül fel (a eample, az alkalmazás blokkolja a mintavételi portot). 
    - Győződjön meg arról, ha a mintavételi csomagok kényszerítve vannak-e éppen más célra (valószínűleg keresztül UDR beállítások) a terheléselosztó elérése előtt. Ennek hatására a forgalmat a háttér virtuális gép soha ne eléréséhez. 
* Módosítsa a mintavételi típust (például, hogy a TCP HTTP), valamint a megfelelő port konfigurálása hálózati biztonsági csoportok hozzáférés-vezérlési listák és a tűzfalon annak ellenőrzéséhez, hogy a probléma van a konfigurációjával kapcsolatban mintavételi válasz. Állapot-mintavételi konfigurációjával kapcsolatos további információkért lásd: [végpont terheléselosztás állapotkonfiguráció mintavételi](https://blogs.msdn.microsoft.com/mast/2016/01/26/endpoint-load-balancing-heath-probe-configuration-details/).

## <a name="symptom-vms-behind-load-balancer-are-not-responding-to-traffic-on-the-configured-data-port"></a>Jelenség: Terheléselosztó mögötti virtuális gépek nem reagál a beállított port forgalmát a

Ha a virtuális gép van megadva, a megfelelő és válaszol-e a health mintavételek menüpontban, de még nem vesz részt a terheléselosztás, vagy nem válaszol az adatforgalmat a háttérkészlet, lehet, a következő okok valamelyike miatt: 
* Virtuális gép nem figyeli a adatok port a terheléselosztó háttérkészletéből betöltése 
* Hálózati biztonsági csoport blokkolja a terheléselosztó háttérkészletéből VM portjához  
* A terheléselosztó az azonos virtuális gép és a hálózati adapter használata 
* Az Internet Terheléselosztói VIP elérése a virtuális gép részt vevő terheléselosztó háttérkészletéből 

### <a name="cause-1-load-balancer-backend-pool-vm-is-not-listening-on-the-data-port"></a>1. ok: Terheléselosztó háttérkészletének virtuális gép nem az adatok portot figyel 
Ha egy virtuális gép nem válaszol az adatforgalmat, lehet, mert vagy a cél port nincs nyitva a programban részt vevő virtuális Gépre, vagy a virtuális gép nem adott portot figyel. 

**Érvényesítési és megoldás szerint**

1. Jelentkezzen be a háttérkiszolgáló virtuális gép. 
2. Nyisson meg egy parancssort, és érvényesítése nem található a következő parancsot egy alkalmazás adatokat porton:  
            netstat - a 
3. Ha a port nem szerepel a állapotú "Figyelés", a megfelelő figyelő port konfigurálása 
4. Ha a port van megjelölve, figyelés, akkor ellenőrizze a célalkalmazás lehetséges probléma merül fel az adott porton. 

### <a name="cause-2-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vm"></a>2. ok: Hálózati biztonsági csoport blokkolja a terheléselosztó háttérkészletéből VM portjához  

Ha egy vagy több hálózati biztonsági csoportot az alhálózaton lévő vagy a virtuális Gépre, blokkolja a forrás IP-cím vagy a port, akkor a virtuális gép nem tud válaszolni.

* A hálózati biztonsági csoportokhoz a háttérkiszolgálón VM listában. További információkért lásd:
    -  [A portál használatával a hálózati biztonsági csoportok kezelése](../virtual-network/virtual-network-manage-nsg-arm-portal.md)
    -  [Hálózati biztonsági csoportok kezelése a PowerShell-lel](../virtual-network/virtual-network-manage-nsg-arm-ps.md)
* Hálózati biztonsági csoportok listában ellenőrizze, hogy:
    - az adatok port bejövő vagy kimenő forgalom zavaró tényező rendelkezik. 
    - egy **megtagadása az összes** hálózati biztonsági csoport szabály a hálózati adapter által a virtuális gép vagy az alhálózat, a magasabb prioritású, amely az alapértelmezett szabály, amely lehetővé teszi, hogy a terheléselosztó-vizsgálat, és a forgalom (hálózati biztonsági csoportok engedélyeznie kell a Load Balancer IP-címe 168.63.129.16, Ez a mintavételi portot) 
* Ha a forgalmat blokkol szabályok valamelyikének, távolítsa el, és konfigurálja újra ezeket a szabályokat az adatforgalom engedélyezéséhez.  
* Ha a virtuális gép most már megkezdődött a health mintavételt válaszolni tesztelése.

### <a name="cause-3-accessing-the-load-balancer-from-the-same-vm-and-network-interface"></a>3. ok: A virtuális gép és a hálózati adaptert a terheléselosztó eléréséhez 

Ha az alkalmazást a háttér virtuális gép egy terheléselosztó megpróbál hozzáférni egy másik alkalmazást a azonos virtuális gép háttérbeli hálózati adaptert keresztül, a nem támogatott lehetőséget, és sikertelen lesz. 

**Megoldási** feloldhatja a problémát az Adminisztráció a következő módszerek egyikét:
* Alkalmazásonként külön háttérkészlet virtuális gépeket konfigurálhatja. 
* Konfigurálja az alkalmazást kettős NIC virtuális gépek, így minden egyes alkalmazás használta a saját hálózati illesztő IP-címet. 

### <a name="cause-4-accessing-the-internal-load-balancer-vip-from-the-participating-load-balancer-backend-pool-vm"></a>4. ok: A részt vevő terheléselosztó háttérkészletéből VM fér hozzá a belső Terheléselosztói VIP

Ha egy ILB VIP egy Vneten belül van konfigurálva, és a résztvevő háttér virtuális gépek egyik próbál hozzáférni a belső Terheléselosztói VIP, amely hibát eredményez. Ez egy nem támogatott eset.
**Megoldási** ilyen forgatókönyv támogatásához Alkalmazásátjáró kiértékelése vagy más proxyk (például nginx vagy haproxy esetén). Alkalmazásátjáró kapcsolatos további információkért lásd: [Alkalmazásátjáró – áttekintés](../application-gateway/application-gateway-introduction.md)

## <a name="additional-network-captures"></a>További hálózati rögzítést
Ha úgy dönt, hogy nyissa meg a támogatási esetet, gyűjtése gyorsabb rendezése a következő információkat. Válassza ki a virtuális Gépet a következő tesztek kerülnek végrehajtásra egyetlen háttérkiszolgálón:
- Az egyik a háttér virtuális gépek a Vneten belül a Psping eszköz használata a mintavételi portot válasz tesztelése (Példa: a psping 10.0.0.4:3389), és jegyezze fel eredmények. 
- A háttér virtuális gépek a Vneten belül egyikéből TCPing segítségével ellenőrizze, a mintavételi portot választ (Példa: a psping 10.0.0.4:3389), és jegyezze fel eredmények.
- Ha nem érkezik válasz ping a vizsgálatok, futtassa egy egyidejű Netsh trace parancsának a háttér virtuális gép és a virtuális hálózat teszteléshez használt virtuális gép közben PsPing futtatni, majd állítsa le a Netsh trace. 
  
## <a name="next-steps"></a>További lépések

Ha az előző lépések nem a probléma megoldásához nyissa meg a [támogatja a jegy](https://azure.microsoft.com/support/options/).

