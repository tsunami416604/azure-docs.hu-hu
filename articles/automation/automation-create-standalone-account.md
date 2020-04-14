---
title: Önálló Azure Automation-fiók létrehozása
description: Ez a cikk bemutatja az Azure Automationben egy rendszerbiztonsági rendszer hitelesítésének létrehozásának, tesztelésének és használatának lépéseit.
services: automation
ms.subservice: process-automation
ms.date: 01/15/2019
ms.topic: conceptual
ms.openlocfilehash: f06480767b697dca8fe41e484c02aefc58f040bf
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261329"
---
# <a name="create-a-standalone-azure-automation-account"></a>Önálló Azure Automation-fiók létrehozása

Ez a cikk bemutatja, hogyan hozhat létre egy Azure Automation-fiókot az Azure Portalon. A portál Automation-fiók segítségével kiértékelheti és megismerheti az Automation további felügyeleti megoldások használata vagy az Azure Monitor naplóinak integrálása nélkül. Ezeket a felügyeleti megoldásokat hozzáadhatja, vagy integrálhatja az Azure Monitor naplóival a runbook-feladatok speciális figyeléséhez a jövőben bármikor.

Automation-fiókkal hitelesítheti a runbookokat az Azure Resource Manager vagy a klasszikus üzembe helyezési modell erőforrásainak kezelésével. Egy Automation-fiókkal egy adott bérlő több régiójában és előfizetésén is kezelhetők erőforrások.

Amikor létrehoz egy Automation-fiókot az Azure Portalon, ezek a fiókok automatikusan létrejönnek:

* **Futtatás mint fiók**. Ez a fiók a következő feladatokat végzi:
  * Egyszerű szolgáltatás létrehozása az Azure Active Directoryban (Azure AD).
  * Létrehoz egy tanúsítványt.
  * Hozzárendeli a közreműködői szerepköralapú hozzáférés-vezérlés (RBAC), amely kezeli az Azure Resource Manager erőforrásait runbookok használatával.

Ezekkel a fiókokat hozva létre az Ön számára, gyorsan elkezdheti a runbookok létrehozását és üzembe helyezését az automatizálási igények kielégítésére.

## <a name="permissions-required-to-create-an-automation-account"></a>Automation-fiók létrehozásához szükséges engedélyek

Automation-fiók létrehozásához vagy frissítéséhez, valamint a cikkben ismertetett feladatok végrehajtásához a következő jogosultságokkal és engedélyekkel kell rendelkeznie:

