---
title: "Azure AD felhasználói fiók létrehozása | Microsoft Docs"
description: "Ez a cikk azt ismerteti, hogyan kell egy Azure AD felhasználói fiók hitelesítő adatait létrehozni az Azure Automation runbookjaihoz úgy, hogy Azure- és klasszikus Azure-hitelesítést is végezzen."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: tysonn
keywords: "azure active directory felhasználó, azure szolgáltatás kezelése, azure ad és felhasználói fiók"
ms.assetid: fcfe266d-b22e-4dfb-8272-adcab09fc0cf
ms.service: automation
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/13/2017
ms.author: magoedte
ms.openlocfilehash: 700c4419821934daac89025c889b21d8e2ef46b6
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2017
---
# <a name="authenticate-runbooks-with-azure-classic-deployment-and-resource-manager"></a>Runbookok hitelesítése a klasszikus Azure üzemi modellel és a Resource Managerrel
Ez a cikk azokat a lépéseket ismerteti, amelyeket végre kell hajtania egy Azure AD felhasználói fiók konfigurálásához klasszikus Azure üzemi modellen vagy Azure Resource Manager-erőforrásokon futó Azure Automation-runbookokhoz.  Miközben ez továbbra is egy támogatott hitelesítési identitás az Azure Resource Manager-alapú runbookok számára, a javasolt módszer egy Azure-beli futtató fiók használata.       

## <a name="create-a-new-azure-active-directory-user"></a>Egy új Azure Active Directory-felhasználó létrehozása
1. Jelentkezzen be a klasszikus Azure portálra a kezelni kívánt Azure-előfizetés szolgáltatás-rendszergazdájaként.
2. Válassza az **Active Directory** lehetőséget, majd válassza ki a szervezete címtárának nevét.
3. Válassza a **Felhasználók** lapot, majd a parancsterületen válassza a **Felhasználó hozzáadása** parancsot.
4. A **Felhasználó bemutatása** lap **Felhasználó típusa** területén válassza az **Új felhasználó a szervezetben** elemet.
5. Írjon be egy felhasználónevet.  
6. Az Active Directory oldalon válassza ki a könyvtárnevet, amely az Azure-előfizetéséhez tartozik.
7. A **felhasználói profil** oldalon adjon meg egy kereszt- és egy vezetéknevet, egy rövid nevet és egy Felhasználót a **Szerepkörök** listából.  Ne jelölje be a **Multi-Factor Authentication engedélyezése** lehetőséget.
8. Jegyezze fel a felhasználó teljes nevét és ideiglenes jelszavát.
9. Válassza a **Beállítások > Rendszergazdák > Hozzáadás** elemet.
10. Írja be a teljes felhasználónevet, amelyet létrehozott.
11. Válassza ki a felhasználó által kezelendő előfizetést.
12. Lépjen ki az Azure-ból, majd lépjen vissza a fiókkal, amelyet az imént létrehozott. A rendszer felkéri a felhasználó jelszavának módosítására.

## <a name="create-an-automation-account-in-azure-classic-portal"></a>Automation-fiók létrehozása a klasszikus Azure portálon
Ebben a szakaszban végre fogja hajtani a következő lépéseket egy Azure Automation-fiók létrehozásához az Azure portálon, amely a runbookokkal együtt erőforrások kezelésére használható a klasszikus Azure üzemi modellben.  

> [!NOTE]
> A klasszikus Azure portálon létrehozott Automation-fiókokat a klasszikus portálon és az Azure Portalon, valamint mindkét parancsmagkészlettel is lehet kezelni. A fiók létrehozása után mindegy, hogyan hoz létre és kezel erőforrásokat a fiókon belül. Ha továbbra is a klasszikus Azure portál használatát tervezi, inkább azon hozzon létre Automation-fiókokat az Azure portál helyett.
> 
> 

1. Jelentkezzen be a klasszikus Azure portálra a kezelni kívánt Azure-előfizetés szolgáltatás-rendszergazdájaként.
2. Válassza az **Automation** elemet.
3. Az **Automation** oldalon válassza az **Automation-fiók létrehozása** elemet.
4. Az **Automation-fiók létrehozása** mezőben adjon egy nevet az új Automation-fióknak, és válasszon egyet a **Régió** legördülő listából.  
5. A beállítások elfogadásához és a fiók létrehozásához kattintson az **OK** gombra.
6. Miután létrejött, listázva lesz az **Automation** oldalon.
7. Kattintson a fiókra, ami az Irányítópult oldalra irányítja.  
8. Az Automation Irányítópult oldalon válassza az **Eszközök** elemet.
9. Az **Eszközök** oldalon válassza az oldal alján található **Beállítások hozzáadása** elemet.
10. A **Beállítások hozzáadása** oldalon válassza a **Hitelesítő adatok hozzáadása** elemet.
11. A **Hitelesítő adatok meghatározása** oldalon válassza a **Windows PowerShell hitelesítés** lehetőséget a **Hitelesítés típusa** legördülő listából, és adjon egy nevet a hitelesítésnek.
12. A következő **Hitelesítő adatok meghatározása** oldalon írja be a korábban létrehozott AD felhasználói fiók felhasználónevét a **Felhasználónév** mezőbe, jelszavát pedig a **Jelszó** és a **Jelszó megerősítése** mezőkbe. Kattintson az **OK** gombra a módosítások mentéséhez.

