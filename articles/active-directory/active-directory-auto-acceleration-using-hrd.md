---
title: "Automatikus-gyorsítás bejelentkezés konfigurálása kezdőlap tartomány Alkalmazásdetektálási szabályzat használó alkalmazások |} Microsoft Docs"
description: "Ismerteti az Azure AD-bérlő fogalmát, valamint az Azure kezelését az Azure Active Directory felületén keresztül"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: it-pro
ms.date: 11/09/2017
ms.author: billmath
ms.openlocfilehash: b1f0e5da09ef1d6acd234b72878cc71d66bb551e
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2017
---
# <a name="configure-sign-in-auto-acceleration-for-an-application-using-home-realm-discovery-hrd-policy"></a>Bejelentkezési automatikus-gyorsítás hitelesítőtartomány felderítése (HRD) házirendet használó alkalmazások konfigurálása

## <a name="introduction-to-home-realm-discovery-and-auto-acceleration"></a>Bevezetés a hitelesítőtartomány feltárását és automatikus-gyorsulás
A következő dokumentum bemutatja azokat a hitelesítőtartomány feltárását és automatikus-gyorsítás.

### <a name="home-realm-discovery"></a>Hitelesítőtartomány feltárásához
Otthoni feltárásának a folyamat, amely lehetővé teszi, hogy Azure Active Directoryban nem sikerült meghatározni, bejelentkezési idő, amikor egy felhasználó kell hitelesíteni.  Az Azure AD-bérlő történő bejelentkezéskor hozzáférést egy erőforrást, illetve az Azure AD közös bejelentkezési oldal, a felhasználó beír egy felhasználónév (UPN).  Az Azure AD használatával, amely felderítése, amikor a felhasználó jelentkezik be kell.   A felhasználó hitelesítését a következő érdekében végrehajtandó lehet szükség:

- A (lehetnek ugyanannak a bérlőnek a felhasználó megpróbál elérni,) a felhasználó otthoni bérlő. 
- Microsoft-fiókkal.   A felhasználó a Vendég az erőforrás-bérlőben
- Egy másik identitásszolgáltató az Azure AD-bérlő összevont.  Például az AD FS helyi identitásszolgáltató példányhoz.

### <a name="auto-acceleration"></a>Automatikus-gyorsulás 
Egyes szervezetek konfigurálása az Azure Active Directory-bérlő egy másik IdP, például az AD FS, a felhasználók hitelesítéséhez összevonni kívánt.  Ezekben az esetekben, amikor bejelentkezik egy alkalmazás a felhasználó először bemutatják az Azure AD bejelentkezési lapok, és az egyszerű Felhasználónevük majd tegyenek a kiállító terjesztési hely bejelentkezési oldal beírt követően.  Olyan esetekben, ahol érdemes rendszergazdák előfordulhat, hogy szeretné, hogy a felhasználók felé irányuló rögtön a bejelentkezési oldal bizonyos alkalmazások történő bejelentkezéskor kihagyása a kezdeti Azure Active Directory-lapon. A "bejelentkezés automatikus-gyorsítás" nevezzük.

Azokban az esetekben, ahol a bérlő össze van-e vonva a bejelentkezéshez egy másik IdP automatikus-gyorsítás engedélyezése lehetővé teszi felhasználói bejelentkezési több zökkenőmentes azokban az esetekben, ahol tudnia, hogy mindenki jelentkezik be, hogy a kiállító terjesztési hely által hitelesíthető.  Konfigurálhatja az automatikus-gyorsítás egyes alkalmazásokhoz.

>[!NOTE]
>Ha az auto-gyorsítás alkalmazások konfigurálása, vendégfelhasználók nem jelentkezzen be. Egyszerű hitelesítés egy összevont IdP véve az a felhasználó nem egy egyirányú utcában, mert az nincs mód kattintva visszatérhet az Azure Active Directory bejelentkezési oldal.  Vendég felhasználók számára, akik esetleg irányíthatja más bérlők vagy egy külső IdP, például a Microsoft-fiók hitelesítését, nem fog tudni bejelentkezni az adott alkalmazáshoz, a program kihagyja a Hitelesítőtartomány lépés.  

Egy összevont IdP való automatikus-gyorsulás vezérlésére két módja van.  Vagy:   

