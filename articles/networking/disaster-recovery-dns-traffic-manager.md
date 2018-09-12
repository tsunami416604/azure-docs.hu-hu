---
title: Az Azure DNS-ben és a Traffic Manager vész-helyreállítási |} A Microsoft Docs
description: A vészhelyreállítási megoldások az Azure DNS-ben és a Traffic Manager áttekintése.
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
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
ms.openlocfilehash: ce3e8f31c7fee6afdeabf931485a49934e98f81b
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44391351"
---
# <a name="disaster-recovery-using-azure-dns-and-traffic-manager"></a>Vészhelyreállítás az Azure DNS-sel és a Traffic Managerrel

Az alkalmazás funkciói súlyos adatvesztést helyreállva vész-helyreállítási összpontosít. Annak érdekében, hogy a vész-helyreállítási megoldást válassza, üzleti és technológiai tulajdonosok először meg kell határoznia a szükséges funkciót egy katasztrófa során például – nem érhető el, korlátozott funkciókkal, vagy a késleltetett rendelkezésre állását, részben elérhető szintjét, vagy teljes körűen elérhető.
A legtöbb vállalati ügyfelek számára az adott alkalmazás vagy infrastruktúra szolgáltatásszint feladatátvételi szembeni ellenálló-képesség a többrégiós architektúrák választja. Ügyfelek feladatátvételi és redundáns architektúra használatával magas rendelkezésre állás elérése érdekében a tanúsítványmegújítási több megközelítés közül választhatja ki. Íme néhány a népszerű megközelítés:

- **Aktív-passzív hidegtartalékkal**: a feladatátvétel megoldásban a virtuális gépek és a készenléti régióban futó egyéb készülékek nem aktívak mindaddig, amíg a feladatátvételhez szükség van. Azonban az éles környezet replikálja a rendszer a biztonsági mentések, a Virtuálisgép-rendszerképek vagy a Resource Manager-sablonok, egy másik régióba. Ez a feladatátvételi mechanizmus költséghatékony, de vállalja a feladatátvétel befejezése hosszabb időt vesz igénybe.
 
    ![Aktív/passzív hidegtartalékkal](./media/disaster-recovery-dns-traffic-manager/active-passive-with-cold-standby.png)
    
    *. Ábra – a meleg készenléti vész-helyreállítási konfiguráció aktív/passzív*

- **A jelzőlámpa aktív/passzív**: a feladatátvétel megoldásban a készenléti környezet be van állítva egy minimális konfigurációval. A telepítő csak a futó csak egy minimális igényű és kritikus fontosságú alkalmazások készlete támogatásához szükséges szolgáltatásokat tartalmaz. A natív formájában ebben a forgatókönyvben csak minimális funkciók végrehajtása, de képes vertikális felskálázás és gyemekfolyamatként létrehozni a következő további szolgáltatások esetén a feladatátvétel történik, hajtsa végre a éles terhelés tömeges.
    
    ![A jelzőlámpa aktív/passzív](./media/disaster-recovery-dns-traffic-manager/active-passive-with-pilot-light.png)
    
    *. Ábra: A vészhelyreállítási konfigurációban jelzőlámpa aktív/passzív*

- **A meleg készenlétben aktív/passzív**: a feladatátvétel megoldásban a készenléti régió van előmelegíteni és, hogy az alapszintű load, automatikus skálázás be van kapcsolva, és összes példány üzembe helyezéséig. Ez a megoldás van méretezve, hogy a teljes éles terhelés igénybe nem, de működik, és minden szolgáltatás áll. Ez a megoldás a jelzőlámpa megközelítés kibővített verziója.
    
    ![A meleg készenlétben aktív/passzív](./media/disaster-recovery-dns-traffic-manager/active-passive-with-warm-standby.png)
    
    *. Ábra: A meleg készenléti vész-helyreállítási konfiguráció aktív/passzív*
    
Feladatátvétel és a magas rendelkezésre állású kapcsolatos további információkért lásd: [Azure-alkalmazások Vészhelyreállítása](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications).


## <a name="planning-your-disaster-recovery-architecture"></a>A vész-helyreállítási architektúra tervezése

