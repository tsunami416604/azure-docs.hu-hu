---
title: Statikus webhely üzemeltetése az Azure Storage-ban
description: Megtudhatja, hogyan szolgálhat statikus tartalom (HTML, CSS, JavaScript és képfájlok) közvetlenül egy Azure Storage GPv2-fiók tárolójából.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 03/04/2020
ms.openlocfilehash: 056e23f0f0cf1a3a1c70042cef3c92dd41f14f82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247010"
---
# <a name="host-a-static-website-in-azure-storage"></a>Statikus webhely üzemeltetése az Azure Storage-ban

Statikus tartalom (HTML, CSS, JavaScript és képfájlok) közvetlenül egy Azure Storage GPv2-fiók tárolóból is kiszolgálható. További információ: [Static website hosting in Azure Storage](storage-blob-static-website.md).

Ez a cikk bemutatja, hogyan engedélyezheti a statikus webhelyüzemeltetést az Azure Portal, az Azure CLI vagy a PowerShell használatával.

## <a name="enable-static-website-hosting"></a>Statikus webhely-üzemeltetés engedélyezése

Statikus webhely üzemeltetése egy olyan funkció, amelyet engedélyeznie kell a tárfiókban.

### <a name="portal"></a>[Portál](#tab/azure-portal)

1. A kezdéshez jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

2. Keresse meg a tárfiókot, és jelenítse meg a fiók áttekintését.

3. Válassza **a Statikus webhely** lehetőséget a statikus webhelyek konfigurációs lapjának megjelenítéséhez.

4. Válassza **az Engedélyezve lehetőséget** a statikus webhely-üzemeltetés engedélyezéséhez a tárfiókhoz.

5. A **Dokumentum névindexe** mezőben adjon meg egy alapértelmezett indexlapot (például: *index.html*). 

   Az alapértelmezett indexlap akkor jelenik meg, amikor a felhasználó a statikus webhely gyökerére navigál.  

6. A **Hiba dokumentum elérési útja** mezőben adjon meg egy alapértelmezett hibalapot (például: *404.html*). 

   Az alapértelmezett hibalap akkor jelenik meg, amikor a felhasználó olyan oldalra próbál navigálni, amely nem létezik a statikus webhelyen.

