---
title: Adja meg az erőforráscsoportot a virtuális gépek az Azure DevTest Labs szolgáltatásban |} A Microsoft Docs
description: Ismerje meg, hogyan adjon meg egy erőforráscsoportot a virtuális gépek az Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2019
ms.author: spelluru
ms.openlocfilehash: e4e2a01bbac7aebb70852b93c51c32933cc75eec
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2019
ms.locfileid: "56652178"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>Adjon meg egy erőforráscsoportot a virtuális gépeiről az Azure DevTest Labs szolgáltatásban

Labortulajdonosként konfigurálhatja a labor virtuális gépek egy adott erőforráscsoportban kell létrehozni. Ez a funkció segítségével a következő esetekben:

- Az előfizetésében labs által létrehozott kevesebb erőforrást csoportokkal rendelkezik.
- A labs, az Ön által konfigurált erőforráscsoportok rögzített számú belüli működésre van.
- Korlátozások és az Azure-előfizetésen belüli erőforráscsoportok létrehozásához szükséges jóváhagyások megkerüléséhez.
- A egyszerűsítése érdekében ezeket az erőforrásokat követésének és alkalmazásának egyetlen erőforráscsoporton belül minden laborerőforrások konszolidálhatja [házirendek](../governance/policy/overview.md) az erőforráscsoport szintjén-erőforrások kezeléséhez.

Ezzel a funkcióval egy parancsfájl segítségével adja meg egy új vagy meglévő erőforráscsoportot az Azure-előfizetésen belül a labor virtuális gépeken. Jelenleg az Azure DevTest Labs szolgáltatásban Ez a funkció egy API-n keresztül támogatja.

## <a name="api-to-configure-a-resource-group-for-lab-vms"></a>API-t konfiguráljon egy erőforráscsoportot a labor virtuális gépeken
A következő lehetőségek állnak rendelkezésére labortulajdonosként az API használata esetén:

- Válassza ki a **tesztkörnyezet erőforráscsoport** minden virtuális gép.
- Válasszon egy **meglévő erőforráscsoportot** kivételével az összes virtuális gép erőforráscsoportját a labor létrehozása.
- Adjon meg egy **új erőforráscsoport** minden virtuális gép neve.
- Továbbra is használni a jelenlegi működését, amelyben egy erőforráscsoport jön létre az egyes virtuális Gépekhez a tesztkörnyezetben.
 
Ez a beállítás a lab-ben létrehozott új virtuális gépekre vonatkozik. A régebbi létrehozott virtuális gépek a tesztkörnyezetében elérhető saját erőforráscsoport érinti. A lab-ben létrehozott környezetekben továbbra is továbbra is elérhető a saját erőforráscsoport.

Útmutató az API-t használja:
- Használja API-verzió **2018_10_15_preview**.
- Ha megad egy új erőforráscsoportot, gondoskodjon arról, hogy **írási engedélyekkel az erőforráscsoportok** az előfizetésében. Ha nem rendelkezik írási engedéllyel, létrehozhat új virtuális gépeket a megadott erőforráscsoport meghiúsul.
- Az API használatakor adja át a **teljes erőforráscsoport azonosítója**. Például: `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>`. Győződjön meg arról, hogy az erőforráscsoport a lab mint ugyanabban az előfizetésben. 

## <a name="use-powershell"></a>A PowerShell használata 
Az alábbi példa bemutatja, hogyan labor összes virtuális gép létrehozása az új erőforráscsoport PowerShell-parancsfájl használatával.

```PowerShell
[CmdletBinding()]
Param(
    $subId,
    $labRg,
    $labName,
    $vmRg
)

az login | out-null

az account set --subscription $subId | out-null

$rgId = "/subscriptions/"+$subId+"/resourceGroups/"+$vmRg

"Updating lab '$labName' with vm rg '$rgId'..."

az resource update -g $labRg -n $labName --resource-type "Microsoft.DevTestLab/labs" --api-version 2018-10-15-preview --set properties.vmCreationResourceGroupId=$rgId

"Done. New virtual machines will now be created in the resource group '$vmRg'."
```

A szkript meghívása a következő parancs segítségével. ResourceGroup.ps1 el a fájlt, amely tartalmazza az előző parancsfájlt:

```PowerShell
.\ResourceGroup.ps1 -subId <subscriptionID> -labRg <labRGNAme> -labName <LanName> -vmRg <RGName> 
```

## <a name="use-an-azure-resource-manager-template"></a>Az Azure Resource Manager-sablonok használata
Ha a labor létrehozása az Azure Resource Manager-sablonnal használ, használja a **vmCreationResourceGroupId** tulajdonság labor tulajdonságok szakaszában a sablon a következő példában látható módon:

```json
        {
            "type": "microsoft.devtestlab/labs",
            "name": "[parameters('lab_name')]",
            "apiVersion": "2018_10_15_preview",
            "location": "eastus",
            "tags": {},
            "scale": null,
            "properties": {
                "vmCreationResourceGroupId": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>",
                "labStorageType": "Premium",
                "premiumDataDisks": "Disabled",
                "provisioningState": "Succeeded",
                "uniqueIdentifier": "000000000f-0000-0000-0000-00000000000000"
            },
            "dependsOn": []
        },
```


## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket: 

- [Tesztkörnyezet szabályzatainak beállítása](devtest-lab-get-started-with-lab-policies.md)
- [Gyakori kérdések](devtest-lab-faq.md)
