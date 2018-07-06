---
title: Attribútumalapú dinamikus csoporttagsági szabályok az Azure Active Directoryban |} A Microsoft Docs
description: A dinamikus tagsági többek között speciális szabályok létrehozása kifejezés operátorokat és paramétereket támogatja.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 07/05/2018
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: a48dcff6eedc2aa6e8bb6cd5b0668af72259493b
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/06/2018
ms.locfileid: "37869091"
---
# <a name="create-attribute-based-rules-for-dynamic-group-membership-in-azure-active-directory"></a>Dinamikus csoport tagságához Attribútumalapú szabályok létrehozása az Azure Active Directoryban
Az Azure Active Directoryban (Azure AD) az összetett Attribútumalapú dinamikus csoporttagságok engedélyezése egyéni szabályokat hozhat létre. Ez a cikk részletesen az attribútumokat és a felhasználók vagy eszközök számára a dinamikus tagsági szabályok létrehozásához szintaxist. Biztonsági vagy Office 365-csoportok esetében dinamikustagság-szabály beállítására is lehetőség van.

Ha módosítja olyan felhasználó vagy eszköz attribútumai, a rendszer kiértékeli az összes dinamikus szabályok annak ellenőrzéséhez, hogy aktiválja a változás minden olyan csoportot ad hozzá vagy távolít el egy könyvtárban. Ha egy felhasználó vagy az eszköz megfelel a egy egy csoportra vonatkozó szabálynak, kerülnek, a csoport tagjaként. Ezek a szabály már nem felel meg, ha eltávolítja.

> [!NOTE]
> Ez a funkció egy Azure AD Premium P1-licenc szükséges minden egyedi felhasználóhoz, amely egy vagy több dinamikus csoport tagja. Licencek hozzárendelése a felhasználókhoz a számukra a dinamikus csoportok tagjai nem szükséges, de minimálisan hány licencet kell rendelkeznie ahhoz, hogy biztosítsák a minden ilyen felhasználót a bérlőben. Például ha 1000 egyedi felhasználók összesen elérhető összes dinamikus a bérlőben, kell legalább 1000 licencek az Azure AD Premium P1 licenc követelménynek.
>
> Eszközök vagy felhasználók dinamikus csoportot hozhat létre, de nem hozhat létre egy szabályt, amely tartalmazza a felhasználókat és eszközöket egyaránt.
> 
> Jelenleg nincs lehetőség a tulajdonos attribútum alapján eszközcsoport létrehozásához. Eszköz tagsági szabályok csak a címtárban található objektumokhoz eszköz azonnali attribútumai hivatkozhat.

