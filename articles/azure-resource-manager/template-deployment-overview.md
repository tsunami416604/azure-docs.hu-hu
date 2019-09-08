---
title: Azure Resource Manager-sablonok
description: Ismerteti, hogyan használhatók Azure Resource Manager sablonok az erőforrások telepítéséhez.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/07/2019
ms.author: tomfitz
ms.openlocfilehash: 61e9bbabee969280c07521edb05d67ba68c0c58e
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/08/2019
ms.locfileid: "70802023"
---
# <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sablonok

A felhőbe való áttéréssel számos csapat agilis fejlesztési módszereket fogadott el. Ezek a csapatok gyorsan megismétlik. A megoldásoknak többször is üzembe kell helyezniük a felhőben, és tudniuk kell, hogy az infrastruktúra megbízható állapotban van. Mivel az infrastruktúra az iterációs folyamat részévé vált, a műveletek és a fejlesztés közötti felosztás eltűnt. A csapatoknak egységes folyamaton keresztül kell kezelniük az infrastruktúrát és az alkalmazás kódját.

Ezeknek a kihívásoknak a kielégítéséhez automatizálhatja az üzembe helyezéseket, és használhatja az infrastruktúra mint kód gyakorlatát. A kódban definiálni kell a telepítendő infrastruktúrát. Az infrastruktúra kódja a projekt részévé válik. Az alkalmazás kódjához hasonlóan az infrastruktúra kódját is tárolhatja egy forrás adattárban, és annak verzióját is. A csapatának bármelyike futtathatja a kódot, és telepíthet hasonló környezeteket is.

Ha az Azure-megoldások számára kíván infrastruktúra-kódot megvalósítani, használja Azure Resource Manager-sablonokat. A sablon egy JavaScript Object Notation (JSON) fájl, amely meghatározza a projekt infrastruktúráját és konfigurációját. A sablon deklaratív szintaxist használ, amely lehetővé teszi, hogy a telepítéshez szükséges programozási parancsok sorrendjének megírása nélkül adja meg az üzembe helyezni kívánt adatokat. A sablonban megadhatja a telepítendő erőforrásokat és az erőforrások tulajdonságait.

## <a name="benefits-of-resource-manager-templates"></a>A Resource Manager-sablonok előnyei

A Resource Manager-sablonok a következő előnyöket nyújtják:

* A megoldáshoz tartozó összes erőforrást egy csoportként telepítheti, kezelheti és figyelheti, ahelyett, hogy ezeket az erőforrásokat külön kezeli.

* A fejlesztési életciklus során ismételten üzembe helyezheti a megoldást, és biztos lehet abban, hogy az erőforrásait konzisztens állapotban helyezik üzembe.

* Az infrastruktúrát nem szkriptek, hanem deklaratív sablonok segítségével kezelheti.

Ha a Resource Manager-sablonok vagy a más infrastruktúra (code Services) használatával szeretne dönteni, vegye figyelembe a következő előnyöket a szolgáltatásokkal kapcsolatban:

* Az új Azure-szolgáltatások és-funkciók azonnal elérhetők a sablonokban. Amint egy erőforrás-szolgáltató új erőforrásokat vezet be, ezeket az erőforrásokat sablonokon keresztül is telepítheti. A Code Services más infrastruktúrájának használatával meg kell várnia, hogy harmadik felek az új erőforrásokhoz is implementálják az interfészeket.

* A sablonok központi telepítése a sablon egyetlen beküldésével történik, nem pedig több kényszerítő parancs használatával. A Resource Manager összehangolja az egymástól függő erőforrások telepítését, hogy azok a megfelelő sorrendben jöjjenek létre. Elemzi a sablont, és meghatározza a telepítés helyes sorrendjét az erőforrások közötti hivatkozások alapján.

   ![Template deployment összehasonlítása](./media/template-deployment-overview/template-processing.png)

* A sablonok központi telepítéseit a Azure Portal követik nyomon. Áttekintheti az üzembe helyezési előzményeket, és információkat kaphat a sablon üzembe helyezéséről. Megtekintheti a telepített sablont, az átadott paramétereket és a kimeneti értékeket. Más infrastruktúrát, mint a Code Services a portálon keresztül nem követik nyomon.

   ![Üzembe helyezési előzmények](./media/template-deployment-overview/deployment-history.png)

* A sablonok üzembe helyezése a repülés előtti ellenőrzésen megy keresztül. A Resource Manager a telepítés megkezdése előtt ellenőrzi a sablont, hogy a telepítés sikeres legyen-e. Az üzembe helyezés kevésbé valószínű, hogy egy félig kész állapotban leáll.

* Ha [Azure-szabályzatokat](../governance/policy/overview.md)használ, a szabályzat szervizelése a nem megfelelő erőforrásokon történik a sablonokkal való üzembe helyezéskor.

* A Microsoft üzembe helyezési [tervezeteket](../governance/blueprints/overview.md) biztosít a szabályozási és megfelelőségi követelmények teljesítéséhez. Ezek a tervrajzok előre elkészített sablonokat is tartalmaznak a különböző architektúrák esetében.

## <a name="idempotent"></a>Idempotens

