---
title: Az Azure Storage-ban statikus webhely üzemeltetése
description: Ismerje meg, hogyan statikustartalom (HTML, CSS, JavaScript és képfájlok) közvetlenül a tárolóban található egy Azure Storage GPv2-fiókra.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.author: normesta
ms.date: 05/28/2019
ms.openlocfilehash: 7a1aef14a2a32266c893933482527c361f17d7fb
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/31/2019
ms.locfileid: "66428560"
---
# <a name="host-a-static-website-in-azure-storage"></a>Az Azure Storage-ban statikus webhely üzemeltetése

Statikus tartalom (HTML, CSS, JavaScript és képfájlok) közvetlenül egy tárolóból egy Azure Storage GPv2-fiókra a szolgálhat. További tudnivalókért lásd: [statikus webhely üzemeltetése az Azure Storage](storage-blob-static-website.md).

Ez a cikk bemutatja, hogyan lehet engedélyezni a statikus webhely üzemeltetése az Azure Portalon, az Azure CLI vagy a PowerShell használatával.

<a id="portal" />

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

Egy lépésenkénti útmutató: [oktatóanyag: A Blob Storage statikus webhely üzemeltetése](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host).

Miután engedélyezte a statikus webhely-üzemeltetésre, megtekintheti a oldalt egy böngészőben a webhely nyilvános URL-címét a webhely használatával.

<a id="portal-find-url" />

### <a name="find-the-website-url-by-using-the-azure-portal"></a>A webhely URL-Címének megkeresése az Azure portal használatával

A fiók áttekintése lapra a tárfiók mellett megjelenő panelén válassza **statikus webhely**. A webhely URL-címe megjelenik a **elsődleges végpont** mező.

![Az Azure Storage statikus webhelyek kiszolgálására metrikák metrika](./media/storage-blob-static-website/storage-blob-static-website-url.png)

<a id="cli" />

## <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata

