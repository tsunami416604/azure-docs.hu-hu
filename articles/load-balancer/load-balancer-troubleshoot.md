---
title: Az Azure Load Balancer hibaelhárítása
description: Ismerje meg, hogy miként lehet elhárítani a Azure Load Balancer ismert problémáit.
services: load-balancer
documentationcenter: na
author: chadmath
manager: dcscontentpm
ms.custom: seodoc18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/09/2018
ms.author: genli
ms.openlocfilehash: d1c10fa8267131f13d3148ace6c97218a18fd494
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076924"
---
# <a name="troubleshoot-azure-load-balancer"></a>Az Azure Load Balancer hibaelhárítása

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Ez az oldal a gyakori Azure Load Balancer kérdésekre vonatkozó hibaelhárítási információkat tartalmaz. Ha a Load Balancer kapcsolat nem érhető el, a leggyakoribb tünetek a következők: 
- A Load Balancer mögötti virtuális gépek nem válaszolnak az állapot-mintavételre 
- A Load Balancer mögötti virtuális gépek nem válaszolnak a konfigurált porton lévő forgalomra

## <a name="symptom-vms-behind-the-load-balancer-are-not-responding-to-health-probes"></a>Tünet: a Load Balancer mögötti virtuális gépek nem válaszolnak az állapot-mintavételre
Ahhoz, hogy a háttér-kiszolgálók részt vegyenek a terheléselosztó készletében, át kell adni a mintavétel-ellenőrzést. További információ az állapot-mintavételekről: Load Balancer mintavételek [ismertetése](load-balancer-custom-probe-overview.md). 

Előfordulhat, hogy a Load Balancer backend-készlet virtuális gépei nem válaszolnak a mintavételre a következő okok miatt: 
- Load Balancer háttérbeli készlet virtuális gépe nem kifogástalan állapotú 
- Load Balancer háttérbeli készlet virtuális gépe nem figyeli a mintavételi portot 
- A tűzfal vagy egy hálózati biztonsági csoport blokkolja a portot a Load Balancer backend-készletben lévő virtuális gépeken 
- Egyéb helytelen konfigurációk Load Balancer

### <a name="cause-1-load-balancer-backend-pool-vm-is-unhealthy"></a>1\. ok: Load Balancer háttérbeli készlet virtuális gépe sérült 

**Ellenőrzés és megoldás**

A probléma megoldásához jelentkezzen be a részt vevő virtuális gépekre, és ellenőrizze, hogy a virtuális gép állapota Kifogástalan-e, és tud-e válaszolni a **PsPing** vagy a **TCPING** egy másik virtuális gépről a készletben. Ha a virtuális gép nem kifogástalan állapotú, vagy nem tud válaszolni a mintavételre, ki kell javítania a problémát, és vissza kell állítania a virtuális gépet egy Kifogástalan állapotba, mielőtt részt tudjon venni a terheléselosztásban.

### <a name="cause-2-load-balancer-backend-pool-vm-is-not-listening-on-the-probe-port"></a>2\. ok: Load Balancer háttérbeli készlet virtuális gépe nem figyeli a mintavételi portot
Ha a virtuális gép kifogástalan állapotban van, de nem válaszol a mintavételre, az egyik lehetséges ok az lehet, hogy a mintavételi port nincs megnyitva a résztvevő virtuális gépen, vagy a virtuális gép nem figyeli a portot.

**Ellenőrzés és megoldás**

1. Jelentkezzen be a háttérbeli virtuális gépre. 
2. Nyisson meg egy parancssort, és futtassa a következő parancsot annak ellenőrzéséhez, hogy van-e olyan alkalmazás, amely figyeli a mintavételi portot:   
            netstat – a
3. Ha a port állapota nem **figyelt**van felsorolva, konfigurálja a megfelelő portot. 
4. Másik lehetőségként kiválaszthat egy másik portot, amely **Figyelésként**jelenik meg, és ennek megfelelően frissíti a terheléselosztó konfigurációját.              

### <a name="cause-3-firewall-or-a-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vms"></a>3\. ok: a tűzfal vagy egy hálózati biztonsági csoport blokkolja a terheléselosztó háttér-készletének virtuális gépei portját.  
Ha a virtuális gépen lévő tűzfal blokkolja a mintavételi portot, vagy egy vagy több, az alhálózaton vagy a virtuális gépen konfigurált hálózati biztonsági csoport nem engedélyezi a mintavételt a port eléréséhez, a virtuális gép nem tud válaszolni az állapot-mintavételre.          

**Ellenőrzés és megoldás**

* Ha a tűzfal engedélyezve van, ellenőrizze, hogy be van-e állítva a mintavételi port engedélyezéséhez. Ha nem, konfigurálja úgy a tűzfalat, hogy engedélyezze a forgalmat a mintavételi porton, majd tesztelje újra. 
* A hálózati biztonsági csoportok listájából ellenőrizze, hogy a mintavételi port bejövő vagy kimenő forgalma interferencia-e. 
* Azt is ellenőrizze, hogy az összes hálózati biztonsági csoport **megtagadva** szabály a virtuális gép hálózati adapterén vagy az alhálózaton, amely magasabb prioritású, mint az LB-szondák & forgalmat engedélyező alapértelmezett szabálynál (a hálózati biztonsági csoportoknak engedélyezniük kell Load Balancer IP-168.63.129.16). 
* Ha a szabályok bármelyike blokkolja a mintavételi forgalmat, távolítsa el és konfigurálja újra a szabályokat a mintavételi forgalom engedélyezéséhez.  
* Tesztelje, hogy a virtuális gép most már megkezdte-e a válaszadást az állapotra. 

