---
title: Az Azure Security Centerben (előzetes verzió) operációs rendszer biztonsági konfigurációival testreszabása |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan szabhatja testre a security center értékelések
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 18/30/2018
ms.author: rkarlin
ms.openlocfilehash: 91a77902e03b4240e00f40dfdb15b6c854f5a29d
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2018
ms.locfileid: "44027112"
---
# <a name="customize-os-security-configurations-in-azure-security-center-preview"></a>Testre szabhatja az operációs rendszer biztonsági konfigurációival az Azure Security Centerben (előzetes verzió)

Ez az útmutató bemutatja, hogyan szabhatja testre az operációs rendszer biztonsági konfigurációs értékelések az Azure Security Centerben.

## <a name="what-are-os-security-configurations"></a>Mik azok az operációs rendszer biztonsági konfigurációival?

Az Azure Security Center biztonsági konfigurációk figyeli egy alkalmazásával [több mint 150 ajánlott szabályok](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) az operációs rendszer korlátozására, például a szabályok kapcsolódó tűzfalak, naplózás, jelszóházirendek és egyéb. Ha egy gép sebezhető konfigurációval rendelkezik, a Security Center biztonsági javaslatot hoz létre.

A szabályok testreszabásával, szervezetek szabályozhatja, hogy melyik konfigurációs lehetőségek a környezetnek jobban megfelelő. Testre szabott értékelés házirend beállítása, és alkalmazza azt az előfizetés összes érvényes gépeken.

> [!NOTE]
> - Jelenleg az operációs rendszer biztonsági konfigurációs testre szabhatja a Windows Server 2008, 2008 R2, 2012 és 2012 R2 verziók operációs rendszerek csak érhető el.
> - A konfigurálás érvényes lesz minden olyan virtuális gépek és a kiválasztott előfizetésben található összes munkaterülethez csatlakoztatott számítógép.
> - Az operációs rendszer biztonsági konfigurációs testreszabása csak a Security Center standard szinten érhető el.
>
>

Testre szabhatja, hogy az operációs rendszer biztonsági konfigurációs szabályok engedélyezésével és a egy adott szabály letiltása, a megfelelő beállítás a meglévő szabály módosítása vagy hozzáadása egy új szabályt, amely a támogatott szabálytípusokat (a beállításjegyzék, a naplózási házirend és a biztonsági szabályzat) alapul. Jelenleg a megfelelő beállítás pontos számnak kell lennie.

Új szabályok azonos típusú egyéb meglévő szabályok azonos formátum és struktúra kell lennie.

> [!NOTE]
> Testre szabhatja az operációs rendszer biztonsági konfigurációival, akkor hozzá kell rendelni a szerepköre *előfizetés tulajdonosa*, *előfizetés Közreműködője*, vagy *biztonsági rendszergazda*.
>
>

## <a name="customize-the-default-os-security-configuration"></a>Az alapértelmezett operációs rendszer biztonsági konfigurációs testreszabása

Testre szabhatja az alapértelmezett operációs rendszer biztonsági konfigurációs a Security Centerben, tegye a következőket:

1.  Nyissa meg a **Security Center** irányítópultját.

2.  A bal oldali panelen válassza ki a **biztonsági házirend**.      

    ![Biztonsági szabályzatok listája](media/security-center-customize-os-security-config/manual-provision.png)

3.  Az előfizetés testre szeretné szabni a sorban kattintson **beállításainak szerkesztése**.

4. Válassza ki **biztonsági konfigurációk szerkesztése**.  
    
    ![A "Biztonsági konfigurációk szerkesztése" ablak](media/security-center-customize-os-security-config/blade.png)