7. Kattintson a **Mentés** gombra. Az Azure Portal most megjeleníti a statikus webhely-végpont. 

    ![Statikus webhely-üzemeltetés engedélyezése tárfiókhoz](media/storage-blob-static-website-host/enable-static-website-hosting.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

<a id="cli" />

A statikus webhelyüzemeltetést az [Azure parancssori felület (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)használatával engedélyezheti.

1. Először nyissa meg az [Azure Cloud Shellt,](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)vagy ha helyileg [telepítette](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) az Azure CLI-t, nyisson meg egy parancskonzolalkalmazást, például a Windows PowerShellt.

2. Ha az identitás egynél több előfizetéshez van társítva, állítsa be az aktív előfizetést a statikus webhelyet tároló tárfiók előfizetésére.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Cserélje `<subscription-id>` le a helyőrző értékét az előfizetés azonosítójára.

3. Statikus webhely-üzemeltetés engedélyezése.

   ```azurecli-interactive
   az storage blob service-properties update --account-name <storage-account-name> --static-website --404-document <error-document-name> --index-document <index-document-name>
   ```

   * Cserélje `<storage-account-name>` le a helyőrző értéket a tárfiók nevére.

   * Cserélje `<error-document-name>` le a helyőrzőt annak a hibaüzenetnek a nevére, amely akkor jelenik meg a felhasználók számára, ha a böngésző nem létező lapot kér a webhelyen.

   * Cserélje `<index-document-name>` le a helyőrzőt a tárgymutató-dokumentum nevére. Ez a dokumentum általában "index.html".

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

<a id="powershell" />

Az Azure PowerShell-modul használatával statikus webhelyüzemeltetést engedélyezhet.

1. Nyisson meg egy Windows PowerShell parancsablakot.

2. Ellenőrizze, hogy rendelkezik-e az Azure PowerShell-modul Az 0.7-es vagy újabb verziójával.

   ```powershell
   Get-InstalledModule -Name Az -AllVersions | select Name,Version
   ```

   Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket.

3. Jelentkezzen be az Azure-előfizetésbe a `Connect-AzAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

   ```powershell
   Connect-AzAccount
   ```

4. Ha az identitás egynél több előfizetéshez van társítva, állítsa be az aktív előfizetést a statikus webhelyet tároló tárfiók előfizetésére.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Cserélje `<subscription-id>` le a helyőrző értékét az előfizetés azonosítójára.

5. A storage-fiók környezetét, amely meghatározza a használni kívánt tárfiókot.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Cserélje `<resource-group-name>` le a helyőrző értéket az erőforráscsoport nevére.

   * Cserélje `<storage-account-name>` le a helyőrző értéket a tárfiók nevére.

6. Statikus webhely-üzemeltetés engedélyezése.

   ```powershell
   Enable-AzStorageStaticWebsite -Context $ctx -IndexDocument <index-document-name> -ErrorDocument404Path <error-document-name>
   ```

   * Cserélje `<error-document-name>` le a helyőrzőt annak a hibaüzenetnek a nevére, amely akkor jelenik meg a felhasználók számára, ha a böngésző nem létező lapot kér a webhelyen.

   * Cserélje `<index-document-name>` le a helyőrzőt a tárgymutató-dokumentum nevére. Ez a dokumentum általában "index.html".

---

## <a name="upload-files"></a>Fájlok feltöltése 

### <a name="portal"></a>[Portál](#tab/azure-portal)

Ezek az utasítások bemutatják, hogyan tölthet fel fájlokat az Azure Portalon megjelenő Storage Explorer verziójával. Azonban használhatja a Storage [Explorer](https://azure.microsoft.com/features/storage-explorer/) verzióját is, amely az Azure Portalon kívül fut. Használhatja [az AzCopy](../common/storage-use-azcopy-v10.md), PowerShell, CLI, vagy bármely egyéni alkalmazás, amely képes feltölteni a fájlokat a **$web** a fiók tárolójára. A Visual Studio-kód használatával fájlokat feltöltő részletes oktatóanyagról [az Oktatóanyag: Statikus webhely üzemeltetése a Blob Storage webhelyen](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host)című témakörben található.

1. Válassza a **Tárolókezelő (előzetes verzió) lehetőséget.**

2. Bontsa ki a **BLOB CONTAINERS** csomópontot, majd jelölje ki a **$web** tárolót.

3. A fájlok feltöltéséhez kattintson a **Feltöltés** gombra.

   ![Fájlok feltöltése](media/storage-blob-static-website/storage-blob-static-website-upload.png)

4. Ha azt szeretné, hogy a böngésző megjelenítse a fájl tartalmát, `text/html`győződjön meg arról, hogy a fájl tartalomtípusa a . 

   ![Tartalomtípusok ellenőrzése](media/storage-blob-static-website/storage-blob-static-website-content-type.png)

   >[!NOTE]
   > A Storage Explorer automatikusan beállítja ezt a tulajdonságot `text/html` a gyakran elismert bővítményekre, például `.html`a. Bizonyos esetekben azonban ezt magának kell beállítania. Ha nem állítja ezt `text/html`a tulajdonságot, a böngésző a tartalom megjelenítése helyett a fájl letöltését kéri a felhasználóktól. A tulajdonság beállításához kattintson a jobb gombbal a fájlra, majd kattintson a **Tulajdonságok parancsra.**

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Objektumok feltöltése a *$web* tárolóba egy forráskönyvtárból.

> [!NOTE]
> Ha Az Azure Cloud Shell, győződjön `\` meg róla, hogy `$web` adjon hozzá egy `\$web`escape karakter, amikor hivatkozva a tároló (Például: ). Ha az Azure CLI helyi telepítését használja, akkor nem kell használnia a escape karaktert.

Ez a példa feltételezi, hogy parancsokat futtat az Azure Cloud Shell-munkamenetből.

```azurecli-interactive
az storage blob upload-batch -s <source-path> -d \$web --account-name <storage-account-name> --content-type 'text/html; charset=utf-8'
```

* Cserélje `<storage-account-name>` le a helyőrző értéket a tárfiók nevére.

* Cserélje `<source-path>` le a helyőrzőt a feltölteni kívánt fájlok helyére vezető elérési útra.

> [!NOTE]
> Ha az Azure CLI helytelepítését használja, akkor használhatja a helyi számítógép bármely helyéhez vezető elérési utat (például: `C:\myFolder`.
>
> Ha Az Azure Cloud Shell, akkor hivatkoznia kell egy fájlmegosztás, amely látható a Cloud Shell. Ez a hely lehet a felhőmegosztás fájlmegosztása, vagy egy meglévő fájlmegosztás, amelyet a Cloud Shellből csatlakoztat. Ennek módjáról a [Fájlok megőrzése az Azure Cloud Shellben](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage)című témakörben olvashat.

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Objektumok feltöltése a *$web* tárolóba egy forráskönyvtárból.

```powershell
# upload a file
set-AzStorageblobcontent -File "<path-to-file>" `
-Properties @{ ContentType = "text/html; charset=utf-8";} `
-Container `$web `
-Blob "<blob-name>" `
-Context $ctx
```

* Cserélje `<path-to-file>` le a helyőrző értéket a feltölteni kívánt fájl teljesen minősített `C:\temp\index.html`elérési útvonalára (például: ).

* Cserélje `<blob-name>` le a helyőrző értéket az eredményül kapott blobnak `index.html`adni kívánt névre (például: ).

---

## <a name="find-the-website-url-by-using-the-azure-portal"></a>A webhely URL-címének megkeresése az Azure Portal használatával

Webhelyének oldalait böngészőből tekintheti meg a webhely nyilvános URL-címének használatával.

### <a name="portal"></a>[Portál](#tab/azure-portal)

<a id="portal-find-url" />

A tárfiók fiókáttekintő lapja mellett megjelenő ablaktáblán válassza a **Statikus webhely**lehetőséget. A webhely URL-címe megjelenik az **Elsődleges végpont** mezőben.

![Az Azure Storage statikus webhelyeinek metrikája](./media/storage-blob-static-website/storage-blob-static-website-url.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

<a id="cli-find-url" />

A statikus webhely nyilvános URL-címét a következő paranccsal találja meg:

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

* Cserélje `<storage-account-name>` le a helyőrző értéket a tárfiók nevére.

* Cserélje `<resource-group-name>` le a helyőrző értéket az erőforráscsoport nevére.

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

<a id="powershell-find-url" />

A statikus webhely nyilvános URL-címét a következő paranccsal találja meg:

```powershell
 $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -Name "<storage-account-name>"
Write-Output $storageAccount.PrimaryEndpoints.Web
```

* Cserélje `<resource-group-name>` le a helyőrző értéket az erőforráscsoport nevére.

* Cserélje `<storage-account-name>` le a helyőrző értéket a tárfiók nevére.

---

<a id="metrics" />

## <a name="enable-metrics-on-static-website-pages"></a>Mérőszámok engedélyezése statikus webhelyoldalakon

Miután engedélyezte a metrikákat, a **$web** tárolóban lévő fájlok forgalmi statisztikái a metrikák irányítópultján kerülnek jelentésre.

1. Kattintson a tárfiók **menüjének Monitor** szakaszának **Metrikák** elemére.

   > [!div class="mx-imgBorder"]
   > ![Mérőszámok hivatkozás](./media/storage-blob-static-website/metrics-link.png)

   > [!NOTE]
   > Metrikák adatok jönnek létre a különböző metrikák API-k hooking. A portál csak egy adott időkereten belül használt API-tagokat jelenít meg, hogy csak az adatokat visszaadó tagokra összpontosítson. Annak érdekében, hogy ki tudja választani a szükséges API-tagot, az első lépés az időkeret bővítése.

2. Kattintson az időkeret gombra, válasszon egy időkeretet, majd kattintson **az Alkalmaz gombra.**

   ![Az Azure Storage statikus webhelyeinek metrikája idikátriai tartomány](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

3. Válassza a **Blob** elemet a *Névtér* legördülő menüből.

   ![Az Azure Storage statikus webhelyeinek metrikái névtér](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

4. Ezután válassza ki a **kimenő forgalom metrika.**

   ![Az Azure Storage statikus webhelyeinek metrikája](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

5. Válassza az **Összeg** lehetőséget az *Összesítés* választóból.

   ![Az Azure Storage statikus webhelyeinek metrikák összesítése](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

6. Kattintson a **Szűrő hozzáadása** gombra, és válassza az **API-név lehetőséget** a *Tulajdonságválasztóból.*

   ![Az Azure Storage statikus webhelyeinek metrikái API-név](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

7. Jelölje be a **GetWebContent** jelölőnégyzetet az *Értékek* választóban a metrikajelentés feltöltéséhez.

   ![Az Azure Storage statikus webhelyeinek metrikái GetWebContent](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

   >[!NOTE]
   > A **GetWebContent** jelölőnégyzet csak akkor jelenik meg, ha az API-tagot egy adott időkereten belül használták. A portál csak egy adott időkereten belül használt API-tagokat jelenít meg, hogy csak az adatokat visszaadó tagokra összpontosítson. Ha nem talál egy adott API-tagot ebben a listában, bontsa ki az időkeretet.

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan konfigurálhat egyéni tartományt a statikus webhelyével. Lásd: [Egyéni tartomány leképezése egy Azure Blob Storage-végponthoz.](storage-custom-domain-name.md)

