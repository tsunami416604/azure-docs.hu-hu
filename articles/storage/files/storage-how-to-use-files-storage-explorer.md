---
title: Azure-fájlmegosztások felügyelete az Azure Storage Explorerrel
description: Megismerheti, hogyan kezelheti az Azure Files szolgáltatást az Azure Storage Explorerrel.
services: storage
author: wmgries
ms.service: storage
ms.topic: get-started-article
ms.date: 02/27/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: 949d96bb1b5ffdc948737d4a47ffa14b2e344b5e
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45574731"
---
# <a name="manage-azure-file-shares-with-azure-storage-explorer"></a>Azure-fájlmegosztások felügyelete az Azure Storage Explorerrel 
Az [Azure Files](storage-files-introduction.md) a Microsoft egyszerűen használható felhőalapú fájlrendszere. Ez a cikk az Azure-fájlmegosztások az [Azure Storage Explorerrel](https://azure.microsoft.com/features/storage-explorer/) való használatának alapvető lépéseit mutatja be. A Storage Explorer a Windows, macOS és Linux esetén egyaránt elérhető népszerű ügyféleszköz. A Storage Explorer segítségével felügyelheti az Azure-fájlmegosztásokat és más tárolási erőforrásokat.

A rövid útmutatóhoz a Storage Explorer telepítése szükséges. A letöltéséhez és telepítéséhez keresse fel az [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) webhelyét.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Erőforráscsoport és tárfiók létrehozása
> * Azure-fájlmegosztás létrehozása 
> * Könyvtár létrehozása
> * Fájl feltöltése
> * Fájl letöltése
> * Megosztási pillanatkép létrehozása és használata

Ha nem rendelkezik Azure-előfizetéssel, első lépésként létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-storage-account"></a>Tárfiók létrehozása
A Storage Explorer nem használható új erőforrások létrehozására. A bemutatóhoz hozzon létre egy tárfiókot az [Azure Portalon](https://portal.azure.com/). 

[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]

## <a name="connect-storage-explorer-to-azure-resources"></a>A Storage Explorer csatlakoztatása Azure-erőforrásokhoz
A Storage Explorer első indításakor megjelenik a **Microsoft Azure Storage Explorer – Csatlakozás** ablak. A Storage Explorer számos különféle módot kínál a tárfiókokhoz való csatlakozáshoz: 

- **Bejelentkezés az Azure-fiókon keresztül**: Bejelentkezhet a vállalata vagy Microsoft-fiókja felhasználói hitelesítő adataival. 
- **Csatlakozás adott tárfiókhoz kapcsolati sztring vagy SAS-jogkivonat használatával**: A kapcsolati sztring egy speciális sztring, amely tartalmazza a tárfiók nevét és a tárfiók kulcsát/SAS-jogkivonatát. A jogkivonattal a Storage Explorer közvetlenül a tárfiókhoz fér hozzá (ahelyett, hogy egyszerűen az Azure-fiókban lévő összes tárfiókot látná). További információ a kapcsolati sztringekról: [Az Azure Storage kapcsolati sztringek konfigurálása](../common/storage-configure-connection-string.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
- **Csatlakozás adott tárfiókhoz a tárfiók nevének és kulcsának használatával**: A tárfiók nevével és kulcsával csatlakozhat az Azure Storage-hoz.

Ebben a rövid útmutatóban az Azure-fiókjával jelentkezzen be. Válassza az **Azure-fiók hozzáadása**, majd a **Bejelentkezés** elemet. Az utasításokat követve jelentkezzen be Azure-fiókjába.

![A Microsoft Azure Storage Explorer – Csatlakozás ablak képernyőképe](./media/storage-how-to-use-files-storage-explorer/connect-to-azure-storage-1.png)

### <a name="create-a-file-share"></a>Fájlmegosztás létrehozása
Az első Azure-fájlmegosztás létrehozása a *storageacct<random number>* tárfiókban:

1. Bontsa ki a létrehozott tárfiókot.
2. Kattintson a jobb gombbal a **Fájlmegosztások** elemre, majd válassza a **Fájlmegosztás létrehozása** lehetőséget.  
    ![A fájlmegosztások mappa és a környezet helyi menüjének képernyőképe](media/storage-how-to-use-files-storage-explorer/create-file-share-1.png)

3. Adja a *myshare* nevet a fájlmegosztásnak, majd nyomja le az Enter billentyűt.

> [!IMPORTANT]  
> A fájlmegosztások neve csak kisbetűket, számokat és kötőjeleket tartalmazhat (de nem kezdődhet kötőjellel). A fájlmegosztások és fájlok elnevezésére vonatkozó információkért tekintse meg a [megosztások, könyvtárak, fájlok és metaadatok elnevezésével és a rájuk való hivatkozással](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata) foglalkozó cikket.

Ha a fájlmegosztás létrejött, a jobb oldali panelen megnyílik a fájlmegosztás lapja. 

## <a name="work-with-the-contents-of-an-azure-file-share"></a>Az Azure-fájlmegosztások tartalmának használata
Most, hogy létrehozott egy Azure-fájlmegosztást, csatlakoztathatja azt az SMB szolgáltatással [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) vagy [macOS](storage-how-to-use-files-mac.md) rendszeren. Másik megoldásként az Azure CLI-vel is használhatja az Azure-fájlmegosztást. Az Azure CLI használata azért előnyösebb a fájlmegosztás SMB-vel való csatlakoztatásánál, mert az Azure CLI-vel kezdeményezett kérések mindegyike a Fájl REST API-t használja. A Fájl REST API segítségével létrehozhat, módosíthat és törölhet fájlokat és könyvtárakat az SMB-hozzáféréssel nem rendelkező ügyfeleken is.

### <a name="create-a-directory"></a>Könyvtár létrehozása
Egy könyvtár hozzáadása hierarchikus struktúrát biztosít a fájlmegosztás felügyeletéhez. A könyvtárban több szintet is létrehozhat. Az alkönyvtárak létrehozása előtt azonban mindig győződjön meg arról, hogy a szülőkönyvtárak léteznek. A myDirectory/mySubDirectory elérési út esetében például előbb a *myDirectory* könyvtárat kell létrehozni. Ezután hozhatja létre a *mySubDirectory* alkönyvtárat. 

1. A fájlmegosztás lapjának felső menüjében kattintson az **Új mappa** gombra. Megnyílik az **Új könyvtár létrehozása** panel.
    ![Képernyőkép a menü Új mappa gombjáról](media/storage-how-to-use-files-storage-explorer/create-directory-1.png)

2. A könyvtár neveként adja meg a *myDirectory* nevet, majd kattintson az **OK** gombra. 

A *myDirectory* könyvtár megjelenik a *myshare* fájlmegosztás lapján.

### <a name="upload-a-file"></a>Fájl feltöltése 
Feltölthet fájlokat a helyi gépről a fájlmegosztás új könyvtárába. Feltölthet teljes mappát vagy akár egyetlen fájlt is.

1. A felső menüben válassza a **Feltöltés** elemet. Ez lehetővé teszi egy mappa vagy fájl feltöltését.
2. Válassza a **Fájl feltöltése** lehetőséget, majd jelöljön ki egy feltölteni kívánt fájlt a helyi gépen.
3. A **Feltöltés könyvtárba** mezőbe írja be a *myDirectory* nevet, majd kattintson a **Feltöltés** gombra. 

Amikor végzett, a fájl megjelenik a *myDirectory* panelen lévő listában.

### <a name="download-a-file"></a>Fájl letöltése
Ha le szeretné tölteni a fájlmegosztásból valamelyik fájl másolatát, kattintson a jobb gombbal a fájlra, majd válassza a **Letöltés** lehetőséget. Válassza ki, hová szeretné menteni a fájlt a helyi gépen, majd válassza a **Mentés** lehetőséget.

A letöltés állapota az ablak alján található **Tevékenységek** panelen látható.

## <a name="create-and-modify-share-snapshots"></a>Megosztási pillanatképek létrehozása és módosítása
A pillanatképek megőrzik az Azure-fájlmegosztások adott időpontban látható másolatát. A fájlmegosztások pillanatképei hasonlók az esetleg már ismert egyéb technológiákhoz:
- [Kötet árnyékmásolata szolgáltatás (VSS)](https://docs.microsoft.com/windows/desktop/VSS/volume-shadow-copy-service-portal) Windows-fájlrendszerekhez (például NTFS és ReFS)
- [Logikaikötet-kezelő (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) pillanatképek Linux rendszerekhez
- [Apple fájlrendszer (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) pillanatképek macOS rendszerhez

Megosztási pillanatkép létrehozása:

1. Kattintson a *myshare* fájlmegosztás lapjára.
2. A felső menüben válassza a **Pillanatkép készítése** elemet. (A Storage Explorer ablakának méreteitől függően előfordulhat, hogy a beállítás megjelenítéséhez először a **Továbbiak** elemre kell kattintania.)  
    ![Képernyőkép a menü Pillanatkép készítése gombjáról](media/storage-how-to-use-files-storage-explorer/create-share-snapshot-1.png)

### <a name="list-and-browse-share-snapshots"></a>Megosztási pillanatképek listázása és böngészése
A pillanatkép létrehozása után a **Fájlmegosztás pillanatképeinek megtekintése** elemet választva jelenítheti meg a megosztás pillanatképeinek listáját. (A Storage Explorer ablakának méreteitől függően előfordulhat, hogy a beállítás megjelenítéséhez először a **Továbbiak** elemre kell kattintania.) Az egyes megosztási pillanatképek böngészéséhez kattintson duplán a pillanatképre.

![A Pillanatképek böngészése ablak képernyőképe](media/storage-how-to-use-files-storage-explorer/list-browse-snapshots-1.png)

### <a name="restore-from-a-share-snapshot"></a>Visszaállítás megosztási pillanatképből
Annak érdekében, hogy bemutathassuk a fájlok visszaállítását egy megosztás pillanatképéből, először törölnünk kell egy fájlt az éles Azure-fájlmegosztásból. Lépjen a *myDirectory* mappába, kattintson a jobb gombbal a feltöltött fájlra, majd válassza a **Törlés** elemet. A fájl visszaállítása a megosztás pillanatképéből:

1. Válassza a **Fájlmegosztás pillanatképeinek megtekintése** elemet. (A Storage Explorer ablakának méreteitől függően előfordulhat, hogy a beállítás megjelenítéséhez először a **Továbbiak** elemre kell kattintania.)
2. A megosztás pillanatképeinek listáján kattintson duplán a megosztás pillanatképére.
3. A pillanatképben tallózva keresse meg a törölt fájlt. Válassza ki a fájlmegosztást, majd válassza a **Pillanatkép visszaállítása** elemet. (A Storage Explorer ablakának méreteitől függően előfordulhat, hogy a beállítás megjelenítéséhez először a **Továbbiak** elemre kell kattintania.) Megjelenik egy ablak, amely figyelmezteti, hogy a fájl visszaállítása felülírja a fájlmegosztás tartalmát, és a művelet nem vonható vissza. Kattintson az **OK** gombra.
4. A fájlnak most az eredeti helyén kell lennie, az éles Azure-fájlmegosztás alatt.

### <a name="delete-a-share-snapshot"></a>Megosztási pillanatkép törlése
Megosztás pillanatképének törléséhez lépjen a [megosztások pillanatképeinek listájára](#list-and-browse-share-snapshots). Kattintson a jobb gombbal a törölni kívánt megosztás-pillanatképre, majd válassza a **Törlés** elemet.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
A Storage Explorer nem használható erőforrások eltávolítására. A jelen gyors útmutató fölöslegessé vált elemeinek törléséhez az [Azure Portalt](https://portal.azure.com/) használhatja. 

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>További lépések
- [Fájlmegosztások felügyelete az Azure Portal használatával](storage-how-to-use-files-portal.md)
- [Fájlmegosztások felügyelete az Azure PowerShell használatával](storage-how-to-use-files-powershell.md)
- [Fájlmegosztások felügyelete az Azure CLI használatával](storage-how-to-use-files-cli.md)
- [Az Azure Files üzembe helyezésének megtervezése](storage-files-planning.md)