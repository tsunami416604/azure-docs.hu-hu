---
title: Bejelentkezési automatikus-gyorsítás Hitelesítőtartományának feltárási házirend segítségével az alkalmazás konfigurálása |} Microsoft Docs
description: Azt ismerteti, milyen az Azure AD-bérlő fogalmát, valamint az Azure Active Directoryn keresztül Azure kezelését.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: it-pro
ms.date: 11/09/2017
ms.author: barbkess
ms.openlocfilehash: 5df12f905595c9b3e8caa8f372b9ba7b54672f81
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="configure-sign-in-auto-acceleration-for-an-application-by-using-a-home-realm-discovery-policy"></a>Bejelentkezési automatikus-gyorsítás az alkalmazás konfigurálása Hitelesítőtartományának feltárási házirendek használatával

A következő dokumentum bemutatja azokat a Hitelesítőtartomány és automatikus-gyorsítás.

## <a name="home-realm-discovery"></a>Hitelesítőtartomány feltárásához
A Kezdőtartomány felderítése (HRD) az a folyamat, amely lehetővé teszi az Azure Active Directory (Azure AD) területen megállapíthatja, hogy a bejelentkezés során, amikor egy felhasználó kell hitelesíteni.  Amikor egy felhasználó bejelentkezik az Azure AD-bérlő erőforrások eléréséhez, vagy az Azure AD közös bejelentkezési oldalra, akkor írja be a felhasználónevet (UPN). Az Azure AD használatával, amely felderítése, ahol a felhasználónak be kell jelentkeznie. 

A felhasználó hitelesítését a következő helyek valamelyikén kell venni lehet szükség:

- A (lehet, hogy a felhasználó megpróbál hozzáférni a erőforrásként ugyanannak a bérlőnek) a felhasználó otthoni bérlő. 

- Microsoft-fiókkal.  A felhasználó nem a Vendég az erőforrás-bérlőnél.

- Össze van vonva az Azure AD bérlője egy másik identitásszolgáltató.

-  A helyszíni identitásszolgáltató például az Active Directory összevonási szolgáltatások (AD FS).

## <a name="auto-acceleration"></a>Automatikus-gyorsulás 
Egyes szervezetek konfigurálása az Azure Active Directory-bérlő egy másik IdP, például az AD FS felhasználói hitelesítés összevonni kívánt.  

Ezekben az esetekben amikor a felhasználó bejelentkezhet egy alkalmazásba, először először jelenjenek meg ezek az az Azure AD bejelentkezési oldalára. Az egyszerű Felhasználónevük beírt, miután azok majd kerül a kiállító terjesztési hely bejelentkezési oldal. Bizonyos körülmények között a rendszergazdák előfordulhat, hogy szeretné, hogy át tudja irányítani a felhasználók számára a bejelentkezési lapon, ha azok most jelentkezik be bizonyos alkalmazásokat. 

Ez azt jelenti, hogy felhasználók kihagyhatja a kezdeti Azure Active Directory-lapon. Ezt a folyamatot nevezzük "bejelentkezés automatikus-gyorsítás."

Azokban az esetekben, ahol a bérlő össze van-e vonva a bejelentkezéshez egy másik IdP automatikus-gyorsítás lehetővé teszi felhasználói bejelentkezési több zökkenőmentes.  Konfigurálhatja az automatikus-gyorsítás egyes alkalmazásokhoz.

>[!NOTE]
>Ha konfigurálja automatikus-gyorsítás alkalmazás, vendég felhasználók nem jelentkezhetnek be. Ha egy felhasználó rögtön a hitelesítés egy összevont IdP, nincs semmilyen módon nem lehet, hogy vissza az Azure Active Directory bejelentkezési oldalára. Vendég, előfordulhat, hogy igénylő felhasználók más bérlők vagy egy külső IdP, például a Microsoft-fiók irányíthatja, nem jelentkezhet be alkalmazásra, mert azok most kihagyja a Hitelesítőtartomány lépés.  

Egy összevont IdP való automatikus-gyorsulás vezérlésére két módja van:   

- A hitelesítési kérelmek egy tartományi mutató használható az alkalmazás. 
- Ahhoz, hogy automatikus-gyorsítás Hitelesítőtartományának feltárási szabályzat beállítása.

