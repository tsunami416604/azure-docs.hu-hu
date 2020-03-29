---
title: Az alkalmazás tesztelése az Azure-ban | Microsoft dokumentumok
description: Megtudhatja, hogyan hozhat létre fájlmegosztást egy laborban, és csatlakoztathatja azt a helyi gépen és egy virtuális gépen a laborban, majd telepítse az asztali/webalkalmazásokat a fájlmegosztásra, és tesztelje őket.
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
ms.openlocfilehash: f8c57b9e1fabbd04a7d9c92484b0f52f074c2577
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "65872405"
---
# <a name="test-your-app-in-azure"></a>Alkalmazás tesztelése az Azure-ban 
Ez a cikk az alkalmazás Azure-beli DevTest Labs használatával történő tesztelésének lépéseit ismerteti. Először állítson be egy fájlmegosztást egy tesztkörnyezetben, és csatlakoztassa meghajtóként a helyi fejlesztői gépen és egy virtuális gépen egy tesztkörnyezetben. Ezután a Visual Studio 2019 segítségével telepítheti az alkalmazást a fájlmegosztásra, így futtathatja az alkalmazást a virtuális gépen a laborban.  

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek 
1. [Hozzon létre egy Azure-előfizetést,](https://azure.microsoft.com/free/) ha még nem rendelkezik ilyen, és jelentkezzen be az [Azure Portalon.](https://portal.azure.com)
2. Ebben a [cikkben](devtest-lab-create-lab.md) található utasításokat követve hozzon létre egy tesztkörnyezetet az Azure DevTest Labs használatával. Rögzítse a tesztkörnyezetet az irányítópulton, hogy a következő bejelentkezéskor könnyen megtalálhassa. Az Azure DevTest Labs lehetővé teszi, hogy gyorsan hozzon létre erőforrásokat az Azure-on belül a hulladék minimalizálásával és a költségek szabályozásával. Ha többet szeretne megtudni a DevTest Labs-ről, olvassa [el az áttekintést.](devtest-lab-overview.md) 
3. Hozzon létre egy Azure Storage-fiókot a labor erőforráscsoportban a [tárfiók létrehozása](../storage/common/storage-create-storage-account.md) cikkben található utasításokat követve. A **Tárfiók létrehozása** lapon válassza a **Meglévő használata** **erőforráscsoporthoz**lehetőséget, és válassza ki a **labor erőforráscsoportját.** 
4. Hozzon létre egy fájlmegosztást az Azure storage-ban az [Azure Files-ban a Fájlmegosztás létrehozása](../storage/files/storage-how-to-create-file-share.md) című cikkutasításait követve. 

## <a name="mount-the-file-share-on-your-local-machine"></a>A fájlmegosztás csatlakoztatása a helyi számítógépen
1. A helyi számítógépen használja a [parancsfájlt a Persisting Azure fájlmegosztási hitelesítő adatok a Windows](../storage/files/storage-how-to-use-files-windows.md#persisting-azure-file-share-credentials-in-windows) [szakaszban Az Azure-fájlmegosztás használata a Windows cikk.](../storage/files/storage-how-to-use-files-windows.md) 
2. Ezután `net use` a paranccsal csatlakoztathatja a fájlmegosztást a számítógéphez. Itt van a minta parancs: Adja meg az Azure storage nevét és a fájlmegosztás nevét a parancs futtatása előtt. 

    `net use Z: \\<YOUR AZURE STORAGE NAME>.file.core.windows.net\<YOUR FILE SHARE NAME> /persistent:yes`

## <a name="create-a-vm-in-the-lab"></a>Virtuális gép létrehozása a tesztkörnyezetben
1. A **Fájlmegosztás** lapon válassza ki az **erőforráscsoportot** a képernyő menüjének menüjében. Megjelenik az **Erőforráscsoport** lap. 
    
    ![Erőforráscsoport kiválasztása a zsemlemorzsa menüből](media/test-app-in-azure/select-resource-group-bread-crump.png)
2. Az **Erőforráscsoport** lapon válassza ki a DevTest Labs ben létrehozott **tesztkörnyezetet.**

    ![A tesztkörnyezet kiválasztása](media/test-app-in-azure/select-devtest-lab-in-resource-group.png)
3. A **labor DevTest** lab lapján válassza a **+ Hozzáadás** lehetőséget az eszköztáron. 

    ![A hozzáadás gomb a laborhoz](media/test-app-in-azure/add-button-in-lab.png)
4. A **Válasszon alaplapot,** keresse meg a **smalldisk**elemet, és válassza a **[smalldisk] Windows Server 2016 Data Center lehetőséget.** 

    ![Kis méretű lemez kiválasztása Windows server](media/test-app-in-azure/choose-small-disk-windows-server.png)
5. A **Virtuálisgép** lapon adja meg a **virtuális gép nevét**, **felhasználónevét**, **jelszavát,** majd válassza a **Létrehozás lehetőséget.**    
    
    ![Virtuális gép lap létrehozása](media/test-app-in-azure/create-virtual-machine-page.png)    

## <a name="mount-the-file-share-on-your-vm"></a>A fájlmegosztás csatlakoztatása a virtuális gépre
1. A virtuális gép sikeres létrehozása után válassza ki a **virtuális gépet** a listából.    

    ![Válassza ki a labor virtuális gép](media/test-app-in-azure/select-lab-vm.png)
2. Válassza a **Csatlakozás** lehetőséget az eszköztáron a virtuális géphez való csatlakozáshoz. 
3. [Telepítse az Azure PowerShellt.](/powershell/azure/install-az-ps)
4. Kövesse a Fájlmegosztás csatlakoztatása című szakasz utasításait. 

## <a name="publish-your-app-from-visual-studio"></a>Az alkalmazás közzététele a Visual Studióból
Ebben a szakaszban közzéteheti az alkalmazást a Visual Studióból egy teszt virtuális gépnek a felhőben.

1. Hozzon létre egy asztali/webes alkalmazást a Visual Studio 2019 használatával.
2. Hozza létre az alkalmazást a Build lehetőséggel.
3. Az alkalmazás közzétételéhez kattintson a jobb gombbal a projektre a **Megoldáskezelőben,** és válassza a **Közzététel parancsot.** 
4. A **Közzététel varázslóban**adja meg a fájlmegosztáshoz leképezett **meghajtót.**

    **Asztali alkalmazás:**

    ![Asztali alkalmazás](media/test-app-in-azure/desktop-app.png)

    **Webalkalmazás:**

    ![Webalkalmazás](media/test-app-in-azure/web-app.png)

1. A **közzétételi** munkafolyamat befejezéséhez válassza a Tovább gombot, majd a **Befejezés**lehetőséget. A varázsló lépéseinek befejeztével a Visual Studio létrehozza az alkalmazást, és közzéteszi azt a fájlmegosztáson. 


## <a name="test-the-app-on-your-test-vm-in-the-lab"></a>Az alkalmazás tesztelése a tesztvirtuális gépen a laborban

1. Keresse meg a virtuális gép lapját a virtuális gép a laborban. 
2. Válassza a **Start** az eszköztáron a virtuális gép elindításához, ha leállított állapotban van. Beállíthatja az automatikus indítási és automatikus leállítási szabályzatok a virtuális gép, hogy ne induljon el és leáll minden alkalommal. 
3. Kattintson a **Csatlakozás** gombra.

    ![Virtuális gép lapja](media/test-app-in-azure/virtual-machine-page.png)
4. A virtuális gépen indítsa el a **Fájlkezelőt,** és válassza a **Számítógép lehetőséget** a fájlmegosztás megkereséséhez.

    ![Megosztás keresése a virtuális gépen](media/test-app-in-azure/find-share-on-vm.png)

    > [!NOTE]
    > Bármilyen okból, ha nem találja a fájlmegosztást a virtuális gépen vagy a helyi számítógépen, a `net use` parancs futtatásával újracsatlakoztathatja azt. A parancs `net use` a **Fájlmegosztás** **csatlakoztatása** varázsló az Azure Portalon.
1. Nyissa meg a fájlmegosztást, és ellenőrizze, hogy a Visual Studio-ból telepített alkalmazás látható-e. 

    ![Megosztás megnyitása a virtuális gépen](media/test-app-in-azure/open-file-share.png)

    Most már elérheti és tesztelheti az alkalmazást az Azure-ban létrehozott tesztvirtuális gépen belül.

## <a name="next-steps"></a>További lépések
Az alábbi cikkekből megtudhatja, hogyan használhatja a virtuális gépeket egy laborban. 

- [Virtuális gép hozzáadása laborhoz](devtest-lab-add-vm.md)
- [Labor virtuális gép újraindítása](devtest-lab-restart-vm.md)
- [Labor virtuális gép átméretezése](devtest-lab-resize-vm.md)