Statikus webhely üzemeltetése használatával engedélyezheti a [Azure parancssori felület (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

1. Először nyissa meg a [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest), vagy, ha [telepített](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) helyileg, az Azure CLI, nyissa meg például a Windows PowerShell-parancs konzolalkalmazást.

2. A megnyitott parancssori ablakban a storage előzetes bővítmény telepítése.

   ```azurecli-interactive
   az extension add --name storage-preview
   ```

3. Ha egynél több előfizetéssel társítva a személyazonosságát, majd állítsa be az aktív előfizetés az előfizetési a storage-fiók, amely a statikus webhelye üzemeltetésére lesz.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Cserélje le a `<subscription-id>` helyőrző értéket cserélje az előfizetés Azonosítóját.

4. Engedélyezze a statikus webhely üzemeltetése.

   ```azurecli-interactive
   az storage blob service-properties update --account-name <storage-account-name> --static-website --404-document <error-document-name> --index-document <index-document-name>
   ```

   * Cserélje le a `<storage-account-name>` helyőrző értéket cserélje a tárfiókja nevére.

   * Cserélje le a `<error-document-name>` helyőrzőt a hiba-dokumentumot, fog megjelenni a felhasználók, ha a böngésző kéri egy oldal, amely nem létezik a webhely nevét.

   * Cserélje le a `<index-document-name>` helyőrzőt az index dokumentum nevét. Ez a dokumentum a gyakran "index.html".

5. Töltse fel az objektumok a *$web* tároló forráskönyvtárból.

   > [!NOTE]
   > Ha az Azure Cloud Shell használata esetén ügyeljen arra, hogy egy `\` kontextusban való megnevezésekor escape-karakter a `$web` tároló (például: `\$web`). Ha az Azure parancssori felület helyi telepítését használja, majd nem kell az escape-karaktert használhatja.

   Ez a példa feltételezi, hogy parancsokat futtat az Azure Cloud Shell-munkamenetet.

   ```azurecli-interactive
   az storage blob upload-batch -s <source-path> -d \$web --account-name <storage-account-name>
   ```

   * Cserélje le a `<storage-account-name>` helyőrző értéket cserélje a tárfiókja nevére.

   * Cserélje le a `<source-path>` helyőrzőt a feltölteni kívánt fájlok helyének elérési útját.

   > [!NOTE]
   > Ha egy helyen telepített Azure CLI használata, akkor használhatja az elérési út tetszőleges helyre a helyi számítógépen (például: `C:\myFolder`.
   >
   > Ha az Azure Cloud Shell használata esetén kell hivatkoznia a fájlmegosztást, amelyet a Cloud Shellben látható-e. Ezen a helyen lehet a fájlmegosztás a felhő magát vagy egy meglévő fájlmegosztást, amely csatlakoztatja a Cloud Shellben való megosztása. Ezzel kapcsolatban lásd: [fájlok az Azure Cloud Shellben megtartása](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage).

<a id="cli-find-url" />

### <a name="find-the-website-url-by-using-the-azure-cli"></a>A webhely URL-Címének megkeresése az Azure CLI-vel

A nyilvános URL-címét a webhely használatával megtekintheti a tartalmat közvetlenül a böngészőből.

Keresse meg az URL-cím a következő paranccsal:

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

* Cserélje le a `<storage-account-name>` helyőrző értéket cserélje a tárfiókja nevére.

* Cserélje le a `<resource-group-name>` helyőrző értékét az erőforráscsoport nevét.

<a id="powershell" />

## <a name="use-powershell"></a>A PowerShell használata

Statikus webhely üzemeltetése az Azure PowerShell-modul használatával engedélyezheti.

1. Nyisson meg egy Windows PowerShell-parancsablakot.

2. Győződjön meg arról, hogy az Azure PowerShell modul Az 0,7 vagy újabb verziójára.

   ```powershell
   Get-InstalledModule -Name Az -AllVersions | select Name,Version
   ```

   Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket.

3. Jelentkezzen be az Azure-előfizetésbe a `Connect-AzAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

   ```powershell
   Connect-AzAccount
   ```

4. Ha egynél több előfizetéssel társítva a személyazonosságát, majd állítsa be az aktív előfizetés az előfizetési a storage-fiók, amely a statikus webhelye üzemeltetésére lesz.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Cserélje le a `<subscription-id>` helyőrző értéket cserélje az előfizetés Azonosítóját.

5. A tárfiók környezetét, amely meghatározza a tárfiók létrehozásához használni szeretne kaphat.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Cserélje le a `<resource-group-name>` helyőrző értékét az erőforráscsoport nevét.

   * Cserélje le a `<storage-account-name>` helyőrző értéket cserélje a tárfiókja nevére.

6. Engedélyezze a statikus webhely üzemeltetése.

   ```powershell
   Enable-AzStorageStaticWebsite -Context $ctx -IndexDocument <index-document-name> -ErrorDocument404Path <error-document-name>
   ```

   * Cserélje le a `<error-document-name>` helyőrzőt a hiba-dokumentumot, fog megjelenni a felhasználók, ha a böngésző kéri egy oldal, amely nem létezik a webhely nevét.

   * Cserélje le a `<index-document-name>` helyőrzőt az index dokumentum nevét. Ez a dokumentum a gyakran "index.html".

7. Töltse fel az objektumok a *$web* tároló forráskönyvtárból.

    ```powershell
    # upload a file
    set-AzStorageblobcontent -File "<path-to-file>" `
    -Container `$web `
    -Blob "<blob-name>" `
    -Context $ctx
     ```

   * Cserélje le a `<path-to-file>` helyőrző értéket cserélje a feltölteni kívánt fájl teljes elérési útja (Példa: `C:\temp\index.html`).

   * Cserélje le a `<blob-name>` helyőrző értéket cserélje kíván adni a létrejövő blob neve (Példa: `index.html`).

<a id="powershell-find-url" />

### <a name="find-the-website-url-by-using-powershell"></a>A webhely URL-cím keresése a PowerShell használatával

A nyilvános URL-címét a webhely használatával megtekintheti a tartalmat közvetlenül a böngészőből.

Keresse meg az URL-cím a következő paranccsal:

```powershell
$context = Get-AzSubscription -SubscriptionId <subscription-d>
Set-AzContext $context
```

Cserélje le a `<subscription-id>` helyőrző értéket cserélje az előfizetés Azonosítóját.

<a id="metrics" />

## <a name="enable-metrics-on-static-website-pages"></a>Statikus webhely lapjain típusú metrikák engedélyezése

Miután engedélyezte a metrikák, forgalom statisztikai értéket is a fájlok a **$web** tároló jelenti a metrikák irányítópultján.

1. Kattintson a **beállítások** > **figyelési** > **metrikák**.

   Metrikai adatok történetének más érdekes mérőszám API-k alapján jönnek létre. A portál csak annak érdekében, hogy csak összpontosíthat tagokat, amelyeket vissza adatokat egy adott időszakon belül használt API-t a tagok jeleníti meg. Annak érdekében, hogy biztosítani tudja válassza a szükséges API-t, az első lépéseként időkeretet kibontásához.

2. Kattintson az időkeret gombra, és válassza ki **az elmúlt 24 órából** majd **alkalmaz**.

   ![Az Azure Storage-statikus webhelyek kiszolgálására metrikák időtartomány](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

3. Válassza ki **Blob** származó a *Namespace* legördülő menü.

   ![Az Azure Storage statikus webhelyek kiszolgálására metrikák névtér](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

4. Válassza ki a **kimenő** metrikát.

   ![Az Azure Storage statikus webhelyek kiszolgálására metrikák metrika](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

5. Válassza ki **Sum** származó a *összesítési* választó.

   ![Az Azure Storage statikus webhelyek kiszolgálására metrikák összesítése](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

6. Kattintson a **szűrő hozzáadása** gombra, és válasszon **API neve** származó a *tulajdonság* választó.

   ![Az Azure Storage statikus webhelyek kiszolgálására metrikák API neve](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

7. kontrola a mező melletti **GetWebContent** a a *értékek* választó feltölti a metrikai jelentést.

   ![Az Azure Storage statikus webhelyek kiszolgálására metrikák GetWebContent](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

## <a name="next-steps"></a>További lépések

* [Statikus webhely üzemeltetése az Azure Storage-ban](storage-blob-static-website.md)
* [Az Azure CDN használatával HTTPS-kapcsolaton keresztül egyéni tartománnyal rendelkező blobok elérése](storage-https-custom-domain-cdn.md)
* [A blob vagy a webszolgáltatás-végpont egyéni tartománynév konfigurálása](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure App Service](/azure/app-service/overview)
* [Az első kiszolgáló nélküli webalkalmazás létrehozása](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Oktatóanyag: Üzemeltessen saját tartományt az Azure DNS-ben](../../dns/dns-delegate-domain-azure-dns.md)
