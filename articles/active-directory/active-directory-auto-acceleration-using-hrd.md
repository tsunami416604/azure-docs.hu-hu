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
ms.date: 06/08/2018
ms.author: barbkess
ms.openlocfilehash: bc1f0341f4e1c07dc16522f5a2ae36fa2e64d1fb
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248775"
---
# <a name="configure-azure-active-directory-sign-in-behavior-for-an-application-by-using-a-home-realm-discovery-policy"></a>Állítsa be a Azure Active Directory bejelentkezési az alkalmazás viselkedését Hitelesítőtartományának feltárási házirend segítségével

A következő dokumentum bevezetője, amelyek konfigurálása az Azure Active Directory hitelesítési viselkedést szövetséges felhasználóknak.   Összevont tartományt a felhasználók automatikus-gyorsítás és a hitelesítési korlátozások konfigurációs magában foglalja.

## <a name="home-realm-discovery"></a>Hitelesítőtartomány feltárásához
A Kezdőtartomány felderítése (HRD) a rendszer, amely lehetővé teszi, hogy az Azure Active Directory (Azure AD) határozza meg, amikor a felhasználó hitelesítésére kell a bejelentkezés során.  Amikor egy felhasználó bejelentkezik az Azure AD-bérlő erőforrások eléréséhez, vagy az Azure AD közös bejelentkezési oldalra, akkor írja be a felhasználónevet (UPN). Az Azure AD használatával, amely felderítése, ahol a felhasználónak be kell jelentkeznie. 

A felhasználó hitelesítését a következő helyek valamelyikén kell venni lehet szükség:

- A (lehet, hogy a felhasználó megpróbál hozzáférni a erőforrásként ugyanannak a bérlőnek) a felhasználó otthoni bérlő. 

- Microsoft-fiókkal.  A felhasználó nem a Vendég az erőforrás-bérlőnél.

-  A helyszíni identitásszolgáltató például az Active Directory összevonási szolgáltatások (AD FS).

- Össze van vonva az Azure AD bérlője egy másik identitásszolgáltató.

## <a name="auto-acceleration"></a>Automatikus-gyorsulás 
Egyes szervezetek tartományok konfigurálása a másik IdP, például az AD FS felhasználói hitelesítés összevonni kívánt Azure Active Directory-bérlőben.  

Amikor a felhasználó bejelentkezik egy alkalmazásba, azokat először megnyílik egy Azure AD bejelentkezési oldalára. Után az egyszerű Felhasználónevük, ha egy összevont tartományt a beírt azok majd kerül a kiállító terjesztési hely bejelentkezési oldalán, hogy a tartományt szolgálja ki. Bizonyos körülmények között a rendszergazdák előfordulhat, hogy szeretné, hogy át tudja irányítani a felhasználók számára a bejelentkezési lapon, ha azok most jelentkezik be bizonyos alkalmazásokat. 

Emiatt a felhasználók az Azure Active Directory kezdőlap kihagyhatja. Ezt a folyamatot nevezzük "bejelentkezés automatikus-gyorsítás."

Azokban az esetekben, ahol a bérlő össze van-e vonva a bejelentkezéshez egy másik IdP automatikus-gyorsítás lehetővé teszi felhasználói bejelentkezési több zökkenőmentes.  Konfigurálhatja az automatikus-gyorsítás egyes alkalmazásokhoz.

>[!NOTE]
>Ha konfigurálja automatikus-gyorsítás alkalmazás, vendég felhasználók nem jelentkezhetnek be. Ha egy felhasználó rögtön a hitelesítés egy összevont IdP, nincs semmilyen módon nem lehet, hogy vissza az Azure Active Directory bejelentkezési oldalára. Vendég, előfordulhat, hogy igénylő felhasználók más bérlők vagy egy külső IdP, például a Microsoft-fiók irányíthatja, nem jelentkezhet be alkalmazásra, mert azok most kihagyja a Hitelesítőtartomány lépés.  

Egy összevont IdP való automatikus-gyorsulás vezérlésére két módja van:   

- A hitelesítési kérelmek egy tartományi mutató használható az alkalmazás. 
- Ahhoz, hogy automatikus-gyorsítás Hitelesítőtartományának feltárási szabályzat beállítása.

