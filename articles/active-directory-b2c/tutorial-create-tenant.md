---
title: Oktatóanyag – Azure Active Directory B2C bérlő létrehozása
description: Megtudhatja, hogyan készítheti elő az alkalmazások regisztrálását úgy, hogy létrehoz egy Azure Active Directory B2C bérlőt a Azure Portal használatával.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 807cc4647a27cf62109885ba1adaeaf5058a20c4
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2020
ms.locfileid: "85355420"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Oktatóanyag: Azure Active Directory B2C-bérlő létrehozása

Az alkalmazások Azure Active Directory B2C (Azure AD B2C) való interakció előtt regisztrálni kell őket egy Ön által kezelt bérlőben.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Azure AD B2C bérlő létrehozása
> * Bérlő összekapcsolása az előfizetéssel
> * Váltson a Azure AD B2C bérlőt tartalmazó könyvtárra
> * Adja hozzá **kedvencként** a Azure ad B2C-erőforrást a Azure Portal

Megtudhatja, hogyan regisztrálhat egy alkalmazást a következő oktatóanyagban.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-azure-ad-b2c-tenant"></a>Azure AD B2C bérlő létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/). Jelentkezzen be egy olyan Azure-fiókkal, amelyet legalább a [közreműködő](../role-based-access-control/built-in-roles.md) szerepkörhöz rendeltek az előfizetésben vagy az előfizetésben található erőforráscsoporthoz.

1. Válassza ki az előfizetést tartalmazó könyvtárat.

    A Azure Portal eszköztáron válassza ki a **címtár + előfizetés** ikont, majd válassza ki az előfizetést tartalmazó könyvtárat. Ez a könyvtár különbözik a Azure AD B2C bérlőt tartalmazó mappától.

    ![Előfizetés bérlője, könyvtár + előfizetés-szűrő és előfizetés-bérlő kiválasztva](media/tutorial-create-tenant/portal-01-pick-directory.png)

1. Az Azure Portal menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet.
1. Keresse meg **Azure Active Directory B2C**, majd válassza a **Létrehozás**lehetőséget.
1. Válassza az **Új Azure AD B2C-bérlő létrehozása** lehetőséget.

    ![Új Azure AD B2C bérlő létrehozása Azure Portal](media/tutorial-create-tenant/portal-02-create-tenant.png)

1. A **címtár létrehozása** lapon adja meg a következőket:

   - **Szervezet neve** – adja meg a szervezet nevét.
   - **Kezdeti tartománynév** – adja meg a tartománynevet. Alapértelmezés szerint ez a név a *. onmicrosoft.com*utótaggal van hozzáfűzve. Ezt később is megváltoztathatja, ha hozzáad egy olyan tartománynevet, amelyet a szervezet már használ, például: "contoso.com".
   - **Ország vagy régió** – válassza ki az országot vagy régiót a listából. Ezt a kijelölést később nem lehet módosítani.
   - **Előfizetés** – válassza ki az előfizetését a listából.
   - **Erőforráscsoport** – válasszon ki egy erőforráscsoportot, amely a bérlőt fogja tartalmazni. Vagy válassza az **új létrehozása**lehetőséget, adja meg az erőforráscsoport **nevét** , válassza ki az **erőforráscsoport helyét**, majd kattintson **az OK gombra**.

    ![Bérlői űrlap létrehozása a példákban szereplő értékekkel Azure Portal](media/tutorial-create-tenant/review-and-create-tenant.png)

1. Válassza az **Áttekintés + létrehozás** lehetőséget.
1. Tekintse át a címtár beállításait. Ezután kattintson a **Létrehozás** elemre.

Egy Azure-előfizetéshez több Azure AD B2C bérlőt is csatolhat számlázási célokra. Bérlő összekapcsolásához rendszergazdai jogosultsággal kell rendelkeznie a Azure AD B2C bérlőben, és az Azure-előfizetésben legalább egy közreműködő szerepkört hozzá kell rendelni. Lásd: [Azure ad B2C bérlő összekapcsolása egy előfizetéssel](billing.md#link-an-azure-ad-b2c-tenant-to-a-subscription).

## <a name="select-your-b2c-tenant-directory"></a>A B2C-bérlő címtárának kiválasztása

Az új Azure AD B2C bérlő használatának megkezdéséhez át kell váltania a bérlőt tartalmazó könyvtárba.

Válassza ki a **könyvtár + előfizetés** szűrőt a Azure Portal felső menüjében, majd válassza ki azt a könyvtárat, amely tartalmazza a Azure ad B2C bérlőt.

Ha először nem látja az új Azure B2C-bérlőt a listában, frissítse a böngészőablakot, majd a felső menüben válassza újra a **címtár + előfizetés** szűrőt.

![B2C-bérlő – a Azure Portal kiválasztott könyvtárat tartalmazza](media/tutorial-create-tenant/portal-07-select-tenant-directory.png)

## <a name="add-azure-ad-b2c-as-a-favorite-optional"></a>Azure AD B2C hozzáadása kedvencként (nem kötelező)

Ez a választható lépés megkönnyíti a Azure AD B2C bérlő kiválasztását a következő és az összes további oktatóanyagban.

Ahelyett, hogy minden alkalommal, amikor a Bérlővel dolgozni szeretne, az **összes szolgáltatás** *Azure ad B2C* keresése helyett kedvencként használhatja az erőforrást. Ezután kiválaszthatja a portál **Kedvencek** szakaszának segítségével, és gyorsan megkeresheti Azure ad B2C bérlőjét.

Ezt a műveletet csak egyszer kell végrehajtania. Mielőtt elvégzi ezeket a lépéseket, győződjön meg arról, hogy az előző szakaszban leírtak szerint átváltotta a Azure AD B2C bérlőt tartalmazó könyvtárba, [majd válassza ki a B2C-bérlői címtárat](#select-your-b2c-tenant-directory).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A Azure Portal menüben válassza a **minden szolgáltatás**lehetőséget.
1. A **minden szolgáltatás** keresése mezőben keresse meg **Azure ad B2C**, vigye a kurzort a keresési eredmények fölé, majd válassza ki a csillag ikont az elemleírásban. **Azure ad B2C** most megjelenik a **kedvencek**területen található Azure Portal.
1. Ha módosítani szeretné az új kedvenc pozícióját, lépjen a Azure Portal menüre, válassza a **Azure ad B2C**lehetőséget, majd húzza a kívánt helyzetbe.

    ![Azure AD B2C, Kedvencek menü, Microsoft Azure Portal](media/tutorial-create-tenant/portal-08-b2c-favorite.png)

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan végezheti el a következőket:

> [!div class="checklist"]
> * Azure AD B2C bérlő létrehozása
> * Bérlő összekapcsolása az előfizetéssel
> * Váltson a Azure AD B2C bérlőt tartalmazó könyvtárra
> * Adja hozzá **kedvencként** a Azure ad B2C-erőforrást a Azure Portal

Következő lépésként megtudhatja, hogyan regisztrálhat egy webalkalmazást az új bérlőbe.

> [!div class="nextstepaction"]
> [Alkalmazások regisztrálása >](tutorial-register-applications.md)
