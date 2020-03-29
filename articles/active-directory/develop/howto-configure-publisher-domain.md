---
title: Alkalmazás közzétevői tartományának konfigurálása | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogy miként konfigurálhatja az alkalmazás közzétevői tartományát úgy, hogy a felhasználók tudják, hová küldik az adataikat.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/05/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, zachowd
ms.custom: aaddev
ms.openlocfilehash: 68040c8ee22454c300296493b6c840eabbca98aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76697132"
---
# <a name="how-to-configure-an-applications-publisher-domain"></a>Útmutató: Alkalmazás közzétevői tartományának konfigurálása

Az alkalmazás közzétevői tartománya az [alkalmazás hozzájárulási üzenetére vonatkozókéréssel](application-consent-experience.md) tudatja a felhasználókkal, hogy az adatküldés hová kerül. 2019. május 21-e után regisztrált több-bérlős alkalmazások, amelyek nem rendelkeznek közzétevői tartománnyal, **ellenőrizetlenként**jelennek meg. A több-bérlős alkalmazások olyan alkalmazások, amelyek egyetlen szervezeti címtáron kívüli fiókokat támogatnak; például az összes Azure AD-fiók támogatása, vagy az összes Azure AD-fiók és személyes Microsoft-fiókok támogatása.

## <a name="new-applications"></a>Új alkalmazások

Új alkalmazás regisztrálásakor előfordulhat, hogy az alkalmazás közzétevői tartománya alapértelmezett értékre van állítva. Az érték attól függ, hogy az alkalmazás hol van regisztrálva, különösen attól, hogy az alkalmazás regisztrálva van-e egy bérlőben, és hogy a bérlő rendelkezik-e bérlő ellenőrzött tartományokkal.

Ha vannak bérlő által ellenőrzött tartományok, az alkalmazás közzétevői tartománya alapértelmezés szerint a bérlő elsődleges ellenőrzött tartománya lesz. Ha nincsenek bérlői ellenőrzött tartományok (ez a helyzet, ha az alkalmazás nincs regisztrálva a bérlőben), az alkalmazás közzétevői tartománya null értékre lesz állítva.

Az alábbi táblázat a közzétevő tartományértékének alapértelmezett viselkedését foglalja össze.  

| Bérlő által ellenőrzött tartományok | A közzétevői tartomány alapértelmezett értéke |
|-------------------------|----------------------------|
| null | null |
| *.onmicrosoft.com | *.onmicrosoft.com |
| - *.onmicrosoft.com<br/>- domain1.com<br/>- domain2.com (elsődleges) | domain2.com |

Ha egy több-bérlős alkalmazás közzétevői tartománya nincs beállítva, vagy ha egy .onmicrosoft.com végződő tartományra van állítva, az alkalmazás hozzájárulási parancsa **nem ellenőrzött** lesz a közzétevői tartomány helyett.

## <a name="grandfathered-applications"></a>Nagyszülői alkalmazások

Ha az alkalmazás regisztrálva volt 2019. **unverified** Javasoljuk, hogy állítsa be a közzétevőtartomány értékét, hogy a felhasználók láthassák ezeket az információkat az alkalmazás hozzájárulási parancsában.

## <a name="configure-publisher-domain-using-the-azure-portal"></a>Közzétevői tartomány konfigurálása az Azure Portal használatával

Az alkalmazás megjelenítői tartományának beállításához kövesse az alábbi lépéseket.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.

1. Ha a fiók több Azure AD-bérlőben is jelen van:
   1. Válassza ki a profilját a lap jobb felső sarkában lévő menüből, majd **a Könyvtár váltása**lehetőséget.
   1. Módosítsa a munkamenetet az Azure AD-bérlőre, ahol létre szeretné hozni az alkalmazást.

