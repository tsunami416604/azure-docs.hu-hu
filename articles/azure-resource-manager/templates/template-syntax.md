---
title: Sablon szerkezete és szintaxisa
description: Az Azure Resource Manager-sablonok szerkezetét és tulajdonságait ismerteti deklaratív JSON-szintaxissal.
ms.topic: conceptual
ms.date: 04/20/2020
ms.openlocfilehash: 60d800eb5251fb3454ba60a67bd109261c6ff9d4
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687866"
---
# <a name="understand-the-structure-and-syntax-of-arm-templates"></a>Az ARM sablonok szerkezetének és szintaxisának megismerése

Ez a cikk egy Azure Resource Manager (ARM) sablon szerkezetét ismerteti. Bemutatja a sablon különböző szakaszait és az ezekben a szakaszokban elérhető tulajdonságokat.

Ez a cikk azoknak a felhasználóknak készült, akik ismerik az ARM sablonokat. Részletes információkat nyújt a sablon szerkezetéről. A sablon létrehozásának folyamatát végigvezető részletes oktatóanyagról az [Oktatóanyag: Az első Azure Resource Manager-sablon létrehozása és üzembe helyezése](template-tutorial-create-first-template.md)című témakörben olvashat.

## <a name="template-format"></a>Sablon formátuma

