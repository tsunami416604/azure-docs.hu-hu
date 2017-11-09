---
title: "Virtuálisgép-bővítmények tartalmazó Azure erőforráscsoportok exportálása |} Microsoft Docs"
description: "Virtuálisgép-bővítmények tartalmazó Resource Manager-sablonok exportálása."
services: virtual-machines-windows
documentationcenter: 
author: danielsollondon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7f4e2ca6-f1c7-4f59-a2cc-8f63132de279
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: danis
ms.openlocfilehash: 739ae7995fca41fe8abb7cd54ccb72ff3bc43854
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2017
---
# <a name="exporting-resource-groups-that-contain-vm-extensions"></a>Virtuálisgép-bővítmények tartalmazó erőforráscsoportok exportálása

Azure erőforráscsoport-sablonok is exportálhatók be egy új Resource Manager-sablon, majd újratelepítése. Az exportálási folyamat értelmezi a meglévő erőforrásokat, és létrehoz egy Resource Manager-sablon, amely telepítésekor hasonló erőforráscsoport eredményez. A virtuálisgép-bővítmények tartalmazó erőforráscsoport elleni erőforráscsoport exportálása beállításának használata esetén több elem bővítmény kompatibilitási például figyelembe kell venni, és védett beállítások.

Ez az erőforráscsoport exportálási folyamat működésével kapcsolatban virtuálisgép-bővítmények, beleértve a listáját a dokumentum részletei bővítmény támogatott, és a védett adatok részleteinek kezelése.

## <a name="supported-virtual-machine-extensions"></a>Támogatott virtuálisgép-bővítmények

Több virtuálisgép-bővítmények érhetők el. Nem az összes bővítmény exportálhatja azokat egy Resource Manager-sablon, a "Automatizálási parancsfájl" funkció használata. A virtuálisgép-bővítmény nem támogatott, így ellenőrzi, hogy manuálisan helyezhetők vissza az exportált sablon.

A következő kiterjesztések exportálhatja az automatizálási parancsfájl szolgáltatással.

| Mellék ||||
|---|---|---|---|
| Acronis biztonsági mentése | Datadog Windows-ügynök | Javítás Linux operációs rendszer | Virtuális gép pillanatkép Linux
| Linux Acronis biztonsági mentése | Docker-bővítmény | Puppet ügynök |
| BG adatai | DSC-bővítmény | Hely 24 x 7 Apm felmérése |
| BMC CTM ügynök Linux | Dynatrace Linux | 24 x 7 Linux helykiszolgáló |
| BMC CTM ügynök Windows | Dynatrace Windows | Hely 24 x 7 a Windows Server |
| Chef ügyfél | HPE biztonsági alkalmazás Defender | Trend Micro DSA |
| Egyéni parancsfájl | Infrastruktúra-szolgáltatási kártevőirtó | Trend Micro DSA Linux |
| Egyéni szkriptbővítmény | IaaS-diagnosztika | Linux virtuális gép hozzáférés |
| Egyéni parancsfájl Linux | Linux-Chef ügyfél | Linux virtuális gép hozzáférés |
| Datadog Linux-ügynök | Linux-diagnosztika | Virtuális gép pillanatkép |

## <a name="export-the-resource-group"></a>Az erőforráscsoportok exportálása

Erőforráscsoport exportálása újrafelhasználható sablonba, kövesse az alábbi lépéseket:

1. Jelentkezzen be az Azure Portalra
2. A központ menüben kattintson a csoportok
3. A célként megadott erőforráscsoportja válasszon a listából
4. Az erőforráscsoport panelen kattintson az automatizálási parancsfájl

![Sablon exportálása](./media/extensions-export-templates/template-export.png)

Az Azure Resource Manager automatizálása parancsfájlt hoz létre a Resource Manager-sablon, egy paraméterfájl és több központi telepítési mintaparancsfájlok például a PowerShell és az Azure parancssori felület. Ekkor az exportált sablon letölthető a Letöltés gombra kattintva, a Sablonkönyvtár új sablonként hozzá, vagy újra telepíteni a telepítés gombra kattintva.

## <a name="configure-protected-settings"></a>Védett beállításainak konfigurálása

Sok Azure virtuálisgép-bővítmények közé tartozik egy védett beállításokat, titkosítja a bizalmas adatokat, például a hitelesítő adatokat és a konfigurációs karakterlánc. Védett beállítás nem exportálása az automation-parancsfájllal. Ha szükséges, a védett beállításokat szeretne lehet ismételten beszúrni az exportált sablon.

