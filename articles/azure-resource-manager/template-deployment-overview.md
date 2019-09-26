---
title: Azure Resource Manager-sablonok
description: Ismerteti, hogyan használhatók Azure Resource Manager sablonok az erőforrások telepítéséhez.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: tomfitz
ms.openlocfilehash: 96f140cfa5e6151ad53ca242d1fc87ba3397316e
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300152"
---
# <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sablonok

A felhőbe való áttéréssel számos csapat agilis fejlesztési módszereket fogadott el. Ezek a csapatok gyorsan megismétlik. A megoldásoknak többször is üzembe kell helyezniük a felhőben, és tudniuk kell, hogy az infrastruktúra megbízható állapotban van. Mivel az infrastruktúra az iterációs folyamat részévé vált, a műveletek és a fejlesztés közötti felosztás eltűnt. A csapatoknak egységes folyamaton keresztül kell kezelniük az infrastruktúrát és az alkalmazás kódját.

Ezeknek a kihívásoknak a kielégítéséhez automatizálhatja az üzembe helyezéseket, és használhatja az infrastruktúra mint kód gyakorlatát. A kódban definiálni kell a telepítendő infrastruktúrát. Az infrastruktúra kódja a projekt részévé válik. Az alkalmazás kódjához hasonlóan az infrastruktúra kódját is tárolhatja egy forrás adattárban, és annak verzióját is. A csapatának bármelyike futtathatja a kódot, és telepíthet hasonló környezeteket is.

Ha az Azure-megoldások számára kíván infrastruktúra-kódot megvalósítani, használja Azure Resource Manager-sablonokat. A sablon egy JavaScript Object Notation (JSON) fájl, amely meghatározza a projekt infrastruktúráját és konfigurációját. A sablon deklaratív szintaxist használ, amely lehetővé teszi, hogy a telepítéshez szükséges programozási parancsok sorrendjének megírása nélkül adja meg az üzembe helyezni kívánt adatokat. A sablonban megadhatja a telepítendő erőforrásokat és az erőforrások tulajdonságait.

## <a name="why-choose-resource-manager-templates"></a>Miért érdemes Resource Manager-sablonokat választani?

Ha a Resource Manager-sablonok és az egyik másik infrastruktúra kód-szolgáltatásként való használatának eldöntését tervezi, vegye figyelembe a következő előnyöket a sablonok használatával:

* **Deklaratív szintaxis**: A Resource Manager-sablonok lehetővé teszik, hogy teljes körű Azure-infrastruktúrát hozzon létre és helyezzen üzembe. Telepítheti például nem csak a virtuális gépeket, hanem a hálózati infrastruktúrát, a tárolási rendszereket és minden más szükséges erőforrást is.

* **Ismételhető eredmények**: Az infrastruktúrát ismételten üzembe helyezheti a fejlesztési életciklus során, és biztos lehet abban, hogy az erőforrások egységes módon vannak üzembe helyezve. A sablonok idempotens, ami azt jelenti, hogy több alkalommal is telepítheti ugyanazt a sablont, és ugyanazokat az erőforrásokat ugyanaz az állapot teszi elérhetővé. Létrehozhat egy olyan sablont, amely a kívánt állapotot jelöli, nem pedig sok különálló sablon kidolgozását a frissítések megjelenítéséhez.

* Előkészítés: Nem kell aggódnia a megrendelési műveletek bonyolultságával kapcsolatban. A Resource Manager összehangolja az egymástól függő erőforrások telepítését, hogy azok a megfelelő sorrendben jöjjenek létre. Ha lehetséges, a Resource Manager párhuzamosan helyezi üzembe az erőforrásokat, így az üzemelő példányok gyorsabban futnak, mint a soros központi telepítések. A sablont egyetlen parancs használatával helyezheti üzembe, nem pedig több, felszólító parancs használatával.

   ![Template deployment összehasonlítása](./media/template-deployment-overview/template-processing.png)

* **Beépített ellenőrzés**: A sablon csak az érvényesítést követően lesz telepítve. A Resource Manager a telepítés megkezdése előtt ellenőrzi a sablont, hogy a telepítés sikeres legyen-e. Az üzembe helyezés kevésbé valószínű, hogy egy félig kész állapotban leáll.

* **Moduláris fájlok**: A sablonokat kioszthatja kisebb, újrafelhasználható összetevőkre, és összekapcsolhatja őket a telepítéskor. Egy sablont egy másik sablonba is beágyazhat.

