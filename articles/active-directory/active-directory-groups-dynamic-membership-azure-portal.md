---
title: "Attribútumalapú dinamikus csoporttagság az Azure Active Directoryban |} Microsoft Docs"
description: "Dinamikus csoport tagsági többek között a speciális szabályok létrehozása támogatott szabályoperátorokat kifejezés és paraméterek."
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: fb434cc2-9a91-4ebf-9753-dd81e289787e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 12/06/2017
ms.author: curtand
ms.reviewer: piotrci
ms.custom: H1Hack27Feb2017;it-pro
ms.openlocfilehash: 8a52d80f32f822691be862d566c17c84efc73c26
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="create-attribute-based-rules-for-dynamic-group-membership-in-azure-active-directory"></a>Dinamikus csoporttagság Attribútumalapú szabályok létrehozása az Azure Active Directoryban
Az Azure Active Directory (Azure AD) összetett Attribútumalapú dinamikus csoporttagságok csoportok engedélyezése speciális szabályokat hozhat létre. Ez a cikk részletezi az attribútumokat és a felhasználók vagy eszközök dinamikus tagsági szabályok létrehozásához szintaxist.

Ha módosítja egy felhasználó vagy eszköz attribútuma, a rendszer kiértékeli az összes dinamikus csoport szabályokat annak ellenőrzéséhez, hogy a módosítás kiváltották bármely csoport hozzáadása vagy eltávolítása a könyvtárban található. Ha egy felhasználó vagy az eszköz megfelel a csoporton szabály, azokat hozzá szeretné adni a csoport tagjai. A szabály már nem megfelelnek eltávolítja.

> [!NOTE]
> Biztonsági vagy Office 365-csoportok esetében dinamikustagság-szabály beállítására is lehetőség van.
>
> Ez a szolgáltatás legalább egy dinamikus csoportba felvett felhasználói tagjaihoz tartozó Azure AD Premium P1-licencre van szükség. Nem kötelező ténylegesen licencek hozzárendelése dinamikus csoportok tagjainak kell azokat a felhasználókat, de kell rendelkeznie a licencek minimális számát, amelyek az ilyen felhasználók bérlő. Példa: Ha 1000 egyedi felhasználók teljes minden dinamikus csoport rendelkezik, ahol az Ön bérlőjében, szeretné-e legalább 1000 licenccel rendelkezik az Azure AD Premium P1, vagy a fenti licencszerződés követelménynek.
>
> Létrehozhat egy dinamikus csoportot eszközök vagy felhasználók számára, de nem hozható létre egy szabályt, amely a felhasználó és eszköz objektumokat is tartalmaz.
> 
> A jelenleg nincs lehetőség a tulajdonos felhasználói attribútumok alapján eszköz csoport létrehozásához. Eszköz tagsági szabályok csak az eszköz a címtárban található objektumokhoz azonnali attribútumok hivatkozhat.
> 
> Microsoft Teams egyelőre nem támogatják a dinamikus csoporttagság. A hiba a "Nem tudja áttelepíteni a dinamikus tagságot csoport" társított naplófájlokban ellenőrizheti

