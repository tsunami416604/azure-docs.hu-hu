---
title: "Azure-beli futtató fiók konfigurálása | Microsoft Docs"
description: "Az oktatóprogram végigvezeti az egyszerű biztonsági hitelesítés létrehozásán, tesztelésén és használatán az Azure Automationben."
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: 
keywords: "egyszerű szolgáltatásnév, setspn, azure-hitelesítés"
ms.assetid: 2f783441-15c7-4ea0-ba27-d7daa39b1dd3
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/15/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: 2c9877f84873c825f96b62b492f49d1733e6c64e
ms.openlocfilehash: 6f2a3880c6cd307282020a689ddd4e22a95c17b0
ms.lasthandoff: 03/15/2017


---
# <a name="authenticate-runbooks-with-azure-run-as-account"></a>Forgatókönyvek hitelesítése Azure-beli futtató fiókkal
Ez a témakör bemutatja, hogyan lehet Automation-fiókot konfigurálni az Azure Portal webhelyről az új futtató fiók funkció használatával, amely az Azure Resource Managerben vagy az Azure Service Managerben erőforrásokat kezelő forgatókönyvek hitelesítésére szolgál.

Amikor létrehoz egy új Automation-fiókot az Azure Portal webhelyen, a rendszer a következőket is létrehozza:

* Egy futtató fiókot, amely létrehoz egy új szolgáltatásnevet az Azure Active Directoryban, illetve egy tanúsítványt, illetve hozzárendeli a Közreműködő szerepkörön alapuló hozzáférés-vezérlést (RBAC). Ezzel a szerepkörrel felügyelhetők a Resource Manager-erőforrások forgatókönyvek segítségével.   
* Egy klasszikus futtató fiókot, úgy, hogy feltölt egy felügyeleti tanúsítványt. Ezzel felügyelhetők az Azure Service Management- és klasszikus erőforrások forgatókönyvek segítségével.  

Ez leegyszerűsíti a folyamatot, és segít a tervezés gyors megkezdésében, és forgatókönyvek üzembe helyezésében az automatizálási szükségletek támogatására.      

A futtató és klasszikus futtató fiókok segítségével a következőket teheti meg:

* Szabványos módot biztosíthat az Azure-hitelesítésre abban az esetben, ha Azure Resource Manager- vagy Azure Service Management-erőforrásokat felügyel az Azure Portal webhelyen, forgatókönyvek segítségével.  
* Automatizálhatja az Azure Alerts szolgáltatásban konfigurált globális forgatókönyvek használatát.

> [!NOTE]
> Az Automation globális forgatókönyvekhez készült Azure [Alert integrációs funkció](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) használatához szükség van egy futtató és klasszikus futtató fiókokkal konfigurált Automation-fiókra. Válasszon egy Automation-fiókot, amelyhez már tartozik futtató és klasszikus futtató fiók, vagy hozzon létre egy új fiókot.
>  

A cikkben bemutatjuk, hogyan hozhat létre Automation-fiókot az Azure Portalról, hogyan frissítheti Automation-fiókját a PowerShell-lel, hogyan kezelheti a fiók konfigurációját, illetve hogyan végezhet hitelesítést a runbookokban.

Mielőtt azonban továbblépnénk, fontos, hogy néhány dolgot tisztázzunk.

1. Az itt leírtak nincsenek hatással a korábban a klasszikus vagy a Resource Manager üzemi modell keretében már létrehozott Automation-fiókokra.  
2. Ezek a lépések kizárólag az Azure Portal webhelyen létrehozott Automation-fiókokon végezhetők el.  Ha a klasszikus portálról hoz létre fiókot, a rendszer nem fogja replikálni a futtató fiók konfigurációját.
3. Ha jelenleg rendelkezik olyan runbookokkal és objektumokkal (azaz ütemezésekkel, változókkal stb.), amelyeket előzőleg a klasszikus erőforrások felügyeletére hozott létre, és szeretné, hogy ezek a runbookok hitelesítést végezhessenek az új klasszikus futtató fiókkal, létre kell hoznia egy klasszikus futtató fiókot a futtató fiókok kezelési funkciójával, vagy frissítenie kell a meglévő fiókot az alábbi PowerShell-szkripttel.  
4. Ha az új futtató fiókkal és a klasszikus futtató Automation-fiókkal szeretne hitelesítést végezni, az alább található példakód segítségével módosítania kell meglévő forgatókönyveit.  **Vegye figyelembe**, hogy a futtató fiók Resource Manager-erőforrásokon, tanúsítványalapú szolgáltatásnévvel történő hitelesítésre, míg a klasszikus futtató fiók Service Manager-erőforrásokon, felügyeleti tanúsítvánnyal történő hitelesítésére szolgál.     

## <a name="create-a-new-automation-account-from-the-azure-portal"></a>Egy új Automation-fiók létrehozása az Azure Portalról
Az ebben a fejezetben olvasható lépések végrehajtásával létrehozhatja az új Azure Automation-fiókját az Azure Portal webhelyen.  Ezzel mind a futtató fiókot, mind a klasszikus futtató fiókot létrehozza.  

