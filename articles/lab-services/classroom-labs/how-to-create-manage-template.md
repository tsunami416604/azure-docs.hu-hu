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
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: fd3396a98359aef966cd8bb2f984e556fc6abcc7
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73584869"
---
# <a name="create-and-manage-a-classroom-template-in-azure-lab-services"></a>Tantermi sablon létrehozása és kezelése Azure Lab Services
A tesztkörnyezet sablonja egy alapszintű virtuálisgép-rendszerkép, amelyből az összes felhasználó virtuális gépe létrejön. Úgy állítsa be a virtuálisgép-sablont, hogy az pontosan aszerint legyen konfigurálva, amit a tesztkörnyezet felhasználóinak biztosítani kíván. A sablonhoz megadhat egy nevet és egy leírást, amely a tesztkörnyezet felhasználói számára jelenik meg. Ezután közzéteszi a sablont, hogy a sablonban lévő virtuális gép példányai elérhetővé tegyék a tesztkörnyezet felhasználói számára. Amikor közzétesz egy sablont, az Azure Lab Services létrehozza a virtuális gépeket a tesztkörnyezetben a sablon használatával. A folyamat során létrehozott virtuális gépek száma megegyezik a tesztkörnyezet felhasználóinak maximális számával, amelyet a tesztkörnyezet használati szabályzatában állíthat be. A virtuális gépek konfigurációja megegyezik a sablonéval.

Ez a cikk bemutatja, hogyan hozhat létre és kezelhet egy sablonból álló virtuális gépet a Azure Lab Services egy osztályterem laborjában. 

## <a name="publish-a-template-while-creating-a-classroom-lab"></a>Sablon közzététele tantermi labor létrehozásakor
Ha meg szeretné tudni, hogyan tehet közzé egy sablont egy osztálytermi labor létrehozásakor, tekintse meg [a tantermi labor létrehozása](how-to-manage-classroom-labs.md#create-a-classroom-lab) című témakört.
 
## <a name="set-or-update-template-title-and-description"></a>Sablon címének és leírásának beállítása vagy frissítése
A következő lépésekkel állíthatja be a címet és a leírást az első alkalommal, és később frissítheti őket. 

1. A **sablon** lapon adja meg a labor új **címét** .  
2. Adja meg a sablon új **leírását** . Ha áthelyezi a fókuszt a szövegmezőből, az automatikusan mentve lesz. 

    ![Sablon neve és leírása](../media/how-to-create-manage-template/template-name-description.png)

## <a name="update-a-template-vm"></a>Sablonos virtuális gép frissítése
A sablon virtuális gép frissítéséhez kövesse az alábbi lépéseket.  

1. A **sablon** lapon válassza a **sablon testreszabása** lehetőséget az eszköztáron. 
2. A **sablon testreszabása** párbeszédpanelen válassza a **Folytatás**lehetőséget. A sablon elindítása és a módosítások elvégzése után már nem lesz ugyanaz a beállítás, mint a virtuális gépek a felhasználók számára legutóbb közzétéve. A sablon módosításait a rendszer addig nem jeleníti meg a felhasználók meglévő virtuális gépein, amíg újra nem teszi közzé.

    ![Testreszabás párbeszédpanel](../media/how-to-create-manage-template/customize-template-dialog.png)
1. Várjon, amíg a sablon virtuális gépe elindult, majd válassza a **Kapcsolódás sablonhoz** lehetőséget az eszköztáron a sablon virtuális géphez való kapcsolódáshoz, és kövesse az utasításokat. Ha ez egy Windows rendszerű gép, megjelenik egy lehetőség az RDP-fájl letöltéséhez. 

    ![Csatlakozás a virtuálisgép-sablonhoz](../media/how-to-create-manage-template/connect-template-vm.png)
1. Telepítse a diákok számára a tesztkörnyezet használatához szükséges szoftvereket (például Visual Studio, Azure Storage Explorer stb). 
2. Szakítsa meg a kapcsolatot a virtuálisgép-sablonnal (zárja be a távoli asztali munkamenetet). 
3. **Állítsa le** a sablon virtuális gépet a **sablon leállítása**lehetőség kiválasztásával. 
4. Kövesse a következő szakaszban leírt lépéseket a sablon frissített virtuális gépe **közzétételéhez** . 

## <a name="publish-the-template-vm"></a>A virtuálisgép-sablon közzététele  
Ha nem teszi közzé a sablont a labor létrehozása során, akkor később közzéteheti. A közzététel előtt érdemes lehet csatlakozni a sablonhoz a virtuális géphez, és frissíteni azt bármilyen szoftverrel. Amikor közzétesz egy sablont, az Azure Lab Services létrehozza a virtuális gépeket a tesztkörnyezetben a sablon használatával. Az ebben a folyamatban létrehozott virtuális gépek száma az első közzétételekor vagy a virtuálisgép-készlet lapon megadott számú virtuális gép száma. A virtuális gépek konfigurációja megegyezik a sablonéval. 

1. A **sablon** lapon válassza a **Közzététel** lehetőséget az eszköztáron. 
1. A **sablon közzététele** üzenetben tekintse át az üzenetet, és válassza a **Közzététel**lehetőséget. A folyamat eltarthat egy ideig attól függően, hogy hány virtuális gépet hoznak létre.

    ![Közzététel gomb](../media/how-to-create-manage-template/publish-button.png)

    > [!IMPORTANT]
    > Miután a sablon közzé lett téve, a közzétételt nem lehet visszavonni. A sablon ismételt közzétételét azonban megteheti. 
4. A közzétételi folyamat állapotát a sablon lapon tekintheti meg. Várjon, amíg a sablon állapota **közzétettre**változik. 

    ![Közzététel állapota](../media/how-to-create-manage-template/publish-status.png)
1. Váltson a **Virtuális gépek** lapra, és ellenőrizze, hogy vannak-e ott **Nem hozzárendelt** állapotú virtuális gépek. Ezek a virtuális gépek még nincsenek diákokhoz rendelve. Várja meg, amíg befejeződik a virtuális gépek létrehozása. **Leállított** állapotban kell lenniük. Ezen a lapon indíthatja el a virtuális gépeket, csatlakozhat hozzájuk, leállíthatja, valamint törölheti őket. Elindíthatja őket ezen az oldalon, vagy engedélyezheti a diákoknak a virtuális gépek indítását. 

    ![Leállított állapotban levő virtuális gépek](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)


## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

- [Rendszergazdaként, labor-fiókok létrehozása és kezelése](how-to-manage-lab-accounts.md)
- [Labor tulajdonosaként Labs létrehozása és kezelése](how-to-manage-classroom-labs.md)
- [Tesztkörnyezet tulajdonosaként konfigurálhatja és szabályozhatja a labor használatát](how-to-configure-student-usage.md)
- [Labor-felhasználóként az osztályterem Labs eléréséhez](how-to-use-classroom-lab.md)
