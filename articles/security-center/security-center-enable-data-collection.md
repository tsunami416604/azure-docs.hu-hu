---
title: Adatgyűjtés az Azure Security Centerben | Microsoft dokumentumok
description: Ez a cikk ismerteti, hogyan telepítheti a Log Analytics-ügynök, és állítsa be a Log Analytics-munkaterületet, ahol az összegyűjtött adatok tárolására.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: 5236fd89ed99ca14bb7fc24e40ef79e12e5177d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245498"
---
# <a name="data-collection-in-azure-security-center"></a>Adatgyűjtés az Azure Security Centerben
A Security Center adatokat gyűjt az Azure virtuális gépekről (VM-ek), a virtuálisgép-méretezési csoportokból, az IaaS-tárolókból és a nem Azure-os (beleértve a helyszíni számítógépeket is) a biztonsági rések és fenyegetések figyeléséhez. Az adatok gyűjtése a Log Analytics-ügynök használatával történik, amely beolvassa a különböző biztonsággal kapcsolatos konfigurációkat és eseménynaplókat a gépről, és átmásolja az adatokat a munkaterületre elemzésre. Ilyen adatok például a következők: az operációs rendszer típusa és verziója, az operációs rendszer naplói (Windows eseménynaplók), futó folyamatok, számítógépnév, IP-címek és bejelentkezett felhasználó. A Log Analytics Ügynök is másolja összeomlási memóriakép fájlokat a munkaterületre.

Adatgyűjtésre van szükség, hogy betekintést nyújtson a hiányzó frissítések, a helytelenül konfigurált operációs rendszer biztonsági beállításai, végpontvédelem állapota, valamint az állapot- és veszélyforrások elleni védelem. 

Ez a cikk ismerteti, hogyan telepítheti a Log Analytics-ügynök, és állítsa be a Log Analytics-munkaterületet, ahol az összegyűjtött adatok tárolására. Mindkét művelet szükséges az adatgyűjtés engedélyezéséhez. 

> [!NOTE]
> - Adatgyűjtésre csak számítási erőforrások (virtuális gépek, virtuálisgép-méretezési csoportok, IaaS-tárolók és nem Azure-számítógépek) számítási erőforrásokhoz van szükség. Akkor is élvezheti az Azure Security Center előnyeit, ha nem nyújt be ügynököket; azonban korlátozott biztonsággal rendelkezik, és a fent felsorolt képességek nem támogatottak.  
> - A támogatott platformok listáját az [Azure Security Center támogatott platformjai című témakörben található.](security-center-os-coverage.md)
> - Az adatok tárolása a Log Analytics szolgáltatásban, függetlenül attól, hogy új vagy meglévő munkaterületet használ, további díjakat vonhat maga után az adattárolásért. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/security-center/) olvasható.

## <a name="enable-automatic-provisioning-of-the-log-analytics-agent"></a>A Log Analytics-ügynök automatikus kiépítésének engedélyezése<a name="auto-provision-mma"></a>

Az adatok gyűjtése a gépekről, telepítenie kell a Log Analytics-ügynök. Az ügynök telepítése történhet automatikusan (ajánlott), vagy telepítheti az ügynök manuálisan.  

>[!NOTE]
> Az automatikus kiépítés alapértelmezés szerint ki van kapcsolva. Ha azt szeretné, hogy a Security Center alapértelmezés szerint telepítse az automatikus kiépítést, állítsa **be a Be**beállítást.
>

