---
title: CreateUiDefinition.json fájl a portálablakhoz
description: Ez a témakör azt ismerteti, hogy miként hozhat létre felhasználói felület-definíciókat az Azure Portalhoz. Az Azure felügyelt alkalmazások definiálásakor használatos.
author: tfitzmac
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: tomfitz
ms.openlocfilehash: 2956c76f5bec353639b39228b982db21b6932deb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294892"
---
# <a name="createuidefinitionjson-for-azure-managed-applications-create-experience"></a>CreateUiDefinition.json az Azure-beli felügyelt példány létrehozási felületéhez

Ez a dokumentum bemutatja a **createUiDefinition.json** fájl alapvető fogalmait, amelyet az Azure Portal a felhasználói felület definiálására használ felügyelt alkalmazás létrehozásakor.

A sablon a következő:

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

* Kezelő
* version
* paraméterek

A kezelőnek `Microsoft.Azure.CreateUIDef`mindig legyen , és `0.1.2-preview`a legújabb támogatott verzió legyen.

A paraméterek tulajdonság sémája a megadott kezelő és verzió kombinációjától függ. Felügyelt alkalmazások esetén a `basics`támogatott `steps`tulajdonságok `outputs`a , és a . Az alapok és a lépések tulajdonságai tartalmazzák az Azure Portalon megjelenítendő [elemeket](create-uidefinition-elements.md) – például a szövegdobozokat és a legördülő listákat. A outputok tulajdonság a megadott elemek kimeneti értékeinek az Azure Resource Manager központi telepítési sablon paramétereinek leképezésére szolgál.

Beleértve `$schema` ajánlott, de nem kötelező. Ha meg van adva, az `version` értéknek `$schema` meg kell egyeznie az URI-n belüli verzióval.

A JSON-szerkesztősegítségével létrehozhatja a createUiDefinition definícióját, majd tesztelheti azt a [createUiDefinition sandboxban](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade) az előnézet megtekintéséhez. A sandboxról további információt a [Portálfelület tesztelése az Azure felügyelt alkalmazásokhoz című témakörben talál.](test-createuidefinition.md)

## <a name="basics"></a>Alapvető beállítások

Az alapok az első lépés, amely akkor jön létre, amikor az Azure Portal elemzi a fájlt. A portál a megadott `basics`elemek megjelenítése mellett a felhasználók nak is beadja az okat, hogy kiválasszák az előfizetést, az erőforráscsoportot és a központi telepítés helyét. Ha lehetséges, a központi telepítési szintű paramétereket lekérdező elemeknek, például egy fürt vagy a rendszergazdai hitelesítő adatok nevének lekérdezése ebben a lépésben kell, hogy legyen.

## <a name="steps"></a>Lépések

A steps tulajdonság nulla vagy több további lépést tartalmazhat az alapok után, amelyek mindegyike egy vagy több elemet tartalmaz. Fontolja meg az üzembe helyezett alkalmazás szerepkörenkénti vagy rétegenkénti lépései hozzáadását. Például adjon hozzá egy lépést a fő csomópont bemenetek, és egy lépést a dolgozó csomópontok egy fürtben.

## <a name="outputs"></a>Kimenetek

Az Azure Portal `outputs` a tulajdonság `basics` segítségével `steps` leképezi az Azure Resource Manager telepítési sablon elemeit és paramétereit. A szótár kulcsai a sablonparaméterek nevei, az értékek pedig a hivatkozott elemek kimeneti objektumainak tulajdonságai.

A felügyelt alkalmazás erőforrás nevének beállításához `applicationResourceName` meg kell adnia egy nevű értéket a outputok tulajdonságban. Ha nem állítja be ezt az értéket, az alkalmazás guid azonosítót rendel a névhez. A felhasználói felületen felvehet egy szövegdobozt, amely nevet kér a felhasználótól.

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

Ha a rendelkezésre álló helyeket csak azokra a helyekre szeretné szűrni, amelyek támogatják az üzembe helyezhetővé kívánt erőforrástípusokat, adjon meg egy tömböt az erőforrástípusokból. Ha egynél több erőforrástípust ad meg, csak az összes erőforrástípust támogató helyek kerülnek visszaadásra. Ez a tulajdonság nem kötelező.

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

A CreateUiDefinition [függvényeket](create-uidefinition-functions.md) biztosít az elemek bemeneteiés kimenetei, valamint a feltételes funkciók használata érdekében. Ezek a függvények mind a szintaxisban, mind a funkciókban hasonlóak az Azure Resource Manager sablonfüggvényeihez.

## <a name="next-steps"></a>További lépések

Maga a createUiDefinition.json fájl egyszerű sémával rendelkezik. Az igazi mélysége származik az összes támogatott elemek és funkciók. Ezeket az elemeket részletesebben a következő helyeken ismertetjük:

- [Elemek](create-uidefinition-elements.md)
- [Functions](create-uidefinition-functions.md)

A createUiDefinition aktuális JSON-séma `https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json`itt érhető el: .

A felhasználói felület fájlját például a [createUiDefinition.json fájlban.](https://github.com/Azure/azure-managedapp-samples/blob/master/Managed%20Application%20Sample%20Packages/201-managed-app-using-existing-vnet/createUiDefinition.json)