A legegyszerűbb struktúrában a sablon a következő elemekkel rendelkezik:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "",
  "apiProfile": "",
  "parameters": {  },
  "variables": {  },
  "functions": [  ],
  "resources": [  ],
  "outputs": {  }
}
```

| Elem neve | Kötelező | Leírás |
|:--- |:--- |:--- |
| $schema |Igen |A sablonnyelv verzióját leíró JSON-sémafájl helye. A használt verziószám a központi telepítés hatókörétől és a JSON-szerkesztőtől függ.<br><br>Ha a VS Code szolgáltatást [használja az Azure Resource Manager eszközbővítményével,](use-vs-code-to-create-template.md)használja a legújabb verziót az erőforráscsoport-telepítésekhez:<br>`https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#`<br><br>Előfordulhat, hogy más szerkesztők (beleértve a Visual Studio-t is) nem tudják feldolgozni ezt a sémát. Azok számára, szerkesztők, használja:<br>`https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`<br><br>Az előfizetések központi telepítéséhez használja a következőket:<br>`https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`<br><br>A felügyeleti csoport központi telepítéseihez használja a következőket:<br>`https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#`<br><br>Bérlői telepítések esetén használja a következőket:<br>`https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#` |
| contentVersion |Igen |A sablon verziója (például 1.0.0.0). Ehhez az elemhez bármilyen értéket megadhat. Ezzel az értékkel dokumentálhatja a sablon jelentős változásait. Erőforrások sablon használatával történő telepítésekor ez az érték biztosíthatja, hogy a megfelelő sablont használja. |
| apiProfile (apiProfile) |Nem | Egy API-verzió, amely az erőforrástípusok API-verzióinak gyűjteményeként szolgál. Ezzel az értékkel elkerülheti, hogy a sablon minden egyes erőforrásához API-verziókat kelljen megadnia. Amikor megad egy API-profil verziót, és nem ad meg API-verziót az erőforrástípushoz, az Erőforrás-kezelő a profilban definiált erőforrástípus API-verzióját használja.<br><br>Az API-profil tulajdonság különösen hasznos, ha egy sablont különböző környezetekben, például az Azure Stack és a globális Azure üzembe helyezésekor. Az API-profil verziója segítségével győződjön meg arról, hogy a sablon automatikusan használja a mindkét környezetben támogatott verziókat. Az aktuális API-profilverziók és a profilban definiált erőforrások API-verzióinak listáját az [API-profil című témakörben tetszetős.](https://github.com/Azure/azure-rest-api-specs/tree/master/profile)<br><br>További információ: [Verziók nyomon követése API-profilok használatával.](templates-cloud-consistency.md#track-versions-using-api-profiles) |
| [Paraméterek](#parameters) |Nem |Az erőforrások központi telepítésének testreszabásához a központi telepítés végrehajtásakor megadott értékek. |
| [Változók](#variables) |Nem |JSON-töredékként használt értékek a sablonnyelvi kifejezések egyszerűsítése érdekében. |
| [Funkciók](#functions) |Nem |A sablonon belül elérhető, felhasználó által definiált függvények. |
| [Erőforrások](#resources) |Igen |Erőforráscsoportokban vagy előfizetésekben üzembe helyezett vagy frissített erőforrástípusok. |
| [Kimenetek](#outputs) |Nem |A telepítés után visszaadott értékek. |

Minden elemnek van általa beállítható tulajdonsága. Ez a cikk részletesebben ismerteti a sablon szakaszait.

## <a name="parameters"></a>Paraméterek

A sablon paraméterek szakaszában megadhatja, hogy mely értékeket adhatja meg az erőforrások üzembe helyezésekor. Egy sablonban legfeljebb 256 paraméter tetsző. A paraméterek számát csökkentheti a több tulajdonságot tartalmazó objektumok használatával.

Egy paraméter elérhető tulajdonságai a következők:

```json
"parameters": {
  "<parameter-name>" : {
    "type" : "<type-of-parameter-value>",
    "defaultValue": "<default-value-of-parameter>",
    "allowedValues": [ "<array-of-allowed-values>" ],
    "minValue": <minimum-value-for-int>,
    "maxValue": <maximum-value-for-int>,
    "minLength": <minimum-length-for-string-or-array>,
    "maxLength": <maximum-length-for-string-or-array-parameters>,
    "metadata": {
      "description": "<description-of-the parameter>"
    }
  }
}
```

| Elem neve | Kötelező | Leírás |
|:--- |:--- |:--- |
| paraméter-név |Igen |A paraméter neve. Érvényes JavaScript-azonosítónak kell lennie. |
| type |Igen |A paraméterérték típusa. Az engedélyezett típusok és értékek a következők: **karakterlánc**, **securestring**, **int**, **bool**, **object**, **secureObject**és **array**. Lásd: [Adattípusok](#data-types). |
| defaultValue |Nem |A paraméter alapértelmezett értéke, ha nincs megadva érték a paraméterhez. |
| allowedValues (engedélyezett értékek) |Nem |A paraméter engedélyezett értékeinek tömbje, hogy megbizonyosodjon arról, hogy a megfelelő érték van megadva. |
| minValue |Nem |Az int type paraméterek minimális értéke, ez az érték tartalmazza. |
| maxValue érték |Nem |Az int type paraméterek maximális értéke, ez az érték tartalmazza. |
| Minlength |Nem |A karakterlánc, a biztonságos karakterlánc és a tömbtípus paramétereinek minimális hossza, ez az érték tartalmazza. |
| Maxlength |Nem |A karakterlánc, a biztonságos karakterlánc és a tömbtípus paramétereinek maximális hossza, ez az érték tartalmazza. |
| leírás |Nem |A portálon keresztül a felhasználók számára megjelenített paraméter leírása. További információt a Megjegyzések a sablonokban című [témakörben talál.](#comments) |

A paraméterek használatára vonatkozó példákat az [Azure Resource Manager-sablonok paraméterei (Paraméterek az Azure Resource Manager-sablonok) témakörben talál.](template-parameters.md)

### <a name="data-types"></a>Adattípusok

Szövegközi paraméterekként átadott egész számok esetén az értékek tartományát korlátozhatja a telepítéshez használt SDK vagy parancssori eszköz. Ha például a PowerShell használatával telepít egy sablont, egész típusú lehet -2147483648 és 2147483647. A korlátozás elkerülése érdekében adjon meg nagy egész értékeket egy [paraméterfájlban.](parameter-files.md) Az erőforrástípusok saját korlátokat alkalmaznak az egész tulajdonságokra.

Amikor logikai és egész értékeket ad meg a sablonban, ne vegye körül az értéket idézőjelekkel. A karakterláncok kezdő és záró értékei idézőjelek közé.

A tárgyak bal merevítővel kezdődnek, és jobb merevítővel végződnek. A tömbök bal oldali zárójelben kezdődnek, és a jobb oldali zárójelben végződnek.

A biztonságos karakterláncok és a biztonságos objektumok nem olvashatók az erőforrások üzembe helyezése után.

Az adattípusok mintáiról a [Paramétertípus-formátumok (Paramétertípus-formátumok) (Paramétertípus-formátumok) (Paramétertípus-formátumok) (Paramétertípus-formátumok) (Paramétertípus-formátumok](parameter-files.md#parameter-type-formats)

## <a name="variables"></a>Változók

A változók szakaszban olyan értékeket hozhat létre, amelyek a sablonban használhatók. Nem kell változókat definiálnia, de gyakran leegyszerűsítik a sablont az összetett kifejezések csökkentésével.

A következő példa a változók definiálásának rendelkezésre álló lehetőségeit mutatja be:

```json
"variables": {
  "<variable-name>": "<variable-value>",
  "<variable-name>": {
    <variable-complex-type-value>
  },
  "<variable-object-name>": {
    "copy": [
      {
        "name": "<name-of-array-property>",
        "count": <number-of-iterations>,
        "input": <object-or-value-to-repeat>
      }
    ]
  },
  "copy": [
    {
      "name": "<variable-array-name>",
      "count": <number-of-iterations>,
      "input": <object-or-value-to-repeat>
    }
  ]
}
```

A változók `copy` több értékének létrehozásáról a [Változó ismétlés](copy-variables.md)című témakörben talál további információt.

A változók használatára vonatkozó példákat az [Azure Resource Manager változók sablonja ismerteti.](template-variables.md)

## <a name="functions"></a>Functions

A sablonon belül létrehozhatja saját funkcióit. Ezek a funkciók a sablonban használhatók. Általában olyan bonyolult kifejezéseket definiál, amelyeket nem szeretne megismételni a sablonban. A felhasználó által definiált függvényeket a sablonokban támogatott kifejezésekből és [függvényekből](template-functions.md) hozhatja létre.

A felhasználói függvény ek meghatározásakor vannak bizonyos korlátozások:

* A függvény nem tud hozzáférni a változókhoz.
* A függvény csak a függvényben definiált paramétereket használhatja. Ha a [paraméterek függvényt](template-functions-deployment.md#parameters) egy felhasználó által definiált függvényen belül használja, akkor az adott függvény paramétereire van korlátozva.
* A függvény nem hívhat meg más felhasználó által definiált függvényeket.
* A függvény nem tudja használni a [referenciafüggvényt.](template-functions-resource.md#reference)
* A függvény paraméterei nem rendelkezhetnek alapértelmezett értékekkel.

```json
"functions": [
  {
    "namespace": "<namespace-for-functions>",
    "members": {
      "<function-name>": {
        "parameters": [
          {
            "name": "<parameter-name>",
            "type": "<type-of-parameter-value>"
          }
        ],
        "output": {
          "type": "<type-of-output-value>",
          "value": "<function-return-value>"
        }
      }
    }
  }
],
```

| Elem neve | Kötelező | Leírás |
|:--- |:--- |:--- |
| névtér |Igen |Névtér az egyéni függvényekhez. Ezzel elkerülheti a sablonfüggvényekkel való elnevezési ütközéseket. |
| függvénynév |Igen |Az egyéni függvény neve. A függvény hívásakor kombinálja a függvény nevét a névtérrel. Ha például egy uniqueName nevű függvényt szeretne meghívni `"[contoso.uniqueName()]"`a contoso névtérben, használja a programot. |
| paraméter-név |Nem |Az egyéni függvényben használandó paraméter neve. |
| paraméter-érték |Nem |A paraméterérték típusa. Az engedélyezett típusok és értékek a következők: **karakterlánc**, **securestring**, **int**, **bool**, **object**, **secureObject**és **array**. |
| kimeneti típus |Igen |A kimeneti érték típusa. A kimeneti értékek ugyanazokat a típusokat támogatják, mint a függvénybemeneti paraméterek. |
| kimeneti érték |Igen |A rendszer kiértékeli és visszaadja a függvényből a sablon nyelvi kifejezést. |

Az egyéni függvények használatára vonatkozó példákat az [Azure Resource Manager sablon Felhasználó által definiált függvények (Felhasználó által definiált) témakörben talál.](template-user-defined-functions.md)

## <a name="resources"></a>További források

Az erőforrások szakaszban megadhatja az üzembe helyezett vagy frissített erőforrásokat.

Az erőforrásokat a következő struktúrával határozhatja meg:

```json
"resources": [
  {
      "condition": "<true-to-deploy-this-resource>",
      "type": "<resource-provider-namespace/resource-type-name>",
      "apiVersion": "<api-version-of-resource>",
      "name": "<name-of-the-resource>",
      "comments": "<your-reference-notes>",
      "location": "<location-of-resource>",
      "dependsOn": [
          "<array-of-related-resource-names>"
      ],
      "tags": {
          "<tag-name1>": "<tag-value1>",
          "<tag-name2>": "<tag-value2>"
      },
      "sku": {
          "name": "<sku-name>",
          "tier": "<sku-tier>",
          "size": "<sku-size>",
          "family": "<sku-family>",
          "capacity": <sku-capacity>
      },
      "kind": "<type-of-resource>",
      "copy": {
          "name": "<name-of-copy-loop>",
          "count": <number-of-iterations>,
          "mode": "<serial-or-parallel>",
          "batchSize": <number-to-deploy-serially>
      },
      "plan": {
          "name": "<plan-name>",
          "promotionCode": "<plan-promotion-code>",
          "publisher": "<plan-publisher>",
          "product": "<plan-product>",
          "version": "<plan-version>"
      },
      "properties": {
          "<settings-for-the-resource>",
          "copy": [
              {
                  "name": ,
                  "count": ,
                  "input": {}
              }
          ]
      },
      "resources": [
          "<array-of-child-resources>"
      ]
  }
]
```

| Elem neve | Kötelező | Leírás |
|:--- |:--- |:--- |
| Feltétel | Nem | Logikai érték, amely azt jelzi, hogy az erőforrás lesz-e kiépítve a központi telepítés során. Amikor `true`az erőforrás jön létre a telepítés során. Ha `false`a program kihagyja az erőforrást a központi telepítéshez. Lásd [a feltételt.](conditional-resource-deployment.md) |
| type |Igen |Az erőforrás típusa. Ez az érték az erőforrás-szolgáltató névterének és az erőforrástípusnak (például **Microsoft.Storage/storageAccounts)** kombinációja. A rendelkezésre álló értékek meghatározásáról a [sablon hivatkozása](/azure/templates/)oldalon olvashat. Egy gyermek erőforrás esetében a típus formátuma attól függ, hogy a szülő erőforrásba van-e ágyazva, vagy a szülőerőforráson kívül van definiálva. Lásd: [Név és típus beállítása a gyermekerőforrásokhoz](child-resource-name-type.md). |
| apiVersion |Igen |A REST API-t az erőforrás létrehozásához használandó verzió. A rendelkezésre álló értékek meghatározásáról a [sablon hivatkozása](/azure/templates/)oldalon olvashat. |
| név |Igen |Az erőforrás neve. A névnek követnie kell az RFC3986-ban meghatározott URI-összetevőkre vonatkozó korlátozásokat. Az Azure-szolgáltatások, amelyek az erőforrás nevét külső felek számára ellenőrzik a nevet, hogy megbizonyosodjon arról, hogy nem egy kísérlet egy másik identitás meghamisítására. Egy gyermek erőforrás esetében a név formátuma attól függ, hogy a szülő erőforrásba van-e ágyazva, vagy a szülőerőforráson kívül van definiálva. Lásd: [Név és típus beállítása a gyermekerőforrásokhoz](child-resource-name-type.md). |
| Hozzászólások |Nem |A sablonban lévő erőforrások dokumentálásához szükséges jegyzetek. További információt a Megjegyzések a sablonokban című [témakörben talál.](template-syntax.md#comments) |
| location |Változó |A megadott erőforrás támogatott földrajzi helyei. Bármelyik rendelkezésre álló helyet kiválaszthatja, de általában célszerű olyat választani, amely közel áll a felhasználókhoz. Általában érdemes az egymással egymással kapcsolatba lépő erőforrásokat ugyanabban a régióban elhelyezni. A legtöbb erőforrástípushoz hely szükséges, de bizonyos típusok (például szerepkör-hozzárendelés) nem igényelnek helyet. Lásd: [Erőforrás helyének beállítása](resource-location.md). |
| dependsOn |Nem |Az erőforrás üzembe helyezése előtt üzembe helyezendő erőforrások. Az Erőforrás-kezelő kiértékeli az erőforrások közötti függőségeket, és a megfelelő sorrendben telepíti azokat. Ha az erőforrások nem függnek egymástól, akkor párhuzamosan vannak telepítve. Az érték lehet egy erőforrásnevek vagy erőforrás-egyedi azonosítók vesszővel tagolt listája. Csak a sablonban üzembe helyezett erőforrásokat sorolja fel. A sablonban nem definiált erőforrásoknak már létezniük kell. Kerülje a felesleges függőségek hozzáadását, mivel lelassíthatják a központi telepítést, és körkörös függőségeket hozhatnak létre. A függőségek beállításával kapcsolatos útmutatásért olvassa [el a Függőségek definiálása az Azure Resource Manager-sablonokban](define-resource-dependency.md). |
| címkét |Nem |Az erőforráshoz társított címkék. Címkék alkalmazása az erőforrások előfizetésen kénti logikai rendszerezéséhez. |
| Sku | Nem | Egyes erőforrások lehetővé teszik a termékváltozatot meghatározó értékek üzembe helyezését. Megadhatja például egy tárfiók redundanciájának típusát. |
| Fajta | Nem | Egyes erőforrások olyan értéket engedélyeznek, amely meghatározza a telepített erőforrás típusát. Megadhatja például a létrehozandó Cosmos DB típusát. |
| Másol |Nem |Ha egynél több példányra van szükség, a létrehozandó erőforrások száma. Az alapértelmezett mód párhuzamos. Adja meg a soros módot, ha nem szeretné, hogy az összes vagy az erőforrások egyszerre legyenek telepítve. További információt az [Erőforrások több példányának létrehozása az Azure Resource Managerben](copy-resources.md)című témakörben talál. |
| Terv | Nem | Egyes erőforrások lehetővé teszik a tervet meghatározó értékek üzembe helyezését. Megadhatja például egy virtuális gép piactéri lemezképét. |
| properties |Nem |Erőforrás-specifikus konfigurációs beállítások. A tulajdonságok értékei megegyeznek az erőforrás létrehozásához a REST API-művelet (PUT metódus) kérelemtörzsében megadott értékekkel. Egy tulajdonság több példányának létrehozásához is megadhat másolati tömböt. A rendelkezésre álló értékek meghatározásáról a [sablon hivatkozása](/azure/templates/)oldalon olvashat. |
| resources |Nem |A definiált erőforrástól függő gyermekerőforrások. Csak olyan erőforrástípusokat adjon meg, amelyeket a szülő erőforrás sémája engedélyez. A szülő erőforrástól való függőség nem hallgatólagos. Ezt a függőséget explicit módon meg kell határoznia. Lásd: [Név és típus beállítása a gyermekerőforrásokhoz](child-resource-name-type.md). |

## <a name="outputs"></a>Kimenetek

A Kimenetek szakaszban adja meg az üzembe helyezésből visszaadott értékeket. Általában értékeket ad vissza az üzembe helyezett erőforrásokból.

A következő példa egy kimeneti definíció szerkezetét mutatja be:

```json
"outputs": {
  "<output-name>": {
    "condition": "<boolean-value-whether-to-output-value>",
    "type": "<type-of-output-value>",
    "value": "<output-value-expression>",
    "copy": {
      "count": <number-of-iterations>,
      "input": <values-for-the-variable>
    }
  }
}
```

| Elem neve | Kötelező | Leírás |
|:--- |:--- |:--- |
| kimeneti név |Igen |A kimeneti érték neve. Érvényes JavaScript-azonosítónak kell lennie. |
| Feltétel |Nem | Logikai érték, amely azt jelzi, hogy a rendszer visszaadja-e ezt a kimeneti értéket. Ha `true`az érték szerepel a központi telepítés kimenetében. Amikor `false`a kimeneti érték kimarad ehhez a központi telepítéshez. Ha nincs megadva, az `true`alapértelmezett érték . |
| type |Igen |A kimeneti érték típusa. A kimeneti értékek ugyanazokat a típusokat támogatják, mint a sablonbemeneti paraméterek. Ha megadja a **biztonságos karakterláncot** a kimeneti típushoz, az érték nem jelenik meg a központi telepítési előzményekben, és nem olvasható be egy másik sablonból. Ha egynél több sablonban szeretne titkos értéket használni, tárolja a titkos kulcsot egy Key Vaultban, és hivatkozzon a titkos kulcsra a paraméterfájlban. További információ: [Az Azure Key Vault használata a biztonságos paraméter értékének a telepítés során történő átadásához](key-vault-parameter.md)című témakörben található. |
| érték |Nem |Sablonnyelvi kifejezés, amely kiértékelésre kerül, és kimeneti értékként ad vissza. Adja meg **az értéket** vagy **a másolást.** |
| Másol |Nem | Egy kimenet egynél több értékének a visszaadására szolgál. Adja meg **az értéket** vagy **a másolást.** További információ: [Output iteráció in Azure Resource Manager templates](copy-outputs.md). |

A kimenetek használatára vonatkozó példákat az [Azure Resource Manager-sablon kimenetei .For](template-outputs.md)examples of how to use outputs, for outputs, for outputs, for outputs for Azure Resource Manager template .

<a id="comments" />

## <a name="comments-and-metadata"></a>Megjegyzések és metaadatok

Néhány lehetősége van arra, hogy megjegyzéseket és metaadatokat adjon a sablonhoz.

### <a name="comments"></a>Megjegyzések

Szövegközi megjegyzések esetén használhatja `//` `/* ... */` vagy használhatja, de ez a szintaxis nem működik minden eszközzel. A portálsablon-szerkesztő nem használható szövegközi megjegyzésekkel rendelkező sablonokon való munkára. Ha hozzáadja ezt a stílust, győződjön meg róla, hogy a használt eszközök támogatják a beépített JSON megjegyzéseket.

> [!NOTE]
> A sablonok üzembe helyezéséhez megjegyzéseket az Azure CLI 2.3.0-s vagy régebbi verzióval, a `--handle-extended-json-format` kapcsolót kell használnia.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[parameters('location')]", //defaults to resource group location
  "dependsOn": [ /* storage account and network interface must be deployed first */
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

A Visual Studio-kódban az [Azure Resource Manager Tools bővítmény](use-vs-code-to-create-template.md#install-resource-manager-tools-extension) automatikusan felismeri az Erőforrás-kezelő sablont, és ennek megfelelően módosítja a nyelvi módot. Ha az **Azure Resource Manager-sablon** a VS-kód jobb alsó sarkában jelenik meg, használhatja a szövegközi megjegyzéseket. A szövegközi megjegyzések már nem érvénytelenként vannak megjelölve.

![Visual Studio Code Azure Resource Manager sablonmód](./media/template-syntax/resource-manager-template-editor-mode.png)

### <a name="metadata"></a>Metaadatok

A sablonban `metadata` szinte bárhol hozzáadhat egy objektumot. Az Erőforrás-kezelő figyelmen kívül hagyja az objektumot, de a JSON-szerkesztő figyelmeztetheti, hogy a tulajdonság érvénytelen. Az objektumban adja meg a szükséges tulajdonságokat.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "metadata": {
    "comments": "This template was developed for demonstration purposes.",
    "author": "Example Name"
  },
```

Paraméterek **parameters**esetén adjon `metadata` hozzá egy `description` tulajdonsággal rendelkező objektumot.

```json
"parameters": {
  "adminUsername": {
    "type": "string",
    "metadata": {
      "description": "User name for the Virtual Machine."
    }
  },
```

Amikor a sablont a portálon keresztül telepíti, a leírásban megadott szöveg automatikusan tippként szolgál az adott paraméterhez.

![Paramétertipp megjelenítése](./media/template-syntax/show-parameter-tip.png)

**Erőforrásokhoz**adjon `comments` hozzá egy elemet vagy metaadat-objektumot. A következő példa egy megjegyzéselemet és egy metaadat-objektumot is bemutat.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2018-07-01",
    "name": "[concat('storage', uniqueString(resourceGroup().id))]",
    "comments": "Storage account used to store VM disks",
    "location": "[parameters('location')]",
    "metadata": {
      "comments": "These tags are needed for policy compliance."
    },
    "tags": {
      "Dept": "[parameters('deptName')]",
      "Environment": "[parameters('environment')]"
    },
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
  }
]
```

**Kimenetek**esetén adjon hozzá egy metaadat-objektumot a kimeneti értékhez.

```json
"outputs": {
  "hostname": {
    "type": "string",
    "value": "[reference(variables('publicIPAddressName')).dnsSettings.fqdn]",
    "metadata": {
      "comments": "Return the fully qualified domain name"
    }
  },
```

Metaadat-objektum nem adhat hozzá metaadat-objektumot a felhasználó által definiált függvényekhez.

## <a name="multi-line-strings"></a>Többsoros karakterláncok

A karakterláncokat több sorra is felbonthatja. Lásd például a helytulajdonságot és a következő JSON-példában található megjegyzések egyikét.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[
    parameters('location')
    ]", //defaults to resource group location
  /*
    storage account and network interface
    must be deployed first
  */
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

A sablonok üzembe helyezéséhez többsoros karakterláncok használatával Azure CLI 2.3.0-s `--handle-extended-json-format` vagy újabb verzióval, a kapcsolót kell használnia.

## <a name="next-steps"></a>További lépések

* A különböző megoldástípusokhoz használható teljes sablonok megtekintéséhez lásd: [Azure gyorsindítási sablonok](https://azure.microsoft.com/documentation/templates/).
* A sablonokon belül használható függvényekről az [Azure Resource Manager sablonfüggvényei](template-functions.md)című témakörben olvashat részletesen.
* Ha több sablont szeretne kombinálni a telepítés során, olvassa el [a Csatolt sablonok használata az Azure Resource Manager rel](linked-templates.md)című témakört.
* A sablonok létrehozásával kapcsolatos javaslatokért tekintse meg az [Azure Resource Manager sablonokkal kapcsolatos gyakorlati tanácsait.](template-best-practices.md)
* A Resource Manager-sablonok létrehozásával kapcsolatos javaslatokat, amelyeket az összes Azure-környezetben és az Azure Stackben használhat, olvassa el [az Azure Resource Manager-sablonok fejlesztése a felhőkonzisztenciához](templates-cloud-consistency.md)című témakört.
