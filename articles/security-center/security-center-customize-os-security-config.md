---
title: "Az Azure Security Centerben [előzetes verzió] operációsrendszer-biztonsági beállítások testreszabása |} Microsoft Docs"
description: "Ez a cikk útmutatást ad, hogy biztonsági center értékelések testreszabása"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/16/2018
ms.author: terrylan
ms.openlocfilehash: 3af59e1b38e70494dd9dc17e2682d31cf7b7d361
ms.sourcegitcommit: 5108f637c457a276fffcf2b8b332a67774b05981
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2018
---
# <a name="customizing-os-security-configurations-in-azure-security-center-preview"></a>Az Azure Security Centerben [előzetes verzió] operációsrendszer-biztonsági beállítások testreszabása

Ismerje meg, hogyan szabhatja testre az operációs rendszer biztonsági beállításai értékelése az Azure Security Centerben használja ezt a forgatókönyvet.

## <a name="what-are-os-security-configurations"></a>Mik azok az operációs rendszer biztonsági beállításokkal?

Az Azure Security Center figyelők biztonsági konfigurációk használatával több mint 150 ajánlott szabályok a az operációs rendszer korlátozására, beleértve az szabályok kapcsolatos tűzfalak, naplózását, jelszóházirendek és több. Ha a számítógép védtelen konfigurációval rendelkezik, biztonsági ajánlás olyan környezetekben jön létre.

Testreszabási szabályok segítségével a szervezetek számára, hogy szabályozni tudja, melyik konfigurációs lehetőségek állnak a környezetnek jobban megfelelő. Ez a funkció lehetővé teszi, hogy a felhasználók testre szabott assessment házirend, és alkalmazza azt az előfizetést minden esetben gépen.

> [!NOTE]
> - Jelenleg az operációs rendszer biztonsági beállításai testreszabási érhető el a Windows Server 2008-2008R2, 2012 2012R2 rendszerre vonatkozik.
- A konfigurálás érvényes lesz az összes virtuális gépen, és a kapcsolódó számítógépek összes munkaterületek a kijelölt előfizetésben.
- Az operációs rendszer biztonsági beállításai testreszabási csak a Security Center Standard csomagban érhető el.
>
>

Hogyan szabhatja testre az operációs rendszer biztonsági beállításai szabályokat?

Testre szabhatja, hogy az operációs rendszer biztonsági konfigurációja szabályok engedélyezésével és egy adott szabály letiltása, a meglévő szabály kívánt beállításainak megváltoztatása, és a támogatott szabálytípusok (a beállításjegyzék, a naplózási házirend és a biztonsági házirend) alapuló új szabály hozzáadása. A kívánt beállítás jelenleg egy pontos számnak kell lennie.

Új szabályokat kell azonos formátum és struktúra, egyéb meglévő szabályok ugyanabba a típusba tartozik.

> [!NOTE]
> Testre szabhatja az operációs rendszer biztonsági beállításokkal, az előfizetés tulajdonosa, előfizetés közreműködő vagy biztonsági rendszergazdai szerepkört kell hozzárendelni.
>
>

## <a name="customize-security-configuration"></a>Biztonsági beállítások testreszabása

Az alapértelmezett operációs rendszer biztonsági konfigurációja a biztonsági központban testreszabása:

1.  Nyissa meg a **Security Center** irányítópultját.

2.  Az a Security Center fő menüben válassza a **biztonsági házirend**.  **A Security Center - biztonsági házirend** nyílik meg.

3.  Válassza ki az előfizetést, hajtsa végre a testreszabás, a kívánt.

    ![](media/security-center-customize-os-security-config/open-security-policy.png)

4. A **csoportházirend-összetevők**, jelölje be **biztonsági beállításokkal szerkesztése**.

