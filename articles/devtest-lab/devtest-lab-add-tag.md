---
title: "Címkék hozzáadása egy tesztkörnyezetet a Azure DevTest Labs szolgáltatásban |} Microsoft Docs"
description: "Megtudhatja, hogyan címke hozzáadása egy laborhoz a Azure DevTest Labs szolgáltatásban"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: dc5b327a-62e4-41bc-80ef-deb3c23d51b2
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2017
ms.author: tarcher
ms.openlocfilehash: c268c1a10e4a313d0520f96e452ef978b6201115
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="add-tags-to-a-lab-in-azure-devtest-labs"></a>Címkék hozzáadása egy tesztkörnyezetet a Azure DevTest Labs szolgáltatásban

Egyéni címkék létrehozása, és alkalmazni azokat a DevTest Labs erőforrásokat logikailag kategorizálhatja az erőforrásokat. Később gyorsan és könnyen Észreveheti, az előfizetés adott címkével rendelkező erőforrásokhoz. Címkék hasznosak, ha az erőforrások számlázási vagy felügyeleti rendszerezéséhez szüksége.

Címkék által támogatott erőforrások közé tartoznak a

* Virtuális gépek számítási
* Hálózati adapter
* IP-címek
* Terheléselosztók
* Tárfiókok
* Felügyelt lemezek

Alkalmazhat mikor címkéket, [létrehozhat egy tesztkörnyezetet](devtest-lab-create-lab.md) és később a Címkék panel konfigurációja és beállításai keresztül kezelheti azokat.

Minden tag épül fel egy **neve**/**érték** pár. Például létrehozhat egy címkét a nevű *costcenter* , és adja neki *34543*. Egy címkét, mint például a segítségére lehetnek később azonosíthatók a labor erőforrások, amelyek az adott területre a szervezet számlázható. Válassza ki a neveket és értékeket, amelyeket célszerű hogyan szeretné rendezni az előfizetéshez tartozó jelenik meg.

## <a name="steps-to-manage-tags-in-an-existing-lab"></a>Egy meglévő laborban címkék felügyeletének lépései

1. Jelentkezzen be az [Azure Portalra](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Ha szükséges, jelölje be **több szolgáltatások**, majd válassza ki **DevTest Labs** a listából. Előfordulhat, hogy az irányítópult már látható a labor **összes erőforrás**.
1. Labs listában jelölje ki a labor, amelyben létre szeretné hozzáadásához vagy címkék kezeléséhez.  
1. A tesztlabor a **áttekintése** területen válassza **konfigurációs és házirendek**.  

    ![Konfigurációs és házirendek gomb](./media/devtest-lab-add-tag/devtestlab-config-and-policies.png)

1. A bal oldali alatt **kezelése**, jelölje be **címkék**.
1. A jelen labor új címke létrehozásához adjon meg egy **neve**/**érték** párosítsa, és válassza ki **mentése**. Kiválaszthat egy meglévő címkéhez is megtekinthetik és kezelheti az erőforrásokat, hogy a címke társított listájáról.

    ![Címkék kezelése](./media/devtest-lab-add-tag/devtestlab-manage-tags.png)

## <a name="understanding-limitations-to-tags"></a>Címkék korlátozások ismertetése

Az alábbi korlátozások érvényesek a címkékre:

* Minden egyes erőforrás vagy erőforráscsoport legfeljebb 15 címkenév/érték párral rendelkezhet. A korlátozás csak a közvetlenül az erőforráscsoportra vagy erőforrásra alkalmazott címkékre érvényes. Az erőforráscsoportok sok olyan erőforrást tartalmazhatnak, amelyek mindegyike 15 címkenév/érték párral rendelkezik. 
* A címke neve legfeljebb 512 karakter, a címke értéke pedig legfeljebb 256 karakter hosszúságú lehet. A tárfiókok esetében a címke neve legfeljebb 128 karakter, a címke értéke pedig legfeljebb 256 karakter hosszúságú lehet.
* Az egyes erőforráscsoportokra alkalmazott címkéket nem öröklik az adott erőforráscsoportba tartozó erőforrások.

[Az Azure-erőforrások rendszerezése címkék használatával](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) nagyobb részletesen bemutatja a címkék használata az Azure, beleértve a mobileszközök kezelése a PowerShell vagy Azure CLI címkék.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Következő lépések
* Testreszabott házirendekkel alkalmazhat korlátozások és egyezmények az előfizetéshez. Megadhat egy házirendet, minden erőforrás rendelkezik-e egy adott címke értéke lehet szükség. További információkért lásd: [házirendek és ütemezések](devtest-lab-set-lab-policy.md).
* Megismerkedhet a [Office DevTest Labs Azure Resource Manager gyorsindítási sablonok](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
