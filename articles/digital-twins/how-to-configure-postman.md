---
title: A Postman - Azure Digital Twins konfigurálása | Microsoft dokumentumok
description: Ismerje meg, hogyan konfigurálhatja és használhatja a Postman t az Azure Digital Twins API-k teszteléséhez.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: ffcfb4f6ec5f6c654d0b243af85034ab575e0d88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297157"
---
# <a name="how-to-configure-postman-for-azure-digital-twins"></a>A Postman beállítása az Azure Digital Twins-hez

Ez a cikk ismerteti, hogyan konfigurálhatja a Postman REST-ügyfél az Azure digital Twins management API-k kommunikálhat, és tesztelje. Pontosabban a következőket írja le:

* Azure Active Directory-alkalmazás konfigurálása az OAuth 2.0 implicit támogatási folyamat használatára.
* Hogyan használhatja a Postman REST-ügyfelet a rendszerkéréseket a felügyeleti API-khoz.
* Hogyan használhatja a Postmant többrészes POST-kérelmek intézésére a felügyeleti API-khoz?

## <a name="postman-summary"></a>Postás összefoglalása

Az Azure Digital Twins használatának megkezdéséhez egy REST-ügyféleszközzel, például [a Postmanrel](https://www.getpostman.com/) készítse elő a helyi tesztelési környezetet. A Postman-ügyfél segít az összetett HTTP-kérelmek gyors létrehozásában. Töltse le a Postman kliens asztali verzióját a [www.getpostman.com/apps.](https://www.getpostman.com/apps)

[Postman](https://www.getpostman.com/) egy REST tesztelési eszköz, amely megkeresi a legfontosabb HTTP kérés funkciókat egy hasznos asztali és plugin-alapú GUI.

A Postman kliensen keresztül a megoldások fejlesztői megadhatják a HTTP-kérelem *(POST,* *GET,* *UPDATE*, *PATCH*és *DELETE),* a hívandó API-végpont és a TLS használatának fajtáját. A Postman támogatja a HTTP-kérelem fejléceinek, paramétereinek, űrlapadatainak és testszerveinek hozzáadását is.

## <a name="configure-azure-active-directory-to-use-the-oauth-20-implicit-grant-flow"></a>Az Azure Active Directory konfigurálása az OAuth 2.0 implicit támogatási folyamat használatára

1. Kövesse a [gyorsútmutató](quickstart-view-occupancy-dotnet.md#set-permissions-for-your-app) lépéseit egy Azure Active Directory-alkalmazás létrehozásához és konfigurálásához. Azt is megteheti, hogy újra felhasználja a meglévő alkalmazásregisztrációt.

    [![Új postás átirányítási URI konfigurálása](media/how-to-configure-postman/authentication-redirect-uri.png)](media/how-to-configure-postman/authentication-redirect-uri.png#lightbox)

1. Most adjon hozzá egy `https://www.getpostman.com/oauth2/callback` **átirányítási URI-t** a hoz.

1. Jelölje be az **Implicit grant** > **Access tokenek jelölőnégyzetet,** ha engedélyezni szeretné az OAuth 2.0 implicit támogatási folyamatot. Válassza **a Konfigurálás**lehetőséget, majd **a Mentés lehetőséget.**

1. Másolja az Azure Active Directory-alkalmazás **ügyfélazonosítóját.**

## <a name="obtain-an-oauth-20-token"></a>OAuth 2.0 token beszerzése

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]

Állítsa be és konfigurálja a Postman egy Azure Active Directory-jogkivonat beszerzése. Ezt követően adjon meg egy hitelesített HTTP-kérelmet az Azure Digital Twins számára a beszerzett jogkivonat használatával:

1. Ellenőrizze, hogy az **engedélyezési URL helyes-e.** Meg kell venni a formátumot:

    ```plaintext
    https://login.microsoftonline.com/YOUR_AZURE_TENANT.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

    | Név  | Csere erre | Példa |
    |---------|---------|---------|
    | YOUR_AZURE_TENANT | A bérlő vagy szervezet neve. Használja az emberbarát nevet az Azure Active Directory-alkalmazás regisztrációjának alfanumerikus **bérlői azonosítója** helyett. | `microsoft` |

1. Az alkalmazás letöltéséhez nyissa [meg a www.getpostman.com.](https://www.getpostman.com/)

1. Azt akarjuk, hogy GET kérelmet. Válassza az **Engedélyezés** lapot, válassza az OAuth 2.0 lehetőséget, majd válassza **az Új hozzáférési jogkivonat bekerülése**lehetőséget.

    | Mező  | Érték |
    |---------|---------|
    | Engedélyezési típus | `Implicit` |
    | Visszahívási URL | `https://www.getpostman.com/oauth2/callback` |
    | Hitelesítési URL-cím | Az **engedélyezési URL használata** az **1.** |
    | Ügyfél-azonosító | Az előző szakaszból létrehozott vagy újra felhasznált Azure Active Directory-alkalmazás **alkalmazásazonosítójának** használata |
    | Hatókör | Hagyja üresen |
    | Állapot | Hagyja üresen |
    | Ügyfél-hitelesítés | `Send as Basic Auth header` |

1. Az ügyfélnek most a következőképpen kell megjelennie:

    [![Példa postás ügyféltokenre](media/how-to-configure-postman/configure-postman-oauth-token.png)](media/how-to-configure-postman/configure-postman-oauth-token.png#lightbox)

1. Válassza a **Request Token** (Jogkivonat kérése) lehetőséget.
  
1. Görgessen le, és válassza **a Jogkivonat használata lehetőséget.**

## <a name="make-a-multipart-post-request"></a>Többrészes POST-kérelem benyújtása

Az előző lépések végrehajtása után állítsa be a Postman t, hogy egy hitelesített HTTP többrészes POST-kérelmet készítsen:

1. A **Fejlécek** lapon adjon hozzá egy HTTP-kérelemfejléckulcsot **Tartalomtípus** értékkel. `multipart/mixed`

   [![Többrészes/vegyes tartalomtípus megadása](media/how-to-configure-postman/configure-postman-content-type.png)](media/how-to-configure-postman/configure-postman-content-type.png#lightbox)

1. A nem szöveges adatok szerializálása fájlokba. A JSON-adatok JSON-fájlként lesznek mentve.
1. A **Törzs** lapban `form-data`válassza a lehetőséget. 
1. Adja hozzá az egyes fájlokat `File`egy **kulcsnév** hozzárendelésével, és válassza a lehetőséget.
1. Ezután jelölje ki az egyes fájlokat a **Fájl kiválasztása** gombbal.

   [![Postás ügyfél űrlap törzsének például](media/how-to-configure-postman/configure-postman-form-body.png)](media/how-to-configure-postman/configure-postman-form-body.png#lightbox)

   >[!NOTE]
   > * A Postman ügyfél nem követeli meg, hogy a többrészes adattömbökhez manuálisan hozzárendelt **tartalomtípus** vagy **tartalom-megintézkedés tartozz.**
   > * Nem kell megadnia ezeket a fejléceket az egyes részekhöz.
   > * A teljes `multipart/mixed` kérelemhez ki kell választania vagy más megfelelő **tartalomtípust** kell választania.

1. Végül válassza a **Küldés** lehetőséget a többrészes HTTP POST-kérelem elküldéséhez. A sikeres `200` kérelem `201` állapotkódja vagy jelzése. A megfelelő válaszüzenet megjelenik az ügyfélkapcsolatban.

1. A HTTP POST-kérelem adatainak ellenőrzése az API-végpont hívásával: 

   ```URL
   YOUR_MANAGEMENT_API_URL/spaces/blobs?includes=description
   ```

## <a name="next-steps"></a>További lépések

- A digitális twins felügyeleti API-król és azok használatáról az [Azure Digital Twins felügyeleti API-k használata](how-to-navigate-apis.md)című olvasni tudó című olvasni tudó című olvasni tudhat.

- Többrészes kérelmek használatával [blobokat adhat hozzá az Azure Digital Twins entitásaihoz.](./how-to-add-blobs.md)

- Ha többet szeretne tudni a felügyeleti API-kkal való hitelesítésről, olvassa el a [Hitelesítés API-kkal](./security-authenticating-apis.md)című olvasni.