### <a name="domain-hints"></a>Tartomány tippek    
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
>Ha egy tartomány mutató hitelesítési kérelmet tartalmaz, jelenlétét felülbírálja HRD szabályzatot az alkalmazáshoz beállított automatikus gyorsítás.

### <a name="home-realm-discovery-policy-for-auto-acceleration"></a>Automatikus-gyorsítás otthoni Hitelesítőtartományának feltárási házirendje
Egyes alkalmazások nem nyújtanak olyan konfigurálásához az általuk hitelesítési kérelmet. Ebben az esetben nincs lehetőség használata tartomány tippek automatikus-gyorsítás szabályozására. Automatikus-gyorsítás állíthatók házirend kezeléséhez a kívánt viselkedést eredményező beállítást.  

## <a name="enable-direct-authentication-for-legacy-applications"></a>Örökölt alkalmazások közvetlen hitelesítés engedélyezése
Ajánlott az alkalmazások az AAD-tárak és interaktív bejelentkezés használatával hitelesíti a felhasználókat. A könyvtárak a összevont felhasználó adatfolyamok kezeli.  Néha örökölt alkalmazások nem szerepel a összevonási ismertetése. Ugyanakkor ne hajtsa végre a hitelesítőtartomány feltárását, és nem kommunikál a megfelelő összevont végpont a felhasználó hitelesítéséhez. Ha szeretné, HRD házirend segítségével adott örökölt alkalmazások, amelyek küldenek felhasználónév/jelszó hitelesítő adataival közvetlenül az Azure Active Directoryban. Jelszókivonat-szinkronizálás engedélyezve kell lennie. 

> [!IMPORTANT]
> Csak engedélyezze a közvetlen hitelesítést, ha engedélyezve van a Jelszókivonat-szinkronizálás és ismeri az alkalmazás minden házirendet a helyszíni IdP által megvalósított használata nélkül végezzen hitelesítést kapcsolatát. Ha kikapcsolja a Jelszókivonat-szinkronizálás, vagy kapcsolja ki az AD Connect címtár-szinkronizálás bármilyen okból, el kell távolítania ezt a házirendet annak megakadályozására, hogy közvetlen hitelesítése Jelszókivonat elavult.

## <a name="set-hrd-policy"></a>HRD-szabály beállítása
Az összevont bejelentkezési automatikus-gyorsítás alkalmazás vagy a felhőalapú alkalmazások közvetlen beállítás HRD házirend három lépésben történik:

1. HRD-házirend létrehozása.

2. Keresse meg a szolgáltatás egyszerű, amelyhez csatolni a házirendet.

3. A szabályzat csatolása a szolgáltatásnevet. 

Szabályzatok csak érvénybe egy adott alkalmazáshoz, amikor egy egyszerű szolgáltatásnév vannak csatolva. 

Egyszerre csak egy HRD házirend lehet aktív a szolgáltatásnevet.  

A Microsoft Azure Active Directory Graph API közvetlenül, vagy az Azure Active Directory PowerShell-parancsmagok segítségével létrehozásához és kezeléséhez HRD házirend.

A Graph API-val, amely kezeli a házirend leírását a [házirend műveletek](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) cikket az MSDN Webhelyén.

Az alábbiakban látható egy példa HRD házirend-definíció:
    
 ```
   {  
    "HomeRealmDiscoveryPolicy":
    {  
    "AccelerateToFederatedDomain":true,
    "PreferredDomain":"federated.example.edu",
    "AllowCloudPasswordValidation":true
    }
   }
```

A házirend típusa nem "HomeRealmDiscoveryPolicy."

**AccelerateToFederatedDomain** nem kötelező megadni. Ha **AccelerateToFederatedDomain** értéke false, a házirend nincs hatása, az automatikus-gyorsítás. Ha **AccelerateToFederatedDomain** igaz, és nem csak az egyik ellenőrzése és a bérlői, majd a felhasználók összevont tartományt megnyílik egyenes az összevont IdP bejelentkezéshez. Ha igaz, és több ellenőrzött tartomány a bérlő **PreferredDomain** meg kell adni.

