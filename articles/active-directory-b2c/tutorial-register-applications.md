---
title: 'Oktatóanyag: alkalmazás regisztrálása'
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan regisztrálhat egy webalkalmazást Azure Active Directory B2C a Azure Portal használatával.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6e78a6b422f592caa252cb5856fc590a4bed35a7
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83637585"
---
# <a name="tutorial-register-an-application-in-azure-active-directory-b2c"></a>Oktatóanyag: alkalmazás regisztrálása Azure Active Directory B2Cban

Az [alkalmazások](application-types.md) Azure Active Directory B2C (Azure ad B2C) való interakció előtt regisztrálni kell őket egy Ön által kezelt bérlőben. Ez az oktatóanyag bemutatja, hogyan regisztrálhat egy webalkalmazást a Azure Portal használatával.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Webalkalmazás regisztrációja
> * Ügyfél titkos kulcsának létrehozása

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

Ha még nem hozott létre saját [Azure ad B2C bérlőt](tutorial-create-tenant.md), hozzon létre egyet most. Meglévő Azure AD B2C bérlőt is használhat.

## <a name="register-a-web-application"></a>Webalkalmazás regisztrációja

Az alkalmazások Azure AD B2C-bérlőben való regisztrálásához használhatja a jelenlegi **alkalmazásokat** , vagy az új, egyesített **Alkalmazásregisztrációk (előzetes verzió)** élményt. [További információ az új felületről](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Alkalmazások](#tab/applications/)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** ikont a portál eszköztárán, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A Azure Portal keresse meg és válassza a **Azure ad B2C**lehetőséget.
1. Válassza az **alkalmazások**lehetőséget, majd válassza a **Hozzáadás**lehetőséget.
1. Adjon nevet az alkalmazásnak. Például: *webapp1*.
1. A **Web App/web API belefoglalása** és az **implicit folyamat engedélyezése**lehetőségnél válassza az **Igen**lehetőséget.
1. A **Válasz URL-cím**mezőben adjon meg egy végpontot, amelyben Azure ad B2C vissza kell adnia az alkalmazás által kért jogkivonatokat. Beállíthatja például, hogy a helyi figyelést a következő időpontban: `https://localhost:44316` . Ha még nem ismeri a portszámot, megadhat egy helyőrző értéket, és később módosíthatja azt.

    Az oktatóanyaghoz hasonló tesztelési célokra beállíthatja, hogy a rendszer `https://jwt.ms` megjelenítse a token tartalmát a vizsgálathoz. Ebben az oktatóanyagban állítsa be a **Válasz URL-címét** a következőre: `https://jwt.ms` .

    A válasz URL-címekre a következő korlátozások vonatkoznak:

    * A válasz URL-címnek a sémával kell kezdődnie `https` .
    * A válasz URL-címe megkülönbözteti a kis-és nagybetűket. Az esetnek egyeznie kell a futó alkalmazás URL-címének elérési útjával. Ha például az alkalmazás az elérési út részeként szerepel `.../abc/response-oidc` , ne adja meg `.../ABC/response-oidc` a válasz URL-címét. Mivel a webböngésző a kis-és nagybetűket megkülönböztető módon kezeli az elérési utakat, a hozzájuk társított cookie-k `.../abc/response-oidc` kizárható, ha a rendszer átirányítja a kis-és nagybetűket `.../ABC/response-oidc` .

1. Válassza a **Létrehozás** lehetőséget az alkalmazás regisztrációjának befejezéséhez.

#### <a name="app-registrations-preview"></a>[Alkalmazásregisztrációk (előzetes verzió)](#tab/app-reg-preview/)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** ikont a portál eszköztárán, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A Azure Portal keresse meg és válassza a **Azure ad B2C**lehetőséget.
1. Válassza a **Alkalmazásregisztrációk (előzetes verzió)** lehetőséget, majd válassza az **új regisztráció**lehetőséget.
1. Adja meg az alkalmazás **nevét** . Például: *webapp1*.
1. A **támogatott fióktípus**területen válassza a **fiókok lehetőséget bármely szervezeti címtárban (bármely Azure ad-címtár-több-bérlős) és a személyes Microsoft-fiókokat (például Skype, Xbox)**.
1. Az **átirányítási URI**területen válassza a **web**lehetőséget, majd írja be `https://jwt.ms` az URL-cím szövegmezőbe.

    Az átirányítási URI azt a végpontot, amelyhez a felhasználót az engedélyezési kiszolgáló Azure AD B2C (ebben az esetben) a felhasználóval való interakció befejezése után küldi el a rendszer, és a sikeres engedélyezéshez hozzáférési jogkivonatot vagy engedélyezési kódot küld. Éles alkalmazásokban ez általában egy nyilvánosan elérhető végpont, amelyben az alkalmazás fut, például: `https://contoso.com/auth-response` . Az oktatóanyaghoz hasonló tesztelési célokra beállíthatja, hogy a egy `https://jwt.ms` Microsoft tulajdonú webalkalmazás, amely egy jogkivonat dekódolású tartalmát jeleníti meg (a jogkivonat tartalma soha nem hagyja el a böngészőt). Az alkalmazásfejlesztés során hozzáadhatja azt a végpontot, amelyben az alkalmazás helyileg, például a következőt figyeli `https://localhost:5000` . A regisztrált alkalmazásokban bármikor hozzáadhat és módosíthat átirányítási URI-ket.

    Az átirányítási URI-k esetében a következő korlátozások érvényesek:

    * A válasz URL-címnek a sémával kell kezdődnie `https` .
    * A válasz URL-címe megkülönbözteti a kis-és nagybetűket. Az esetnek egyeznie kell a futó alkalmazás URL-címének elérési útjával. Ha például az alkalmazás az elérési út részeként szerepel `.../abc/response-oidc` , ne adja meg `.../ABC/response-oidc` a válasz URL-címét. Mivel a webböngésző a kis-és nagybetűket megkülönböztető módon kezeli az elérési utakat, a hozzájuk társított cookie-k `.../abc/response-oidc` kizárható, ha a rendszer átirányítja a kis-és nagybetűket `.../ABC/response-oidc` .

1. Az **engedélyek**területen jelölje be a *rendszergazdai jóváhagyás megadása az OpenID-hez és a offline_access engedélyekhez* jelölőnégyzetet.
1. Kattintson a **Register** (Regisztrálás) elemre.

Az alkalmazás regisztrációjának befejezése után engedélyezze az implicit engedélyezési folyamatot:

1. A **kezelés**területen válassza a **hitelesítés**lehetőséget.
1. Válassza **az új élmény kipróbálása** (ha látható) lehetőséget.
1. Az **implicit engedélyezés**területen jelölje be a **hozzáférési jogkivonatok** és az **azonosító tokenek** jelölőnégyzetet.
1. Kattintson a **Mentés** gombra.

* * *

## <a name="create-a-client-secret"></a>Ügyfél titkos kulcsának létrehozása

Ha az alkalmazás egy hozzáférési jogkivonat engedélyezési kódját cseréli, létre kell hoznia egy alkalmazás-titkot.

#### <a name="applications"></a>[Alkalmazások](#tab/applications/)

1. A **Azure ad B2C-alkalmazások** lapon válassza ki a létrehozott alkalmazást, például *webapp1*.
1. Válassza a **kulcsok** lehetőséget, majd válassza a **kulcs generálása**elemet.
1. Kattintson a **Save (Mentés** ) gombra a kulcs megtekintéséhez. Jegyezze fel az **Alkalmazáskulcs** értékét. Ezt az értéket használja az alkalmazás kódjában található alkalmazás-titokként.

#### <a name="app-registrations-preview"></a>[Alkalmazásregisztrációk (előzetes verzió)](#tab/app-reg-preview/)

1. A **Azure ad B2C-Alkalmazásregisztrációk (előzetes verzió)** lapon válassza ki a létrehozott alkalmazást, például *webapp1*.
1. A **kezelés**területen válassza a **tanúsítványok & Secrets**elemet.
1. Válassza az **Új titkos ügyfélkód** lehetőséget.
1. Adja meg az ügyfél titkos kódjának leírását a **Leírás** mezőben. Például: *clientsecret1*.
1. A **lejárat**szakaszban válassza ki azt az időtartamot, amelynek a titka érvényes, majd válassza a **Hozzáadás**lehetőséget.
1. Jegyezze fel a titkos kulcs **értékét**. Ezt az értéket használja az alkalmazás kódjában található alkalmazás-titokként.

* * *

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan végezheti el a következőket:

> [!div class="checklist"]
> * Webalkalmazás regisztrációja
> * Ügyfél titkos kulcsának létrehozása

Következő lépésként megtudhatja, hogyan hozhat létre felhasználói folyamatokat, hogy a felhasználók regisztrálhatják, bejelentkezhetnek és kezelhetik a profiljaikat.

> [!div class="nextstepaction"]
> [Felhasználói folyamatok létrehozása Azure Active Directory B2C >](tutorial-create-user-flows.md)
