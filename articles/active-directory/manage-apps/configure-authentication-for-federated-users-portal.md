---
title: Jelentkezzen be az automatikus-gyorsítás egy alkalmazáshoz egy Kezdőtartomány-felderítés csoportházirend használatával konfigurálja |} A Microsoft Docs
description: Ismerteti az Azure AD-bérlő fogalmát, és kezelése az Azure, Azure Active Directoryn keresztül.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/08/2018
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: 333258ef9696e6dbe4aab5b10e815bb84428d425
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56190262"
---
# <a name="configure-azure-active-directory-sign-in-behavior-for-an-application-by-using-a-home-realm-discovery-policy"></a>Azure Active Directory-bejelentkezés konfigurálása az alkalmazás viselkedésében egy Kezdőtartomány-felderítés házirend segítségével

A következő dokumentum bevezetést nyújt az Azure Active Directory hitelesítési viselkedést az összevont felhasználók konfigurálását.   Ez fedezi a konfiguráció automatikus és a hitelesítési korlátozások az összevont tartományokban lévő felhasználók számára.

## <a name="home-realm-discovery"></a>Kezdőtartomány felderítése
Otthoni Kezdőtartomány felderítése (HRD) a rendszer, amely lehetővé teszi, hogy az Azure Active Directory (Azure AD) határozza meg, ahol a felhasználó hitelesítenie kell a bejelentkezési időpontban.  Amikor egy felhasználó bejelentkezik az Azure AD-bérlő erőforrások eléréséhez, vagy az Azure AD közös bejelentkezési oldalát, akkor írja be a felhasználónevet (UPN). Az Azure AD használatával, amely Fedezze fel, ahol a felhasználónak kell bejelentkezni. 

A felhasználó hitelesítését az alábbi helyek egyikét kell venni lehet szükség:

- (Előfordulhat, és, hogy a felhasználó megpróbál hozzáférni az erőforrásnak ugyanabban a bérlőben) a felhasználó a saját bérlőjén. 

- A Microsoft-fiók.  A felhasználó nem a Vendég az erőforrás-bérlőben.

-  Egy a helyszíni identitásszolgáltató például az Active Directory összevonási szolgáltatások (AD FS).

- Össze van vonva az Azure AD-bérlő egy másik identitásszolgáltatót.

## <a name="auto-acceleration"></a>Auto-acceleration 
Egyes szervezetekben, például az AD FS használata felhasználói hitelesítéshez egy másik identitásszolgáltató összevonni kívánt Azure Active Directory bérlőben tartományok konfigurálása.  

Amikor egy felhasználó bejelentkezik, egy alkalmazásba, akkor először megnyílik egy Azure AD bejelentkezési oldalára. Egyszerű, ha összevont tartományt a beírt után kerül az identitásszolgáltató bejelentkezési oldalára szolgálja ki, hogy a tartományhoz. Bizonyos körülmények között a rendszergazdák érdemes közvetlen felhasználók számára a bejelentkezési oldalon, ha, amelybe bejelentkezik bizonyos alkalmazásokat. 

Ennek eredményeképpen a felhasználók Azure Active Directory kezdőlap kihagyhatja. Ez a folyamat a neve "bejelentkezés automatikus-gyorsítás."

Azokban az esetekben, ahol össze van vonva a bérlő egy másik identitásszolgáltatót a bejelentkezéshez, automatikus teszi felhasználói bejelentkezési még hatékonyabbá.  Konfigurálhatja az egyes alkalmazások automatikus gyorsítását.

>[!NOTE]
>Ha egy alkalmazás automatikus konfigurál, vendég felhasználók nem jelentkezhetnek be. Ha a felhasználó közvetlenül a egy összevont identitásszolgáltató a hitelesítéshez, nincs semmilyen módon nem lehet, hogy a Azure Active Directory-Bejelentkezés lapra való visszatéréshez. Vendég felhasználók, akik előfordulhat, hogy a rendszer más bérlők vagy egy külső identitásszolgáltató, például a Microsoft-fiók szükséges, nem jelentkezhet be, amelyek mivel azokat már a rendszer kihagyja a Kezdőtartomány-felderítés lépés.  

Automatikus-gyorsítás, egy összevont IdP vezérléséhez két módja van:   

