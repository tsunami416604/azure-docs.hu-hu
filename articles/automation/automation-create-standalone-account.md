---
title: Önálló Azure Automation-fiók létrehozása
description: Ez a cikk végigvezeti a létrehozása, tesztelése és egy példa biztonsági egyszerű hitelesítés használata az Azure Automationben.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: acd1ea95f4ef2717e315d9a247cd2fed54a02bf3
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
---
# <a name="create-a-standalone-azure-automation-account"></a>Önálló Azure Automation-fiók létrehozása

Ez a cikk bemutatja, hogyan egy Azure Automation-fiók létrehozása az Azure portálon. A portál Automation-fiók segítségével értékelje ki és további automatizálásával kapcsolatos további felügyeleti megoldások vagy integráció az Azure Naplóelemzés használata nélkül. Ezen megoldások hozzáadása, vagy speciális figyelésére egyetlen runbook-feladatok a jövőben Naplóelemzési integrálható.

Az Automation-fiók a runbookok hitelesítik az Azure Resource Manager vagy a klasszikus üzembe helyezési modellel erőforrások kezelése.

Automation-fiók létrehozása az Azure portálon, ha ezek a fiókok automatikusan jönnek létre:

* **A Futtatás mint fiók**. Ezt a fiókot a következő feladatokat hajtja végre:
  * Az Azure Active Directory (Azure AD) hoz létre egy egyszerű szolgáltatást.
  * Létrehoz egy tanúsítványt.
  * A Contributor Role-Based hozzáférés-vezérlés (RBAC), amely runbookok használatával kezeli az Azure Resource Manager erőforrások rendeli.
* **Klasszikus Futtatás mint fiók**. Ez a fiók feltölt egy felügyeleti tanúsítványt. A tanúsítvány hagyományos erőforrások runbookok használatával kezeli.

Ezek a fiókok hozott létre, a létrehozása és telepítése a forgatókönyvek az automation igényeinek megfelelő gyorsan megkezdheti.

## <a name="permissions-required-to-create-an-automation-account"></a>Automation-fiók létrehozásához szükséges engedélyek

Az Automation-fiók létrehozása vagy frissítése, és a jelen cikkben ismertetett feladatok végrehajtásához, a következő jogosultságokkal és engedélyekkel kell rendelkeznie:

