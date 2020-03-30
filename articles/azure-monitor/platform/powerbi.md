---
title: Azure Log Analytics-adatok importálása a Power BI-ba | Microsoft dokumentumok
description: A Power BI a Microsoft felhőalapú üzleti elemzési szolgáltatása, amely gazdag vizualizációkat és jelentéseket biztosít a különböző adathalmazok elemzéséhez.  Ez a cikk azt ismerteti, hogy miként konfigurálhatja és importálhatja a Log Analytics-adatokat a Power BI-ba, és hogyan konfigurálhatja őket automatikus frissítésre.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/01/2019
ms.openlocfilehash: 8ff24d508eb35c4f2a04c7d024254fa6f1875da8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659283"
---
# <a name="import-azure-monitor-log-data-into-power-bi"></a>Az Azure Monitor naplóadatainak importálása a Power BI-ba


[A Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) a Microsoft felhőalapú üzleti elemzési szolgáltatása, amely gazdag vizualizációkat és jelentéseket biztosít a különböző adathalmazok elemzéséhez.  Importálhatja az Azure Monitor naplólekérdezésének eredményeit egy Power BI-adatkészletbe, így kihasználhatja annak funkcióit, például a különböző forrásokból származó adatok kombinálását és a webes és mobileszközökön lévő jelentések megosztását.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="overview"></a>Áttekintés
Ha adatokat szeretne importálni az Azure Monitor [naplóanalytics-munkaterületéről](manage-access.md) a Power BI-ba, hozzon létre egy adatkészletet a Power BI-ban az Azure Monitor [ban lévő naplólekérdezés](../log-query/log-query-overview.md) alapján.  A lekérdezés az adatkészlet minden frissítésekor fut.  Ezután létrehozhat olyan Power BI-jelentéseket, amelyek az adatkészletből származó adatokat használnak.  Ha a Power BI-ban szeretné létrehozni az adatkészletet, exportálja a lekérdezést a Log Analytics szolgáltatásból a [Power Query (M) nyelvre.](https://docs.microsoft.com/powerquery-m/power-query-m-language-specification)  Ezután ezzel lekérdezést hozhat létre a Power BI Desktopban, majd adatkészletként közzéteheti azt a Power BI-ban.  A folyamat részleteit az alábbiakban ismertetjük.

![Az Elemzés naplózása a Power BI-ba](media/powerbi/overview.png)

## <a name="export-query"></a>Lekérdezés exportálása
Először hozzon létre egy [naplólekérdezést,](../log-query/log-query-overview.md) amely visszaadja a Power BI adatkészlet feltöltésére kívánt adatokat.  Ezután exportálja a lekérdezést a Power BI Desktop által használható [Power Query (M) nyelvre.](https://docs.microsoft.com/powerquery-m/power-query-m-language-specification)

1. [Hozza létre a log lekérdezést a Log Analytics az](../log-query/get-started-portal.md) adatkészlet adatainak kinyeréséhez.
2. Válassza a > **Power BI-lekérdezés exportálása (M) lehetőséget.** **Export**  Ez a lekérdezést egy **PowerBIQuery.txt**nevű szövegfájlba exportálja. 

    ![Naplókeresés exportálása](media/powerbi/export-analytics.png)

3. Nyissa meg a szövegfájlt, és másolja annak tartalmát.

## <a name="import-query-into-power-bi-desktop"></a>Lekérdezés importálása a Power BI Desktopba
A Power BI Desktop egy asztali alkalmazás, amely lehetővé teszi a Power BI-ban közzétehető adatkészletek és jelentések létrehozását.  Azt is használhatja, hogy hozzon létre egy lekérdezést az Azure Monitorból exportált Power Query nyelv használatával. 

1. Telepítse a [Power BI Desktopot,](https://powerbi.microsoft.com/desktop/) ha még nem rendelkezik ilyennel, majd nyissa meg az alkalmazást.
2. Új lekérdezés megnyitásához jelölje be az > **Adatüres lekérdezés** **beszerezni**e jelölőnégyzetet.  Ezután válassza **a Speciális szerkesztő** lehetőséget, és illessze be az exportált fájl tartalmát a lekérdezésbe. Kattintson a **Done** (Kész) gombra.

    ![Power BI Desktop lekérdezés](media/powerbi/desktop-new-query.png)

5. A lekérdezés fut, és az eredmények jelennek meg.  Előfordulhat, hogy a rendszer hitelesítő adatokat kér az Azure-hoz való csatlakozáshoz.  
6. Írja be a lekérdezés leíró nevét.  Az alapértelmezett érték a **Query1**. Kattintson a **Bezárás és az Alkalmaz** gombra, ha hozzá szeretné adni az adatkészletet a jelentéshez.

    ![Power BI Desktop neve](media/powerbi/desktop-results.png)



## <a name="publish-to-power-bi"></a>Közzététel a Power BI-ban
Amikor a Power BI-ban tesz közzé egy adatkészletet és egy jelentést.  Ha jelentést hoz létre a Power BI Desktopban, akkor ez az adatokkal együtt közzétételre kerül.  Ha nem, akkor egy üres jelentés jön létre.  Módosíthatja a jelentést a Power BI-ban, vagy létrehozhat egy újat az adatkészlet alapján.

1. Jelentés létrehozása az adatok alapján.  Használja a [Power BI Desktop dokumentációját,](https://docs.microsoft.com/power-bi/desktop-report-view) ha nem ismeri.  
1. Ha készen áll arra, hogy elküldje a Power BI-ba, kattintson **a Közzététel gombra.**  
1. Amikor a rendszer kéri, válasszon ki egy úti célt a Power BI-fiókjában.  Ha csak egy konkrét célt nem szem előtt tartva, használja **a Saját munkaterületet**.

    ![A Power BI Desktop közzététele](media/powerbi/desktop-publish.png)

1. Amikor a közzététel befejeződik, kattintson a **Megnyitás a Power BI-ban** gombra a Power BI új adatkészlettel történő megnyitásához.


### <a name="configure-scheduled-refresh"></a>Ütemezett frissítés beállítása
A Power BI-ban létrehozott adatkészlet ekként fog nak rendelkezni a Power BI Desktopban korábban látott adatokkal.  Rendszeresen frissítenie kell az adatkészletet a lekérdezés ismételt futtatásához, és az Azure Monitor legújabb adatainak feltöltéséhez.  

1. Kattintson arra a munkaterületre, ahol a jelentést feltöltötte, és válassza az **Adatkészletek menüt.** 
1. Válassza ki az új adatkészlet melletti helyi menüt, és válassza a **Beállítások lehetőséget.** 
1. Az **Adatforrás hitelesítő adatai** csoportban kell lennie egy üzenetnek, hogy a hitelesítő adatok érvénytelenek.  Ennek az az oka, hogy még nem adott meg hitelesítő adatokat az adatkészlet számára az adatok frissítésekor.  
1. Kattintson **a Hitelesítő adatok szerkesztése** elemre, és adja meg a hitelesítő adatokat az Azure Monitor Log Analytics-munkaterületéhez való hozzáféréssel. Ha kétfaktoros hitelesítésre van szüksége, válassza az **OAuth2** lehetőséget a **hitelesítési módszer** hitelesítő adatokkal való bejelentkezésre.

    ![Power BI ütemezése](media/powerbi/powerbi-schedule.png)

5. Az **Ütemezett frissítés** csoportban kapcsolja be az adatok **naprakészen tartásának**lehetőségét.  A frissítés futtatásához tetszés szerint módosíthatja a **frissítés gyakoriságát** és egy vagy több konkrét időpontot.

    ![A Power BI frissítése](media/powerbi/powerbi-schedule-refresh.png)



## <a name="next-steps"></a>További lépések
* Ismerje meg a Power BI-ba exportálható lekérdezések létrehozásához használható [naplókereséseket.](../log-query/log-query-overview.md)
* További információ a [Power BI-ról](https://powerbi.microsoft.com) az Azure Monitor naplóexportálása alapján vizualizációk létrehozásához.
