---
title: Az Azure Load Balancer hibaelhárítása
description: Ismerje meg, hogyan háríthatja el az Azure Load Balancer ismert problémáit.
services: load-balancer
documentationcenter: na
author: asudbring
manager: dcscontentpm
ms.custom: seodoc18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/28/2020
ms.author: allensu
ms.openlocfilehash: ca9b70bd71a618f8e3d5f4fe9504ba66a9f14c6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76935479"
---
# <a name="troubleshoot-azure-load-balancer"></a>Az Azure Load Balancer hibaelhárítása

Ez a lap hibaelhárítási információkat tartalmaz az alapvető és standard Azure load balancer kérdések hibaelhárítási információkat tartalmaz. A Standard Load Balancer-ről a [Standard Load Balancer overview című témakörben olvashat bővebben.](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics)

Ha a terheléselosztó kapcsolata nem érhető el, a leggyakoribb tünetek a következők: 

- A terheléselosztó mögött ivirtuális gépek nem reagálnak az állapotmintákra 
- A terheléselosztó mögött ivirtuális gépek nem válaszolnak a konfigurált porton lévő forgalomra

Amikor a külső ügyfelek a háttér-virtuális gépek megy keresztül a terheléselosztó, az ügyfelek IP-címét fogja használni a kommunikációhoz. Győződjön meg arról, hogy az ügyfelek IP-címe hozzá van adva az NSG engedélyezési listájához. 

## <a name="symptom-vms-behind-the-load-balancer-are-not-responding-to-health-probes"></a>Jelenség: A terheléselosztó mögött im., a virtuális gépek nem reagálnak az állapotmintákra
Ahhoz, hogy a háttérkiszolgálók részt vehessenek a terheléselosztó készletben, át kell menniük a mintavételi ellenőrzésen. Az állapotmintákról további információt a [Terheléselosztó-mintavételek ismertetése című témakörben talál.](load-balancer-custom-probe-overview.md) 

Előfordulhat, hogy a terheléselosztó háttérkészletének virtuális gépei a következő okok miatt nem válaszolnak a mintavételekre: 
- A terheléselosztó háttérkészletvirtuális gépe nem megfelelő állapotú 
- A terheléselosztó háttérkészletvirtuális gépe nem figyel a mintavételi porton 
- Tűzfal vagy egy hálózati biztonsági csoport blokkolja a terheléselosztó háttérkészletvirtuális gépének portját 
- Egyéb helytelen konfigurációk a terheléselosztóban

### <a name="cause-1-load-balancer-backend-pool-vm-is-unhealthy"></a>1. ok: A terheléselosztó háttérkészlet virtuális gépe nem megfelelő állapotú 

**Érvényesítés és megoldás**

A probléma megoldásához jelentkezzen be a részt vevő virtuális gépek, és ellenőrizze, hogy a virtuális gép állapota kifogástalan, és válaszolhat **a PsPing** vagy **TCPing** egy másik virtuális gép a készletben. Ha a virtuális gép nem megfelelő állapotú, vagy nem tud válaszolni a mintavételre, ki kell javítania a problémát, és a virtuális gép kifogástalan állapotba kell hoznia, mielőtt részt vehetne a terheléselosztásban.

### <a name="cause-2-load-balancer-backend-pool-vm-is-not-listening-on-the-probe-port"></a>2. ok: A terheléselosztó háttérkészletvirtuális gépnem figyel a mintavételi porton
Ha a virtuális gép kifogástalan állapotú, de nem válaszol a mintavételre, akkor az egyik lehetséges oka lehet, hogy a mintavételi port nincs megnyitva a részt vevő virtuális gép, vagy a virtuális gép nem figyel a porton.

**Érvényesítés és megoldás**

1. Jelentkezzen be a háttérrendszer virtuális gép. 
2. Nyisson meg egy parancssort, és futtassa a következő parancsot annak ellenőrzéséhez, hogy van-e alkalmazás, amely figyeli a mintavételi portot:   
            netstat -an