- Tartomány mutató használata hitelesítési kérelmek vonatkozó kérelmet 
- Ahhoz, hogy automatikus-gyorsítás HomeRealmDiscovery házirend konfigurálása

## <a name="domain-hints"></a>Tartomány tippek 
Tartomány mutatók irányelvek a hitelesítési kérelmet egy alkalmazás szerepel.  Annak érdekében, a felhasználó az összevont IdP bejelentkezési oldalára is használhatók, vagy azok által használható egy több-bérlős alkalmazás annak érdekében, a felhasználó közvetlenül a vállalati arculattal ellátott az Azure AD bejelentkezési oldalára a bérlő.  Például egy alkalmazás largeapp.com lehetővé teheti az ügyfelek egy egyéni URL-cím contoso.largeapp.com az alkalmazás eléréséhez, és előfordulhat, hogy a Contoso.com tartomány javaslat szerepeljenek a hitelesítési kérelmet. Tartomány mutató szintaxis a protokolltól függ, és általában az alkalmazás vannak konfigurálva.

**WS-Federation**: whr=contoso.com a lekérdezési karakterláncban

**SAML**: vagy egy SAML-alapú hitelesítési kérelmet egy tartományi mutatót, vagy egy lekérdezési karakterlánc whr=contoso.com tartalmazó

**Nyissa meg az ID Connect**: egy lekérdezési karakterlánc domain_hint=contoso.com 

Ha tartományi mutató szerepel a hitelesítési kérelmet az alkalmazásból, és a bérlő össze van vonva az adott tartománnyal, az Azure AD megkísérli bejelentkezési átirányítása a kiállító terjesztési hely konfigurálva ehhez a tartományhoz.  Ha a tartomány mutató nem ellenőrzött összevont tartományt, figyelmen kívül, és normál feltárásának elindítását.

Ez [blogbejegyzés](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/11/using-azure-ad-to-land-users-on-their-custom-login-page-from-within-your-app/) gyorsítás használata az Azure Active Directory által támogatott tartományban javaslatot olvashat.

>[!NOTE]
>Ha egy tartomány mutató jelenlétét felülbírálja az alkalmazáshoz beállított bármely HRD-házirend a hitelesítési kérelem tartalmazza.

## <a name="home-realm-discovery-hrd-policy"></a>Hitelesítőtartomány felderítése (HRD) házirend
Egyes alkalmazások nem teszik lehetővé a hitelesítési kérelmet általuk konfigurálása, és ebben az esetben nincs lehetőség használata tartomány tippek automatikus-gyorsítás szabályozására.   Automatikus-gyorsítás állíthatók házirend kezeléséhez a kívánt viselkedést eredményező beállítást.  

### <a name="setting-hrd-policy"></a>HRD házirend beállítása
Automatikus-gyorsítás bejelentkezési beállítását az alkalmazás három lépésben történik


1. Az automatikus-gyorsítás HRD házirend létrehozása
2. A szolgáltatás tagot, amelyhez csatolni a szabályzat keresése
3. A szabályzat csatolása a szolgáltatás elvet.  Házirendek előfordulhat, hogy létrehozott egy bérlő, de nincs hatása, ha csak egy entitás vannak csatolva.  Egy HRD házirend csatolható egy szolgáltatásnevet, és egyszerre csak egy HRD házirend egy adott entitás aktív lehet.  

Automatikus-gyorsítás beállításához használhatja a Microsoft Azure Active Directory Graph API közvetlenül, vagy az Azure Active Directory PowerShell-parancsmagok HRD-házirenddel

A Graph API-val, amely kezeli a házirend leírását [Itt](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations).

Íme egy példa a házirend-definíció HRD:
    
 ```
   {  
    "HomeRealmDiscoveryPolicy":
    {  
    "AccelerateToFederatedDomain":true,
    "PreferredDomain":"federated.example.edu"
    }
   }
```

A házirend-típus: "HomeRealmDiscoveryPolicy".

Ha **AccelerateToFederatedDomain** értéke HAMIS, akkor a házirend nincs hatása **PreferredDomain** annak érdekében, hogy egy tartományhoz kell jeleznie, és abban az esetben, ha a bérlő rendelkezik, csak egyetlen összevont elhagyható tartomány.  Ha ki van hagyva, és több ellenőrzése után összevont tartományt, a házirend nincs hatása.

