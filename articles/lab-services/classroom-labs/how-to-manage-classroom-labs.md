---
title: Tantermi laborok kezelése az Azure Lab Servicesben | Microsoft dokumentumok
description: Ismerje meg, hogyan hozhat létre és konfigurálhat tantermi labort, hogyan tekintheti meg az összes tantermi labort, hogyan oszthat meg regisztrációs hivatkozást egy tesztkörnyezet-felhasználóval, illetve hogyan törölhet egy tesztkörnyezetet.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/10/2020
ms.author: spelluru
ms.openlocfilehash: 46c53c99c12ade986ab913bf013b652a931a4d22
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257742"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Tantermi laborok kezelése az Azure Lab Servicesben 
Ez a cikk ismerteti, hogyan hozhat létre és törölhet egy tantermi labor. Azt is bemutatja, hogyan tekintheti meg az összes tantermi laborok egy laborfiókban. 

## <a name="prerequisites"></a>Előfeltételek
A **Tesztkörnyezet-létrehozó** szerepkörrel kell rendelkeznie a tesztkörnyezetfiókban ahhoz, hogy létrehozhasson egy osztályterem-tesztkörnyezetet. A tesztkörnyezetfiók létrehozásához használt fiók automatikusan hozzáadódik ehhez a szerepkörhöz. A tesztkörnyezet tulajdonosa a [felhasználó a Tesztkörnyezet-létrehozó szerepkörhöz történő hozzáadását](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role) ismertető cikkben leírt lépésekkel adhat hozzá további felhasználókat a Tesztkörnyezet-létrehozó szerepkörhöz.

## <a name="create-a-classroom-lab"></a>Osztályterem-tesztkörnyezet létrehozása