> [!NOTE]
> Ahhoz, hogy a felhasználó elvégezhesse ezeket a lépéseket, a Szolgáltatás-adminisztrátorok szerepkör tagjának kell lennie, vagy azon előfizetés társadminisztrátorának kell lennie, amely hozzáférést biztosít az előfizetéshez a felhasználónak. Ezenfelül a felhasználót fel kell venni az előfizetés alapértelmezett Active Directoryjának felhasználói közé. A fiókhoz nem szükséges kiemelt szerepkört rendelni. Azok a felhasználók, akik nem tagjai az előfizetéshez tartozó Active Directorynak, mielőtt hozzáadják őket az előfizetés társadminisztrátori szerepköréhez, vendégként lesznek hozzáadva az Active Directoryhoz, és megjelenik számukra a „Nincs engedélye létrehozni…” figyelmeztetés az **Automation-fiók hozzáadása** panelen. A társrendszergazdai szerepkörhöz hozzáadott felhasználók először eltávolíthatók az előfizetéshez tartozó Active Directoryból, és újra hozzáadhatóak, így teljes jogú felhasználók lehetnek az Active Directoryban. Ez a helyzet ellenőrizhető az Azure Portal **Azure Active Directory** panelén a **Felhasználók és csoportok** és a **Minden felhasználó** elemre kattint, majd a konkrét felhasználó kiválasztása után a **Profil** elemet választja.  A felhasználók profilja alatti **Felhasználó típusa** attribútum értéke ne legyen **Guest** (vendég).  
> 

1. Jelentkezzen be az Azure Portal webhelyre egy olyan fiókkal, amely tagja az Előfizetés-adminisztrátorok szerepkörhöz tartozó csoportnak, és emellett az előfizetés társadminisztrátorának is számít.
2. Válassza az **Automation-fiókok** elemet.
3. Az Automation-fiókok panelen kattintson a **Hozzáadás** elemre.<br>![Automation-fiók hozzáadása](media/automation-sec-configure-azure-runas-account/create-automation-account-properties-b.png)
   
   > [!NOTE]
   > Ha az **Automation-fiók hozzáadása** panelen a következő figyelmeztetés jelenik meg, annak az az oka, hogy a fiók nem tagja az Előfizetés-adminisztrátorok szerepkörhöz tartozó csoportnak, illetve nem az előfizetés társadminisztrátora.<br>![Az Automation-fiókhoz kapcsolódó figyelmeztetés hozzáadása](media/automation-sec-configure-azure-runas-account/create-account-without-perms.png)
   > 
   > 
4. Az **Automation-fiók hozzáadása** panel **Név** mezőjébe adjon meg egy nevet az új Automation-fióknak.
5. Ha egynél több előfizetéssel rendelkezik, adja meg, hogy melyiket kívánja használni az új fiókhoz. Ezenfelül az **Erőforráscsoport** beállításnál adjon meg egy új vagy meglévő erőforráscsoportot, a **Hely** mezőben pedig válassza ki az Azure-adatközpont helyét.
6. Ellenőrizze, hogy az **Igen** érték van kiválasztva az **Azure-beli futtató fiók létrehozása** beállításnál, és kattintson a **Létrehozás** gombra.  
   
   > [!NOTE]
   > Ha a **Nem** beállítás kiválasztásával úgy dönt, hogy nem hozza létre a futtató fiókot, egy figyelmeztető üzenet jelenik meg az **Automation-fiók hozzáadása** panelen.  Ha a fiókot az Azure Portalon hozza létre, az nem kap hitelesítési identitást a klasszikus vagy Resource Manager-előfizetés címtárszolgáltatásában, így az előfizetéshez tartozó erőforrásokhoz sem fér hozzá.  Ez megakadályozza, hogy az erre a fiókra hivatkozó forgatókönyvek hitelesítést végezhessenek, illetve feladatokat futtathassanak az ezekben az üzemi modellekben található erőforrások alapján.
   > 
   > ![Az Automation-fiókhoz kapcsolódó figyelmeztetés hozzáadása](media/automation-sec-configure-azure-runas-account/create-account-decline-create-runas-msg.png)<br>
   > Ha nem hozza létre a szolgáltatásnevet, a rendszer nem osztja ki a Közreműködő szerepkört.
   > 

7. Amíg az Azure létrehozza az Automation-fiókot, a menü **Értesítések** részén nyomon követheti a folyamat állapotát.

### <a name="resources-included"></a>Érintett erőforrások
Ha befejeződött az Automation-fiók létrehozása, számos erőforrás automatikusan létrejön.  Az alábbi táblázat a futtató fiókhoz kapcsolódó erőforrásokat foglalja össze.<br>

| Erőforrás | Leírás |
| --- | --- |
| AzureAutomationTutorial forgatókönyv |Grafikus mintarunbook, amely bemutatja, hogyan kell a futtató fiókkal hitelesítést végezni, és megjeleníti az összes Resource Manager-alapú erőforrást. |
| AzureAutomationTutorialScript forgatókönyv |PowerShell-mintaforgatókönyv, amely bemutatja, hogyan kell a futtató fiók használatával hitelesíteni, és megjeleníti az összes Resource Manager-erőforrást. |
| AzureRunAsCertificate |Tanúsítványobjektum, amely automatikusan létrejön, amikor Automation-fiókot hoz létre, vagy lefuttatja egy meglévő fiókon az alább található PowerShell-parancsprogramot.  Lehetővé teszi az Azure-hitelesítést, így a forgatókönyvekből is kezelheti az Azure Resource Manager-erőforrásokat.  Ennek a tanúsítványnak egy éves időtartama van. |
| AzureRunAsConnection |Kapcsolatobjektum, amely automatikusan létrejön, amikor Automation-fiókot hoz létre, vagy lefuttatja egy meglévő fiókon az alább található PowerShell-parancsprogramot. |

