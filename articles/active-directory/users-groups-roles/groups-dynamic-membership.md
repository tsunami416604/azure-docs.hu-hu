---
title: A dinamikus csoporttagság szabályai - Azure AD | Microsoft dokumentumok
description: A csoportok automatikus feltöltéséhez és a szabályhivatkozáshoz létrejön tagsági szabályok.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/27/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: da983f87977de922ec547c3ade2972dfb4d69363
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253077"
---
# <a name="dynamic-membership-rules-for-groups-in-azure-active-directory"></a>Dinamikus tagsági szabályok csoportokhoz az Azure Active Directoryban

Az Azure Active Directoryban (Azure AD) összetett attribútumalapú szabályokat hozhat létre a csoportok dinamikus tagságának engedélyezéséhez. A dinamikus csoporttagság csökkenti a felhasználók hozzáadásának és eltávolításának adminisztratív terhelését. Ez a cikk részletezi a tulajdonságokat és a szintaxist a felhasználók vagy eszközök dinamikus tagsági szabályainak létrehozásához. Biztonsági vagy Office 365-csoportok esetében dinamikustagság-szabály beállítására is lehetőség van.

Amikor egy felhasználó vagy eszköz bármely attribútuma megváltozik, a rendszer kiértékeli a könyvtárösszes dinamikus csoportszabályát, hogy lássa, a módosítás elindít-e egy csoport összeadását vagy eltávolítását. Ha egy felhasználó vagy eszköz megfelel egy csoport egyik szabályának, akkor a csoport tagjaként kerül hozzáadásra. Ha már nem felelnek meg a szabálynak, akkor törlődnek. Dinamikus csoport tagjának manuálishozzáadása és eltávolítása nem adható hozzá és nem távolítható el.

- Dinamikus csoportot hozhat létre az eszközök höz vagy a felhasználókhoz, de nem hozhat létre olyan szabályt, amely felhasználókat és eszközöket is tartalmaz.
- Az eszköztulajdonosok attribútumai alapján nem hozhat létre eszközcsoportot. Az eszköztagsági szabályok csak eszközattribútumokra hivatkozhatnak.

> [!NOTE]
> Ehhez a funkcióhoz egy Azure AD Premium P1 licenc szükséges minden olyan egyedi felhasználóhoz, aki egy vagy több dinamikus csoport tagja. Nem kell licenceket hozzárendelnie a felhasználókhoz ahhoz, hogy dinamikus csoportok tagjai legyenek, de a bérlőben a minimális számú licenccel kell rendelkeznie az összes ilyen felhasználó lefedéséhez. Ha például összesen 1000 egyedi felhasználóval rendelkezik a bérlő összes dinamikus csoportjában, legalább 1000 licencre van szüksége az Azure AD Premium P1-hez a licenckövetelmények teljesítéséhez.
> Dinamikus eszközcsoporthoz tartozó eszközökhöz nincs szükség licencre.

## <a name="rule-builder-in-the-azure-portal"></a>Szabályszerkesztő az Azure Portalon

Az Azure AD egy szabályszerkesztőt biztosít a fontos szabályok gyorsabb létrehozásához és frissítéséhez. A szabályszerkesztő legfeljebb öt kifejezés felépítését támogatja. A szabályszerkesztő megkönnyíti a szabály néhány egyszerű kifejezéssel történő kialakítását, azonban nem használható minden szabály reprodukálására. Ha a szabályszerkesztő nem támogatja a létrehozni kívánt szabályt, használhatja a szövegdobozt.

Íme néhány példa olyan speciális szabályokra vagy szintaxisokra, amelyeket a szövegmező használatával szeretne létrehozni:

