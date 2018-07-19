---
title: Linux virtuális gép MSI identitásának használata az Azure Storage eléréséhez SAS-hitelesítő adatokkal
description: Oktatóanyag, amely bemutatja, hogyan használhatja a Linux virtuális gép MSI-identitását az Azure Storage elérésére a tárelérési kulcs helyett SAS-hitelesítő adatok használatával.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: adf3df6dd9163ef40b4f953c07fce6a18b5ab30f
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39044274"
---
# <a name="tutorial-use-a-linux-vm-managed-service-identity-to-access-azure-storage-via-a-sas-credential"></a>Oktatóanyag: Az Azure Storage elérése SAS-hitelesítő adatok használatával Linux VM-beli felügyeltszolgáltatás-identitással

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Az oktatóanyag bemutatja, hogyan engedélyezheti a felügyeltszolgáltatás-identitást (MSI) egy Linux virtuális gépen, majd hogyan használhatja ezt az identitást egy tároló közös hozzáférésű jogosultságkódú (SAS) hitelesítő adatai lekérésére. Kifejezetten [szolgáltatási SAS-hitelesítő adatok](/azure/storage/common/storage-dotnet-shared-access-signature-part-1?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-shared-access-signatures) lekérésére. 

A szolgáltatási SAS segítségével korlátozott hozzáférési lehetőséget biztosíthat a tárfiókjában található objektumokhoz, korlátozott időre és egy adott szolgáltatáshoz (esetünkben egy Blob szolgáltatáshoz) anélkül, hogy ki kellene adnia a tárfiók hozzáférési kulcsát. A SAS-hitelesítő adatokat a szokásos módon használhatja a tárolási műveletek során, például a Storage SDK használata esetén. Ebben az oktatóanyagban a blobok az Azure Storage CLI használatával való fel- és letöltését mutatjuk be. Az alábbiakat fogja elsajátítani:


> [!div class="checklist"]
> * MSI engedélyezése Linux rendszerű virtuális gépen 
> * Hozzáférés engedélyezése virtuális gép számára a tárfiók a Resource Managerben lévő SAS-adataihoz 
> * Hozzáférési jogkivonat lekérése a VM identitásával, majd a SAS-adatok lekérése a Resource Managerből annak használatával 

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.


## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Linux rendszerű virtuális gép létrehozása új erőforráscsoportban

Ebben az oktatóanyagban egy új linuxos virtuális gépet hozunk létre. A meglévő virtuális gépeken is engedélyezheti az MSI-t.

