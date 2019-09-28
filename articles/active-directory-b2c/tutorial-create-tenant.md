---
title: Oktatóanyag – Azure Active Directory B2C bérlő létrehozása
description: Megtudhatja, hogyan készítheti elő az alkalmazások regisztrálását úgy, hogy létrehoz egy Azure Active Directory B2C bérlőt a Azure Portal használatával.
services: B2C
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: de29929c8fda476fe276f91d4a68ce4d632503fd
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71345211"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Oktatóanyag: Azure Active Directory B2C bérlő létrehozása

Az alkalmazások Azure Active Directory B2C (Azure AD B2C) való interakció előtt regisztrálni kell őket egy Ön által kezelt bérlőben.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Azure AD B2C bérlő létrehozása
> * Bérlő összekapcsolása az előfizetéssel
> * Váltson a Azure AD B2C bérlőt tartalmazó könyvtárra
> * Adja hozzá *kedvencként* a Azure ad B2C-erőforrást a Azure Portal

Megtudhatja, hogyan regisztrálhat egy alkalmazást a következő oktatóanyagban.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-an-azure-ad-b2c-tenant"></a>Azure AD B2C bérlő létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Győződjön meg arról, hogy az előfizetést tartalmazó könyvtárat használja.

    Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, majd válassza ki az előfizetést tartalmazó könyvtárat. Ez a könyvtár különbözik a Azure AD B2C bérlőt tartalmazó mappától.

    ![Címtár + előfizetés-szűrő és előfizetési bérlő kiválasztva](media/tutorial-create-tenant/portal-01-select-directory.png)

1. Válassza az **erőforrás létrehozása** lehetőséget a Azure Portal bal felső sarkában.
1. Keresse meg és válassza ki **Active Directory B2C**, majd válassza a **Létrehozás**lehetőséget.
1. Válassza az **Új Azure AD B2C-bérlő létrehozása** lehetőséget.

    ![Új Azure AD B2C bérlő létrehozása Azure Portal](media/tutorial-create-tenant/portal-02-create-tenant.png)

1. Adja meg a **szervezet nevét** és a **kezdeti tartománynevet**. Válassza ki az **országot vagy régiót** (ez később nem módosítható), majd válassza a **Létrehozás**lehetőséget.

    A tartománynév a teljes bérlői tartománynév részeként használatos. Ebben a példában a bérlő neve *contosob2c.onmicrosoft.com*:

    ![Bérlői űrlap létrehozása a példákban szereplő értékekkel Azure Portal](media/tutorial-create-tenant/portal-03-tenant-naming.png)

1. Miután befejeződött a bérlő létrehozása, válassza az **új B2C-bérlő létrehozása vagy** a bérlői Létrehozás lap tetején található meglévő bérlő hivatkozásra mutató hivatkozást.

    ![A bérlői navigációs hivatkozás kiemelése Azure Portal](media/tutorial-create-tenant/portal-04-select-link-sub-link.png)

1. Válassza a **meglévő Azure ad B2C bérlő összekapcsolása az Azure-előfizetéssel**lehetőséget.

   ![Meglévő előfizetés összekapcsolása Azure Portal](media/tutorial-create-tenant/portal-05-link-subscription.png)

1. Válassza ki a létrehozott **Azure ad B2C bérlőt** , majd válassza ki az **előfizetését**.

    Az **Erőforráscsoport** esetében válassza az **Új létrehozása** lehetőséget. Adja meg a bérlőt tartalmazó erőforráscsoport **nevét** , válassza ki az **erőforráscsoport helyét**, majd válassza a **Létrehozás**lehetőséget.

    ![Az előfizetési beállítások űrlapjának csatolása Azure Portal](media/tutorial-create-tenant/portal-06-link-subscription-settings.png)

## <a name="select-your-b2c-tenant-directory"></a>A B2C-bérlő címtárának kiválasztása

Az új Azure AD B2C bérlő használatának megkezdéséhez át kell váltania a bérlőt tartalmazó könyvtárba.

Válassza ki a **könyvtár + előfizetés** szűrőt a Azure Portal felső menüjében, majd válassza ki azt a könyvtárat, amely tartalmazza a Azure ad B2C bérlőt.

Ha először nem látja az új Azure B2C-bérlőt a listában, frissítse a böngészőablakot, majd a felső menüben válassza újra a **címtár + előfizetés** szűrőt.

![B2C-bérlő – a Azure Portal kiválasztott könyvtárat tartalmazza](media/tutorial-create-tenant/portal-07-select-tenant-directory.png)

## <a name="add-azure-ad-b2c-as-a-favorite-optional"></a>Azure AD B2C hozzáadása kedvencként (nem kötelező)

Ez a választható lépés megkönnyíti a Azure AD B2C bérlő kiválasztását a következő és az összes további oktatóanyagban.

Ahelyett, hogy minden egyes **szolgáltatásban** a "Azure ad B2C" kifejezésre lenne szükség, amikor a Bérlővel szeretne dolgozni, kedvencként használhatja az erőforrást. Ezután kiválaszthatja a bal oldali **Kedvencek** menüből, és gyorsan megkeresheti Azure ad B2C bérlőjét.

Ezt a műveletet csak egyszer kell végrehajtania. Mielőtt elvégzi ezeket a lépéseket, győződjön meg arról, hogy az előző szakaszban leírtak szerint átváltotta a Azure AD B2C bérlőt tartalmazó könyvtárba, [majd válassza ki a B2C-bérlői címtárat](#select-your-b2c-tenant-directory).

1. A [Azure Portal](https://portal.azure.com) bal oldali menüjében válassza az **összes szolgáltatás** elemet
1. Írja be a *Azure ad B2C* kifejezést a keresés szövegmezőbe.
1. Válassza ki a **csillagot** , Azure ad B2C hozzáadni a kedvencekhez
1. *Azure ad B2C* most megjelenik a bal oldali **Kedvencek** menüben. Az alábbi képen látható módon kiválaszthatja és áthúzhatja azt a listában, ha úgy tetszik:

![Azure AD B2C hozzáadásának lépései kedvencként a Azure Portal](media/tutorial-create-tenant/portal-08-favorite-b2c.png)

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan végezheti el a következőket:

> [!div class="checklist"]
> * Azure AD B2C bérlő létrehozása
> * Bérlő összekapcsolása az előfizetéssel
> * Váltson a Azure AD B2C bérlőt tartalmazó könyvtárra
> * Adja hozzá *kedvencként* a Azure ad B2C-erőforrást a Azure Portal

Következő lépésként megtudhatja, hogyan regisztrálhat egy webalkalmazást az új bérlőbe.

> [!div class="nextstepaction"]
> [Alkalmazások regisztrálása >](tutorial-register-applications.md)
