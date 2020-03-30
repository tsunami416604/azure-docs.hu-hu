---
title: Oktatóanyag – Hozzon létre egy Azure Active Directory B2C-bérlőt
description: Ismerje meg, hogyan készülhet fel az alkalmazások regisztrálására egy Azure Active Directory B2C-bérlő használatával az Azure Portalon.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c31f3c4c6688af7d2142180e8d9b7100965bad96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186403"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Oktatóanyag: Azure Active Directory B2C-bérlő létrehozása

Ahhoz, hogy az alkalmazások kommunikálhassanak az Azure Active Directory B2C (Azure AD B2C) használatával, regisztrálnikell őket egy ön által kezelt bérlőben.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Azure AD B2C bérlő létrehozása
> * A bérlő összekapcsolása az előfizetéssel
> * Váltás az Azure AD B2C-bérlőt tartalmazó könyvtárra
> * Az Azure AD B2C-erőforrás **hozzáadása kedvencként** az Azure Portalon

Megtudhatja, hogyan regisztrálhat egy alkalmazást a következő oktatóanyagban.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="create-an-azure-ad-b2c-tenant"></a>Azure AD B2C bérlő létrehozása

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/) Jelentkezzen be egy Azure-fiókkal, amely legalább az előfizetésen belüli [közreműködői](../role-based-access-control/built-in-roles.md) szerepkörrel vagy az előfizetésen belüli erőforráscsoporttal van hozzárendelve.

1. Válassza ki az előfizetést tartalmazó könyvtárat.

    Az Azure Portal eszköztáron válassza ki a **Címtár + Előfizetés** ikont, majd válassza ki az előfizetést tartalmazó könyvtárat. Ez a könyvtár eltér az Azure AD B2C-bérlőt tartalmazó könyvtártól.

    ![Előfizetés-bérlő, Directory + Előfizetésszűrő a kijelölt előfizetési bérlővel](media/tutorial-create-tenant/portal-01-pick-directory.png)

1. Az Azure Portal menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet.
1. Keressen az **Azure Active Directory B2C**mezőben, és válassza a **Létrehozás gombot.**
1. Válassza az **Új Azure AD B2C-bérlő létrehozása** lehetőséget.

    ![Az Azure Portalon kiválasztott új Azure AD B2C-bérlő létrehozása](media/tutorial-create-tenant/portal-02-create-tenant.png)

1. Adja meg **a szervezet nevét** és a kezdeti **tartománynevet**. Jelölje ki az **országot vagy régiót** (később nem módosítható), majd válassza a **Létrehozás gombot.**

    A tartománynév a teljes bérlői tartománynév részeként használatos. Ebben a példában a bérlő neve *contosob2c.onmicrosoft.com:*

    ![Bérlői űrlap létrehozása példaértékekkel az Azure Portalon](media/tutorial-create-tenant/portal-03-tenant-naming.png)

1. Miután a bérlő létrehozása befejeződött, válassza ki az **új B2C-bérlő létrehozása vagy a Kapcsolat** a meglévő bérlőhöz hivatkozást a bérlő létrehozási lap tetején.

    ![Az Azure Portalon kiemelt bérlői navigációs lánc hivatkozása](media/tutorial-create-tenant/portal-04-select-link-sub-link.png)

1. Válassza **a Hivatkozás egy meglévő Azure AD B2C-bérlő az én Azure-előfizetés.**

   ![Meglévő előfizetés-kiválasztás csatolása az Azure Portalon](media/tutorial-create-tenant/portal-05-link-subscription.png)

1. Válassza ki a létrehozott **Azure AD B2C bérlőt,** majd válassza ki az **előfizetést.**

    Az **Erőforráscsoport** esetében válassza az **Új létrehozása** lehetőséget. Adja meg a bérlőt tartalmazó erőforráscsoport **nevét,** válassza az **Erőforráscsoport helyét**, majd kattintson a **Létrehozás gombra.**

    ![Előfizetés-beállítások hivatkozása az Azure Portalon](media/tutorial-create-tenant/portal-06-link-subscription-settings.png)

    Több Azure AD B2C-bérlőt is összekapcsolhat egyetlen Azure-előfizetéssel számlázási célokra.

## <a name="select-your-b2c-tenant-directory"></a>Válassza ki a B2C bérlői könyvtárat

Az új Azure AD B2C-bérlő használatának megkezdéséhez át kell váltania a bérlőt tartalmazó könyvtárra.

Válassza ki a **Directory + előfizetésszűrőt** az Azure Portal felső menüjében, majd válassza ki az Azure AD B2C-bérlőt tartalmazó könyvtárat.

Ha először nem látja az új Azure B2C-bérlőt a listában, frissítse a böngészőablakot, majd válassza újra a **Könyvtár + előfizetés** szűrőt a felső menüben.

![Az Azure Portalon kiválasztott B2C-bérlőt tartalmazó könyvtár](media/tutorial-create-tenant/portal-07-select-tenant-directory.png)

## <a name="add-azure-ad-b2c-as-a-favorite-optional"></a>Azure AD B2C hozzáadása kedvencként (nem kötelező)

Ez az opcionális lépés megkönnyíti az Azure AD B2C-bérlő kiválasztását a következő és az összes későbbi oktatóanyagban.

Ahelyett, hogy az *Azure AD B2C* **minden szolgáltatásban** minden alkalommal, amikor szeretne dolgozni a bérlővel, ehelyett kedvenc az erőforrás. Ezután kiválaszthatja azt a portál menü **Kedvencek** szakaszában gyorsan keresse meg az Azure AD B2C bérlő.

Ezt a műveletet csak egyszer kell végrehajtania. A lépések végrehajtása előtt győződjön meg arról, hogy átváltott az Azure AD B2C-bérlőt tartalmazó könyvtárra az előző szakaszban leírtak szerint, [válassza ki a B2C-bérlői könyvtárat.](#select-your-b2c-tenant-directory)

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Az Azure Portal menüben válassza a **Minden szolgáltatás**lehetőséget.
1. A **Minden szolgáltatás** keresőmezőben keresse meg az **Azure AD B2C**elemet, mutasson a keresési eredményre, majd válassza ki a csillag ikont az elemleírásban. **Az Azure AD B2C** most antól megjelenik az Azure Portalon a **Kedvencek**csoportban.
1. Ha módosítani szeretné az új kedvenc pozícióját, nyissa meg az Azure Portal menüt, válassza az **Azure AD B2C**lehetőséget, majd húzza felfelé vagy lefelé a kívánt helyre.

    ![Azure AD B2C, Kedvencek menü, Microsoft Azure portál](media/tutorial-create-tenant/portal-08-b2c-favorite.png)

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan:

> [!div class="checklist"]
> * Azure AD B2C bérlő létrehozása
> * A bérlő összekapcsolása az előfizetéssel
> * Váltás az Azure AD B2C-bérlőt tartalmazó könyvtárra
> * Az Azure AD B2C-erőforrás **hozzáadása kedvencként** az Azure Portalon

Ezután megtudhatja, hogyan regisztrálhat egy webalkalmazást az új bérlőben.

> [!div class="nextstepaction"]
> [Alkalmazások regisztrálása >](tutorial-register-applications.md)
