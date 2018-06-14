---
title: Új Azure verem bérlői fiók hozzáadása az Azure Active Directoryban |} Microsoft Docs
description: Microsoft Azure verem szoftverfejlesztői készlet való telepítése után kell legalább egy bérlői felhasználói fiók létrehozása, így megismerheti, hogy a bérlői portált.
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
ms.openlocfilehash: 590426563936c66b1353f769be138759bb53f58c
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2018
ms.locfileid: "29553205"
---
# <a name="add-a-new-azure-stack-tenant-account-in-azure-active-directory"></a>Új Azure verem bérlői fiók hozzáadása az Azure Active Directoryban
Miután [üzembe helyezése az Azure verem szoftverfejlesztői készlet](azure-stack-run-powershell-script.md), szüksége lesz egy bérlői felhasználói fiókkal, így megismerkedhet a bérlői portált, és tesztelje a ajánlatok és tervek. Létrehozhat egy bérlői fiókot által [az Azure portál használatával](#create-an-azure-stack-tenant-account-using-the-azure-portal) vagy [PowerShell-lel](#create-an-azure-stack-tenant-account-using-powershell).

## <a name="create-an-azure-stack-tenant-account-using-the-azure-portal"></a>Az Azure portál használatával Azure verem bérlői fiók létrehozása
Az Azure portál használata Azure-előfizetéssel kell rendelkeznie.

1. Jelentkezzen be [Azure](https://portal.azure.com).
2. A Microsoft Azure bal oldali navigációs sávon kattintson **Active Directory**.
3. A könyvtár listában kattintson arra a címtárra, amely Azure verem használni kívánt, vagy hozzon létre egy újat.
4. A címtár lapján kattintson a **felhasználók**.
5. Kattintson a **felhasználó hozzáadása**.
6. Az a **felhasználó hozzáadása** varázslót, a a **felhasználó típusa** menüben válassza ki **a szervezet új felhasználó**.
7. Az a **felhasználónév** mezőbe írja be a felhasználó nevét.
8. Az a  **@**  válassza ki a megfelelő bejegyzés.
9. Kattintson a Tovább nyílra.
10. Az a **felhasználói profil** a varázsló, adjon meg egy **Utónév**, **Vezetéknév**, és **megjelenített név**.
11. Az a **szerepkör** menüben válassza ki **felhasználói**.
12. Kattintson a Tovább nyílra.
13. Az a **ideiglenes jelszó beszerzése** kattintson **létrehozása**.
14. Másolás a **új jelszó**.
15. Jelentkezzen be a Microsoft Azure az új fiók. Módosítsa a jelszót, amikor a rendszer kéri.
16. Jelentkezzen be `https://portal.local.azurestack.external` az új fiók a bérlői portál megjelenítéséhez.

## <a name="create-an-azure-stack-tenant-account-using-powershell"></a>PowerShell-lel Azure verem bérlői fiók létrehozása
Ha nem rendelkezik Azure-előfizetéssel, bérlői felhasználói fiók hozzáadása az Azure-portál nem használhat. Ebben az esetben használhatja az Active Directory modul Windows Powershellhez készült Azure helyette.

> [!NOTE]
> Ha Azure verem szoftverfejlesztői készlet telepítése a Microsoft Account (Live ID) segítségével végzi, a AAD PowerShell bérlői fiók létrehozása nem használhat. 
> 
> 

1. Telepítse a [a Microsoft Online Services – bejelentkezési segéd az informatikai szakemberek számára készült RTW](https://www.microsoft.com/en-us/download/details.aspx?id=41950).
2. Telepítse a [Active Directory modul Windows Powershellhez készült Azure (64 bites változat)](http://go.microsoft.com/fwlink/p/?linkid=236297) , majd nyissa meg.
3. A következő parancsmagok futtatásához:

    ```powershell
    # Provide the AAD credential you use to deploy Azure Stack Development Kit

            $msolcred = get-credential

    # Add a tenant account "Tenant Admin <username>@<yourdomainname>" with the initial password "<password>".

            connect-msolservice -credential $msolcred
            $user = new-msoluser -DisplayName "Tenant Admin" -UserPrincipalName <username>@<yourdomainname> -Password <password>
            Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType User -RoleMemberObjectId $user.ObjectId

    ```

1. Jelentkezzen be az új fiókot a Microsoft Azure. Módosítsa a jelszót, amikor a rendszer kéri.
2. Jelentkezzen be `https://portal.local.azurestack.external` az új fiók a bérlői portál megjelenítéséhez.

