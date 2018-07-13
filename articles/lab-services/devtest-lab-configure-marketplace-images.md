---
title: Azure Marketplace-en kép beállításainak konfigurálása az Azure DevTest Labs szolgáltatásban |} A Microsoft Docs
description: Konfigurálása, mely az Azure Marketplace-rendszerképek is használható, ha a virtuális gép létrehozása az Azure DevTest Labs szolgáltatásban
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
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: 90df853eeca89e61fa69e6967218c4c18274ac1b
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38633036"
---
# <a name="configure-azure-marketplace-image-settings-in-azure-devtest-labs"></a>Azure Marketplace-en kép beállításainak konfigurálása az Azure DevTest Labs szolgáltatásban
DevTest Labs létrehozása virtuális gépek Azure piactér képeinek attól függően, hogyan konfigurálta az Azure Marketplace-rendszerképek alapján az használható a tesztkörnyezet támogatja. Ez a cikk bemutatja, hogyan határozhatja meg, ha van ilyen, az Azure Marketplace-rendszerképek lehet a labor virtuális gépek létrehozásakor használt. Ez biztosítja, hogy a csapat csak szükségük van a Marketplace-rendszerképek a hozzáférést. 

## <a name="select-which-azure-marketplace-images-are-allowed-when-creating-a-vm"></a>Válassza ki, mely az Azure Marketplace-rendszerképek használata engedélyezett a virtuális gép létrehozásakor
1. Jelentkezzen be az [Azure Portalra](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Válassza ki **minden szolgáltatás**, majd válassza ki **DevTest Labs** a listából.
3. Tesztkörnyezetek listájában jelölje ki a kívánt tesztkörnyezetben. 
4. A labor paneljén válassza ki a **Konfigurace a zásady**.
5. A tesztkörnyezet **Konfigurace a zásady** panel **virtuálisgép-bázisok**válassza **Piactérről származó rendszerképek**.
6. Adja meg, hogy minden a minősített Azure Marketplace-en képek egy új virtuális gép alapként használható. Ha **Igen**, majd az Azure Marketplace-lemezkép a laborban engedélyezett a következő feltételeknek megfelelő:
   
   * A lemezképet hoz létre egyetlen virtuális Gépet, **és**
   * A kép használ Azure Resource Manager-beli virtuális gépek kiépítése **és**
   * A kép nem szükséges egy külön licencelési csomag vásárlása
     
    Ha azt szeretné, hogy a kép nem számára engedélyezett, vagy meg szeretné határozni melyik lemezképek is használhatók, jelölje be **nem**.
     
     ![A beállítást, az alaplemezképek a virtuális gépek felhasználhatók minden Marketplace-rendszerképek](./media/devtest-lab-configure-marketplace-images/allow-all-marketplace-images.png)
7. Ha **nem** az előző lépéshez, a **engedélyezett képek/válassza ki az összes** jelölőnégyzet engedélyezve van. 
   Használhatja ezt a beállítást a keresőmezőbe együtt gyors jelölje be vagy törölje az összes elem megjelenik a listában.
   * Válassza ki az Azure Marketplace-rendszerképek külön-külön engedélyezheti a virtuális gép létrehozása az egyes rendszerképek megfelelő jelölőnégyzet bejelölésével.
   * Válassza ki semmi a listából, ha nem szeretné, hogy minden Azure Marketplace-rendszerképek a lab-ben használható.
   
    ![Megadhatja, mely az Azure Marketplace-rendszerképek használható alaplemezképek a virtuális gépek](./media/devtest-lab-configure-marketplace-images/select-marketplace-images.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>További lépések
Miután konfigurálta, hogy hogyan használata engedélyezett az Azure Marketplace-rendszerképek, virtuális gép létrehozásakor, a következő lépés az, hogy [virtuális gép hozzáadása a labor](devtest-lab-add-vm.md).

