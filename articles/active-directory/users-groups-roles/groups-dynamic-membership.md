---
title: Dinamikus automatikus tagsági szabályok hivatkoznak az Azure Active Directoryban |} A Microsoft Docs
description: Hogyan lehet automatikusan feltölti a csoportokat és a egy odkaz nA pravidlo tagsági szabályok létrehozásához.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 11/07/2018
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: adb53bb5722bff2374097626e8a3f1679ca00788
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51633529"
---
# <a name="dynamic-membership-rules-for-groups-in-azure-active-directory"></a>Az Azure Active Directory a dinamikus tagsági szabályok

Az Azure Active Directory (Azure AD) létrehozhat összetett Attribútumalapú szabályok dinamikus csoporttagságok engedélyezése. Dinamikus csoporttagság csökkenti az adminisztratív terhelést, a felhasználók hozzáadása és eltávolítása. Ez a cikk részletesen, a tulajdonságok és a felhasználók vagy eszközök számára a dinamikus tagsági szabályok létrehozásához szintaxisát. Biztonsági vagy Office 365-csoportok esetében dinamikustagság-szabály beállítására is lehetőség van.

Ha módosítja olyan felhasználó vagy eszköz attribútumai, a rendszer kiértékeli az összes dinamikus szabályok annak ellenőrzéséhez, hogy aktiválja a változás minden olyan csoportot ad hozzá vagy távolít el egy könyvtárban. Ha egy felhasználó vagy az eszköz megfelel a egy egy csoportra vonatkozó szabálynak, kerülnek, a csoport tagjaként. Ezek a szabály már nem felel meg, ha eltávolítja. Nem lehet manuálisan hozzáadásakor vagy eltávolításakor egy dinamikus csoport tagja.

* Eszközök vagy felhasználók dinamikus csoportot hozhat létre, de nem hozhat létre egy szabályt, amely tartalmazza a felhasználókat és eszközöket egyaránt.
* Nem hozható létre egy eszközcsoportot a eszköztulajdonosok attribútumok alapján. Eszköz tagsági szabályok csak is lehet hivatkozni az eszköz attribútumait.

> [!NOTE]
> Ez a funkció egy Azure AD Premium P1-licenc szükséges minden egyedi felhasználóhoz, amely egy vagy több dinamikus csoport tagja. Licencek hozzárendelése a felhasználókhoz a számukra a dinamikus csoportok tagjai nem szükséges, de minimálisan hány licencet kell rendelkeznie ahhoz, hogy biztosítsák a minden ilyen felhasználót a bérlőben. Például ha 1000 egyedi felhasználók összesen elérhető összes dinamikus a bérlőben, kell legalább 1000 licencek az Azure AD Premium P1 licenc követelménynek.
>

## <a name="constructing-the-body-of-a-membership-rule"></a>A szervezet egy tagsági szabály létrehozása

A tagsági szabály, amely automatikusan feltölti a felhasználók vagy eszközök egy csoport a bináris kifejezés, amely egy igaz vagy HAMIS eredményt eredményez. A három része egy egyszerű szabályt a következők:

* Tulajdonság
* Művelet
* Érték

A kifejezés a kijelzők sorrendje fontosak szintaktikai hibák elkerülése érdekében.

### <a name="rules-with-a-single-expression"></a>Egyetlen kifejezés a szabályok

Egyetlen kifejezés a legegyszerűbb egy tagsági szabályt, és csak három részből áll a fent említett. Egy szabályt az egyetlen kifejezés ehhez hasonlóan néz ki: `Property Operator Value`, ahol a tulajdonság a szintaxis object.property nevére.

Az alábbiakban látható egy példa egy megfelelően kialakított tagsági szabályt, az egyetlen kifejezés:

```
user.department -eq "Sales"
```

Zárójelek egyetlen kifejezés nem kötelezően megadandó jellegűek. A tagsági szabály törzsét teljes hossza nem lehet hosszabb 2048 karakternél.

## <a name="supported-properties"></a>Támogatott tulajdonságok

