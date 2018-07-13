---
title: Egy felhasználó egy Linux rendszerű virtuális gépen az MSI használata az Azure Storage eléréséhez
description: Ez az oktatóanyag végigvezeti az egy felhasználó hozzárendelve Felügyeltszolgáltatás-identitás (MSI) használatával egy Linux rendszerű virtuális gépen az Azure Storage eléréséhez.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: arluca
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 4a1a2d0c40012649f6cd89193fd3f704f325e38a
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38611044"
---
# <a name="use-a-user-assigned-managed-service-identity-msi-on-a-linux-vm-to-access-azure-storage"></a>Egy felhasználó által hozzárendelt Felügyeltszolgáltatás-identitás (MSI) használata a Linux rendszerű virtuális gép Azure Storage eléréséhez

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Ez az oktatóanyag bemutatja, hogyan hozzon létre és a egy felhasználó által hozzárendelt Felügyeltszolgáltatás-identitás (MSI) a Linux rendszerű virtuális gép használja, majd használhatja azokat az Azure Storage eléréséhez. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Felügyeltszolgáltatás-identitás (MSI) hozzárendelt felhasználó létrehozása
> * Rendelje hozzá a felhasználó által hozzárendelt MSI a Linuxos virtuális gép
> * Az MSI hozzáférést egy Azure Storage-példányt
> * Az MSI felhasználó által hozzárendelt identitással hozzáférési jogkivonatot kapjon, és használhatja azokat az Azure Storage eléréséhez

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

Futtassa a CLI-példaszkriptek ebben az oktatóanyagban, két lehetősége van:

- Használat [Azure Cloud Shell](~/articles/cloud-shell/overview.md) vagy az Azure Portalról vagy a "Kipróbálom" gomb, mindegyik blokk jobb felső sarkában található.
- [Telepítse a CLI 2.0 legújabb verzióját](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23-as vagy újabb) Ha inkább a helyi CLI-konzol használatával.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Linux rendszerű virtuális gép létrehozása az új erőforráscsoport

Először hozzon létre egy új Linux rendszerű virtuális Gépet. Ha inkább a meglévő virtuális gép MSI is engedélyezheti.

