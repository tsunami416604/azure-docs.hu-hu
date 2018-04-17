---
title: Azure Storage eléréséhez használja a Linux virtuális gép MSI hozzárendelt felhasználó
description: Ez az oktatóanyag bemutatja, hogyan a folyamatot, amely a felhasználó hozzárendelt felügyelt szolgáltatás identitásának (MSI) használata a Linux virtuális gép Azure Storage eléréséhez.
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
ms.openlocfilehash: dd82f1757d9c5a5fc8fb110cc36ec9f4bbd73e8a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="use-a-user-assigned-managed-service-identity-msi-on-a-linux-vm-to-access-azure-storage"></a>A felhasználó által hozzárendelt felügyelt szolgáltatás Identity (MSI) használja a Linux virtuális gép Azure Storage eléréséhez

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Az oktatóanyag bemutatja, hogyan hozzon létre és egy felhasználó által hozzárendelt felügyelt szolgáltatás Identity (MSI) a Linux virtuális gépek, majd használja az Azure Storage eléréséhez. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Hozzon létre egy felhasználó lehet hozzárendelve felügyelt Szolgáltatásidentitás (MSI)
> * Rendelje hozzá a felhasználó által hozzárendelt MSI a Linux virtuális gépek
> * Az MSI hozzáférést biztosíthat az Azure Storage-példányra
> * Szereznie egy hozzáférési jogkivonatot, a felhasználó által hozzárendelt MSI-azonosítójának használatával, és Azure Storage hozzáférhetne

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

Ebben az oktatóanyagban a CLI-parancsfájlt példák futtatásához két lehetőség közül választhat:

- Használjon [Azure Cloud rendszerhéj](~/articles/cloud-shell/overview.md) vagy Azure-portálról, vagy a "próbálja" gombra, keresztül minden kódblokk jobb felső sarkában található.
- [Telepítse a legújabb verziót a CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 vagy újabb verzió) Ha a helyi CLI-konzollal szeretné.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Egy új erőforráscsoportot a Linux virtuális gép létrehozása

Először hozzon létre egy új Linux virtuális Gépet. A meglévő virtuális MSI is engedélyezheti, ha szeretné.

