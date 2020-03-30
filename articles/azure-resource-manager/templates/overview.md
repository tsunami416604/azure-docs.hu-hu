---
title: Sablonok – Áttekintés
description: Az Azure Resource Manager-sablonok használatával az erőforrások üzembe helyezéséhez nyújtott előnyök ismertetése.
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: 4570f5471ef6baf6f3f4a920be4d93c3f5a90438
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258124"
---
# <a name="what-are-arm-templates"></a>Mik azok az ARM-sablonok?

A felhőbe való áthelyezéssel számos csapat agilis fejlesztési módszereket fogadott el. Ezek a csapatok gyorsan haladnak. Ismételten telepíteniük kell a megoldásaikat a felhőbe, és tudniuk kell, hogy az infrastruktúrájuk megbízható állapotban van. Mivel az infrastruktúra az iteratív folyamat részévé vált, a műveletek és a fejlesztés közötti megosztás megszűnt. A csapatoknak egységes folyamaton keresztül kell kezelnie az infrastruktúrát és az alkalmazáskódot.

Ezeknek a kihívásoknak való megfeleltetéshez automatizálhatja a központi telepítéseket, és az infrastruktúra kódként való használatát használhatja. A kódban megadhatja a telepítendő infrastruktúrát. Az infrastruktúra-kód a projekt részévé válik. Az alkalmazáskódhoz hasonlóan az infrastruktúrakódot is egy forrástárházban tárolja, és verziószámmal verzióal kell elkönyvelni. A csapat bármely tagja futtathatja a kódot, és hasonló környezeteket helyezhet üzembe.

Az Azure-megoldások infrastruktúra-kódként való megvalósításához használja az Azure Resource Manager (ARM) sablonokat. A sablon egy JavaScript-objektumjelölési (JSON) fájl, amely meghatározza a projekt infrastruktúráját és konfigurációját. A sablon deklaratív szintaxist használ, amely lehetővé teszi a telepítéshez használt állapot ot anélkül, hogy meg kellene írnia a létrehozáshoz szükséges programozási parancsok sorrendjét. A sablonban megadhatja az üzembe helyezandó erőforrásokat és az erőforrások tulajdonságait.

## <a name="why-choose-arm-templates"></a>Miért válassza az ARM sablonokat?

Ha az ARM-sablonok és a másik infrastruktúra kódszolgáltatásként való használata között próbál dönteni, vegye figyelembe a sablonok használatának következő előnyeit:

* **Deklaratív szintaxis:** ARM sablonok lehetővé teszik, hogy hozzon létre és telepítsen egy teljes Azure-infrastruktúra deklaratív módon. Például nem csak a virtuális gépeket telepítheti, hanem a hálózati infrastruktúrát, a tárolórendszereket és minden más erőforrást is, amire szüksége lehet.

* **Ismételhető eredmények:** Az infrastruktúra ismételt üzembe helyezése a fejlesztési életciklus során, és bízom abban, hogy az erőforrások at konzisztens módon telepítik. A sablonok idempotensek, ami azt jelenti, hogy ugyanazt a sablont többször is üzembe helyezheti, és ugyanazokat az erőforrástípusokat kaphatja ugyanabban az állapotban. Kifejleszthet egy sablont, amely a kívánt állapotot képviseli, ahelyett, hogy sok különálló sablont fejlesztene a frissítések reprezentatiójára.

* **Vezénylés:** Nem kell aggódnia a rendelési műveletek bonyolultsága miatt. Az Erőforrás-kezelő vezényli az egymástól függő erőforrások üzembe helyezését, így azok a megfelelő sorrendben jönnek létre. Ha lehetséges, az Erőforrás-kezelő párhuzamosan telepíti az erőforrásokat, így a központi telepítések gyorsabban befejeződnek, mint a soros telepítések. A sablont nem több imperatív parancs, hanem egy parancs segítségével telepítheti.

   ![A sablonok központi telepítésének összehasonlítása](./media/overview/template-processing.png)

* **Beépített érvényesítés:** A sablon csak az ellenőrzés átadása után kerül üzembe helyezésre. Az Erőforrás-kezelő ellenőrzi a sablont a központi telepítés megkezdése előtt, hogy a központi telepítés sikeres lesz-e. A központi telepítés kevésbé valószínű, hogy félig kész állapotban leáll.

* **Moduláris fájlok:** A sablonokat kisebb, újrafelhasználható összetevőkre bonthatja, és a telepítés kor összekapcsolhatja őket. Egy sablont egy másik sablonba is beágyazhat.

* **Hozzon létre bármilyen Azure-erőforrást:** Azonnal használhatja az új Azure-szolgáltatásokat és -funkciókat a sablonokban. Amint egy erőforrás-szolgáltató új erőforrásokat vezet be, ezeket az erőforrásokat sablonokon keresztül telepítheti. Az új szolgáltatások használata előtt nem kell megvárnia az eszközök vagy modulok frissítésének megvárását.

* **Nyomon követett telepítések:** Az Azure Portalon megtekintheti a központi telepítési előzményeket, és információkat kaphat a sablon üzembe helyezéséről. Láthatja az üzembe helyezett sablont, a paraméterértékeket és a kimeneti értékeket. Más infrastruktúra kódszolgáltatásként nem követi nyomon a portálon keresztül.

   ![Telepítési előzmények](./media/overview/deployment-history.png)

* **Szabályzat kódként:** [Az Azure Policy](../../governance/policy/overview.md) egy szabályzat, amely kódkeretrendszerként a cégirányítási automatizálásautomatizálási. Ha Azure-szabályzatokat használ, a szabályzatok szervizelése nem megfelelő erőforrásokon történik, amikor sablonokon keresztül telepítik őket.

