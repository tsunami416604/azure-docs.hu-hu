---
title: "Figyelésére és hibaelhárítására egy cloud storage-alkalmazást az Azure-ban |} Microsoft Docs"
description: "Diagnosztikai eszközök, metrikákat és riasztások segítségével hibaelhárításához és a felhőalapú alkalmazások figyeléséhez."
services: storage
documentationcenter: 
author: georgewallace
manager: timlt
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 09/19/2017
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: db88c331f79d83e0124519f8b6dbb34514b456dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-and-troubleshoot-a-cloud-storage-application"></a>És a felhőalapú tároló alkalmazás hibaelhárítása

Ez az oktatóanyag négy és egy sor utolsó részét. Megismerheti, hogyan figyelheti és a felhőalapú tároló-alkalmazás hibaelhárításához.

A sorozat négy része a megismerheti, hogyan:

> [!div class="checklist"]
> * Kapcsolja be a naplózás és a metrikák
> * A hitelesítési hibák-riasztások engedélyezése
> * Futtassa a test-forgalom megfelelő SAS-tokenje
> * Töltse le és naplóinak elemzése

[Az Azure storage analytics](../common/storage-analytics.md) naplózás és metrika adatokat biztosít a tárfiókon. Ezeket az adatokat a tárfiók állapotának betekintést nyújt. Látható a tárfiókhoz be lehet, mielőtt szüksége adatgyűjtés beállítása. A folyamat során bekapcsolja a naplózást, riasztások engedélyezése és konfigurálása a metrikákat.

Naplózás és a storage-fiókok metrikák engedélyezhető a **diagnosztika** fülre az Azure portálon. A mérőszámok két típusa van. **Összesített** metrikák gyűjt be-és kilépési, a rendelkezésre állási, a késés és a sikeres százalékos aránya. A blob, a várólista, a tábla és a Fájlszolgáltatások a metrikák összesítése. **/ API** ugyanazokat az Azure Storage szolgáltatás API-ban minden tárolási műveletet metrikáját gyűjti. Tárolási naplózás lehetővé teszi a tárfiókban lévő sikeres és a sikertelen kérelmek adatainak rögzítését. Ezek a naplók engedélyezése láthatja az olvasási, írási és törlési műveleteket az Azure-táblákban, üzenetsorokat és blobokat részleteit. Lehetővé teszik a sikertelen kérések, például időtúllépések, a sávszélesség-szabályozás és a hitelesítési hibák okok megjelenítéséhez.

## <a name="log-in-to-the-azure-portal"></a>Jelentkezzen be az Azure portálra.

