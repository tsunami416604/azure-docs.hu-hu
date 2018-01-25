---
title: "Az Azure Security Centerben (előzetes verzió) operációsrendszer-biztonsági beállítások testreszabása |} Microsoft Docs"
description: "Ez a cikk bemutatja, hogyan kell biztonsági center értékelések testreszabása"
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
ms.openlocfilehash: d42dd4ba150a28109d6bb3f7c2281d07b21a366e
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2018
---
# <a name="customize-os-security-configurations-in-azure-security-center-preview"></a>Az Azure Security Centerben (előzetes verzió) operációsrendszer-biztonsági beállítások testreszabása

Ez a forgatókönyv bemutatja, hogyan szabhatja testre az operációs rendszer biztonsági konfiguráció értékelése az Azure Security Centerben.

## <a name="what-are-os-security-configurations"></a>Mik azok az operációs rendszer biztonsági beállításokkal?

Az Azure Security Center figyelők biztonsági konfigurációk az operációs rendszer, beleértve a korlátozására szabályok több mint 150 ajánlott szabályok alkalmazásával tűzfalak, naplózását, jelszóházirendek és egyéb kapcsolódó. Ha a számítógép védtelen konfigurációval rendelkezik, a Security Center biztonsági ajánlás olyan környezetekben állít elő.

A szabályok testreszabásával szervezetek szabályozhatja, hogy mely beállítási lehetőségek állnak a környezetnek jobban megfelelő. Testre szabott assessment házirend, és alkalmazza azt az előfizetést minden esetben gépen.

> [!NOTE]
> - Az operációs rendszer biztonsági beállításai testreszabása jelenleg a Windows Server 2008, 2008 R2, 2012 és 2012 R2 operációs rendszereken csak.
> - A konfigurálás érvényes lesz minden olyan virtuális gépek és a kijelölt előfizetéshez tartozó összes munkaterületek csatlakozó számítógépek.
> - Az operációs rendszer biztonsági konfiguráció testreszabási csak a Security Center standard csomagban érhető el.
>
>

Testre szabhatja, hogy az operációs rendszer biztonsági konfigurációs szabályok engedélyezésével és egy adott szabály letiltása, a kívánt beállítást a meglévő szabály módosítása vagy a támogatott szabálytípusok (a beállításjegyzék, a naplózási házirend és a biztonsági házirend) alapuló új szabály hozzáadása. A kívánt beállítás jelenleg egy pontos számnak kell lennie.

Új szabályok azonos típusú egyéb meglévő szabályok azonosnak kell lennie a azonos formátum és struktúrája.

> [!NOTE]
> Operációsrendszer-biztonsági beállítások testreszabása, akkor hozzá kell rendelni a szerepe *előfizetés tulajdonosa*, *előfizetés közreműködői*, vagy *biztonsági rendszergazda*.
>
>

## <a name="customize-the-default-os-security-configuration"></a>Az alapértelmezett operációs rendszer biztonsági beállításai testreszabása

Az alapértelmezett operációs rendszer biztonsági beállításai a Security Center testreszabása, tegye a következőket:

1.  Nyissa meg a **Security Center** irányítópultját.

2.  A bal oldali panelen válassza ki a **biztonsági házirend**.  
    A **Security Center - biztonsági házirend** ablak nyílik meg.

    ![Biztonsági házirendek listája](media/security-center-customize-os-security-config/open-security-policy.png)

3.  Válassza ki az előfizetést, hajtsa végre a testreszabás, a kívánt.

4. A **csoportházirend-összetevők**, jelölje be **biztonsági beállításokkal szerkesztése**.  
    A **biztonsági beállításokkal szerkesztése** ablak nyílik meg. 
    
    ![A "Biztonsági konfigurációk módosítása" ablak](media/security-center-customize-os-security-config/blade.png)

