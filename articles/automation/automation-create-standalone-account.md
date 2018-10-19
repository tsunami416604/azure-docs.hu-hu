---
title: Önálló Azure Automation-fiók létrehozása
description: Ez a cikk végigvezeti a lépéseken, létrehozásához, teszteléséhez, és a egy példa egyszerű biztonsági hitelesítés használata az Azure Automationben.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 581e9d052738b8e1976a766ea6e3525ecf76d741
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49426430"
---
# <a name="create-a-standalone-azure-automation-account"></a>Önálló Azure Automation-fiók létrehozása

Ez a cikk bemutatja, hogyan hozhat létre egy Azure Automation-fiókot az Azure Portalon. A portál Automation-fiók segítségével értékelje ki és más felügyeleti megoldásokat vagy integrációs használata az Azure Log Analytics nélkül Automation ismertetése. Ezen felügyeleti megoldások hozzáadása vagy a Log Analytics szolgáltatással integrált speciális monitorozás bármikor runbook-feladatok a jövőben is.

Az Automation-fiók runbookok hitelesítheti, mivel kezeli az erőforrások Azure Resource Manager vagy a klasszikus üzemi modellben. Egy Automation-fiók erőforrások is kezelhetők a régiók és a egy adott bérlő esetében az előfizetések között.

Ha egy Automation-fiókot hoz létre az Azure Portalon, ezek a fiókok automatikusan jönnek létre:

* **Futtató fiók**. Ezt a fiókot a következő feladatokat hajtja végre:
  * Létrehoz egy egyszerű szolgáltatást az Azure Active Directoryban (Azure AD).
  * Létrehoz egy tanúsítványt.
  * Hozzárendeli az Contributor Role-Based hozzáférés-vezérlés (RBAC), amely runbookok használatával kezeli az Azure Resource Manager-erőforrásokat.
* **Klasszikus futtató fiók**. Ez a fiók feltölt egy felügyeleti tanúsítványt. A tanúsítvány runbookok használatával kezeli a klasszikus erőforrások.

Ezek az Ön számára létrehozott fiókokhoz gyorsan megkezdheti létrehozásának és üzembe helyezésének a runbookok az automatizálási szükségletek támogatására.

## <a name="permissions-required-to-create-an-automation-account"></a>Az Automation-fiók létrehozásához szükséges engedélyek

Létrehozni vagy frissíteni egy Automation-fiókot, és ebben a cikkben ismertetett feladatok végrehajtásához, a következő jogosultságokkal és engedélyekkel kell rendelkeznie:

