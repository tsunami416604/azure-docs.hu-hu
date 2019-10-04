---
title: Tantermi laborok kezelése Azure Lab Servicesban | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre és konfigurálhat egy tantermi labort, megtekintheti az összes tantermi labort, megoszthatja a regisztrációs hivatkozást egy labor felhasználóval, vagy törölhet egy labort.
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
ms.date: 06/07/2019
ms.author: spelluru
ms.openlocfilehash: 1f9cb82abd5bc0823f5e7bc23fe437007bccc8e0
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70873582"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Tantermi laborok kezelése Azure Lab Services 
Ez a cikk a tantermi laborok létrehozását és törlését ismerteti. Azt is bemutatja, hogyan tekintheti meg az összes osztályterem Labs-t egy labor-fiókban. 

## <a name="prerequisites"></a>Előfeltételek
A **Tesztkörnyezet-létrehozó** szerepkörrel kell rendelkeznie a tesztkörnyezetfiókban ahhoz, hogy létrehozhasson egy osztályterem-tesztkörnyezetet. A tesztkörnyezetfiók létrehozásához használt fiók automatikusan hozzáadódik ehhez a szerepkörhöz. A labor tulajdonosa további felhasználókat adhat hozzá a labor létrehozói szerepkörhöz a következő cikkben ismertetett lépések segítségével: [Adjon hozzá egy felhasználót a labor létrehozói szerepkörhöz](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).

## <a name="create-a-classroom-lab"></a>Osztályterem-tesztkörnyezet létrehozása

