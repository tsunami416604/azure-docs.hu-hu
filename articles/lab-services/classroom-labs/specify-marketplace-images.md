---
title: Marketplace-lemezképek megadása Azure Lab Servicesban található laborhoz
description: Ebből a cikkből megtudhatja, hogyan adhatja meg azokat a Piactéri rendszerképeket, amelyekkel a labor Creator használatával labor-fiókokat hozhat létre Azure Lab Servicesban.
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
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444666"
---
# <a name="specify-marketplace-images-available-to-lab-creators"></a>A tesztkörnyezet-készítők számára elérhető Piactéri lemezképek meghatározása
A tesztkörnyezetfiók tulajdonosaként megadhatja azokat a Marketplace-beli rendszerképeket, amelyek használatával a tesztkörnyezet-létrehozók tesztkörnyezeteket hozhatnak létre a tesztkörnyezetfiókban. 

## <a name="select-images-available-for-labs"></a>Válassza ki a Labs számára elérhető rendszerképeket
Válassza a bal oldali menüből a **Marketplace-beli rendszerképek** elemet. Alapértelmezés szerint a rendszerképek teljes listája jelenik meg (az engedélyezett és a letiltott rendszerképek egyaránt). A fenti legördülő listában a **Csak az engedélyezettek**/**Csak a letiltottak** lehetőségre kattintva szűrheti a listát, hogy csak az engedélyezett/letiltott rendszerképek jelenjenek meg. 
    
![Marketplace-beli rendszerképek oldal](../media/tutorial-setup-lab-account/marketplace-images-page.png)

A listában csak azok a Marketplace-beli rendszerképek jelennek meg, amelyek megfelelnek a következő feltételeknek:
    
- Egyetlen virtuális gépet hoznak létre.
- A virtuális gépek üzembe helyezéséhez az Azure Resource Managert használják.
- Nincs szükség külön licenccsomag vásárlására.

## <a name="disable-images-for-a-lab"></a>A tesztkörnyezet rendszerképeinek letiltása 
Egy tesztkörnyezet egyetlen rendszerképének letiltásához válassza a **... lehetőséget. (három pont)** az utolsó oszlopban, majd válassza a **rendszerkép letiltása**lehetőséget. 

![Egy rendszerkép letiltása](../media/tutorial-setup-lab-account/disable-one-image.png) 

Azt is megteheti, hogy a kép neve előtt bejelöli a jelölőnégyzetet, majd kiválasztja a **kijelölt lemezképek letiltása** lehetőséget az eszköztáron. 

Ha egyszerre több lemezképet szeretne letiltani, jelölje be a jelölőnégyzeteket a lemezkép neve előtt, majd válassza a **kijelölt lemezképek letiltása** lehetőséget az eszköztáron. 

![Több rendszerkép letiltása](../media/tutorial-setup-lab-account/disable-multiple-images.png) 


## <a name="enable-images-for-a-lab"></a>Képek engedélyezése laborhoz
A letiltott képek engedélyezéséhez válassza a... lehetőséget.  **(három pont)** az utolsó oszlopban válassza a **rendszerkép engedélyezése**lehetőséget. Azt is megteheti, hogy a kép neve előtt bejelöli a jelölőnégyzetet, majd kiválasztja a **kijelölt rendszerképek engedélyezése** lehetőséget az eszköztáron. 

Ha egyszerre több lemezképet szeretne letiltani, jelölje be a jelölőnégyzeteket a lemezkép neve előtt, majd válassza a **kijelölt lemezképek engedélyezése** lehetőséget az eszköztáron. 

## <a name="next-steps"></a>Következő lépések
Lásd az alábbi cikkeket:

- [Labor tulajdonosaként Labs létrehozása és kezelése](how-to-manage-classroom-labs.md)
- [A labor tulajdonosaként hozzon létre és tegyen közzé sablonokat](how-to-create-manage-template.md)
- [Tesztkörnyezet tulajdonosaként konfigurálhatja és szabályozhatja a labor használatát](how-to-configure-student-usage.md)
- [Labor-felhasználóként az osztályterem Labs eléréséhez](how-to-use-classroom-lab.md)