## <a name="domain-hints"></a>Tartomány tippek 
Tartomány mutatók olyan irányelvek, amelyek a hitelesítési kérelmet egy alkalmazás szerepel. Annak érdekében, a felhasználó az összevont IdP bejelentkezési oldalra használható. Vagy a felhasználásuk egy több-bérlős alkalmazás annak érdekében, a felhasználó közvetlenül a vállalati arculattal ellátott az Azure AD bejelentkezési oldalára a bérlő.  

Például a "largeapp.com" alkalmazás lehetővé teheti az ügyfeleknek hozzáférni az alkalmazáshoz, egy egyéni URL-címe "contoso.largeapp.com." Az alkalmazás a hitelesítési kérelem is közé tartozik a contoso.com tartomány javaslat. 

Tartomány mutató szintaxis a protokoll, amellyel függ, és általában az alkalmazás van konfigurálva.

**WS-Federation**: whr=contoso.com a lekérdezési karakterláncban.

**SAML**: vagy a SAML-alapú hitelesítési kérelmeket, amelyek tartományi mutató vagy egy lekérdezési karakterlánc whr=contoso.com tartalmaz.

**Nyissa meg az ID Connect**: egy lekérdezési karakterlánc domain_hint=contoso.com. 

Ha tartományi mutató szerepel a hitelesítési kérelmet az alkalmazásból, és a bérlő össze van vonva az adott tartománnyal, az Azure AD megkísérli bejelentkezési átirányítása a kiállító terjesztési hely, amely tartományhoz van konfigurálva. 

Ha a tartomány mutató nem ellenőrzött összevont tartományt, figyelmen kívül, és normál Hitelesítőtartományának feltárási elindítását.

A gyorsítás az Azure Active Directory által támogatott tartományban mutató használatával kapcsolatos további információkért lásd: a [nagyvállalati mobilitási és biztonsági blogot](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/11/using-azure-ad-to-land-users-on-their-custom-login-page-from-within-your-app/).

>[!NOTE]
>Ha a tartomány mutató hitelesítési kérelmet tartalmaz, jelenlétét felülbírálják bármely HRD házirend, amely az alkalmazás.

## <a name="home-realm-discovery-policy"></a>Otthoni Hitelesítőtartományának feltárási házirend
Egyes alkalmazások nem nyújtanak olyan konfigurálásához az általuk hitelesítési kérelmet. Ebben az esetben nincs lehetőség használata tartomány tippek automatikus-gyorsítás szabályozására. Automatikus-gyorsítás állíthatók házirend kezeléséhez a kívánt viselkedést eredményező beállítást.  

### <a name="set-hrd-policy"></a>HRD-szabály beállítása
A bejelentkezési automatikus-gyorsítás beállítása egy alkalmazás három lépésben történik:


1. Az automatikus-gyorsítás HRD házirend létrehozása.

2. A szolgáltatás egyszerű, amelyhez csatolni a szabályzat keresése.

3. A szabályzat csatolása az egyszerű szolgáltatásnév. Házirendek előfordulhat, hogy létrehozott egy bérlőt, de nem rendelkeznek hatással, amíg egy entitás vannak csatolva. 

Egy HRD házirend csatlakoztatható egy egyszerű szolgáltatást, és egyszerre csak egy HRD házirend egy adott entitás aktív lehet.  

A Microsoft Azure Active Directory Graph API közvetlenül, vagy az Azure Active Directory PowerShell-parancsmagok segítségével állítsa be automatikus-gyorsítás HRD-házirenddel.

A Graph API-val, amely kezeli a házirend leírását a [házirend műveletek](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) cikket az MSDN Webhelyén.

Az alábbiakban látható egy példa HRD házirend-definíció:
    
 ```
   {  
    "HomeRealmDiscoveryPolicy":
    {  
    "AccelerateToFederatedDomain":true,
    "PreferredDomain":"federated.example.edu"
    }
   }
```

A házirend típusa nem "HomeRealmDiscoveryPolicy."

Ha **AccelerateToFederatedDomain** értéke false, a házirend nincs hatása.

