---
title: A Publisher-ellenőrzés hibáinak megoldása – Microsoft Identity platform | Azure
description: Ismerteti, hogyan lehet elhárítani a Microsoft Identity platform közzétevő-ellenőrzésének (előzetes verzió) hibaelhárítását Microsoft Graph API-k meghívásával.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 05/08/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: 3069e3caf81d9bb2f809b21c88383c419e3b90b3
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87282976"
---
# <a name="troubleshoot-publisher-verification-preview"></a>Közzétevő ellenőrzésének hibaelhárítása (előzetes verzió)
Ha nem tudja befejezni a folyamatot, vagy nem várt viselkedést tapasztal a [közzétevő ellenőrzésekor (előzetes verzió)](publisher-verification-overview.md), akkor a következő lépésekkel kell kezdenie, ha hibákat kap, vagy nem várt viselkedés jelenik meg: 

1. Tekintse át a [követelményeket](publisher-verification-overview.md#requirements) , és győződjön meg arról, hogy az összes teljesült.

1. Tekintse át az [alkalmazás közzétevőként való megjelölésének](mark-app-as-publisher-verified.md) utasításait, és győződjön meg arról, hogy az összes lépés sikeresen elvégezve.

1. Tekintse át a [gyakori problémák](#common-issues)listáját.

1. A kérelem ismételt előállítása a [Graph Explorerrel](#making-microsoft-graph-api-calls) további információk összegyűjtéséhez és a felhasználói felületen felmerülő problémák kizárásához.

## <a name="common-issues"></a>Gyakori problémák
Az alábbiakban néhány gyakori probléma merülhet fel, amelyek a folyamat során fordulhatnak elő. 

- **Nem tudom a Microsoft Partner Network AZONOSÍTÓját (MPN-AZONOSÍTÓját), vagy nem vagyok a fiók elsődleges kapcsolattartója** 
    1. Navigáljon az [MPN-regisztráció oldalra](https://partner.microsoft.com/dashboard/account/v3/enrollment/joinnow/basicpartnernetwork/new)
    1. Bejelentkezés felhasználói fiókkal a szervezeti elsődleges Azure AD-bérlőben 
    1. Ha egy MPN-fiók már létezik, a rendszer felismeri, és hozzáadja a fiókhoz. 
    1. Lépjen a [partner profil oldalra](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) , ahol megjelenik az MPN-azonosító és az elsődleges fiók kapcsolata

- **Nem tudom, kik az Azure AD globális rendszergazdája (más néven vállalati rendszergazda vagy bérlői rendszergazda), hogyan találom meg őket? Mi a helyzet az alkalmazás rendszergazdájával vagy egy másik rendszergazdai szerepkörrel?**
    1. Jelentkezzen be az [Azure ad-portálra](https://aad.portal.azure.com) a szervezete elsődleges bérlője felhasználói fiókjának használatával
    1. Navigáljon a [szerepkör-kezelés](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) elemre
    1. Kattintson a globális rendszergazda lehetőségre, vagy a kívánt rendszergazdai szerepkörre.
    1. Megjelenik a szerepkörhöz hozzárendelt felhasználók listája

- **Nem tudom, kik az MPN-fiókomhoz tartozó rendszergazda (k)** Nyissa meg az [MPN felhasználói kezelés lapot](https://partner.microsoft.com/pcv/users) , és a felhasználók listájának szűrésével tekintse meg, hogy a felhasználók milyen rendszergazdai szerepkörökkel rendelkeznek.

- **Hibaüzenet jelenik meg, hogy az MPN-azonosító érvénytelen, vagy nem fér hozzá.**
    1. Lépjen a [partner profiljához](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) , és ellenőrizze, hogy: 
        - Az MPN-azonosító helyes. 
        - Nincsenek hibák vagy "függőben lévő műveletek", valamint a jogi üzleti profilban és a partneri adatokban található ellenőrzési állapot "engedélyezett" vagy "sikeres".
    1. Nyissa meg az [MPN bérlői felügyelet oldalt](https://partner.microsoft.com/dashboard/account/v3/tenantmanagement) , és ellenőrizze, hogy az alkalmazás regisztrálva van-e a bérlőben, és hogy felhasználói fiókkal van-e aláírva a társított bérlők listáján.
    1. Nyissa meg az [MPN-felhasználó felügyelete lapot](https://partner.microsoft.com/pcv/users) , és erősítse meg, hogy a bejelentkezéshez használt felhasználó globális rendszergazda, MPN-rendszergazda vagy rendszergazdai fiók.

- **Az Azure AD-portálra való bejelentkezéskor nem jelenik meg a regisztrált alkalmazások egyike sem. miért?** 
    Előfordulhat, hogy az alkalmazás regisztrációja egy másik felhasználói fiókkal vagy egy másik Bérlővel lett létrehozva. Győződjön meg arról, hogy a megfelelő fiókkal van bejelentkezve abban a bérlőben, amelyben az alkalmazás regisztrációja létrejött.

- **Hogyan tudni, ki az Azure AD-ban az alkalmazás regisztrációjának tulajdonosa?** 
    Amikor bejelentkezett egy olyan bérlőbe, ahol az alkalmazás regisztrálva van, keresse meg az alkalmazás regisztrációi paneljét, kattintson az alkalmazásra, majd kattintson a tulajdonosok elemre.

## <a name="making-microsoft-graph-api-calls"></a>Microsoft Graph API-hívások készítése 

Ha probléma merül fel, de nem tudja megérteni, hogy mit lát a felhasználói felületen, érdemes lehet további hibaelhárítást végezni, ha Microsoft Graph hívásokat hajt végre az alkalmazás regisztrációs portálján végrehajtható műveletek végrehajtásához. Az előnézeti fázisban ezek az API-k csak Microsoft Graph/Beta-végpontján lesznek elérhetők.  

Ezeknek a kéréseknek a legegyszerűbb módja a [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)használata. Érdemes lehet más lehetőségeket is megfontolni, például a [Poster](https://www.postman.com/)használatát, vagy a PowerShell használatával [webes kérést meghívni](/powershell/module/microsoft.powershell.utility/invoke-webrequest?view=powershell-7).  

A Microsoft Graph az alkalmazás ellenőrzött közzétevőjét is beállíthatja és törölheti, és az eredményt az alábbi műveletek egyikének végrehajtása után ellenőrizheti. Az eredmény az alkalmazás regisztrációjának megfelelő [Application](/graph/api/resources/application?view=graph-rest-beta) objektumon és az alkalmazásból létrehozott [egyszerű szolgáltatásokon](/graph/api/resources/serviceprincipal?view=graph-rest-beta) is látható. Az objektumok közötti kapcsolattal kapcsolatos további információkért lásd: [alkalmazás-és szolgáltatásnév-objektumok Azure Active Directoryban](app-objects-and-service-principals.md).  

Példák néhány hasznos kérelemre:  

### <a name="set-verified-publisher"></a>Ellenőrzött közzétevő beállítása 

Kérés

```
POST /applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec/setVerifiedPublisher 

{ 

    "verifiedPublisherId": "12345678" 

} 
```
 
Reagálás 
```
204 No Content 
```
> [!NOTE]
> a *verifiedPublisherID* az MPN-azonosító. 

### <a name="unset-verified-publisher"></a>Ellenőrzött közzétevő nem lett beállítva 

Kérés:  
```
POST /applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec/unsetVerifiedPublisher 
```
 
Reagálás 
```
204 No Content 
```
### <a name="get-verified-publisher-info-from-application"></a>Ellenőrzött közzétevői adatok beolvasása az alkalmazásból 
 
```
GET https://graph.microsoft.com/beta/applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec 

HTTP/1.1 200 OK 

{ 
    "id": "0cd04273-0d11-4e62-9eb3-5c3971a7cbec", 

    ... 

    "verifiedPublisher" : { 
        "displayName": "myexamplePublisher", 
        "verifiedPublisherId": "12345678", 
        "addedDateTime": "2019-12-10T00:00:00" 
    } 
} 
```

### <a name="get-verified-publisher-info-from-service-principal"></a>Ellenőrzött közzétevői adatok beolvasása az egyszerű Szolgáltatásnévtől 
```
GET https://graph.microsoft.com/beta/servicePrincipals/010422a7-4d77-4f40-9335-b81ef5c23dd4 

HTTP/1.1 200 OK 

{ 
    "id": "010422a7-4d77-4f40-9335-b81ef5c22dd4", 

    ... 

    "verifiedPublisher" : { 
        "displayName": "myexamplePublisher", 
        "verifiedPublisherId": "12345678", 
        "addedDateTime": "2019-12-10T00:00:00" 
    } 
} 
```

## <a name="error-reference"></a>Hiba leírása 

Az alábbi lista felsorolja a lehetséges hibakódokat, amelyeket a Microsoft Graph vagy az alkalmazás regisztrációs portálon végzett hibaelhárítás során kaphat.

### <a name="mpnaccountnotfoundornoaccess"></a>MPNAccountNotFoundOrNoAccess     

A megadott MPN-azonosító ( <MPNID> ) nem létezik, vagy Önnek nincs hozzáférése. Adjon meg egy érvényes MPN-azonosítót, és próbálkozzon újra. 

### <a name="mpnglobalaccountnotfound"></a>MPNGlobalAccountNotFound     

A megadott MPN-azonosító ( <MPNID> ) érvénytelen. Adjon meg egy érvényes MPN-azonosítót, és próbálkozzon újra. 

### <a name="mpnaccountinvalid"></a>MPNAccountInvalid    

A megadott MPN-azonosító ( <MPNID> ) érvénytelen. Adjon meg egy érvényes MPN-azonosítót, és próbálkozzon újra. 

### <a name="mpnaccountnotvetted"></a>MPNAccountNotVetted  

A megadott MPN-azonosító ( <MPNID> ) nem fejeződött be az átvett folyamaton. Fejezze be ezt a folyamatot a partner Centerben, és próbálkozzon újra. 

### <a name="nopublisheridonassociatedmpnaccount"></a>NoPublisherIdOnAssociatedMPNAccount  

A megadott MPN-azonosító ( <MPNID> ) érvénytelen. Adjon meg egy érvényes MPN-azonosítót, és próbálkozzon újra. 

### <a name="mpniddoesnotmatchassociatedmpnaccount"></a>MPNIdDoesNotMatchAssociatedMPNAccount    

A megadott MPN-azonosító ( <MPNID> ) érvénytelen. Adjon meg egy érvényes MPN-azonosítót, és próbálkozzon újra. 

### <a name="applicationnotfound"></a>ApplicationNotFound  

A célalkalmazás ( <AppId> ) nem található. Érvényes alkalmazás-azonosítót adjon meg, majd próbálkozzon újra. 

### <a name="b2ctenantnotallowed"></a>B2CTenantNotAllowed  

Ez a képesség Azure AD B2C bérlő esetében nem támogatott. 

### <a name="emailverifiedtenantnotallowed"></a>EmailVerifiedTenantNotAllowed    

Ez a funkció nem támogatott e-mailben ellenőrzött bérlőn. 

### <a name="nopublisherdomainonapplication"></a>NoPublisherDomainOnApplication   

A célalkalmazás () alkalmazásnak rendelkeznie <AppId> kell közzétevő tartománnyal. Állítsa be a közzétevő tartományát, és próbálkozzon újra. 

### <a name="publisherdomainisnotdnsverified"></a>PublisherDomainIsNotDNSVerified  

A célalkalmazás közzétevő tartománya ( <publisherDomain> ) nem ellenőrzött tartomány ebben a bérlőben. Ellenőrizze a bérlői tartományt a DNS-ellenőrzés használatával, majd próbálkozzon újra. 

### <a name="publisherdomainmismatch"></a>PublisherDomainMismatch  

A célalkalmazás közzétevő tartománya ( <publisherDomain> ) nem egyezik az e-mail-ellenőrzés végrehajtásához használt tartománnyal a partner Centerben ( <pcDomain> ). Győződjön meg arról, hogy ezek a tartományok egyeznek, és próbálkozzon újra. 

### <a name="notauthorizedtoverifypublisher"></a>NotAuthorizedToVerifyPublisher   

Nem jogosult az ellenőrzött közzétevő tulajdonság beállítására az alkalmazásban ( <AppId> ) 

### <a name="mpnidwasnotprovided"></a>MPNIdWasNotProvided  

Az MPN-azonosító nem lett megadva a kérelem törzsében, vagy a kérelem tartalomtípusa nem "Application/JSON". 

### <a name="msanotsupported"></a>MSANotSupported  

Ez a funkció Microsoft fogyasztói fiókok esetén nem támogatott. Csak az Azure AD-beli Azure AD-felhasználó által regisztrált alkalmazások támogatottak. 

## <a name="next-steps"></a>Következő lépések

Ha áttekintette az összes korábbi információt, és továbbra is a Microsoft Graph hibaüzenetet kap, gyűjtsön annyit az alábbi információk közül, amennyire csak lehetséges, és [forduljon a Microsoft ügyfélszolgálatához](developer-support-help-options.md#open-a-support-request).

- Timestamp 
- CorrelationId 
- A bejelentkezett felhasználó ObjectID vagy UserPrincipalName 
- Célalkalmazás ObjectId
- Célalkalmazás AppId
- TenantId, ahol az alkalmazás regisztrálva van
- MPN-azonosító
- REST-kérelem történt 
- Hibakód és visszaadott üzenet 