1. Kattintson az Azure Portal bal felső sarkában található **+/Új szolgáltatás létrehozása** gombra.
2. Válassza a **Számítás**, majd az **Ubuntu Server 16.04 LTS** elemet.
3. Adja meg a virtuális gép adatait. A **Hitelesítés típusa** résznél válassza az **SSH nyilvános kulcs** vagy a **Jelszó** lehetőséget. A létrehozott hitelesítő adatokkal jelentkezhet be a virtuális gépre.

    ![Helyettesítő képszöveg](media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Válasszon ki egy **előfizetést** a legördülő menüben a virtuális gép számára.
5. Ha a virtuális gépet egy új **Erőforráscsoportban** szeretné létrehozni, válassza az **Új létrehozása** elemet. Amikor végzett, kattintson az **OK** gombra.
6. Válassza ki a virtuális gép méretét. További méretek megjelenítéséhez válassza **Az összes megtekintése** lehetőséget, vagy módosítsa a Támogatott lemeztípus szűrőt. A Beállítások panelen hagyja változatlanul az alapértelmezett beállításokat, és kattintson az **OK** gombra.

## <a name="enable-msi-on-your-vm"></a>MSI engedélyezése a virtuális gépen

A virtuális gép MSI-vel anélkül kérhet le hozzáférési jogkivonatokat az Azure AD-ből, hogy hitelesítő adatokat kellene a kódba illesztenie. A felügyeltszolgáltatás-identitás VM-en való engedélyezése két dolgot tesz: regisztrálja a VM-et az Azure Active Directoryban a felügyelt identitása létrehozásához, és konfigurálja az identitást a VM-en. 

1. Lépjen az új virtuális gép erőforráscsoportjára, és válassza ki az előző lépésben létrehozott virtuális gépet.
2. A virtuális gép bal oldalon található „Beállításai” között kattintson a **Konfiguráció** elemre.
3. Az MSI regisztrálásához és engedélyezéséhez kattintson az **Igen**, a letiltásához a Nem gombra.
4. Mindenképp kattintson a **Mentés** gombra a konfiguráció mentéséhez.

    ![Helyettesítő képszöveg](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

## <a name="create-a-storage-account"></a>Tárfiók létrehozása 

Ha még nem rendelkezik tárfiókkal, most létrehoz egyet.  Ki is hagyhatja ezt a lépést, és a VM MSI számára biztosíthat hozzáférést egy meglévő tárfiók kulcsaihoz. 

1. Kattintson az Azure Portal bal felső sarkában található **+/Új szolgáltatás létrehozása** gombra.
2. Kattintson a **Tárolás**, majd a **Tárfiók** elemre, amit követően megjelenik egy új „Tárfiók létrehozása” panel.
3. Adjon meg egy nevet a tárfiók számára a **Név** mezőben, amelyet később használ majd.  
4. Az **Üzemi modell** mezőben a „Resource Manager”, a **Fióktípus** mezőben az „Általános célú” értéket kell megadni. 
5. Ellenőrizze, hogy az **Előfizetés** és az **Erőforráscsoport** mező értéke egyezik-e az előző lépésben a virtuális gép létrehozása során megadottakkal.
6. Kattintson a **Create** (Létrehozás) gombra.

    ![Új tárfiók létrehozása](../managed-service-identity/media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Blobtároló létrehozása a tárfiókban

Később feltöltünk egy fájlt az új tárfiókba, majd letöltjük abból. Mivel a fájlok tárolásához blobtároló szükséges, létre kell hoznunk egyet, amelyben tárolhatjuk a fájlt.

1. Lépjen vissza az újonnan létrehozott tárfiókra.
2. Kattintson a **Tárolók** hivatkozásra bal oldalon található „Blob szolgáltatás” területen.
3. Kattintson a **+ Tároló** gombra a lap tetején, amit követően becsúszik az „Új tároló” panel.
4. Nevezze el a tárolót, válasszon ki egy hozzáférési szintet, majd kattintson az **OK** gombra. A megadott névre az oktatóanyag későbbi részében lesz majd szükség. 

    ![Storage-tároló létrehozása](../managed-service-identity/media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-msi-access-to-use-a-storage-sas"></a>Hozzáférés engedélyezése virtuális gép MSI-identitása számára a tároló SAS-adatainak használatához 

Az Azure Storage nem támogatja natív módon az Azure AD-hitelesítést.  Az MSI használatával azonban lekérheti a tároló SAS-adatait a Resource Managerből, majd a SAS-adatokkal elérheti a tárolót.  Ebben a lépésben hozzáférést biztosít a virtuális gép MSI-identitása számára a tárfiók SAS-adataihoz.   

1. Lépjen vissza az újonnan létrehozott tárfiókra.   
2. Kattintson a **Hozzáférés-vezérlés (IAM)** hivatkozásra a bal oldali panelen.  
3. A lap tetején a **+ Hozzáadás** gombra kattintva adjon hozzá egy új szerepkör-hozzárendelést a VM-hez.
4. A lap jobb oldalán a **Szerepkör** értékeként adja meg a „Tárfiók-közreműködő” értéket. 
5. A következő legördülő menüben a **Hozzáférés hozzárendelése** beállítás számára válassza ki a „Virtuális gép” értéket.  
6. Ezután ellenőrizze, hogy a megfelelő előfizetés szerepel-e az **Előfizetés** legördülő menüben, majd állítsa az **Erőforráscsoport** értékét a „Minden erőforráscsoport” értékre.  
7. Végül a **Kiválasztás** mezőben válassza ki a Linux rendszerű virtuális gépet a legördülő menüben, majd kattintson a **Mentés** gombra.  

    ![Helyettesítő képszöveg](../managed-service-identity/media/msi-tutorial-linux-vm-access-storage/msi-storage-role-sas.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Hozzáférési jogkivonat lekérése a VM identitásával, majd az Azure Resource Manager meghívása a használatával

Az oktatóanyag további részében a korábban létrehozott virtuális gépről dolgozunk.

A lépések elvégzéséhez szüksége lesz egy SSH-ügyfélre. Windows használata esetén használhatja a [Linux Windows alrendszerében](https://msdn.microsoft.com/commandline/wsl/install_guide) elérhető SSH-ügyfelet. Amennyiben segítségre van szüksége az SSH-ügyfél kulcsának konfigurálásához, [Az SSH-kulcsok és a Windows együttes használata az Azure-ban](../../virtual-machines/linux/ssh-from-windows.md) vagy [Nyilvános és titkos SSH-kulcspár létrehozása és használata az Azure-ban Linux rendszerű virtuális gépekhez](../../virtual-machines/linux/mac-create-ssh-keys.md) című cikkekben talál további információt.

1. Az Azure Portalon lépjen a **Virtuális gépek** felületre, keresse meg a Linux virtuális gépet, majd az **Áttekintés** lap tetején kattintson a **Csatlakozás** gombra. Másolja ki sztringet a virtuális géphez való csatlakozáshoz. 
2. Csatlakozzon a virtuális géphez az SSH-ügyfél használatával.  
3. Ezután meg kell adnia majd a **Linux VM** létrehozásakor hozzáadott **Jelszót**. Ezzel sikeresen be kell tudnia jelentkezni.  
4. A CURL használatával kérjen le egy hozzáférési jogkivonatot az Azure Resource Manager számára.  

    A hozzáférési jogkivonatra vonatkozó CURL-kérelmet és -választ alább láthatja:
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true    
    ```
    
    > [!NOTE]
    > Az előző kérésben a „resource” (erőforrás) paraméter értékének pontosan egyeznie kell az Azure AD által várt értékkel. Az Azure Resource Manager erőforrás-azonosítójának használatakor a záró perjelet is szerepeltetni kell az URI-ban.
    > A következő válaszban az access_token elemet a helytakarékosság miatt rövidítve jelenik meg.
    
    ```bash
    {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"} 
     ```

## <a name="get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls"></a>SAS-hitelesítő adatok lekérése az Azure Resource Managerből tárolóhívások indításához

Most a CURL használatával hívja meg a Resource Managert az előző szakaszban lekért hozzáférési jogkivonattal egy tároló SAS-hitelesítő adatainak létrehozásához. Amint rendelkezésre állnak a SAS-hitelesítő adatok, meg lehet hívni a tárfeltöltési/-letöltési műveleteket.

Ehhez a kéréshez az alábbi HTTP-kérésparamétereket használja majd a SAS-hitelesítő adatok létrehozásához:

```JSON
{
    "canonicalizedResource":"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>",
    "signedResource":"c",              // The kind of resource accessible with the SAS, in this case a container (c).
    "signedPermission":"rcw",          // Permissions for this SAS, in this case (r)ead, (c)reate, and (w)rite.  Order is important.
    "signedProtocol":"https",          // Require the SAS be used on https protocol.
    "signedExpiry":"<EXPIRATION TIME>" // UTC expiration time for SAS in ISO 8601 format, for example 2017-09-22T00:06:00Z.
}
```

Ezek a paraméterek a SAS-hitelesítő adatokra vonatkozó kérés POST-törzsében találhatók. A SAS-hitelesítő adatok létrehozására vonatkozó paraméterekkel kapcsolatos további információkat [a szolgáltatási SAS REST referenciában](/rest/api/storagerp/storageaccounts/listservicesas) találja.

A SAS-hitelesítő adatok lekéréséhez használja a következő CURL-kérést. A `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, `<STORAGE ACCOUNT NAME>`, `<CONTAINER NAME>` és `<EXPIRATION TIME>` paraméterek értékét mindenképp helyettesítse be a saját értékeivel. Az `<ACCESS TOKEN>` értéket cserélje le a korábban lekért hozzáférési jogkivonattal:

```bash 
curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/listServiceSas/?api-version=2017-06-01 -X POST -d "{\"canonicalizedResource\":\"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>\",\"signedResource\":\"c\",\"signedPermission\":\"rcw\",\"signedProtocol\":\"https\",\"signedExpiry\":\"<EXPIRATION TIME>\"}" -H "Authorization: Bearer <ACCESS TOKEN>"
```

> [!NOTE]
> Az előző URL-cím szövege megkülönbözteti a kis- és nagybetűket, ezért az erőforráscsoportok esetében is különböztesse meg ezeket a megfelelő hivatkozás érdekében. Ezenkívül fontos észben tartani, hogy ez egy POST és nem egy GET kérés.

A CURL-válasz adja vissza a SAS-hitelesítő adatot:  

```bash 
{"serviceSasToken":"sv=2015-04-05&sr=c&spr=https&st=2017-09-22T00%3A10%3A00Z&se=2017-09-22T02%3A00%3A00Z&sp=rcw&sig=QcVwljccgWcNMbe9roAJbD8J5oEkYoq%2F0cUPlgriBn0%3D"} 
```

Hozzon létre egy példablobfájlt, amelyet feltölthet a Blob Storage-tárolóba. Linux virtuális gépen azt a következő paranccsal teheti meg. 

```bash
echo "This is a test file." > test.txt
```

Ezután hitelesítsen az `az storage` CLI-paranccsal a SAS-hitelesítő adatok használatával, és töltse fel a fájlt a blobtárolóba. Ehhez a lépéshez [telepítenie kell az Azure CLI legújabb verzióját](https://docs.microsoft.com/cli/azure/install-azure-cli) a virtuális gépen, ha eddig még nem tette volna meg.

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

Emellett a fájlt letöltheti az Azure CLI használatával is. A hitelesítéshez a SAS-hitelesítő adatokat használja. 

Kérés: 

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

Az oktatóanyag bemutatta, hogyan használhat felügyeltszolgáltatás-identitásokat Linux virtuális gépeken az Azure Storage SAS-hitelesítő adatok használatával való eléréséhez.  További információ az Azure Storage SAS-hitelesítéséről:

> [!div class="nextstepaction"]
>[Közös hozzáférésű jogosultságkódok (SAS) használata](/azure/storage/common/storage-dotnet-shared-access-signature-part-1)
