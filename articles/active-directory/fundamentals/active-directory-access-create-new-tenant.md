---
title: Rövid útmutató – Az Access & új bérlő létrehozása - Azure AD
description: Az Azure Active Directory megkeresésére és a szervezet új bérlőjének létrehozására vonatkozó utasítások.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f29d103ce1be426fb0b5c462cc1d831fefe87b6
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80049994"
---
# <a name="quickstart-create-a-new-tenant-in-azure-active-directory"></a>Rövid útmutató: Új bérlő létrehozása az Azure Active Directoryban
Az Azure Active Directory (Azure AD) portált használva elvégezheti valamennyi felügyeleti feladatát, a szervezet számára új bérlő létrehozását is beleértve. 

Ebből a rövid útmutatóból megtudhatja, hogyan érheti el az Azure portált és az Azure Active Directory-t, és megtudhatja, hogyan hozhat létre alapszintű bérlőt a szervezete számára.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="create-a-new-tenant-for-your-organization"></a>Új bérlő létrehozása a szervezetén belül
Miután bejelentkezett az Azure portálra, létrehozhat egy új bérlőt a szervezet számára. Az új bérlő a szervezetét képviseli és segíti a Microsoft felhőszolgáltatások konkrét példányának kezelését a belső és külső felhasználók számára.

### <a name="to-create-a-new-tenant"></a>Új bérlő létrehozása

1. Jelentkezzen be a szervezet [Azure-portáljára.](https://portal.azure.com/)

1. Az Azure Portal menüjében válassza az **Erőforrás létrehozása** elemet.  

    ![Az Azure Active Directory létrehozása lap](media/active-directory-access-create-new-tenant/azure-ad-portal.png)

1. Válassza **az Identitás**lehetőséget, majd az Azure Active **Directory**lehetőséget.

    Megjelenik a **Címtár létrehozása** lap.

    ![Azure Active Directory Létrehozás oldal](media/active-directory-access-create-new-tenant/azure-ad-create-new-tenant.png)

1.  A **Címtár létrehozása** lapon adja meg a következő információkat:
    
    - Írja be, hogy _Contoso_ a **Szervezet név** mezőbe.

    - Írja be, hogy _Contoso_ a **Kezdeti tartománynév** mezőbe.

    - Hagyja meg az _Egyesült Államok_ beállítást az **Ország vagy régió** mezőben.

1. Kattintson a **Létrehozás** gombra.

Az új bérlő létrejön a contoso.onmicrosoft.com tartománnyal.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha nem fogja tovább használni ezt az alkalmazást, a következő lépésekkel törölheti a bérlőt:

- Győződjön meg arról, hogy be van jelentkezve a címtárba, amelyet törölni szeretne az Azure Portal **címtár + előfizetésszűrőn** keresztül, és szükség esetén átvált a célkönyvtárra.
- Válassza ki **Azure Active Directoryt**, majd a **Contoso – áttekintés** lapon a **Címtár törlése** lehetőséget.

    A bérlő és a hozzá kapcsolódó információk törlődnek.

    ![Áttekintő lap a kijelölt Könyvtár törlése gombbal](media/active-directory-access-create-new-tenant/azure-ad-delete-new-tenant.png)

## <a name="next-steps"></a>További lépések
- Tartománynevek módosítása és továbbiak felvétele: [Egyéni tartománynév hozzáadása az Azure Active Directoryhoz](add-custom-domain.md)

- Felhasználók felvétele: [Új felhasználó hozzáadása és törlése](add-users-azure-active-directory.md)

- Csoportok és tagok hozzáadása: [Alapszintű csoport létrehozása és tagok hozzáadása](active-directory-groups-create-azure-portal.md)

- Ismerje meg [a szerepköralapú hozzáférést a Kiemelt identitáskezelés](../../role-based-access-control/pim-azure-resource.md) és [a feltételes hozzáférés](../../role-based-access-control/conditional-access-azure-management.md) használatával a szervezet alkalmazás- és erőforrás-hozzáférésének kezeléséhez.

- Tudnivalók az Azure Active Directoryról, beleértve [az alapszintű licenceléssel, a terminológiával és a társított szolgáltatásokkal](active-directory-whatis.md) kapcsolatos információkat.
