---
title: "Önálló Azure Automation-fiók létrehozása | Microsoft Docs"
description: "Az oktatóprogram végigvezeti az egyszerű biztonsági hitelesítés létrehozásán, tesztelésén és használatán az Azure Automationben."
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: 
ms.assetid: 2f783441-15c7-4ea0-ba27-d7daa39b1dd3
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/18/2017
ms.author: magoedte
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: 6eadfb0c3f91c1f2c7783d70604b45d5dc9912a3
ms.contentlocale: hu-hu
ms.lasthandoff: 08/19/2017

---

# <a name="create-a-standalone-azure-automation-account"></a>Önálló Azure Automation-fiók létrehozása
Ez a témakör bemutatja, hogyan hozhat létre Automation-fiókot az Azure Portalról, ha szeretné kipróbálni és megismerni az Azure Automationt anélkül, hogy más felügyeleti megoldásokat vagy OMS Log Analytics-integrációt venne igénybe a runbook-feladatok hatékonyabb figyeléséhez.  A felügyeleti megoldások, illetve a Log Analytics-integráció a későbbiekben bármikor igénybe vehetőek.  Az Automation-fiókkal hitelesítheti az Azure Resource Managerben vagy a klasszikus Azure üzemelő példányokon lévő erőforrásokat kezelő runbookokat.

Amikor létrehoz egy Automation-fiókot az Azure Portalon, a következők is létrejönnek:

* Egy futtató fiókot, amely létrehoz egy új egyszerű szolgáltatást az Azure Active Directoryban, létrehoz egy tanúsítványt, valamint kiosztja a Közreműködő szerepköralapú hozzáférés-vezérlést (RBAC), amelynek használatával a Resource Manager-erőforrások kezelhetők runbookokkal.   
* Egy klasszikus futtató fiókot egy felügyeleti tanúsítvány feltöltésével, amely segítségével a klasszikus erőforrások kezelhetők runbookokkal.  

Ez leegyszerűsíti a folyamatot, és segít a tervezés gyors megkezdésében, és forgatókönyvek üzembe helyezésében az automatizálási szükségletek támogatására.  

## <a name="permissions-required-to-create-automation-account"></a>Az Automation-fiók létrehozásához szükséges engedélyek
Automation-fiók létrehozásához vagy frissítéséhez az alábbi, a jelen témakör végrehajtásához szükséges jogosultságokkal és engedélyekkel kell rendelkeznie.   
 
