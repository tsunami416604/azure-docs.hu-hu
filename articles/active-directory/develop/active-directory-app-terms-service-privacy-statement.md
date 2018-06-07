---
title: Szolgáltatás és az adatvédelmi nyilatkozat feltételeit. az Azure AD alkalmazásaiban |} Microsoft Docs
description: Ismerje meg, hogyan konfigurálhat a regisztrált az Azure AD-alkalmazások szolgáltatás és az adatvédelmi nyilatkozat feltételeit.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/23/2018
ms.author: celested
ms.reviwer: lenalepa; sureshja
ms.custom: aaddev
ms.openlocfilehash: 8abd9c35c7d979e1e1a42dd7e178d802f06c4227
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655056"
---
# <a name="terms-of-service-and-privacy-statement-for-registered-azure-active-directory-apps"></a>Regisztrált Azure Active Directory-alkalmazások szolgáltatás és az adatvédelmi nyilatkozat feltételeit

Létre és kezeli az alkalmazásokat, amelyekbe beépül az Azure Active Directory (Azure AD) és Microsoft-fiókok fejlesztőknek tartalmaznia kell az alkalmazás használati feltételeinek szolgáltatás és az adatvédelmi nyilatkozatot mutató hivatkozásokat tartalmaz. A felhasználók számára a felhasználói hozzájárulás kezelőfelület segítségével illesztett szolgáltatás és az adatvédelmi nyilatkozat feltételeit. A felhasználókkal, hogy az alkalmazás megbízhatóságát is segítenek. A szolgáltatás és az adatvédelmi nyilatkozat feltételeit különösen fontos a felhasználók számára is elérhető több-bérlős alkalmazásokhoz – több könyvtárak használják, vagy bármely Microsoft-fiók számára elérhető alkalmazások.

Ön felelősséggel tartozik a feltételeket és adatvédelmi nyilatkozat dokumentumok beállítása az alkalmazáshoz, és biztosítani azt az URL-címeket a dokumentumokhoz való létrehozása. Több-bérlős alkalmazásokhoz, amelyek nem nyújt ezeket a hivatkozásokat egy riasztást, amely előfordulhat, hogy ismeretlen hozzájárul ahhoz, hogy az alkalmazás megjeleníti a felhasználói hozzájárulás funkciót alkalmazásához.

> [!NOTE]
> * Single-bérlői alkalmazások nem jelennek meg a riasztást.
> * Ha legalább két hivatkozások egyike hiányzik, az alkalmazás egy riasztás jelenik meg.

## <a name="user-consent-experience"></a>Felhasználói hozzájárulás élmény

Az alábbi példák bemutatják a felhasználói élmény hozzájárulás, ha a szolgáltatás és az adatvédelmi nyilatkozat feltételeit vannak konfigurálva, és ha ezek a hivatkozások nincs konfigurálva.

![Képernyőfelvételek rendelkező és anélküli adatvédelmi nyilatkozatot és a meghatározott szolgáltatási feltételek](./media/active-directory-integrating-applications/user-consent-exp-privacy-statement-terms-service.png)

## <a name="formatting-links-to-the-terms-of-service-and-privacy-statement-documents"></a>A szerződést a szolgáltatás és az adatvédelmi nyilatkozat dokumentumok formázási hivatkozások

Csak az alkalmazás használati feltételeinek szolgáltatás és az adatvédelmi nyilatkozat dokumentumok hivatkozásokat hozzáadni, győződjön meg arról az URL-címeket az alábbiakra.

| Iránymutatás     | Leírás                           |
|---------------|---------------------------------------|
| Formátum        | Érvényes URL-címe                             |
| Érvényes sémák | HTTP és HTTPS</br>Azt javasoljuk, hogy HTTPS |
| Maximális hossz    | 2048 karakter                       |

Példák: `https://myapp.com/terms-of-service` és `https://myapp.com/privacy-statement`

## <a name="adding-links-to-the-terms-of-service-and-privacy-statement"></a>Hivatkozások hozzáadása a szolgáltatás és az adatvédelmi nyilatkozat feltételeit.

Amikor készen áll a szolgáltatás és az adatvédelmi nyilatkozat feltételeit, adhat hozzá hivatkozásokat ezeket a dokumentumokat az alkalmazásban, az alábbi módszerek egyikének használatával:
* [Az Azure portálon keresztül](#registered-in-azure-portal)
* [A fejlesztői központot vagy alkalmazásregisztrációs portálra](#registered-in-app-reg-portal)
* [Az alkalmazás objektum JSON](#app-object-json)
* [A MSGraph beta REST API használatával](#msgraph-beta-rest-api)

### <a name="registered-in-azure-portal"></a>Ha regisztrálta az alkalmazását az Azure-portálon
Ha regisztrálta az alkalmazását az Azure portálon, kövesse az alábbi lépéseket.

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com/).
2. Keresse meg a **App regisztrációk** szakaszt, és válassza ki az alkalmazást.
3. Nyissa meg a **tulajdonságok** szakaszban az alkalmazás.
4. Töltse ki a **feltételeket az URL-címe** és **adatvédelmi nyilatkozatának URL-címe** mezőket.
5. Mentse a módosításokat.

![Alkalmazás tulajdonságainak szakaszában adatokkal szolgáltatás és az adatvédelmi nyilatkozat URL-címek](./media/active-directory-integrating-applications/azure-portal-terms-service-privacy-statement-urls.png)

### <a name="registered-in-app-reg-portal"></a>Ha az alkalmazás az alkalmazásregisztrációs portálra
Ha az alkalmazás az alkalmazásregisztrációs portálra vagy a fejlesztői központban regisztrált, kövesse az alábbi lépéseket.

1. Jelentkezzen be a [alkalmazásregisztrációs portálra](https://apps.dev.microsoft.com/).
2. Válassza ki az alkalmazást, és görgessen a **profil** szakasz.
3. Töltse ki a **feltételeket az URL-címe** és **adatvédelmi nyilatkozatának URL-címe** mezőket.
4. Mentse a módosításokat.

![Alkalmazás profil szakaszában adatokkal szolgáltatás és az adatvédelmi nyilatkozat URL-címek](./media/active-directory-integrating-applications/app-registration-portal-profile-terms-service-privacy-statement-urls.png)

### <a name="app-object-json"></a>Az alkalmazás objektum JSON
Inkább az alkalmazás objektum JSON közvetlenül módosítható, ha az Azure-portálon vagy az alkalmazásregisztrációs portálra a jegyzék szerkesztő segítségével mutató hivatkozásokat, az alkalmazás használati feltételeinek szolgáltatás és az adatvédelmi nyilatkozatot tartalmaznak.

```json
    "informationalUrls": { 
        "termsOfService": “<your_terms_of_service_url>”, 
        "privacy": “<your_privacy_statement_url>” 
    }
```

### <a name="msgraph-beta-rest-api"></a>A MSGraph beta REST API használatával
Programozott módon frissítse az alkalmazások, a MSGraph beta REST API-t is használhat a szolgáltatás és az adatvédelmi nyilatkozat dokumentumok feltételeit mutató hivatkozásokat tartalmaznak az alkalmazások frissítése.

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
> * Ügyeljen arra, hogy felülírja a meglévő értékeket rendelt mezőt: `supportUrl`, `marketingUrl`, és `logoUrl`
> * A MSGraph beta REST API-t csak akkor fog működni, ha az Azure AD-fiókkal jelentkezik. Személyes Microsoft-fiókok nem támogatottak.