---
title: Azure-fájlmegosztások felügyelete az Azure Storage Explorer használatával
description: Itt megismerheti, hogyan kezelheti az Azure Files szolgáltatást az Azure Storage Explorer használatával.
services: storage
documentationcenter: ''
author: wmgries
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/27/2018
ms.author: wgries
ms.openlocfilehash: 0c16d3b0ef0b178f99eaafecd74eabb00cca5af9
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2018
---
# <a name="managing-azure-file-shares-with-azure-storage-explorer"></a>Azure-fájlmegosztások felügyelete az Azure Storage Explorer használatával 
Az [Azure Files](storage-files-introduction.md) a Microsoft könnyen használható felhőalapú fájlrendszere. Ez az útmutató az Azure-fájlmegosztások az [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) segítségével való használatának alapvető lépéseit mutatja be. Az Azure Storage Explorer a Windows, macOS és Linux rendszereken elérhető, az Azure-fájlmegosztások és más tárolási erőforrások felügyeletére szolgáló népszerű ügyféleszköz.

A rövid útmutatóhoz az Azure Storage Explorer telepítése szükséges. Ha még telepítenie kell, akkor az [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) oldalról töltheti le.

Ebben a cikkben az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Erőforráscsoport és tárfiók létrehozása
> * Azure-fájlmegosztás létrehozása 
> * Könyvtár létrehozása
> * Fájl feltöltése
> * Fájl letöltése
> * Megosztási pillanatkép létrehozása és használata

