---
title: Az Azure Storage-fiókhoz tartozó egyéni tartománynév beállítása |} A Microsoft Docs
description: Az Azure portal segítségével a saját kanonikus nevének (CNAME) leképezése egy Azure Storage-fiókban a Blob vagy webes végpont.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/26/2018
ms.author: tamram
ms.component: blobs
ms.openlocfilehash: beacb39752b2af51aba4ddeb41c9bff68803df1b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46954772"
---
# <a name="configure-a-custom-domain-name-for-your-azure-storage-account"></a>Az Azure Storage-fiókhoz tartozó egyéni tartománynév konfigurálása

Konfigurálhat egy egyéni tartományt az Azure-tárfiókjában lévő Blobadatok eléréséhez. Az alapértelmezett végpont a Blob Storage `<storage-account-name>.blob.core.windows.net`. A részeként létrehozott webes végpont is használhatja a [statikus webhelyek kiszolgálására funkció (előzetes verzió)](storage-blob-static-website.md). Ha leképez egy egyéni tartományt és altartományt, például **www.contoso.com** szeretné az storage blob vagy webes végpontja fiók számára, a felhasználók is, és a tartomány a tárfiókban lévő Blobadatok eléréséhez.

> [!IMPORTANT]
> Az Azure Storage nem még natív módon támogatja a HTTPS egyéni tartománnyal rendelkező. Jelenleg is [az Azure CDN használatával HTTPS-kapcsolaton keresztül egyéni tartománnyal rendelkező blobok elérése](storage-https-custom-domain-cdn.md).
>

> [!NOTE]  
> Storage-fiókok jelenleg támogatja a-fiókonként csak egy egyéni tartománynevet. Ez azt jelenti, hogy a webes és a blob Szolgáltatásvégpontok nem leképez egy egyéni tartománynevet.

Az alábbi táblázatban láthatók a nevű storage-fiókban található blob adatok néhány minta URL-címek **mystorageaccount**. Az egyéni tartomány regisztrálni a storage-fiók **www.contoso.com**:

| Erőforrás típusa | Alapértelmezett URL-cím | Egyéni tartomány URL-címe |
| --- | --- | --- | --- |
| Tárfiók | http://mystorageaccount.blob.core.windows.net | http://www.contoso.com |
| Blob |http://mystorageaccount.blob.core.windows.net/mycontainer/myblob | http://www.contoso.com/mycontainer/myblob |
| Legfelső szintű tároló | http://mystorageaccount.blob.core.windows.net/myblob vagy http://mystorageaccount.blob.core.windows.net/$root/myblob| http://www.contoso.com/myblob vagy http://www.contoso.com/$root/myblob |
| Web |  http://mystorageaccount. [zone].web.core.windows.net/$web/[indexdoc] vagy http://mystorageaccount. [ Zone].Web.Core.Windows.NET/[indexdoc] vagy http://mystorageaccount. [ Zone].Web.Core.Windows.NET/$Web vagy http://mystorageaccount. [ Zone].Web.Core.Windows.NET/ | http://www.contoso.com/$web vagy http://www.contoso.com/ vagy http://www.contoso.com/$web / [indexdoc] vagy http://www.contoso.com/[indexdoc] |

> [!NOTE]  
> A Blob-szolgáltatásvégpont alatti összes példák a webszolgáltatási végpontot is vonatkozik.

## <a name="direct-vs-intermediary-domain-mapping"></a>Közvetlen és a köztes tartomány-hozzárendelés

Az egyéni tartomány átirányítása a tárfiók blobvégpontjához két módja van: a közvetlen CNAME-leképezés, és használja a *asverify* köztes altartomány.

### <a name="direct-cname-mapping"></a>Közvetlen CNAME-leképezés

Az első és a legegyszerűbb, módja, ha egy kanonikus nevének (CNAME) rekordot, amely leképezi az egyéni tartományt és altartományt közvetlenül a blob végpontja. Egy CNAME-rekordot funkciója tartomány neve tartománynévrendszer (DNS), amely egy forrástartomány leképezhető egy céltartományra. Ebben az esetben a forrástartomány a saját egyéni tartományt és altartományt, például akkor *www.contoso.com*. A cél tartománya például a a Blob-szolgáltatásvégpont *mystorageaccount.blob.core.windows.net*.

