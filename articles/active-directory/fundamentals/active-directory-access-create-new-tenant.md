---
title: 'Rövid útmutató: Azure Active Directory elérése és új bérlő létrehozása | Microsoft Docs'
description: Rövid útmutató az Azure Active Directory megtalálásához és a szervezete számára új bérlő létrehozásához szükséges lépésekkel.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: lizross
custom: it-pro
ms.openlocfilehash: 8ef68c8afcf61a1a11c341a679443071aece9812
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46363003"
---
# <a name="quickstart-access-azure-active-directory-to-create-a-new-tenant"></a>Rövid útmutató: Az Azure Active Directory elérése új bérlő létrehozásához
Az Azure Active Directory (Azure AD) portált használva elvégezheti valamennyi felügyeleti feladatát, a szervezet számára új bérlő létrehozását is beleértve. 

Ebből a rövid útmutatóból megtudhatja, hogyan érheti el az Azure portált és az Azure Active Directory-t, és megtudhatja, hogyan hozhat létre alapszintű bérlőt a szervezete számára.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek
A kezdéshez a következőket kell tennie:

- Győződjön meg róla, hogy a szervezete rendelkezik érvényes Azure AD-licenccel.

- Ellenőrizze, hogy globális rendszergazda jogosultságai legyenek.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra
Jelentkezzen be az [Azure portálra](https://portal.azure.com/) globális rendszergazdai fiókkal.

![Azure portál képernyő](media/active-directory-access-create-new-tenant/azure-ad-portal.png)

## <a name="create-a-new-tenant-for-your-organization"></a>Új bérlő létrehozása a szervezetén belül
Miután bejelentkezett az Azure portálra, létrehozhat egy új bérlőt a szervezet számára. Az új bérlő a szervezetét képviseli és segíti a Microsoft felhőszolgáltatások konkrét példányának kezelését a belső és külső felhasználók számára.

### <a name="to-create-a-new-tenant"></a>Új bérlő létrehozása
1. Válassza ki az **Azure Active Directoryt**, jelölje be az **Erőforrás létrehozást**, az **Identitást**, majd válassza ki az **Azure Active Directoryt**.

    Megjelenik a **Címtár létrehozása** lap.

    ![Azure Active Directory Létrehozás oldal](media/active-directory-access-create-new-tenant/azure-ad-create-new-tenant.png)

2.  A **Címtár létrehozása** lapon adja meg a következő információkat:
    
    - Írja be, hogy _Contoso_ a **Szervezet név** mezőbe.

    - Írja be, hogy _Contoso_ a **Kezdeti tartománynév** mezőbe.

    - Hagyja meg az _Egyesült Államok_ beállítást az **Ország vagy régió** mezőben.

3. Kattintson a **Létrehozás** gombra.

Az új bérlő létrejön a contoso.onmicrosoft.com tartománnyal.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha nem fogja használni az alkalmazást, törölheti a bérlőt a következő lépésekkel:

- Válassza ki **Azure Active Directoryt**, majd a **Contoso – áttekintés** lapon a **Címtár törlése** lehetőséget.

    A bérlő és a hozzá kapcsolódó információk törlődnek.

    ![Címtár oldal létrehozása](media/active-directory-access-create-new-tenant/azure-ad-delete-new-tenant.png)

## <a name="next-steps"></a>További lépések
- Tartománynevek módosítása és továbbiak felvétele: [Egyéni tartománynév hozzáadása az Azure Active Directoryhoz](add-custom-domain.md)

- Felhasználók felvétele: [Új felhasználó hozzáadása és törlése](add-users-azure-active-directory.md)

- Csoportok és tagok hozzáadása: [Alapszintű csoport létrehozása és tagok hozzáadása](active-directory-groups-create-azure-portal.md)

- A szervezete alkalmazás- és erőforrás-hozzáférés kezelésének megkönnyítése érdekében ismerje meg a [Szerepkörön alapuló hozzáférést a Privileged Identity Management használatával](../../role-based-access-control/pim-azure-resource.md) és a [Feltételes hozzáférést](../../role-based-access-control/conditional-access-azure-management.md).
