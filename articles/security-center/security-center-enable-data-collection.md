---
title: Az adatgyűjtést az Azure Security Centerben |} A Microsoft Docs
description: " Ismerje meg, hogyan lehet engedélyezni az adatgyűjtést az Azure Security Centerben. "
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: 6dcb7fd1ae2dc5ca3a950f5055e79d95f779b029
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300793"
---
# <a name="data-collection-in-azure-security-center"></a>Az adatgyűjtést az Azure Security Centerben
A Security Center adatokat gyűjt az Azure-beli virtuális gépekről (VM), a virtuálisgép-méretezési csoportokról, a IaaS-tárolók és a nem Azure-beli (beleértve a helyszíni) számítógépekről a biztonsági rések és fenyegetések figyeléséhez. Az adatok gyűjtése a Log Analytics ügynök használatával történik, amely beolvassa a különböző biztonsággal kapcsolatos konfigurációkat és eseménynaplókat a gépről, és az adatokat a munkaterületre másolja az elemzéshez. Az ilyen adatok többek között: operációs rendszer típusa és verziója, az operációs rendszer naplói (Windows-eseménynaplók), a futó folyamatok, a gép nevét, az IP-címeket, és bejelentkezett felhasználó. A Log Analytics ügynök az összeomlási memóriaképek fájljait is másolja a munkaterületre.

Az adatgyűjtés nem kell megadnia a hiányzó frissítések, hibásan konfigurált operációs rendszer biztonsági beállításai, endpoint protection engedélyezése és állapot- és fenyegetés-észlelési láthatósága. 

Ez a cikk azt ismerteti, hogyan telepíthet egy Log Analytics-ügynököt, és hogyan állíthat be egy Log Analytics-munkaterületet, amelyben tárolni kívánja az összegyűjtött adatokat. Mindkét műveletet az adatgyűjtés engedélyezéséhez szükségesek. 

> [!NOTE]
> - Az adatgyűjtés csak számítási erőforrások (virtuális gépek, virtuálisgép-méretezési csoportok, IaaS-tárolók és nem Azure-beli számítógépek) esetében szükséges. Élvezheti az Azure Security Center akkor is, ha nem üzembe helyezi az ügynökök; azonban Ön csak korlátozott biztonsági, és a fent felsorolt funkciók nem támogatottak.  
> - A támogatott platformok listáját lásd: [az Azure Security Center által támogatott platformok](security-center-os-coverage.md).
> - Az adatok tárolása Log Analyticsban, akár új, akár meglévő munkaterületet használ, az adattárolásra további díjak merülhetnek fel. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/security-center/) olvasható.

## Az log Analytics-ügynök automatikus kiépítés engedélyezése<a name="auto-provision-mma"></a>

Az adatok a gépekről való összegyűjtéséhez telepítenie kell a Log Analytics-ügynököt. Az ügynök telepítése automatikusan végezhető el (ajánlott), vagy manuálisan is telepítheti az ügynököt.  

>[!NOTE]
> Alapértelmezés szerint az Automatikus kiépítés le van. A Security Center telepítése alapértelmezés szerint az Automatikus kiépítés beállítása, állítsa **a**.
>

