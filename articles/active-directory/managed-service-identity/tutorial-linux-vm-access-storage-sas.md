---
title: A Linux virtuális gép MSI SAS hitelesítő adat segítségével Azure Storage eléréséhez használja
description: Ez az oktatóanyag bemutatja, hogyan egy Linux virtuális gép felügyelt szolgáltatás Identity (MSI) Azure Storage egy SAS-hitelesítő adat használata helyett a fiók tárelérési kulcs elérésére használhat.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: db65d53258af523bb74209613df3af23e890f49d
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="use-a-linux-vm-managed-service-identity-to-access-azure-storage-via-a-sas-credential"></a>Linux virtuális gép felügyelt Szolgáltatásidentitás segítségével Azure Storage érhetnek el egy SAS-hitelesítő adat

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Az oktatóanyag bemutatja, hogyan engedélyezése felügyelt szolgáltatás identitásának (MSI) a Linux virtuális gép, majd használja az MSI storage közös hozzáférésű Jogosultságkód (SAS) hitelesítő adatok beszerzése. Pontosabban a [szolgáltatás SAS hitelesítőadat-](/azure/storage/common/storage-dotnet-shared-access-signature-part-1?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-shared-access-signatures). 

A szolgáltatás SAS lehetővé teszi a hozzáférést korlátozott a tárfiókban lévő objektumok adott időtartamra és egy adott szolgáltatáshoz (ebben az esetben a blob szolgáltatás), anélkül, hogy egy fiók hozzáférési kulcsot. Használhatja a szokásos módon esetén a következőnek tárolási műveletek, például amikor a Storage szolgáltatás SDK használatával SAS hitelesítő adatokat. Ebben az oktatóanyagban bemutatjuk a fel- és Azure Storage parancssori felület használatával blob letöltéséhez. Megtudhatja, hogyan:


> [!div class="checklist"]
> * A Linux virtuális gépek MSI engedélyezése 
> * A virtuális gép hozzáférést biztosíthat a tárfiók SAS az erőforrás-kezelőben 
> * Szereznie egy hozzáférési jogkivonatot, a virtuális gép azonosítójának használatával, és a SAS lekérni az erőforrás-kezelő használatával 

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.


## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Egy új erőforráscsoportot a Linux virtuális gép létrehozása

Ebben az oktatóanyagban létrehozhatunk egy új Linux virtuális Gépet. A meglévő virtuális MSI is engedélyezheti.