### <a name="step-1---remove-template-parameter"></a>1. lépés – a sablonparaméter eltávolítása

Az erőforráscsoport exportálásakor egyetlen sablonparaméter értéket adhat az exportált védett beállítások jön létre. Ezzel a paraméterrel eltávolítható. Távolítsa el a paramétert, nézze át a paraméterlista, és törli a következő paramétert, amely a JSON-példához hasonló.

```json
"extensions_extensionname_protectedSettings": {
    "defaultValue": null,
    "type": "SecureObject"
}
```

### <a name="step-2---get-protected-settings-properties"></a>2. lépés - Get védett beállításainak tulajdonságai

Mivel minden egyes védett beállítás szükséges tulajdonságait, ezek a tulajdonságok listájának kell összegyűjteni. Minden védett beállításokat paramétere megtalálható a [Azure Resource Manager séma a Githubon](https://raw.githubusercontent.com/Azure/azure-resource-manager-schemas/master/schemas/2015-08-01/Microsoft.Compute.json). Ebben a sémában csak az a jelen dokumentum-áttekintés szakaszban felsorolt kiterjesztések a paraméter-készletet tartalmaz. 

A belül a séma-tárházban, keresse meg a kívánt kiterjesztése, ez a példa `IaaSDiagnostics`. A bővítmények egyszer `protectedSettings` objektum található, jegyezze fel az egyes paramétereket. A példában a `IaasDiagnostic` bővítmény, a szükséges paraméterek `storageAccountName`, `storageAccountKey`, és `storageAccountEndPoint`.

```json
"protectedSettings": {
    "type": "object",
    "properties": {
        "storageAccountName": {
            "type": "string"
        },
        "storageAccountKey": {
            "type": "string"
        },
        "storageAccountEndPoint": {
            "type": "string"
        }
    },
    "required": [
        "storageAccountName",
        "storageAccountKey",
        "storageAccountEndPoint"
    ]
}
```

### <a name="step-3---re-create-the-protected-configuration"></a>3. lépés – a védett konfiguráció újbóli létrehozása

Az exportált sablon keressen `protectedSettings` és az exportált védett beállításobjektumot cserélje le egy új, amely tartalmazza a szükséges kiterjesztés paraméterei, és minden egyes értéket.

A példában a `IaasDiagnostic` bővítmény, az új védett beállítások konfigurálása jelenne meg az alábbi példa:

```json
"protectedSettings": {
    "storageAccountName": "[parameters('storageAccountName')]",
    "storageAccountKey": "[parameters('storageAccountKey')]",
    "storageAccountEndPoint": "https://core.windows.net"
}
```

A végső kiterjesztés erőforrás hasonlít a JSON-példa:

```json
{
    "name": "Microsoft.Insights.VMDiagnosticsSettings",
    "type": "extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "[variables('apiVersion')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "tags": {
        "displayName": "AzureDiagnostics"
    },
    "properties": {
        "publisher": "Microsoft.Azure.Diagnostics",
        "type": "IaaSDiagnostics",
        "typeHandlerVersion": "1.5",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), variables('vmName'), variables('wadcfgxend')))]",
            "storageAccount": "[parameters('existingdiagnosticsStorageAccountName')]"
        },
        "protectedSettings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "storageAccountKey": "[parameters('storageAccountKey')]",
            "storageAccountEndPoint": "https://core.windows.net"
        }
    }
}
```

Sablonparaméterek segítségével adja meg a tulajdonság értékét, ha ezek kell létrehozni. Létrehozásakor Sablonparaméterek védett, a beállítás értéke, ügyeljen arra, hogy használja a `SecureString` paraméter írja be, hogy az érzékeny értékek biztonságosak. Paraméterek használatával kapcsolatos további információkért lásd: [Azure Resource Manager-sablonok készítése](../../resource-group-authoring-templates.md).

A példában a `IaasDiagnostic` bővítmény, az alábbi paraméterekkel létrehozott Resource Manager-sablon a Paraméterek szakaszban.

```json
"storageAccountName": {
    "defaultValue": null,
    "type": "SecureString"
},
"storageAccountKey": {
    "defaultValue": null,
    "type": "SecureString"
}
```

Ezen a ponton a sablon is telepíthető a sablon az üzembe helyezési módszer használatával.
