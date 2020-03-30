---
title: Virtuálisgép-bővítményeket tartalmazó Azure-erőforráscsoportok exportálása
description: Virtuálisgép-bővítményeket tartalmazó Erőforrás-kezelő sablonok exportálása.
services: virtual-machines-windows
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 7f4e2ca6-f1c7-4f59-a2cc-8f63132de279
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: akjosh
ms.openlocfilehash: 79991dad96742109817d579b951082d1a30e3951
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253909"
---
# <a name="exporting-resource-groups-that-contain-vm-extensions"></a>Virtuálisgép-bővítményeket tartalmazó erőforráscsoportok exportálása

Az Azure Resource Groups exportálható egy új Resource Manager-sablonba, amely ezután újratelepíthető. Az exportálási folyamat értelmezi a meglévő erőforrásokat, és létrehoz egy Erőforrás-kezelő sablont, amely telepítéskor hasonló erőforráscsoportot eredményez. Ha az Erőforráscsoport exportálási beállítást virtuálisgép-bővítményeket tartalmazó erőforráscsoporttal használja, több elemet is figyelembe kell venni, például a bővítmények kompatibilitását és a védett beállításokat.

Ez a dokumentum részletezi, hogyan működik az erőforráscsoport exportálási folyamata a virtuálisgép-bővítmények tekintetében, beleértve a támogatott bővítmények listáját és a biztonságos adatok kezelésének részleteit.

## <a name="supported-virtual-machine-extensions"></a>Támogatott virtuálisgép-bővítmények

Számos virtuálisgép-bővítmény érhető el. Nem minden bővítmény exportálható erőforrás-kezelősablonba az "Automation Script" szolgáltatás használatával. Ha egy virtuálisgép-bővítmény nem támogatott, manuálisan kell visszahelyezni az exportált sablonba.

A következő bővítmények exportálhatók az automation script funkcióval.

| Mellék ||||
|---|---|---|---|
| Acronis biztonsági mentés | Datadog Windows ügynök | OS javítás Linuxra | Virtuális gép pillanatképe Linux
| Acronis Biztonsági Mentés Linux | Docker-bővítmény | Bábügynök |
| Bg információ | DSC-bővítmény | Site 24x7 Apm Insight |
| BMC CTM Ügynök Linux | Dynatrace Linux | Site 24x7 Linux Server |
| BMC CTM ügynök Windows | Dynatrace Windows | Hely 24x7 Windows Server |
| Chef ügyfél | HPE biztonsági alkalmazás védő | Trend Mikro DSA |
| Egyéni szkript | IaaS kártevőirtó | Trend Mikro DSA Linux |
| Egyéni szkriptbővítmény | IaaS diagnosztika | Vm-hozzáférés Linuxra |
| Egyéni Script Linux | Linux Chef kliens | Vm-hozzáférés Linuxra |
| Datadog Linux ügynök | Linux diagnosztika | Virtuális gép pillanatképe |

## <a name="export-the-resource-group"></a>Az erőforráscsoport exportálása

Ha újrafelhasználható sablonba szeretne exportálni egy erőforráscsoportot, hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be az Azure Portalra
2. A Központ menüben kattintson az Erőforráscsoportok parancsra.
3. A célerőforrás-csoport kijelölése a listából
4. Az Erőforráscsoport panelen kattintson az Automation Script elemre.

![Sablon exportálása](./media/export-templates/template-export.png)

Az Azure Resource Manager automations parancsfájl létrehoz egy Resource Manager-sablont, egy paraméterfájlt és számos minta központi telepítési parancsfájlt, például a PowerShellt és az Azure CLI-t. Ezen a ponton az exportált sablon letölthető a letöltés gombbal, új sablonként hozzáadva a sablontárhoz, vagy újratelepíthető a telepítés gombbal.

## <a name="configure-protected-settings"></a>Védett beállítások konfigurálása

Számos Azure virtuálisgép-bővítmény tartalmaz egy védett beállítási konfigurációt, amely titkosítja a bizalmas adatokat, például a hitelesítő adatokat és a konfigurációs karakterláncokat. A védett beállítások at nem exportálja a rendszer az automatizálási parancsfájllal. Szükség esetén a védett beállításokat újra be kell illeszteni az exportált sablonba.

### <a name="step-1---remove-template-parameter"></a>1. lépés - Sablonparaméter eltávolítása

Az erőforráscsoport exportálásakor egyetlen sablonparaméter jön létre, amely értéket ad az exportált védett beállításoknak. Ez a paraméter eltávolítható. A paraméter eltávolításához tekintse át a paraméterlistát, és törölje a JSON-példához hasonló paramétert.

```json
"extensions_extensionname_protectedSettings": {
    "defaultValue": null,
    "type": "SecureObject"
}
```

### <a name="step-2---get-protected-settings-properties"></a>2. lépés - Védett beállítások tulajdonságainak beszereznie

Mivel minden védett beállítás rendelkezik a szükséges tulajdonságok készletével, ezeknek a tulajdonságoknak a listáját össze kell gyűjteni. A védett beállítások konfigurációjának minden paramétere megtalálható az [Azure Resource Manager sémában a GitHubon.](https://raw.githubusercontent.com/Azure/azure-resource-manager-schemas/master/schemas/2015-08-01/Microsoft.Compute.json) Ez a séma csak a jelen dokumentum áttekintő szakaszában felsorolt bővítmények paraméterkészleteit tartalmazza. 

A sématárból keresse meg a kívánt bővítményt `IaaSDiagnostics`ebben a példában. Miután megtalálta `protectedSettings` a bővítményobjektumot, vegye figyelembe az egyes paramétereket. A bővítmény `IaasDiagnostic` példájában a szükséges paraméterek `storageAccountName` `storageAccountKey`a `storageAccountEndPoint`, és a.

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

### <a name="step-3---re-create-the-protected-configuration"></a>3. lépés - A védett konfiguráció újbóli létrehozása

Az exportált sablonon `protectedSettings` keresse meg és cserélje le az exportált védett beállításobjektumot egy olyan újobjektumra, amely tartalmazza a szükséges kiterjesztési paramétereket és mindegyikhez egy értéket.

A bővítmény példájában az `IaasDiagnostic` új védett beállításkonfiguráció a következő példához hasonlóan nézne ki:

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

Ha sablonparamétereket használ tulajdonságértékek megadásához, ezeket létre kell hozni. Amikor sablonparamétereket hoz létre a védett beállítási `SecureString` értékekhez, győződjön meg arról, hogy a paramétertípust használja, hogy a bizalmas értékek biztonságosak legyenek. A paraméterek használatáról az [Azure Resource Manager-sablonok készítése című témakörben olvashat bővebben.](../../resource-group-authoring-templates.md)

A bővítmény példájában a `IaasDiagnostic` következő paraméterek jönnek létre az Erőforrás-kezelő sablon paraméterek szakaszában.

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

Ezen a ponton a sablon bármilyen sablon telepítési módszerrel telepíthető.
