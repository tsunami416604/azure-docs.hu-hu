---
title: Az Azure-portál képzés útmutató – Azure alkalmazásregisztrációk
description: Az eszköz kódmegadásának használata embedded és a böngésző nélküli hitelesítési folyamatokat hozhat létre.
services: active-directory
documentationcenter: ''
author: archieag
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: aragra
ms.reviewer: lenalepa, keyam
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 118c6ecb16d325a384246a0b3d9e685f6f6f04ee
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870117"
---
# <a name="training-guide-app-registrations-in-the-azure-portal"></a>Képzési-Útmutató: Az Azure Portalon alkalmazásregisztrációk  

Az új számos fejlesztései található [alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) élmény az Azure Portalon. Ha több ismeri a korábbi élmény, a képzési útmutatóban segítségével az új felhasználói felület használatának első lépéseit.

## <a name="key-changes"></a>Legfontosabb változások

- Alkalmazásregisztrációk nem kell vagy egy **web app és az API** vagy egy **natív** alkalmazást. Az összes ilyen regisztrálja a megfelelő átirányítási URI-nak az ugyanazon alkalmazás regisztrációját is használhatja.
- Jelentkezzen be a régi környezetből támogatott alkalmazások csak fiókok szervezeti (Azure AD). Alkalmazások regisztráltak egybérlős (támogatása csak a szervezeti fiókokat a címtárból, az alkalmazás sikeresen regisztrálva lett), és sikerült módosítani kell, hogy a több-bérlős (az összes munkahelyi fiókok támogatása). Az új funkció lehetővé teszi, hogy regisztrálja az alkalmazásokat, amelyek is támogatja a mindkét ezek a lehetőségek, valamint egy harmadik beállítás: az összes szervezeti fiókokat, valamint a személyes Microsoft-fiókok.
- A korábbi élmény csak volt elérhető, ha bejelentkezett az Azure Portalra, szervezeti fiókkal. Az új felhasználói felület, a személyes Microsoft-fiókok, amelyek nem tartoznak, és a címtár is használhatja.

## <a name="list-of-applications"></a>Alkalmazások listája