Ha az automatikus kiépítés be van kapcsolva, a Security Center a Log Analytics-ügynököt az összes támogatott Azure-virtuális gépre és a létrehozott újgépekre vonatkozóan leépíti. Automatikus kiépítése erősen ajánlott, de kézi ügynök telepítése is rendelkezésre áll. [További információ a Log Analytics Ügynök bővítmény telepítéséről.](#manual-agent)



A Log Analytics-ügynök automatikus kiépítésének engedélyezése:
1. A Biztonsági központ főmenüjében válassza **az Árképzési & beállítások lehetőséget.**
2. Kattintson a megfelelő előfizetésre

   ![Előfizetés kiválasztása][7]

3. Válassza **az Adatgyűjtés**lehetőséget.
4. Az **Automatikus kiépítés**csoportban válassza **a Be** lehetőséget az automatikus kiépítés engedélyezéséhez.
5. Kattintson a **Mentés** gombra.

   ![Automatikus kiépítés engedélyezése][1]

>[!NOTE]
> - A már meglévő telepítés kiépítésével kapcsolatos tudnivalókat az [Automatikus kiépítés meglévő ügynöktelepítés esetén](#preexisting)című témakörben találja.
> - A manuális kiépítésről [a Log Analytics ügynök bővítmény manuális telepítése című témakörben](#manual-agent)olvashat.
> - Az automatikus kiépítés kikapcsolásával kapcsolatos tudnivalókat az [Automatikus kiépítés kikapcsolása (Automatikus kiépítés) (Automatikus kiépítés kikapcsolása) (Automatikus kiépítés kikapcsolása) témakörben találja.](#offprovisioning)
> - A Security Center PowerShell használatával történő fedélzeti verziójáról az [Azure Security Center PowerShell használatával történő bevezetés automatizálása témakörben talál útmutatást.](security-center-powershell-onboarding.md)
>

## <a name="workspace-configuration"></a>Munkaterület konfigurálása
A Security Center által gyűjtött adatokat a rendszer Log Analytics-munkaterület(ek)en tárolja Kiválaszthatja, hogy az Azure-beli virtuális gépekről gyűjtött adatokat a Security Center által létrehozott munkaterületeken vagy egy ön által létrehozott meglévő munkaterületen tárolja.You can select to have collected from Azure VMs stored in workspaces created by Security Center or in a existing workspace you created. 

A munkaterület-konfiguráció előfizetésenként van beállítva, és sok előfizetés használhatja ugyanazt a munkaterületet.

### <a name="using-a-workspace-created-by-security-center"></a>A Security Center által létrehozott munkaterület használata

A Biztonsági központ automatikusan létrehozhat egy alapértelmezett munkaterületet, ahol az adatokat tárolni szeretné. 

A Security Center által létrehozott munkaterület kiválasztása:

1. Az **Alapértelmezett munkaterület-konfiguráció**csoportban válassza a Biztonsági központ által létrehozott munkaterület(ek) használata lehetőséget.
   ![Tarifacsomag kiválasztása][10] 

1. Kattintson a **Mentés** gombra.<br>
    A Security Center új erőforráscsoportot és alapértelmezett munkaterületet hoz létre a földrajzi helyen, és az ügynököt a munkaterülethez kapcsolja. A munkaterület és az erőforráscsoport elnevezési konvenciója a következő:<br>
   **Munkaterület: DefaultWorkspace-[subscription-ID]-[geo]<br> Erőforráscsoport: DefaultResourceGroup-[geo]**

   Ha egy előfizetés több földrajzi helyről származó virtuális gépeket tartalmaz, majd a Security Center több munkaterületet hoz létre. Több munkaterület jön létre az adatvédelmi szabályok fenntartásához.
1. A Security Center automatikusan engedélyezi a Security Center-megoldást a munkaterületen az előfizetéshez beállított tarifacsomag szerint. 

> [!NOTE]
> A Security Center által létrehozott munkaterületek Log Analytics-tarifacsomagja nincs hatással a Security Center számlázására. A Security Center árazása minden esetben a Security Center biztonsági szabályzat és az egyes munkaterületekre telepített megoldások alapján történik. Az ingyenes szinten a Security Center a *SecurityCenterFree* megoldást teszi elérhetővé az alapértelmezett munkaterületen. A Standard szinten a Security Center a *Security* megoldást teszi elérhetővé az alapértelmezett munkaterületen.
> Az adatok log analytics-ben való tárolása további díjakat vonhat maga után az adattárolásért. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/security-center/) olvasható.

A meglévő naplóelemzési fiókokról további információt a [Meglévő naplóelemzési ügyfelek című](./faq-azure-monitor-logs.md)témakörben talál.

### <a name="using-an-existing-workspace"></a>Meglévő munkaterület használata

Ha már rendelkezik egy meglévő Log Analytics-munkaterülettel, érdemes lehet ugyanazt a munkaterületet használni.

A meglévő Log Analytics-munkaterület használatához olvasási és írási engedéllyel kell rendelkeznie a munkaterületen.

> [!NOTE]
> A meglévő munkaterületen engedélyezett megoldások lesznek alkalmazva az Azure-beli virtuális gépek, amelyek kapcsolódnak hozzá. A fizetős megoldások esetében ez további díjakat vonhat maga után. Adatvédelmi szempontból győződjön meg arról, hogy a kiválasztott munkaterület a megfelelő földrajzi régióban található.
> Az adatok naplóelemzésben való tárolása további díjakat vonhat maga után az adattárolásért. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/security-center/) olvasható.

Meglévő Log Analytics-munkaterület kiválasztása:

1. Az **Alapértelmezett munkaterület-konfiguráció**csoportban válassza **a Másik munkaterület használata**lehetőséget.

   ![Meglévő munkaterület kijelölése][2]

2. A legördülő menüben válassza ki az összegyűjtött adatok tárolására kívánt munkaterületet.

   > [!NOTE]
   > A legördülő menüben az összes előfizetés összes munkaterülete elérhető. További információt az [előfizetések közötti munkaterület kiválasztása](security-center-enable-data-collection.md#cross-subscription-workspace-selection) című témakörben talál. A munkaterület eléréséhez engedéllyel kell rendelkeznie.
   >
   >

3. Kattintson a **Mentés** gombra.
4. A **Mentés**lehetőség kiválasztása után a rendszer megkérdezi, hogy szeretné-e újrakonfigurálni azokat a figyelt virtuális gépeket, amelyek korábban egy alapértelmezett munkaterülethez csatlakoztak.

   - Válassza a **Nem** lehetőséget, ha azt szeretné, hogy az új munkaterületi beállítások csak az új virtuális gépekre vonatkozzanak. Az új munkaterület-beállítások csak az új ügynöktelepítésekre vonatkoznak; újonnan felderített virtuális gépek, amelyek nem rendelkeznek a Log Analytics-ügynök telepítve.
   - Válassza az **Igen** lehetőséget, ha azt szeretné, hogy az új munkaterületi beállítások az összes virtuális gépre vonatkozzanak. Emellett minden virtuális gép csatlakozik a Security Center létrehozott munkaterület újracsatlakozik az új cél munkaterületre.

   > [!NOTE]
   > Ha az Igen lehetőséget választja, nem törölheti a Security Center által létrehozott munkaterület(eke)t, amíg az összes virtuális gép újra nem lett csatlakoztatva az új célmunkaterülethez. Ez a művelet sikertelen, ha egy munkaterületet túl korán törölnek.
   >
   >

   - A művelet megszakításához válassza a **Mégse** lehetőséget.

     ![Meglévő munkaterület kijelölése][3]

5. Válassza ki a loganalytics-ügynök beállítására kívánt munkaterület tarifacsomagját. <br>Meglévő munkaterület használatához állítsa be a munkaterület tarifacsomagát. Ez telepítegy Security Center-megoldást a munkaterületre, ha még nincs meg.

    a.  A Biztonsági központ főmenüjében válassza **az Árképzés i & beállítások lehetőséget.**
     
    b.  Válassza ki a kívánt munkaterületet, amelyben csatlakoztatni kívánja az ügynököt.
        ![Válassza ki][7] a c munkaterületet. Állítsa be a tarifacsomagot.
        ![Tarifacsomag kiválasztása][9]
   
   >[!NOTE]
   >Ha a munkaterületen már engedélyezve van a **Security** vagy **securitycenterFree** megoldás, a díjszabás automatikusan be lesz állítva. 

## <a name="cross-subscription-workspace-selection"></a>Előfizetések közötti munkaterület kiválasztása
Amikor kiválaszt egy munkaterületet, ahol az adatokat tárolni szeretné, az összes előfizetés összes munkaterülete elérhető. A munkaterület előfizetések közötti kiválasztása lehetővé teszi, hogy különböző előfizetésekben található virtuális gépekről gyűjtsön adatokat, és az Ön által választott munkaterületen tárolja őket. Ez a beállítás akkor hasznos, ha egy központosított munkaterületet használ a szervezetben, és ezt szeretné használni a biztonsági adatok gyűjtéséhez is. A munkaterületek kezeléséről a [Munkaterület-hozzáférés kezelése című](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access)témakörben talál további információt.


## <a name="data-collection-tier"></a>Adatgyűjtési szint
Amikor az Azure Security Centerben kiválaszt egy adatgyűjtési szintet, az csak a biztonsági események Log Analytics-munkaterületen való tárolására lesz hatással. A Log Analytics-ügynök továbbra is gyűjti és elemzi az Azure Security Center fenyegetésvédelmi rendszeréhez szükséges biztonsági eseményeket, függetlenül attól, hogy a Log Analytics-munkaterületen (ha van ilyen) melyik biztonsági eseményt tárolja. Ha úgy dönt, hogy eltárolja a biztonsági eseményeket a munkaterületen, ezáltal lehetővé teszi a vizsgálatukat, keresésüket és naplózásukat. 
> [!NOTE]
> Az adatok naplóelemzésben való tárolása további díjakat vonhat maga után az adattárolásért. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/security-center/) olvasható.
> 
> Az előfizetésekhez és munkaterületekhez a megfelelő szűrési szabályzatot választhatja ki a munkaterületen tárolandó négy eseménycsoportból: 

- **Nincs** – A biztonsági események tárolásának letiltása. Ez az alapértelmezett beállítás.
- **Minimális** – Kisebb események az on-t, akik szeretnék minimalizálni az esemény mennyiségét.
- **Gyakori** – Ez olyan események készlete, amelyek a legtöbb ügyfélnek megfelelnek, és lehetővé teszik számukra a teljes naplózási nyomvonalat.
- **Minden esemény** – Azoknak az ügyfeleknek, akik meg szeretnék győződni arról, hogy minden esemény tárolásra került.


> [!NOTE]
> Ezek a biztonsági eseménykészletek csak a Security Center standard szintjén érhetők el. A Security Center tarifacsomagjaival kapcsolatos további információért lásd a [díjszabást](security-center-pricing.md).
Ezeket a készleteket úgy tervezték, hogy tipikus forgatókönyveket kezeljenek. A megvalósítás előtt értékelje, hogy melyik felel meg az igényeinek.
>
>

A **Közös** és **minimális** eseménykészletekhez tartozó események meghatározásához együttműködve az ügyfelekkel és az iparági szabványokkal együttműködve megtudtuk az egyes események szűretlen gyakoriságát és azok használatát. Ebben a folyamatban a következő irányelveket alkalmaztuk:

- **Minimális** – Győződjön meg arról, hogy ez a készlet csak azokat az eseményeket tartalmazza, amelyek sikeres behatolást jelezhetnek, és olyan fontos eseményeket, amelyek nagyon kis mennyiséggel rendelkeznek. Ez a készlet például a felhasználó sikeres és sikertelen bejelentkezési adatait tartalmazza (4624-es, 4625-ös eseményazonosító), de nem tartalmaz olyan kijelentkezést, amely fontos a naplózáshoz, de nem értelmezhető az észleléshez, és viszonylag nagy mennyiséggel rendelkezik. A készlet adatmennyiségének nagy része a bejelentkezési események és a folyamatlétrehozási esemény (4688-as azonosítójú esemény).
- **Gyakori** – Teljes felhasználói naplózási nyomvonal biztosítása ebben a készletben. Ez a készlet például felhasználói bejelentkezéseket és felhasználói kijelentkezéseket is tartalmaz (4634-es azonosítójú esemény). Olyan naplózási műveleteket is tartalmazunk, mint a biztonsági csoport módosításai, a kulcsfontosságú tartományvezérlő Kerberos-műveletei és az iparági szervezetek által ajánlott egyéb események.

A nagyon alacsony volumenű események a közös készletbe kerültek, mivel a fő motiváció, hogy az összes esemény közül azt válasszák, a hangerő csökkentése, és nem az egyes események kiszűrése.

Itt van egy teljes bontásban a biztonsági és App Locker esemény azonosítók minden készlet:

| Adatréteg | Összegyűjtött eseménymutatók |
| --- | --- |
| Minimális | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| Közös | 1,299,300,324,340,403,404,410,411,412,413,431,500,501,1100,1102,1107,1108,4608,4610,4611,4614,4622, |
| |  4624,4625,4634,4647,4648,4649,4657,4661,4662,4663,4665,4666,4667,4688,4670,4672,4673,4674,4675,4689,4697, |
| | 4700,4702,4704,4705,4716,4717,4718,4719,4720,4722,4723,4724,4725,4726,4727,4728,4729,4733,4732,4735,4737, |
| | 4738,4739,4740,4742,4744,4745,4746,4750,4751,4752,4754,4755,4756,4757,4760,4761,4762,4764,4767,4768,4771, |
| | 4774,4778,4779,4781,4793,4797,4798,4799,4800,4801,4802,4803,4825,4826,4870,4886,4887,4888,4893,4898,4902, |
| | 4904,4905,4907,4931,4932,4933,4946,4948,4956,4985,5024,5033,5059,5136,5137,5140,5145,5632,6144,6145,6272, |
| | 6273,6278,6416,6423,6424,8001,8002,8003,8004,8005,8006,8007,8222,26401,30004 |

> [!NOTE]
> - Ha csoportházirend-objektumot (GPO) használ, ajánlott engedélyezni a naplózási házirendeket A folyamatlétrehozási esemény 4688-as eseménye és a *CommandLine* mező a 4688-as eseményen belül. A 4688-as folyamatlétrehozási eseményről a Security Center [gyIK című témakörében talál](faq-data-collection-agents.md#what-happens-when-data-collection-is-enabled)további információt. Ezekről a naplózási házirendekről további információt a [Naplózási házirend ajánlásai című témakörben talál.](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations)
> -  Az [Adaptív alkalmazásvezérlők](security-center-adaptive-application.md)adatgyűjtésének engedélyezéséhez a Security Center egy helyi AppLocker-házirendet konfigurál naplózási módban, hogy az összes alkalmazás engedélyezze. Ez azt eredményezi, hogy az AppLocker olyan eseményeket hoz létre, amelyeket a Security Center gyűjt és használ ki. Fontos megjegyezni, hogy ez a házirend nem lesz konfigurálva olyan gépeken, amelyeken már van konfigurált AppLocker-házirend. 
> - Az [5156-os Azonosítójú](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=5156)Windows-szűrőplatform-esemény (Auditfiltering Platform Connection) (Auditpol /set /subcategory:"Filtering Platform Connection" /Success:Enable) engedélyezéséhez engedélyeznie kell [a naplózási szűrési platformkapcsolatot.](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection)
>

A szűrési házirend kiválasztása:
1. Az **Adatgyűjtés** lapon válassza ki a szűrési házirendet a **Biztonsági események területen.**
2. Kattintson a **Mentés** gombra.

   ![Szűrési házirend kiválasztása][5]

### <a name="automatic-provisioning-in-cases-of-a-pre-existing-agent-installation"></a>Automatikus üzembe helyezés már meglévő ügynöktelepítés esetén<a name="preexisting"></a> 

A következő használati esetek ben határozza meg, hogyan működik az automatikus üzembe építés azokban az esetekben, amikor már van egy ügynök vagy bővítmény telepítve. 

- A Log Analytics-ügynök telepítve van a számítógépen, de nem bővítményként (közvetlen ügynökként)<br>
Ha a Log Analytics-ügynök közvetlenül a virtuális gépre van telepítve (nem Azure-bővítményként), a Security Center telepíti a Log Analytics ügynök bővítményt, és frissítheti a Log Analytics-ügynököt a legújabb verzióra.
A telepített ügynök továbbra is jelentést tesz a már konfigurált munkaterület(ek)nek, továbbá jelentést tesz a Security Centerben konfigurált munkaterületnek (a Több-homing szolgáltatás windowsos gépeken támogatott).
Ha a konfigurált munkaterület egy felhasználói munkaterület (nem a Security Center alapértelmezett munkaterülete), akkor telepítenie kell a "security/"securityFree" megoldást a Security Center számára, hogy megkezdhesse az események feldolgozását a virtuális gépekről és az adott munkaterületre jelentést tevő számítógépekről.<br>
<br>
Linux-gépek esetében az ügynök több-homing még nem támogatott - ezért ha egy meglévő ügynök telepítése észlelhető, automatikus kiépítés nem történik meg, és a gép konfigurációja nem módosul.
<br>
A 2019-03-17 előtt a Security Centerbe beszállt meglévő gépek esetében, amikor egy meglévő ügynököt észlel, a Log Analytics-ügynök bővítmény nem lesz telepítve, és a gép nem lesz hatással. Ezekről a gépekről tekintse meg a "Monitoring agent health issues on your machines" ajánlást az ügynök telepítési problémáinak megoldásához ezeken a gépeken.

  
- A System Center Operations Manager ügynök telepítve van a számítógépen<br>
A Security Center telepíti a Log Analytics ügynök bővítményt egymás mellett a meglévő Operations Managerre. A meglévő Operations Manager-ügynök továbbra is a szokásos módon jelent az Operations Manager-kiszolgálónak. Vegye figyelembe, hogy az Operations Manager-ügynök és a Log Analytics-ügynök közös futásidejű kódtárak, amelyek a folyamat során a legújabb verzióra frissülnek.
Megjegyzés – Ha az Operations Manager ügynök 2012-es verziója telepítve van, **ne** kapcsolja be az automatikus kiépítést.<br>

- Egy már meglévő virtuálisgép-bővítmény van jelen<br>
    - Ha a figyelőügynök bővítményként van telepítve, a bővítmény konfigurációja lehetővé teszi, hogy csak egyetlen munkaterületre jelentsen. A Security Center nem bírálja felül a felhasználói munkaterületekkel létesített meglévő kapcsolatokat. A Security Center a már csatlakoztatott munkaterületen tárolja a virtuális gép biztonsági adatait, feltéve, hogy a "security" vagy a "securityFree" megoldás telepítve van. A Security Center a bővítményverziót a folyamat legújabb verziójára frissítheti.  
    - Ha meg szeretné tudni, hogy a meglévő bővítmény melyik munkaterületre küld adatokat, futtassa a tesztet a Kapcsolat ellenőrzése az Azure Security Centerrel című [témakörben.](https://blogs.technet.microsoft.com/yuridiogenes/2017/10/13/validating-connectivity-with-azure-security-center/) Másik lehetőségként megnyithatja a Log Analytics-munkaterületeket, kijelölhet egy munkaterületet, kiválaszthatja a virtuális gép kiválasztását, és megnézheti a Log Analytics-ügynök-kapcsolatot. 
    - Ha olyan környezettel rendelkezik, amelyben a Log Analytics-ügynök telepítve van az ügyfélmunkaállomásokon, és egy meglévő Log Analytics-munkaterületnek jelentést készít, tekintse át az [Azure Security Center által támogatott operációs rendszerek](security-center-os-coverage.md) listáját, és győződjön meg arról, hogy az operációs rendszer támogatott. További információ: [Meglévő naplóelemzési ügyfelek.](./faq-azure-monitor-logs.md)
 
### <a name="turn-off-automatic-provisioning"></a>Az automatikus kiépítés kikapcsolása<a name="offprovisioning"></a>
Az erőforrásokautomatikus kiépítést bármikor kikapcsolhatja, ha a biztonsági házirendben kikapcsolja ezt a beállítást. 


1. Térjen vissza a Biztonsági központ főmenüjéhez, és válassza a Biztonsági házirend lehetőséget.
2. Kattintson a **Beállítások szerkesztése** elemre annak az előfizetésnek a sorában, amelynek automatikus kiépítést szeretne letiltani.
3. A **Biztonsági házirend – Adatgyűjtés** panelen az **Automatikus kiépítés** csoportban válassza a **Ki**lehetőséget.
4. Kattintson a **Mentés** gombra.

   ![Automatikus kiépítés letiltása][6]

Ha az automatikus kiépítés le van tiltva (ki van kapcsolva), az alapértelmezett munkaterület-konfigurációs szakasz nem jelenik meg.

Ha a korábbi bekapcsolás után kapcsolja ki az automatikus kiépítést:
-   Az ügynökök nem lesznek kiépítve az új virtuális gépeken.
-   A Security Center leállítja az adatok gyűjtését az alapértelmezett munkaterületről.
 
> [!NOTE]
>  Az automatikus kiépítés letiltása nem távolítja el a Log Analytics-ügynököt az Azure-beli virtuális gépekről, ahol az ügynök ki van építve. Az OMS-bővítmény eltávolításáról a [Security Center által telepített OMS-bővítmények eltávolítása](faq-data-collection-agents.md#remove-oms)című témakörben talál további információt.
>
    
## <a name="manual-agent-provisioning"></a>Manuális ügynök-kiépítés<a name="manual-agent"></a>
 
A Log Analytics-ügynök manuális annektív telepítése többféleképpen is telepíthető. Manuális telepítés esetén győződjön meg arról, hogy letiltja az automatikus kiépítést.

### <a name="operations-management-suite-vm-extension-deployment"></a>Az Operations Management Suite virtuálisgép-bővítményének telepítése 

Manuálisan telepítheti a Log Analytics-ügynököt, így a Security Center biztonsági adatokat gyűjthet a virtuális gépekről, és javaslatokat és riasztásokat adhat meg.
1. Válassza az Automatikus kiépítés – KI lehetőséget.
2. Hozzon létre egy munkaterületet, és állítsa be a Log Analytics-ügynök beállítására kívánt munkaterület tarifacsomagját:

   a.  A Biztonsági központ főmenüjében válassza a **Biztonsági házirend lehetőséget.**
     
   b.  Jelölje ki azt a munkaterületet, amelyben az ügynököt csatlakoztatni kívánja. Győződjön meg arról, hogy a munkaterület ugyanabban az előfizetésben van, amelyet a Security Centerben használ, és rendelkezik olvasási/írási engedélyekkel a munkaterületen.
       ![Munkaterület kiválasztása][8]
3. Állítsa be a tarifacsomagot.
   ![Tarifacsomag kiválasztása][9] 
   >[!NOTE]
   >Ha a munkaterületen már engedélyezve van a **Security** vagy **securitycenterFree** megoldás, a díjszabás automatikusan be lesz állítva. 
   > 

4. Ha az ügynököket új virtuális gépeken szeretné telepíteni egy Erőforrás-kezelő sablon használatával, telepítse az OMS virtuálisgép-bővítményt:

   a.  [A Windows OMS virtuálisgép-bővítményének telepítése](../virtual-machines/extensions/oms-windows.md)
    
   b.  [Az OMS virtuálisgép-bővítmény telepítése Linuxhoz](../virtual-machines/extensions/oms-linux.md)
5. A bővítmények meglévő virtuális gépeken való üzembe helyezéséhez kövesse az [Azure virtuális gépekadatainak összegyűjtése](../azure-monitor/learn/quick-collect-azurevm.md)című útmutató utasításait.

   > [!NOTE]
   > Az **esemény- és teljesítményadatok gyűjtése** szakasz nem kötelező.
   >
6. A PowerShell használatával a bővítmény üzembe helyezéséhez használja a következő PowerShell-példát:
   
   [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
   
   1. Lépjen a **Log Analytics** menüre, és kattintson a Speciális **beállítások gombra.**
    
      ![Naplóelemzés beállítása][11]

   2. Másolja az értékeket a **Munkaterületazonosító** és az **Elsődleges kulcsból.**
  
      ![Értékek másolása][12]

   3. A nyilvános konfiguráció és a magánkonfiguráció feltöltése ezekkel az értékekkel:
     
           $PublicConf = @{
               "workspaceId"= "<WorkspaceID value>"
           }
 
           $PrivateConf = @{
               "workspaceKey"= "<Primary key value>"
           }

      - Windows virtuális gépre történő telepítéskor:
        
            Set-AzVMExtension -ResourceGroupName $vm.ResourceGroupName -VMName $vm.Name -Name "MicrosoftMonitoringAgent" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "MicrosoftMonitoringAgent" -TypeHandlerVersion '1.0' -Location $vm.Location -settings $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True 
    
      - Linuxos virtuális gépre történő telepítéskor:
        
            Set-AzVMExtension -ResourceGroupName $vm1.ResourceGroupName -VMName $vm1.Name -Name "OmsAgentForLinux" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "OmsAgentForLinux" -TypeHandlerVersion '1.0' -Location $vm.Location -Settingstring $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True`

> [!NOTE]
> A Security Center PowerShell használatával történő fedélzeti verziójáról az [Azure Security Center PowerShell használatával történő bevezetés automatizálása témakörben talál útmutatást.](security-center-powershell-onboarding.md)

## <a name="troubleshooting"></a>Hibaelhárítás

-   Az automatikus üzembe helyezési telepítési problémák azonosításáról az [Ügynök állapotproblémáinak figyelése](security-center-troubleshooting-guide.md#mon-agent)című témakörben található.

-  A figyelőügynök hálózati követelményeinek azonosításáról a [Figyelési ügynök hálózati követelményeinek hibaelhárítása című témakörben.](security-center-troubleshooting-guide.md#mon-network-req)
-   A manuális bevezetési problémák azonosításáról az [Operations Management Suite bevezetési problémáinak elhárítása című témakörben olvashat.](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues)

- A nem figyelt virtuális gépek és számítógépek problémáinak azonosítása:

    A virtuális gépet vagy számítógépet a Security Center nem figyeli, ha a számítógépen nem fut a Microsoft Monitoring Agent bővítmény. Előfordulhat, hogy egy számítógépen már telepítve van egy helyi ügynök, például az OMS közvetlen ügynök e vagy a System Center Operations Manager ügynök. Az ezekkel az ügynökökkel rendelkező gépek nem figyeltként vannak azonosítva, mert ezek az ügynökök nem teljes mértékben támogatottak a Security Centerben. A Security Center összes funkciójának legteljesebb kihasználása érdekében szükség van a Microsoft Monitoring Agent bővítményre.

    A Security Center automatikus kiépítésre nem képes szolgáltatásának sikeres figyelése miatt további információt az [Ügynök állapotproblémáinak figyelése](security-center-troubleshooting-guide.md#mon-agent)című témakörben talál.


## <a name="next-steps"></a>További lépések
Ez a cikk bemutatja, hogyan működik az adatgyűjtés és az automatikus kiépítés a Security Centerben. A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Azure Security Center – gyakran ismételt kérdések](faq-general.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md) – Megtudhatja, hogyan figyelheti az Azure-erőforrások állapotát.



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
