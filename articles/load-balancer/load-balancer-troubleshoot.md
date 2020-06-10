---
title: Az Azure Load Balancer hibaelhárítása
description: Ismerje meg, hogy miként lehet elhárítani a Azure Load Balancer ismert problémáit.
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
ms.openlocfilehash: b5b2d13c727cd0183bb14341767ee2dee89eb9f8
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84604346"
---
# <a name="troubleshoot-azure-load-balancer"></a>Az Azure Load Balancer hibaelhárítása

Ez az oldal az alapszintű és standard Azure Load Balancer kérdésekre vonatkozó hibaelhárítási információkat tartalmaz. További információ a standard Load Balancerről: [standard Load Balancer áttekintése](load-balancer-standard-diagnostics.md).

Ha a Load Balancer kapcsolat nem érhető el, a leggyakoribb tünetek a következők: 

- A Load Balancer mögötti virtuális gépek nem válaszolnak az állapot-mintavételre 
- A Load Balancer mögötti virtuális gépek nem válaszolnak a konfigurált porton lévő forgalomra

Amikor a külső ügyfelek a háttérbeli virtuális gépeken haladnak végig a terheléselosztóon, a rendszer az ügyfelek IP-címét fogja használni a kommunikációhoz. Győződjön meg arról, hogy az ügyfelek IP-címe a NSG engedélyezési listába kerül. 

## <a name="symptom-vms-behind-the-load-balancer-are-not-responding-to-health-probes"></a>Tünet: a Load Balancer mögötti virtuális gépek nem válaszolnak az állapot-mintavételre
Ahhoz, hogy a háttér-kiszolgálók részt vegyenek a terheléselosztó készletében, át kell adni a mintavétel-ellenőrzést. További információ az állapot-mintavételekről: Load Balancer mintavételek [ismertetése](load-balancer-custom-probe-overview.md). 

Előfordulhat, hogy a Load Balancer backend-készlet virtuális gépei nem válaszolnak a mintavételre a következő okok miatt: 
- Load Balancer háttérbeli készlet virtuális gépe nem kifogástalan állapotú 
- Load Balancer háttérbeli készlet virtuális gépe nem figyeli a mintavételi portot 
- A tűzfal vagy egy hálózati biztonsági csoport blokkolja a portot a Load Balancer backend-készletben lévő virtuális gépeken 
- Egyéb helytelen konfigurációk Load Balancer

### <a name="cause-1-load-balancer-backend-pool-vm-is-unhealthy"></a>1. ok: Load Balancer háttérbeli készlet virtuális gépe sérült 

**Ellenőrzés és megoldás**

A probléma megoldásához jelentkezzen be a részt vevő virtuális gépekre, és ellenőrizze, hogy a virtuális gép állapota Kifogástalan-e, és tud-e válaszolni a **PsPing** vagy a **TCPING** egy másik virtuális gépről a készletben. Ha a virtuális gép nem kifogástalan állapotú, vagy nem tud válaszolni a mintavételre, ki kell javítania a problémát, és vissza kell állítania a virtuális gépet egy Kifogástalan állapotba, mielőtt részt tudjon venni a terheléselosztásban.

### <a name="cause-2-load-balancer-backend-pool-vm-is-not-listening-on-the-probe-port"></a>2. ok: Load Balancer háttérbeli készlet virtuális gépe nem figyeli a mintavételi portot
Ha a virtuális gép kifogástalan állapotban van, de nem válaszol a mintavételre, az egyik lehetséges ok az lehet, hogy a mintavételi port nincs megnyitva a résztvevő virtuális gépen, vagy a virtuális gép nem figyeli a portot.

**Ellenőrzés és megoldás**

1. Jelentkezzen be a háttérbeli virtuális gépre. 
2. Nyisson meg egy parancssort, és futtassa a következő parancsot annak ellenőrzéséhez, hogy van-e olyan alkalmazás, amely figyeli a mintavételi portot:   
            netstat – a