* Automation-fiók létrehozásához az AD-felhasználói fiókot a [Szerepköralapú hozzáférés-vezérlés az Azure Automationben](automation-role-based-access-control.md) című cikkben ismertetett módon egy olyan szerepkörhöz kell hozzáadni, amely a Microsoft Automation-erőforrások tulajdonosi szerepkörével egyenértékű engedélyekkel rendelkezik.  
* Ha az Alkalmazásregisztrációk beállítás értéke **Igen**, az Azure AD-bérlő nem rendszergazda jogosultságú felhasználói is elvégezhetik az [AD-alkalmazások regisztrálását](../azure-resource-manager/resource-group-create-service-principal-portal.md#check-azure-subscription-permissions).  Ha az Alkalmazásregisztrációk beállítás értéke **Nem**, ezt a műveletet csak az Azure AD globális rendszergazdái hajthatják végre. 

Ha nem tagja az előfizetéshez tartozó Active Directory-példánynak, mielőtt hozzáadják Önt az előfizetés globális rendszergazdai vagy társadminisztrátori szerepköréhez, vendégként lesz hozzáadva az Active Directoryhoz. Ebben az esetben „Nincs engedélye létrehozni…” figyelmeztető üzenetet kap az **Automation-fiók hozzáadása** panelen. A globális rendszergazdai vagy társadminisztrátori szerepkörhöz hozzáadott felhasználók először eltávolíthatók az előfizetéshez tartozó Active Directory-példányból, majd újra hozzáadhatók, így teljes jogú felhasználók lehetnek az Active Directoryban. Ez a helyzet úgy ellenőrizhető, ha az Azure Portal **Azure Active Directory** panelén a **Felhasználók és csoportok** és a **Minden felhasználó** elemre kattint, majd a konkrét felhasználó kiválasztása után a **Profil** elemet választja. A felhasználók profilja alatti **Felhasználó típusa** attribútum értéke ne legyen **Guest** (vendég).

## <a name="create-a-new-automation-account-from-the-azure-portal"></a>Egy új Automation-fiók létrehozása az Azure Portalról
Az ebben a szakaszban szereplő lépések végrehajtásával létrehozhat egy Azure Automation-fiókot az Azure Portalon.    

1. Jelentkezzen be az Azure Portal webhelyre egy olyan fiókkal, amely tagja az Előfizetés-adminisztrátorok szerepkörhöz tartozó csoportnak, és emellett az előfizetés társadminisztrátorának is számít.
2. Kattintson az **Új** lehetőségre.<br><br> ![Válassza az Új lehetőséget az Azure Portalon](media/automation-offering-get-started/automation-portal-martketplacestart.png)<br>  
3. Keressen rá az **Automatizálás** kifejezésre, majd a találatok listájában válassza az **Automatizálás és vezérlés*** lehetőséget.<br><br> ![Keresse meg és válassza ki az Automationt a Marketplace-en](media/automation-create-standalone-account/automation-marketplace-select-create-automationacct.png)<br> 
3. Az Automation-fiókok panelen kattintson a **Hozzáadás** elemre.<br><br>![Automation-fiók hozzáadása](media/automation-create-standalone-account/automation-create-automationacct-properties.png)
   
   > [!NOTE]
   > Ha az **Automation-fiók hozzáadása** panelen a következő figyelmeztetés jelenik meg, annak az az oka, hogy a fiók nem tagja az Előfizetés-adminisztrátorok szerepkörhöz tartozó csoportnak, illetve nem az előfizetés társadminisztrátora.<br><br>![Az Automation-fiókhoz kapcsolódó figyelmeztetés hozzáadása](media/automation-create-standalone-account/create-account-without-perms.png)
   > 
   > 
4. Az **Automation-fiók hozzáadása** panel **Név** mezőjébe adjon meg egy nevet az új Automation-fióknak.
5. Ha több előfizetéssel rendelkezik, adja meg, hogy melyiket kívánja használni az új fiókhoz. Ezenkívül az **Erőforráscsoport** beállításnál adjon meg egy új vagy meglévő erőforráscsoportot, a **Hely** mezőben pedig válassza ki az Azure-adatközpont helyét.
6. Ellenőrizze, hogy az **Igen** érték van kiválasztva az **Azure-beli futtató fiók létrehozása** beállításnál, és kattintson a **Létrehozás** gombra.  
   
   > [!NOTE]
   > Ha a **Nem** beállítás kiválasztásával úgy dönt, hogy nem hozza létre a futtató fiókot, egy figyelmeztető üzenet jelenik meg az **Automation-fiók hozzáadása** panelen.  Ha a fiókot az Azure Portalon hozza létre, az nem kap hitelesítési identitást a klasszikus vagy Resource Manager-előfizetés címtárszolgáltatásában, így az előfizetéshez tartozó erőforrásokhoz sem fér hozzá.  Ez megakadályozza, hogy az erre a fiókra hivatkozó runbookok hitelesítést végezhessenek, illetve feladatokat futtathassanak az ezekben az üzemi modellekben található erőforrások alapján.
   > 
   > ![Az Automation-fiókhoz kapcsolódó figyelmeztetés hozzáadása](media/automation-create-standalone-account/create-account-decline-create-runas-msg.png)<br>
   > Ha nem hozott létre szolgáltatásnevet, a rendszer nem osztja ki a Közreműködő szerepkört.
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
| AzureClassicAutomationTutorial forgatókönyv |Grafikus mintarunbook, amely a klasszikus futtató fiókkal (tanúsítvánnyal) lekéri az előfizetéshez tartozó klasszikus virtuális gépeket, majd megjeleníti a virtuális gépek nevét és állapotát. |
| AzureClassicAutomationTutorial parancsprogram-forgatókönyv |PowerShell-mintarunbook, amely a klasszikus futtató fiókkal (tanúsítvánnyal) lekéri az előfizetéshez tartozó klasszikus virtuális gépeket, majd megjeleníti a virtuális gépek nevét és állapotát. |
| AzureClassicRunAsCertificate |Automatikusan létrejövő tanúsítványobjektum, amely Azure-hitelesítésre használható, így a klasszikus Azure-erőforrások is kezelhetők a forgatókönyvekből.  Ennek a tanúsítványnak egy éves időtartama van. |
| AzureClassicRunAsConnection |Automatikusan létrejövő kapcsolatobjektum, amely Azure-hitelesítésre használható, így a klasszikus Azure-erőforrások is kezelhetők a forgatókönyvekből. |


## <a name="next-steps"></a>Következő lépések
* További információk a grafikus létrehozásról: [Grafikus létrehozás az Azure Automationben](automation-graphical-authoring-intro.md).
* A PowerShell-forgatókönyvek használatának megismeréséhez tekintse meg a következőt: [Az első PowerShell-runbookom](automation-first-runbook-textual-powershell.md).
* A PowerShell-alapú munkafolyamat-runbookok első lépéseit [Az első PowerShell-alapú munkafolyamat-runbookom](automation-first-runbook-textual.md) című témakör ismerteti.
