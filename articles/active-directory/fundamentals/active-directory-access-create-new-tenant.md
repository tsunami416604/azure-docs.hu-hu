---
title: Gyors útmutató – hozzáférés & új bérlő létrehozása – Azure AD
description: Útmutatás a Azure Active Directory megkereséséhez és új bérlő létrehozásához a szervezet számára.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: ajburnle
ms.custom: fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: de205beaf9800c2061cc41343b4153bd0e2b5dc7
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024602"
---
# <a name="quickstart-create-a-new-tenant-in-azure-active-directory"></a>Gyors útmutató: új bérlő létrehozása Azure Active Directory
Az Azure Active Directory (Azure AD) portált használva elvégezheti valamennyi felügyeleti feladatát, a szervezet számára új bérlő létrehozását is beleértve. 

Ebből a rövid útmutatóból megtudhatja, hogyan érheti el az Azure portált és az Azure Active Directory-t, és megtudhatja, hogyan hozhat létre alapszintű bérlőt a szervezete számára.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-a-new-tenant-for-your-organization"></a>Új bérlő létrehozása a szervezetén belül
Miután bejelentkezett az Azure portálra, létrehozhat egy új bérlőt a szervezet számára. Az új bérlő a szervezetét képviseli és segíti a Microsoft felhőszolgáltatások konkrét példányának kezelését a belső és külső felhasználók számára.

### <a name="to-create-a-new-tenant"></a>Új bérlő létrehozása

1. Jelentkezzen be a szervezet [Azure Portalba](https://portal.azure.com/).

1. A Azure Portal menüben válassza az **erőforrás létrehozása**lehetőséget.  

    ![Azure Active Directory resoure lap létrehozása](media/active-directory-access-create-new-tenant/azure-ad-portal.png)

1. Válassza az **identitás**lehetőséget, majd válassza a **Azure Active Directory**lehetőséget.

    Megjelenik a **Címtár létrehozása** lap.

    ![Azure Active Directory Létrehozás oldal](media/active-directory-access-create-new-tenant/azure-ad-create-new-tenant.png)

1.  A **Címtár létrehozása** lapon adja meg a következő információkat:
    
    - Írja be, hogy _Contoso_ a **Szervezet név** mezőbe.

    - Írja be, hogy _Contoso_ a **Kezdeti tartománynév** mezőbe.

    - Hagyja meg az _Egyesült Államok_ beállítást az **Ország vagy régió** mezőben.

1. Kattintson a **Létrehozás** gombra.

Az új bérlő létrejön a contoso.onmicrosoft.com tartománnyal.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha nem fogja használni az alkalmazást, törölheti a bérlőt a következő lépésekkel:

- Győződjön meg arról, hogy be van jelentkezve a törölni kívánt címtárba az Azure Portal **címtár + előfizetés** szűrője segítségével, és szükség esetén váltson a célhelyre.
- Válassza ki **Azure Active Directoryt**, majd a **Contoso – áttekintés** lapon a **Címtár törlése** lehetőséget.

    A bérlő és a hozzá kapcsolódó információk törlődnek.

    ![Áttekintő lap, a kijelölt könyvtár törlése gombbal](media/active-directory-access-create-new-tenant/azure-ad-delete-new-tenant.png)

## <a name="next-steps"></a>Következő lépések
- Tartománynevek módosítása és továbbiak felvétele: [Egyéni tartománynév hozzáadása az Azure Active Directoryhoz](add-custom-domain.md)

- Felhasználók felvétele: [Új felhasználó hozzáadása és törlése](add-users-azure-active-directory.md)

- Csoportok és tagok hozzáadása: [Alapszintű csoport létrehozása és tagok hozzáadása](active-directory-groups-create-azure-portal.md)

- Tudnivalók a [szerepköralapú hozzáférésről Privileged Identity Management](../../role-based-access-control/pim-azure-resource.md) és [feltételes hozzáférés](../../role-based-access-control/conditional-access-azure-management.md) használatával a szervezet alkalmazás-és erőforrás-hozzáférésének kezeléséhez.

- Tudnivalók az Azure Active Directoryról, beleértve [az alapszintű licenceléssel, a terminológiával és a társított szolgáltatásokkal](active-directory-whatis.md) kapcsolatos információkat.