**PreferredDomain** kell jeleznie, annak érdekében, amelyhez egy tartományhoz. Ha a bérlő csak egy összevont tartományt elhagyható.  Ha ki van hagyva, és több ellenőrizte, hogy összevont tartományt, a házirend nincs hatása.

Ha **PreferredDomain** meg van adva, akkor meg kell egyeznie egy ellenőrzött tartomány összevont a bérlő számára. Az alkalmazás minden felhasználó ebbe a tartományba való bejelentkezéshez képesnek kell lennie.

### <a name="priority-and-evaluation-of-hrd-policies"></a>Prioritás és HRD házirendek kiértékelése
HRD-házirendek létrehozása és hozzárendeli az adott szervezetek és szolgáltatásnevekről. Ez azt jelenti, hogy a több házirendet alkalmazhat az adott alkalmazást. A HRD-házirend érvénybe ezeket a szabályokat követi:


- Ha egy tartomány mutató megtalálható-e a hitelesítési kérést, bármely HRD házirend figyelmen kívül hagyja. A tartomány mutató által megadott viselkedését szolgál.

- Ellenkező esetben ha egy házirend explicit módon van rendelve a szolgáltatás egyszerű, van kényszerítve. 

- Ha nem tartományi mutatót, és nincs házirend explicit módon van rendelve a szolgáltatás egyszerű, egy egyszerű szolgáltatás a szülő szervezeti explicit módon hozzárendelt lesz alkalmazva, 

- Ha nem tartományi mutatót, és nincs házirend van rendelve a szolgáltatás egyszerű vagy a szervezet, az alapértelmezett HRD viselkedés szolgál.

## <a name="tutorial-for-setting-sign-in-auto-acceleration-on-an-application-by-using-an-hrd-policy"></a>Útmutató az alkalmazás automatikus-gyorsítás bejelentkezési beállítása az HRD házirend használatával
Azure AD PowerShell-parancsmagok néhány olyan forgatókönyvet, beleértve a bízná használjuk:


- A bérlő egyetlen összevont tartományt a auto-gyorsítás az alkalmazás beállítását.

- Automatikus-gyorsítás az alkalmazás számára a bérlő ellenőrzött több tartomány egyik beállítása.

- Az alkalmazások, amelyekre a szabályzat van konfigurálva listázása.

### <a name="prerequisites"></a>Előfeltételek
A következő példákban meg létrehozása, frissítése, hivatkozás és törlése alkalmazás szolgáltatásnevekről házirendek az Azure AD.

1.  Első lépésként töltse le a legújabb Azure AD PowerShell-parancsmag preview. 

2.  Miután letöltötte az Azure AD PowerShell-parancsmagok, a Csatlakozás parancsot jelentkezzen be rendszergazdai fiókkal az Azure AD:

    ``` powershell
    Connect-AzureAD -Confirm
    ```
3.  Futtassa a következő parancsot, hogy a szervezet összes házirend:

    ``` powershell
    Get-AzureADPolicy
    ```

Ha semmit, akkor nincsenek házirendek, a bérlő létre.

### <a name="example-set-auto-acceleration-for-an-application"></a>Példa: Készlet automatikus-gyorsítás az alkalmazáshoz 
Ebben a példában automatikus-gyorsító felhasználók egy AD FS bejelentkezési képernyő, ha azok bejelentkezik, egy alkalmazás házirend létrehozása. Felhasználó bejelentkezve is az AD FS nem kell először az Azure AD bejelentkezési lapon adjon meg egy felhasználónevet. 

#### <a name="step-1-create-an-hrd-policy"></a>1. lépés: HRD házirend létrehozása
``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Ha egyetlen összevont tartományt, amely hitelesíti a felhasználókat az alkalmazások, csak egy HRD-házirend létrehozásához szüksége.  

Tekintse meg az új házirend, és a **ObjectID**, a következő parancsot:

``` powershell
Get-AzureADPolicy
```


Miután egy HRD házirend automatikus-gyorsító engedélyezéséhez hozzá lehet rendelni a több alkalmazás szolgáltatásnevekről.

#### <a name="step-2-locate-the-service-principal-to-which-to-assign-the-policy"></a>2. lépés:, Keresse meg a szolgáltatás egyszerű, amelyhez a házirend kijelölése  
Van szüksége a **ObjectID** a szolgáltatás rendszerbiztonsági tagot, amelyhez hozzá szeretné a házirendet. Többféleképpen is található a **ObjectID** szolgáltatás rendszerbiztonsági tagot.    