1. Kattintson a **+/ hozzon létre új szolgáltatást** az Azure portal bal felső sarkában található gomb.
2. Válassza a **Számítás**, majd az **Ubuntu Server 16.04 LTS** elemet.
3. Adja meg a virtuális gép adatait. A **hitelesítési típus**válassza **nyilvános SSH-kulcs** vagy **jelszó**. A létrehozott hitelesítő adatok lehetővé teszik jelentkezzen be a virtuális Gépre.

    ![Kép ALT szövege](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Válasszon egy **előfizetés** a virtuális gép a legördülő listában.
5. Jelölje be egy új **erőforráscsoport** szeretne létrehozni, válassza ki a virtuális gép **hozzon létre új**. Amikor végzett, kattintson az **OK** gombra.
6. Válassza ki a méretet a virtuális gép számára. További méretek megjelenítéséhez válassza ki a **az összes megtekintése** vagy a lemez típusát támogatott szűrő módosítása. A Beállítások panelen hagyja változatlanul az alapértelmezett beállításokat, és kattintson az **OK** gombra.

## <a name="create-a-user-assigned-msi"></a>Hozzon létre egy felhasználó által hozzárendelt MSI

1. Ha a CLI-konzol (helyett az Azure Cloud Shell-munkamenetek) használ, először jelentkezik be az Azure-bA. Az Azure-előfizetést amellyel szeretné létrehozni az új MSI társított fiók használata:

    ```azurecli
    az login
    ```

2. Hozzon létre egy felhasználó által hozzárendelt MSI [az identitás létrehozása](/cli/azure/identity#az_identity_create). A `-g` paraméter adja meg az erőforráscsoport, amelyben az MSI jön létre, és a `-n` paraméter adja meg a nevét. Ne felejtse el a `<RESOURCE GROUP>` és `<MSI NAME>` paraméterértékeket a saját értékeire:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```

    A választ a felhasználó által hozzárendelt MSI létrehozott, az alábbi példához hasonló részleteit tartalmazza. Megjegyzés: a `id` érték az MSI-vel, mivel a következő lépésben fogja használni:

    ```json
    {
    "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
    "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=123444643-8088-4d70-9532-c3a0fdc190fz",
    "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
    "location": "westcentralus",
    "name": "<MSI NAME>",
    "principalId": "9012",
    "resourceGroup": "<RESOURCE GROUP>",
    "tags": {},
    "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
    "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
    }
    ```

## <a name="assign-your-user-assigned-msi-to-your-linux-vm"></a>A felhasználó által hozzárendelt MSI hozzárendelése a Linux rendszerű virtuális gép

Egy rendszer által hozzárendelt MSI-vel ellentétben a felhasználó által hozzárendelt MSI több Azure-erőforrást az ügyfelek által használható. Ebben az oktatóanyagban, rendelje hozzá egy virtuális Gépen. Akkor is hozzárendelheti egynél több virtuális gépre.

A felhasználó által hozzárendelt MSI hozzárendelése a Linux virtuális gép [az virtuális gép hozzárendelése-identity](/cli/azure/vm#az-vm-identity-assign). Ne felejtse el a `<RESOURCE GROUP>` és `<VM NAME>` paraméterértékeket a saját értékeire. Használja a `id` tulajdonság által visszaadott az előző lépésben a `--identities` paraméter értékét:

```azurecli-interactive
az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>"
```

## <a name="create-a-storage-account"></a>Tárfiók létrehozása 

Ha még nem rendelkezik egy, most hozzon létre egy tárfiókot. Kihagyhatja ezt a lépést is, és használható a meglévő storage-fiókot. 

1. Kattintson a **+/ hozzon létre új szolgáltatást** az Azure portal bal felső sarkában található gomb.
2. Kattintson a **tárolási**, majd **Tárfiók**, és a egy új "Tárfiók létrehozása" panelen jelenik meg.
3. Adjon meg egy **neve** a tárfiók, amely későbbi használatra.  
4. **Üzemi modell** és **fióktípus** értékre kell állítani "Erőforrás-kezelő" és "Általános célú", illetve. 
5. Győződjön meg, hogy a **előfizetés** és **erőforráscsoport** egyeznek azokkal az előző lépésben a virtuális gép létrehozásakor megadott.
6. Kattintson a **Create** (Létrehozás) gombra.

    ![Új tárfiók létrehozása](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Hozzon létre egy blobtárolót a tárfiókban

Mivel a fájlok blob storage-bA igényel, hozzon létre egy blobtárolót a fájl tárolására szeretne. Ezután töltse fel, és töltse le a fájlt a blob-tárolóba, az új storage-fiókban.

1. Lépjen vissza az újonnan létrehozott tárfiókra.
2. Kattintson a **tárolók** hivatkozásra a bal oldalon, a "Blob szolgáltatás."
3. Kattintson a **+ tároló** tetején az oldalon, és a egy "új tároló" panel becsúszik.
4. Adja meg a tároló nevét, válasszon egy hozzáférési szintet, majd kattintson a **OK**. Az oktatóanyag későbbi részében is szolgál a megadott néven. 

    ![Storage-tároló létrehozása](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. Fájl feltöltése a tároló nevére, majd kattintson az újonnan létrehozott tároló **feltöltése**, majd válasszon ki egy fájlt, majd kattintson a **feltöltése**.

    ![Szöveges fájl feltöltése](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

## <a name="grant-your-user-assigned-msi-access-to-an-azure-storage-container"></a>A felhasználó hozzárendelt MSI hozzáférést biztosít az Azure Storage-tárolóba

A kód egy MSI-csomag használatával kérheti le hozzáférési jogkivonatok, amelyek támogatják az Azure AD-hitelesítés erőforrásokhoz történő hitelesítéshez. Ebben az oktatóanyagban az Azure Storage használatához.

Először hozzáférést biztosít az MSI identitás egy Azure Storage-tárolóba. Ebben az esetben használja a korábban létrehozott tároló. Frissítse az értékeket a `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, `<STORAGE ACCOUNT NAME>`, és `<CONTAINER NAME>` a környezetének megfelelő módon. Továbbá cserélje le `<MSI PRINCIPALID>` együtt a `principalId` tulajdonság által visszaadott a `az identity create` parancsot [hozzon létre egy felhasználó által hozzárendelt MSI](#create-a-user-assigned-msi):

```azurecli-interactive
az role assignment create --assignee <MSI PRINCIPALID> --role 'Reader' --scope "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/blobServices/default/containers/<CONTAINER NAME>"
```

A válasz tartalmazza a létrehozott szerepkör-hozzárendelés részletei:

```
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Authorization/roleAssignments/b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "name": "b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "properties": {
    "principalId": "f5fdfdc1-ed84-4d48-8551-999fb9dedfbl",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.storage/storageAccounts/<STORAGE ACCOUNT NAME>/blogServices/default/<CONTAINER NAME>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="get-an-access-token-using-the-user-assigned-msis-identity-and-use-it-to-call-azure-storage"></a>A felhasználó által hozzárendelt MSI-identitással hozzáférési jogkivonatot kapjon és meghívására az Azure Storage

Az oktatóanyag további részében meg kell dolgoznia a korábban létrehozott virtuális gépről.

A lépések elvégzéséhez szüksége van egy SSH-ügyfél. Ha Windows használ, használhatja az SSH-ügyfél a a [a Linux Windows alrendszere](https://msdn.microsoft.com/commandline/wsl/about). Ha az SSH-ügyfél kulcsok konfigurálása segítségre van szüksége, tekintse meg [használata SSH-kulcsok az Azure-ban a Windows hogyan](~/articles/virtual-machines/linux/ssh-from-windows.md), vagy [létrehozása és a nyilvános és titkos ssh-kulcs használata Linux rendszerű virtuális gépekhez az Azure-ban](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).

1. Az Azure Portalon lépjen **virtuális gépek**, a Linux virtuális gépet, majd lépjen a **áttekintése** lapon kattintson **Connect** tetején. Másolja a karakterláncot, a virtuális Géphez való csatlakozáshoz.
2. **Csatlakozás** az SSH-ügyféllel a választott a virtuális géphez. 
3. A terminálablakban a CURL, használatával indítson egy a helyi MSI-végpontot a hozzáférési jogkivonat beszerzése az Azure Storage.

   A CURL-kérést hozzáférési jogkivonat beszerzése az alábbi példában látható. Ne felejtse el `<CLIENT ID>` együtt a `clientId` tulajdonság által visszaadott a `az identity create` parancsot [hozzon létre egy felhasználó által hozzárendelt MSI](#create-a-user-assigned-msi):
   
   ```bash
   curl -H Metadata:true "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fstorage.azure.com/&client_id=<MSI CLIENT ID>" 
   ```

   > [!NOTE]
   > Az előző kérelem értékét a `resource` paraméternek kell lennie az Azure AD által várt pontos egyezést. Az Azure Storage erőforrás-azonosító használata esetén meg kell adnia az URI a záró perjellel.
   > A következő választ access_token elem, kivonatosan csonkolva lettek.

   ```bash
   {"access_token":"eyJ0eXAiOiJ...",
   "refresh_token":"",
   "expires_in":"3599",
   "expires_on":"1504130527",
   "not_before":"1504126627",
   "resource":"https://storage.azure.com",
   "token_type":"Bearer"}
   ```

4. A hozzáférési jogkivonat segítségével eléréséhez az Azure Storage, például a mintafájl, amelyet korábban feltöltött a tároló tartalmának a beolvasása. Cserélje le az értékeket a `<STORAGE ACCOUNT>`, `<CONTAINER NAME>`, és `<FILE NAME>` a korábban megadott értékeket és `<ACCESS TOKEN>` adja vissza az előző lépésben a jogkivonat.

   ```bash
   curl https://<STORAGE ACCOUNT>.blob.core.windows.net/<CONTAINER NAME>/<FILE NAME> -H "x-ms-version: 2017-11-09" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   A válasz tartalmazza a fájl tartalmát:

   ```bash
   Hello world! :)
   ```

## <a name="next-steps"></a>További lépések

- MSI áttekintését lásd: [Felügyeltszolgáltatás-identitás – áttekintés](msi-overview.md).
- Megtudhatja, hogyan teheti ezt a tároló SAS-hitelesítő adatok használatával ugyanezt az oktatóanyagot, tekintse meg a [egy Linux rendszerű virtuális gépek Felügyeltszolgáltatás-identitás segítségével Azure Storage elérése SAS-hitelesítő adatot keresztül](msi-tutorial-linux-vm-access-storage-sas.md)
- Az Azure Storage-fiók SAS funkció kapcsolatos további információkért lásd:
  - [Közös hozzáférésű jogosultságkódok (SAS) használata](~/articles/storage/common/storage-dotnet-shared-access-signature-part-1.md)
  - [A szolgáltatásalapú SAS létrehozása](/rest/api/storageservices/Constructing-a-Service-SAS.md)

Használja a következő megjegyzéseket visszajelzést, és segítsen finomíthatja és a tartalom formázása.