Három feltételtípus tulajdonságok, a tagsági szabály létrehozására használható.

* Logikai
* Karakterlánc
* Karakterlánc-gyűjtemény

Az alábbiakban a felhasználói tulajdonságok, amelyek segítségével hozzon létre egy egyetlen kifejezés.

### <a name="properties-of-type-boolean"></a>Tulajdonságait írja be a logikai érték

| Tulajdonságok | Megengedett értékek | Használat |
| --- | --- | --- |
| accountEnabled |IGAZ, hamis |user.accountEnabled - eq igaz |
| dirSyncEnabled |IGAZ, hamis |user.dirSyncEnabled - eq igaz |

### <a name="properties-of-type-string"></a>Karakterlánc típusú tulajdonságok

| Tulajdonságok | Megengedett értékek | Használat |
| --- | --- | --- |
| city |Bármilyen karakterlánc értékét, vagy *null értékű* |(user.city - eq "value") |
| Ország |Bármilyen karakterlánc értékét, vagy *null értékű* |(felhasználó.ország - eq "value") |
| Cégnév | Bármilyen karakterlánc értékét, vagy *null értékű* | (user.companyName - eq "value") |
| Szervezeti egység |Bármilyen karakterlánc értékét, vagy *null értékű* |(user.department - eq "value") |
| displayName |bármilyen karakterlánc típusú értéket |(user.displayName - eq "value") |
| employeeId |bármilyen karakterlánc típusú értéket |(user.employeeId - eq "value")<br>(user.employeeId - ne *null*) |
| facsimileTelephoneNumber |Bármilyen karakterlánc értékét, vagy *null értékű* |(user.facsimileTelephoneNumber - eq "value") |
| givenName |Bármilyen karakterlánc értékét, vagy *null értékű* |(user.givenName - eq "value") |
| Beosztás |Bármilyen karakterlánc értékét, vagy *null értékű* |(user.jobTitle - eq "value") |
| levelezés |Bármilyen karakterlánc értékét, vagy *null* (SMTP-cím a felhasználó) |(user.mail - eq "value") |
| mailNickName |Bármilyen karakterlánc típusú értéket (a felhasználó e-mail aliasa) |(user.mailNickName - eq "value") |
| mobil |Bármilyen karakterlánc értékét, vagy *null értékű* |(user.mobile - eq "value") |
| objectId |A user objektum GUID-ja |(user.objectId - eq "11111111-1111-1111-1111-111111111111") |
| onPremisesSecurityIdentifier | A helyszíni biztonsági azonosítója (SID) a helyszínről a felhőbe szinkronizált felhasználók. |(user.onPremisesSecurityIdentifier -eq "S-1-1-11-1111111111-1111111111-1111111111-1111111") |
| passwordPolicies |None DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword |(user.passwordPolicies -eq "DisableStrongPassword") |
| physicalDeliveryOfficeName |Bármilyen karakterlánc értékét, vagy *null értékű* |(user.physicalDeliveryOfficeName - eq "value") |
| Irányítószám |Bármilyen karakterlánc értékét, vagy *null értékű* |(user.postalCode - eq "value") |
| preferredLanguage |ISO 639-1 kód |(user.preferredLanguage - eq "en-US") |
| sipProxyAddress |Bármilyen karakterlánc értékét, vagy *null értékű* |(user.sipProxyAddress - eq "value") |
| state |Bármilyen karakterlánc értékét, vagy *null értékű* |(user.state - eq "value") |
| streetAddress |Bármilyen karakterlánc értékét, vagy *null értékű* |(user.streetAddress - eq "value") |
| Vezetéknév |Bármilyen karakterlánc értékét, vagy *null értékű* |(user.surname - eq "value") |
| telephoneNumber |Bármilyen karakterlánc értékét, vagy *null értékű* |(user.telephoneNumber - eq "value") |
| a usagelocation érték |Két betűkkel országkód: |(user.usageLocation - eq "US") |
| userPrincipalName |bármilyen karakterlánc típusú értéket |(user.userPrincipalName - eq "alias@domain") |
| userType |tag vendég *null értékű* |(user.userType - eq "Tag") |

