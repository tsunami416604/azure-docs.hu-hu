---
title: Az Azure storage-fiókjában az egyéni tartománynév beállítása |} A Microsoft Docs
description: Az Azure portal segítségével a saját kanonikus nevének (CNAME) leképezése az Azure storage-fiók Blob storage vagy a webes végpont.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/26/2018
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: edd011509c9129e95bcf7ea49f5a84e17fffd176
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2019
ms.locfileid: "56310550"
---
# <a name="configure-a-custom-domain-name-for-your-azure-storage-account"></a>Az Azure storage-fiók egy egyéni tartománynév konfigurálása

Konfigurálhat egy egyéni tartományt az Azure-tárfiókjában lévő Blobadatok eléréséhez. Az alapértelmezett végpont az Azure Blob storage  *\<storage-fiók-neve >. blob.core.windows.net*. Részeként is használhatja a webes végpont, amely akkor jön létre a [statikus webhelyek kiszolgálására funkció (előzetes verzió)](storage-blob-static-website.md). Ha leképez egy egyéni tartományt és altartományt, mint például *www.contoso.com*, a tárfiók blob vagy webes végpontra, a felhasználók hozzáférhetnek a blob adatokhoz a tárfiókban lévő tartományt is használhatja.

> [!IMPORTANT]
> Az Azure Storage nem még natív módon támogatja a HTTPS egyéni tartománnyal rendelkező. Jelenleg is [használata az Azure CDN blobok elérése az egyéni tartományok HTTPS-kapcsolaton keresztül](storage-https-custom-domain-cdn.md).
>

> [!NOTE]  
> Storage-fiókok jelenleg támogatja a-fiókonként csak egy egyéni tartománynevet. A webes és a blob Szolgáltatásvégpontok nem leképez egy egyéni tartománynevet.

Az alábbi táblázatban láthatók a nevű storage-fiókban található blob adatok néhány minta URL-címek *mystorageaccount*. Az egyéni tartományt, amely regisztrálva van a tárfiók *www.contoso.com*:

| Erőforrás típusa | Alapértelmezett URL-cím | Egyéni tartomány URL-címe |
| --- | --- | --- | --- |
| Tárfiók | http://mystorageaccount.blob.core.windows.net | http://www.contoso.com |
| Blob |http://mystorageaccount.blob.core.windows.net/mycontainer/myblob | http://www.contoso.com/mycontainer/myblob |
| Legfelső szintű tároló | http://mystorageaccount.blob.core.windows.net/myblob vagy http://mystorageaccount.blob.core.windows.net/$root/myblob| http://www.contoso.com/myblob vagy http://www.contoso.com/$root/myblob |
| Web |  http://mystorageaccount. [zone].web.core.windows.net/$web/[indexdoc] vagy http://mystorageaccount. [ Zone].Web.Core.Windows.NET/[indexdoc] vagy http://mystorageaccount. [ Zone].Web.Core.Windows.NET/$Web vagy http://mystorageaccount. [ Zone].Web.Core.Windows.NET/ | http://www.contoso.com/$web vagy http://www.contoso.com/ vagy http://www.contoso.com/$web / [indexdoc] vagy http://www.contoso.com/[indexdoc] |

> [!NOTE]  
> Ahogyan az alábbi szakaszokban található, minden példák a blob-szolgáltatásvégpont is alkalmazható a webszolgáltatási végpontot.

## <a name="direct-vs-intermediary-domain-mapping"></a>Közvetlen és a köztes tartomány-hozzárendelés

Az egyéni tartomány két módon a tárfiók blobvégpontjához is mutat: 
* Használjon közvetlen CNAME-leképezés.
* Használja a *asverify* köztes altartományt.

### <a name="direct-cname-mapping"></a>Direct CNAME mapping