- Az új alkalmazás lista mutatja azokat az alkalmazásokat, amelyek az örökölt alkalmazáson keresztül regisztráltak regisztrációk tapasztalattal az Azure Portalon (olyan alkalmazásokba, jelentkezzen be az Azure AD-fiókok), valamint alkalmazásokat regisztrálni, ha a [alkalmazásregisztrációs portálon](https://apps.dev.microsoft.com/) (jelentkezzen be az Azure AD alkalmazások és a személyes Microsoft-fiókok).
- Az új alkalmazások listájának nem rendelkezik egy **alkalmazástípus** oszlop (mivel egyetlen alkalmazásregisztráció számos különböző típusú is lehet), és két további oszlopot tartalmaz: egy **létrehozott** oszlop és a egy **tanúsítványok & titkos kódok** oszlopot, amely a hitelesítő adatokat, amelyeket az alkalmazás regisztrált (jelenlegi, hamarosan lejár vagy lejárt) állapotát jeleníti meg.

## <a name="new-app-registration"></a>Új alkalmazás regisztrálása

A korábbi élmény esetén az alkalmazás regisztrálása is számára meg kell adnia: **Név**, **alkalmazástípus**, és **bejelentkezési URL-cím/átirányítási URI**. A létrehozott alkalmazások voltak, ami azt jelenti, hogy azok csak támogatott szervezeti fiókokat a címtárból, az alkalmazás sikeresen regisztrálva lett az Azure AD csak egybérlős alkalmazások.

Az új felületen meg kell adnia egy **neve** az alkalmazáshoz, és válassza a **támogatott fióktípusok**. Opcionálisan megadhat egy **átirányítási URI**. Átirányítási URI-t ad meg, ha szüksége, adja meg, hogy webes vagy nyilvános (asztali és mobil). További információ az új alkalmazásregisztrációk alkalmazás regisztrálása az élményt, lásd: [ebben a rövid útmutatóban](quickstart-register-app.md).

## <a name="the-legacy-properties-page"></a>A régi Tulajdonságok lap

A korábbi élmény kellett egy **tulajdonságok** lap, amelyen az új felhasználói felület nincs telepítve. A **tulajdonságok** panel kellett a következő mezőket: **Név**, **Objektumazonosító**, **Alkalmazásazonosító**, **Alkalmazásazonosító URI-t**, **embléma**, **kezdőlap URL-címe** , **Kijelentkezési URL-címe**, **feltételeinek szolgáltatás URL-címe**, **adatvédelmi nyilatkozatának URL-címe**, **alkalmazástípus**, és  **Több-bérlős.**

Itt látható, ahol megtalálhatja az új felhasználói felületre a megfelelő funkciók:

- **Név**, **embléma**, **kezdőlap URL-címe**, **feltételeinek szolgáltatás URL-címe**, és **adatvédelmi nyilatkozatának URL-címe** el, az alkalmazás  **Védjegyzési** lapot.
- **Objektumazonosító:** és **Alkalmazásazonosítót (ügyfél)** be van kapcsolva a **áttekintése** lapot.
- A funkció szabályozza a **több-bérlős** felváltotta a korábbi élmény váltógombot **fióktípus esetében támogatott** a a **hitelesítési** lap. További információ a több-bérlős hogyan képezi le a támogatott típusú Fiókbeállítások: [ebben a rövid útmutatóban](quickstart-modify-supported-accounts.md).
- **Kijelentkezési URL-címe** most már a **hitelesítési** lapot.
- **Az alkalmazástípus** már nem érvényes mező. Ehelyett átirányítási URI-k (amely megtalálhatja a **hitelesítési** lap) határozza meg, milyen típusú alkalmazások támogatottak.
- **Alkalmazásazonosító URI-t** neve most **Alkalmazásazonosító URI-ja** és megtalálhatja a a **közzé API-k** panelen. A korábbi élmény esetén ez a tulajdonság lett automatikus – regisztrálva a következő formátumban: `https://{tenantdomain}/{appID}` (például `https://microsoft.onmicrosoft.com/aeb4be67-a634-4f20-9a46-e0d4d4f1f96d`). Az új formátumban, automatikusan létrehozott `api://{appID}`, de explicit módon menteni kell.

## <a name="reply-urlsredirect-urls"></a>Válasz URL-címek/átirányítási URL-címek

A korábbi élmény esetén az alkalmazás volt egy **válasz URL-címek** lapot. Az új felhasználói felület, a válasz URL-címek egy alkalmazásban található **hitelesítési** szakaszban. Emellett azok nevezzük **átirányítási URI-k**. Emellett a formátum az átirányítási URI-k megváltozott. Ezek szükségesek az alkalmazás típusát (web- vagy nyilvános) társítani kell. Emellett biztonsági okokból helyettesítő karaktereket és http:// sémákat nem támogatottak (kivéve a http://localhost).

## <a name="keyscertificates--secrets"></a>Kulcsok és tanúsítványok és titkos kulcsok

A korábbi élmény esetén az alkalmazás volt **kulcsok** lapot. Az új funkció a át lett nevezve a **tanúsítványok és titkos kulcsok**. Emellett **nyilvános kulcsok** nevezzük **tanúsítványok** és **jelszavak** nevezzük **ügyfél titkos kódok**.

## <a name="required-permissionsapi-permissions"></a>Szükséges engedélyek és az API-engedélyek

- A korábbi élmény esetén az alkalmazás volt egy **szükséges engedélyek** lapot. Az új funkció a át lett nevezve a **API-engedélyek**.
- A korábbi élmény az API-k kiválasztásakor dönthet a Microsoft APIs vagy keresést végezhet a bérlő szolgáltatásnevek kis listájából. Az új felhasználói felület, a több lap közül választhat: **A Microsoft APIs**, **API-k saját szervezete**, vagy **saját API-k**. A keresősávba, a **API-kat a szervezeten** használja az egyszerű szolgáltatások lap a keresésre a bérlő. 

   > [!NOTE]
   > Ezen a lapon nem jelenik meg, ha az alkalmazás nem bérlőhöz társított. További információ kérésének engedélyeket az új felhasználói felület segítségével: [ebben a rövid útmutatóban](quickstart-configure-app-access-web-apis.md).

- Kellett a korábbi élmény egy **engedélyeket** gombot a felső részén a **engedélyeket kért** lap. Az új élményt, és van egy **hozzájárulását** szakasz a egy **biztosítson rendszergazdai jóváhagyás** gombra egy alkalmazás **API-engedélyek** szakaszban. Emellett vannak eltérések a módon a gombok függvény:
   - A korábbi élmény esetén a logika maximalizálásában, attól függően, a bejelentkezett felhasználó és a kért engedélyeket. A logikai a következő volt:
      - Ha csak felhasználói jóváhagyás képes engedélyeket kért lettek, és a bejelentkezett felhasználó nem rendszergazda, a felhasználó tudta a kért engedélyeket a felhasználó hozzájárulását.
      - Ha, amely rendszergazdai jóváhagyást igénylő legalább egy engedélyt kért a bejelentkezett felhasználó nem rendszergazda, a felhasználó van hiba a hozzájárulás megkísérlése során.
      - A bejelentkezett felhasználó volt rendszergazda, ha a kért engedélyeket kapnak rendszergazdai jóváhagyás.
   - Az új felhasználói felületre, az csak egy rendszergazdai jóváhagyás adhat. Amikor kiválasztja a rendszergazda a **biztosítson rendszergazdai jóváhagyás** gomb, rendszergazdai jóváhagyás engedélyezett a kért engedélyeket.

## <a name="deleting-an-app-registration"></a>Az alkalmazásregisztráció törlése

A korábbi élmény esetén alkalmazás kellett egybérlős törölni kell. A Törlés gombra. a több-bérlős alkalmazások le lett tiltva. Az új felület apps munkalehetőségek törölhetők, de meg kell erősítenie a műveletet. Alkalmazásregisztrációk törlésével kapcsolatos további információkért lásd: [ebben a rövid útmutatóban](quickstart-remove-app.md).

## <a name="application-manifest"></a>Alkalmazásjegyzék

A régi és új funkciókat eltérő verziókat használnak, a JSON-t az alkalmazásjegyzék-szerkesztőben formátumát. További információ: [alkalmazásjegyzék](reference-app-manifest.md).

## <a name="new-ui"></a>Új felhasználói felület

Nincs új felhasználói felület azokhoz a tulajdonságokhoz, amelyek korábban csak beállíthatók az alkalmazásjegyzék-szerkesztőben vagy az API használatával, vagy nem létezik.

- **Implicit folyamat megadása** (oauth2AllowImplicitFlow) található a **hitelesítési** lapot. Ellentétben a korábbi élmény esetén a engedélyezheti a **hozzáférési jogkivonatokat** vagy **azonosító-jogkivonatokat**, vagy mindkettőt.
- **Az API által meghatározott hatókörök** (oauth2Permissions) és **ügyfélalkalmazások jogosult** (preAuthorizedApplications) segítségével konfigurálható a **közzé API-k** lapot. További információ a webes API-k és engedélyek/hatókörök közzé egy alkalmazás konfigurálása, lásd: [ebben a rövid útmutatóban](quickstart-configure-app-expose-web-apis.md).
- **Közzétevő tartománya** (amely megjelenik a felhasználók számára a [alkalmazás jóváhagyási kérése](application-consent-experience.md)) található a **Branding panel** lap. A közzétevői tartományt konfigurálásáról további információ: [ebben az útmutatóban](howto-configure-publisher-domain.md).

## <a name="limitations"></a>Korlátozások

Az új funkció a következő korlátozások vonatkoznak:

- Az új funkció jelenleg nem érhető el az Azure AD B2C-bérlők.
- Ügyfél titkos kulcsokat (alkalmazásjelszók) formátuma eltér a korábbi felhasználói élményt, és működésképtelenné válik a parancssori felület.
- Az érték támogatott fiókok módosítása nem támogatott a felhasználói felületen. Kell használnia az alkalmazásjegyzék, kivéve, ha a Váltás egybérlős és több-bérlős Azure AD között.
