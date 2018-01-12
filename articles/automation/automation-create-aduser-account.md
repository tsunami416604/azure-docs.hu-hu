---
title: "Azure AD felhasználói fiók létrehozása | Microsoft Docs"
description: "Ez a cikk ismerteti az Azure AD felhasználói fiók hitelesítő adatait, a runbookok létrehozása az Azure Automation szolgáltatásbeli hitelesítéshez az Azure-ban."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: tysonn
keywords: "azure active directory felhasználó, azure szolgáltatás kezelése, azure ad és felhasználói fiók"
ms.assetid: fcfe266d-b22e-4dfb-8272-adcab09fc0cf
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/13/2017
ms.author: magoedte
ms.openlocfilehash: f0a9664898cd27529daf73d130dd25fd296a9b48
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2018
---
# <a name="authenticate-runbooks-with-azure-classic-deployment-and-resource-manager"></a>Runbookok hitelesítése a klasszikus Azure üzemi modellel és a Resource Managerrel
Ez a cikk azokat a lépéseket ismerteti, amelyeket végre kell hajtania egy Azure AD felhasználói fiók konfigurálásához klasszikus Azure üzemi modellen vagy Azure Resource Manager-erőforrásokon futó Azure Automation-runbookokhoz.  Ez továbbra is az Azure Resource Manager-alapú runbookok egy támogatott hitelesítési identitását, amíg az ajánlott módszer az Azure-beli futtató fiók használatára.       

## <a name="create-a-new-azure-active-directory-user"></a>Egy új Azure Active Directory-felhasználó létrehozása
1. Jelentkezzen be az Azure portálra azon Azure-előfizetés szolgáltatás-rendszergazdájaként, amelyet kezelni szeretne.
2. Válassza ki **Azure Active Directory** > **felhasználók és csoportok** > **minden felhasználó** > **új felhasználó**.
3. Adja meg a felhasználó például **neve** és **felhasználónév**.  
4. Jegyezze fel a felhasználó teljes nevét és ideiglenes jelszavát.
5. Válassza ki **Directory szerepkör**.
6. Globális vagy korlátozott rendszergazdai szerepkör hozzárendelése.
7. Lépjen ki az Azure-ból, majd lépjen vissza a fiókkal, amelyet az imént létrehozott. A rendszer felkéri a felhasználó jelszavának módosítására.

## <a name="create-an-automation-account-in-the-azure-portal"></a>Automation-fiók létrehozása az Azure Portalon
Ebben a szakaszban végre fogja hajtani a következő lépéseket egy Azure Automation-fiók létrehozásához az Azure Portalon, amely a runbookokkal együtt erőforrások kezelésére használható az Azure Resource Manager módban.  

1. Jelentkezzen be az Azure portálra azon Azure-előfizetés szolgáltatás-rendszergazdájaként, amelyet kezelni szeretne.
2. Válassza az **Automation-fiókok** elemet.
3. Válassza a **Hozzáadás** lehetőséget.<br><br>![Automation-fiók hozzáadása](media/automation-create-aduser-account/add-automation-acct-properties.png)
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

## <a name="next-steps"></a>További lépések
* Tekintse át a különböző forgatókönyvtípusokat és lépéseket a saját forgatókönyveinek létrehozásához a következő cikkben: [Azure Automation forgatókönyvtípusok](automation-runbook-types.md)

