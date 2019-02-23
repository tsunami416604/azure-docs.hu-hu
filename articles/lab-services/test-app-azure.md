---
title: Az Azure-ban az alkalmazás tesztelése |} A Microsoft Docs
description: Ismerje meg, hogyan hozzon létre fájlmegosztást egy tesztkörnyezetben és csatlakoztathatom azokat a helyi gépen és a egy virtuális gépet a tesztkörnyezetben, asztali és webalkalmazásokat a fájlmegosztáshoz telepíteni és tesztelje le azokat.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2018
ms.author: spelluru
ms.openlocfilehash: 9ca1c4a1ed1841f82bc386cff48315dd15ef91bb
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56730409"
---
# <a name="test-your-app-in-azure"></a>Alkalmazás tesztelése az Azure-ban 
Ez a cikk ismerteti az Azure-ban az alkalmazás tesztelése a DevTest Labs használatával. Először egy fájlmegosztást a tesztkörnyezet beállításához, és csatlakoztassa meghajtóként helyi fejlesztői gépen és a egy laborban lévő virtuális Gépet. Ezután használja a Visual Studio 2017 helyezze üzembe az alkalmazást a fájlmegosztáshoz, hogy a lab-ben a virtuális gépen futtathatja az alkalmazást.  

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek 
1. [Azure-előfizetés létrehozása](https://azure.microsoft.com/free/) Ha nincs már rendelkezik ilyennel, és jelentkezzen be a [az Azure portal](https://portal.azure.com).
2. Kövesse az utasításokat [Ez a cikk](devtest-lab-create-lab.md) használata az Azure DevTest Labs labor létrehozásához. A labor létrehozása az irányítópulton rögzítheti, így megtalálhatja, amikor legközelebb bejelentkezik. Az Azure DevTest Labs lehetővé teszi, hogy gyorsan hozzon létre Azure-erőforrások minimálisra csökkentheti a veszteséget és a költségek ellenőrzés alatt tartásával. DevTest Labs kapcsolatos további információkért lásd: [áttekintése](devtest-lab-overview.md). 
3. Hozzon létre egy Azure Storage-fiókot a labor erőforráscsoportban található utasításokat követve a [hozzon létre egy tárfiókot](../storage/common/storage-create-storage-account.md) cikk. A a **storage-fiók létrehozása** lapon jelölje be **meglévő használata** a **erőforráscsoport**, és válassza ki a **tesztkörnyezet erőforráscsoport**. 
4. Fájlmegosztás létrehozása az Azure storage-ban található utasításokat követve a [fájlmegosztás létrehozása az Azure Files](../storage/files/storage-how-to-create-file-share.md) cikk. 

## <a name="mount-the-file-share-on-your-local-machine"></a>Csatlakoztassa a fájlmegosztást a helyi gépen
1. A parancsfájlt használja a helyi gépen [megőrzése Azure fájl megosztás hitelesítő adatai a Windows](../storage/files/storage-how-to-use-files-windows.md#persisting-azure-file-share-credentials-in-windows) szakaszában [használata a Windows Azure-fájlmegosztások](../storage/files/storage-how-to-use-files-windows.md) cikk. 
2. Ezután használja `net use` parancsot a fájlmegosztás csatlakoztatása a gépen. A mintául szolgáló parancs a következő: Az Azure storage nevét és a fájlmegosztás nevét adja meg a parancs futtatása előtt. 

    `net use Z: \\<YOUR AZURE STORAGE NAME>.file.core.windows.net\<YOUR FILE SHARE NAME> /persistent:yes`

## <a name="create-a-vm-in-the-lab"></a>Virtuális gép létrehozása a tesztkörnyezetben
1. Az a **fájlmegosztás** lapon válassza ki a **erőforráscsoport** tetején lévő navigációs menüben található. Megjelenik a **erőforráscsoport** lapot. 
    
    ![Válassza ki az erőforráscsoportot a navigációs menüben](media/test-app-in-azure/select-resource-group-bread-crump.png)
2. Az a **erőforráscsoport** lapon válassza ki a **labor** DevTest Labs szolgáltatásban létrehozott.

    ![A tesztkörnyezet kiválasztása](media/test-app-in-azure/select-devtest-lab-in-resource-group.png)
3. Az a **fejlesztési és tesztelési labor** a tesztkörnyezethez, válassza a lap **+ Hozzáadás** az eszköztáron. 

    ![A labor gomb hozzáadása](media/test-app-in-azure/add-button-in-lab.png)
4. Az a **vyberte bázi** lapon, keressen rá a **smalldisk**, és válassza ki **[smalldisk] a Windows Server 2016 adatközpont**. 

    ![Válassza ki a Windows server kisebb lemezt](media/test-app-in-azure/choose-small-disk-windows-server.png)
5. Az a **virtuális gép** adja meg azokat **virtuális gép neve**, **felhasználónév**, **jelszó**, és válassza ki **létrehozása** .    
    
    ![Virtuális gép létrehozása](media/test-app-in-azure/create-virtual-machine-page.png)    

## <a name="mount-the-file-share-on-your-vm"></a>Fájlmegosztás csatlakoztatása a virtuális Gépen
1. A virtuális gép sikeres létrehozása után válassza ki a **virtuális gép** a listából.    

    ![Válassza ki a labor virtuális gép](media/test-app-in-azure/select-lab-vm.png)
2. Válassza ki **Connect** az eszköztáron a virtuális Géphez való csatlakozáshoz. 
3. [Telepítse az Azure PowerShellt](/powershell/azure/install-az-ps).
4. Kövesse az utasításokat a csatlakoztatási a fájl megosztási szakaszban. 

## <a name="publish-your-app-from-visual-studio"></a>Tegye közzé az alkalmazást a Visual Studióból
Ebben a szakaszban az alkalmazást a közzététel a Visual Studióból a tesztelési virtuális gép a felhőben.

1. Asztali/webalkalmazás létrehozása a Visual Studio 2017 használatával.
2. Az alkalmazás elkészítésére.
3. Az alkalmazás közzétételéhez kattintson a jobb gombbal a projektre a **Megoldáskezelőben**, és válassza ki **közzététel**. 
4. Az a **közzétételi varázsló**, adja meg a **meghajtó** , hogy a fájlmegosztás van leképezve.

    **Asztali alkalmazás:**

    ![Asztali alkalmazás](media/test-app-in-azure/desktop-app.png)

    **Webová aplikace:**

    ![Webalkalmazás](media/test-app-in-azure/web-app.png)

1. Válassza ki **tovább** a publish-munkafolyamatot, majd válassza ki **Befejezés**. Ha befejezte a varázsló lépéseit, a Visual Studio létrehozza az alkalmazást, és közzéteszi azt a fájlmegosztást. 


## <a name="test-the-app-on-your-test-vm-in-the-lab"></a>A lab-ben a teszteléshez használt virtuális gép az alkalmazás tesztelése

1. Lépjen a virtuális gép a lab-ben a virtuális gép oldalára. 
2. Válassza ki **Start** gombra az eszköztárban, indítsa el a virtuális Gépet, ha a leállt állapotban. Beállíthat automatikus indítási és automatikus leállítási házirendek, a virtuális gép indítása és leállítása minden alkalommal, amikor elkerülése érdekében. 
3. Kattintson a **Csatlakozás** gombra.

    ![Virtuális gép](media/test-app-in-azure/virtual-machine-page.png)
4. Indítsa el a virtuális gépben **fájlkezelő**, és válassza ki **Ez a gép** a fájlmegosztás található.

    ![Keresse meg a fájlmegosztás a virtuális Gépen](media/test-app-in-azure/find-share-on-vm.png)

    > [!NOTE]
    > Bármilyen okból, ha Ön nem található a fájlmegosztás a virtuális gépen vagy a helyi gépen is újracsatlakoztatása, futtassa a `net use` parancsot. Annak a `net use` parancs a **Connect** varázslóban a **fájlmegosztás** az Azure Portalon.
1. Nyissa meg a fájlmegosztás, és ellenőrizze, hogy látja-e a Visual Studióból üzembe helyezte az alkalmazást. 

    ![Megosztás megnyitása a virtuális Gépen](media/test-app-in-azure/open-file-share.png)

    Most már elérheti, és tesztelje alkalmazását a teszt virtuális gép Azure-ban létrehozott belül.

## <a name="next-steps"></a>További lépések
Tekintse meg az alábbi cikkekből tudhat meg a virtuális gépek használata tesztkörnyezetben. 

- [Virtuális gép hozzáadása laborhoz](devtest-lab-add-vm.md)
- [A labor virtuális gép újraindítása](devtest-lab-restart-vm.md)
- [A labor virtuális gép átméretezése](devtest-lab-resize-vm.md)
