---
title: Felhőalapú tárolási alkalmazások monitorozása és hibaelhárítása az Azure-ban | Microsoft Docs
description: Diagnosztikai eszközök, mérőszámok és riasztások használata a felhőalapú alkalmazások hibaelhárításához és monitorozásához.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.workload: web
ms.devlang: csharp
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: tamram
ms.custom: mvc
ms.openlocfilehash: eb58104309802125a8424cbbf8a1bef3d1c5e79c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="monitor-and-troubleshoot-a-cloud-storage-application"></a>Felhőalapú tárolási alkalmazások monitorozása és hibaelhárítása

Ez az oktatóanyag egy sorozat negyedik, utolsó része. Megismerheti a felhőalapú tárolási alkalmazások monitorozásának és hibaelhárításának módjával.

A sorozat negyedik részében az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A naplózás és a mérőszámok bekapcsolása
> * Hitelesítési hibák riasztásainak engedélyezése
> * Tesztelési célú adatforgalom futtatása helytelen SAS-tokenekkel
> * Naplók letöltése és elemzése

Az [Azure Storage Analytics](../common/storage-analytics.md) naplózási funkciót és mérőszámadatokat biztosít a tárfiókok számára. Ezen adatok révén betekintést kaphat a tárfiók állapotába. Ha a tárfiók adataira kíváncsi, előbb be kell állítania az adatgyűjtést. E folyamat magában foglalja a naplózás bekapcsolását, a mérőszámok konfigurálását és a riasztások engedélyezését.

A tárfiókok naplózásának és mérőszámainak engedélyezése az Azure Portal **Diagnosztika** lapján történik. Kétféle mérőszámtípust különböztetünk meg. Az **Összesített** mérőszámok a bejövő/kimenő forgalom, a rendelkezésre állás, a késleltetés és a sikerességi arányok adatait gyűjtik össze. A mérőszámok összesítése blobok, üzenetsorok, táblák és fájlszolgáltatások szerint történik. Az **API-specifikus** ugyanezeket a mérőszámokat gyűjti össze az egyes tárolási műveletek esetében az Azure Storage-szolgáltatás API-jában. A Storage naplózási szolgáltatásával a sikeres és a sikertelen kérelmek adatai is rögzíthetők a tárfiókban. Ezekben a naplókban az Azure-táblák, -üzenetsorok és -blobok olvasási, írási és törlési műveleteinek részletei tekinthetők meg. Ezen kívül lehetővé teszik a kérelmek meghiúsulási okának (például időtúllépések, szabályozás vagy engedélyezési hibák) megtekintését is.

## <a name="log-in-to-the-azure-portal"></a>Bejelentkezés az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com)

## <a name="turn-on-logging-and-metrics"></a>A naplózás és a mérőszámok bekapcsolása

A bal oldali menüben válassza az **Erőforráscsoportok**, a **myResourceGroup** elemet, majd válassza ki a tárfiókját az erőforrások listájából.

A **Diagnosztika** területen állítsa az **Állapot** értékét a következőre: **Be**. Győződjön meg arról, hogy a **Blob tulajdonságai** területen lévő összes beállítás engedélyezve van.

Amikor végzett, kattintson a **Mentés** gombra

![Diagnosztika panel](media/storage-monitor-troubleshoot-storage-application/contoso.png)

## <a name="enable-alerts"></a>Riasztások engedélyezése

Ha egy adott mérőszám értéke meghalad egy küszöbértéket, erről a rendszergazdák e-mail formájában értesülhetnek, illetve a riasztások webhookokat is aktiválhatnak. Ebben a példában az `SASClientOtherError` mérőszámhoz tartozó riasztást fogja engedélyezni.

### <a name="navigate-to-the-storage-account-in-the-azure-portal"></a>A tárfiók megkeresése az Azure Portalon

A bal oldali menüben válassza az **Erőforráscsoportok**, a **myResourceGroup** elemet, majd válassza ki a tárfiókját az erőforrások listájából.

A **Monitorozás** szakaszban válassza a **Riasztási szabályok** elemet.

Válassza a **+ Riasztások hozzáadása** lehetőséget, majd a **Riasztási szabály hozzáadása** területen adja meg a szükséges adatokat. A **Metrika** legördülő listában válassza ki a következő elemet: `SASClientOtherError`.