- Hitelesítési kérelmek tartományemlékeztetőt használja az alkalmazáshoz. 
- Engedélyezze az automatikus Kezdőtartomány-felderítés házirend beállításával.

### <a name="domain-hints"></a>Tartomány mutatók    
Tartomány mutatók olyan irányelvek, amelyek szerepelnek a hitelesítési kérelmet egy alkalmazásból. Gyorsítsa fel a felhasználó bejelentkezési oldalra összevont IdP használható. Ezek segítségével egy több-bérlős alkalmazás a felhasználó gyorsabban rögtön az a vállalati arculattal vagy a bérlő Azure AD bejelentkezési oldala.  

Például a "largeapp.com" alkalmazás lehetővé teheti azok az ügyfelek számára az alkalmazás egy egyéni URL-cím "contoso.largeapp.com." Az alkalmazás a hitelesítési kérelem is tartalmazhat egy a contoso.com tartomány érhető el. 

Tartomány mutató szintaxis használt protokoll függően változik, és általában az alkalmazásban van konfigurálva.

**WS-Federation**: whr=contoso.com a lekérdezési karakterláncban.

**SAML**:  Vagy egy SAML hitelesítési kérelmet, amely tartományemlékeztetőt vagy egy lekérdezési karakterlánc whr=contoso.com tartalmaz.

**Open ID Connect**: A lekérdezési karakterlánc domain_hint=contoso.com. 

Ha tartományemlékeztetőt szerepel a hitelesítési kérelmet az alkalmazásból, és a bérlő össze van vonva az adott tartománnyal, az Azure AD megpróbálja, amely ahhoz a tartományhoz van konfigurálva az identitásszolgáltató bejelentkezési átirányítása. 

Ha a tartomány mutató ellenőrzött összevont tartományt nem hivatkozik, figyelmen kívül, és a normál Kezdőtartomány-felderítés meghívásainak.

Gyorsítás a tartomány mutatók, amelyek támogatottak az Azure Active Directory használatával kapcsolatos további információkért lásd: a [Enterprise Mobility + Security blog](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/11/using-azure-ad-to-land-users-on-their-custom-login-page-from-within-your-app/).

>[!NOTE]
>Tartományemlékeztetőt hitelesítési kérelmet tartalmaz, a jelenléte felülírja az alkalmazás HRD-szabályzattal be automatikus gyorsítás.

### <a name="home-realm-discovery-policy-for-auto-acceleration"></a>Az automatikus otthoni tartomány Alkalmazásdetektálási szabályzat
Bizonyos alkalmazások nem rendelkeznek a hitelesítési kérelem általuk konfigurálhat. Ezekben az esetekben nincs lehetőség a tartomány mutatók segítségével szabályozza a automatikus. Automatikus szabályzatot, hogy ugyanez a viselkedés keresztül konfigurálhatók.  

## <a name="enable-direct-authentication-for-legacy-applications"></a>Örökölt alkalmazások számára a közvetlen hitelesítés engedélyezése
Ajánlott eljárás, az alkalmazások AAD-kódtárak és interaktív bejelentkezés segítségével hitelesítheti a felhasználókat. Az összevont felhasználói folyamatok gondoskodik a kódtárakat.  Néha örökölt alkalmazások nem szerepel a összevonási ismertetése. Ezeket ne hajtsa végre a hitelesítőtartomány feltárásához, és nem kommunikál a megfelelő összevont végpontot, hogy a felhasználó hitelesítéséhez. Ha kívánja, használhatja a HRD-szabályzattal konkrét örökölt alkalmazások, amelyek közvetlenül az Azure Active Directory-hitelesítést a felhasználónév/jelszó hitelesítő adatokat küldenek. Jelszókivonat-szinkronizálás engedélyezve kell lennie. 

> [!IMPORTANT]
> Közvetlen hitelesítés csak akkor engedélyezze, ha a Jelszókivonat-szinkronizálás engedélyezve van, és tudja, hogy szabadon beágyazhatók-e a helyszíni identitásszolgáltatók által megvalósított szabályzatok használata nélkül végezzen hitelesítést az alkalmazás. Ha kikapcsolja a Jelszókivonat-szinkronizálás, vagy kapcsolja ki az AD Connect címtár-szinkronizálás bármilyen okból, távolítsa el ezt a házirendet annak megakadályozására, hogy közvetlen hitelesítése Jelszókivonat elavult.

