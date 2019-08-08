---
title: Statikus webhely üzemeltetése az Azure Storage-ban
description: Megtudhatja, hogyan szolgálhat ki statikus tartalmat (HTML-, CSS-, JavaScript-és képfájlokat) közvetlenül egy Azure Storage GPv2-fiókban található tárolóból.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 05/28/2019
ms.openlocfilehash: 591a62ebb6b83ea23d3ed6cf867c07b91e1596b3
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68847050"
---
# <a name="host-a-static-website-in-azure-storage"></a>Statikus webhely üzemeltetése az Azure Storage-ban

A statikus tartalom (HTML-, CSS-, JavaScript-és képfájlok) közvetlenül az Azure Storage GPv2-fiókban lévő tárolóból is kiszolgálható. További információ: [statikus webhely üzemeltetése az Azure Storage-ban](storage-blob-static-website.md).

Ez a cikk bemutatja, hogyan engedélyezheti a statikus webhelyek üzemeltetését az Azure Portal, az Azure CLI vagy a PowerShell használatával.

<a id="portal" />

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

Részletes oktatóanyagért lásd [: oktatóanyag: Statikus webhely üzemeltetése Blob Storageon](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host).

A statikus webhely üzemeltetésének engedélyezése után megtekintheti a webhely oldalait egy böngészőben a webhely nyilvános URL-címének használatával.

<a id="portal-find-url" />

### <a name="find-the-website-url-by-using-the-azure-portal"></a>A webhely URL-címének megkeresése a Azure Portal használatával

A Storage-fiók fiók áttekintése oldalán megjelenő ablaktáblán válassza a **statikus webhely**lehetőséget. A webhely URL-címe az **elsődleges végpont** mezőben jelenik meg.

![Azure Storage – statikus webhelyek mérőszámai metrika](./media/storage-blob-static-website/storage-blob-static-website-url.png)

<a id="cli" />

## <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata

A statikus webhely üzemeltetését az [Azure parancssori felületének (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)használatával engedélyezheti.

1. Először nyissa meg a [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest), vagy ha helyileg [telepítette](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) az Azure CLI-t, nyisson meg egy parancssori alkalmazást, például a Windows PowerShellt.

2. Ha az identitása egynél több előfizetéshez van társítva, akkor állítsa be az aktív előfizetést a statikus webhelyét futtató Storage-fiók előfizetésére.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Cserélje le `<subscription-id>` a helyőrző értékét az előfizetés azonosítójával.

3. A statikus webhely üzemeltetésének engedélyezése.

   ```azurecli-interactive
   az storage blob service-properties update --account-name <storage-account-name> --static-website --404-document <error-document-name> --index-document <index-document-name>
   ```

   * Cserélje le `<storage-account-name>` a helyőrző értékét a Storage-fiók nevére.

   * Cserélje le `<error-document-name>` a helyőrzőt a felhasználók számára megjelenő hibaüzenet nevére, ha egy böngésző olyan oldalt kér a webhelyen, amely nem létezik.

   * Cserélje le `<index-document-name>` a helyőrzőt az index dokumentum nevére. Ez a dokumentum általában "index. html".

4. Objektumok feltöltése a *$web* tárolóba egy forrás könyvtárából.

   > [!NOTE]
   > Ha Azure Cloud Shell használ, ügyeljen arra, hogy a `\` `$web` tárolóra hivatkozó Escape-karaktert vegyen fel (például: `\$web`). Ha az Azure CLI helyi telepítését használja, akkor nem kell használni az Escape-karaktert.

   Ez a példa feltételezi, hogy Azure Cloud Shell-munkamenetből futtat parancsokat.

   ```azurecli-interactive
   az storage blob upload-batch -s <source-path> -d \$web --account-name <storage-account-name>
   ```

   * Cserélje le `<storage-account-name>` a helyőrző értékét a Storage-fiók nevére.

   * Cserélje le `<source-path>` a helyőrzőt a feltölteni kívánt fájlok helyének elérési útjára.

   > [!NOTE]
   > Ha az Azure CLI-t használja, az elérési utat használhatja a helyi számítógép bármely helyére (például: `C:\myFolder`).
   >
   > Azure Cloud Shell használata esetén olyan fájlmegosztást kell hivatkoznia, amely látható a Cloud Shell számára. Ez a hely lehet a megosztott felhőalapú fájlmegosztás vagy egy meglévő fájlmegosztás, amelyet a Cloud Shell lehet csatlakoztatni. Ennek megismeréséhez tekintse meg [a fájlok](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage)megőrzése Azure Cloud Shellban című témakört.

<a id="cli-find-url" />

### <a name="find-the-website-url-by-using-the-azure-cli"></a>A webhely URL-címének megkeresése az Azure CLI használatával

A tartalmak egy böngészőben a webhely nyilvános URL-címével tekinthetők meg.

Keresse meg az URL-címet a következő parancs használatával:

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

* Cserélje le `<storage-account-name>` a helyőrző értékét a Storage-fiók nevére.

* Cserélje le `<resource-group-name>` a helyőrző értékét az erőforráscsoport nevére.

<a id="powershell" />

## <a name="use-powershell"></a>A PowerShell használata

A Azure PowerShell modul használatával engedélyezheti a statikus webhely üzemeltetését.

1. Nyisson meg egy Windows PowerShell-parancssori ablakot.

2. Ellenőrizze, hogy rendelkezik-e Azure PowerShell modul az 0,7-es vagy újabb verziójával.

   ```powershell
   Get-InstalledModule -Name Az -AllVersions | select Name,Version
   ```

   Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket.

3. Jelentkezzen be az Azure-előfizetésbe a `Connect-AzAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

   ```powershell
   Connect-AzAccount
   ```

4. Ha az identitása egynél több előfizetéshez van társítva, akkor állítsa be az aktív előfizetést a statikus webhelyét futtató Storage-fiók előfizetésére.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Cserélje le `<subscription-id>` a helyőrző értékét az előfizetés azonosítójával.

5. Szerezze be a használni kívánt Storage-fiókot meghatározó Storage-fiók környezetét.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Cserélje le `<resource-group-name>` a helyőrző értékét az erőforráscsoport nevére.

   * Cserélje le `<storage-account-name>` a helyőrző értékét a Storage-fiók nevére.

6. A statikus webhely üzemeltetésének engedélyezése.

   ```powershell
   Enable-AzStorageStaticWebsite -Context $ctx -IndexDocument <index-document-name> -ErrorDocument404Path <error-document-name>
   ```

   * Cserélje le `<error-document-name>` a helyőrzőt a felhasználók számára megjelenő hibaüzenet nevére, ha egy böngésző olyan oldalt kér a webhelyen, amely nem létezik.

   * Cserélje le `<index-document-name>` a helyőrzőt az index dokumentum nevére. Ez a dokumentum általában "index. html".

7. Objektumok feltöltése a *$web* tárolóba egy forrás könyvtárából.

    ```powershell
    # upload a file
    set-AzStorageblobcontent -File "<path-to-file>" `
    -Container `$web `
    -Blob "<blob-name>" `
    -Context $ctx
     ```

   * Cserélje le `<path-to-file>` a helyőrző értékét a feltölteni kívánt fájl teljes elérési útjára (például: `C:\temp\index.html`).

   * Cserélje le `<blob-name>` a helyőrző értékét arra a névre, amelyet az eredményül kapott blobhoz kíván adni ( `index.html`például:).

<a id="powershell-find-url" />

### <a name="find-the-website-url-by-using-powershell"></a>A webhely URL-címének megkeresése a PowerShell használatával

A tartalmak egy böngészőben a webhely nyilvános URL-címével tekinthetők meg.

Keresse meg az URL-címet a következő parancs használatával:

```powershell
 $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
Write-Output $storageAccount.PrimaryEndpoints.Web
```

* Cserélje le `<resource-group-name>` a helyőrző értékét az erőforráscsoport nevére.

* Cserélje le `<storage-account-name>` a helyőrző értékét a Storage-fiók nevére.

<a id="metrics" />

## <a name="enable-metrics-on-static-website-pages"></a>Metrikák engedélyezése a statikus webhelyek oldalain

A metrikák engedélyezése után a **$web** tárolóban található fájlokra vonatkozó forgalmi statisztikát a metrikák irányítópultján kell jelenteni.

1. Kattintson a **Beállítások** > **figyelési** > **metrikák**elemre.

   A metrikák adatait a rendszer a különböző metrikák API-kkal összekapcsolva hozza létre. A portál csak az adott időkereten belül használt API-tagokat jeleníti meg, hogy csak az adatvisszaadó tagokra összpontosítsanak. Annak érdekében, hogy ki tudja választani a szükséges API-tagot, az első lépés az időkeret kibontása.

2. Kattintson az időkeret gombra, és válassza az **elmúlt 24 óra** lehetőséget, majd kattintson az **alkalmaz**gombra.

   ![Azure Storage – statikus webhelyek metrikáinak időtartománya](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

3. Válassza a **blob** elemet a *névtér* legördülő listából.

   ![Azure Storage – statikus webhelyek mérőszámai – névtér](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

4. Ezután válassza ki a **kimenő** forgalom metrikáját.

   ![Azure Storage – statikus webhelyek mérőszámai metrika](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

5. Válassza ki az **összeg** elemet az *összesítési* választóból.

   ![Azure Storage – statikus webhelyek metrikáinak összesítése](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

6. Kattintson a **szűrő hozzáadása** gombra, és válassza az **API-név** lehetőséget a *tulajdonság* -választóban.

   ![Az Azure Storage statikus webhelyeinek metrikai API-neve](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

7. Jelölje be a **GetWebContent** melletti jelölőnégyzetet az *értékek* kiválasztásakor a metrikák jelentésének feltöltéséhez.

   ![Azure Storage – statikus webhelyek metrikái GetWebContent](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

## <a name="next-steps"></a>További lépések

* [Statikus webhely üzemeltetése az Azure Storage-ban](storage-blob-static-website.md)
* [A Blobok egyéni tartományokkal való elérésének Azure CDN használata HTTPS-kapcsolaton keresztül](storage-https-custom-domain-cdn.md)
* [Egyéni tartománynév beállítása a blob vagy a webes végpont számára](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure App Service](/azure/app-service/overview)
* [Az első kiszolgáló nélküli Webalkalmazás létrehozása](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Oktatóanyag: A tartomány üzemeltetése Azure DNS](../../dns/dns-delegate-domain-azure-dns.md)
