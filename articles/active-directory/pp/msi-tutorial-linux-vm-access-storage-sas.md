---
title: Linux rendszerű virtuális gép MSI használata az Azure Storage-ban egy SAS-hitelesítő adatot eléréséhez
description: Ez az oktatóanyag bemutatja, hogyan egy Linux rendszerű virtuális gépek Felügyeltszolgáltatás-identitás (MSI) Azure Storage helyett a tárfiók hozzáférési kulcsát egy SAS-hitelesítő adatok használatával elérésére használhat.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: fc7c5b4ab025666fc7fa1d9073198ec90d8e71c3
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38611027"
---
# <a name="use-a-linux-vm-managed-service-identity-to-access-azure-storage-via-a-sas-credential"></a>Azure Storage elérése SAS-hitelesítő adatot keresztül egy Linux rendszerű virtuális gépek Felügyeltszolgáltatás-identitás segítségével

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Ez az oktatóanyag bemutatja, hogyan lehet engedélyezni a Felügyeltszolgáltatás-identitás (MSI) egy Linux rendszerű virtuális gép esetében, akkor az MSI használata egy tároló közös hozzáférésű Jogosultságkód (SAS) hitelesítő adatok beszerzése. Pontosabban a [szolgáltatásalapú SAS-hitelesítő adatok](~/articles/storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-shared-access-signatures). 

A szolgáltatás SAS lehetővé teszi a tárfiókban tárolt objektumok korlátozott hozzáférés adható adott időtartamra és a egy adott szolgáltatás (ebben az esetben a blob szolgáltatás), anélkül, hogy a fiók hozzáférési kulcsára. Használhatja a szokásos módon ha ezzel a tárolási műveletek, például amikor a Storage SDK használatával egy SAS-hitelesítő adatot. Ebben az oktatóanyagban bemutatjuk a fel- és Azure Storage CLI-vel blob letöltése. Megtudhatja, hogyan lehet:


> [!div class="checklist"]
> * Linux rendszerű virtuális gépen az MSI engedélyezéséhez 
> * A virtuális gép hozzáférést biztosítani a tárfiók SAS a Resource Managerben 
> * A virtuális gép identitással hozzáférési jogkivonatot kapjon, és a SAS lekérése a Resource Manager használatával 

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.


## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Linux rendszerű virtuális gép létrehozása az új erőforráscsoport

Ebben az oktatóanyagban létrehozunk egy új Linux rendszerű virtuális Gépet. A meglévő virtuális gép MSI is engedélyezheti.