## <a name="set-hrd-policy"></a>Set HRD-szabályzattal
Összevont bejelentkezés automatikus – gyorsított alkalmazás vagy a felhőalapú alkalmazások közvetlen beállítás HRD-szabályzattal három lépésből áll:

1. Hozzon létre egy HRD-szabályzattal.

2. Keresse meg a szolgáltatásnév, amelyhez csatolni a szabályzatot.

3. A szabályzat csatolása az egyszerű szolgáltatás. 

Szabályzatok csak akkor lépnek hatályba egy adott alkalmazáshoz, ha azok egy egyszerű szolgáltatás társítva. 

Egyszerre csak egy HRD-szabályzattal lehet aktív egy egyszerű szolgáltatást a.  

A Microsoft Azure Active Directory Graph API közvetlenül, vagy az Azure Active Directory PowerShell-parancsmagok segítségével HRD-szabályzat létrehozása és kezelése.

A Graph API-t, amely kezeli a házirend leírását a [házirend műveletek](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) cikket az MSDN Webhelyén.

Következő egy példa HRD szabályzatdefiníció:
    
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

A házirend-típus: "HomeRealmDiscoveryPolicy."

**AccelerateToFederatedDomain** nem kötelező. Ha **AccelerateToFederatedDomain** false (hamis), a házirend nem befolyásolja az automatikus. Ha **AccelerateToFederatedDomain** igaz, és ott, csak az egyik ellenőrzése és a bérlő, akkor a felhasználóknak az összevont tartományt megnyílik egyenes az összevont identitásszolgáltató bejelentkezési. Ha igaz, és nincs több ellenőrzött tartomány a bérlőben **PreferredDomain** meg kell adni.

**PreferredDomain** nem kötelező. **PreferredDomain** jelzi, hogy egy tartomány, amelyhez felgyorsításához. Ha a bérlő csak egy összevont tartomány van elhagyható.  Ha ezt kihagyja, és több ellenőrzött összevont tartomány van, a házirend nem lesz hatása.

 Ha **PreferredDomain** van megadva, meg kell egyeznie a bérlő ellenőrzött, összevont tartományhoz. Az alkalmazás minden felhasználó jelentkezhet be ebbe a tartományba kell lennie.

**AllowCloudPasswordValidation** nem kötelező. Ha **AllowCloudPasswordValidation** értéke igaz, majd az alkalmazás engedélyezett felhasználóneves és jelszavas hitelesítő adatokat közvetlenül az Azure Active Directory jogkivonat-végpont szabályzatkérelem összevont felhasználó hitelesítéséhez. Ez csak működik, ha a Jelszókivonat-szinkronizálás engedélyezve van.

### <a name="priority-and-evaluation-of-hrd-policies"></a>Prioritás és HRD-házirendek kiértékelése
HRD-házirendeket lehet létrehozni és majd rendelt meghatározott szervezetek, és az egyszerű szolgáltatások. Ez azt jelenti, hogy a alkalmazni egy adott alkalmazást több szabályzat. A következő ezeket a szabályokat a HRD-szabályzattal, amely akkor lép érvénybe:


- Tartományemlékeztetőt szerepel a hitelesítési kérést, ha bármely HRD-szabályzattal figyelmen kívül hagyja az automatikus. A tartomány mutató által meghatározott viselkedés szolgál.

- Ellenkező esetben a házirend explicit módon hozzá van rendelve, az egyszerű szolgáltatás, ha van kényszerítve. 

- Ha nincs tartományemlékeztető van, és nincs házirend explicit módon hozzá van rendelve az egyszerű szolgáltatás, egy egyszerű szolgáltatás a szülő szervezeti explicit módon hozzárendelt lép életbe. 

- Ha nincs tartományemlékeztető van, és nem a szabályzat az egyszerű szolgáltatás vagy a szervezet lett hozzárendelve, az alapértelmezett HRD viselkedés szolgál.

## <a name="tutorial-for-setting-hrd-policy-on-an-application"></a>Oktatóanyag az alkalmazás HRD-szabályzattal beállítása 
Az Azure AD PowerShell-parancsmagok segítségével néhány esetben, beleértve a végig:


