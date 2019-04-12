---
title: Alkalmazás közzétevőjének tartománya konfigurálása |} Az Azure
description: Ismerje meg, hogyan tudja, ahol a adatokat küld a felhasználóknak, hogy egy alkalmazás közzétevőjének tartománya konfigurálása.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/05/2019
ms.author: celested
ms.reviewer: lenalepa, sureshja, zachowd
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: efbf448770bfcf797d6bf01cd3c28dc98023adff
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59502278"
---
# <a name="how-to-configure-an-applications-publisher-domain-preview"></a>Útmutató: Alkalmazás közzétevőjének tartománya (előzetes verzió) konfigurálása

Alkalmazás közzétevőjének tartománya jelenik meg a felhasználók számára a [alkalmazás jóváhagyási kérése](application-consent-experience.md) érhessék, ahol azok információkat küld a rendszer. Szabályzatként jelenik meg több-bérlős alkalmazások 2019. május 21. után regisztrált, amelyek nem rendelkeznek a közzétevői tartományt **nem ellenőrzött**. Több-bérlős alkalmazások olyan alkalmazások, amelyek támogatja a szervezeti egyetlen címtárban; kívül-fiókok például az összes Azure AD-fiókokat támogatja, vagy az Azure AD-fiókok és a személyes Microsoft-fiókok támogatják.

## <a name="new-applications"></a>Új alkalmazások

Amikor regisztrál egy új alkalmazást, az az alkalmazás közzétevőjének tartománya beállítható egy alapértelmezett értéket. Az érték attól függ, ahol az alkalmazás regisztrálva van, különösen azt, hogy az alkalmazás regisztrálva van a bérlő, és hogy a bérlő rendelkezik bérlői ellenőrzött tartományok.

Ha a bérlő ellenőrzött tartományok, az alkalmazás közzétevőjének tartománya alapértelmezés szerint az elsődleges, a bérlő ellenőrzött tartomány. Van-e nem a bérlő ellenőrzött tartományok (amely a helyzet, ha az alkalmazás a bérlő nincs regisztrálva), az alkalmazás közzétevőjének tartománya állítja be null értékre.

A következő táblázat összefoglalja a közzétevő tartományérték alapértelmezett viselkedését.  

| Bérlő ellenőrzött tartományok | Alapértelmezett érték a közzétevő tartománya |
|-------------------------|----------------------------|
| null | null |
| *.onmicrosoft.com | *.onmicrosoft.com |
| - *.onmicrosoft.com<br/>- domain1.com<br/>-Tartomány2.com (elsődleges) | domain2.com |

Ha egy több-bérlős alkalmazás közzétevőjének tartománya nincs beállítva, vagy ha egy tartományhoz, amely értékre van állítva. onmicrosoft.com, megjelenik az alkalmazás jóváhagyási kérése **nem ellenőrzött** helyett a közzétevői tartományt.

## <a name="grandfathered-applications"></a>Grandfathered alkalmazások

Ha az alkalmazás 2019. május 21. előtt regisztrálva lett az alkalmazás jóváhagyási kérése nem jelenik meg **nem ellenőrzött** Ha nem állított be a közzétevői tartományt. Azt javasoljuk, hogy a közzétevő tartomány értéket beállítani, hogy a felhasználók ezeket az információkat tekintheti meg az alkalmazás jóváhagyási kérése az.

## <a name="configure-publisher-domain-using-the-azure-portal"></a>Konfigurálja a közzétevői tartományt az Azure portal használatával

Az alkalmazás közzétevőjének tartománya beállításához kövesse az alábbi lépéseket.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).

1. Ha a fiókja egynél több Azure AD-bérlő szerepel:
   1. Az oldal jobb felső sarkában a menüből válassza ki a profilt, majd **címtár váltása**.
   1. A munkamenet módosítani kívánja az alkalmazás létrehozása az Azure AD-bérlőhöz.

1. Navigáljon a [Azure Active Directory > alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) keresése, és válassza ki a konfigurálni kívánt alkalmazást.

   Miután kiválasztotta az alkalmazás, látni fogja az alkalmazás **áttekintése** lapot.

1. Az alkalmazás **áttekintése** lapon válassza ki a **Branding** szakaszban.

1. Keresse meg a **közzétevőjének tartománya** mezőbe, majd válasszon az alábbi lehetőségek közül:

   - Válassza ki **tartomány konfigurálása** Ha már korábban nem konfigurálta a tartományhoz.
   - Válassza ki **frissítési tartomány** Ha már konfigurálva van egy tartományhoz.

