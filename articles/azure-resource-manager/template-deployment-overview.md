---
title: Azure Resource Manager-sablonok
description: Ismerteti, hogyan használhatók Azure Resource Manager sablonok az erőforrások telepítéséhez.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: tomfitz
ms.openlocfilehash: 95c127b3a7c9c47c96b292066bf1597a02896806
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70166523"
---
# <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sablonok

A Azure Resource Manager segítségével létrehozhat olyan sablonokat, amelyek meghatározzák, hogy mit szeretne telepíteni az Azure-ba. A sablon egy JavaScript Object Notation (JSON) fájl, amely az Azure-megoldás infrastruktúráját és konfigurációját tartalmazza. A sablonok segítségével a megoldás a teljes életciklusa során ismételten üzembe helyezhető, és az erőforrások üzembe helyezése biztosan konzisztens lesz.

A sablon deklaratív szintaxist használ, amely lehetővé teszi, hogy a telepítéshez szükséges programozási parancsok sorrendjének megírása nélkül adja meg az üzembe helyezni kívánt adatokat. A sablonban megadhatja a telepítendő erőforrásokat és az erőforrások tulajdonságait.

## <a name="benefits-of-resource-manager-templates"></a>A Resource Manager-sablonok előnyei

A Resource Manager-sablonok számos előnnyel rendelkeznek:

* A megoldás összes erőforrását egy csoportként telepítheti, felügyelheti és figyelheti meg az erőforrások különálló kezelése helyett.

* A megoldást ismételten telepítheti a fejlesztési életciklus során, és biztos lehet abban, hogy az erőforrások telepítése konzisztens lesz.

* Az infrastruktúrát szkriptek helyett deklaratív sablonok segítségével is kezelheti.

* Meghatározhatja az erőforrások közötti függőségeket, hogy azok a megfelelő sorrendben legyenek telepítve.

Az alábbi javaslatokat követve teljes mértékben kihasználhatja a Resource Manager előnyeit a megoldásaival végzett munka során.

* Az infrastruktúrát a Resource Manager-sablonok deklaratív szintaxisán keresztül határozhatja meg és telepítheti imperatív parancsok helyett.

* Meghatározhatja az összes telepítési és konfigurációs lépést a sablonban. Nem szükséges manuális lépéseket megadnia a megoldás beállításához.

* Imperatív parancsok futtatásával kezelheti az erőforrásokat, például elindíthat vagy leállíthat egy alkalmazást vagy gépet.

* Kövesse [Azure Resource Manager sablon ajánlott eljárásait](template-best-practices.md).

## <a name="template-deployment-process"></a>Template deployment folyamat

Egy sablon központi telepítésekor a Resource Manager elemzi a sablont, és átalakítja a szintaxisát REST API műveletekre. Ezeket a műveleteket a megfelelő erőforrás-szolgáltatóknak küldi el. Például amikor a Resource Manager megkap egy sablont, amely a következő erőforrás-definíciót tartalmazza:

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

## <a name="template-structure"></a>Sablonszerkezet

A sablonok és erőforráscsoportok meghatározási módja teljes mértékben Öntől függ, illetve attól, hogyan szeretné kezelni a megoldást. Például egyetlen sablon segítségével üzembe helyezheti a háromszintű alkalmazását egyetlen erőforráscsoportra.

![háromrétegű sablon](./media/resource-group-overview/3-tier-template.png)

Azonban nem kell meghatároznia a teljes infrastruktúrát egyetlen sablonban. Gyakran érdemes felosztani a telepítési követelményeket konkrét, célspecifikus sablonokra. Ezeket a sablonokat egyszerűen újból felhasználhatja különböző megoldásokhoz. Egy adott megoldás telepítéséhez hozzon létre egy fősablont, amely összekapcsolja az összes szükséges sablont. Az alábbi kép bemutatja, hogyan telepíthető egy háromszintű megoldás egy olyan fölérendelt sablon segítségével, amely három beágyazott sablont tartalmaz.

![beágyazott rétegsablon](./media/resource-group-overview/nested-tiers-template.png)

Ha elképzeli, hogy a rétegek külön életciklusokkal rendelkeznek, akkor a három szintet különálló erőforráscsoportokra telepítheti. Ügyeljen rá, hogy az erőforrások továbbra is kapcsolódhatnak egyéb erőforráscsoportok erőforrásaihoz.

![rétegsablon](./media/resource-group-overview/tier-templates.png)

A beágyazott sablonokkal kapcsolatos további információkért lásd: [Kapcsolt sablonok használata az Azure Resource Manager eszközben](resource-group-linked-templates.md).

Az Azure Resource Manager a függőségek elemzésével biztosítja, hogy az erőforrások a megfelelő sorrendben jöjjenek létre. Ha egy erőforrás egy másik erőforráshoz tartozó értéket használ fel (például egy virtuális gép, amely egy tárfiókot igényel a lemezekhez), akkor beállíthat egy függőséget. További információ: [Függőségek meghatározása az Azure Resource Manager sablonokban](resource-group-define-dependencies.md).

A sablonokat az infrastruktúra frissítésére is használhatja. Hozzáadhat például egy erőforrást a megoldáshoz, valamint hozzáadhat konfigurációs szabályokat a már telepített erőforrásokhoz. Ha a sablon olyan erőforrást határoz meg, amely már létezik, a Resource Manager frissíti a meglévő erőforrást egy új létrehozása helyett.

A Resource Manager bővítményeket biztosít olyan forgatókönyvek esetére, amikor további műveletek szükségesek, például egy adott szoftver telepítése, amelyet nem tartalmaz a beállítás. Ha már használ valamilyen konfigurációfelügyeleti szolgáltatást, mint a DSC, Chef vagy Puppet, bővítmények segítségével folytathatja a munkát az adott szolgáltatással. További információ a virtuális gépi bővítményekről: [A virtuális gépi bővítmények és funkcióik áttekintése](../virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Amikor létrehoz egy megoldást a portálról, az automatikusan tartalmaz egy telepítési sablont. Nem szükséges teljesen új sablont létrehoznia, mivel kezdetben használhatja a sablont a megoldásához, majd testre szabhatja az adott igényeknek megfelelően. Példa: gyors útmutató [: Azure Resource Manager-sablonok létrehozása és üzembe helyezése a](./resource-manager-quickstart-create-templates-use-the-portal.md)Azure Portal használatával. Egy meglévő erőforráscsoport sablonjának lekéréséhez is exportálhatja az erőforráscsoport aktuális állapotát, vagy megtekintheti az adott telepítéshez felhasznált sablont. Az [exportált sablon](./manage-resource-groups-portal.md#export-resource-groups-to-templates) megtekintése hasznos információkat nyújt a sablon szintaxisáról.

Végül a sablon az alkalmazás forráskódjának részévé válik. Elhelyezheti a forráskódraktárban, és frissítheti az alkalmazás továbbfejlesztésekor. A sablont a Visual Studio eszközben szerkesztheti.

## <a name="next-steps"></a>További lépések

További információ a sablonfájlokről: [Azure Resource Manager sablonok struktúrájának és szintaxisának megismerése](resource-group-authoring-templates.md).

A sablon meghatározása után készen áll, hogy üzembe helyezze az erőforrásokat az Azure-ban. Az erőforrások üzembe helyezéséhez lásd:

* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](resource-group-template-deploy.md)
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure parancssori felületével](resource-group-template-deploy-cli.md)
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure Portallal](resource-group-template-deploy-portal.md)
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure Manager REST API-val](resource-group-template-deploy-rest.md)