A portál is használhatja, vagy lekérheti [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity). Ön is végrehajthatja a [Graph Explorer eszköz](https://graphexplorer.cloudapp.net/) és a szervezet szolgáltatásnevekről megtekintéséhez jelentkezzen be az Azure AD-fiókot. PowerShell használ, mert a get-AzureADServicePrincipal parancsmag segítségével kilistázhatja a szolgáltatásnevekről és azonosítók.

#### <a name="step-3-assign-the-policy-to-your-service-principal"></a>3. lépés: A házirend hozzárendelése az egyszerű szolgáltatásnév  
Miután a **ObjectID** , az egyszerű szolgáltatás, amelynek automatikus-gyorsítás konfigurálni szeretné az alkalmazás, a következő parancsot. Ez a parancs a HRD-házirendet, amely a 2. lépésben megkeresett egyszerű az 1. lépésben létrehozott társítja.

``` powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

Minden szolgáltatás egyszerű kívánt a házirend hozzáadása a megismételheti ezt a parancsot.

#### <a name="step-4-check-which-application-service-principals-your-auto-acceleration-policy-is-assigned-to"></a>4. lépés: Ellenőrizze a auto-gyorsítás a házirendet hozzárendeli alkalmazás szolgáltatás rendszerbiztonsági tagok
Mely alkalmazások kereséséhez konfigurált automatikus-gyorsítás házirenddel rendelkezik, használja a **Get-AzureADPolicyAppliedObject** parancsmag. Adja át a **ObjectID** az ellenőrizni kívánt házirend.

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
#### <a name="step-5-youre-done"></a>5. lépés: Elkészült!
Próbálja meg az alkalmazás annak ellenőrzéséhez, hogy működik-e az új házirendet.

### <a name="example-list-the-applications-for-which-an-auto-acceleration-policy-is-configured"></a>Példa: Az alkalmazások, amely az auto-gyorsítás házirend beállított felsorolása

#### <a name="step-1-list-all-policies-that-were-created-in-your-organization"></a>1. lépés: A szervezet létrehozott összes házirendek felsorolása 

``` powershell
Get-AzureADPolicy
```

Megjegyzés: a **ObjectID** lista hozzárendeléseinek kívánt házirend.

#### <a name="step-2-list-the-service-principals-to-which-the-policy-is-assigned"></a>2. lépés:, Amelyhez a házirendet hozzárendeli szolgáltatásnevekről felsorolása  

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```

### <a name="example-remove-an-auto-acceleration-policy-for-an-application"></a>Példa: Egy automatikus-gyorsítás házirend, az alkalmazás eltávolítása
#### <a name="step-1-get-the-objectid"></a>1. lépés: Az ObjectId azonosító lekérése
Használja az előző példában a **ObjectID** a házirendet, és, hogy az egyszerű szolgáltatás, amelyből el kívánja, hogy eltávolítsa a. 

#### <a name="step-2-remove-the-policy-assignment-from-the-application-service-principal"></a>2. lépés: A házirend-hozzárendelés eltávolítása az alkalmazás egyszerű szolgáltatás  

``` powershell
Remove-AzureADApplicationPolicy -ObjectId <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

#### <a name="step-3-check-removal-by-listing-the-service-principals-to-which-the-policy-is-assigned"></a>3. lépés:, Amelyhez a házirendet hozzárendeli szolgáltatásnevekről listázása eltávolítása ellenőrzése 

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
## <a name="next-steps"></a>További lépések
- Az Azure AD hitelesítési működésével kapcsolatos további információkért lásd: [hitelesítési forgatókönyvek az Azure AD](develop/active-directory-authentication-scenarios.md).
- A felhasználó egyszeri bejelentkezést kapcsolatos további információkért lásd: [alkalmazások elérése és az egyszeri bejelentkezés az Azure Active Directoryval](active-directory-enterprise-apps-manage-sso.md).
- Látogasson el a [Active Directory fejlesztői útmutatója](develop/active-directory-developers-guide.md) minden fejlesztéssel kapcsolatos tartalom áttekintését.
