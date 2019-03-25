---
title: Az adatgyűjtést az Azure Security Centerben |} A Microsoft Docs
description: " Ismerje meg, hogyan lehet engedélyezni az adatgyűjtést az Azure Security Centerben. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2018
ms.author: monhaber
ms.openlocfilehash: 2ce3a94b7ea4745ec3727e508cd10bb580a74c98
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401033"
---
# <a name="data-collection-in-azure-security-center"></a>Az adatgyűjtést az Azure Security Centerben
A Security Center adatokat gyűjt az Azure-beli virtuális gépek (VM), a virtual machine scale sets, IaaS-tárolók és nem Azure-beli (pl. helyszíni) számítógépek biztonsági rések és fenyegetések monitorozásához. Az adatgyűjtés a Microsoft Monitoring Agent segítségével történik, amely a biztonsághoz kapcsolódó különböző konfigurációkat és eseménynaplókat olvas be a gépről, és elemzés céljából átmásolja az adatokat az Ön munkaterületére. Az ilyen adatok többek között: operációs rendszer típusa és verziója, az operációs rendszer naplói (Windows-eseménynaplók), a futó folyamatok, a gép nevét, az IP-címeket, és bejelentkezett felhasználó. A Microsoft Monitoring Agent ügynököt az összeomlási memóriaképeket is átmásolja a munkaterülethez.

Az adatgyűjtés nem kell megadnia a hiányzó frissítések, hibásan konfigurált operációs rendszer biztonsági beállításai, endpoint protection engedélyezése és állapot- és fenyegetés-észlelési láthatósága. 

Ez a cikk a Microsoft Monitoring Agent telepítése, és állítsa be, amely tárolja a gyűjtött adatokat Log Analytics-munkaterületet nyújt útmutatást. Mindkét műveletet az adatgyűjtés engedélyezéséhez szükségesek. 

> [!NOTE]
> - Adatok gyűjtése csak akkor van szükség a számítási erőforrásokat (virtuális gépek, a virtual machine scale sets, IaaS-tárolók és nem Azure-beli számítógépek). Élvezheti az Azure Security Center akkor is, ha nem üzembe helyezi az ügynökök; azonban Ön csak korlátozott biztonsági, és a fent felsorolt funkciók nem támogatottak.  
> - A támogatott platformok listáját lásd: [az Azure Security Center által támogatott platformok](security-center-os-coverage.md).
> - Adatgyűjtés virtuálisgép-méretezési csoport esetében jelenleg nem támogatott.
> - Adatok tárolása a Log Analytics, akár egy új vagy meglévő munkaterületet, előfordulhat, hogy további díjak vonatkoznak az adattárolás, a díjszabási lapon talál további részleteket.

## A Microsoft Monitoring Agent automatikus kiépítésének engedélyezése <a name="auto-provision-mma"></a>

Az adatok gyűjtését a gépek rendelkeznie kell a Microsoft Monitoring Agent telepítve van.  Az ügynök telepítése automatikusan lehet (ajánlott), vagy dönthet úgy, hogy telepítse kézzel az ügynököt.  

>[!NOTE]
> Alapértelmezés szerint az Automatikus kiépítés le van. A Security Center telepítése alapértelmezés szerint az Automatikus kiépítés beállítása, állítsa **a**.
>

