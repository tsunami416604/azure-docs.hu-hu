---
title: Önálló Azure Automation-fiók létrehozása
description: Ebből a cikkből megtudhatja, hogyan hozhat létre önálló Azure Automation fiókot és klasszikus futtató fiókot.
services: automation
ms.subservice: process-automation
ms.date: 01/15/2019
ms.topic: conceptual
ms.openlocfilehash: ffe7edd5faf5ae4f88f9f3e0c40256bee32395d7
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83714865"
---
# <a name="create-a-standalone-azure-automation-account"></a>Önálló Azure Automation-fiók létrehozása

Ez a cikk bemutatja, hogyan hozhat létre Azure Automation fiókot a Azure Portalban. A portál Automation-fiók használatával kiértékelheti és megismerheti az Automation szolgáltatást további felügyeleti funkciók használata vagy a Azure Monitor naplókkal való integráció nélkül. A runbook-feladatok speciális monitorozásához a jövőben bármikor hozzáadhat felügyeleti funkciókat, illetve integrálhatja azokat Azure Monitor naplókkal.

Az Automation-fiókkal a runbookok hitelesítése Azure Resource Manager vagy a klasszikus üzemi modell erőforrásainak kezelésével végezhető el. Egy Automation-fiókkal egy adott bérlő több régiójában és előfizetésén is kezelhetők erőforrások.

Amikor létrehoz egy Automation-fiókot a Azure Portalban, a rendszer automatikusan létrehozza a **futtató** fiókot. Ez a fiók a következő feladatokat hajtja végre:

* Egyszerű szolgáltatásnevet hoz létre Azure Active Directoryban (Azure AD).
* Létrehoz egy tanúsítványt.
* Hozzárendeli a közreműködői szerepkör-alapú Access Control (RBAC), amely a runbookok használatával kezeli Azure Resource Manager erőforrásait.

A fiók létrehozásával gyorsan megkezdheti a runbookok létrehozását és üzembe helyezését az automatizálási igények kielégítése érdekében.

## <a name="permissions-required-to-create-an-automation-account"></a>Az Automation-fiók létrehozásához szükséges engedélyek

Automation-fiók létrehozásához vagy frissítéséhez, valamint a cikkben ismertetett feladatok végrehajtásához a következő jogosultságokkal és engedélyekkel kell rendelkeznie:

