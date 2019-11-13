---
title: Alkalmazás közzétevő tartományának konfigurálása
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan konfigurálhat egy alkalmazás közzétevő tartományát, hogy a felhasználók tisztában legyenek az adataik elküldésével.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/05/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, zachowd
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3558e589004e0488e5b96b0e39bd3f5ceea69658
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013060"
---
# <a name="how-to-configure-an-applications-publisher-domain"></a>Útmutató: alkalmazás közzétevő tartományának konfigurálása

Az alkalmazás közzétevői tartománya megjelenik a felhasználók számára az [alkalmazás jóváhagyására vonatkozó kérésben](application-consent-experience.md) , hogy a felhasználók tudják, hol küldik el az adataikat. A több-bérlős alkalmazások, amelyek a 2019. május 21. után vannak regisztrálva, amelyek nem rendelkeznek közzétevő tartománnyal, nem **ellenőrzöttként**jelennek meg. A több-bérlős alkalmazások olyan alkalmazások, amelyek egyetlen szervezeti címtáron kívül is támogatják a fiókokat. például támogassa az összes Azure AD-fiókot, vagy támogassa az összes Azure AD-fiókot és a személyes Microsoft-fiókokat.

## <a name="new-applications"></a>Új alkalmazások

Új alkalmazás regisztrálása esetén előfordulhat, hogy az alkalmazás közzétevő tartománya alapértelmezett értékre van állítva. Az érték attól függ, hogy az alkalmazás hol van regisztrálva, különösen azt, hogy az alkalmazás regisztrálva van-e a bérlőben, és hogy a bérlő rendelkezik-e a bérlő által ellenőrzött tartományokkal.

Bérlő által ellenőrzött tartományok esetén az alkalmazás közzétevő tartománya alapértelmezés szerint a bérlő elsődleges ellenőrzött tartománya lesz. Ha nincsenek bérlő által ellenőrzött tartományok (amelyek akkor jelennek meg, ha az alkalmazás nincs bérlőn regisztrálva), az alkalmazás közzétevő tartománya NULL értékűre lesz állítva.

A következő táblázat összefoglalja a közzétevő tartomány értékének alapértelmezett viselkedését.  

| Bérlő által ellenőrzött tartományok | Közzétevő tartomány alapértelmezett értéke |
|-------------------------|----------------------------|
| NULL | NULL |
| *.onmicrosoft.com | *.onmicrosoft.com |
| - *.onmicrosoft.com<br/>- domain1.com<br/>-domain2.com (elsődleges) | domain2.com |

Ha a több-bérlős alkalmazás közzétevő tartománya nincs beállítva, vagy ha egy olyan tartományra van beállítva, amely a. onmicrosoft.com-ben ér véget, akkor az alkalmazás hozzájárulási kérése nem **ellenőrzöttként** jelenik meg a közzétevő tartománya helyett.

## <a name="grandfathered-applications"></a>Nagyszülő alkalmazások

Ha az alkalmazás regisztrálása a 2019. május 21. előtt történt meg, az alkalmazás jóváhagyására vonatkozó kérés **nem jelenik meg, ha nem** állított be közzétevő tartományt. Azt javasoljuk, hogy állítsa be a közzétevői tartomány értékét, hogy a felhasználók láthassák ezeket az információkat az alkalmazás jóváhagyására vonatkozó kérdésben.

## <a name="configure-publisher-domain-using-the-azure-portal"></a>A közzétevő tartomány konfigurálása a Azure Portal használatával

Az alkalmazás közzétevő tartományának beállításához kövesse az alábbi lépéseket.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).

1. Ha a fiókja több Azure AD-bérlőn is megtalálható:
   1. Válassza ki a profilt a lap jobb felső sarkában található menüből, majd **váltson át a könyvtárra**.
   1. Módosítsa a munkamenetet arra az Azure AD-bérlőre, ahol létre szeretné hozni az alkalmazást.

1. Keresse meg [Azure Active Directory > Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) a konfigurálni kívánt alkalmazás megkereséséhez és kiválasztásához.

   Miután kiválasztotta az alkalmazást, látni fogja az alkalmazás **Áttekintés** lapját.

1. Az alkalmazás **Áttekintés** lapján válassza a **branding (védjegyezés** ) szakaszt.

1. Keresse meg a **közzétevő tartomány** mezőt, és válasszon egyet a következő lehetőségek közül:

   - Válassza **a tartomány konfigurálása** lehetőséget, ha már nem konfigurált tartományt.
   - Válassza a **tartomány frissítése** lehetőséget, ha már konfigurálva van egy tartomány.

Ha az alkalmazás regisztrálva van egy bérlőben, két lap közül választhat: **válasszon ki egy ellenőrzött tartományt** , és **ellenőrizze az új tartományt**.

Ha az alkalmazás nincs a bérlőben regisztrálva, akkor csak az új tartomány ellenőrzésének lehetősége jelenik meg.