* **Hozzon létre bármely Azure-erőforrást**: A sablonokban azonnal használhatja az új Azure-szolgáltatásokat és-szolgáltatásokat. Amint egy erőforrás-szolgáltató új erőforrásokat vezet be, ezeket az erőforrásokat sablonokon keresztül is telepítheti. Az új szolgáltatások használata előtt nem kell megvárnia, hogy az eszközök és modulok frissítése megtörténjen.

* **Követett központi telepítések**: A Azure Portal áttekintheti az üzembe helyezési előzményeket, és információkat kaphat a sablon telepítéséről. Megtekintheti a telepített sablont, az átadott paramétereket és a kimeneti értékeket. Más infrastruktúrát, mint a Code Services a portálon keresztül nem követik nyomon.

   ![Üzembe helyezési előzmények](./media/template-deployment-overview/deployment-history.png)

* **Szabályzat kódként**: [Azure Policy](../governance/policy/overview.md) egy szabályzat, amely az irányítás automatizálására szolgál. Ha Azure-szabályzatokat használ, a szabályzat szervizelése a nem megfelelő erőforrásokon történik a sablonokkal való üzembe helyezéskor.

* **Üzembe helyezési tervezetek**: Kihasználhatja a Microsoft által biztosított [tervrajzok](../governance/blueprints/overview.md) előnyeit a szabályozási és megfelelőségi követelmények teljesítése érdekében. Ezek a tervrajzok előre elkészített sablonokat is tartalmaznak a különböző architektúrák esetében.

* **CI/CD-integráció**: Integrálhatja a sablonokat a folyamatos integrációs és folyamatos üzembe helyezési (CI/CD) eszközökbe, amelyekkel automatizálhatja a kiadási folyamatokat a gyors és megbízható alkalmazás-és infrastruktúra-frissítésekhez. Az Azure DevOps és a Resource Manager-sablon feladatának használatával Azure-folyamatokat használhat Azure Resource Manager sablonok projektjeinek folyamatos létrehozásához és üzembe helyezéséhez. További információért lásd: [a vs projekt a folyamatokkal](./vs-resource-groups-project-devops-pipelines.md) és a [folyamatos integráció az Azure-folyamatokkal](./resource-manager-tutorial-use-azure-pipelines.md).

* **Exportálható kód**: Egy meglévő erőforráscsoport sablonjának lekéréséhez exportálhatja az erőforráscsoport aktuális állapotát, vagy megtekintheti az adott központi telepítéshez használt sablont. Az [exportált sablon](export-template-portal.md) megtekintése hasznos információkat nyújt a sablon szintaxisáról.

* **Szerzői eszközök**: A sablonok a [Visual Studio Code](resource-manager-tools-vs-code.md) és a Template Tool bővítménnyel hozhatók létre. Az IntelliSense, a szintaxis kiemelése, a beágyazott Súgó és számos más nyelvi funkció is elérhető. A Visual Studio Code mellett a [Visual studiót](./vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)is használhatja.

## <a name="template-file"></a>Sablonfájl

A sablonon belül olyan sablon- [kifejezéseket](template-expressions.md) írhat, amelyek kibővítik a JSON képességeit. Ezek a kifejezések a Resource Manager által biztosított [függvények](resource-group-template-functions.md) használatát teszik elérhetővé.

A sablon a következő résszel rendelkezik:

* [Paraméterek](template-parameters.md) – az üzembe helyezés során értékeket adhat meg, amelyek lehetővé teszik, hogy ugyanaz a sablon más környezetekben is használható legyen.

* [Változók](template-variables.md) – a sablonokban újra felhasználható értékek definiálása. Ezek a paraméterek értékeiből állíthatók össze.

* [Felhasználó által definiált függvények](template-user-defined-functions.md) – a sablont leegyszerűsítő testreszabott függvények létrehozása.

* [Erőforrások](resource-group-authoring-templates.md#resources) – a telepítendő erőforrások meghatározása.

* [Kimenetek](template-outputs.md) – az üzembe helyezett erőforrások értékeit adja vissza.

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
* A sablonok exportálásával kapcsolatos további tudnivalókért [lásd: gyors útmutató: Azure Resource Manager-sablonok létrehozása és üzembe helyezése a](./resource-manager-quickstart-create-templates-use-the-portal.md)Azure Portal használatával.
