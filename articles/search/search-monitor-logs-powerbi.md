---
title: Az Azure Cognitive Search naplók és metrikák megjelenítése a Power BI-val
description: Az Azure Cognitive Search naplók és metrikák megjelenítése a Power BI-val
manager: eladz
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: 618033e24236d26601fafaf7b0678ee83ec973e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650150"
---
# <a name="visualize-azure-cognitive-search-logs-and-metrics-with-power-bi"></a>Az Azure Cognitive Search naplók és metrikák megjelenítése a Power BI-val
[Az Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-what-is-azure-search) lehetővé teszi a keresési szolgáltatás működési naplóinak és szolgáltatásmetrikáknak az Azure Storage-fiókban való tárolását. Ez a lap azt ismerteti, hogyan jelenítheti meg ezeket az információkat a Power BI-sablonalkalmazáson keresztül. Az alkalmazás részletes információkat nyújt a keresési szolgáltatásról, beleértve a kereséssel, az indexeléssel, a műveletekkel és a szolgáltatásmérőszámokkal kapcsolatos információkat.

A Power BI-sablonalkalmazás **Azure Cognitive Search: Naplók és metrikák elemzése** a Power [BI-alkalmazások piacterén](https://appsource.microsoft.com/marketplace/apps)található.

## <a name="how-to-get-started-with-the-app"></a>Az alkalmazás sal való ismerkedése
1. Diagnosztikai naplózás engedélyezése a keresési szolgáltatásban:
    1. Meglévő [Azure Storage-fiók](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) létrehozása vagy azonosítása, ahol archiválhatja a naplókat
    1. Keresse meg az Azure Cognitive Search szolgáltatást az Azure Portalon
    1. A bal oldali oszlop Figyelés szakaszában válassza a **Diagnosztikai beállítások lehetőséget.**

        ![](media/search-monitor-logs-powerbi/diagnostic-settings.png)

    1. Válassza **a + Diagnosztikai beállítás hozzáadása lehetőséget**
    1. Ellenőrizze **az Archiválás tárfiókba jelölőnégyzetet,** adja meg a tárfiók adatait, és ellenőrizze az OperationLogs and AllMetrics **(Archiválás** és **AllMetrics) jelölőnégyzetet.**

        ![](media/search-monitor-logs-powerbi/add-diagnostic-setting.png)
    1. **Mentés** kiválasztása

1. A naplózás engedélyezése után a keresési szolgáltatás segítségével indítsa el a naplók és metrikák generálását. Akár egy órát is igénybe vesz, amíg a tárolók megjelennek a Blob storage-ban ezekkel a naplókkal. Megjelenik egy **insights-logs-operationlogs** tároló keresési forgalmi naplók és egy **insights-metrics-pt1m** tároló metrikák.

1. Keresse meg az Azure Cognitive Search Power BI alkalmazást a [Power BI-alkalmazások piacterében,](https://appsource.microsoft.com/marketplace/apps) és telepítse egy új munkaterületre vagy egy meglévő munkaterületre. Az alkalmazás neve **Azure Cognitive Search: Naplók és metrikák elemzése.**

1. Az alkalmazás telepítése után válassza ki az alkalmazást a Power BI alkalmazáslistájából.

    ![](media/search-monitor-logs-powerbi/azure-search-app-tile.png)

1. Az adatok csatlakoztatásához válassza a **Csatlakozás** lehetőséget

    ![](media/search-monitor-logs-powerbi/get-started-with-your-new-app.png)

1. Adja meg a naplókat és a metrikákat tartalmazó tárfiók nevét. Alapértelmezés szerint az alkalmazás megvizsgálja az utolsó 10 nap adatait, de ez az érték a **Napok** paraméterrel módosítható.

    ![](media/search-monitor-logs-powerbi/connect-to-storage-account.png)

1. Válassza a **kulcs** hitelesítési módszerként lehetőséget, és adja meg a tárfiók kulcsát. Válassza a **Privát** adatvédelmi szintet. Kattintson a Bejelentkezés gombra. Ennek hatására elkezdődik a betöltés.

    ![](media/search-monitor-logs-powerbi/connect-to-storage-account-step-two.png)

1. Várja meg, amíg az adatok frissülnek. Ez eltarthat egy ideig attól függően, hogy mennyi adatvan. Az alábbi mutató alapján ellenőrizheti, hogy az adatok frissítése még mindig folyamatban van-e.

    ![](media/search-monitor-logs-powerbi/workspace-view-refreshing.png)

1. Miután az adatfrissítés befejeződött, válassza az **Azure Cognitive Search Report** a jelentés megtekintéséhez.

    ![](media/search-monitor-logs-powerbi/workspace-view-select-report.png)

1. A jelentés megnyitása után frissítse a lapot, hogy az az adatokkal együtt nyíljon meg.

    ![](media/search-monitor-logs-powerbi/powerbi-search.png)

## <a name="how-to-change-the-app-parameters"></a>Az alkalmazás paramétereinek módosítása
Ha egy másik tárfiókból származó adatokat szeretne vizualizálni, vagy módosítani szeretné a lekérdezendő adatok számát, kövesse az alábbi lépéseket a **Days** és a **StorageAccount** paraméterek módosításához.

1. Nyissa meg a Power BI-alkalmazásokat, keresse meg az Azure Cognitive Search alkalmazást, és kattintson az **Alkalmazás szerkesztése** gombra a munkaterület megtekintéséhez.

    ![](media/search-monitor-logs-powerbi/azure-search-app-tile-edit.png)

1. Válassza a **Beállítások lehetőséget** az Adatkészlet beállításai között.

    ![](media/search-monitor-logs-powerbi/workspace-view-select-settings.png)

1. Az Adatkészletek lapon módosítsa a paraméterértékeket, és válassza az **Alkalmaz**lehetőséget. Ha probléma merül fel a kapcsolattal kapcsolat esetén, frissítse az adatforrás hitelesítő adatait ugyanazon az oldalon.

1. Lépjen vissza a munkaterületre, és válassza a **Frissítés most** lehetőséget az Adatkészlet beállításai között.

    ![](media/search-monitor-logs-powerbi/workspace-view-select-refresh-now.png)

1. Nyissa meg a jelentést a frissített adatok megtekintéséhez. Előfordulhat, hogy a legfrissebb adatok megtekintéséhez frissítenie kell a jelentést.

## <a name="troubleshooting"></a>Hibaelhárítás
Ha úgy találja, hogy nem látja az adatait, kövesse az alábbi hibaelhárítási lépéseket:

1. Nyissa meg a jelentést, és frissítse a lapot, hogy biztosan megtekintse a legfrissebb adatokat. A jelentésben lehetőség van az adatok frissítésére. A legfrissebb adatok beszerezéséhez válassza ezt a lehetőséget.

1. Győződjön meg arról, hogy a tárfiók neve és hozzáférési kulcsa helyes. A tárfiók nevének meg kell egyeznie a keresési szolgáltatás naplóival konfigurált fiókkal.

1. Győződjön meg arról, hogy a tárfiók tartalmazza a **tárolók insights-logs-operationlogs** és **insights-metrics-pt1m** és minden tároló adatokkal rendelkezik. A naplók és a metrikák a mappák néhány rétegén belül lesznek.

1. Ellenőrizze, hogy az adatkészlet továbbra is frissül-e. A frissítési állapot jelzője a fenti 8. Ha még mindig frissül, várja meg, amíg a frissítés befejeződik a jelentés megnyitásához és frissítéséhez.

## <a name="next-steps"></a>További lépések
[További információ az Azure Cognitive Search szolgáltatásról](https://docs.microsoft.com/azure/search/)

[A Power BI bemutatása](https://docs.microsoft.com/power-bi/fundamentals/power-bi-overview)

[A Power BI szolgáltatás alapfogalmai tervezők számára](https://docs.microsoft.com/power-bi/service-basic-concepts)