---
title: Az Azure Portal regisztrációs mezői az egyéni fejlesztésű alkalmazásokhoz
description: Útmutató egy egyéni fejlesztésű alkalmazás azure AD-vel történő regisztrálásához
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
ms.openlocfilehash: c44575ca43063388d5c65855542cf15700d2cb5a
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80883168"
---
# <a name="azure-portal-registration-fields-for-custom-developed-apps"></a>Az Azure Portal regisztrációs mezői az egyéni fejlesztésű alkalmazásokhoz

Ez a cikk röviden ismerteti az Azure Portal alkalmazásregisztrációs űrlapján elérhető összes [mezőt.](https://portal.azure.com)

## <a name="register-a-new-application"></a>Új alkalmazás regisztrálása

-   Új alkalmazás regisztrálásához keresse meg az [Azure Portalt.](https://portal.azure.com)

-   A bal oldali navigációs ablakban kattintson az **Azure Active Directory.**

-   Válassza **az Alkalmazásregisztrációk** lehetőséget, és kattintson **a Hozzáadás gombra.**

-   Ez megnyitja a jelentkezési lapot.

## <a name="fields-in-the-application-registration-form"></a>Mezők a jelentkezési lapon

| Mező            | Leírás                                                                              |
|------------------|------------------------------------------------------------------------------------------|
| Név             | Az alkalmazás neve. Legalább négy karakterből kell állnia.                |
| Támogatott fióktípusok| Válassza ki, hogy az alkalmazás mely fiókokat szeretné támogatni: csak ebben a szervezeti címtárban lévő fiókokat, bármely szervezeti címtárban lévő fiókokat, illetve bármely szervezeti címtárban lévő fiókokat és személyes Microsoft-fiókokat.  |
| Uri átirányítása (nem kötelező) | Válassza ki az általad épített alkalmazás típusát, **a webes** vagy a **nyilvános ügyfelet (mobil & asztali számítógépen),** majd adja meg az alkalmazás átirányítási URI-ját (vagy válasz URL-címét). Webalkalmazás esetében adja meg alkalmazás alap URL-címét. A http://localhost:31544 például a helyi gépen futó webalkalmazás URL-címe lehet. A felhasználók ezzel az URL-címmel jelentkeznek be egy webes ügyfélalkalmazásba. Nyilvános ügyfélalkalmazások esetében adja meg az URI-t, amelyet az Azure AD a jogkivonatválaszok visszaadására használ. Adjon meg egy, az alkalmazásra jellemző értéket, például myapp://auth. A webes alkalmazásokra vagy natív alkalmazásokra vonatkozó konkrét példák megtekintéséhez tekintse meg [a rövid útmutatókat.](https://docs.microsoft.com/azure/active-directory/develop)|

Miután kitöltötte a fenti mezőket, az alkalmazás regisztrálva lesz az Azure Portalon, és átirányítja az alkalmazás áttekintése oldalra. A bal oldali ablaktábla **Kezelés** csoportban található beállítási lapokon több mező is található az alkalmazás testreszabásához. Az alábbi táblázatok az összes mezőt ismertetik. Ezeknek a mezőknek csak egy részhalmaza jelenik meg, attól függően, hogy létrehozott-e webalkalmazást vagy nyilvános ügyfélalkalmazást.

### <a name="overview"></a>Áttekintés

| Mező           | Leírás        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alkalmazásazonosító  | Amikor regisztrál egy alkalmazást, az Azure AD hozzárendeli az alkalmazás egy alkalmazásazonosítót. Az alkalmazásazonosító segítségével egyedileg azonosíthatja az alkalmazást az Azure AD hitelesítési kérelmekben, valamint erőforrások, például a Graph API eléréséhez.                                                          |
| Alkalmazásazonosító URI      | Ennek egyedi URI-nak kell lennie, amely általában **&lt;a https:// bérlői\_névalkalmazás&gt;/&lt;\_nevének formájában jelenik&gt;meg.** Ezt az engedélyezési engedélyezési folyamat során, egyedi azonosítóként használja az erőforrás, amelya jogkivonatot ki kell adni. Ez is lesz a "aud" jogcím a kiadott hozzáférési jogkivonatban. |

### <a name="branding"></a>Védjegyezés

| Mező           | Leírás        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Új embléma feltöltése | Ezzel feltölthet egy emblémát az alkalmazáshoz. Az emblémának .bmp, .jpg vagy .png formátumúnak kell lennie, és a fájlméretnek 100 KB-nál kisebbnek kell lennie. A kép méretei 215x215 képpont méretűek, a központi képméretek 94x94 képpont.|
| Kezdőlap URL-címe   | Ez az alkalmazás regisztrációja során megadott bejelentkezési URL-cím.|

### <a name="authentication"></a>Hitelesítés

| Mező           | Leírás        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Kijelentkezés URL-címe      | Ez az egyszeri kijelentkezési kijelentkezési URL-cím. Az Azure AD egy kijelentkezési kérelmet küld erre az URL-címre, amikor a felhasználó bármely más regisztrált alkalmazás használatával törli a munkamenetét az Azure AD-vel.|
| Támogatott fióktípusok  | Ez a kapcsoló azt határozza meg, hogy az alkalmazást több bérlő is használhatja-e. Ez általában azt jelenti, hogy a külső szervezetek használhatják az alkalmazást a bérlőben történő regisztrálásával és a szervezet adataihoz való hozzáférés biztosításával.|
| Átirányítási URL-címek      | Az átirányítás vagy a válasz URL-címek azok a végpontok, ahol az Azure AD az alkalmazás által kért jogkivonatokat adja vissza. Natív alkalmazások esetén a sikeres engedélyezés után a rendszer a felhasználót küldi el. Az Azure AD ellenőrzi, hogy az átirányítás URI az alkalmazás által kiküldött az OAuth 2.0 kérelem megfelel a portálon regisztrált értékek egyikének.|

### <a name="certificates-and-secrets"></a>Tanúsítványok és titkok

| Mező           | Leírás        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ügyfél titkos kódja            | Ügyféltitkokat vagy kulcsokat hozhat létre az Azure AD által felhasználói beavatkozás nélkül biztosított webes API-k programozott eléréséhez. Az **Új ügyféltitkos** laplapon adja meg a kulcs leírását és a lejárati dátumot, és mentse a kulcs létrehozásához. Ügyeljen arra, hogy mentse el egy biztonságos helyre, mivel később nem fogja tudni elérni.             |

## <a name="next-steps"></a>További lépések

[Alkalmazások kezelése az Azure Active Directoryval](../manage-apps/what-is-application-management.md)
