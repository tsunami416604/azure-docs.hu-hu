---
title: "A Mcirosoft Power BI Embedded bemutatása"
description: "Power BI Embedded, interaktív Power BI-jelentések felvétele üzletiintelligencia-alkalmazásokba"
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.assetid: 4787cf44-5d1c-4bc3-b3fd-bf396e5c1176
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 01/06/2017
ms.author: asaxton
translationtype: Human Translation
ms.sourcegitcommit: 7db56a4c0efb208591bb15aa03a4c0dbf833d22e
ms.openlocfilehash: cedf69c6e50470e9d16d8e1e361bb51eb8abed72


---
# <a name="get-started-with-microsoft-power-bi-embedded"></a>A Mcirosoft Power BI Embedded bemutatása
Az Azure-szolgáltatások körébe tartozó **Power BI Embedded** segítségével az alkalmazásfejlesztők interaktív Power BI-jelentéseket vehetnek fel saját alkalmazásaikba. A **Power BI Embedded** úgy működik együtt a meglévő alkalmazásokkal, hogy nem kell újratervezni vagy módosítani a felhasználók bejelentkezésének módját.

A **Microsoft Power BI Embedded** erőforrásai az [Azure ARM API-k](https://msdn.microsoft.com/library/mt712306.aspx) használatával hozhatók létre. Ebben az esetben a létrehozott erőforrás egy **Power BI munkaterület-csoport**.

![](media/power-bi-embedded-get-started/introduction.png)

## <a name="create-a-workspace-collection"></a>Munkaterület-csoport létrehozása
A **munkaterület-csoportok** a legfelsőbb szintű Azure-erőforrások, melyek az alkalmazásba beágyazandó tartalmat tárolják. A **munkaterület-csoportok** kétféleképpen hozhatók létre:

* Manuálisan, az Azure Portal segítségével
* Programozott módon, Azure Resource Manager (ARM) API-k segítségével

Az alábbiakban lépésről lépésre bemutatjuk, hogyan hozhat létre egy **munkaterület-csoportot** az Azure Portal segítségével.

1. Nyissa meg az **Azure Portalt** és jelentkezzen be: [http://portal.azure.com](http://portal.azure.com).
2. Kattintson a **+Új** lehetőségre az ablak tetején.
   
   ![](media/power-bi-embedded-get-started/create-workspace-1.png)
3. Az **Adatok + analitika** területen kattintson a **Power BI Embedded** elemre.
4. A **Létrehozás panelen** adja meg a szükséges információkat. A **Díjszabásról** további információt [A Power BI Embedded díjszabása](http://go.microsoft.com/fwlink/?LinkID=760527) című témakörben talál.
   
   ![](media/power-bi-embedded-get-started/create-workspace-2.png)
5. Kattintson a **Létrehozás** gombra.

A **munkaterület-csoport** pár percen belül létrejön. Ha ez elkészült, megnyílik a **Munkaterület-csoport panel**.

   ![](media/power-bi-embedded-get-started/create-workspace-3.png)

A **Létrehozás panelen** találhatók a munkaterületek létrehozásáért és a tartalom telepítéséért felelős API-k meghívásához szükséges adatok.

<a name="view-access-keys"/>

## <a name="view-power-bi-api-access-keys"></a>A Power BI API elérési kulcsainak megtekintése
A Power BI REST API-k meghívásához szükséges legfontosabb információt az **elérési kulcsok** jelentik. Segítségükkel lehetséges ugyanis az API-kérelmek hitelesítéséhez szükséges **alkalmazási jogkivonatok** létrehozása. Az **elérési kulcsok** megtekintéséhez a **Beállítások panelen** kattintson az **Elérési kulcsok** lehetőségre. További információk az **alkalmazás-jogkivonatokról**: [Authenticating and authorizing with Power BI Embedded](power-bi-embedded-app-token-flow.md) (Hitelesítés és engedélyezés a Power BI Embedded használatával).

   ![](media/power-bi-embedded-get-started/access-keys.png)

Láthatja, hogy két kulccsal rendelkezik.

   ![](media/power-bi-embedded-get-started/access-keys-2.png)

Készítsen másolatot a kulcsokról, és tárolja őket biztonságos helyen az alkalmazáson belül. Nagyon fontos, hogy ezeket a kulcsokat úgy kezelje, mintha jelszavak volnának, mivel a segítségükkel a **munkaterület-csoport** minden tartalmához hozzá lehet férni.

Jóllehet két kulcs van listázva, egyszerre azonban mindig csak az egyikre lesz szüksége. A második kulcsra azért van szükség, hogy a kulcsok rendszeres újragenerálása ne zavarja meg a szolgáltatáshoz való hozzáférést.

Most, hogy rendelkezik **elérési kulcsokkal** és az alkalmazáshoz való Power BI-példánnyal, jelentést importálhat az alkalmazásba. Mielőtt megismertetnénk a jelentések importálásának módjával, a következő szakasz azt mutatja be, hogyan hozhatók létre az alkalmazásba beágyazandó Power BI-adatkészletek és jelentések.

## <a name="create-power-bi-datasets-and-reports-to-embed-into-an-app"></a>Az alkalmazásba beágyazandó Power BI-adatkészletek és jelentések létrehozása
Most, hogy rendelkezik **elérési kulcsokkal**, és létrehozta az alkalmazáshoz való Power BI-példányt, el kell készítenie a beágyazni kívánt Power BI-adatkészleteket és jelentéseket. Az adatkészletek és a jelentések a **Power BI Desktop** segítségével hozhatók létre. A [Power BI Desktop ingyenesen](https://go.microsoft.com/fwlink/?LinkId=521662) letölthető. Vagy, a gyorsabb kezdéshez, letöltheti a [Kiskereskedelmi elemzési minta PBIX-fájlt](http://go.microsoft.com/fwlink/?LinkID=780547).

> [!NOTE]
> A **Power BI Desktop** működéséről további információt a [Bevezetés a Power BI Desktop használatába](https://powerbi.microsoft.com/en-us/guided-learning/powerbi-learning-0-2-get-started-power-bi-desktop) című témakörben talál.

A **Power BI Desktop** segítségével úgy csatlakozhat az adatforráshoz, hogy a **Power BI Desktopba** importálja az adatok másolatát, de a **DirectQueryt** is használhatja ugyanerre a célra.

Az alábbiakban a két módszer (**importálás** és **DirectQuery**) közti különbségeket láthatja.

| Importálás | DirectQuery |
| --- | --- |
| Ezzel a módszerrel a táblákat, oszlopokat *és adatokat* is a **Power BI Desktopba** importálhatja vagy másolja. A megjelenítésekkel végzett munka során a **Power BI Desktop** lekéri az adatok másolatát. A mögöttes adatok módosulásainak megtekintéséhez újból frissítenie vagy importálnia kell a teljes aktuális adatkészletet. |Ezzel a módszerrel csak *a táblákat és oszlopokat* importálhatja vagy másolhatja a **Power BI Desktopba**. A megjelenítésekkel végzett munka során a **Power BI Desktop** lekéri a mögöttes adatokat, azaz Önnek mindig az aktuális adatok állnak a rendelkezésére. |

Az adatforráshoz való csatlakozásról további információt a [Csatlakozás az adatforráshoz](power-bi-embedded-connect-datasource.md) című témakörben talál.

A **Power BI Desktopban** végzett munka mentésekor létrejön egy PBIX-fájl. Ez tartalmazza a jelentést. Ha importálja az adatokat, a PBIX-fájl a teljes adatkészletet tartalmazza, ha azonban a **DirectQueryt** használja, a PBIX-fájlban csak az adatkészlet sémája található. A [Power BI importálási API-jával](https://msdn.microsoft.com/library/mt711504.aspx) programozott módon telepítheti a PBIX-fájlt a munkaterületre.

> [!NOTE]
> A **Power BI Embedded** által kínált további API-k segítségével módosíthatja, hogy az adatkészlet mely kiszolgálóra vagy adatbázisra mutasson, illetve beállíthatja a szolgáltatásfiók hitelesítő adatait, melyek segítségével az adatkészlet majd csatlakozhat az adatbázishoz. További információ: [Post SetAllConnections](https://msdn.microsoft.com/library/mt711505.aspx) és [Patch Gateway Datasource](https://msdn.microsoft.com/library/mt711498.aspx).

## <a name="next-steps"></a>Következő lépések
Az előző lépések során létrehozott egy munkaterület-csoportot, valamint egy jelentést és egy adatkészletet. Most azt is megismerheti, hogyan írhat kódot a **Power BI Embedded** szolgáltatáshoz. Segítségképpen létrehoztunk egy webalkalmazás-mintát: [Bevezetés a minta használatába](power-bi-embedded-get-started-sample.md). A minta segítségével az alábbi műveleteket sajátíthatja el:

* Tartalom kiépítése
  * Munkaterület létrehozása
  * PBIX-fájl importálása
  * A kapcsolati karakterláncok frissítése, és hitelesítő adatok beállítása az adatkészletekhez.
* Jelentés biztonságos beágyazása

## <a name="see-also"></a>Lásd még:
* [Bevezetés a minta használatába](power-bi-embedded-get-started-sample.md)
* [Authenticating and authorizing with Power BI Embedded (Hitelesítés és engedélyezés a Power BI Embedded használatával)](power-bi-embedded-app-token-flow.md)
* [Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)

További kérdései vannak? [Tegye próbára a Power BI közösségét](http://community.powerbi.com/)




<!--HONumber=Jan17_HO1-->