1. Kattintson a **/ hozzon létre új szolgáltatást** gomb az Azure portál bal felső sarkában található.
2. Válassza a **Számítás**, majd az **Ubuntu Server 16.04 LTS** elemet.
3. Adja meg a virtuális gép adatait. A **hitelesítési típus**, jelölje be **nyilvános SSH-kulcs** vagy **jelszó**. A létrehozott hitelesítő adatok lehetővé teszik-e jelentkezni a virtuális gép.

    ![Kép helyettesítő szövege](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Válasszon egy **előfizetés** a virtuális gép meg a legördülő listában.
5. Jelölje be egy új **erőforráscsoport** szeretne létrehozni, válassza a virtuális gép **hozzon létre új**. Amikor végzett, kattintson az **OK** gombra.
6. Adja meg a virtuális gép számára. További méretek megtekintéséhez válasszon **összes** , vagy módosítsa a lemez típusát támogatott szűrő. A Beállítások panelen hagyja változatlanul az alapértelmezett beállításokat, és kattintson az **OK** gombra.

## <a name="create-a-user-assigned-msi"></a>A felhasználó által hozzárendelt MSI-fájl létrehozása

1. A parancssori felület konzol (helyett egy Azure-felhő rendszerhéj munkamenet) használata, ha először jelentkezik be az Azure-bA. Használjon olyan fiókot, amelybe szeretné létrehozni az új MSI Azure-előfizetéssel társított:

    ```azurecli
    az login
    ```

2. Hozzon létre egy felhasználó által hozzárendelt MSI az [az identitás létrehozása](/cli/azure/identity#az_identity_create). A `-g` paraméter határozza meg az erőforráscsoportot, ahol az MSI-fájl jön létre, és a `-n` paraméter határozza meg a nevét. Ügyeljen arra, hogy cserélje le a `<RESOURCE GROUP>` és `<MSI NAME>` paraméterértékeket a saját értékekkel:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```

    A választ a felhasználó által hozzárendelt MSI létrehozott, az alábbi példához hasonló részleteit tartalmazza. Megjegyzés: a `id` értékét az MSI-fájl, a következő lépésben fogja használni:

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

## <a name="assign-your-user-assigned-msi-to-your-linux-vm"></a>A felhasználó által hozzárendelt MSI hozzárendelése a Linux virtuális gép

Egy alapértelmezett MSI eltérően a felhasználó által hozzárendelt MSI több Azure-erőforrások az ügyfelek által használható. Ebben az oktatóanyagban, rendelje hozzá egy virtuális. Is hozzárendelheti az egynél több virtuális géphez.

A felhasználó által hozzárendelt MSI hozzárendelése a Linux virtuális gép [az vm hozzárendelése-identitás](/cli/azure/vm#az_vm_assign_identity). Ügyeljen arra, hogy cserélje le a `<RESOURCE GROUP>` és `<VM NAME>` paraméterértékeket a saját értékekkel. Használja a `id` tulajdonságot adott vissza az előző lépésben a `--identities` paraméter értékét:

```azurecli-interactive
az vm assign-identity -g <RESOURCE GROUP> -n <VM NAME> --identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>"
```

## <a name="create-a-storage-account"></a>Create a storage account 

Ha még nem rendelkezik egy, a most hozzon létre egy tárfiókot. Is ezt a lépést kihagyhatja, és használja a meglévő tárfiókot, ha szeretné. 

1. Kattintson a **/ hozzon létre új szolgáltatást** gomb az Azure portál bal felső sarkában található.
2. Kattintson a **tárolási**, majd **Tárfiók**, és egy új, "Create a storage-fiók" panel jeleníti meg.
3. Adjon meg egy **neve** a tárfiók, amelyet később használhat.  
4. **Telepítési modell** és **fiók kind** meg "Erőforrás-kezelő" és "Általános célú", illetve. 
5. Győződjön meg arról a **előfizetés** és **erőforráscsoport** az előző lépésben a virtuális gép létrehozásakor megadott megfelelően.
6. Kattintson a **Create** (Létrehozás) gombra.

    ![Új tárfiók létrehozása](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>A tárfiók egy blob-tároló létrehozása

Mivel a fájlok a blob storage igényel, a blob-tároló, amely tárolja a fájl létrehozásához szükséges. Ezután töltse fel, és töltse le a fájlt a blob-tárolóhoz, az új tárfiókot.

1. Lépjen vissza az újonnan létrehozott tárfiók.
2. Kattintson a **tárolók** hivatkozásra a bal oldali "Blob szolgáltatásban."
3. Kattintson a **+ tároló** tetején a lap és az "új tároló" panel diák ki.
4. Nevezze el a tároló, válassza ki a hozzáférési szintet, majd kattintson a **OK**. A megadott név is szolgál az oktatóanyag későbbi részében. 

    ![A tároló létrehozása](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. Fájl feltöltése a tároló neve, majd kattintson az újonnan létrehozott tároló **feltöltése**, majd válassza ki a fájlt, majd kattintson a **feltöltése**.

    ![Szöveg-fájl feltöltése](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

## <a name="grant-your-user-assigned-msi-access-to-an-azure-storage-container"></a>A felhasználó hozzárendelt MSI hozzáférést biztosíthat az Azure Storage-tároló

Használatával egy olyan MSI Csomaghoz, a kódot, hogy erőforrásokat, amelyek támogatják az Azure AD-alapú hitelesítés hitelesítést hozzáférési jogkivonatok kérheti le. Ebben az oktatóanyagban használhatja az Azure Storage.

Először egy Azure Storage-tárolóhoz MSI identitás hozzáférést engedélyez. Ebben az esetben használhatja a korábban létrehozott tároló. Frissítse az értékeket a `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, `<STORAGE ACCOUNT NAME>`, és `<CONTAINER NAME>` a környezetnek megfelelően. Továbbá cserélje le `<MSI PRINCIPALID>` rendelkező a `principalId` tulajdonság által visszaadott a `az identity create` parancsot [hozzon létre egy felhasználó által hozzárendelt MSI](#create-a-user-assigned-msi):

```azurecli-interactive
az role assignment create --assignee <MSI PRINCIPALID> --role 'Reader' --scope "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/blobServices/default/containers/<CONTAINER NAME>"
```

A válasz tartalmazza a szerepkör-hozzárendelés létrehozása a részletei:

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

## <a name="get-an-access-token-using-the-user-assigned-msis-identity-and-use-it-to-call-azure-storage"></a>Szereznie egy hozzáférési jogkivonatot, a felhasználó által hozzárendelt MSI-azonosítójának használatával, és hívja az Azure Storage segítségével

Az oktatóanyag a hátralévő kell a korábban létrehozott virtuális gép dolgozhassanak.

Lépések elvégzéséhez szüksége van egy SSH-ügyfél. Windows használ, ha az SSH-ügyfél a használhatja a [Linux rendszerhez készült Windows alrendszer](https://msdn.microsoft.com/commandline/wsl/about). Ha az SSH-ügyfél kulcsok konfigurálása segítségre van szüksége, tekintse meg [a Windows Azure használatára SSH-kulcsok hogyan](~/articles/virtual-machines/linux/ssh-from-windows.md), vagy [létrehozása, és az SSH nyilvános és titkos kulcsból álló kulcspárt használata a Linux virtuális gépek Azure-ban](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).

1. Az Azure-portálon lépjen a **virtuális gépek**, keresse fel a Linux virtuális gépet, majd a a **áttekintése** kattintson **Connect** tetején. Másolja a karakterláncot, amellyel a virtuális Géphez csatlakozik.
2. **Csatlakozás** a virtuális géphez a az SSH-ügyfél az Ön által választott. 
3. A Terminálszolgáltatások ablakban CURL, használatával indítson egy lekérdezést a helyi MSI végpontot egy hozzáférési jogkivonat beszerzése az Azure Storage.

   A CURL kérelem olyan hozzáférési jogkivonatot szerezni az alábbi példában látható. Ügyeljen arra, hogy a csere `<CLIENT ID>` rendelkező a `clientId` tulajdonság által visszaadott a `az identity create` parancsot [hozzon létre egy felhasználó által hozzárendelt MSI](#create-a-user-assigned-msi):
   
   ```bash
   curl -H Metadata:true "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fstorage.azure.com/&client_id=<MSI CLIENT ID>" 
   ```

   > [!NOTE]
   > Az előző kérelem értékének a `resource` paraméter az Azure AD által várt pontosan egyeznie kell. Az Azure Storage erőforrás-azonosító használata esetén meg kell adni a záró perjelet URI-n.
   > A következő reagálva a access_token elem szerint lettek rövidítve kivonatosan mutatja.

   ```bash
   {"access_token":"eyJ0eXAiOiJ...",
   "refresh_token":"",
   "expires_in":"3599",
   "expires_on":"1504130527",
   "not_before":"1504126627",
   "resource":"https://storage.azure.com",
   "token_type":"Bearer"}
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

- MSI áttekintését lásd: [Szolgáltatásidentitás felügyelete – áttekintés](msi-overview.md).
- Ez a tároló SAS-hitelesítő adatok használatával ugyanezt az oktatóanyagot módjáról további tudnivalókért lásd: [egy Linux virtuális gép felügyelt szolgáltatás identitás használatával Azure Storage érhetnek el egy SAS-hitelesítő adat](msi-tutorial-linux-vm-access-storage-sas.md)
- Az Azure Storage-fiók SAS szolgáltatás kapcsolatos további információkért lásd:
  - [Közös hozzáférésű jogosultságkód (SAS) használatával](~/articles/storage/common/storage-dotnet-shared-access-signature-part-1.md)
  - [A szolgáltatásalapú SAS létrehozása](/rest/api/storageservices/Constructing-a-Service-SAS.md)

Az alábbi Megjegyzések szakasz segítségével visszajelzést, és segítsen pontosítsa és a tartalom.