1. Kattintson a **/ hozzon létre új szolgáltatást** gomb az Azure portál bal felső sarkában található.
2. Válassza a **Számítás**, majd az **Ubuntu Server 16.04 LTS** elemet.
3. Adja meg a virtuális gép adatait. A **hitelesítési típus**, jelölje be **nyilvános SSH-kulcs** vagy **jelszó**. A létrehozott hitelesítő adatok lehetővé teszik-e jelentkezni a virtuális gép.

    ![Kép helyettesítő szövege](../media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Válasszon egy **előfizetés** a virtuális gép meg a legördülő listában.
5. Jelölje be egy új **erőforráscsoport** szeretne létrehozni, válassza a virtuális gép **hozzon létre új**. Amikor végzett, kattintson az **OK** gombra.
6. Adja meg a virtuális gép számára. További méretek megtekintéséhez válasszon **összes** , vagy módosítsa a lemez típusát támogatott szűrő. A Beállítások panelen hagyja változatlanul az alapértelmezett beállításokat, és kattintson az **OK** gombra.

## <a name="enable-msi-on-your-vm"></a>A virtuális Gépen lévő MSI engedélyezése

A virtuális gép MSI hozzáférési jogkivonatok beolvasása az Azure AD meg szeretne adni a kód hitelesítő adatokat igénylő nélkül teszi lehetővé. Engedélyezése felügyelt Szolgáltatásidentitás a virtuális gép, két dolgot eredményez: regiszterekben az Azure Active Directory segítségével felügyelt identitását, és hozzon létre a virtuális gép identitásának konfigurálja a virtuális Gépen. 

1. Keresse meg az erőforráscsoport a új virtuális gép, és válassza ki a virtuális gépet, az előző lépésben létrehozott.
2. Kattintson a virtuális gép "Beállítások" a bal oldali **konfigurációs**.
3. Regisztrálja, és engedélyezze a MSI-t, jelölje be **Igen**, ha szeretné letiltani, válassza a nem.
4. Győződjön meg arról, hogy kattintson **mentése** a konfiguráció mentéséhez.

    ![Kép helyettesítő szövege](../media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

## <a name="create-a-storage-account"></a>Create a storage account 

Ha még nem rendelkezik egy, most létrehoz egy tárfiókot.  Is ezt a lépést kihagyhatja, és a virtuális gép MSI hozzáférést biztosíthat a meglévő tárfiók kulcsait. 

1. Kattintson a **/ hozzon létre új szolgáltatást** gomb az Azure portál bal felső sarkában található.
2. Kattintson a **tárolási**, majd **Tárfiók**, és egy új, "Create a storage-fiók" panelen jelenik meg.
3. Adjon meg egy **neve** a tárfiók, amelyek később fogja használni.  
4. **Telepítési modell** és **fiók kind** meg "Erőforrás-kezelő" és "Általános célú", illetve. 
5. Győződjön meg arról a **előfizetés** és **erőforráscsoport** az előző lépésben a virtuális gép létrehozásakor megadott megfelelően.
6. Kattintson a **Create** (Létrehozás) gombra.

    ![Új tárfiók létrehozása](../media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>A tárfiók egy blob-tároló létrehozása

Később rendszer feltöltése és töltse le a fájlt az új tárfiókot. Mivel a szükséges blob-tároló fájlok, igazolnia kell a fájl tárolására blob tárolókat hozhat létre.

1. Lépjen vissza az újonnan létrehozott tárfiók.
2. Kattintson a **tárolók** hivatkozásra a bal oldali panelen kattintson a "Blob szolgáltatás."
3. Kattintson a **+ tároló** tetején a lap és az "új tároló" panel diák ki.
4. Nevezze el a tároló, válassza ki a hozzáférési szintet, majd kattintson a **OK**. A megadott név az oktatóanyag későbbi részében fogja használni. 

    ![A tároló létrehozása](../media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-msi-access-to-use-a-storage-sas"></a>A virtuális gép MSI hozzáférést egy SAS tárolók használatához 

Az Azure Storage natív módon támogatja az Azure AD-alapú hitelesítés.  Azonban a tároló SAS lekérése a Resource Manager egy olyan MSI Csomaghoz használatával, majd a SAS használatával férjenek hozzá a tároló.  Ebben a lépésben megadta a virtuális gép MSI hozzáférést a tárfiók SAS.   

1. Lépjen vissza az újonnan létrehozott tárfiók...   
2. Kattintson a **hozzáférés-vezérlés (IAM)** hivatkozás a bal oldali panelen.  
3. Kattintson a **+ Hozzáadás** fölött a lap egy új szerepkör-hozzárendelés hozzáadása a virtuális gép számára
4. Állítsa be **szerepkör** "Tárolási fiók munkatárs", a lap jobb oldalán. 
5. A következő legördülő menüből, állítson be **való hozzáférés hozzárendelése** az erőforrás "Virtuális gép".  
6. A következő szerepel-e a megfelelő előfizetést a **előfizetés** legördülő menüből, majd állítsa be **erőforráscsoport** "Az összes erőforráscsoport" számára.  
7. Végezetül a **válasszon** meg a legördülő listában válassza ki a Linux virtuális gép, majd kattintson az **mentése**.  

    ![Kép helyettesítő szövege](../media/msi-tutorial-linux-vm-access-storage/msi-storage-role-sas.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Szereznie egy hozzáférési jogkivonatot, a virtuális gép azonosítójának használatával, és hívja az Azure Resource Manager használatával

Az oktatóanyag a hátralévő azt fog működni a korábban létrehozott virtuális gépről.

Ezek a lépések elvégzéséhez szüksége lesz egy SSH-ügyfél. Windows használ, ha az SSH-ügyfél a használhatja a [Linux rendszerhez készült Windows alrendszer](https://msdn.microsoft.com/commandline/wsl/install_guide). Ha az SSH-ügyfél kulcsok konfigurálása segítségre van szüksége, tekintse meg [a Windows Azure használatára SSH-kulcsok hogyan](../../virtual-machines/linux/ssh-from-windows.md), vagy [létrehozása, és az SSH nyilvános és titkos kulcsból álló kulcspárt használata a Linux virtuális gépek Azure-ban](../../virtual-machines/linux/mac-create-ssh-keys.md).

1. Az Azure-portálon lépjen a **virtuális gépek**, keresse fel a Linux virtuális gépet, majd a a **áttekintése** kattintson **Connect** tetején. Másolja a karakterláncot, amellyel a virtuális Géphez csatlakozik. 
2. Csatlakoztassa a virtuális Gépet az SSH-ügyfél használatával.  
3. A következő kéri be a **jelszó** létrehozásakor hozzáadta a **Linux virtuális gép**. Meg kell majd lehet sikeres volt.  
4. CURL használatával szerezze be a hozzáférési tokent az Azure Resource Manager.  

    A CURL kérés- és a hozzáférési token nem éri el:
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true    
    ```
    
    > [!NOTE]
    > Az előző kérelem a "resource" paraméter értékének az Azure AD által várt pontosan egyeznie kell. Az Azure Resource Manager erőforrás-azonosító használata esetén meg kell adni a záró perjelet URI-n.
    > A következő reagálva a access_token elem szerint lettek rövidítve kivonatosan mutatja.
    
    ```bash
    {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"} 
     ```

## <a name="get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls"></a>SAS hitelesítő adatokat az Azure Resource Manager tárolási hívásokat beolvasása

Mostantól a CURL használatával hívja az erőforrás-kezelőt a hozzáférési jogkivonat azt lekérése az előző szakaszban tároló SAS-hitelesítő adat létrehozása. Tudunk a SAS-hitelesítő adatokat, miután tárolási feltöltés/letöltés műveletet is nevezzük.

A kérelem a SAS-hitelesítő adat létrehozása a következő HTTP kérelemben szereplő paraméterek használjuk:

```JSON
{
    "canonicalizedResource":"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>",
    "signedResource":"c",              // The kind of resource accessible with the SAS, in this case a container (c).
    "signedPermission":"rcw",          // Permissions for this SAS, in this case (r)ead, (c)reate, and (w)rite.  Order is important.
    "signedProtocol":"https",          // Require the SAS be used on https protocol.
    "signedExpiry":"<EXPIRATION TIME>" // UTC expiration time for SAS in ISO 8601 format, for example 2017-09-22T00:06:00Z.
}
```

Ezek a paraméterek szerepelnek a FELADÁS egy vagy több szervezet SAS hitelesítési kérelem. A paraméterek egy SAS-hitelesítő adatok létrehozására vonatkozó további információkért lásd: a [lista szolgáltatás SAS REST-referencia](/rest/api/storagerp/storageaccounts/listservicesas).

Használja a következő CURL-kérelmet a SAS-hitelesítő adat. Ügyeljen arra, hogy cserélje le a `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, `<STORAGE ACCOUNT NAME>`, `<CONTAINER NAME>`, és `<EXPIRATION TIME>` paraméterértékeket a saját értékekkel. Cserélje le a `<ACCESS TOKEN>` a korábban kapott hozzáférési jogkivonat értéket:

```bash 
curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/listServiceSas/?api-version=2017-06-01 -X POST -d "{\"canonicalizedResource\":\"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>\",\"signedResource\":\"c\",\"signedPermission\":\"rcw\",\"signedProtocol\":\"https\",\"signedExpiry\":\"<EXPIRATION TIME>\"}" -H "Authorization: Bearer <ACCESS TOKEN>"
```

> [!NOTE]
> Az előzetes URL-címben a szöveg kis-és nagybetűket, ezért győződjön meg arról, ha ennek megfelelően igazodjon felső-kisbetűk az erőforráscsoportok használ. Ezenkívül fontos, hogy ez egy POST kérést nem GET kérés.

A CURL választ ad vissza a SAS-hitelesítő adat:  

```bash 
{"serviceSasToken":"sv=2015-04-05&sr=c&spr=https&st=2017-09-22T00%3A10%3A00Z&se=2017-09-22T02%3A00%3A00Z&sp=rcw&sig=QcVwljccgWcNMbe9roAJbD8J5oEkYoq%2F0cUPlgriBn0%3D"} 
```

Hozzon létre egy minta blob fájlt tölthet fel a blob storage tárolót. A Linux virtuális gép ehhez a következő paranccsal. 

```bash
echo "This is a test file." > test.txt
```

A következő hitelesítik magukat a CLI `az storage` parancs SAS hitelesítő adatokkal, és a fájl feltöltése a blob-tárolóhoz. Ebben a lépésben szüksége lesz a [a legújabb Azure parancssori felület telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli) a virtuális gépen, ha még nem tette meg.

```azurecli-interactive
 az storage blob upload --container-name 
                        --file 
                        --name
                        --account-name 
                        --sas-token
```

Válasz: 

```JSON
Finished[#############################################################]  100.0000%
{
  "etag": "\"0x8D4F9929765C139\"",
  "lastModified": "2017-09-21T03:58:56+00:00"
}
```

Emellett letöltheti a fájlt az Azure parancssori felület használatával, és a SAS-hitelesítő adat hitelesítése. 

A kérelem: 

```azurecli-interactive
az storage blob download --container-name
                         --file 
                         --name 
                         --account-name
                         --sas-token
```

Válasz: 

```JSON
{
  "content": null,
  "metadata": {},
  "name": "testblob",
  "properties": {
    "appendBlobCommittedBlockCount": null,
    "blobType": "BlockBlob",
    "contentLength": 16,
    "contentRange": "bytes 0-15/16",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentMd5": "Aryr///Rb+D8JQ8IytleDA==",
      "contentType": "text/plain"
    },
    "copy": {
      "completionTime": null,
      "id": null,
      "progress": null,
      "source": null,
      "status": null,
      "statusDescription": null
    },
    "etag": "\"0x8D4F9929765C139\"",
    "lastModified": "2017-09-21T03:58:56+00:00",
    "lease": {
      "duration": null,
      "state": "available",
      "status": "unlocked"
    },
    "pageBlobSequenceNumber": null,
    "serverEncrypted": false
  },
  "snapshot": null
}
```

## <a name="next-steps"></a>További lépések

- MSI áttekintését lásd: [Szolgáltatásidentitás felügyelete – áttekintés](overview.md).
- Hogyan hajtsa végre ugyanezt az oktatóanyagot használata a tárfiók kulcsára, lásd: [elérni az Azure Storage szolgáltatás Linux virtuális gép felügyelt identitás használatára](tutorial-linux-vm-access-storage.md)
- Az Azure Storage-fiók SAS szolgáltatás kapcsolatos további információkért lásd:
  - [Közös hozzáférésű jogosultságkód (SAS) használatával](/azure/storage/common/storage-dotnet-shared-access-signature-part-1.md)
  - [A szolgáltatásalapú SAS létrehozása](/rest/api/storageservices/Constructing-a-Service-SAS.md)

Az alábbi Megjegyzések szakasz segítségével visszajelzést, és segítsen pontosítsa és a tartalom.