## <a name="to-create-an-advanced-rule"></a>Egy olyan speciális szabályt
1. Jelentkezzen be a [Azure AD felügyeleti központ](https://aad.portal.azure.com) egy olyan fiókkal, amely globális rendszergazda vagy egy felhasználói fiók rendszergazdája.
2. Válassza ki **felhasználók és csoportok**.
3. Válassza ki **összes csoport**, és válassza ki **új csoport**.

   ![Új csoport hozzáadása](./media/groups-dynamic-membership/new-group-creation.png)

4. Az a **csoport** panelen adjon meg egy nevet és leírást az új csoport. Válassza ki a **tagsági típussal** lehet **dinamikus felhasználói** vagy **dinamikus eszköz**, attól függően, hogy hozzon létre egy szabályt, felhasználók vagy eszközök számára, és válassza ki a szeretné**Dinamikus lekérdezés hozzáadása**. A szabály builder használatával egy egyszerű szabályt hozhat létre, vagy írhat egy olyan speciális szabályt, saját magának. Ez a cikk tartalmaz további információt az elérhető felhasználó és eszköz attribútumait, valamint a példák a speciális szabályok.

   ![Dinamikus tagsági szabály hozzáadása](./media/groups-dynamic-membership/add-dynamic-group-rule.png)

5. A szabály létrehozása után válassza ki a **lekérdezés hozzáadása** a panel alján.
6. Válassza ki **létrehozás** a a **csoport** panelen a csoport létrehozásához.

> [!TIP]
> Csoport létrehozása sikertelen lesz, ha a megadott szabály helytelenül formázott vagy érvénytelen volt. A tartalmazó annak magyarázatát, miért érdemes a szabályt nem sikerült feldolgozni a portál jobb felső sarkában megjelenik egy értesítés. Olvassa el, hogy alaposan megismerheti, hogyan, hogy a szabály érvényes, hogy módosítani kell.

## <a name="status-of-the-dynamic-rule"></a>A dinamikus szabály állapota

Láthatja, hogy a tagság feldolgozási állapotát és a egy dinamikus csoport Áttekintés lapján az utolsó frissítés dátumaként.
  
  ![dinamikus csoport állapotának megjelenítése](./media/groups-dynamic-membership/group-status.png)


A következő állapotüzeneteket is látható, a **tagsági feldolgozási** állapota:
* **Kiértékelése**: A csoport módosítása érkezett, és a frissítések kiértékelése megtörténik.
* **Feldolgozási**: frissítések feldolgozása folyamatban van.
* **Frissítés befejezve**: feldolgozása befejeződött, és minden szükséges frissítés történtek-e.
* **Feldolgozási hiba**: Hiba történt a tagsági szabály értékelése és feldolgozása nem fejeződött be.
* **Frissítés szüneteltetve**: frissítések szünetelnek a rendszergazda által a dinamikus tagsági szabály. MembershipRuleProcessingState "Felfüggesztett" értékre van állítva.

A következő állapotüzeneteket is látható, a **tagság utolsó frissítés** állapota:
* &lt;**Dátum és idő**&gt;: a tagság utolsó frissítésekor.
* **Folyamatban lévő**: frissítések jelenleg folyamatban vannak.
* **Ismeretlen**: nem lehet beolvasni a utolsó frissítésének időpontját. Ennek oka az újonnan létrehozott csoport lehet.

Az egy adott csoport tagsági szabály feldolgozása során hiba történik, ha egy riasztás tetején látható a **áttekintőlapján** a csoporthoz. Ha nincs függőben van a dinamikus tagsági frissítések feldolgozási az a bérlőn belüli összes csoportra további majd 24 órán keresztül, egy riasztás jelenik meg tetején **összes csoport**.

![Hiba történt az üzenet feldolgozásakor](./media/groups-dynamic-membership/processing-error.png)

## <a name="constructing-the-body-of-an-advanced-rule"></a>A szervezet egy speciális szabály létrehozása
A speciális szabályt, amely a dinamikus csoporttagságba hozhat létre a lényegében bináris kifejezés, amely három részből áll, és a egy igaz vagy HAMIS eredményt eredményez. A három részből áll a következők:

* Bal oldali paraméter
* Binární operátor
* Jobb oldali állandó

Egy teljes speciális szabály ehhez hasonló: (leftParameter binaryOperator "RightConstant"), ahol a nyitó és záró zárójel teljes bináris kifejezés nem kötelező, dupla idézőjelek között megadása nem kötelező, csak a megfelelő állandó szükséges Ha a karakterlánc, és a bal oldali paraméter szintaxisa user.property. Egy olyan speciális szabályt tartalmazhat egynél több bináris kifejezések elválasztva a - és, - vagy, és – nincs logikai operátorok.

A következő példák a speciális szabály megfelelően:
```
(user.department -eq "Sales") -or (user.department -eq "Marketing")
(user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")
```
A támogatott paraméterek és kifejezés szabály operátorok teljes listáját tekintse meg az alábbi szakaszokban. Az attribútumok Eszközszabályok használni, lásd: [eszközobjektumok szabályok létrehozása attribútumok használatával](#using-attributes-to-create-rules-for-device-objects).

A speciális szabály törzsét teljes hossza nem lehet hosszabb 2048 karakternél.

> [!NOTE]
> Karakterlánc és regex műveletek nagybetűk nem számítanak. Is Null ellenőrzéseket végezhet, használatával *null* konstansként, például user.department - eq *null*.
> Idézőjeleket tartalmazó karakterláncok "érdemes lehet átléptetni" karakter, például user.department - eq \`"Értékesítés".

## <a name="supported-expression-rule-operators"></a>Támogatott kifejezés szabály operátorok
A következő táblázat felsorolja a speciális szabály törzsében használandó minden támogatott kifejezés a szabály operátor és a szintaxis:

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

## <a name="operator-precedence"></a>Műveleti sorrendet

Minden operátor száma nagyobb, kisebb a elsőbbséget alább láthatók. Operátorok ugyanabban a sorban azonos prioritású szerepelnek:
````
-any -all
-or
-and
-not
-eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch -in -notIn
````
Minden operátor is használható, vagy a kötőjel előtag nélkül. Zárójelek között van szükség, csak akkor, ha a sorrendje nem felel meg a követelményeknek.
Példa:
```
   user.department –eq "Marketing" –and user.country –eq "US"
```
a következő azonos:
```
   (user.department –eq "Marketing") –and (user.country –eq "US")
```
## <a name="using-the--in-and--notin-operators"></a>Használja a - az - notIn operátorok és

Ha azt szeretné, a felhasználói attribútum több különböző érték összehasonlítására használhatja a - a vagy - notIn operátorok. Íme egy példa használatával – az In operátor:
```
   user.department -In ["50001","50002","50003",“50005”,“50006”,“50007”,“50008”,“50016”,“50020”,“50024”,“50038”,“50039”,“51100”]
```
Figyeljük meg a "[" és "]" elején és végén az értékek listáját. Ez a feltétel kiértékelése user.department egyenlő a listában található értékek az egyik értéke igaz.


## <a name="query-error-remediation"></a>Lekérdezési hiba szervizelés
Az alábbi táblázat a gyakori hibák és javítsa ki őket annak

| Lekérdezés-elemzési hiba | Hiba használat | Javított használat |
| --- | --- | --- |
| Chyba: Attribútum nem támogatott. |(user.invalidProperty - eq "Value") |(user.department - eq "value")<br/><br/>Ellenőrizze, hogy a rendszer az attribútum a [támogatott tulajdonságok listája](#supported-properties). |
| Chyba: Operátor nem támogatott az attribútum. |(user.accountEnabled – igaz tartalmaz) |(user.accountEnabled - eq true)<br/><br/>Az operátor nem támogatott a tulajdonság típusa (ebben a példában-tartalmaz nem használható írja be a logikai érték). Használja a megfelelő operátorok a tulajdonság típusát. |
| Chyba: Lekérdezésfordítási hiba. |1. (user.department - eq "Értékesítés") (user.department - eq "Marketing")<br/><br/>2. (user.userPrincipalName-egyezik "*@domain.ext") |1. Hiányzó operátor. Használja a - és vagy - vagy két predikátumok csatlakoztatása<br/><br/>(user.department - eq "Értékesítés") – vagy (user.department - eq "Marketing")<br/><br/>2. hiba történt az - használt reguláris kifejezést az felel meg<br/><br/>(user.userPrincipalName-egyezik ". *@domain.ext"), azt is megteheti: (user.userPrincipalName-egyezik "\@domain.ext$")|

## <a name="supported-properties"></a>Támogatott tulajdonságok
A speciális szabályt is használhat az összes felhasználói tulajdonságok a következők:

### <a name="properties-of-type-boolean"></a>Tulajdonságait írja be a logikai érték
Engedélyezett operátorok

* -eq
* -ne

| Tulajdonságok | Megengedett értékek | Használat |
| --- | --- | --- |
| accountEnabled |IGAZ, hamis |user.accountEnabled - eq igaz |
| dirSyncEnabled |IGAZ, hamis |user.dirSyncEnabled - eq igaz |

### <a name="properties-of-type-string"></a>Karakterlánc típusú tulajdonságok
Engedélyezett operátorok

* -eq
* -ne
* -notStartsWith
* -StartsWith
* -tartalmaz
* -notContains
* -felel meg
* -notMatch
* -a
* -notIn

| Tulajdonságok | Megengedett értékek | Használat |
| --- | --- | --- |
| city |Bármilyen karakterlánc értékét, vagy *null értékű* |(user.city - eq "value") |
| ország |Bármilyen karakterlánc értékét, vagy *null értékű* |(felhasználó.ország - eq "value") |
| Cégnév | Bármilyen karakterlánc értékét, vagy *null értékű* | (user.companyName - eq "value") |
| részleg |Bármilyen karakterlánc értékét, vagy *null értékű* |(user.department - eq "value") |
| displayName |Bármilyen karakterlánc típusú értéket |(user.displayName - eq "value") |
| employeeId |Bármilyen karakterlánc típusú értéket |(user.employeeId - eq "value")<br>(user.employeeId - ne *null*) |
| facsimileTelephoneNumber |Bármilyen karakterlánc értékét, vagy *null értékű* |A tagságtípusának a csoport azonnal frissül a listájából. |
| givenName |Bármilyen karakterlánc értékét, vagy *null értékű* |Csoport konvertálása sikertelen lehet, ha a speciális szabály beírt helytelen volt. |
| A benne található annak magyarázatát, miért érdemes a szabályt nem fogadja el a rendszer a portál jobb felső sarkában megjelenik egy értesítés. |Bármilyen karakterlánc értékét, vagy *null értékű* |Olvassa el, hogy alaposan megismerheti, hogyan módosíthatja a szabályt, hogy érvényes. |
| levelezés |Tagság felügyeleti csoport módosítása a PowerShell használatával |A parancsmagok használatával kell a dinamikus csoport tulajdonságainak módosítása előzetes verzióját az Azure AD PowerShell 2-es verziójú. |
| mailNickName |Az előzetes verzióját, a telepítése a PowerShell-galériából. |Íme egy példa, amely egy meglévő csoportot a tagsági felügyeleti kapcsoló funkciók. |
| Ebben a példában van ügyelni arra, hogy megfelelően GroupTypes tulajdonság módosítására, és megőrizheti azokat az értékeket, a dinamikus tagsági kapcsolódnak. |Bármilyen karakterlánc értékét, vagy *null értékű* |Egy csoport statikus tétele: |
| objectId |A csoport dinamikus tétele: |E cikkekben további információk az Azure Active Directory-csoportokon. |
| onPremisesSecurityIdentifier | Hozzon létre egy új csoportot, és a tagok hozzáadása |(user.onPremisesSecurityIdentifier -eq "S-1-1-11-1111111111-1111111111-1111111111-1111111") |
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
| userPrincipalName |Bármilyen karakterlánc típusú értéket |(user.userPrincipalName - eq "alias@domain") |
| userType |tag vendég *null értékű* |(user.userType - eq "Tag") |

### <a name="properties-of-type-string-collection"></a>Típus string gyűjtemény tulajdonságai
Engedélyezett operátorok

* -tartalmaz
* -notContains

| Tulajdonságok | Megengedett értékek | Használat |
| --- | --- | --- |
| otherMails |Bármilyen karakterlánc típusú értéket |(user.otherMails-tartalmaz "alias@domain") |
| proxyAddresses |SMTP: alias@domain smtp: alias@domain |(user.proxyAddresses -contains "SMTP: alias@domain") |

## <a name="multi-value-properties"></a>Többértékű tulajdonságai
Engedélyezett operátorok

* -bármely (elégedett az eredménnyel ha legalább egy elem a gyűjteményben lévő megegyezik a feltétel)
* -az összes (teljesülnek, ha a gyűjteményben lévő összes elem a feltételnek megfelelő)

| Tulajdonságok | Értékek | Használat |
| --- | --- | --- |
| assignedPlans |A gyűjtemény minden egyes objektum a következő karakterlánc-tulajdonságok közzététele: capabilityStatus, szolgáltatás, servicePlanId |user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled") |

Többértékű tulajdonságok azonos típusú objektumok gyűjteményei. Használható – egy olyan feltétel alkalmazására egy, vagy az összes elem a gyűjteményben, illetve bármely és - minden operátorok. Példa:

assignedplans parancsmagot, amely felsorolja a felhasználóhoz rendelt minden szolgáltatáscsomag többértékű tulajdonság. Az alábbi kifejezés választ ki, akik rendelkeznek az Exchange Online (2. csomag) service-csomag, amely egyben az engedélyezési állapotot:

```
user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled")
```

(A Guid azonosító azonosítja az Exchange Online (2. csomag) service-csomag).

> [!NOTE]
> Ez akkor hasznos, ha azt szeretné, azonosíthatja a minden felhasználó, akinek egy Office 365-höz (vagy más Microsoft Online Services) funkció engedélyezve van, például a házirendek egy meghatározott készletével együtt lehetőséget.

A következő kifejezés választ ki semmilyen service-csomag, amely társítva van az Intune szolgáltatással ("SCO" szolgáltatásnév alapján azonosított) rendelkező felhasználók:
```
user.assignedPlans -any (assignedPlan.service -eq "SCO" -and assignedPlan.capabilityStatus -eq "Enabled")
```

## <a name="use-of-null-values"></a>Null értékek használatát

Adjon meg null értéket a szabályban, használhatja a *null* értéket. Ügyeljen arra, hogy nem használja a word idézőjeleket *null* – Ha így tesz, azt fogja értelmezni egy szöveges karakterlánc értéket. A - not operátor nem használható, összehasonlító operátor null értékű. Ha használ, hibaüzenetet kap vagy $null e null használja. Ehelyett használja a - eq vagy - ne. A megfelelő módon való hivatkozáshoz a null értéket a következőképpen történik:
```
   user.mail –ne $null
```

## <a name="extension-attributes-and-custom-attributes"></a>A bővítményattribútumok és egyéni attribútumok
A bővítményattribútumok és az egyéni attribútumokat a dinamikus tagsági szabályok támogatottak.

A bővítményattribútumok szinkronizálva lesznek a helyszíni Windows Server AD, és tegye meg "ExtensionAttributeX", ahol az X értéke 1 – 15 formátumát.
Egy példa bővítményattribútum használó szabály
```
(user.extensionAttribute15 -eq "Marketing")
```
Egyéni attribútumok szinkronizálva lesznek a helyszíni Windows Server AD vagy egy csatlakoztatott SaaS-alkalmazás és a formátuma "user.extension_[GUID]\__ [attribútum]", ahol a [GUID] az alkalmazás által létrehozott egyedi azonosítója az aad-ben a attribútum az AAD és a [attribútum] az attribútum neve megegyezik lett létrehozva.
Vlastní atribut használó szabály például
```
user.extension_c272a57b722d4eb29bfe327874ae79cb__OfficeNumber  
```
Az egyéni attribútum nevében található a címtárban lekérdezi a felhasználó által a attribútum Graph Explorer használatával, és megkeresi az attribútum neve.

## <a name="direct-reports-rule"></a>"A közvetlen beosztottak" szabály
Létrehozhat egy vezető közvetlen beosztottjainak összes tartalmazó csoport. A közvetlen beosztottjait később módosíthatja, ha a csoport tagsága automatikusan módosul.

> [!NOTE]
> 1. A szabály működik, ellenőrizze, hogy a **Kezelőazonosító** tulajdonság megfelelően van-e állítva, a felhasználók a saját bérlőjében. A jelenlegi érték a felhasználó ellenőrizheti a saját **profil lapon**.
> 2. Ez a szabály csak **közvetlen** jelentéseket. Ez jelenleg nem lehetséges egy egymásba ágyazott hierarchiák; csoport létrehozása például egy csoportot, amely tartalmazza a közvetlen beosztottak és jelentéseiket.
> 3. Ez a szabály más speciális szabályokkal nelze kombinovat.

**A csoport konfigurálása**

1. Lépése 1 – 5 szakaszban [a speciális szabályt](#to-create-the-advanced-rule), és válassza ki egy **tagság típusa** , **dinamikus felhasználói**.
2. Az a **dinamikus tagsági szabályok** panelen adja meg a szabály a következő szintaxissal:

    *"{ObjectID_of_manager}" közvetlen beosztottjainak profilját*

    Egy érvényes szabályt egy példát:
```
                    Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863"
```
    where “62e19b97-8b3d-4d4a-a106-4ce66896a863” is the objectID of the manager. The object ID can be found on manager's **Profile tab**.
3. A szabály mentése után minden felhasználó a megadott kezelő azonosító értéket a hozzáadja a csoporthoz.

## <a name="using-attributes-to-create-rules-for-device-objects"></a>Eszközobjektumok szabályok létrehozása attribútumok használatával
Egy szabály, amely eszközobjektumok tagsági kiválaszt egy csoportot is létrehozhat. A következő eszköz attribútumokat is használható.

 Eszköz-attribútum  | Értékek | Példa
 ----- | ----- | ----------------
 accountEnabled | IGAZ, hamis | (device.accountEnabled - eq true)
 displayName | bármilyen karakterlánc típusú értéket |(device.displayName - eq "Rob Iphone")
 deviceOSType | bármilyen karakterlánc típusú értéket | (vagy device.deviceOSType - eq "iPad") – vagy (vagy device.deviceOSType - eq "iPhone")
 deviceOSVersion | bármilyen karakterlánc típusú értéket | (eszköz. OSVersion - eq "9.1")
 deviceCategory | egy érvényes eszközkategória-név | (device.deviceCategory - eq "BYOD")
 deviceManufacturer | bármilyen karakterlánc típusú értéket | (device.deviceManufacturer - eq "Samsung")
 deviceModel | bármilyen karakterlánc típusú értéket | (device.deviceModel - eq "iPad Air")
 deviceOwnership | Személyes, munkahelyi, ismeretlen | (device.deviceOwnership - eq "Vállalati")
 Tartománynév | bármilyen karakterlánc típusú értéket | (device.domainName - eq "contoso.com")
 enrollmentProfileName | Az Apple Eszközregisztrációs profilt, vagy a Windows Autopilot-profil neve | (device.enrollmentProfileName - eq "DEP iPhone-OK")
 isRooted | IGAZ, hamis | (device.isRooted - eq true)
 managementType | Mobileszköz-kezelési (mobileszközök)<br>PC (az Intune PC-ügynökkel kezelt számítógépekhez) | (device.managementType - eq "MDM")
 organizationalUnit | bármilyen karakterlánc típusú értéket megfelelő által egy helyszíni Active Directory a szervezeti egység neve | (device.organizationalUnit - eq "USA számítógépek")
 deviceId | egy érvényes Azure AD-Eszközazonosító | (device.deviceId -eq "d4fe7726-5966-431c-b3b8-cddc8fdb717d")
 objectId | érvényes Azure AD-objektumazonosító |  (device.objectId -eq 76ad43c9-32c5-45e8-a272-7b58b58f596d")



## <a name="changing-dynamic-membership-to-static-and-vice-versa"></a>Dinamikus csoporttagság módosítása statikusra, és ez fordítva is igaz
Akkor lehet módosítani egy csoport tagságát kezeléséről. Ez akkor hasznos, ha meg szeretné tartani csoport neve és azonosítója a rendszeren, hogy a csoport bármely meglévő hivatkozások még érvényesek; Új csoport létrehozása miatt frissíteni kellene az ezeket a hivatkozásokat.

Frissítettük az Azure AD felügyeleti központban nyílik ezt a funkciót. Most már használó ügyfelek átalakíthatják a meglévő csoportokat a dinamikus tagsági hozzárendelt tagságot és fordítva vagy Azure AD felügyeleti központ vagy a PowerShell-parancsmagok alább látható módon.

> [!WARNING]
> Egy meglévő statikus csoportot egy dinamikus csoport módosításakor összes meglévő tag törlődni fog a csoportból, és ezután a tagsági szabály új tagok hozzáadása a program feldolgozza. Alkalmazások vagy erőforrásokhoz való hozzáférés szabályozásához a csoport használata esetén az eredeti tagok elveszthetik a hozzáférést, amíg a tagsági szabály feldolgoz.
>
> Azt javasoljuk, hogy előre az új tagsági szabályt, győződjön meg arról, hogy az új a csoporttagságot a várt módon vizsgálati.

### <a name="using-azure-ad-admin-center-to-change-membership-management-on-a-group"></a>Az Azure AD felügyeleti központ használatával tagsági felügyeleti csoport módosítása 

1. Jelentkezzen be a [Azure AD felügyeleti központ](https://aad.portal.azure.com) egy olyan fiókkal, amely globális rendszergazda vagy egy felhasználói fiók rendszergazdája a bérlőben.
2. Válassza ki **csoportok**.
3. Az a **összes csoport** list, nyissa meg a csoportot, amelyet módosítani szeretne.
4. Válassza ki **tulajdonságok**.
5. A a **tulajdonságok** csoportban válassza a lap egy **tagságtípusának** hozzárendelt (statikus), a dinamikus felhasználó vagy a dinamikus eszköz, a kívánt tagsági típusától függően. A dinamikus tagságot a szabály builder használatával jelölje be egy egyszerű szabályt, vagy írjon egy olyan speciális szabályt, saját magának. 

A következő lépéseket kell egy példa egy csoport módosítása statikusról dinamikus tagsági a felhasználók egy csoportjánál. 

1. Az a **tulajdonságok** a kiválasztott csoportot, válassza az oldal egy **tagság típusa** , **dinamikus felhasználói**, majd válassza az Igen lehetőséget a módosítások elmagyarázza a csoporthoz párbeszédpanelen tagság a folytatáshoz. 
  
   ![Válassza ki a felhasználó dinamikus tagsági típusa](./media/groups-dynamic-membership/select-group-to-convert.png)
  
2. Válassza ki **dinamikus lekérdezés hozzáadása**, majd adja meg a szabály.
  
   ![Adja meg a szabály](./media/groups-dynamic-membership/enter-rule.png)
  
3. A szabály létrehozása után válassza ki a **lekérdezés hozzáadása** az oldal alján.
4. Válassza ki **mentése** a a **tulajdonságok** lap a csoport a módosítások mentéséhez. A **tagságtípusának** a csoport azonnal frissül a listájából.

> [!TIP]
> Csoport konvertálása sikertelen lehet, ha a speciális szabály beírt helytelen volt. A benne található annak magyarázatát, miért érdemes a szabályt nem fogadja el a rendszer a portál jobb felső sarkában megjelenik egy értesítés. Olvassa el, hogy alaposan megismerheti, hogyan módosíthatja a szabályt, hogy érvényes.

### <a name="using-powershell-to-change-membership-management-on-a-group"></a>Tagság felügyeleti csoport módosítása a PowerShell használatával

> [!NOTE]
> A parancsmagok használatával kell a dinamikus csoport tulajdonságainak módosítása **előzetes verzióját** [az Azure AD PowerShell 2-es verziójú](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0). Az előzetes verzióját, a telepítése a [PowerShell-galériából](https://www.powershellgallery.com/packages/AzureADPreview).

Íme egy példa, amely egy meglévő csoportot a tagsági felügyeleti kapcsoló funkciók. Ebben a példában van ügyelni arra, hogy megfelelően GroupTypes tulajdonság módosítására, és megőrizheti azokat az értékeket, a dinamikus tagsági kapcsolódnak.

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
Egy csoport statikus tétele:
```
ConvertDynamicGroupToStatic "a58913b2-eee4-44f9-beb2-e381c375058f"
```
A csoport dinamikus tétele:
```
ConvertStaticGroupToDynamic "a58913b2-eee4-44f9-beb2-e381c375058f" "user.displayName -startsWith ""Peter"""
```
## <a name="next-steps"></a>További lépések
E cikkekben további információk az Azure Active Directory-csoportokon.

* [Tekintse meg a meglévő csoportok](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Hozzon létre egy új csoportot, és a tagok hozzáadása](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Csoport beállításainak kezelése](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Egy csoport tagságát kezelése](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [A csoport dinamikus szabályok kezelése](groups-dynamic-membership.md)
