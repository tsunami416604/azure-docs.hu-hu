---
title: "Munkaterületek kezelése az Azure Log Analyticsben és az OMS-portálon | Microsoft Docs"
description: "Az Azure Log Analytics és az OMS-portál munkaterületeinek kezeléséhez különféle adminisztratív feladatokat végezhet el a felhasználókon, fiókokon, munkaterületeken és Azure-fiókokon."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: d0e5162d-584b-428c-8e8b-4dcaa746e783
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/01/2017
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: dd09c109594e0ba86fe2f40625e765494bfc06eb
ms.openlocfilehash: 1221de9ae16022f7300510b2db67ed0849b61397
ms.lasthandoff: 03/02/2017


---
# <a name="manage-workspaces"></a>Munkaterületek kezelése

A Log Analyticshez való hozzáférés kezeléséhez a munkaterületekhez kapcsolódó különféle adminisztratív feladatokat kell végrehajtania. Ez a témakör praktikus tanácsokat és ajánlott eljárásokat tartalmaz a munkaterületek kezelésére vonatkozóan. A munkaterület lényegében egy tároló, amely a fiók adatait és egyszerű konfigurációs adatait tartalmazza. Ön vagy a szervezet más tagjai több munkaterületet is használhatnak az informatikai infrastruktúra egészéből vagy egyes részeiből begyűjtött különböző adatkészletek kezeléséhez.

Munkaterület létrehozásához az alábbiak szükségesek:

1. Azure-előfizetés.
2. Egy választott név a munkaterületnek.
3. A munkaterület társítása az előfizetéshez.
4. Földrajzi hely kiválasztása.

## <a name="determine-the-number-of-workspaces-you-need"></a>A szükséges munkaterületek számának meghatározása
A munkaterület egy Azure-erőforrás, továbbá egy tároló is, amelyben az adatok összegyűjtése, összesítése, elemzése és az Azure Portalon való megjelenítése történik.

Több munkaterület is létrehozható, hogy a felhasználók egy vagy több munkaterülethez is hozzáférhessenek. A munkaterületek számának minimalizálásával a legtöbb adat lekérdezhető és összevethető. Ez a szakasz azt ismerteti, hogy mikor lehet hasznos egynél több munkaterület létrehozása.

A munkaterületek jelenleg a következőket biztosítják:

* Egy földrajzi helyet az adattárolás számára
* A számlázáshoz szükséges részletes adatokat
* Az adatok elkülönítését
* Konfiguráció hatóköre

A fenti jellemzők alapján a következő esetekben lehet érdemes több munkaterületet létrehozni:

* Globális vállalatként az adatokat elkülönítési vagy megfelelőségi okok miatt külön régiókban kell tárolnia.
* Az Azure-t használja, és a kimenő adatátviteli díjak elkerülése érdekében ugyanabban a régióban szeretne létesíteni egy munkaterületet, amelyikben az általa kezelt Azure-erőforrás is található.
* Használat alapján szeretné meghatározni a különböző részlegekre vagy üzleti csoportokra eső költségeket. Ha külön munkaterületet hoz létre az egyes részlegek vagy üzleti csoportok számára, az Azure-számlán és a használati kimutatásban külön jelennek meg az egyes munkaterületekre vonatkozó költségek.
* Felügyelt szolgáltatást kínál, és az egyes felügyelt ügyfelek naplóelemzési adatait külön kell tárolnia a többi ügyfél adataitól.
* Több ügyfelet is kezel, és azt szeretné, hogy az egyes ügyfelek, részlegek és üzleti csoportok lássák ugyan a saját adataikat, de a másokét ne.

Ha az adatgyűjtéshez ügynököket használ, konfigurálhatja úgy az egyes ügynököket, hogy egy vagy több munkaterületnek küldjenek jelentést.

A System Center Operations Manager használata esetén az Operations Manager egyes felügyeleti csoportjai csak egy munkaterülethez csatlakoztathatók. Telepítheti a Microsoft Monitoring Agentet az Operations Manager által felügyelt számítógépekre, és beállíthatja az ügynököt úgy, hogy az Operations Managernek és egy másik Log Analytics-munkaterületnek is küldjön jelentést.

### <a name="workspace-information"></a>Munkaterület-információk

A munkaterületek adatait az Azure Portalon tekintheti meg. Az információk az OMS-portálon is elérhetők.

