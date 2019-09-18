---
title: Oktatóanyag – alkalmazás regisztrálása – Azure Active Directory B2C
description: Megtudhatja, hogyan regisztrálhat egy webalkalmazást Azure Active Directory B2C a Azure Portal használatával.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 08/23/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 06b2c273f41bfa74ee968b6fd6676e83767ce8b2
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063255"
---
# <a name="tutorial-register-an-application-in-azure-active-directory-b2c"></a>Oktatóanyag: Alkalmazás regisztrálása Azure Active Directory B2C

Az [alkalmazások](active-directory-b2c-apps.md) Azure Active Directory B2C (Azure ad B2C) való interakció előtt regisztrálni kell őket egy Ön által kezelt bérlőben. Ez az oktatóanyag bemutatja, hogyan regisztrálhat egy webalkalmazást a Azure Portal használatával.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Webalkalmazás regisztrációja
> * Ügyfél titkos kulcsának létrehozása

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Ha még nem hozott létre saját [Azure ad B2C bérlőt](tutorial-create-tenant.md), hozzon létre egyet most. Meglévő Azure AD B2C bérlőt is használhat.

## <a name="register-a-web-application"></a>Webalkalmazás regisztrációja

1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a bérlőt tartalmazó könyvtárat.
1. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
1. Válassza az **alkalmazások**lehetőséget, majd válassza a **Hozzáadás**lehetőséget.
1. Adja meg az alkalmazás nevét. Például: *webapp1*.
1. A **Web App/web API** belefoglalása és az **implicit folyamat engedélyezése**lehetőségnél válassza az **Igen**lehetőséget.
1. A **Válasz URL-cím**mezőben adjon meg egy végpontot, amelyben Azure ad B2C vissza kell adnia az alkalmazás által kért jogkivonatokat. Beállíthatja például, hogy a helyi figyelést a következő `https://localhost:44316`időpontban:. Ha még nem ismeri a portszámot, megadhat egy helyőrző értéket, és később módosíthatja azt.

    Az oktatóanyaghoz hasonló tesztelési célokra beállíthatja, hogy `https://jwt.ms` a rendszer megjelenítse a token tartalmát a vizsgálathoz. Ebben az oktatóanyagban állítsa be a **Válasz URL-címét** `https://jwt.ms`a következőre:.

    A válasz URL-címekre a következő korlátozások vonatkoznak:

    * A válasz URL-címnek a sémával `https`kell kezdődnie.
    * A válasz URL-címe megkülönbözteti a kis-és nagybetűket. Az esetnek egyeznie kell a futó alkalmazás URL-címének elérési útjával. Ha például az alkalmazás az elérési út `.../abc/response-oidc`részeként szerepel, ne adja meg `.../ABC/response-oidc` a válasz URL-címét. Mivel a webböngésző a kis-és nagybetűket megkülönböztető módon kezeli az `.../abc/response-oidc` elérési utakat, a hozzájuk társított cookie-k kizárható, `.../ABC/response-oidc` ha a rendszer átirányítja a kis-és nagybetűket.

1. Az alkalmazás regisztrációjának befejezéséhez kattintson a **Létrehozás** gombra.

## <a name="create-a-client-secret"></a>Ügyfél titkos kulcsának létrehozása

Ha az alkalmazás egy jogkivonathoz tartozó kódot cserél, létre kell hoznia egy alkalmazás-titkot.

1. A **Azure ad B2C-alkalmazások** lapon válassza ki a létrehozott alkalmazást, például *webapp1*.
1. Válassza a **kulcsok** lehetőséget, majd válassza a **kulcs generálása**elemet.
1. Kattintson a **Save (Mentés** ) gombra a kulcs megtekintéséhez. Jegyezze fel az **Alkalmazáskulcs** értékét. Ezt az értéket használja az alkalmazás kódjában található alkalmazás-titokként.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan végezheti el a következőket:

> [!div class="checklist"]
> * Webalkalmazás regisztrációja
> * Ügyfél titkos kulcsának létrehozása

Következő lépésként megtudhatja, hogyan hozhat létre felhasználói folyamatokat, hogy a felhasználók regisztrálhatják, bejelentkezhetnek és kezelhetik a profiljaikat.

> [!div class="nextstepaction"]
> [Felhasználói folyamatok létrehozása Azure Active Directory B2C >](tutorial-create-user-flows.md)
