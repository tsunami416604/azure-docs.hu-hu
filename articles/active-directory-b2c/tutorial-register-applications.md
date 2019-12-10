---
title: 'Oktatóanyag: alkalmazás regisztrálása'
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan regisztrálhat egy webalkalmazást Azure Active Directory B2C a Azure Portal használatával.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 10/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 76d38e4b35111cd2c09685653fb4948f98387132
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950782"
---
# <a name="tutorial-register-an-application-in-azure-active-directory-b2c"></a>Oktatóanyag: alkalmazás regisztrálása Azure Active Directory B2Cban

Az [alkalmazások](active-directory-b2c-apps.md) Azure Active Directory B2C (Azure ad B2C) való interakció előtt regisztrálni kell őket egy Ön által kezelt bérlőben. Ez az oktatóanyag bemutatja, hogyan regisztrálhat egy webalkalmazást a Azure Portal használatával.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Webalkalmazás regisztrációja
> * Ügyfél titkos kulcsának létrehozása

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Ha még nem hozott létre saját [Azure ad B2C bérlőt](tutorial-create-tenant.md), hozzon létre egyet most. Meglévő Azure AD B2C bérlőt is használhat.

## <a name="register-a-web-application"></a>Webalkalmazás regisztrációja

Az alkalmazások Azure AD B2C-bérlőben való regisztrálásához használhatja a jelenlegi **alkalmazásokat** , vagy az új, egyesített **Alkalmazásregisztrációk (előzetes verzió)** élményt. [További információ az új felületről](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Alkalmazások](#tab/applications/)

1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a bérlőt tartalmazó könyvtárat.
1. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
1. Válassza az **alkalmazások**lehetőséget, majd válassza a **Hozzáadás**lehetőséget.
1. Adjon nevet az alkalmazásnak. Például: *webapp1*.
1. A **Web App/web API belefoglalása** és az **implicit folyamat engedélyezése**lehetőségnél válassza az **Igen**lehetőséget.
1. A **Válasz URL-cím**mezőben adjon meg egy végpontot, amelyben Azure ad B2C vissza kell adnia az alkalmazás által kért jogkivonatokat. Beállíthatja például, hogy helyileg figyeljen a `https://localhost:44316`. Ha még nem ismeri a portszámot, megadhat egy helyőrző értéket, és később módosíthatja azt.

    Az oktatóanyaghoz hasonló tesztelési célokra beállíthatja, hogy `https://jwt.ms`, amely megjeleníti a token tartalmát a vizsgálathoz. Ebben az oktatóanyagban állítsa a **Válasz URL-címét** `https://jwt.ms`ra.

    A válasz URL-címekre a következő korlátozások vonatkoznak:

    * A válasz URL-címének az `https`sémával kell kezdődnie.
    * A válasz URL-címe megkülönbözteti a kis-és nagybetűket. Az esetnek egyeznie kell a futó alkalmazás URL-címének elérési útjával. Ha például az alkalmazás az elérési út részeként `.../abc/response-oidc`, ne adja meg `.../ABC/response-oidc` a válasz URL-címében. Mivel a webböngésző a kis-és nagybetűket megkülönböztető módon kezeli az elérési utakat, előfordulhat, hogy a `.../abc/response-oidc` társított cookie-k nem illeszkednek a `.../ABC/response-oidc` URL-címhez.

1. Az alkalmazás regisztrációjának befejezéséhez kattintson a **Létrehozás** gombra.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Alkalmazásregisztrációk (előzetes verzió)](#tab/app-reg-preview/)

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A bal oldali menüben válassza a **Azure ad B2C**lehetőséget. Vagy válassza a **minden szolgáltatás** lehetőséget, és keresse meg, majd válassza a **Azure ad B2C**lehetőséget.
1. Válassza a **Alkalmazásregisztrációk (előzetes verzió)** lehetőséget, majd válassza az **új regisztráció**lehetőséget.
1. Adja meg az alkalmazás **nevét** . Például: *webapp1*.
1. Válassza **a fiókok lehetőséget bármely szervezeti címtárban vagy bármely identitás-szolgáltatóban**.
1. Az **átirányítási URI**területen válassza a **web**lehetőséget, majd írja be `https://jwt.ms` az URL-cím szövegmezőbe.

    Az átirányítási URI azt a végpontot, amelyhez a felhasználót az engedélyezési kiszolgáló Azure AD B2C (ebben az esetben) a felhasználóval való interakció befejezése után küldi el a rendszer, és a sikeres engedélyezéshez hozzáférési jogkivonatot vagy engedélyezési kódot küld. Éles alkalmazásokban általában egy nyilvánosan elérhető végpont, amelyben az alkalmazás fut, például `https://contoso.com/auth-response`. Az oktatóanyaghoz hasonló tesztelési célokra beállíthatja, hogy `https://jwt.ms`, egy Microsoft tulajdonú webalkalmazás, amely egy jogkivonat dekódolású tartalmát jeleníti meg (a jogkivonat tartalma soha nem hagyja el a böngészőt). Az alkalmazásfejlesztés során hozzáadhatja azt a végpontot, amelyben az alkalmazás helyi figyelést végez, például `https://localhost:5000`. A regisztrált alkalmazásokban bármikor hozzáadhat és módosíthat átirányítási URI-ket.

    Az átirányítási URI-k esetében a következő korlátozások érvényesek:

    * A válasz URL-címének az `https`sémával kell kezdődnie.
    * A válasz URL-címe megkülönbözteti a kis-és nagybetűket. Az esetnek egyeznie kell a futó alkalmazás URL-címének elérési útjával. Ha például az alkalmazás az elérési út részeként `.../abc/response-oidc`, ne adja meg `.../ABC/response-oidc` a válasz URL-címében. Mivel a webböngésző a kis-és nagybetűket megkülönböztető módon kezeli az elérési utakat, előfordulhat, hogy a `.../abc/response-oidc` társított cookie-k nem illeszkednek a `.../ABC/response-oidc` URL-címhez.

1. Az **engedélyek**területen jelölje be a *rendszergazdai jóváhagyás megadása az OpenID-hez és a offline_access engedélyekhez* jelölőnégyzetet.
1. Kattintson a **Register** (Regisztrálás) elemre.

Az alkalmazás regisztrációjának befejezése után engedélyezze az implicit engedélyezési folyamatot:

1. A **kezelés**területen válassza a **hitelesítés**lehetőséget.
1. Válassza **az új élmény kipróbálása** (ha látható) lehetőséget.
1. Az **implicit engedélyezés**területen jelölje be a **hozzáférési jogkivonatok** és az **azonosító tokenek** jelölőnégyzetet.
1. Kattintson a **Mentés** gombra.

* * *

## <a name="create-a-client-secret"></a>Ügyfél titkos kulcsának létrehozása

Ha az alkalmazás egy jogkivonathoz tartozó kódot cserél, létre kell hoznia egy alkalmazás-titkot.

#### <a name="applicationstabapplications"></a>[Alkalmazások](#tab/applications/)

1. A **Azure ad B2C-alkalmazások** lapon válassza ki a létrehozott alkalmazást, például *webapp1*.
1. Válassza a **kulcsok** lehetőséget, majd válassza a **kulcs generálása**elemet.
1. Kattintson a **Save (Mentés** ) gombra a kulcs megtekintéséhez. Jegyezze fel az **Alkalmazáskulcs** értékét. Ezt az értéket használja az alkalmazás kódjában található alkalmazás-titokként.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Alkalmazásregisztrációk (előzetes verzió)](#tab/app-reg-preview/)

1. A **Azure ad B2C-Alkalmazásregisztrációk (előzetes verzió)** lapon válassza ki a létrehozott alkalmazást, például *webapp1*.
1. A **kezelés**területen válassza a **tanúsítványok & Secrets**elemet.
1. Válassza az **Új titkos ügyfélkód** lehetőséget.
1. Adja meg az ügyfél titkos kódjának leírását a **Leírás** mezőben. Például: *clientsecret1*.
1. A **lejárat**szakaszban válassza ki azt az időtartamot, amelynek a titka érvényes, majd válassza a **Hozzáadás**lehetőséget.
1. Jegyezze fel a titkos kulcs **értékét**. Ezt az értéket használja az alkalmazás kódjában található alkalmazás-titokként.

* * *

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan végezheti el a következőket:

> [!div class="checklist"]
> * Webalkalmazás regisztrációja
> * Ügyfél titkos kulcsának létrehozása

Következő lépésként megtudhatja, hogyan hozhat létre felhasználói folyamatokat, hogy a felhasználók regisztrálhatják, bejelentkezhetnek és kezelhetik a profiljaikat.

> [!div class="nextstepaction"]
> [Felhasználói folyamatok létrehozása Azure Active Directory B2C >](tutorial-create-user-flows.md)