3. Ha a portállapota nem **LISTENING**szerepel listening-ként, konfigurálja a megfelelő portot. 
4. Másik lehetőségként válasszon ki egy másik portot, amely **a LISTENING**listában szerepel, és ennek megfelelően frissítse a terheléselosztó konfigurációját.              

### <a name="cause-3-firewall-or-a-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vms"></a>3. ok: Tűzfal vagy hálózati biztonsági csoport blokkolja a portot a terheléselosztó háttérkészlet virtuális gépein  
Ha a virtuális gép tűzfala blokkolja a mintavételi portot, vagy az alhálózaton vagy a virtuális gépen konfigurált egy vagy több hálózati biztonsági csoport nem teszi lehetővé a mintavétel a port elérését, a virtuális gép nem tud válaszolni az állapotmintára.          

**Érvényesítés és megoldás**

* Ha a tűzfal engedélyezve van, ellenőrizze, hogy be van-e állítva a mintavételi port engedélyezésére. Ha nem, állítsa be a tűzfalat úgy, hogy engedélyezze a forgalmat a mintavételi porton, majd tesztelje újra. 
* A hálózati biztonsági csoportok listájában ellenőrizze, hogy a mintavételi port bejövő vagy kimenő forgalma nak van-e interferencia. 
* Ellenőrizze azt is, hogy a virtuális gép vagy az alhálózat hálózati adapterén az összes hálózati biztonsági csoport **megtagadása** szabály magasabb prioritással rendelkezik-e, mint az alapértelmezett szabály, amely lehetővé teszi az LB-mintavételeket & a forgalmat (a hálózati biztonsági csoportoknak engedélyezniük kell a 168.63.129.16-os terheléselosztó IP-címét). 
* Ha a szabályok bármelyike blokkolja a mintavételi forgalmat, távolítsa el és konfigurálja újra a szabályokat, hogy a mintavételi forgalom.  
* Tesztelje, hogy a virtuális gép megkezdte-e a válaszadást az állapotmintákra. 

### <a name="cause-4-other-misconfigurations-in-load-balancer"></a>4. ok: Egyéb helytelen konfigurációk a terheléselosztóban
Ha az összes előző okok úgy tűnik, hogy érvényesítve és helyesen oldva, és a háttér-virtuális gép továbbra sem válaszol az állapotminta, majd manuálisan tesztelje a kapcsolatot, és gyűjtsön néhány nyomkövetési a kapcsolat megértéséhez.

**Érvényesítés és megoldás**

* Használja a virtuális hálózat on belüli másik virtuális gépek **psping** használatával a mintavételi port válaszának (például: .\psping.exe -t 10.0.0.4:3389) és az eredmények rögzítéséhez. 
* A virtuális hálózaton belüli másik virtuális gépek ről származó **TCPing** használatával tesztelje a mintavételi port válaszát (például: .\tcping.exe 10.0.0.4 3389) és rögzítse az eredményeket. 
* Ha ezekben a pingtesztekben nem érkezik válasz, akkor
    - Futtasson egy egyidejű Netsh-nyomkövetést a cél-háttérkészlet virtuális gépén és egy másik teszt virtuális gép ugyanazon virtuális hálózatról. Most futtasson egy PsPing tesztet egy ideig, gyűjtsön össze néhány hálózati nyomkövetést, majd állítsa le a tesztet. 
    - Elemezze a hálózati rögzítést, és nézze meg, hogy vannak-e bejövő és kimenő csomagok a ping lekérdezéshez. 
        - Ha a háttérkészlet virtuális gépén nem észlelnek bejövő csomagokat, akkor potenciálisan egy hálózati biztonsági csoport vagy az UDR helytelen konfigurációja blokkolja a forgalmat. 
        - Ha nem figyelhető meg kimenő csomagok a háttérkészlet virtuális gépén, a virtuális gép nek ellenőriznie kell a nem kapcsolódó problémákat (például az alkalmazás blokkolja a mintavételi portot). 
    - A terheléselosztó elérése előtt ellenőrizze, hogy a mintavételi csomagok egy másik helyre vannak-e kényszerítve (esetleg udr-beállításokon keresztül). Ez azt eredményezheti, hogy a forgalom soha nem éri el a háttérvirtuális gép. 
