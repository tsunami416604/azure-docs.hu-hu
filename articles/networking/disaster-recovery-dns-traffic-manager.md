---
title: Azure DNS-ben és a Traffic Manager vész-helyreállítási |} Microsoft Docs
description: Az Azure DNS-ben és a Traffic Manager vész-helyreállítási megoldások áttekintése.
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
ms.openlocfilehash: d608378f9b3ff3179f9e37ef13f88c65a645d018
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37112986"
---
# <a name="disaster-recovery-using-azure-dns-and-traffic-manager"></a>Vészhelyreállítás az Azure DNS-sel és a Traffic Managerrel

Végezze el az alkalmazás funkciói súlyos adatvesztést a vész-helyreállítási összpontosít. Ahhoz, hogy válassza ki a vész-helyreállítási megoldást, üzleti és informatikai tulajdonosok először meg kell határoznia szolgáltatási szükséges során katasztrófa, például - nem érhető el, részben elérhető csökkentett vagy késleltetett rendelkezésre állását, vagy teljesen érhető el.
A legtöbb vállalati ügyfelek választ egy alkalmazás vagy infrastruktúra szintű feladatátvételi szembeni hibatűrést több területi architektúrát. Az ügyfelek több megközelítés közül választhatja ki a kérelem kitöltése feladatátvételi és a redundáns architektúra keresztül magas rendelkezésre állás elérése érdekében. Íme néhány a népszerű megközelítések közül:

- **Aktív-passzív a meleg készenléti**: feladatátvételi ebben a megoldásban a virtuális gépek és egyéb készülékek, hogy a készenléti régióban fut csak nem aktív feladatátvételi szükség van. Azonban az éles környezetben replikálja a rendszer biztonsági mentést, a Virtuálisgép-rendszerképek vagy a Resource Manager-sablonok, hogy egy másik régióban formájában. A feladatátvételi mechanizmus költséghatékony, de egy befejezheti a feladatátvételt elvégzésére hosszabb időt vesz igénybe.
 
    ![A meleg készenléti állapotban lévő aktív/passzív](./media/disaster-recovery-dns-traffic-manager/active-passive-with-cold-standby.png)
    
    *. Ábra – aktív/passzív meleg készenléti vész helyreállítási konfiguráció*

- **A próbaüzem világos aktív/passzív**: feladatátvételi ebben a megoldásban a készenléti kiszolgálói környezet kialakítása minimális konfigurációval. A telepítő csak a futó csak minimális és a kritikus meghatározott alkalmazások támogatásához szükséges szolgáltatásokat tartalmaz. Natív formájában ebben a forgatókönyvben csak minimális funkciók hajtható végre, de is növelheti és elindítanak érvénybe az üzemi terhelés tömeges, ha feladatátvétel kiegészítő szolgáltatásokat.
    
    ![Aktív/passzív a próbaüzem világos](./media/disaster-recovery-dns-traffic-manager/active-passive-with-pilot-light.png)
    
    *Ábra: Aktív/passzív próbaüzem világos vész helyreállítási konfiguráció*

- **A meleg készenléti állapotban lévő aktív/passzív**: feladatátvételi ebben a megoldásban a készenléti régió előmelegíteni van, és készen áll a kiinduló terhelés elkészítésére, automatikus skálázás be van kapcsolva, és a példányok megfelelően működik. Ez a megoldás nem méretezve, a teljes üzemi terhelés érvénybe, de működik, és az összes szolgáltatás megfelelően működik. Ez a megoldás a próbaüzem világos megközelítés egy kibővített verziója telepítve.
    
    ![A meleg készenléti állapotban lévő aktív/passzív](./media/disaster-recovery-dns-traffic-manager/active-passive-with-warm-standby.png)
    
    *Ábra: Aktív/passzív meleg készenléti vész-helyreállítási konfiguráció*
    
Feladatátvétel és a magas rendelkezésre állású kapcsolatos további információkért lásd: [Azure alkalmazások vész-helyreállítási](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications).


## <a name="planning-your-disaster-recovery-architecture"></a>A vész-helyreállítási architektúrájának tervezése