* Automation-fiók létrehozásához az Azure AD felhasználói fiókját hozzá kell adni egy szerepkörhöz, amelynek a Microsoft tulajdonosi szerepkörével egyenértékű engedélyekkel **rendelkezik. Automatizálási** erőforrások. További információt az [Azure Automation szerepköralapú hozzáférés-vezérlése című témakörben talál.](automation-role-based-access-control.md)
* Az Azure Portalon az **Azure Active Directory** > **MANAGE** > **felhasználói beállításai**csoportban, ha az **alkalmazásregisztrációk** **igen,** az Azure AD-bérlő nem rendszergazdai felhasználói regisztrálhatják az [Active Directory-alkalmazásokat.](../active-directory/develop/howto-create-service-principal-portal.md#check-azure-subscription-permissions) Ha **az alkalmazásregisztrációk** beállítása **Nem,** a műveletet végző felhasználónak globális rendszergazdának kell lennie az Azure AD-ben.

Ha nem tagja az előfizetés Active Directory-példányának, mielőtt hozzáadna az előfizetés globális rendszergazdai/társrendszergazdai szerepköréhez, vendégként hozzáadódik az Active Directoryhoz. Ebben az esetben ez az üzenet jelenik meg az **Automation-fiók hozzáadása** lapon: "Nincs engedélye a létrehozáshoz."

Ha először egy felhasználót ad hozzá a globális rendszergazdai/társrendszergazdai szerepkörhöz, eltávolíthatja őket az előfizetés Active Directory-példányából, majd felolvashatja őket az Active Directory teljes felhasználói szerepkörébe.

A felhasználói szerepkörök ellenőrzése:

1. Az Azure Portalon nyissa meg az **Azure Active Directory** ablaktábláját.
1. Válassza a **Felhasználók és csoportok** elemet.
1. Válassza az **Összes felhasználó lehetőséget.**
1. Miután kijelölt egy adott felhasználót, válassza **a Profil**lehetőséget. A felhasználó profiljában lévő **User type** attribútum értéke nem lehet **Vendég**.

## <a name="create-a-new-automation-account-in-the-azure-portal"></a>Új Automation-fiók létrehozása az Azure Portalon

Ha azure Automation-fiókot szeretne létrehozni az Azure Portalon, hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be az Azure Portalra egy olyan fiókkal, amely az előfizetésrendszergazdák szerepkör tagja és az előfizetés társrendszergazdája.
1. Válassza **a + Erőforrás létrehozása**lehetőséget .
1. **Automatizálás**keresése . A keresési eredmények között válassza az **Automatizálás**lehetőséget.

   ![Az Automation & Control keresése és kiválasztása az Azure Piactéren](media/automation-create-standalone-account/automation-marketplace-select-create-automationacct.png)

1. A következő képernyőn válassza a **Létrehozás gombot.**

   ![Automatizálási fiók hozzáadása](media/automation-create-standalone-account/automation-create-automationacct-properties.png)

   > [!NOTE]
   > Ha a következő üzenet jelenik meg az **Automation-fiók hozzáadása** ablaktáblán, a fiók nem tagja az előfizetésrendszergazdák szerepkörnek és az előfizetés társrendszergazdájának.
   >
   > ![Automation-fiók hozzáadása – figyelmeztetés](media/automation-create-standalone-account/create-account-without-perms.png)

1. Az **Automatizálási fiók hozzáadása** ablaktáblán a **Név** mezőben adja meg az új Automation-fiók nevét. Ez a név nem módosítható a kiválasztása után. *Automation A fióknevek régiónként és erőforráscsoportonként egyediek. Előfordulhat, hogy a törölt Automation-fiókok nevei nem érhetők el azonnal.*
1. Ha egynél több előfizetéssel rendelkezik, az **Előfizetés** mezőben adja meg az új fiókhoz használni kívánt előfizetést.
1. Az **Erőforráscsoport**csoportban adjon meg vagy jelöljön ki egy új vagy meglévő erőforráscsoportot.
1. A **Hely**területen válasszon ki egy Azure-adatközpont-helyet.
1. Az **Azure Run As fiók létrehozása beállításnál** győződjön meg arról, hogy az **Igen** jelölőnégyzet be van jelölve, majd válassza a **Létrehozás lehetőséget.**

   > [!NOTE]
   > Ha úgy dönt, hogy nem hozza létre a Futtatás másként fiókot az **Azure-futtatás létrehozása fiók** **létrehozása** lehetőséghez lehetőséget választva, egy üzenet jelenik meg az **Automatizálási fiók hozzáadása** ablaktáblán. Bár a fiók az Azure Portalon jön létre, a fiók nem rendelkezik a megfelelő hitelesítési identitás a klasszikus üzembe helyezési modell előfizetés, vagy az Azure Resource Manager előfizetési címtárszolgáltatásban. Ezért az Automation-fiók nem fér hozzá az előfizetésben lévő erőforrásokhoz. Ez megakadályozza, hogy a fiókra hivatkozó runbookok hitelesítsék magukat, és feladatokat hajtsanak végre az adott telepítési modellek erőforrásaival.
   >
   > ![Automation-fiók hozzáadása – figyelmeztetés](media/automation-create-standalone-account/create-account-decline-create-runas-msg.png)
   >
   > Ha az egyszerű szolgáltatás nincs létrehozva, a közreműködői szerepkör nincs hozzárendelve.
   >

1. Az Automation-fiók létrehozásának előrehaladásának nyomon követéséhez a menüben válassza az **Értesítések**lehetőséget.

### <a name="resources-included"></a>Érintett erőforrások

Ha befejeződött az Automation-fiók létrehozása, számos erőforrás automatikusan létrejön. A létrehozás után ezek a runbookok biztonságosan törölhetők, ha nem szeretné megtartani őket. A Futtatás mint fiókok, használható hitelesítésre a fiókját egy runbookban, és meg kell hagyni, kivéve, ha létrehoz egy másikat, vagy nem igényel nekik. Az alábbi táblázat a futtató fiókhoz kapcsolódó erőforrásokat foglalja össze.

| Erőforrás | Leírás |
| --- | --- |
| AzureAutomationTutorial forgatókönyv |Példa grafikus runbook, amely bemutatja, hogyan lehet hitelesíteni a Futtatás másként fiók használatával. A runbook leveszi az összes Erőforrás-kezelő erőforrás-kezelő erőforrást. |
| AzureAutomationTutorialScript forgatókönyv |Példa a PowerShell-runbookra, amely bemutatja a Futtatás másként fiók használatával történő hitelesítést. A runbook leveszi az összes Erőforrás-kezelő erőforrás-kezelő erőforrást. |
| AzureAutomationTutorialPython2 forgatókönyv |Példa a Python runbook, amely bemutatja, hogyan lehet hitelesíteni a Futtatás másként fiók használatával. A runbook felsorolja az előfizetésben található összes erőforráscsoportot. |
| AzureRunAsCertificate |Olyan tanúsítványeszköz, amely automatikusan létrejön az Automation-fiók létrehozásakor, vagy egy meglévő fiók PowerShell-parancsfájljával. A tanúsítvány hitelesíti magát az Azure Resource Manager erőforrásait runbookokból. Ennek a tanúsítványnak egy éves időtartama van. |
| AzureRunAsConnection |Olyan kapcsolati eszköz, amely automatikusan létrejön az Automation-fiók létrehozásakor, vagy egy meglévő fiók PowerShell-parancsfájljával. |

## <a name="create-a-classic-run-as-account"></a>Klasszikus futtatási fiók létrehozása

Klasszikus Run-As-fiókok már nem jönnek létre, alapértelmezés szerint, amikor létrehoz egy Azure Automation-fiókot. Ha továbbra is szüksége van egy Klasszikus Run-As-fiókra, hajtsa végre a következő lépéseket.

1. Az **Automation-fiók** lapon válassza a **Futtatás fiókként** lehetőséget a **Fiókbeállítások területen.**
2. Válassza az **Azure Classic Futtatás fiókként lehetőséget.**
3. Kattintson a **Létrehozás** gombra a Klasszikus futtatás mint fiók létrehozásához.

## <a name="next-steps"></a>További lépések

* A grafikus szerzői jogról további információ: [Grafikus szerzői jog az Azure Automationben.](automation-graphical-authoring-intro.md)
* A PowerShell-forgatókönyvek használatának megismeréséhez tekintse meg a következőt: [Az első PowerShell-runbookom](automation-first-runbook-textual-powershell.md).
* A PowerShell-alapú munkafolyamat-runbookok első lépéseit [Az első PowerShell-alapú munkafolyamat-runbookom](automation-first-runbook-textual.md) című témakör ismerteti.
* A Python2-forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első Python2-forgatókönyvem](automation-first-runbook-textual-python2.md).