Ha az alkalmazás regisztrálva van a bérlő, akkor válassza ki a két lap jelenik meg: **Válasszon ellenőrzött tartományt** és **új tartomány hitelesítése**.

Ha az alkalmazás a bérlő nincs regisztrálva, ellenőrizheti az alkalmazás egy új tartományt csak látni fogja.

### <a name="to-verify-a-new-domain-for-your-app"></a>Az alkalmazás egy új tartomány ellenőrzése

1. Hozzon létre egy fájlt `microsoft-identity-association.json` , és illessze be a következő JSON-kódrészlet.

   ```json
   {
      "associatedApplications": [
        {
           "applicationId": "{YOUR-APP-ID-HERE}"
        }
      ]
    }
   ```

1. Cserélje le a helyőrző *{YOUR-APP-ID-Itt}* az alkalmazás (ügyfél) azonosítójával, amely megfelel az alkalmazás.

1. A fájlban a következő gazdagép: `https://{YOUR-DOMAIN-HERE}.com/.well-known/microsoft-identity-association.json`. Cserélje le a helyőrző *{YOUR-Ön-GYÖKÉRTARTOMÁNYA}* ellenőrzött tartományának megfelelően.

1. Kattintson a **győződjön meg arról, és mentse a tartomány** gombra.

### <a name="to-select-a-verified-domain"></a>Jelölje be egy ellenőrzött tartomány

- Ha a bérlő ellenőrizte a tartományokat, válasszon egyet a tartománynak a **válasszon ellenőrzött tartományt** legördülő listából.

## <a name="implications-on-the-app-consent-prompt"></a>Az alkalmazás gyakorolt jóváhagyási kérés

A közzétevői tartományt konfigurálása mit látnak a felhasználók az alkalmazás-jóváhagyási kérése a hatással van. A beleegyezést kérő összetevőinek teljes megismeréséhez tekintse meg [ismertetése során lép fel az alkalmazás jóváhagyásának](application-consent-experience.md).

A következő táblázat ismerteti a 2019. május 21. előtt létrehozott alkalmazások viselkedését.

![2019. május 21. előtt létrehozott alkalmazások beleegyezést kérő üzenete](./media/howto-configure-publisher-domain/old-app-behavior-table.png)

2019. május 21. után létrehozott új alkalmazások viselkedését a közzétevői tartományt és alkalmazás típusától függ. A következő táblázat ismerteti a módosítások megtekintéséhez, a konfigurációk különböző kombinációit számíthat.

![Jóváhagyási kérés 2019. május 21. után létrehozott alkalmazások](./media/howto-configure-publisher-domain/new-app-behavior-table.png)

## <a name="implications-on-redirect-uris"></a>Átirányítási URI-k gyakorolt

Az alkalmazásokat, amelyek a felhasználók minden olyan munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókok ([több-bérlős](single-and-multi-tenant-apps.md)) van néhány korlátozások megadásakor átirányítási URI-k.

### <a name="single-root-domain-restriction"></a>Egyetlen legfelső szintű tartomány korlátozása

Ha a közzétevő tartomány több-bérlős alkalmazások értéke null, alkalmazások korlátozva, egy egyetlen legfelső szintű tartományt az átirányítási URI-k megosztása. Ha például nem engedélyezett a következő kombinaci hodnot, mert a legfelső szintű tartományt, a contoso.com, fabrikam.com nem felel meg.

```
"https://contoso.com",
"https://fabrikam.com",
```

### <a name="subdomain-restrictions"></a>Subdomain restrictions

Altartományok engedélyezettek, de a legfelső szintű tartományt explicit módon kell regisztrálni. Ha például a következő URI-k egy egyetlen legfelső szintű tartományt oszt meg, amíg együttes használata nem engedélyezett.

```
"https://app1.contoso.com",
"https://app2.contoso.com",
```

Azonban ha a fejlesztői explicit módon a legfelső szintű tartomány ad hozzá, együttes használata engedélyezett.

```
"https://contoso.com",
"https://app1.contoso.com",
"https://app2.contoso.com",
```

### <a name="exceptions"></a>Kivételek

A következő esetekben nem az egyetlen legfelső szintű tartomány korlátozás alá:

- Egybérlős alkalmazások, illetve egyetlen címtárban fiókok megcélzó alkalmazások
- Localhost, átirányítási URI-k használata
- Átirányítási URI-k egyéni sémák (nem HTTP vagy HTTPS)

## <a name="configure-publisher-domain-programmatically"></a>Közzétevő tartománya konfigurálása szoftveresen

Jelenleg nincs nem REST API vagy a PowerShell támogatott programozott módon konfigurálhatja a közzétevői tartományt.