## <a name="create-an-automation-account-in-the-azure-portal"></a>Automation-fiók létrehozása az Azure Portalon
Ebben a szakaszban végre fogja hajtani a következő lépéseket egy Azure Automation-fiók létrehozásához az Azure Portalon, amely a runbookokkal együtt erőforrások kezelésére használható az Azure Resource Manager módban.  

1. Jelentkezzen be az Azure portálra azon Azure-előfizetés szolgáltatás-rendszergazdájaként, amelyet kezelni szeretne.
2. Válassza az **Automation-fiókok** elemet.
3. Az Automation-fiókok panelen kattintson a **Hozzáadás** elemre.<br><br>![Automation-fiók hozzáadása](media/automation-create-aduser-account/add-automation-acct-properties.png)
4. Az **Automation-fiók hozzáadása** panel **Név** mezőjébe adjon meg egy nevet az új Automation-fióknak.
5. Ha egynél több előfizetéssel rendelkezik, adja meg az új fiókhoz használni kívántat, valamint egy új vagy meglévő **Erőforráscsoport** és egy Azure adatközpont **Hely** elemet.
6. Válassza az **Igen** értéket az **Azure-beli futtató fiók létrehozása** beállításnál, és kattintson a **Létrehozás** gombra.  
   
    > [!NOTE]
    > Ha a **Nem** beállítás kiválasztásával úgy dönt, hogy nem hozza létre a futtató fiókot, egy figyelmeztető üzenet jelenik meg az **Automation-fiók hozzáadása** panelen.  Miközben a fiók létrejön, és csatolva lesz az előfizetés **Közreműködő** szerepköréhez, nem fog hozzá tartozó hitelesítési identitással rendelkezni az előfizetések könyvtáron belül, és így nem fog hozzáférni erőforrásokhoz az előfizetésben.  Ez megakadályozza a fiókra hivatkozó forgatókönyveket abban, hogy hitelesítsenek és feladatokat végezzenek el az Azure Resource Manager-erőforrásokon.
    > 
    >

    <br>![Az Automation-fiókhoz kapcsolódó figyelmeztetés hozzáadása](media/automation-create-aduser-account/add-automation-acct-properties-error.png)<br>  
7. Amíg az Azure létrehozza az Automation-fiókot, a menü **Értesítések** részén nyomon követheti a folyamat állapotát.

Amikor a hitelesítés létrehozása befejeződött, létre kell hoznia egy hitelesítőadat-eszközt, amelyet hozzárendel az Automation-fiókhoz a korábban létrehozott AD felhasználói fiókkal.  Eddig csak az Automation-fiókot hoztuk létre, amely még nincs hozzárendelve a hitelesítési identitáshoz.  Hajtsa végre az [Azure Automation hitelesítőeszközei](automation-credentials.md#creating-a-new-credential-asset) című cikk lépéseit, és adja meg a **felhasználónév** értékét **tartomány\felhasználónév** formátumban.

## <a name="use-the-credential-in-a-runbook"></a>Hitelesítés használata egy forgatókönyvben
A hitelesítési adatokat a [Get-AutomationPSCredential](http://msdn.microsoft.com/library/dn940015.aspx) tevékenység használatával szerezheti meg egy forgatókönyvben, majd az [Add-AzureAccount](http://msdn.microsoft.com/library/azure/dn722528.aspx) használatával kapcsolhatja össze Azure-előfizetésével. Ha a hitelesítési adatok egy több Azure-előfizetést kezelő rendszergazdához tartoznak, használja a [Select-AzureSubscription](http://msdn.microsoft.com/library/dn495203.aspx) parancsot a megfelelő meghatározásához. Ez látható az alábbi Windows PowerShell-mintában, amely jellemzően a legtöbb Azure Automation forgatókönyv tetején megjelenik.

    $cred = Get-AutomationPSCredential –Name "myuseraccount.onmicrosoft.com"
    Add-AzureAccount –Credential $cred
    Select-AzureSubscription –SubscriptionName "My Subscription"

A forgatókönyv minden [ellenőrzőpontja](http://technet.microsoft.com/library/dn469257.aspx#bk_Checkpoints) után ismételje meg ezeket a sorokat. Ha a forgatókönyvet felfüggesztik, majd később egy másik dolgozón újraindul, ismét végre kell hajtani a hitelesítést.

## <a name="next-steps"></a>Következő lépések
* Tekintse át a különböző forgatókönyvtípusokat és lépéseket a saját forgatókönyveinek létrehozásához a következő cikkben: [Azure Automation forgatókönyvtípusok](automation-runbook-types.md)

