---
title: Az Azure Portal regisztrációs mezői egyéni fejlesztésű alkalmazásokhoz
description: Útmutató egyéni fejlesztett alkalmazások Azure AD-vel való regisztrálásához
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: ryanwi
ms.openlocfilehash: 1469fcfa68b10353b78d31ccc758c61bf7746692
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88120694"
---
# <a name="azure-portal-registration-fields-for-custom-developed-apps"></a>Az Azure Portal regisztrációs mezői egyéni fejlesztésű alkalmazásokhoz

Ez a cikk a [Azure Portal](https://portal.azure.com)alkalmazás regisztrációs űrlapján található összes elérhető mező rövid leírását tartalmazza.

## <a name="register-a-new-application"></a>Új alkalmazás regisztrálása

-   Új alkalmazás regisztrálásához navigáljon a [Azure Portal](https://portal.azure.com).

-   A bal oldali navigációs panelen kattintson a **Azure Active Directory** elemre.

-   Válassza a **Alkalmazásregisztrációk** lehetőséget, majd kattintson a **Hozzáadás**gombra.

-   Ezzel megnyitja az alkalmazás regisztrációs űrlapját.

## <a name="fields-in-the-application-registration-form"></a>Az alkalmazás regisztrációs űrlap mezői

| Mező            | Leírás                                                                              |
|------------------|------------------------------------------------------------------------------------------|
| Name (Név)             | Az alkalmazás neve. Legalább négy karakterből kell állnia.                |
| Támogatott fióktípusok| Válassza ki, hogy mely fiókokat kívánja támogatni: ebben a szervezeti címtárban, a szervezeti címtárban vagy bármely szervezeti címtárban, illetve személyes Microsoft-fiókban lévő fiókokban.  |
| Átirányítási URI (nem kötelező) | Válassza ki az Ön által felépített alkalmazás típusát, a **webes** vagy a **nyilvános ügyfelet (Mobile & Desktop)**, majd adja meg az alkalmazás átirányítási URI-ját (vagy válasz URL-címét). Webalkalmazás esetében adja meg alkalmazás alap URL-címét. A http://localhost:31544 például a helyi gépen futó webalkalmazás URL-címe lehet. A felhasználók ezzel az URL-címmel jelentkeznek be egy webes ügyfélalkalmazásba. Nyilvános ügyfélalkalmazások esetében adja meg az URI-t, amelyet az Azure AD a jogkivonatválaszok visszaadására használ. Adja meg az alkalmazáshoz tartozó értéket, például myapp://auth. Ha szeretné megtekinteni a webalkalmazások és a natív alkalmazások konkrét [quickstarts](./index.yml)példáit, tekintse meg a gyors útmutatókat.|

Miután kitöltötte a fenti mezőket, az alkalmazás regisztrálva lesz a Azure Portalban, és a rendszer átirányítja az alkalmazás áttekintés lapjára. A bal oldali ablaktáblán a **kezelés** alatt található beállítások lapok több mezőt biztosítanak az alkalmazás testreszabásához. Az alábbi táblázat az összes mezőt ismerteti. Ezen mezők részhalmaza csak akkor jelenik meg, ha létrehozott egy webalkalmazást vagy egy nyilvános ügyfélalkalmazás.

### <a name="overview"></a>Áttekintés

| Mező           | Leírás        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alkalmazásazonosító  | Amikor regisztrál egy alkalmazást, az Azure AD hozzárendeli az alkalmazást egy alkalmazás-AZONOSÍTÓhoz. Az alkalmazás-azonosító segítségével egyedileg azonosíthatja az alkalmazást az Azure AD-ben használt hitelesítési kérelmekben, valamint hozzáférhet az erőforrásokhoz, például a Graph APIhoz.                                                          |
| Alkalmazásazonosító URI      | Ennek egy egyedi URI-nak kell lennie, amely általában a **https://- &lt; bérlő nevének \_ &gt; / &lt; alkalmazási \_ neve &gt; .** Ezt használja a rendszer az engedélyezési folyamat során, amely egyedi azonosítóként adja meg azt az erőforrást, amelyet a jogkivonat számára ki kell állítani. A kiállított hozzáférési jogkivonatban a "AUD" jogcím is lesz. |

### <a name="branding"></a>Arculat

| Mező           | Leírás        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Új embléma feltöltése | Ezt felhasználhatja az alkalmazás emblémájának feltöltéséhez. Az emblémának. bmp,. jpg vagy. png formátumúnak kell lennie, és a fájlméretnek 100 KB-nál kisebbnek kell lennie. A képméretnek 215x215 képpont méretűnek kell lennie, a 94x94 képpontok központi képméretével.|
| Kezdőlap URL-címe   | Ez az alkalmazás regisztrációja során megadott bejelentkezési URL-cím.|

### <a name="authentication"></a>Hitelesítés

| Mező           | Leírás        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Kijelentkezési URL-cím      | Ez az egyszeri kijelentkezés kijelentkezési URL-címe. Az Azure AD kijelentkezési kérelmet küld erre az URL-címre, ha a felhasználó bármely más regisztrált alkalmazás használatával törli a munkamenetét az Azure AD-vel.|
| Támogatott fióktípusok  | Ezzel a kapcsolóval adható meg, hogy az alkalmazást több bérlő is használhatja-e. Ez általában azt jelenti, hogy a külső szervezetek használhatják az alkalmazást úgy, hogy regisztrálják a bérlőben, és hozzáférést biztosítanak a szervezet adataihoz.|
| Átirányítási URL-címek      | Az átirányítás vagy válasz, az URL-címek azok a végpontok, ahol az Azure AD visszaadja az alkalmazás által kért jogkivonatokat. Natív alkalmazások esetén a sikeres hitelesítés után a felhasználó elküldése történik. Az Azure AD ellenőrzi, hogy az alkalmazás által a OAuth 2,0 kérelemben megadott átirányítási URI megfelel-e a portálon regisztrált értékek egyikének.|

### <a name="certificates-and-secrets"></a>Tanúsítványok és titkos kódok

| Mező           | Leírás        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ügyfél titkos kódja            | Az Azure AD által védett webes API-k programozott módon való eléréséhez felhasználói beavatkozás nélkül is létrehozhat ügyfél-titkokat vagy kulcsokat. Az **új ügyfél titka** lapon adja meg a kulcs leírását, a lejárati dátumot és a mentést a kulcs létrehozásához. Győződjön meg róla, hogy biztonságos helyre menti, mert később nem fogja tudni elérni.             |

## <a name="next-steps"></a>Következő lépések

[Alkalmazások kezelése Azure Active Directory](../manage-apps/what-is-application-management.md)