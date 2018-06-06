---
title: Azure Storage eléréséhez Linux virtuális gép felügyelt identitás használatára
description: Ez az oktatóanyag végigvezeti egy System-Assigned felügyelt azonosító használata a Linux virtuális gép, Azure Storage eléréséhez.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/09/2018
ms.author: skwan
ms.openlocfilehash: 1bd4c422bd952cb6abe2432904adfd9ae9e2fd4f
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34801704"
---
# <a name="tutorial-use-a-linux-vms-managed-identity-to-access-azure-storage"></a>Oktatóanyag: A Linux virtuális gépek felügyelt identitás Azure Storage elérésére használhat. 

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]


Ez az oktatóanyag bemutatja, hogyan létrehozása és használata a Linux virtuális gép felügyelt identitás Azure Storage eléréséhez. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Egy új erőforráscsoportot a Linux virtuális gép létrehozása
> * A Linux virtuális gép (VM) felügyelt identitásának engedélyezése
> * A tárfiók egy blob-tároló létrehozása
> * A Linux virtuális gép identitásának felügyelt hozzáférést egy Azure Storage-tároló
> * Szereznie egy hozzáférési jogkivonatot, és hívja az Azure Storage segítségével

> [!NOTE]
> Az Azure Storage Azure Active Directory-hitelesítés nyilvános előzetes verziójában van.

## <a name="prerequisites"></a>Előfeltételek

