---
title: Rövid útmutató az Azure-fájlmegosztásoknak az Azure Portallal történő felügyeletéhez
description: Ebből a rövid útmutatóból megtudhatja, hogyan felügyelheti az Azure Files szolgáltatást az Azure Portallal.
services: storage
author: wmgries
ms.service: storage
ms.topic: quickstart
ms.date: 10/18/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: 12e6b3a3fe790183a35c71fbb87243890ad22236
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2018
ms.locfileid: "49944838"
---
# <a name="quickstart-create-and-manage-azure-file-shares-with-the-azure-portal"></a>Rövid útmutató: Azure-fájlmegosztások létrehozása és felügyelete az Azure Portallal 
Az [Azure Files](storage-files-introduction.md) a Microsoft könnyen használható felhőalapú fájlrendszere. Az Azure-fájlmegosztások Windows, Linux és macOS rendszeren csatlakoztathatók. Ez az útmutató az Azure-fájlmegosztások [Azure Portallal](https://portal.azure.com/) való használatának alapvető lépéseit mutatja be.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-a-storage-account"></a>Tárfiók létrehozása
[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]

## <a name="create-an-azure-file-share"></a>Azure-fájlmegosztás létrehozása
Azure-fájlmegosztás létrehozása:

1. Válassza ki a tárfiókot az irányítópulton.
2. A tárfiók oldalának **Szolgáltatások** szakaszában válassza a **Fájlok** elemet.
    ![A tárfiók szolgáltatások szakaszának képernyőképe; válassza a Fájlok szolgáltatást](media/storage-how-to-use-files-portal/create-file-share-1.png)

3. A **Fájlszolgáltatás** tetején lévő menüben kattintson a **+ Fájlmegosztás** elemre. Lenyílik az **Új fájlmegosztás** oldal.
4. A **Név** mezőbe írja be a *myshare* karakterláncot.
5. Az Azure-fájlmegosztás létrehozásához kattintson az **OK** gombra.

A fájlmegosztások neve kisbetűket, számokat és kötőjeleket tartalmazhat, de nem kezdődhet kötőjellel. A fájlmegosztások és fájlok elnevezésére vonatkozó információkért lásd: [Naming and Referencing Shares, Directories, Files, and Metadata](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata) (Megosztások, könyvtárak, fájlok és metaadatok elnevezése és hivatkozása).

## <a name="use-your-azure-file-share"></a>Az Azure-fájlmegosztás használata
Az Azure Files két módszert biztosít a fájloknak és mappáknak az Azure-fájlmegosztásban való használatához: az iparági szabvány [Server Message Block (SMB) protokollt](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) és a [Fájl REST protokollt](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api). 

A fájlmegosztás SMB-vel való csatlakoztatásához tekintse meg a használt operációs rendszernek megfelelő dokumentumot az alábbiak közül:
- [Windows](storage-how-to-use-files-windows.md)
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)

### <a name="using-an-azure-file-share-from-the-azure-portal"></a>Azure-fájlmegosztás használata az Azure Portalról
Az Azure Portalon keresztül intézett összes kérelem a fájl REST API-jával történik, amely lehetővé teszi a fájlok és könyvtárak létrehozását, módosítását és törlését SMB-hozzáférés nélküli ügyfeleken. A Fájl REST protokollal közvetlenül is dolgozhat (vagyis saját maga készítheti el a REST HTTP-hívásokat), de a Fájl REST protokoll használatának legáltalánosabb módja (az Azure Portal használatán túl) az [AzureRM PowerShell-modul](storage-how-to-use-files-powershell.md), az [Azure CLI](storage-how-to-use-files-cli.md) vagy egy Azure Storage SDK használata, amelyek megfelelő burkolóval látják el a Fájl REST protokollt a választott parancs-/programozási nyelven. 

Arra számítunk, hogy a legtöbb Azure Files-felhasználó az SMB protokollon keresztül kíván majd dolgozni az Azure-fájlmegosztásával, mivel ez lehetővé teszi számukra a mások által is vélhetően használt meglévő alkalmazások és eszközök használatát. A Fájl REST API használata azonban számos előnnyel jár az SMB-vel szemben, például a következő esetekben:

- Útközben kell elvégeznie egy gyors módosítást az Azure-fájlmegosztáson, például egy SMB-hozzáférés nélküli laptopról, táblagépről vagy mobileszközről.
- Olyan ügyfélről kell szkriptet vagy alkalmazást futtatnia, amely nem tud SMB-megosztást csatlakoztatni, például a helyszíni ügyfelekről, amelyekhez nincs feloldva a 445-ös port.
- Ki szeretné használni a kiszolgáló nélküli erőforrások, például az [Azure Functions](../../azure-functions/functions-overview.md) előnyeit. 

Az alábbi példák azt mutatják be, hogyan használhatja az Azure Portalt az Azure-fájlmegosztás Fájl REST protokollal való módosítására. 

Most, hogy létrehozott egy Azure-fájlmegosztást, csatlakoztathatja azt az SMB szolgáltatással [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) vagy [macOS](storage-how-to-use-files-mac.md) rendszeren. Másik megoldásként az Azure Portalon is használhatja az Azure-fájlmegosztást. 

#### <a name="create-a-directory"></a>Könyvtár létrehozása
Ha egy új *myDirectory* nevű könyvtárat szeretne létrehozni az Azure-fájlmegosztás gyökérmappájában:

1. A **Fájlszolgáltatás** oldalon válassza ki a **myshare** fájlmegosztást. Megnyílik a fájlmegosztás oldala.
2. Az oldal tetején lévő menüben kattintson a **+ Könyvtár hozzáadása** elemre. Lenyílik az **Új könyvtár** oldal.
3. Írja be a *myDirectory* nevet, majd kattintson az **OK**.gombra.

#### <a name="upload-a-file"></a>Fájl feltöltése 
A fájlok feltöltésének bemutatása érdekében először létre kell hoznia vagy ki kell választania a feltölteni kívánt fájlt. Ezt bármilyen módon megteheti. Miután kiválasztotta a feltölteni kívánt fájlt:

1. Kattintson a **myDirectory** könyvtárra. Megnyílik a **myDirectory** panel.
2. A felül található menüben kattintson a **Feltöltés** elemre. Megnyílik a **Fájlok feltöltése** panel.  
    ![A fájlok feltöltése panel képernyőképe](media/storage-how-to-use-files-portal/upload-file-1.png)

3. Kattintson a mappa ikonra a helyi fájlok megkeresésére szolgáló ablak megnyitásához. 
4. Válasszon egy fájlt, majd kattintson a **Megnyitás** gombra. 
5. A **Fájlok feltöltése** oldalon ellenőrizze a fájl nevét, majd kattintson a **Feltöltés** elemre.
6. Amikor végzett, a fájlnak meg kell jelennie a **myDirectory** oldalon lévő listában.

#### <a name="download-a-file"></a>Fájl letöltése
A fájlra a jobb gombbal kattintva letöltheti a feltöltött fájl másolatát. Miután a letöltés gombra kattintott, a pontos élmény az operációs rendszertől és a használt böngészőtől függ.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Mi az Azure Files?](storage-files-introduction.md)