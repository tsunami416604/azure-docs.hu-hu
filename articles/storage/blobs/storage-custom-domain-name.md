---
title: Egyéni tartomány hozzárendelése Az Azure Blob Storage-végponthoz
titleSuffix: Azure Storage
description: Egyéni tartomány hozzárendelése egy Blob Storage vagy webes végpont egy Azure-tárfiókban.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: normesta
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: 9d05677ec47851557594ef47499da653accad141
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370474"
---
# <a name="map-a-custom-domain-to-an-azure-blob-storage-endpoint"></a>Egyéni tartomány hozzárendelése Az Azure Blob Storage-végponthoz

Egyéni tartományt leképezhet egy blobszolgáltatás-végpontra vagy egy [statikus webhelyvégpontra.](storage-blob-static-website.md) 

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

> [!NOTE] 
> Ez a leképezés csak altartományokesetében működik (például: `www.contoso.com`). Ha azt szeretné, hogy a webes végpont elérhető legyen `contoso.com`a gyökértartományban (például: ), akkor az Azure CDN-t kell használnia. Útmutatásért tekintse meg a [cikk HTTPS-kompatibilis egyéni tartományának leképezése](#enable-https) című részét. Mivel a cikk adott szakaszára való bekerülésaz egyéni tartomány gyökértartományának engedélyezéséhez nem kötelező a HTTPS engedélyezésére szolgáló szakasz. 

<a id="enable-http" />

## <a name="map-a-custom-domain-with-only-http-enabled"></a>Egyéni tartomány leképezése csak HTTP protokollal

Ez a megközelítés könnyebb, de csak HTTP-hozzáférést tesz lehetővé. Ha a tárfiók úgy van beállítva, hogy https-en keresztül [imázsbiztonságos átvitelt igényeljen,](../common/storage-require-secure-transfer.md) akkor engedélyeznie kell a HTTPS-hozzáférést az egyéni tartományhoz. 

A HTTPS-hozzáférés engedélyezéséhez tekintse meg a cikk [HTTPS-kompatibilis egyéni tartományának leképezése](#enable-https) című részét. 

<a id="map-a-domain" />

### <a name="map-a-custom-domain"></a>Egyéni tartomány leképezése

> [!IMPORTANT]
> Az egyéni tartomány rövid ideig nem lesz elérhető a felhasználók számára a konfiguráció befejezése közben. Ha a tartomány jelenleg olyan alkalmazást támogat, amely szolgáltatásszintű szerződést (SLA) ír elő, és amely nulla állásidőt igényel, akkor kövesse a cikk [nulla állásidővel rendelkező egyéni tartomány ának leképezése](#zero-down-time) című szakaszlépéseit, hogy a felhasználók hozzáférhessenek a tartományhoz a DNS-hozzárendelés ideje alatt.

Ha nem érdekli, hogy a tartomány rövid ideig nem érhető el a felhasználók számára, kövesse az alábbi lépéseket.

:heavy_check_mark: 1. lépés: A tárolóvégpont állomásnevének beszerezése.

:heavy_check_mark: 2. lépés: Hozzon létre egy gyűjtőnév (CNAME) rekordot a tartományszolgáltatójával.

:heavy_check_mark: 3. lépés: Regisztrálja az egyéni tartományt az Azure-ral. 

:heavy_check_mark: 4. lépés: Tesztelje az egyéni tartományt.

<a id="endpoint" />

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>1. lépés: A tárolási végpont állomásnevének beszerezni 

Az állomásnév a tárolási végpont URL-címe a protokollazonosító és a záró perjel nélkül. 

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg a tárfiókot.

2. A menüablak **Beállítások**területén válassza a **Tulajdonságok lehetőséget.**  

3. Másolja az **elsődleges blobszolgáltatás végpontja** vagy az **elsődleges statikus webhely végpont** értékét egy szöveges fájlba. 

4. Távolítsa el a protokollazonosítót *(pl.* HTTPS) és a záró perjelet a karakterláncból. Az alábbi táblázat példákat tartalmaz.

   | Végpont típusa |  endpoint | állomásnév |
   |------------|-----------------|-------------------|
   |blob szolgáltatás  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |statikus webhely  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   Ezt az értéket későbbre tegye félre.

<a id="create-cname-record" />

#### <a name="step-2-create-a-canonical-name-cname-record-with-your-domain-provider"></a>2. lépés: Gyűjtőnév (CNAME) rekord létrehozása a tartományszolgáltatóval

Hozzon létre egy CNAME rekordot, amely az állomásnevére mutat. A CNAME rekord egy olyan DNS-rekordtípus, amellyel egy forrástartománynév leképezhető egy céltartománynévre.

1. Jelentkezzen be a tartományregisztráló webhelyére, majd lépjen a LAPra a DNS-beállítások kezeléséhez.

   A lap a **Domain Name**, **DNS**vagy Name **Server Management**nevű szakaszban található.

2. Keresse meg a CNAME rekordok kezelésére szolgáló szakaszt. 

   Előfordulhat, hogy egy speciális beállítási lapot kell megkeresnie a **CNAME**, **Alias**vagy **Aldomains**név re.

3. CNAME rekord létrehozása. A rekord részeként adja meg a következő elemeket: 

   - Az altartomány aliasa például a `www` vagy `photos`a . Az altartomány szükséges, a gyökértartományok nem támogatottak. 
      
   - A cikk korábbi részében a [storage-végpont állomásnevének beszerzése](#endpoint) című szakaszban kapott állomásnév. 

<a id="register" />

#### <a name="step-3-register-your-custom-domain-with-azure"></a>3. lépés: Egyéni tartomány regisztrálása az Azure-ral

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg a tárfiókot.

2. A menüablaktábla **Blob Service csoportjában**válassza az **Egyéni tartomány**lehetőséget.  

   ![egyéni tartomány beállítás](./media/storage-custom-domain-name/custom-domain-button.png "egyéni tartomány")

   Megnyílik **az Egyéni tartomány** ablaktábla.

3. A **Domain name (Tartománynév)** mezőbe írja be az egyéni tartomány nevét, beleértve az altartományt is.  
   
   Ha például a tartomány *contoso.com,* és az altartomány aliasa *www,* írja be a mezőbe. `www.contoso.com` Ha az aldomain *fényképek,* írja be a mezőbe. `photos.contoso.com`

4. Az egyéni tartomány regisztrálásához kattintson a **Mentés** gombra.

   Miután a CNAME rekord propagálása a TARTOMÁNYnév-kiszolgálókon (DNS) keresztül történt, és ha a felhasználók rendelkeznek a megfelelő engedélyekkel, megtekinthetik a blobadatokat az egyéni tartomány használatával.

#### <a name="step-4-test-your-custom-domain"></a>4. lépés: Az egyéni tartomány tesztelése

Annak ellenőrzéséhez, hogy az egyéni tartomány le van képezve a blob szolgáltatás végpontjára, hozzon létre egy blobot egy nyilvános tárolóban a tárfiókon belül. Ezt követően egy webböngészőben a következő formátumban, URI használatával érheti el a blobot:`http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Ha például az *photos.contoso.com egyéni* altartomány *myforms* tárolójában lévő webes űrlapokat szeretné elérni, a következő URI-t használhatja:`http://photos.contoso.com/myforms/applicationform.htm`

<a id="zero-down-time" />

### <a name="map-a-custom-domain-with-zero-downtime"></a>Egyéni tartomány hozzárendelése nulla állásidővel

> [!NOTE]
> Ha nem érdekli, hogy a tartomány rövid ideig nem érhető el a felhasználók számára, akkor kövesse a cikk [Egyéni tartomány leképezése](#map-a-domain) című szakaszának lépéseit. Ez egy egyszerűbb megközelítés kevesebb lépéssel.  

Ha a tartomány jelenleg olyan alkalmazást támogat, amely szolgáltatásszintű szerződést (SLA) ír elő, és amely nulla állásidőt igényel, akkor hajtsa végre az alábbi lépéseket annak érdekében, hogy a felhasználók a DNS-hozzárendelés ideje alatt hozzáférhessenek a tartományhoz. 

:heavy_check_mark: 1. lépés: A tárolóvégpont állomásnevének beszerezése.

:heavy_check_mark: 2. lépés: Hozzon létre egy köztes kanonikus név (CNAME) rekordot a tartományszolgáltatójával.

:heavy_check_mark: 3. lépés: Regisztrálja előre az egyéni tartományt az Azure-ral.

:heavy_check_mark: 4. lépés: Hozzon létre egy CNAME rekordot a tartományszolgáltatójával.

:heavy_check_mark: 5. lépés: Tesztelje az egyéni tartományt.

<a id="endpoint-2" />

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>1. lépés: A tárolási végpont állomásnevének beszerezni 

Az állomásnév a tárolási végpont URL-címe a protokollazonosító és a záró perjel nélkül. 

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg a tárfiókot.

2. A menüablak **Beállítások**területén válassza a **Tulajdonságok lehetőséget.**  

3. Másolja az **elsődleges blobszolgáltatás végpontja** vagy az **elsődleges statikus webhely végpont** értékét egy szöveges fájlba. 

4. Távolítsa el a protokollazonosítót *(pl.* HTTPS) és a záró perjelet a karakterláncból. Az alábbi táblázat példákat tartalmaz.

   | Végpont típusa |  endpoint | állomásnév |
   |------------|-----------------|-------------------|
   |blob szolgáltatás  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |statikus webhely  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   Ezt az értéket későbbre tegye félre.

#### <a name="step-2-create-a-intermediary-canonical-name-cname-record-with-your-domain-provider"></a>2. lépés: Köztes kanonikus név (CNAME) rekord létrehozása a tartományszolgáltatóval

Hozzon létre egy ideiglenes CNAME rekordot, amely az állomásnévre mutat. A CNAME rekord egy olyan DNS-rekordtípus, amellyel egy forrástartománynév leképezhető egy céltartománynévre.

1. Jelentkezzen be a tartományregisztráló webhelyére, majd lépjen a LAPra a DNS-beállítások kezeléséhez.

   A lap a **Domain Name**, **DNS**vagy Name **Server Management**nevű szakaszban található.

2. Keresse meg a CNAME rekordok kezelésére szolgáló szakaszt. 

   Előfordulhat, hogy egy speciális beállítási lapot kell megkeresnie a **CNAME**, **Alias**vagy **Aldomains**név re.

3. CNAME rekord létrehozása. A rekord részeként adja meg a következő elemeket: 

   - Az altartomány aliasa például a `www` vagy `photos`a . Az altartomány szükséges, a gyökértartományok nem támogatottak.

     Adja `asverify` hozzá az altartományt az aliashoz. Például: `asverify.www` `asverify.photos`vagy .
       
   - A cikk korábbi részében a [storage-végpont állomásnevének beszerzése](#endpoint) című szakaszban kapott állomásnév. 

     Adja hozzá `asverify` az altartományt az állomásnévhez. Például: `asverify.mystorageaccount.blob.core.windows.net`.

4. Az egyéni tartomány regisztrálásához kattintson a **Mentés** gombra.

   Ha a regisztráció sikeres, a portál értesíti, hogy a tárfiók frissítése sikeresen megtörtént. Az egyéni tartományt az Azure ellenőrizte, de a tartományra irányuló forgalom még nincs átirányítva a tárfiókba.

#### <a name="step-3-pre-register-your-custom-domain-with-azure"></a>3. lépés: Regisztrálja előre az egyéni tartományt az Azure-ral

Amikor előre regisztrálja egyéni tartományát az Azure-ban, lehetővé teszi az Azure számára, hogy az egyéni tartományt anélkül ismerje fel, hogy módosítania kellene a tartomány DNS-rekordját. Így, ha módosítja a DNS-rekord a tartomány, akkor lesz leképezve a blob végpont nélkül állásidő.

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg a tárfiókot.

2. A menüablaktábla **Blob Service csoportjában**válassza az **Egyéni tartomány**lehetőséget.  

   ![egyéni tartomány beállítás](./media/storage-custom-domain-name/custom-domain-button.png "egyéni tartomány")

   Megnyílik **az Egyéni tartomány** ablaktábla.

3. A **Domain name (Tartománynév)** mezőbe írja be az egyéni tartomány nevét, beleértve az altartományt is.  
   
   Ha például a tartomány *contoso.com,* és az altartomány aliasa *www,* írja be a mezőbe. `www.contoso.com` Ha az aldomain *fényképek,* írja be a mezőbe. `photos.contoso.com`

4. Jelölje be a **Közvetett CNAME érvényesítésének használata** jelölőnégyzetet.

5. Az egyéni tartomány regisztrálásához kattintson a **Mentés** gombra.
  
   Miután a CNAME rekord propagálása a TARTOMÁNYnév-kiszolgálókon (DNS) keresztül történt, és ha a felhasználók rendelkeznek a megfelelő engedélyekkel, megtekinthetik a blobadatokat az egyéni tartomány használatával.

#### <a name="step-4-create-a-cname-record-with-your-domain-provider"></a>4. lépés: CNAME rekord létrehozása a tartományszolgáltatóval

Hozzon létre egy ideiglenes CNAME rekordot, amely az állomásnévre mutat.

1. Jelentkezzen be a tartományregisztráló webhelyére, majd lépjen a LAPra a DNS-beállítások kezeléséhez.

   A lap a **Domain Name**, **DNS**vagy Name **Server Management**nevű szakaszban található.

2. Keresse meg a CNAME rekordok kezelésére szolgáló szakaszt. 

   Előfordulhat, hogy egy speciális beállítási lapot kell megkeresnie a **CNAME**, **Alias**vagy **Aldomains**név re.

3. CNAME rekord létrehozása. A rekord részeként adja meg a következő elemeket: 

   - Az altartomány aliasa például a `www` vagy `photos`a . Az altartomány szükséges, a gyökértartományok nem támogatottak.
      
   - A cikk korábbi részében a [storage-végpont állomásnevének beszerzése](#endpoint-2) című szakaszban kapott állomásnév. 

#### <a name="step-5-test-your-custom-domain"></a>5. lépés: Az egyéni tartomány tesztelése

Annak ellenőrzéséhez, hogy az egyéni tartomány le van képezve a blob szolgáltatás végpontjára, hozzon létre egy blobot egy nyilvános tárolóban a tárfiókon belül. Ezt követően egy webböngészőben a következő formátumban, URI használatával érheti el a blobot:`http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Ha például az *photos.contoso.com egyéni* altartomány *myforms* tárolójában lévő webes űrlapokat szeretné elérni, a következő URI-t használhatja:`http://photos.contoso.com/myforms/applicationform.htm`

### <a name="remove-a-custom-domain-mapping"></a>Egyéni tartományleképezés eltávolítása

Egyéni tartományleképezés eltávolításához szüntesse meg az egyéni tartomány regisztrációjának megszüntetését. Használja az alábbi eljárások egyikét.

#### <a name="portal"></a>[Portál](#tab/azure-portal)

Az egyéni tartománybeállítás eltávolításához tegye a következőket:

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg a tárfiókot.

2. A menüablaktábla **Blob Service csoportjában**válassza az **Egyéni tartomány**lehetőséget.  
   Megnyílik **az Egyéni tartomány** ablaktábla.

3. Törölje az egyéni tartománynevet tartalmazó szövegdoboz tartalmának törlését.

4. Válassza ki a **Mentés** gombot.

Az egyéni tartomány sikeres eltávolítása után megjelenik egy portálértesítés a tárfiók sikeres frissítéséről

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Egyéni tartományregisztráció eltávolításához használja az [az storage account update](https://docs.microsoft.com/cli/azure/storage/account) CLI parancsot, majd adjon meg egy üres karakterláncot (`""`) az `--custom-domain` argumentum értékéhez.

* Parancsformátum:

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

#### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Egyéni tartományregisztráció eltávolításához használja a [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) PowerShell-parancsmagot,`""`majd adjon `-CustomDomainName` meg egy üres karakterláncot ( ) az argumentum értékéhez.

* Parancsformátum:

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
---

<a id="enable-https" />

## <a name="map-a-custom-domain-with-https-enabled"></a>Egyéni tartomány hozzárendelése HTTPS-kompatibilis sel

Ez a megközelítés további lépéseket foglal magában, de https-hozzáférést tesz lehetővé. 

Ha nincs szüksége a felhasználók eléréséhez a blob vagy a webes tartalom https használatával, majd tekintse meg az [egyéni tartomány leképezése csak HTTP-kompatibilis](#enable-http) szakasza a cikkben. 

Egyéni tartomány leképezéséhez és a HTTPS-hozzáférés engedélyezéséhez tegye a következőket:

1. Engedélyezze [az Azure CDN-t](../../cdn/cdn-overview.md) a blobon vagy a webes végponton. 

   Blob Storage-végpont esetén [olvassa el Az Azure-tárfiók integrálása az Azure CDN-nel.](../../cdn/cdn-create-a-storage-account-with-cdn.md) 

   Statikus webhely-végpont esetén [olvassa el A statikus webhely integrálása az Azure CDN-nel című témakört.](static-website-content-delivery-network.md)

2. [Az Azure CDN-tartalom hozzárendelése egyéni tartományhoz.](../../cdn/cdn-map-content-to-custom-domain.md)

3. [Https engedélyezése Azure CDN-alapú egyéni tartományban.](../../cdn/cdn-custom-ssl.md)

   > [!NOTE] 
   > A statikus webhely frissítésekor a CDN-végpont törlésével törölje a gyorsítótárazott tartalmat a CDN peremhálózati kiszolgálókon. További információkért lásd az [Azure CDN-végpontok végleges törléséről](../../cdn/cdn-purge-endpoint.md) szóló cikket.

4. (Nem kötelező) Tekintse át a következő útmutatást:

   * [Megosztott hozzáférés-aláírási (SAS) jogkivonatok az Azure CDN-nel.](https://docs.microsoft.com/azure/cdn/cdn-storage-custom-domain-https#shared-access-signatures)

   * [HTTP-HTTPS átirányítás az Azure CDN-nel.](https://docs.microsoft.com/azure/cdn/cdn-storage-custom-domain-https#http-to-https-redirection)

   * [Díjszabás és számlázás a Blob Storage azure CDN-nel való használata kor.](https://docs.microsoft.com/azure/cdn/cdn-storage-custom-domain-https#http-to-https-redirection)

## <a name="next-steps"></a>További lépések

* [További információ az Azure Blob storage statikus webhelyüzemeltetéséről](storage-blob-static-website.md)
