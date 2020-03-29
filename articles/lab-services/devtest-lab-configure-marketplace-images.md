---
title: Az Azure Marketplace lemezképbeállításainak konfigurálása az Azure DevTest Labsben
description: Konfigurálja, hogy mely Azure Marketplace-lemezképek használhatók virtuális gép létrehozásakor az Azure DevTest Labsben
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 804c6af2-17e9-4320-af3a-f454bd398379
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 2dc3aa000cefc0e65305b58f8fdce93b94bfd35f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169554"
---
# <a name="configure-azure-marketplace-image-settings-in-azure-devtest-labs"></a>Az Azure Marketplace lemezképbeállításainak konfigurálása az Azure DevTest Labsben
A DevTest Labs támogatja a virtuális gépek létrehozását az Azure Marketplace-rendszerképek alapján, attól függően, hogy hogyan konfigurálta az Azure Marketplace-lemezképeket a laborban való használathoz. Ez a cikk bemutatja, hogyan adhatja meg, hogy az Azure Marketplace-lemezképek, ha használhatók, ha virtuális gépek létrehozása egy tesztkörnyezetben. Ez biztosítja, hogy a csapat csak a marketplace-lemezképek et kell elérni. 

## <a name="select-which-azure-marketplace-images-are-allowed-when-creating-a-vm"></a>Válassza ki, hogy mely Azure Marketplace-lemezképek engedélyezettek virtuális gép létrehozásakor
1. Jelentkezzen be az [Azure Portalra.](https://go.microsoft.com/fwlink/p/?LinkID=525040)
2. Válassza az **Összes szolgáltatás**lehetőséget, majd a listából válassza a **DevTest Labs** elemet.
3. A laborok listájából válassza ki a kívánt labort. 
4. A tesztkörnyezet paneljén válassza a **Konfiguráció és házirendek**lehetőséget.
5. A lab **konfigurációs és házirendek** panelen a **Virtuálisgép-alapzat ok**alatt válassza **a Marketplace-képek**lehetőséget.
6. Adja meg, hogy szeretné-e az összes minősített Azure Marketplace-lemezképek elérhetővé kell tenni egy új virtuális gép alapjaként. Ha az **Igen**lehetőséget választja, akkor az alábbi feltételeknek megfelelő összes Azure Marketplace-lemezkép engedélyezett a laborban:
   
   * A lemezkép egyetlen virtuális **and** géppel
   * A rendszerkép az Azure Resource Manager segítségével építi ki a virtuális gépeket, **és**
   * A kép nem igényel további licenccsomagot
     
     Ha azt szeretné, hogy a képek ne legyenek engedélyezve, vagy meg szeretné adni, hogy mely képek használhatók, válassza a **Nem**lehetőséget.
     
     ![Lehetőség, hogy az összes Marketplace-lemezképek használható alaplemezképek virtuális gépek](./media/devtest-lab-configure-marketplace-images/allow-all-marketplace-images.png)
7. Ha az előző lépésben a **Nem** lehetőséget választja, az **Engedélyezett képek/Az összes kijelölése** jelölőnégyzet engedélyezve van. 
   Ezzel a beállítással a keresőmezővel együtt gyorsan kijelölheti vagy kiválaszthatja a listában megjelenő összes elemet.
   * Válassza ki az Azure Marketplace-lemezképek, amelyeket engedélyezni szeretne a virtuális gép létrehozása külön-külön az egyes rendszerképek megfelelő jelölőnégyzetének ellenőrzésével.
   * Válasszon ki semmit a listából, ha nem szeretné engedélyezni az Azure Marketplace-lemezképek használatával a laborban.
   
     ![Megadhatja, hogy mely Azure Marketplace-lemezképek használhatók a virtuális gépek alaplemezképeiként](./media/devtest-lab-configure-marketplace-images/select-marketplace-images.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>További lépések
Miután konfigurálta, hogy az Azure Marketplace-lemezképek hogyan engedélyezettek a virtuális gép létrehozásakor, a következő lépés egy [virtuális gép hozzáadása a laborhoz.](devtest-lab-add-vm.md)