Amikor az Automatikus kiépítés egy, a Security Center a Microsoft Monitoring agent, az összes támogatott Azure-beli és újonnan létrehozott látja el. Az Automatikus kiépítés használata erősen ajánlott, de a manuális ügynöktelepítések is rendelkezésre áll. [Ismerje meg, hogyan telepítse a Microsoft Monitoring Agent bővítményt](#manualagent).



A Microsoft Monitoring Agent automatikus kiépítésének engedélyezése:
1. A Security Center főmenüjében válassza **biztonsági házirend**.
2. Kattintson a **beállításainak szerkesztése** a beállítások oszlop a listában a kívánt előfizetés.

   ![Előfizetés kiválasztása][7]

3. A **Biztonsági szabályzat** területen válassza az **Adatgyűjtés** elemet.
4. A **automatikus kiépítés**válassza **a** az Automatikus kiépítés engedélyezéséhez.
5. Kattintson a **Mentés** gombra.

   ![Automatikus kiépítés engedélyezése][1]

>[!NOTE]
> - Hogyan építheti ki egy már meglévő telepítési utasításokért lásd: [automatikus üzembe helyezés abban az esetben egy már létező ügynöktelepítés](#preexisting).
> - A manuális kiépítési útmutatásért lásd: [manuális telepítése a Microsoft Monitoring Agent bővítményt](#manualagent).
> - Útmutatás az Automatikus kiépítés kikapcsolása: [kapcsolja ki az Automatikus kiépítés](#offprovisioning).
> - Hogyan felvétele a Security Center PowerShell-lel, tekintse meg útmutatást [automatizálása a PowerShell-lel az Azure Security Center bevezetése](security-center-powershell-onboarding.md).
>

## <a name="workspace-configuration"></a>Munkaterület-konfiguráció
A Security Center által gyűjtött adatokat Log Analytics-munkaterületen tárolja.  Kiválaszthatja, hogy az adatok tárolása a Security Center által létrehozott munkaterületeken vagy egy meglévő munkaterületet hozott létre az Azure virtuális gépekről gyűjtött. 

Munkaterület-konfiguráció beállítása előfizetésenként, de sok előfizetést használhatja ugyanazon a munkaterületen.

### <a name="using-a-workspace-created-by-security-center"></a>A Security Center által létrehozott munkaterület használatával

A Security center automatikusan létrehozhat egy alapértelmezett munkaterületet, amely tárolja az adatokat. 

A Security Center által létrehozott munkaterület kiválasztása:

1. A **alapértelmezett munkaterület-konfiguráció**, válassza ki a Security center által létrehozott munkaterület(ek) használata.
   ![Válasszon tarifacsomagot][10] 

1. Kattintson a **Save** (Mentés) gombra.<br>
    A Security Center egy új erőforrás és egy alapértelmezett munkaterületet hoz létre, hogy földrajzi hely, és csatlakoztatja az ügynököt a munkaterülethez. A munkaterület és erőforrás-csoport elnevezési van:<br>
   **Munkaterület: Alapértelmezettmunkaterület-[előfizetés-azonosító]-[geo]<br> erőforráscsoportot: DefaultResourceGroup-[geo]**

   Ha egy előfizetés több geolocations a virtuális gépeket tartalmaz, a Security Center több munkaterületet hoz létre. Több munkaterülettel jönnek létre az adatok adatvédelmi szabályok kezelése.
1. A Security Center automatikusan engedélyezi a a tarifacsomagot állítsa be az előfizetés-munkaterülethez a Security Center megoldást. 

> [!NOTE]
> A Security Center által létrehozott munkaterület tarifacsomagját a Log Analytics nincs hatással a Security Center a számlázás. A Security Center számlázási mindig alapján a Security Center biztonsági házirend és a megoldások a munkaterülethez telepítve. Az ingyenes, a Security Center lehetővé teszi a *SecurityCenterFree* megoldás az alapértelmezett munkaterületre. A Standard csomag esetében a Security Center lehetővé teszi a *biztonsági* megoldás az alapértelmezett munkaterületre.
> A Log Analytics adattárolási előfordulhat, hogy további díjak vonatkoznak az adattárolás, a díjszabási lapon talál további részleteket.

További információ a díjszabásról lásd: [a Security Center díjszabási](https://azure.microsoft.com/pricing/details/security-center/).

További információ a meglévő log analytics-fiókok: [meglévő log analytics-ügyfél](security-center-faq.md#existingloganalyticscust).

### <a name="using-an-existing-workspace"></a>Egy meglévő munkaterület használata

Ha már rendelkezik egy meglévő Log Analytics-munkaterületet, érdemes ugyanazt a munkaterületet használja.

A meglévő Log Analytics-munkaterület használatához rendelkeznie kell olvasási és írási engedélyekkel a munkaterületen.

> [!NOTE]
> Azure virtuális gépekre, amelyek csatlakoznak a meglévő munkaterületen engedélyezett megoldások lépnek érvénybe. Fizetett megoldások esetén ez további díjakat eredményezhet. Az adatok adatvédelmi megfontolások ellenőrizze, hogy a kijelölt munkaterület a megfelelő földrajzi régióban.
> A log analytics adattárolási előfordulhat, hogy további díjak vonatkoznak az adattárolás, a díjszabási lapon talál további részleteket.

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

   - Válassza ki **nem** Ha azt szeretné, hogy az új munkaterület-beállítások csak új virtuális gépeket a alkalmazni. Az új munkaterület csak vonatkoznak új ügynökök telepítése; az újonnan felderített virtuális gépek, amelyeken nincs telepítve a Microsoft Monitoring Agent telepítve van.
   - Válassza ki **Igen** Ha azt szeretné, hogy az új munkaterület-beállítások minden virtuális gép a alkalmazni. Emellett egy munkaterület létrehozása a Security Center minden virtuális Gépről az új cél munkaterületet újra csatlakoztatni.

   > [!NOTE]
   > Ha az Igen lehetőséget választja, nem kell törölnie a mindaddig, amíg az összes virtuális gép újra lett csatlakozott az új cél munkaterületet, a Security Center által létrehozott munkaterület(ek). Ez a művelet sikertelen lesz, ha a munkaterület túl korán törlődik.
   >
   >

   - Válassza ki **Mégse** megszakítja a műveletet.

     ![Meglévő munkaterület kiválasztása][3]

5. Válassza ki a tarifacsomagot állítsa be a Microsoft Monitoring agent szeretne a kívánt munkaterülethez. <br>Egy meglévő munkaterületet használja, állítsa be a munkaterület tarifacsomagja. Ez telepíti a security Center megoldás a munkaterület Ha egyik még nem létezik.

    a.  A Security Center főmenüjében válassza **biztonsági házirend**.
     
    b.  Válassza ki a kívánt munkaterületet, amelyben szeretné az ügynököt csatlakoztatja kattintva **beállításainak szerkesztése** a beállítások oszlop a listában a kívánt előfizetés.
        ![Válassza ki a munkaterület][8] c. Állítsa a tarifacsomagot.
        ![Válasszon tarifacsomagot][9] 
   
   >[!NOTE]
   >Ha már rendelkezik a munkaterület egy **biztonsági** vagy **SecurityCenterFree** a megoldás engedélyezve van, a díjszabás a rendszer automatikusan beállítja. 

## <a name="cross-subscription-workspace-selection"></a>Az előfizetések közötti munkaterület kiválasztása
Amikor kiválaszt egy munkaterületet, amely tárolja az adatokat, az összes előfizetés a munkaterületek érhetők el. Az előfizetések közötti munkaterület kiválasztása lehetővé teszi adatok gyűjtését különböző előfizetésben található virtuális gépeken, és tárolja a munkaterületet az Ön által választott. Ez a beállítás akkor hasznos, ha központosított munkaterületű használ a szervezetben, és azt szeretné használni a biztonsági adatok gyűjtését. Munkaterületek kezeléséről további információkért lásd: [munkaterület-hozzáférés kezelése](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access).


## <a name="data-collection-tier"></a>Gyűjtemény adatszint
Egy adatrétegbeli gyűjtemény kiválasztása az Azure Security Centerben a biztonsági események Log Analytics-munkaterület az a tároló csak hatással lesz. A Log Analytics-ügynök továbbra is gyűjthet, és elemezheti a biztonsági események az Azure Security Center fenyegetések felderítése során, függetlenül attól, melyik szint a biztonsági események úgy dönt, hogy tárolja a Log Analytics-munkaterület (ha vannak) szükséges. A munkaterületen tárolja a biztonsági események kiválasztása lehetővé teszi vizsgálat, a Keresés és a munkaterület események naplózását. 
> [!NOTE]
> A log analytics adattárolási előfordulhat, hogy további díjak vonatkoznak az adattárolás, a díjszabási lapon talál további részleteket.
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

- **Minimális** – győződjön meg arról, hogy ez csak olyan eseményeket, amelyek esetleg jelzik a sikeres biztonsági incidenseinek és a fontos eseményekről, amelyek nagyon kevés üzenettel rendelkező vonatkozik-e. Például a felhasználó sikeres és sikertelen bejelentkezés (esemény azonosítók 4624, 4625-ös számú) tartalmazza, de jelentkezzen ki, amely a naplózás fontos, de nem értelmezhető az észlelést, és viszonylag nagy mennyiségű nem tartalmaz. Ezen adatok mennyisége a legtöbb, a bejelentkezési események és folyamat létrehozása event (esemény azonosítója 4688).
- **Közös** – adjon meg egy teljes felhasználói auditnapló ebben a készletben. A készlet például felhasználói bejelentkezéseket és a felhasználói kijelentkezésre (event ID 4634) tartalmazza. Például a biztonsági csoportok változásait, legfontosabb tartomány tartományvezérlő Kerberos műveleti és az eseményeket, amelyek a szervezetek által ajánlott műveletek naplózási tartalmazza.

Eseményeket, amelyek nagyon kevés a Rendszeríró a közös állítja be a fő motiváció kiválasztása az összes esemény keresztül csökkentése és a meghatározott események kiszűrésére.

Íme a biztonsági és AppLocker esemény azonosítók az egyes teljes áttekintését:

| Adatszint | Összegyűjtött események mutatók |
| --- | --- |
| Minimális | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| Közös | 1,299,300,324,340,403,404,410,411,412,413,431,500,501,1100,1102,1107,1108,4608,4610,4611,4614,461,4622, |
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
1. Az a **az adatgyűjtést a biztonsági házirend** panelen jelölje be a szűrési házirend alapján **biztonsági események**.
2. Kattintson a **Mentés** gombra.

   ![Válassza ki a házirend szűrése][5]

### Automatikus üzembe helyezés abban az esetben egy már létező ügynök telepítése <a name="preexisting"></a> 

A következő használati esetek adja meg, hogy működik az esetre, ha már van egy ügynök vagy a bővítmény telepítése automatikus kiépítése. 

- A Microsoft Monitoring Agent telepítve van a gépen, de nem bővítményeként (közvetlen ügynök)<br>
Ha a Microsoft Monitoring Agent közvetlenül a virtuális gép (nem pedig egy Azure-bővítmény) van telepítve, a Security Center telepíti a Microsoft Monitoring Agent bővítményt, és előfordulhat, hogy a Microsoft Monitoring agent frissítése a legújabb verzióra.
Az ügynök telepítve van továbbra is, hogy a már konfigurált munkaterületeire, és emellett a munkaterülethez, a Security Center konfigurált jelentést (többkiszolgálós támogatott).
Ha a konfigurált munkaterületével felhasználói munkaterület (nem a Security Center alapértelmezett munkaterületen), akkor telepítenie kell a "biztonsági /"securityFree"megoldás a Security Center használatával indítsa el az események feldolgozását a virtuális gépek és számítógépek számára a jelentéskészítés munkaterületről.<br>
<br>
Előfizetések előkészítette a Security Center a 2019-03-17., amikor egy meglévő ügynököt érzékeli, mielőtt a meglévő gépek nem lesz telepítve a Microsoft Monitoring Agent bővítményt, és a gép nem lesz hatással. Ezek a gépek tekintse meg a "Feloldás a monitoring agent állapotproblémái a gépeken" javaslathoz ezeken a gépeken az ügynök telepítésével kapcsolatos problémák megoldásához.

  
- A System Center Operations Manager-ügynök telepítve van a gépen<br>
A Security center a Microsoft Monitoring Agent bővítmény egymás mellett telepíti a meglévő Operations Manager. A meglévő Operations Manager-ügynök továbbra is, hogy az Operations Manager-kiszolgáló szokásos módon. Vegye figyelembe, hogy az Operations Manager-ügynök és a Microsoft Monitoring Agent megosztani futásidejű kódtárak, amely a folyamat során a legújabb verzióra frissül.
Megjegyzés: Ha az Operations Manager 2012 ügynök verziója telepítve van, **nem** az Automatikus kiépítés a.<br>

- Jelen egy már meglévő Virtuálisgép-bővítmény<br>
    - Amikor a Monitoring Agent bővítmény van telepítve, a bővítmény konfigurációja lehetővé teszi, hogy csak egyetlen munkaterület jelentéskészítés. A Security Center nem bírálja felül a meglévő kapcsolatok felhasználó munkaterületeket. A Security Center tárolja a virtuális gép biztonsági adatait a munkaterülethez már csatlakoztatott, feltéve, hogy a "security" vagy "securityFree" megoldás telepítve van rajta. A Security Center előfordulhat, hogy a bővítmény verziója frissítsen a legújabb verzióra a folyamat.  
    - Megtekintheti, hogy melyik munkaterület a meglévő bővítmény küld adatokat, hogy a teszt futtatása [ellenőrizze a csatlakozását az Azure Security Center](https://blogs.technet.microsoft.com/yuridiogenes/2017/10/13/validating-connectivity-with-azure-security-center/). Azt is megteheti hogy is nyissa meg a Log Analytics-munkaterületek, válasszon ki egy munkaterületet, válassza ki a virtuális Gépet és tekintse meg a Log Analytics az ügynök kapcsolatot. 
    - Ha-környezettel rendelkezik, a Log Analytics-ügynök telepítve van az ügyfél-munkaállomásokon és jelentéskészítés meglévő Log Analytics-munkaterülethez, tekintse át a [az Azure Security Center által támogatott operációs rendszerek](security-center-os-coverage.md) győződjön meg arról, hogy az operációs rendszer támogatott, és tekintse meg [meglévő log analytics-ügyfél](security-center-faq.md#existingloganalyticscust) további információt.
 
### Kapcsolja ki az Automatikus kiépítés <a name="offprovisioning"></a>
Kikapcsolhatja az Automatikus kiépítés erőforrásokból bármikor ezt a beállítást, a biztonsági szabályzatban kikapcsolásával. 


1. Térjen vissza a Security Center főmenüjébe, és válassza ki a biztonsági szabályzatot.
2. Kattintson a **beállításainak szerkesztése** az előfizetést, amelyhez le kívánja tiltani az Automatikus kiépítés sorában.
3. Az a **biztonsági szabályzat – adatgyűjtés** panel alatt **automatikus kiépítés** kiválasztása **ki**.
4. Kattintson a **Mentés** gombra.

   ![Automatikus kiépítés letiltása][6]

Automatikus kiépítés letiltása (kikapcsolva) esetén az alapértelmezett munkaterület-konfigurációs szakasz nem jelenik meg.

Ha kikapcsolhatja az automatikus üzembe helyezése után nem található:
-   Ügynökök nem építi ki a új virtuális gépeket.
-   A Security Center leállítja az adatok gyűjtését az alapértelmezett munkaterületre.
 
> [!NOTE]
>  Az Automatikus kiépítés letiltása nem távolítja el a Microsoft Monitoring Agent az Azure virtuális gépekről, ha az ügynök lett üzembe helyezve. Az OMS bővítmény eltávolításával kapcsolatban további információkért lásd: [Hogyan távolíthatom el a Security Center által telepített OMS-kiterjesztéseket](security-center-faq.md#remove-oms).
>
    
## Ügynök manuális kiépítése <a name="manualagent"></a>
 
Többféleképpen is lehet manuális telepítése a Microsoft Monitoring Agent. Ha manuálisan telepíti, győződjön meg róla, az Automatikus kiépítés letiltása.

### <a name="operations-management-suite-vm-extension-deployment"></a>Az Operations Management Suite virtuális Géphez konzolbővítmény üzembe helyezése 

A Microsoft Monitoring Agent, manuálisan is telepítheti, így a Security Center biztonsági adatok gyűjtésére a virtuális gépeket, és adja meg a javaslatok és riasztások.
1. Válassza ki a automatikus üzembe helyezése – kikapcsolva.
2. Hozzon létre egy munkaterületet, és adja meg, állítsa be a Microsoft Monitoring agent kívánja a munkaterület tarifacsomagja:

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
     
           $PublicConf = '{
               "workspaceId": "WorkspaceID value",
           }' 
 
           $PrivateConf = '{
               "workspaceKey": "<Primary key value>”
           }' 

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
