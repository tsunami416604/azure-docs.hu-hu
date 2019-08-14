---
title: A Poster konfigurálása az Azure Digital Twins szolgáltatáshoz | Microsoft Docs
description: A Poster konfigurálása az Azure Digital Twins szolgáltatáshoz.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 08/09/2019
ms.author: v-adgera
ms.openlocfilehash: 7ceb36d818c84642461372f0df70c8088908550c
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68965809"
---
# <a name="how-to-configure-postman-for-azure-digital-twins"></a>A Poster konfigurálása az Azure Digital Twins szolgáltatáshoz

Ez a cikk bemutatja, hogyan konfigurálhatja a Poster REST-ügyfelet az Azure digitális Twins felügyeleti API-k használatához és teszteléséhez. Pontosabban leírja a következőket:

* Azure Active Directory alkalmazás konfigurálása a OAuth 2,0 implicit engedélyezési folyamat használatára.
* A Poster REST-ügyfél használata, hogy jogkivonat-tartalmú HTTP-kéréseket készítsen a felügyeleti API-khoz.
* A Poster használatával többrészes POST-kérelmeket készíthet a felügyeleti API-khoz.

## <a name="postman-summary"></a>Poster – összefoglalás

Ismerkedjen meg az Azure Digital Twins szolgáltatással egy REST-ügyfél eszközzel [](https://www.getpostman.com/) , például a Poster használatával, és készítse elő a helyi tesztelési környezetet. A Poster-ügyfél segít az összetett HTTP-kérések gyors létrehozásában. A Poster-ügyfél asztali verziójának letöltéséhez nyissa meg a [www.getpostman.com/apps](https://www.getpostman.com/apps).

[](https://www.getpostman.com/) A Poster egy Rest-tesztelési eszköz, amely a kulcsfontosságú HTTP-kérések funkcióit egy hasznos asztali és beépülő modul-alapú grafikus felhasználói felületre helyezi.

A Poster-ügyfélen keresztül a megoldások fejlesztői megadhatják a HTTP-kérelem típusát (*post*, *Get*, *Update*, *patch*és *delete*), az API-végpontot a híváshoz és az SSL használatát. A Poster a HTTP-kérelmek fejlécének, paramétereinek, űrlap-és testületeinek hozzáadását is támogatja.

## <a name="configure-azure-active-directory-to-use-the-oauth-20-implicit-grant-flow"></a>Azure Active Directory konfigurálása a OAuth 2,0 implicit engedélyezési folyamat használatára

Konfigurálja a Azure Active Directory alkalmazást az OAuth 2,0 implicit engedélyezési folyamat használatára.

1. Az [ebben](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad) a rövid útmutatóban található lépéseket követve hozzon létre egy natív típusú Azure ad-alkalmazást. Vagy újból felhasználhat egy meglévő natív alkalmazás-regisztrációt is.

1. A **szükséges engedélyek**területen válassza a **Hozzáadás** lehetőséget, majd adja meg az **Azure Digital Twins** elemet az **API-hozzáférés hozzáadása**területen. Ha a keresés nem találja meg az API-t, keressen inkább az **Azure Smart Spaces** kifejezésre. Ezután válassza az **engedélyek megadása > delegált engedélyek** és **kész**lehetőséget.

    [![Azure Active Directory alkalmazás-regisztráció API hozzáadása](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png)](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png#lightbox)

1. Válassza a **jegyzékfájl** lehetőséget az alkalmazáshoz tartozó alkalmazási jegyzékfájl megnyitásához. *Oauth2AllowImplicitFlow* beállítása a `true`következőre:.

    [![Azure Active Directory implicit folyamat](media/how-to-configure-postman/implicit-flow.png)](media/how-to-configure-postman/implicit-flow.png#lightbox)

1. Adja meg a **Válasz URL-címét** a `https://www.getpostman.com/oauth2/callback`következőre:.

    [![Azure Active Directory válasz URL-címe](media/how-to-configure-postman/reply-url.png)](media/how-to-configure-postman/reply-url.png#lightbox)

1. Másolja és őrizze meg Azure Active Directory ALKALMAZÁSának azonosítóját. Ezt az alábbi lépések használják.

## <a name="obtain-an-oauth-20-token"></a>OAuth 2,0 token beszerzése

Ezután állítsa be és konfigurálja a Poster-t Azure Active Directory token beszerzéséhez. Ezt követően hozzon végre egy hitelesített HTTP-kérést az Azure Digital Twins számára a beszerzett jogkivonat használatával:

1. Az alkalmazás letöltéséhez nyissa meg a [www.getpostman.com](https://www.getpostman.com/) .
1. Ellenőrizze, hogy helyes-e az **engedélyezési URL-cím** . A formátum a következőket teszi:

    ```plaintext
    https://login.microsoftonline.com/YOUR_AZURE_TENANT.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

    | Name (Név)  | Csere erre | Példa |
    |---------|---------|---------|
    | YOUR_AZURE_TENANT | A bérlő vagy szervezet neve | `microsoft` |

1. Válassza az **Engedélyezés** lapot, válassza a **OAuth 2,0**, majd az **új hozzáférési jogkivonat**beolvasása elemet.

    | Mező  | Value |
    |---------|---------|
    | Engedélyezési típus | `Implicit` |
    | Visszahívási URL | `https://www.getpostman.com/oauth2/callback` |
    | Hitelesítési URL-cím | Az **engedélyezési URL-cím** használata a 2. lépésből |
    | Ügyfél-azonosító | Az előző szakaszban létrehozott vagy repurposed Azure Active Directory **alkalmazás azonosítójának** használata |
    | Scope | Hagyja üresen |
    | Állapot | Hagyja üresen |
    | Ügyfél-hitelesítés | `Send as Basic Auth header` |

1. Az ügyfélnek ekkor a következőképpen kell megjelennie:

    [![Példa a Poster-ügyfélre](media/how-to-configure-postman/postman-oauth-token.png)](media/how-to-configure-postman/postman-oauth-token.png#lightbox)

1. Válassza a **kérelem tokenje**elemet.

    >[!TIP]
    >Ha a "OAuth 2 nem sikerült befejezni" hibaüzenet jelenik meg, próbálja meg a következőket:
    > * Zárjuk be a Poster-t, majd nyissa meg újra, és próbálkozzon újra.
  
1. Görgessen le, és válassza a **token használata**lehetőséget.

<div id="multi"></div>

## <a name="make-a-multipart-post-request"></a>Többrészes POST-kérelem létrehozása

Az előző lépések elvégzése után konfigurálja a Poster-t egy hitelesített HTTP többrészes POST-kérelem létrehozásához:

1. A **fejléc** lapon adja hozzá a HTTP-kérelem fejlécének kulcsához tartozó **Content-Type** értéket `multipart/mixed`.

   [![Tartalom típusa többrészes/vegyes](media/how-to-configure-postman/content-type.png)](media/how-to-configure-postman/content-type.png#lightbox)

1. Nem szöveges adatfájlok szerializálása fájlokba. A JSON-fájlok JSON-fájlként lesznek mentve.
1. A **törzs** lapon adja hozzá az egyes fájlokat egy **Kulcsnév** hozzárendelésével, vagy válassza a `file` vagy `text`a elemet.
1. Ezután válassza ki az egyes fájlokat a **Fájl választása** gombbal.

   [![Példa a Poster-ügyfélre](media/how-to-configure-postman/form-body.png)](media/how-to-configure-postman/form-body.png#lightbox)

   >[!NOTE]
   > * A Poster-ügyfélnek nincs szüksége arra, hogy a többrészes adattömbökhöz manuálisan hozzárendelt **tartalom-típus** vagy **tartalom-törlés**legyen.
   > * Az egyes részekhez nem kell megadnia ezeket a fejléceket.
   > * Ki kell választania `multipart/mixed` vagy egy másik megfelelő **Content-Type értéket** a teljes kérelemhez.

1. Végül válassza a **Küldés** lehetőséget a többrészes http post-kérelem elküldéséhez.

## <a name="next-steps"></a>További lépések

- Ha többet szeretne megtudni a digitális Twins felügyeleti API-król és azok használatáról, olvassa el az [Azure digitális Twins felügyeleti API](how-to-navigate-apis.md)-k használatát ismertető témakört.

- A többrészes kérelmek használatával [blobokat adhat hozzá az Azure Digital Twins entitásokhoz](./how-to-add-blobs.md).

- A felügyeleti API-k hitelesítésének megismeréséhez olvassa el a [hitelesítés API](./security-authenticating-apis.md)-kkal című témakört.