A idempotens egyszerűen annyit jelent, hogy ugyanazokat a műveleteket többször is futtatja, és ugyanazt az eredményt kapja. A Resource Manager-sablonok üzembe helyezése a idempotens. Egyszerre több alkalommal is telepítheti ugyanazt a sablont, és ugyanazokat az erőforrásokat is lekérheti. Ez a koncepció azért fontos, mert konzisztens eredményeket biztosít, akár egy sablon meglévő erőforráscsoporthoz való üzembe helyezését, akár egy új erőforráscsoport üzembe helyezését jelenti.

Tegyük fel, hogy három erőforrást helyezett üzembe egy erőforráscsoporthoz, majd eldönti, hogy egy negyedik erőforrást kell felvennie. Ahelyett, hogy olyan új sablont hozna létre, amely csak az új erőforrást tartalmazza, felveheti a negyedik erőforrást a meglévő sablonba. Ha az új sablont olyan erőforráscsoporthoz telepíti, amely már rendelkezik három erőforrással, az erőforrás-kezelő kiszámítja, hogy mely műveleteket kell végrehajtania.

Ha az erőforrás létezik az erőforráscsoporthoz, és a kérelem nem tartalmazza a tulajdonságok frissítését, a rendszer nem végez műveletet. Ha az erőforrás létezik, de a tulajdonságok megváltoztak, a rendszer frissíti a meglévő erőforrást. Ha az erőforrás nem létezik, a rendszer létrehozza az új erőforrást.

Biztos benne, hogy az üzembe helyezés befejezésekor az erőforrások mindig a várt állapotban vannak.

## <a name="template-file"></a>Sablonfájl

A sablonon belül olyan sablon- [kifejezéseket](template-expressions.md) írhat, amelyek kibővítik a JSON képességeit. Ezek a kifejezések a Resource Manager által biztosított [függvények](resource-group-template-functions.md) használatát teszik elérhetővé.

A sablon a következő résszel rendelkezik:

* [Paraméterek](template-parameters.md) – az üzembe helyezés során értékeket adhat meg, amelyek lehetővé teszik, hogy ugyanaz a sablon más környezetekben is használható legyen.

* [Változók](template-variables.md) – a sablonokban újra felhasználható értékek definiálása. Ezek a paraméterek értékeiből állíthatók össze.

* [Felhasználó által definiált függvények](template-user-defined-functions.md) – a sablont leegyszerűsítő testreszabott függvények létrehozása.

* [Erőforrások](resource-group-authoring-templates.md#resources) – a telepítendő erőforrások meghatározása.

* [Kimenetek](template-outputs.md) – az üzembe helyezett erőforrások értékeit adja vissza.

## <a name="template-features"></a>Sablon szolgáltatásai

A Resource Manager a függőségek elemzésével biztosítja, hogy az erőforrások a megfelelő sorrendben jöjjenek létre. A legtöbb függőség implicit módon van meghatározva. A függőséget azonban explicit módon beállíthatja úgy, hogy az egy erőforrást egy másik erőforrás előtt telepítsen. További információ: [Függőségek meghatározása az Azure Resource Manager sablonokban](resource-group-define-dependencies.md).

Hozzáadhat egy erőforrást a sablonhoz, és igény szerint telepítheti is. A paramétereket általában egy paraméter értékével adja meg, amely jelzi, hogy az erőforrást telepíteni kell-e. További információ: [feltételes üzembe helyezés a Resource Manager-sablonokban](conditional-resource-deployment.md).

Ahelyett, hogy többször is megismétli a JSON-blokkokat a sablonban, használhat egy másolási elemet egy változó, tulajdonság vagy erőforrás egynél több példányának megadásához. További információ: [erőforrás, tulajdonság vagy változó iteráció Azure Resource Manager-sablonokban](resource-group-create-multiple.md).

## <a name="export-templates"></a>Sablonok exportálása

Egy meglévő erőforráscsoport sablonjának lekéréséhez exportálhatja az erőforráscsoport aktuális állapotát, vagy megtekintheti az adott központi telepítéshez használt sablont. Az [exportált sablon](export-template-portal.md) megtekintése hasznos információkat nyújt a sablon szintaxisáról.

Amikor létrehoz egy megoldást a portálról, az automatikusan tartalmaz egy telepítési sablont. Nem szükséges teljesen új sablont létrehoznia, mivel kezdetben használhatja a sablont a megoldásához, majd testre szabhatja az adott igényeknek megfelelően. Példa: gyors útmutató [: Azure Resource Manager-sablonok létrehozása és üzembe helyezése a](./resource-manager-quickstart-create-templates-use-the-portal.md)Azure Portal használatával.

## <a name="template-deployment-process"></a>Template deployment folyamat

Sablon központi telepítésekor a Resource Manager átalakítja a sablont REST API műveletekre. Például amikor a Resource Manager megkap egy sablont, amely a következő erőforrás-definíciót tartalmazza:

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
* A Resource Manager szolgáltatás bevezetéséhez, beleértve annak felügyeleti funkcióit, tekintse meg a [Azure Resource Manager áttekintése](resource-group-overview.md)című témakört.

