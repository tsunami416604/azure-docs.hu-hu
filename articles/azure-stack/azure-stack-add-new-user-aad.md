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
ms.date: 02/21/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.openlocfilehash: c312658750c1e9ef024a837ccc16e5cd5be8a5ef
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35925500"
---
# <a name="add-a-new-azure-stack-tenant-account-in-azure-active-directory"></a>Adjon hozzá egy új Azure Stack-bérlői fiókkal az Azure Active Directoryban
Miután [üzembe helyezése az Azure Stack Development Kit](azure-stack-run-powershell-script.md), szüksége lesz egy bérlői felhasználói fiókkal, így felderítheti a bérlői portálra, és tesztelje az ajánlatok és csomagok. Létrehozhat egy bérlői fiókot által [az Azure portal használatával](#create-an-azure-stack-tenant-account-using-the-azure-portal) vagy [PowerShell-lel](#create-an-azure-stack-tenant-account-using-powershell).

## <a name="create-an-azure-stack-tenant-account-using-the-azure-portal"></a>Hozzon létre egy Azure Stack-bérlői fiókkal az Azure portal használatával
Az Azure portal használata az Azure-előfizetéssel kell rendelkeznie.

1. Jelentkezzen be a [Azure](https://portal.azure.com).
2. A Microsoft Azure bal oldali navigációs sávon kattintson **Active Directory**.
3. A könyvtár listában kattintson arra a címtárra, amelyet szeretne használni az Azure Stack, vagy hozzon létre egy újat.
4. A címtár lapján kattintson a **felhasználók**.
5. Kattintson a **Felhasználó hozzáadása** parancsra.
6. Az a **felhasználó hozzáadása** varázsló a **felhasználó típusa** menüben válassza ki **új felhasználó a munkahelyen**.
7. Az a **felhasználónév** mezőbe írja be a felhasználó nevét.
8. Az a **@** válassza ki a megfelelő bejegyzést.
9. Kattintson a Tovább nyílra.
10. Az a **felhasználói profil** lapján, adjon meg egy **Utónév**, **Vezetéknév**, és **megjelenítendő név**.
11. Az a **szerepkör** menüben válassza ki **felhasználói**.
12. Kattintson a Tovább nyílra.
13. Az a **ideiglenes jelszó beszerzése** kattintson **létrehozás**.
14. Másolás a **új jelszót**.
15. Jelentkezzen be a Microsoft Azure-az új fiókot. Módosítsa a jelszót, amikor a rendszer kéri.
16. Jelentkezzen be a `https://portal.local.azurestack.external` a bérlői portál új fiókkal.

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

