---
title: Oktatóanyag – Azure Active Directory B2C-bérlő létrehozása
description: Ismerje meg, hogyan készíti elő a Regisztrálás az alkalmazások létrehozásával az Azure Active Directory B2C-bérlő az Azure portal használatával.
services: B2C
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/07/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 711b9152f9f3fa1b3573e39d1950f18b628c268a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056319"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Oktatóanyag: Az Azure Active Directory B2C-bérlő létrehozása

Az alkalmazások az Azure Active Directory (Azure AD) B2C-vel kezelheti, mielőtt azok szerepelnie kell egy felügyelt bérlőhöz.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Azure AD B2C bérlő létrehozása
> * A bérlő az előfizetés mutató hivatkozás

Megismerheti, hogyan kell regisztrálni egy alkalmazást a következő oktatóanyagban.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-an-azure-ad-b2c-tenant"></a>Azure AD B2C bérlő létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Győződjön meg arról, hogy használja a könyvtárat, amely tartalmazza az előfizetés. Kattintson a **címtár és előfizetés-szűrő** a felső menüben, majd válassza ki a címtárat, amely tartalmazza az előfizetés. Ez a könyvtár eltér, amely tartalmazza az Azure AD B2C-bérlőben.

    ![Váltás előfizetési könyvtár](./media/tutorial-create-tenant/switch-directory-subscription.PNG)

3. Válasszon **erőforrás létrehozása** az Azure portal bal felső sarkában.
4. Keresse meg és válassza **Active Directory B2C**, és kattintson a **létrehozás**.
5. Válasszon **hozzon létre egy új Azure AD B2C-bérlő** , és adja meg egy nevet és kezdeti tartománynév. Válassza ki az országot vagy régiót (azt később már nem módosítható), és kattintson a **létrehozás**.

    A kezdeti tartománynév a bérlő nevének részeként használatos. Ebben a példában a bérlő neve a következő *contoso0926Tenant.onmicrosoft.com*:

    ![Bérlő létrehozása](./media/tutorial-create-tenant/create-tenant.PNG)

6. Az a **új B2C-bérlő létrehozása vagy összekapcsolás meglévő Bérlővel** lapon a **hivatkozásra egy meglévő Azure AD B2C-bérlőt szeretne aktiválni az Azure-előfizetésem**.

    Válassza ki a létrehozott bérlő, és válassza ki az előfizetését.

    Válassza ki az erőforráscsoportban **új létrehozása**. Adjon meg egy nevet az erőforráscsoport, amely tartalmazni fogja a bérlő, válassza ki azt a helyet, majd kattintson a **létrehozás**.
1. Az új bérlő használatának megkezdéséhez, győződjön meg arról, a címtár, amely tartalmazza az Azure AD B2C-bérlő kattintva használja-e a **címtár és előfizetés-szűrő** a felső menüben, és az azt tartalmazó könyvtár kiválasztása.

    Ha először az új Azure B2C-bérlő a listában nem látható, frissítse a böngészőt, majd válassza a **címtár és előfizetés-szűrő** múlva a felső menüben.

    ![Váltás a directory-bérlőben](./media/tutorial-create-tenant/switch-directories.PNG)

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan lehet:

> [!div class="checklist"]
> * Azure AD B2C bérlő létrehozása
> * A bérlő az előfizetés mutató hivatkozás

Ezután megtudhatja, hogyan egy webes alkalmazás regisztrálása az új bérlőben.

> [!div class="nextstepaction"]
> [Az alkalmazások regisztrálása >](tutorial-register-applications.md)
