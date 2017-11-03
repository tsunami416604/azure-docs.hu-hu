---
title: "Az Azure Blob storage endpoint az egyéni tartománynév beállítása |} Microsoft Docs"
description: "Az Azure portál segítségével a saját kanonikus név (CNAME) van leképezve a Blob storage endpoint az Azure Storage-fiók."
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: aaafd8c5-eacb-49dc-8c8b-3f7011ad5e92
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2017
ms.author: tamram
ms.openlocfilehash: cbc8654bf1755826afa2cf83e5476e88903e0854
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-custom-domain-name-for-your-blob-storage-endpoint"></a>Egyéni tartományév konfigurálása a Blob Storage-végponthoz

A blob az Azure storage-fiók adataihoz fér hozzá az egyéni tartománynév konfigurálása Az alapértelmezett végpont Blob Storage `<storage-account-name>.blob.core.windows.net`. Ha leképez egy egyéni tartomány és altartomány például **www.contoso.com** a tárolási blob végpontja fiók számára, a felhasználók is tud majd, hogy a tartomány a tárfiókban lévő Blobadatok eléréséhez.

> [!IMPORTANT]
> Az Azure Storage nem még natív módon támogatja a HTTPS az egyéni tartományokat. Jelenleg is [használja az Azure CDN blobokhoz való hozzáférést az egyéni tartomány HTTPS-KAPCSOLATON keresztül](storage-https-custom-domain-cdn.md).
>

Az alábbi táblázat néhány minta URL-címéből nevű tárfiók lévő Blobadatok **mystorageaccount**. Az egyéni tartomány regisztrálva van a tárfiók **www.contoso.com**:

| Erőforrás típusa | Alapértelmezett URL-címe | Az egyéni tartomány URL-címe |
| --- | --- | --- |
| Tárfiók | http://mystorageaccount.BLOB.Core.Windows.NET | http://www.contoso.com |
| Blob |http://mystorageaccount.BLOB.Core.Windows.NET/mycontainer/myblob | http://www.contoso.com/mycontainer/myblob |
| Legfelső szintű tárolója | http://mystorageaccount.BLOB.Core.Windows.NET/myblob vagy http://mystorageaccount.blob.core.windows.net/$ legfelső szintű/myblob| http://www.contoso.com/myblob vagy http://www.contoso.com/$ legfelső szintű/myblob |

## <a name="direct-vs-intermediary-domain-mapping"></a>Közvetlen és köztes tartomány leképezése

Az egyéni tartomány mutasson a tárfiók a blob végpontja két módja van: közvetlen CNAME mapping, és használja a *asverify* közvetítő altartomány.

### <a name="direct-cname-mapping"></a>Közvetlen CNAME-leképezés

Az első és a legegyszerűbb, metódus, amely leképezhető az egyéni tartomány és altartomány közvetlenül a blob végpontja kanonikus név (CNAME) rekord létrehozásához. Egy olyan CNAME rekordot az egy tartomány neve rendszer (DNS) szolgáltatása a céltartományra a forrástartomány van leképezve. Ebben az esetben a forrástartomány a saját egyéni tartomány és altartomány, például *www.contoso.com*. A célként megadott tartomány pedig a Blob-szolgáltatásvégpont, például *mystorageaccount.blob.core.windows.net*.