Két szempontot műszaki felé a vész-helyreállítási architektúra beállítása:
-  A központi telepítés alapján történő példányok, az adatok és konfigurációk replikálja az elsődleges és készenléti különböző környezetek között. Az ilyen típusú vész-helyreállítási megteheti a natív Azure-helyreállítás keresztül a Microsoft Azure partner készülékek vagy szolgáltatásokat, mint a Veritas vagy a NetApp. 
- A hálózati vagy webes forgalom az elsődleges helyről a készenléti helyre átállításával megoldás kifejlesztése. Az ilyen típusú vész-helyreállítási elérhető Azure DNS-ben, az Azure Traffic Manager(DNS) vagy a külső globális terheléselosztók.

Ez a cikk a hálózat és a webes forgalom átirányítással megközelítések korlátozódik. Az Azure Site Recovery beállítása útmutatáshoz lásd: [Azure Site Recovery dokumentációjában](https://docs.microsoft.com/azure/site-recovery/).
DNS egyike a leghatékonyabb mechanizmusokat átirányít hálózati forgalmat, mert DNS gyakran globális és az Adatközpont külső, területi vagy rendelkezésre állási zóna (AZ) szintű hibák van szigetelve. Egy DNS-alapú feladatátvevő mechanizmust használni, és az Azure két DNS-szolgáltatások is elérheti a án - Azure DNS (mérvadó DNS-) és az Azure Traffic Manager (DNS-alapú intelligens forgalom-Útválasztás). 

Fontos tudni, hogy a DNS-ben, és beszéljék meg a jelen cikkben szereplő megoldások széles körben használt néhány fogalommal:
- **DNS-A rekord** – A rekordok mutatók, amelyek egy IPv4-cím a tartományban. 
- **CNAME-vagy Canonical** -bejegyzéstípus szolgál, hogy egy másik DNS-rekord mutasson. A rekord, amely tartalmazza az IP-cím az IP-címet, de ahelyett, hogy a mutató CNAME nem válaszol. 
- **Útválasztás súlyozott** – egy weight a végpontok és közzétennie a forgalmat a társított súlyok alapján választhat. Az útválasztási módszer a négy forgalom útválasztási módszerek egyikével Traffic Manager belül érhető el. További információkért lásd: [útválasztási módszer súlyozott](../traffic-manager/traffic-manager-routing-methods.md#weighted).
- **Prioritás útválasztás** – állapotellenőrzést végpontok prioritású virtuális gép útválasztási alapul. Alapértelmezés szerint az Azure Traffic manager az összes forgalom küldése a legmagasabb prioritású végpont, és egy hiba vagy katasztrófa esetén a Traffic Manager irányítja a forgalmat a másodlagos végponti. További információkért lásd: [prioritású virtuális gép útválasztási módszer](../traffic-manager/traffic-manager-routing-methods.md#priority).

## <a name="manual-failover-using-azure-dns"></a>Kézi feladatátvételre Azure DNS-sel
Az Azure DNS kézi feladatátvételre vész-helyreállítási megoldás a szabványos DNS mechanizmus, amellyel a feladatátvétel a biztonsági mentési hely használja. A manuális beállítás Azure DNS-rendszerben működik optimálisan, ha a meleg készenléti vagy a próbaüzem világos megközelítés együtt használható. 

![Kézi feladatátvételre Azure DNS-sel](./media/disaster-recovery-dns-traffic-manager/manual-failover-using-dns.png)

*Ábra - kézi feladatátvételre Azure DNS-sel*

A megoldás a ismertetése a következők:
-   Elsődleges és másodlagos végpontok rendelkezik statikus IP-címek, amelyek nem változnak. Az IP-cím 100.168.124.44, és az IP-Címek használhatók a másodlagos hely 100.168.124.43 mondja ki a elsődleges helyhez.
-   Az Azure DNS-zóna az elsődleges és másodlagos hely tartozik. Az elsődleges helyhez tartozó mondja ki a végpont prod.contoso.com pedig a biztonsági mentési hely dr.contoso.com. A www.contoso.com néven alkalmazás egy DNS-rekordot is létezik.   
-   Az élettartam (TTL) vagy az alatt állítsa be a szervezet RTO SLA-t. Ha például egy vállalati beállítja 60 perc, lehetőség szerint az alsó-nál kisebb lehet 60 perc, a TTL-t kell alkalmazás katasztrófa válasz RTO minél pontosabban. Állíthat be Azure DNS kézi feladatátvételre az alábbiak szerint:
1. DNS-zóna létrehozása
2. DNS-zóna-rekordok létrehozása
3. CNAME rekord frissítése

### <a name="step-1-create-a-dns"></a>1. lépés: A DNS-létrehozása
Hozzon létre egy DNS-zóna (például www.contoso.com), alább látható módon:

![Hozzon létre egy DNS-zónát az Azure-ban](./media/disaster-recovery-dns-traffic-manager/create-dns-zone.png)

*. Ábra – hozzon létre egy DNS-zónát az Azure-ban*

### <a name="step-2-create-dns-zone-records"></a>2. lépés: A DNS-zóna-rekordok létrehozása

A zónán belül (például - www.contoso.com, prod.contoso.com és dr.consoto.com) három rekordokat hozza létre, mint az alábbi megjelenítése.

![DNS-zóna-rekordok létrehozása](./media/disaster-recovery-dns-traffic-manager/create-dns-zone-records.png)

*. Ábra – a DNS-zóna-rekordok létrehozása az Azure-ban*

Ebben a forgatókönyvben a helyet, a www.contoso.com 30 perc, amely alatt a megadott RTO, és a termelési hely prod.contoso.com mutató TTL rendelkezik. Ez a konfiguráció nincs normál üzleti műveletek során. Az élettartam prod.contoso.com és dr.contoso.com 300 másodperc vagy 5 perc van beállítva. Használhatja az Azure szolgáltatással, például az Azure-megfigyelőt vagy az Azure App Insights figyelési, vagy figyelési megoldások például Dynatrace partnerszervezetek, otthoni részben megoldások, amelyek figyelése, vagy alkalmazás vagy virtuális infrastruktúra szintű hibák is használhatja.

### <a name="step-3-update-the-cname-record"></a>3. lépés: A CNAME rekord frissítése

Ha hiba lép fel, módosítsa a rekord dr.contoso.com mutassanak, alább látható módon:
       
![CNAME rekord frissítése](./media/disaster-recovery-dns-traffic-manager/update-cname-record.png)

*Ábra - a frissítések a CNAME rekord, az Azure-ban*

30 perc során, ami a legtöbb feloldókat frissíti a gyorsítótárazott zónafájl belül bármely www.contoso.com lekérdezést dr.contoso.com irányítja.
A következő Azure CLI parancsot, módosítsa a CNAME értéket is futtathatja:
 ```azurecli
   az network dns record-set cname set-record \
   --resource-group 123 \
   --zone-name contoso.com \
   --record-set-name www \
   --cname dr.contoso.com
```
Ebben a lépésben kézzel vagy automation segítségével hajtható végre. Azt is manuálisan kell elvégezni a konzol révén vagy az Azure parancssori felület. Az Azure SDK-t és az API segítségével automatizálhatja a CNAME frissítés, hogy nincs manuális beavatkozásra szükség. Automatizálás az Azure functions segítségével vagy egy külső figyelési alkalmazáson belül vagy akár a helyszíni építhető.

### <a name="how-manual-failover-works-using-azure-dns"></a>Hogyan kézi feladatátvételt works Azure DNS-sel
Mivel a DNS-kiszolgáló a feladatátvétel vagy katasztrófa területen kívül, a rendszer megfelelően leállási ellen. Ez lehetővé teszi, hogy a felhasználót, hogy egy egyszerű feladatátvétel forgatókönyv költséghatékony tervezővel és működni fog minden a idő feltéve, hogy az üzemeltető rendelkezik hálózati kapcsolattal vészhelyreállítás során, és végezheti a tükrözés. Ha a megoldás van parancsprogrammal létrehozva, majd egy ellenőriznie kell, hogy a kiszolgáló vagy a szolgáltatás fut a parancsfájl kell lennie megfelelően szemben a problémát, hogy az hatással lenne az éles környezetben. Emellett vegye figyelembe a kis TTL-t, hogy a zóna elleni be lett állítva, hogy a világ különböző feloldó tartja a gyorsítótárba helyezett hosszú végpont és ügyfelek is hozzáférhetnek a RTO a webhelyen. A meleg készenléti állapotban lévő és a próbaüzem világos mivel egyes prewarming és más felügyeleti tevékenység lehet szükség – egy is adjon elég ideje a tükrözés elvégzése előtt.

## <a name="automatic-failover-using-azure-traffic-manager"></a>Automatikus feladatátvétel Azure Traffic Manager használatával
Ha összetett architektúrák és erőforrások azonos funkció végrehajtására képes több példányban, konfigurálhatja az erőforrások állapotának ellenőrzése és irányíthatja a forgalmat a nem kifogástalan erőforrás a megfelelő Azure Traffic Manager (a DNS-alapú) az erőforrás. A következő példában az elsődleges régióban, mind a másodlagos régióba rendelkezik teljes körű telepítésére. A központi telepítés a felhőszolgáltatások és egy szinkronizált adatbázist tartalmaz. 

![Automatikus feladatátvétel Azure Traffic Manager használatával](./media/disaster-recovery-dns-traffic-manager/automatic-failover-using-traffic-manager.png)

*Ábra - automatikus feladatátvételi Azure Traffic Manager használatával*

Csak az elsődleges régióban azonban aktívan kezeli a hálózati kérelmek, a felhasználóktól. A másodlagos régióba válik aktívvá, csak akkor, ha az elsődleges régióban észlel a szolgáltatás szüneteltetése. Ebben az esetben minden új hálózati kérelmek átirányíthatja a másodlagos régióba. Az adatbázis a biztonsági mentés óta van szinte azonnali, mindkét a terheléselosztók rendelkezik IP-címet, amely be van jelölve, állapotát és a példányt a rendszer mindig naprakészek, és fut, ez a topológia lehetőséget biztosít számára alacsony RTO és feladatátvételi kézi beavatkozás nélkül is. A másodlagos feladatátvételi régió éles közvetlenül az elsődleges régióban hiba után készen kell lennie.
Ebben a forgatókönyvben ideális megoldás az Azure Traffic Managerben, amelynek használata beépített mintavételt különféle típusú állapotellenőrzést, beleértve a http / https- és TCP. Az Azure Traffic manager is rendelkezik, amely beállítható úgy, hogy feladatátvétel során hiba lép fel, az alább ismertetett jogcímszabály-motor. Mérlegeljük, ezzel a megoldással a Traffic Manager használata:
- Ügyfél van a régió #1 végpont egy statikus IP-100.168.124.44 és egy régió 2 végpont egy statikus IP-mint 100.168.124.43 dr.contoso.com néven prod.contoso.com néven ismert. 
-   Egyes ezekben a környezetekben van fronted, például a terheléselosztók nyilvános irányuló tulajdonsága révén. A load balancer beállítható úgy, hogy rendelkezik egy DNS-alapú végpont vagy teljesen minősített tartománynevét (FQDN), a fentiek szerint.
-   Közel valós idejű replikációs 1 régió van régió 2 példányait. Továbbá a gépek lemezképét naprakészek legyenek, és minden szoftver vagy a konfigurációs adatok van telepítve, és 1 régió összhangban legyenek.  
-   Automatikus skálázás előre előre konfigurált. 

A feladatátvétel konfigurálása az Azure Traffic Manager tett lépések a következők:
1. Új Azure Traffic Manager-profil létrehozása
2. A Traffic Manager-profil belüli végpontok létrehozása
3. Állapotának ellenőrzése és a feladatátvételi konfigurációjának beállítása

### <a name="step-1-create-a-new-azure-traffic-manager-profile"></a>1. lépés: Új Azure Traffic Manager-profil létrehozása
Hozzon létre egy új Azure Traffic manager-profil a név contoso123 és útválasztási mód a prioritás. Ha egy már meglévő erőforráscsoportot, amelyet szeretne társítani, jelölje be egy meglévő erőforráscsoportot, máskülönben hozzon létre egy új erőforráscsoportot.

![Traffic Manager-profil létrehozása](./media/disaster-recovery-dns-traffic-manager/create-traffic-manager-profile.png)
 *. ábra - a Traffic Manager-profil létrehozása*

### <a name="step-2-create-endpoints-within-the-traffic-manager-profile"></a>2. lépés: A Traffic Manager-profil belüli végpontok létrehozása

Ebben a lépésben hoz létre, amely a termelési és vész-helyreállítási helyeken végpontok. Itt válassza a **típus** külső végpont, de ha az erőforrás található, az Azure-ban, akkor választhatja **Azure-végpont** is. Ha úgy dönt, **Azure-végpont**, majd jelölje be a **célerőforrás** , amely vagy egy **App Service** vagy egy **nyilvános IP-cím** , amely is lefoglalta Azure. A prioritás értéke **1** mivel ez a régió 1 elsődleges szolgáltatáshoz.
Hasonlóképpen, a vész helyreállítási-végpont létrehozása Traffic Manager belül is.

![Katasztrófa utáni helyreállítás végpontok létrehozása](./media/disaster-recovery-dns-traffic-manager/create-disaster-recovery-endpoint.png)

*. Ábra – vész helyreállítási végpontok létrehozása*

### <a name="step-3-set-up-health-check-and-failover-configuration"></a>3. lépés: Állapotának ellenőrzése és a feladatátvételi konfigurációjának beállítása

Ebben a lépésben beállította a DNS-élettartam 10 másodpercet, amely a legtöbb internet felé néző rekurzív feloldókat van ezt a funkciót. Ez a konfiguráció azt jelenti, hogy nincs DNS-feloldási gyorsítótárazza-e a több mint 10 másodperc az információkat. A végpont a megadott beállítások, az elérési út jelenlegi beállított / vagy a gyökér, de beállítások is testre szabhatók a végpont elérési útja, például prod.contoso.com/index kiértékeléséhez. Az alábbi példa a **https** a vizsgálathoz használt protokoll. Választhatja azonban **http** vagy **tcp** is. A választott protokoll függ az alkalmazás bezárása. A vizsgálathoz használt időköz értéke 10 másodperc, amely lehetővé teszi, hogy gyors probing, és az újrapróbálkozási értéke 3. Ennek eredményeképpen a Traffic Manager fog megfelelni a feladatátvétel, a második végpontnak, ha három egymást követő intervallumok regisztrálja hiba. A következő képlet határozza meg a teljes időnek az Automatikus feladatátvétel: a feladatátvételi idő = TTL + próbálja meg újra * vizsgálat időköz és ebben az esetben az értéke 10 + 3 * 10 = 40 másodperc (max.).
Ha az ismételt próbálkozás értéke 1 és a TTL majd a hozzá tartozó feladatátvételi 10 + 1 * 10 = 20 másodperc érték 10 másodperc. Az újrapróbálkozási nagyobb értékre beállítva **1** elkerülése érdekében feladatátvételek miatt a vakriasztások vagy bármely másodlagos hálózati blips esélyét. 


![Az állapot-ellenőrzéssel beállítása](./media/disaster-recovery-dns-traffic-manager/set-up-health-check.png)

*Ábra - állapotának ellenőrzése és a feladatátvételi konfigurációjának beállítása*

### <a name="how-automatic-failover-works-using-traffic-manager"></a>Automatikus feladatátvétel működik a Traffic Managerrel

A vészhelyreállítás során az elsődleges végpont megadott lekérdezi és állapota **csökkentett teljesítményű** és a vész-helyreállítási hely marad **Online**. Alapértelmezés szerint a Traffic Manager elküldi az összes forgalom az elsődleges (legnagyobb prioritás) végpont. Ha az elsődleges végpont csökkent jelenik meg, a Traffic Manager irányítja a forgalmat a második végpontnak mindaddig, amíg azt kifogástalan. Egy további végpontok Traffic Manager belül, amely további feladatátvételi végpontként, vagy, mint a terheléselosztók a terhelés, végpontok közötti megosztása konfigurálása lehetősége van.

## <a name="next-steps"></a>További lépések
- További információ [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md).
- További információ [Azure DNS](../dns/dns-overview.md).