1. Lépjen az [Azure Lab Services weboldalára](https://labs.azure.com). Az Internet Explorer 11 még nem támogatott. 
2. Válassza a **Bejelentkezés** lehetőséget, és adja meg a hitelesítő adatait. Jelöljön ki vagy adjon meg egy **felhasználói azonosítót,** amely a **Labor készítői** szerepkör tagja a laborfiókban, és adja meg a jelszót. Az Azure Lab Services támogatja a szervezeti fiókok és a Microsoft-fiókok használatát is. 
3. Válassza az **Új tesztkörnyezet**lehetőséget. 
    
    ![Osztályterem-tesztkörnyezet létrehozása](../media/tutorial-setup-classroom-lab/new-lab-button.png)
3. Az **Új tesztkörnyezet** ablakban tegye a következőket: 
    1. Adjon egy **nevet** a tesztkörnyezetnek. 
    2. Válassza ki az osztályhoz szükséges **virtuális gépek méretét.** Az elérhető méretek listáját lásd a [Virtuálisgép méretek](#vm-sizes) szakaszban. 
    3. Válassza ki az osztályteremben található laborhoz használni kívánt **virtuálisgép-lemezképet.** Ha kiválaszt egy Linux-lemezképet, megjelenik egy lehetőség a távoli asztali kapcsolat engedélyezésére. További információt a [Távoli asztali kapcsolat engedélyezése Linuxra](how-to-enable-remote-desktop-linux.md)című témakörben talál.

        Ha a tesztkörnyezet-fiók tulajdonosi hitelesítő adataival jelentkezett be, megjelenik egy lehetőség, amely további képeket engedélyez a tesztkörnyezetszámára. További információ: [Képek engedélyezése a labor létrehozásakor](specify-marketplace-images.md#enable-images-at-the-time-of-lab-creation).
    4. Tekintse át az oldalon megjelenő **órateljes árat.** 
    6. Kattintson a **Mentés** gombra.

        ![Új laborablak](../media/tutorial-setup-classroom-lab/new-lab-window.png)

        > [!NOTE]
        > Megjelenik egy lehetőség, hogy válassza ki a helyet a labor, ha a tesztkörnyezet fiók úgy volt beállítva, hogy [a tesztkörnyezet létrehozója, hogy válasszon labor hely](allow-lab-creator-pick-lab-location.md) beállítást. 
4. A **virtuális gép hitelesítő adatai** lapon adja meg az alapértelmezett hitelesítő adatokat a laborban lévő összes virtuális géphez.
    1. Adja meg a **felhasználónevet** a tesztkörnyezet összes virtuális gépéhez.
    2. Adja meg a felhasználó **jelszavát**. 

        > [!IMPORTANT]
        > Jegyezze fel a felhasználónevet és a jelszót, mert többször nem fognak megjelenni.
    3. Tiltsa **le: Használja ugyanazt a jelszót az összes virtuális géphez,** ha azt szeretné, hogy a diákok saját jelszavakat állítsanak be. Ez a lépés **nem kötelező**. 

        A tanár dönthet úgy, hogy ugyanazt a jelszót használja a laborban lévő összes virtuális géphez, vagy engedélyezheti a diákoknak, hogy jelszavakat állítsanak be a virtuális gépeikhez. Alapértelmezés szerint ez a beállítás engedélyezve van az összes Windows és Linux rendszerképeken, kivéve az Ubuntut. Ha az **Ubuntu** VM lehetőséget választja, ez a beállítás le van tiltva, így a diákoknak be kell állítaniuk egy jelszót, amikor először jelentkeznek be.  

        ![Új laborablak](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)
    4. Ezután válassza a **Tovább** lehetőséget a **Virtuálisgép hitelesítő adatai** lapon. 
5. A **Labor házirendek** lapon tegye a következő lépéseket:
    1. Adja meg az egyes felhasználók számára kiosztott órák számát (**kvóta az egyes felhasználókesetében)** a tesztkörnyezet ütemezett idején kívül. 
    2. A **virtuális gépek automatikus leállítása** beállítás, adja meg, hogy a virtuális gép automatikusan leáll-e, amikor a felhasználó bontja a kapcsolatot. Azt is megadhatja, hogy a virtuális gép mennyi ideig várjon a felhasználó újracsatlakozására, mielőtt automatikusan leállna.. További információt a [Virtuális gépek automatikus leállításának engedélyezése a kapcsolat bontásakor](how-to-enable-shutdown-disconnect.md)című témakörben talál.
    3. Ezután válassza a **Befejezés gombot.** 

        ![Kvóta minden felhasználószámára](../media/tutorial-setup-classroom-lab/quota-for-each-user.png)
    
5. Meg kell jelennie a következő képernyőn, amely a sablon virtuális gép létrehozásának állapotát mutatja. A sablon létrehozása a tesztkörnyezetben akár 20 percig is eltarthat. 

    ![A sablon virtuális gép létrehozásának állapota](../media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. A **Sablon** lapon tegye a következő lépéseket: Ezek a lépések **nem kötelezőek** az oktatóanyag számára.

    2. A **Csatlakozás** gomb kiválasztásával csatlakozzon a virtuálisgép-sablonhoz. Ha ez egy Linux sablon virtuális gép, akkor válassza ki, hogy szeretne csatlakozni ssh vagy RDP (ha RDP engedélyezve van).
    1. Válassza **a Jelszó alaphelyzetbe állításához** válassza a virtuális gép jelszavának alaphelyzetbe állításához. 
    1. Telepítsen és konfiguráljon szoftvert a virtuálisgép-sablonon. 
    1. **Állítsa le** a virtuális gépet.  
    1. Adja meg a sablon **leírását**.
10. A **Sablon** lapon válassza a **Közzététel** lehetőséget az eszköztáron. 

    ![Sablon közzététele gomb](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Közzététel után a lépés nem vonható vissza. 
8. A **Közzétételi sablon** lapon adja meg a laborban létrehozni kívánt virtuális gépek számát, majd válassza a **Közzététel**lehetőséget. 

    ![Közzétételi sablon – virtuális gépek száma](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. A sablon **közzétételi állapota** a lapon. Ez a folyamat akár egy órát is igénybe vehet. 

    ![Sablon közzétételének folyamata](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. Váltson a **Virtuális gépek készlet** lapra a bal oldali menü Virtuális gépek parancsával vagy a Virtuális gépek csempéjének kiválasztásával. Ellenőrizze, hogy olyan virtuális gépek jelennek-e meg, amelyek **hozzárendelés nélküli** állapotban vannak. Ezek a virtuális gépek még nincsenek diákokhoz rendelve. **Leállított** állapotban kell lenniük. Ezen a lapon indíthatja el a virtuális gépeket, csatlakozhat hozzájuk, leállíthatja, valamint törölheti őket. A virtuális gépeket elindíthatja ezen a lapon, vagy engedheti, hogy a diákjai indítsák el őket. 

    ![Leállított állapotban levő virtuális gépek](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

    Ezen az oldalon a következő feladatokat kell elvégeznie (ne tegye meg ezeket a lépéseket az oktatóanyaghoz. Ezek a lépések csak tájékoztatásul szolgálnak.): 
    
    1. A labor kapacitásának (a tesztkörnyezetben lévő virtuális gépek száma) módosításához válassza az eszköztár Laborkapacitás a választókörnyezet **kapacitása** lehetőséget.
    2. Ha egyszerre szeretné elindítani az összes virtuális gépet, válassza az **összes indítása** lehetőséget az eszköztáron. 
    3. Egy adott virtuális gép indításához jelölje ki a lefelé mutató nyilat az **Állapot**területen, majd kattintson a **Start**gombra. Virtuális gép is elindíthatja, ha kiválaszt egy virtuális gép az első oszlopban, majd az eszköztár **Indítás** parancsa.                

### <a name="vm-sizes"></a>A virtuális gépek mérete  

| Méret | Cores | RAM | Leírás | 
| ---- | ----- | --- | ----------- | 
| Kicsi | 2 | 3,5 GB | Ez a méret a legalkalmasabb a parancssori, webböngésző, alacsony forgalmú webszerverek, kis és közepes adatbázisok megnyitásához. |
| Közepes | 4 | 7 GB | Ez a méret a legalkalmasabb relációs adatbázisokhoz, memórián belüli gyorsítótárazáshoz és elemzéshez | 
| Közepes (beágyazott virtualizáció) | 4 | 16 GB | Ez a méret a legalkalmasabb relációs adatbázisokhoz, memórián belüli gyorsítótárazáshoz és elemzésekhez. Ez a méret támogatja a beágyazott virtualizációt is. <p>Ez a méret olyan esetekben használható, ahol minden tanulónak több virtuális gépre van szüksége. A tanárok egymásba ágyazott virtualizációval állíthatnak be néhány kis méretű beágyazott virtuális gépet a virtuális gépen belül. </p> |
| Nagy | 8 | 32 GB | Ez a méret a legalkalmasabb olyan alkalmazásokhoz, amelyeknek gyorsabb processzorokra, jobb helyi lemezteljesítményre, nagy adatbázisokra, nagy memória-gyorsítótárakra van szükségük. Ez a méret támogatja a beágyazott virtualizációt is |  
| Kis GPU (képi megjelenítés) | 6 | 56 GB | Ez a méret a legalkalmasabb távoli vizualizációra, streamelésre, játékra, kódolásra olyan keretrendszerek használatával, mint az OpenGL és a DirectX. | 
| Kis GPU (számítás) | 6 | 56 GB | Ez a méret a legalkalmasabb olyan nagy számítási igényű és hálózatigényes alkalmazásokhoz, mint a mesterséges intelligencia és a mélytanulási alkalmazások. | 
| Közepes GPU (képi megjelenítés) | 12 | 112 GB | Ez a méret a legalkalmasabb távoli vizualizációra, streamelésre, játékra, kódolásra olyan keretrendszerek használatával, mint az OpenGL és a DirectX. | 

> [!NOTE]
> Az Azure Lab Services automatikusan telepíti és konfigurálja a szükséges GPU-illesztőprogramokat, amikor gpu-lemezképekkel rendelkező tesztkörnyezetet hoz létre.  

## <a name="view-all-classroom-labs"></a>Az összes tantermi labor megtekintése
1. Nyissa meg az [Azure Lab Services portált.](https://labs.azure.com)
2. Válassza a **Bejelentkezés** lehetőséget. Jelöljön ki vagy adjon meg egy **felhasználói azonosítót,** amely a **Labor készítői** szerepkör tagja a laborfiókban, és adja meg a jelszót. Az Azure Lab Services támogatja a szervezeti fiókok és a Microsoft-fiókok használatát is. 
3. Erősítse meg, hogy a kiválasztott laborfiók összes laborja látható. A labor csempéjén láthatja a tesztkörnyezetben lévő virtuális gépek számát és az egyes felhasználók kvótáját (az ütemezett időn kívül).

    ![Minden labor](../media/how-to-manage-classroom-labs/all-labs.png)
3. A felső legördülő lista segítségével válasszon ki egy másik tesztkörnyezet-fiókot. Laborokat lát a kiválasztott laborfiókban. 

## <a name="delete-a-classroom-lab"></a>Tantermi labor törlése
1. A labor csempéjén jelöljön ki három pontot (...) a sarokban, majd kattintson a **Törlés gombra.** 

    ![Törlés gomb](../media/how-to-manage-classroom-labs/delete-button.png)
3. A **Tesztkörnyezet törlése** párbeszédpanelen válassza a **Törlés** lehetőséget a törlés folytatásához. 

## <a name="switch-to-another-classroom-lab"></a>Váltás másik tantermi laborra
Ha az aktuális tantermi laborra szeretne váltani, válassza ki a laborok legördülő listáját a tesztkörnyezet-fiók tetején.

![Válassza ki a labort a felső legördülő listából](../media/how-to-manage-classroom-labs/switch-lab.png)

Új tesztkörnyezetet is létrehozhat az **új tesztkörnyezet** használatával ebben a legördülő listában. 

> [!NOTE]
> Az.LabServices PowerShell-modul (előzetes verzió) is használhatja a laborok kezeléséhez. További információt az [Az.LabServices kezdőlapján talál a GitHubon.](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library)

Ha másik tesztkörnyezet-fiókra szeretne váltani, válassza ki a laborfiók melletti legördülő menüt, és válassza ki a másik tesztkörnyezet-fiókot. 

## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

- [Labortulajdonosként sablonok beállítása és közzététele](how-to-create-manage-template.md)
- [Labortulajdonosként konfigurálja és szabályozza a tesztkörnyezet használatát](how-to-configure-student-usage.md)
- [Laborfelhasználóként tantermi laborok elérése](how-to-use-classroom-lab.md)