Ha nem rendelkezik Azure-előfizetéssel, első lépésként létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-storage-account"></a>Create a storage account
Az Azure Storage Explorer nem képes új erőforrások létrehozására, ezért a bemutatóhoz az [Azure Portallal](https://portal.azure.com/) hozza létre a tárfiókot. 

[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]

## <a name="connecting-azure-storage-explorer-to-azure-resources"></a>Az Azure Storage Explorer csatlakoztatása Azure-erőforrásokhoz
Első indításkor megjelenik a **Microsoft Azure Storage Explorer – Csatlakozás** ablak. Az Azure Storage Explorer számos különféle módot kínál a tárfiókokhoz való csatlakozáshoz: 

- **Bejelentkezés az Azure-fiókon keresztül**: Lehetővé teszi, hogy a cége vagy Microsoft-fiókja felhasználói hitelesítő adataival jelentkezzen be. 
- **Csatlakozás megadott tárfiókhoz kapcsolati karakterlánccal vagy SAS-jogkivonattal**: A kapcsolati karakterláncok speciális karakterláncok, amelyek tartalmazzák a tárfiók nevét és a tárfiók kulcsát/SAS-jogkivonatát, amellyel az Azure Storage Explorer közvetlenül hozzáfér a tárfiókhoz (ahelyett, hogy egyszerűen az Azure-fiókban lévő összes tárfiókot látná). További információ a kapcsolati karakterláncokról: [Az Azure Storage kapcsolati karakterláncok konfigurálása](../common/storage-configure-connection-string.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
- **Csatlakozás adott tárfiókhoz a tárfiók nevével és kulcsával**: A tárfiók nevével és kulcsával csatlakozhat az Azure Storage-hoz.

Ebben a rövid útmutatóban jelentkezzen be az Azure-fiókjával. Válassza az **Azure-fiók hozzáadása** lehetőséget, majd kattintson a **Bejelentkezés** gombra. A képernyőn megjelenő utasításokat követve jelentkezzen be Azure-fiókjába.

![Microsoft Azure Storage Explorer – Csatlakozás ablak](./media/storage-how-to-use-files-storage-explorer/connect-to-azure-storage-1.png)

### <a name="create-a-file-share"></a>Fájlmegosztás létrehozása
Az első Azure-fájlmegosztás létrehozása a *storageacct<random number>* tárfiókban:

1. Bontsa ki a létrehozott tárfiókot.
2. Kattintson a jobb gombbal a **Fájlmegosztások** elemre, majd válassza a **Fájlmegosztás létrehozása** lehetőséget.  
    ![A fájlmegosztások mappa és a környezet helyi menüjének képernyőképe](media/storage-how-to-use-files-storage-explorer/create-file-share-1.png)

3. Írja be a *myshare* nevet a fájlmegosztáshoz, és nyomja le az **Enter** billentyűt.

> [!Important]  
> A fájlmegosztások neve kisbetűket, számokat és kötőjeleket tartalmazhat, de nem kezdődhet kötőjellel. A fájlmegosztások és fájlok elnevezésére vonatkozó információkért lásd: [Naming and Referencing Shares, Directories, Files, and Metadata](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata) (Megosztások, könyvtárak, fájlok és metaadatok elnevezése és hivatkozása).

Ha a fájlmegosztás létrejött, a jobb oldali panelen megnyílik a fájlmegosztás lapja. 

## <a name="manipulating-the-contents-of-the-azure-file-share"></a>Az Azure-fájlmegosztás tartalmának módosítása
Most, hogy létrehozott egy Azure-fájlmegosztást, csatlakoztathatja azt az SMB szolgáltatással [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) vagy [macOS](storage-how-to-use-files-mac.md) rendszeren. Másik megoldásként az Azure Portallal is módosíthatja az Azure-fájlmegosztást. Az Azure Portalon keresztül intézett összes kérelem a fájl REST API-jával történik, amely lehetővé teszi a fájlok és könyvtárak létrehozását, módosítását és törlését SMB-hozzáférés nélküli ügyfeleken.

### <a name="create-a-directory"></a>Könyvtár létrehozása
Egy könyvtár hozzáadása hierarchikus struktúrát biztosít a fájlmegosztás felügyeletéhez. Több szintet is létrehozhat, de az alkönyvtárak létrehozása előtt mindig győződjön meg arról, hogy az összes szülőkönyvtár létezik. A myDirectory/mySubDirectory elérési út esetében például először a *myDirectory* könyvtárat kell létrehozni, majd utána a *mySubDirectory* alkönyvtárat. 

1. A fájlmegosztás lapjának felső menüjében kattintson az **+ Új mappa** gombra. Megnyílik az **Új könyvtár létrehozása** oldal.
    ![Képernyőkép a környezetében lévő új mappa gombról](media/storage-how-to-use-files-storage-explorer/create-directory-1.png)

2. Írja be a *myDirectory* nevet, majd kattintson az **OK** gombra. 

A *myDirectory* könyvtár a *myshare* fájlmegosztás lapján fog szerepelni.

### <a name="upload-a-file"></a>Fájl feltöltése 
Töltsön fel egy fájlt a helyi gépről a fájlmegosztás új könyvtárába. Feltölthet egy teljes mappát, de akár egyetlen fájlt is.

1. A felső menüben válassza a **Feltöltés** elemet. Ez lehetővé teszi egy mappa vagy fájl feltöltését.

2. Válassza a **Fájl feltöltése** elemet, majd válasszon egy feltölteni kívánt fájlt a helyi gépről.

3. A **Feltöltés könyvtárba** mezőbe írja be a *myDirectory* nevet, majd kattintson a **Feltöltés** gombra. 

Amikor végzett, a fájlnak meg kell jelennie a **myDirectory** oldalon lévő listában.

### <a name="download-a-file"></a>Fájl letöltése
Egy fájlra a jobb gombbal kattintva, majd a **Letöltés** parancsot kiválasztva töltheti le a fájlmegosztásban lévő fájl másolatát. Válassza ki, hová szeretné helyezni a fájlt a helyi gépen, majd kattintson a **Mentés** gombra.

Az ablak alján lévő **Tevékenységek** panelen láthatja a letöltés állapotát.

## <a name="create-and-modify-share-snapshots"></a>Megosztási pillanatképek létrehozása és módosítása
A pillanatképek megőrzik az Azure-fájlmegosztások adott időpontban látható másolatát. A fájlmegosztások pillanatképei hasonlók az esetleg már ismert más technológiákhoz, például a következőkhöz:
- [Kötet árnyékmásolata szolgáltatás (VSS)](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee923636) Windows fájlrendszerekhez (például NTFS és ReFS).
- [Logikaikötet-kezelő (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) pillanatképek Linux rendszerekhez
- [Apple fájlrendszer (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) pillanatképek macOS rendszerhez. 

Megosztási pillanatkép létrehozása:

1. Nyissa meg a *myshare* fájlmegosztás lapját.
2. A lap tetején található menüben kattintson a **Pillanatkép készítése** elemre (ez az Azure Storage Explorer ablakméreteitől függően egy **... Továbbiak** elem mögött lehet elrejtve).  
    ![Képernyőkép a környezetében lévő pillanatkép készítése gombról](media/storage-how-to-use-files-storage-explorer/create-share-snapshot-1.png)

### <a name="list-and-browse-share-snapshots"></a>Megosztási pillanatképek listázása és böngészése
A pillanatkép létrehozása után a **Fájlmegosztás pillanatképeinek megtekintése** elemre kattintva (az Azure Storage Explorer ablakméreteitől függően ez egy **... Továbbiak** elem mögött lehet elrejtve) listázhatja a megosztás pillanatképeit. Kattintson duplán egy megosztási pillanatképre annak böngészéséhez.

![A pillanatképek böngészése képernyő képernyőképe](media/storage-how-to-use-files-storage-explorer/list-browse-snapshots-1.png)

### <a name="restore-from-a-share-snapshot"></a>Visszaállítás megosztási pillanatképből
A fájlok megosztási pillanatképből való visszaállításának bemutatása érdekében először törölnünk kell egy fájlt az éles Azure-fájlmegosztásból. Lépjen a *myDirectory* mappába, kattintson a jobb gombbal a feltöltött fájlra, majd kattintson a **Törlés** gombra. Ezután a fájl megosztási pillanatképből való visszaállításához:

1. Kattintson a **Fájlmegosztás pillanatképeinek megtekintése** elemre (az Azure Storage Explorer ablakméreteitől függően ez egy **... Továbbiak** elem mögött lehet elrejtve).
2. Válasszon egy megosztási pillanatképet a listából, és kattintson duplán a navigáláshoz.
3. Haladjon végig a pillanatképen, amíg meg nem találja a törölt fájlt, jelölje ki azt, majd kattintson a **Pillanatkép visszaállítása** elemre (az Azure Storage Explorer ablakméreteitől függően ez egy **... Továbbiak** elem mögött lehet elrejtve). Megjelenik egy figyelmeztetés, amely tájékoztatja, hogy a fájl visszaállítása felülírja a fájlmegosztás tartalmát, és ez a művelet nem vonható vissza. Kattintson az **OK** gombra.
4. A fájlnak most az eredeti helyén kell lennie, az éles Azure-fájlmegosztás alatt.

### <a name="delete-a-share-snapshot"></a>Megosztási pillanatkép törlése
Megosztási pillanatkép törléséhez [keresse meg a megosztási pillanatképek listáját](#list-and-browse-share-snapshots). Kattintson a jobb gombbal a törölni kívánt megosztási pillanatképre, majd válassza a törlés lehetőséget.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Az Azure Storage Explorer nem képes az erőforrások eltávolítására; az [Azure Portalon](https://portal.azure.com/) törölheti a rövid útmutató elemeit. 

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>További lépések
- [Fájlmegosztások felügyelete az Azure Portal használatával](storage-how-to-use-files-portal.md)
- [Fájlmegosztások felügyelete az Azure PowerShell használatával](storage-how-to-use-files-powershell.md)
- [Fájlmegosztások felügyelete az Azure CLI használatával](storage-how-to-use-files-cli.md)
- [Az Azure Files üzembe helyezésének megtervezése](storage-files-planning.md)