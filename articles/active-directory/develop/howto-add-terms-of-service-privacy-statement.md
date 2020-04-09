---
title: Szolgáltatási feltételek és adatvédelmi nyilatkozat az alkalmazásokra | Azure
description: Ismerje meg, hogyan konfigurálhatja az Azure AD használatára regisztrált alkalmazások szolgáltatási feltételeit és adatvédelmi nyilatkozatát.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja
ms.custom: aaddev
ms.openlocfilehash: 40e7a05505bc501c1c622e627a6d97cc57db1cfa
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80884239"
---
# <a name="how-to-configure-terms-of-service-and-privacy-statement-for-an-app"></a>Útmutató: A szolgáltatási feltételek és az adatvédelmi nyilatkozat konfigurálása egy alkalmazáshoz

Az Azure Active Directoryval (Azure AD) és AD-fiókkal integrálható alkalmazásokat fejlesztő fejlesztőknek tartalmazniuk kell az alkalmazás szolgáltatási feltételeire és adatvédelmi nyilatkozatára mutató hivatkozásokat. A szolgáltatási feltételek és az adatvédelmi nyilatkozat a felhasználói hozzájárulási élményen keresztül jelennek meg a felhasználók számára. Segítenek a felhasználóknak megtudni, hogy megbízhatnak az alkalmazásban. A szolgáltatási feltételek és az adatvédelmi nyilatkozat különösen kritikus a felhasználó felé néző több-bérlős alkalmazások - alkalmazások, amelyek több könyvtár által használt, vagy bármely Microsoft-fiók számára elérhető.

Ön felelős az alkalmazás szolgáltatási feltételeinek és adatvédelmi nyilatkozatának dokumentumainak létrehozásáért, valamint a dokumentumok URL-címeinek biztosításáért. A több-bérlős alkalmazások, amelyek nem biztosítják ezeket a hivatkozásokat, az alkalmazás felhasználói hozzájárulási élménye riasztást jelenít meg, amely elriaszthatja a felhasználókat attól, hogy hozzájáruljanak az alkalmazáshoz.

> [!NOTE]
> * Az egybérlős alkalmazások nem jelenítenek meg riasztást.
> * Ha a két hivatkozás egyike vagy mindkettő hiányzik, az alkalmazás figyelmeztetést jelenít meg.

## <a name="user-consent-experience"></a>Felhasználói hozzájárulási élmény

A következő példák a felhasználói hozzájárulási élményt mutatják be, amikor a szolgáltatási feltételek és az adatvédelmi nyilatkozat konfigurálva van, és ha ezek a hivatkozások nincsenek konfigurálva.

![Képernyőképek adatvédelmi nyilatkozattal és anélkül, valamint a nyújtott szolgáltatási feltételek](./media/howto-add-terms-of-service-privacy-statement/user-consent-exp-privacy-statement-terms-service.png)

## <a name="formatting-links-to-the-terms-of-service-and-privacy-statement-documents"></a>A szolgáltatási feltételekre és az adatvédelmi nyilatkozatdokumentumaira mutató hivatkozások formázása

Mielőtt hivatkozásokat ad hozzá az alkalmazás szolgáltatási feltételeihez és az adatvédelmi nyilatkozat dokumentumaihoz, győződjön meg arról, hogy az URL-ek betartják ezeket az irányelveket.

| Irányelv     | Leírás                           |
|---------------|---------------------------------------|
| Formátum        | Érvényes URL-cím                             |
| Érvényes sémák | HTTP és HTTPS<br/>Https-t ajánlunk |
| Maximális hossz    | 2048 karakter                       |

Példák: `https://myapp.com/terms-of-service` és`https://myapp.com/privacy-statement`

## <a name="adding-links-to-the-terms-of-service-and-privacy-statement"></a>Hivatkozások hozzáadása a szolgáltatási feltételekhez és az adatvédelmi nyilatkozathoz

Ha a szolgáltatási feltételek és az adatvédelmi nyilatkozat készen áll, az alábbi módszerek egyikével adhat hozzá hivatkozásokat ezekhez a dokumentumokhoz az alkalmazásban:

* [Az Azure portalon keresztül](#azure-portal)
* [A JSON alkalmazásobjektum használata](#app-object-json)
* [A Microsoft Graph API használata](#msgraph-rest-api)

### <a name="using-the-azure-portal"></a><a name="azure-portal"></a>Az Azure Portal használata
Kövesse az alábbi lépéseket az Azure Portalon.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Nyissa meg az **Alkalmazásregisztrációk szakaszt,** és válassza ki az alkalmazást.
3. Nyissa meg a **Márkajelzés** ablaktáblát.
4. Töltse ki a **Szolgáltatási feltételek URL-címét** és **az adatvédelmi nyilatkozat URL-mezőit.**
5. Mentse a módosításokat.

    ![Az alkalmazás tulajdonságai szolgáltatási feltételeket és adatvédelmi nyilatkozat URL-címeket tartalmaznak](./media/howto-add-terms-of-service-privacy-statement/azure-portal-terms-service-privacy-statement-urls.png)

### <a name="using-the-app-object-json"></a><a name="app-object-json"></a>A JSON alkalmazásobjektum használata

Ha közvetlenül szeretné módosítani a JSON alkalmazásobjektumot, használhatja az Azure Portalon vagy az Alkalmazásregisztrációs portálon a jegyzékszerkesztőt az alkalmazás szolgáltatási feltételeire és adatvédelmi nyilatkozatára mutató hivatkozások felvételéhez.

```json
    "informationalUrls": { 
        "termsOfService": "<your_terms_of_service_url>", 
        "privacy": "<your_privacy_statement_url>" 
    }
```

### <a name="using-the-microsoft-graph-api"></a><a name="msgraph-rest-api"></a>A Microsoft Graph API használata

Az összes alkalmazás programozott frissítéséhez a Microsoft Graph API segítségével frissítheti az összes alkalmazást, hogy az tartalmazza a szolgáltatási feltételekre és az adatvédelmi nyilatkozatra mutató dokumentumokra mutató hivatkozásokat.

```
PATCH https://graph.microsoft.com/v1.0/applications/{application id}
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
> * Ügyeljen arra, hogy ne írja felül a következő mezők höz `supportUrl`rendelt `marketingUrl`már meglévő értékeket: , és`logoUrl`
> * A Microsoft Graph API csak akkor működik, ha egy Azure AD-fiókkal jelentkezik be. A személyes Microsoft-fiókok nem támogatottak.