### <a name="cause-4-other-misconfigurations-in-load-balancer"></a>4\. ok: egyéb helytelen konfigurációk Load Balancer
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

### <a name="cause-1-load-balancer-backend-pool-vm-is-not-listening-on-the-data-port"></a>1\. ok: Load Balancer háttérbeli készlet virtuális gépe nem figyeli az adatportot 
Ha egy virtuális gép nem válaszol az adatforgalomra, annak oka az lehet, hogy a célként megadott port nincs megnyitva a résztvevő virtuális gépen, vagy a virtuális gép nem figyeli a portot. 

**Ellenőrzés és megoldás**

1. Jelentkezzen be a háttérbeli virtuális gépre. 
2. Nyisson meg egy parancssort, és futtassa a következő parancsot annak ellenőrzéséhez, hogy az alkalmazás figyeli-e az adatportot:  netstat-an 
3. Ha a port nem szerepel a "LISTENing" állapotú állapotban, konfigurálja a megfelelő figyelő portot. 
4. Ha a port figyelt van megjelölve, akkor bármely lehetséges probléma esetén ellenőrizze az adott porton található célalkalmazás használatát. 

### <a name="cause-2-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vm"></a>2\. ok: a hálózati biztonsági csoport blokkolja a portot a Load Balancer háttérrendszer-készlet virtuális gépén.  

Ha az alhálózaton vagy a virtuális gépen konfigurált egy vagy több hálózati biztonsági csoport blokkolja a forrás IP-címet vagy portot, akkor a virtuális gép nem tud válaszolni.

* A háttérbeli virtuális gépen konfigurált hálózati biztonsági csoportok listázása. További információ: [hálózati biztonsági csoportok kezelése](../virtual-network/manage-network-security-group.md).
* A hálózati biztonsági csoportok listájából ellenőrizze, hogy:
    - az adatporton bejövő vagy kimenő forgalom interferenciát okoz. 
    - az összes hálózati biztonsági csoport **megtagadása** a virtuális gép hálózati adapterén vagy az alhálózaton, amelynek nagyobb a prioritása, hogy az Load Balancer-mintavételt és-forgalmat engedélyező alapértelmezett szabály (a hálózati biztonsági csoportoknak engedélyezniük kell a 168.63.129.16 Load Balancer IP-címét, azaz a mintavételi portot) 
* Ha a szabályok bármelyike blokkolja a forgalmat, távolítsa el és konfigurálja újra ezeket a szabályokat az adatforgalom engedélyezéséhez.  
* Tesztelje, hogy a virtuális gép most már elindult-e az állapotra.

### <a name="cause-3-accessing-the-load-balancer-from-the-same-vm-and-network-interface"></a>3\. ok: a Load Balancer elérése ugyanarról a virtuális gépről és hálózati adapterről 

Ha egy Load Balancer háttérbeli virtuális gépén lévő alkalmazása megpróbál elérni egy másik, ugyanazon a háttér-virtuális gépen futó alkalmazást ugyanazon a hálózati adapteren keresztül, akkor ez nem támogatott forgatókönyv, és sikertelen lesz. 

**Megoldás** A probléma a következő módszerek egyikével oldható meg:
* Különálló háttérrendszer-készlet virtuális gépek konfigurálása egy alkalmazásban. 
* Konfigurálja az alkalmazást kettős NIC virtuális gépeken, hogy minden alkalmazás a saját hálózati adapterét és IP-címét használja. 

### <a name="cause-4-accessing-the-internal-load-balancer-frontend-from-the-participating-load-balancer-backend-pool-vm"></a>4\. ok: a belső Load Balancer előtér elérésének elérése a résztvevő Load Balancer backend Pool virtuális gépről

Ha egy belső Load Balancer konfigurálva van egy VNet belül, és a résztvevői háttérbe tartozó virtuális gépek egyike megpróbál hozzáférni a belső Load Balancer előtérhöz, a hibák akkor fordulhatnak elő, ha a folyamat a kezdeményező virtuális gépre van leképezve. Ez a forgatókönyv nem támogatott. Tekintse át a részletes megbeszélések [korlátozásait](load-balancer-overview.md#limitations) .

**Megoldás** A forgatókönyv feloldásának számos módja van, beleértve a proxy használatát is. Értékelje ki Application Gateway vagy más harmadik féltől származó proxyt (például Nginx vagy HAProxy). További információ a Application Gatewayról: [Application Gateway áttekintése](../application-gateway/application-gateway-introduction.md)

## <a name="additional-network-captures"></a>További hálózati rögzítések
Ha úgy dönt, hogy megnyit egy támogatási esetet, a következő információkat kell összegyűjtenie a gyorsabb megoldás érdekében. A következő tesztek végrehajtásához válasszon egyetlen háttérbeli virtuális gépet:
- A VNet belül található egyik háttérbeli virtuális gép Psping használatával tesztelheti a mintavételi port válaszát (például: Psping 10.0.0.4:3389), és rögzíthet eredményeket. 
- Ha nem érkezik válasz ezen pingelési tesztekben, futtasson egyidejű netsh-nyomkövetést a háttérbeli virtuális gépen és a VNet teszt virtuális gépen a PsPing futtatásakor, majd állítsa le a netsh nyomkövetést. 
  
## <a name="next-steps"></a>További lépések

Ha a fenti lépések nem oldják meg a problémát, nyisson meg egy [támogatási jegyet](https://azure.microsoft.com/support/options/).