Ha **PreferredDomain** van megadva, akkor meg kell egyeznie egy ellenőrzött tartomány összevont a bérlő számára, és a szóban forgó alkalmazás a felhasználók jelentkezhetnek be, hogy a tartományban kell lennie.

### <a name="priority-and-evaluation-of-hrd-policies"></a>Prioritás és HRD házirendek kiértékelése
HRD-házirendek létrehozása és hozzárendeli az adott szervezetek és szolgáltatásnevekről. Ez azt jelenti, hogy a több házirendet alkalmazhat az adott alkalmazást. A HRD-házirend érvénybe ezeket a szabályokat követi:


- Tartomány mutató megtalálható-e a hitelesítési kérelmet, ha bármely HRD-házirend a rendszer figyelmen kívül hagyja, és a tartomány mutató által előírt viselkedését szolgál.
- Ellenkező esetben ha egy házirend explicit módon van rendelve a szolgáltatás egyszerű, van kényszerítve. 
- Ha nem tartományi mutatót, és nincs házirend explicit módon van rendelve a szolgáltatás egyszerű, egy egyszerű szolgáltatás a szülő szervezeti explicit módon hozzárendelt lesz alkalmazva, 
- Ha nem tartományi mutatót, és nincs házirend van rendelve a szolgáltatás egyszerű vagy a szervezet, az alapértelmezett HRD viselkedés szolgál.

## <a name="tutorial-for-setting-sign-in-auto-acceleration-on-an-application-using-hrd-policy-with-azure-active-directory-powershell-cmdlets"></a>Az oktatóanyag a bejelentkezési automatikus-gyorsítás beállítása az Azure Active Directory PowerShell-parancsmagok HRD-szabályzatot használó alkalmazások
Végigvezetjük néhány olyan forgatókönyvet, beleértve:


- Egyetlen összevont tartományt egy bérlőt automatikus-gyorsítás az alkalmazás beállítása
- Automatikus-gyorsítás az alkalmazás számára a bérlő ellenőrzött több tartomány egyik beállítása
- Az alkalmazások, amelyekre a szabályzat van konfigurálva listázása

### <a name="prerequisites"></a>Előfeltételek
Az alábbi példákban létrehozása, frissítése, hivatkozásra, és szolgáltatásnevekről alkalmazás-házirendek törlése az Azure ad-ben.

1.  Első lépésként töltse le a legújabb Azure AD PowerShell-parancsmag Preview. 
2.  Miután az Azure AD PowerShell-parancsmagok, futtassa a jelentkezzen be rendszergazdai fiókkal az Azure AD Connect parancsot.

    ``` powershell
    Connect-AzureAD -Confirm
    ```
3.  Futtassa a következő parancsot a szervezet összes házirend.

    ``` powershell
    Get-AzureADPolicy
    ```

Ha semmit, hogy a bérlő létre házirendek

### <a name="example-setting-auto-acceleration-for-an-application"></a>Példa: A beállítás automatikus-gyorsítás az alkalmazáshoz 
Ebben a példában hozzon létre egy házirendet, automatikus-gyorsító felhasználók számára az AD FS bejelentkezési képernyő alkalmazáshoz való bejelentkezéskor.  Ez azokat először írja be a felhasználónevét, az Azure AD bejelentkezési oldal kellene nélkül történik. 

