---
title: Áttekintés – Az Azure Logic Apps üzembe helyezésének automatizálása
description: Ismerje meg az Azure Resource Manager-sablonokat az Azure Logic Apps üzembe helyezésének automatizálásához
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 07/25/2019
ms.openlocfilehash: 7a99038f41043b899886c7161f9b12c77c807c4c
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641814"
---
# <a name="overview-automate-deployment-for-azure-logic-apps-by-using-azure-resource-manager-templates"></a>Áttekintés: Az Azure Logic Apps üzembe helyezésének automatizálása az Azure Resource Manager-sablonok használatával

Ha készen áll a logikai alkalmazás létrehozásának és üzembe helyezésének automatizálására, kiterjesztheti a logikai alkalmazás alapjául szolgáló munkafolyamat-definícióját egy [Azure Resource Manager-sablonba.](../azure-resource-manager/management/overview.md) Ez a sablon határozza meg az infrastruktúrát, az erőforrásokat, a paramétereket és a logikai alkalmazás kiépítéséhez és üzembe helyezéséhez szükséges egyéb információkat. A központi telepítéskor változó értékek paramétereinek meghatározásával , más néven *paraméterezés,* többször és következetesen telepítheti a logikai alkalmazásokat a különböző telepítési igények alapján.

Ha például fejlesztési, tesztelési és éles környezetekben telepíti a környezeteket, valószínűleg minden környezethez különböző kapcsolati karakterláncokat használ. Deklarálhatja a különböző kapcsolati karakterláncokat fogadó sablonparamétereket, majd ezeket a karakterláncokat külön [paraméterfájlban](../azure-resource-manager/templates/parameter-files.md)tárolhatja. Így módosíthatja ezeket az értékeket anélkül, hogy frissítenie és újratelepítenie kellene a sablont. Olyan esetekben, ahol a paraméterértékek, amelyek bizalmasak, vagy biztonságosnak kell lennie, például a jelszavak és a titkos kulcsok, tárolhatja ezeket az értékeket [az Azure Key Vaultban,](../azure-resource-manager/templates/key-vault-parameter.md) és a paraméterek fájl beolvasni ezeket az értékeket. Ezekben a forgatókönyvekben azonban az aktuális értékek beolvasása érdekében újratelepíti.