1. Lépjen az [Azure Lab Services weboldalára](https://labs.azure.com). Vegye figyelembe, hogy az Internet Explorer 11 még nem támogatott. 
2. Válassza **a bejelentkezés**lehetőséget. Válasszon ki vagy adjon meg egy olyan **felhasználói azonosítót** , amely tagja a **labor Creator** szerepkörnek a labor-fiókban, majd írja be a jelszót. Az Azure Lab Services támogatja a szervezeti fiókok és a Microsoft-fiókok használatát is. 
3. Az **Új tesztkörnyezet** ablakban tegye a következőket: 
    1. Adjon egy **nevet** a tesztkörnyezetnek. 
    2. A laborban található **virtuális gépek maximális számát** határozza meg. A laborban később növelheti vagy csökkentheti a virtuális gépek számát. 
    6. Kattintson a **Mentés** gombra.

        ![Osztályterem-tesztkörnyezet létrehozása](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. A **virtuális gép specifikációinak kiválasztására** szolgáló lapon hajtsa végre a következőket:
    1. Válasszon **méretet** a tesztkörnyezetben létrehozott virtuális gépeknek. Jelenleg a **kis**, **közepes**, **közepes (virtualizációs)** , **nagyméretű**és **GPU** méretek használata engedélyezett. Részletekért lásd a [VM-méretek](#vm-sizes) szakaszt.
    1. Válassza ki a **régiót**, amelyben létre szeretné hozni a virtuális gépeket. 
    1. Válassza ki a tesztkörnyezetben a virtuális gépek létrehozásához használni kívánt **virtuálisgép-rendszerképet**. Ha Linux-rendszerképet választ, megjelenik egy lehetőség, amely lehetővé teszi a távoli asztali kapcsolat engedélyezését. Részletekért lásd: [Távoli asztali kapcsolat engedélyezése Linux](how-to-enable-remote-desktop-linux.md)rendszerhez.
    1. Kattintson a **Tovább** gombra.

        ![Virtuális gép specifikációinak megadása](../media/tutorial-setup-classroom-lab/select-vm-specifications.png)    
5. A **Hitelesítő adatok beállítása** oldalon adja meg az alapértelmezett hitelesítő adatokat a tesztkörnyezet összes virtuális gépéhez. 
    1. Adja meg a **felhasználónevet** a tesztkörnyezet összes virtuális gépéhez.
    2. Adja meg a felhasználó **jelszavát**. 

        > [!IMPORTANT]
        > Jegyezze fel a felhasználónevet és a jelszót, mert többször nem fognak megjelenni.
    3. Ha azt szeretné, hogy a diákok saját jelszavukat állítsanak be, tiltsa le **ugyanazt a jelszót az összes virtuális gép** beállításnál. Ez a lépés **nem kötelező**. 

        A tanár dönthet úgy, hogy ugyanazt a jelszót használja a laborban lévő összes virtuális géphez, vagy lehetővé teszi a tanulók számára a virtuális gépek jelszavának beállítását. Alapértelmezés szerint ez a beállítás az Ubuntu kivételével minden Windows-és Linux-lemezkép esetében engedélyezve van. Ha az **Ubuntu** virtuális gépet választja, akkor ez a beállítás le van tiltva, így a tanulóknak először be kell állítania egy jelszót, amikor első alkalommal jelentkeznek be.
    1. Kattintson a **Létrehozás** gombra. 

        ![Hitelesítő adatok beállítása](../media/tutorial-setup-classroom-lab/set-credentials.png)
6. A **Sablon konfigurálása** oldalon látható a tesztkörnyezet létrehozási folyamatának állapota. A sablon létrehozása a tesztkörnyezetben akár 20 percig is eltarthat. A tesztkörnyezet sablonja egy alapszintű virtuálisgép-rendszerkép, amelyből az összes felhasználó virtuális gépe létrejön. Úgy állítsa be a virtuálisgép-sablont, hogy az pontosan aszerint legyen konfigurálva, amit a tesztkörnyezet felhasználóinak biztosítani kíván.  

    ![Sablon konfigurálása](../media/tutorial-setup-classroom-lab/configure-template.png)
7. Miután a sablon konfigurálása befejeződött, az alábbi oldal jelenik meg: 

    ![Sablon konfigurálása oldal a folyamat befejeződése után](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)
8. Az oktatóanyag alábbi lépéseit nem kötelező végrehajtani: 
    2. A **Csatlakozás** gomb kiválasztásával csatlakozzon a virtuálisgép-sablonhoz. Linux-sablonos virtuális gép esetén válassza ki, hogy SSH vagy RDP használatával szeretne-e csatlakozni (ha az RDP engedélyezve van).
    1. A virtuális gép jelszavának alaphelyzetbe állításához válassza a **jelszó** alaphelyzetbe állítása lehetőséget. 
    1. Telepítsen és konfiguráljon szoftvert a virtuálisgép-sablonon. 
    1. **Állítsa le** a virtuális gépet.  
    1. Adja meg a sablon **leírását**.
9. Válassza ki a **Tovább** gombot a sablon oldalán. 
10. A **Sablon közzététele** oldalon tegye az alábbiakat. 
    1. A sablon azonnali közzétételéhez jelölje be a következő figyelmeztetés jelölőnégyzetét: *Megértettem, hogy közzététel után nem lehet módosítani a sablont. A folyamatot csak egyszer lehet elvégezni, és akár egy órát is igénybe vehet*, majd válassza ki a **Közzététel** gombot.  A sablon közzétételével a tesztkörnyezet felhasználói számára elérhető példányokat hozhat létre a virtuálisgép-sablonról.

        > [!WARNING]
        > Közzététel után a lépés nem vonható vissza. 
    2. Későbbi közzétételhez kattintson a **Mentés későbbre** gombra. A varázsló futtatása után közzéteheti a virtuálisgép-sablont. A varázsló befejeződése után történő konfigurálásával és közzétételével kapcsolatos részletekért tekintse meg a következő témakört: a konfigurálás és közzététel a varázsló befejezése után című rész, a sablon közzététele című [](how-to-manage-classroom-labs.md) szakasz, témakör.

        ![Sablon közzététele](../media/tutorial-setup-classroom-lab/publish-template.png)
11. Nyomon követheti, hogy **hol tart a sablon közzététele**. Ez a folyamat akár egy órát is igénybe vehet. 

    ![Sablon közzétételének folyamata](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. Miután a sablon közzététele sikeresen befejeződött, az alábbi oldalt fogja látni. Válassza a **Done** (Kész) lehetőséget.

    ![Sablon sikeres közzététele](../media/tutorial-setup-classroom-lab/publish-success.png)
1. A rendszer megjeleníti a tesztkörnyezet **irányítópultját**. 
    
    ![Az osztályterem-tesztkörnyezet irányítópultja](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)
4. Váltson a **Virtuális gépek** lapra, és ellenőrizze, hogy vannak-e ott **Nem hozzárendelt** állapotú virtuális gépek. Ezek a virtuális gépek még nincsenek diákokhoz rendelve. **Leállított** állapotban kell lenniük. Ezen a lapon indíthatja el a virtuális gépeket, csatlakozhat hozzájuk, leállíthatja, valamint törölheti őket. A virtuális gépeket elindíthatja ezen a lapon, vagy engedheti, hogy a diákjai indítsák el őket. 

    ![Leállított állapotban levő virtuális gépek](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

### <a name="vm-sizes"></a>A virtuális gépek mérete  

| Size | Processzormagok | RAM | Leírás | 
| ---- | ----- | --- | ----------- | 
| Kicsi | 2 | 3,5 GB | Ez a méret a legmegfelelőbb a parancssorhoz, a webböngésző megnyitásához, az alacsony forgalmú webkiszolgálók, a kis és közepes adatbázisok eléréséhez. |
| Közepes | 4 | 7 GB | Ez a méret a legmegfelelőbb a kapcsolatok adatbázisaihoz, a memóriában történő gyorsítótárazáshoz és az elemzésekhez. | 
| Közepes (beágyazott virtualizálás) | 4 | 16 GB | Ez a méret a legmegfelelőbb a kapcsolatok adatbázisaihoz, a memóriában történő gyorsítótárazáshoz és az elemzésekhez. Ez a méret támogatja a beágyazott virtualizálás szolgáltatást is. <p>Ezt a méretet olyan helyzetekben lehet használni, ahol minden tanulónak több virtuális gépre van szüksége. A tanárok beágyazott virtualizálás használatával állíthatnak be néhány kis méretű beágyazott virtuális gépet a virtuális gépen belül. </p> |
| Nagy | 8 | 32 GB | Ez a méret a gyorsabb processzorokat, nagyobb teljesítményű helyi lemezeket, nagyméretű adatbázisokat és nagyméretű memória-gyorsítótárat igénylő alkalmazások számára ajánlott. Ez a méret támogatja a beágyazott virtualizálás szolgáltatást is |  
| Kis GPU (vizualizáció) | 6 | 56 GB | Ez a méret a távoli vizualizációhoz, a folyamatos átvitelhez, a játékokhoz, a kódoláshoz, például az OpenGL és a DirectX keretrendszerekhez használható. | 
| Kis GPU (számítás) | 6 | 56 GB | Ez a méret a legjobb megoldás a nagy számítási igényű és hálózati igényű alkalmazások, például a mesterséges intelligencia és a Deep learning-alkalmazások számára. | 
| Közepes GPU (vizualizáció) | 12 | 112 GB | Ez a méret a távoli vizualizációhoz, a folyamatos átvitelhez, a játékokhoz, a kódoláshoz, például az OpenGL és a DirectX keretrendszerekhez használható. | 

## <a name="view-all-classroom-labs"></a>Az összes tantermi labor megtekintése
1. Navigáljon [Azure Lab Services portálra](https://labs.azure.com).
2. Válassza **a bejelentkezés**lehetőséget. Válasszon ki vagy adjon meg egy olyan **felhasználói azonosítót** , amely tagja a **labor Creator** szerepkörnek a labor-fiókban, majd írja be a jelszót. Az Azure Lab Services támogatja a szervezeti fiókok és a Microsoft-fiókok használatát is. 
3. Ellenőrizze, hogy megjelenik-e az összes labor a kiválasztott labor-fiókban. 

    ![Minden labor](../media/how-to-manage-classroom-labs/all-labs.png)
3. A felső legördülő listából válassza ki a másik Lab-fiókot. A Labs a kiválasztott labor-fiókban jelenik meg. 

## <a name="delete-a-classroom-lab"></a>Tantermi labor törlése
1. A labor csempén válassza a három pontot (...) a sarokban. 

    ![A tesztkörnyezet kiválasztása](../media/how-to-manage-classroom-labs/select-three-dots.png)
2. Válassza a **Törlés** elemet. 

    ![Törlés gomb](../media/how-to-manage-classroom-labs/delete-button.png)
3. A **labor törlése** párbeszédpanelen válassza a **Törlés**lehetőséget. 

    ![Törlés párbeszédpanel](../media/how-to-manage-classroom-labs/delete-lab-dialog-box.png)

## <a name="switch-to-another-classroom-lab"></a>Váltás másik osztályterem laborra
Ha át szeretne váltani egy másik tantermi laborra az aktuálisból, válassza ki a labor-fiók felső részén található Labs legördülő listáját.

![Válassza ki a labort a felül lévő legördülő listából.](../media/how-to-manage-classroom-labs/switch-lab.png)


## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

- [A labor tulajdonosaként hozzon létre és tegyen közzé sablonokat](how-to-create-manage-template.md)
- [Tesztkörnyezet tulajdonosaként konfigurálhatja és szabályozhatja a labor használatát](how-to-configure-student-usage.md)
- [Labor-felhasználóként az osztályterem Labs eléréséhez](how-to-use-classroom-lab.md)

