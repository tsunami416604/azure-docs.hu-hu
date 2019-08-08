---
title: Egyéni tartománynév beállítása az Azure Storage-fiókhoz | Microsoft Docs
description: Az Azure Portal használatával képezhető le saját kanonikus nevüket (CNAME) a blob Storage-ba vagy egy Azure Storage-fiók webes végpontján.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: normesta
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: ae809dd4841d6df8caabebfaded263b52f386834
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68845035"
---
# <a name="configure-a-custom-domain-name-for-your-azure-storage-account"></a>Egyéni tartománynév beállítása az Azure Storage-fiókhoz

A blob-adatok Azure Storage-fiókban való eléréséhez egyéni tartományt is beállíthat. Az Azure Blob Storage  *\<alapértelmezett végpontja a Storage-Account-Name >. blob. Core. Windows. net*. Használhatja a [statikus webhelyek szolgáltatás](storage-blob-static-website.md)részeként generált webes végpontot is. Ha az egyéni tartományt és altartományt (például a *www\.contoso.com*) a Storage-fiókhoz tartozó blob vagy webes végpontra képezi le, a felhasználók ezt a tartományt használhatják a Storage-fiókban található Blobok eléréséhez.

> [!IMPORTANT]
> Az Azure Storage még nem támogatja natív módon a HTTPS-t az egyéni tartományokkal. Jelenleg Azure CDN használatával [férhet hozzá a blobokhoz egyéni tartományok használatával HTTPS-kapcsolaton keresztül](storage-https-custom-domain-cdn.md).
> 
> 
> [!NOTE]
> A Storage-fiókok jelenleg csak egy egyéni tartománynevet támogatnak. Egyéni tartománynevet nem képezhető le a webes és a blob szolgáltatásbeli végpontokra is.
> 
> [!NOTE]
> A leképezés csak altartományok esetében működik (például www\.contoso.com). Ha szeretné, hogy a webes végpont elérhető legyen a legfelső szintű tartományban (például contoso.com), akkor a Azure CDNt [Egyéni tartományokkal](storage-https-custom-domain-cdn.md) kell használnia

Az alábbi táblázatban néhány példa látható a *mystorageaccount*nevű Storage-fiókban található blob-adatra. A Storage-fiókhoz regisztrált egyéni altartomány a *www\.contoso.com*:

| Erőforrás típusa | Alapértelmezett URL-cím | Egyéni tartomány URL-címe |
| --- | --- | --- |
| Tárfiók | http:\//mystorageaccount.blob.Core.Windows.net | http:\//www.contoso.com |
| Blob |http:\//mystorageaccount.blob.Core.Windows.net/mycontainer/myblob | http:\//www.contoso.com/mycontainer/myblob |
| Gyökér tároló | http:\//mystorageaccount.blob.Core.Windows.net/myblob vagy http:\//mystorageaccount.blob.Core.Windows.net/$root/myblob | http:\//www.contoso.com/myblob vagy http:\//www.contoso.com/$root/myblob |
| Web |  http:\//mystorageaccount. [ Zone]. Web. Core. Windows. net/$Web/[indexdoc] vagy http:\//mystorageaccount. [ Zone]. Web. Core. Windows. net/[indexdoc] vagy http:\//mystorageaccount. [ Zone]. Web. Core. Windows. net/$Web vagy http:\//mystorageaccount. [ Zone]. Web. Core. Windows. net/ | http:\//www.contoso.com/$web vagy http:\//www.contoso.com/vagy http:\//www.contoso.com/$web/[indexdoc] vagy http:\//www.contoso.com/[indexdoc] |

> [!NOTE]  
> Ahogy az a következő szakaszban is látható, a blob Service-végpontra vonatkozó összes példa a webszolgáltatás-végpontra is érvényes.

## <a name="direct-vs-intermediary-cname-mapping"></a>Közvetlen és közbenső CNAME-megfeleltetés

Az előrögzített egyéni tartományt egy altartománnyal (például a www\.contoso.com) is megadhatja a Storage-fiók blob-végpontja számára a következő két módszer egyikével: 
* Használjon közvetlen CNAME leképezést.
* Használja a *asverify* közbenső altartományt.

### <a name="direct-cname-mapping"></a>Közvetlen CNAME-leképezés

