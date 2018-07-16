---
title: Linux VM-beli felügyelt identitás használata az Azure Storage eléréséhez
description: Oktatóanyag, amely végigvezeti az Azure Storage Linux VM-beli, rendszer által hozzárendelt felügyelt identitással való elérésének folyamatán.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/09/2018
ms.author: daveba
ms.openlocfilehash: fb67d1eea588d96129c4b58a8c1b2f569c9663bf
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2018
ms.locfileid: "37904407"
---
# <a name="tutorial-use-a-linux-vms-managed-identity-to-access-azure-storage"></a>Oktatóanyag: Linux rendszerű virtuális gép felügyelt identitásának használata az Azure Storage eléréséhez 

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]


Ez az oktatóanyag bemutatja, hogyan hozhat létre és használhat Linux VM-beli felügyelt identitást az Azure Storage eléréséhez. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Linux rendszerű virtuális gép létrehozása új erőforráscsoportban
> * Felügyelt identitás engedélyezése Linux rendszerű virtuális gépen (VM)
> * Blobtároló létrehozása egy tárfiókban
> * Hozzáférés engedélyezése a Linux rendszerű virtuális gép felügyelt identitása számára egy Azure Storage-tárolóhoz
> * Hozzáférési jogkivonat lekérése, majd azzal az Azure Storage meghívása

> [!NOTE]
> Az Azure Storage Azure Active Directory-hitelesítése nyilvános előzetes verzióban érhető el.

## <a name="prerequisites"></a>Előfeltételek

Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com).

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

A jelen oktatóanyagban a parancssori példaszkriptek futtatása kétféleképpen végezhető el:

- Az [Azure Cloud Shell](~/articles/cloud-shell/overview.md) használatával az Azure Portalon vagy a minden egyes kódblokk jobb felső sarkában megtalálható **Kipróbálom** gombbal.
- Ha inkább a helyi parancssori konzolt kívánja használni, [telepítse a CLI 2.0 legújabb verzióját](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 vagy újabb).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Linux rendszerű virtuális gép létrehozása új erőforráscsoportban

Ebben a szakaszban olyan Linux rendszerű virtuális gépet hoz létre, amelyhez később felügyelt identitást fog adni.