* Az Automation-fiók létrehozása az Azure AD-felhasználói fiókot hozzá kell adni a tulajdonosi szerepkört, a megfelelő engedélyekkel rendelkező szerepkörhöz **Microsoft. Automatizálási** erőforrásokat. További információkért lásd: [szerepköralapú hozzáférés-vezérlés az Azure Automationben](automation-role-based-access-control.md).
* Az Azure portálon a **Azure Active Directory** > **kezelése** > **App regisztrációk**, ha **App regisztrációk**  értéke **Igen**, az Azure AD-bérlő nem rendszergazdai felhasználók számára is [regisztrálni az Active Directory alkalmazások](../azure-resource-manager/resource-group-create-service-principal-portal.md#check-azure-subscription-permissions). Ha **App regisztrációk** értéke **nem**, ezt a műveletet végző felhasználót az Azure AD globális rendszergazdának kell lennie.

Ha még nem tagja az Active Directory előfizetésre, ahhoz az előfizetéshez globális rendszergazda/coadministrator szerepkör hozzáadásakor, amelyek hozzáadott Active Directory vendégként. Ebben a forgatókönyvben a következő üzenet jelenik meg a **Automation-fiók hozzáadása** lap: "Nincs létrehozásához szükséges engedélyek."

Ha egy felhasználó hozzáadódik a globális rendszergazda/coadministrator szerepkör először, akkor is távolítsa el azokat az Active Directory előfizetésre, és majd újra vegye fel a teljes Active Directory a felhasználói szerepkörhöz.

Felhasználói szerepkörök ellenőrzése:

1. Az Azure-portálon lépjen a **Azure Active Directory** ablaktáblán.
1. Válassza ki **felhasználók és csoportok**.
1. Válassza ki **minden felhasználó**.
1. Egy adott felhasználó kiválasztása után válassza ki a **profil**. Értékét a **felhasználótípust** attribútum a profil alapján nem lehet **vendég**.

## <a name="create-a-new-automation-account-in-the-azure-portal"></a>Új Automation-fiók létrehozása az Azure portálon

Egy Azure Automation-fiók létrehozása az Azure portálon, kövesse az alábbi lépéseket:

1. Jelentkezzen be egy olyan fiókkal, amely az előfizetés-Rendszergazdák szerepkör tagja, és az előfizetés egy coadministrator az Azure portálon.
1. Válassza ki **+ hozzon létre egy erőforrást**.
1. Keresse meg **Automation**. A keresési eredmények között, válassza ki a **Automation**.

   ![Jelölje ki az Automation & vezérlés az Azure piactéren](media/automation-create-standalone-account/automation-marketplace-select-create-automationacct.png)

1. A következő képernyőn válassza ki a **létrehozása**.
  ![Automation-fiók hozzáadása](media/automation-create-standalone-account/automation-create-automationacct-properties.png)

  > [!NOTE]
  > Ha a következő üzenet jelenik meg a **Automation-fiók hozzáadása** ablaktáblán, a fiók nincs az előfizetés-Rendszergazdák szerepkör tagja, és az előfizetés egy coadministrator.
  >
  > ![Adja hozzá az Automation-fiók figyelmeztetés](media/automation-create-standalone-account/create-account-without-perms.png)
  >
1. Az a **Automation-fiók hozzáadása** ablaktáblán, a a **neve** mezőbe írja be az új Automation-fiók nevét.
1. Ha egynél több előfizetésnek a a **előfizetés** adja meg az új fiókot használni kívánt előfizetést.
1. A **erőforráscsoport**adja meg vagy válasszon egy új vagy meglévő erőforráscsoportot.
1. A **hely**, adja meg az Azure-adatközpontban helyét.
1. Az a **létrehozása Azure-beli futtató fiók** lehetőségre, győződjön meg arról, hogy **Igen** kiválasztva, majd jelölje ki **létrehozása**.

  > [!NOTE]
  > Ha nem kíván létrehozni a futtató fiók kiválasztásával **nem** a **létrehozása Azure-beli futtató fiók**, egy üzenet jelenik meg a **Automation-fiók hozzáadása** ablaktáblán. Bár a fiókot az Azure portálon jön létre, a fiók nem rendelkezik a megfelelő hitelesítési identitását, a klasszikus üzembe helyezési modell előfizetés vagy az Azure Resource Manager előfizetés címtárszolgáltatásban. Ezért az Automation-fiók nem rendelkezik az erőforrásokhoz való hozzáférés az előfizetésben. Ez megakadályozza, hogy ez a fiók nem tudnak hitelesítést, és azokat üzembe helyezési modellel erőforrásokon feladatokat hivatkozó runbookokat.
  >
  > ![Adja hozzá az Automation-fiók figyelmeztetés](media/automation-create-standalone-account/create-account-decline-create-runas-msg.png)
  >
  > A szolgáltatásnév nem jön létre, amikor a közreműködői szerepkör nincs hozzárendelve.
  >

1. Az automatizálási fiók létrehozása, a menü előrehaladását úgy követheti nyomon válassza **értesítések**.

### <a name="resources-included"></a>Érintett erőforrások

Ha befejeződött az Automation-fiók létrehozása, számos erőforrás automatikusan létrejön. Létrehozása után ezek a runbookok lehetnek biztonságosan törölve, ha nem szeretné, hogy továbbra is. A futtató fiókok számára, a fiókba egy runbook hitelesítésére is használható, és ha hozzon létre egy újat kell hagyni vagy van szükség. Az alábbi táblázat a futtató fiókhoz kapcsolódó erőforrásokat foglalja össze.

| Erőforrás | Leírás |
| --- | --- |
| AzureAutomationTutorial forgatókönyv |Grafikus példaforgatókönyv, amely bemutatja, hogyan kell elvégezni a hitelesítést a Futtatás mint fiók. A runbook összes Resource Managerhez tartozó erőforrások lekérése. |
| AzureAutomationTutorialScript forgatókönyv |PowerShell példaforgatókönyv, amely bemutatja, hogyan kell elvégezni a hitelesítést a Futtatás mint fiók. A runbook összes Resource Managerhez tartozó erőforrások lekérése. |
| AzureAutomationTutorialPython2 forgatókönyv |Python példaforgatókönyv, amely bemutatja, hogyan kell elvégezni a hitelesítést a Futtatás mint fiók. A runbook összes erőforráscsoport a előfizetésben sorolja fel. |
| AzureRunAsCertificate |A tanúsítvány eszköz, amely automatikusan jön létre, az Automation-fiók létrehozásakor, vagy egy meglévő fiók PowerShell parancsfájl használatával. A tanúsítvány hitelesíti az Azure-ral, így az Azure Resource Manager-erőforrások kezelhetők a runbookokból. Ennek a tanúsítványnak egy éves időtartama van. |
| AzureRunAsConnection |A kapcsolódási eszköz, amely automatikusan jön létre, az Automation-fiók létrehozásakor, vagy egy meglévő fiók PowerShell parancsfájl használatával. |

Az alábbi táblázat a klasszikus futtató fiókhoz kapcsolódó erőforrásokat foglalja össze.

| Erőforrás | Leírás |
| --- | --- |
| AzureClassicAutomationTutorial forgatókönyv |Grafikus példaforgatókönyv. A runbook egy előfizetésben klasszikus virtuális gépeinek lekérdezi a klasszikus futtató fiók (tanúsítvány) használatával. Mutatja majd, a virtuális gép nevét és állapotát. |
| AzureClassicAutomationTutorial parancsprogram-forgatókönyv |PowerShell példaforgatókönyv. A runbook egy előfizetésben klasszikus virtuális gépeinek lekérdezi a klasszikus futtató fiók (tanúsítvány) használatával. Mutatja majd, a virtuális gép nevét és állapotát. |
| AzureClassicRunAsCertificate |A tanúsítvány eszköz, amely automatikusan jön létre. Az a tanúsítvány hitelesíti az Azure-ral, így runbookok a klasszikus Azure-erőforrások kezelhetők. Ennek a tanúsítványnak egy éves időtartama van. |
| AzureClassicRunAsConnection |A kapcsolódási eszköz, amely automatikusan jön létre. Az eszköz, a runbookokból kezelheti a klasszikus Azure-erőforrások Azure hitelesíti. |

## <a name="next-steps"></a>További lépések

* Grafikus szerzői kapcsolatos további információkért lásd: [grafikus készítése az Azure Automationben](automation-graphical-authoring-intro.md).
* A PowerShell-forgatókönyvek használatának megismeréséhez tekintse meg a következőt: [Az első PowerShell-runbookom](automation-first-runbook-textual-powershell.md).
* A PowerShell-alapú munkafolyamat-runbookok első lépéseit [Az első PowerShell-alapú munkafolyamat-runbookom](automation-first-runbook-textual.md) című témakör ismerteti.
* A Python2-forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első Python2-forgatókönyvem](automation-first-runbook-textual-python2.md).
