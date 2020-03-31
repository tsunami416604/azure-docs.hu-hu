---
title: Bejelentkezés automatikus gyorsításának konfigurálása a Home Realm Discovery használatával
description: Megtudhatja, hogyan konfigurálhatja a Home Realm Discovery szabályzatot az Azure Active Directory-hitelesítéshez az összevont felhasználók számára, beleértve az automatikus gyorsítást és a tartományi tippeket.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mimart
ms.custom: seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60bfc964ffc394b3f79c9d279158003f383b7331
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943442"
---
# <a name="configure-azure-active-directory-sign-in-behavior-for-an-application-by-using-a-home-realm-discovery-policy"></a>Az Azure Active Directory bejelentkezési viselkedésének konfigurálása egy alkalmazáshoz otthoni tartományi felderítési szabályzat használatával

Ez a cikk bemutatja az Azure Active Directory hitelesítési viselkedésének konfigurálását az összevont felhasználók számára. Ez magában foglalja az automatikus gyorsítás és a hitelesítési korlátozások konfigurálása a felhasználók számára az összevont tartományokban.

## <a name="home-realm-discovery"></a>Kezdőtartomány felderítése
Home Realm Discovery (HRD) az a folyamat, amely lehetővé teszi az Azure Active Directory (Azure AD) határozza meg, ahol a felhasználónak kell hitelesíteni a bejelentkezési időben.  Amikor egy felhasználó bejelentkezik egy Azure AD-bérlő egy erőforrás eléréséhez, vagy az Azure AD közös bejelentkezési lap, akkor írja be a felhasználónevét (UPN). Az Azure AD ezt használja a felhasználó bejelentkezési helyének felderítéséhez. 

Előfordulhat, hogy a hitelesítéshez a felhasználót az alábbi helyek egyikére kell vinni:

- A felhasználó otthoni bérlője (lehet, hogy ugyanaz a bérlő, mint a felhasználó által elérni kívánt erőforrás). 

- Microsoft-fiók lehetőséget.  A felhasználó az erőforrás-bérlő vendége.

-  Helyszíni identitásszolgáltató, például az Active Directory összevonási szolgáltatások (AD FS).

- Egy másik identitásszolgáltató, amely az Azure AD-bérlővel van összeegyítve.

## <a name="auto-acceleration"></a>Automatikus gyorsítás 
Egyes szervezetek konfigurálják a tartományokat az Azure Active Directory-bérlőben, hogy egy másik IdP-vel, például az AD FS-sel egyenlítsenek a felhasználói hitelesítéshez.  

Amikor egy felhasználó bejelentkezik egy alkalmazásba, először megjelenik egy Azure AD bejelentkezési lap. Miután beírták a saját upn, ha azok egy összevont tartományban, akkor majd az adott tartományt kiszolgáló IdP bejelentkezési oldalára kerülnek. Bizonyos körülmények között előfordulhat, hogy a rendszergazdák szeretnék irányítani a felhasználókat a bejelentkezési oldalra, amikor ők bejelentkezik az adott alkalmazások. 

Ennek eredményeképpen a felhasználók kihagyhatják a kezdeti Azure Active Directory-lapot. Ezt a folyamatot "automatikus gyorsításnak" nevezzük.

Azokban az esetekben, ahol a bérlő egy másik IdP-be van összeveteve a bejelentkezéshez, az automatikus gyorsítás egyszerűbbé teszi a felhasználó bejelentkezését.  Az egyes alkalmazásokhoz konfigurálhatja az automatikus gyorsítást.

>[!NOTE]
>Ha egy alkalmazást automatikus gyorsításra konfigurál, a vendégfelhasználók nem tudnak bejelentkezni. Ha egy felhasználó közvetlenül egy összevont idp hitelesítéshez, nincs mód számukra, hogy visszatérjen az Azure Active Directory bejelentkezési lapra. Vendég felhasználók, akik esetleg kell irányítani más bérlők vagy egy külső IdP, például egy Microsoft-fiók, nem tud bejelentkezni az alkalmazásba, mert ők kimaradnak a Home Realm Discovery lépés.  

Az összevont azonosító azonosító konstitúdisza automatikus gyorsításának kétféleképpen szabályozható:   

