---
title: A Microsoft Power BI-munkaterületcsoportok bemutatása
description: Az Azure-szolgáltatások körébe tartozó Power BI-munkaterületcsoportok segítségével az alkalmazásfejlesztők interaktív Power BI-jelentéseket vehetnek fel saját alkalmazásaikba.
services: power-bi-embedded
author: markingmyname
ROBOTS: NOINDEX
ms.assetid: 4787cf44-5d1c-4bc3-b3fd-bf396e5c1176
ms.service: power-bi-embedded
ms.topic: conceptual
ms.workload: powerbi
ms.date: 09/25/2017
ms.author: maghan
ms.openlocfilehash: b565955abaae44efe20291a6e46b57a5fec941d1
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43046558"
---
# <a name="get-started-with-microsoft-power-bi-workspace-collections"></a>A Microsoft Power BI-munkaterületcsoportok bemutatása

Az Azure-szolgáltatások körébe tartozó **Power BI-munkaterületcsoportok** segítségével az alkalmazásfejlesztők interaktív Power BI-jelentéseket vehetnek fel saját alkalmazásaikba. A **Power BI-munkaterületcsoportok** úgy működnek együtt a meglévő alkalmazásokkal, hogy nem kell újratervezni vagy módosítani a felhasználók bejelentkezésének módját.

> [!IMPORTANT]
> A Power BI munkaterületi gyűjtemények szolgáltatás elavult, és 2018 júniusáig vagy a szerződésében jelzett időpontig érhető el. Javasoljuk, hogy az alkalmazása zavartalan működése érdekében tervezze meg a migrációt a Power BI Embedded szolgáltatásba. Az adatok a Power BI Embedded szolgáltatásba való migrálásának részleteiért lásd a [Power BI munkaterületi gyűjtemények tartalmának Power BI Embedded szolgáltatásba történő migrálásával](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/) foglalkozó cikket.