![Diagnosztika panel](media/storage-monitor-troubleshoot-storage-application/figure2.png)

## <a name="simulate-an-error"></a>Hiba szimulálása

Egy érvényes riasztás szimulálásához próbáljon lekérni egy nem létező blobot a tárfiókból. Ehhez cserélje le az `<incorrect-blob-name>` értéket egy nem létező értékkel. Futtassa néhányszor az alábbi kódmintát a meghiúsult blobkérelmek szimulálásához.

```azurecli-interactive
sasToken=$(az storage blob generate-sas \
    --account-name <storage-account-name> \
    --account-key <storage-account-key> \
    --container-name <container> \
    --name <incorrect-blob-name> \
    --permissions r \
    --expiry `date --date="next day" +%Y-%m-%d` \
    --output tsv)

curl https://<storage-account-name>.blob.core.windows.net/<container>/<incorrect-blob-name>?$sasToken
```

Az alábbi képen példa látható egy olyan riasztásra, amely az előző példában szereplő, szimulált sikertelen futtatáson alapul.

 ![Példa riasztásra](media/storage-monitor-troubleshoot-storage-application/alert.png)

## <a name="download-and-view-logs"></a>Naplók letöltése és megtekintése

A Storage-naplók az adatok tárolását blobkészletek formájában végzik a tárfiók **$logs** nevű blobtárolójában. Ez a tároló nem jelenik meg a fiók összes blobtárolóját felsoroló listában, de ha közvetlenül nyitja meg, akkor megtekintheti annak tartalmát.

Ebben az esetben a [Microsoft Message Analyzer](http://technet.microsoft.com/library/jj649776.aspx) használatával kommunikálhat az Azure Storage-fiókkal.

### <a name="download-microsoft-message-analyzer"></a>A Microsoft Message Analyzer letöltése

Töltse le és telepítse a [Microsoft Message Analyzer](https://www.microsoft.com/download/details.aspx?id=44226) alkalmazást.

Indítsa el az alkalmazást, és válassza a **File** (Fájl) > **Open** (Megnyitás) > **From Other File Sources** (Egyéb fájlforrásokból) lehetőséget.

A **File Selector** (Fájl kiválasztása) párbeszédablakban válassza a **+ Add Azure Connection** (+ Azure-kapcsolat hozzáadása) lehetőséget. Adja meg a **tárfiók nevét** és a **fiókkulcsot**, majd kattintson az **OK** gombra.

![Microsoft Message Analyzer – Azure Storage-kapcsolat hozzáadása párbeszédpanel](media/storage-monitor-troubleshoot-storage-application/figure3.png)

A csatlakozást követően bontsa ki a tárolókat a Storage fanézetében a naplóblobok megtekintéséhez. Válassza ki a legutóbbi naplóbejegyzést, és kattintson az **OK** gombra.

![Microsoft Message Analyzer – Azure Storage-kapcsolat hozzáadása párbeszédpanel](media/storage-monitor-troubleshoot-storage-application/figure4.png)

A **New Session** (Új munkamenet) párbeszédpanelen kattintson a **Start** (Indítás) gombra a napló megtekintéséhez.

Miután a naplót megnyitotta, megtekintheti a Storage-eseményeket. Ahogy az alábbi képen is láthatja, a rendszer egy `SASClientOtherError` műveletet aktivált a tárfiókon. További információk a Storage-naplózásról: [Storage Analytics](../common/storage-analytics.md).

![Microsoft Message Analyzert – Események megtekintése](media/storage-monitor-troubleshoot-storage-application/figure5.png)

A [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) szintén egy olyan eszköz, amelyet a tárfiókkal való kommunikációhoz használhat, beleértve a **$logs** tárolót és az abban található naplókat is.

## <a name="next-steps"></a>További lépések

A sorozat negyedik, utolsó részében megismerkedett a tárfiókok monitorozásával és hibaelhárításával, például a következőkkel:

> [!div class="checklist"]
> * A naplózás és a mérőszámok bekapcsolása
> * Hitelesítési hibák riasztásainak engedélyezése
> * Tesztelési célú adatforgalom futtatása helytelen SAS-tokenekkel
> * Naplók letöltése és elemzése

Kövesse ezt a hivatkozást az előre felépített tárolóminták megtekintéséhez.

> [!div class="nextstepaction"]
> [Azure Storage-szkriptminták](storage-samples-blobs-cli.md)