A közvetlen metódus foglalkozik [egyéni tartomány regisztrálása](#register-a-custom-domain).

### <a name="intermediary-mapping-with-asverify"></a>A közvetítő leképezés *asverify*

A második módszer is a CNAME-rekordokat használ, de először használ az üzemszünet elkerülése érdekében az Azure által felismert speciális altartomány: **asverify**.

Az egyéni tartomány leképezése egy blob végpontja folyamata okozhat rövid idő alatt a tartomány állásidő, amikor regisztrálja az a [az Azure portal](https://portal.azure.com). Ha az egyéni tartomány jelenleg támogatja az alkalmazás egy szolgáltatásiszint-szerződés (SLA), amelyhez szükséges állásidő nélkül, akkor használhatja az Azure *asverify* altartomány köztes regisztrációs lépésként. Ez a köztes lépés biztosítja a felhasználók tudnak hozzáférni a tartományban, a DNS-hozzárendelés végrehajtása közben.

A közvetítő metódus foglalkozik [egy egyéni tartomány használatával regisztrálja a *asverify* altartomány](#register-a-custom-domain-using-the-asverify-subdomain).

## <a name="register-a-custom-domain"></a>Egyéni tartomány regisztrálása
Ezzel az eljárással regisztrálja az egyéni tartományt, ha kétségei vannak nincs rövid ideig nem volt elérhető, a felhasználók számára a tartománnyal kapcsolatban, vagy ha az egyéni tartomány jelenleg nem tartalmazzák az alkalmazás. Azure DNS segítségével a konfigurálása az Azure Blob-tároló egyedi DNS-nevet. További információt az [egyéni tartománybeállítások egy Azure-szolgáltatáshoz az Azure DNS használatával történő megadását](https://docs.microsoft.com/azure/dns/dns-custom-domain#blob-storage) ismertető cikkben talál.

Ha az egyéni tartomány jelenleg támogatja az olyan alkalmazás, amely nem rendelkezik az állásidő, kövesse a leírt eljárást [egy egyéni tartomány használatával regisztrálja a *asverify* altartomány](#register-a-custom-domain-using-the-asverify-subdomain).

Egyéni tartománynév beállítása, létre kell hoznia egy új CNAME-rekordot a DNS-ben. A CNAME-rekord egy tartománynevet alias határozza meg. Ebben az esetben vannak leképezve az egyéni tartomány a címet a tárfiók a Blob storage-végponthoz.

Általában kezelheti a tartomány DNS-beállítások a tartományregisztrálója webhelyén. Minden tartományregisztráló egy hasonló, de eltérő módot a CNAME rekordot, de a koncepció azonos. Néhány alapszintű regisztrációs csomag nem ajánlja fel a DNS-konfiguráció, így előfordulhat, hogy frissítse a tartomány regisztrációs csomagot, a CNAME-rekord létrehozása előtt kell.

1. Keresse meg a tárfiók a [az Azure portal](https://portal.azure.com).
1. Alatt **BLOB SERVICE** menü panelen válassza ki a **egyéni tartomány** megnyitásához a *egyéni tartomány* panelen.
1. Jelentkezzen be a tartományregisztráló webhelyére, és nyissa meg a DNS kezelésére szolgáló lap. Ezt a **Tartománynév**, **DNS**, **Névkiszolgáló kezelése** vagy hasonló területen találja.
1. Keresse meg a CNAME-rekordok kezelésére szolgáló felületet. Ugrás a különleges beállítások lapjára, és keresse meg a szavakat is **CNAME**, **Alias**, vagy **altartományok**.
1. Hozzon létre egy új CNAME-rekordot, és adjon meg egy altartományt alias például **www** vagy **fényképek**. Adja meg egy állomásnevet, amely a Blob service-végpont, a következő formátumban **mystorageaccount.blob.core.windows.net** (ahol *mystorageaccount* a tárfiók neve). #1 elem szerepel a gazdagép nevét, a *egyéni tartomány* paneljén a [az Azure portal](https://portal.azure.com).
1. A szövegmezőbe írja be a *egyéni tartomány* paneljén a [az Azure portal](https://portal.azure.com), adja meg az egyéni tartományt az altartománnyal együtt nevét. Például, ha a tartomány **contoso.com** és az altartomány-alias **www**, adja meg **www.contoso.com**. Ha az altartomány **fényképek**, adja meg **photos.contoso.com**. Az altartomány van *szükséges*.
1. Válassza ki **mentése** a a *egyéni tartomány* panel az egyéni tartomány regisztrálása. Ha a regisztráció sikeres, megjelenik egy portal-értesítést, hogy a tárfiók frissítése sikerült.

Az új CNAME-rekord rendelkezik propagálása a DNS-sel, miután a felhasználók megtekintéséhez Blobadatok az egyéni tartomány mindaddig, amíg a megfelelő engedélyek lettek.

## <a name="register-a-custom-domain-using-the-asverify-subdomain"></a>Egy egyéni tartomány használatával regisztrálja a *asverify* altartomány
Ezzel az eljárással regisztrálja az egyéni tartomány Ha az egyéni tartomány jelenleg támogatja az SLA-t, amely megköveteli, hogy egy alkalmazást, hogy nem jár. Hozzon létre egy CNAME REKORDOT, amely a mutat `asverify.<subdomain>.<customdomain>` való `asverify.<storageaccount>.blob.core.windows.net`, előre regisztrálhatja a tartomány az Azure-ral. Ezután létrehozhat egy második CNAME rekord, amely a mutat `<subdomain>.<customdomain>` való `<storageaccount>.blob.core.windows.net`, ekkor az egyéni tartomány forgalmat a rendszer átirányítja a blob-végpontra.

A **asverify** altartomány altartománya különleges Azure rozpoznána. Előtag-Beillesztés által `asverify` saját együtt, hogy az egyéni tartomány felismerje a tartomány DNS-rekord módosítása nélkül az Azure engedélyezi. Amikor módosít, a tartomány DNS-rekordját, azt a blob végpontja üzemkimaradás nélkül kell hozzárendelni.

1. Keresse meg a tárfiók a [az Azure portal](https://portal.azure.com).
1. Alatt **BLOB SERVICE** menü panelen válassza ki a **egyéni tartomány** megnyitásához a *egyéni tartomány* panelen.
1. Jelentkezzen be a DNS-szolgáltatónál webhelyre, és nyissa meg a DNS kezelésére szolgáló lap. Ezt a **Tartománynév**, **DNS**, **Névkiszolgáló kezelése** vagy hasonló területen találja.
1. Keresse meg a CNAME-rekordok kezelésére szolgáló felületet. Ugrás a különleges beállítások lapjára, és keresse meg a szavakat is **CNAME**, **Alias**, vagy **altartományok**.
1. Hozzon létre egy új CNAME-rekordot, és adjon meg egy altartományt alias, amely tartalmazza a *asverify* altartományt. Ha például **asverify.www** vagy **asverify.photos**. Adja meg egy állomásnevet, amely a Blob service-végpont, a következő formátumban **asverify.mystorageaccount.blob.core.windows.net** (ahol **mystorageaccount** a tárfiók neve). A gazdagép nevét, a #2 elem jelenik meg a *egyéni tartomány* paneljén a [az Azure portal](https://portal.azure.com).
1. A szövegmezőbe írja be a *egyéni tartomány* paneljén a [az Azure portal](https://portal.azure.com), adja meg az egyéni tartományt az altartománnyal együtt nevét. Nem tartalmaznak *asverify*. Például, ha a tartomány **contoso.com** és az altartomány-alias **www**, adja meg **www.contoso.com**. Ha az altartomány **fényképek**, adja meg **photos.contoso.com**. Az altartomány megadása kötelező.
1. Válassza ki a **CNAME rekord közvetett ellenőrzésének használata** jelölőnégyzetet.
1. Válassza ki **mentése** a a *egyéni tartomány* panel az egyéni tartomány regisztrálása. Ha a regisztráció sikeres, megjelenik egy portal-értesítést arról, hogy a tárfiók frissítése sikerült. Ezen a ponton az egyéni tartomány ellenőrzése után az Azure, de a forgalmat a tartományhoz van még irányítása nem a tárfiókhoz.
1. Térjen vissza a DNS-szolgáltatónál webhelyet, és hozzon létre egy másik olyan CNAME rekordot, amely a Blob service-végpont az altartomány van leképezve. Adja meg az altartományt, mint például **www** vagy **fényképek** (nélkül a *asverify*), és az állomásnevet, **mystorageaccount.blob.core.windows.net**  (ahol **mystorageaccount** a tárfiók neve). Az ebben a lépésben az egyéni tartomány a regisztráció befejeződött.
1. Végül a CNAME-rekordot tartalmazó létrehozott törölheti a **asverify** együtt, mert csak, egy köztes lépés szükséges volt.

Az új CNAME-rekord rendelkezik propagálása a DNS-sel, miután a felhasználók megtekintéséhez Blobadatok az egyéni tartomány mindaddig, amíg a megfelelő engedélyek lettek.

## <a name="test-your-custom-domain"></a>Az egyéni tartomány tesztelése

Annak ellenőrzéséhez, hogy az egyéni tartomány valóban van leképezve a Blob-szolgáltatásvégpont, egy nyilvános tárolóban, a tárfiókban található blob létrehozása. Ezután egy webes böngésző használatával egy URI-t a következő formátumban hozzáférjen a blobhoz:

`http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Például előfordulhat, hogy használja a következő URI Azonosítót webes űrlap eléréséhez a **myforms** a tárolót a **photos.contoso.com** egyéni altartomány:

`http://photos.contoso.com/myforms/applicationform.htm`

## <a name="deregister-a-custom-domain"></a>Egyéni tartomány regisztrációjának megszüntetése

Regisztrációjának megszüntetése a Blob storage-végpont egyéni tartományt, használja az alábbi eljárások egyikét.

### <a name="azure-portal"></a>Azure Portal

Hajtsa végre a következő eltávolítani az egyéni tartomány beállítása az Azure Portalon:

1. Keresse meg a tárfiók a [az Azure portal](https://portal.azure.com).
1. Alatt **BLOB SERVICE** menü panelen válassza ki a **egyéni tartomány** megnyitásához a *egyéni tartomány* panelen.
1. Az egyéni tartománynevet tartalmazó szövegmező tartalmának törlése.
1. Válassza ki a **Mentés** gombot.

Az egyéni tartomány sikeresen el lett távolítva, látni fogja a portal értesítése arról, hogy a tárfiók frissítése sikerült.

### <a name="azure-cli"></a>Azure CLI

Használja a [storage-fiók frissítése az](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_update) CLI parancsot, és adjon meg egy üres karakterláncot (`""`) számára a `--custom-domain` argumentum értéke egy egyéni tartomány regisztráció eltávolítása.

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

Használja a [Set-AzureRmStorageAccount](/powershell/module/azurerm.storage/set-azurermstorageaccount) PowerShell-parancsmag és a egy üres karakterláncot kell megadni (`""`) esetében a `-CustomDomainName` argumentum értéke egy egyéni tartomány regisztráció eltávolítása.

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

## <a name="next-steps"></a>További lépések
* [Egyéni tartomány leképezése egy Azure Content Delivery Network (CDN) végponthoz](../../cdn/cdn-map-content-to-custom-domain.md)
* [Az Azure CDN használatával HTTPS-kapcsolaton keresztül egyéni tartománnyal rendelkező blobok elérése](storage-https-custom-domain-cdn.md)
* [Statikus webhely üzemeltetése az Azure Blob Storage (előzetes verzió)](storage-blob-static-website.md)