## <a name="to-create-an-advanced-rule"></a>Speciális szabály létrehozása
1. Jelentkezzen be a [az Azure AD felügyeleti központban](https://aad.portal.azure.com) egy olyan fiókkal, amely egy globális rendszergazda vagy egy felhasználói fiók rendszergazdájához.
2. Válassza ki **felhasználók és csoportok**.
3. Válassza ki **összes csoport**, és válassza ki **új csoport**.

   ![Új csoport hozzáadása](./media/active-directory-groups-dynamic-membership-azure-portal/new-group-creation.png)

4. Az a **csoport** panelen adjon nevet és leírást az új csoporthoz. Válassza ki a **tagságtípusának** a következők **dinamikus felhasználói** vagy **dinamikus eszköz**, attól függően, hogy szeretné-e szabályok létrehozása a felhasználók vagy eszközök számára, és adja **Hozzáadás dinamikus lekérdezés**. A szabály jelentéskészítő használni egy egyszerű szabály létrehozásához, vagy saját kezűleg speciális szabály írása. A cikkben olvashat a felhasználó-eszköz attribútumot, valamint a speciális szabályok példái.

   ![Dinamikus tagsági szabály hozzáadása](./media/active-directory-groups-dynamic-membership-azure-portal/add-dynamic-group-rule.png)

5. Válassza ki a szabály létrehozása után **Hozzáadás lekérdezés** a panel alján.
6. Válassza ki **létrehozása** a a **csoport** panelt, és a csoport létrehozásához.

> [!TIP]
> Csoport létrehozása sikertelen lehet, ha a megadott speciális szabályt helytelen volt. Egy értesítés jelenik meg a jobb felső sarokban, a portál; Miért érdemes a szabályt nem fogadta el a rendszer magyarázatát tartalmazza. Olvassa el, hogy alaposan megérteni, hogyan kell úgy, hogy a szabály abba, hogy érvényes.

## <a name="constructing-the-body-of-an-advanced-rule"></a>A szervezet egy speciális szabály létrehozása
A speciális szabályt, a dinamikus tagságot csoportokat hozhat létre az lényegében egy bináris kifejezés, amely három részből áll, és IGAZ vagy hamis eredménye eredményez. A három részt a következők:

* Bal oldali paraméter
* Bináris operátor
* Jobb oldali állandó

A teljes speciális szabály néz: (leftParameter binaryOperator "RightConstant"), ahol a nyitó és záró zárójel kötelező megadni a teljes bináris kifejezésből, dupla idézőjelek között nem kötelező, csak a megfelelő állandó kötelező, ha a karakterlánc, és a bal oldali paraméter szintaxisa a következő user.property. Speciális szabály állhat egynél több bináris kifejezések vesszővel elválasztva a - és, - vagy, és - nem logikai operátor.

A következő példák a megfelelően felépített speciális szabályt:
```
(user.department -eq "Sales") -or (user.department -eq "Marketing")
(user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")
```
Támogatott paraméterek és kifejezés szabályoperátorokat teljes listájáért tekintse meg az alábbi szakaszok. Az eszköz szabályok használt attribútumok, lásd: [attribútumok használata eszközobjektumok szabályok létrehozása](#using-attributes-to-create-rules-for-device-objects).

A speciális szabály törzsét teljes hossza legfeljebb 2048 karakter hosszú lehet.

> [!NOTE]
> Karakterlánc és regex műveletek még nem kis-és nagybetűket. Is végrehajtható, Null-ellenőrzések *null* konstansként, például felhasználó.részleg - eq *null*.
> Idézőjeleket tartalmazó karakterláncok "használatával kell megjelölni" karakter, például felhasználó.részleg - eq \`"Értékesítési".

## <a name="supported-expression-rule-operators"></a>Támogatott kifejezés szabály operátorok
Az alábbi táblázat a támogatott kifejezés szabályoperátorokat és szintaxisát a speciális szabály törzsét használhatók:

| Operátor | Szintaxis |
| --- | --- |
| Nem egyenlő |-ne |
| Egyenlő |-eq |
| Nem kezdődik |-notStartsWith |
| Kezdődik |-startswith elemnek |
| Nem tartalmazza |-notContains |
| Contains |-tartalmaz |
| Nem egyeznek |-notMatch |
| Egyezés |-felel meg |
| A | -a |
| Nem található | -notIn |

## <a name="operator-precedence"></a>Precedencia

Minden operátorok részesíti a alacsonyabb magasabb / alább láthatók. Ugyanabban a sorban operátorok egyenlő sorrend szerepelnek:
````
-any -all
-or
-and
-not
-eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch -in -notIn
````
Minden műveleteivel végrehajtható vagy a kötőjel előtag nélkül. Kerek zárójeleket tartalmazhatnak van szükség, csak akkor, amikor sorrendje nem felel meg a követelményeknek.
Példa:
```
   user.department –eq "Marketing" –and user.country –eq "US"
```
az egyenértékű:
```
   (user.department –eq "Marketing") –and (user.country –eq "US")
```
## <a name="using-the--in-and--notin-operators"></a>Használja az - a és - notIn operátorok

Ha számos különböző értéket elleni felhasználói attribútum értékének összehasonlítandó használhatja az - a vagy - notIn operátorokat. Példa használja a - operátorban:
```
    user.department -In [ "50001", "50002", "50003", “50005”, “50006”, “50007”, “50008”, “50016”, “50020”, “50024”, “50038”, “50039”, “51100” ]
```
Vegye figyelembe a használatát a "[" és "]" elején és értékek listájának végét. Ez a feltétel eredménye IGAZ érték felhasználó.részleg egyenlő a listában szereplő értékek közül.


## <a name="query-error-remediation"></a>Lekérdezési hiba szervizelés
A következő táblázat felsorolja a gyakran előforduló hiba és azok megoldására

| Lekérdezés-elemzési hiba | Hiba kihasználtsága | Javított kihasználtsága |
| --- | --- | --- |
| Hiba történt: Az attribútum nem támogatott. |(user.invalidProperty -eq "Value") |(felhasználó.részleg - eq "érték")<br/><br/>Győződjön meg arról, hogy az attribútum szerepel a [tulajdonságainak listája támogatott](#supported-properties). |
| Hiba: Operátor nem támogatott az attribútum. |(user.accountEnabled-igaz tartalmazza) |(user.accountEnabled - eq igaz)<br/><br/>Az operátor nem támogatott a tulajdonság típusa (ebben a példában-tartalmaz nem használható logikai érték típusú). Használja a megfelelő operátorok a tulajdonság típusa. |
| Hiba: Lekérdezésfordítási hiba. |1. (felhasználó.részleg - eq "Értékesítési") (felhasználó.részleg - eq "Marketing")<br/><br/>2. (user.userPrincipalName -match "*@domain.ext") |1. Nincs megadva operátor. Használja az - és vagy - vagy két predikátumok csatlakozás<br/><br/>(felhasználó.részleg - eq "Értékesítési") – vagy (felhasználó.részleg - eq "Marketing")<br/><br/>2 hiba az - használt reguláris kifejezés a megfelelő<br/><br/>(user.userPrincipalName-egyeznie ". *@domain.ext"), azt is megteheti: (user.userPrincipalName-egyeznie "@domain.ext$")|

## <a name="supported-properties"></a>Támogatott tulajdonságok
A speciális szabály használható felhasználói tulajdonságok a következők:

### <a name="properties-of-type-boolean"></a>Logikai érték típusú tulajdonságairól
Engedélyezett operátorok

* -eq
* -ne

| Tulajdonságok | Megengedett értékek | Használat |
| --- | --- | --- |
| AccountEnabled |IGAZ, hamis |user.accountEnabled - eq igaz |
| dirSyncEnabled |IGAZ, hamis |user.dirSyncEnabled - eq igaz |

### <a name="properties-of-type-string"></a>Karakterlánc típusú tulajdonságok
Engedélyezett operátorok

* -eq
* -ne
* -notStartsWith
* -Startswith elemnek
* -tartalmaz
* -notContains
* -felel meg
* -notMatch
* -a
* -notIn

| Tulajdonságok | Megengedett értékek | Használat |
| --- | --- | --- |
| city |A karakterlánc értéke vagy *null értékű* |(user.city - eq "érték") |
| Ország |A karakterlánc értéke vagy *null értékű* |(felhasználó.ország - eq "érték") |
| Cégnév | A karakterlánc értéke vagy *null értékű* | (user.companyName - eq "érték") |
| Szervezeti egység |A karakterlánc értéke vagy *null értékű* |(felhasználó.részleg - eq "érték") |
| displayName |Bármilyen karakterlánc típusú értéket |(user.displayName - eq "érték") |
| employeeId |Bármilyen karakterlánc típusú értéket |(user.employeeId -eq "value")<br>(user.employeeId - ne *null*) |
| facsimileTelephoneNumber |A karakterlánc értéke vagy *null értékű* |(user.facsimileTelephoneNumber - eq "érték") |
| givenName |A karakterlánc értéke vagy *null értékű* |(user.givenName -eq "value") |
| jobTitle |A karakterlánc értéke vagy *null értékű* |(user.jobTitle - eq "érték") |
| mail |A karakterlánc értéke vagy *null* (SMTP-cím felhasználó) |(user.mail - eq "érték") |
| mailNickName |Bármilyen karakterlánc típusú értéket (mail alias a felhasználó) |(user.mailNickName - eq "érték") |
| Mobileszköz |A karakterlánc értéke vagy *null értékű* |(user.mobile - eq "érték") |
| objectId |A user objektum GUID-azonosítója |(user.objectId - eq "1111111-1111-1111-1111-111111111111") |
| onPremisesSecurityIdentifier | A helyi biztonsági azonosítóját (SID) a felhasználók számára a felhőbe a helyszíni szinkronizálva. |(user.onPremisesSecurityIdentifier -eq "S-1-1-11-1111111111-1111111111-1111111111-1111111") |
| passwordPolicies |None DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword |(user.passwordPolicies -eq "DisableStrongPassword") |
| physicalDeliveryOfficeName |A karakterlánc értéke vagy *null értékű* |(user.physicalDeliveryOfficeName -eq "value") |
| Irányítószám |A karakterlánc értéke vagy *null értékű* |(user.postalCode - eq "érték") |
| preferredLanguage |ISO 639-1 kódot |(user.preferredLanguage - eq "en-US") |
| sipProxyAddress |A karakterlánc értéke vagy *null értékű* |(user.sipProxyAddress -eq "value") |
| state |A karakterlánc értéke vagy *null értékű* |(user.state - eq "érték") |
| streetAddress |A karakterlánc értéke vagy *null értékű* |(user.streetAddress -eq "value") |
| Vezetéknév |A karakterlánc értéke vagy *null értékű* |(user.surname - eq "érték") |
| TelephoneNumber |A karakterlánc értéke vagy *null értékű* |(user.telephoneNumber - eq "érték") |
| usageLocation |Két betűkkel országhívószám |(user.usageLocation - eq "US") |
| userPrincipalName |Bármilyen karakterlánc típusú értéket |(user.userPrincipalName - eq "alias@domain") |
| userType |tag vendég *null értékű* |(user.userType - eq "Tag") |

### <a name="properties-of-type-string-collection"></a>Típusú karakterlánc gyűjtemény tulajdonságai
Engedélyezett operátorok

* -tartalmaz
* -notContains

| Tulajdonságok | Megengedett értékek | Használat |
| --- | --- | --- |
| otherMails |Bármilyen karakterlánc típusú értéket |(user.otherMails -contains "alias@domain") |
| proxyAddresses |SMTP: alias@domain smtp: alias@domain |(user.proxyAddresses -contains "SMTP: alias@domain") |

## <a name="multi-value-properties"></a>Többértékű tulajdonságai
Engedélyezett operátorok

* -a (kielégíteni, ha legalább a gyűjtemény több eleme egyezik a következő feltételt:)
* -az összes (teljesülnek, ha a gyűjtemény összes elemének felel meg a feltétel)

| Tulajdonságok | Értékek | Használat |
| --- | --- | --- |
| assignedPlans |A gyűjtemény minden vezérlőnek a következő karakterlánc tulajdonságai: capabilityStatus, szolgáltatás, servicePlanId |user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled") |

A rendszer ugyanolyan típusú objektumok gyűjteményeit adják többértékű tulajdonságokat. Használhat - bármely és - minden üzemeltetői számára, illetve egy vagy az összes elem a gyűjteményben, alkalmaz rá egy feltételt. Példa:

assignedPlans egy többértékű tulajdonság, amely tartalmazza a felhasználóhoz rendelt összes service-csomagokról. Az alábbi kifejezés fogja kiválasztani az Exchange Online (terv 2) service-csomag, amely egyúttal az engedélyezési állapotot rendelkező felhasználók:

```
user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled")
```

(A Guid azonosító azonosítja az Exchange Online (terv 2) service-csomag).

> [!NOTE]
> Ez akkor hasznos, ha azt szeretné, hogy minden felhasználó azonosításához, amely az Office 365 (vagy más Microsoft Online Services szolgáltatással) funkció engedélyezve van, például, amelyekre őket egy bizonyos házirendcsoport.

Az alábbi kifejezés fogja kiválasztani az összes, akik rendelkeznek minden service-csomag, amely az Intune szolgáltatással (szolgáltatásnév "SCO" által azonosított) van társítva:
```
user.assignedPlans -any (assignedPlan.service -eq "SCO" -and assignedPlan.capabilityStatus -eq "Enabled")
```

## <a name="use-of-null-values"></a>Null értékek használatát

A szabály a null érték megadásához használja a *null* érték. Ügyeljen arra, hogy nem használja a word idézőjelbe *null* -Ha így tesz, azt fogja értelmezni literál karakterlánc-érték. A megfelelő módon való hivatkozáshoz a null érték a következőképpen történik:
```
   user.mail –ne null
```

## <a name="extension-attributes-and-custom-attributes"></a>Egyéni oszlopainál és a bővítmény
A bővítményattribútumokat és egyéni attribútumok dinamikus tagsági szabályok támogatottak.

Kiterjesztési attribútumot a helyszíni Windows Server AD szinkronizált, és tegye meg "ExtensionAttributeX", ahol X értéke 1 – 15 formátuma.
Egy bővítmény attribútumot használó szabály például lenne.
```
(user.extensionAttribute15 -eq "Marketing")
```
Az egyéni attribútumok szinkronizált a helyi Windows Server AD-ről vagy csatlakoztatott SaaS-alkalmazás és a formátuma "user.extension_[GUID]\__ [attribútum]" ahol [GUID] az az attribútum az aad-ben létrehozott alkalmazás az aad-ben az egyedi azonosítója, amely [attribútum] esetén az attribútum neve, mert azt létrehozták.
Példa egy szabályt, amely egy egyéni attribútumot használ:
```
user.extension_c272a57b722d4eb29bfe327874ae79cb__OfficeNumber  
```
Az egyéni attribútum nevében található a könyvtárban a felhasználó lekérdezésével attribútum Graph Explorerrel és keres az attribútum nevét.

## <a name="direct-reports-rule"></a>"Közvetlen beosztottai" szabály
Létrehozhat egy Manager minden közvetlen beosztottai tartalmazó csoport. A kezelő közvetlen beosztottai később módosíthatja, ha a csoport tagsága automatikusan igazítani.

> [!NOTE]
> 1. A szabály érvényesítéséhez, ellenőrizze, hogy a **kezelő azonosítója** tulajdonság helyesen beállítva a felhasználók az Ön bérelt szolgáltatásának. Akkor is ellenőrizhesse a felhasználó aktuális értékét az **profil lapon**.
> 2. Ez a szabály csak **közvetlen** jelentéseket. Jelenleg nem lehetséges egy beágyazott hierarchiához, pl. tartalmazó csoport közvetlen jelentések és a csoport létrehozásához.

**A csoport konfigurálása**

1. Kövesse a 1-5 lépések szakaszából [a speciális szabály létrehozásához](#to-create-the-advanced-rule), és válassza ki egy **tagsági típusa** a **dinamikus felhasználói**.
2. Az a **dinamikus tagsági szabályok** panelen adja meg a szabály a következő szintaxissal:

    *A(z) "{obectID_of_manager}" közvetlen beosztottaik*

    Példa egy érvényes szabályt:
```
                    Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863"
```
    where “62e19b97-8b3d-4d4a-a106-4ce66896a863” is the objectID of the manager. The object ID can be found on manager's **Profile tab**.
3. A szabály mentése után a megadott kezelő azonosítója értékkel rendelkező összes felhasználó bekerül a csoporthoz.

## <a name="using-attributes-to-create-rules-for-device-objects"></a>Attribútumok használata eszközobjektumok szabályok létrehozása
Olyan szabály, amely kijelöli a tagság eszközobjektumok egy csoportot is létrehozhat. A következő eszközattribútumokon is használható.

 Eszköz attribútum  | Értékek | Példa
 ----- | ----- | ----------------
 AccountEnabled | IGAZ, hamis | (device.accountEnabled - eq igaz)
 displayName | Bármilyen karakterlánc típusú értéket |(device.displayName - eq "Rob Iphone")
 deviceOSType | Bármilyen karakterlánc típusú értéket | (device.deviceOSType - eq "iPad") – vagy (device.deviceOSType - eq "iPhone")
 deviceOSVersion | Bármilyen karakterlánc típusú értéket | (eszköz. OSVersion - eq "9.1")
 deviceCategory | egy érvényes eszköznévvel kategória | (device.deviceCategory - eq "BYOD")
 DeviceManufacturer | Bármilyen karakterlánc típusú értéket | (device.deviceManufacturer - eq "Samsung")
 DeviceModel | Bármilyen karakterlánc típusú értéket | (device.deviceModel - eq "iPad vezeték nélkül")
 deviceOwnership | Személyes, munkahelyi, ismeretlen | (device.deviceOwnership - eq "Vállalati")
 domainName | Bármilyen karakterlánc típusú értéket | (device.domainName -eq "contoso.com")
 enrollmentProfileName | Az Apple Eszközregisztrációs profil neve | (device.enrollmentProfileName - eq "DEP iPhone-OK")
 isRooted | IGAZ, hamis | (device.isRooted - eq igaz)
 managementType | Mobileszköz-kezelési (csak mobil eszközökön)<br>PC (számára a számítógépes Intune-ügynök által felügyelt számítógépek) | (device.managementType - eq "MDM")
 OrganizationalUnit | bármilyen karakterlánc típusú értéket állítja be a helyszíni Active Directory szervezeti egység névnek megfelelő | (device.organizationalUnit - eq "USA számítógépek")
 deviceId | egy érvényes Azure AD-Eszközazonosító | (device.deviceId -eq "d4fe7726-5966-431c-b3b8-cddc8fdb717d")
 objectId | egy érvényes Azure AD objektumazonosító: |  (device.objectId -eq 76ad43c9-32c5-45e8-a272-7b58b58f596d")



## <a name="changing-dynamic-membership-to-static-and-vice-versa"></a>Dinamikus tagság módosítása a statikus, és ez fordítva is igaz
Akkor lehet módosítani a módjára vonatkozik a csoport tagságát. Ez akkor hasznos, ha meg szeretné tartani a azonos csoport nevét és Azonosítóját a rendszer úgy, hogy minden meglévő csoporthoz továbbra is érvényes; Új csoport létrehozásakor igényelnének frissítése ezeket a hivatkozásokat.

Az Azure-portál támogatja ezt a funkciót frissítése végezzük. Addig használható PowerShell-parancsmagok alább látható módon.

> [!WARNING]
> Egy létező statikus csoportot egy dinamikus csoport módosításakor a csoport összes meglévő tag törlődik, és ezután a tagsági szabály dolgoz fel új tagokat adhat. Ha a csoport használatával alkalmazásokhoz és erőforrásokhoz való hozzáférést, az eredeti tagok elveszthetik a hozzáférést a tagsági szabály teljesen feldolgozásáig.
>
> Javasolt tesztelni az új tagsági szabály előzetesen gondoskodjon arról, hogy az új a csoporttagságot a várt módon.

**Egy csoport tagságát változáskezelés a PowerShell használatával**

> [!NOTE]
> Szüksége lesz a parancsmagok használatával dinamikus csoport tulajdonságainak módosításához **előzetes verziójában** [Azure AD PowerShell-verzió 2](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0). Az előzetes verzióját, a telepítése [Itt](https://www.powershellgallery.com/packages/AzureADPreview).

Íme egy példa, függvények, amelyek tagságkezelés váltani egy meglévő csoporthoz. Vegye figyelembe, hogy van ügyelni megfelelően kezelheti a GroupTypes tulajdonság megőrzéséhez van, előfordulhat, hogy létezik értékek dinamikus tagságot egymástól független.

```
#The moniker for dynamic groups as used in the GroupTypes property of a group object
$dynamicGroupTypeString = "DynamicMembership"

function ConvertDynamicGroupToStatic
{
    Param([string]$groupId)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -eq $null -or !$groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a static group. Aborting conversion.";
    }


    #remove the type for dynamic groups, but keep the other type values
    $groupTypes.Remove($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to remove the dynamic type, ii) pause execution of the current rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "Paused"
}

function ConvertStaticGroupToDynamic
{
    Param([string]$groupId, [string]$dynamicMembershipRule)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -ne $null -and $groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a dynamic group. Aborting conversion.";
    }
    #add the dynamic group type to existing types
    $groupTypes.Add($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to add the dynamic type, ii) start execution of the rule, iii) set the rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "On" -MembershipRule $dynamicMembershipRule
}
```
Egy csoport statikus tételéhez:
```
ConvertDynamicGroupToStatic "a58913b2-eee4-44f9-beb2-e381c375058f"
```
A csoport dinamikus tételéhez:
```
ConvertStaticGroupToDynamic "a58913b2-eee4-44f9-beb2-e381c375058f" "user.displayName -startsWith ""Peter"""
```
## <a name="next-steps"></a>További lépések
Ezek a cikkek kiegészítő információt nyújt az Azure Active Directory csoportokat.

* [Tekintse meg a meglévő csoportok](active-directory-groups-view-azure-portal.md)
* [Hozzon létre egy új csoportot és tagok hozzáadása](active-directory-groups-create-azure-portal.md)
* [Egy csoport beállításainak kezelése](active-directory-groups-settings-azure-portal.md)
* [A csoport tagságát kezelése](active-directory-groups-membership-azure-portal.md)
* [A csoport dinamikus szabályok kezelése](active-directory-groups-dynamic-membership-azure-portal.md)
