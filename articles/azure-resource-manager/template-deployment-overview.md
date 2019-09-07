---
title: Azure Resource Manager-sablonok
description: Ismerteti, hogyan használhatók Azure Resource Manager sablonok az erőforrások telepítéséhez.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: tomfitz
ms.openlocfilehash: 4f273a04322246a2b4112c0b5b8a062732bab555
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390767"
---
# <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sablonok

A felhőbe való áttéréssel számos csapat agilis fejlesztési módszereket fogadott el. Ezek a csapatok gyorsan megismétlik. A felhőben ismételten és megbízhatóan kell telepíteni a megoldásaikat. A műveletek és a fejlesztés közötti felosztás eltűnt, mivel a csapatoknak egyetlen folyamatban kell kezelnie az infrastruktúrát és a forráskódot.

Ezen kihívások egyik megoldása az üzembe helyezés automatizálása és az infrastruktúra mint kód gyakorlatának használata. A kód használatával határozhatja meg, hogy mit kell telepíteni, és hogyan kezelheti a kódot a forráskódtal megegyező folyamaton keresztül. Az infrastruktúrát kódként tárolja a forrás adattárában, és annak verzióját.

Azure Resource Manager lehetővé teszi, hogy a Resource Manager-sablonokon keresztül programkódot implementáljon. A sablon egy JavaScript Object Notation (JSON) fájl, amely az Azure-megoldás infrastruktúráját és konfigurációját tartalmazza. A sablon deklaratív szintaxist használ, amely lehetővé teszi, hogy a telepítéshez szükséges programozási parancsok sorrendjének megírása nélkül adja meg az üzembe helyezni kívánt adatokat. A sablonban megadhatja a telepítendő erőforrásokat és az erőforrások tulajdonságait.

## <a name="benefits-of-resource-manager-templates"></a>A Resource Manager-sablonok előnyei

A Resource Manager-sablonok számos előnnyel rendelkeznek. A következőket teheti:

* A megoldáshoz tartozó összes erőforrást egy csoportként telepítheti, kezelheti és figyelheti, ahelyett, hogy ezeket az erőforrásokat külön kezeli.

* A fejlesztési életciklus során ismételten üzembe helyezheti a megoldást, és biztos lehet abban, hogy az erőforrásait konzisztens állapotban helyezik üzembe.

* Az infrastruktúrát nem szkriptek, hanem deklaratív sablonok segítségével kezelheti.

* Definiálja az erőforrások közötti függőségeket, hogy azok a megfelelő sorrendben legyenek telepítve.

* Azonnal kihasználhatja új verzióit és szolgáltatásait, mivel a többi fél nem igényel közbenső munkát.

## <a name="template-file"></a>Sablonfájl

A sablonon belül olyan sablon- [kifejezéseket](template-expressions.md) írhat, amelyek kibővítik a JSON képességeit. Ezek a kifejezések a Resource Manager által biztosított [függvények](resource-group-template-functions.md) használatát teszik elérhetővé.

A sablon a következő résszel rendelkezik:

* [Paraméterek](template-parameters.md) – az üzembe helyezés során értékeket adhat meg, amelyek lehetővé teszik, hogy ugyanaz a sablon más környezetekben is használható legyen.

* [Változók](template-variables.md) – a sablonokban használt értékek definiálása.

* [Felhasználó által definiált függvények](template-user-defined-functions.md) – a sablont leegyszerűsítő testreszabott függvények létrehozása.