Az alábbi táblázat a klasszikus futtató fiókhoz kapcsolódó erőforrásokat foglalja össze.<br>

| Erőforrás | Leírás |
| --- | --- |
| AzureClassicAutomationTutorial forgatókönyv |Grafikus mintaforgatókönyv, amely a klasszikus futtató fiók (tanúsítvány) segítségével lekéri az előfizetéshez tartozó klasszikus virtuális gépeket, majd megjeleníti a virtuális gépek nevét és állapotát. |
| AzureClassicAutomationTutorial parancsprogram-forgatókönyv |PowerShell mintaforgatókönyv, amely a klasszikus futtató fiók (tanúsítvány) segítségével lekéri az előfizetéshez tartozó klasszikus virtuális gépeket, majd megjeleníti a virtuális gépek nevét és állapotát. |
| AzureClassicRunAsCertificate |Automatikusan létrejövő tanúsítványobjektum, amely Azure-hitelesítésre használható, így a klasszikus Azure-erőforrások is kezelhetők a forgatókönyvekből.  Ennek a tanúsítványnak egy éves időtartama van. |
| AzureClassicRunAsConnection |Automatikusan létrejövő kapcsolatobjektum, amely Azure-hitelesítésre használható, így a klasszikus Azure-erőforrások is kezelhetők a forgatókönyvekből. |

## <a name="verify-run-as-authentication"></a>A futtató fiókos hitelesítés ellenőrzése
A következőkben egy rövid tesztet fogunk végrehajtani, amellyel megállapítjuk, hogy képes-e sikeres hitelesítést végezni az új futtató fiók használatával.     

1. Az Azure Portalon nyissa meg a korábban létrehozott Automation-fiókot.  
2. Kattintson a **Forgatókönyvek** csempére a forgatókönyvek listájának megnyitásához.
3. Válassza ki az **AzureAutomationTutorialScript** forgatókönyvet, majd a forgatókönyv elindításához kattintson az **Indítás** gombra.  A rendszer felkéri, hogy erősítse meg, valóban el szeretné indítani a forgatókönyvet.
4. Létrejön egy [forgatókönyv-feladat](automation-runbook-execution.md), megjelenik a Feladat panel, a feladat állapota pedig megjelenik a **Feladat összegzése** csempén.  
5. A feladat állapota kezdetben *Várólistán*, azt mutatva, hogy egy felhőben lévő forgatókönyv-feldolgozó elérhetővé válására vár. Ezután *Indítás* állapotúra változik, ha egy feldolgozó elvállalja a feladatot, majd *Fut* állapotúra, amikor a forgatókönyv elkezd futni.  
6. Ha befejeződik a forgatókönyv-feladat, normál esetben a **Befejezve** állapotnak kell megjelennie.<br> ![Rendszerbiztonsági tag forgatókönyvtesztje](media/automation-sec-configure-azure-runas-account/job-summary-automationtutorialscript.png)<br>
7. Ha szeretné megtekinteni a forgatókönyv részletes eredményeit, kattintson a **Kimenet** csempére.
8. A **Kimenet** panelen azt kell látnia, hogy a forgatókönyv sikeresen elvégezte a hitelesítést, és visszaadta az erőforráscsoportban elérhető összes erőforrás listáját.
9. Zárja be a **Kimenet** panelt, és lépjen vissza a **Feladat összegzése** panelre.
10. Zárja be a **Feladat összegzése** panelt, és a megfelelő **AzureAutomationTutorialScript** forgatókönyv paneljét.

## <a name="verify-classic-run-as-authentication"></a>A klasszikus futtató fiókos hitelesítés ellenőrzése
A következőkben egy rövid tesztet fogunk végrehajtani, amellyel megállapítjuk, hogy képes-e sikeres hitelesítést végezni az új klasszikus futtató fiók használatával.     

1. Az Azure Portalon nyissa meg a korábban létrehozott Automation-fiókot.  
2. Kattintson a **Forgatókönyvek** csempére a forgatókönyvek listájának megnyitásához.
3. Válassza ki az **AzureClassicAutomationTutorialScript** forgatókönyvet, majd a forgatókönyv elindításához kattintson az **Indítás** gombra.  A rendszer felkéri, hogy erősítse meg, valóban el szeretné indítani a forgatókönyvet.
4. Létrejön egy [forgatókönyv-feladat](automation-runbook-execution.md), megjelenik a Feladat panel, a feladat állapota pedig megjelenik a **Feladat összegzése** csempén.  
5. A feladat állapota kezdetben *Várólistán*, azt mutatva, hogy egy felhőben lévő forgatókönyv-feldolgozó elérhetővé válására vár. Ezután *Indítás* állapotúra változik, ha egy feldolgozó elvállalja a feladatot, majd *Fut* állapotúra, amikor a forgatókönyv elkezd futni.  
6. Ha befejeződik a forgatókönyv-feladat, normál esetben a **Befejezve** állapotnak kell megjelennie.<br> ![Rendszerbiztonsági tag forgatókönyvtesztje](media/automation-sec-configure-azure-runas-account/job-summary-automationclassictutorialscript.png)<br>
7. Ha szeretné megtekinteni a forgatókönyv részletes eredményeit, kattintson a **Kimenet** csempére.
8. A **Kimenet** panelen azt kell látnia, hogy a forgatókönyv sikeresen elvégezte a hitelesítést, és visszaadta az előfizetésben szereplő összes klasszikus virtuális gép listáját.
9. Zárja be a **Kimenet** panelt, és lépjen vissza a **Feladat összegzése** panelre.
10. Zárja be a **Feladat összegzése** panelt, és a megfelelő **AzureClassicAutomationTutorialScript** forgatókönyv paneljét.

