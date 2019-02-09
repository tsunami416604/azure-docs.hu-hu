---
title: Rövid útmutató eléréséhez, és hozzon létre egy új bérlőt – Azure Active Directory |} A Microsoft Docs
description: Azure Active Directoryban található, illetve hogyan hozhat létre egy új bérlőt a szervezetén belül kapcsolatos utasításokat.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: lizross
ms.custom: it-pro, seodec18
ms.openlocfilehash: 88efa25e09cf6034e9320569358488d9aebb7ad6
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55958439"
---
# <a name="quickstart-create-a-new-tenant-in-azure-active-directory"></a>Gyors útmutató: Hozzon létre egy új bérlőt az Azure Active Directoryban
Az Azure Active Directory (Azure AD) portált használva elvégezheti valamennyi felügyeleti feladatát, a szervezet számára új bérlő létrehozását is beleértve. 

Ebből a rövid útmutatóból megtudhatja, hogyan érheti el az Azure portált és az Azure Active Directory-t, és megtudhatja, hogyan hozhat létre alapszintű bérlőt a szervezete számára.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra
Jelentkezzen be az [Azure portálra](https://portal.azure.com/) globális rendszergazdai fiókkal.

![Azure portál képernyő](media/active-directory-access-create-new-tenant/azure-ad-portal.png)

## <a name="create-a-new-tenant-for-your-organization"></a>Új bérlő létrehozása a szervezetén belül
Miután bejelentkezett az Azure portálra, létrehozhat egy új bérlőt a szervezet számára. Az új bérlő a szervezetét képviseli és segíti a Microsoft felhőszolgáltatások konkrét példányának kezelését a belső és külső felhasználók számára.

### <a name="to-create-a-new-tenant"></a>Új bérlő létrehozása
1. Válassza ki **erőforrás létrehozása**válassza **identitás**, majd válassza ki **Azure Active Directory**.

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

- Tudnivalók az Azure Active Directoryról, beleértve [az alapszintű licenceléssel, a terminológiával és a társított szolgáltatásokkal](active-directory-whatis.md) kapcsolatos információkat.
