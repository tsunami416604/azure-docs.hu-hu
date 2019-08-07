---
title: Önálló Azure Automation-fiók létrehozása
description: Ez a cikk bemutatja, hogyan hozhat létre, tesztelheti és használhat egy példa rendszerbiztonsági tag hitelesítését Azure Automationban.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 01/15/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 32fafaeb6332ca0e76dbc8d72f11872a82ca1cbe
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779156"
---
# <a name="create-a-standalone-azure-automation-account"></a>Önálló Azure Automation-fiók létrehozása

Ez a cikk bemutatja, hogyan hozhat létre Azure Automation fiókot a Azure Portalban. A portál Automation-fiók használatával kiértékelheti és megismerheti az Automationt további felügyeleti megoldások használata vagy Azure Monitor naplókkal való integráció nélkül. Ezeket a felügyeleti megoldásokat felveheti, vagy integrálhatja Azure Monitor naplókkal a runbook-feladatok speciális monitorozásához a jövőben.

Az Automation-fiókkal a runbookok hitelesítése Azure Resource Manager vagy a klasszikus üzemi modell erőforrásainak kezelésével végezhető el. Egy Automation-fiókkal egy adott bérlő több régiójában és előfizetésén is kezelhetők erőforrások.

Amikor létrehoz egy Automation-fiókot a Azure Portalban, a rendszer automatikusan létrehozza ezeket a fiókokat:

* **Futtató fiók**. Ez a fiók a következő feladatokat hajtja végre:
  * Egyszerű szolgáltatásnevet hoz létre Azure Active Directoryban (Azure AD).
  * Létrehoz egy tanúsítványt.
  * Hozzárendeli a közreműködői szerepkör-alapú Access Control (RBAC), amely a runbookok használatával kezeli Azure Resource Manager erőforrásait.
* **Klasszikus futtató fiók**. Ez a fiók feltölt egy felügyeleti tanúsítványt. A tanúsítvány a klasszikus erőforrásokat a runbookok használatával kezeli.

Az Ön által létrehozott fiókokkal gyorsan megkezdheti a runbookok létrehozását és üzembe helyezését az automatizálási igények támogatásához.

## <a name="permissions-required-to-create-an-automation-account"></a>Az Automation-fiók létrehozásához szükséges engedélyek

Automation-fiók létrehozásához vagy frissítéséhez, valamint a cikkben ismertetett feladatok végrehajtásához a következő jogosultságokkal és engedélyekkel kell rendelkeznie:

