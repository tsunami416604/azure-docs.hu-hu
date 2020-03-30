---
title: 'Oktatóanyag: Alkalmazás regisztrálása'
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan regisztrálhat egy webalkalmazást az Azure Active Directory B2C-ben az Azure Portal használatával.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 10/16/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a688f5e75f7513d0ea4308b751f87f75a2c9510a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183091"
---
# <a name="tutorial-register-an-application-in-azure-active-directory-b2c"></a>Oktatóanyag: Alkalmazás regisztrálása az Azure Active Directory B2C-ben

Ahhoz, hogy az [alkalmazások](application-types.md) kommunikálhassanak az Azure Active Directory B2C (Azure AD B2C) használatával, regisztrálnikell őket egy ön által kezelt bérlőben. Ez az oktatóanyag bemutatja, hogyan regisztrálhat egy webalkalmazást az Azure Portal használatával.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Webalkalmazás regisztrációja
> * Ügyféltitok létrehozása

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Ha még nem hozta létre a saját [Azure AD B2C-bérlő,](tutorial-create-tenant.md)hozzon létre egyet most. Használhatja a meglévő Azure AD B2C bérlő.

## <a name="register-a-web-application"></a>Webalkalmazás regisztrációja

Ha regisztrálegy alkalmazást az Azure AD B2C-bérlőben, használhatja az aktuális **alkalmazások** vagy az új egyesített **alkalmazásregisztrációk (előzetes verzió)** használatát. [További információ az új felületről](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Alkalmazások](#tab/applications/)

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Válassza ki a **Könyvtár + Előfizetés** ikont a portál eszköztárán, majd válassza ki az Azure AD B2C-bérlőt tartalmazó könyvtárat.
1. Az Azure Portalon keresse meg és válassza ki **az Azure AD B2C parancsot.**
1. Válassza **az Alkalmazások**lehetőséget, majd a **Hozzáadás**lehetőséget.
1. Adjon nevet az alkalmazásnak. Például *webapp1*.
1. A **Webapp/web API belefoglalása** és **az Implicit folyamat engedélyezése csoportban**válassza az **Igen**lehetőséget.
1. A **válasz URL-cím,** adjon meg egy végpontot, ahol az Azure AD B2C vissza kell adnia az alkalmazás által kért jogkivonatokat. Beállíthatja például, hogy helyileg `https://localhost:44316`figyeljen a alkalmazásban. Ha még nem tudja a portszámát, megadhat egy helyőrző értéket, és később módosíthatja azt.

    Tesztelési célokra, mint ez az `https://jwt.ms` oktatóanyag beállíthatja, hogy melyik jeleníti meg a token tartalmát ellenőrzésre. Ebben az oktatóanyagban állítsa `https://jwt.ms`a **Válasz URL-címét** a-ra.

    A válasz URL-címekre a következő korlátozások vonatkoznak:

    * A válasz URL-címének `https`a sémával kell kezdődnie.
    * A válasz URL-címe a kis- és nagybetűket is figyelembe vevő. Az esetnek meg kell egyeznie a futó alkalmazás URL-elérési útjának esetével. Ha például az alkalmazás az elérési `.../abc/response-oidc`út részeként szerepel, ne adja meg `.../ABC/response-oidc` a válasz URL-címét. Mivel a webböngésző az elérési utakat a `.../abc/response-oidc` kis- és nagybetűk megkülönböztetéseként kezeli, a `.../ABC/response-oidc` kapcsolódó cookie-k kizárhatók, ha az okat nem egyező URL-re irányítják át.

1. Az alkalmazásregisztráció befejezéséhez válassza a **Létrehozás** gombot.

#### <a name="app-registrations-preview"></a>[Alkalmazásregisztrációk (előzetes verzió)](#tab/app-reg-preview/)

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Válassza ki a **Könyvtár + Előfizetés** ikont a portál eszköztárán, majd válassza ki az Azure AD B2C-bérlőt tartalmazó könyvtárat.
1. Az Azure Portalon keresse meg és válassza ki **az Azure AD B2C parancsot.**
1. Válassza **az Alkalmazásregisztrációk (Előzetes verzió)** lehetőséget, majd az **Új regisztráció**lehetőséget.
1. Adja meg az alkalmazás **nevét.** Például *webapp1*.
1. Válassza **a Fiókok lehetőséget bármely szervezeti címtárban vagy identitásszolgáltatóban.**
1. Az **Átirányítás URI**csoportban válassza `https://jwt.ms` a **Web**lehetőséget, majd írja be az URL-cím mezőbe.

    Az átirányítási URI az a végpont, amelyre az engedélyezési kiszolgáló (az Azure AD B2C, ebben az esetben) elküldi a felhasználót, és amelynek egy hozzáférési jogkivonatvagy engedélyezési kód küldése sikeres engedélyezés esetén. Egy éles alkalmazásokban általában egy nyilvánosan elérhető végpont, ahol az `https://contoso.com/auth-response`alkalmazás fut, például. Tesztelési célokra, mint ez az `https://jwt.ms`oktatóanyag, beállíthatja, hogy a Microsoft tulajdonában lévő webalkalmazás, amely megjeleníti a dekódolt tartalmát egy token (a token tartalmát soha nem hagyja el a böngészőt). Az alkalmazásfejlesztés során hozzáadhatja azt a végpontot, ahol `https://localhost:5000`az alkalmazás helyileg figyel, például. A regisztrált alkalmazásokban bármikor hozzáadhat és módosíthat átirányítási URI-kat.

    Az átirányítási URI-kra a következő korlátozások vonatkoznak:

    * A válasz URL-címének `https`a sémával kell kezdődnie.
    * A válasz URL-címe a kis- és nagybetűket is figyelembe vevő. Az esetnek meg kell egyeznie a futó alkalmazás URL-elérési útjának esetével. Ha például az alkalmazás az elérési `.../abc/response-oidc`út részeként szerepel, ne adja meg `.../ABC/response-oidc` a válasz URL-címét. Mivel a webböngésző az elérési utakat a `.../abc/response-oidc` kis- és nagybetűk megkülönböztetéseként kezeli, a `.../ABC/response-oidc` kapcsolódó cookie-k kizárhatók, ha az okat nem egyező URL-re irányítják át.

1. **Az Engedélyek csoportban**jelölje be a Rendszergazda hozzájárulása a *nyílt és offline_access engedélyekhez* jelölőnégyzetet.
1. Kattintson a **Register** (Regisztrálás) elemre.

Az alkalmazás regisztrációjának befejezése után engedélyezze az implicit támogatási folyamatot:

1. A **Kezelés csoportban**válassza a **Hitelesítés**lehetőséget.
1. Válassza **az Új felület kipróbálása** lehetőséget (ha látható).
1. Az **Implicit támogatás**csoportban jelölje be mind az **Access-jogkivonatokat,** mind az **Azonosító-jogkivonatokat** jelölőnégyzetekbe.
1. Kattintson a **Mentés** gombra.

* * *

## <a name="create-a-client-secret"></a>Ügyféltitok létrehozása

Ha az alkalmazás egy hozzáférési jogkivonat engedélyezési kódját cseréli, létre kell hoznia egy alkalmazástitkos kulcsot.

#### <a name="applications"></a>[Alkalmazások](#tab/applications/)

1. Az **Azure AD B2C – Applications** lapon válassza ki a létrehozott alkalmazást, például *a webapp1.In*the Azure AD B2C - Applications page, select the application you created, for example webapp1 .
1. Válassza **a Kulcsok,** majd **a Kulcs létrehozása**lehetőséget.
1. A kulcs megtekintéséhez válassza a **Mentés** gombot. Jegyezze fel az **Alkalmazáskulcs** értékét. Ezt az értéket használja az alkalmazás titkos kódja ként.

#### <a name="app-registrations-preview"></a>[Alkalmazásregisztrációk (előzetes verzió)](#tab/app-reg-preview/)

1. Az **Azure AD B2C – App registrations (Preview) (Előzetes verzió)** lapon válassza ki a létrehozott alkalmazást, például *a webapp1.*
1. A **Kezelés csoportban**válassza **a Tanúsítványok & titkos kulcsok**lehetőséget.
1. Válassza az **Új titkos ügyfélkód** lehetőséget.
1. Adja meg az ügyféltitkos kulcsot a **Leírás** mezőben. Például *clientsecret1*.
1. A **Lejár,** válassza ki azt az időtartamot, amelyre a titkos titok érvényes, majd válassza a **Hozzáadás**lehetőséget.
1. Jegyezze fel a titkos **kulcsot.** Ezt az értéket használja az alkalmazás titkos kódja ként.

* * *

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan:

> [!div class="checklist"]
> * Webalkalmazás regisztrációja
> * Ügyféltitok létrehozása

Ezután megtudhatja, hogyan hozhat létre felhasználói folyamatokat, hogy a felhasználók regisztrálhassanak, bejelentkezhessenek és kezelhessék a profiljukat.

> [!div class="nextstepaction"]
> [Felhasználói folyamatok létrehozása az Azure Active Directory B2C >](tutorial-create-user-flows.md)