Az első és legegyszerűbb módszer egy kanonikus név (CNAME) rekord létrehozása, amely az egyéni tartományt és altartományt közvetlenül a blob-végpontra képezi le. A CNAME record is a domain name system (DNS) feature that maps a source domain to a destination domain. A példánkban a forrástartomány a saját egyéni tartománya és altartománya (például a*www\.-contoso.com*). A cél tartomány a blob Service-végpont (például*mystorageaccount.blob.Core.Windows.net*).

A közvetlen metódus az "egyéni tartomány regisztrálása" szakaszban szerepel.

### <a name="intermediary-mapping-with-asverify"></a>Közvetítői leképezés a *asverify*

A második metódus CNAME rekordokat is használ. Az állásidő elkerüléséhez azonban először egy, az Azure által elismert altartomány- *asverify* alkalmaz.

Az egyéni tartomány blob-végpontra való leképezése rövid állásidőt okozhat a tartománynak a [Azure Portal](https://portal.azure.com)való regisztrálása közben. Ha a tartomány jelenleg olyan szolgáltatói szerződéssel (SLA) rendelkező alkalmazást támogat, amely nulla állásidőt igényel, használja az Azure *asverify* altartományt közbenső regisztrációs lépésként. Ez a lépés biztosítja, hogy a felhasználók hozzáférhessenek a tartományhoz, miközben a DNS-megfeleltetés zajlik.

A köztes metódust az egyéni tartomány regisztrálása a *asverify* altartomány használatával című rész tartalmazza.

## <a name="register-a-custom-domain"></a>Egyéni tartomány regisztrálása
Regisztrálja a tartományt az ebben a részben ismertetett eljárással, ha az alábbi utasítások érvényesek:
* Nem aggódik amiatt, hogy a tartomány rövid ideig nem érhető el a felhasználók számára.
* Az egyéni tartomány jelenleg nem üzemeltet alkalmazást. 

A Azure DNS használatával egyéni DNS-nevet konfigurálhat az Azure Blob-tárolóhoz. További információt az [egyéni tartománybeállítások egy Azure-szolgáltatáshoz az Azure DNS használatával történő megadását](https://docs.microsoft.com/azure/dns/dns-custom-domain#blob-storage) ismertető cikkben talál.

Ha az egyéni tartomány jelenleg olyan alkalmazást támogat, amely nem tartalmazhat állásidőt, használja az egyéni tartomány regisztrálása a *asverify* altartomány használatával című eljárást.

Egyéni tartománynév konfigurálásához hozzon létre egy új CNAME rekordot a DNS-ben. A CNAME rekord egy tartománynév aliasát adja meg. A példánkban leképezi az egyéni tartomány címeit a Storage-fiók blob Storage-végpontján.

A tartomány DNS-beállításait általában a tartományregisztráló webhelyén kezelheti. Az egyes regisztrátorok hasonló, de némileg eltérő módon határozzák meg a CNAME rekordot, de a koncepció ugyanaz. Mivel egyes alapszintű tartományi regisztrációs csomagok nem biztosítanak DNS-konfigurációt, előfordulhat, hogy a CNAME rekord létrehozása előtt frissítenie kell a tartomány regisztrációs csomagját.

1. A [Azure Portal](https://portal.azure.com)nyissa meg a Storage-fiókját.

1. A párbeszédpanel **blob Service**területén válassza az **egyéni tartomány**lehetőséget.  
   Megnyílik az **egyéni tartomány** panel.

1. Jelentkezzen be a tartományregisztráló webhelyére, majd nyissa meg a lapot a DNS kezeléséhez.  
   Előfordulhat, hogy a lapot a **tartománynév**, a **DNS**vagy a **Névkiszolgálók kezelése**nevű szakaszban találja.

1. Keresse meg a CNAME-rekordok kezelésére szolgáló felületet.  
   Előfordulhat, hogy a speciális beállítások lapra kell lépnie, és meg kell keresnie a **CNAME**, **alias**vagy altartományokat.

1. Hozzon létre egy új CNAME rekordot, adjon meg egy altartomány-aliast, például a **www** vagy a **photos** (altartomány szükséges, a gyökértartomány nem támogatott), majd adjon meg egy állomásnevet.  
   A gazdagép neve a blob Service-végpont. A formátuma  *\<a mystorageaccount >. blob. Core. Windows. net*, ahol a *mystorageaccount* a Storage-fiók neve. A használni kívánt állomásnév a [Azure Portal](https://portal.azure.com) **egyéni tartomány** paneljének #1 elemében jelenik meg. 

1. Az **egyéni tartomány** ablaktáblában, a szövegmezőben adja meg az egyéni tartomány nevét, beleértve az altartományt is.  
   Ha például a tartománya *contoso.com* , és az altartomány aliasa a *www*, írja be a következőt: **www\.contoso.com**. Ha az altartomány *fényképek*, adja meg a **photos.contoso.com**.

1. Az egyéni tartomány regisztrálásához válassza a **Mentés**lehetőséget.  
   Ha a regisztráció sikeres, a portál értesíti arról, hogy a Storage-fiók frissítése sikeresen megtörtént.

Miután az új CNAME rekordot propagálta a DNS-en keresztül, ha a felhasználók rendelkeznek a megfelelő engedélyekkel, megtekinthetik a Blobok adatait az egyéni tartomány használatával.

## <a name="register-a-custom-domain-by-using-the-asverify-subdomain"></a>Egyéni tartomány regisztrálása a *asverify* altartomány használatával
Ha az egyéni tartomány jelenleg egy SLA-val rendelkező alkalmazást támogat, amely megköveteli, hogy ne legyen leállás, regisztrálja az egyéni tartományt az ebben a részben ismertetett eljárással. Egy asverify-ból származó CNAME-rekord létrehozásával *.\< altartomány >. a\<customdomain >* *asverify.\< storageaccount >. blob. Core. Windows. net*, előre regisztrálhat tartományát az Azure-ban. Ezután létrehozhat egy második CNAME-t, amely az  *\<altartományból >\< mutat. a customdomain >* a  *\<storageaccount >. blob. Core. Windows. net*, majd az egyéni tartomány felé irányuló adatforgalmat a blob-végpontra irányítja.

A *asverify* altartomány az Azure által elismert speciális altartomány. A saját altartománya *asverify* függően lehetővé teszi, hogy az Azure felismerje az egyéni tartományt anélkül, hogy módosítania kellene a tartomány DNS-rekordját. Ha módosítja a tartomány DNS-rekordját, a rendszer leképezi a blob-végpontra a leállás nélkül.

1. A [Azure Portal](https://portal.azure.com)nyissa meg a Storage-fiókját.

1. A párbeszédpanel **blob Service**területén válassza az **egyéni tartomány**lehetőséget.  
   Megnyílik az **egyéni tartomány** panel.

1. Jelentkezzen be a DNS-szolgáltató webhelyére, majd nyissa meg a lapot a DNS kezeléséhez.  
   Előfordulhat, hogy a lapot a **tartománynév**, a **DNS**vagy a **Névkiszolgálók kezelése**nevű szakaszban találja.

1. Keresse meg a CNAME-rekordok kezelésére szolgáló felületet.  
   Előfordulhat, hogy a speciális beállítások lapra kell lépnie, és meg kell keresnie a **CNAME**, **alias**vagy altartományokat.

1. Hozzon létre egy új CNAME-rekordot, adjon meg egy altartomány-aliast, amely tartalmazza a *asverify* altartományt, például a **asverify. www** vagy a **asverify. photos**nevet, majd adjon meg egy állomásnevet.  
   A gazdagép neve a blob Service-végpont. A formátuma *asverify.\< mystorageaccount >. blob. Core. Windows. net*, ahol a *mystorageaccount* a Storage-fiók neve. A használni kívánt állomásnév a [Azure Portal](https://portal.azure.com) *egyéni tartomány* paneljének #2 elemében jelenik meg.

1. Az **egyéni tartomány** ablaktáblában, a szövegmezőben adja meg az egyéni tartomány nevét, beleértve az altartományt is.  
   Ne foglalja bele a *asverify*. Ha például a tartománya *contoso.com* , és az altartomány aliasa a *www*, írja be a következőt: **www\.contoso.com**. Ha az altartomány *fényképek*, adja meg a **photos.contoso.com**.

1. Jelölje be a **közvetlen CNAME-ellenőrzés használata** jelölőnégyzetet.

1. Az egyéni tartomány regisztrálásához válassza a **Mentés**lehetőséget.  
   Ha a regisztráció sikeres, a portál értesíti arról, hogy a Storage-fiók frissítése sikeresen megtörtént. Az egyéni tartományt az Azure ellenőrizte, de a tartományra irányuló forgalom még nincs átirányítva a Storage-fiókba.

1. Térjen vissza a DNS-szolgáltató webhelyére, majd hozzon létre egy másik CNAME rekordot, amely leképezi az altartományt a blob Service-végpontra.  
   Adja meg például a *www* vagy a photos ( *asverify*nélkül) altartományt, és  *\<adja meg az állomásnevet mystorageaccount >. blob. Core. Windows. net*néven, ahol a mystorageaccount a neve. Storage-fiók. Ezzel a lépéssel elkészült az egyéni tartomány regisztrálása.

1. Végezetül törölheti az újonnan létrehozott CNAME rekordot, amely a *asverify* altartományt tartalmazza, ami csak közvetítő lépésként szükséges.

Miután az új CNAME rekordot propagálta a DNS-en keresztül, ha a felhasználók rendelkeznek a megfelelő engedélyekkel, megtekinthetik a Blobok adatait az egyéni tartomány használatával.

## <a name="test-your-custom-domain"></a>Egyéni tartomány tesztelése

Annak ellenőrzéséhez, hogy az egyéni tartomány hozzá van-e rendelve a blob Service-végponthoz, hozzon létre egy blobot a Storage-fiókban lévő nyilvános tárolóban. Ezután egy webböngészőben nyissa meg a blobot egy URI használatával a következő formátumban:`http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Ha például egy webes űrlapot szeretne elérni a *myForms* -tárolóban a *photos.contoso.com* egyéni altartományában, a következő URI-t használhatja:`http://photos.contoso.com/myforms/applicationform.htm`

## <a name="deregister-a-custom-domain"></a>Egyéni tartomány regisztrációjának törlése

A blob Storage-végponthoz tartozó egyéni tartomány regisztrációjának megszüntetéséhez használja az alábbi eljárások egyikét.

### <a name="azure-portal"></a>Azure Portal

Az egyéni tartomány beállításának eltávolításához tegye a következőket:

1. A [Azure Portal](https://portal.azure.com)nyissa meg a Storage-fiókját.

1. A párbeszédpanel **blob Service**területén válassza az **egyéni tartomány**lehetőséget.  
   Megnyílik az **egyéni tartomány** panel.

1. Törölje az egyéni tartománynevet tartalmazó szövegmező tartalmát.

1. Válassza ki a **Mentés** gombot.

Miután sikeresen eltávolította az egyéni tartományt, megjelenik egy portál értesítés arról, hogy a Storage-fiók frissítése sikeresen megtörtént.

### <a name="azure-cli"></a>Azure CLI

Egyéni tartományi regisztráció eltávolításához használja az az [Storage Account Update](https://docs.microsoft.com/cli/azure/storage/account) CLI parancsot, majd adja meg az`""` `--custom-domain` argumentum értékének üres karakterláncát ().

* Parancs formátuma:

  ```azurecli
  az storage account update \
      --name <storage-account-name> \
      --resource-group <resource-group-name> \
      --custom-domain ""
  ```

* Példa a parancsra:

  ```azurecli
  az storage account update \
      --name mystorageaccount \
      --resource-group myresourcegroup \
      --custom-domain ""
  ```

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Egyéni tartományi regisztráció eltávolításához használja a [set-AzStorageAccount PowerShell-](/powershell/module/az.storage/set-azstorageaccount) parancsmagot, majd adja meg az`""` `-CustomDomainName` argumentum értékének üres karakterláncát ().

* Parancs formátuma:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "<resource-group-name>" `
      -AccountName "<storage-account-name>" `
      -CustomDomainName ""
  ```

* Példa a parancsra:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "myresourcegroup" `
      -AccountName "mystorageaccount" `
      -CustomDomainName ""
  ```

## <a name="next-steps"></a>További lépések
* [Egyéni tartomány leképezése egy Azure Content Delivery Network (CDN) végpontra](../../cdn/cdn-map-content-to-custom-domain.md)
* [Blobok elérésének Azure CDN használata egyéni tartományok használatával HTTPS-kapcsolaton keresztül](storage-https-custom-domain-cdn.md)
* [Statikus webhely üzemeltetése az Azure Blob Storage-ban (előzetes verzió)](storage-blob-static-website.md)
