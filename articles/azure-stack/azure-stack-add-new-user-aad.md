---
title: Adjon hozzá egy új Azure Stack-bérlői fiókkal az Azure Active Directoryban |} A Microsoft Docs
description: A Microsoft Azure Stack fejlesztői készletének telepítése, után kell legalább egy bérlői felhasználói fiók létrehozása, így megismerheti a bérlői portál.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: a75d5c88-5b9e-4e9a-a6e3-48bbfa7069a7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.openlocfilehash: 9a4d7200a2bc2445fcdfefc0332d67a045b5a2e1
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47038017"
---
# <a name="add-a-new-azure-stack-tenant-account-in-azure-active-directory"></a>Adjon hozzá egy új Azure Stack-bérlői fiókkal az Azure Active Directoryban

Miután [üzembe helyezése az Azure Stack Development Kit](azure-stack-run-powershell-script.md), szüksége lesz egy bérlői felhasználói fiókkal, így felderítheti a bérlői portálra, és tesztelje az ajánlatok és csomagok. Létrehozhat egy bérlői fiókot által [az Azure portal használatával](#create-an-azure-stack-tenant-account-using-the-azure-portal) vagy [PowerShell-lel](#create-an-azure-stack-tenant-account-using-powershell).

## <a name="create-an-azure-stack-tenant-account-using-the-azure-portal"></a>Hozzon létre egy Azure Stack-bérlői fiókkal az Azure portal használatával

Az Azure portal használata az Azure-előfizetéssel kell rendelkeznie.

1. Jelentkezzen be a [Azure](https://portal.azure.com).
2. A bal oldali navigációs sávon válassza **Active Directory** és lépjen abba a könyvtárba, amelyet szeretne használni az Azure Stack, vagy hozzon létre egy újat.
3. Válassza ki **Azure Active Directory** > **felhasználók** > **új felhasználó**.

    ![Felhasználók – minden felhasználó oldalon kiemelve az új felhasználóval](media/azure-stack-add-new-user-aad/new-user-all-users.png)

4. Az a **felhasználói** lap, adja meg a szükséges adatokat.

    ![Új felhasználó, a felhasználó oldalon a felhasználói adatok hozzáadása](media/azure-stack-add-new-user-aad/new-user-user.png)

    - **A név (kötelező).** Az első és utolsó az új felhasználó neve. Ha például Anna Parker.
    - **A felhasználónév (kötelező).** Az új felhasználó felhasználóneve. Például: mary@contoso.com.
        A felhasználó nevét tartomány része kell használnia a vagy a kezdeti alapértelmezett tartománynévnek, <_saját_tartománynév_>. onmicrosoft.com, vagy egy egyéni tartománynevet, például contoso.com. Egyéni tartománynév létrehozásával kapcsolatos további információkért lásd: [egyéni tartománynév hozzáadása az Azure Active Directoryhoz](../active-directory/fundamentals/add-custom-domain.md).
    - **Profil.** További információ a felhasználó igény szerint adhat hozzá. Felhasználói adatok később is hozzáadhat. Felhasználói adatok hozzáadásával kapcsolatos további információkért lásd: [hozzáadása vagy módosítása a felhasználói profil adatainak](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md).
    - **Címtárbeli szerepkör.**  Válasszon **felhasználói**.

5. Ellenőrizze **jelszó megjelenítése** , és másolja a megadott automatikusan generált jelszót a **jelszó** mezőbe. A kezdeti bejelentkezési folyamathoz kell ezt a jelszót.

6. Kattintson a **Létrehozás** gombra.

    A felhasználó létrehozása és az Azure AD-bérlőhöz hozzáadni.

7. Jelentkezzen be az új fiókot a Microsoft Azure-portálra. Módosítsa a jelszót, amikor a rendszer kéri.
8. Jelentkezzen be a `https://portal.local.azurestack.external` a bérlői portál új fiókkal.

## <a name="create-an-azure-stack-tenant-account-using-powershell"></a>Hozzon létre egy Azure Stack-bérlői fiókkal, PowerShell-lel

Ha nem rendelkezik Azure-előfizetéssel, az Azure Portalon adjon hozzá egy bérlő felhasználót nem használhat. Ebben az esetben használhatja az Azure Active Directory modul a Windows PowerShell helyette.

> [!NOTE]
> Ha az Azure Stack fejlesztői készletének telepítése a Microsoft Account (Live ID) használ, a AAD PowerShell bérlői fiók létrehozása nem használhat. 
> 
> 

1. Telepítse a [Microsoft Online Services bejelentkezési segéd az informatikusok RTW](https://www.microsoft.com/en-us/download/details.aspx?id=41950).
2. Telepítse a [Azure Active Directory modul a Windows PowerShell (64 bites verzió)](http://go.microsoft.com/fwlink/p/?linkid=236297) , és nyissa meg azt.
3. A következő parancsmagok futtatásához:

    ```powershell
    # Provide the AAD credential you use to deploy Azure Stack Development Kit

            $msolcred = get-credential

    # Add a tenant account "Tenant Admin <username>@<yourdomainname>" with the initial password "<password>".

            connect-msolservice -credential $msolcred
            $user = new-msoluser -DisplayName "Tenant Admin" -UserPrincipalName <username>@<yourdomainname> -Password <password>
            Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType User -RoleMemberObjectId $user.ObjectId

    ```

1. Jelentkezzen be a Microsoft Azure-az új fiókot. Módosítsa a jelszót, amikor a rendszer kéri.
2. Jelentkezzen be a `https://portal.local.azurestack.external` a bérlői portál új fiókkal.

