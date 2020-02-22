---
title: Tantermi labor sablonjának kezelése Azure Lab Servicesban | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre és kezelhet egy tantermi Lab-sablont Azure Lab Servicesban.
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
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: fcf31fcc266358911612c25e0b73a0a9de696b1d
ms.sourcegitcommit: 163be411e7cd9c79da3a3b38ac3e0af48d551182
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2020
ms.locfileid: "77539022"
---
# <a name="create-and-manage-a-classroom-template-in-azure-lab-services"></a>Tantermi sablon létrehozása és kezelése Azure Lab Services
A tesztkörnyezet sablonja egy alapszintű virtuálisgép-rendszerkép, amelyből az összes felhasználó virtuális gépe létrejön. Úgy állítsa be a virtuálisgép-sablont, hogy az pontosan aszerint legyen konfigurálva, amit a tesztkörnyezet felhasználóinak biztosítani kíván. A sablonhoz megadhat egy nevet és egy leírást, amely a tesztkörnyezet felhasználói számára jelenik meg. Ezután közzéteszi a sablont, hogy a sablonban lévő virtuális gép példányai elérhetővé tegyék a tesztkörnyezet felhasználói számára. Amikor közzétesz egy sablont, az Azure Lab Services létrehozza a virtuális gépeket a tesztkörnyezetben a sablon használatával. A folyamat során létrehozott virtuális gépek száma megegyezik a tesztkörnyezet felhasználóinak maximális számával, amelyet a tesztkörnyezet használati szabályzatában állíthat be. A virtuális gépek konfigurációja megegyezik a sablonéval.

Ez a cikk bemutatja, hogyan hozhat létre és kezelhet egy sablonból álló virtuális gépet a Azure Lab Services egy osztályterem laborjában. 

## <a name="set-or-update-template-title-and-description"></a>Sablon címének és leírásának beállítása vagy frissítése
A következő lépésekkel állíthatja be a címet és a leírást az első alkalommal, és később frissítheti őket. 

1. A **sablon** lapon adja meg a labor új **címét** .  
2. Adja meg a sablon új **leírását** . Ha áthelyezi a fókuszt a szövegmezőből, az automatikusan mentve lesz. 

    ![Sablon neve és leírása](../media/how-to-create-manage-template/template-name-description.png)

## <a name="update-a-template-vm"></a>Sablonos virtuális gép frissítése
A sablon virtuális gép frissítéséhez kövesse az alábbi lépéseket.  

1. Várjon, amíg a sablon virtuális gépe elindult, majd válassza a **Kapcsolódás sablonhoz** lehetőséget az eszköztáron a sablon virtuális géphez való kapcsolódáshoz, és kövesse az utasításokat. Ha ez egy Windows rendszerű gép, megjelenik egy lehetőség az RDP-fájl letöltéséhez. 
1. Miután kapcsolódott a sablonhoz, és módosításokat hajt végre, a továbbiakban nem lesz ugyanazzal a beállítással, mint a virtuális gépek a felhasználók számára utoljára közzétéve. A sablon módosításait a rendszer addig nem jeleníti meg a felhasználók meglévő virtuális gépein, amíg újra nem teszi közzé.

    ![Csatlakozás a virtuálisgép-sablonhoz](../media/how-to-create-manage-template/connect-template-vm.png)
    
1. Telepítse a diákok számára a tesztkörnyezet használatához szükséges szoftvereket (például Visual Studio, Azure Storage Explorer stb). 
1. Szakítsa meg a kapcsolatot a virtuálisgép-sablonnal (zárja be a távoli asztali munkamenetet). 
1. **Állítsa le** a sablon virtuális gépet a **sablon leállítása**lehetőség kiválasztásával. 
1. Kövesse a következő szakaszban leírt lépéseket a sablon frissített virtuális gépe **közzétételéhez** . 

## <a name="publish-the-template-vm"></a>A virtuálisgép-sablon közzététele  
Ebben a lépésben közzéteszi a sablon virtuális gépet. Amikor közzéteszi a sablon virtuális gépet, Azure Lab Services létrehozza a virtuális gépeket a laborban a sablon használatával. A virtuális gépek konfigurációja megegyezik a sablonéval.


1. A **sablon** lapon válassza a **Közzététel** lehetőséget az eszköztáron. 

    ![Sablon közzététele gomb](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Közzététel után a lépés nem vonható vissza. 
2. A **sablon közzététele** lapon adja meg a laborban létrehozni kívánt virtuális gépek számát, majd válassza a **Közzététel**lehetőséget. 

    ![Sablon közzététele – virtuális gépek száma](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
3. A sablon **közzétételének állapota** az oldalon látható. Ez a folyamat akár egy órát is igénybe vehet. 

    ![Sablon közzétételének folyamata](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. Várjon, amíg a közzététel befejeződik, majd váltson a **Virtual Machines Pool** lapra, ehhez válassza a bal oldali menüben a **virtuális gépek** lehetőséget, vagy a **virtuális gépek** csempét. Győződjön meg arról, hogy a nem **hozzárendelt** állapotú virtuális gépek láthatók. Ezek a virtuális gépek még nincsenek diákokhoz rendelve. **Leállított** állapotban kell lenniük. Ezen a lapon indíthatja el a virtuális gépeket, csatlakozhat hozzájuk, leállíthatja, valamint törölheti őket. A virtuális gépeket elindíthatja ezen a lapon, vagy engedheti, hogy a diákjai indítsák el őket. 

    ![Leállított állapotban levő virtuális gépek](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)
## <a name="next-steps"></a>Következő lépések
Lásd az alábbi cikkeket:

- [Rendszergazdaként, labor-fiókok létrehozása és kezelése](how-to-manage-lab-accounts.md)
- [Labor tulajdonosaként Labs létrehozása és kezelése](how-to-manage-classroom-labs.md)
- [Tesztkörnyezet tulajdonosaként konfigurálhatja és szabályozhatja a labor használatát](how-to-configure-student-usage.md)
- [Labor-felhasználóként az osztályterem Labs eléréséhez](how-to-use-classroom-lab.md)