Az első és a legegyszerűbb, módja, ha egy kanonikus nevének (CNAME) rekordot, amely leképezi az egyéni tartományt és altartományt közvetlenül a blob végpontja. A CNAME record is a domain name system (DNS) feature that maps a source domain to a destination domain. Ebben a példában a forrástartomány a saját egyéni tartományt és altartományt (*www.contoso.com*, például). A cél-tartomány a blob service-végpont (*mystorageaccount.blob.core.windows.net*, például).

A közvetlen metódus az "Egyéni tartomány regisztrálása" szakasz tárgyalja.

### <a name="intermediary-mapping-with-asverify"></a>A közvetítő leképezés *asverify*

A második módszer a CNAME-rekordokat is használ. Állásidő elkerülése érdekében azonban először az üzemeléshez speciális altartomány *asverify* , amely felismeri a Azure.

Az egyéni tartomány leképezése egy blob végpontja okozhat állásidő rövid ideig, amíg a tartomány regisztrál a [az Azure portal](https://portal.azure.com). Ha a tartomány jelenleg támogatja az egy szolgáltatásiszint-szerződés (SLA) üzemkimaradás igénylő alkalmazás, használja az Azure *asverify* altartomány köztes regisztrációs lépésként. Ez a lépés biztosítja, hogy a felhasználók hozzáférhessenek a tartományban, a DNS-hozzárendelés végrehajtása közben.

A közvetítő metódus tárgyalunk regisztrálni az egyéni tartomány használatával a *asverify* altartományt.

## <a name="register-a-custom-domain"></a>Egyéni tartomány regisztrálása
A tartomány regisztrálása ebben a szakaszban az eljárás segítségével, ha a alkalmazni az alábbi utasításokat:
* Ön unconcerned, hogy a tartomány nem érhető el röviden a felhasználók számára.
* Az egyéni tartomány jelenleg nem tartalmazzák az alkalmazás. 

Azure DNS segítségével a konfigurálása az Azure Blob-tároló egyedi DNS-nevet. További információt az [egyéni tartománybeállítások egy Azure-szolgáltatáshoz az Azure DNS használatával történő megadását](https://docs.microsoft.com/azure/dns/dns-custom-domain#blob-storage) ismertető cikkben talál.

Az egyéni tartomány jelenleg támogatja az olyan alkalmazás, amely nem rendelkezik az állásidő, ha az eljárással regisztrálja az egyéni tartomány használatával a *asverify* altartományt.

Egyéni tartománynév beállítása, hozzon létre egy új CNAME-rekordot a DNS-ben. The CNAME record specifies an alias for a domain name. Ebben a példában a tárfiók Blob storage-végpont vannak leképezve az egyéni tartomány a címet.

Általában kezelheti a tartomány DNS-beállítások a tartományregisztrálója webhelyén. Minden tartományregisztráló egy hasonló, de eltérő módot a CNAME rekordot, de a koncepció azonos. DNS-konfiguráció nem érhető el néhány alapszintű regisztrációs csomag, mert előfordulhat, hogy frissíteni szeretne a tartomány regisztrációs csomagot a CNAME-rekord létrehozása előtt.

1. Az a [az Azure portal](https://portal.azure.com), lépjen a tárfiókhoz.

1. A menü panelen alatt **Blob Service**válassza **egyéni tartomány**.  
   A **egyéni tartomány** panel nyílik meg.

1. Jelentkezzen be a tartományregisztráló webhelyére, és keresse meg a DNS kezelésére szolgáló lap.  
   Az oldal egy nevű szakaszban találhatja **tartománynév**, **DNS**, vagy **névkiszolgáló kezelése**.

1. Keresse meg a CNAME-rekordok kezelésére szolgáló felületet.  
   Lehetséges, hogy egy speciális beállítások lapot és keressen **CNAME**, **Alias**, vagy **altartományok**.

1. Hozzon létre egy új CNAME-rekordot, írjon be egy altartomány például **www** vagy **fényképek**, majd adja meg egy állomásnevet.  
   A gazdagép nevét a blob-szolgáltatásvégpont. A formátum  *\<mystorageaccount >. blob.core.windows.net*, ahol *mystorageaccount* a tárfiók neve. #1 elem szerepel a gazdagép nevét, a **egyéni tartomány** ablaktábláján a [az Azure portal](https://portal.azure.com).

1. Az a **egyéni tartomány** panelen szövegbeviteli mezőben adja meg az egyéni tartományt az altartománnyal együtt nevét.  
   Például, ha a tartomány *contoso.com* és az altartomány-alias *www*, adja meg **www.contoso.com**. Ha az altartomány *fényképek*, adja meg **photos.contoso.com**.

1. Válassza ki az egyéni tartomány regisztrálása, **mentése**.  
   Ha a regisztráció sikeres, a portál értesíti, hogy a tárfiók frissítése sikerült.

Után az új CNAME-rekord rendelkezik propagál DNS használata esetén, ha a felhasználók a megfelelő engedélyekkel rendelkezik, akkor az egyéni tartomány használatával megtekintheti a Blobadatok.

## <a name="register-a-custom-domain-by-using-the-asverify-subdomain"></a>Az egyéni tartomány regisztrálása a *asverify* altartomány
Ha az egyéni tartomány jelenleg támogatja az SLA-t, amely megköveteli, hogy ott, leállás nélkül lehet egy alkalmazást, regisztrálni az egyéni tartomány ebben a szakaszban az eljárás használatával. Hozzon létre egy CNAME REKORDOT, amely a mutat *asverify.\< altartomány >. \<customdomain >* való *asverify.\< storageaccount >. blob.core.windows.net*, előre regisztrálhatja a tartomány az Azure-ral. Ezután létrehozhat egy második CNAME rekord, amely a mutat  *\<altartomány >.\< customdomain >* való  *\<storageaccount >. blob.core.windows.net*, és ezután átirányítja a forgalmat az egyéni tartományban a blob-végponthoz.

A *asverify* altartomány altartománya különleges Azure rozpoznána. Előtag-Beillesztés által *asverify* saját együtt, hogy az egyéni tartomány felismerje a tartomány DNS-rekord módosítása nélkül az Azure engedélyezi. Amikor módosít, a tartomány DNS-rekordját, azt a blob végpontja üzemkimaradás nélkül kell hozzárendelni.

1. Az a [az Azure portal](https://portal.azure.com), lépjen a tárfiókhoz.

1. A menü panelen alatt **Blob Service**válassza **egyéni tartomány**.  
   A **egyéni tartomány** panel nyílik meg.

1. Jelentkezzen be a DNS-szolgáltatónál webhelyre, és keresse meg a DNS kezelésére szolgáló lap.  
   Az oldal egy nevű szakaszban találhatja **tartománynév**, **DNS**, vagy **névkiszolgáló kezelése**.

1. Keresse meg a CNAME-rekordok kezelésére szolgáló felületet.  
   Lehetséges, hogy egy speciális beállítások lapot és keressen **CNAME**, **Alias**, vagy **altartományok**.

1. Hozzon létre egy új CNAME-rekordot, adjon meg egy altartományt alias, amely tartalmazza a *asverify* együtt, mint például **asverify.www** vagy **asverify.photos**, és adja meg egy állomásnevet.  
   A gazdagép nevét a blob-szolgáltatásvégpont. A formátum *asverify.\< mystorageaccount >. blob.core.windows.net*, ahol *mystorageaccount* a tárfiók neve. A gazdagép nevét, a #2 elem jelenik meg a *egyéni tartomány* ablaktábláján a [az Azure portal](https://portal.azure.com).

1. Az a **egyéni tartomány** panelen szövegbeviteli mezőben adja meg az egyéni tartományt az altartománnyal együtt nevét.  
   Nem tartalmaznak *asverify*. Például, ha a tartomány *contoso.com* és az altartomány-alias *www*, adja meg **www.contoso.com**. Ha az altartomány *fényképek*, adja meg **photos.contoso.com**.

1. Válassza ki a **CNAME rekord közvetett ellenőrzésének használata** jelölőnégyzetet.

1. Válassza ki az egyéni tartomány regisztrálása, **mentése**.  
   Ha a regisztráció sikeres, a portál értesíti, hogy a tárfiók frissítése sikerült. Az egyéni tartomány ellenőrzése után az Azure, de a forgalmat a tartományhoz van még irányítása nem a tárfiókhoz.

1. Térjen vissza a DNS-szolgáltatónál webhelyet, és majd hozzon létre egy másik olyan CNAME rekordot, amely a blob service-végpont az altartomány van leképezve.  
   Adja meg az altartományt, mint például *www* vagy *fényképek* (nélkül a *asverify*), és adja meg a gazdagép nevét  *\<mystorageaccount >. blob.core.windows.net*, ahol *mystorageaccount* a tárfiók neve. Az ebben a lépésben az egyéni tartomány a regisztráció befejeződött.

1. Végül, törölheti az újonnan létrehozott CNAME rekordot, amely tartalmazza a *asverify* együtt, amely csak egy köztes lépés szükséges.

Után az új CNAME-rekord rendelkezik propagál DNS használata esetén, ha a felhasználók a megfelelő engedélyekkel rendelkezik, akkor az egyéni tartomány használatával megtekintheti a Blobadatok.

## <a name="test-your-custom-domain"></a>Az egyéni tartomány tesztelése

Győződjön meg arról, hogy az egyéni tartomány le van képezve a blob service-végpont, hozzon létre egy blob egy nyilvános tárolóban, a tárfiókban. Ezután egy webes böngészőben érhető a blob URI-t a következő formátumban: `http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Ha például egy webes űrlap eléréséhez a *myforms* tárolót az a *photos.contoso.com* egyéni együtt, a következő URI Azonosítót használhatja: `http://photos.contoso.com/myforms/applicationform.htm`

## <a name="deregister-a-custom-domain"></a>Egyéni tartomány regisztrációjának megszüntetése

Regisztrációjának megszüntetése a Blob storage-végpont egyéni tartományt, használja az alábbi eljárások egyikét.

### <a name="azure-portal"></a>Azure Portal

Az egyéni tartomány beállítása eltávolításához tegye a következőket:

1. Az a [az Azure portal](https://portal.azure.com), lépjen a tárfiókhoz.

1. A menü panelen alatt **Blob Service**válassza **egyéni tartomány**.  
   A **egyéni tartomány** panel nyílik meg.

1. Az egyéni tartománynevet tartalmazó szövegmező tartalmának törlése.

1. Válassza ki a **Mentés** gombot.

Miután az egyéni tartomány sikeresen el lett távolítva, látni fogja a portál értesítései, hogy a tárfiók frissítése sikerült.

### <a name="azure-cli"></a>Azure CLI

Egy egyéni tartomány regisztrációs eltávolításához használja a [storage-fiók frissítése az](https://docs.microsoft.com/cli/azure/storage/account) CLI parancsot, és adja meg egy üres karakterláncot (`""`) esetében a `--custom-domain` hodnota argumentu.

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

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Egy egyéni tartomány regisztrációs eltávolításához használja a [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) PowerShell-parancsmagot, és adja meg egy üres karakterláncot (`""`) a a `-CustomDomainName` hodnota argumentu.

* A parancs formátuma:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "<resource-group-name>" `
      -AccountName "<storage-account-name>" `
      -CustomDomainName ""
  ```

* Példa:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "myresourcegroup" `
      -AccountName "mystorageaccount" `
      -CustomDomainName ""
  ```

## <a name="next-steps"></a>További lépések
* [Egyéni tartomány leképezése egy Azure Content Delivery Network (CDN) végponthoz](../../cdn/cdn-map-content-to-custom-domain.md)
* [Blobok elérése az egyéni tartományok HTTPS-kapcsolaton keresztül az Azure CDN használatával](storage-https-custom-domain-cdn.md)
* [Statikus webhely üzemeltetése az Azure Blob storage (előzetes verzió)](storage-blob-static-website.md)
