---
title: Az Azure AD-alkalmazásokhoz és az adatvédelmi nyilatkozat feltételeit |} A Microsoft Docs
description: Ismerje meg, hogy hogyan konfigurálhat a regisztrált Azure AD használata az alkalmazások és az adatvédelmi nyilatkozat feltételeit.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.reviwer: lenalepa, sureshja
ms.custom: aaddev
ms.openlocfilehash: 2195a43665f99e8388b13fc6dc3e528496bd3901
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50242687"
---
# <a name="terms-of-service-and-privacy-statement-for-registered-azure-active-directory-apps"></a>A regisztrált az Azure Active Directory-alkalmazások és az adatvédelmi nyilatkozat feltételeit

Fejlesztők számára, akik alkalmazásokat, amelyekbe beépül az Azure Active Directory (Azure AD) és a Microsoft-fiókok kezelése tartalmaznia kell az alkalmazás használati és az adatvédelmi nyilatkozat mutató hivatkozásokat. A felhasználók számára a felhasználói jóváhagyási felületen keresztül illesztett és az adatvédelmi nyilatkozat feltételeit. Ezek segítenek a felhasználókkal, hogy nyugodtan megbízhatják az alkalmazást. A feltételeket és adatvédelmi nyilatkozata különösen létfontosságúak a felhasználók felé néző több-bérlős alkalmazások – alkalmazásokat, amelyek több címtárat használ vagy bármilyen Microsoft-fiókkal érhetők el.

Ön felelős létrehozása a használati feltételekre és az adatvédelmi nyilatkozat dokumentumok az alkalmazáshoz, és ezeket a dokumentumokat, az URL-címeket, amelyek biztosítják. A több-bérlős alkalmazások, amelyek nem adja meg ezeket a hivatkozásokat a felhasználói jóváhagyási felületen az alkalmazás egy riasztást, amely előfordulhat, hogy ismeretlen hozzájárul ahhoz, hogy az alkalmazás jelennek meg.

> [!NOTE]
> * Egybérlős alkalmazások nem jelennek meg a riasztást.
> * Ha egyik vagy mindkét a két kapcsolat hiányzik, akkor az alkalmazás riasztást jelennek meg.

## <a name="user-consent-experience"></a>Felhasználói jóváhagyás élmény

Az alábbi példák bemutatják a felhasználói élmény hozzájárulás, ha a feltételeket és adatvédelmi nyilatkozata vannak konfigurálva, és ha ezek a hivatkozások nem konfigurált.

![Képernyőképek és a egy adatvédelmi nyilatkozat és a megadott szolgáltatási feltételeit nélkül](./media/howto-add-terms-of-service-privacy-statement/user-consent-exp-privacy-statement-terms-service.png)

## <a name="formatting-links-to-the-terms-of-service-and-privacy-statement-documents"></a>A feltételeket és adatvédelmi nyilatkozata dokumentumok formázási hivatkozások

Mielőtt hivatkozásokat ad hozzá az alkalmazás használati és az adatvédelmi nyilatkozat dokumentumok, ellenőrizze az URL-címeket az alábbiakra.

| Irányelv     | Leírás                           |
|---------------|---------------------------------------|
| Formátum        | Érvényes URL-cím                             |
| Érvényes sémák | HTTP és HTTPS</br>Azt javasoljuk, hogy HTTPS |
| Maximális hossz    | 2048 karakternél                       |

Példák: `https://myapp.com/terms-of-service` és `https://myapp.com/privacy-statement`

## <a name="adding-links-to-the-terms-of-service-and-privacy-statement"></a>Hivatkozások hozzáadása a feltételeket és adatvédelmi nyilatkozata

Amikor készen áll a szolgáltatás és az adatvédelmi nyilatkozat feltételeit, adhat hozzá hivatkozásokat ezeket a dokumentumokat az alkalmazásban az alábbi módszerek egyikének használatával:

* [Az Azure Portalon keresztül](#registered-in-azure-portal)
* [Az alkalmazásregisztrációs portálon vagy a fejlesztői központ](#registered-in-app-reg-portal)
* [Az alkalmazás objektum JSON használatával](#app-object-json)
* [Az MSGraph beta REST API használatával](#msgraph-beta-rest-api)

### <a name="registered-in-azure-portal"></a>Ha regisztrálta az alkalmazást az Azure Portalon

Ha regisztrálta az alkalmazást az Azure Portalon, kövesse az alábbi lépéseket.

1. Jelentkezzen be az  [Azure Portalra](https://portal.azure.com/).
2. Keresse meg a **Alkalmazásregisztrációk** szakaszt, és válassza ki az alkalmazást.
3. Nyissa meg a **tulajdonságok** szakaszában.
4. Töltse ki a **feltételek az URL-címe** és **adatvédelmi nyilatkozatának URL-címe** mezőket.
5. Mentse a módosításokat.

    ![Alkalmazás Tulajdonságok szakaszában feltételeket és adatvédelmi nyilatkozat URL-címek](./media/howto-add-terms-of-service-privacy-statement/azure-portal-terms-service-privacy-statement-urls.png)

### <a name="registered-in-app-reg-portal"></a>Ha regisztrálta az alkalmazást az alkalmazás regisztrációs portálon

Ha alkalmazását az alkalmazásregisztrációs portálon vagy a fejlesztői központban regisztrált, kövesse az alábbi lépéseket.

1. Jelentkezzen be a [alkalmazásregisztrációs portálon](https://apps.dev.microsoft.com/).
2. Válassza ki az alkalmazást, és görgessen a **profil** szakaszban.
3. Töltse ki a **feltételek az URL-címe** és **adatvédelmi nyilatkozatának URL-címe** mezőket.
4. Mentse a módosításokat.

    ![Alkalmazás profil szakaszában feltételeket és adatvédelmi nyilatkozat URL-címek](./media/howto-add-terms-of-service-privacy-statement/app-registration-portal-profile-terms-service-privacy-statement-urls.png)

### <a name="app-object-json"></a>Az alkalmazás objektum JSON használatával

Ha az alkalmazás objektum JSON közvetlenül módosítani szeretné, az Azure Portalon vagy az alkalmazásregisztrációs portálon az alkalmazásjegyzék-szerkesztőben segítségével olyan hivatkozásoknak, amelyek az alkalmazás és az adatvédelmi nyilatkozat feltételeit.

```json
    "informationalUrls": { 
        "termsOfService": "<your_terms_of_service_url>", 
        "privacy": "<your_privacy_statement_url>" 
    }
```

### <a name="msgraph-beta-rest-api"></a>Az MSGraph beta REST API használatával

Programozott módon frissíti alkalmazásait, az MSGraph beta REST API-val feltételeit és adatvédelmi nyilatkozata dokumentumok mutató hivatkozásokat tartalmazza az alkalmazások frissítéséhez használhatja.

```
PATCH https://graph.microsoft.com/beta/applications/{application id}
{ 
    "appId": "{your application id}", 
    "info": { 
        "termsOfServiceUrl": "<your_terms_of_service_url>", 
        "supportUrl": null, 
        "privacyStatementUrl": "<your_privacy_statement_url>", 
        "marketingUrl": null, 
        "logoUrl": null 
    }
}
```

> [!NOTE]
> * Ügyeljen arra, hogy nem minden hozzárendelt mezőt előre meglévő érték felülírása: `supportUrl`, `marketingUrl`, és `logoUrl`
> * Az MSGraph beta REST API-t csak akkor fog működni, ha egy Azure AD-fiókkal jelentkezik be. Személyes Microsoft-fiókok nem támogatottak.