### <a name="properties-of-type-string-collection"></a>Típus string gyűjtemény tulajdonságai

| Tulajdonságok | Megengedett értékek | Használat |
| --- | --- | --- |
| otherMails |bármilyen karakterlánc típusú értéket |(user.otherMails-tartalmaz "alias@domain") |
| proxyAddresses |SMTP: alias@domain smtp: alias@domain |(user.proxyAddresses -contains "SMTP: alias@domain") |

A használt eszköz szabályok tulajdonságainak megtekintéséhez [eszközök szabályai](#rules-for-devices).

## <a name="supported-operators"></a>Támogatott operátorok

Az alábbi táblázat a támogatott operátorok és azok egyetlen kifejezés szintaxisa. Operátorok használhatók, vagy a kötőjel (-) előtag nélkül.

| Művelet | Szintaxis |
| --- | --- |
| Nem egyenlő |-ne |
| Egyenlő |-eq |
| Nem ezzel kezdődik |-notStartsWith |
| Ezzel kezdődik |-startsWith |
| Nem tartalmazza |-notContains |
| Contains |-tartalmaz |
| Nem egyezik |-notMatch |
| Egyezés |-felel meg |
| Eleme ennek | -a |
| Nem található | -notIn |

### <a name="using-the--in-and--notin-operators"></a>Használja a - az - notIn operátorok és

Ha azt szeretné, a felhasználói attribútum több különböző érték összehasonlítására használhatja a - a vagy - notIn operátorok. A szögletes zárójelet szimbólumok használata "[" és "]", és az értékek listája végén.

 A következő példában a kifejezés eredménye IGAZ Ha user.department értéke megegyezik a listában található értékek valamelyikét:

```
   user.department -in ["50001","50002","50003",“50005”,“50006”,“50007”,“50008”,“50016”,“50020”,“50024”,“50038”,“50039”,“51100”]
```


### <a name="using-the--match-operator"></a>Használja a - egyezés operátor 
A **-megfelelő** operátor használata az egyező bármely reguláris kifejezést. Példák:

```
user.displayName -match "Da.*"   
```
Da, Dav, David igaz értéked, akadálymentes kifejezés hamis.

```
user.displayName -match ".*vid"
```
David igaz értéket ad vissza, Da kifejezés hamis.

## <a name="supported-values"></a>Támogatott értékek

A kifejezésben használt értékek számos különböző típusú, beleértve a következőket tartalmazhatják:

* Sztringek
* Logikai – IGAZ, hamis
* Számok
* Tömbök – tömb szám, karakterlánc-tömbben

Egy kifejezés belüli értéket megadásakor fontos hibák elkerülése érdekében használja a megfelelő szintaxist. Szintaxis tippek a következők:

* Dupla idézőjelek között megadása nem kötelező, ha az érték nem karakterlánc.
* Karakterlánc és regex műveletek nagybetűk nem számítanak.
* Ha egy karakterláncértéket tartalmaz dupla idézőjelek között, mindkét idézőjelek között kell lehet átléptetni a \` karakter, például user.department - eq \`"értékesítési\`" a megfelelő szintaxist akkor, ha "Értékesítés" értékét.
* Is Null ellenőrzéseket végezhet, használja a null értékként, például `user.department -eq null`.

### <a name="use-of-null-values"></a>Null értékek használatát

Adjon meg null értéket a szabályban, használhatja a *null* értéket. 

* Használja a - eq vagy - ne összehasonlítása a *null* értéke egy kifejezés.
* Használjon idézőjeleket szó *null* csak akkor, ha azt szeretné, az értéknek szövegkonstansnak kell értelmezni.
* A - not operátor nem használható, összehasonlító operátor null értékű. Ha használ, hibaüzenetet kap vagy $null e null használja.

A megfelelő módon való hivatkozáshoz a null értéket a következőképpen történik:

```
   user.mail –ne null
```

## <a name="rules-with-multiple-expressions"></a>Szabályok több kifejezésekkel

A csoporttagsági szabály egynél több egyetlen kifejezés által csatlakoztatott állhat a - és, - vagy, és – nincs logikai operátorok. Logikai operátorok is együtt használható. 

A következő példák több kifejezésekkel megfelelően kialakított tagsági szabályok:

```
(user.department -eq "Sales") -or (user.department -eq "Marketing")
(user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")
```

### <a name="operator-precedence"></a>Műveleti sorrendet

Minden operátor ellentmondások legmagasabbtól a legalacsonyabb alább láthatók. Operátorok ugyanabban a sorban azonos prioritással a következők:

```
-eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch -in -notIn
-not
-and
-or
-any -all
```

A következő egy példa precedencia ahol kiértékelése a felhasználó számára a két kifejezés:

```
   user.department –eq "Marketing" –and user.country –eq "US"
```

Zárójelek között van szükség, csak akkor, ha a sorrendje nem felel meg a követelményeknek. Például, ha azt szeretné, hogy először ki kell értékelni részleg, az alábbiakban látható hogyan zárójelek használható sorrend meghatározására:

```
   user.country –eq "US" –and (user.department –eq "Marketing" –or user.department –eq "Sales")
```

## <a name="rules-with-complex-expressions"></a>Az összetett kifejezések szabályok

A tagsági szabály összetett kifejezések, ahol a tulajdonságait, operátorokat és értékeket is összetettebb űrlapokon állhat. Kifejezések összetett tekinthetők, ha a következők bármelyike igaz:

* A tulajdonság értékek; áll. pontosabban a többértékű tulajdonságai
* A kifejezések használata a - bármely és - minden operátor
* A kifejezés értéke maga is lehet egy vagy több kifejezése

## <a name="multi-value-properties"></a>Többértékű tulajdonságai

Többértékű tulajdonságok azonos típusú objektumok gyűjteményei. Használatával a tagsági szabályok létrehozásához használható a - bármely és - az összes logikai operátor.

| Tulajdonságok | Értékek | Használat |
| --- | --- | --- |
| assignedPlans | A gyűjtemény minden egyes objektum a következő karakterlánc-tulajdonságok közzététele: capabilityStatus, szolgáltatás, servicePlanId |user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled") |
| proxyAddresses| SMTP: alias@domain smtp: alias@domain | (user.proxyAddresses – bármely (\_ – tartalmazza a "contoso")) |

### <a name="using-the--any-and--all-operators"></a>Használja a - bármely és - minden operátor

Használható – egy olyan feltétel alkalmazására egy, vagy az összes elem a gyűjteményben, illetve bármely és - minden operátorok.

* -bármely (elégedett az eredménnyel ha legalább egy elem a gyűjteményben lévő megegyezik a feltétel)
* -az összes (teljesülnek, ha a gyűjteményben lévő összes elem a feltételnek megfelelő)

#### <a name="example-1"></a>1. példa

assignedplans parancsmagot, amely felsorolja a felhasználóhoz rendelt minden szolgáltatáscsomag többértékű tulajdonság. A következő kifejezés lehetőséget választja, az Exchange Online (2. csomag) service-csomag (a GUID értéket), amely egyben az engedélyezési állapotot rendelkező felhasználók számára:

```
user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled")
```

Egy szabály például a segítségével minden felhasználó csoport számára, akikkel az Office 365-höz (vagy más Microsoft Online Services) funkció engedélyezve van. Ezután alkalmazhat házirendeket vannak beállítva a csoporthoz.

#### <a name="example-2"></a>2. példa

A következő kifejezés bármely service-csomag, amely társítva van az Intune szolgáltatással ("SCO" szolgáltatásnév alapján azonosított) rendelkező minden felhasználó számára lehetőséget választja:

```
user.assignedPlans -any (assignedPlan.service -eq "SCO" -and assignedPlan.capabilityStatus -eq "Enabled")
```

### <a name="using-the-underscore--syntax"></a>Használja az aláhúzás (\_) szintaxissal

Az aláhúzás (\_) szintaxisa megegyezik egy adott értéket a többértékű karakterlánc gyűjtemény tulajdonságait, felhasználókat vagy eszközöket ad hozzá egy dinamikus csoport egyik előfordulását. Együtt használva a - bármely vagy - minden operátorok.

Íme egy példa használatával az aláhúzás (\_) egy szabályban a tagokat user.proxyAddress alapján (ezt ugyanúgy működik a user.otherMails). Ez a szabály a proxykiszolgáló címét, amely tartalmazza a "contoso" a csoporthoz minden felhasználó hozzáadása.

```
(user.proxyAddresses -any (_ -contains "contoso"))
```

## <a name="other-properties-and-common-rules"></a>Egyéb tulajdonságok és a közös szabályok

### <a name="create-a-direct-reports-rule"></a>"A közvetlen beosztottak" szabály létrehozása

Létrehozhat egy vezető közvetlen beosztottjainak összes tartalmazó csoport. A közvetlen beosztottjait később módosíthatja, ha a csoport tagsága automatikusan módosul.

A közvetlen beosztottak szabály jön létre a következő szintaxis használatával:

```
Direct Reports for "{objectID_of_manager}"
```

Íme egy példa egy érvényes szabályt, ahol a "62e19b97-8b3d-4d4a-a106-4ce66896a863" az ObjectId azonosítóját, a kezelő:

```
Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863"
```

Az alábbi tippek segíthetnek a szabály megfelelően használja.

* A **Kezelőazonosító** a kezelő objektum azonosítója. A kezelőben található **profil**.
* A szabály működik, ellenőrizze, hogy a **Manager** tulajdonság helyesen van-e állítva a bérlőn belüli felhasználók számára. A jelenlegi érték a felhasználó ellenőrizheti **profil**.
* Ez a szabály csak a közvetlen beosztottjait támogatja. Más szóval nem hozható létre egy csoportot a felettese, közvetlen beosztottai *és* jelentéseiket.
* Ez a szabály nem kombinálható más tagsági szabályok.

### <a name="create-an-all-users-rule"></a>"Minden felhasználó" szabály létrehozása

Létrehozhat egy tagsági szabállyal a bérlőn belüli összes felhasználóját tartalmazó csoporthoz. Amikor a felhasználók hozzáadásakor vagy eltávolításakor a bérlőtől a jövőben, a csoport tagsága automatikusan módosul.

A "Minden felhasználó" szabály - ne operátor és a null érték egyetlen kifejezés használatával jön létre. Ez a szabály B2B vendégfelhasználók, valamint a tag felhasználók hozzáadása a csoporthoz.

```
user.objectid -ne null
```

### <a name="create-an-all-devices-rule"></a>"Minden eszköz" szabály létrehozása

Létrehozhat egy tagsági szabály használatával a bérlőn belüli összes eszközök csoportja. Eszközök hozzáadásakor vagy eltávolításakor a bérlőtől a jövőben, ha a csoport tagsága automatikusan módosul.

A "Minden eszköz" szabály jön létre a - ne operátor és a null érték egyetlen kifejezés használatával:

```
device.objectid -ne null
```

## <a name="extension-properties-and-custom-extension-properties"></a>Bővítmény tulajdonságai és egyéni bővítmény tulajdonságai

A dinamikus tagsági szabályok karakterlánc tulajdonságai bővítményattribútumok és egyéni extenson tulajdonságok támogatottak. A bővítményattribútumok szinkronizálva lesznek a helyszíni Windows Server AD, és tegye meg "ExtensionAttributeX", ahol az X értéke 1 – 15 formátumát. Íme egy példa bővítményattribútum-tulajdonságként használó szabály:

```
(user.extensionAttribute15 -eq "Marketing")
```

Egyéni bővítménytulajdonságok szinkronizálva lesznek a helyszíni Windows Server AD- vagy csatlakoztatott SaaS-alkalmazás és -formátumát `user.extension_[GUID]__[Attribute]`, ahol:

* [GUID] egy egyedi azonosítója, amely a tulajdonság az Azure AD-ben létrehozott Azure AD-ben
* [] Attribútum a tulajdonság nevét módon lett létrehozva

Például egy szabályt, amely egy egyéni bővítmény tulajdonságot használja a következő:

```
user.extension_c272a57b722d4eb29bfe327874ae79cb__OfficeNumber -eq "123"
```

Az egyéni tulajdonság neve található a címtárban lekérdezi a felhasználó által a tulajdonság Graph Explorer használatával, és megkeresi a tulajdonság nevét. Ezenkívül bejelölheti **egyéni bővítménytulajdonság lekérése** hivatkozásra a dinamikus felhasználói csoport szabály builder adjon meg egy egyedi Alkalmazásazonosító, és teljes listája megtalálható a dinamikus tagsági szabály létrehozásakor egyéni bővítménytulajdonságok fogadására. Ez a lista is frissíthetők úgy, hogy minden olyan új egyéni bővítmény tulajdonságainak lekérése az adott alkalmazáshoz.

## <a name="rules-for-devices"></a>Eszközök szabályai

Egy szabály, amely eszközobjektumok tagsági kiválaszt egy csoportot is létrehozhat. Csoport tagjai nem lehet a felhasználókat és eszközöket egyaránt. A **organizationalUnit** attribútum már nincs felsorolva, és nem használható. Ez a karakterlánc adott esetben az Intune által van beállítva, de nem ismeri fel az Azure AD-ben, az eszközök nem ez az attribútum alapján kerülnek.

A következő eszköz attribútumokat is használható.

 Eszköz-attribútum  | Értékek | Példa
 ----- | ----- | ----------------
 accountEnabled | IGAZ, hamis | (device.accountEnabled - eq true)
 displayName | bármilyen karakterlánc típusú értéket |(device.displayName - eq "Rob Iphone")
 deviceOSType | bármilyen karakterlánc típusú értéket | (vagy device.deviceOSType - eq "iPad") – vagy (vagy device.deviceOSType - eq "iPhone")
 deviceOSVersion | bármilyen karakterlánc típusú értéket | (eszköz. OSVersion - eq "9.1")
 deviceCategory | egy érvényes eszközkategória-név | (device.deviceCategory - eq "BYOD")
 deviceManufacturer | bármilyen karakterlánc típusú értéket | (device.deviceManufacturer - eq "Samsung")
 deviceModel | bármilyen karakterlánc típusú értéket | (device.deviceModel - eq "iPad Air")
 deviceOwnership | Személyes, vállalati, ismeretlen | (device.deviceOwnership - eq "Céges")
 Tartománynév | bármilyen karakterlánc típusú értéket | (device.domainName - eq "contoso.com")
 enrollmentProfileName | Az Apple Eszközregisztrációs profilt, vagy a Windows Autopilot-profil neve | (device.enrollmentProfileName - eq "DEP iPhone-OK")
 isRooted | IGAZ, hamis | (device.isRooted - eq true)
 managementType | Mobileszköz-kezelési (mobileszközök)<br>PC (az Intune PC-ügynökkel kezelt számítógépekhez) | (device.managementType - eq "MDM")
 deviceId | egy érvényes Azure AD-Eszközazonosító | (device.deviceId -eq "d4fe7726-5966-431c-b3b8-cddc8fdb717d")
 objectId | érvényes Azure AD-objektumazonosító |  (device.objectId -eq 76ad43c9-32c5-45e8-a272-7b58b58f596d")
 systemLabels | bármilyen karakterlánc megfelelő az Intune eszköz tulajdonságot a Modern munkahely eszközök címkézése | (device.systemLabels-tartalmaz "M365Managed")

## <a name="next-steps"></a>További lépések

E cikkekben további információk az Azure Active Directory-csoportokon.

* [Meglévő csoportok megtekintése](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Új csoport létrehozása és tagok hozzáadása](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Csoportbeállítások kezelése](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Csoporttagságok kezelése](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [A csoportban lévő felhasználók dinamikus szabályainak kezelése](groups-dynamic-membership.md)