* Az Automation-fiók létrehozásához hozzá kell adni az Azure AD-felhasználói fiókot egy olyan szerepkörhöz, amely az erőforrások tulajdonosi szerepkörével egyenértékű engedélyekkel rendelkezik `Microsoft.Automation` . További információ: [szerepköralapú Access Control Azure Automation](automation-role-based-access-control.md).
* A Azure Portal **Azure Active Directory**  >  **MANAGE**  >  **felhasználói beállítások**kezelése területen, ha a **Alkalmazásregisztrációk** értéke **Igen**, akkor az Azure ad-bérlő nem rendszergazda felhasználója [regisztrálhat Active Directory alkalmazásokat](../active-directory/develop/howto-create-service-principal-portal.md#check-azure-subscription-permissions). Ha **Alkalmazásregisztrációk** a **nem**értékre van állítva, akkor a műveletet végrehajtó felhasználónak globális rendszergazdának kell lennie az Azure ad-ben.

Ha nem tagja az előfizetés Active Directory példányának, mielőtt felveszi az előfizetéshez tartozó globális rendszergazdai vagy társ-felügyeleti szerepkörbe, a rendszer vendégként adja hozzá a Active Directory. Ebben az esetben a következő üzenet jelenik meg az Automation-fiók hozzáadása panelen:`You do not have permissions to create.`

Ha először ad hozzá egy felhasználót a globális rendszergazda vagy a rendszergazda szerepkörhöz, akkor eltávolíthatja a felhasználót az előfizetés Active Directory példányáról. A felhasználó a Active Directoryban található felhasználói szerepkörhöz is elolvasható. Felhasználói szerepkörök ellenőrzése:

1. A Azure Portal nyissa meg a Azure Active Directory ablaktáblát.
1. Válassza a **Felhasználók és csoportok** elemet.
1. Válassza **a minden felhasználó**lehetőséget.
1. Egy adott felhasználó kijelölése után válassza a **profil**lehetőséget. A felhasználó profiljában lévő **felhasználói Type** attribútum értéke nem lehet **vendég**.

## <a name="create-a-new-automation-account-in-the-azure-portal"></a>Hozzon létre egy új Automation-fiókot a Azure Portal

Ha Azure Automation fiókot szeretne létrehozni a Azure Portalban, hajtsa végre a következő lépéseket:

1. Jelentkezzen be a Azure Portalba egy olyan fiókkal, amely tagja az előfizetés-adminisztrátorok szerepkörnek, és az előfizetés egy rendszergazdája.
1. Válassza **az + erőforrás létrehozása**lehetőséget.
1. Keressen az **Automation**kifejezésre. A keresési eredmények között válassza az **automatizálás**lehetőséget.

   ![Automatizálási & vezérlésének keresése és kiválasztása az Azure Marketplace-en](media/automation-create-standalone-account/automation-marketplace-select-create-automationacct.png)

1. A következő képernyőn válassza az **új létrehozása**lehetőséget.

   ![Automation-fiók hozzáadása](media/automation-create-standalone-account/automation-create-automationacct-properties.png)

   > [!NOTE]
   > Ha a következő üzenet jelenik meg az Automation-fiók hozzáadása panelen, a fiókja nem tagja az előfizetés-adminisztrátorok szerepkörnek, és az előfizetés egyik felügyelője.
   >
   > ![Automation-fiókra vonatkozó figyelmeztetés hozzáadása](media/automation-create-standalone-account/create-account-without-perms.png)

1. Az Automation-fiók hozzáadása panelen adja meg az új Automation-fiók nevét a **név** mezőben. Ez a név nem módosítható a kiválasztása után. 

    > [!NOTE]
    > Az Automation-fiókok nevei régiónként és erőforráscsoporthoz egyediek. Előfordulhat, hogy a törölt Automation-fiókok neve nem érhető el azonnal.

1. Ha egynél több előfizetéssel rendelkezik, használja az **előfizetés** mezőt az új fiókhoz használni kívánt előfizetés megadásához.
1. Az **erőforráscsoport**mezőben adjon meg vagy válasszon ki egy új vagy egy meglévő erőforráscsoportot.
1. A **hely**mezőben válasszon ki egy Azure-adatközpont helyét.
1. Az Azure-beli **futtató fiók létrehozása** beállításnál győződjön meg arról, hogy az **Igen** lehetőség van kiválasztva, majd kattintson a **Létrehozás**gombra.

   > [!NOTE]
   > Ha úgy dönt, hogy nem hozza létre a futtató fiókot az Azure-beli **futtató fiók létrehozása** **nem lehetőség kiválasztásával** , egy üzenet jelenik meg az Automation-fiók hozzáadása panelen. Bár a fiók létrejön a Azure Portalban, a fiók nem rendelkezik a megfelelő hitelesítési identitással a klasszikus üzemi modell előfizetésében vagy a Azure Resource Manager előfizetés-címtárszolgáltatás szolgáltatásban. Ezért az Automation-fióknak nincs hozzáférése az előfizetéséhez tartozó erőforrásokhoz. Ez megakadályozza, hogy az erre a fiókra hivatkozó runbookok az adott üzemi modell erőforrásaihoz tartozó erőforrások hitelesítéséhez és végrehajtásához is hozzáférjenek.
   >
   > ![Automation-fiókra vonatkozó figyelmeztetés hozzáadása](media/automation-create-standalone-account/create-account-decline-create-runas-msg.png)
   >
   > Ha az egyszerű szolgáltatásnév nincs létrehozva, a közreműködői szerepkör nincs hozzárendelve.
   >

1. Az Automation-fiók létrehozási folyamatának nyomon követéséhez válassza a menü **értesítések** elemét.

Ha befejeződött az Automation-fiók létrehozása, számos erőforrás automatikusan létrejön. A létrehozás után ezeket a runbookok biztonságosan törölheti, ha nem szeretné megtartani őket. A futtató fiókok segítségével hitelesítheti a fiókját egy runbook, és csak akkor hagyhatja el, ha létrehoz egy másikat, vagy ha nem igényel ilyet. Az alábbi táblázat a futtató fiókhoz kapcsolódó erőforrásokat foglalja össze.

| Erőforrás | Leírás |
| --- | --- |
| AzureAutomationTutorial forgatókönyv |Példa grafikus runbook, amely bemutatja, hogyan történik a hitelesítés a futtató fiók használatával. A runbook lekéri az összes Resource Manager-erőforrást. |
| AzureAutomationTutorialScript forgatókönyv |Egy PowerShell-runbook, amely bemutatja, hogyan történik a hitelesítés a futtató fiók használatával. A runbook lekéri az összes Resource Manager-erőforrást. |
| AzureAutomationTutorialPython2 forgatókönyv |Egy példa Python-runbook, amely bemutatja, hogyan történik a hitelesítés a futtató fiók használatával. A runbook az előfizetésben lévő összes erőforráscsoportot listázza. |
| AzureRunAsCertificate |Az Automation-fiók létrehozásakor automatikusan létrehozott tanúsítvány, vagy egy meglévő fiókhoz tartozó PowerShell-parancsfájl használatával. A tanúsítvány az Azure-ban hitelesíthető, így Azure Resource Manager erőforrásokat felügyelheti a runbookok. Ennek a tanúsítványnak egy éves időtartama van. |
| AzureRunAsConnection |Az Automation-fiók létrehozásakor automatikusan létrehozott, vagy egy meglévő fiókhoz tartozó PowerShell-szkriptet használó kapcsolódási eszköz. |

## <a name="create-a-classic-run-as-account"></a>Klasszikus futtató fiók létrehozása

A klasszikus futtató fiókok már nem jönnek létre alapértelmezés szerint Azure Automation fiók létrehozásakor. Ha továbbra is klasszikus futtató fiókra van szüksége:

1. Az Automation-fiókban válassza a **futtató fiókok** lehetőséget a **Fiókbeállítások**területen.
2. Válassza a **klasszikus Azure-beli futtató fiók**lehetőséget.
3. A klasszikus futtató fiók létrehozásának folytatásához kattintson a **Létrehozás** gombra.

## <a name="next-steps"></a>Következő lépések

* [Grafikus szerzői műveletek Azure Automation](automation-graphical-authoring-intro.md)
* [Az első PowerShell-forgatókönyvem](automation-first-runbook-textual-powershell.md)
* [Az első PowerShell-munkafolyamat runbook](automation-first-runbook-textual.md)
* [Az első Python2-runbook](automation-first-runbook-textual-python2.md)
* [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation)
