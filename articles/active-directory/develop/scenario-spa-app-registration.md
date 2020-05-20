---
title: Egyoldalas alkalmazások regisztrálása (SPA) | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre egy egyoldalas alkalmazást (alkalmazás-regisztráció)
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/19/2020
ms.author: hahamil
ms.custom: aaddev
ms.openlocfilehash: 9dc5b446e2ab26ca43c2a300e1af1237353325a3
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682393"
---
# <a name="single-page-application-app-registration"></a>Egyoldalas alkalmazás: alkalmazás regisztrálása

Ha egy egyoldalas alkalmazást (SPA) szeretne regisztrálni a Microsoft Identity platformon, hajtsa végre az alábbi lépéseket. A regisztrációs lépések eltérnek a MSAL. js 1,0-től, amely támogatja az implicit engedélyezési folyamatot és a MSAL. js 2,0-et, amely támogatja az engedélyezési kódot a PKCE-mel.

## <a name="create-the-app-registration"></a>Az alkalmazás regisztrációjának létrehozása

A MSAL. js 1,0-és a 2,0-alapú alkalmazások esetében az alábbi lépések végrehajtásával hozza létre a kezdeti alkalmazás regisztrációját.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Ha a fiókja több bérlőhöz fér hozzá, válassza ki a **könyvtár + előfizetés** szűrőt a felső menüben, majd válassza ki azt a bérlőt, amelynek a létrehozni kívánt alkalmazás-regisztrációt tartalmaznia kell.
1. Keresse meg és válassza ki az **Azure Active Directoryt**.
1. A **kezelés**területen válassza a **Alkalmazásregisztrációk**lehetőséget.
1. Válassza az **új regisztráció**lehetőséget, adjon meg egy **nevet** az alkalmazásnak, és válassza ki az alkalmazás **támogatott fiókjának típusát** . Ne **adjon meg** **átirányítási URI**-t. A különböző fióktípus leírását az [új alkalmazás regisztrálása a Azure Portal használatával](quickstart-register-app.md#register-a-new-application-using-the-azure-portal)című témakörben tekintheti meg.
1. Az alkalmazás regisztrációjának létrehozásához válassza a **regisztráció** lehetőséget.

Ezután konfigurálja az alkalmazás regisztrációját egy **átirányítási URI** -val annak megadásához, hogy a Microsoft Identity platform hová irányítsa át az ügyfelet a biztonsági jogkivonatokkal együtt. Használja az alkalmazásban használt MSAL. js verziójának megfelelő lépéseket:

- [MSAL. js 2,0 Auth-kóddal](#redirect-uri-msaljs-20-with-auth-code-flow) (ajánlott)
- [MSAL. js 1,0 implicit folyamattal](#redirect-uri-msaljs-10-with-implicit-flow)

## <a name="redirect-uri-msaljs-20-with-auth-code-flow"></a>Átirányítási URI: MSAL. js 2,0 és Auth kód folyamata

Az alábbi lépéseket követve hozzáadhat egy átirányítási URI-t egy MSAL. js 2,0 vagy újabb verziót használó alkalmazáshoz. A MSAL. js 2.0 és a PKCE és a CORS által támogatott engedélyezési kód a [böngésző harmadik féltől származó cookie-korlátozásokra](reference-third-party-cookies-spas.md)reagál. Az implicit engedélyezési folyamat nem támogatott a MSAL. js 2.0 + verzióban.

1. A Azure Portal válassza ki az alkalmazás [regisztrációjának létrehozásakor](#create-the-app-registration)korábban létrehozott regisztrációs alkalmazást.
1. A **kezelés**területen válassza a **hitelesítés**lehetőséget, majd válassza **a platform hozzáadása**lehetőséget.
1. A **webalkalmazások**területen válassza az **egylapos alkalmazás** csempét.
1. Az **átirányítási**URI-k területen adjon meg egy [átirányítási URI](reply-url.md)-t. Ne **jelölje be az** **implicit engedélyezés**jelölőnégyzetet.
1. Válassza a **Konfigurálás** lehetőséget az átirányítási URI hozzáadásának befejezéséhez.

Ezzel befejezte az egyoldalas alkalmazás (SPA) regisztrációját, és konfigurált egy átirányítási URI-t, amelybe az ügyfél átirányítja az ügyfelet, és a rendszer minden biztonsági jogkivonatot elküld. Ha az átirányítási URI-t egy **egyoldalas alkalmazás** csempével konfigurálja a **platform hozzáadása** panelen, az alkalmazás regisztrációja úgy van konfigurálva, hogy támogassa az engedélyezési kódot a PKCE és a CORS használatával.

## <a name="redirect-uri-msaljs-10-with-implicit-flow"></a>Átirányítási URI: MSAL. js 1,0 implicit folyamattal

Az alábbi lépéseket követve hozzáadhat egy átirányítási URI-t egy egyoldalas alkalmazáshoz, amely a MSAL. js 1,3-as vagy korábbi verzióját és az implicit engedélyezési folyamatot használja. A MSAL. js 1,3-es vagy korábbi verzióját használó alkalmazások nem támogatják az Auth kód folyamatát.

1. A Azure Portal válassza ki az alkalmazás [regisztrációjának létrehozásakor](#create-the-app-registration)korábban létrehozott regisztrációs alkalmazást.
1. A **kezelés**területen válassza a **hitelesítés**lehetőséget, majd válassza **a platform hozzáadása**lehetőséget.
1. A **webalkalmazások**területen válassza az **egylapos alkalmazás** csempét.
1. Az **átirányítási**URI-k területen adjon meg egy [átirányítási URI](reply-url.md)-t.
1. Az **implicit folyamat**engedélyezése:
    - Ha az alkalmazás bejelentkezik a felhasználók között, válassza az **azonosító tokenek**lehetőséget.
    - Ha az alkalmazásnak egy védett webes API-t is meg kell hívnia, válassza a **hozzáférési jogkivonatok**lehetőséget. További információ ezekről a jogkivonat-típusokról: [azonosító jogkivonatok](id-tokens.md) és [hozzáférési tokenek](access-tokens.md).
1. Válassza a **Konfigurálás** lehetőséget az átirányítási URI hozzáadásának befejezéséhez.

Ezzel befejezte az egyoldalas alkalmazás (SPA) regisztrációját, és konfigurált egy átirányítási URI-t, amelybe az ügyfél átirányítja az ügyfelet, és a rendszer minden biztonsági jogkivonatot elküld. Egy vagy több **azonosító token** és **hozzáférési jogkivonat**kiválasztásával engedélyezte az implicit engedélyezési folyamatot.

## <a name="note-about-authorization-flows"></a>Megjegyzés az engedélyezési folyamatokról

Alapértelmezés szerint egy egyoldalas alkalmazás-platform konfiguráció használatával létrehozott alkalmazás-regisztráció engedélyezi az engedélyezési kód folyamatát. A folyamat előnyeinek kihasználásához az alkalmazásnak a MSAL. js 2,0 vagy újabb verzióját kell használnia.

Ahogy korábban említettük, a MSAL. js 1,3-et használó egylapos alkalmazások az implicit engedélyezési folyamatra korlátozódnak. A jelenlegi [OAuth 2,0 ajánlott eljárások](v2-oauth2-auth-code-flow.md) az engedélyezési kód folyamatának használatát ajánljuk a gyógyfürdők implicit folyamata helyett. A korlátozott élettartamú frissítési tokenek segítségével az alkalmazás alkalmazkodik a [modern böngésző cookie-i adatvédelmi korlátaihoz](reference-third-party-cookies-spas.md), például a Safari itp-hez.

Ha az MSAL. js 2,0 és az engedélyezési kód folyamatát egy alkalmazás-regisztráció által képviselt összes éles üzemi egyoldalas alkalmazás használja, törölje az implicit engedélyezési beállítások az alkalmazás regisztrációjának **hitelesítési** paneljét a Azure Portal. A MSAL. js 1. x és az implicit folyamat használatával folytatott alkalmazások továbbra is működőképesek maradnak, azonban ha az implicit folyamat engedélyezve van (bejelölve).

## <a name="next-steps"></a>További lépések

Ezután konfigurálja az alkalmazás kódját az előző lépésekben létrehozott alkalmazás-regisztráció használatára:.

> [!div class="nextstepaction"]
> [Az alkalmazás kódjának konfigurálása](scenario-spa-app-configuration.md)