A **Microsoft Power BI-munkaterületcsoportok** erőforrásai az [Azure Resource Manager API-k](https://msdn.microsoft.com/library/mt712306.aspx) használatával hozhatók létre. Ebben az esetben a létrehozott erőforrás egy **Power BI-munkaterületcsoport**.

![A Power BI-munkaterületcsoportok általános munkafolyamata](media/get-started/introduction.png)

## <a name="create-a-workspace-collection"></a>Munkaterület-csoport létrehozása

A **munkaterület-csoportok** a legfelsőbb szintű Azure-erőforrások, melyek az alkalmazásba beágyazandó tartalmat tárolják. A **munkaterület-csoportok** kétféleképpen hozhatók létre:

* Manuálisan, az Azure Portal segítségével
* Programozott módon, Azure Resource Manager API-k segítségével

Az alábbiakban részletes bemutatjuk, hogyan hozhat létre egy **munkaterület-csoportot** az Azure Portal segítségével.

1. Nyissa meg az **Azure Portalt** és jelentkezzen be: [http://portal.azure.com](http://portal.azure.com).
2. Válassza a **+Új** lehetőséget az ablak tetején.
   
   ![+Új lehetőség az Azure Portalon](media/get-started/create-workspace-1.png)
3. Az **Adatok és analitika** területen válassza a **Power BI-munkaterületcsoport** elemet.
4. Ha már rendelkezik egy Power BI-munkaterületcsoport-előfizetéssel, az első lépésekre vonatkozó üzenetben válassza az alsó, **Munkaterület-csoport létrehozása** elnevezésű lehetőséget.

5. A **Munkaterület-csoport** oldalon adja meg a szükséges információkat.
   
   ![Munkaterület-csoport létrehozása](media/get-started/create-workspace-2.png)
1. Kattintson a **Létrehozás** gombra.

A **munkaterület-csoport** pár percen belül létrejön. Miután elkészült, a **munkaterület-csoport** megnyílik.

   ![Munkaterület-csoport az Azure Portalon](media/get-started/create-workspace-3.png)

A **Létrehozás** eredményei között megtalálhatók a munkaterületek létrehozásáért és a tartalom telepítéséért felelős API-k meghívásához szükséges adatok.

<a name="view-access-keys"/>

## <a name="view-power-bi-api-access-keys"></a>A Power BI API elérési kulcsainak megtekintése

A Power BI REST API-k meghívásához szükséges legfontosabb információt az **elérési kulcsok** jelentik. Segítségükkel lehetséges ugyanis az API-kérelmek hitelesítéséhez szükséges **alkalmazási jogkivonatok** létrehozása. A **hozzáférési kulcsok** megtekintéséhez a **Beállítások** alatt kattintson a **Hozzáférési kulcsok** elemre. További információt az **alkalmazás-jogkivonatokról** a [Power BI-munkaterületcsoportok használatával történő hitelesítést és engedélyezést](app-token-flow.md) ismertető cikkben talál.

   ![Hozzáférési kulcsok a munkaterület-csoport beállításai között az Azure Portalon](media/get-started/access-keys.png)

Láthatja, hogy két kulccsal rendelkezik.

   ![Két kulcs a hozzáférési kulcsok között](media/get-started/access-keys-2.png)

Készítsen másolatot a kulcsokról, és tárolja őket biztonságos helyen az alkalmazáson belül. Fontos, hogy ezeket a kulcsokat úgy kezelje, mintha jelszavak volnának, mivel ezek segítségével lehet hozzáférni a **munkaterület-csoport** minden tartalmához.

Jóllehet két kulcs van listázva, egyszerre azonban mindig csak az egyikre lesz szüksége. A második kulcsra azért van szükség, hogy a kulcsok rendszeres újragenerálása ne zavarja meg a szolgáltatáshoz való hozzáférést.

Most, hogy rendelkezik **elérési kulcsokkal** és az alkalmazáshoz való Power BI-példánnyal, jelentést importálhat az alkalmazásba. Mielőtt megismertetnénk a jelentések importálásának módjával, a következő szakasz azt mutatja be, hogyan hozhatók létre az alkalmazásba beágyazandó Power BI-adatkészletek és jelentések.

## <a name="working-with-workspaces"></a>A munkaterületek használata

Munkaterület-csoport létrehozása után létre kell hoznia egy olyan munkaterületet, amely helyet biztosít a jelentéseknek és az adatkészleteknek. A munkaterület létrehozásához használja a [Munkaterület küldése REST API](https://msdn.microsoft.com/library/azure/mt711503.aspx)-t.

## <a name="create-power-bi-datasets-and-reports-to-embed-into-an-app-using-power-bi-desktop"></a>Power BI-adatkészletek és -jelentések létrehozása és beágyazása egy Power BI Desktopot használó alkalmazásba

Most, hogy rendelkezik **hozzáférési kulcsokkal**, és létrehozta az alkalmazáshoz való Power BI-példányt, el kell készítenie a beágyazni kívánt Power BI-adatkészleteket és -jelentéseket. Az adatkészletek és a jelentések a **Power BI Desktop** segítségével hozhatók létre. A [Power BI Desktop ingyenesen](https://go.microsoft.com/fwlink/?LinkId=521662) letölthető. Vagy, a gyorsabb kezdéshez, letöltheti a [Kiskereskedelmi elemzési minta PBIX-fájlt](http://go.microsoft.com/fwlink/?LinkID=780547).

> [!NOTE]
> A **Power BI Desktop** működéséről további információt a [Bevezetés a Power BI Desktop használatába](https://powerbi.microsoft.com/guided-learning/powerbi-learning-0-2-get-started-power-bi-desktop) című témakörben talál.

A **Power BI Desktop** segítségével úgy csatlakozhat az adatforráshoz, hogy a **Power BI Desktopba** importálja az adatok másolatát, de a **DirectQueryt** is használhatja ugyanerre a célra.

Az alábbiakban a két módszer (**importálás** és **DirectQuery**) közti különbségeket láthatja.

| Importálás | DirectQuery |
| --- | --- |
| Ezzel a módszerrel a táblákat, oszlopokat *és adatokat* is a **Power BI Desktopba** importálhatja vagy másolja. A megjelenítésekkel végzett munka során a **Power BI Desktop** lekéri az adatok másolatát. A mögöttes adatok módosulásainak megtekintéséhez újból frissítenie vagy importálnia kell a teljes aktuális adatkészletet. |Ezzel a módszerrel csak *a táblákat és oszlopokat* importálhatja vagy másolhatja a **Power BI Desktopba**. A megjelenítésekkel végzett munka során a **Power BI Desktop** lekéri a mögöttes adatokat, azaz Önnek mindig az aktuális adatok állnak a rendelkezésére. |

Az adatforráshoz való csatlakozásról további információt a [Csatlakozás az adatforráshoz](connect-datasource.md) című témakörben talál.

A **Power BI Desktopban** végzett munka mentésekor létrejön egy PBIX-fájl. Ez tartalmazza a jelentést. Ha importálja az adatokat, a PBIX-fájl a teljes adatkészletet tartalmazza, ha azonban a **DirectQueryt** használja, a PBIX-fájlban csak az adatkészlet sémája található. A [Power BI importálási API-jával](https://msdn.microsoft.com/library/mt711504.aspx) programozott módon telepítheti a PBIX-fájlt a munkaterületre.

> [!NOTE]
> A **Power BI-munkaterületcsoportok** által kínált további API-k segítségével módosíthatja, hogy az adatkészlet mely kiszolgálóra vagy adatbázisra mutasson, illetve beállíthatja a szolgáltatásfiók hitelesítő adatait, melyek segítségével az adatkészlet majd csatlakozhat az adatbázishoz. További információ: [Post SetAllConnections](https://msdn.microsoft.com/library/mt711505.aspx) és [Patch Gateway Datasource](https://msdn.microsoft.com/library/mt711498.aspx).

## <a name="create-power-bi-datasets-and-reports-using-apis"></a>Power BI-adatkészletek és -jelentések létrehozása API-k használatával

### <a name="datasets"></a>Adathalmazok

A REST API segítségével létrehozhat adatkészleteket a Power BI-munkaterületcsoportokban. Ezután elküldheti adatait saját adatkészletébe. Ez lehetővé teszi, hogy dolgozhasson az adattal a Power BI Desktop használata nélkül is. További információkat az [adatkészletek közzétételét](https://msdn.microsoft.com/library/azure/mt778875.aspx) ismertető részben talál.

### <a name="reports"></a>Jelentések

A JavaScript API használatával saját alkalmazásában készíthet jelentést egy adatkészletből. További információkért lásd az [új jelentések adatkészletből, a Power BI-munkaterületcsoportok használatával történő létrehozását](create-report-from-dataset.md) ismertető részt.

## <a name="see-also"></a>Lásd még:

[Bevezetés a minta használatába](get-started-sample.md)  
[Hitelesítés és engedélyezés a Power BI-munkaterületcsoportok használatával](app-token-flow.md)  
[Jelentés beágyazása](embed-report.md)  
[Új jelentés létrehozása adatkészletből a Power BI-munkaterületcsoportok használatával](create-report-from-dataset.md)
[Jelentések mentése](save-reports.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[JavaScript beágyazási minta](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

További kérdései vannak? [Tegye próbára a Power BI közösségét](http://community.powerbi.com/)