1. Keresse meg az [Azure Active Directory > alkalmazásregisztrációkat,](https://go.microsoft.com/fwlink/?linkid=2083908) és válassza ki a konfigurálni kívánt alkalmazást.

   Miután kiválasztotta az alkalmazást, megjelenik az alkalmazás **áttekintése** lap.

1. Az alkalmazás **áttekintése** lapon válassza a **Márkajelzés** szakaszt.

1. Keresse meg a **Publisher tartomány** mezőt, és válasszon az alábbi lehetőségek közül:

   - Válassza **a Tartomány konfigurálása** lehetőséget, ha még nem konfigurált tartományt.
   - Válassza **a Tartomány frissítése lehetőséget,** ha már konfigurált egy tartományt.

Ha az alkalmazás regisztrálva van egy bérlőben, két lap közül választhat: **Válasszon egy ellenőrzött tartományt** és **erősítsen meg egy új tartományt**.

Ha az alkalmazás nincs regisztrálva egy bérlőben, csak akkor jelenik meg a lehetőség, hogy ellenőrizze az új tartományt az alkalmazáshoz.

### <a name="to-verify-a-new-domain-for-your-app"></a>Új tartomány ellenőrzése az alkalmazáshoz

1. Hozzon létre `microsoft-identity-association.json` egy nevű fájlt, és illessze be a következő JSON-kódrészletet.

   ```json
   {
      "associatedApplications": [
        {
           "applicationId": "{YOUR-APP-ID-HERE}"
        }
      ]
    }
   ```

1. Cserélje le *a(z) {YOUR-APP-ID-HERE}* helyőrzőt az alkalmazásnak megfelelő alkalmazás (ügyfél) azonosítóra.

1. A fájl állomása a következő helyen: `https://{YOUR-DOMAIN-HERE}.com/.well-known/microsoft-identity-association.json`. Cserélje le *a(z) {YOUR-DOMAIN-HERE}* helyőrzőt az ellenőrzött tartománynak megfelelően.

1. Kattintson a **Tartomány ellenőrzése és mentése** gombra.

### <a name="to-select-a-verified-domain"></a>Ellenőrzött tartomány kijelölése

- Ha a bérlő ellenőrizte a tartományokat, válassza ki az egyik tartományt a **Válasszon ellenőrzött tartomány** legördülő menüből.

>[!Note]
> A várt "Content-Type" fejléc, `application/json`amelyet vissza kell adni, a . Lehet, hogy kap egy hiba, mint az alábbiakban említettük, ha használ mást, mint`application/json; charset=utf-8` 
> 
>``` "Verification of publisher domain failed. Error getting JSON file from https:///.well-known/microsoft-identity-association. The server returned an unexpected content type header value. " ```
>

## <a name="implications-on-the-app-consent-prompt"></a>Az alkalmazás hozzájárulási gyorsátokra gyakorolt hatások

A közzétevői tartomány konfigurálása hatással van arra, hogy a felhasználók mit látnak az alkalmazás jóváhagyási parancsában. A jóváhagyási gyorskérés összetevőinek teljes megértéséhez [olvassa el Az alkalmazás hozzájárulási élményének ismertetése](application-consent-experience.md)című témakört.

Az alábbi táblázat a 2019.

![2019. május 21.-e előtt létrehozott alkalmazások hozzájárulási kérése](./media/howto-configure-publisher-domain/old-app-behavior-table.png)

2019. május 21-e után létrehozott új alkalmazások viselkedése a közzétevő tartományától és az alkalmazás típusától függ. Az alábbi táblázat azokat a változásokat ismerteti, amelyekvárhatóan a konfigurációk különböző kombinációival várhatóak.

![2019. május 21-e után létrehozott alkalmazások hozzájárulási kérése](./media/howto-configure-publisher-domain/new-app-behavior-table.png)

## <a name="implications-on-redirect-uris"></a>Az átirányítási URI-kra gyakorolt hatások

A munkahelyi vagy iskolai fiókkal vagy személyes[Microsoft-fiókkal (több-bérlős)](single-and-multi-tenant-apps.md)felhasználókkal bejelentkező alkalmazásokra néhány korlátozás vonatkozik az átirányítási URI-k megadásakor.

### <a name="single-root-domain-restriction"></a>Egyetlen gyökértartomány-korlátozás

Ha a több-bérlős alkalmazások közzétevői tartományértéke null értékű, az alkalmazások csak egyetlen gyökértartományt oszthatnak meg az átirányítási URI-khoz. Például a következő értékkombináció nem engedélyezett, mert a gyökértartomány, contoso.com, nem felel meg fabrikam.com.

```
"https://contoso.com",
"https://fabrikam.com",
```

### <a name="subdomain-restrictions"></a>Altartományra vonatkozó korlátozások

Az altartományok engedélyezettek, de explicit módon regisztrálnia kell a gyökértartományt. Például míg a következő URI-k egyetlen gyökértartománnyal rendelkeznek, a kombináció nem engedélyezett.

```
"https://app1.contoso.com",
"https://app2.contoso.com",
```

Ha azonban a fejlesztő kifejezetten hozzáadja a gyökértartományt, a kombináció engedélyezett.

```
"https://contoso.com",
"https://app1.contoso.com",
"https://app2.contoso.com",
```

### <a name="exceptions"></a>Kivételek

A következő esetekre nem vonatkozik az egyetlen legfelső szintű tartománykorlátozás:

- Egybérlős alkalmazások vagy egyetlen címtárban lévő fiókokat célzó alkalmazások
- Localhost használata átirányítási URI-ként
- Az URI-k átirányítása egyéni sémákkal (nem HTTP vagy HTTPS)

## <a name="configure-publisher-domain-programmatically"></a>Közzétevőtartomány konfigurálása programozott módon

Jelenleg nincs REST API vagy PowerShell-támogatás a közzétevői tartomány programozott konfigurálásához.