**PreferredDomain** nem kötelező megadni. **PreferredDomain** kell jeleznie, annak érdekében, amelyhez egy tartományhoz. Ha a bérlő csak egy összevont tartományt elhagyható.  Ha ki van hagyva, és több ellenőrizte, hogy összevont tartományt, a házirend nincs hatása.

 Ha **PreferredDomain** meg van adva, akkor meg kell egyeznie egy ellenőrzött tartomány összevont a bérlő számára. Az alkalmazás minden felhasználó ebbe a tartományba való bejelentkezéshez képesnek kell lennie.

**AllowCloudPasswordValidation** nem kötelező megadni. Ha **AllowCloudPasswordValidation** értéke igaz, akkor az alkalmazás kívánja elvégezni a hitelesítést egy összevont felhasználó felhasználónév/jelszó hitelesítő adatok közvetlenül az Azure Active Directory-jogkivonat végpontjához az engedélyezett. Ez csak akkor működik a Jelszókivonatok szinkronizálásának engedélyezése.

### <a name="priority-and-evaluation-of-hrd-policies"></a>Prioritás és HRD házirendek kiértékelése
HRD-házirendek létrehozása és hozzárendeli az adott szervezetek és szolgáltatásnevekről. Ez azt jelenti, hogy a több házirendet alkalmazhat az adott alkalmazást. A HRD-házirend érvénybe ezeket a szabályokat követi:


- Tartomány mutató megtalálható-e a hitelesítési kérelmet, ha bármely HRD házirend figyelmen kívül hagyja az auto-gyorsítás. A tartomány mutató által megadott viselkedését szolgál.

- Ellenkező esetben ha egy házirend explicit módon van rendelve a szolgáltatás egyszerű, van kényszerítve. 

- Ha nem tartományi mutatót, és nincs házirend explicit módon van rendelve a szolgáltatás egyszerű, egy egyszerű szolgáltatás a szülő szervezeti explicit módon hozzárendelt lesz alkalmazva, 

- Ha nem tartományi mutatót, és nincs házirend van rendelve a szolgáltatás egyszerű vagy a szervezet, az alapértelmezett HRD viselkedés szolgál.

## <a name="tutorial-for-setting-hrd-policy-on-an-application"></a>Útmutató az alkalmazás HRD házirend beállítása 
Azure AD PowerShell-parancsmagok néhány olyan forgatókönyvet, beleértve a bízná használjuk:


- Ehhez az automatikus-gyorsítás egy bérlő egyetlen összevont tartományt a alkalmazásának HRD házirend beállítása.

- HRD házirend beállítása automatikus-gyorsítás az alkalmazás számára több ellenőrzött tartomány egyik tegye a bérlő számára.

- HRD házirend beállítása az örökölt alkalmazások közvetlen felhasználónév/jelszó-hitelesítés az Azure Active Directory egy összevont felhasználó engedélyezése.

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

### <a name="example-set-hrd-policy-for-an-application"></a>Példa: Állítsa be az alkalmazás HRD-házirend 

Ebben a példában házirendet hoz létre, ha hozzá van rendelve egy alkalmazás vagy: 
- Automatikus-felgyorsítják felhasználók egy AD FS bejelentkezési képernyő, ha azok bejelentkezés egy alkalmazás Ha a bérlő egyetlen tartományt tartalmaz. 
- Felhasználók automatikus felgyorsítják egy AD FS bejelentkezési képernyő, nincs több összevont tartományt a bérlő.
- Lehetővé teszi, hogy közvetlenül az Azure Active Directoryval összevont felhasználók az alkalmazásra, a házirend be van-e rendelve a bejelentkezés nem interaktív felhasználónév/jelszó.

#### <a name="step-1-create-an-hrd-policy"></a>1. lépés: HRD házirend létrehozása