* Az Automation-fiók létrehozása az Azure AD-felhasználói fiókot hozzá kell adni egy szerepkörhöz a tulajdonosi szerepkörével egyenértékű engedélyekkel **Microsoft. Automation** erőforrásokat. További információkért lásd: [szerepköralapú hozzáférés-vezérlés az Azure Automationben](automation-role-based-access-control.md).
* Az Azure Portalon alatt **Azure Active Directory** > **kezelés** > **alkalmazásregisztrációk**, ha **alkalmazásregisztrációk**  értékre van állítva **Igen**, az Azure AD-bérlő nem rendszergazda jogosultságú felhasználói is [Active Directory-alkalmazások regisztrálását](../azure-resource-manager/resource-group-create-service-principal-portal.md#check-azure-subscription-permissions). Ha **alkalmazásregisztrációk** értékre van állítva **nem**, ezt a műveletet végző felhasználót az Azure AD globális rendszergazdának kell lennie.

Ha nem tagja az előfizetéshez tartozó Active Directory-példánynak, mielőtt hozzáadják az előfizetés globális rendszergazdai vagy coadministrator szerepkörhöz, akkor lesz hozzáadva az Active Directory vendégként. Ebben a forgatókönyvben a következő üzenet jelenik meg a **Automation-fiók hozzáadása** lap: "Nincs engedélye létrehozni."

Ha egy felhasználó hozzá van rendelve a globális rendszergazdai vagy coadministrator szerepkör először, eltávolíthatja őket az előfizetéshez tartozó Active Directory-példányból, és majd szerepkörtagok őket az Active Directoryban a teljes felhasználói szerepkörhöz.

Annak ellenőrzése, felhasználói szerepkörök:

1. Az Azure Portalon nyissa meg a **Azure Active Directory** ablaktáblán.
1. Válassza a **Felhasználók és csoportok** elemet.
1. Válassza ki **minden felhasználó**.
1. Miután kiválasztott egy adott felhasználó, válassza ki a **profil**. Értékét a **felhasználótípus** attribútuma a felhasználói profil alapján nem lehet **vendég**.

## <a name="create-a-new-automation-account-in-the-azure-portal"></a>Hozzon létre egy új Automation-fiókot az Azure Portalon

Az Azure Portalon egy Azure Automation-fiók létrehozásához hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be egy olyan fiókkal, amely tagja az előfizetés-Adminisztrátorok szerepkörnek, és a egy coadministrator az előfizetés az Azure Portalon.
1. Válassza ki **+ erőforrás létrehozása**.
1. Keresse meg **Automation**. A keresési eredmények között, válassza ki a **Automation**.

   ![Keresse meg és az Azure Marketplace automatizálás és vezérlés kiválasztása](media/automation-create-standalone-account/automation-marketplace-select-create-automationacct.png)

1. A következő képernyőn válassza ki a **létrehozás**.

  ![Automation-fiók hozzáadása](media/automation-create-standalone-account/automation-create-automationacct-properties.png)

  > [!NOTE]
  > Ha az alábbi üzenet jelenik meg a **Automation-fiók hozzáadása** ablaktáblán, a fiók nem tagja az előfizetés-Adminisztrátorok szerepkörnek, és a egy coadministrator az előfizetés.
  >
  > ![Automation-fiók figyelmeztetés hozzáadása](media/automation-create-standalone-account/create-account-without-perms.png)

1. Az a **Automation-fiók hozzáadása** ablaktáblán, a a **neve** adjon meg egy nevet az új Automation-fiók. Ez a név nem módosítható, miután akkor kell kiválasztani. *Automation-fióknevek minden régióban és erőforráscsoportban egyedi.*
1. Ha több előfizetéssel rendelkezik, a a **előfizetés** mezőben adja meg az új fiókhoz használni kívánt előfizetést.
1. A **erőforráscsoport**, adja meg, vagy válasszon ki egy új vagy meglévő erőforráscsoportot.
1. A **hely**, válasszon ki egy Azure-adatközpontot.
1. Az a **létrehozása Azure-beli futtató fiók** lehetőséget, győződjön meg arról, hogy **Igen** kiválasztott, és adja meg a **létrehozás**.

  > [!NOTE]
  > Ha nem kíván létrehozni a futtató fiók kiválasztásával **nem** a **létrehozása Azure-beli futtató fiók**, egy üzenet jelenik meg a **Automation-fiók hozzáadása** ablaktáblán. Bár a fiók létrehozása az Azure Portalon, a fióknak nincs megfelelő hitelesítési identitást a klasszikus üzemi modell előfizetés vagy az Azure Resource Manager-előfizetés címtárszolgáltatásában. Ezért az Automation-fiók nem rendelkezik az erőforrásokhoz való hozzáférés az előfizetésben. Ez megakadályozza, hogy a hitelesítéshez és az ezekben az üzemi modellekben található erőforrások feladatokat végezzen el erre a fiókra hivatkozó forgatókönyveket.
  >
  > ![Automation-fiók figyelmeztetés hozzáadása](media/automation-create-standalone-account/create-account-decline-create-runas-msg.png)
  >
  > Ha nem hozott létre szolgáltatásnevet, a közreműködő szerepkör nincs hozzárendelve.
  >

1. Válassza ki az Automation-fiók létrehozása, a menüben az előrehaladását úgy követheti nyomon a **értesítések**.

### <a name="resources-included"></a>Érintett erőforrások

Ha befejeződött az Automation-fiók létrehozása, számos erőforrás automatikusan létrejön. A létrehozás után ezek a runbookok biztonságosan törölhető, ha nem szeretné tartani őket. A futtató fiókok, a runbook-fiókjába való hitelesítéséhez használható, és kell hagyni, ha nem hoz létre egy másik, vagy nincs szükség rájuk. Az alábbi táblázat a futtató fiókhoz kapcsolódó erőforrásokat foglalja össze.

| Erőforrás | Leírás |
| --- | --- |
| AzureAutomationTutorial forgatókönyv |Grafikus mintarunbook, amely bemutatja, hogyan kell a futtató fiókkal hitelesítést végezni. A runbook lekéri az összes Resource Manager-erőforrásokat. |
| AzureAutomationTutorialScript forgatókönyv |PowerShell-mintarunbook, amely bemutatja, hogyan kell a futtató fiókkal hitelesítést végezni. A runbook lekéri az összes Resource Manager-erőforrásokat. |
| AzureAutomationTutorialPython2 forgatókönyv |Python-mintaforgatókönyv, amely bemutatja, hogyan kell a futtató fiókkal hitelesítést végezni. A runbook összes erőforráscsoport az előfizetésben található sorolja fel. |
| AzureRunAsCertificate |Tanúsítványobjektum, amely automatikusan létrejön az Automation-fiók létrehozásakor, vagy egy meglévő fiókon a PowerShell-parancsfájl használatával. A tanúsítvány hitelesíti az Azure-ral, így az Azure Resource Manager-erőforrások is kezelhetők a runbookokból. Ennek a tanúsítványnak egy éves időtartama van. |
| AzureRunAsConnection |A kapcsolatobjektum, amely automatikusan létrejön az Automation-fiók létrehozásakor, vagy egy meglévő fiókon a PowerShell-parancsfájl használatával. |

Az alábbi táblázat a klasszikus futtató fiókhoz kapcsolódó erőforrásokat foglalja össze.

| Erőforrás | Leírás |
| --- | --- |
| AzureClassicAutomationTutorial forgatókönyv |Grafikus mintarunbook. A runbook összes klasszikus virtuális gép lekéri az előfizetéshez a klasszikus futtató fiók (tanúsítvány) segítségével. Majd megjeleníti a virtuális gépek nevét és állapotát. |
| AzureClassicAutomationTutorial parancsprogram-forgatókönyv |PowerShell-mintarunbook. A runbook összes klasszikus virtuális gép lekéri az előfizetéshez a klasszikus futtató fiók (tanúsítvány) segítségével. Majd megjeleníti a virtuális gépek nevét és állapotát. |
| AzureClassicRunAsCertificate |Tanúsítványobjektum, amely automatikusan létrejön. A tanúsítvány hitelesíti az Azure-ral, így a klasszikus Azure-erőforrások kezelhetők runbookokkal. Ennek a tanúsítványnak egy éves időtartama van. |
| AzureClassicRunAsConnection |A kapcsolatobjektum, amely automatikusan létrejön. Az eszköz hitelesíti az Azure-ral, így a klasszikus Azure-erőforrások kezelhetők runbookokkal. |

## <a name="next-steps"></a>További lépések

* További információk a grafikus létrehozásról, lásd: [grafikus létrehozás az Azure Automationben](automation-graphical-authoring-intro.md).
* A PowerShell-forgatókönyvek használatának megismeréséhez tekintse meg a következőt: [Az első PowerShell-runbookom](automation-first-runbook-textual-powershell.md).
* A PowerShell-alapú munkafolyamat-runbookok első lépéseit [Az első PowerShell-alapú munkafolyamat-runbookom](automation-first-runbook-textual.md) című témakör ismerteti.
* A Python2-forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első Python2-forgatókönyvem](automation-first-runbook-textual-python2.md).