A közvetlen módszer tárgyalja [regisztrálni az egyéni tartománynév](#register-a-custom-domain).

### <a name="intermediary-mapping-with-asverify"></a>A köztes leképezési *asverify*

A második módszer is használja a CNAME-rekordot, de először alkalmazza az állásidő elkerülése érdekében az Azure-ban felismerhető különleges altartomány: **asverify**.

Az egyéni tartomány hozzárendelését a blob végponthoz rövid idő alatt a tartomány állásidő is eredményezhettek, miközben regisztrál vele a [Azure-portálon](https://portal.azure.com). Ha az egyéni tartomány jelenleg támogat az alkalmazás a szolgáltatásiszint-szerződés (SLA), amelyhez az állásidő, akkor is használhatja az Azure *asverify* altartomány egy köztes regisztrációs lépés. Ez a köztes lépés biztosítja, hogy a felhasználók csak a tartomány eléréséhez, amíg a DNS-hozzárendelése kerül sor.

A köztes metódus tárgyalja [regisztráljon egy egyéni tartomány a *asverify* altartomány](#register-a-custom-domain-using-the-asverify-subdomain).

## <a name="register-a-custom-domain"></a>Az egyéni tartománynév regisztrálása
Ez az eljárás segítségével regisztrálja az egyéni tartomány, ha kérdése nincs folyamatban röviden nem érhető el, hogy a felhasználók tartománnyal kapcsolatos információk, vagy ha az egyéni tartomány jelenleg nem üzemeltet egy alkalmazást.

Ha az egyéni tartomány jelenleg támogat, amelyek nem rendelkeznek leállási kérelmet, hajtsa végre az ismertetett módon [regisztráljon egy egyéni tartomány a *asverify* altartomány](#register-a-custom-domain-using-the-asverify-subdomain).

Állítson be egy egyéni tartománynevet, új CNAME rekordot kell létrehoznia a DNS-ben. A CNAME rekordot a tartománynévhez tartozó alias határozza meg. Ebben az esetben azt rendeli hozzá az egyéni tartomány címét a tárfiók a Blob storage endpoint.

Általában a tartomány DNS-beállítások a tartományregisztráló webhelyen keresztül kezelheti. Minden tartományregisztráló egy CNAME rekordot a telepítésükhöz hasonló, csak metódust tartalmaz, azonban a koncepció azonos. Néhány alapvető tartomány regisztrációs csomagok nem képes DNS-konfiguráció, így előfordulhat, hogy frissítse a tartomány regisztrációs csomagot a CNAME rekord létrehozása előtt kell.

1. Keresse meg a storage-fiókot a [Azure-portálon](https://portal.azure.com).
1. A **BLOB szolgáltatás** menü paneljén válassza **egyéni tartomány** megnyitásához a *egyéni tartomány* panelen.
1. Jelentkezzen be a tartományregisztrálójához webhelyre, és a DNS kezelése lapon. Ezt a **Tartománynév**, **DNS**, **Névkiszolgáló kezelése** vagy hasonló területen találja.
1. Keresse meg a CNAME-rekordok kezelésére szolgáló felületet. Lehet, hogy egy speciális beállítások lapot, és keresse meg a szavakat **CNAME**, **Alias**, vagy **altartományok**.
1. Új CNAME rekordot kell létrehozni, és adja meg például egy altartomány alias **www** vagy **fényképek**. Adja meg egy állomásnevet, amely a Blob-szolgáltatásvégpont, formátumú **mystorageaccount.blob.core.windows.net** (ahol *mystorageaccount* a tárfiók neve). Az állomásnév használandó #1 elem jelenik meg a *egyéni tartomány* paneljén a [Azure-portálon](https://portal.azure.com).
1. A szövegmezőben meg a *egyéni tartomány* paneljén a [Azure-portálon](https://portal.azure.com), adja meg az egyéni tartomány, beleértve az altartomány nevét. Ha a tartomány például **contoso.com** és a altartomány alias **www**, adja meg **www.contoso.com**. Ha a altartomány **fényképek**, adja meg **photos.contoso.com**. Az altartomány van *szükséges*.
1. Válassza ki **mentése** a a *egyéni tartomány* panelt, és regisztrálja az egyéni tartomány. Ha a regisztráció sikeres, látni fogja, hogy a tárfiók sikeresen frissült a portál értesítései.

Az új CNAME-rekord propagálása DNS-en keresztül, amennyiben a felhasználók megtekintéséhez Blobadatok az egyéni tartomány, feltéve, hogy a megfelelő engedélyek lettek.

## <a name="register-a-custom-domain-using-the-asverify-subdomain"></a>Regisztráljon egy egyéni tartomány a *asverify* altartomány
Ezzel az eljárással regisztrálja az egyéni tartomány az egyéni tartomány van jelenleg támogatása az SLA-t, amely megköveteli, hogy az alkalmazás előfordulhatnak állásidő nélkül. Hozzon létre egy CNAME REKORDOT, mely az `asverify.<subdomain>.<customdomain>` való `asverify.<storageaccount>.blob.core.windows.net`, előre regisztrálhatja a tartomány az Azure-ral. Ezután létrehozhat egy második CNAME REKORDOT, mely az `<subdomain>.<customdomain>` való `<storageaccount>.blob.core.windows.net`, amikor az egyéni tartomány forgalmat a rendszer kéri a blob-végpontot.

A **asverify** altartomány altartománya különleges ismeri az Azure-ban. Által fertőző `asverify` saját altartomány, hogy az egyéni tartomány felismerje a tartomány DNS-rekordját módosítása nélkül Azure engedélyezése. Ha módosítja a tartomány DNS-bejegyzést, azt kell hozzárendelni a blob végpontja állásidő nélkül.

1. Keresse meg a storage-fiókot a [Azure-portálon](https://portal.azure.com).
1. A **BLOB szolgáltatás** menü paneljén válassza **egyéni tartomány** megnyitásához a *egyéni tartomány* panelen.
1. Jelentkezzen be a DNS-szolgáltatónál webhelyre, és a DNS kezelése lapon. Ezt a **Tartománynév**, **DNS**, **Névkiszolgáló kezelése** vagy hasonló területen találja.
1. Keresse meg a CNAME-rekordok kezelésére szolgáló felületet. Lehet, hogy egy speciális beállítások lapot, és keresse meg a szavakat **CNAME**, **Alias**, vagy **altartományok**.
1. Új CNAME rekordot kell létrehozni, és adjon meg egy altartomány alias, amely tartalmazza a *asverify* altartomány. Például **asverify.www** vagy **asverify.photos**. Adja meg egy állomásnevet, amely a Blob-szolgáltatásvégpont, formátumú **asverify.mystorageaccount.blob.core.windows.net** (ahol **mystorageaccount** a tárfiók neve). Az állomásnév használandó #2 elemét jelenik meg a *egyéni tartomány* paneljén a [Azure-portálon](https://portal.azure.com).
1. A szövegmezőben meg a *egyéni tartomány* paneljén a [Azure-portálon](https://portal.azure.com), adja meg az egyéni tartomány, beleértve az altartomány nevét. Nem tartalmaznak *asverify*. Ha a tartomány például **contoso.com** és a altartomány alias **www**, adja meg **www.contoso.com**. Ha a altartomány **fényképek**, adja meg **photos.contoso.com**. Az altartomány szükség.
1. Válassza ki a **CNAME rekord közvetett ellenőrzésének használata** jelölőnégyzetet.
1. Válassza ki **mentése** a a *egyéni tartomány* panelt, és regisztrálja az egyéni tartomány. Ha a regisztráció sikeres, megjelenik a portál értesítései figyelmezteti a felhasználókat arra, hogy a tárfiók sikeresen megtörtént. Ezen a ponton az egyéni tartomány ellenőrzése után az Azure-ban, de a forgalmat a tartományhoz nem még routedevent irányítása a tárfiókhoz.
1. A DNS-szolgáltatónál webhely adja vissza, és hozzon létre egy másik olyan CNAME rekordot, amely a altartomány van leképezve a Blob-szolgáltatásvégpont. Adja meg az altartományt, mint például **www** vagy **fényképek** (nélkül a *asverify*), és az állomásnevet, **mystorageaccount.blob.core.windows.net** (ahol **mystorageaccount** a tárfiók neve). Az ebben a lépésben az egyéni tartomány regisztrálása sikeresen befejeződött.
1. Végül, törölheti a létrehozott tartalmazó CNAME rekordot a **asverify** altartomány, mert csak egy közvetítő lépéseként szükséges volt.

Az új CNAME-rekord propagálása DNS-en keresztül, amennyiben a felhasználók megtekintéséhez Blobadatok az egyéni tartomány, feltéve, hogy a megfelelő engedélyek lettek.

## <a name="test-your-custom-domain"></a>Az egyéni tartomány tesztelése

Erősítse meg az egyéni tartomány valóban van leképezve a Blob-szolgáltatásvégpont, hozzon létre egy blobot a tárfiókon belül nyilvános tárolókban lévő. Ezt követően egy webböngészőben URI a következő formátumban eléréséhez használja a blob:

`http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Például előfordulhat, hogy használja a következő URI-azonosítója egy webes űrlap eléréséhez a **myforms** tárolóhoz a **photos.contoso.com** egyéni altartomány:

`http://photos.contoso.com/myforms/applicationform.htm`

## <a name="deregister-a-custom-domain"></a>Az egyéni tartománynév regisztrációjának törléséhez

Megszüntetni az egyéni tartományt a Blob storage-végponthoz, a következő eljárások egyikét használhatja.

### <a name="azure-portal"></a>Azure Portal

Az alábbi parancs eltávolítja az egyéni tartomány beállítása az Azure portálon:

1. Keresse meg a storage-fiókot a [Azure-portálon](https://portal.azure.com).
1. A **BLOB szolgáltatás** menü paneljén válassza **egyéni tartomány** megnyitásához a *egyéni tartomány* panelen.
1. Az egyéni tartománynevet tartalmazó szövegmező tartalmának törlése.
1. Válassza ki a **Mentés** gombot.

Ha az egyéni tartomány sikeresen el lett távolítva, látni fogja a portál értesítései figyelmezteti a felhasználókat arra, hogy a tárfiók sikeresen megtörtént.

### <a name="azure-cli-20"></a>Azure CLI 2.0

Használja a [az tárolási fiók frissítés](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_update) CLI parancsot, és adjon meg üres karakterláncot (`""`) az a `--custom-domain` argumentum értéke egy egyéni tartományregisztrációs eltávolítása.

* A parancs formátuma:

  ```azurecli
  az storage account update \
      --name <storage-account-name> \
      --resource-group <resource-group-name> \
      --custom-domain ""
  ```

* Példa:

  ```azurecli
  az storage account update \
      --name mystorageaccount \
      --resource-group myresourcegroup \
      --custom-domain ""
  ```

### <a name="powershell"></a>PowerShell

Használja a [Set-AzureRmStorageAccount](/powershell/module/azurerm.storage/set-azurermstorageaccount) PowerShell-parancsmag és egy üres karakterláncot kell megadni (`""`) az a `-CustomDomainName` argumentum értéke egy egyéni tartományregisztrációs eltávolítása.

* A parancs formátuma:

  ```powershell
  Set-AzureRmStorageAccount `
      -ResourceGroupName "<resource-group-name>" `
      -AccountName "<storage-account-name>" `
      -CustomDomainName ""
  ```

* Példa:

  ```powershell
  Set-AzureRmStorageAccount `
      -ResourceGroupName "myresourcegroup" `
      -AccountName "mystorageaccount" `
      -CustomDomainName ""
  ```

## <a name="next-steps"></a>Következő lépések
* [Az Azure Content Delivery Network (CDN) végpont rendelni egy egyéni tartományt](../../cdn/cdn-map-content-to-custom-domain.md)
* [Az Azure CDN használatával blobokhoz való hozzáférést az egyéni tartomány HTTPS-KAPCSOLATON keresztül](storage-https-custom-domain-cdn.md)