A következő házirend automatikus-felgyorsítják felhasználók egy AD FS bejelentkezési képernyő, ha azok bejelentkezés egy alkalmazás Ha a bérlő egyetlen tartományt tartalmaz.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```
A következő házirend automatikus-felgyorsítják felhasználók egy összevont tartományt a bérlő több olyan AD FS bejelentkezési képernyő van. Ha egynél több összevont tartományt, amely hitelesíti a felhasználókat az alkalmazások, meg kell adnia annak érdekében, automatikus tartomány.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true, "PreferredDomain":"federated.example.edu"}}") -DisplayName MultiDomainAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Ahhoz, hogy felhasználónév/jelszó hitelesítés szövetséges felhasználóknak közvetlenül az Azure Active Directoryval adott alkalmazásokra vonatkozó házirend létrehozásához a következő parancsot:

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AllowCloudPasswordValidation`":true}}") -DisplayName EnableDirectAuthPolicy -Type HomeRealmDiscoveryPolicy
```


Tekintse meg az új házirend, és a **ObjectID**, a következő parancsot:

``` powershell
Get-AzureADPolicy
```


A HRD-házirendet a létrehozást követően az alkalmazásához hozzá lehet rendelni a több alkalmazás szolgáltatásnevekről.

#### <a name="step-2-locate-the-service-principal-to-which-to-assign-the-policy"></a>2. lépés:, Keresse meg a szolgáltatás egyszerű, amelyhez a házirend kijelölése  
Van szüksége a **ObjectID** a szolgáltatás rendszerbiztonsági tagot, amelyhez hozzá szeretné a házirendet. Többféleképpen is található a **ObjectID** szolgáltatás rendszerbiztonsági tagot.    

A portál is használhatja, vagy lekérheti [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity). Ön is végrehajthatja a [Graph Explorer eszköz](https://graphexplorer.cloudapp.net/) és a szervezet szolgáltatásnevekről megtekintéséhez jelentkezzen be az Azure AD-fiókot. PowerShell használ, mert a get-AzureADServicePrincipal parancsmag segítségével kilistázhatja a szolgáltatásnevekről és azonosítók.

#### <a name="step-3-assign-the-policy-to-your-service-principal"></a>3. lépés: A házirend hozzárendelése az egyszerű szolgáltatásnév  
Miután a **ObjectID** , az egyszerű szolgáltatás, amelynek automatikus-gyorsítás konfigurálni szeretné az alkalmazás, a következő parancsot. Ez a parancs a HRD-házirendet, amely a 2. lépésben megkeresett egyszerű az 1. lépésben létrehozott társítja.

``` powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

Minden szolgáltatás egyszerű kívánt a házirend hozzáadása a megismételheti ezt a parancsot.

Abban az esetben, ha egy alkalmazás már rendelkezik hozzárendelt HomeRealmDiscovery házirend akkor nem fogja tudni hozzáadása egy másikat.  Ebben az esetben módosítsa a definíciót a további paraméterek hozzáadása az alkalmazáshoz hozzárendelt Hitelesítőtartományának feltárási házirend.

#### <a name="step-4-check-which-application-service-principals-your-hrd-policy-is-assigned-to"></a>4. lépés: Ellenőrizze a HRD a házirendet hozzárendeli alkalmazás szolgáltatás rendszerbiztonsági tagok
Mely alkalmazások kereséséhez beállított HRD-házirendje, használja a **Get-AzureADPolicyAppliedObject** parancsmag. Adja át a **ObjectID** az ellenőrizni kívánt házirend.

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
#### <a name="step-5-youre-done"></a>5. lépés: Elkészült!
Próbálja meg az alkalmazás annak ellenőrzéséhez, hogy működik-e az új házirendet.

### <a name="example-list-the-applications-for-which-hrd-policy-is-configured"></a>Példa: A házirend mely HRD van konfigurálva alkalmazások felsorolása

#### <a name="step-1-list-all-policies-that-were-created-in-your-organization"></a>1. lépés: A szervezet létrehozott összes házirendek felsorolása 

``` powershell
Get-AzureADPolicy
```

Megjegyzés: a **ObjectID** lista hozzárendeléseinek kívánt házirend.

#### <a name="step-2-list-the-service-principals-to-which-the-policy-is-assigned"></a>2. lépés:, Amelyhez a házirendet hozzárendeli szolgáltatásnevekről felsorolása  

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```

### <a name="example-remove-an-hrd-policy-for-an-application"></a>Példa: Egy HRD házirend, az alkalmazás eltávolítása
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
