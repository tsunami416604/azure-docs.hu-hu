---
title: Oktatóanyag – Azure Active Directory B2C bérlő létrehozása
description: Megtudhatja, hogyan készítheti elő az alkalmazások regisztrálását úgy, hogy létrehoz egy Azure Active Directory B2C bérlőt a Azure Portal használatával.
services: B2C
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/07/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ce389d1f434fb0eb37413873b02e3ddfff8f7fba
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849395"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Oktatóanyag: Azure Active Directory B2C bérlő létrehozása

Az alkalmazások a Azure Active Directory (Azure AD) B2C-vel való interakció előtt regisztrálni kell őket egy Ön által kezelt bérlőben.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Azure AD B2C bérlő létrehozása
> * Bérlő összekapcsolása az előfizetéssel

Megtudhatja, hogyan regisztrálhat egy alkalmazást a következő oktatóanyagban.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-an-azure-ad-b2c-tenant"></a>Azure AD B2C bérlő létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Győződjön meg arról, hogy az előfizetést tartalmazó könyvtárat használja. Kattintson a **könyvtár és előfizetés szűrőre** a felső menüben, majd válassza ki az előfizetést tartalmazó könyvtárat. Ez a könyvtár különbözik a Azure AD B2C bérlőt tartalmazó mappától.

    ![Címtár-és előfizetés-szűrő a kiválasztott előfizetési Bérlővel](./media/tutorial-create-tenant/switch-directory-subscription.PNG)

3. Válassza az **erőforrás létrehozása** lehetőséget a Azure Portal bal felső sarkában.
4. Keresse meg és válassza ki **Active Directory B2C**, majd kattintson a **Létrehozás**gombra.
5. Válassza az **új Azure ad B2C bérlő létrehozása** elemet, és adja meg a szervezet nevét és a kezdeti tartománynevet. Válassza ki az országot/régiót (ez később nem módosítható), majd kattintson a **Létrehozás**gombra.

    A kezdeti tartománynév a bérlő nevének részeként van használatban. Ebben a példában a bérlő neve *contoso0926Tenant.onmicrosoft.com*:

    ![A B2C-bérlő létrehozási lapja a Azure Portal](./media/tutorial-create-tenant/create-tenant.PNG)

6. Az **új B2C-bérlő létrehozása vagy a meglévő bérlő hivatkozása** lapon válassza a **meglévő Azure ad B2C bérlő összekapcsolása az Azure-** előfizetéssel lehetőséget.

    Válassza ki a létrehozott bérlőt, és válassza ki az előfizetését.

    Az erőforráscsoport területen válassza az **új létrehozása**lehetőséget. Adja meg a bérlőt tartalmazó erőforráscsoport nevét, válassza ki a helyet, majd kattintson a **Létrehozás**gombra.
1. Az új bérlő használatának megkezdéséhez győződjön meg arról, hogy a felső menüben található **címtár és előfizetés szűrőre** kattint, és kiválasztja az azt tartalmazó könyvtárat, és a Azure ad B2C bérlőjét tartalmazó könyvtárat használja.

    Ha először nem látja az új Azure B2C-bérlőt a listában, frissítse a böngészőablakot, majd a felső menüben válassza újra a **címtár-és előfizetés-szűrőt** .

    ![Címtár-és előfizetés-szűrő a B2C-bérlő kiválasztásával](./media/tutorial-create-tenant/switch-directories.PNG)

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan végezheti el a következőket:

> [!div class="checklist"]
> * Azure AD B2C bérlő létrehozása
> * Bérlő összekapcsolása az előfizetéssel

Következő lépésként megtudhatja, hogyan regisztrálhat egy webalkalmazást az új bérlőbe.

> [!div class="nextstepaction"]
> [Alkalmazások regisztrálása >](tutorial-register-applications.md)
