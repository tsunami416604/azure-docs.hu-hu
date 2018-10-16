---
title: Tudnivalók az Azure digitális Twins API-hitelesítés |} A Microsoft Docs
description: Az Azure digitális Twins csatlakozás és hitelesítés API-k használatával
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/02/2018
ms.author: lyrana
ms.openlocfilehash: 1d5b1869428cec6bf80b8518485f685e38ad5997
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2018
ms.locfileid: "49324118"
---
# <a name="connect-and-authenticate-to-apis"></a>Csatlakozás és hitelesítés API-k

Az Azure digitális Twins felhasználók hitelesítéséhez és alkalmazások védelmét az Azure Active Directory (Azure AD) használja.

Ha még nem ismeri az Azure ad-vel, további információ érhető el a [fejlesztői útmutató](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-developers-guide). A Windows Azure-hitelesítési tár az Active Directory-jogkivonatok beszerzésére számos lehetőséget kínál. A részletesen a könyvtárba, figyelje [Itt](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki).

Ez a cikk áttekintést nyújt a két forgatókönyv: egy éles forgatókönyvet, és a egy középső rétegbeli API-t és a hitelesítést az ügyfélalkalmazásban Postman rövid indítási és a tesztelés.

## <a name="authentication-in-production"></a>Hitelesítési éles környezetben

Megoldások a fejlesztők kétféleképpen digitális Twins csatlakozni kell.  Megoldások a fejlesztők a következő módokon csatlakozhat az Azure digitális Twins:

* Egy ügyfélalkalmazás vagy egy középső rétegbeli API-t is létrehozhatnak. Ügyfélalkalmazások kérése a felhasználóktól hitelesítheti, majd a [OAuth 2.0-alapú meghatalmazásos](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow) biztonsági folyamat alárendelt API hívása.
* Hozzon létre, vagy egy meglévő Azure AD-alkalmazást használják. A dokumentáció megtekintése [Itt](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad).
    1. Adja meg a **bejelentkezéshez és az átirányítási URI-k** (ha szükséges).
    1. Az alkalmazás jegyzékfájlja set `oauth2AllowImplicitFlow` igaz értékre.
    1. A **szükséges engedélyek**, adja hozzá a digitális Twins kereséssel "Az Azure digitális Twins." Válassza ki **delegált engedélyek olvasási/írási hozzáférést** , és kattintson a **engedélyek megadása** gombra.

Az a-meghatalmazásos folyamat szervezését kapcsolatos részletes útmutatásért látogasson el [ezt oldal](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow). Megtekintheti továbbá kódpéldák [Itt](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapi-onbehalfof/).

## <a name="test-with-the-postman-client"></a>Tesztelése a Postman-ügyféllel

1. Kövesse a kezdeti lépéseket az Azure Active Directory-alkalmazás létrehozása (vagy módosítása). Ezután adja meg a `oauth2AllowImplicitFlow` igaz értékre, az alkalmazásjegyzékben, és a hozzáférési jogot "Az Azure digitális Twins."
1. A válasz URL-cím beállítása [ https://www.getpostman.com/oauth2/callback ](https://www.getpostman.com/oauth2/callback).
1. Válassza ki a **engedélyezési lapon**, kattintson a **OAuth 2.0**, és válassza ki **új hozzáférési Token letöltése**.

    |**Mező**  |**Érték** |
    |---------|---------|
    | Engedélyezési típus | Implicit |
    | Visszahívási URL | [https://www.getpostman.com/oauth2/callback](https://www.getpostman.com/oauth2/callback) |
    | Hitelesítési URL-cím | [https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0](https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0)
    | Ügyfél-azonosító | Az alkalmazásazonosítót használni létrehozott vagy az 1. lépésben azt egy megváltozott célra az Azure AD-alkalmazás |
    | Hatókör | Hagyja üresen |
    | Állapot | Hagyja üresen |
    | Ügyfél-hitelesítés | Alapszintű hitelesítési fejléc küldése |

1. Kattintson a **jogkivonat kérelmezéséhez**.

    >[!TIP]
    >Ha hibaüzenetet kap, "OAuth 2 nem lehet befejezni a", próbálkozzon az alábbi lehetőségek közül:
    > * Postman zárja be és nyissa meg újra, és próbálkozzon újra.
    > * Az alkalmazás titkos kulcs törlése, egy újat, és megadja az értéket a fenti űrlapon hozza létre újra.

1. Görgessen lefelé, és kattintson a **használható jogkivonat**.

## <a name="next-steps"></a>További lépések

Az Azure digitális Twins biztonságával kapcsolatban, olvassa el [létrehozása és kezelése a szerepkör-hozzárendelések](./security-create-manage-role-assignments.md).