3. Ha a port állapota nem **figyelt**van felsorolva, konfigurálja a megfelelő portot. 
4. Másik lehetőségként kiválaszthat egy másik portot, amely **Figyelésként**jelenik meg, és ennek megfelelően frissíti a terheléselosztó konfigurációját.              

### <a name="cause-3-firewall-or-a-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vms"></a>3. ok: a tűzfal vagy egy hálózati biztonsági csoport blokkolja a terheléselosztó háttér-készletének virtuális gépei portját.  
Ha a virtuális gépen lévő tűzfal blokkolja a mintavételi portot, vagy egy vagy több, az alhálózaton vagy a virtuális gépen konfigurált hálózati biztonsági csoport nem engedélyezi a mintavételt a port eléréséhez, a virtuális gép nem tud válaszolni az állapot-mintavételre.          

**Ellenőrzés és megoldás**

* Ha a tűzfal engedélyezve van, ellenőrizze, hogy be van-e állítva a mintavételi port engedélyezéséhez. Ha nem, konfigurálja úgy a tűzfalat, hogy engedélyezze a forgalmat a mintavételi porton, majd tesztelje újra. 
* A hálózati biztonsági csoportok listájából ellenőrizze, hogy a mintavételi port bejövő vagy kimenő forgalma interferencia-e. 
* Azt is ellenőrizze, hogy az összes hálózati biztonsági csoport **megtagadva** szabály a virtuális gép hálózati adapterén vagy az alhálózaton, amely magasabb prioritású, mint az LB-szondák & forgalmat engedélyező alapértelmezett szabálynál (a hálózati biztonsági csoportoknak engedélyezniük kell Load Balancer IP-168.63.129.16). 
* Ha a szabályok bármelyike blokkolja a mintavételi forgalmat, távolítsa el és konfigurálja újra a szabályokat a mintavételi forgalom engedélyezéséhez.  
* Tesztelje, hogy a virtuális gép most már megkezdte-e a válaszadást az állapotra. 

### <a name="cause-4-other-misconfigurations-in-load-balancer"></a>4. ok: egyéb helytelen konfigurációk Load Balancer
Ha az összes korábbi ok úgy tűnik, hogy a rendszer megfelelően érvényesíti és orvosolja az összes okot, és a háttérbeli virtuális gép továbbra sem válaszol az állapot-mintavételre, akkor manuálisan tesztelje a kapcsolatot, és gyűjtsön néhány nyomkövetést a kapcsolat megismeréséhez.

**Ellenőrzés és megoldás**

* Használja a **Psping** a VNet belüli egyik másik virtuális gépről a mintavételi port válaszának teszteléséhez (például: .\psping.exe-t 10.0.0.4:3389), és jegyezze fel az eredményeket. 
* Használja a **TCPing** a VNet belüli egyik másik virtuális gépről a mintavételi port válaszának teszteléséhez (például: .\tcping.exe 10.0.0.4 3389), és jegyezze fel az eredményeket. 
* Ha nem érkezik válasz a ping tesztek során, akkor
    - Futtasson egyidejű netsh-nyomkövetést a cél háttérrendszer-készlet virtuális gépén és egy másik, ugyanabból a VNet származó teszt virtuális gépről. Most futtasson egy PsPing-tesztet egy ideig, gyűjtsön néhány hálózati nyomkövetést, majd állítsa le a tesztet. 
    - Elemezze a hálózati rögzítést, és ellenőrizze, hogy vannak-e a ping lekérdezéshez kapcsolódó bejövő és kimenő csomagok is. 
        - Ha nem figyelhetők meg bejövő csomagok a háttérrendszer-készlet virtuális gépen, akkor potenciálisan hálózati biztonsági csoportok vagy UDR hibás konfiguráció blokkolja a forgalmat. 
        - Ha nem figyelhetők meg kimenő csomagok a háttérrendszer-készlet virtuális gépen, a virtuális gépet ellenőrizni kell a nem kapcsolódó problémák miatt (például az alkalmazás blokkolja a mintavételi portot). 
    - Ellenőrizze, hogy a mintavételi csomagok kényszerítve vannak-e egy másik célra (valószínűleg UDR-beállításokon keresztül) a terheléselosztó elérése előtt. Ez azt eredményezheti, hogy a forgalom soha nem éri el a háttérbeli virtuális gépet. 