5. Kövesse a lépéseket letöltése, szerkesztheti, és töltse fel a módosított fájlt.

   > [!NOTE]
   > Alapértelmezés szerint a konfigurációs fájl letöltése van *json* formátumban. Ez a fájl módosításával kapcsolatos útmutatásért lépjen [a konfigurációs fájljának testreszabása](#customize-the-configuration-file).
   >

   Miután sikeresen mentette a fájlt, a konfiguráció alkalmazása az összes virtuális gépek és az előfizetéshez tartozó összes munkaterülethez csatlakoztatott számítógép. A folyamat általában néhány percet vesz igénybe, de az infrastruktúra méretétől függően több időt vesz igénybe.

6. A módosítás véglegesítéséhez kattintson **mentése**. Ellenkező esetben a házirend nem tárolja.

Bármikor visszaállíthatja a jelenlegi házirend-konfigurációt az alapértelmezett állapotba. Az ehhez a **szerkesztése operációs rendszer biztonsági konfigurációs szabályok** ablakban válassza **alaphelyzetbe**. Erősítse meg ezt a lehetőséget választva **Igen** a megerősítési előugró ablakban.

![Az ablak-visszaállítási megerősítése](media/security-center-customize-os-security-config/edit-alert.png)

## <a name="customize-the-configuration-file"></a>A konfigurációs fájljának testreszabása

A Testreszabás fájlban mindegyik operációs rendszer támogatott verziója szabályokat, vagy a szabálykészletben készletével rendelkezik. Minden egyes szabálykészletben rendelkezik a saját nevét és az egyedi azonosító, az alábbi példában látható módon:

![A ruleset neve és azonosítója](media/security-center-customize-os-security-config/custom-file.png)

> [!NOTE]
> Ez a példa fájlt a Visual Studióban szerkesztették, de használhatja a Jegyzettömb is, ha a JSON-megjelenítő beépülő modul telepítve van.
>
>

A Testreszabás fájl szerkesztése, amikor egy szabály vagy ezek mindegyike módosíthatja. Minden egyes szabálykészletben tartalmaz egy *szabályok* szakaszt, amely három kategóriába elkülönített: beállításjegyzék, a naplózási házirend és a biztonsági házirend, az itt látható módon:

![Három szabálykészletben kategória](media/security-center-customize-os-security-config/rules-section.png)

Az egyes kategóriákhoz saját attribútumok készletét. Módosíthatja a következő attribútumokat:

- **expectedValue**: Ez az attribútum mező adattípusának egyeznie kell a támogatott értékek száma *szabály típusa*, például:

  - **baselineRegistryRules**: az értéknek meg kell egyeznie a [regValueType](https://msdn.microsoft.com/library/windows/desktop/ms724884) által a szabályban definiált.

  - **baselineAuditPolicyRules**: használja a következő karakterlánc-értékek egyikét:

    - *A sikeres és sikertelen*

    - *Sikeres*

  - **baselineSecurityPolicyRules**: használja a következő karakterlánc-értékek egyikét:

    - *Senki*

    - Listája engedélyezett felhasználói csoportok, például: *rendszergazdák*, *biztonságimásolat-felelősök*

-   **állapot**: A karakterlánc a beállításokat is tartalmazhat *letiltott* vagy *engedélyezve*. A privát előzetes kiadás az karakterlánca kis-és nagybetűket.

Ezek azok az egyetlen mezője, amely konfigurálható. Ha Ön megsérti formátuma vagy mérete, nem lehet menteni a módosítást. Kapni fog egy hiba, amely közli, hogy fel kell töltenie egy érvényes JSON-konfigurációs fájlt.

Más lehetséges hibák listáját lásd: [hibakódok](#error-codes).

Az alábbi három szakaszok példákat a fenti szabályok. A *expectedValue* és *állapot* attribútumokat is módosítható.

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

Néhány szabály számára a különböző operációs rendszer ismétlődik. Ismétlődő szabályokkal rendelkezik azonos *originalId* attribútum.

## <a name="create-custom-rules"></a>Egyéni szabályok létrehozása

Új szabályokat is létrehozhat. Mielőtt létrehozna egy új szabályt, vegye figyelembe a következő korlátozásokkal:

-   Sémaverzió *baselineId* és *baselineName* nem módosítható.

-   Nem lehet eltávolítani a RuleSet.

-   Nem adható hozzá a RuleSet.

-   A szabályok engedélyezve (az alapértelmezett szabályok is beleértve) maximális száma 1000.

Új egyéni szabályok egy új egyéni forrás lesznek megjelölve (! = "Microsoft"). A *ruleId* mező lehet null értékű vagy üres. Ha üres, a Microsoft hoz létre egyet. Ha nem üres, érvényes GUID, amely egyedi összes szabályt (alapértelmezett és egyéni) között kell legyen. Tekintse át az alapvető mezők a következő korlátozásokkal:

-   **originalId**: NULL értékű vagy üres is lehet. Ha *originalId* van nem üres, kell lennie egy érvényes GUID.

-   **cceId**: NULL értékű vagy üres is lehet. Ha *cceId* van nem üres, ennek egyedinek kell lennie.

-   **Szabálytípus**: (egyet válasszon) beállításjegyzék, AuditPolicy vagy SecurityPolicy.

-   **Súlyosság**: (egyet válasszon) ismeretlen, kritikus, figyelmeztető vagy tájékoztató.

-   **analyzeOperation**: kell *egyenlő*.

-   **auditPolicyId**: érvényes GUID azonosítónak kell lennie.

-   **regValueType**: (egyet válasszon) Int, Long, String vagy MultipleString.

> [!NOTE]
> Hive kell *LocalMachine*.
>
>

Példa: egy új egyéni szabályt:

**Beállításjegyzék**:
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

## <a name="file-upload-failures"></a>Sikertelen fájlfeltöltés

A beküldött konfigurációs fájlban érvénytelen értékek vagy a formázást hibák miatt, ha egy hiba jelenik meg, mint például **mentési művelet nem sikerült**. Részletes hibaüzenetek .csv jelentést és javítsa ki a hibákat, mielőtt a javított konfigurációs fájlt küldje el újra töltheti le.

Hiba történt a fájl egy példa:

![Hiba történt a fájl példa](media/security-center-customize-os-security-config/errors-file.png)

## <a name="error-codes"></a>Hibakódok

Az összes lehetséges hibákat az alábbi táblázatban láthatók:

| **Hiba történt**                                | **Leírás**                                                                                                                              |
|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------|
| BaselineConfiguratiohSchemaVersionError  | A tulajdonság *sémaverzióval* található érvénytelen vagy üres. Az értéket kell beállítani *{0}*.                                                         |
| BaselineInvalidStringError               | A tulajdonság *{0}* nem tartalmazhat  *\\n*.                                                                                                         |
| BaselineNullRuleError                    | A referenciakonfiguráció konfigurációs szabályok listája tartalmaz egy szabályt az érték *null*.                                                                         |
| BaselineRuleCceIdNotUniqueError          | A CCE-azonosító *{0}* , nem egyedi.                                                                                                                  |
| BaselineRuleEmptyProperty                | A tulajdonság *{0}* hiányzik vagy érvénytelen.                                                                                                       |
| BaselineRuleIdNotInDefault               | A szabály forrás tulajdonsága *Microsoft* , de nem található a Microsoft alapértelmezett szabálykészletet.                                                   |
| BaselineRuleIdNotUniqueError             | A szabály azonosítója nem egyedi.                                                                                                                       |
| BaselineRuleInvalidGuid                  | A tulajdonság *{0}* található érvénytelen. Az érték nem érvényes GUID.                                                                             |
| BaselineRuleInvalidHive                  | A hive a helyi gépen lévő kell lennie.                                                                                                                   |
| BaselineRuleNameNotUniqueError           | Szabály név nem egyedi.                                                                                                                 |
| BaselineRuleNotExistInConfigError        | A szabály nem található az új konfigurációt. A szabály nem törölhető.                                                                     |
| BaselineRuleNotFoundError                | A szabály nem található az alapértelmezett alapkonfiguráció szabálykészletben.                                                                                        |
| BaselineRuleNotInPlace                   | A szabály vonatkozzon, írja be az alapértelmezett szabály {0} és szerepel a {1} listája.                                                                       |
| BaselineRulePropertyTooLong              | A tulajdonság *{0}* túl hosszú. Maximális megengedett hossz: {1}.                                                                                        |
| BaselineRuleRegTypeInvalidError          | A várt értékkel *{0}* a definiált beállításazonosító típusa nem felel meg.                                                              |
| BaselineRulesetAdded                     | A szabálykészletben azonosítójú *{0}* az alapértelmezett beállítás nem található. A ruleset nem adható hozzá.                                               |
| BaselineRulesetIdMustBeUnique            | Az adott alaptervbe szabálykészletben *{0}* egyedinek kell lennie.                                                                                           |
| BaselineRulesetNotFound                  | A szabálykészletben azonosítójú *{0}* és név *{1}* a megadott konfigurációban nem található. A ruleset nem lehet törölni.                                |
| BaselineRuleSourceNotMatch               | A szabály azonosítójú *{0}* már definiálva van.                                                                                                       |
| BaselineRuleTypeDoesntMatch              | Az alapértelmezett szabály típus *{0}*.                                                                                                              |
| BaselineRuleTypeDoesntMatchError         | A szabály a tényleges típus *{0}*, de a *ruleType* tulajdonság *{1}*.                                                                          |
| BaselineRuleUnpermittedChangesError      | Csak *expectedValue* és *állapot* tulajdonságok megengedettek módosítani.                                                                       |
| BaselineTooManyRules                     | A legfeljebb engedélyezett testre szabott szabályokkal {0} szabályokat. A megadott konfiguráció tartalmazza {1} szabályok {2} alapértelmezett szabályokkal, és {3} testre szabott szabályokkal. |
| ErrorNoConfigurationStatus               | Nincsenek konfigurációs állapot található. Adja meg a szükséges konfiguráció állapota: *alapértelmezett* vagy *egyéni*.                                    |
| ErrorNonEmptyRulesetOnDefault            | A konfigurációs állapot alapértelmezett értéke. A *BaselineRulesets* lista kell lennie, NULL értékű vagy üres.                                                          |
| ErrorNullRulesetsPropertyOnCustom        | A megadott konfigurációs állapot *egyéni* , de a *baselineRulesets* tulajdonság null értékű vagy üres.                                             |
| ErrorParsingBaselineConfig               | A megadott konfiguráció érvénytelen. Egy vagy több a meghatározott értékeknek kell null értékű vagy érvénytelen típusú.                                  |
| ErrorParsingIsDefaultProperty            | A megadott *configurationStatus* érték *{0}* je neplatná. Az érték csak lehet *alapértelmezett* vagy *egyéni*.                                         |
| InCompatibleViewVersion                  | A nézet verzió *{0}* van *nem* munkaterület típus támogatott.                                                                                   |
| InvalidBaselineConfigurationGeneralError | Az adott alapkonfiguráció típus érvényesítése egy vagy több hiba is található.                                                          |
| ViewConversionError                      | A nézet nem régebbi verziójú, mint a munkaterület támogatja. Sikertelen volt a konverzió megtekintése: {0}.                                                                 |

Ha nem rendelkezik megfelelő engedélyekkel, kaphat egy általános hiba itt látható módon:

![A művelet nem sikerült"mentése" hibaüzenet](media/security-center-customize-os-security-config/general-failure-error.png)

## <a name="next-steps"></a>További lépések
Ez a cikk tárgyalja, hogyan szabhatja testre az operációs rendszer biztonsági konfigurációs értékelések a Security Centerben. A konfigurációs szabályok és szervizelési kapcsolatos további információkért lásd:

- [A Security Center gyakori konfigurációs azonosítók és alapkonfigurációs szabályok](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335).
- A Security Center gyakori konfigurációs enumerálása (CCE) egyedi azonosítók hozzárendelése a konfigurációs szabályok használ. További információkért lásd: [CCE](https://nvd.nist.gov/config/cce/index).
- Ha az operációs rendszer konfigurációja nem egyezik a javasolt biztonsági konfigurációs szabályok biztonsági rések elhárításához lásd: [biztonsági konfigurációk javítása](security-center-remediate-os-vulnerabilities.md).