### <a name="to-verify-a-new-domain-for-your-app"></a>Új tartomány ellenőrzése az alkalmazáshoz

1. Hozzon létre egy `microsoft-identity-association.json` nevű fájlt, és illessze be a következő JSON-kódrészletet.

   ```json
   {
      "associatedApplications": [
        {
           "applicationId": "{YOUR-APP-ID-HERE}"
        }
      ]
    }
   ```

1. Cserélje le a *{Your-app-ID-here}* helyőrzőt az alkalmazásnak megfelelő alkalmazás-(ügyfél-) azonosítóra.

1. A fájlt a következő helyen tárolja: `https://{YOUR-DOMAIN-HERE}.com/.well-known/microsoft-identity-association.json`. Cserélje le a *{Your-Domain-ide}* helyőrzőt az ellenőrzött tartománynak megfelelően.

1. Kattintson a **tartomány ellenőrzése és mentése** gombra.

### <a name="to-select-a-verified-domain"></a>Ellenőrzött tartomány kiválasztása

- Ha a bérlő ellenőrizte a tartományokat, válassza ki az egyik tartományt a **Select an ellenőrzött tartomány** legördülő listából.

>[!Note]
> A rendszer a várt "Content-Type" fejlécet `application/json`adja vissza. Az alább leírtak szerint előfordulhat, hogy az alábbihoz hasonló hibaüzenetet használ, ha mást szeretne használni `application/json; charset=utf-8` 
> 
>``` "Verification of publisher domain failed. Error getting JSON file from https:///.well-known/microsoft-identity-association. The server returned an unexpected content type header value. " ```
>

## <a name="implications-on-the-app-consent-prompt"></a>Az alkalmazás jóváhagyására vonatkozó kérdés következményei

A közzétevő tartomány konfigurálása hatással van arra, hogy a felhasználók mit látnak az alkalmazás jóváhagyására vonatkozó kérésben. Az egyetértési kérdés összetevőinek teljes megértéséhez tekintse meg [az alkalmazás-hozzájárulási tapasztalatok megismerése](application-consent-experience.md)című témakört.

Az alábbi táblázat a 2019. május 21. előtt létrehozott alkalmazások viselkedését ismerteti.

![Beleegyezés kérése a 2019. május 21. előtt létrehozott alkalmazásokhoz](./media/howto-configure-publisher-domain/old-app-behavior-table.png)

A 2019. május 21. után létrehozott új alkalmazások viselkedése a közzétevő tartománytól és az alkalmazás típusától függ. A következő táblázat azokat a módosításokat ismerteti, amelyeket el kell várnia a konfigurációk különböző kombinációinak megjelenítéséhez.

![Beleegyezés kérése a 2019. május 21. után létrehozott alkalmazásokhoz](./media/howto-configure-publisher-domain/new-app-behavior-table.png)

## <a name="implications-on-redirect-uris"></a>Az átirányítási URI-k következményei

Az olyan alkalmazások, amelyek munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal ([több-bérlős](single-and-multi-tenant-apps.md)) jelentkeznek be a felhasználókba, az átirányítási URI-k megadására néhány korlátozás vonatkozik.

### <a name="single-root-domain-restriction"></a>Egyetlen legfelső szintű tartományi korlátozás

Ha a több-bérlős alkalmazások közzétevői tartományának értéke null, az alkalmazások csak egyetlen gyökértartomány megosztására korlátozódnak az átirányítási URI-k számára. Például a következő értékek nem engedélyezettek, mert a gyökértartomány, a contoso.com, nem felel meg a fabrikam.com.

```
"https://contoso.com",
"https://fabrikam.com",
```

### <a name="subdomain-restrictions"></a>Altartományokra vonatkozó korlátozások

Az altartományok engedélyezettek, de explicit módon regisztrálnia kell a gyökértartomány tartományát. Ha például a következő URI-k egyetlen gyökértartomány megosztását használják, a kombináció nem engedélyezett.

```
"https://app1.contoso.com",
"https://app2.contoso.com",
```

Ha azonban a fejlesztő explicit módon hozzáadja a gyökértartomány tartományát, akkor a kombináció engedélyezett.

```
"https://contoso.com",
"https://app1.contoso.com",
"https://app2.contoso.com",
```

### <a name="exceptions"></a>Kivételek

A következő esetek nem vonatkoznak az egyetlen legfelső szintű tartományi korlátozásra:

- Egybérlős alkalmazások vagy olyan alkalmazások, amelyek egyetlen címtárban célozzák meg a fiókokat.
- A localhost használata átirányítási URI-ként
- URI-k átirányítása egyéni sémákkal (nem HTTP vagy HTTPS)

## <a name="configure-publisher-domain-programmatically"></a>Közzétevői tartomány programozott módon történő konfigurálása

Jelenleg nincs REST API vagy PowerShell-támogatás a közzétevői tartomány programozott módon történő konfigurálásához.
