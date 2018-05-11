---
title: Az Azure AD-felhasználói fiók létrehozása
description: Ez a cikk ismerteti az Azure AD felhasználói fiók hitelesítő adatait, a runbookok létrehozása az Azure Automation szolgáltatásbeli hitelesítéshez az Azure-ban.
keywords: azure active directory felhasználó, azure szolgáltatás kezelése, azure ad és felhasználói fiók
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 07fb9284372020ba2aadb3758d0ab46682b50831
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
---
# <a name="authenticate-runbooks-with-azure-classic-deployment-and-resource-manager"></a>Runbookok hitelesítése a klasszikus Azure üzemi modellel és a Resource Managerrel
Ez a cikk azokat a lépéseket ismerteti, amelyeket végre kell hajtania egy Azure AD felhasználói fiók konfigurálásához klasszikus Azure üzemi modellen vagy Azure Resource Manager-erőforrásokon futó Azure Automation-runbookokhoz. Ez továbbra is az Azure Resource Manager-alapú runbookok egy támogatott hitelesítési identitását, amíg az ajánlott módszer az Azure-beli futtató fiók használatára.       

## <a name="create-a-new-azure-active-directory-user"></a>Egy új Azure Active Directory-felhasználó létrehozása
1. Jelentkezzen be az Azure portálra azon Azure-előfizetés szolgáltatás-rendszergazdájaként, amelyet kezelni szeretne.
2. Válassza ki **Azure Active Directory** > **felhasználók és csoportok** > **minden felhasználó** > **új felhasználó**.
3. Adja meg a felhasználó például **neve** és **felhasználónév**.  
4. Jegyezze fel a felhasználó teljes nevét és ideiglenes jelszavát.
5. Válassza ki **Directory szerepkör**.
6. Globális vagy korlátozott rendszergazdai szerepkör hozzárendelése.
7. Jelentkezzen ki az Azure, és ezután bejelentkezés vissza a most létrehozott fiókot. A jelszó módosításához kéri.

## <a name="create-an-automation-account-in-the-azure-portal"></a>Automation-fiók létrehozása az Azure Portalon
Ebben a szakaszban végre fogja hajtani a következő lépéseket egy Azure Automation-fiók létrehozásához az Azure Portalon, amely a runbookokkal együtt erőforrások kezelésére használható az Azure Resource Manager módban.  

1. Jelentkezzen be az Azure portálra azon Azure-előfizetés szolgáltatás-rendszergazdájaként, amelyet kezelni szeretne.
2. Válassza az **Automation-fiókok** elemet.
3. Válassza a **Hozzáadás** lehetőséget.<br><br>![Automation-fiók hozzáadása](media/automation-create-aduser-account/add-automation-acct-properties.png)
4. Az **Automation-fiók hozzáadása** panel **Név** mezőjébe adjon meg egy nevet az új Automation-fióknak.
5. Ha egynél több előfizetéssel rendelkezik, adja meg az új fiókhoz használni kívántat, valamint egy új vagy meglévő **Erőforráscsoport** és egy Azure adatközpont **Hely** elemet.
6. Válassza az **Igen** értéket az **Azure-beli futtató fiók létrehozása** beállításnál, és kattintson a **Létrehozás** gombra.  
   
    > [!NOTE]
    > Ha úgy dönt, hogy a Futtatás mint fiókot létrehozni beállítás kiválasztásával **nem**, lehetősége lesz a figyelmeztető üzenetet a **Automation-fiók hozzáadása** panelen. Amíg a fiók létrehozni és hozzárendelni a **közreműködő** szerepkört az előfizetés, a megfelelő hitelesítési identitás belül az előfizetések címtárszolgáltatás, és ezért nem az Azure-erőforrások nem rendelkezik a előfizetés. Ez megakadályozza, hogy ez a fiók nem tudnak hitelesítést és a feladatokat az Azure Resource Manager erőforrásokon hivatkozó runbookokat.
    > 
    >

    <br>![Az Automation-fiókhoz kapcsolódó figyelmeztetés hozzáadása](media/automation-create-aduser-account/add-automation-acct-properties-error.png)<br>  
7. Amíg az Azure létrehozza az Automation-fiókot, a menü **Értesítések** részén nyomon követheti a folyamat állapotát.

Amikor a hitelesítés létrehozása befejeződött, létre kell hoznia egy hitelesítőadat-eszközt, amelyet hozzárendel az Automation-fiókhoz a korábban létrehozott AD felhasználói fiókkal. Ne feledje, hogy csak az Automation-fiók létrehozott, és nincs társítva egy hitelesítési identitását. Hajtsa végre az [Azure Automation hitelesítőeszközei](automation-credentials.md#creating-a-new-credential-asset) című cikk lépéseit, és adja meg a **felhasználónév** értékét **tartomány\felhasználónév** formátumban.

## <a name="use-the-credential-in-a-runbook"></a>Hitelesítés használata egy forgatókönyvben
A hitelesítési adatokat a [Get-AutomationPSCredential](http://msdn.microsoft.com/library/dn940015.aspx) tevékenység használatával szerezheti meg egy forgatókönyvben, majd az [Add-AzureAccount](http://msdn.microsoft.com/library/azure/dn722528.aspx) használatával kapcsolhatja össze Azure-előfizetésével. Ha a hitelesítési adatok egy több Azure-előfizetést kezelő rendszergazdához tartoznak, használja a [Select-AzureSubscription](http://msdn.microsoft.com/library/dn495203.aspx) parancsot a megfelelő meghatározásához. Ez általában a legtöbb Azure Automation-runbook tetején megjelenő következő PowerShell-példa látható.

    $cred = Get-AutomationPSCredential –Name "myuseraccount.onmicrosoft.com"
    Add-AzureAccount –Credential $cred
    Select-AzureSubscription –SubscriptionName "My Subscription"

Ezek a sorok ismétlés után bármely [ellenőrzőpontokat](http://technet.microsoft.com/library/dn469257.aspx#bk_Checkpoints) a runbookban. Ha egy másik feldolgozónak a majd folytatja a runbook fel van függesztve, majd kell újra a hitelesítés végrehajtásához.

## <a name="next-steps"></a>További lépések
* Tekintse át a különböző forgatókönyvtípusokat és lépéseket a saját forgatókönyveinek létrehozásához a következő cikkben: [Azure Automation forgatókönyvtípusok](automation-runbook-types.md)

