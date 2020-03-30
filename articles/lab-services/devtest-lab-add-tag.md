---
title: Címkék hozzáadása egy tesztkörnyezethez az Azure DevTest Labsben | Microsoft dokumentumok
description: Megtudhatja, hogy miként adhat hozzá címkét egy tesztkörnyezethez az Azure DevTest Labsben
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: dc5b327a-62e4-41bc-80ef-deb3c23d51b2
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2019
ms.author: spelluru
ms.openlocfilehash: 1d7175d000eebd3f68665e7f097bbe3f68025bc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72302799"
---
# <a name="add-tags-to-a-lab-in-azure-devtest-labs"></a>Címkék hozzáadása egy tesztkörnyezethez az Azure DevTest Labsben

Egyéni címkéket hozhat létre, és alkalmazhatja őket a DevTest Labs-erőforrásoklogikusan kategorizálni az erőforrásokat. Később gyorsan és egyszerűen megtekintheti az előfizetésben lévő összes olyan erőforrást, amely rendelkezik ezzel a címkével. A címkék akkor hasznosak, ha számlázási vagy felügyeleti erőforrásokat kell rendszereznie.

A címkék által támogatott erőforrások közé tartoznak a következők:

* Virtuális gépek számítása
* Hálózati adapterek (NIC-k)
* IP-címek
* Terheléselosztók
* Tárfiókok
* Felügyelt lemezek

Címkéket alkalmazhat, amikor [létrehoz egy tesztkörnyezetet,](devtest-lab-create-lab.md) és később kezelheti őket a Beállítások és beállítások csoport Címkék paneljén keresztül.

Minden címke egy/**névértékpárból** áll. **name** Létrehozhat például egy *costcenter* nevű címkét, amelynek értéke *34543.* Egy ilyen címke segíthet később azonosítani a szervezet adott területén számlázható laborerőforrásokat. Kiválaszthatja azelőfizetés rendszerezésének módját szolgáló neveket és értékeket.

## <a name="steps-to-manage-tags-in-an-existing-lab"></a>Címkék kezelésének lépései meglévő tesztkörnyezetben

1. Jelentkezzen be az [Azure Portalra.](https://go.microsoft.com/fwlink/p/?LinkID=525040)
1. Ha szükséges, válassza a **Minden szolgáltatás**lehetőséget, majd válassza a **DevTest Labs** elemet a listából. Előfordulhat, hogy a tesztkörnyezet már látható az irányítópulton a **Minden erőforrás csoportban.**
1. A laborok listájából válassza ki azt a tesztkörnyezetet, amelyben címkéket szeretne hozzáadni vagy kezelni.
1. A tesztkörnyezet **áttekintése** területen válassza a **Konfiguráció és házirendek**lehetőséget.

    ![Konfiguráció és házirendek gomb](./media/devtest-lab-add-tag/devtestlab-config-and-policies.png)

1. A BAL OLDALON válassza a **Címkék**lehetőséget a **KEZELÉS**csoportban.
1. Ha új címkét szeretne létrehozni ehhez a tesztkörnyezethez, adjon meg egy/**Névérték** párt, és válassza a **Mentés lehetőséget.** **Name** A listából is kijelölhet egy meglévő címkét a címkéhez társított erőforrások megtekintéséhez vagy kezeléséhez.

    ![Címkék kezelése](./media/devtest-lab-add-tag/devtestlab-manage-tags.png)

> [!NOTE]
> A tesztkörnyezet szintjén létrehozott címkék végighaladnak az összes számlázható erőforráson, amelyet a tesztkörnyezet az előfizetésben felpörget. Például a tesztkörnyezet szintű címkék a tesztkörnyezetvirtuális gépek alapjául szolgáló számítási virtuális gépekhez áramlanak. A laborszintű címkék megjelennek a költségkezelés címkeszűrőjében.

## <a name="understanding-limitations-to-tags"></a>A címkékre vonatkozó korlátozások ismertetése

Az alábbi korlátozások érvényesek a címkékre:

* Minden egyes erőforrás vagy erőforráscsoport legfeljebb 15 címkenév/érték párral rendelkezhet. A korlátozás csak a közvetlenül az erőforráscsoportra vagy erőforrásra alkalmazott címkékre érvényes. Az erőforráscsoportok sok olyan erőforrást tartalmazhatnak, amelyek mindegyike 15 címkenév/érték párral rendelkezik.
* A címke neve legfeljebb 512 karakter, a címke értéke pedig legfeljebb 256 karakter hosszúságú lehet. A tárfiókok esetében a címke neve legfeljebb 128 karakter, a címke értéke pedig legfeljebb 256 karakter hosszúságú lehet.
* Az egyes erőforráscsoportokra alkalmazott címkéket nem öröklik az adott erőforráscsoportba tartozó erőforrások.

[Címkék használatával rendszerezheti az Azure-erőforrásokat,](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) így részletesebben ismerteti a címkék használatát az Azure-ban, többek között a címkék PowerShell vagy Az Azure CLI használatával történő kezelésével kapcsolatban.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>További lépések
* Korlátozásokat és konvenciókat alkalmazhat az előfizetésben a testreszabott szabályzatok használatával. A megadott házirendek esetében szükség lehet arra, hogy minden erőforrásnak legyen értéke egy adott címkéhez. További információt a [Házirendek és ütemezések beállítása című témakörben talál.](devtest-lab-set-lab-policy.md)
* Fedezze fel a [DevTest Labs Azure Resource Manager rövid útmutató sablongyűjteményét.](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)
