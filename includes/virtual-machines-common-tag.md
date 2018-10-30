---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: ccc2b574ea054a1b0ecf32a1e59691050fb66fcf
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50227294"
---
## <a name="tagging-a-virtual-machine-through-templates"></a>Sablonok virtuális gép címkézése
Először is lássuk a sablonokban történő címkézése. [Ez a sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags) címkék helyez el az alábbi forrásanyagokat: számítási (virtuális gép), a tárolási (Tárfiók) és a hálózaton (a nyilvános IP-címet, a virtuális hálózat és a hálózati adapter). Ez a sablon egy Windows virtuális gép számára, de alkalmazható Linux rendszerű virtuális gépekhez.

Kattintson a **üzembe helyezés az Azure** gombra a [sablon hivatkozása](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags). Ez nyitja meg a [az Azure portal](https://portal.azure.com/) ahol telepítheti ezt a sablont.

![Egyszerű üzembe helyezés, a címkék használatával](./media/virtual-machines-common-tag/deploy-to-azure-tags.png)

Ez a sablon tartalmazza a következő címkékkel: *részleg*, *alkalmazás*, és *Created By*. Akkor is hozzáadása/szerkesztése ezekkel a címkékkel, közvetlenül a sablonban Ha szeretné, hogy különböző tartalmaznak a címkenevek.

![A sablonokban Azure címkék](./media/virtual-machines-common-tag/azure-tags-in-a-template.png)

Amint láthatja, a címkéket kulcs/érték párok, egy kettőspontot (:) elválasztva vannak meghatározva. A címkék definiálni kell a következő formátumban:

        “tags”: {
            “Key1” : ”Value1”,
            “Key2” : “Value2”
        }

Mentse a sablonfájlt, miután befejezte a szerkesztést, a címkék a választott.

Ezután a **paraméterek szerkesztése** szakaszban kitöltheti az értékeket a címkék.

![Címkék szerkesztése az Azure Portalon](./media/virtual-machines-common-tag/edit-tags-in-azure-portal.png)

Kattintson a **létrehozás** a címkeértékeket az a sablon üzembe helyezésére.

## <a name="tagging-through-the-portal"></a>A portálon keresztül címkézése
Miután létrehozta az erőforrások címkékkel rendelkező, megtekintheti, adja hozzá, és törli a címkét a portálon.

A címkék ikonra a címkék megtekintéséhez:

![A címkék ikonra az Azure Portalon](./media/virtual-machines-common-tag/azure-portal-tags-icon.png)

A portálon keresztül új címke hozzáadása a saját kulcs/érték pár definiálásával, és mentse azt.

![Új címke hozzáadása az Azure Portalon](./media/virtual-machines-common-tag/azure-portal-add-new-tag.png)

Az új címke most meg kell jelennie az erőforráshoz tartozó címkék listája.

![Új címke mentése az Azure Portalon](./media/virtual-machines-common-tag/azure-portal-saved-new-tag.png)

