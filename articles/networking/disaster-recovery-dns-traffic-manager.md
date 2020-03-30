---
title: Katasztrófa utáni helyreállítás az Azure DNS és Traffic Manager használatával | Microsoft dokumentumok
description: A vész-helyreállítási megoldások áttekintése az Azure DNS és traffic manager használatával.
services: dns
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/08/2018
ms.author: kumud
ms.openlocfilehash: 6eab1803bf5adab42be87b5f8567682c6d75947e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74483533"
---
# <a name="disaster-recovery-using-azure-dns-and-traffic-manager"></a>Vészhelyreállítás az Azure DNS-sel és a Traffic Managerrel

A vészhelyreállítás az alkalmazás funkcióinak súlyos elvesztéséből való helyreállításra összpontosít. A vész-helyreállítási megoldás kiválasztásához az üzleti és technológiai tulajdonosoknak először meg kell határozniuk a katasztrófa során szükséges funkcionalitás szintjét, például - nem érhető el, részben elérhető csökkentett funkcionalitással vagy késleltetett rendelkezésre állással, vagy teljes mértékben rendelkezésre állnak.
A legtöbb vállalati ügyfél egy több régióra kiterjedő architektúrát választ az alkalmazás- vagy infrastruktúraszintű feladatátvételhez. Az ügyfelek több megközelítést is választhatnak a feladat során a feladatátvétel és a magas rendelkezésre állás elérése redundáns architektúrán keresztül. Íme néhány a népszerű megközelítések:

- **Aktív-passzív hideg készenléti:** Ebben a feladatátvételi megoldásban a virtuális gépek és más, a készenléti régióban futó készülékek nem aktívak, amíg nincs szükség feladatátvételre. Az éles környezet azonban biztonsági mentések, virtuálisgép-lemezképek vagy Erőforrás-kezelő sablonok formájában replikálódik egy másik régióba. Ez a feladatátvételi mechanizmus költséghatékony, de hosszabb időt vesz igénybe a teljes feladatátvétel elvégzéséhez.
 
    ![Aktív/passzív hideg készenléti állapottal](./media/disaster-recovery-dns-traffic-manager/active-passive-with-cold-standby.png)
    
    *Ábra - Aktív/passzív hideg készenléti vész-helyreállítási konfigurációval*

- **Aktív/passzív a jelzőlámpával:** Ebben a feladatátvételi megoldásban a készenléti környezet minimális konfigurációval van beállítva. A telepítő csak a szükséges szolgáltatások fut, hogy támogassa csak egy minimális és kritikus alkalmazások. A natív formában ez a forgatókönyv csak minimális funkciókat hajthat végre, de felskálázhatja és további szolgáltatásokat hozhat létre, hogy a termelési terhelés nagy részét, ha feladatátvétel történik.
    
    ![Aktív/passzív jelzőfénnyel](./media/disaster-recovery-dns-traffic-manager/active-passive-with-pilot-light.png)
    
    *Ábra: Aktív/passzív a jelzőfény vész-helyreállítási konfigurációjával*

- **Aktív/passzív meleg készenléti:** Ebben a feladatátvételi megoldásban a készenléti terület előmelegedett, és készen áll az alapterhelésre, az automatikus skálázás be van kapcsolva, és az összes példány működik. Ez a megoldás nincs méretezve, hogy a teljes termelési terhelés, de működőképes, és minden szolgáltatás működik. Ez a megoldás a jelzőfény megközelítés kibővített változata.
    
    ![Aktív/passzív meleg készenléti állapottal](./media/disaster-recovery-dns-traffic-manager/active-passive-with-warm-standby.png)
    
    *Ábra: Aktív/passzív meleg készenléti vész-helyreállítási konfigurációval*
    
A feladatátvételről és a magas rendelkezésre állásról a [Vész-helyreállítási az Azure-alkalmazásokhoz](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications)( Vészhelyreállítás az Azure Applications hez) témakörben olvashat bővebben.


## <a name="planning-your-disaster-recovery-architecture"></a>A vész-helyreállítási architektúra megtervezése

