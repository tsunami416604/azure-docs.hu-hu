---
title: Az Azure digitális Twins Postman konfigurálása |} A Microsoft Docs
description: Az Azure digitális Twins Postman konfigurálása
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: adgera
ms.openlocfilehash: 5c06e38bf5a51744a4878a7acb6c365d7e812a61
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51711140"
---
# <a name="how-to-configure-postman-for-azure-digital-twins"></a>Az Azure digitális Twins Postman konfigurálása

Ez a cikk ismerteti a OAuth 2.0 típusú implicit engedélyezés folyamat használatához egy Azure Active Directory (Azure AD-) alkalmazást. Ezután azt tárgyalja, hogyan konfigurálhatja a Postman REST-ügyféllel, hogy jogkivonat hordozó HTTP-kéréseket a felügyeleti API-k.

## <a name="postman-summary"></a>Postman-összefoglaló

Bevezetés az Azure digitális Twins egy REST-ügyféleszköz segítségével [Postman](https://www.getpostman.com/) készítse elő a helyi tesztelési környezetét. A Postman-ügyfél segítségével gyorsan létrehozhat összetett HTTP-kérelmekre. Töltse le a Postman-ügyfél asztali verzióját a [www.getpostman.com/apps](https://www.getpostman.com/apps).

[Postman](https://www.getpostman.com/) REST teszteli, amely megkeresi a legfontosabb HTTP kérés funkciói egy hasznos, asztali és a grafikus felhasználói Felülettel beépülő modul-alapú eszköz. A Postman-ügyfélen keresztül megoldások fejlesztők adhatja meg a HTTP-kérelem típusú hívás és az SSL (POST, GET, frissítés, PATCH és DELETE), API-végpont. Postman is támogatja a HTTP-kérelmek fejléceinek hozzáadását, paraméterek, űrlapadatokból és szervek.

## <a name="configure-azure-active-directory-to-use-the-oauth-20-implicit-grant-flow"></a>Az Azure Active Directory is használhassa azt az OAuth 2.0 típusú implicit engedélyezés konfigurálása

Az OAuth 2.0 típusú implicit engedélyezés folyamat használata az Azure AD-alkalmazás konfigurálása.

1. Kövesse a [ebben a rövid útmutatóban](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad) típusú natív Azure AD-alkalmazást létrehozni. Vagy használhat egy meglévő natív alkalmazás regisztrációja.

1. A **szükséges engedélyek**, adja meg `Azure Digital Twins` válassza **delegált engedélyek**. Válassza ki **engedélyek megadása**.

    ![Az Azure AD-alkalmazásregisztrációk api hozzáadása](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png)

1. Kattintson a **Manifest** az alkalmazásjegyzékben, az alkalmazás megnyitásához. Állítsa be *oauth2AllowImplicitFlow* való `true`.

      ![Az Azure AD implicit folyamat][1]

1. Konfigurálja a **válasz URL-cím** való [ `https://www.getpostman.com/oauth2/callback` ](https://www.getpostman.com/oauth2/callback).

      ![Az Azure AD-válasz URL-címe][2]

1. Másolja ki és tartsa a **Alkalmazásazonosító** az Azure AD-alkalmazáshoz. Alább szolgál.

## <a name="configure-the-postman-client"></a>A Postman-ügyfél konfigurálása

Ezután állítsa be, és konfigurálja az Azure AD-token beszerzése a Postman. Ezt követően hajtsa végre egy hitelesített HTTP-kérelem Azure digitális Twins beszerzett token használatával:

1. Lépjen a [www.getpostman.com]([https://www.getpostman.com/) letölteni az alkalmazást.
1. Ügyeljen arra, hogy a **engedélyezési URL-címet** helyes-e. A formátum kell vennie:

    ```plaintext
    https://login.microsoftonline.com/YOUR_AZURE_TENANT.onmicrosoft.com/oauth2/authorize?resource=YOUR_RESOURCE_ID
    ```

    | Name (Név)  | Csere erre | Példa |
    |---------|---------|---------|
    | YOUR_AZURE_TENANT | A bérlő vagy a szervezet neve | `microsoft` |
    | YOUR_RESOURCE_ID | Az erőforrás-azonosítója | `10b07f429-9f4b-4714-9392-cc5e8e80c8b0` |

1. Válassza ki a **engedélyezési** lapon jelölje be **OAuth 2.0**, majd válassza ki **új hozzáférési Token letöltése**.

    | Mező  | Érték |
    |---------|---------|
    | Engedélyezési típus | `Implicit` |
    | Visszahívási URL | [`https://www.getpostman.com/oauth2/callback`](https://www.getpostman.com/oauth2/callback) |
    | Hitelesítési URL-cím | Használja a **engedélyezési URL-címet** a fenti 2. lépésben |
    | Ügyfél-azonosító | Használja a **Alkalmazásazonosító** létrehozott vagy az előző szakaszban azt egy megváltozott célra az Azure AD-alkalmazás |
    | Hatókör | Hagyja üresen |
    | Állapot | Hagyja üresen |
    | Ügyfél-hitelesítés | `Send as Basic Auth header` |

1. Az ügyfél hasonlóan kell kinéznie:

   ![Postman-ügyfél példa][3]

1. Válassza ki **jogkivonat kérelmezéséhez**.

    >[!NOTE]
    >Ha a hibaüzenetet kapja, "OAuth 2 nem lehet befejezni a", megpróbálkozhat a következőkkel:
    > * Postman, zárja be és nyissa meg újra, és próbálkozzon újra.
  
1. Görgessen lefelé, és válassza ki **használható jogkivonat**.

## <a name="next-steps"></a>További lépések

Hitelesítés a felügyeleti API-kkal kapcsolatos további információkért olvassa el a [hitelesítés API-kkal](./security-authenticating-apis.md).

<!-- Images -->
[1]: media/how-to-configure-postman/implicit-flow.png
[2]: media/how-to-configure-postman/reply-url.png
[3]: media/how-to-configure-postman/postman-oauth-token.png