Ha még nem telepítette az Azure-fiók [regisztráljon egy ingyenes fiókot](https://azure.microsoft.com) folytatása előtt.

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

Ebben az oktatóanyagban a CLI-parancsfájlt példák futtatásához két lehetőség közül választhat:

- Használjon [Azure Cloud rendszerhéj](~/articles/cloud-shell/overview.md) vagy az Azure-portálon keresztül a **, próbálja** gomb, minden egyes kódblokk jobb felső sarkában található.
- [Telepítse a legújabb verziót a CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 vagy újabb verzió) Ha a helyi CLI-konzollal szeretné.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Egy új erőforráscsoportot a Linux virtuális gép létrehozása

Ebben a szakaszban egy Linux virtuális Gépet, amely egy felügyelt identitás később engedélyezett hoz létre.

1. Válassza ki a **új** gomb az Azure portál bal felső sarkában.
2. Válassza a **Számítás**, majd az **Ubuntu Server 16.04 LTS** elemet.
3. Adja meg a virtuális gép adatait. A **hitelesítési típus**, jelölje be **nyilvános SSH-kulcs** vagy **jelszó**. A létrehozott hitelesítő adatok lehetővé teszik-e jelentkezni a virtuális gép.

   !["Alapvető beállítások" ablaktáblán a virtuális gép létrehozása](../media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Az a **előfizetés** listára, válassza ki a virtuális gép előfizetés.
5. Egy új erőforráscsoportot, amelyet létre kell hozni a virtuális gép kiválasztásához jelölje be **erőforráscsoport** > **hozzon létre új**. Amikor elkészült, válassza **OK**.
6. Adja meg a virtuális gép számára. További méretek megjelenítéséhez válassza **Az összes megtekintése** lehetőséget, vagy módosítsa a **Támogatott lemeztípus** szűrőt. A beállítások panelen hagyja az alapértelmezett beállításokat, és válassza ki **OK**.

## <a name="enable-managed-identity-on-your-vm"></a>A virtuális Gépet felügyelt identitásának engedélyezése

A virtuális gép felügyelt azonosítójának hozzáférési jogkivonatok lekérni az Azure AD anélkül, hogy a hitelesítő adatok kerüljenek a kódot a teszi lehetővé. A színfalak engedélyezése egy virtuális gépet az Azure-portálon kezelt identitásának két dolgot eredményez: regisztrálja a virtuális Gépet az Azure AD-hoz létre egy felügyelt, és konfigurálja a virtuális gép identitását.

1. Keresse meg az erőforráscsoport a új virtuális gép, és válassza ki a virtuális gépet, az előző lépésben létrehozott.
2. Az a **beállítások** kategória, kattintson a **konfigurációs**.
3. Felügyelt identitás engedélyezéséhez jelölje be **Igen**.
4. Kattintson a **mentése** elemre a konfiguráció alkalmazásához. 

## <a name="create-a-storage-account"></a>Create a storage account 

Ebben a szakaszban hozzon létre egy tárfiókot. 

1. Kattintson a **+ hozzon létre egy erőforrást** gomb az Azure portál bal felső sarkában található.
2. Kattintson a **tárolási**, majd **tárfiók - blob, a fájl, a tábla, a várólista**.
3. A **neve**, adja meg a tárfiók nevét.  
4. **Telepítési modell** és **fiók kind** meg **erőforrás-kezelő** és **Storage (az általános célú v1)**. 
5. Győződjön meg arról a **előfizetés** és **erőforráscsoport** az előző lépésben a virtuális gép létrehozásakor megadott megfelelően.
6. Kattintson a **Create** (Létrehozás) gombra.

    ![Új tárfiók létrehozása](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-and-upload-a-file-to-the-storage-account"></a>A blob-tároló létrehozása és a fájl feltöltése a tárfiókba

Szükséges fájlok blob Storage tárolóban, ezért szüksége lesz egy blob tároló, amely tárolja a fájl létrehozásához. Majd fájlt töltsön fel az új tárfiók a blob-tárolóhoz.

1. Lépjen vissza az újonnan létrehozott tárfiók.
2. A **Blob szolgáltatás**, kattintson a **tárolók**.
3. Kattintson a **+ tároló** az oldal tetején.
4. A **új tároló**, adjon meg egy nevet, a tároló és a **nyilvános hozzáférési szint** tartsa meg az alapértelmezett értéket.

    ![A tároló létrehozása](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. Egy fájlt egy szerkesztővel az Ön által választott, hozzon létre *hello world.txt* a helyi számítógépen.  Nyissa meg a fájlt, és adja hozzá a "Hello world szöveg (idézőjelek nélkül)! :) ", és mentse azt. 

6. Töltse fel a fájlt a tároló neve, majd kattintson az újonnan létrehozott tárolóhoz **feltöltése**
7. Az a **feltöltése a blob** ablaktáblán, a **fájlok**, a mappa ikonra, és keresse meg a fájlt **hello_world.txt** a helyi számítógépen, válassza ki a fájlt, majd kattintson a **Feltöltése**.

    ![Szöveg-fájl feltöltése](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

## <a name="grant-your-vm-access-to-an-azure-storage-container"></a>A virtuális gép hozzáférést biztosíthat az Azure Storage-tároló 

A virtuális Gépet felügyelt identitás használhatja az Azure storage-blob adatainak beolvasása.   

1. Lépjen vissza az újonnan létrehozott tárfiók.  
2. Kattintson a **hozzáférés-vezérlés (IAM)** hivatkozás a bal oldali panelen.  
3. Kattintson a **+ Hozzáadás** fölött a lap egy új szerepkör-hozzárendelés hozzáadása a virtuális gép számára.
4. A **szerepkör**, a legördülő listából válassza ki a **Adatolvasó a tárolási Blob (előzetes verzió)**. 
5. A következő legördülő alatt **való hozzáférés hozzárendelése**, válassza a **virtuális gép**.  
6. A következő szerepel-e a megfelelő előfizetést a **előfizetés** legördülő menüből, majd állítsa be **erőforráscsoport** való **összes erőforráscsoport**.  
7. A **válasszon**, válassza ki a virtuális Gépet, és kattintson a **mentése**.

    ![Engedélyek hozzárendelése](~/articles/active-directory/managed-service-identity/media/tutorial-linux-vm-access-storage/access-storage-perms.png)

## <a name="get-an-access-token-and-use-it-to-call-azure-storage"></a>Szereznie egy hozzáférési jogkivonatot, és hívja az Azure Storage segítségével

Az Azure Storage natív módon támogatja az Azure AD hitelesítési, így közvetlenül elfogadása jogkivonatot kapott egy felügyelt identitásával. Az Azure Storage-integráció az Azure ad-val része, és biztosítja a kapcsolati karakterlánc-felhasználó hitelesítő adatai eltérnek.

Az alábbi lépésekkel kíván dolgozni a korábban létrehozott virtuális gépről, és egy SSH-ügyfél a csatlakozáshoz szükséges. Windows használ, ha az SSH-ügyfél a használhatja a [Linux rendszerhez készült Windows alrendszer](https://msdn.microsoft.com/commandline/wsl/about). Ha az SSH-ügyfél kulcsok konfigurálása segítségre van szüksége, tekintse meg [a Windows Azure használatára SSH-kulcsok hogyan](~/articles/virtual-machines/linux/ssh-from-windows.md), vagy [létrehozása, és az SSH nyilvános és titkos kulcsból álló kulcspárt használata a Linux virtuális gépek Azure-ban](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).

1. Az Azure-portálon lépjen a **virtuális gépek**, keresse fel a Linux virtuális gépet, majd a a **áttekintése** kattintson **Connect**. Másolja a karakterláncot, amellyel a virtuális Géphez csatlakozik.
2. **Csatlakozás** a virtuális géphez a az SSH-ügyfél az Ön által választott. 
3. A Terminálszolgáltatások ablakban CURL, használatával indítson egy lekérdezést a helyi felügyelt identitás végpontot egy hozzáférési jogkivonat beszerzése az Azure Storage.
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fstorage.azure.com%2F' -H Metadata:true
    ```
4. A hozzáférési jogkivonat segítségével elérni az Azure Storage, például a mintafájlt, amely korábban a tárolóba feltöltött tartalmának olvasását. Cserélje le az értékeket `<STORAGE ACCOUNT>`, `<CONTAINER NAME>`, és `<FILE NAME>` a korábban megadott értékeket, és `<ACCESS TOKEN>` a jogkivonatok az előző lépés eredményeképpen visszakapott.

   ```bash
   curl https://<STORAGE ACCOUNT>.blob.core.windows.net/<CONTAINER NAME>/<FILE NAME> -H "x-ms-version: 2017-11-09" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   A válasz tartalmazza a fájl tartalma:

   ```bash
   Hello world! :)
   ```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan engedélyezze a Linux virtuális gépek felügyelt identitás Azure Storage eléréséhez.  További információért lásd a Azure Storage:

> [!div class="nextstepaction"]
> [Azure Storage](/azure/storage/common/storage-introduction)