1. Az Azure Portal bal felső sarkában válassza az **Új** gombot.
2. Válassza a **Számítás**, majd az **Ubuntu Server 16.04 LTS** elemet.
3. Adja meg a virtuális gép adatait. A **Hitelesítés típusa** résznél válassza az **SSH nyilvános kulcs** vagy a **Jelszó** lehetőséget. A létrehozott hitelesítő adatokkal jelentkezhet be a virtuális gépre.

   ![A virtuális gép létrehozásának „Alapvető beállítások” panelje](../media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Válasszon ki egy előfizetést az **Előfizetés** listából a virtuális géphez.
5. Ha a virtuális gépet egy új erőforráscsoportban szeretné létrehozni, válassza az **Erőforráscsoport** > **Új létrehozása** lehetőséget. Ha elkészült, kattintson az **OK** gombra.
6. Válassza ki a virtuális gép méretét. További méretek megjelenítéséhez válassza **Az összes megtekintése** lehetőséget, vagy módosítsa a **Támogatott lemeztípus** szűrőt. A beállítások paneljén tartsa meg az alapértelmezett beállításokat, majd kattintson az **OK** gombra.

## <a name="enable-managed-identity-on-your-vm"></a>Felügyelt identitás engedélyezése a virtuális gépen

A virtuális gép felügyelt identitásával anélkül kérhet le hozzáférési jogkivonatokat az Azure AD-ből, hogy hitelesítő adatokat kellene a kódba illesztenie. A háttérben a felügyelt identitás Azure Portalon keresztül, virtuális gépen történő engedélyezésének két következménye van: regisztrálja a virtuális gépet az Azure AD-ben egy felügyelt identitás létrehozásához, és konfigurálja az identitást a virtuális gépen.

1. Lépjen az új virtuális gép erőforráscsoportjára, és válassza ki az előző lépésben létrehozott virtuális gépet.
2. A **Beállítások** kategóriában kattintson a **Konfiguráció** elemre.
3. A felügyelt identitás engedélyezéséhez kattintson az **Igen** gombra.
4. Kattintson a **Mentés** gombra a konfiguráció alkalmazásához. 

## <a name="create-a-storage-account"></a>Tárfiók létrehozása 

Ebben a szakaszban egy új tárfiókot fog létrehozni. 

1. Kattintson az Azure Portal bal felső sarkában található **+ Erőforrás létrehozása** gombra.
2. Kattintson a **Storage**, majd a **Tárfiók – blob, fájl, tábla, üzenetsor** lehetőségre.
3. A **Név** mezőben adja meg a tárfiók nevét.  
4. A **Telepítési modell** mezőben a **Resource Manager**, a **Fiók típusa** mezőben a **Storage (általános célú v1)** beállítást kell megadni. 
5. Ellenőrizze, hogy az **Előfizetés** és az **Erőforráscsoport** mező értéke egyezik-e az előző lépésben a virtuális gép létrehozása során megadottakkal.
6. Kattintson a **Create** (Létrehozás) gombra.

    ![Új tárfiók létrehozása](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-and-upload-a-file-to-the-storage-account"></a>Blobtároló létrehozása és egy fájl feltöltése a tárfiókba

A fájlok tárolásához blobtároló szükséges, ezért létre kell hoznia egyet, amelyben a fájlt tárolhatja. Ezután fel fog tölteni egy fájlt az új tárfiókon lévő blobtárolóba.

1. Lépjen vissza az újonnan létrehozott tárfiókra.
2. A **Blob Service** területen kattintson a **Tárolók** lehetőségre.
3. Kattintson a **+ Tároló** gombra a lap tetején.
4. Az **Új tároló** területen írja be a tároló nevét, és a **Nyilvános hozzáférés szintje** területen tartsa meg az alapértelmezett értéket.

    ![Storage-tároló létrehozása](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. Egy tetszőleges szövegszerkesztővel hozzon létre egy *hello world.txt* nevű fájlt a helyi gépen.  Nyissa meg a fájlt, adja hozzá a „Hello, world! :)” szöveget (idézőjel nélkül), majd mentse el. 

6. A tároló nevére, majd a **Feltöltés** lehetőségre kattintva töltse fel a fájlt az újonnan létrehozott tárolóba.
7. A **Blob feltöltése** panel **Fájlok** területén kattintson a mappa ikonra, keresse meg a **hello_world.txt** fájlt a helyi gépen, válassza ki a fájlt, majd kattintson a **Feltöltés** gombra.

    ![Szövegfájl feltöltése](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

## <a name="grant-your-vm-access-to-an-azure-storage-container"></a>Hozzáférés engedélyezése a virtuális gép számára egy Azure Storage-tárolóhoz 

A VM felügyelt identitásával lekérheti az Azure-tárolóblob adatait.   

1. Lépjen vissza az újonnan létrehozott tárfiókra.  
2. Kattintson a **Hozzáférés-vezérlés (IAM)** hivatkozásra a bal oldali panelen.  
3. A lap tetején a **+ Hozzáadás** gombra kattintva adjon hozzá egy új szerepkör-hozzárendelést a VM-hez.
4. A **Szerepkör** területen lévő legördülő listában válassza a **Storage-blobadatok olvasója (minta)** elemet. 
5. A következő legördülő menüben, a **Hozzáférés hozzárendelése** területen válassza ki a **Virtuális gép** elemet.  
6. Ezután ellenőrizze, hogy a megfelelő előfizetés szerepel-e az **Előfizetés** legördülő menüben, majd állítsa az **Erőforráscsoport** értékét a **Minden erőforráscsoport** értékre.  
7. A **Kiválasztás** mezőben válassza ki a virtuális gépet, majd kattintson a **Mentés** gombra.

    ![Engedélyek kiosztása](~/articles/active-directory/managed-service-identity/media/tutorial-linux-vm-access-storage/access-storage-perms.png)

## <a name="get-an-access-token-and-use-it-to-call-azure-storage"></a>Hozzáférési jogkivonat lekérése, majd azzal az Azure Storage meghívása

Az Azure Storage natív támogatást nyújt az Azure AD-hitelesítésnek, így közvetlenül is elfogadhatja a felügyelt identitás használatával beszerzett hozzáférési jogkivonatokat. Ez az Azure Storage és az Azure AD integrációjának része, és eltér attól a megoldástól, amikor a kapcsolati sztringen adja meg a hitelesítő adatokat.

Az alábbi lépések elvégzéséhez a korábban létrehozott virtuális gépről kell dolgoznia, az ahhoz való csatlakozáshoz pedig egy SSH-ügyfélre van szüksége. Windows használata esetén használhatja a [Linux Windows alrendszerében](https://msdn.microsoft.com/commandline/wsl/about) elérhető SSH-ügyfelet. Amennyiben segítségre van szüksége az SSH-ügyfél kulcsának konfigurálásához, [Az SSH-kulcsok és a Windows együttes használata az Azure-ban](~/articles/virtual-machines/linux/ssh-from-windows.md) vagy [Nyilvános és titkos SSH-kulcspár létrehozása és használata az Azure-ban Linux rendszerű virtuális gépekhez](~/articles/virtual-machines/linux/mac-create-ssh-keys.md) című cikkekben talál további információt.

1. Az Azure Portalon lépjen a **Virtuális gépek** területre, keresse meg a Linux rendszerű virtuális gépét, majd az **Áttekintés** lapon kattintson a **Csatlakozás** gombra. Másolja ki sztringet a virtuális géphez való csatlakozáshoz.
2. **Csatlakozzon** a virtuális géphez a választott SSH-ügyféllel. 
3. A terminálablakban a CURL használatával küldjön kérést a helyi felügyeltidentitás-végpontra egy hozzáférési jogkivonat lekérésére az Azure Resource Managerhez.
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fstorage.azure.com%2F' -H Metadata:true
    ```
4. A hozzáférési jogkivonat segítségével most már elérheti az Azure Storage-et, például a korábban a tárolóba feltöltött mintafájl tartalmának olvasásához. Cserélje le a `<STORAGE ACCOUNT>`, `<CONTAINER NAME>` és `<FILE NAME>` értékeit a korábban megadott értékekre, az `<ACCESS TOKEN>` értékét pedig az előző lépésben visszaadott jogkivonatra.

   ```bash
   curl https://<STORAGE ACCOUNT>.blob.core.windows.net/<CONTAINER NAME>/<FILE NAME> -H "x-ms-version: 2017-11-09" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   A válasz tartalmazza a fájl tartalmát:

   ```bash
   Hello world! :)
   ```

## <a name="next-steps"></a>További lépések

Az oktatóanyag bemutatta, hogyan gondoskodhat róla, hogy egy Linux rendszerű virtuális gép felügyelt identitása hozzá tudjon férni az Azure Storage-hoz.  További információ az Azure Storage-ról:

> [!div class="nextstepaction"]
> [Azure Storage](/azure/storage/common/storage-introduction)
