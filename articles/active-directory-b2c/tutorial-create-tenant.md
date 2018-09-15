---
title: Oktatóanyag – Azure Active Directory B2C-bérlő létrehozása |} A Microsoft Docs
description: Ismerje meg, hogyan készíti elő a Regisztrálás az alkalmazások létrehozásával az Azure Active Directory B2C-bérlő az Azure portal használatával.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: d5831d868bec940c4e38f62e70e456ae84903ada
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45604910"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Oktatóanyag: Azure Active Directory B2C-bérlő létrehozása

Az alkalmazások az Azure Active Directory (Azure AD) B2C-vel kezelheti, mielőtt azok szerepelnie kell egy felügyelt bérlőhöz.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Azure AD B2C bérlő létrehozása
> * A bérlő az előfizetés mutató hivatkozás

Megismerheti, hogyan kell regisztrálni egy alkalmazást a következő oktatóanyagban.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-an-azure-ad-b2c-tenant"></a>Azure AD B2C bérlő létrehozása

1. Válasszon **erőforrás létrehozása** az Azure portal bal felső sarkában.
2. A keresőmezőbe az eszközlista feletti Azure Marketplace-erőforrások, keresése és kiválasztása **Active Directory B2C**, és kattintson a **létrehozás**.
3. Válasszon **hozzon létre egy új Azure AD B2C-bérlő**, adjon meg egy nevet és kezdeti tartománynév, ami a bérlő nevét, válassza ki az országot, és kattintson **létrehozás**. Győződjön meg a bérlő az ország, mivel azt később már nem módosítható.

    ![Bérlő létrehozása](./media/tutorial-create-tenant/create-tenant.png)

    Ebben a példában a bérlő neve a következő contoso0522Tenant.onmicrosoft.com

Az új bérlő kezelésének megkezdéséhez, kattintson a word **Itt** , ugyanakkor **az új címtár kezeléséhez kattintson ide,**. Látni fogja a **hibaelhárítás** üzenet arról, hogy a kell összekapcsolni az előfizetés az új bérlőhöz. 

## <a name="link-your-tenant-to-your-subscription"></a>A bérlő az előfizetés mutató hivatkozás

Az összes funkció engedélyezéséhez és a használati díjak kifizetéséhez össze kell kapcsolnia Azure-előfizetését az Azure AD B2C-bérlőjével. Ha nem a bérlő az előfizetéshez, az alkalmazások nem fog megfelelően működni.

Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása. 

![Átváltás az Azure AD B2C-bérlőre](./media/tutorial-create-tenant/switch-directories.png)

1. Válasszon **erőforrás létrehozása** az Azure portal felső bal felső sarkában.
2. A keresőmezőbe az eszközlista feletti Azure Marketplace-erőforrások, keresése és kiválasztása **Active Directory B2C**, és kattintson a **létrehozás**.
3. Válasszon **hivatkozásra egy meglévő Azure AD B2C-bérlőt szeretne aktiválni az Azure-előfizetésem**, válassza ki az Ön által létrehozott bérlőt, válassza ki az előfizetését, adja meg *myContosoTenantRG* az erőforráscsoport nevét, fogadja el a helyre, és kattintson **létrehozás**.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan lehet:

> [!div class="checklist"]
> * Azure AD B2C bérlő létrehozása
> * A bérlő az előfizetés mutató hivatkozás

> [!div class="nextstepaction"]
> [Egy webalkalmazás, fiókokkal történő hitelesítésének engedélyezése](active-directory-b2c-tutorials-web-app.md)