- Használjon tartományi emlékeztetőt egy alkalmazás hitelesítési kérelmeihez. 
- Konfiguráljon egy otthoni birodalom felderítési házirendet az automatikus gyorsítás engedélyezéséhez.

### <a name="domain-hints"></a>Tartományi tanácsok    
A tartományi tanácsok olyan irányelvek, amelyek egy alkalmazás hitelesítési kérelmében szerepelnek. Ezek segítségével felgyorsíthatja a felhasználó tetszésszerint idP bejelentkezési oldalra. Vagy egy több-bérlős alkalmazás használhatja őket, hogy gyorsítsa fel a felhasználót közvetlenül a márkajelzéssel ellátható Azure AD bejelentkezési oldalra a bérlő számára.  

Például az "largeapp.com" alkalmazás lehetővé teheti ügyfelei számára, hogy az alkalmazást egy "contoso.largeapp.com" egyéni URL-címen férjenek hozzá. Az alkalmazás tartalmazhat egy tartományi emlékeztetőt is, amely contoso.com a hitelesítési kérelemben. 

A tartományemlékeztető szintaxisa a használt protokolltól függ, és általában az alkalmazásban van konfigurálva.

**WS-Federation**: whr=contoso.com a lekérdezési karakterláncban.

**SAML**: Tartományemlékeztetőt vagy lekérdezési karakterláncot tartalmazó SAML-hitelesítési kérelem.

**Open ID Connect**: Lekérdezési karakterlánc domain_hint=contoso.com. 

Ha egy tartományi emlékeztető szerepel az alkalmazás hitelesítési kérelemben, és a bérlő össze van vonva az adott tartománnyal, az Azure AD megpróbálja átirányítani a bejelentkezést az adott tartományhoz konfigurált idp-be. 

Ha a tartomány tipp nem hivatkozik egy ellenőrzött összevont tartomány, figyelmen kívül hagyja, és a normál Home Realm Discovery meghívása.

Az Azure Active Directory által támogatott tartományi tippeket használó automatikus gyorsításról az [Enterprise Mobility + Security blogban](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/11/using-azure-ad-to-land-users-on-their-custom-login-page-from-within-your-app/)talál további információt.

>[!NOTE]
>Ha egy tartományi emlékeztető szerepel egy hitelesítési kérelemben, annak jelenléte felülbírálja az alkalmazáshoz a HRD-házirendben beállított automatikus gyorsítást.

### <a name="home-realm-discovery-policy-for-auto-acceleration"></a>Home Realm Discovery házirend automatikus gyorsításhoz
Egyes alkalmazások nem biztosítják az általuk kiküldött hitelesítési kérelem konfigurálását. Ezekben az esetekben nem lehet tartománytippeket használni az automatikus gyorsítás vezérléséhez. Az automatikus gyorsítás házirenden keresztül konfigurálható ugyanannak a viselkedésnek a elérése érdekében.  

## <a name="enable-direct-authentication-for-legacy-applications"></a>Közvetlen hitelesítés engedélyezése az örökölt alkalmazásokhoz
Ajánlott eljárás az alkalmazások használata AAD könyvtárak és interaktív bejelentkezés a felhasználók hitelesítéséhez. A könyvtárak gondoskodnak az összevont felhasználói folyamatokról.  Néha a régebbi alkalmazások nem íródnak az összevonás megértéséhez. Nem hajtanak végre otthoni birodalom felderítése, és nem lépnek kapcsolatba a megfelelő összevont végpont hitelesítéséhez a felhasználó. Ha úgy dönt, hogy, a HRD-szabályzat használatával engedélyezheti, hogy a felhasználónév/jelszó hitelesítő adatokat küldő, a felhasználónév/jelszó hitelesítő adatait küldő adott örökölt alkalmazások közvetlenül az Azure Active Directoryhasználatával hitelesítse magát. A jelszókivonat-szinkronizálást engedélyezni kell. 

> [!IMPORTANT]
> Csak akkor engedélyezze a közvetlen hitelesítést, ha be van kapcsolva a Jelszókivonat-szinkronizálás, és tudja, hogy rendben van az alkalmazás hitelesítése a helyszíni IdP által megvalósított szabályzatok nélkül. Ha bármilyen okból kikapcsolja a jelszókivonat-szinkronizálást, vagy bármilyen okból kikapcsolja a címtár-szinkronizálást az AD Connecttel, távolítsa el ezt a házirendet, hogy ne legyen lehetőség a közvetlen hitelesítésre elavult jelszókivonat használatával.

