---
title: Osztályterem-tesztkörnyezet beállítása az Azure Lab Services szolgáltatással | Microsoft Docs
description: Ebben az oktatóanyagban megtanulhatja, hogyan állíthat be egy tesztkörnyezetet osztálytermi használatra.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 07/23/2018
ms.author: spelluru
ms.openlocfilehash: fe41728b6f08ba767dbcb40d0595b9f7cdc79615
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39420199"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Oktatóanyag: Osztályterem-tesztkörnyezet beállítása 
Ebben az oktatóanyagban megtanulhatja, hogyan állíthat be egy diákok által használható virtuális gépekkel rendelkező osztályterem-tesztkörnyezetet.  

Az oktatóanyag során a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * Osztályterem-tesztkörnyezet létrehozása
> * Az osztályterem-tesztkörnyezet konfigurálása
> * Regisztrációs hivatkozás küldése a diákoknak

## <a name="prerequisites"></a>Előfeltételek
A **Tesztkörnyezet-létrehozó** szerepkörrel kell rendelkeznie a tesztkörnyezetfiókban ahhoz, hogy létrehozhasson egy osztályterem-tesztkörnyezetet. A tesztkörnyezetfiók létrehozásához használt fiók automatikusan hozzáadódik ehhez a szerepkörhöz. A tesztkörnyezet tulajdonosa a [felhasználó a Tesztkörnyezet-létrehozó szerepkörhöz történő hozzáadását](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role) ismertető cikkben leírt lépésekkel adhat hozzá további felhasználókat a Tesztkörnyezet-létrehozó szerepkörhöz.


## <a name="create-a-classroom-lab"></a>Osztályterem-tesztkörnyezet létrehozása