1. Kattintson a **+/ hozzon létre új szolgáltatást** az Azure portal bal felső sarkában található gomb.
2. Válassza a **Számítás**, majd az **Ubuntu Server 16.04 LTS** elemet.
3. Adja meg a virtuális gép adatait. A **hitelesítési típus**válassza **nyilvános SSH-kulcs** vagy **jelszó**. A létrehozott hitelesítő adatok lehetővé teszik jelentkezzen be a virtuális Gépre.

    ![Kép ALT szövege](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Válasszon egy **előfizetés** a virtuális gép a legördülő listában.
5. Jelölje be egy új **erőforráscsoport** szeretne létrehozni, válassza ki a virtuális gép **hozzon létre új**. Amikor végzett, kattintson az **OK** gombra.
6. Válassza ki a méretet a virtuális gép számára. További méretek megjelenítéséhez válassza ki a **az összes megtekintése** vagy a lemez típusát támogatott szűrő módosítása. A Beállítások panelen hagyja változatlanul az alapértelmezett beállításokat, és kattintson az **OK** gombra.

## <a name="enable-msi-on-your-vm"></a>A virtuális Gépen az MSI engedélyezéséhez

A virtuális gép MSI lehetővé teszi hozzáférési tokenek beszerzése az Azure ad-ből anélkül, hogy hitelesítő adatok üzembe a kódot kellene. Valójában MSI engedélyezéséhez két dolgot eredményez: a virtuális Gépen az MSI-Virtuálisgép-bővítmény telepítése és a Felügyeltszolgáltatás-identitás lehetővé teszi a virtuális gép számára.  

1. Keresse meg az erőforráscsoport, az új virtuális gépet, és válassza ki a virtuális gépet az előző lépésben létrehozott.
2. Kattintson a virtuális gép a bal oldalon a "beállítások" **konfigurációs**.
3. Regisztráljon, és az MSI engedélyezéséhez, válassza ki a **Igen**, ha szeretné letiltani, válassza a nem.
4. Győződjön meg arról, hogy kattintson **mentése** a konfiguráció mentéséhez.

    ![Kép ALT szövege](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Ha ellenőrizni szeretné milyen kiterjesztésű vannak-e a virtuális gépre, kattintson a **bővítmények**. Ha az MSI engedélyezve van, a **ManagedIdentityExtensionforLinux** megjelenik a listában.

    ![Kép ALT szövege](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="create-a-storage-account"></a>Tárfiók létrehozása 

Ha még nem rendelkezik egy, most már létrehozhat egy storage-fiókot.  Kihagyhatja ezt a lépést is, és a virtuális gép MSI hozzáférést a kulcsokhoz, egy meglévő tárfiókot. 

1. Kattintson a **+/ hozzon létre új szolgáltatást** az Azure portal bal felső sarkában található gomb.
2. Kattintson a **tárolási**, majd **Tárfiók**, és a egy új "Tárfiók létrehozása" panelen jelenik meg.
3. Adjon meg egy **neve** a tárfiók, amely később fogja használni.  
4. **Üzemi modell** és **fióktípus** értékre kell állítani "Erőforrás-kezelő" és "Általános célú", illetve. 
5. Győződjön meg, hogy a **előfizetés** és **erőforráscsoport** egyeznek azokkal az előző lépésben a virtuális gép létrehozásakor megadott.
6. Kattintson a **Create** (Létrehozás) gombra.

    ![Új tárfiók létrehozása](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Hozzon létre egy blobtárolót a tárfiókban

Hogy később feltöltése és fájl letöltése az új tárfiókot. Mivel a fájlok van szüksége a blob storage-ba, hozzon létre egy blobtárolót a fájl tárolására kell.

1. Lépjen vissza az újonnan létrehozott tárfiókra.
2. Kattintson a **tárolók** hivatkozásra a bal oldali panelen, a "Blob szolgáltatás."
3. Kattintson a **+ tároló** tetején az oldalon, és a egy "új tároló" panel becsúszik.
4. Adja meg a tároló nevét, válasszon egy hozzáférési szintet, majd kattintson a **OK**. A megadott név lesz az oktatóanyag későbbi részében. 

    ![Storage-tároló létrehozása](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-msi-access-to-use-a-storage-sas"></a>Hozzáférést biztosít a virtuális gép MSI használata egy tároló SAS 

Az Azure Storage nem támogatja natív módon az Azure AD-hitelesítés.  Azonban a tároló SAS lekérése a Resource Manager egy MSI-csomag használatával, majd az SAS tároló eléréséhez.  Ebben a lépésben a virtuális gép MSI hozzáférést adhat a tárfiók SAS.   

1. Lépjen vissza az újonnan létrehozott tárfiók...   
2. Kattintson a **hozzáférés-vezérlés (IAM)** hivatkozásra a bal oldali panelen.  
3. Kattintson a **+ Hozzáadás** az oldalra egy új szerepkör-hozzárendelés hozzáadása a virtuális gép felett
4. Állítsa be **szerepkör** a "Tárfiók-közreműködő", az oldal jobb oldalán. 
5. Állítsa be a következő legördülő **rendelhet hozzáféréseket** az erőforrás "Virtuális gép".  
6. Következő lépésként győződjön meg arról, a megfelelő előfizetés szerepel-e **előfizetés** legördülő menüből, majd állítsa be **erőforráscsoport** "Minden erőforráscsoportok".  
7. Végül **kiválasztása** a legördülő listában válassza ki a Linuxos virtuális géphez, majd kattintson a **mentése**.  

    ![Kép ALT szövege](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/msi-storage-role-sas.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>A virtuális gép identitással hozzáférési jogkivonatot kapjon, és hívja az Azure Resource Manager használatával

Az oktatóanyag további részében hogy működik a korábban létrehozott virtuális gépről.

A lépések elvégzéséhez szüksége lesz egy SSH-ügyfél. Ha Windows használ, használhatja az SSH-ügyfél a a [a Linux Windows alrendszere](https://msdn.microsoft.com/commandline/wsl/install_guide). Ha az SSH-ügyfél kulcsok konfigurálása segítségre van szüksége, tekintse meg [használata SSH-kulcsok az Azure-ban a Windows hogyan](~/articles/virtual-machines/linux/ssh-from-windows.md), vagy [létrehozása és a nyilvános és titkos ssh-kulcs használata Linux rendszerű virtuális gépekhez az Azure-ban](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).

1. Az Azure Portalon lépjen **virtuális gépek**, a Linux virtuális gépet, majd lépjen a **áttekintése** lapon kattintson **Connect** tetején. Másolja a karakterláncot, a virtuális Géphez való csatlakozáshoz. 
2. A virtuális gépet az SSH-ügyfél csatlakozhat.  
3. Ezután kéri, adja meg a **jelszó** hozzáadott létrehozásakor a **Linux rendszerű virtuális gép**. Meg kell majd lennie sikeresen bejelentkezett.  
4. Hozzáférési jogkivonat beszerzése az Azure Resource Manager a CURL használatával.  

    A CURL kérés- és a hozzáférési jogkivonat nem éri el:
    
    ```bash
    curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true    
    ```
    
    > [!NOTE]
    > Az előző kérelem az "erőforrás" paraméter értéke az Azure AD által várt pontos egyezésűnek kell lennie. Az Azure Resource Manager erőforrás-azonosító használata esetén meg kell adnia az URI a záró perjellel.
    > A következő választ access_token elem, kivonatosan csonkolva lettek.
    
    ```bash
    {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"} 
     ```

## <a name="get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls"></a>SAS-hitelesítő adatot az Azure Resource Manager-tároló hívásokhoz beolvasása

Most már a CURL használatával hívja meg az előző szakaszban azt lekérése hozzáférési token használatával-tároló SAS-hitelesítő adat létrehozása a Resource Manager. Ha már rendelkezünk a SAS-hitelesítő adatot, storage feltöltési/letöltési műveletek is nevezzük.

A kérelem használjuk a kövesse HTTP-kérelem paramétereit a SAS-hitelesítő adat létrehozása:

```JSON
{
    "canonicalizedResource":"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>",
    "signedResource":"c",              // The kind of resource accessible with the SAS, in this case a container (c).
    "signedPermission":"rcw",          // Permissions for this SAS, in this case (r)ead, (c)reate, and (w)rite.  Order is important.
    "signedProtocol":"https",          // Require the SAS be used on https protocol.
    "signedExpiry":"<EXPIRATION TIME>" // UTC expiration time for SAS in ISO 8601 format, for example 2017-09-22T00:06:00Z.
}
```

Ezeket a paramétereket a SAS-hitelesítő adatok kérése bejegyzés törzse tartalmazza. A paramétereket egy SAS-hitelesítő adat létrehozása a további információkért lásd: a [lista szolgáltatás SAS REST-referencia](/rest/api/storagerp/storageaccounts/listservicesas).

A SAS-hitelesítő adatot használja a következő CURL-kérelmet. Ne felejtse el a `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, `<STORAGE ACCOUNT NAME>`, `<CONTAINER NAME>`, és `<EXPIRATION TIME>` paraméterértékeket a saját értékeire. Cserélje le a `<ACCESS TOKEN>` értékét a korábban kapott hozzáférési jogkivonattal:

```bash 
curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/listServiceSas/?api-version=2017-06-01 -X POST -d "{\"canonicalizedResource\":\"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>\",\"signedResource\":\"c\",\"signedPermission\":\"rcw\",\"signedProtocol\":\"https\",\"signedExpiry\":\"<EXPIRATION TIME>\"}" -H "Authorization: Bearer <ACCESS TOKEN>"
```

> [!NOTE]
> Az előzetes URL-címben a szöveg megkülönbözteti a kis-és nagybetűket, ezért győződjön meg arról, ha a felső-kisbetűk, az erőforráscsoportokra vonatkozó használ, ennek megfelelően. Ezenkívül fontos, hogy ez egy POST-kérés nem egy GET kéréssel.

A CURL-válasz a SAS-hitelesítő adatot adja vissza:  

```bash 
{"serviceSasToken":"sv=2015-04-05&sr=c&spr=https&st=2017-09-22T00%3A10%3A00Z&se=2017-09-22T02%3A00%3A00Z&sp=rcw&sig=QcVwljccgWcNMbe9roAJbD8J5oEkYoq%2F0cUPlgriBn0%3D"} 
```

Hozzon létre egy mintafájlt blob feltöltése a blob storage-tárolóba. A Linux rendszerű virtuális gép ehhez a következő paranccsal. 

```bash
echo "This is a test file." > test.txt
```

Ezután a CLI-vel hitelesítéséhez `az storage` parancsának használatával a SAS-hitelesítő adatot, és töltse fel a fájlt a blobtárolóba. Ehhez a lépéshez szüksége lesz [telepítse az Azure CLI legújabb](https://docs.microsoft.com/cli/azure/install-azure-cli) a virtuális Gépen, ha még nem tette.

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

Ezenkívül, letöltheti a fájlt az Azure CLI-vel, és a SAS-hitelesítő adatot való hitelesítés közben. 

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

- MSI áttekintését lásd: [Felügyeltszolgáltatás-identitás – áttekintés](msi-overview.md).
- Megtudhatja, hogyan teheti ezt ugyanezt az oktatóanyagot tárfiókkulcs használatával, lásd: [egy Linux rendszerű virtuális gépek Felügyeltszolgáltatás-identitás használata az Azure Storage eléréséhez](msi-tutorial-linux-vm-access-storage.md)
- Az Azure Storage-fiók SAS funkció kapcsolatos további információkért lásd:
  - [Közös hozzáférésű jogosultságkódok (SAS) használata](~/articles/storage/common/storage-dotnet-shared-access-signature-part-1.md)
  - [A szolgáltatásalapú SAS létrehozása](/rest/api/storageservices/Constructing-a-Service-SAS.md)

Használja a következő megjegyzéseket visszajelzést, és segítsen finomíthatja és a tartalom formázása.