Két szempontot műszaki felé a vész-helyreállítási architektúra beállítása:
-  A központi telepítési mechanizmussal példányok, az adatok és konfigurációk replikálni az elsődleges és készenléti környezetek között. Ilyen típusú vész-helyreállítási megteheti a natív módon az Azure Site-Recovery keresztül a Microsoft Azure partneri berendezések/szolgáltatások, például a Veritas vagy a NetApp. 
- Fejlesztés a készenléti hely az elsődleges helyről származó hálózati/webes adatforgalom átirányít megoldást. Az ilyen típusú vész-helyreállítási Azure DNS-ben, az Azure Traffic Manager(DNS) vagy harmadik felek globális terheléselosztók keresztül érhető el.

Ez a cikk megközelítések a hálózati és a webes forgalom átirányítása keresztül korlátozódik. Az Azure Site Recovery beállítása útmutatáshoz lásd: [Azure Site Recovery dokumentációja](https://docs.microsoft.com/azure/site-recovery/).
DNS a leghatékonyabb mechanizmusokat átirányít a hálózati forgalmat, mert DNS gyakran globális és az Adatközpont külső, és minden olyan régióhoz, vagy a rendelkezésre állási zóna (AZ) meghibásodások van szigetelve egyike. Egy DNS-alapú feladatátvevő mechanizmust használni, és az Azure-ban két DNS-szolgáltatás végezheti azonos valamilyen módon – az Azure DNS (mérvadó DNS-) és az Azure Traffic Manager (az intelligens forgalom DNS-alapú útválasztás). 

Fontos tudni, hogy a DNS-ben tárgyalják a jelen cikkben szereplő megoldások széles körben használt néhány fogalommal:
- **A DNS-rekord** – A rekordok olyan mutatók, amelyek tartomány átirányítása egy IPv4-címet. 
- **CNAME-vagy Canonical** -e rekordtípus szolgál, hogy egy másik DNS-rekord mutasson. A rekord, amely tartalmazza az IP-cím az IP-címet, de inkább a mutató CNAME nem válaszol. 
- **Súlyozott útválasztási** – választhat egy súlyt Szolgáltatásvégpontok, és majd terjesztheti a forgalom, a hozzárendelt súlyok alapján. Az útválasztási módszer a négy forgalom útválasztási módszerek egyikével Traffic Manager belül érhető el. További információkért lásd: [súlyozott útválasztási mód](../traffic-manager/traffic-manager-routing-methods.md#weighted).
- **Prioritás útválasztás** – állapot-ellenőrzések végpontok prioritása útválasztás alapul. Alapértelmezés szerint az Azure Traffic manager a legmagasabb prioritású végpontra minden forgalmat küld, és a egy hiba vagy katasztrófa esetén a Traffic Manager irányítja a forgalmat a másodlagos végpontra. További információkért lásd: [prioritásos útválasztási mód](../traffic-manager/traffic-manager-routing-methods.md#priority).

## <a name="manual-failover-using-azure-dns"></a>Manuális feladatátvétel az Azure DNS-sel
Az Azure DNS manuális feladatátvétel vész-helyreállítási megoldás a helykiszolgáló biztonsági mentése a szabványos DNS mechanizmus feladatátvételre használja. Az Azure DNS segítségével a manuális beállítás akkor működik a legjobban, és a ritkán használt készenléti vagy jelzőlámpa megközelítés együttes használatakor. 

![Manuális feladatátvétel az Azure DNS-sel](./media/disaster-recovery-dns-traffic-manager/manual-failover-using-dns.png)

*. Ábra – a manuális feladatátvételt az Azure DNS-sel*

A kalkulált aránnyal megoldáshoz a következők:
-   Elsődleges és másodlagos végpontoknak statikus IP-címek, amelyek nem változnak gyakran. Tegyük fel, hogy az elsődleges hely számára az IP-címe 100.168.124.44 és a másodlagos hely az IP-címe 100.168.124.43.
-   Azure DNS-zóna az elsődleges és másodlagos hely tartozik. Például: az elsődleges hely számára a végpont prod.contoso.com pedig a biztonsági mentési hely dr.contoso.com. A DNS-rekordot a főalkalmazástól www.contoso.com néven is létezik.   
-   Az élettartam (TTL), vagy az alatt állítsa be a szervezet RTO SLA-t. Ha például egy vállalati beállítja az RTO 60 perc, lehetőség szerint az alsó-nál kisebb lehet 60 perc, akkor az élettartam kell lennie a vészhelyreállítási alkalmazásteljesítmény annál jobbak. Ön is Azure DNS a manuális feladatátvételt az alábbiak szerint állíthatja:
1. DNS-zóna létrehozása
2. DNS-zóna-rekordok létrehozása
3. CNAME rekord frissítése

### <a name="step-1-create-a-dns"></a>1. lépés: Hozzon létre egy DNS
(Például www.contoso.com) DNS-zóna létrehozása a lent látható módon:

![DNS-zóna létrehozása az Azure-ban](./media/disaster-recovery-dns-traffic-manager/create-dns-zone.png)

*. Ábra – DNS-zóna létrehozása az Azure-ban*

### <a name="step-2-create-dns-zone-records"></a>2. lépés: Hozzon létre DNS-zóna rekordjait

Ebben a zónában belül hozzon létre három rekordot (például: www.contoso.com, prod.contoso.com és dr.consoto.com) az alábbi megjelenítése.

![DNS-zóna-rekordok létrehozása](./media/disaster-recovery-dns-traffic-manager/create-dns-zone-records.png)

*. Ábra – DNS-zóna rekordok létrehozása az Azure-ban*

Ebben a forgatókönyvben a hely, a www.contoso.com 30 perc alatt a megadott RTO-és az éles webhely prod.contoso.com mutató Élettartamot. Ez a konfiguráció van a normál üzleti műveletek során. Az élettartam prod.contoso.com és dr.contoso.com 300 másodperc vagy 5 percre van beállítva. Használhatja az Azure monitoring, például az Azure Monitor vagy az Azure App service, vagy bármely partner figyelési megoldások, például a dynatrace-szel, is használható, amely figyelheti és észleli az alkalmazást vagy a virtuális infrastruktúra meghibásodások otthoni túlhaladottá megoldások.

### <a name="step-3-update-the-cname-record"></a>3. lépés: A CNAME rekord frissítése

Ha hibát észlel, módosítsa a rekord értéket dr.contoso.com mutasson az alább látható módon:
       
![CNAME rekord frissítése](./media/disaster-recovery-dns-traffic-manager/update-cname-record.png)

*. Ábra – az Azure-ban a CNAME-rekord frissítése*

30 percen belül, amely során a legtöbb feloldók frissíti a gyorsítótárazott zónafájl www.contoso.com minden lekérdezés dr.contoso.com irányítja.
Módosítsa a CNAME értéket a következő Azure CLI-parancsot is futtathatja:
 ```azurecli
   az network dns record-set cname set-record \
   --resource-group 123 \
   --zone-name contoso.com \
   --record-set-name www \
   --cname dr.contoso.com
```
Ezt a lépést manuálisan vagy automation is végrehajthatók. Ezt megteheti manuálisan a konzolon keresztül, vagy az Azure CLI. Az Azure SDK és az API segítségével automatizálhatja a CNAME-frissítés, így nincs szükség kézi beavatkozásra. Automation keresztül az Azure functions vagy egy külső monitorozási alkalmazáson belül, vagy akár a helyszíni építhetők fel.

### <a name="how-manual-failover-works-using-azure-dns"></a>Az Azure DNS használatával hogyan kézi feladatátvételi folyamatok
Mivel a DNS-kiszolgáló a feladatátvétel vagy katasztrófa zónán kívüli, a van szigetelni állásidő ellen. Ez lehetővé teszi, hogy a felhasználó egyszerű feladatátviteli forgatókönyv költséghatékony tervezhet és az összes az időt feltételezve, hogy az üzemeltető rendelkezik hálózati kapcsolattal katasztrófa során fog működni, és teheti a tükrözés. Ha a megoldás parancsfájlalapú van, majd egy biztosítania kell, hogy a kiszolgáló vagy a szolgáltatás fut a parancsfájl kell lennie szigetelni ellen a problémát, hogy ez hatással lenne az éles környezetben. Továbbá vegye figyelembe az alacsony élettartam, amelyet a zóna ellen, hogy nincs feloldó a világ különböző pontjain található a végpont hosszú ideig a gyorsítótárban tartja, és az ügyfelek elérhetik az RTO belül a hely. Áll készenlétben és jelzőlámpa mivel bizonyos prewarming és más felügyeleti tevékenységre lehet szükség – egyet kell is meg kell adni elegendő időt a tükrözés elvégzése előtt.

## <a name="automatic-failover-using-azure-traffic-manager"></a>Automatikus feladatátvétel az Azure Traffic Managerrel
Ha rendelkezik összetett architektúrák és több példányban képes végrehajtani ugyanannak a függvénynek erőforrásokat, az Azure Traffic Manager (a DNS alapján) konfigurálhatja az erőforrások állapotának ellenőrzését, és átirányítja a forgalmat a kifogástalan állapotú, a nem kifogástalan erőforrás az erőforrás. A következő példában mind az elsődleges régióban, és a másodlagos régióba rendelkezik teljes körű telepítésére. A központi telepítés magában foglalja a cloud services és a egy szinkronizált adatbázis. 

![Automatikus feladatátvétel az Azure Traffic Managerrel](./media/disaster-recovery-dns-traffic-manager/automatic-failover-using-traffic-manager.png)

*. Ábra – az Automatikus feladatátvétel az Azure Traffic Managerrel*

Azonban csak az elsődleges régió aktívan kezeli a felhasználók a hálózati kérelmek. A másodlagos régióhoz csak amikor az elsődleges régióban egy szolgáltatáskimaradás aktívvá válik. Ebben az esetben minden új hálózati kérés átirányítása a másodlagos régióba. Mivel a biztonsági másolatot az adatbázisról van közel azonnali, mind a terheléselosztók rendelkezik IP-címek, hogy be van jelölve, egészségügyi és a példányok mindig naprakészen és fut, ez a topológia a kezdeti időszak után egy alacsony RTO- és manuális beavatkozás nélküli feladatátvevő lehetőséget biztosít. A másodlagos feladatátvételi régióban éles közvetlenül az elsődleges régióban hiba után készen kell lennie.
Ebben a forgatókönyvben ideális választás használata az Azure Traffic Manager, amely rendelkezik a beépített mintavételek különféle típusú állapot-ellenőrzések, beleértve a http / https és a TCP. Az Azure Traffic manager is rendelkezik egy szabályalapú motor, amely hiba esetén az alább ismertetett feladatátvételre konfigurálva. Vegyünk például a Traffic Managert használva a következő megoldást:
- -Ügyfél a régió 1 végpont prod.contoso.com néven 100.168.124.44 és a egy régió 2 végpont 100.168.124.43, statikus IP-Címmel rendelkező dr.contoso.com néven statikus IP-címmel rendelkezik. 
-   Ezekben a környezetekben mindegyike van fronted, például a terheléselosztó a nyilvános irányuló tulajdonság használatával. A load balancer beállítható úgy, hogy rendelkezik egy DNS-alapú végpontot, illetve egy teljesen minősített tartománynevét (FQDN), ahogy fent látható.
-   Közel valós idejű replikációs régió 1 szerepelnek régió 2 szereplő összes példányt. Továbbá a gép képek naprakészek legyenek, és minden szoftver vagy konfigurációs adatok javítva van, és régió 1 összhangban legyenek.  
-   Az automatikus skálázás előre előre konfigurált. 

A feladatátvétel konfigurálása az Azure Traffic Managerrel tett lépések a következők:
1. Új Azure Traffic Manager-profil létrehozása
2. A Traffic Manager-profil belüli végpontok létrehozása
3. Állapot ellenőrzése és a feladatátvételi konfiguráció beállítása

### <a name="step-1-create-a-new-azure-traffic-manager-profile"></a>1. lépés: Hozzon létre egy új Azure Traffic Manager-profil
A név contoso123 hozzon létre egy új Azure Traffic manager-profilt, és válassza ki az útválasztási módszer prioritásúként. Ha rendelkezik egy meglévő erőforráscsoportot, amelyet szeretne társítani, majd egy meglévő erőforráscsoportot kiválaszthatja, ellenkező esetben hozzon létre egy új erőforráscsoportot.

![Traffic Manager-profil létrehozása](./media/disaster-recovery-dns-traffic-manager/create-traffic-manager-profile.png)
 *. ábra – Traffic Manager-profil létrehozása*

### <a name="step-2-create-endpoints-within-the-traffic-manager-profile"></a>2. lépés: Végpontok belül a Traffic Manager-profil létrehozása

Ebben a lépésben hoz létre, amelyek az éles és a vész-helyreállítási hely végpontok. Itt válassza ki a **típus** külső végpontja, de ha az erőforrást az Azure-ban üzemel, akkor választhatja **Azure-végpont** is. Ha úgy dönt, **Azure-végpont**, majd válassza ki a **Célerőforrásnál** , amely vagy egy **App Service-ben** vagy egy **nyilvános IP-cím** is lefoglalta Azure-t. A prioritás van beállítva, **1** az elsődleges régió 1 szolgáltatást, mert.
Ehhez hasonlóan hozzon létre a vész helyreállítási végpont a Traffic Manager belül is.

![Vész-helyreállítási végpontok létrehozása](./media/disaster-recovery-dns-traffic-manager/create-disaster-recovery-endpoint.png)

*. Ábra – vész-helyreállítási végpontok létrehozása*

### <a name="step-3-set-up-health-check-and-failover-configuration"></a>3. lépés: Állapotának ellenőrzése és a feladatátvételi konfiguráció beállítása

Ebben a lépésben beállíthat egy a DNS-Élettartamot 10 másodperc, amely a legtöbb internetkapcsolattal rendelkező rekurzív feloldók tartja. Ez a konfiguráció azt jelenti, hogy nincs DNS-feloldási gyorsítótárazzák az adatokat több mint 10 másodpercig. A Végpontfigyelő beállításai, az az elérési út jelenlegi beállított / vagy a legfelső szintű, de egy elérési utat, például prod.contoso.com/index kiértékelheti, hogy a végpont a beállítások testre. Az alábbi példában a **https** ellenőrzési protokollként. Választhatja azonban **http** vagy **tcp** is. A kiválasztott protokoll attól függ, hogy a teljes alkalmazás. Az ellenőrzési időköz értéke 10 másodperc, amely lehetővé teszi, hogy gyors tesztelés, és az újrapróbálkozási 3 értékre van állítva. A Traffic Manager ennek eredményeképpen a második végpontnak feladatátvételt hajt végre, ha három egymást követő időközök regisztrálása sikertelen. A következő képlet határozza meg az automatikus feladatátvételre fordított időt: a feladatátvételi idő = TTL + újra * Probing időköz és ebben az esetben az értéke 10 + 3 * 10 = 40 másodperc (Max).
Ha az újrapróbálkozás értéke 1 és az élettartam szerepel majd a hozzá tartozó feladatátvételi 10 + 1 * 10 = 20 másodperc értéke 10 másodperc. Az újrapróbálkozás nagyobb értékre állítva **1** kiküszöbölése vakriasztások vagy bármely kisebb hálózati jelekből miatt folyamatban lévő feladatátvételi teszteket esélyét. 


![Állapot-ellenőrzés beállítása](./media/disaster-recovery-dns-traffic-manager/set-up-health-check.png)

*. Ábra – állapotának ellenőrzése és a feladatátvételi konfiguráció beállítása*

### <a name="how-automatic-failover-works-using-traffic-manager"></a>A Traffic Manager használatával automatikus feladatátvételi folyamatok

Egy katasztrófa során az elsődleges végpont vizsgált lekérdezi és állapotra vált, **csökkentett teljesítményű** és a vész-helyreállítási webhelyként marad **Online**. Alapértelmezés szerint a Traffic Manager minden forgalmat küld az elsődleges (legnagyobb prioritás) végponthoz. Ha az elsődleges végpont csökkentett teljesítményű jelenik meg, a Traffic Manager irányítja a forgalmat a második végpontnak mindaddig, amíg kifogástalan állapotban marad. További végpontok Traffic Manager belül, amely további feladatátvételi végpontként szolgál, akár, mint terheléselosztók a terhelés, végpontok közötti megosztása konfigurálhatja egy rendelkezik.

## <a name="next-steps"></a>További lépések
- Tudjon meg többet [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md).
- Tudjon meg többet [Azure DNS](../dns/dns-overview.md).