- Beállítása HRD-szabályzattal, egy bérlő egyetlen összevont tartományt az alkalmazás automatikus gyorsítását tennie.

- Egy alkalmazás több ellenőrzött tartomány egyik automatikus gyorsítását ehhez a bérlőhöz tartozó beállítása HRD-szabályzattal.

- Ahhoz, hogy az örökölt alkalmazások közvetlen felhasználóneves és jelszavas hitelesítéssel az Azure Active Directory összevont felhasználó beállítása HRD-szabályzattal.

- Listázás az alkalmazásokat, amelyekre a szabályzat van konfigurálva.


### <a name="prerequisites"></a>Előfeltételek
A következő példákban, létrehozása, frissítése, hivatkozás és szabályzatokat az alkalmazás szolgáltatásnevek törlése az Azure ad-ben.

1.  Első lépésként töltse le a legújabb Azure AD PowerShell-parancsmag előzetes verziót. 

2.  Miután letöltötte az Azure AD PowerShell-parancsmagokat, futtassa a jelentkezzen be rendszergazdai fiókkal az Azure AD Connect parancsot:

    ``` powershell
    Connect-AzureAD -Confirm
    ```
3.  Futtassa a következő parancsot a szervezet összes házirend megtekintéséhez:

    ``` powershell
    Get-AzureADPolicy
    ```

Ha nem ad vissza semmit, az azt jelenti, nincsenek házirendek létrehozása a bérlőben.

### <a name="example-set-hrd-policy-for-an-application"></a>Példa: HRD-szabályzattal, egy alkalmazás beállítása 

Ebben a példában létrehozhat egy olyan szabályzatot, amely hozzá van rendelve egy alkalmazás vagy ha: 
- Automatikus – gyorsítja felhasználók egy AD FS bejelentkezési képernyő, ha azok bejelentkezés egy alkalmazás esetén egyetlen tartomány a bérlőben. 
- Felhasználók automatikus felgyorsítja az AD FS bejelentkezési képernyő van az egynél több összevont tartomány a bérlőben.
- Lehetővé teszi, hogy közvetlenül az Azure Active Directoryhoz az összevont felhasználók esetében a szabályzat hozzá van rendelve az alkalmazások nem interaktív felhasználóneves és jelszavas bejelentkezést.

#### <a name="step-1-create-an-hrd-policy"></a>1. lépés: Hozzon létre egy HRD-szabályzattal

A következő szabályzatot automatikus – gyorsítja felhasználók egy AD FS bejelentkezési képernyő, ha azok bejelentkezés egy alkalmazás esetén egyetlen tartomány a bérlőben.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```
A következő szabályzatot automatikus – gyorsítja felhasználók, az AD FS bejelentkezési képernyő van több, mint egy összevont tartomány a bérlőben. Ha egynél több összevont tartományt, amely hitelesíti a felhasználókat az alkalmazások, a tartomány automatikus felgyorsítása kell adnia.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true, `"PreferredDomain`":`"federated.example.edu`"}}") -DisplayName MultiDomainAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Ahhoz, hogy felhasználóneves és jelszavas hitelesítéssel összevont felhasználók esetében közvetlenül az Azure Active Directoryval adott alkalmazásokra vonatkozó házirend létrehozásához futtassa a következő parancsot:

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AllowCloudPasswordValidation`":true}}") -DisplayName EnableDirectAuthPolicy -Type HomeRealmDiscoveryPolicy
```


Az új házirend megtekintéséhez és annak **ObjectID**, futtassa a következő parancsot:

``` powershell
Get-AzureADPolicy
```


A alkalmazni a HRD-szabályzattal, már létrehozott, hozzárendelheti a több alkalmazások szolgáltatásnevének.

#### <a name="step-2-locate-the-service-principal-to-which-to-assign-the-policy"></a>2. lépés: Keresse meg a szolgáltatásnév, amelyhez a szabályzat hozzárendelése  
Van szüksége a **ObjectID** , amelyhez hozzárendeli a szabályzatot szeretné egyszerű szolgáltatást lekéri. Többféleképpen is található a **ObjectID** szolgáltatásnevek.    