## <a name="set-hrd-policy"></a>HrD-házirend beállítása
A HRD-házirend beállításának három lépése van az összevont bejelentkezési automatikus gyorsításra vagy közvetlen felhőalapú alkalmazásokra vonatkozó alkalmazásokban:

1. Hrd-házirend létrehozása.

2. Keresse meg azt a egyszerű szolgáltatást, amelyhez csatolni szeretné a házirendet.

3. Csatolja a szabályzatot a szolgáltatásnévhez. 

A házirendek csak akkor lépnek érvénybe egy adott alkalmazáshoz, ha egy egyszerű szolgáltatáshoz vannak csatolva. 

Egyszerre csak egy HRD-házirend lehet aktív egy egyszerű szolgáltatáson.  

Az Azure Active Directory PowerShell-parancsmagokkal hozhat létre és kezelhet HRD-szabályzatot.

Az alábbiakban egy példa HRD politika meghatározása:
    
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

A házirend típusa "HomeRealmDiscoveryPolicy."

**AccelerateToFederatedDomain** nem kötelező. Ha **az AccelerateToFederatedDomain** hamis, a házirend nincs hatással az automatikus gyorsításra. Ha **az AccelerateToFederatedDomain** igaz, és csak egy ellenőrzött és összevont tartomány van a bérlőben, akkor a felhasználók közvetlenül az összevont idp-be kerülnek a bejelentkezéshez. Ha ez igaz, és a bérlőben egynél több ellenőrzött tartomány van, meg kell adni **a PreferredDomain** tartományt.

**A PreferredDomain** nem kötelező. **A PreferredDomain** tartománynak olyan tartományt kell jeleznie, amelyre fel kell gyorsítani. Elhagyható, ha a bérlő csak egy összevont tartománnyal rendelkezik.  Ha nincs megadva, és egynél több ellenőrzött összevont tartomány van, a házirendnek nincs hatása.

 Ha **a PreferredDomain** meg van adva, akkor meg kell egyeznie egy ellenőrzött, összevont tartománya a bérlő. Az alkalmazás minden felhasználójának képesnek kell lennie arra, hogy bejelentkezzen az adott tartományba.

**Az AllowCloudPasswordValidation** megadása nem kötelező. Ha **az AllowCloudPasswordValidation** igaz, akkor az alkalmazás hitelesítheti az összevont felhasználót azáltal, hogy a felhasználónév/jelszó hitelesítő adatait közvetlenül az Azure Active Directory tokenvégpontra mutatja be. Ez csak akkor működik, ha a Jelszókivonat-szinkronizálás engedélyezve van.

### <a name="priority-and-evaluation-of-hrd-policies"></a>A hrd-politikák prioritása és értékelése
A HRD-házirendek létrehozhatók, majd hozzárendelhetők adott szervezetekhez és szolgáltatástagokhoz. Ez azt jelenti, hogy egy adott alkalmazásra több szabályzat is alkalmazható. A hrd-szabályzat az alábbi szabályokat követi:


- Ha a hitelesítési kérelemben szerepel egy tartománytipp, akkor a rendszer figyelmen kívül hagyja az automatikus gyorsítást. A tartományemlékeztető által megadott viselkedést használja a tartománytipp.

- Ellenkező esetben, ha egy szabályzat explicit módon van hozzárendelve a szolgáltatásnévhez, a rendszer kényszeríti. 

- Ha nincs tartománytipp, és nincs explicit házirend a szolgáltatásnévhez rendelve, a rendszer a szolgáltatásnév szülőszervezetéhez explicit módon hozzárendelt házirendet érvényesít. 

- Ha nincs tartománytipp, és nincs házirend hozzárendelve a szolgáltatásnévhez vagy a szervezethez, a rendszer az alapértelmezett HRD-viselkedést használja.

## <a name="tutorial-for-setting-hrd-policy-on-an-application"></a>Oktatóanyag az alkalmazás HRD-házirendjének beállítására 
Az Azure AD PowerShell-parancsmagokkal végigvezetünk néhány forgatókönyvön, többek között a következőkben:


- HrD-házirend beállítása automatikus gyorsítás hoz egy alkalmazás egy bérlő egyetlen összevont tartományban.