## <a name="managing-azure-run-as-account"></a>Azure futtató fiókok kezelése
Az Automation-fiók élettartama során meg kell újítania a tanúsítványt mielőtt lejárna, vagy ha úgy gondolja, hogy a fiókját feltörték, törölheti a futtató fiókot, és létrehozhatja újra.  Ebben a részben ezeknek a lépéseit ismertetjük.  

### <a name="self-signed-certificate-renewal"></a>Önaláírt tanúsítvány megújítása
Az Azure futtató fiókhoz létrehozott önaláírt tanúsítványt bármikor meg lehet újítani a lejárata előtt, amely a létrehozás dátumától számítva egy év.  Ha megújítja, akkor a régi érvényes tanúsítványt megőrzi a rendszer, hogy a művelet ne érintse a várólistán lévő vagy aktívan futó runbookokat, amelyek hitelesítést végeznek a futtató fiókkal.  A tanúsítvány a lejáratáig megmarad.    

> [!NOTE]
> Ha úgy konfigurálta az Automation futtató fiókot, hogy a vállalati hitelesítésszolgáltató által kibocsátott tanúsítványt használja, és ezt a lehetőséget alkalmazza, a tanúsítvány egy önaláírt tanúsítványra lesz lecserélve.  

1. Az Azure Portalon nyissa meg az Automation-fiókot.  
2. Az Automation-fiók panelen, a fiók tulajdonságait tartalmazó ablaktáblán a **Fiókbeállítások** részben válassza a **Futtató fiókok** lehetőséget.<br><br> ![Automation-fiók tulajdonságpanelje](media/automation-sec-configure-azure-runas-account/automation-account-properties-pane.png)<br><br>
3. A **Futtató fiókok** tulajdonságpaneljén válassza azt a futtató fiókot vagy klasszikus futtató fiókot, amely esetében meg szeretné újítani a tanúsítványt, majd a kiválasztott fiók tulajdonságpaneljén kattintson a **Tanúsítvány megújítása** lehetőségre.<br><br> ![Futtató fiók tanúsítványának megújítása](media/automation-sec-configure-azure-runas-account/automation-account-renew-runas-certificate.png)<br><br> A rendszer felkéri, hogy erősítse meg, valóban folytani kívánja-e.  
4. A tanúsítvány megújítása során a menü **Értesítések** részén nyomon követheti a folyamat állapotát.

### <a name="delete-run-as-account"></a>Futtató fiók törlése
A következő lépésekkel törölhet és hozhat újra létre Azure futtató fiókot vagy klasszikus futtató fiókot.  A művelet során a rendszer megőrzi az Automation-fiókot.  A futtató fiók vagy klasszikus futtató fiók törlése után a portálon újra létrehozhatja azt.  

1. Az Azure Portalon nyissa meg az Automation-fiókot.  
2. Az Automation-fiók panelen, a fiók tulajdonságait tartalmazó ablaktáblán a **Fiókbeállítások** részben válassza a **Futtató fiókok** lehetőséget.
3. A **Futtató fiókok** tulajdonságpaneljén válassza ki azt a futtató fiókot vagy klasszikus futtató fiókot, amelyet törölni kíván, majd a kiválasztott fiók tulajdonságpaneljén kattintson a **Törlés** lehetőségre.<br><br> ![Futtató fiók törlése](media/automation-sec-configure-azure-runas-account/automation-account-delete-runas.png)<br><br>  A rendszer felkéri, hogy erősítse meg, valóban folytani kívánja-e.
4. A fiók törlése során a menü **Értesítések** részén nyomon követheti a folyamat állapotát.  A törlés után újra létrehozhatja a fiókot a **Futtató fiókok** tulajdonságpaneljéről az **Azure futtató fiók** lehetőség kiválasztásával.<br><br> ![Automation futtató fiók újbóli létrehozása](media/automation-sec-configure-azure-runas-account/automation-account-create-runas.png)<br> 

### <a name="misconfiguration"></a>Hibás konfiguráció
Ha a futtató fiók vagy klasszikus futtató fiók normál működéséhez szükséges valamelyik konfigurációs elem törlődött vagy nem hozták létre a megfelelően az első beállításkor; ilyen elemek például a következők:

* Tanúsítványobjektum 
* Kapcsolatobjektum 
* A futtató fiók el lett távolítva a közreműködői szerepkörből
* Egyszerű szolgáltatás vagy alkalmazás az Azure AD-ben

Az Automation észleli ezeket a módosításokat, és **Hiányos** állapotról küld értesítést a fiók **Futtató fiókok** tulajdonságpanelén.<br><br> ![Hiányos futtatófiók-konfiguráció állapotüzenete](media/automation-sec-configure-azure-runas-account/automation-account-runas-incomplete-config.png)<br><br>Ha kiválasztja a futtató fiókot, a következő hibaüzenet jelenik meg a fiók tulajdonságpaneljén:<br><br> ![Hiányos futtatófiók-konfigurációra figyelmeztető üzenet](media/automation-sec-configure-azure-runas-account/automation-account-runas-incomplete-config-msg.png).<br>  
Ha hibásan konfigurálta a futtató fiókot, gyorsan megoldhatja ezt a fiók törlésével és ismételt létrehozásával.   

