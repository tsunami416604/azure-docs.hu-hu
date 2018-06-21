---
title: Útmutató – Azure Active Directory B2C-bérlő létrehozása |} Microsoft Docs
description: Ismerje meg, hogyan készíti elő az alkalmazásokat az Azure portál használata az Azure Active Directory B2C-bérlő létrehozásával a regisztrációhoz.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 06/19/2018
ms.author: davidmu
ms.openlocfilehash: 04f3dbbe461bfe0f07b6930a92bdd8a721e55098
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36296098"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Oktatóanyag: Azure Active Directory B2C-bérlő létrehozása

Az alkalmazások és az Azure Active Directory (Azure AD) B2C kommunikációját, mielőtt azok szerepelnie kell egy bérlőt, amelyeket Ön kezel.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Azure AD B2C bérlő létrehozása
> * A bérlő csatolása az előfizetéshez

Megismerheti, hogyan lehet egy alkalmazást regisztrálni a következő oktatóanyag.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

## <a name="create-an-azure-ad-b2c-tenant"></a>Azure AD B2C bérlő létrehozása

1. Válasszon **hozzon létre egy erőforrást** az Azure portál bal felső sarkában.
2. A keresési mezőbe a lista fölött Azure piactér erőforrások, és adja meg **Active Directory B2C**, és kattintson a **létrehozása**.
3. Válasszon **hozzon létre egy új Azure AD B2C-bérlő**, adjon meg egy nevet és a kezdeti tartomány nevét, amely a bérlő nevét, válassza ki az országot, és kattintson **létrehozása**. Lehet, hogy a bérlő az ország, mert azt később már nem módosítható.

    ![Bérlő létrehozása](./media/tutorial-create-tenant/create-tenant.png)

    Ebben a példában a bérlő neve contoso0522Tenant.onmicrosoft.com:

Az új bérlő kezelésének megkezdéséhez kattintson a word **Itt** ahol felirat látható **új címtárcsoportok kezeléséhez kattintson ide,**. Látni fogja a **kapcsolatos problémák elhárítása** üzenetre adott válaszként, össze kell kapcsolni az előfizetés az új bérlőhöz. 

## <a name="link-your-tenant-to-your-subscription"></a>A bérlő csatolása az előfizetéshez

Az Azure AD B2C-bérlő csatolása az Azure-előfizetéshez minden funkció engedélyezéséhez és a használati díjat kell fizetnie kell. A bérlő nem kapcsolódik az előfizetéshez, az alkalmazások nem fognak megfelelően működni.

1. Győződjön meg arról, hogy az előfizetés szeretne hozzárendelni az új bérlőhöz a címtár az Azure-portál jobb felső sarkában váltásával tartalmazó könyvtár használata.

    ![Könyvtárváltás](./media/tutorial-create-tenant/switch-directories.png)

    És jelölje be az előfizetés tartalmazó könyvtárat.

    ![Könyvtár kijelölése](./media/tutorial-create-tenant/select-directory.png)

2. Válasszon **hozzon létre egy erőforrást** az Azure portál felső bal felső sarokban.
3. A keresési mezőbe a lista fölött Azure piactér erőforrások, és adja meg **Active Directory B2C**, és kattintson a **létrehozása**.
4. Válasszon **kapcsolat egy meglévő Azure AD B2C bérlő számára az Azure-előfizetésem**, válassza ki a létrehozott bérlő, jelölje ki az előfizetését, adja meg *myContosoTenantRG* az erőforráscsoport neve, fogadja el a hely, és kattintson **létrehozása**.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan:

> [!div class="checklist"]
> * Azure AD B2C bérlő létrehozása
> * A bérlő csatolása az előfizetéshez

> [!div class="nextstepaction"]
> [Egy webes alkalmazás való hitelesítéshez szükséges fiókok engedélyezése](active-directory-b2c-tutorials-web-app.md)