* [Erőforrások](resource-group-authoring-templates.md#resources) – a telepítendő erőforrások meghatározása.

* [Kimenetek](template-outputs.md) – az üzembe helyezett erőforrások értékeit adja vissza.

## <a name="dependencies"></a>Függőségek

Az Azure Resource Manager a függőségek elemzésével biztosítja, hogy az erőforrások a megfelelő sorrendben jöjjenek létre. Ha egy erőforrás egy másik erőforráshoz tartozó értéket használ fel (például egy virtuális gép, amely egy tárfiókot igényel a lemezekhez), akkor beállíthat egy függőséget. További információ: [Függőségek meghatározása az Azure Resource Manager sablonokban](resource-group-define-dependencies.md).

## <a name="conditional-deployment"></a>Feltételes üzembe helyezés

Hozzáadhat egy erőforrást a sablonhoz, és igény szerint telepítheti is. A paramétereket általában egy paraméter értékével adja meg, amely jelzi, hogy az erőforrást telepíteni kell-e. További információ: [feltételes üzembe helyezés a Resource Manager-sablonokban](conditional-resource-deployment.md).

## <a name="create-multiple-instances"></a>Több példány létrehozása

Ahelyett, hogy többször is megismétli a JSON-blokkokat a sablonban, használhat egy másolási elemet egy változó, tulajdonság vagy erőforrás egynél több példányának megadásához. További információ: [erőforrás, tulajdonság vagy változó iteráció Azure Resource Manager-sablonokban](resource-group-create-multiple.md).

## <a name="export-templates"></a>Sablonok exportálása

Egy meglévő erőforráscsoport sablonjának lekéréséhez exportálhatja az erőforráscsoport aktuális állapotát, vagy megtekintheti az adott központi telepítéshez használt sablont. Az [exportált sablon](export-template-portal.md) megtekintése hasznos információkat nyújt a sablon szintaxisáról.

Amikor létrehoz egy megoldást a portálról, az automatikusan tartalmaz egy telepítési sablont. Nem szükséges teljesen új sablont létrehoznia, mivel kezdetben használhatja a sablont a megoldásához, majd testre szabhatja az adott igényeknek megfelelően. Példa: gyors útmutató [: Azure Resource Manager-sablonok létrehozása és üzembe helyezése a](./resource-manager-quickstart-create-templates-use-the-portal.md)Azure Portal használatával.

## <a name="template-deployment-process"></a>Template deployment folyamat

Egy sablon központi telepítésekor a Resource Manager elemzi a sablont, és átalakítja a szintaxisát REST API műveletekre. Például amikor a Resource Manager megkap egy sablont, amely a következő erőforrás-definíciót tartalmazza:

```json
"resources": [
  {
    "apiVersion": "2016-01-01",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "mystorageaccount",
    "location": "westus",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {
    }
  }
]
```

Átalakítja a definíciót a következő REST API-műveletté, amelyet a rendszer elküld a Microsoft.Storage erőforrás-szolgáltatónak:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/mystorageaccount?api-version=2016-01-01
REQUEST BODY
{
  "location": "westus",
  "properties": {
  }
  "sku": {
    "name": "Standard_LRS"
  },
  "kind": "Storage"
}
```

Ha az erőforrás már létezik az erőforráscsoporthoz, és a kérelem nem tartalmaz a tulajdonságok frissítését, akkor a rendszer semmilyen műveletet nem végez. Ha az erőforrás létezik, de a tulajdonságok megváltoztak, a rendszer frissíti a meglévő erőforrást. Ha az erőforrás nem létezik, a rendszer létrehozza az új erőforrást. Ezzel a módszerrel a sablon újból üzembe helyezhető, és biztos lehet abban, hogy az erőforrások konzisztens állapotban maradnak.

## <a name="template-design"></a>Sablon kialakítása

A sablonok és erőforráscsoportok meghatározási módja teljes mértékben Öntől függ, illetve attól, hogyan szeretné kezelni a megoldást. Például egyetlen sablon segítségével üzembe helyezheti a háromszintű alkalmazását egyetlen erőforráscsoportra.

![háromrétegű sablon](./media/template-deployment-overview/3-tier-template.png)

Azonban nem kell meghatároznia a teljes infrastruktúrát egyetlen sablonban. Gyakran érdemes felosztani a telepítési követelményeket konkrét, célspecifikus sablonokra. Ezeket a sablonokat egyszerűen újból felhasználhatja különböző megoldásokhoz. Egy adott megoldás telepítéséhez hozzon létre egy fősablont, amely összekapcsolja az összes szükséges sablont. Az alábbi kép bemutatja, hogyan telepíthető egy háromszintű megoldás egy olyan fölérendelt sablon segítségével, amely három beágyazott sablont tartalmaz.

![beágyazott rétegsablon](./media/template-deployment-overview/nested-tiers-template.png)

Ha elképzeli, hogy a rétegek külön életciklusokkal rendelkeznek, akkor a három szintet különálló erőforráscsoportokra telepítheti. Ügyeljen rá, hogy az erőforrások továbbra is kapcsolódhatnak egyéb erőforráscsoportok erőforrásaihoz.

![rétegsablon](./media/template-deployment-overview/tier-templates.png)

A beágyazott sablonokkal kapcsolatos további információkért lásd: [Kapcsolt sablonok használata az Azure Resource Manager eszközben](resource-group-linked-templates.md).

## <a name="next-steps"></a>További lépések

* További információ a sablonfájlok tulajdonságairól: [Azure Resource Manager sablonok struktúrájának és szintaxisának megismerése](resource-group-authoring-templates.md).
* A sablon fejlesztésének megkezdéséhez tekintse [meg a Visual Studio Code használata Azure Resource Manager sablonok létrehozásához](resource-manager-tools-vs-code.md)című témakört.