## <a name="update-an-automation-account-using-powershell"></a>Egy Automation-fiók frissítése a PowerShell használatával
Ebben a részben leírjuk, hogyan frissítheti meglévő Automation-fiókját a PowerShell segítségével. Erre a következő esetekben lehet szükség:

1. Létrehozott egy Automation-fiókot, de futtató fiókot nem. 
2. Már rendelkezik egy, a Resource Manager-erőforrások felügyeletére szolgáló Automation-fiókkal, de szeretné azt frissíteni, hogy forgatókönyves hitelesítést lehetővé tevő futtató fiókot is tartalmazzon.
4. Már rendelkezik egy, a klasszikus erőforrások felügyeletére szolgáló Automation-fiókkal, de szeretné azt frissíteni, és klasszikus futtató fiókként használni, hogy megtakarítsa az új fiók létrehozásához és a forgatókönyvek és objektumok áthelyezéséhez szükséges időt.   
5. Egy Azure-beli futtató és egy klasszikus Azure-beli futtató fiókot szeretne létrehozni a vállalati hitelesítésszolgáltató által kibocsátott tanúsítvány használatával

A szkriptre a következő előfeltételek vonatkoznak:

1. Ez a szkript kizárólag Windows 10 és Azure Resource Manager 2.01 vagy újabb modulokkal rendelkező Windows Server 2016 esetében támogatja a futtatást.  A korábbi Windows-verziók esetében nem támogatott.  
2. Azure PowerShell 1.0 és újabb verziója. Ezen kiadásról és annak telepítéséről további információkért lásd: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azureps-cmdlets-docs).
3. Létrehozott egy Automation-fiókot.  A fiókra az –AutomationAccountName és -ApplicationDisplayName paraméterek értékeként hivatkozik az alábbi parancsprogram.

A parancsprogram végrehajtásához feltétlenül szükséges *SubscriptionID*, *ResourceGroup* és *AutomationAccountName* paraméterek értékének beszerzéséhez lépjen be az Azure Portalra, válassza ki Automation-fiókját az **Automation-fiók** panelen, majd válassza a **Minden beállítás** elemet.  A **Minden beállítás** panel **Fiókbeállítások** részénél válassza a **Tulajdonságok** lehetőséget.  A **Tulajdonságok** panelen megtalálja a keresett értékeket.<br><br> ![Automation-fiók tulajdonságai](media/automation-sec-configure-azure-runas-account/automation-account-properties.png)  

### <a name="create-run-as-account-powershell-script"></a>Futtató fiókhoz használható PowerShell-parancsprogram létrehozása
Ez a PowerShell-szkript a következő konfigurációk támogatását tartalmazza: 

* Azure-beli futtató fiók létrehozása önaláírt tanúsítvány használatával
* Azure-beli futtató fiók és klasszikus Azure-beli futtató fiók létrehozása önaláírt tanúsítvány használatával
* Azure-beli futtató fiók és klasszikus Azure-beli futtató fiók létrehozása vállalati tanúsítvány használatával
* Azure-beli futtató fiók és klasszikus Azure-beli futtató fiók létrehozása önaláírt tanúsítvány használatával az Azure Government Cloudban

A szkript a következőket hozza létre a kiválasztott konfigurációtól függően:

* Egy Azure AD alkalmazást, amelyet az önaláírt vagy vállalati tanúsítvány hitelesít. Hozzon létre egy egyszerű szolgáltatásfiókot ehhez az alkalmazáshoz az Azure AD-ben, és rendelje hozzá a Közreműködő szerepkört (ezt módosíthatja Tulajdonosra vagy bármelyik másik szerepkörre) ehhez a fiókhoz a jelenlegi előfizetésében.  További információkért tekintse át [Az Azure Automation szerepköralapú hozzáférés-vezérlése](automation-role-based-access-control.md) című cikket.
* Egy **AzureRunAsCertificate** nevű Automation-tanúsítványobjektum a megadott Automation-fiókban. Ez tartalmazza az Azure AD-alkalmazás által használt titkos tanúsítványkulcsot.
* Egy **AzureRunAsConnection** nevű, a megadott Automation-fiókban található Automation kapcsolati eszközt, amely magában foglalja az alkalmazásazonosítót, a bérlőazonosítót, az előfizetés-azonosítót és a tanúsítvány ujjlenyomatát.    

Klasszikus futtatófiók esetében:

* Egy **AzureClassicRunAsCertificate** nevű Automation-tanúsítványobjektum a megadott Automation-fiókban. Ez tartalmazza a felügyeleti tanúsítvány által használt titkos tanúsítványkulcsot.  
* Egy **AzureClassicRunAsConnection** nevű Automation-kapcsolatobjektum a megadott Automation-fiókban. Ez tartalmazza az előfizetés nevét, a subscriptionId paramétert, valamint a tanúsítványobjektum nevét.

Ha klasszikus futtató fiók létrehozását választja, a szkript végrehajtása után fel kell töltenie a nyilvános tanúsítványt (.cer formátumban) annak az előfizetésnek a felügyeleti tárába, amelyben az Automation-fiókot létrehozták. Az alábbi lépésekből megtudhatja, hogyan hajtsa végre a parancsprogramot, és hogyan töltse fel a tanúsítványt.    