Jelentkezzen be az [Azure Portalra](https://portal.azure.com)

## <a name="turn-on-logging-and-metrics"></a>Kapcsolja be a naplózás és a metrikák

A bal oldali menüben válassza ki a **erőforráscsoportok**, jelölje be **myResourceGroup**, majd válassza ki a tárfiók az erőforráslistát.

A **diagnosztika** beállítása **állapot** való **a**. Győződjön meg arról **összesített metrikák Blob**, **Blob API-specifikus metrikái**, és **Blob-naplók** engedélyezve vannak.

Amikor végzett, kattintson **mentése**

![Diagnosztika ablaktábla](media/storage-monitor-troubleshoot-storage-application/figure1.png)

## <a name="enable-alerts"></a>-Riasztások engedélyezése

Riasztások teszik lehetővé a rendszergazdák e-mail vagy eseményindítót, a küszöbérték megsértése metrika alapján a webhook. Ebben a példában egy riasztást engedélyezi a `SASClientOtherError` metrikát.

### <a name="navigate-to-the-storage-account-in-the-azure-portal"></a>Lépjen a tárfiókhoz, az Azure-portálon

A bal oldali menüben válassza ki a **erőforráscsoportok**, jelölje be **myResourceGroup**, majd válassza ki a tárfiók az erőforráslistát.

Az a **figyelés** szakaszban jelölje be **riasztási szabályok**.

Válassza ki **+ Hozzáadás riasztás**a **riasztási szabály felvétele**, töltse ki a szükséges adatokat. Válasszon `SASClientOtherError` a a **metrika** legördülő listán.

![Diagnosztika ablaktábla](media/storage-monitor-troubleshoot-storage-application/figure2.png)

## <a name="simulate-an-error"></a>Hiba szimulálása

Egy érvényes riasztás szimulálása, esetleg egy nem létező blobot kér a tárfiók. Ehhez cserélje le a `<incorrect-blob-name>` értékének egy érték, amely nem létezik. Futtassa a következő példakód néhány alkalommal szimulálása sikertelen blob kérelmek.

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

Az alábbi képen, az előző példában futtatott egy példa riasztást, amelyet a szimulált hiba épül.

 ![Példa riasztás](media/storage-monitor-troubleshoot-storage-application/alert.png)

## <a name="download-and-view-logs"></a>A letöltés és nézet naplók

A tárolási naplófájljai adatok tárolása a nevű blob-tárolóban lévő blobok készlete **$logs** tárfiókba. Ez a tároló nem jelenik meg, ha a fiók felsorolja az összes blob-tároló, de láthatja annak tartalmát, ha közvetlen hozzáféréshez.

Ebben az esetben használhatja az [Microsoft Message Analyzert](http://technet.microsoft.com/library/jj649776.aspx) kommunikál az Azure storage-fiók.

### <a name="download-microsoft-message-analyzer"></a>Microsoft Message Analyzert letöltése

Töltse le [Microsoft Message Analyzert](https://www.microsoft.com/download/details.aspx?id=44226) és telepítheti az alkalmazást.

Indítsa el az alkalmazást, és válassza a **fájl** > **nyitott** > **egyéb fájl forrásokból származó**.

Az a **fájl választó** párbeszédablakban válassza **+ Azure-kapcsolat hozzáadása**. Adja meg a **tárfióknév** és **fiókkulcs** kattintson **OK**.

![Microsoft Message Analyzer - hozzáadása az Azure Storage kapcsolati párbeszédpanel](media/storage-monitor-troubleshoot-storage-application/figure3.png)

Miután csatlakozott, bontsa ki a tárolási fában tárolók megtekintéséhez a naplóban blobok. Válassza ki a legújabb naplót, és kattintson a **OK**.

![Microsoft Message Analyzer - hozzáadása az Azure Storage kapcsolati párbeszédpanel](media/storage-monitor-troubleshoot-storage-application/figure4.png)

Az a **új munkamenet** párbeszédpanel, kattintson a **Start** a napló megtekintéséhez.

Ha a napló nyit, megtekintheti a storage-események. Az alábbi képen látható, mert hiba történt egy `SASClientOtherError` a tárfiókon indított. További információt a tárolási naplózást, [tárolási analitika](../common/storage-analytics.md).

![Microsoft Message Analyzert - események megtekintése](media/storage-monitor-troubleshoot-storage-application/figure5.png)

[A Tártallózó](https://azure.microsoft.com/features/storage-explorer/) egy másik eszköz, amely segítségével kommunikál a storage-fiókok, beleértve a **$logs** tároló és a naplókat, amelyek szerepelnek.

## <a name="next-steps"></a>Következő lépések

Négy rész és az adatsorozat utolsó része megtudta, hogyan figyelésére és hibaelhárítására a tárfiók, például:

> [!div class="checklist"]
> * Kapcsolja be a naplózás és a metrikák
> * A hitelesítési hibák-riasztások engedélyezése
> * Futtassa a test-forgalom megfelelő SAS-tokenje
> * Töltse le és naplóinak elemzése

Kövesse a hivatkozásra kattintva megtekintheti az előre elkészített tárolási minták.

> [!div class="nextstepaction"]
> [Az Azure storage parancsfájl minták](storage-samples-blobs-cli.md)