---
title: Hogyan kell kitölteni egy egyénileg fejlesztett alkalmazásba az adott mezők |} A Microsoft Docs
description: Meghatározott mezők kitöltéséhez, amikor regisztrál egy egyéni fejlesztésű alkalmazás az Azure AD útmutatást
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: ryanwi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9bccaa28d34ebff47c7de73a4d9b3d8296ae9fef
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476126"
---
# <a name="how-to-fill-out-specific-fields-for-a-custom-developed-application"></a>Hogyan kell kitölteni egy egyénileg fejlesztett alkalmazásba az adott mezők

Ez a cikk röviden a az alkalmazás regisztrációs űrlapot az összes elérhető mező lehetővé teszi a [az Azure portal](https://portal.azure.com).

## <a name="register-a-new-application"></a>Új alkalmazás regisztrálása

-   Új alkalmazás regisztrálásához lépjen a [az Azure portal](https://portal.azure.com).

-   A bal oldali navigációs ablaktáblán kattintson a **Azure Active Directoryban.**

-   Válasszon **alkalmazásregisztrációk** kattintson **Hozzáadás**.

-   Ez az alkalmazás regisztrációs űrlap nyílt.

## <a name="fields-in-the-application-registration-form"></a>Az alkalmazás regisztrációs űrlap mezőinek


| Mező            | Leírás                                                                              |
|------------------|------------------------------------------------------------------------------------------|
| Name (Név)             | Az alkalmazás neve. Ennek tartalmaznia kell legalább 4 karakterből kell állnia.                |
| Támogatott fióktípusok| Válassza ki, hogy mely fiókok szeretné az alkalmazás támogatja: csak a szervezeti könyvtárban található fiókok, bármely szervezeti directory fiókok vagy bármely szervezeti könyvtárban fiókok és a személyes Microsoft-fiókok.  |
| Átirányítási URI-t (nem kötelező) | Válassza ki az alkalmazást is fejleszt, típusát **webes** vagy **(mobil és asztali) nyilvános ügyfél**, majd adja meg az alkalmazás az átirányítási URI-t (vagy a válasz URL-cím). Webalkalmazás esetében adja meg alkalmazás alap URL-címét. A http://localhost:31544 például a helyi gépen futó webalkalmazás URL-címe lehet. A felhasználók ezzel az URL-címmel jelentkeznek be egy webes ügyfélalkalmazásba. Nyilvános ügyfélalkalmazások esetében adja meg az URI-t, amelyet az Azure AD a jogkivonatválaszok visszaadására használ. Adjon meg egy adott értéket az alkalmazáshoz, például a myapp://auth. Webalkalmazásokra és natív alkalmazásokra konkrét példákat [rövid útmutatóinkban](https://docs.microsoft.com/azure/active-directory/develop/#quickstarts) talál.|

Miután megadta a fenti mezőket, az alkalmazás regisztrálva van az Azure Portalon, és a rendszer átirányítja az alkalmazás – áttekintés oldalra. A bal oldali ablaktábla, a beállításlapok **kezelés** testre szabhatja az alkalmazás további mezőket rendelkezik. Az alábbi táblázatok bemutatják az összes mezőt. Csak jelennének meg ezeket a mezőket, attól függően, hogy létrehozott egy webalkalmazás vagy egy nyilvános ügyfélalkalmazás egy részét.

### <a name="overview"></a>Áttekintés
| Mező           | Leírás        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alkalmazásazonosító  | Amikor regisztrál egy alkalmazást, az Azure AD rendeli az alkalmazást egy alkalmazás azonosítóját. Az alkalmazás-azonosító egyedi módon azonosítja az alkalmazást az Azure AD a hitelesítési kérelmek, valamint erőforrások eléréséhez használható, mint a Graph API-t.                                                          |
| Alkalmazásazonosító URI      | Ez lehet egy egyedi URI-t, általában a következő formában **https://&lt;bérlői\_neve&gt;/&lt;alkalmazás\_neve&gt;.** Adni az erőforrást, amely a token kell kiadni, az egyedi azonosítóként a engedélyezési engedélyezési folyamat során használatos. A "aud" jogcím a kiállított hozzáférési jogkivonat szintén elérhetetlenné válik. |

### <a name="branding"></a>Védjegyzési

| Mező           | Leírás        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Új embléma feltöltése | Ezzel az alkalmazás emblémát tölthet fel. Az embléma .bmp, .jpg vagy .png formátumúnak kell lennie, és a fájl mérete kisebb, mint 100 KB lehet. A kép 215 x 215 képpont, a középső kép mérete 94 x 94 képpont kell lennie.|
| Kezdőlap URL-címe   | Ez az alkalmazás regisztrációja során megadott bejelentkezési URL.|

### <a name="authentication"></a>Hitelesítés

| Mező           | Leírás        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Kijelentkezési URL      | Ez az egyszeri kijelentkezési kijelentkezési URL-CÍMÉT. Az Azure AD kijelentkezési kérelmet küld az URL-címet a felhasználónak törölnie a munkamenetet az Azure ad-vel bármely más regisztrált alkalmazás használatával.|
| Támogatott fióktípusok  | Ez a kapcsoló megadja, hogy használható-e az alkalmazás több bérlő. Általában ez azt jelenti, hogy külső szervezetek számára az alkalmazás használatának annak regisztrálásával a bérlőben, és a céges adatokhoz való hozzáférést.|
| Redirect URLs      | Az átirányítás, vagy a válasz, akkor URL-címek olyan a végpontok, amelyeken az Azure ad-ben visszaadja az alkalmazás által kért jogkivonatokat. Natív alkalmazások esetén ez a Ha a felhasználó a sikeres hitelesítést követően érkezik. Az Azure AD ellenőrzi, hogy az átirányítási URI-t az OAuth 2.0 kérelemben megadja az alkalmazás megfelel a portálon a regisztrált értékeket.|

### <a name="certificates-and-secrets"></a>Tanúsítványok és titkos kulcsok

| Mező           | Leírás        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ügyfél titkos kulcsok            | Ügyfél titkos kódok vagy programozott hozzáférés webes API-k, felhasználói beavatkozás nélkül az Azure AD által védett kulcsok hozhat létre. Az a **új titkos ügyfélkulcsot** lapon adja meg a kulcs leírása és a lejárati dátumot, és mentse a kulcs létrehozásához. Ellenőrizze, hogy helyre mentse, biztonságos, akkor később elérheti azt.             |

## <a name="next-steps"></a>További lépések
[Alkalmazások kezelése az Azure Active Directoryval](../manage-apps/what-is-application-management.md)
