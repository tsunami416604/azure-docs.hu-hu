---
title: Virtuális gép címkézése sablon használatával
description: Ismerje meg, hogyan címkézheti a virtuális gépeket sablon használatával.
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.author: cynthn
author: cynthn
ms.date: 10/26/2018
ms.openlocfilehash: d1acbe82a086574a102e7897bbd3b99683c1185e
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2020
ms.locfileid: "94594979"
---
# <a name="tagging-a-vm-using-a-template"></a>Virtuális gép címkézése sablon használatával


Ez a cikk az Azure-beli virtuális gépek Resource Manager-sablonok használatával történő címkézését ismerteti. A címkék felhasználó által definiált kulcs/érték párok, amelyek közvetlenül egy erőforráson vagy erőforráscsoporton helyezhetők el. Az Azure jelenleg legfeljebb 50 címkét támogat erőforrás és erőforráscsoport szerint. A címkéket a létrehozáskor vagy egy meglévő erőforráshoz való hozzáadáskor lehet elhelyezni egy erőforráson.

[Ez a sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags) címkéket helyez el a következő erőforrásokon: számítás (virtuális gép), tárterület (Storage-fiók) és hálózat (nyilvános IP-cím, Virtual Network és hálózati adapter). Ez a sablon egy Windows rendszerű virtuális gép esetében használható, de Linux rendszerű virtuális gépekhez is adaptálható.

Kattintson az **üzembe helyezés az Azure** -ban gombra a [sablon hivatkozásán](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags). Ekkor megnyílik a [Azure Portal](https://portal.azure.com/) , ahol üzembe helyezheti a sablont.

![Egyszerű üzembe helyezés címkékkel](./media/tag/deploy-to-azure-tags.png)

Ez a sablon a következő címkéket tartalmazza: *részleg* , *alkalmazás* , és *létrehozta*. Ezeket a címkéket közvetlenül a sablonban is hozzáadhatja vagy szerkesztheti, ha különböző címkéket szeretne megadni.

![Azure-címkék sablonban](./media/tag/azure-tags-in-a-template.png)

Amint láthatja, a címkék kulcs/érték párokként vannak definiálva, kettősponttal elválasztva (:). A címkéket a következő formátumban kell megadni:

```config
"tags": {
    "Key1" : "Value1",
    "Key2" : "Value2"
}
```

Mentse a sablonfájlt a Szerkesztés befejezése után az Ön által választott címkékkel.

Ezután a **Paraméterek szerkesztése** szakaszban kitöltheti a címkék értékeit.

![Címkék szerkesztése a Azure Portalban](./media/tag/edit-tags-in-azure-portal.png)

Kattintson a **Létrehozás** gombra a sablon a címke értékeivel való telepítéséhez.


**Következő lépések**

- Az Azure-erőforrások címkézésével kapcsolatos további információkért lásd: [Azure Resource Manager áttekintése](../azure-resource-manager/management/overview.md) és [címkék használata az Azure-erőforrások rendszerezéséhez](../azure-resource-manager/management/tag-resources.md).
- Ha szeretné megtekinteni, hogyan segíthetnek az Azure-erőforrások használatának kezelésében, tekintse meg [Az Azure-számla megismerése](../cost-management-billing/understand/review-individual-bill.md) és [a Microsoft Azure erőforrás-felhasználás betekintését](../cost-management-billing/manage/usage-rate-card-overview.md)ismertető témakört.