1. Lépjen az [Azure Lab Services weboldalára](https://labs.azure.com). 
2. Válassza a **Bejelentkezés** lehetőséget, és adja meg a hitelesítő adatait. Az Azure Lab Services támogatja a szervezeti fiókok és a Microsoft-fiókok használatát is. 
3. Az **Új tesztkörnyezet** ablakban tegye a következőket: 
    1. Adja meg az osztályterem-tesztkörnyezet **nevét**. 
    2. Válassza ki az osztályteremben használni kívánt virtuális gép **méretét**.
    3. Válassza ki a virtuális gép létrehozásához használandó **rendszerképet**.
    4. Adja meg a tesztkörnyezetben lévő virtuális gépbe való bejelentkezéshez szükséges **alapértelmezett hitelesítő adatokat**. 
    7. Kattintson a **Mentés** gombra.

        ![Osztályterem-tesztkörnyezet létrehozása](../media/tutorial-setup-classroom-lab/new-lab-window.png)
1. A tesztkörnyezet létrehozása után válassza a **Tovább a tesztkörnyezetre** lehetőséget. 

    ![Tovább a tesztkörnyezetre](../media/tutorial-setup-classroom-lab/go-to-my-lab.png)
1. A rendszer megjeleníti a tesztkörnyezet **irányítópultját**. 
    
    ![Az osztályterem-tesztkörnyezet irányítópultja](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)

## <a name="configure-usage-policy"></a>Használati szabályzat konfigurálása

1. Válassza a **Használati szabályzat** lehetőséget. 
2. A **Használati szabályzat** terület beállításaiban adja meg, **hány felhasználó** használhatja a tesztkörnyezetet.
3. Kattintson a **Mentés** gombra. 

    ![Használati szabályzat](../media/tutorial-setup-classroom-lab/usage-policy-settings.png)


## <a name="set-up-the-template"></a>Sablon beállítása 
A tesztkörnyezet sablonja egy alapszintű virtuálisgép-rendszerkép, amelyből az összes felhasználó virtuális gépe létrejön. Úgy állítsa be a virtuálisgép-sablont, hogy az pontosan aszerint legyen konfigurálva, amit a tesztkörnyezet felhasználóinak biztosítani kíván. A sablonhoz megadhat egy nevet és egy leírást, amely a tesztkörnyezet felhasználói számára jelenik meg. A sablon közzétételével a tesztkörnyezet felhasználói számára elérhető példányokat hozhat létre a virtuálisgép-sablonról. 

### <a name="set-title-and-description"></a>Cím és leírás beállítása
1. A **Sablon** szakaszban válassza a sablonhoz tartozó **Szerkesztés** (ceruza ikon) elemet. 
2. A **Felhasználói nézet** ablakban adja meg a sablon **címét**.
3. Adja meg a sablon **leírását**.
4. Kattintson a **Mentés** gombra.

    ![Az osztályterem-tesztkörnyezet leírása](../media/tutorial-setup-classroom-lab/lab-description.png)

### <a name="set-up-the-template-vm"></a>Virtuálisgép-sablon beállítása
 A virtuálisgép-sablonhoz csatlakozva telepítse a szükséges szoftvereket, mielőtt elérhetővé tenné a sablont a diákok számára. 

1. Várja meg, amíg elkészül a virtuálisgép-sablon. Ha elkészült, elérhetővé válik a **Start** gomb. A virtuális gép elindításához kattintson a **Start** gombra.

    ![A virtuálisgép-sablon elindítása](../media/tutorial-setup-classroom-lab/start-template-vm.png)
1. A virtuális géphez való csatlakozáshoz kattintson a **Csatlakozás** gombra, majd kövesse az utasításokat. 

    ![Csatlakozás a virtuálisgép-sablonhoz](../media/tutorial-setup-classroom-lab/connect-template-vm.png)
1. Telepítse a diákok számára a tesztkörnyezet használatához szükséges szoftvereket (például Visual Studio, Azure Storage Explorer stb). 
2. Szakítsa meg a kapcsolatot a virtuálisgép-sablonnal (zárja be a távoli asztali munkamenetet). 
3. **Állítsa le** a virtuálisgép-sablont a **Leállítás** kiválasztásával. 

    ![A virtuálisgép-sablon leállítása](../media/tutorial-setup-classroom-lab/stop-template-vm.png)

### <a name="publish-the-template"></a>A sablon közzététele 
Amikor közzétesz egy sablont, az Azure Lab Services létrehozza a virtuális gépeket a tesztkörnyezetben a sablon használatával. A folyamat során létrehozott virtuális gépek száma megegyezik a tesztkörnyezet felhasználóinak maximális számával, amelyet a tesztkörnyezet használati szabályzatában állíthat be. A virtuális gépek konfigurációja megegyezik a sablonéval. 

1. A **Sablon** szakaszban válassza a **Közzététel** lehetőséget. 

    ![A virtuálisgép-sablon közzététele](../media/tutorial-setup-classroom-lab/public-access.png)
1. A **Közzététel** ablakban válassza a **Közzétett** lehetőséget. 
2. Most kattintson a **Közzététel** gombra. Ez a folyamat eltarthat egy ideig a létrehozandó virtuális gépek számától függően, amely a tesztkörnyezet felhasználóinak maximális számával egyezik meg.
    
    > [!IMPORTANT]
    > Miután a sablon közzé lett téve, a közzétételt nem lehet visszavonni. 
4. Váltson a **Virtuális gépek** lapra, és ellenőrizze, hogy vannak-e ott **Nem hozzárendelt** állapotú virtuális gépek. Ezek a virtuális gépek még nincsenek diákokhoz rendelve. 

    ![Virtual machines (Virtuális gépek)](../media/tutorial-setup-classroom-lab/virtual-machines.png)
5. Várja meg, amíg befejeződik a virtuális gépek létrehozása. **Leállított** állapotban kell lenniük. Ezen a lapon indíthatja el a virtuális gépeket, csatlakozhat hozzájuk, leállíthatja, valamint törölheti őket. A virtuális gépeket elindíthatja ezen a lapon, vagy engedheti, hogy a diákjai indítsák el őket. 

    ![Leállított állapotban levő virtuális gépek](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

## <a name="send-registration-link-to-students"></a>Regisztrációs hivatkozás küldése a diákoknak

1. Váltson az **Irányítópult** nézetre. 
2. Válassza a **Felhasználói regisztráció** csempét.

    ![A diákok regisztrációs hivatkozása](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. A **Felhasználói regisztráció** párbeszédablakban kattintson a **Másolás** gombra. A rendszer a vágólapra másolja a hivatkozást. Illessze be egy e-mail-szerkesztőbe, majd küldje el e-mailben a diákoknak. 

    ![A diákok regisztrációs hivatkozása](../media/tutorial-setup-classroom-lab/registration-link.png)
2. A **Felhasználói regisztráció** párbeszédablakban kattintson a **Bezárás** gombra. 


## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban létrehozott egy osztályterem-tesztkörnyezetet, és konfigurálta azt. Ha meg szeretné tudni, hogyan férhetnek hozzá a diákok a tesztkörnyezet virtuális gépeihez a regisztrációs hivatkozással, folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Kapcsolódás az osztályterem-tesztkörnyezet virtuális gépeihez](tutorial-connect-virtual-machine-classroom-lab.md)

