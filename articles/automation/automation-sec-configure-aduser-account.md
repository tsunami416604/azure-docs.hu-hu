---
title: Azure AD felhasználói fiók konfigurálása | Microsoft Docs
description: Ez a cikk leírja, hogyan kell az Azure AD felhasználói fiók hitelesítését konfigurálni az Azure Automation forgatókönyveihez, hogy ARM- és ASM-hitelesítést is végezzen.
services: automation
documentationcenter: ''
author: MGoedtel
manager: jwhit
editor: tysonn
keywords: azure active directory felhasználó, azure szolgáltatás kezelése, azure ad és felhasználói fiók

ms.service: automation
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/12/2016
ms.author: magoedte

---
# Forgatókönyvek hitelesítése Azure szolgáltatásfelügyelet és Resource Manager használatával
Ez a cikk ismerteti a lépéseket, amelyeket végre kell hajtani egy Azure AD felhasználói fiók Azure szolgáltatásfelügyelet (ASM) vagy Azure Resource Manager (ARM) erőforrásokkal szemben futó Azure Automation forgatókönyvek számára történő konfigurálásához.  Miközben ez továbbra is egy támogatott hitelesítési identitás az ARM-alapú forgatókönyvek számára, a javasolt módszer az új Azure-beli futtató fiók használata.       

## Egy új Azure Active Directory-felhasználó létrehozása
1. Jelentkezzen be a klasszikus Azure portálra azon Azure-előfizetés szolgáltatás-rendszergazdájaként, amelyet kezelni szeretne.
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
12. Lépjen ki az Azure-ból, majd lépjen vissza a fiókkal, amelyet az imént létrehozott. A rendszer meg fogja kérni a felhasználó jelszavának módosítására.

## Egy Automation-fiók létrehozása a klasszikus Azure portálon
Ebben a szakaszban végre fogja hajtani a következő lépéseket egy új Azure Automation-fiók létrehozásához az Azure portálon, amely a forgatókönyvekkel együtt erőforrások kezelésére használható ASM- és ARM-módban.  

> [!NOTE]
> A klasszikus Azure portálon létrehozott Automation-fiókokat a klasszikus portálon és az Azure portálon, valamint mindkét parancsmagkészlettel is lehet kezelni. A fiók létrehozása után mindegy, hogyan hoz létre és kezel erőforrásokat a fiókon belül. Ha továbbra is a klasszikus Azure portál használatát tervezi, inkább azon hozzon létre Automation-fiókokat az Azure portál helyett.
> 
> 

1. Jelentkezzen be a klasszikus Azure portálra azon Azure-előfizetés szolgáltatás-rendszergazdájaként, amelyet kezelni szeretne.
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

## Egy Automation-fiók létrehozása az Azure portálon
Ebben a szakaszban végre fogja hajtani a következő lépéseket egy új Azure Automation-fiók létrehozásához az Azure portálon, amely a forgatókönyvekkel együtt erőforrások kezelésére használható ARM-módban.  

1. Jelentkezzen be az Azure portálra azon Azure-előfizetés szolgáltatás-rendszergazdájaként, amelyet kezelni szeretne.
2. Válassza az **Automation-fiókok** elemet.
3. Az Automation-fiókok panelen kattintson a **Hozzáadás** elemre.<br>![Automation-fiók hozzáadása](media/automation-sec-configure-azure-runas-account/add-automation-acct-properties.png)
4. Az **Automation-fiók hozzáadása** panel **Név** mezőjébe adjon meg egy nevet az új Automation-fióknak.
5. Ha egynél több előfizetéssel rendelkezik, adja meg az új fiókhoz használni kívántat, valamint egy új vagy meglévő **Erőforráscsoport** és egy Azure adatközpont **Hely** elemet.
6. Válassza a **Nem** értéket az **Azure-beli futtató fiók létrehozása** beállításnál, és kattintson a **Létrehozás** gombra.  
   
   > [!NOTE]
   > Ha a **Nem** beállítás kiválasztásával úgy dönt, hogy nem hozza létre a futtató fiókot, egy figyelmeztető üzenet jelenik meg az **Automation-fiók hozzáadása** panelen.  Miközben a fiók létrejön, és csatolva lesz az előfizetés **Közreműködő** szerepköréhez, nem fog hozzá tartozó hitelesítési identitással rendelkezni az előfizetések könyvtáron belül, és így nem fog hozzáférni erőforrásokhoz az előfizetésben.  Ez megakadályozza a fiókra hivatkozó forgatókönyveket abban, hogy hitelesítsenek és feladatokat végezzenek el az ARM-erőforrásokon.
   > 
   > 
   
    ![Az Automation-fiókhoz kapcsolódó figyelmeztetés hozzáadása](media/automation-sec-configure-azure-runas-account/add-automation-acct-properties-error.png)
7. Amíg az Azure létrehozza az Automation-fiókot, a menü **Értesítések** részén nyomon követheti a folyamat állapotát.

Amikor a hitelesítés létrehozása befejeződött, létre kell hoznia egy hitelesítőeszközt, amelyet hozzárendel az Automation-fiókhoz a korábban létrehozott AD felhasználói fiókkal.  Eddig csak az Automation-fiókot hoztuk létre, amely még nincs hozzárendelve a hitelesítési identitáshoz.  Hajtsa végre az [Azure Automation hitelesítőeszközei](automation-credentials.md#creating-a-new-credential) című cikk lépéseit, és adja meg a **felhasználónév** értékét **tartomány\felhasználónév** formátumban.

## Hitelesítés használata egy forgatókönyvben
A hitelesítési adatokat a [Get-AutomationPSCredential](http://msdn.microsoft.com/library/dn940015.aspx) tevékenység használatával szerezheti meg egy forgatókönyvben, majd az [Add-AzureAccount](http://msdn.microsoft.com/library/azure/dn722528.aspx) használatával kapcsolhatja össze Azure-előfizetésével. Ha a hitelesítési adatok egy több Azure-előfizetést kezelő rendszergazdához tartoznak, használja a [Select-AzureSubscription](http://msdn.microsoft.com/library/dn495203.aspx) parancsot a megfelelő meghatározásához. Ez látható az alábbi Windows PowerShell-mintában, amely jellemzően a legtöbb Azure Automation forgatókönyv tetején megjelenik.

    $cred = Get-AutomationPSCredential –Name "myuseraccount.onmicrosoft.com"
    Add-AzureAccount –Credential $cred
    Select-AzureSubscription –SubscriptionName "My Subscription"

A forgatókönyv minden [ellenőrzőpontja](http://technet.microsoft.com/library/dn469257.aspx#bk_Checkpoints) után ismételje meg ezeket a sorokat. Ha a forgatókönyvet felfüggesztik, majd később egy másik dolgozón újraindul, ismét végre kell hajtani a hitelesítést.

## Következő lépések
* Tekintse át a különböző forgatókönyvtípusokat és lépéseket a saját forgatókönyveinek létrehozásához a következő cikkben: [Azure Automation forgatókönyvtípusok](automation-runbook-types.md)

<!--HONumber=Sep16_HO4-->


