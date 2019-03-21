---
title: Az Azure Log Analytics-adatok importálása a Power BI-bA |} A Microsoft Docs
description: A Power BI egy olyan felhőalapú üzleti analitikai szolgáltatás, amelynek látványos vizualizációkat és jelentéseket biztosít a különböző adatkészletek elemzése.  Ez a cikk azt ismerteti, konfigurálásáról és a Log Analytics-adatok importálása a Power BI-ba, és konfigurálja úgy, hogy automatikusan frissítse.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 83edc411-6886-4de1-aadd-33982147b9c3
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2018
ms.author: bwren
ms.openlocfilehash: 53e24a6874a1e43b0de07893a6ace3a44b81d373
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58110171"
---
# <a name="import-azure-monitor-log-data-into-power-bi"></a>Az Azure Monitor log-adatok importálása a Power BI-bA


[Power bi-ban](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) van egy felhőalapú üzleti analitikai szolgáltatás, amelynek látványos vizualizációkat és jelentéseket biztosít a különböző adatkészletek elemzése.  Az Azure Monitor log-lekérdezés eredményeit, importálhatja a Power BI-adatkészletbe, így kihasználhatja annak funkcióit, például a különböző forrásokból származó adatok összevonása és a webes és mobil eszközök jelentések megosztása.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="overview"></a>Áttekintés
Importálja az adatokat egy [Log Analytics-munkaterület](manage-access.md) az Azure monitorban Power BI-ba, a alapján a Power BI-ban egy adatkészletet hoz létre egy [naplólekérdezés](../log-query/log-query-overview.md) az Azure monitorban.  Az adatkészlet frissítésekor minden alkalommal, amikor futtatni a lekérdezést.  Ezután az adatkészlet adatait használó Power BI-jelentéseket hozhat létre.  Az adatkészlet létrehozása a Power bi-ban, a lekérdezés exportálhat a Log Analytics [Power Query (M) nyelvi](https://msdn.microsoft.com/library/mt807488.aspx).  Ezután használja ezt a lekérdezés létrehozása a Power BI Desktopban, és majd közzé kell tennie az adatkészletként a Power bi-ban.  Ez a folyamat részletei az alábbiakban tekintheti át.

![A log Analytics, a Power bi-bA](media/powerbi/overview.png)

## <a name="export-query"></a>Lekérdezés exportálása
Először hozzon létre egy [naplólekérdezés](../log-query/log-query-overview.md) , amely feltölti a Power BI-adatkészletekbe kívánt adatot adja vissza.  Ez a lekérdezés, majd exportálja [Power Query (M) nyelvi](https://msdn.microsoft.com/library/mt807488.aspx) Power BI Desktop által használható.

1. [A napló-lekérdezés létrehozása a Log Analytics](../log-query/get-started-portal.md) el az adatkészletet az adatok kinyerése érdekében.
2. Válassza ki **exportálása** > **Power BI-lekérdezések (millió)**.  Ez a lekérdezés nevű szöveges fájlba exportálja **PowerBIQuery.txt**. 

    ![Naplóbeli keresés exportálása](media/powerbi/export-analytics.png)

3. Nyissa meg a szöveges fájlt, és másolja a tartalmat.

## <a name="import-query-into-power-bi-desktop"></a>Lekérdezés importálása a Power BI desktopba
A Power BI Desktop egy asztali alkalmazás, amely lehetővé teszi, hogy hozzon létre adatkészleteket és jelentéseket, amely közzétehető a Power bi-bA.  Hozzon létre egy lekérdezést a Power Query language exportálva az Azure Monitor használatával is használhatja. 

1. Telepítés [Power BI Desktop](https://powerbi.microsoft.com/desktop/) Ha nem már rendelkezik, és nyissa meg az alkalmazást.
2. Válassza ki **adatok lekérése** > **üres lekérdezés** , nyisson egy új lekérdezést.  Válassza ki **speciális szerkesztő** és az exportált fájl tartalmát, illessze be a lekérdezést. Kattintson a **Done** (Kész) gombra.

    ![A Power BI Desktop-lekérdezés](media/powerbi/desktop-new-query.png)

5. A lekérdezés fut, és az eredmények jelennek meg.  A hitelesítő adatokat csatlakoztatása az Azure-kérheti.  
6. Írja be a lekérdezést egy leíró nevet.  Az alapértelmezett érték **lekérdezés1**. Kattintson a **bezárásához és a alkalmazni** az adatkészlet hozzáadása a jelentéshez.

    ![A Power BI Desktop neve](media/powerbi/desktop-results.png)



## <a name="publish-to-power-bi"></a>Közzététel a Power bi-bA
Ha közzéteszi a Power bi-ba, létrejön egy adatkészlet és a egy jelentést.  Ha létrehoz egy jelentést a Power BI Desktopban, majd ez teszi közzé az adatokkal.  Ha nem, majd jön létre egy üres jelentésből.  A jelentés a Power bi-ban, vagy hozzon létre egy új adatkészlet alapján.

1. Hozzon létre egy jelentést, az adatok alapján.  Használat [Power BI Desktop-dokumentáció](https://docs.microsoft.com/power-bi/desktop-report-view) Ha még nem ismeri azt.  Amikor készen áll a Power bi-bA küldendő, kattintson az **közzététel**.  Amikor a rendszer kéri, válassza ki a célhelyet a Power BI-fiók.  Kivéve, ha egy adott cél szem előtt, használjon **saját munkaterület**.

    ![A Power BI Desktop közzététel](media/powerbi/desktop-publish.png)

1. A közzététel befejezése után kattintson **nyissa meg a Power BI-ban** , nyissa meg az új adatkészlet a Power bi-ban.


### <a name="configure-scheduled-refresh"></a>Az ütemezett frissítés konfigurálása
A Power BI-ban létrehozott adatkészlet ugyanazokat az adatokat a Power BI Desktopban korábban látott fog rendelkezni.  Frissítse az adatkészletet, rendszeres időközönként, futtassa újra a lekérdezést, és az Azure Monitor a legújabb adatokkal való feltöltéséhez kell.  

1. Kattintson a munkaterülethez, ahol a feltöltött a jelentést, majd válassza a **adatkészletek** menü. Válassza ki a helyi menü az új adatkészlet mellett, majd **beállítások**. A **adatforráshoz tartozó hitelesítő adatok** rendelkeznie kell egy üzenet, hogy a hitelesítő adatok érvénytelenek.  Ez azért, mert még nem a megadott hitelesítő adatok még a adatkészletet, akkor használja, ha az adatok frissítésekor.  Kattintson a **hitelesítő adatok szerkesztése** , és adja meg a hitelesítő adatokat a Log Analytics-munkaterületet az Azure monitorban való hozzáférést.

    ![A Power BI-ütemezés](media/powerbi/powerbi-schedule.png)

5. A **ütemezett frissítés** lehetőség bekapcsolása **tartsa adatait naprakészen**.  Igény szerint módosíthatja a **frissítési gyakoriság** és a egy vagy több megadott időpontok a frissítés végrehajtásához.

    ![A Power BI-frissítés](media/powerbi/powerbi-schedule-refresh.png)



## <a name="next-steps"></a>További lépések
* Ismerje meg [naplókereséseket](../log-query/log-query-overview.md) építhetők fel lekérdezések a Power bi-ba exportált.
* Tudjon meg többet [Power BI](https://powerbi.microsoft.com) Azure Monitor log-exportálások vizualizációkat hozhat létre.