---
title: CreateUiDefinition.jsa fájl a portálon panel
description: Útmutatás a Azure Portal felhasználói felületi definícióinak létrehozásához. Azure Managed Applications definiálásakor használatos.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: tomfitz
ms.openlocfilehash: 327fa1d7eb73d8e65bb4f81c1dff0fe2bec2913b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89319567"
---
# <a name="createuidefinitionjson-for-azure-managed-applications-create-experience"></a>CreateUiDefinition.json az Azure-beli felügyelt példány létrehozási felületéhez

Ez a dokumentum ismerteti a fájl **createUiDefinition.jsének** alapvető fogalmait. A Azure Portal ezt a fájlt használja a felhasználói felület definiálásához a felügyelt alkalmazások létrehozásakor.

A sablon a következő

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Azure.CreateUIDef",
    "version": "0.1.2-preview",
    "parameters": {
        "config": {
            "isWizard": false,
            "basics": { }
        },
        "basics": [ ],
        "steps": [ ],
        "outputs": { },
        "resourceTypes": [ ]
    }
}
```

A `CreateUiDefinition` mindig három tulajdonságot tartalmaz:

* kezelő
* version
* parameters

A kezelőnek mindig a-nek kell lennie `Microsoft.Azure.CreateUIDef` , és a legújabb támogatott verzió a `0.1.2-preview` .

A Parameters tulajdonság sémája a megadott kezelő és verzió kombinációjából függ. A felügyelt alkalmazások esetében a támogatott tulajdonságok a következők:,, `config` `basics` `steps` és `outputs` . Csak akkor használja, `config` Ha felül kell bírálnia a lépés alapértelmezett viselkedését `basics` . Az alapértékek és a lépések tulajdonságai a Azure Portal megjeleníteni kívánt [elemeket](create-uidefinition-elements.md) , például a szövegmezőket és a legördülő listákat tartalmazzák. A kimenet tulajdonság a megadott elemek kimeneti értékének leképezésére szolgál a Azure Resource Manager sablon paramétereinek.

Beleértve `$schema` a javasolt, de nem kötelező. Ha meg van adva, a értékének `version` meg kell egyeznie az URI-n belüli verzióval `$schema` .

A createUiDefinition létrehozásához JSON-szerkesztőt használhat, majd a [createUiDefinition-homokozóban](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade) tesztelheti azt. A homokozóval kapcsolatos további információkért lásd: [Azure Managed Applications-portál felületének tesztelése](test-createuidefinition.md).

## <a name="config"></a>Konfigurálás

A `config` tulajdonság megadása nem kötelező. Használja az alapszintű lépés alapértelmezett viselkedésének felülbírálását, vagy állítsa be a felületet lépésről lépésre varázslóként. Ha a `config` használatban van, akkor ez az első tulajdonság **createUiDefinition.json** a fájl `parameters` szakaszánakcreateUiDefinition.js. A következő példában az elérhető tulajdonságok láthatók.

```json
"config": {
    "isWizard": false,
    "basics": {
        "description": "Customized description with **markdown**, see [more](https://www.microsoft.com).",
        "subscription": {
            "constraints": {
                "validations": [
                    {
                        "isValid": "[expression for checking]",
                        "message": "Please select a valid subscription."
                    },
                    {
                        "permission": "<Resource Provider>/<Action>",
                        "message": "Must have correct permission to complete this step."
                    }
                ]
            },
            "resourceProviders": [
                "<Resource Provider>"
            ]
        },
        "resourceGroup": {
            "constraints": {
                "validations": [
                    {
                        "isValid": "[expression for checking]",
                        "message": "Please select a valid resource group."
                    }
                ]
            },
            "allowExisting": true
        },
        "location": {
            "label": "Custom label for location",
            "toolTip": "provide a useful tooltip",
            "resourceTypes": [
                "Microsoft.Compute/virtualMachines"
            ],
            "allowedValues": [
                "eastus",
                "westus2"
            ],
            "visible": true
        }
    }
},
```

### <a name="wizard"></a>Varázsló

A `isWizard` tulajdonság lehetővé teszi az egyes lépések sikeres érvényesítését, mielőtt továbblép a következő lépésre. Ha a `isWizard` tulajdonság nincs megadva, az alapértelmezett érték a **false**, és a lépésenkénti ellenőrzés nem szükséges.

Ha `isWizard` engedélyezve van, állítsa az **igaz**értékre, az **alapok** lap elérhető, és az összes többi lap le van tiltva. Ha a **tovább** gomb be van jelölve, a TAB ikon azt jelzi, hogy a TAB érvényesítése sikeres vagy sikertelen volt-e. A lap kötelező mezőinek befejezése és ellenőrzése után a **következő** gomb lehetővé teszi a navigálást a következő lapra. Ha az összes lap átadja az érvényesítést, lépjen a **felülvizsgálat és létrehozás** lapra, és válassza a **Létrehozás** gombot az üzembe helyezés megkezdéséhez.

:::image type="content" source="./media/create-uidefinition-overview/tab-wizard.png" alt-text="Lap varázsló":::

### <a name="override-basics"></a>Alapvető beállítások felülbírálása

Az alapvető beállítások lehetőséggel testreszabhatja az alapvető lépések lépéseit.

A (z) esetében `description` adjon meg egy Markdown-kompatibilis karakterláncot, amely leírja az erőforrást. A többsoros formátum és hivatkozások támogatottak.

A `subscription` és `resourceGroup` elemek lehetővé teszik további érvényesítések megadását. Az érvényesítések megadásának szintaxisa megegyezik a [szövegmező](microsoft-common-textbox.md)egyéni ellenőrzésével. `permission`Az előfizetés vagy az erőforráscsoport érvényességét is megadhatja.  

Az előfizetés vezérlőelem elfogadja az erőforrás-szolgáltatói névterek listáját. Megadhatja például a **Microsoft. számítást**. Hibaüzenet jelenik meg, amikor a felhasználó olyan előfizetést választ ki, amely nem támogatja az erőforrás-szolgáltatót. A hiba akkor fordul elő, ha az erőforrás-szolgáltató nincs regisztrálva az adott előfizetésen, és a felhasználónak nincs engedélye az erőforrás-szolgáltató regisztrálására.  

Az erőforráscsoport-vezérlőhöz lehetőség van `allowExisting` . Amikor `true` a felhasználók olyan erőforráscsoportokat választhatnak, amelyek már rendelkeznek erőforrásokkal. Ez a jelző a legtöbb esetben a megoldási sablonokra vonatkozik, ahol az alapértelmezett viselkedési feladatnak a felhasználóknak új vagy üres erőforráscsoportot kell kiválasztaniuk. A legtöbb esetben ezt a tulajdonságot nem kell megadnia.  

A esetében válassza ki a `location` felülbírálni kívánt hely vezérlőelem tulajdonságait. A nem felülbírált tulajdonságok az alapértelmezett értékre vannak beállítva. `resourceTypes` a teljesen minősített erőforrástípusok nevét tartalmazó karakterláncok tömbjét fogadja el. A hely beállításai csak olyan régiókra korlátozódnak, amelyek támogatják az erőforrás-típusokat.  `allowedValues`   régióbeli karakterláncok tömbjét fogadja el. Csak ezek a régiók jelennek meg a legördülő listában.`allowedValues`   A és a is beállítható  `resourceTypes` . Ennek eredménye mindkét listának metszéspontja. Végül a `visible` tulajdonság felhasználható a hely legördülő menüjének feltételes vagy teljes letiltására is.  

## <a name="basics"></a>Alapvető beállítások

Az **alapismeretek** lépés az első lépés, amikor a Azure Portal elemzi a fájlt. Alapértelmezés szerint az alapvető lépések lépés lehetővé teszi a felhasználók számára az előfizetés, az erőforráscsoport és a telepítés helyének kiválasztását.

:::image type="content" source="./media/create-uidefinition-overview/basics.png" alt-text="Lap varázsló":::

Ebben a szakaszban további elemeket is hozzáadhat. Ha lehetséges, adjon hozzá olyan elemeket, amelyek a központi telepítési szintű paramétereket kérdezik le, például egy fürt vagy egy rendszergazdai hitelesítő adatok nevét.

Az alábbi példa egy olyan szövegmezőt mutat be, amely az alapértelmezett elemekhez lett hozzáadva.

```json
"basics": [
    {
        "name": "textBox1",
        "type": "Microsoft.Common.TextBox",
        "label": "Textbox on basics",
        "defaultValue": "my text value",
        "toolTip": "",
        "visible": true
    }
]
```

## <a name="steps"></a>Lépések

A Steps tulajdonság nulla vagy több további lépést tartalmaz az alapvető beállítások megjelenítéséhez. Minden lépés egy vagy több elemet tartalmaz. Vegye fontolóra az üzembe helyezett alkalmazás szerepköreinek vagy szintjeinek hozzáadását. Adjon meg például egy lépést a főcsomópont bemenetei számára, valamint egy lépést a fürt munkavégző csomópontjaihoz.

```json
"steps": [
    {
        "name": "demoConfig",
        "label": "Configuration settings",
        "elements": [
          ui-elements-needed-to-create-the-instance
        ]
    }
]
```

## <a name="outputs"></a>Kimenetek

A Azure Portal a `outputs` tulajdonság használatával képezi le az elemeket a `basics` és a `steps` Azure Resource Manager központi telepítési sablon paraméterei között. A szótár kulcsai a sablon paramétereinek nevei, és az értékek a hivatkozott elemek kimeneti objektumainak tulajdonságai.

A felügyelt alkalmazás-erőforrás nevének megadásához meg kell adnia egy nevű értéket `applicationResourceName` a kimenetek tulajdonságban. Ha nem állítja be ezt az értéket, az alkalmazás hozzárendel egy GUID azonosítót a névhez. A felhasználói felületen olyan szövegmezőt is hozzáadhat, amely a felhasználótól származó nevet kér.

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

Maga a fájl createUiDefinition.jsegy egyszerű sémával rendelkezik. A valódi mélysége az összes támogatott elemtől és függvénytől származik. Ezeket az elemeket részletesebben a következő helyen ismertetjük:

- [Elemek](create-uidefinition-elements.md)
- [Functions](create-uidefinition-functions.md)

A createUiDefinition aktuális JSON-sémája itt érhető el: `https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json` .

Példa felhasználói felületre: [createUiDefinition.js](https://github.com/Azure/azure-managedapp-samples/blob/master/Managed%20Application%20Sample%20Packages/201-managed-app-using-existing-vnet/createUiDefinition.json).