Ha az automatikus kiosztás be van kapcsolva, Security Center kiépíti a Log Analytics ügynököt az összes támogatott Azure-beli virtuális gépen és a létrehozott újakon. Az Automatikus kiépítés használata erősen ajánlott, de a manuális ügynöktelepítések is rendelkezésre áll. [Ismerje meg, hogyan telepítheti a log Analytics Agent bővítményt](#manual-agent).



Az log Analytics-ügynök automatikus kiépítés engedélyezése:
1. A Security Center Főmenüben válassza a **díjszabás & beállítások**lehetőséget.
2. Kattintson a megfelelő előfizetésre

   ![Előfizetés kiválasztása][7]

3. Válassza **az adatgyűjtés**lehetőséget.
4. A **automatikus kiépítés**válassza **a** az Automatikus kiépítés engedélyezéséhez.
5. Kattintson a **Mentés** gombra.

   ![Automatikus kiépítés engedélyezése][1]

>[!NOTE]
> - Hogyan építheti ki egy már meglévő telepítési utasításokért lásd: [automatikus üzembe helyezés abban az esetben egy már létező ügynöktelepítés](#preexisting).
> - A manuális üzembe helyezéssel kapcsolatos utasításokért lásd: [a log Analytics Agent bővítmény telepítése manuálisan](#manual-agent).
> - Útmutatás az Automatikus kiépítés kikapcsolása: [kapcsolja ki az Automatikus kiépítés](#offprovisioning).
> - Hogyan felvétele a Security Center PowerShell-lel, tekintse meg útmutatást [automatizálása a PowerShell-lel az Azure Security Center bevezetése](security-center-powershell-onboarding.md).
>

## <a name="workspace-configuration"></a>Munkaterület-konfiguráció
A Security Center által gyűjtött adatokat Log Analytics-munkaterületen tárolja. Kiválaszthatja, hogy az adatok tárolása a Security Center által létrehozott munkaterületeken vagy egy meglévő munkaterületet hozott létre az Azure virtuális gépekről gyűjtött. 

Munkaterület-konfiguráció beállítása előfizetésenként, de sok előfizetést használhatja ugyanazon a munkaterületen.

### <a name="using-a-workspace-created-by-security-center"></a>A Security Center által létrehozott munkaterület használatával

A Security center automatikusan létrehozhat egy alapértelmezett munkaterületet, amely tárolja az adatokat. 

A Security Center által létrehozott munkaterület kiválasztása:

1. A **alapértelmezett munkaterület-konfiguráció**, válassza ki a Security center által létrehozott munkaterület(ek) használata.
   ![Válasszon tarifacsomagot][10] 

1. Kattintson a **Save** (Mentés) gombra.<br>
    A Security Center egy új erőforrás és egy alapértelmezett munkaterületet hoz létre, hogy földrajzi hely, és csatlakoztatja az ügynököt a munkaterülethez. A munkaterület és erőforrás-csoport elnevezési van:<br>
   **Munkaterület Alapértelmezettmunkaterület-[előfizetés-azonosító]-[geo]<br> erőforráscsoport: DefaultResourceGroup-[geo]**

   Ha egy előfizetés több geolocations a virtuális gépeket tartalmaz, a Security Center több munkaterületet hoz létre. Több munkaterülettel jönnek létre az adatok adatvédelmi szabályok kezelése.
1. A Security Center automatikusan engedélyezi a a tarifacsomagot állítsa be az előfizetés-munkaterülethez a Security Center megoldást. 

> [!NOTE]
> A Security Center által létrehozott munkaterület tarifacsomagját a Log Analytics nincs hatással a Security Center a számlázás. A Security Center számlázási mindig alapján a Security Center biztonsági házirend és a megoldások a munkaterülethez telepítve. Az ingyenes, a Security Center lehetővé teszi a *SecurityCenterFree* megoldás az alapértelmezett munkaterületre. A Standard csomag esetében a Security Center lehetővé teszi a *biztonsági* megoldás az alapértelmezett munkaterületre.
> Az adatok Log Analyticsban való tárolása további díjakat eredményezhet az adattároláshoz. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/security-center/) olvasható.

A meglévő log Analytics-fiókokkal kapcsolatos további információkért lásd: [meglévő log Analytics-ügyfelek](security-center-faq.md#existingloganalyticscust).

### <a name="using-an-existing-workspace"></a>Egy meglévő munkaterület használata

Ha már rendelkezik meglévő Log Analytics munkaterülettel, érdemes lehet ugyanazt a munkaterületet használni.

A meglévő Log Analytics-munkaterület használatához rendelkeznie kell olvasási és írási engedélyekkel a munkaterületen.

> [!NOTE]
> Azure virtuális gépekre, amelyek csatlakoznak a meglévő munkaterületen engedélyezett megoldások lépnek érvénybe. Fizetett megoldások esetén ez további díjakat eredményezhet. Az adatok adatvédelmi megfontolások ellenőrizze, hogy a kijelölt munkaterület a megfelelő földrajzi régióban.
> Az adatok log Analyticsben való tárolása további díjakat eredményezhet az adattároláshoz. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/security-center/) olvasható.

Egy meglévő Log Analytics-munkaterület kiválasztása:

1. A **alapértelmezett munkaterület-konfiguráció**válassza **másik munkaterület használata**.

   ![Meglévő munkaterület kiválasztása][2]

2. A legördülő menüből válassza ki a munkaterületet a gyűjtött adatok tárolásához.

   > [!NOTE]
   > A lekéréses menüre, az összes előfizetés között a munkaterületek érhetők el. Lásd: [előfizetés munkaterület kiválasztása adatbázisközi](security-center-enable-data-collection.md#cross-subscription-workspace-selection) további információt. A munkaterület hozzáférési engedéllyel kell rendelkeznie.
   >
   >

3. Kattintson a **Mentés** gombra.
4. Kiválasztása után **mentése**, meg kell adnia, ha szeretné újrakonfigurálja a figyelt virtuális gépek, amelyek a alapértelmezett munkaterülettel.

   - Válassza ki **nem** Ha azt szeretné, hogy az új munkaterület-beállítások csak új virtuális gépeket a alkalmazni. Az új munkaterület-beállítások csak az új ügynök telepítésére érvényesek; újonnan felderített virtuális gépek, amelyeken nincs telepítve a Log Analytics ügynök.
   - Válassza ki **Igen** Ha azt szeretné, hogy az új munkaterület-beállítások minden virtuális gép a alkalmazni. Emellett egy munkaterület létrehozása a Security Center minden virtuális Gépről az új cél munkaterületet újra csatlakoztatni.

   > [!NOTE]
   > Ha az Igen lehetőséget választja, nem kell törölnie a mindaddig, amíg az összes virtuális gép újra lett csatlakozott az új cél munkaterületet, a Security Center által létrehozott munkaterület(ek). Ez a művelet sikertelen lesz, ha a munkaterület túl korán törlődik.
   >
   >

   - Válassza ki **Mégse** megszakítja a műveletet.

     ![Meglévő munkaterület kiválasztása][3]

5. Válassza ki a kívánt munkaterülethez tartozó árképzési szintet, amelyet a Log Analytics-ügynököt kíván beállítani. <br>Egy meglévő munkaterületet használja, állítsa be a munkaterület tarifacsomagja. Ez telepíti a security Center megoldás a munkaterület Ha egyik még nem létezik.

    a.  A Security Center főmenüjében válassza a **díjszabás & beállítások**elemet.
     
    b.  Válassza ki azt a kívánt munkaterületet, amelyben csatlakozni kíván az ügynökhöz.
        ![Válassza ki a munkaterület][7] c. Állítsa a tarifacsomagot.
        ![Válasszon tarifacsomagot][9]
   
   >[!NOTE]
   >Ha már rendelkezik a munkaterület egy **biztonsági** vagy **SecurityCenterFree** a megoldás engedélyezve van, a díjszabás a rendszer automatikusan beállítja. 

## <a name="cross-subscription-workspace-selection"></a>Az előfizetések közötti munkaterület kiválasztása
Amikor kiválaszt egy munkaterületet, amely tárolja az adatokat, az összes előfizetés a munkaterületek érhetők el. Az előfizetések közötti munkaterület kiválasztása lehetővé teszi adatok gyűjtését különböző előfizetésben található virtuális gépeken, és tárolja a munkaterületet az Ön által választott. Ez a beállítás akkor hasznos, ha központosított munkaterületű használ a szervezetben, és azt szeretné használni a biztonsági adatok gyűjtését. Munkaterületek kezeléséről további információkért lásd: [munkaterület-hozzáférés kezelése](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access).


## <a name="data-collection-tier"></a>Gyűjtemény adatszint
Egy adatrétegbeli gyűjtemény kiválasztása az Azure Security Centerben a biztonsági események Log Analytics-munkaterület az a tároló csak hatással lesz. A Log Analytics ügynök továbbra is összegyűjti és elemzi a Azure Security Center fenyegetések észleléséhez szükséges biztonsági eseményeket, függetlenül attól, hogy az Log Analytics-munkaterületen milyen biztonsági események közül melyiket kell tárolnia (ha van ilyen). A munkaterületen tárolja a biztonsági események kiválasztása lehetővé teszi vizsgálat, a Keresés és a munkaterület események naplózását. 
> [!NOTE]
> Az adatok log Analyticsben való tárolása további díjakat eredményezhet az adattároláshoz. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/security-center/) olvasható.
> 
> A jobb házirend az előfizetések és munkaterületek négyféle események szűrése a munkaterület-ben való tárolásának közül választhat: 

- **Nincs** – tiltsa le a biztonsági események tárhelye. Ez az alapértelmezett beállítás.
- **Minimális** – kisebb események egy meghatározott készletének a objem událostí minimalizálása érdekében használni.
- **Közös** – Ez olyan események, amely megfelel a legtöbb ügyfél számára, és lehetővé teszi, hogy teljes auditnaplót.
- **Az összes esemény** – a vásárlóknak, akik szeretnék, hogy az összes esemény tárolódnak.


> [!NOTE]
> Ezen biztonsági események készletek csak a Security Center Standard szinten érhetők el. A Security Center tarifacsomagjaival kapcsolatos további információért lásd a [díjszabást](security-center-pricing.md).
Ezen készletek tervezték, hogy a tipikus forgatókönyvek. Ellenőrizze, hogy melyik illik jobban az igényeinek, még a megvalósítás előtt kiértékeléséhez.
>
>

Meghatározni az eseményeket, fog tartozni a **közös** és **minimális** események, működtünk az ügyfelekkel és az iparági normák megismerheti az egyes események és a használatuk szűretlen gyakoriságát. Ez a folyamat használjuk a következő irányelveket:

- **Minimális** – győződjön meg arról, hogy ez csak olyan eseményeket, amelyek esetleg jelzik a sikeres biztonsági incidenseinek és a fontos eseményekről, amelyek nagyon kevés üzenettel rendelkező vonatkozik-e. Ez a készlet például a felhasználó sikeres és sikertelen bejelentkezését (4624, 4625) tartalmazza, de nem tartalmazza a kijelentkezést, amely fontos a naplózáshoz, de az észleléshez nem releváns, és viszonylag nagy mennyiségű. Ezen adatok mennyisége a legtöbb, a bejelentkezési események és folyamat létrehozása event (esemény azonosítója 4688).
- **Közös** – adjon meg egy teljes felhasználói auditnapló ebben a készletben. Ez a készlet például felhasználói bejelentkezéseket és felhasználói bejelentkezéseket is tartalmaz (4634-es azonosítójú esemény). Például a biztonsági csoportok változásait, legfontosabb tartomány tartományvezérlő Kerberos műveleti és az eseményeket, amelyek a szervezetek által ajánlott műveletek naplózási tartalmazza.

Eseményeket, amelyek nagyon kevés a Rendszeríró a közös állítja be a fő motiváció kiválasztása az összes esemény keresztül csökkentése és a meghatározott események kiszűrésére.

Íme a biztonsági és AppLocker esemény azonosítók az egyes teljes áttekintését:

| Adatszint | Összegyűjtött események mutatók |
| --- | --- |
| Minimális | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| Közös | 1, 299, 300, 324, 340, 403, 404, 410, 411, 412, 413, 431, 500, 501, 1100, 1102, 1107, 1108, 4608, 4610, 4611, 4614, 4622, |
| |  4624,4625,4634,4647,4648,4649,4657,4661,4662,4663,4665,4666,4667,4688,4670,4672,4673,4674,4675,4689,4697, |
| | 4700,4702,4704,4705,4716,4717,4718,4719,4720,4722,4723,4724,4725,4726,4727,4728,4729,4733,4732,4735,4737, |
| | 4738,4739,4740,4742,4744,4745,4746,4750,4751,4752,4754,4755,4756,4757,4760,4761,4762,4764,4767,4768,4771, |
| | 4774,4778,4779,4781,4793,4797,4798,4799,4800,4801,4802,4803,4825,4826,4870,4886,4887,4888,4893,4898,4902, |
| | 4904,4905,4907,4931,4932,4933,4946,4948,4956,4985,5024,5033,5059,5136,5137,5140,5145,5632,6144,6145,6272, |
| | 6273,6278,6416,6423,6424,8001,8002,8003,8004,8005,8006,8007,8222,26401,30004 |

> [!NOTE]
> - Ha a csoportházirend-objektumot (GPO) használ, javasoljuk, hogy engedélyezze-e folyamat létrehozásának esemény 4688 naplórendek és a *CommandLine* mezőt 4688-as belül. Folyamat létrehozása esemény 4688 kapcsolatos további információkért lásd: a Security Center [– gyakori kérdések](security-center-faq.md#what-happens-when-data-collection-is-enabled). További tudnivalókat a naplózási házirendek, lásd: [naplózási szabályzatra vonatkozó javaslatok](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations).
> -  Adatgyűjtés engedélyezése [adaptív Alkalmazásvezérlők](security-center-adaptive-application.md), konfigurálja a helyi AppLocker-házirend rendszervizsgálati módban, hogy az összes alkalmazást. Ennek hatására az AppLocker események, amelyeket gyűjteni, és a Security Center által használható létrehozása. Fontos megjegyezni, hogy ez a szabályzat minden olyan gépeken, amelyeken már van egy konfigurált AppLocker-házirendek nem lesznek konfigurálva. 
> - Windows szűrőplatform gyűjtéséhez [Event ID 5156](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=5156), engedélyeznie kell a [naplózási szűrés kapcsolat](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection) (Auditpol parancshoz//subcategory beállítása: "Szűrés Platform kapcsolat" /Success:Enable)
>

A szűrési házirend kiválasztásához:
1. Az **adatgyűjtés** lapon válassza ki a szűrési házirendet a **biztonsági események**alatt.
2. Kattintson a **Mentés** gombra.

   ![Válassza ki a házirend szűrése][5]

### Automatikus kiépítés egy korábban létező ügynök telepítése esetén<a name="preexisting"></a> 

A következő használati esetek adja meg, hogy működik az esetre, ha már van egy ügynök vagy a bővítmény telepítése automatikus kiépítése. 

- Log Analytics ügynök telepítve van a gépen, de nem bővítményként (közvetlen ügynök)<br>
Ha a Log Analytics ügynök közvetlenül a virtuális gépre van telepítve (nem Azure-bővítményként), Security Center telepíti a Log Analytics-ügynök bővítményt, és a Log Analytics-ügynököt a legújabb verzióra frissíti.
A telepített ügynök továbbra is jelentést készít a már konfigurált munkaterület (ek) ről, és a Security Center-ben konfigurált munkaterületnek is jelentést küld (a Windows rendszerű gépeken támogatott a többszörös vezérlési funkció).
Ha a konfigurált munkaterület egy felhasználói munkaterület (nem Security Center alapértelmezett munkaterülete), akkor telepítenie kell a "Security/" securityFree "megoldást arra a Security Centerra, hogy elindítsa a munkaterületre jelentett virtuális gépekről és számítógépekről származó események feldolgozását.<br>
<br>
Linux rendszerű gépek esetén az ügynök többsoros vezérlése még nem támogatott – ezért ha egy meglévő ügynök telepítését észleli, az automatikus kiépítés nem történik meg, és a gép konfigurációja nem módosul.
<br>
A 2019-03-17 előtti Security Center előfizetésekben lévő meglévő gépek esetében a rendszer nem telepíti a Log Analytics ügynök bővítményt, és a gép nem lesz hatással. Ezen gépek esetében tekintse meg a "figyelési ügynök állapotával kapcsolatos problémák megoldása a gépeken" című javaslatot az ügynök telepítési problémáinak megoldásához ezeken a gépeken.

  
- System Center Operations Manager ügynök telepítve van a gépen<br>
A Security Center a Log Analytics Agent bővítményt a meglévő Operations Manager telepíti. A meglévő Operations Manager ügynöke a szokásos módon folytatja a Operations Manager-kiszolgáló jelentését. Vegye figyelembe, hogy a Operations Manager ügynök és Log Analytics ügynök közös futásidejű kódtárakat használ, amelyeket a rendszer a folyamat során a legújabb verzióra frissít.
Megjegyzés: Ha a Operations Manager ügynök 2012-es verziója telepítve van, ne kapcsolja be az automatikus kiépítés beállítást.<br>

- Jelen egy már meglévő Virtuálisgép-bővítmény<br>
    - Ha a figyelési ügynök bővítményként van telepítve, a bővítmény konfigurációja lehetővé teszi, hogy a jelentéskészítés csak egyetlen munkaterületre legyen elérhető. A Security Center nem bírálja felül a meglévő kapcsolatok felhasználó munkaterületeket. Security Center a virtuális gépről a már csatlakoztatott munkaterületen tárolja a biztonsági adatait, ha a "biztonság" vagy a "securityFree" megoldás telepítve van rajta. Security Center a folyamat legújabb verziójára frissítheti a bővítmény verzióját.  
    - Megtekintheti, hogy melyik munkaterület a meglévő bővítmény küld adatokat, hogy a teszt futtatása [ellenőrizze a csatlakozását az Azure Security Center](https://blogs.technet.microsoft.com/yuridiogenes/2017/10/13/validating-connectivity-with-azure-security-center/). Másik lehetőségként megnyithatja Log Analytics munkaterületeket, kiválaszthat egy munkaterületet, kiválaszthatja a virtuális gépet, és megtekintheti a Log Analytics-ügynök közötti kapcsolatokat. 
    - Ha olyan környezettel rendelkezik, amelyben a Log Analytics-ügynök telepítve van az ügyfél-munkaállomásokon, és jelentéskészítés egy meglévő Log Analytics munkaterületre, tekintse át az [Azure Security Center által támogatott operációs rendszerek](security-center-os-coverage.md) listáját, és győződjön meg arról, hogy az operációs rendszer támogatott. További információ: [meglévő log Analytics-ügyfelek](security-center-faq.md#existingloganalyticscust).
 
### Kapcsolja ki az Automatikus kiépítés <a name="offprovisioning"></a>
Kikapcsolhatja az Automatikus kiépítés erőforrásokból bármikor ezt a beállítást, a biztonsági szabályzatban kikapcsolásával. 


1. Térjen vissza a Security Center főmenüjébe, és válassza ki a biztonsági szabályzatot.
2. Kattintson a **beállítások szerkesztése** elemre azon előfizetés sorában, amely esetében le szeretné tiltani az automatikus kiépítés beállítást.
3. Az a **biztonsági szabályzat – adatgyűjtés** panel alatt **automatikus kiépítés** kiválasztása **ki**.
4. Kattintson a **Mentés** gombra.

   ![Automatikus kiépítés letiltása][6]

Automatikus kiépítés letiltása (kikapcsolva) esetén az alapértelmezett munkaterület-konfigurációs szakasz nem jelenik meg.

Ha kikapcsolhatja az automatikus üzembe helyezése után nem található:
-   Ügynökök nem építi ki a új virtuális gépeket.
-   A Security Center leállítja az adatok gyűjtését az alapértelmezett munkaterületre.
 
> [!NOTE]
>  Az automatikus kiépítés letiltása nem távolítja el a Log Analytics ügynököt azon Azure-beli virtuális gépekről, amelyeken az ügynök ki lett építve. Az OMS bővítmény eltávolításával kapcsolatban további információkért lásd: [Hogyan távolíthatom el a Security Center által telepített OMS-kiterjesztéseket](security-center-faq.md#remove-oms).
>
    
## Ügynök manuális kiépítése <a name="manual-agent"></a>
 
Több módon is telepítheti a Log Analytics-ügynököt manuálisan. Ha manuálisan telepíti, győződjön meg róla, az Automatikus kiépítés letiltása.

### <a name="operations-management-suite-vm-extension-deployment"></a>Az Operations Management Suite virtuális Géphez konzolbővítmény üzembe helyezése 

Manuálisan is telepítheti a Log Analytics-ügynököt, így Security Center a virtuális gépekről gyűjthet biztonsági adatokat, és javaslatokat és riasztásokat is biztosíthat.
1. Válassza ki a automatikus üzembe helyezése – kikapcsolva.
2. Hozzon létre egy munkaterületet, és állítsa be azt a munkaterülethez tartozó árképzési szintet, amelyet a Log Analytics-ügynököt kíván beállítani:

   a.  A Security Center főmenüjében válassza **biztonsági házirend**.
     
   b.  Válassza ki a munkaterületet, ahol csatlakoztassa az ügynököt kíván. Ellenőrizze, hogy a munkaterület ugyanabban az előfizetésben, használhatja a Security Centerben, és hogy van-e olvasási/írási engedéllyel a munkaterületen.
       ![Válasszon munkaterületet][8]
3. Állítsa a tarifacsomagot.
   ![Válasszon tarifacsomagot][9] 
   >[!NOTE]
   >Ha már rendelkezik a munkaterület egy **biztonsági** vagy **SecurityCenterFree** a megoldás engedélyezve van, a díjszabás a rendszer automatikusan beállítja. 
   > 

4. Ha szeretné az ügynökök a Resource Manager-sablon használatával új virtuális gépek üzembe helyezése, telepítse az OMS virtuális gépi bővítmény:

   a.  [Windows virtuális gép OMS bővítményének telepítése](../virtual-machines/extensions/oms-windows.md)
    
   b.  [Linuxhoz készült OMS virtuálisgép-bővítmény telepítése](../virtual-machines/extensions/oms-linux.md)
5. A bővítmények a meglévő virtuális gépek üzembe helyezéséhez kövesse a [Azure virtuális gépekről történő adatgyűjtést](../azure-monitor/learn/quick-collect-azurevm.md).

   > [!NOTE]
   > A szakasz **esemény-és teljesítményadatok gyűjtése** nem kötelező.
   >
6. A bővítmény telepítése a PowerShell használatával, használja a következő PowerShell-példa:
   
   [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
   
   1. Lépjen a **Log Analytics** , majd kattintson a **speciális beállítások**.
    
      ![A log analytics beállítása][11]

   2. Másolja ki az értékeket **munkaterület azonosítója** és **elsődleges kulcs**.
  
      ![Értékek másolása][12]

   3. Töltse fel a nyilvános config és a privát config ezekkel az értékekkel:
     
           $PublicConf = @{
               "workspaceId"= "<WorkspaceID value>"
           }
 
           $PrivateConf = @{
               "workspaceKey"= "<Primary key value>”
           }

      - Amikor telepíti egy Windows virtuális gépen:
        
            Set-AzVMExtension -ResourceGroupName $vm.ResourceGroupName -VMName $vm.Name -Name "MicrosoftMonitoringAgent" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "MicrosoftMonitoringAgent" -TypeHandlerVersion '1.0' -Location $vm.Location -Settingstring $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True 
    
      - Amikor telepíti egy Linux rendszerű virtuális gépen:
        
            Set-AzVMExtension -ResourceGroupName $vm1.ResourceGroupName -VMName $vm1.Name -Name "OmsAgentForLinux" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "OmsAgentForLinux" -TypeHandlerVersion '1.0' -Location $vm.Location -Settingstring $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True`

> [!NOTE]
> Hogyan felvétele a Security Center PowerShell-lel, tekintse meg útmutatást [automatizálása a PowerShell-lel az Azure Security Center bevezetése](security-center-powershell-onboarding.md).

## <a name="troubleshooting"></a>Hibaelhárítás

-   Az Automatikus kiépítés telepítésével kapcsolatos problémák azonosítására, lásd: [Monitoring agent állapotproblémái](security-center-troubleshooting-guide.md#mon-agent).

-  Figyelési ügynök hálózati követelmények azonosításához tekintse meg a [hibaelhárítási monitoring agent hálózati követelményeit](security-center-troubleshooting-guide.md#mon-network-req).
-   A manuális előkészítési problémáinak azonosítása, lásd: [Operations Management Suite előkészítési problémáinak hibaelhárítása](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues).

- Nem Monitorozott virtuális gépek és számítógépek problémák azonosításához tekintse meg a [nem Monitorozott virtuális gépek és számítógépek](security-center-virtual-machine-protection.md#unmonitored-vms-and-computers).

## <a name="next-steps"></a>További lépések
Ez a cikk bemutatta, hogyan gyűjti az adatokat, és a Security Center működik az automatikus kiépítést. A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Azure Security Center: gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md) –Tanulja meg az Azure-erőforrások állapotfigyelésének módját.



<!--Image references-->
[1]: ./media/security-center-enable-data-collection/enable-automatic-provisioning.png
[2]: ./media/security-center-enable-data-collection/use-another-workspace.png
[3]: ./media/security-center-enable-data-collection/reconfigure-monitored-vm.png
[5]: ./media/security-center-enable-data-collection/data-collection-tiers.png
[6]: ./media/security-center-enable-data-collection/disable-data-collection.png
[7]: ./media/security-center-enable-data-collection/select-subscription.png
[8]: ./media/security-center-enable-data-collection/manual-provision.png
[9]: ./media/security-center-enable-data-collection/pricing-tier.png
[10]: ./media/security-center-enable-data-collection/workspace-selection.png
[11]: ./media/security-center-enable-data-collection/log-analytics.png
[12]: ./media/security-center-enable-data-collection/log-analytics2.png