* **Üzembe helyezési tervrajzok:** A Microsoft által biztosított [tervrajzok](../../governance/blueprints/overview.md) előnyeit kihasználhatja a szabályozási és megfelelőségi szabványok nak való megfelelés érdekében. Ezek a tervrajzok előre elkészített sablonokat tartalmaznak a különböző architektúrákhoz.

* **CI/CD integráció:** Sablonokat integrálhat a folyamatos integrációs és folyamatos üzembe helyezési (CI/CD) eszközökbe, amelyek automatizálhatják a kiadási folyamatokat a gyors és megbízható alkalmazás- és infrastruktúra-frissítések érdekében. Az Azure DevOps és a Resource Manager sablonfeladat használatával az Azure Pipelines segítségével folyamatosan hozhat létre és helyezhet üzembe ARM-sablonprojekteket. További információ: [VS-projekt folyamatokés](add-template-to-azure-pipelines.md) [folyamatos integráció az Azure Pipelines.](template-tutorial-use-azure-pipelines.md)

* **Exportálható kód**: Egy meglévő erőforráscsoport sablonját az erőforráscsoport aktuális állapotának exportálásával vagy egy adott központi telepítéshez használt sablon megtekintésével szerezheti be. Az [exportált sablon](export-template-portal.md) megtekintése hasznos módszer a sablon szintaxisának megismerésére.

* **Szerzői eszközök**: A Visual [Studio-kóddal](use-vs-code-to-create-template.md) és a sabloneszköz-kiterjesztéssel sablonokat hozhat meg. Kapsz intellisense, szintaxis kiemelése, in-line segítséget, és sok más nyelvi funkciókat. A Visual Studio-kód mellett használhatja a [Visual Studio](create-visual-studio-deployment-project.md)alkalmazást is.

## <a name="template-file"></a>Sablonfájl

A sablonon belül [olyan sablonkifejezéseket](template-expressions.md) írhat, amelyek kiterjesztik a JSON képességeit. Ezek a kifejezések az Erőforrás-kezelő által biztosított [függvényeket](template-functions.md) használják.

A sablon a következő szakaszokat ismerteti:

* [Paraméterek](template-parameters.md) – Adja meg azokat az értékeket a központi telepítés során, amelyek lehetővé teszik, hogy ugyanazt a sablont különböző környezetekben használják.

* [Változók](template-variables.md) – A sablonokban újrafelhasznált értékek meghatározása. Ezek paraméterértékekből is létrehozhatók.

* [Felhasználó által definiált függvények](template-user-defined-functions.md) – A sablont leegyszerűsítő testreszabott függvények létrehozása.

* [Erőforrások](template-syntax.md#resources) – Adja meg az üzembe helyezendő erőforrásokat.

* [Kimenetek](template-outputs.md) – Az üzembe helyezett erőforrások ból származó értékek visszaadása.

## <a name="template-deployment-process"></a>Sablon telepítési folyamata

Sablon üzembe helyezésekor az Erőforrás-kezelő a sablont REST API-műveletekké alakítja át. Például amikor a Resource Manager megkap egy sablont, amely a következő erőforrás-definíciót tartalmazza:

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2016-01-01",
    "name": "mystorageaccount",
    "location": "westus",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
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
  "sku": {
    "name": "Standard_LRS"
  },
  "kind": "Storage",
  "properties": {}
}
```

## <a name="template-design"></a>Sablon terv

A sablonok és erőforráscsoportok meghatározási módja teljes mértékben Öntől függ, illetve attól, hogyan szeretné kezelni a megoldást. Például egyetlen sablon segítségével üzembe helyezheti a háromszintű alkalmazását egyetlen erőforráscsoportra.

![háromrétegű sablon](./media/overview/3-tier-template.png)

Azonban nem kell meghatároznia a teljes infrastruktúrát egyetlen sablonban. Gyakran érdemes felosztani a telepítési követelményeket konkrét, célspecifikus sablonokra. Ezeket a sablonokat egyszerűen újból felhasználhatja különböző megoldásokhoz. Egy adott megoldás telepítéséhez hozzon létre egy fősablont, amely összekapcsolja az összes szükséges sablont. Az alábbi kép bemutatja, hogyan telepíthető egy háromszintű megoldás egy olyan fölérendelt sablon segítségével, amely három beágyazott sablont tartalmaz.

![beágyazott rétegsablon](./media/overview/nested-tiers-template.png)

Ha elképzeli, hogy a rétegek külön életciklusokkal rendelkeznek, akkor a három szintet különálló erőforráscsoportokra telepítheti. Ügyeljen rá, hogy az erőforrások továbbra is kapcsolódhatnak egyéb erőforráscsoportok erőforrásaihoz.

![rétegsablon](./media/overview/tier-templates.png)

A beágyazott sablonokkal kapcsolatos további információkért lásd: [Kapcsolt sablonok használata az Azure Resource Manager eszközben](linked-templates.md).

## <a name="next-steps"></a>További lépések

* A sablon létrehozásának folyamatát végigvezető részletes oktatóanyagról az [Oktatóanyag: Az első ARM-sablon létrehozása és üzembe helyezése](template-tutorial-create-first-template.md)című témakörben olvashat.
* A sablonfájlok tulajdonságairól az [ARM-sablonok szerkezetének és szintaxisának ismertetése című témakörben olvashat.](template-syntax.md)
* A sablonok exportálásáról a [Rövid útmutató: ARM-sablonok létrehozása és üzembe helyezése az Azure Portal használatával](quickstart-create-templates-use-the-portal.md)című témakörben olvashat.