6.  **Szerkessze a biztonsági beállításokkal** nyílik meg. Kövesse a képernyőn letöltéséhez, szerkesztheti, és töltse fel a módosított fájlt a kijelölt.

    ![](media/security-center-customize-os-security-config/blade.png)

  > [!NOTE]
  > Alapértelmezés szerint a letöltött konfigurációs fájl nem *json* formátumban. Lépjen a következő fájl módosításához útmutatást, [a konfigurációs fájljának testreszabása](#customize-the-configuration-file).
  >

7. A fájl mentése sikeresen megtörtént, miután a konfiguráció alkalmazása az összes virtuális gépen és a kapcsolódó számítógépek összes munkaterületek a kijelölt előfizetésben. Ez a folyamat eltarthat egy ideig, általában néhány percet, de azt is több időt vesz igénybe óta infrastruktúra méretétől függ. Válassza ki **mentése** véglegesíti a módosítást, ellenkező esetben a házirendet a rendszer nem tárolja.

    ![](media/security-center-customize-os-security-config/save-successfully.png)

Bármikor, visszaállíthatja az aktuális házirend-konfigurációt az alapértelmezett házirend állapot kiválasztásával a **alaphelyzetbe** beállítást **szerkesztése az operációs rendszer biztonsági beállításai szabályok**. Ha ezt a lehetőséget választja, a következő előugró riasztás jelenik meg. Válassza ki **Igen** megerősítéséhez.

![](media/security-center-customize-os-security-config/edit-alert.png)

## <a name="customize-the-configuration-file"></a>A konfigurációs fájljának testreszabása

A testreszabási fájlban minden operációs rendszer támogatott verziója van egy szabálykészlet (szabálykészletben). Minden egyes szabályok készletét rendelkezik a saját nevét és az egyedi azonosító, a következő példában látható módon:

![](media/security-center-customize-os-security-config/custom-file.png)

> [!NOTE]
> Ez a fájl szerkesztették a Visual Studio használatával, de használhatja a Jegyzettömb mindaddig, amíg a JSON Viewer beépülő modul telepítve van.
>
>

Ez a fájl szerkesztése, amikor egy szabály vagy az összes módosíthatja. Minden egyes szabálykészletben tartalmaz egy *szabályok* a szabályokat, 3 szabálykategóriák bontásban tartalmazó szakasz: beállításjegyzék, a naplózási házirend és a biztonsági házirend, mint az alábbi megjelenítése:

![](media/security-center-customize-os-security-config/rules-section.png)

Saját kategóriákhoz attribútumok rendelkezik. A meglévő szabályok a következő megfelelően módosításokat végezheti el:

- expectedValue: Ez az attribútum mező adattípusának egyeznie kell a támogatott értékek száma minden egyes szabály típusa, például:

  - baselineRegistryRules: az értéket meg kell felelnie a [regValueType](https://msdn.microsoft.com/library/windows/desktop/ms724884) szabályban meghatározott.

  - baselineAuditPolicyRules: értékének kell karakterlánc-érték, a következők egyikét:

    - Sikeres és sikertelen műveletek

    - Sikeres

  - baselineSecurityPolicyRules: értékének kell karakterlánc-érték, a következők egyikét:

    - "Senki"

    - Listája engedélyezett felhasználói csoportok, például: "A rendszergazdák, a biztonságimásolat-felelősök"

-   állapot: karakterlánc, amely a "Letiltott" vagy "Enabled" beállításokat is tartalmazhat. A private Preview verziójára ezen verziója a karakterlánca kis-és nagybetűket.

Ezek a beállítható, hogy csak mezőket. Ha megsértették a formátuma vagy mérete, nem fogja tudni menteni a módosítást. A következő hibaüzenetet akkor fordul elő, ha a fájl nem dolgozható fel:

![](media/security-center-customize-os-security-config/invalid-json.png)

Lásd: [hibakódok](#error-codes) lehetséges hibák listája.

Az alábbiakban néhány olyan ezek a szabályok rendelkezik. Attribútumok "expectedValue" és "állapot" módosíthatja:

**Szabályok szakasz:** baselineRegistryRules
```
{

    "hive": "LocalMachine",
    "regValueType": "Int",
    "keyPath":
    "System\\\\CurrentControlSet\\\\Services\\\\LanManServer\\\\Parameters",
    "valueName": "restrictnullsessaccess",
    "ruleId": "f9020046-6340-451d-9548-3c45d765d06d",
    "originalId": "0f319931-aa36-4313-9320-86311c0fa623",
    "cceId": "CCE-10940-5",
    "ruleName": "Network access: Restrict anonymous access to Named Pipes and
    Shares",
    "ruleType": "Registry",
    "expectedValue": "1",
    "severity": "Warning",
    "analyzeOperation": "Equals",
    "source": "Microsoft",
    "state": "Disabled"

}
```

**Szabályok szakasz:** baselineAuditPolicyRules
```
{
"auditPolicyId": "0cce923a-69ae-11d9-bed3-505054503030",
"ruleId": "37745508-95fb-44ec-ab0f-644ec0b16995",
"originalId": "2ea0de1a-c71d-46c8-8350-a7dd4d447895",
"cceId": "CCE-11001-5",
"ruleName": "Audit Policy: Account Management: Other Account Management Events",
"ruleType": "AuditPolicy",
"expectedValue": "Success and Failure",
"severity": "Critical",
"analyzeOperation": "Equals",
"source": "Microsoft",
"state": "Enabled"
},
```

**Szabályok szakaszok:** baselineSecurityPolicyRules
```
{
"sectionName": "Privilege Rights",
"settingName": "SeIncreaseWorkingSetPrivilege",
"ruleId": "b0ec9d5e-916f-4356-83aa-c23522102b33",
"originalId": "b61bd492-74b0-40f3-909d-36b9bf54e94c",
"cceId": "CCE-10548-6",
"ruleName": "Increase a process working set",
"ruleType": "SecurityPolicy",
"expectedValue": "Administrators, Local Service",
"severity": "Warning",
"analyzeOperation": "Equals",
"source": "Microsoft",
"state": "Enabled"
},
```

Néhány szabály, amely az operációs rendszer különböző kétszer van. Ismétlődő szabályok az ugyanazon "originalId" rendelkeznek.

## <a name="adding-a-new-custom-rule"></a>Új egyéni szabály hozzáadása

Létrehozhat egy új szabályt is. Mielőtt létrehozna egy új szabályt, tartsa szem előtt a következő korlátozások vonatkoznak:

-   Sémaverzió *baselineId* és *baselineName* nem módosítható.

-   Nem lehet eltávolítani a RuleSet.

-   Nem adható hozzá a RuleSet.

-   A szabályok (beleértve az alapértelmezett szabályok) megengedett maximális száma: 1000 szabályok.

Új egyéni szabályok jelöli egy új egyéni forrás (! = "Microsoft"). A *ruleId* mező lehet null vagy üres. Ha üres, a Microsoft hoz létre egyet. Ha nem üres, akkor egyedi érvényes GUID összes szabályt (alapértelmezett és egyéni) kell rendelkeznie. Tekintse át az alábbi kapcsolatos alapvető mezők korlátait:

-   *originalId* -lehet null vagy üres. Ha *originalId* értéke nem üres, meg kell érvényes GUID.

-   *cceId* -lehet null vagy üres. Ha *cceId* értéke nem üres, ennek egyedinek kell lennie.

-   *ruleType* - egy a: beállításjegyzék, AuditPolicy vagy SecurityPolicy.

-   *Súlyossági* - egy a: ismeretlen, kritikus, figyelmeztető vagy tájékoztató.

-   *analyzeOperation* -egyenlő kell lennie.

-   *auditPolicyId* -érvényes GUID kell lennie.

-   *regValueType* -valamelyike lehet: Int, Long, karakterlánc vagy MultipleString.

> [!NOTE]
> Hive kell *LocalMachine*.
>
>

Példa: egy új egyéni szabályt:

**Registry**:
```
    {
    "hive": "LocalMachine",
    "regValueType": "Int",
    "keyPath":
    "System\\\\CurrentControlSet\\\\Services\\\\Netlogon\\\\MyKeyName",
    "valueName": "MyValueName",
    "originalId": "",
    "cceId": "",
    "ruleName": "My new registry rule”, "baselineRuleType": "Registry",
    "expectedValue": "123", "severity": "Critical",
    "analyzeOperation": "Equals",
    "source": "MyCustomSource",
    "state": "Enabled"
   }
```
**Biztonsági házirend**:
```
{

   "sectionName": "Privilege Rights",
   "settingName": "SeDenyBatchLogonRight",
   "originalId": "",
   "cceId": "",
   "ruleName": "My new security policy rule", "baselineRuleType":
   "SecurityPolicy",
   "expectedValue": "Guests",
   "severity": "Critical",
   "analyzeOperation": "Equals", "source": " MyCustomSource ",
   "state": "Enabled"
   }
```
**Naplózási házirend:**
```
   {
   "auditPolicyId": "0cce923a-69ae-11d9-bed3-505054503030",
   "originalId": "",
   "cceId": "",
   "ruleName": " My new audit policy rule ", "baselineRuleType": "AuditPolicy",
   "expectedValue": " Failure",
   "severity": "Critical",
   "analyzeOperation": "Equals", "source": " MyCustomSource ",
   "state": "Enabled"
   }
```

## <a name="file-upload-failures"></a>Fájl feltöltése sikertelen

Ha az elküldött konfigurációs fájl érvénytelen értéket vagy a formázást hibák miatt, a hiba jelenik meg a hiba. Javítsa ki, és a javított konfigurációs fájl újraküldés előtt javítsa a hibákat a részletes hibaüzenetek csv jelentések töltheti le.

![](media/security-center-customize-os-security-config/invalid-configuration.png)

Példa: a hibák fájlt:

![](media/security-center-customize-os-security-config/errors-file.png)

## <a name="error-codes"></a>Hibakódok

Az alábbi lista az összes lehetséges hibákat tartalmaz:

| **Hiba történt**                                | **Leírás**                                                                                                                              |
|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------|
| BaselineConfiguratiohSchemaVersionError  | Található, hogy a "sémaverzióval" tulajdonság érvénytelen vagy üres. Az értéket kell beállítani: "{0}".                                                         |
| BaselineInvalidStringError               | "{0}" tulajdonság nem tartalmazhat "\\n".                                                                                                         |
| BaselineNullRuleError                    | Alapkonfiguráció konfigurációs szabályok listát "null" értéket a szabályt tartalmaz.                                                                         |
| BaselineRuleCceIdNotUniqueError          | CCE-ID "{0}" osztálynév nem egyedi.                                                                                                                  |
| BaselineRuleEmptyProperty                | : "{0}" tulajdonság hiányzik vagy érvénytelen.                                                                                                       |
| BaselineRuleIdNotInDefault               | A szabály a "Microsoft" tulajdonságra van, de nem található a Microsoft alapértelmezett szabálykészletben.                                                   |
| BaselineRuleIdNotUniqueError             | Szabály azonosítója nem egyedi.                                                                                                                       |
| BaselineRuleInvalidGuid                  | A(z) "{0}' található tulajdonság érvénytelen. Értéke nem érvényes Guid.                                                                             |
| BaselineRuleInvalidHive                  | Hive LocalMachine kell lennie.                                                                                                                   |
| BaselineRuleNameNotUniqueError           | A szabály neve nem egyedi.                                                                                                                 |
| BaselineRuleNotExistInConfigError        | A szabály nem található az új konfigurációt. A szabály nem törölhető.                                                                     |
| BaselineRuleNotFoundError                | A szabály nem található az alapértelmezett eredeti szabályok beállítása.                                                                                        |
| BaselineRuleNotInPlace                   | A szabály típusa {0} egy alapértelmezett szabályának, és szerepel-e {1}.                                                                       |
| BaselineRulePropertyTooLong              | : "{0}" tulajdonság túl hosszú. A megengedett maximális hossz: {1}.                                                                                        |
| BaselineRuleRegTypeInvalidError          | A várt érték "{0}" a megadott beállításazonosító típusa nem egyezik.                                                              |
| BaselineRulesetAdded                     | A(z) "{0}' azonosítójú szabályok beállítása nem található az alapértelmezett beállítás. Nem adható hozzá a szabályoknak.                                               |
| BaselineRulesetIdMustBeUnique            | Az adott alaptervbe szabályok beállítása "{0}" egyedinek kell lennie.                                                                                           |
| BaselineRulesetNotFound                  | A(z) "{0}' azonosítójú beállított szabályok és a neve"{1}"nem található a megadott konfigurációs. Szabályok beállítása nem lehet törölni.                                |
| BaselineRuleSourceNotMatch               | A(z) "{0}' azonosítójú szabálya már definiálva van.                                                                                                       |
| BaselineRuleTypeDoesntMatch              | Alapértelmezett szabály típusa: "{0}".                                                                                                              |
| BaselineRuleTypeDoesntMatchError         | A szabály a tényleges típus: {0}, de ruleType tulajdonság értéke: {1}.                                                                          |
| BaselineRuleUnpermittedChangesError      | Csak "expectedValue" és "állapot" tulajdonságok módosítására megengedettek.                                                                       |
| BaselineTooManyRules                     | Egyéni szabályok engedélyezett maximális {0} szabály. A megadott konfigurációs {1} szabályokat tartalmazza. (alapértelmezett szabály {2} + {3} egyéni szabályokat. |
| ErrorNoConfigurationStatus               | Nincs konfiguráció állapota található. Akkor határozza meg a szükséges konfiguráció állapota – a "Default" vagy "Custom".                                    |
| ErrorNonEmptyRulesetOnDefault            | Konfiguráció állapota az alapértelmezett érték. BaselineRulesets lista NULL értékű vagy üresnek kell lennie.                                                          |
| ErrorNullRulesetsPropertyOnCustom        | A megadott konfigurációs állapot "Custom", de a "baselineRulesets" tulajdonság értéke null vagy üres.                                             |
| ErrorParsingBaselineConfig               | A megadott konfiguráció érvénytelen. Egy vagy több megadott rendelkezik null értékű vagy érvénytelen típusú.                                  |
| ErrorParsingIsDefaultProperty            | A megadott "configurationStatus" "{0}" értéke érvénytelen. Az érték csak a "Default" vagy "Custom" lehet.                                         |
| InCompatibleViewVersion                  | Verzió megtekintése: {0} a munkaterület-típus nem támogatott.                                                                                   |
| InvalidBaselineConfigurationGeneralError | A megadott alapkonfigurációjának típus érvényesítése egy vagy több hiba is található.                                                          |
| ViewConversionError                      | Nézet munkaterületről támogatott régebbi verzió. Sikertelen átalakítás megtekintése: {0}                                                                 |

Ha nem rendelkezik megfelelő engedélyekkel, egy általános hiba jelenhet meg:

![](media/security-center-customize-os-security-config/general-failure-error.png)

## <a name="next-steps"></a>További lépések
Ez a cikk a Security Center az operációs rendszer biztonsági beállításai értékelések testreszabása bemutatta. A konfigurációs szabályok és szervizelésével kapcsolatos további tudnivalókért lásd:

- [A Security Center közös konfigurációs azonosítók és alapkonfigurációs szabályok](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335).
- A Security Center CCE (közös konfigurációs enumerálási) használatával rendelje hozzá a konfigurációs szabályok egyedi azonosítót. Látogasson el a [CCE](https://nvd.nist.gov/config/cce/index) webhelyen olvashat.
- [Biztonsági beállításokkal szervizelése](security-center-remediate-os-vulnerabilities.md) bemutatja, hogyan biztonsági rések feloldása, amikor az operációs rendszer konfigurációja nem egyezik meg a javasolt biztonsági konfigurációs szabályok.
