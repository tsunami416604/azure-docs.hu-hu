---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: f6bd574c83d309ce6d6f54fdb1c7d23cb713420d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73182225"
---
## <a name="tagging-a-virtual-machine-through-templates"></a>Virtuális gép címkézése sablonokon keresztül
Először nézzük meg a sablonok címkézését. [Ez](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags) a sablon címkéket helyez el a következő erőforrásokon: Számítási (virtuális gép), Storage (Storage Account) és Hálózati (nyilvános IP-cím, virtuális hálózat és hálózati adapter). Ez a sablon egy Windows virtuális géphez készült, de linuxos virtuális gépekhez adaptálható.

Kattintson az **Azure-ba való üzembe helyezés** gombra a [sablonhivatkozáson.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags) Ez az [Azure Portalra](https://portal.azure.com/) kerül, ahol telepítheti ezt a sablont.

![Egyszerű telepítés címkékkel](./media/virtual-machines-common-tag/deploy-to-azure-tags.png)

Ez a sablon a következő címkéket tartalmazza: *Részleg*, *Alkalmazás*és *Létrehozva*. Ezeket a címkéket közvetlenül a sablonban is hozzáadhatja/szerkesztheti, ha különböző címkeneveket szeretne.

![Azure-címkék egy sablonban](./media/virtual-machines-common-tag/azure-tags-in-a-template.png)

Mint látható, a címkék kulcs/érték párokként vannak definiálva, kettősponttal (:) elválasztva. A címkéket ebben a formátumban kell definiálni:

        "tags": {
            "Key1" : "Value1",
            "Key2" : "Value2"
        }

Mentse a sablonfájlt, miután befejezte a szerkesztést az Ön által kiválasztott címkékkel.

Ezután a **Paraméterek szerkesztése** szakaszban kitöltheti a címkék értékeit.

![Címkék szerkesztése az Azure Portalon](./media/virtual-machines-common-tag/edit-tags-in-azure-portal.png)

A **Létrehozás gombra** kattintva telepítse ezt a sablont a címkeértékekkel.

## <a name="tagging-through-the-portal"></a>Címkézés a portálon keresztül
Miután címkékkel létrehozta az erőforrásokat, megtekintheti, hozzáadhatja és törölheti a címkéket a portálon.

A címkék megtekintéséhez válassza a címkék ikonját:

![Címkék ikon az Azure Portalon](./media/virtual-machines-common-tag/azure-portal-tags-icon.png)

Adjon hozzá egy új címkét a portálon saját kulcs/érték pár definiálásával, és mentse azt.

![Új címke hozzáadása az Azure Portalon](./media/virtual-machines-common-tag/azure-portal-add-new-tag.png)

Az új címkének most meg kell jelennie az erőforrás címkéinek listájában.

![Az Azure Portalon mentett új címke](./media/virtual-machines-common-tag/azure-portal-saved-new-tag.png)