#### <a name="step-1-create-an-hrd-policy"></a>1. lépés: HRD házirend létrehozása
``` powershell
New-AzureADPoly -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Ha egyetlen összevont tartományt, amely hitelesíti a felhasználókat az alkalmazások, csak egy HRD-házirend létrehozása szükséges.  
Tekintse meg az új házirend, és az objektumazonosító, a következő parancsot.

``` powershell
Get-AzureADPolicy
```


Ha már van egy HRD engedélyezésére vonatkozó házirendet, automatikus – a gyorsítás hozzá lehet rendelni több alkalmazás szolgáltatás alapelveket.

#### <a name="step-2-locate-the-service-principal-to-assign-the-policy-to"></a>2. lépés: Keresse meg a házirend hozzárendelése a szolgáltatásnevet.  
A szolgáltatás rendszerbiztonsági tagot kíván rendelni a szabályzatot, ObjectId van szüksége. Többféleképpen szolgáltatásnevekről Objektumazonosítója kereséséhez.    

Is használhatja a portált, lekérheti a [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) , vagy a [Graph Explorer eszköz](https://graphexplorer.cloudapp.net/) , és jelentkezzen be az Azure AD-fiókot a szervezete szolgáltatás rendszerbiztonsági tagok megtekintéséhez, vagy azóta használata PowerShell, a get-AzureADServicePrincipal parancsmagot használhatja a szolgáltatás alapelveket és azonosítók listáját.

#### <a name="step-3-assign-the-policy-to-your-service-principal"></a>3. lépés: A házirend hozzárendelése az egyszerű szolgáltatásnév  
Miután a szolgáltatás egyszerű az auto-gyorsítás a konfigurálni kívánt alkalmazás objektumazonosító, a következő parancsot kell rendelnie a HRD-szabályzatot az egyszerű szolgáltatás található, a 2. lépésben az 1. lépésben létrehozott.

``` powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

Megismételheti ezt a parancsot az egyes egyszerű szolgáltatást hozzá szeretné adni a házirendet.

#### <a name="step-4-check-which-application-service-principals-your-auto-acceleration-policy-is-assigned-to"></a>4. lépés: Ellenőrizze a auto-gyorsítás a házirendet hozzárendeli alkalmazás szolgáltatás rendszerbiztonsági tagok
Ellenőrizheti, mely alkalmazások automatikus-gyorsítás van konfigurált házirend használja a Get-AzureADPolicyAppliedObject parancsmagot, és adja át az ellenőrizni kívánt házirend objectId.

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
#### <a name="step-5-youre-done"></a>5. lépés: Elkészült!
Próbálja meg az alkalmazás annak ellenőrzéséhez, hogy működik-e az új házirendet.

### <a name="example-listing-the-applications-for-which-an-auto-acceleration-policy-is-configured"></a>Példa:, Amelynek az auto-gyorsítás házirend konfigurált alkalmazások listája

#### <a name="step-1-list-all-policies-created-in-your-organization"></a>1. lépés: Lista összes házirend létre a szervezetében 

``` powershell
Get-AzureADPolicy
```

Megjegyzés: a **Objektumazonosító** lista hozzárendeléseinek kíván a házirend.

#### <a name="step-2-list-the-service-principals-the-policy-is-assigned-to"></a>2. lépés: A házirend be van-e rendelve szolgáltatásnevekről listában.  

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```

### <a name="example-removing-an-auto-acceleration-policy-for-an-application"></a>Példa: Egy automatikus-gyorsítás házirend, az alkalmazás eltávolítása
#### <a name="step-1-use-the-previous-example-to-get-the-objectid-of-the-policy-and-that-of-the-application-service-principal-you-wish-to-remove-it-from"></a>1. lépés: Az előző példát követve a házirend ObjectId, és hogy a alkalmazás service rendszerbiztonsági tag el szeretné távolítani az beszerzése
#### <a name="step-2-remove-the-policy-assignment-from-the-application-service-principal"></a>2. lépés: A házirend-hozzárendelés eltávolítása az alkalmazás egyszerű szolgáltatás.  

``` powershell
Remove-AzureADApplicationPolicy -ObjectId <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

#### <a name="step-3-check-removal-by-listing-the-service-principals-the-policy-is-assigned-to"></a>3. lépés: Ehhez listázza a szolgáltatásnevekről ellenőrzés eltávolítása a házirendet hozzárendeli 

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
## <a name="next-steps"></a>Következő lépések
- Az Azure AD hitelesítési működéséről további információkért lásd: [hitelesítési forgatókönyvek az Azure AD](develop/active-directory-authentication-scenarios.md).
- A felhasználó egyszeri bejelentkezést további információ: [alkalmazások elérése és az egyszeri bejelentkezés az Azure Active Directoryval](active-directory-enterprise-apps-manage-sso.md)
- Látogasson el a [Active Directory fejlesztői útmutatója](develop/active-directory-developers-guide.md) minden fejlesztéssel kapcsolatos tartalom áttekintését.