Ez az áttekintés az Erőforrás-kezelő sablon attribútumait ismerteti, amelyek logikai alkalmazás munkafolyamat-definícióját tartalmazzák. Mind a sablon, mind a munkafolyamat-definíció JSON-szintaxist használ, de vannak különbségek, mert a munkafolyamat-definíciós definíciós [nyelvsémát](../logic-apps/logic-apps-workflow-definition-language.md)is követi. A sablonkifejezések és a munkafolyamat-definíciós kifejezések például különböznek a [paraméterekhivatkozás](#parameter-references) módjától és az általuk elfogadható értékektől.

> [!TIP]
> A legegyszerűbb módja annak, hogy egy érvényes paraméterezett logikai alkalmazás sablon, amely többnyire készen áll a telepítésre, használja a Visual Studio (ingyenes közösségi kiadás vagy újabb) és az Azure Logic Apps Tools for Visual Studio. Ezután [létrehozhatja a logikai alkalmazást a Visual Studióban,](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) vagy [megkeresheti és letöltheti az Azure-ból a Visual Studióba egy meglévő tanológiai alkalmazást.](../logic-apps/manage-logic-apps-with-visual-studio.md)
>
> Vagy létrehozhat logikai alkalmazássablonokat [az Azure PowerShell használatával a LogicAppTemplate modullal.](../logic-apps/logic-apps-create-azure-resource-manager-templates.md#azure-powershell)

Ebben a témakörben a példaként szereplő logikai alkalmazás egy [Office 365 Outlook-eseményindítót](/connectors/office365/) használ, amely új e-mail érkezésekor aktiválódik, és egy [Azure Blob Storage-műveletet,](/connectors/azureblob/) amely létrehoz egy blobot az e-mail törzséhez, és feltölti a blobot egy Azure-tárolóba. A példák azt is bemutatják, hogyan paraméterezhet értékeket, amelyek a központi telepítés során eltérőek.

Az Erőforrás-kezelő sablonjairól az alábbi témakörökben olvashat bővebben:

* [Az Azure Resource Manager sablonszerkezete és szintaxisa](../azure-resource-manager/templates/template-syntax.md)
* [Ajánlott eljárások az Azure Resource Manager-sablonokhoz](../azure-resource-manager/templates/template-best-practices.md)
* [Azure Resource Manager-sablonok fejlesztése felhőkonzisztenciához](../azure-resource-manager/templates/templates-cloud-consistency.md)

Mintalogikai alkalmazássablonokról az alábbi példákban olvashat:

* [A](#full-example-template) témakör példáihoz használt teljes sablon
* [Minta rövid útmutató logikai alkalmazássablon](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create) a GitHubon

A logikai alkalmazásokra, az integrációs fiókokra és az integrációs fiók összetevőire vonatkozó sablonerőforrás-információkat a [Microsoft.Logic erőforrástípusok](https://docs.microsoft.com/azure/templates/microsoft.logic/allversions)című témakörben talál.

<a name="template-structure"></a>

## <a name="template-structure"></a>Sablonszerkezet

A legfelső szinten egy Resource Manager-sablon követi ezt a struktúrát, amelyet teljes körűen ismertet az [Azure Resource Manager sablonszerkezete és szintaktikai témaköre:](../azure-resource-manager/templates/template-syntax.md)

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "functions": [],
   "resources": [],
   "outputs": {}
}
```

Logikai alkalmazássablon esetén elsősorban a következő sablonobjektumokkal dolgozik:

| Attribútum | Leírás |
|-----------|-------------|
| `parameters` | Deklarálja a [sablon paramétereit](../azure-resource-manager/templates/template-syntax.md#parameters) az Azure-beli üzembe helyezéshez használt értékek elfogadásához. Ezek a paraméterek például elfogadják a logikai alkalmazás nevének és helyének, kapcsolatainak és a központi telepítéshez szükséges egyéb erőforrásoknak az értékeit. Ezeket a paraméterértékeket egy [paraméterfájlban](#template-parameter-files)tárolhatja, amelyet a témakör későbbi részében ismertetünk le. Az általános részleteket a [Paraméterek – Erőforrás-kezelő sablonszerkezete és szintaxisa (Paraméterek – Erőforrás-kezelő) sablonszerkezete és szintaxisa témakörben találja.](../azure-resource-manager/templates/template-syntax.md#parameters) |
| `resources` | Meghatározza az Azure-erőforráscsoporthoz, például a logikai alkalmazáshoz, a kapcsolatokhoz, az Azure storage-fiókokhoz és így tovább történő létrehozásához, frissítéséhez és üzembe helyezéséhez szükséges [erőforrásokat.](../azure-resource-manager/templates/template-syntax.md#resources) Az általános részleteket az [Erőforrások – Erőforrás-kezelő sablonszerkezet e.](../azure-resource-manager/templates/template-syntax.md#resources) |
||||

A logikai alkalmazássablon ezt a fájlnévformátumot használja:

**<*logikai alkalmazás-név*>.json**

> [!IMPORTANT]
> A sablon szintaxisa a kis- és nagybetűket is figyelembe teszi, ezért ügyeljen arra, hogy egységes burkolatot használjon. 

<a name="template-parameters"></a>

## <a name="template-parameters"></a>Sablon paraméterei

A logikai alkalmazássablon több `parameters` objektumot tartalmaz, amelyek különböző szinteken léteznek, és különböző funkciókat hajtanak végre. A legfelső szinten például deklarálhatja az okat az [értékeket,](../azure-resource-manager/templates/template-syntax.md#parameters) amelyeket az Azure-beli erőforrások létrehozásakor és üzembe helyezésekor el kell fogadni és használni, például:

* A logikai alkalmazás
* A logika által más szolgáltatások és rendszerek [felügyelt összekötőkön](../connectors/apis-list.md) keresztül történő eléréséhez használt kapcsolatok
* A logikai alkalmazás nak a központi telepítéshez szükséges egyéb erőforrásai

  Ha például a logikai alkalmazás [integrációs fiókot](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) használ a vállalkozások közötti (B2B) `parameters` forgatókönyvekhez, a sablon legfelső szintű objektuma deklarálja azt a paramétert, amely elfogadja az adott integrációs fiók erőforrás-azonosítóját.

Itt van az általános szerkezet és szintaxis egy paraméterdefinícióhoz, amelyet teljes mértékben a [Paraméterek - Erőforrás-kezelő sablon szerkezete és szintaxisa](../azure-resource-manager/templates/template-syntax.md#parameters)ír le:

```json
"<parameter-name>": {
   "type": "<parameter-type>",
   "defaultValue": <default-parameter-value>,
   <other-parameter-attributes>,
   "metadata": {
      "description": "<parameter-description>"
   }
},
```

Ez a példa csak az erőforrások Azure-beli létrehozásához és üzembe helyezéséhez használt értékek sablonparamétereit mutatja be:

* A logikai alkalmazás neve és helye
* A logikai alkalmazáshoz kapcsolódó integrációs fiókhoz használandó azonosító

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "defaultValue": "MyLogicApp",
         "metadata": {
            "description": "The resource name for the logic app"
         }
      },
      "LogicAppLocation": {
         "type": "string",
         "minLength": 1,
         "defaultValue": "[resourceGroup().location]",
         "metadata": {
            "description": "The resource location for the logic app"
         }
      },
      "LogicAppIntegrationAccount": {
         "type":"string",
         "minLength": 1,
         "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/fabrikam-integration-account-rg/providers/Microsoft.Logic/integrationAccounts/fabrikam-integration-account",
         "metadata": {
            "description": "The ID to use for the integration account"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [],
   "outputs": {}
}
```

A bizalmas vagy biztonságos értékeket kezelő paraméterek , például a felhasználónevek, a jelszavak és `defaultValue` a titkos kulcsok kivételével ezek a paraméterek attribútumokat tartalmaznak, bár bizonyos esetekben az alapértelmezett értékek üres értékek. A sablonparaméterekhez használandó központi telepítési értékeket a témakör későbbi részében ismertetett [mintaparaméter-fájl](#template-parameter-files) biztosítja.

A sablonparaméterek védelméről az alábbi témakörökben olvashat bővebben:

* [Biztonsági javaslatok a sablonparaméterekhez](../azure-resource-manager/templates/template-best-practices.md#parameters)
* [A sablonparaméterek biztonságának növelése](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-deployment-template)
* [Biztonságos paraméterértékek átadhatók az Azure Key Vault segítségével](../azure-resource-manager/templates/key-vault-parameter.md)

Más sablonobjektumok gyakran hivatkoznak a sablon paramétereire, hogy használhassák a sablonparamétereken áthaladó értékeket, például:

* A [sablon erőforrás-objektuma](#template-resources), amelyet a témakör későbbi részében ismertetett, meghatározza az Azure-ban létrehozni és telepíteni kívánt erőforrásokat, például a [logikai alkalmazás erőforrás-definícióját.](#logic-app-resource-definition) Ezek az erőforrások gyakran használnak sablonparaméter-értékeket, például a logikai alkalmazás nevét, valamint helyét és kapcsolati adatait.

* A logikai alkalmazás erőforrás-definíciójának mélyebb szintjén a [munkafolyamat-definíció paraméterobjektum](#workflow-definition-parameters) deklarálja a logikai alkalmazás futásidejében használandó értékek paramétereit. Deklarálhatja például annak a felhasználónévnek és jelszónak a munkafolyamat-definíciós paramétereit, amelyet egy HTTP-eseményindító a hitelesítéshez használ. A munkafolyamat-definíciós paraméterek értékeinek `parameters` megadásához használja azt az objektumot, amely kívül esik a munkafolyamat-definícióján, de továbbra is a logikai alkalmazás erőforrás-definícióján *belül* van. *outside* Ebben a `parameters` külső objektumban hivatkozhat a korábban deklarált sablonparaméterekre, amelyek a telepítéskor értékeket fogadhatnak el egy paraméterfájlból.

Paraméterekre való hivatkozáskor a sablonkifejezések és -függvények eltérő szintaxist használnak, és a munkafolyamat-definíciós kifejezésektől és függvényektől eltérően viselkednek. Ezekről a különbségekről a témakör későbbi, [a paraméterekre mutató hivatkozások](#parameter-references) című témakörben olvashat bővebben.

<a name="best-practices-template-parameters"></a>

## <a name="best-practices---template-parameters"></a>Gyakorlati tanácsok - sablonparaméterek

Íme néhány gyakorlati tanács a paraméterek meghatározásához:

* Csak a telepítési igényektől függően változó értékek paramétereinek deklarációja. Ne deklarálja a paramétereket olyan értékekhez, amelyek a különböző telepítési követelmények között azonosak maradnak.

* Adja `defaultValue` meg az attribútumot, amely üres értékeket adhat meg az összes paraméterhez, kivéve azokat az értékeket, amelyek bizalmasak vagy biztonságosak. Mindig használjon biztonságos paramétereket a felhasználónevekhez, jelszavakhoz és titkos kulcsokhoz. A bizalmas paraméterértékek elrejtéséhez vagy védelméhez kövesse az alábbi témakörökben található útmutatást:

  * [Biztonsági javaslatok a sablonparaméterekhez](../azure-resource-manager/templates/template-best-practices.md#parameters)

  * [A sablonparaméterek biztonságának növelése](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-deployment-template)

  * [Biztonságos paraméterértékek átadhatók az Azure Key Vault segítségével](../azure-resource-manager/templates/key-vault-parameter.md)

* Ha meg szeretné különböztetni a sablonparaméterneveket a munkafolyamat-definícióparamétereinek nevétől, használhat leíró sablonparaméter-neveket, például:`TemplateFabrikamPassword`

A sablonokkal kapcsolatos gyakorlati tanácsokért olvassa el [a sablonparaméterek gyakorlati tanácsait.](../azure-resource-manager/templates/template-best-practices.md#parameters)

<a name="template-parameter-files"></a>

## <a name="template-parameters-file"></a>Sablon paraméterfájlja

A sablonparaméterek értékeinek megadásához tárolja ezeket az értékeket egy [paraméterfájlban.](../azure-resource-manager/templates/parameter-files.md) Így különböző paramétereket használhat a központi telepítési igények alapján. Itt van a fájlnév formátuma használata:

* Logikai alkalmazássablon fájlneve: ** < *logikai alkalmazásnév*>.json**
* Paraméterek fájlneve: ** < *logikai alkalmazás-név*>.parameters.json**

Itt van a szerkezet a paraméterfájlon belül, amely egy key vault-hivatkozást tartalmaz [egy biztonságos paraméterérték átadásához az Azure Key Vault segítségével:](../azure-resource-manager/templates/key-vault-parameter.md)

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "<parameter-name-1>": {
         "value": "<parameter-value>"
      },
      "<parameter-name-2>": {
         "value": "<parameter-value>"
      },
      "<secured-parameter-name>": {
         "reference": {
            "keyVault": {
               "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/<key-vault-name>",
            },
            "secretName: "<secret-name>"
         }
      },
      <other-parameter-values>
   }
}
```

Ez a példa paraméterfájlja a témakör korábbi, deklarált sablonparamétereinek értékeit adja meg:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "LogicAppName": {
         "value": "Email-Processor-Logic-App"
      },
      "LogicAppLocation": {
         "value": "westeurope"
      }
   }
}
```

<a name="template-resources"></a>

## <a name="template-resources"></a>Sablonerőforrások

A sablon `resources` rendelkezik egy objektummal, amely egy tömb, amely tartalmazza az azure-ban létrehozandó és üzembe helyezendő erőforrások definícióit, például a [logikai alkalmazás erőforrás-definícióját,](#logic-app-resource-definition)a [kapcsolati erőforrás-definíciókat](#connection-resource-definitions)és a logikai alkalmazás üzembe helyezéséhez szükséges egyéb erőforrásokat.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Start connection resource definitions
      {
         <connection-resource-definition-1>
      },
      {
         <connection-resource-definition-2>
      }
   ],
   "outputs": {}
}
```

> [!NOTE]
> A sablonok több logikai alkalmazás erőforrás-definícióit is tartalmazhatják, ezért győződjön meg arról, hogy a logikai alkalmazás erőforrásai ugyanazt az Azure-erőforráscsoportot határozzák meg. Amikor a sablont egy Azure-erőforráscsoportba telepíti a Visual Studio használatával, a rendszer megkérdezi, hogy melyik logikai alkalmazást szeretné megnyitni. Emellett az Azure erőforráscsoport-projekt több sablont is tartalmazhat, ezért győződjön meg arról, hogy a megfelelő paraméterek fájl kiválasztása, amikor a rendszer kéri.

A sablonerőforrásokról és azok jellemzőiről az alábbi témakörökben olvashat:

* [Erőforrások – Erőforrás-kezelő sablonszerkezete és szintaxisa](../azure-resource-manager/templates/template-syntax.md#resources)
* [Gyakorlati tanácsok a sablon-erőforrásokhoz](../azure-resource-manager/templates/template-best-practices.md#resources)

<a name="logic-app-resource-definition"></a>

### <a name="logic-app-resource-definition"></a>Logikai alkalmazás erőforrás-definíciója

A logikai alkalmazás erőforrás-definíciója az `properties` objektummal kezdődik, amely tartalmazza ezt az információt:

* A logikai alkalmazás állapota a telepítés során
* A logikai alkalmazás által használt integrációs fiók azonosítója
* A logikai alkalmazás munkafolyamat-definíciója
* Olyan `parameters` objektum, amely futásidőben beállítja az értékeket
* A logikai alkalmazás egyéb erőforrásadatai, például név, típus, hely és így tovább

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            "state": "<Enabled or Disabled>",
            "integrationAccount": {
               "id": "[parameters('LogicAppIntegrationAccount')]" // Template parameter reference
            },
            "definition": {<workflow-definition>},
            "parameters": {<workflow-definition-parameter-values>},
            "accessControl": {}
         },
         "name": "[parameters('LogicAppName')]", // Template parameter reference
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]", // Template parameter reference
         "tags": {
           "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "dependsOn": [
         ]
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

Az alábbiakban a logikai alkalmazás erőforrás-definíciójára jellemző attribútumokat ismertetik:

| Attribútum | Kötelező | Típus | Leírás |
|-----------|----------|------|-------------|
| `state` | Igen | Sztring | A logikai alkalmazás állapota `Enabled` a központi telepítés, `Disabled` ahol azt jelenti, hogy a logikai alkalmazás élő, és azt jelenti, hogy a logikai alkalmazás inaktív. Ha például nem áll készen a logikai alkalmazás éles, de szeretné telepíteni a `Disabled` piszkozatverzió, használhatja a lehetőséget. |
| `integrationAccount` | Nem | Objektum | Ha a logikai alkalmazás egy integrációs fiókot használ, amely tárolja a gazdik `id` a vállalkozások közötti (B2B) forgatókönyvek, ez az objektum tartalmazza az attribútumot, amely megadja az integrációs fiók azonosítóját. |
| `definition` | Igen | Objektum | A logikai alkalmazás alapjául szolgáló munkafolyamat-definíciója, amely ugyanaz az objektum, amely kódnézetben jelenik meg, és teljes körűen ismertet és a [Munkafolyamat-definíció nyelve témakör Séma hivatkozása](../logic-apps/logic-apps-workflow-definition-language.md) ismerteti. Ebben a munkafolyamat-definícióban az `parameters` objektum deklarálja a logikai alkalmazások futásidejű értékeit. További információt a [Munkafolyamat definíciója és paraméterei című témakörben talál.](#workflow-definition-parameters) <p><p>A logikai alkalmazás munkafolyamat-definíciójában szereplő attribútumok megtekintéséhez váltson a "tervező nézetről" a "kódnézetre" az Azure Portalon vagy a Visual Studio-ban, vagy egy olyan eszköz használatával, mint az [Azure Resource Explorer.](https://resources.azure.com) |
| `parameters` | Nem | Objektum | A [munkafolyamat-definíció paraméterértékei](#workflow-definition-parameters) a logikai alkalmazások futásidejében. Ezeknek az értékeknek a paraméterdefiníciói a [munkafolyamat-definíció paraméterobjektumában](#workflow-definition-parameters)jelennek meg. Továbbá, ha a logikai alkalmazás [felügyelt összekötőket](../connectors/apis-list.md) használ más szolgáltatások `$connections` és rendszerek eléréséhez, ez az objektum tartalmaz egy objektumot, amely beállítja a kapcsolat idotartamán használható kapcsolatok értékeit. |
| `accessControl` | Nem | Objektum | A logikai alkalmazás biztonsági attribútumainak megadásához, például az IP-hozzáférés korlátozásához a kérelem-eseményindítókhoz vagy az előzménybemenetek és kimenetek futtatásához. További információ: [Biztonságos hozzáférés a logikai alkalmazásokhoz.](../logic-apps/logic-apps-securing-a-logic-app.md) |
||||

A logikai alkalmazásokra, az integrációs fiókokra és az integrációs fiók összetevőire vonatkozó sablonerőforrás-információkat a [Microsoft.Logic erőforrástípusok](https://docs.microsoft.com/azure/templates/microsoft.logic/allversions)című témakörben talál.

<a name="workflow-definition-parameters"></a>

## <a name="workflow-definition-and-parameters"></a>Munkafolyamat-definíció és paraméterek

A logikai alkalmazás munkafolyamat-definíciója megjelenik `definition` az `properties` objektumban, amely a logikai alkalmazás erőforrás-definíciójában lévő objektumban jelenik meg. Ez `definition` az objektum ugyanaz az objektum, amely kódnézetben jelenik meg, és teljes körűleírása a [Munkafolyamat-definíció nyelve témakör Séma hivatkozásában](../logic-apps/logic-apps-workflow-definition-language.md) található. A munkafolyamat-definíció `parameters` tartalmaz egy belső deklarációs objektumot, ahol meghatározhatja az új paramétereket, vagy szerkesztheti a munkafolyamat-definíció által futásidőben használt értékek meglévő paramétereit. Ezután hivatkozhat ezekre a paraméterekre az eseményindítón vagy a munkafolyamatban lévő műveleteken belül. Ez az `parameters` objektum alapértelmezés szerint üres, kivéve, ha a logikai alkalmazás [felügyelt összekötőkön](../connectors/apis-list.md)keresztül kapcsolatot hoz létre más szolgáltatásokkal és rendszerekkel.

A munkafolyamat-definíciós paraméterek értékeinek `parameters` beállításához használja azt az objektumot, amely kívül esik a munkafolyamat-definícióján, de még mindig a logikai alkalmazás erőforrás-definícióján *belül* van. *outside* Ebben a `parameters` külső objektumban hivatkozhat a korábban deklarált sablonparaméterekre, amelyek a telepítéskor értékeket fogadhatnak el egy paraméterfájlból.

> [!TIP]
>
> Ajánlott eljárásként ne hivatkozzon közvetlenül a sablon paraméterekre, amelyeket a telepítéskor kiértékelnek a munkafolyamat-definíción belülről. Ehelyett deklarálegy munkafolyamat-definíciós paramétert, amelyet `parameters` amunkafolyamat-definíción *kívül* eső, de a logikai alkalmazás erőforrás-definíciójában továbbra is *tartalmazó* objektumban állíthat be. További információ: [Hivatkozások a paraméterekre](#parameter-references).

Ez a szintaxis azt mutatja, hogy hol deklarálhatja a paramétereket mind a sablon, mind a munkafolyamat-definíció szintjén, valamint azt, hogy a sablon- és munkafolyamat-definíciós paraméterekre hivatkozva hol állíthatja be ezeket a paraméterértékeket:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "<template-parameter-name>": {
         "type": "<parameter-type>",
         "defaultValue": "<parameter-default-value>",
         "metadata": {
            "description": "<parameter-description>"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <other-logic-app-resource-properties>,
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {<action-definitions>},
               // Workflow definition parameters
               "parameters": {
                  "<workflow-definition-parameter-name>": {
                     "type": "<parameter-type>",
                     "defaultValue": "<parameter-default-value>",
                     "metadata": {
                        "description": "<parameter-description>"
                     }
                  }
               },
               "triggers": {
                  "<trigger-name>": {
                     "type": "<trigger-type>",
                     "inputs": {
                         // Workflow definition parameter reference
                         "<attribute-name>": "@parameters('<workflow-definition-parameter-name')"
                     }
                  }
               },
               <...>
            },
            // Workflow definition parameter value
            "parameters": {
               "<workflow-definition-parameter-name>": { 
                  "value": "[parameters('<template-parameter-name>')]"
               }
            },
            "accessControl": {}
         },
         <other-logic-app-resource-definition-attributes>
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

<a name="secure-workflow-definition-parmameters"></a>

### <a name="secure-workflow-definition-parameters"></a>Munkafolyamat-definíciós paraméterek biztonságossá tétele

A bizalmas adatokat, jelszavakat, hozzáférési kulcsokat vagy titkos kulcsokat futásidőben kezelő munkafolyamat-definíciós `secureobject` paraméter esetén deklarálja vagy szerkeszti a paramétert a vagy a `securestring` paramétertípus használatához. Erre a paraméterre hivatkozhat a munkafolyamat-definíció egészében és azon belül. A sablon legfelső szintjén deklaráljon egy olyan paramétert, amely nek ugyanolyan típusú a központi telepítésekor ez az információ ja.

A munkafolyamat-definícióparaméter értékének beállításához `parameters` használja azt az objektumot, amely kívül esik a munkafolyamat-definícióján, de még mindig a logikai alkalmazás erőforrás-definícióján *belül* van a sablonparaméterre való hivatkozáshoz. *outside* Végül adja át az értéket a sablon paraméter üzembe helyezéskor tárolja ezt az értéket az [Azure Key Vaultban,](../azure-resource-manager/templates/key-vault-parameter.md) és hivatkozzon a key vault a [paraméterek fájl,](#template-parameter-files) amely a sablon által használt üzembe helyezéskor.

Ez a példasablon bemutatja, hogyan hajthatja végre ezeket a feladatokat, ha szükséges, biztonságos paramétereket határoz meg, hogy az azure-beli key vaultban tárolhassa értékeiket:

* A hozzáférés hitelesítéséhez használt értékek biztonságos paramétereinek deklarálása.
* Használja ezeket az értékeket mind a sablon, mind a munkafolyamat-definíció szintjén.
* Adja meg ezeket az értékeket egy paraméterfájl használatával.

**Sablon**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      <previously-defined-template-parameters>,
      // Additional template parameters for passing values to use in workflow definition
      "TemplateAuthenticationType": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The type of authentication used for the Fabrikam portal"
         }
      },
      "TemplateFabrikamPassword": {
         "type": "securestring",
         "metadata": {
            "description": "The password for the Fabrikam portal"
         }
      },
      "TemplateFabrikamUserName": {
         "type": "securestring",
         "metadata": {
            "description": "The username for the Fabrikam portal"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <other-logic-app-resource-properties>,
            // Start workflow definition
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {<action-definitions>},
               // Workflow definition parameters
               "parameters": {
                  "authenticationType": {
                     "type": "string",
                     "defaultValue": "",
                     "metadata": {
                        "description": "The type of authentication used for the Fabrikam portal"
                     }
                  },
                  "fabrikamPassword": {
                     "type": "securestring",
                     "metadata": {
                        "description": "The password for the Fabrikam portal"
                     }
                  },
                  "fabrikamUserName": {
                     "type": "securestring",
                     "metadata": {
                        "description": "The username for the Fabrikam portal"
                     }
                  }
               },
               "triggers": {
                  "HTTP": {
                     "inputs": {
                        "authentication": {
                           // Reference workflow definition parameters
                           "password": "@parameters('fabrikamPassword')",
                           "type": "@parameters('authenticationType')",
                           "username": "@parameters('fabrikamUserName')"
                        }
                     },
                     "recurrence": {<...>},
                     "type": "Http"
                  }
               },
               <...>
            },
            // End workflow definition
            // Start workflow definition parameter values
            "parameters": {
               "authenticationType": "[parameters('TemplateAuthenticationType')]", // Template parameter reference
               "fabrikamPassword": "[parameters('TemplateFabrikamPassword')]", // Template parameter reference
               "fabrikamUserName": "[parameters('TemplateFabrikamUserName')]" // Template parameter reference
            },
            "accessControl": {}
         },
         <other-logic-app-resource-attributes>
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

**Paraméterek fájl**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      <previously-defined-template-parameter-values>,
     "TemplateAuthenticationType": {
        "value": "Basic"
     },
     "TemplateFabrikamPassword": {
        "reference": {
           "keyVault": {
              "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
           },
           "secretName": "FabrikamPassword"
        }
     },
     "TemplateFabrikamUserName": {
        "reference": {
           "keyVault": {
              "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
           },
           "secretName": "FabrikamUserName"
        }
     }
   }
}
```

<a name="best-practices-workflow-definition-parameters"></a>

## <a name="best-practices---workflow-definition-parameters"></a>Gyakorlati tanácsok – munkafolyamat-definíciós paraméterek

Annak érdekében, hogy a Logic App Designer megfelelően tudja megjeleníteni a munkafolyamat-definícióparamétereit, kövesse az alábbi gyakorlati tanácsokat:

* Adja `defaultValue` meg az attribútumot, amely üres értékeket adhat meg az összes paraméterhez, kivéve azokat az értékeket, amelyek bizalmasak vagy biztonságosak.

* Mindig használjon biztonságos paramétereket a felhasználónevekhez, jelszavakhoz és titkos kulcsokhoz. A bizalmas paraméterértékek elrejtéséhez vagy védelméhez kövesse az alábbi témakörökben található útmutatást:

  * [Biztonsági javaslatok a műveletparaméterekhez](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters)

  * [Biztonsági javaslatok a munkafolyamat-definíciók paramétereihez](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-workflow)

  * [Biztonságos paraméterértékek átadhatók az Azure Key Vault segítségével](../azure-resource-manager/templates/key-vault-parameter.md)

A munkafolyamat-definíciós paraméterekről további információt a [Paraméterek - Munkafolyamat-definíció nyelve című témakörben talál.](../logic-apps/logic-apps-workflow-definition-language.md#parameters)

<a name="connection-resource-definitions"></a>

## <a name="connection-resource-definitions"></a>Kapcsolati erőforrás-definíciók

Amikor a logikai alkalmazás más szolgáltatásokkal és rendszerekkel létesít és `resources` használ [kapcsolatokat felügyelt összekötők](../connectors/apis-list.md)használatával, a sablon objektuma tartalmazza a kapcsolatok erőforrás-definícióit.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Start connection resource definitions
      {
         <connection-resource-definition-1>
      },
      {
         <connection-resource-definition-2>
      }
   ],
   "outputs": {}
}
```

A kapcsolati erőforrás-definíciók a sablon felső szintű paramétereire hivatkoznak az értékeikhez, ami azt jelenti, hogy ezeket az értékeket paraméterfájl használatával biztosíthatja a központi telepítéskor. Győződjön meg arról, hogy a kapcsolatok ugyanazt az Azure-erőforráscsoportot és helyet használják, mint a logikai alkalmazás.

Íme egy példa az Office 365 Outlook-kapcsolat erőforrás-definíciójára és a megfelelő sablonparaméterekre:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "office365_1_Connection_Name": {
         "type": "string",
         "defaultValue": "office365",
         "metadata": {
            "description": "The resource name for the Office 365 Outlook connection"
         }
      },
      "office365_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The display name for the Office 365 Outlook connection"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Office 365 Outlook API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         // Template parameter reference for connection name
         "name": "[parameters('office365_1_Connection_Name')]",
         // Template parameter reference for connection resource location. Must match logic app location.
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               // Connector ID
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            // Template parameter reference for connection display name
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      }
   ],
   "outputs": {}
}
```

A logikai alkalmazás erőforrás-definíciója a következő módokon is működik a kapcsolati erőforrás-definíciókkal:

* A munkafolyamat-definíción `parameters` belül az `$connections` objektum deklarálja a kapcsolati értékek logikai alkalmazás futásidejű használata paraméterét. A kapcsolatot létrehozó eseményindító vagy művelet a megfelelő értékeket használja, amelyek áthaladnak ezen a `$connections` paraméteren.

* A munkafolyamat-definíción *kívül,* de még mindig `parameters` a logikai alkalmazás erőforrás-definícióján `$connections` *belül,* egy másik objektum a megfelelő sablonparaméterekre hivatkozva állítja be a paraméter futásidőben használandó értékeit. Ezek az értékek sablonkifejezéseket használnak olyan erőforrásokhivatkozására, amelyek biztonságosan tárolják a kapcsolatok metaadatait a logikai alkalmazásban.

  A metaadatok közé tartozhatnak például a kapcsolati karakterláncok és a hozzáférési jogkivonatok, amelyeket az [Azure Key Vaultban](../azure-resource-manager/templates/key-vault-parameter.md)tárolhat. Ha ezeket az értékeket a sablon paraméterei, hivatkozzon, hogy a key vault a [paraméterek fájlban,](#template-parameter-files) amely a sablon által használt központi telepítés. A paraméterek hivatkozása közötti különbségekről a témakör későbbi, [a paraméterekre mutató hivatkozások](#parameter-references) című témakörben olvashat bővebben.

  Amikor megnyitja a logikai alkalmazás munkafolyamat-definícióját kódnézetben az `$connections` Azure Portalon vagy a Visual Studio-n keresztül, az objektum a munkafolyamat-definíción kívül, de ugyanazon a szinten jelenik meg. Ez a rendezés kódnézetben megkönnyíti a hivatkozást a munkafolyamat-definíció manuális frissítésekénél:

  ```json
  {
     "$connections": {<workflow-definition-parameter-connection-values-runtime},
     "definition": {<workflow-definition>}
  }
  ```

* A logikai alkalmazás erőforrás-definíciója rendelkezik egy `dependsOn` objektummal, amely meghatározza a logikai alkalmazás által használt kapcsolatok függőségeit.

Minden létrehozott kapcsolat egyedi névvel rendelkezik az Azure-ban. Ha több kapcsolatot hoz létre ugyanahhoz a szolgáltatáshoz vagy rendszerhez, a rendszer minden kapcsolatnevét egy `office365`számmal fűzi hozzá, amely minden létrehozott új kapcsolathoz , például a , `office365-1`és így tovább növekszik.

Ez a példa a logikai alkalmazás erőforrás-definíciója és az Office 365 Outlook kapcsolati erőforrás-definíciója közötti interakciókat mutatja be:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "office365_1_Connection_Name": {<parameter-definition>},
      "office365_1_Connection_DisplayName": {<parameter-definition>}
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <...>,
            "definition": {
               <...>,
               "parameters": {
                  // Workflow definition "$connections" parameter
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               <...>
            },
            "parameters": {
               // Workflow definition "$connections" parameter values to use at runtime
               "$connections": {
                  "value": {
                     "office365": {
                        // Template parameter references
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]",
                        "connectionName": "[parameters('office365_1_Connection_Name')]"
                     }
                  }
               }
            },
            <other-logic-app-resource-information>,
            "dependsOn": [
               "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]"
            ]
         }
         // End logic app resource definition
      },
      // Office 365 Outlook API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         // Template parameter reference for connection name
         "name": "[parameters('office365_1_Connection_Name')]",
         // Template parameter reference for connection resource location. Must match logic app location.
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               // Connector ID
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            // Template parameter reference for connection display name
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      }
   ],
   "outputs": {}
}
```

<a name="secure-connection-parameters"></a>

### <a name="secure-connection-parameters"></a>Biztonságos kapcsolati paraméterek

A bizalmas adatokat, jelszavakat, hozzáférési kulcsokat vagy titkos kulcsokat kezelő kapcsolatparaméter esetén a kapcsolat erőforrás-definíciója tartalmaz egy `parameterValues` objektumot, amely név-érték pár formátumban határozza meg ezeket az értékeket. Az adatok elrejtéséhez deklarálhatja vagy szerkesztheti ezeknek `securestring` `secureobject` az értékeknek a sablonparamétereit a vagy a paramétertípusok használatával. Ezeket az adatokat ezután tárolhatja az [Azure Key Vaultban.](../azure-resource-manager/templates/key-vault-parameter.md) Ha ezeket az értékeket a sablon paraméterei, hivatkozzon, hogy a key vault a [paraméterek fájlban,](#template-parameter-files) amely a sablon által használt központi telepítés.

Íme egy példa, amely megadja a fiók nevét és a hozzáférési kulcsot egy Azure Blob Storage-kapcsolathoz:

**Paraméterek fájl**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "LogicAppName": {
         "value": "Email-Processor-Logic-App"
      },
      "LogicAppLocation": {
         "value": "westeurope"
      },
      "azureblob_1_Connection_Name": {
         "value": "Fabrikam-Azure-Blob-Storage-Connection"
      },
      "azureblob_1_Connection_DisplayName": {
         "value": "Fabrikam-Storage"
      },
      "azureblob_1_accountName": {
         "value": "Fabrikam-Storage-Account"
      },
      "azureblob_1_accessKey": {
         "reference": {
            "keyVault": {
               "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
            },
            "secretName": "FabrikamStorageKey"
         }
      }
   }
}
```

**Sablon**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "azureblob_1_Connection_Name": {<parameter-definition>},
      "azureblob_1_Connection_DisplayName": {<parameter-definition>},
      "azureblob_1_accountName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         }
      },
      "azureblob_1_accessKey": {
         "type": "secureobject",
         "metadata": {
            "description": "Specify a valid primary/secondary storage account access key."
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         "properties": {
            "state": "Disabled",
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {
                  // Azure Blob Storage action
                  "Create_blob": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              // Workflow definition parameter reference for values to use at runtime
                              "name": "@parameters('$connections')['azureblob']['connectionId']"
                           }
                        },
                     },
                     "method": "post",
                     "body": "@triggerBody()?['Body']",
                     "path": "/datasets/default/files",
                     "queries": {
                        "folderPath": "/emails",
                        "name": "@triggerBody()?['Subject']",
                        "queryParametersSingleEncoded": true
                     },
                     "runAfter": {},
                     "runtimeConfiguration": {
                        "contentTransfer": {
                           "transferMode": "Chunked"
                        }
                     }
                  }
               },
               "parameters": {
                  // Workflow definition parameter for values to use at runtime
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               "triggers": {<trigger-definition>},
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "$connections": {
                  "value": {
                     // Template parameter references for values to use at runtime
                     "azureblob": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
                        "connectionName": "[parameters('azureblob_1_Connection_Name')]"
                    }
                  }
               }
            },
            "name": "[parameters('LogicAppName')]",
            "type": "Microsoft.Logic/workflows",
            "location": "[parameters('LogicAppLocation')]",
            "tags": {
               "displayName": "LogicApp"
            },
            "apiVersion": "2016-06-01",
            // Template parameter reference for value to use at deployment
            "dependsOn": [
               "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]"
            ]
         }
      },
      // Azure Blob Storage API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('azureblob_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]"
            },
            "displayName": "[parameters('azureblob_1_Connection_DisplayName')]",
            // Template parameter reference for values to use at deployment
            "parameterValues": {
               "accountName": "[parameters('azureblob_1_accountName')]",
               "accessKey": "[parameters('azureblob_1_accessKey')]"
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="authenticate-connections"></a>

### <a name="authenticate-connections"></a>Kapcsolatok hitelesítése

Üzembe helyezés után a logikai alkalmazás működik, végpontok között érvényes paraméterekkel. A [hitelesítő adatok hitelesítéséhez](../active-directory/develop/authentication-scenarios.md)azonban engedélyeznie kell az OAuth-kapcsolatokat. További információt az [OAuth-kapcsolatok engedélyezése](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md#authorize-oauth-connections)című témakörben talál.

Egyes kapcsolatok támogatják az Azure Active Directory (Azure AD) [egyszerű szolgáltatás](../active-directory/develop/app-objects-and-service-principals.md) használatával az [Azure AD-ben regisztrált](../active-directory/develop/quickstart-register-app.md)logikai alkalmazások kapcsolatainak engedélyezéséhez. Ez az Azure Data Lake-kapcsolaterőforrás-definíció például bemutatja, hogyan hivatkozzon az egyszerű szolgáltatás adatait kezelő sablonparaméterekre, és hogyan deklarálja a sablon ezeket a paramétereket:

**Kapcsolati erőforrás definíciója**

```json
{
   <other-template-objects>
   "type": "MICROSOFT.WEB/CONNECTIONS",
   "apiVersion": "2016-06-01",
   "name": "[parameters('azuredatalake_1_Connection_Name')]",
   "location": "[parameters('LogicAppLocation')]",
   "properties": {
      "api": {
         "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', 'resourceGroup().location', '/managedApis/', 'azuredatalake')]"
      },
      "displayName": "[parameters('azuredatalake_1_Connection_DisplayName')]",
      "parameterValues": {
         "token:clientId": "[parameters('azuredatalake_1_token:clientId')]",
         "token:clientSecret": "[parameters('azuredatalake_1_token:clientSecret')]",
         "token:TenantId": "[parameters('azuredatalake_1_token:TenantId')]",
         "token:grantType": "[parameters('azuredatalake_1_token:grantType')]"
      }
   }
}
```

| Attribútum | Leírás |
|-----------|-------------|
| `token:clientId` | A szolgáltatásnévhez társított alkalmazás vagy ügyfélazonosító |
| `token:clientSecret` | Az egyszerű szolgáltatáshoz társított kulcsérték |
| `token:TenantId` | Az Azure AD-bérlő címtárazonosítója |
| `token:grantType` | A kért támogatás típusa, `client_credentials`amelynek meg kell lennie. További információt a [Microsoft identity platform és az OAuth 2.0 ügyfélhitelesítő adatok folyamatában talál.](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md) |
|||

**Sablonparaméter-definíciói**

A sablon legfelső szintű `parameters` objektuma deklarálja a példakapcsolat paramétereit:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "azuredatalake_1_Connection_Name": {
        "type": "string",
        "defaultValue": "azuredatalake"
      },
      "azuredatalake_1_Connection_DisplayName": {
        "type": "string",
        "defaultValue": "<connection-name>"
      },
      "azuredatalake_1_token:clientId": {
        "type": "securestring",
        "metadata": {
          "description": "Client (or Application) ID of the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:clientSecret": {
        "type": "securestring",
        "metadata": {
          "description": "Client secret of the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:TenantId": {
        "type": "securestring",
        "metadata": {
          "description": "The tenant ID of for the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:resourceUri": {
        "type": "string",
        "metadata": {
          "description": "The resource you are requesting authorization to use."
        }
      },
      "azuredatalake_1_token:grantType": {
        "type": "string",
        "metadata": {
          "description": "Grant type"
        },
        "defaultValue": "client_credentials",
        "allowedValues": [
          "client_credentials"
        ]
      },
      // Other template parameters
   }
   // Other template objects
}
```

A szolgáltatástagok kal való együttműködésről az alábbi témakörökben olvashat bővebben:

* [Egyszerű szolgáltatás létrehozása az Azure Portal használatával](../active-directory/develop/howto-create-service-principal-portal.md)
* [Egyszerű Azure-szolgáltatás létrehozása az Azure PowerShell használatával](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps)
* [Egyszerű szolgáltatás létrehozása tanúsítvánnyal az Azure PowerShell használatával](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

<a name="parameter-references"></a>

## <a name="references-to-parameters"></a>Hivatkozások paraméterekre

A sablonparaméterekre való hivatkozáshoz sablonkifejezéseket használhat [sablonfüggvényekkel,](../azure-resource-manager/templates/template-functions.md)amelyeket a rendszer a telepítéskor kiértékel. A sablonkifejezések szögletes zárójeleket használnak (**[]**):

`"<attribute-name>": "[parameters('<template-parameter-name>')]"`

A munkafolyamat-definíciós paraméterekre való hivatkozáshoz [munkafolyamat-definíciós nyelvi kifejezéseket és függvényeket](../logic-apps/workflow-definition-language-functions-reference.md)használhat, amelyeket a program futásidőben értékel ki. Előfordulhat, hogy egyes sablonfüggvények és munkafolyamat-definíciós függvények neve megegyezik. A munkafolyamat-definíciós kifejezések az "at" szimbólummal (**@**):

`"<attribute-name>": "@parameters('<workflow-definition-parameter-name>')"`

Sablonparaméter-értékeket adhat át a munkafolyamat-definíciónak a logikai alkalmazás futásidőben való használatához. Kerülje azonban a sablonparaméterek, -kifejezések és -szintaxis használatát a munkafolyamat-definícióban, mert a Logic App Designer nem támogatja a sablonelemeket. A sablon szintaxisa és kifejezései bonyolíthatják a kódot a kifejezések kiértékelésekorban fennálló különbségek miatt.

Ehelyett kövesse ezeket az általános lépéseket a futásidőben használandó munkafolyamat-definíciós paraméterek deklarálásához és hivatkozásához, deklaráljon és hivatkozzon a központi telepítéssorán használandó sablonparaméterekre, és adja meg azokat az értékeket, amelyeket a telepítéssorán paraméterfájl használatával továbbít. A részletes részleteket a [témakör korábbi, a Munkafolyamat-definíció és -paraméterek](#workflow-definition-parameters) című szakaszában találja.

1. Hozza létre a sablont, és deklarálja a központi telepítéskor elfogadandó és használandó értékek sablonparamétereit.

1. A munkafolyamat-definícióban deklarálja az elfogadandó és futásidőben használandó értékek paramétereit. Ezután hivatkozhat ezekre az értékekre a munkafolyamat-definíció ban és azon belül.

1. A `parameters` munkafolyamat-definíción *kívül,* de a logikai alkalmazás erőforrás-definícióján *belül* lévő objektumban állítsa be a munkafolyamat-definíciós paraméterek értékeit a megfelelő sablonparaméterekre hivatkozva. Így sablonparaméter-értékeket adhat át a munkafolyamat-definícióparamétereinek.

1. A paraméterfájlban adja meg a sablon központi telepítéskor használandó értékeit.

<a name="full-example-template"></a>

## <a name="full-example-template"></a>Teljes példasablon

Az alábbiakban a témakör példái által használt paraméterezett mintasablon látható:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "defaultValue": "MyLogicApp",
         "metadata": {
            "description": "The resource name to use for the logic app"
         }
      },
      "LogicAppLocation": {
         "type": "string",
         "minLength": 1,
         "defaultValue": "[resourceGroup().location]",
         "metadata": {
            "description": "The resource location to use for the logic app"
         }
      },
      "office365_1_Connection_Name": {
         "type": "string",
         "defaultValue": "office365",
         "metadata": {
            "description": "The resource name to use for the Office 365 Outlook connection"
         }
      },
      "office365_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The display name to use for the Office 365 Outlook connection"
         }
      },
      "azureblob_1_Connection_Name": {
         "type": "string",
         "defaultValue": "azureblob",
         "metadata": {
            "description": "The resource name to use for the Azure Blob storage account connection"
         }
      },
      "azureblob_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         },

      },
      "azureblob_1_accountName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         }
      },
      "azureblob_1_accessKey": {
         "type": "securestring",
         "metadata": {
            "description": "Specify a valid primary/secondary storage account access key."
         }
      },
      "LogicAppIntegrationAccount": {
         "type":"string",
         "minLength": 1,
         "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/fabrikam-integration-account-rg/providers/Microsoft.Logic/integrationAccounts/fabrikam-integration-account",
         "metadata": {
            "description": "The ID to use for the integration account"
         }
      }
   },
   "variables": {},
   "resources": [
      {
         "properties": {
            "state": "Disabled",
            "integrationAccount": {
              "id": "[parameters('LogicAppIntegrationAccount')]"
            },
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {
                  "Create_blob": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              "name": "@parameters('$connections')['azureblob']['connectionId']"
                           }
                        },
                     },
                     "method": "post",
                     "body": "@triggerBody()?['Body']",
                     "path": "/datasets/default/files",
                     "queries": {
                        "folderPath": "/emails",
                        "name": "@triggerBody()?['Subject']",
                        "queryParametersSingleEncoded": true
                     },
                     "runAfter": {},
                     "runtimeConfiguration": {
                        "contentTransfer": {
                           "transferMode": "Chunked"
                        }
                     }
                  }
               },
               "parameters": {
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               "triggers": {
                  "When_a_new_email_arrives": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              "name": "@parameters('$connections')['office365']['connectionId']"
                           }
                        },
                        "method": "get",
                        "path": "/Mail/OnNewEmail",
                        "queries": {
                           "folderPath": "Inbox",
                           "importance": "Any",
                           "fetchOnlyWithAttachment": false,
                           "includeAttachments": false
                        }
                     },
                     "recurrence": {
                        "frequency": "Day",
                        "interval": 1
                     },
                     "splitOn": "@triggerBody()?['value']"
                  }
               },
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "$connections": {
                  "value": {
                     "azureblob": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
                        "connectionName": "[parameters('azureblob_1_Connection_Name')]"
                     },
                     "office365": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]",
                        "connectionName": "[parameters('office365_1_Connection_Name')]"
                     }
                  }
               }
            },
            "accessControl": {}
         },
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "dependsOn": [
            "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
            "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]"
         ]
      },
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('office365_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
                "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      },
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('azureblob_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]"
            },
            "displayName": "[parameters('azureblob_1_Connection_DisplayName')]",
            "parameterValues": {
               "accountName": "[parameters('azureblob_1_accountName')]",
               "accessKey": "[parameters('azureblob_1_accessKey')]"
            }
         }
      }
   ],
   "outputs": {}
}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Logikaialkalmazás-sablonok létrehozása](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