* Módosítsa a mintavételi típust (például HTTP-ről TCP-re), és konfigurálja a megfelelő portot a hálózati biztonsági csoportok ACL-jei és a tűzfalon annak ellenőrzéséhez, hogy a probléma a mintavételi válasz konfigurációjának megfelelően van-e kiválasztva. További információ az állapot-mintavételezési konfigurációról: a végpont terheléselosztási [állapotának mintavételi beállítása](https://blogs.msdn.microsoft.com/mast/2016/01/26/endpoint-load-balancing-heath-probe-configuration-details/).

## <a name="symptom-vms-behind-load-balancer-are-not-responding-to-traffic-on-the-configured-data-port"></a>Tünet: Load Balancer mögötti virtuális gépek nem válaszolnak a konfigurált adatporton tárolt forgalomra

Ha a háttérrendszer-készlet virtuális gépe kifogástalan állapotú, és válaszol az állapotfigyelő tesztekre, de továbbra sem vesz részt a terheléselosztásban, vagy nem válaszol az adatforgalomra, az a következő okok bármelyike miatt előfordulhat: 
* Load Balancer háttérbeli készlet virtuális gépe nem figyeli az adatportot 
* A hálózati biztonsági csoport blokkolja a portot a Load Balancer háttérrendszer-készlet virtuális gépén.  
* A Load Balancer elérése ugyanarról a virtuális gépről és hálózati adapterről 
* Az Internet Load Balancer frontend elérése a résztvevő Load Balancer háttérbeli készletből származó virtuális gépről 

### <a name="cause-1-load-balancer-backend-pool-vm-is-not-listening-on-the-data-port"></a>1. ok: Load Balancer háttérbeli készlet virtuális gépe nem figyeli az adatportot 
Ha egy virtuális gép nem válaszol az adatforgalomra, annak oka az lehet, hogy a célként megadott port nincs megnyitva a résztvevő virtuális gépen, vagy a virtuális gép nem figyeli a portot. 

**Ellenőrzés és megoldás**

1. Jelentkezzen be a háttérbeli virtuális gépre. 
2. Nyisson meg egy parancssort, és futtassa a következő parancsot annak ellenőrzéséhez, hogy egy alkalmazás figyeli-e az adatportot:   netstat-a 
3. Ha a port nem szerepel a "LISTENing" állapotú állapotban, konfigurálja a megfelelő figyelő portot. 
4. Ha a port figyelt van megjelölve, akkor bármely lehetséges probléma esetén ellenőrizze az adott porton található célalkalmazás használatát.

### <a name="cause-2-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vm"></a>2. ok: a hálózati biztonsági csoport blokkolja a portot a Load Balancer háttérrendszer-készlet virtuális gépén.  

Ha az alhálózaton vagy a virtuális gépen konfigurált egy vagy több hálózati biztonsági csoport blokkolja a forrás IP-címet vagy portot, akkor a virtuális gép nem tud válaszolni.

A nyilvános terheléselosztó esetében az internetes ügyfelek IP-címe az ügyfelek és a terheléselosztó háttérbeli virtuális gépek közötti kommunikációhoz lesz használva. Győződjön meg arról, hogy az ügyfelek IP-címe engedélyezett a háttérbeli virtuális gép hálózati biztonsági csoportjában.

1. A háttérbeli virtuális gépen konfigurált hálózati biztonsági csoportok listázása. További információ: [hálózati biztonsági csoportok kezelése](../virtual-network/manage-network-security-group.md)
1. A hálózati biztonsági csoportok listájából ellenőrizze, hogy:
    - az adatporton bejövő vagy kimenő forgalom interferenciát okoz. 
    - az összes hálózati biztonsági csoport **megtagadása** a virtuális gép hálózati adapterén vagy az alhálózaton, amelynek nagyobb a prioritása, hogy az Load Balancer-mintavételt és-forgalmat engedélyező alapértelmezett szabály (a hálózati biztonsági csoportoknak engedélyezniük kell a 168.63.129.16 Load Balancer IP-címét, azaz a mintavételi portot)
1. Ha a szabályok bármelyike blokkolja a forgalmat, távolítsa el és konfigurálja újra ezeket a szabályokat az adatforgalom engedélyezéséhez.  
1. Tesztelje, hogy a virtuális gép most már elindult-e az állapotra.

### <a name="cause-3-accessing-the-load-balancer-from-the-same-vm-and-network-interface"></a>3. ok: a Load Balancer elérése ugyanarról a virtuális gépről és hálózati adapterről 

Ha egy Load Balancer háttérbeli virtuális gépén lévő alkalmazása megpróbál elérni egy másik, ugyanazon a háttér-virtuális gépen futó alkalmazást ugyanazon a hálózati adapteren keresztül, akkor ez nem támogatott forgatókönyv, és sikertelen lesz. 

**Megoldás** A probléma a következő módszerek egyikével oldható meg:
* Különálló háttérrendszer-készlet virtuális gépek konfigurálása egy alkalmazásban. 
* Konfigurálja az alkalmazást kettős NIC virtuális gépeken, hogy minden alkalmazás a saját hálózati adapterét és IP-címét használja. 

### <a name="cause-4-accessing-the-internal-load-balancer-frontend-from-the-participating-load-balancer-backend-pool-vm"></a>4. ok: a belső Load Balancer előtér elérésének elérése a résztvevő Load Balancer backend Pool virtuális gépről

Ha egy belső Load Balancer konfigurálva van egy VNet belül, és a résztvevői háttérbe tartozó virtuális gépek egyike megpróbál hozzáférni a belső Load Balancer előtérhöz, a hibák akkor fordulhatnak elő, ha a folyamat a kezdeményező virtuális gépre van leképezve. Ez a forgatókönyv nem támogatott.

**Megoldás** A forgatókönyv feloldásának számos módja van, beleértve a proxy használatát is. Értékelje ki Application Gateway vagy más harmadik féltől származó proxyt (például Nginx vagy HAProxy). További információ a Application Gatewayról: [Application Gateway áttekintése](../application-gateway/application-gateway-introduction.md)

**Részletek** A belső terheléselosztó nem fordítja le a kimenő kapcsolatokat egy belső Load Balancer elülső végére, mert mindkettő magánhálózati IP-címtartomány részét képezi. A nyilvános terheléselosztó [kimenő kapcsolatokat](load-balancer-outbound-connections.md) biztosít a virtuális hálózaton belüli magánhálózati IP-címekről a nyilvános IP-címekre. A belső terheléselosztó esetében ez a módszer elkerüli a lehetséges SNAT-portok kimerülését egy egyedi belső IP-címtartomány belsejében, ahol a fordítás nem szükséges.

Ennek egyik mellékhatása, hogy ha a háttér-készletben lévő virtuális gépről érkező kimenő forgalom a készlet belső Load Balancerának végére kísérli meg a folyamatot, _és_ az önmagához van rendelve, a folyamat két lába nem egyezik. Mivel nem egyeznek, a folyamat meghiúsul. A folyamat abban az esetben sikeres, ha a folyamat nem képezi vissza ugyanazt a virtuális gépet a háttér-készletben, amely létrehozta a folyamatot az előtér-végpontnak.

Ha a folyamat önmagára mutat, a kimenő folyamat úgy tűnik, hogy a virtuális gépről származik, és a megfelelő bejövő folyamat úgy tűnik, hogy a virtuális gépről származik. A vendég operációs rendszer szempontjából az ugyanazon folyamat bejövő és kimenő részei nem egyeznek meg a virtuális géppel. A TCP-verem nem ismeri fel ugyanezen folyamat azon felét, mint ugyanazon folyamat részeként. A forrás és a cél nem egyezik. Ha a folyamat a háttérbeli készlet bármely más virtuális gépe felé mutat, a folyamat felei megegyeznek, és a virtuális gép válaszolni tud a folyamatra.

Ennek a forgatókönyvnek a tünete átmeneti kapcsolati időtúllépés, amikor a folyamat visszatér ugyanarra a háttérre, amely a folyamatból származik. Gyakori megkerülő megoldások közé tartozik a belső Load Balancer mögötti proxy réteg beszúrása, valamint a közvetlen kiszolgáló Return (DSR) stílusú szabályok használata. További információ: [Azure Load Balancer több előtérbeli felülete](load-balancer-multivip-overview.md).

A belső Load Balancer bármely harmadik féltől származó proxyval egyesítheti, vagy használhat belső [Application Gateway](../application-gateway/application-gateway-introduction.md) a proxy forgatókönyvekhez HTTP/HTTPS használatával. Habár a probléma megoldásához nyilvános Load Balancer is használható, az eredményül kapott forgatókönyv a [SNAT kimerülését](load-balancer-outbound-connections.md#snat)eredményezi. Kerülje ezt a második megközelítést, ha körültekintően nem felügyelt.

## <a name="symptom-cannot-change-backend-port-for-existing-lb-rule-of-a-load-balancer-which-has-vm-scale-set-deployed-in-the-backend-pool"></a>Tünet: nem módosítható a háttér-port egy olyan terheléselosztó meglévő LB-szabályához, amely a háttér-készletben üzembe helyezett virtuálisgép-méretezési csoporttal rendelkezik. 
### <a name="cause--the-backend-port-cannot-be-modified-for-a-load-balancing-rule-thats-used-by-a-health-probe-for-load-balancer-referenced-by-vm-scale-set"></a>Ok: a háttér-port nem módosítható olyan terheléselosztási szabály esetében, amelyet egy, a virtuálisgép-méretezési csoport által hivatkozott Load Balancer számára használ.
**Megoldás** A port módosításához távolítsa el az állapot-mintavételt a virtuálisgép-méretezési csoport frissítésével, frissítse a portot, majd konfigurálja újra az állapot-mintavételt.

## <a name="symptom-small-traffic-is-still-going-through-load-balancer-after-removing-vms-from-backend-pool-of-the-load-balancer"></a>Tünet: a kisméretű forgalom továbbra is a Load balancern halad át, miután eltávolította a virtuális gépeket a terheléselosztó backend-készletéből. 
### <a name="cause--vms-removed-from-backend-pool-should-no-longer-receive-traffic-the-small-amount-of-network-traffic-could-be-related-to-storage-dns-and-other-functions-within-azure"></a>Ok: a háttér-készletből eltávolított virtuális gépek nem kapják meg a forgalmat. Az Azure-ban a hálózati forgalom kis hányada lehet a tárolással, a DNS-sel és az egyéb funkciókkal. 
Az ellenőrzéshez hálózati nyomkövetést végezhet. A blob Storage-fiókokhoz használt teljes tartománynév az egyes Storage-fiókok tulajdonságai között szerepel.  Az Azure-előfizetésben található virtuális gépekről az nslookup segítségével meghatározhatja az adott Storage-fiókhoz rendelt Azure IP-címet.

## <a name="additional-network-captures"></a>További hálózati rögzítések
Ha úgy dönt, hogy megnyit egy támogatási esetet, a következő információkat kell összegyűjtenie a gyorsabb megoldás érdekében. A következő tesztek végrehajtásához válasszon egyetlen háttérbeli virtuális gépet:
- A VNet belül található egyik háttérbeli virtuális gép Psping használatával tesztelheti a mintavételi port válaszát (például: Psping 10.0.0.4:3389), és rögzíthet eredményeket. 
- Ha nem érkezik válasz ezen pingelési tesztekben, futtasson egyidejű netsh-nyomkövetést a háttérbeli virtuális gépen és a VNet teszt virtuális gépen a PsPing futtatásakor, majd állítsa le a netsh nyomkövetést. 
 
## <a name="next-steps"></a>Következő lépések

Ha a fenti lépések nem oldják meg a problémát, nyisson meg egy [támogatási jegyet](https://azure.microsoft.com/support/options/).