- HrD-házirend beállítása az alkalmazás automatikus gyorsításának elvégzésére a bérlő számára ellenőrzött tartományok egyikére.

- HrD-házirend beállítása, amely lehetővé teszi, hogy egy örökölt alkalmazás közvetlen felhasználónév/jelszó hitelesítést hajtson létre az Azure Active Directoryban egy összevont felhasználó számára.

- Azoknak az alkalmazásoknak a felsorolása, amelyekhez a házirend konfigurálva van.


### <a name="prerequisites"></a>Előfeltételek
A következő példákban hozzon létre, frissítse, linkelje és törölje az Azure AD alkalmazásszolgáltatás-egyszerű szolgáltatásszabályzatait.

1.  Először töltse le a legújabb Azure AD PowerShell-parancsmag előnézetét. 

2.  Miután letöltötte az Azure AD PowerShell-parancsmagokat, futtassa a Connect parancsot az Azure AD-be való bejelentkezéshez a rendszergazdai fiókkal:

    ``` powershell
    Connect-AzureAD -Confirm
    ```
3.  A szervezet összes házirendjének megtekintéséhez futtassa a következő parancsot:

    ``` powershell
    Get-AzureADPolicy
    ```

Ha semmit sem ad vissza, az azt jelenti, hogy nincs házirend ek a bérlőben.

### <a name="example-set-hrd-policy-for-an-application"></a>Példa: HrD-házirend beállítása egy alkalmazáshoz 

Ebben a példában olyan házirendet hoz létre, amely, ha egy alkalmazáshoz van rendelve: 
- Automatikusan felgyorsítja a felhasználókat egy AD FS bejelentkezési képernyőre, amikor bejelentkeznek egy alkalmazásba, ha egyetlen tartomány van a bérlőben. 
- Az automatikus anameddig csak egy AD FS bejelentkezési képernyőre gyorsítja a felhasználókat, és a bérlőben egynél több összevont tartomány található.
- Engedélyezi a nem interaktív felhasználónév/jelszó bejelentkezést közvetlenül az Azure Active Directoryba az összevont felhasználók számára azon alkalmazások hoz, amelyekhez a házirend hozzá van rendelve.

#### <a name="step-1-create-an-hrd-policy"></a>1. lépés: HrD-házirend létrehozása

A következő házirend automatikusan felgyorsítja a felhasználókat egy AD FS bejelentkezési képernyőre, amikor bejelentkeznek egy alkalmazásba, ha egyetlen tartomány van a bérlőben.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```
A következő házirend automatikusan felgyorsítja a felhasználókat egy AD FS bejelentkezési képernyőre, amely a bérlőben egynél több összevont tartomány található. Ha egynél több összevont tartománnyal rendelkezik, amely hitelesíti a felhasználókat az alkalmazásokszámára, meg kell adnia az automatikusan gyorsítani kívánt tartományt.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true, `"PreferredDomain`":`"federated.example.edu`"}}") -DisplayName MultiDomainAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Ha olyan házirendet szeretne létrehozni, amely engedélyezi az összevont felhasználók felhasználónév-/jelszó-hitelesítését közvetlenül az Azure Active Directoryval az Azure Active Directoryval adott alkalmazásokhoz, futtassa a következő parancsot:

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AllowCloudPasswordValidation`":true}}") -DisplayName EnableDirectAuthPolicy -Type HomeRealmDiscoveryPolicy
```


Az új házirend megtekintéséhez és **az Objektumazonosító**bekéséhez futtassa a következő parancsot:

``` powershell
Get-AzureADPolicy
```


A HRD-házirend alkalmazásához a létrehozása után, hozzárendelheti azt több alkalmazás egyszerű alkalmazás.

#### <a name="step-2-locate-the-service-principal-to-which-to-assign-the-policy"></a>2. lépés: Keresse meg azt a egyszerű szolgáltatást, amelyhez hozzá kívánja rendelni a házirendet  
Szüksége van annak a szolgáltatásnévitagnak az **Objektumazonosítójára,** amelyhez a házirendet hozzá kívánja rendelni. A szolgáltatásnévi tagok **objectid-jának** megkeresésére többféleképpen is kereshető.    

