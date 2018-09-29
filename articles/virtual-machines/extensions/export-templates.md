---
title: A Virtuálisgép-bővítmények tartalmazó Azure-erőforráscsoportok exportálása |} A Microsoft Docs
description: Resource Manager-sablonok a virtuális gépi bővítmények exportálása.
services: virtual-machines-windows
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 7f4e2ca6-f1c7-4f59-a2cc-8f63132de279
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: roiyz
ms.openlocfilehash: f56cfeeede393dbdb9632ea4120d3a81e89f3f7c
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47451969"
---
# <a name="exporting-resource-groups-that-contain-vm-extensions"></a>Erőforrás-csoportok, amelyek tartalmazzák a Virtuálisgép-bővítmények exportálása

Az Azure erőforrás-csoportok az új Resource Manager-sablonnal, majd újratelepítése exportálhatók. Az exportálási folyamat értelmezi a meglévő erőforrások, és létrehoz egy Resource Manager-sablon, amely telepítésekor hasonló erőforráscsoport eredményez. Az erőforráscsoport exportálási beállítás elleni tartalmazó virtuálisgép-bővítmények használata esetén több elemet bővítmény kompatibilitási például figyelembe kell venni, és a védett beállításainál.

Ez a dokumentum részletesen kapcsolatos virtuálisgép-bővítmények, például az az erőforráscsoport exportálási folyamat működése támogatja a bővítményeket, és a védett adatok részletei kezeléséről.

## <a name="supported-virtual-machine-extensions"></a>Támogatott virtuálisgép-bővítmények

Számos virtuálisgép-bővítmények érhetők el. Nem az összes bővítmény be az "Automation-szkript" funkció használatával a Resource Manager-sablon exportálhatók. Virtuálisgép-bővítmények nem támogatott, ha manuálisan kell helyezni az exportált sablonhoz szükséges.

A következő kiterjesztések exportálhatók az automatizálási parancsfájl szolgáltatásával.

| Mellék ||||
|---|---|---|---|
| Acronis biztonsági mentés | Datadoggal Windows-ügynök | Az operációs rendszer javításai a Linux rendszerre | VM-pillanatkép Linux
| Linux Acronis biztonsági mentés | Docker-bővítmény | Puppet ügynök |
| BG adatai | DSC-bővítmény | Hely 24 x 7 Apm Insight |
| BMC CTM ügynök Linux | Linux a dynatrace-szel | 24 x 7 Linux helykiszolgáló |
| BMC CTM ügynök Windows | Windows a dynatrace-szel | Hely 24 x 7, Windows Server |
| Chef-ügyfél | HPE biztonsági alkalmazás Defender | Trend Micro DSA |
| Egyéni szkript | IaaS kártevőirtó | Trend Micro DSA Linux |
| Egyéni szkriptbővítmény | IaaS-diagnosztika | Linuxos Virtuálisgép-hozzáférés |
| Egyéni parancsfájl Linux rendszeren | Linux-Chef ügyfél | Linuxos Virtuálisgép-hozzáférés |
| Linux-ügynök Datadoggal | Linux diagnosztikai | Virtuális gép pillanatképe |

## <a name="export-the-resource-group"></a>Az erőforráscsoport exportálása

Újrahasznosítható sablonok exportálása egy erőforráscsoportot, hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be az Azure Portalra
2. A központi menüben kattintson az erőforráscsoportok
3. Válassza ki a céloldali erőforráscsoport a listából
4. Az erőforráscsoport panelen kattintson az Automation-szkript

![A sablon exportálása](./media/export-templates/template-export.png)

Az Azure Resource Manager automatizálását parancsfájl Resource Manager-sablonnal, paraméterfájl és több üzembe helyezési mintaszkriptek például a PowerShell és az Azure CLI-vel hoz létre. Ezen a ponton az exportált sablon segítségével lehet letölteni a Letöltés gombra, vagy a könyvtár új sablonként hozzáadott, újratelepítése, az üzembe helyezés gomb használatával.

## <a name="configure-protected-settings"></a>Védett beállításainak konfigurálása

Számos Azure-beli virtuálisgép-bővítmény egy védett beállítások konfigurációját, titkosítja a bizalmas adatokat, például a hitelesítő adatok és a konfiguráció karakterláncokat tartalmaznak. Védett beállításokat a rendszer nem exportál az automation-szkript. Ha szükséges, a védett beállításait lehet ismételten beszúrni kell azokat az exportált sablonalapúak.

### <a name="step-1---remove-template-parameter"></a>1. lépés – a sablonparaméter eltávolítása

Amikor exportálja az erőforráscsoport, egy egyetlen sablonparaméter, adjon meg egy értéket az exportált védett beállítások jön létre. Ez a paraméter távolíthatja el. Távolítsa el a paramétert, nézze át a paraméterek listája, és törli a következő paramétert a JSON-példához hasonlóan néz ki.

```json
"extensions_extensionname_protectedSettings": {
    "defaultValue": null,
    "type": "SecureObject"
}
```

### <a name="step-2---get-protected-settings-properties"></a>2. lépés – Get védett tulajdonságai

Mivel minden egyes védett beállítás kötelező tulajdonságai készletét, ezek a tulajdonságok listáját kell összegyűjteni. A védett beállításainak konfigurálása az egyes paraméter található a [Azure Resource Manager-sémát a Githubon](https://raw.githubusercontent.com/Azure/azure-resource-manager-schemas/master/schemas/2015-08-01/Microsoft.Compute.json). Ebben a sémában csak a jelen dokumentum az Áttekintés szakaszban felsorolt bővítmények a paraméterkészlettel tartalmazza. 

A séma rendszerképréteg, keresse meg a kívánt bővítményt, ebben a példában `IaaSDiagnostics`. Miután a kiterjesztések `protectedSettings` objektum található, jegyezze fel az egyes paraméterek. Az a példában a `IaasDiagnostic` bővítmény, a szükséges paraméterek a következők `storageAccountName`, `storageAccountKey`, és `storageAccountEndPoint`.

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

Az exportált sablon lévő keresése `protectedSettings` és a egy újat, amely tartalmazza a kötelező kiterjesztés paraméterei, és minden egyes értéket cserélje le az exportált védett beállítás objektum.

A példában a `IaasDiagnostic` bővítmény, az új védett beállítás konfigurációjának jelenne meg az alábbi példához hasonlóan:

```json
"protectedSettings": {
    "storageAccountName": "[parameters('storageAccountName')]",
    "storageAccountKey": "[parameters('storageAccountKey')]",
    "storageAccountEndPoint": "https://core.windows.net"
}
```

A végső bővítmény erőforrás a következő JSON-példához hasonlóan néz ki:

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

Ha a tulajdonság értékének megadására, használja a sablon paramétereit, ezeket kell létrehozni. Létrehozásakor Sablonparaméterek értékeinek beállítása védett, ügyeljen arra, hogy használja a `SecureString` paraméter írja be, hogy bizalmas értékek biztonságosak. Paraméterek használatával kapcsolatos további információkért lásd: [Azure Resource Manager-sablonok készítése](../../resource-group-authoring-templates.md).

A példában a `IaasDiagnostic` bővítmény, az alábbi paraméterekkel jön a Resource Manager-sablon a paraméterek szakaszához.

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

Ezen a ponton a sablon is üzembe helyezhetők a sablon üzembe helyezési módszerrel.
