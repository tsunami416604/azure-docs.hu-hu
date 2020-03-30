---
title: Piactéri lemezképek megadása egy laborhoz az Azure Lab Servicesben
description: Ez a cikk bemutatja, hogyan adja meg a Marketplace-lemezképek, amelyek labor készítője segítségével laborok létrehozása egy laborfiókban az Azure Lab Services- ben.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: ad56041f853d030e3a286610fe4872bffecaee12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444666"
---
# <a name="specify-marketplace-images-available-to-lab-creators"></a>A laborkészítők számára elérhető Marketplace-képek megadása
A tesztkörnyezetfiók tulajdonosaként megadhatja azokat a Marketplace-beli rendszerképeket, amelyek használatával a tesztkörnyezet-létrehozók tesztkörnyezeteket hozhatnak létre a tesztkörnyezetfiókban. 

## <a name="select-images-available-for-labs"></a>A laborokban elérhető képek kiválasztása
Válassza a bal oldali menüből a **Marketplace-beli rendszerképek** elemet. Alapértelmezés szerint a rendszerképek teljes listája jelenik meg (az engedélyezett és a letiltott rendszerképek egyaránt). A lista szűrésével csak az engedélyezett/letiltott képeket láthatja, ha a lista tetején található legördülő listából bejelöli a **Csak**/**engedélyezve csak letiltva** lehetőséget. 
    
![Marketplace-beli rendszerképek oldal](../media/tutorial-setup-lab-account/marketplace-images-page.png)

A listában csak azok a Marketplace-beli rendszerképek jelennek meg, amelyek megfelelnek a következő feltételeknek:
    
- Egyetlen virtuális gépet hoznak létre.
- A virtuális gépek üzembe helyezéséhez az Azure Resource Managert használják.
- Nincs szükség külön licenccsomag vásárlására.

## <a name="disable-images-for-a-lab"></a>Képek letiltása laborban 
Egyetlen kép letiltásához válassza a **... (Ellipszis)** az utolsó oszlopban, és válassza **a Kép letiltása**lehetőséget. 

![Egy rendszerkép letiltása](../media/tutorial-setup-lab-account/disable-one-image.png) 

Másik lehetőségként a kép neve előtt jelölje be a jelölőnégyzetet, és válassza a **Kijelölt képek letiltása lehetőséget** az eszköztáron. 

Ha egyszerre több képet szeretne letiltani, jelölje be a jelölőnégyzeteket a képnevek előtt, és válassza a **Kijelölt képek letiltása** lehetőséget az eszköztáron. 

![Több rendszerkép letiltása](../media/tutorial-setup-lab-account/disable-multiple-images.png) 


## <a name="enable-images-for-a-lab"></a>Képek engedélyezése laborban
A letiltott kép engedélyezéséhez válassza **a ... (Ellipszis)** az utolsó oszlopban, és válassza **a Kép engedélyezése**lehetőséget. Másik lehetőségként jelölje be a jelölőnégyzetet a kép neve előtt, és válassza a **Kijelölt képek engedélyezése lehetőséget** az eszköztáron. 

Ha egyszerre több képet szeretne letiltani, jelölje be a jelölőnégyzeteket a képnevek előtt, és jelölje be a **Kijelölt képek engedélyezése** jelölőnégyzetet az eszköztáron. 

## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

- [Labortulajdonosként hozzon létre és kezeljen laborokat](how-to-manage-classroom-labs.md)
- [Labortulajdonosként sablonok beállítása és közzététele](how-to-create-manage-template.md)
- [Labortulajdonosként konfigurálja és szabályozza a tesztkörnyezet használatát](how-to-configure-student-usage.md)
- [Laborfelhasználóként tantermi laborok elérése](how-to-use-classroom-lab.md)