* Módosítsa a mintavétel típusát (például HTTP-t TCP-re), és konfigurálja a megfelelő portot a hálózati biztonsági csoportok ACL-okban és a tűzfalban, hogy ellenőrizze, hogy a probléma a mintavételi válasz konfigurációjával van-e. Az állapotminta konfigurációjáról további információt az [Endpoint terheléselosztás állapotminta konfigurációja című](https://blogs.msdn.microsoft.com/mast/2016/01/26/endpoint-load-balancing-heath-probe-configuration-details/)témakörben talál.

## <a name="symptom-vms-behind-load-balancer-are-not-responding-to-traffic-on-the-configured-data-port"></a>Jelenség: A terheléselosztó mögötti virtuális gépek nem válaszolnak a beállított adatport on-ra vonatkozó forgalomra

Ha egy háttérkészlet virtuális gépe kifogástalan állapotúként van felsorolva, és válaszol az állapotmintákra, de továbbra sem vesz részt a terheléselosztásban, vagy nem válaszol az adatforgalomra, annak oka a következő okok lehetnek: 
* A terheléselosztó háttérkészlet virtuális gépe nem figyel az adatporton 
* A hálózati biztonsági csoport blokkolja a terheléselosztó háttérkészletvirtuális gépének portját  
* A terheléselosztó elérése ugyanattól a virtuális géptől és hálózati adaptertől 
* Az Internet Load Balancer előtér elérése a részt vevő terheléselosztó háttérkészletvirtuális gépről 

### <a name="cause-1-load-balancer-backend-pool-vm-is-not-listening-on-the-data-port"></a>1. ok: A terheléselosztó háttérkészlet virtuális gépe nem figyel az adatporton 
Ha egy virtuális gép nem válaszol az adatforgalomra, annak az lehet az oka, hogy a célport nincs megnyitva a részt vevő virtuális számítógépen, vagy a virtuális gép nem figyel az adott porton. 

**Érvényesítés és megoldás**

1. Jelentkezzen be a háttérrendszer virtuális gép. 
2. Nyisson meg egy parancssort, és futtassa a következő  parancsot annak ellenőrzéséhez, hogy van-e egy alkalmazás, amely figyeli az adatportot: netstat -an 
3. Ha a port nem szerepel a "LISTENING" állapottal, konfigurálja a megfelelő figyelőportot 
4. Ha a port figyelőként van megjelölve, ellenőrizze a célalkalmazást a porton az esetleges problémákat.

### <a name="cause-2-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vm"></a>2. ok: A hálózati biztonsági csoport blokkolja a portot a terheléselosztó háttérkészlet virtuális gépén  

Ha egy vagy több hálózati biztonsági csoport konfigurálva az alhálózaton vagy a virtuális gépen, blokkolja a forrás IP vagy port, majd a virtuális gép nem tud válaszolni.

A nyilvános terheléselosztó esetében az internetes ügyfelek IP-címét fogja használni az ügyfelek és a terheléselosztó háttérvirtuális gépei közötti kommunikációhoz. Győződjön meg arról, hogy az ügyfelek IP-címe engedélyezett a háttérvirtuális gép hálózati biztonsági csoportjában.

1. A háttérvirtuális gépen konfigurált hálózati biztonsági csoportok listázása. További információt a Hálózati biztonsági csoportok kezelése című témakörben [talál.](../virtual-network/manage-network-security-group.md)
1. A hálózati biztonsági csoportok listájában ellenőrizze, hogy:
    - az adatport bejövő vagy kimenő forgalma interferenciát okoz. 
    - a A Virtuálisgép vagy az alhálózat hálózati adapterének minden hálózati adapterének **megtagadása,** amelynek magasabb prioritása van, mint a terheléselosztó mintavételeket és a forgalmat engedélyező alapértelmezett szabálynak (a hálózati biztonsági csoportoknak engedélyezniük kell a 168.63.129.16-os terheléselosztó IP-címét, azaz a mintavételi portot)
1. Ha a szabályok bármelyike blokkolja a forgalmat, távolítsa el és konfigurálja újra ezeket a szabályokat, hogy az adatforgalom.  
1. Tesztelje, hogy a virtuális gép megkezdte-e az állapotminta válaszol.

### <a name="cause-3-accessing-the-load-balancer-from-the-same-vm-and-network-interface"></a>3. ok: A terheléselosztó elérése ugyanaról a virtuális gépről és a hálózati interfészről 

Ha a terheléselosztó háttérvirtuális gépében üzemeltetett alkalmazás megpróbál hozzáférni egy másik alkalmazáshoz, amely ugyanabban a háttérvirtuális gépben található ugyanazon a hálózati felületen, akkor nem támogatott forgatókönyv, és sikertelen lesz. 

**Felbontás** A probléma az alábbi módszerek egyikével oldható meg:
* Külön háttérkészlet virtuális gépek alkalmazásonként konfigurálása. 
* Konfigurálja az alkalmazást két hálózati hálózati adapterrel, hogy minden alkalmazás saját hálózati adaptert és IP-címet használjon. 

### <a name="cause-4-accessing-the-internal-load-balancer-frontend-from-the-participating-load-balancer-backend-pool-vm"></a>4. ok: A belső terheléselosztó előtétjének elérése a részt vevő terheléselosztó háttérkészlet virtuális gépéből

Ha egy belső terheléselosztó van konfigurálva egy virtuális hálózaton belül, és az egyik résztvevő háttérvirtuális gépek próbál hozzáférni a belső terheléselosztó előtér, hibák fordulhatnak elő, ha a folyamat le van képezve a származó virtuális gép. Ez a forgatókönyv nem támogatott. Tekintse át a részletes vita [korlátjait.](concepts-limitations.md#limitations)

**Felbontás** Ennek a forgatókönyvnek a blokkolását többféleképpen is feloldja, beleértve a proxy használatát is. Az Application Gateway vagy más külső proxyk (például nginx vagy haproxy) kiértékelése. Az Alkalmazásátjáróról az [Application Gateway áttekintése című témakörben olvashat bővebben.](../application-gateway/application-gateway-introduction.md)

## <a name="symptom-cannot-change-backend-port-for-existing-lb-rule-of-a-load-balancer-which-has-vm-scale-set-deployed-in-the-backend-pool"></a>Jelenség: Nem lehet módosítani a háttér-port egy terheléselosztó meglévő LB-szabályához, amelya háttérkészletben üzembe helyezett virtuálisgép-méretezési készlettel rendelkezik. 
### <a name="cause--the-backend-port-cannot-be-modified-for-a-load-balancing-rule-thats-used-by-a-health-probe-for-load-balancer-referenced-by-vm-scale-set"></a>Ok: A háttérport nem módosítható olyan terheléselosztási szabályhoz, amelyet a virtuális gép méretezési készlete által hivatkozott terheléselosztó hoz használ.
**Felbontás** A port módosítása érdekében eltávolíthatja az állapotminta frissítésével a virtuális gép méretezési készlet, frissítse a portot, majd konfigurálja újra az állapotminta.

## <a name="additional-network-captures"></a>További hálózati rögzítések
Ha úgy dönt, hogy megnyit egy támogatási esetet, gyűjtse össze a következő információkat a gyorsabb megoldás érdekében. Válasszon egyetlen háttér-virtuális gépa következő teszteket:
- Használja psping a virtuális hálózat on belül a háttérvirtuális gép a virtuális hálózaton belül a mintavételi port válaszának (például: psping 10.0.0.4:3389) és az eredmények rögzítéséhez. 
- Ha ezekben a pingtesztekben nem érkezik válasz, futtasson egy egyidejű Netsh-nyomkövetést a háttérvirtuális gépen és a virtuális hálózat tesztvirtuális gépén a PsPing futtatása közben, majd állítsa le a Netsh-nyomkövetést. 
  
## <a name="next-steps"></a>További lépések

Ha az előző lépések nem oldják meg a problémát, nyisson meg egy [támogatási jegyet.](https://azure.microsoft.com/support/options/)