5. A jobb oldali ablaktáblában utasításai a letöltés, szerkesztése és a módosított fájlt feltölteni.

   > [!NOTE]
   > Alapértelmezés szerint a letöltött konfigurációs fájl nem *json* formátumban. Ez a fájl módosításával kapcsolatos utasításokért ugorjon [a konfigurációs fájljának testreszabása](#customize-the-configuration-file).
   >

   Miután már sikeresen mentette a fájlt, a konfiguráció alkalmazása az összes virtuális gépek és az előfizetéshez tartozó összes munkaterületek csatlakozó számítógépek. A folyamat általában néhány percet vesz igénybe, de infrastruktúra méretétől függően több időt vesz igénybe. 

6. A módosítás véglegesítése, válassza ki a **mentése**. Ellenkező esetben a házirendet a rendszer nem tárolja.

    ![A Mentés gombra](media/security-center-customize-os-security-config/save-successfully.png)

Bármikor visszaállíthatja az aktuális házirend-konfigurációt az alapértelmezett állapotába. Az ehhez a **szerkesztése operációs rendszer biztonsági konfigurációs szabályok** ablakban válassza ki **alaphelyzetbe**. Erősítse meg a beállítás kiválasztásával **Igen** a megerősítő előugró ablakban.

![A az ablak-visszaállítási megerősítése](media/security-center-customize-os-security-config/edit-alert.png)

## <a name="customize-the-configuration-file"></a>A konfigurációs fájljának testreszabása

A testreszabási fájlban minden támogatott operációs rendszer verziója tartozik egy szabály vagy a ruleset. Minden egyes szabálykészletben rendelkezik a saját nevét és az egyedi azonosító, a következő példában látható módon:

![A ruleset nevét és Azonosítóját](media/security-center-customize-os-security-config/custom-file.png)

> [!NOTE]
> Ez a példa fájl a Visual Studio szerkesztették, de akkor is használhatja az Jegyzettömb, ha a JSON megjelenítőből beépülő modul telepítve van.
>
>

A testreszabási fájl szerkesztése, amikor egy szabály vagy az összes módosíthatja. Minden egyes szabálykészletben tartalmaz egy *szabályok* három kategóriába soroltuk el szakasz: beállításjegyzék, a naplózási házirend és a biztonsági házirend, ahogy az itt látható:

![Három szabálykészletben kategóriák](media/security-center-customize-os-security-config/rules-section.png)

Saját kategóriákhoz attribútumok rendelkezik. Módosíthatja a következő attribútumokat:

- **expectedValue**: Ez az attribútum mező adattípusának egyeznie kell a támogatott értékek száma *szabály típusa*, például:

  - **baselineRegistryRules**: az értéket meg kell felelnie a [regValueType](https://msdn.microsoft.com/library/windows/desktop/ms724884) szabályban definiált.

  - **baselineAuditPolicyRules**: használja a következő karakterlánc-értékek egyikét:

    - *A sikeres és sikertelen*

    - *Success*

  - **baselineSecurityPolicyRules**: használja a következő karakterlánc-értékek egyikét:

    - *Senki*

    - Listája engedélyezett felhasználói csoportok, például: *rendszergazdák*, *biztonságimásolat-felelősök*

-   **állapot**: A karakterlánc a beállításokat is tartalmazhat *letiltott* vagy *engedélyezve*. A private Preview verziójára ezen verziója a karakterlánca kis-és nagybetűket.

Ezek a beállítható, hogy csak mezőket. Ha megsértették a formátuma vagy mérete, nem fogja tudni menteni a módosítást. A következő hibaüzenetet akkor fordul elő, ha a fájl nem dolgozható fel:

![Biztonsági konfigurációs hibaüzenet](media/security-center-customize-os-security-config/invalid-json.png)

Más lehetséges hibákat listájáért lásd: [hibakódok](#error-codes).

A következő három szakasz a fenti szabályok példákat tartalmaz. A *expectedValue* és *állapot* attribútumok módosítható.

**baselineRegistryRules**
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

**baselineAuditPolicyRules**
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
    }
```

**baselineSecurityPolicyRules**
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
    }
```

Néhány szabály az operációs rendszer különböző ismétlődik. Ismétlődő szabályok rendelkeznek azonos *originalId* attribútum.

## <a name="create-custom-rules"></a>Egyéni szabályok létrehozása

Létrehozhat új szabályokat is. Mielőtt létrehozna egy új szabályt, vegye figyelembe a következő korlátozások vonatkoznak:

-   Sémaverzió *baselineId* és *baselineName* nem módosítható.

-   Nem lehet eltávolítani a RuleSet.

-   Nem adható hozzá a RuleSet.

-   A szabályok (beleértve az alapértelmezett szabályok) megengedett maximális száma érték 1000.

Új egyéni szabályok jelöli egy új egyéni forrás (! = "Microsoft"). A *ruleId* mező lehet null vagy üres. Ha üres, a Microsoft hoz létre egyet. Ha nem üres, egy érvényes GUID Azonosítót, amely egyedi összes szabályt (alapértelmezett és egyéni) között kell legyen. Tekintse át a következő korlátozások vonatkoznak a core mezők:

-   **originalId**: null vagy üres lehet. Ha *originalId* értéke nem üres, meg kell érvényes GUID.

-   **cceId**: null vagy üres lehet. Ha *cceId* értéke nem üres, ennek egyedinek kell lennie.

-   **ruleType**: (válasszon egyet) beállításjegyzékbeli, AuditPolicy vagy SecurityPolicy.

-   **Súlyossági**: (válasszon egyet) ismeretlen, kritikus, figyelmeztető vagy tájékoztató.

-   **analyzeOperation**: kell *egyenlő*.

-   **auditPolicyId**: érvényes GUID azonosítónak kell lennie.

-   **regValueType**: (válasszon egyet): Int, Long, karakterlánc vagy MultipleString.

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
**Naplózási házirend**:
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

Ha az elküldött konfigurációs fájl érvénytelen értéket vagy a formázást hibák miatt, a hiba jelenik meg. Javítsa ki és javítsa ki a hibákat, mielőtt egy javított konfigurációs fájl küldje el újra a részletes hibaüzenetek .csv jelentések töltheti le.

!["Mentés művelete nem sikerült" hibaüzenet](media/security-center-customize-os-security-config/invalid-configuration.png)

Egy hiba történt a fájl példát:

![Hiba történt a fájl például](media/security-center-customize-os-security-config/errors-file.png)

## <a name="error-codes"></a>Hibakódok

Az alábbi táblázatban felsorolt összes lehetséges hibákat:

| **Hiba történt**                                | **Leírás**                                                                                                                              |
|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------|
| BaselineConfiguratiohSchemaVersionError  | A tulajdonság *schemaVersion* található érvénytelen vagy üres. Az értéket úgy kell beállítani, *{0}*.                                                         |
| BaselineInvalidStringError               | A tulajdonság *{0}* nem tartalmazhat  *\\ n* .                                                                                                         |
| BaselineNullRuleError                    | Az alapkonfiguráció konfigurációs szabályok listája értéket a szabályt tartalmaz *null*.                                                                         |
| BaselineRuleCceIdNotUniqueError          | A CCE-ID *{0}* eleme nem egyedi.                                                                                                                  |
| BaselineRuleEmptyProperty                | A tulajdonság *{0}* hiányzik vagy érvénytelen.                                                                                                       |
| BaselineRuleIdNotInDefault               | A szabály van a forrástulajdonság *Microsoft* , de nem található a Microsoft alapértelmezett szabálykészletben.                                                   |
| BaselineRuleIdNotUniqueError             | A szabály azonosítója nem egyedi.                                                                                                                       |
| BaselineRuleInvalidGuid                  | A tulajdonság *{0}* érvénytelennek bizonyult. Értéke nem érvényes GUID.                                                                             |
| BaselineRuleInvalidHive                  | A hive LocalMachine kell lennie.                                                                                                                   |
| BaselineRuleNameNotUniqueError           | A szabály neve nem egyedi.                                                                                                                 |
| BaselineRuleNotExistInConfigError        | A szabály nem található az új konfigurációt. A szabály nem törölhető.                                                                     |
| BaselineRuleNotFoundError                | A szabály nem található az alapértelmezett alapterv szabálykészletben.                                                                                        |
| BaselineRuleNotInPlace                   | A szabály típusa {0} egy alapértelmezett szabályának, és szerepel-e {1}.                                                                       |
| BaselineRulePropertyTooLong              | A tulajdonság *{0}* túl hosszú. A megengedett maximális hossz: {1}.                                                                                        |
| BaselineRuleRegTypeInvalidError          | A várt érték *{0}* a definiált beállításazonosító típusa nem egyezik meg.                                                              |
| BaselineRulesetAdded                     | A ruleset azonosítójú *{0}* nem található az alapértelmezett beállítás. A ruleset nem vehető fel.                                               |
| BaselineRulesetIdMustBeUnique            | Az adott alaptervbe szabálykészletben *{0}* egyedinek kell lennie.                                                                                           |
| BaselineRulesetNotFound                  | A ruleset azonosítójú *{0}* és name *{1}* nem található a megadott konfiguráció. A ruleset nem lehet törölni.                                |
| BaselineRuleSourceNotMatch               | A szabály azonosítójú *{0}* már definiálva van.                                                                                                       |
| BaselineRuleTypeDoesntMatch              | Az alapértelmezett szabály típus *{0}*.                                                                                                              |
| BaselineRuleTypeDoesntMatchError         | A szabály a tényleges típus *{0}*, de a *ruleType* tulajdonság *{1}*.                                                                          |
| BaselineRuleUnpermittedChangesError      | Csak *expectedValue* és *állapot* módosítani tulajdonságok megengedettek.                                                                       |
| BaselineTooManyRules                     | Egyéni szabályok megengedett maximális számának {0} szabály. A megadott konfigurációs {1} szabályok, {2} alapértelmezett szabályok és testre szabott {3} szabályokat tartalmazza. |
| ErrorNoConfigurationStatus               | Nincs konfiguráció állapota található. Adja meg a kívánt konfiguráció állapota: *alapértelmezett* vagy *egyéni*.                                    |
| ErrorNonEmptyRulesetOnDefault            | A konfiguráció állapota az alapértelmezett érték. A *BaselineRulesets* lista NULL értékű vagy üres lehet.                                                          |
| ErrorNullRulesetsPropertyOnCustom        | A megadott konfigurációs állapot *egyéni* , de a *baselineRulesets* tulajdonsága null értékű vagy üres.                                             |
| ErrorParsingBaselineConfig               | A megadott konfiguráció érvénytelen. A megadott értékek közül rendelkezik null értékű vagy érvénytelen típusú.                                  |
| ErrorParsingIsDefaultProperty            | A megadott *configurationStatus* érték *{0}* érvénytelen. Az érték csak lehet *alapértelmezett* vagy *egyéni*.                                         |
| InCompatibleViewVersion                  | A verzió megtekintése *{0}* van *nem* adott munkaterület típus támogatott.                                                                                   |
| InvalidBaselineConfigurationGeneralError | A megadott alapkonfigurációjának típus érvényesítése egy vagy több hiba is található.                                                          |
| ViewConversionError                      | A nézet nem régebbi verziójú, mint a munkaterület támogatja. Megtekintheti a konverzió sikertelen: {0}.                                                                 |

Ha nem rendelkezik megfelelő engedélyekkel, egy általános hiba kaphat, ahogy az itt látható:

!["Mentés művelete nem sikerült" hibaüzenet](media/security-center-customize-os-security-config/general-failure-error.png)

## <a name="next-steps"></a>További lépések
Ez a cikk az operációs rendszer biztonsági konfiguráció értékelések a Security Center testreszabásának módját tárgyalja. A konfigurációs szabályok és szervizelésével kapcsolatos további tudnivalókért lásd:

- [A Security Center közös konfigurációs azonosítók és alapkonfigurációs szabályok](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335).
- A Security Center egyedi azonosítók hozzárendelése a konfigurációs szabályok közös konfigurációs enumerálása (CCE) használja. További információkért lásd: [CCE](https://nvd.nist.gov/config/cce/index).
- Hárítsa el a biztonsági rések, ha az operációs rendszer konfigurációja nem felel meg a javasolt biztonsági konfigurációs szabályok, lásd: [biztonsági beállításokkal szervizelése](security-center-remediate-os-vulnerabilities.md).