1. Mentse el a következő parancsprogramot a számítógépén.  Ebben a példában mentse a következő fájlnéven: **New-RunAsAccount.ps1**.  
   
         #Requires -RunAsAdministrator
         Param (
        [Parameter(Mandatory=$true)]
        [String] $ResourceGroup,

        [Parameter(Mandatory=$true)]
        [String] $AutomationAccountName,

        [Parameter(Mandatory=$true)]
        [String] $ApplicationDisplayName,

        [Parameter(Mandatory=$true)]
        [String] $SubscriptionId,

        [Parameter(Mandatory=$true)]
        [Boolean] $CreateClassicRunAsAccount,

        [Parameter(Mandatory=$true)]
        [String] $SelfSignedCertPlainPassword,
 
        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPathForRunAsAccount,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPlainPasswordForRunAsAccount,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPathForClassicRunAsAccount,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPlainPasswordForClassicRunAsAccount,

        [Parameter(Mandatory=$false)]
        [ValidateSet("AzureCloud","AzureUSGovernment")]
        [string]$EnvironmentName="AzureCloud",

        [Parameter(Mandatory=$false)]
        [int] $SelfSignedCertNoOfMonthsUntilExpired = 12
        )

        function CreateSelfSignedCertificate([string] $keyVaultName, [string] $certificateName, [string] $selfSignedCertPlainPassword, 
                                      [string] $certPath, [string] $certPathCer, [string] $selfSignedCertNoOfMonthsUntilExpired ) {
        $Cert = New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation cert:\LocalMachine\My `
           -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
           -NotAfter (Get-Date).AddMonths($selfSignedCertNoOfMonthsUntilExpired)

        $CertPassword = ConvertTo-SecureString $selfSignedCertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPath -Password $CertPassword -Force | Write-Verbose
        Export-Certificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPathCer -Type CERT | Write-Verbose 
        }

        function CreateServicePrincipal([System.Security.Cryptography.X509Certificates.X509Certificate2] $PfxCert, [string] $applicationDisplayName) {  
        $CurrentDate = Get-Date
        $keyValue = [System.Convert]::ToBase64String($PfxCert.GetRawCertData())
        $KeyId = (New-Guid).Guid 

        $KeyCredential = New-Object  Microsoft.Azure.Commands.Resources.Models.ActiveDirectory.PSADKeyCredential
        $KeyCredential.StartDate = $CurrentDate
        $KeyCredential.EndDate= [DateTime]$PfxCert.GetExpirationDateString()
        $KeyCredential.KeyId = $KeyId
        $KeyCredential.CertValue  = $keyValue

        # Use Key credentials and create AAD Application
        $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $KeyId) -KeyCredentials $KeyCredential
        $ServicePrincipal = New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId 
        $GetServicePrincipal = Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id
   
        # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
        Sleep -s 15
        $NewRole = New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
        $Retries = 0;
        While ($NewRole -eq $null -and $Retries -le 6)
        {
           Sleep -s 10
           New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
           $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
           $Retries++;
        }
           return $Application.ApplicationId.ToString();
        }

        function CreateAutomationCertificateAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $certifcateAssetName,[string] $certPath, [string] $certPlainPassword, [Boolean] $Exportable) {
        $CertPassword = ConvertTo-SecureString $certPlainPassword -AsPlainText -Force   
        Remove-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $certifcateAssetName -ErrorAction SilentlyContinue
        New-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Path $certPath -Name $certifcateAssetName -Password $CertPassword -Exportable:$Exportable  | write-verbose
        }

        function CreateAutomationConnectionAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $connectionAssetName, [string] $connectionTypeName, [System.Collections.Hashtable] $connectionFieldValues ) {
        Remove-AzureRmAutomationConnection -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -Force -ErrorAction SilentlyContinue
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -ConnectionTypeName $connectionTypeName -ConnectionFieldValues $connectionFieldValues 
        }

        Import-Module AzureRM.Profile
        Import-Module AzureRM.Resources

        $AzureRMProfileVersion= (Get-Module AzureRM.Profile).Version
        if (!(($AzureRMProfileVersion.Major -ge 2 -and $AzureRMProfileVersion.Minor -ge 1) -or ($AzureRMProfileVersion.Major -gt 2)))
        {
           Write-Error -Message "Please install the latest Azure PowerShell and retry. Relevant doc url : https://docs.microsoft.com/powershell/azureps-cmdlets-docs/ "
           return
        }
 
        Login-AzureRmAccount -EnvironmentName $EnvironmentName
        $Subscription = Select-AzureRmSubscription -SubscriptionId $SubscriptionId

        # Create Run As Account using Service Principal
        $CertifcateAssetName = "AzureRunAsCertificate"
        $ConnectionAssetName = "AzureRunAsConnection"
        $ConnectionTypeName = "AzureServicePrincipal"
 
        if ($EnterpriseCertPathForRunAsAccount -and $EnterpriseCertPlainPasswordForRunAsAccount) {
        $PfxCertPathForRunAsAccount = $EnterpriseCertPathForRunAsAccount
        $PfxCertPlainPasswordForRunAsAccount = $EnterpriseCertPlainPasswordForRunAsAccount
        } else {
          $CertificateName = $AutomationAccountName+$CertifcateAssetName
          $PfxCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".pfx")
          $PfxCertPlainPasswordForRunAsAccount = $SelfSignedCertPlainPassword
          $CerCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".cer")
          CreateSelfSignedCertificate $KeyVaultName $CertificateName $PfxCertPlainPasswordForRunAsAccount $PfxCertPathForRunAsAccount $CerCertPathForRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired 
        }

        # Create Service Principal
        $PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPathForRunAsAccount, $PfxCertPlainPasswordForRunAsAccount)
        $ApplicationId=CreateServicePrincipal $PfxCert $ApplicationDisplayName

        # Create the automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $CertifcateAssetName $PfxCertPathForRunAsAccount $PfxCertPlainPasswordForRunAsAccount $true

        # Populate the ConnectionFieldValues
        $SubscriptionInfo = Get-AzureRmSubscription -SubscriptionId $SubscriptionId
        $TenantID = $SubscriptionInfo | Select TenantId -First 1
        $Thumbprint = $PfxCert.Thumbprint
        $ConnectionFieldValues = @{"ApplicationId" = $ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Thumbprint; "SubscriptionId" = $SubscriptionId} 

        # Create a Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ConnectionAssetName $ConnectionTypeName $ConnectionFieldValues

        if ($CreateClassicRunAsAccount) {
            # Create Run As Account using Service Principal
            $ClassicRunAsAccountCertifcateAssetName = "AzureClassicRunAsCertificate"
            $ClassicRunAsAccountConnectionAssetName = "AzureClassicRunAsConnection"
            $ClassicRunAsAccountConnectionTypeName = "AzureClassicCertificate "
            $UploadMessage = "Please upload the .cer format of #CERT# to the Management store by following the steps below." + [Environment]::NewLine +
                    "Log in to the Microsoft Azure Management portal (https://manage.windowsazure.com) and select Settings -> Management Certificates." + [Environment]::NewLine +
                    "Then click Upload and upload the .cer format of #CERT#" 
 
             if ($EnterpriseCertPathForClassicRunAsAccount -and $EnterpriseCertPlainPasswordForClassicRunAsAccount ) {
             $PfxCertPathForClassicRunAsAccount = $EnterpriseCertPathForClassicRunAsAccount
             $PfxCertPlainPasswordForClassicRunAsAccount = $EnterpriseCertPlainPasswordForClassicRunAsAccount
             $UploadMessage = $UploadMessage.Replace("#CERT#", $PfxCertPathForClassicRunAsAccount)
        } else {
             $ClassicRunAsAccountCertificateName = $AutomationAccountName+$ClassicRunAsAccountCertifcateAssetName
             $PfxCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".pfx")
             $PfxCertPlainPasswordForClassicRunAsAccount = $SelfSignedCertPlainPassword
             $CerCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".cer")
             $UploadMessage = $UploadMessage.Replace("#CERT#", $CerCertPathForClassicRunAsAccount)
             CreateSelfSignedCertificate $KeyVaultName $ClassicRunAsAccountCertificateName $PfxCertPlainPasswordForClassicRunAsAccount $PfxCertPathForClassicRunAsAccount $CerCertPathForClassicRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired 
        }

        # Create the automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountCertifcateAssetName $PfxCertPathForClassicRunAsAccount $PfxCertPlainPasswordForClassicRunAsAccount $false

        # Populate the ConnectionFieldValues
        $SubscriptionName = $subscription.Subscription.SubscriptionName
        $ClassicRunAsAccountConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicRunAsAccountCertifcateAssetName}

        # Create a Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountConnectionAssetName $ClassicRunAsAccountConnectionTypeName $ClassicRunAsAccountConnectionFieldValues

        Write-Host -ForegroundColor red $UploadMessage
        }

2. A számítógépén indítsa el a **Windows PowerShell** alkalmazást a **Kezdőlap** képernyőről emelt szintű felhasználói jogokkal.
3. Az emelt szintű PowerShell parancssori rendszerhéjból lépjen a mappába, amely az 1. lépésben létrehozott szkriptet tartalmazza, majd futtassa azt a kívánt konfigurációhoz szükséges paraméterértékek beállításával.  

    **Azure-beli futtató fiók létrehozása önaláírt tanúsítvány használatával**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false` 

    **Azure-beli futtató fiók és klasszikus Azure-beli futtató fiók létrehozása önaláírt tanúsítvány használatával**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true`

    **Azure-beli futtató fiók és klasszikus Azure-beli futtató fiók létrehozása vállalati tanúsítvány használatával**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>`

    **Azure-beli futtató fiók és klasszikus Azure-beli futtató fiók létrehozása önaláírt tanúsítvány használatával az Azure Government Cloudban**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment`
 
    > [!NOTE]
    > A rendszer az Azure rendszerrel történő hitelesítést fogja kérni a parancsprogram futtatása után. Olyan fiókkal kell bejelentkeznie, amely tagja az Előfizetés-adminisztrátorok szerepkörnek, illetve az előfizetés társadminisztrátorai közé tartozik.
    > 
    > 

Miután a szkript sikeresen lefutott, a (.cer formátumú) önaláírt nyilvános tanúsítvánnyal rendelkező klasszikus futtató fiók létrehozása esetén a szkript a PowerShell-munkamenet végrehajtásához használt felhasználói profil ideiglenes mappájába hozza létre és menti a tanúsítványt: *%USERPROFILE%\AppData\Local\Temp*. Ha (.cer formátumú) vállalati nyilvános tanúsítvánnyal rendelkező klasszikus futtató fiókot hozott létre, akkor ezt a tanúsítványt kell használnia.  Kövesse a lépéseket, amelyek bemutatják, hogy hogyan [tölthet fel egy felügyeleti API-tanúsítványt](../azure-api-management-certs.md) a klasszikus Azure-portálra, majd a [mintakód](#sample-code-to-authenticate-with-service-management-resources) segítségével ellenőrizze a hitelesítő adatok konfigurációját a Service Management-erőforrásokkal.  Ha nem klasszikus futtató fiókot hozott létre, az alább látható [kódminta](#sample-code-to-authenticate-with-resource-manager-resources) segítségével állítsa be a hitelesítést a Resource Manager-erőforrásokkal, valamint ellenőrizze a hitelesítő adatok konfigurációját.

## <a name="sample-code-to-authenticate-with-resource-manager-resources"></a>Mintakód a Resource Manager-erőforrásokkal történő hitelesítéshez
A Resource Manager-erőforrások forgatókönyvekkel való kezeléséhez használhatja az alábbi, az **AzureAutomationTutorialScript** mintaforgatókönyvből származó frissített mintakódot a futtató fiók használatával történő hitelesítéshez.   

    $connectionName = "AzureRunAsConnection"
    $SubId = Get-AutomationVariable -Name 'SubscriptionId'
    try
    {
       # Get the connection "AzureRunAsConnection "
       $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

       "Signing in to Azure..."
       Add-AzureRmAccount `
         -ServicePrincipal `
         -TenantId $servicePrincipalConnection.TenantId `
         -ApplicationId $servicePrincipalConnection.ApplicationId `
         -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
       "Setting context to a specific subscription"     
       Set-AzureRmContext -SubscriptionId $SubId              
    }
    catch {
        if (!$servicePrincipalConnection)
        {
           $ErrorMessage = "Connection $connectionName not found."
           throw $ErrorMessage
         } else{
            Write-Error -Message $_.Exception
            throw $_.Exception
         }
    }


A parancsprogram tartalmaz két további kódsort az előfizetési környezet hivatkozásának támogatására, így könnyedén tud több előfizetéssel is dolgozni. Egy SubscriptionId nevű változó objektum tartalmazza az előfizetés azonosítóját, és az Add-AzureRmAccount parancsmag nyilatkozata után a [Set-AzureRmContext parancsmag](https://msdn.microsoft.com/library/mt619263.aspx) a *-SubscriptionId* paraméterkészlettel nyilatkozik. Ha a változó neve túl általános, módosíthatja a változó nevét, hogy tartalmazzon egy előtagot vagy igazodjon más elnevezési konvenciókhoz, és könnyebbé tegye az azonosítását. Alternatív megoldásként a -SubscriptionId helyett használhatja a -SubscriptionName paraméterkészletet egy megfelelő változó objektummal.    

Figyelje meg, hogy a forgatókönyvben való hitelesítést biztosító parancsmag, (**Add-AzureRmAccount**) a *ServicePrincipalCertificate* paraméterkészletet használja.  Ez a szolgáltatásnév segítségével, és nem hitelesítő adatokkal végzi el a hitelesítést.  

## <a name="sample-code-to-authenticate-with-service-management-resources"></a>Mintakód a Service Manager-erőforrásokkal történő hitelesítéshez
Ha a klasszikus futtató fiókkal szeretne hitelesítést végezni a klasszikus erőforrások forgatókönyvekkel való felügyeletéhez, használhatja az alábbi, az **AzureClassicAutomationTutorialScript** mintaforgatókönyvből származó mintakódot is.

    $ConnectionAssetName = "AzureClassicRunAsConnection"
    # Get the connection
    $connection = Get-AutomationConnection -Name $connectionAssetName        

    # Authenticate to Azure with certificate
    Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
    if ($Conn -eq $null)
    {
       throw "Could not retrieve connection asset: $ConnectionAssetName. Assure that this asset exists in the Automation account."
    }

    $CertificateAssetName = $Conn.CertificateAssetName
    Write-Verbose "Getting the certificate: $CertificateAssetName" -Verbose
    $AzureCert = Get-AutomationCertificate -Name $CertificateAssetName
    if ($AzureCert -eq $null)
    {
       throw "Could not retrieve certificate asset: $CertificateAssetName. Assure that this asset exists in the Automation account."
    }

    Write-Verbose "Authenticating to Azure with certificate." -Verbose
    Set-AzureSubscription -SubscriptionName $Conn.SubscriptionName -SubscriptionId $Conn.SubscriptionID -Certificate $AzureCert
    Select-AzureSubscription -SubscriptionId $Conn.SubscriptionID

## <a name="next-steps"></a>Következő lépések
* Az Egyszerű szolgáltatásokkal kapcsolatos további információkért lásd: [Alkalmazásobjektumok és egyszerű szolgáltatási objektumok](../active-directory/active-directory-application-objects.md).
* Az Azure Automation szerepköralapú hozzáférés-vezérlési funkciójáról további információkért lásd: [Az Azure Automation szerepköralapú hozzáférés-vezérlése](automation-role-based-access-control.md).
* A tanúsítványokkal és az Azure-szolgáltatásokkal kapcsolatos részletes információkért lásd: [Certificates overview for Azure Cloud Services](../cloud-services/cloud-services-certs-create.md) (Tanúsítványok áttekintése az Azure Cloud Servicesben).


