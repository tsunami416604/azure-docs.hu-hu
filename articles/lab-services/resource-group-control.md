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
ms.openlocfilehash: 94e5f5b29e93409df2373cf6c56e8185dc5373a2
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2019
ms.locfileid: "56312974"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>Adjon meg egy erőforráscsoportot a virtuális gépeiről az Azure DevTest Labs szolgáltatásban
Labortulajdonosként konfigurálhatja a labor virtuális gépek egy adott erőforráscsoportban kell létrehozni. Ez a funkció segítségével a következő esetekben: 

- Az előfizetésében labs által létrehozott kevesebb erőforrást csoportokkal rendelkezik.
- A labs, az Ön által konfigurált erőforráscsoportok rögzített számú belüli működésre van
- Korlátozások és az Azure-előfizetésen belüli erőforráscsoportok létrehozásához szükséges jóváhagyások megkerüléséhez.
- A egyszerűsítése érdekében ezeket az erőforrásokat követésének és alkalmazásának egyetlen erőforráscsoporton belül minden laborerőforrások konszolidálhatja [házirendek](../governance/policy/overview.md) kezelheti őket az erőforráscsoport szintjén.

Ezzel a funkcióval is használhat egy parancsfájl adja meg egy új vagy egy meglévő erőforráscsoportot az Azure-előfizetésen belül a labor virtuális gépeken. Jelenleg a DevTest Labs-API-n keresztül a szolgáltatás támogatja. 

## <a name="api-to-configure-a-resource-group-for-lab-virtual-machines"></a>API-t konfiguráljon egy erőforráscsoportot a labor virtuális gépekhez
Most nézzük meg a beállításokat, az API használatakor labortulajdonosként rendelkezik: 

- Választhat a **tesztkörnyezet erőforráscsoport** minden virtuális gép.
- Választhat egy **meglévő erőforráscsoportot** kivételével az összes virtuális gép erőforráscsoportját a labor létrehozása.
- Megadhat egy **új erőforráscsoport** minden virtuális gép neve.
- A jelenlegi működését, folytathatja azt jelenti, egy erőforráscsoport jön létre az egyes virtuális Gépekhez a tesztkörnyezetben.
 
Ez a beállítás a lab-ben létrehozott új virtuális gépekre vonatkozik. A régebbi létrehozott virtuális gépek a tesztkörnyezetében elérhető saját erőforráscsoport továbbra is változatlan marad. A lab-ben létrehozott környezetekben továbbra is továbbra is elérhető a saját erőforráscsoport.

### <a name="how-to-use-this-api"></a>Útmutató az API-t használja:
- Az API-verzió használata **2018_10_15_preview** az API használata során. 
- Ha megad egy új erőforráscsoportot, gondoskodjon arról, hogy **írási engedélyekkel az erőforráscsoportok** az előfizetésben. Írási engedélyekkel, nem rendelkező új virtuális gépek létrehozását a megadott erőforrás-csoport eredmény hibát. 
- Az API használatakor adja át a **teljes erőforráscsoport azonosítója**. Például: `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>`. Győződjön meg arról, hogy az erőforráscsoport, a labor létrehozása, amely ugyanabban az előfizetésben. 

## <a name="use-powershell"></a>A PowerShell használata 
A következő példában labor összes virtuális gép létrehozása egy új erőforráscsoportot, egy PowerShell-szkripttel ismerteti.

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

A szkript a következő parancsot (a parancsfájl a fájlt, amely tartalmazza az előző ResourceGroup.ps1) meghívásához: 

```PowerShell
.\ResourceGroup.ps1 -subId <subscriptionID> -labRg <labRGNAme> -labName <LanName> -vmRg <RGName> 
```

## <a name="use-azure-resource-manager-template"></a>Az Azure Resource Manager sablonjainak használata
Ha a labor létrehozása az Azure Resource Manager-sablont használ, használja a **vmCreationResourceGroupId** tulajdonság labor tulajdonságok szakaszában a Resource Manager-sablon a következő példában látható módon:

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
