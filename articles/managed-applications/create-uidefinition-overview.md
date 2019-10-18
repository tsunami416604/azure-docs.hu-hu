---
title: CreateUiDefinition. JSON – Azure által felügyelt alkalmazás
description: Útmutatás a Azure Portal felhasználói felületi definícióinak létrehozásához. Azure Managed Applications definiálásakor használatos.
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
ms.openlocfilehash: 9009d6a54c27617dd0d1d53369405555becde59d
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72529327"
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
* paraméterek

A kezelőnek mindig `Microsoft.Azure.CreateUIDef` értékűnek kell lennie, a legújabb támogatott verzió pedig `0.1.2-preview`.

A Parameters tulajdonság sémája a megadott kezelő és verzió kombinációjából függ. A felügyelt alkalmazások esetében a támogatott tulajdonságok a következők: `basics`, `steps` és `outputs`. Az alapértékek és a lépések tulajdonságai a Azure Portal megjeleníteni kívánt [elemeket](create-uidefinition-elements.md) , például a szövegmezőket és a legördülő listákat tartalmazzák. A kimenet tulajdonság a megadott elemek kimeneti értékének leképezésére szolgál a Azure Resource Manager telepítési sablon paramétereinek.

Beleértve a `$schema` használata ajánlott, de nem kötelező. Ha meg van adva, a `version` értékének meg kell egyeznie a `$schema` URI-n belüli verzióval.

A createUiDefinition létrehozásához JSON-szerkesztőt használhat, majd a [createUiDefinition-homokozóban](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade) tesztelheti azt. A homokozóval kapcsolatos további információkért lásd: [Azure Managed Applications-portál felületének tesztelése](test-createuidefinition.md).

## <a name="basics"></a>Alapvető beállítások

Az alapismeretek az első lépés, amikor a Azure Portal elemzi a fájlt. A `basics` paraméterben megadott elemek megjelenítése mellett a portál a felhasználók elemeit adja meg a telepítéshez szükséges előfizetés, erőforráscsoport és hely kiválasztásához. Ha lehetséges, a központi telepítésre vonatkozó paramétereket lekérdező elemek, például a fürt vagy a rendszergazdai hitelesítő adatok neve, ebben a lépésben kell megjelenniük.

## <a name="steps"></a>Lépések

A Steps (lépések) tulajdonsága nulla vagy több további lépést is tartalmazhat az alapértékek után, amelyek mindegyike egy vagy több elemet tartalmaz. Vegye fontolóra az üzembe helyezett alkalmazás szerepköreinek vagy szintjeinek hozzáadását. Adjon meg például egy lépést a főcsomópont bemenetei számára, valamint egy lépést a fürt munkavégző csomópontjaihoz.

## <a name="outputs"></a>Kimenetek

A Azure Portal a `outputs` tulajdonsággal rendeli hozzá az elemeket `basics` és `steps` értékről a Azure Resource Manager telepítési sablon paraméterei között. A szótár kulcsai a sablon paramétereinek nevei, és az értékek a hivatkozott elemek kimeneti objektumainak tulajdonságai.

A felügyelt alkalmazás-erőforrás nevének megadásához a kimenetek tulajdonságban szerepelnie kell egy `applicationResourceName` nevű értéknek. Ha nem állítja be ezt az értéket, az alkalmazás hozzárendel egy GUID azonosítót a névhez. A felhasználói felületen olyan szövegmezőt is hozzáadhat, amely a felhasználótól származó nevet kér.

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

## <a name="functions"></a>Functions

A CreateUiDefinition [funkciói](create-uidefinition-functions.md) az elemek bemeneteit és kimeneteit, valamint a feltételes funkciókat is lehetővé teszik. Ezek a függvények a szintaxisban és a funkcionalitásban is hasonlóak Azure Resource Manager a Template functions szolgáltatásban.

## <a name="next-steps"></a>Következő lépések

A createUiDefinition. JSON fájlnak is van egy egyszerű sémája. A valódi mélysége az összes támogatott elemtől és függvénytől származik. Ezeket az elemeket részletesebben a következő helyen ismertetjük:

- [Elemek](create-uidefinition-elements.md)
- [Functions](create-uidefinition-functions.md)

A createUiDefinition aktuális JSON-sémája itt érhető el: https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json.

Példa felhasználói felületre: [createUiDefinition. JSON](https://github.com/Azure/azure-managedapp-samples/blob/master/Managed%20Application%20Sample%20Packages/201-managed-app-using-existing-vnet/createUiDefinition.json).
