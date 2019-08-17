---
title: CreateUiDefinition. JSON az Azure által felügyelt alkalmazás létrehozási élményéhez | Microsoft Docs
description: Útmutató a Azure Managed Applications felhasználói felületi definícióinak létrehozásához
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/06/2019
ms.author: tomfitz
ms.openlocfilehash: 1ee6d9332a2be5ccb22b7571b348e2e0aae78fb2
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69563561"
---
# <a name="createuidefinitionjson-for-azure-managed-applications-create-experience"></a>CreateUiDefinition. JSON az Azure által felügyelt alkalmazás létrehozási élményéhez

Ez a dokumentum bemutatja a **createUiDefinition. JSON** fájl alapvető fogalmait, amelyeket Azure Portal használ a felhasználói felület definiálásához a felügyelt alkalmazások létrehozásakor.

A sablon a következő

```json
{
   "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
   "handler": "Microsoft.Azure.CreateUIDef",
   "version": "0.1.2-preview",
   "parameters": {
      "basics": [ ],
      "steps": [ ],
      "outputs": { },
      "resourceTypes": [ ]
   }
}
```

A CreateUiDefinition mindig három tulajdonságot tartalmaz: 

* kezelő
* version
* parameters

A kezelőnek mindig a `Microsoft.Azure.CreateUIDef`-nek kell lennie, és `0.1.2-preview`a legújabb támogatott verzió a.

A Parameters tulajdonság sémája a megadott kezelő és verzió kombinációjából függ. A felügyelt alkalmazások esetében a támogatott tulajdonságok `basics`a `steps`következők: `outputs`, és. Az alapértékek és a lépések tulajdonságai a Azure Portal megjeleníteni kívánt [elemeket](create-uidefinition-elements.md) , például a szövegmezőket és a legördülő listákat tartalmazzák. A kimenet tulajdonság a megadott elemek kimeneti értékének leképezésére szolgál a Azure Resource Manager telepítési sablon paramétereinek.

Beleértve `$schema` a javasolt, de nem kötelező. Ha `version` meg van adva, a értékének meg kell egyeznie `$schema` az URI-n belüli verzióval.

A felhasználói felület definícióját JSON-szerkesztővel is létrehozhatja, majd megtekintheti azt a [felhasználói felület definíciójának Homokozójában](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade) . A homokozóval kapcsolatos további információkért lásd: [Azure Managed Applications-portál felületének tesztelése](test-createuidefinition.md).

## <a name="basics"></a>Alapvető beállítások

Az alapismeretek az első lépés, amikor a Azure Portal elemzi a fájlt. A-ben `basics`megadott elemek megjelenítésén kívül a portál befecskendezi az elemeket a felhasználók számára az előfizetés, az erőforráscsoport és a telepítés helyének kiválasztásához. Ha lehetséges, a központi telepítésre vonatkozó paramétereket lekérdező elemek, például a fürt vagy a rendszergazdai hitelesítő adatok neve, ebben a lépésben kell megjelenniük.

Ha egy elem viselkedése a felhasználó előfizetésének, erőforráscsoportának vagy helyének a függvénye, akkor ez az elem nem használható az alapbeállításokban. Például a **Microsoft. számítás. SizeSelector** függvény a felhasználó előfizetése és helye alapján határozza meg az elérhető méretek listáját. Ezért a **Microsoft. számítás. SizeSelector** csak lépésekben használható. Általánosságban elmondható, hogy csak a **Microsoft. Common** névtér elemei használhatók az alapbeállításokban. Bár a más névterekben található egyes elemek (például a **Microsoft. számítás. hitelesítő adatok**) továbbra sem függenek a felhasználó környezetének.

## <a name="steps"></a>Lépések

A Steps (lépések) tulajdonsága nulla vagy több további lépést is tartalmazhat az alapértékek után, amelyek mindegyike egy vagy több elemet tartalmaz. Vegye fontolóra az üzembe helyezett alkalmazás szerepköreinek vagy szintjeinek hozzáadását. Adjon meg például egy lépést a főcsomópont bemenetei számára, valamint egy lépést a fürt munkavégző csomópontjaihoz.

## <a name="outputs"></a>Kimenetek

A Azure Portal a `outputs` tulajdonság használatával képezi le `basics` az elemeket `steps` a és a Azure Resource Manager központi telepítési sablon paraméterei között. A szótár kulcsai a sablon paramétereinek nevei, és az értékek a hivatkozott elemek kimeneti objektumainak tulajdonságai.

A felügyelt alkalmazás-erőforrás nevének megadásához meg kell adnia egy `applicationResourceName` nevű értéket a kimenetek tulajdonságban. Ha nem állítja be ezt az értéket, az alkalmazás hozzárendel egy GUID azonosítót a névhez. A felhasználói felületen olyan szövegmezőt is hozzáadhat, amely a felhasználótól származó nevet kér.

```json
"outputs": {
    "vmName": "[steps('appSettings').vmName]",
    "trialOrProduction": "[steps('appSettings').trialOrProd]",
    "userName": "[steps('vmCredentials').adminUsername]",
    "pwd": "[steps('vmCredentials').vmPwd.password]",
    "applicationResourceName": "[steps('appSettings').vmName]"
}
```

## <a name="resource-types"></a>Erőforrástípusok

Ha csak azokra a helyszínekre szeretné szűrni a rendelkezésre álló helyet, amelyek támogatják a telepítendő erőforrásokat, adja meg az erőforrástípusok tömbjét. Ha egynél több erőforrástípust ad meg, csak az összes erőforrástípust támogató helyet adja vissza. Ez a tulajdonság nem kötelező.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Azure.CreateUIDef",
    "version": "0.1.2-preview",
    "parameters": {
      "resourceTypes": ["Microsoft.Compute/disks"],
      "basics": [
        ...
```  

## <a name="functions"></a>Funkciók

A CreateUiDefinition [funkciói](create-uidefinition-functions.md) az elemek bemeneteit és kimeneteit, valamint a feltételes funkciókat is lehetővé teszik. Ezek a függvények a szintaxisban és a funkcionalitásban is hasonlóak Azure Resource Manager a Template functions szolgáltatásban.

## <a name="next-steps"></a>További lépések

A createUiDefinition. JSON fájlnak is van egy egyszerű sémája. A valódi mélysége az összes támogatott elemtől és függvénytől származik. Ezeket az elemeket részletesebben a következő helyen ismertetjük:

- [Elemek](create-uidefinition-elements.md)
- [Functions](create-uidefinition-functions.md)

A createUiDefinition aktuális JSON-sémája itt érhető el https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json:.

Példa felhasználói felületre: [createUiDefinition. JSON](https://github.com/Azure/azure-managedapp-samples/blob/master/samples/201-managed-app-using-existing-vnet/createUiDefinition.json).