#### <a name="view-workspace-information-the-azure-portal"></a>Munkaterületek adatainak megtekintése az Azure Portalon

1. Ha még nem tette meg, jelentkezzen be az [Azure Portalra](https://portal.azure.com) Azure-előfizetésével.
2. A **központi** menüben kattintson a **További szolgáltatások** elemre, majd az erőforrások listájába írja be a következőt: **Log Analytics**. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Kattintson a **Log Analytics** elemre.  
    ![Azure-központ](./media/log-analytics-manage-access/hub.png)  
3. Válasszon egy munkaterületet a Log Analytics-előfizetések panelen.
4. A munkaterület paneljén a munkaterület adatai és a további információkra mutató hivatkozások szerepelnek.  
    ![munkaterület részletei](./media/log-analytics-manage-access/workspace-details.png)  


## <a name="manage-accounts-and-users"></a>Fiókok és felhasználók kezelése
Minden munkaterülethez több fiókot lehet hozzárendelni, és minden egyes felhasználói fiók (Microsoft-fiók vagy szervezeti fiók) több munkaterülethez férhet hozzá.

Az alapértelmezett beállítások szerint a munkaterület létrehozásához használt Microsoft-fiók vagy szervezeti fiók lesz a munkaterület rendszergazdája. A rendszergazda további Microsoft-fiókokat hívhat meg, illetve felhasználókat emelhet be a Azure Active Directory szolgáltatásból.

A munkaterülethez való hozzáférést két helyen lehet szabályozni:

* Az Azure szolgáltatásban szerepköralapú hozzáférés-vezérléssel szabályozható az Azure-előfizetéshez és a kapcsolódó Azure-erőforrásokhoz való hozzáférés. Ezek az engedélyek vonatkoznak a PowerShell és a REST API hozzáféréseire is.
* Az OMS-portálon csak az OMS-portál érhető el, a társított Azure-előfizetés nem.

Ahhoz, hogy meg tudja jeleníteni az adatokat a Backup és a Site Recovery megoldás csempéiben, rendszergazdai vagy társadminisztrátori jogosultság szükséges ahhoz az Azure-előfizetéshez, amelyhez a munkaterület társítva van.   

### <a name="managing-access-to-log-analytics-using-the-azure-portal"></a>A Log Analytics hozzáférésének szabályozása az Azure Portal használatával
Amennyiben Azure-engedélyekkel, például az Azure Portal felületén keresztül hozzáférést biztosít a Log Analytics munkaterülethez, az érintett felhasználók a Log Analytics portálhoz is hozzáférhetnek. A felhasználók az Azure Portalról az OMS-portálra ugorhatnak az **OMS-portál** feladatra kattintva a Log Analytics munkaterület erőforrás megtekintésekor.

Néhány dolog, amit érdemes észben tartani az Azure Portal kapcsán:

* Ez nem *szerepköralapú hozzáférés-vezérlés*. Amennyiben legalább *Olvasó* hozzáférési engedéllyel rendelkezik az Azure Portal Log Analytics munkaterületéhez, akkor az OMS-portált használva módosításokat hajthat végre. Az OMS-portál a következőket ismeri: Rendszergazda, Közreműködő és ReadOnly felhasználók. Amennyiben a bejelentkezésre használt fiók a munkaterülethez társított Azure Active Directory szolgáltatásban található, akkor Ön Rendszergazda felhasználó lesz az OMS-portálon, egyéb esetben pedig Közreműködő.
* Amikor a http://mms.microsoft.com webhelyről jelentkezik be az OMS-portálra, akkor alapértelmezés szerint a **Munkaterület kiválasztása** lista jelenik meg. Ezen kizárólag az OMS-portállal létrehozott munkaterületek jelennek meg. Az Azure-előfizetéssel elérhető munkaterületek csak akkor lesznek láthatók, ha az URL-cím részeként megad egy bérlőt. Példa:

  `mms.microsoft.com/?tenant=contoso.com` A bérlőazonosító gyakran a bejelentkezéshez használt e-mail cím utolsó része.
* Amennyiben a bejelentkezéshez használt fiók a bérlő Azure Active Directory szolgáltatáshoz tartozó fiók, akkor *Rendszergazda* lesz az OMS-portálon. Ez mindenkire igaz, aki nem felhőszolgáltatóként (CSP) jelentkezik be.  Amennyiben a fiókja nem a bérlő Azure Active Directory szolgáltatásában található, akkor Ön *Felhasználó* az OMS-portálon.
* Amennyiben közvetlenül kíván elérni egy portált, amelyhez Azure-engedélyekkel rendelkezik, akkor meg kell adni az erőforrást az URL-cím részeként. Ezt az URL-címet a PowerShell használatával is elő lehet állítani.

  Például: `(Get-AzureRmOperationalInsightsWorkspace).PortalUrl`.

  Az URL-cím az alábbihoz hasonló: `https://eus.mms.microsoft.com/?tenant=contoso.com&resource=%2fsubscriptions%2faaa5159e-dcf6-890a-a702-2d2fee51c102%2fresourcegroups%2fdb-resgroup%2fproviders%2fmicrosoft.operationalinsights%2fworkspaces%2fmydemo12`

Például felügyeleti megoldások hozzáadásához vagy eltávolításához a felhasználónak az Azure Portal használatakor egy Azure-előfizetés rendszergazdájának vagy közreműködőjének kell lennie. Emellett a felhasználó az OMS-munkaterület közreműködői csoportjának tagja vagy az OMS-portál rendszergazdája kell, hogy legyen.

### <a name="managing-users-in-the-oms-portal"></a>Az OMS-portál felhasználóinak kezelése
A felhasználók és csoportok kezelése a Beállítások oldal **Fiókok** lapjának **Felhasználók kezelése** fülére kattintva történik.   

![felhasználók kezelése](./media/log-analytics-manage-access/setup-workspace-manage-users.png)


#### <a name="add-a-user-to-an-existing-workspace"></a>Felhasználó hozzáadása meglévő munkaterülethez
A következő lépésekkel adhat hozzá egy felhasználót vagy csoportot egy munkaterülethez.

1. Az OMS-portálon kattintson a **Beállítások** csempére.
2. Kattintson a **Fiókok** fülre, majd a **Felhasználók kezelése** fülre.
3. A **Felhasználók kezelése** részen válassza ki a hozzáadni kívánt fióktípust: **Szervezeti fiók**, **Microsoft-fiók**, **Microsoft ügyfélszolgálat**.

   * Amennyiben a Microsoft-fiók lehetőséget választja, írja be Microsoft-fiókhoz tartozó felhasználó e-mail-címét.
   * Amennyiben a Szervezeti fiók lehetőséget választja, elég beírni a felhasználó vagy csoport nevének vagy e-mail-aliasának egy részét, és a legördülő menüben megjelenik a feltételeknek megfelelő felhasználók és csoportok listája. Válasszon ki egy felhasználót vagy csoportot.
   * A Microsoft ügyfélszolgálatán keresztül átmeneti hozzáférést biztosíthat a munkaterületéhez a Microsoft ügyfélszolgálat egyik szakemberének vagy más alkalmazottjának, hogy el tudja végezni a hibaelhárítást.

     > [!NOTE]
     > Az optimális teljesítmény érdekében legfeljebb három Active Directory-csoport legyen társítva egy OMS-fiókhoz. Az egyik legyen a rendszergazdák, a második a közreműködők, a harmadik pedig a csak olvasási hozzáféréssel rendelkező felhasználók csoportja. Ha ennél több csoportot használ, az befolyásolhatja a Log Analytics teljesítményét.
     >
     >
4. Válassza ki a hozzáadni kívánt felhasználó vagy csoport típusát: **Rendszergazda**, **Közreműködő** vagy **ReadOnly felhasználó**.  
5. Kattintson az **Add** (Hozzáadás) parancsra.

   Microsoft-fiók hozzáadásakor egy meghívót kap a munkaterület használatára a korábban megadott e-mail címére. Ha a felhasználó követi az OMS-meghívóban leírtakat, hozzáférhet a munkaterülethez.
   Szervezeti fiók hozzáadásakor a felhasználó azonnal hozzáférést kaphat a Log Analytics megoldáshoz.  

#### <a name="edit-an-existing-user-type"></a>Meglévő felhasználótípus hozzáadása
Az OMS-fiókjával lehetősége van módosítani egy társított felhasználó fiókjának szerepét. A következő szerepek közül választhat:

* *Rendszergazda*: Kezelheti a felhasználókat, látja a riasztásokat és reagálhat rájuk, hozzáadhat és eltávolíthat kiszolgálókat.
* *Közreműködő*: Látja a riasztásokat és reagálhat rájuk, hozzáadhat és eltávolíthat kiszolgálókat.
* *ReadOnly felhasználó*: A csak olvasási jogosultsággal rendelkező felhasználók nem tudják végrehajtani a következőket:

  1. Megoldások hozzáadása és eltávolítása. A megoldástár nem látható.
  2. Csempék hozzáadása, módosítása és eltávolítása a **Saját irányítópult** felületen.
  3. A **Beállítások** oldalak megtekintése. Ezek az oldalak rejtve maradnak.
  4. A Keresés nézetben a PowerBI konfigurálása, a Mentett keresések és a Riasztás feladatok rejtve maradnak.

#### <a name="to-edit-an-account"></a>Fiók szerkesztése
1. Az OMS-portálon kattintson a **Beállítások** csempére.
2. Kattintson a **Fiókok** fülre, majd a **Felhasználók kezelése** fülre.
3. Jelölje ki a felhasználó módosítani kívánt szerepét.
4. A megerősítési párbeszédpanelen kattintson az **Igen** gombra.

### <a name="remove-a-user-from-a-workspace"></a>Felhasználó eltávolítása a munkaterületről
A következő lépésekkel távolíthat el egy felhasználót egy munkaterületről. A felhasználó eltávolításával nem záródik be a munkaterület. Csupán eltávolítja a felhasználó és a munkaterület közötti társítást. Ha egy felhasználóhoz több munkaterület is társítva van, a felhasználó továbbra is bejelentkezhet az OMS-be, hogy megtekintse a többi munkaterületet.

1. Az OMS-portálon kattintson a **Beállítások** csempére.
2. Kattintson a **Fiókok** fülre, majd a **Felhasználók kezelése** fülre.
3. Kattintson az eltávolítani kívánt felhasználó neve mellett található **Eltávolítás** gombra.
4. A megerősítési párbeszédpanelen kattintson az **Igen** gombra.

### <a name="add-a-group-to-an-existing-workspace"></a>Csoport hozzáadása meglévő munkaterülethez
1. Járjon el a fenti „Felhasználó hozzáadása meglévő munkaterülethez” szakasz 1-4. lépése szerint.
2. A **Felhasználó vagy csoport kiválasztása** pontban válassza a **Csoport** lehetőséget.  
   ![csoport hozzáadása meglévő munkaterülethez](./media/log-analytics-manage-access/add-group.png)
3. Írja be a hozzáadni kívánt csoport megjelenítendő nevét vagy e-mail-címét.
4. Jelölje ki a csoportot a kapott listán, majd kattintson a **Hozzáadás** gombra.

## <a name="link-an-existing-workspace-to-an-azure-subscription"></a>Meglévő munkaterület csatolása Azure-előfizetéshez
Minden 2016. szeptember 26. után létrehozott munkaterület a létrehozásukkor kell társítani egy Azure-előfizetéshez. Az e dátum előtt létrehozott munkaterületeket a legközelebbi bejelentkezéskor kell társítani egy munkaterülethez. Amikor az Azure Portalon hoz létre egy munkaterületet, vagy ha munkaterületét egy Azure-előfizetéshez társítja, az Azure Active Directory a szervezeti fiókjaként lesz társítva.

### <a name="to-link-a-workspace-to-an-azure-subscription-in-the-oms-portal"></a>Munkaterület társítása egy Azure-előfizetéshez az OMS-portálon

- Amikor bejelentkezik az OMS-portálra, a rendszer felkéri egy Azure-előfizetés kiválasztására. Jelölje ki a munkaterülethez kapcsolni kívánt előfizetést, majd kattintson az **Összekapcsolás** gombra.  
    ![Azure-előfizetés társítása](./media/log-analytics-manage-access/required-link.png)

    > [!IMPORTANT]
    > Munkaterület társításához az Azure-fióknak már hozzáféréssel kell rendelkeznie a társítani kívánt munkaterülethez.  Az Azure Portal eléréséhez tehát **ugyanazt** a fiókot kell használnia, mint a munkaterület eléréséhez. Ha ez nem igaz, kövesse a [Felhasználó hozzáadása meglévő munkaterülethez](#add-a-user-to-an-existing-workspace) szakasz lépéseit.




### <a name="to-link-a-workspace-to-an-azure-subscription-in-the-azure-portal"></a>Munkaterület társítása egy Azure-előfizetéshez az Azure Portalon
1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com).
2. Keresse meg a **Log Analytics** elemet, majd jelölje ki.
3. Ekkor megjelenik a meglévő munkaterületek listája. Kattintson az **Add** (Hozzáadás) parancsra.  
   ![munkaterületek listája](./media/log-analytics-manage-access/manage-access-link-azure01.png)
4. Az **OMS-munkaterület** részen kattintson a **Vagy meglévő társítása** gombra.  
   ![meglévő társítása](./media/log-analytics-manage-access/manage-access-link-azure02.png)
5. Kattintson a **Kötelező beállítások konfigurálása** lehetőségre.  
   ![kötelező beállítások megadása](./media/log-analytics-manage-access/manage-access-link-azure03.png)
6. Megjelenik a munkaterület listája, amelyek még nincsenek társítva az Azure-fiókhoz. Jelöljön ki egy munkaterületet.  
   ![munkaterület kijelölése](./media/log-analytics-manage-access/manage-access-link-azure04.png)
7. Ha szükséges, a következő elemek értékeit módosíthatja:
   * Előfizetés
   * Erőforráscsoport
   * Hely
   * Tarifacsomag  
     ![értékek módosítása](./media/log-analytics-manage-access/manage-access-link-azure05.png)
8. Kattintson az **OK** gombra. A munkaterület az Azure-fiókhoz van társítva.

> [!NOTE]
> Amennyiben nem találja a társítani kívánt munkaterületet, akkor az Azure-előfizetése nem rendelkezik hozzáféréssel az OMS webhelyen létrehozott munkaterülethez.  További információ a fiók OMS-portálról való eléréséről: [Felhasználó hozzáadása meglévő munkaterülethez](#add-a-user-to-an-existing-workspace).
>
>

## <a name="upgrade-a-workspace-to-a-paid-plan"></a>Munkaterület frissítése fizetős díjcsomagra
Az OMS három munkaterület díjcsomagtípust ismer: **Ingyenes**, **Önálló** és **OMS**.  Amennyiben *Ingyenes* díjcsomagot használ, naponta legfeljebb 500 MB adatot küldhet a Log Analytics szolgáltatásnak.  Ha túllépi ezt a mennyiséget, munkaterületét fizetős díjcsomagra kell módosítania, hogy ennél több adatot gyűjthessen be. A díjcsomag típusa bármikor módosítható.  Az OMS díjszabásról a [díjszabás](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite-pricing) leírásában tájékozódhat.

### <a name="using-entitlements-from-an-oms-subscription"></a>Az OMS-előfizetésből származó jogosultságok használata
Ha szeretné használni az OMS E1, OMS E2 OMS vagy a System Center OMS-bővítményének megvásárlásából származó jogosultságokat, válassza az OMS Log Analytics *OMS* csomagját.

OMS-előfizetés megvásárlásakor a jogosultságok bekerülnek a Nagyvállalati szerződésbe. A megállapodás hatálya alatt létrehozott Azure-előfizetések mind rendelkeznek ezekkel a jogosultságokkal. Így például több olyan munkaterülete lehet, amely az OMS-előfizetés jogosultságát használja.

Ha biztosítani kívánja, hogy a munkaterületén érvényesek legyenek az OMS-előfizetésből származó jogosultságok, a következőket kell tennie:

1. Olyan Azure-előfizetésben hozza létre a munkaterületet, amely az OMS-előfizetést tartalmazó Nagyvállalati szerződés része
2. *OMS* adatforgalmi díjcsomag kiválasztása a munkaterület számára

> [!NOTE]
> Amennyiben munkaterületét 2016. szeptember 26. előtt hozta létre, és *Prémium* szintű Log Analytics tarifacsomagot használ, akkor ez a munkaterület a System Center OMS-bővítményéből származó jogosultságokat fogja használni. Jogosultságait az *OMS* tarifacsomagra való váltással is használhatja.
>
>

Az OMS-előfizetés jogosultságai nem jelennek meg az Azure- vagy OMS-portálon. A jogosultságokat és használatot az Enterprise Portalon tekintheti meg.  

Ha módosítania kell az Azure-előfizetést, amelyhez a munkaterületet társította, használja az Azure PowerShell [Move-AzureRmResource](https://msdn.microsoft.com/library/mt652516.aspx) parancsmagját.

### <a name="using-azure-commitment-from-an-enterprise-agreement"></a>Azure kötelezettségvállalás használata egy Nagyvállalati szerződésből
Amennyiben Ön nem rendelkezik OMS-előfizetéssel, az OMS egyes összetevőiért külön-külön kell fizetnie, és a használat megjelenik az Azure-számláján.

Ha rendelkezik Azure pénzügyi kerettel a nagyvállalati beléptetés részeként, amelyhez az Azure-előfizetést társította, akkor a Log Analytics automatikusan felhasználja az esetleg rendelkezésre álló pénzügyi keretet.

Ha módosítania kell az Azure-előfizetést, amelyhez a munkaterületet társította, használja az Azure PowerShell [Move-AzureRmResource](https://msdn.microsoft.com/library/mt652516.aspx) parancsmagját.  

### <a name="change-a-workspace-to-a-paid-pricing-tier-in-the-azure-portal"></a>Munkaterület módosítása fizetős tarifacsomagra az Azure Portalon
1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com).
2. Keresse meg a **Log Analytics** elemet, majd jelölje ki.
3. Ekkor megjelenik a meglévő munkaterületek listája. Jelöljön ki egy munkaterületet.  
4. A munkaterület panel **Általános** területén kattintson a **Tarifacsomag** elemre.  
5. A **Tarifacsomag** lapon kattintással jelölje ki a tarifacsomagot, majd kattintson a **Kiválasztás** gombra.  
    ![díjcsomag kiválasztása](./media/log-analytics-manage-access/manage-access-change-plan03.png)
6. Amikor frissíti az Azure Portal nézetét, láthatja, hogy a **Tarifacsomag** a választott csomagra módosult.  
    ![frissített csomag](./media/log-analytics-manage-access/manage-access-change-plan04.png)

> [!NOTE]
> Ha a munkaterület Automation-fiókhoz van társítva, az *Önálló (GB-alapú)* tarifacsomag kiválasztása előtt törölnie kell az összes **Automation and Control** megoldást, és meg kell szüntetnie az Automation-fiók társítását. A megoldások megtekintéséhez és törléséhez kattintson a munkaterület panel **Általános** területén a **Megoldások** elemre. Az Automation-fiók társításának megszüntetéséhez kattintson az Automation-fiók nevére a **Tarifacsomag** panelen.
>
>

## <a name="change-your-data-plan-in-the-oms-portal"></a>Az adatforgalmi díjcsomag módosítása az OMS-portálon

Ahhoz, hogy egy adatforgalmi díjcsomagot módosítani tudjon az OMS-portálon, a bejelentkezett felhasználónak már rendelkeznie kell Azure-fiókkal.

1. Az OMS-portálon kattintson a **Beállítások** csempére.
2. Kattintson a **Fiókok** fülre, majd az **Azure-előfizetés és adatforgalmi díjcsomag** fülre.
3. Kattintson a használni kívánt adatforgalmi díjcsomagra.
4. Kattintson a **Save** (Mentés) gombra.  
   ![előfizetés és adatforgalmi díjcsomagok](./media/log-analytics-manage-access/subscription-tab.png)

Az új adatforgalmi díjcsomag megjelenik az OMS-portál menüszalagján a webhely felső részén.

![OMS menüszalag](./media/log-analytics-manage-access/data-plan-changed.png)


## <a name="change-how-long-log-analytics-stores-data"></a>A Log Analytics adattárolási idejének módosítása

Az ingyenes tarifacsomagban a Log Analytics az utolsó hét nap adatait teszi elérhetővé.
A standard tarifacsomagban a Log Analytics az utolsó 30 nap adatait teszi elérhetővé.
A prémium tarifacsomagban a Log Analytics az utolsó 365 nap adatait teszi elérhetővé.
Az önálló és OMS tarifacsomagban a Log Analytics alapértelmezés szerint az utolsó 31 nap adatait teszi elérhetővé.

Az önálló és OMS tarifacsomag használata esetén akár 2 évig (730 napig) is megőrizheti az adatokat. Az alapértelmezett 31 napnál tovább tárolt adatokért adatmegőrzési díjat számolunk fel. Az árakkal kapcsolatos további információkért lásd a [többletköltségek](https://azure.microsoft.com/pricing/details/log-analytics/) című szakaszt.

Az adatmegőrzés hosszának módosítása:

1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com).
2. Keresse meg a **Log Analytics** elemet, majd jelölje ki.
3. Ekkor megjelenik a meglévő munkaterületek listája. Jelöljön ki egy munkaterületet.  
4. A munkaterület panel **Általános** területén kattintson a **Megőrzés** elemre.  
5. A csúszka segítségével növelje vagy csökkentse a megőrzési napok számát, majd kattintson a **Mentés** gombra.  
    ![adatmegőrzés módosítása](./media/log-analytics-manage-access/manage-access-change-retention01.png)

## <a name="change-an-azure-active-directory-organization-for-a-workspace"></a>Munkaterülethez tartozó Azure Active Directory-szervezet módosítása

Igény szerint módosíthatja a munkaterülethez tartozó Azure Active Directory-szervezetet. Az Azure Active Directory-szervezet módosításával felhasználókat és csoportokat adhat hozzá a munkaterülethez az adott címtárból.

### <a name="to-change-the-azure-active-directory-organization-for-a-workspace"></a>Munkaterülethez tartozó Azure Active Directory-szervezet módosítása

1. Az OMS-portál Beállítások lapján kattintson a **Fiókok** fülre, majd a **Felhasználók kezelése** fülre.  
2. Tekintse át a szervezeti fiókok adatait, majd kattintson a **Szervezet módosítása** elemre.  
    ![szervezet módosítása](./media/log-analytics-manage-access/manage-access-add-adorg01.png)
3. Adja meg Azure Active Directory-tartománya rendszergazdájának identitását. Ezután megjelenik a visszaigazolás, hogy munkaterülete társítva lett Azure Active Directory-tartományához.  
    ![társított munkaterületek visszaigazolása](./media/log-analytics-manage-access/manage-access-add-adorg02.png)


## <a name="delete-a-log-analytics-workspace"></a>Log Analytics-munkaterület törlése
Amikor töröl egy Log Analytics-munkaterületet, 30 napon belül a munkaterülethez kapcsolódó összes adat törlődik az OMS szolgáltatásból.

Ha Ön rendszergazda, és több felhasználó van társítva a munkaterülethez, a felhasználók és a munkaterület közötti társítás megszűnik. Ha a felhasználók más munkaterületekhez is társítva vannak, továbbra is használhatják az OMS-t azokkal a munkaterületekkel. Ha azonban nincsenek más munkaterületekhez társítva, az OMS használatához létre kell hozniuk egy munkaterületet.

### <a name="to-delete-a-workspace"></a>Munkaterület törlése
1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com).
2. Keresse meg a **Log Analytics** elemet, majd jelölje ki.
3. Ekkor megjelenik a meglévő munkaterületek listája. Jelölje ki a törölni kívánt munkaterületet.
4. A munkaterület paneljén kattintson a **Törlés** elemre.  
    ![törlés](./media/log-analytics-manage-access/delete-workspace01.png)
5. A munkaterület törlésének megerősítését kérő párbeszédpanelen kattintson az **Igen** gombra.

## <a name="next-steps"></a>Következő lépések
* Az ügynökök hozzáadásával és az adatgyűjtéssel kapcsolatban lásd: [Connect Windows computers to Log Analytics](log-analytics-windows-agents.md) (Windows rendszerű számítógépek csatlakoztatása a Log Analyticshez).
* A funkciók hozzáadásával és az adatgyűjtéssel kapcsolatban lásd: [Add Log Analytics solutions from the Solutions Gallery](log-analytics-add-solutions.md) (Log Analytics-megoldások hozzáadása a megoldástárból).
* Ha a szervezete proxykiszolgálót vagy tűzfalat használ annak érdekében, hogy az ügynökök kommunikálni tudjanak a Log Analytics szolgáltatással: [Configure proxy and firewall settings in Log Analytics](log-analytics-proxy-firewall.md) (Proxy- és tűzfalbeállítások konfigurálása a Log Analyticsben).