A portálon, vagy lekérdezheti [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity). Azt is választhatja a [Graph Explorer eszköz](https://developer.microsoft.com/graph/graph-explorer) és a szervezet szolgáltatásnevek megtekintéséhez jelentkezzen be az Azure AD-fiókot. Mivel a Powershellt használ, használhatja a get-azureadserviceprincipal parancsmagot parancsmag az egyszerű szolgáltatásnevekről és a hozzájuk tartozó azonosítóik listáját.

#### <a name="step-3-assign-the-policy-to-your-service-principal"></a>3. lépés: A szabályzat az egyszerű szolgáltatás hozzárendelése  
Miután a **ObjectID** a szolgáltatásnév az alkalmazás legyen automatikus konfigurálásához, futtassa a következő parancsot. Ez a parancs a HRD-szabályzattal, amely a 2. lépésben megkeresett egyszerű szolgáltatást az 1. lépésben létrehozott társítja.

``` powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

Minden egyes, amelyhez hozzá szeretné a szabályzat hozzáadása szolgáltatásnévhez tartozó megismételheti ezt a parancsot.

Abban az esetben, ha egy alkalmazás már rendelkezik-e hozzárendelve egy HomeRealmDiscovery szabályzat akkor adhat hozzá egy másikat.  Ebben az esetben módosítsa a definíció a Kezdőtartomány-felderítés házirend, amely további paraméterek hozzáadása az alkalmazáshoz van hozzárendelve.

#### <a name="step-4-check-which-application-service-principals-your-hrd-policy-is-assigned-to"></a>4. lépés: Ellenőrizze, hogy mely alkalmazások szolgáltatásnevének HRD-szabályzattal hozzá van rendelve
Ellenőrizze, hogy mely alkalmazások konfigurálása HRD-szabályzattal rendelkezik, használja a **Get-AzureADPolicyAppliedObject** parancsmagot. Adja át azt a **ObjectID** ellenőrizze a kívánt szabályzat.

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
#### <a name="step-5-youre-done"></a>5. lépés: Kész!
Annak ellenőrzéséhez, hogy működik-e az új szabályzat az alkalmazás kipróbálásához.

### <a name="example-list-the-applications-for-which-hrd-policy-is-configured"></a>Példa: Melyik HRD szabályzat van konfigurálva az alkalmazások listáját

#### <a name="step-1-list-all-policies-that-were-created-in-your-organization"></a>1. lépés: A szervezetben létrehozott összes szabályzat listázása 

``` powershell
Get-AzureADPolicy
```

Megjegyzés: a **ObjectID** lista hozzárendeléseinek kívánt szabályzat.

#### <a name="step-2-list-the-service-principals-to-which-the-policy-is-assigned"></a>2. lépés: A szolgáltatásnevek, amelyhez hozzá van rendelve a szabályzat listázása  

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```

### <a name="example-remove-an-hrd-policy-for-an-application"></a>Példa: Egy HRD-szabályzattal, egy alkalmazás eltávolítása
#### <a name="step-1-get-the-objectid"></a>1. lépés: Az ObjectID beolvasása
Az előző példa használatával a **ObjectID** , a szabályzatot, és hogy az alkalmazás egyszerű szolgáltatás, amelyről távolítsa el a kívánt. 

#### <a name="step-2-remove-the-policy-assignment-from-the-application-service-principal"></a>2. lépés: A szabályzat-hozzárendelés eltávolítása az alkalmazás egyszerű szolgáltatás  

``` powershell
Remove-AzureADApplicationPolicy -ObjectId <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

#### <a name="step-3-check-removal-by-listing-the-service-principals-to-which-the-policy-is-assigned"></a>3. lépés: Ellenőrizze az eltávolító, amely a szabályzat hozzá van rendelve a szolgáltatásnevek listázásával 

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
## <a name="next-steps"></a>További lépések
- Az Azure AD hitelesítési működésével kapcsolatos további információkért lásd: [hitelesítési forgatókönyvek az Azure ad-ben](../develop/authentication-scenarios.md).
- Felhasználói egyszeri bejelentkezéssel kapcsolatos további információkért lásd: [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](configure-single-sign-on-portal.md).
- Látogasson el a [Active Directory fejlesztői útmutatója](../develop/v1-overview.md) áttekintheti az összes fejlesztői kapcsolódó tartalmat.
