---
title: Erőforráscsoport megadása virtuális gépekhez az Azure DevTest Labsben | Microsoft dokumentumok
description: Megtudhatja, hogyan adhat meg erőforráscsoportot a virtuális gépekhez az Azure DevTest Labs tesztkörnyezetében.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/03/2019
ms.author: spelluru
ms.openlocfilehash: 29816d158cf1428727b7ff17bcc2c347f402dedf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77134536"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>Erőforráscsoport megadása az Azure DevTest Labs ben lévő tesztkörnyezetben lévő virtuális gépekhez

Labortulajdonosként konfigurálhatja a tesztkörnyezet virtuális gépeit egy adott erőforráscsoportban létrehozva. Ez a funkció a következő esetekben nyújt segítséget:

- Kevesebb erőforráscsoportok által létrehozott labs az előfizetésben.
- A laborok a konfigurált erőforráscsoportok rögzített készletén belül működjenek.
- Az Azure-előfizetésen belüli erőforráscsoportok létrehozásához szükséges korlátozások és jóváhagyások megkerülése.
- Konszolidálja az összes labor erőforrást egyetlen erőforráscsoporton belül, hogy egyszerűsítse az erőforrások nyomon követését és az erőforrások erőforráscsoport szintjén történő kezeléséhez szükséges [házirendek](../governance/policy/overview.md) alkalmazását.

Ezzel a funkcióval egy parancsfájl segítségével adhat meg egy új vagy meglévő erőforráscsoportot az Azure-előfizetésében az összes tesztkörnyezetbeli virtuális géphez. Jelenleg az Azure DevTest Labs támogatja ezt a funkciót egy API-n keresztül.

> [!NOTE]
> Minden előfizetési korlát akkor érvényes, ha laborokat hoz létre a DevTest Labs-ben. Gondoljon egy tesztkörnyezetre, mint bármely más erőforrásra az előfizetésében. Erőforráscsoportok esetén a korlát [előfizetésenként 980 erőforráscsoport.](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits) 

## <a name="use-azure-portal"></a>Az Azure Portal használata
Az alábbi lépések végrehajtásával adja meg a laborban létrehozott összes virtuális gép erőforráscsoportját. 

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Válassza a bal oldali navigációs menü **Minden szolgáltatás lehetőséget.** 
3. Válassza a **DevTest Labs** elemet a listából.
4. A laborok listájából válassza ki a **labort.**  
5. A bal oldali menü **Beállítások** szakaszában válassza a **Konfiguráció és házirendek** lehetőséget. 
6. Válassza a bal oldali menü **Lab-beállításai parancsát.** 
7. Válassza az **Összes virtuális gép egy erőforráscsoportban**lehetőséget. 
8. Jelöljön ki egy meglévő erőforráscsoportot a legördülő listában (vagy) válassza **az Új létrehozása lehetőséget,** adja meg az erőforráscsoport **nevét,** és válassza az **OK gombot.** 

    ![Az összes tesztkörnyezet virtuális gépéhez az erőforráscsoport kiválasztása](./media/resource-group-control/select-resource-group.png)

## <a name="use-powershell"></a>A PowerShell használata 
A következő példa bemutatja, hogyan egy PowerShell-parancsfájl használatával hozzon létre egy új erőforráscsoportban az összes tesztkörnyezet virtuális gépét.

```powershell
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

A parancsfájl meghívása a következő paranccsal. A ResourceGroup.ps1 fájl az előző parancsfájlt tartalmazó fájl:

```powershell
.\ResourceGroup.ps1 -subId <subscriptionID> -labRg <labRGNAme> -labName <LanName> -vmRg <RGName> 
```

## <a name="use-an-azure-resource-manager-template"></a>Azure Resource Manager-sablon használata
Ha egy Azure Resource Manager-sablont használ egy tesztkörnyezet létrehozásához, használja a **vmCreationResourceGroupId tulajdonságot** a sablon labortulajdonságai nak szakaszában, ahogy az a következő példában látható:

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


## <a name="api-to-configure-a-resource-group-for-lab-vms"></a>API-t a tesztkörnyezet virtuális gépei erőforráscsoportjának konfigurálásához
Az API használatakor a következő lehetőségek állnak rendelkezésre a tesztkörnyezet tulajdonosaként:

- Válassza ki a **labor erőforráscsoport** az összes virtuális gép.
- Válasszon egy **meglévő erőforráscsoportot** a labor erőforráscsoportjától eltérő minden virtuális géphez.
- Adja meg az összes virtuális gép **új erőforráscsoportnevét.**
- Folytassa a meglévő viselkedés használatát, amelyben a laborban minden egyes virtuális géphez létrehoz egy erőforráscsoportot.
 
Ez a beállítás a tesztkörnyezetben létrehozott új virtuális gépekre vonatkozik. A laborban a saját erőforráscsoportokban létrehozott régebbi virtuális gépek változatlanok maradnak. A laborban létrehozott környezetek továbbra is a saját erőforráscsoportokban maradnak.

Az API használata:
- Az API-verzió **használata 2018_10_15_preview.**
- Ha új erőforráscsoportot ad meg, győződjön meg arról, hogy **rendelkezik írási engedélyekkel az előfizetéserőforrás-csoportokhoz.** Ha nem rendelkezik írási engedélyekkel, a megadott erőforráscsoportban új virtuális gépek létrehozása sikertelen lesz.
- Az API használata közben adja át a **teljes erőforráscsoport-azonosítót.** Például: `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>`. Győződjön meg arról, hogy az erőforráscsoport ugyanabban az előfizetésben van, mint a labor. 


## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket: 

- [Szabályzatok beállítása egy tesztkörnyezethez](devtest-lab-get-started-with-lab-policies.md)
- [Gyakori kérdések](devtest-lab-faq.md)