* Az Automation-fiók létrehozásához hozzá kell adni az Azure ad-felhasználói fiókot egy olyan szerepkörhöz, amely a **Microsoft tulajdonosi szerepkörével egyenértékű engedélyekkel rendelkezik. Automation** -erőforrások. További információ: [szerepköralapú Access Control Azure Automation](automation-role-based-access-control.md).
* A Azure Portal **Azure Active Directory** > **felhasználói beállítások** **kezelése** > területen, ha a **Alkalmazásregisztrációk** értéke **Igen**, akkor az Azure ad-bérlő nem rendszergazda felhasználója [regisztrálhat aktív Alkalmazásspecifikus alkalmazások](../active-directory/develop/howto-create-service-principal-portal.md#check-azure-subscription-permissions). Ha **Alkalmazásregisztrációk** a **nem**értékre van állítva, akkor a műveletet végrehajtó felhasználónak globális rendszergazdának kell lennie az Azure ad-ben.

Ha nem tagja az előfizetés Active Directory példányának, mielőtt hozzáadja az előfizetéshez a globális rendszergazda vagy a társ-rendszergazda szerepkörhöz, a rendszer a Active Directory vendégként adja hozzá. Ebben az esetben a következő üzenet jelenik meg az **Automation-fiók hozzáadása** oldalon: "Nincs engedélye a létrehozásához."

Ha először ad hozzá felhasználót a globális rendszergazda vagy a rendszergazdai szerepkörhöz, eltávolíthatja őket az előfizetés Active Directory példányáról, majd elolvashatja őket a Active Directory teljes felhasználói szerepkörével.

Felhasználói szerepkörök ellenőrzése:

1. A Azure Portal nyissa meg a **Azure Active Directory** ablaktáblát.
1. Válassza a **Felhasználók és csoportok** elemet.
1. Válassza **a minden felhasználó**lehetőséget.
1. Egy adott felhasználó kijelölése után válassza a **profil**lehetőséget. A felhasználó profiljában lévő **felhasználói Type** attribútum értéke nem lehet **vendég**.

## <a name="create-a-new-automation-account-in-the-azure-portal"></a>Hozzon létre egy új Automation-fiókot a Azure Portal

Ha Azure Automation fiókot szeretne létrehozni a Azure Portalban, hajtsa végre a következő lépéseket:

1. Jelentkezzen be a Azure Portalba egy olyan fiókkal, amely tagja az előfizetés-adminisztrátorok szerepkörnek, és az előfizetés egy rendszergazdája.
1. Válassza **az + erőforrás létrehozása**lehetőséget.
1. Keressen az **Automation**kifejezésre. A keresési eredmények között válassza az **automatizálás**lehetőséget.

   ![Automation & Control keresése és kiválasztása az Azure Marketplace-en](media/automation-create-standalone-account/automation-marketplace-select-create-automationacct.png)

1. A következő képernyőn válassza a **Létrehozás**lehetőséget.

   ![Automation-fiók hozzáadása](media/automation-create-standalone-account/automation-create-automationacct-properties.png)

   > [!NOTE]
   > Ha a következő üzenet jelenik meg az **Automation-fiók hozzáadása** panelen, a fiókja nem tagja az előfizetés-adminisztrátorok szerepkörnek, és az előfizetés egyik felügyelője.
   >
   > ![Automation-fiókra vonatkozó figyelmeztetés hozzáadása](media/automation-create-standalone-account/create-account-without-perms.png)

1. Az **Automation-fiók hozzáadása** panel **név** mezőjébe írja be az új Automation-fiók nevét. Ez a név nem módosítható a kiválasztása után. *Az Automation-fiókok nevei régiónként és erőforráscsoporthoz egyediek. A törölt Automation-fiókok neve nem lehet azonnal elérhető.*
1. Ha egynél több előfizetéssel rendelkezik, az **előfizetés** mezőben adhatja meg az új fiókhoz használni kívánt előfizetést.
1. Az **erőforráscsoport**mezőben adjon meg vagy válasszon ki egy új vagy egy meglévő erőforráscsoportot.
1. A **hely**mezőben válasszon ki egy Azure-adatközpont helyét.
1. Az Azure-beli **futtató fiók létrehozása** beállításnál győződjön meg arról, hogy az **Igen** lehetőség van kiválasztva, majd válassza a **Létrehozás**lehetőséget.

   > [!NOTE]
   > Ha úgy dönt, hogy nem hozza létre a futtató fiókot az Azure-beli **futtató fiók létrehozása**nem lehetőség kiválasztásával, egy üzenet jelenik meg az **Automation-fiók hozzáadása** panelen. Bár a fiók létrejön a Azure Portalban, a fiók nem rendelkezik a megfelelő hitelesítési identitással a klasszikus üzemi modell előfizetésében vagy a Azure Resource Manager előfizetés-címtárszolgáltatás szolgáltatásban. Ezért az Automation-fióknak nincs hozzáférése az előfizetéséhez tartozó erőforrásokhoz. Ez megakadályozza, hogy az erre a fiókra hivatkozó runbookok az adott üzemi modell erőforrásaihoz tartozó erőforrások hitelesítéséhez és végrehajtásához is hozzáférjenek.
   >
   > ![Automation-fiókra vonatkozó figyelmeztetés hozzáadása](media/automation-create-standalone-account/create-account-decline-create-runas-msg.png)
   >
   > Ha az egyszerű szolgáltatásnév nincs létrehozva, a közreműködői szerepkör nincs hozzárendelve.
   >

1. Az Automation-fiók létrehozási folyamatának nyomon követéséhez válassza a menü **értesítések**elemét.

### <a name="resources-included"></a>Érintett erőforrások

Ha befejeződött az Automation-fiók létrehozása, számos erőforrás automatikusan létrejön. A létrehozás után ezeket a runbookok biztonságosan törölheti, ha nem szeretné megtartani őket. A futtató fiókok segítségével hitelesítheti a fiókját egy runbook, és csak akkor hagyhatja el, ha létrehoz egy másikat, vagy ha nem igényel ilyet. Az alábbi táblázat a futtató fiókhoz kapcsolódó erőforrásokat foglalja össze.

| Resource | Leírás |
| --- | --- |
| AzureAutomationTutorial forgatókönyv |Példa grafikus runbook, amely bemutatja, hogyan történik a hitelesítés a futtató fiók használatával. A runbook lekéri az összes Resource Manager-erőforrást. |
| AzureAutomationTutorialScript forgatókönyv |Egy PowerShell-runbook, amely bemutatja, hogyan történik a hitelesítés a futtató fiók használatával. A runbook lekéri az összes Resource Manager-erőforrást. |
| AzureAutomationTutorialPython2 forgatókönyv |Egy példa Python-runbook, amely bemutatja, hogyan történik a hitelesítés a futtató fiók használatával. A runbook az előfizetésben lévő összes erőforráscsoportot listázza. |
| AzureRunAsCertificate |Az Automation-fiók létrehozásakor automatikusan létrehozott tanúsítvány, vagy egy meglévő fiókhoz tartozó PowerShell-parancsfájl használatával. A tanúsítvány az Azure-ban hitelesíthető, így Azure Resource Manager erőforrásokat felügyelheti a runbookok. Ennek a tanúsítványnak egy éves időtartama van. |
| AzureRunAsConnection |Az Automation-fiók létrehozásakor automatikusan létrehozott, vagy egy meglévő fiókhoz tartozó PowerShell-szkriptet használó kapcsolódási eszköz. |

Az alábbi táblázat a klasszikus futtató fiókhoz kapcsolódó erőforrásokat foglalja össze.

| Resource | Leírás |
| --- | --- |
| AzureClassicAutomationTutorial forgatókönyv |Példa grafikus runbook. A runbook a klasszikus futtató fiók (tanúsítvány) segítségével lekéri az előfizetésben lévő összes klasszikus virtuális gépet. Ezután megjeleníti a virtuális gépek nevét és állapotát. |
| AzureClassicAutomationTutorial parancsprogram-forgatókönyv |Példa PowerShell-runbook. A runbook a klasszikus futtató fiók (tanúsítvány) segítségével lekéri az előfizetésben lévő összes klasszikus virtuális gépet. Ezután megjeleníti a virtuális gépek nevét és állapotát. |
| AzureClassicRunAsCertificate |Egy automatikusan létrehozott tanúsítvány-eszköz. A tanúsítvány az Azure-ban hitelesítve van, így a klasszikus Azure-erőforrásokat kezelheti a runbookok. Ennek a tanúsítványnak egy éves időtartama van. |
| AzureClassicRunAsConnection |Egy automatikusan létrehozott összekötő-eszköz. Az eszköz hitelesíti magát az Azure-ban, így a klasszikus Azure-erőforrásokat kezelheti a runbookok. |

## <a name="next-steps"></a>További lépések

* További információ a grafikus létrehozásról: [grafikus létrehozás Azure Automationban](automation-graphical-authoring-intro.md).
* A PowerShell-forgatókönyvek használatának megismeréséhez tekintse meg a következőt: [Az első PowerShell-runbookom](automation-first-runbook-textual-powershell.md).
* A PowerShell-alapú munkafolyamat-runbookok első lépéseit [Az első PowerShell-alapú munkafolyamat-runbookom](automation-first-runbook-textual.md) című témakör ismerteti.
* A Python2-forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első Python2-forgatókönyvem](automation-first-runbook-textual-python2.md).