A vész-helyreállítási architektúra beállításának két technikai szempontja van:
-  Telepítési mechanizmus használata példányok, adatok és konfigurációk replikálására az elsődleges és a készenléti környezetek között. Az ilyen típusú vészhelyreállítás az Azure Site-Recovery szolgáltatáson keresztül natív módon elvégezhető a Microsoft Azure partnerkészülékein/szolgáltatásain, például a Veritason vagy a NetAppon keresztül. 
- Megoldás kidolgozása a hálózati/webes forgalom elsődleges helyről készenléti helyre történő átirányítására. Az ilyen típusú vészhelyreállítás érhető el az Azure DNS, az Azure Traffic Manager(DNS) vagy a külső globális terheléselosztók keresztül.

Ez a cikk a hálózati és webes forgalom átirányításán keresztüli megközelítésekre korlátozódik. Az Azure Site Recovery beállítására vonatkozó utasításokat az [Azure Site Recovery dokumentációjában találja.](https://docs.microsoft.com/azure/site-recovery/)
A DNS az egyik leghatékonyabb mechanizmus a hálózati forgalom átirányítására, mivel a DNS gyakran globális és az adatközponton kívüli, és el van szigetelve bármely regionális vagy rendelkezésre állási zóna (AZ) szintű hibától. Dns-alapú feladatátvételi mechanizmust használhatunk, és az Azure-ban két DNS-szolgáltatás is elvégezheti ugyanezt valamilyen módon – az Azure DNS (mérvadó DNS) és az Azure Traffic Manager (DNS-alapú intelligens forgalmi útválasztás). 

Fontos megérteni néhány fogalmat a DNS-ben, amelyek széles körben használják, hogy megvitassák a megoldásokat ebben a cikkben:
- **DNS A rekord** – A rekordok olyan mutatók, amelyek egy tartományt egy IPv4-címre mutatnak. 
- **CNAME vagy Canonical name** – Ez a bejegyzéstípus egy másik DNS-rekordra mutat. A CNAME nem IP-címmel válaszol, hanem az IP-címet tartalmazó rekordra mutató mutató. 
- **Súlyozott útválasztás** – választhat, hogy súlyt társítanak a szolgáltatás végpontjaihoz, majd a forgalmat a hozzárendelt súlyok alapján osztják el. Ez az útválasztási módszer a Traffic Manager ben elérhető négy forgalomútválasztási mechanizmus egyike. További információt a [Súlyozott útválasztási módszer című](../traffic-manager/traffic-manager-routing-methods.md#weighted)témakörben talál.
- **Prioritás-műveletválasztás** – A prioritásos útválasztás a végpontok állapotellenőrzésein alapul. Alapértelmezés szerint az Azure Traffic Manager elküldi az összes forgalmat a legmagasabb prioritású végpontra, és egy hiba vagy katasztrófa esetén a Traffic Manager a forgalmat a másodlagos végpontra irányítja. További információt a [Prioritás-útválasztási módszer című](../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method)témakörben talál.

## <a name="manual-failover-using-azure-dns"></a>Manuális feladatátvétel az Azure DNS használatával
Az Azure DNS-kézikönyv feladatátvételi megoldás a vész-helyreállítási használja a szabványos DNS-mechanizmus feladatátvétela a biztonsági mentési hely. Az Azure DNS-en keresztüli manuális beállítás akkor működik a legjobban, ha a hideg készenléti állapottal vagy a jelzőfény megközelítésével együtt használja. 

![Manuális feladatátvétel az Azure DNS használatával](./media/disaster-recovery-dns-traffic-manager/manual-failover-using-dns.png)

*Ábra – Manuális feladatátvétel az Azure DNS használatával*

A megoldásra vonatkozó feltételezések a következők:
- Az elsődleges és a másodlagos végpontok statikus IP-k, amelyek nem változnak gyakran. Mondja az elsődleges hely az IP 100.168.124.44 és az IP a másodlagos hely 100.168.124.43.
- Egy Azure DNS-zóna létezik az elsődleges és a másodlagos hely. Tegyük fel, hogy az elsődleges helyhez a végpont prod.contoso.com, és a biztonsági mentési hely dr.contoso.com. A fő alkalmazáshoz létezik\.egy DNS-rekord is, amelyet www contoso.com néven ismernek.   
- A TTL a szervezetben beállított RTO SLA-n vagy az alatt található. Ha például egy vállalat az alkalmazás katasztrófa-elhárításának RTO-ját 60 mins-re állítja be, akkor a TTL-nek 60 min-nél kisebbnek kell lennie, lehetőleg minél alacsonyabb, annál jobb. 
  Az Azure DNS-t a következőképpen állíthatja be a manuális feladatátvételhez:
- DNS-zóna létrehozása
- DNS-zónarekordok létrehozása
- CNAME rekord frissítése

### <a name="step-1-create-a-dns"></a>1. lépés: DNS létrehozása
Hozzon létre egy DNS-zónát (például www\.contoso.com), az alábbiak szerint:

![DNS-zóna létrehozása az Azure-ban](./media/disaster-recovery-dns-traffic-manager/create-dns-zone.png)

*Ábra – DNS-zóna létrehozása az Azure-ban*

### <a name="step-2-create-dns-zone-records"></a>2. lépés: DNS-zónarekordok létrehozása

Ezen a zónán belül hozzon\.létre három rekordot (például www contoso.com, prod.contoso.com és dr.consoto.com) az alábbiak szerint.

![DNS-zónarekordok létrehozása](./media/disaster-recovery-dns-traffic-manager/create-dns-zone-records.png)

*Ábra – DNS-zónarekordok létrehozása az Azure-ban*

Ebben a forgatókönyvben\.a www contoso.com 30 mins TTL-vel rendelkezik, amely jóval a megadott RTO alatt van, és a termelési helyszínre mutat, prod.contoso.com. Ez a konfiguráció normál üzleti műveletek során történik. A prod.contoso.com és dr.contoso.com TTL-je 300 másodpercre vagy 5 másodpercre van állítva. Használhatja az Azure figyelési szolgáltatás, például az Azure Monitor vagy az Azure App Insights, vagy bármely partner figyelési megoldások, például a Dynatrace, Akár otthoni megoldásokat is használhat, amelyek figyelhetik vagy észlelhetik az alkalmazás vagy a virtuális infrastruktúra szintű hibákat.

### <a name="step-3-update-the-cname-record"></a>3. lépés: A CNAME rekord frissítése

A hiba észlelése után módosítsa a rekord értékét úgy, hogy dr.contoso.com mutasson az alábbi módon:
       
![CNAME rekord frissítése](./media/disaster-recovery-dns-traffic-manager/update-cname-record.png)

*Ábra – A CNAME rekord frissítése az Azure-ban*

30 percen belül, amely alatt a legtöbb feloldó frissíti\.a gyorsítótárazott zónafájlt, a www contoso.com lekérdezései dr.contoso.com lesznek irányítva.
A CNAME érték módosításához a következő Azure CLI-parancsot is futtathatja:
 ```azurecli
   az network dns record-set cname set-record \
   --resource-group 123 \
   --zone-name contoso.com \
   --record-set-name www \
   --cname dr.contoso.com
```
Ez a lépés manuálisan vagy automatizálással hajtható végre. Ezt manuálisan a konzolon keresztül vagy az Azure CLI-n keresztül végezheti el. Az Azure SDK és API segítségével automatizálhatja a CNAME frissítést, így nincs szükség manuális beavatkozásra. Az automatizálás azure-funkciókon keresztül vagy egy külső monitorozási alkalmazáson belül, vagy akár a helyszíni környezetben is létrehozható.

### <a name="how-manual-failover-works-using-azure-dns"></a>A manuális feladatátvétel működése az Azure DNS használatával
Mivel a DNS-kiszolgáló kívül esik a feladatátvételi vagy katasztrófazónán, minden állásidőtől szigetelve van. Ez lehetővé teszi a felhasználó számára, hogy egy egyszerű feladatátvételi forgatókönyv, amely költséghatékony, és működni fog minden alkalommal feltételezve, hogy az üzemeltető hálózati kapcsolat katasztrófa során, és lehet, hogy a flip. Ha a megoldás parancsfájllal van elvezetve, akkor biztosítani kell, hogy a parancsfájlt futtató kiszolgáló vagy szolgáltatás szigetelve legyen az éles környezetet érintő problémával szemben. Is, ne feledje, hogy az alacsony TTL, amely a zónához beállított, hogy nincs feloldó világszerte tartja a végpont gyorsítótárazott hosszú és az ügyfelek hozzáférhetnek a hely az RTO-n belül. A hideg készenléti és a jelzőlámpa, mivel néhány prewarming és egyéb adminisztratív tevékenység lehet szükség - az egyik is meg kell adni elég időt, mielőtt a flip.

## <a name="automatic-failover-using-azure-traffic-manager"></a>Automatikus feladatátvétel az Azure Traffic Manager használatával
Ha összetett architektúrákkal és több erőforráskészlettel rendelkezik, amelyek képesek ugyanazt a funkciót végrehajtani, konfigurálhatja az Azure Traffic Managert (a DNS alapján) az erőforrások állapotának ellenőrzésére és a nem kifogástalan állapotú erőforrásból a kifogástalan állapotú erőforráshoz való forgalom átirányítására Erőforrás. A következő példában az elsődleges és a másodlagos régió teljes telepítéssel rendelkezik. Ez a központi telepítés magában foglalja a felhőszolgáltatások és a szinkronizált adatbázis. 

![Automatikus feladatátvétel az Azure Traffic Manager használatával](./media/disaster-recovery-dns-traffic-manager/automatic-failover-using-traffic-manager.png)

*Ábra – Automatikus feladatátvétel az Azure Traffic Manager használatával*

Azonban csak az elsődleges régió aktívan kezeli a felhasználók hálózati kéréseit. A másodlagos régió csak akkor válik aktívvá, ha az elsődleges régió szolgáltatáskimaradást tapasztal. Ebben az esetben minden új hálózati kérelem a másodlagos régióba vezet. Mivel az adatbázis biztonsági mentése közel azonnali, mind a terheléselosztók rendelkeznek IP-címekkel, amelyek állapotszempontjából ellenőrizhetők, és a példányok mindig vannak és futnak, ez a topológia lehetőséget biztosít az alacsony RTO és a feladatátvétel manuális beavatkozás nélküli betöltésére. A másodlagos feladatátvételi régiónak készen kell lennie az elsődleges régió meghibásodása után azonnal való használatra.
Ez a forgatókönyv ideális az Azure Traffic Manager, amely beépített mintavételek különböző típusú állapot-ellenőrzések, beleértve a http / https és a TCP használatát. Az Azure Traffic Manager is rendelkezik egy szabálymotor, amely konfigurálható feladatátvétel, ha hiba történik az alábbiakban leírtak szerint. Vegyük figyelembe a következő megoldást a Traffic Manager használatával:
- Az ügyfél rendelkezik a régió #1 végpont néven prod.contoso.com statikus IP 100.168.124.44 és a régió #2 végpont néven ismert dr.contoso.com statikus IP 100.168.124.43. 
-   Ezek a környezetek mindegyike egy nyilvános tulajdonon keresztül, például terheléselosztón keresztül kerül előirányozva. A terheléselosztó beállítható úgy, hogy DNS-alapú végponttal vagy teljesen minősített tartománynévvel (FQDN) rendelkezzen a fentiek szerint.
-   A 2-es régió összes példánya közel valós idejű replikációban van az 1-es régióval. Továbbá a géplemezképek naprakészek, és minden szoftver/konfigurációs adat javításra kerül, és összhangban vannak az 1.  
-   Az automatikus skálázás előre konfigurálva van. 

A feladatátvétel Azure Traffic Managerrel való konfigurálásához a következő lépések:
1. Új Azure Traffic Manager-profil létrehozása
2. Végpontok létrehozása a Traffic Manager-profilon belül
3. Állapot-ellenőrzés és feladatátvétel ibeállításainak beállítása

### <a name="step-1-create-a-new-azure-traffic-manager-profile"></a>1. lépés: Új Azure Traffic Manager-profil létrehozása
Hozzon létre egy új Azure Traffic Manager-profilt contoso123 névvel, és válassza ki az Útválasztási metódust prioritásként. Ha van egy már meglévő erőforráscsoportja, amelyhez társítani szeretne, akkor kiválaszthat egy meglévő erőforráscsoportot, ellenkező esetben létrehozhat egy új erőforráscsoportot.

![Traffic Manager-profil létrehozása](./media/disaster-recovery-dns-traffic-manager/create-traffic-manager-profile.png)

*Ábra – Traffic Manager-profil létrehozása*

### <a name="step-2-create-endpoints-within-the-traffic-manager-profile"></a>2. lépés: Végpontok létrehozása a Traffic Manager-profilon belül

Ebben a lépésben hozzon létre végpontokat, amelyek az éles és vész-helyreállítási helyekre mutatnak. Itt válassza a **típus** külső végpontként, de ha az erőforrás az Azure-ban található, akkor az **Azure-végpont** is választhat. Ha az **Azure-végpontot**választja, majd válasszon egy **célerőforrást,** amely vagy egy **App Service** vagy egy nyilvános **IP,amely** az Azure által lefoglalt. A prioritás **1-re** van állítva, mivel az 1.
Hasonlóképpen hozza létre a vész-helyreállítási végpontot a Traffic Manager-ben is.

![Vész-helyreállítási végpontok létrehozása](./media/disaster-recovery-dns-traffic-manager/create-disaster-recovery-endpoint.png)

*Ábra – Vész-helyreállítási végpontok létrehozása*

### <a name="step-3-set-up-health-check-and-failover-configuration"></a>3. lépés: Állapot-ellenőrzés és feladatátvétel ikonfigurációbeállítása

Ebben a lépésben a DNS TTL-t 10 másodpercre állítja be, amelyet a legtöbb internetre néző rekurzív feloldó tiszteletben tart. Ez a konfiguráció azt jelenti, hogy egyetlen DNS-feloldó sem gyorsítótárazza az adatokat 10 másodpercnél tovább. A végpontfigyelő beállításainál az elérési út aktuális beállítása / vagy gyökér, de testre szabhatja a végpont beállításait egy elérési út kiértékeléséhez, például prod.contoso.com/index. Az alábbi példa a **https-t** tekinti a probing protokollként. Azonban választhat **http** vagy **tcp** is. A protokoll kiválasztása a végalkalmazástól függ. A szondázási időköz 10 másodpercre van állítva, ami lehetővé teszi a gyors szondázást, és az újrapróbálkozás 3-ra van állítva. Ennek eredményeképpen a Traffic Manager feladatátvételt a második végpontra, ha három egymást követő időközönként hiba regisztrál. A következő képlet határozza meg az automatikus feladatátvétel teljes idejét: Feladatátvétel ideje = TTL + Újrapróbálkozás * Próbálkozási idő És ebben az esetben az érték 10 + 3 * 10 = 40 másodperc (Max).
Ha az Újra beállítás 1, a TTL pedig 10 mp, akkor a feladatátvétel ideje 10 + 1 * 10 = 20 másodperc. Állítsa be az újrapróbálkozást **1-nél** nagyobb értékre, hogy kiküszöbölje a téves pozitív vagy kisebb hálózati blips miatti feladatátvétel esélyét. 


![Állapot-ellenőrzés beállítása](./media/disaster-recovery-dns-traffic-manager/set-up-health-check.png)

*Ábra – Állapot-ellenőrzés és feladatátvétel beállítása*

### <a name="how-automatic-failover-works-using-traffic-manager"></a>Az automatikus feladatátvétel működése a Traffic Manager használatával

Katasztrófa esetén az elsődleges végpont ot vizsgálja meg, és az állapot **állapota leromlottállapot-változásra változik,** és a vész-helyreállítási hely **online állapotban**marad. A Traffic Manager alapértelmezés szerint minden forgalmat az elsődleges (legmagasabb prioritású) végpontra irányít. Ha az elsődleges végpont sérültnek tűnik, a Traffic Manager addig irányítja a forgalmat a második végpontra, amíg kifogástalan állapotban marad. Az egyik a traffic manageren belül további végpontok konfigurálása, amelyek további feladatátvételi végpontokként szolgálhatnak, vagy terheléselosztókként, amelyek megosztják a terhelést a végpontok között.

## <a name="next-steps"></a>További lépések
- További információ az [Azure Traffic Managerről.](../traffic-manager/traffic-manager-overview.md)
- További információ az [Azure DNS-ről.](../dns/dns-overview.md)