Használhatja a portált, vagy lekérdezheti a [Microsoft Graph](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta). A Graph Explorer [eszközre](https://developer.microsoft.com/graph/graph-explorer) is megléphet, és bejelentkezhet az Azure AD-fiókjába a szervezet összes szolgáltatásnévi szolgáltatásának megtekintéséhez. 

A PowerShell használata miatt a következő parancsmag használatával listázhatja a szolgáltatástagokat és azok azonosítóit.

``` powershell
Get-AzureADServicePrincipal
```

#### <a name="step-3-assign-the-policy-to-your-service-principal"></a>3. lépés: A szabályzat hozzárendelése a szolgáltatásnévhez  
Miután rendelkezik annak az alkalmazásnak az **objektumazonosítójával,** amelyhez konfigurálni szeretné az automatikus gyorsítást, futtassa a következő parancsot. Ez a parancs az 1.

``` powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

Ezt a parancsot megismételheti minden olyan egyszerű szolgáltatásesetében, amelyhez hozzá szeretné adni a házirendet.

Abban az esetben, ha egy alkalmazás már rendelkezik egy HomeRealmDiscovery házirendrendelt, nem lesz képes felvenni egy második.  Ebben az esetben módosítsa az alkalmazáshoz rendelt, az alkalmazáshoz rendelt főingatlan-felderítési házirend definícióját további paraméterek hozzáadásához.

#### <a name="step-4-check-which-application-service-principals-your-hrd-policy-is-assigned-to"></a>4. lépés: Ellenőrizze, hogy a HRD-házirend mely alkalmazásszolgáltatás-felelősökhöz van hozzárendelve
Annak ellenőrzéséhez, hogy mely alkalmazások hrd-házirendje van konfigurálva, használja a **Get-AzureADPolicyAppliedObject** parancsmag. Adja át annak a házirendnek az **ObjectID azonosítóját,** amelyről ellenőrizni szeretné.

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```
#### <a name="step-5-youre-done"></a>5. lépés: Kész!
Próbálja ki az alkalmazást, és ellenőrizze, hogy az új házirend működik-e.

### <a name="example-list-the-applications-for-which-hrd-policy-is-configured"></a>Példa: Azoknak az alkalmazásoknak a listázása, amelyekhez a HRD-házirend konfigurálva van

#### <a name="step-1-list-all-policies-that-were-created-in-your-organization"></a>1. lépés: A szervezetben létrehozott összes házirend listázása 

``` powershell
Get-AzureADPolicy
```

Jegyezze fel annak a házirendnek az **Objektumazonosítóját,** amelyhez hozzárendeléseket szeretne felsorolni.

#### <a name="step-2-list-the-service-principals-to-which-the-policy-is-assigned"></a>2. lépés: A házirendhez rendelt szolgáltatásnévi tagok felsorolása  

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

### <a name="example-remove-an-hrd-policy-for-an-application"></a>Példa: Egy alkalmazás HRD-házirendjének eltávolítása
#### <a name="step-1-get-the-objectid"></a>1. lépés: Az objektumazonosító beszereznie
Az előző példában lejuthat a házirend, és az alkalmazás egyszerű, amelyből el szeretné távolítani az **objektumazonosítóját.** 

#### <a name="step-2-remove-the-policy-assignment-from-the-application-service-principal"></a>2. lépés: A házirend-hozzárendelés eltávolítása az alkalmazás egyszerű alkalmazásból  

``` powershell
Remove-AzureADServicePrincipalPolicy -id <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

#### <a name="step-3-check-removal-by-listing-the-service-principals-to-which-the-policy-is-assigned"></a>3. lépés: Az eltávolítás ellenőrzése azoknak a szolgáltatástagoknak a felsorolásával, amelyekhez a házirend hozzá van rendelve 

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```
## <a name="next-steps"></a>További lépések
- A hitelesítés Azure AD-ben való működéséről az [Azure AD hitelesítési forgatókönyvei](../develop/authentication-scenarios.md)című témakörben talál további információt.
- A felhasználó egyszeri bejelentkezéséről az [Azure Active Directoryban lévő alkalmazásokra való egyszeri bejelentkezés](what-is-single-sign-on.md)című témakörben talál további információt.
- Látogasson el a [Microsoft identitásplatformjára,](../develop/v2-overview.md) ahol áttekintést kaphat a fejlesztővel kapcsolatos összes tartalomról.