- Ötnél több kifejezéssel rendelkező szabály
- A Közvetlen jelentések szabály
- [Operátori elsőbbség](groups-dynamic-membership.md#operator-precedence) beállítása
- [Összetett kifejezésekkel rendelkező szabályok](groups-dynamic-membership.md#rules-with-complex-expressions); például`(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> Előfordulhat, hogy a szabályszerkesztő nem tud megjeleníteni néhány, a szövegmezőben készült szabályt. Akkor jelenhet meg üzenet, ha a szabályszerkesztő nem tudja megjeleníteni a szabályt. A szabályszerkesztő semmilyen módon nem módosítja a támogatott szintaxist, az érvényesítést vagy a dinamikus csoportszabályok feldolgozását.

További részletes útmutatást a [Dinamikus csoport létrehozása vagy frissítése](groups-create-rule.md)című témakörben talál.

![Tagsági szabály hozzáadása dinamikus csoporthoz](./media/groups-dynamic-membership/update-dynamic-group-rule.png)

### <a name="rule-syntax-for-a-single-expression"></a>Egyetlen kifejezés szabályszintaxisa

Egyetlen kifejezés a tagsági szabály legegyszerűbb formája, és csak a fent említett három részből áll. Az egyetlen kifejezéssel rendelkező szabály `Property Operator Value`a következőhöz hasonlóan néz ki: . .

A következő példa egy megfelelően felépített tagsági szabály egyetlen kifejezéssel:

```
user.department -eq "Sales"
```

A zárójelek használata nem kötelező egyetlen kifejezéshez. A tagsági szabály törzsének teljes hossza nem haladhatja meg a 2048 karaktert.

## <a name="constructing-the-body-of-a-membership-rule"></a>Tagsági szabály törzsének összeállítása

A tagsági szabály, amely automatikusan feltölti a csoportot a felhasználókkal vagy eszközökkel, egy bináris kifejezés, amely igaz vagy hamis eredményt eredményez. Egy egyszerű szabály három része a következő:

- Tulajdonság
- Művelet
- Érték

A kifejezésen belüli részek sorrendje fontos a szintaktikai hibák elkerülése érdekében.

## <a name="supported-properties"></a>Támogatott tulajdonságok

A tagsági szabályok létrehozásához háromféle tulajdonság használható.

- Logikai
- Sztring
- Karakterlánc-gyűjtemény

Az alábbiakban azalábbi felhasználói tulajdonságokat használhatja egyetlen kifejezés létrehozásához.

### <a name="properties-of-type-boolean"></a>Logikai típusú tulajdonságok

| Tulajdonságok | Megengedett értékek | Használat |
| --- | --- | --- |
| accountEnabled |igaz hamis |user.accountEnabled -eq igaz |
| dirSyncEnabled |igaz hamis |user.dirSyncEnabled -eq igaz |

### <a name="properties-of-type-string"></a>Karakterlánc tulajdonságai

| Tulajdonságok | Megengedett értékek | Használat |
| --- | --- | --- |
| city |Bármely karakterlánc-érték vagy *null* |(user.city -eq "érték") |
| ország |Bármely karakterlánc-érték vagy *null* |(user.country -eq "érték") |
| vállalatneve | Bármely karakterlánc-érték vagy *null* | (user.companyName -eq "érték") |
| Részleg |Bármely karakterlánc-érték vagy *null* |(user.department -eq "érték") |
| displayName |Bármely karakterlánc-érték |(user.displayName -eq "érték") |
| alkalmazottazonosító |Bármely karakterlánc-érték |(user.employeeId -eq "érték")<br>(user.employeeId -ne *null)* |
| faxphoneTelefonszám |Bármely karakterlánc-érték vagy *null* |(user.faxPhoneNumber -eq "érték") |
| givenName |Bármely karakterlánc-érték vagy *null* |(user.givenName -eq "érték") |
| jobTitle |Bármely karakterlánc-érték vagy *null* |(user.jobTitle -eq "érték") |
| Levelezés |Bármely karakterlánc-érték vagy *null* (a felhasználó SMTP-címe) |(user.mail -eq "érték") |
| mailNickName |Bármilyen karakterláncérték (a felhasználó levelezési aliasa) |(user.mailNickName -eq "érték") |
| mobil |Bármely karakterlánc-érték vagy *null* |(user.mobile -eq "érték") |
| objectId |A felhasználói objektum GUID azonosítója |(user.objectId -eq "11111111-1111-1111-111111111111") |
| onPremisesSecurityIdentifier | Helyszíni biztonsági azonosító (SID) a helyszíni és a felhőből szinkronizált felhasználók számára. |(user.onPremisesSecurityIdentifier -eq "S-1-1-1111111111-11111111111-11111111-1111111-111111") |
| passwordPolicies |Nincs DisableStrongPassword DisablePasswordExpirationDisablePasswordExpiration, DisableStrongPassword |(user.passwordPolicies -eq "DisableStrongPassword") |
| physicalDeliveryOfficeName |Bármely karakterlánc-érték vagy *null* |(user.physicalDeliveryOfficeName -eq "érték") |
| postai kód |Bármely karakterlánc-érték vagy *null* |(user.postalCode -eq "érték") |
| preferredLanguage (elsődleges nyelv) |ISO 639-1 kód |(user.preferredLanguage -eq "en-US") |
| sipProxyAddress |Bármely karakterlánc-érték vagy *null* |(user.sipProxyAddress -eq "érték") |
| state |Bármely karakterlánc-érték vagy *null* |(user.state -eq "érték") |
| utcacím |Bármely karakterlánc-érték vagy *null* |(user.streetAddress -eq "érték") |
| surname |Bármely karakterlánc-érték vagy *null* |(user.surname -eq "érték") |
| telefonszáma |Bármely karakterlánc-érték vagy *null* |(user.phoneNumber -eq "érték") |
| usageLocation (használathelye) |Kétbetűs országkód |(user.usageLocation -eq "US") |
| userPrincipalName |Bármely karakterlánc-érték |(user.userPrincipalName -eqalias@domain" ") |
| userType (felhasználótípusa) |tag vendég *null* |(user.userType -eq "Tag") |

### <a name="properties-of-type-string-collection"></a>A karakterlánc-gyűjtemény tulajdonságai

| Tulajdonságok | Megengedett értékek | Használat |
| --- | --- | --- |
| egyéb E-mailek |Bármely karakterlánc-érték |(user.otherMails -containsalias@domain" ") |
| proxyAddresses |SMTP: alias@domain smtp:alias@domain |(user.proxyAddresses -contains "SMTP: alias@domain") |

Az eszközszabályokhoz használt tulajdonságokról az [eszközökre vonatkozó szabályok](#rules-for-devices)című témakörben van.

## <a name="supported-expression-operators"></a>Támogatott kifejezésoperátorok

Az alábbi táblázat az összes támogatott operátort és azok szintaxisát sorolja fel egyetlen kifejezéshez. Az operátorok kötőjellel (-) vagy anélkül is használhatók.

| Művelet | Szintaxis |
| --- | --- |
| Nem egyenlő |-ne |
| Egyenlő |-eq |
| Nem kezdődik |-notStartsWith |
| Kezdődik |-kezdődik |
| Nem tartalmaz |-notContains |
| Contains |-tartalmaz |
| Nem egyezik |-notMatch |
| Match |-mérkőzés |
| A | -ban |
| Nincs | -notIn |

### <a name="using-the--in-and--notin-operators"></a>A -in és -notIn operátorok használata

Ha egy felhasználói attribútum értékét több különböző értékkel szeretné összehasonlítani, használhatja a -in vagy -notIn operátorokat. Az értékek listájának megkezdéséhez és befejezéséhez használja a zárójelszimbólumokat.[" és "]" az értékek listájának megkezdéséhez és befejezéséhez.

 A következő példában a kifejezés igaz értéket ad ki, ha a user.department értéke megegyezik a lista bármelyik értékével:

```
   user.department -in ["50001","50002","50003","50005","50006","50007","50008","50016","50020","50024","50038","50039","51100"]
```


### <a name="using-the--match-operator"></a>A -match operátor használata 
A **-match** operátor bármely reguláris kifejezés egyeztetésére szolgál. Példák:

```
user.displayName -match "Da.*"   
```
Da, Dav, David értékeli, hogy igaz, aDa értékeli a hamis.

```
user.displayName -match ".*vid"
```
David igaz, Da értékeli a hamis.

## <a name="supported-values"></a>Támogatott értékek

A kifejezésekben használt értékek több típusból állhatnak, többek között a következőkből:

* Sztringek
* Logikai – igaz, hamis
* Számok
* Tömbök – számtömb, karakterlánctömb

Amikor egy kifejezést megad egy kifejezésen belül, fontos a megfelelő szintaxis használata a hibák elkerülése érdekében. Néhány szintaktikai tipp:

* A dupla idézőjelek nem kötelezőek, kivéve, ha az érték karakterlánc.
* A karakterlánc- és regex-műveletek nem érzékenyek a kis- és nagybetűkre.
* Ha egy karakterlánc értéke idézőjeleket tartalmaz, mindkét \` idézőjelet meg kell kerülni a \`karakter\`használatával, például user.department -eq "Sales" a megfelelő szintaxis, ha az "Értékesítés" az érték.
* Null csekkeket is végrehajthat, például null `user.department -eq null`értékként használva.

### <a name="use-of-null-values"></a>Null értékek használata

Ha null értéket szeretne megadni egy szabályban, *használhatja* a null értéket. 

* Használja az -eq vagy -ne értéket egy kifejezés *null* értékének összehasonlításához.
* Csak akkor használjon idézőjeleket a *null* szó körül, ha azt szeretné, hogy a karakterlánc konstans karakterlánc-értékként értelmezze.
* A -not operátor nem használható összehasonlító operátorként a null esetében. Ha használja, hibaüzenetet kap, függetlenül attól, hogy null vagy $null.

A null értékre való hivatkozás helyes módja a következő:

```
   user.mail –ne null
```

## <a name="rules-with-multiple-expressions"></a>Szabályok több kifejezéssel

A csoporttagsági szabály több kifejezésből is állhat, amelyeket az -és -vagy- és -nem logikai operátorok kötnek össze. Logikai operátorok is használhatók együtt. 

Az alábbi példák a több kifejezéssel rendelkező, megfelelően felépített tagsági szabályokra mutatnak be:

```
(user.department -eq "Sales") -or (user.department -eq "Marketing")
(user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")
```

### <a name="operator-precedence"></a>Operátorok műveleti sorrendje

Az összes operátor az alábbiakban a legmagasabbtól a legalacsonyabbig minden sorrendben fel van sorolva. Az azonos sorban lévő operátorok azonos prioritásúak:

```
-eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch -in -notIn
-not
-and
-or
-any -all
```

Az alábbi példa az operátorok elsőbbségére mutat be, ahol két kifejezés van kiértékelve a felhasználó számára:

```
   user.department –eq "Marketing" –and user.country –eq "US"
```

Zárójelek csak akkor szükségesek, ha az elsőbbség nem felel meg az Ön igényeinek. Ha például azt szeretné, hogy a részleg legyen először kiértékelve, az alábbiakban látható, hogyan használhatók zárójelek a sorrend meghatározására:

```
   user.country –eq "US" –and (user.department –eq "Marketing" –or user.department –eq "Sales")
```

## <a name="rules-with-complex-expressions"></a>Szabályok összetett kifejezésekkel

A tagsági szabály összetett kifejezésekből állhat, ahol a tulajdonságok, operátorok és értékek összetettebb formákat vesznek fel. A kifejezések összetettnek minősülnek, ha az alábbiak bármelyike igaz:

* A tulajdonság értékek gyűjteményéből áll; pontosabban, többértékű tulajdonságok
* A kifejezések a -any és -all operátort használják
* A kifejezés értéke maga is lehet egy vagy több kifejezés.

## <a name="multi-value-properties"></a>Többértékű tulajdonságok

A többértékű tulajdonságok azonos típusú objektumok gyűjteményei. Ezeket a tagsági szabályok létrehozásához a -any és -all logikai operátorok használatával lehet létrehozni.

| Tulajdonságok | Értékek | Használat |
| --- | --- | --- |
| hozzárendelt tervek | A gyűjtemény minden objektuma a következő karakterlánctulajdonságokat teszi elérhetővé: capabilityStatus, service, servicePlanId |user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -és assignedPlan.capabilityStatus -eq "Enabled") |
| proxyAddresses| SMTP: alias@domain smtp:alias@domain | (user.proxyAddresses -any\_ ( -contains "contoso")) |

### <a name="using-the--any-and--all-operators"></a>A -any és -all operátorok használata

A -any és az -all operátorok segítségével feltételt alkalmazhat a gyűjtemény egy vagy összes elemére.

* -minden (elégedett, ha a gyűjtemény legalább egy eleme megfelel a feltételnek)
* -all (elégedett, ha a gyűjtemény összes eleme megfelel a feltételnek)

#### <a name="example-1"></a>1. példa

A assignedPlans egy többértékű tulajdonság, amely felsorolja a felhasználóhoz rendelt összes szolgáltatási tervet. A következő kifejezés azokat a felhasználókat jelöli ki, akik az Exchange Online (2. csomag) szolgáltatáscsomaggal (GUID értékként) rendelkeznek, és amelyek szintén Engedélyezett állapotúak:

```
user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled")
```

Az ilyen szabály az összes olyan felhasználó csoportosítására használható, akiknél engedélyezve van az Office 365 (vagy más Microsoft Online Service) funkció. Ezután a házirendek készletével is alkalmazhatja a csoportot.

#### <a name="example-2"></a>2. példa

A következő kifejezés minden olyan felhasználót kijelöl, aki rendelkezik az Intune szolgáltatáshoz társított (az "SCO" szolgáltatásnévvel azonosult) szolgáltatási csomaggal):

```
user.assignedPlans -any (assignedPlan.service -eq "SCO" -and assignedPlan.capabilityStatus -eq "Enabled")
```

### <a name="using-the-underscore-_-syntax"></a>Az aláhúzás\_( ) szintaxis használata

Az aláhúzás (\_) szintaxis megfelel egy adott érték előfordulásainak az egyik többértékű karakterlánc-gyűjtemény tulajdonságában, hogy felhasználókat vagy eszközöket adjon hozzá egy dinamikus csoporthoz. A -any vagy -all operátorokkal együtt használatos.

Íme egy példa az aláhúzás\_( ) használatával egy szabályban a user.proxyAddress alapján tagok hozzáadására (a user.otherMails esetében is ugyanúgy működik). Ez a szabály minden olyan felhasználót hozzáad a csoporthoz, amely "contoso" értéket tartalmazó proxycímmel rendelkező felhasználót tartalmaz.

```
(user.proxyAddresses -any (_ -contains "contoso"))
```

## <a name="other-properties-and-common-rules"></a>Egyéb tulajdonságok és közös szabályok

### <a name="create-a-direct-reports-rule"></a>"Közvetlen jelentések" szabály létrehozása

Létrehozhat egy csoportot, amely tartalmazza a vezető összes közvetlen jelentését. Amikor a vezető közvetlen jelentései a jövőben megváltoznak, a csoport tagsága automatikusan módosul.

A közvetlen jelentések szabály a következő szintaxissal épül fel:

```
Direct Reports for "{objectID_of_manager}"
```

Íme egy példa egy érvényes szabályra, amelyben a "62e19b97-8b3d-4d4a-a106-4ce66896a863" a menedzser objectID azonosítója:

```
Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863"
```

Az alábbi tippek segíthetnek a szabály megfelelő használatában.

- A **kezelőazonosító** a kezelő objektumazonosítója. Megtalálható a menedzser **profiljában**.
- Ahhoz, hogy a szabály működjön, győződjön meg arról, hogy a **Kezelő** tulajdonság megfelelően van beállítva a bérlőfelhasználói számára. Az aktuális értéket a felhasználó **profiljában**ellenőrizheti.
- Ez a szabály csak a vezető közvetlen jelentéseit támogatja. Más szóval nem hozhat létre csoportot a vezető közvetlen jelentéseivel *és* azok jelentéseivel.
- Ez a szabály nem kombinálható más tagsági szabályokkal.

### <a name="create-an-all-users-rule"></a>"Minden felhasználó" szabály létrehozása

Létrehozhat egy csoportot, amely egy bérlő összes felhasználója tagsági szabály használatával. Amikor a felhasználók at ad nak hozzá, vagy eltávolítja a bérlő a jövőben, a csoport tagsága automatikusan módosul.

A "Minden felhasználó" szabály egyetlen kifejezéssel épül fel a -ne operátor és a null érték használatával. Ez a szabály hozzáadja a B2B vendégfelhasználókat és a tagfelhasználókat a csoporthoz.

```
user.objectId -ne null
```
Ha azt szeretné, hogy a csoport kizárja a vendégfelhasználókat, és csak a bérlő tagjait foglalja magában, a következő szintaxist használhatja:

```
(user.objectId -ne null) -and (user.userType -eq “Member”)
```

### <a name="create-an-all-devices-rule"></a>"Minden eszköz" szabály létrehozása

Létrehozhat egy csoportot, amely egy bérlőn belül az összes eszközt egy tagsági szabály használatával. Amikor a jövőben eszközöket ad hozzá vagy távolít el a bérlőből, a csoport tagsága automatikusan módosul.

Az "Összes eszköz" szabály egyetlen kifejezéssel épül fel a -ne operátor és a null érték használatával:

```
device.objectId -ne null
```

## <a name="extension-properties-and-custom-extension-properties"></a>Bővítmény tulajdonságai és egyéni bővítménytulajdonságai

A bővítményattribútumok és az egyéni bővítménytulajdonságok karakterlánc-tulajdonságokként támogatottak a dinamikus tagsági szabályokban. [A bővítményattribútumok](https://docs.microsoft.com/graph/api/resources/onpremisesextensionattributes?view=graph-rest-1.0) szinkronizálása a helyszíni Ablakkiszolgáló AD-ből történik, és az "ExtensionAttributeX" formátumot veszik fel, ahol x 1–15. Íme egy példa egy olyan szabályra, amely egy kiterjesztésattribútumot használ tulajdonságként:

```
(user.extensionAttribute15 -eq "Marketing")
```

[Az egyéni bővítménytulajdonságok](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-feature-directory-extensions) szinkronizálása a helyszíni Windows Server AD rendszerből vagy egy csatlakoztatott `user.extension_[GUID]_[Attribute]`SaaS-alkalmazásból történik, és a következő formátumúak:

* [GUID] az Azure AD-ben a tulajdonságot az Azure AD-ben létrehozó alkalmazás egyedi azonosítója
* Az [Attribútum] a létrehozáskori tulajdonság neve

Egyéni bővítménytulajdonságot használó szabály például:

```
user.extension_c272a57b722d4eb29bfe327874ae79cb_OfficeNumber -eq "123"
```

Az egyéni tulajdonság neve a címtárban található, ha a Graph Explorer segítségével lekérdezi a felhasználó tulajdonságát, és megkeresi a tulajdonság nevét. Emellett most már **kiválaszthatja az Egyéni bővítmény tulajdonságainak beírása** hivatkozást a dinamikus felhasználói csoportszabályszerkesztőben egy egyedi alkalmazásazonosító megadásához, és megkaphatja a dinamikus tagsági szabály létrehozásakor használandó egyéni bővítménytulajdonságok teljes listáját. Ez a lista is frissíthető, hogy az alkalmazás új egyéni bővítménytulajdonságait is lekérhesse.

## <a name="rules-for-devices"></a>Az eszközökre vonatkozó szabályok

Létrehozhat egy szabályt is, amely kijelöli az eszközobjektumokat egy csoporttagsághoz. Nem lehet mind a felhasználók és az eszközök csoporttagok. 

> [!NOTE]
> A **OrganizationalUnit** attribútum már nem szerepel a listában, ezért nem használható. Ezt a karakterláncot az Intune bizonyos esetekben állítja be, de az Azure AD nem ismeri fel, így az attribútum alapján egyetlen eszköz sem kerül hozzá a csoportokhoz.

> [!NOTE]
> A rendszercímkék olyan írásvédett attribútum, amely nem állítható be az Intune-nal.
>
> Windows 10 esetén a deviceOSVersion attribútum helyes formátuma a következő: (device.deviceOSVersion -eq "10.0.17763"). A formázás a Get-MsolDevice PowerShell parancsmaggal érvényesíthető.

A következő eszközattribútumok használhatók.

 Eszközattribútum  | Értékek | Példa
 ----- | ----- | ----------------
 accountEnabled | igaz hamis | (device.accountEnabled -eq igaz)
 displayName | bármilyen karakterlánc érték |(device.displayName -eq "Rob iPhone")
 deviceOSType | bármilyen karakterlánc érték | (device.deviceOSType -eq "iPad") -vagy (device.deviceOSType -eq "iPhone")<br>(device.deviceOSType -tartalmazza az "AndroidEnterprise")<br>(device.deviceOSType -eq "AndroidForWork")
 deviceOSVersion | bármilyen karakterlánc érték | (device.deviceOSVersion -eq "9.1")
 Kategóriák | érvényes eszközkategória-név | (device.deviceCategory -eq "BYOD")
 eszközGyártó | bármilyen karakterlánc érték | (device.deviceManufacturer -eq "Samsung")
 deviceModel | bármilyen karakterlánc érték | (device.deviceModel -eq "iPad Air")
 eszköztulajdonjog | Személyes, Cég, Ismeretlen | (device.deviceOwnership -eq "Vállalat")
 enrollmentProfileName | Apple-eszközregisztrációs profil, eszközregisztráció – Vállalati eszközazonosítók (Android – Kioszk) vagy Windows Autopilot-profilnév | (device.enrollmentProfileName -eq "DEP iPhone")
 isRooted | igaz hamis | (device.isRooted -eq igaz)
 managementType (kezeléstípusa) | MDM (mobileszközökhöz)<br>SZÁMÍTÓGÉP (az Intune PC-ügynök által kezelt számítógépekhez) | (device.managementType -eq "MDM")
 deviceId | érvényes Azure AD-eszközazonosító | (device.deviceId -eq "d4fe7726-5966-431c-b3b8-cddc8fdb717d")
 objectId | érvényes Azure AD-objektumazonosító |  (device.objectId -eq "76ad43c9-32c5-45e8-a272-7b58b58f596d")
 devicePhysicalIds | az Autopilot által használt karakterlánc-érték, például az összes Autopilot eszköz, az OrderID vagy a PurchaseOrderID  | (device.devicePhysicalIDs -any _ -contains "[ZTDId]") (device.devicePhysicalIds -any _ -eq "[OrderID]:179887111881") (device.devicePhysicalIds -any _ -eq "[PurchaseOrderId]:76222342342")
 systemLabels | bármely olyan karakterlánc, amely megfelel az Intune eszköztulajdonságának a Modern Workplace eszközök címkézéséhez | (device.systemLabels -tartalmazza az "M365Managed")(device.systemLabels -contains "M365Managed")

> [!Note]  
> Az eszköztulajdonosi dinamikus csoportok eszközökhöz való létrehozásakor be kell állítania a "Vállalat" értékkel megegyező értéket. Az Intune-ban az eszköz tulajdonjoga inkább vállalati ként jelenik meg. További részletekért olvassa el a [OwnerTypes](https://docs.microsoft.com/intune/reports-ref-devices#ownertypes) hivatkozást. 

## <a name="next-steps"></a>További lépések

Ezek a cikkek további információkat nyújtanak az Azure Active Directory csoportokról.

- [Meglévő csoportok megtekintése](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Új csoport létrehozása és tagok hozzáadása](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Csoportbeállítások kezelése](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Csoporttagságok kezelése](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [A csoportban lévő felhasználók dinamikus szabályainak kezelése](groups-create-rule.md)
