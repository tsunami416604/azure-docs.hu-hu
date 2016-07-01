<properties
   pageTitle="Ismerkedés a Microsoft Power BI Embedded előzetes verziójával"
   description="Power BI Embedded, interaktív Power BI-jelentések felvétele üzletiintelligencia-alkalmazásokba"
   services="power-bi-embedded"
   documentationCenter=""
   authors="dvana"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="04/24/2016"
   ms.author="derrickv"/>

# Ismerkedés a Microsoft Power BI Embedded előzetes verziójával

Az Azure-szolgáltatások körébe tartozó **Microsoft Power BI Embedded** segítségével az alkalmazásfejlesztők interaktív Power BI-jelentéseket vehetnek fel saját alkalmazásaikba. A **Power BI Embedded** anélkül használható a már meglévő alkalmazásokkal, hogy azokat bármilyen módon át kéne alakítani vagy módosítani kéne a felhasználói bejelentkezés módját.

A szolgáltatásról további információt [A Power BI Embedded bemutatása](power-bi-embedded-what-is-power-bi-embedded.md) című témakörben talál.

[A Power BI Embedded bemutatása](power-bi-embedded-what-is-power-bi-embedded.md) című témakörben leírtak szerint a **Power BI Embedded** szolgáltatás erőforrásai az [Azure ARM API-kon](https://msdn.microsoft.com/library/mt712306.aspx) keresztül szerezhetők be. Ebben az esetben a kiosztott erőforrás egy **Power BI-munkaterületcsoport**. Ez a szakasz a munkaterület-csoportok létrehozását ismerteti.

![](media\power-bi-embedded-get-started\introduction.png)

## Munkaterület-csoport létrehozása
A **munkaterület-csoportok** a legfelsőbb szintű Azure-erőforrások, melyek az alkalmazásba beágyazandó tartalmat tárolják. A **munkaterület-csoportok** kétféleképpen hozhatók létre:

   -    Manuálisan, az Azure portál segítségével
   -    Programozott módon, Azure Resource Manager (ARM) API-k segítségével

Az alábbiakban lépésről lépésre bemutatjuk, hogyan hozhat létre egy **munkaterület-csoportot** az Azure portál segítségével.

   1.   Nyissa meg az **Azure portált** és jelentkezzen be: [http://portal.azure.com](http://portal.azure.com).

   2.   Kattintson a **+Új** lehetőségre az ablak tetején.

       ![](media\power-bi-embedded-get-started\create-workspace-1.png)

   3.   Az **Adatok + analitika** területen kattintson a **Power BI Embedded** elemre.
   4.   A **Létrehozás panelen** adja meg a szükséges információkat. A **Díjszabásról** további információt [A Power BI Embedded díjszabása](http://go.microsoft.com/fwlink/?LinkID=760527) című témakörben talál.

       ![](media\power-bi-embedded-get-started\create-workspace-2.png)

   5. Kattintson a **Létrehozás** gombra.

A **munkaterület-csoport** pár percen belül létrejön. Ha elkészült, megnyílik a **Munkaterület-csoport panel**.

   ![](media\power-bi-embedded-get-started\create-workspace-3.png)

Ezen a **Létrehozás panelen** találhatók a munkaterületek létrehozásáért és a tartalom telepítéséért felelős API-k meghívásához szükséges adatok.

A következő szakasz azt ismerteti, hogy miként lehet **elérési kulcsokkal** létrehozni az API-kérelmek hitelesítéséhez szükséges **alkalmazási jogkivonatokat**.

<a name="view-access-keys"/>
## A Power BI API elérési kulcsainak megtekintése

A Power BI REST API-k meghívásához szükséges legfontosabb információt az **elérési kulcsok** jelentik. Segítségükkel lehetséges ugyanis az API-kérelmek hitelesítéséhez szükséges **alkalmazási jogkivonatok** létrehozása. Az **elérési kulcsok** megtekintéséhez a **Beállítások panelen** kattintson az **Elérési kulcsok** lehetőségre. Az **alkalmazási jogkivonatokról** további információt [Az alkalmazási jogkivonatok folyamata](power-bi-embedded-app-token-flow.md) című témakörben talál.

   ![](media\power-bi-embedded-get-started\access-keys.png)

Megfigyelheti, hogy két kulccsal is rendelkezik.

   ![](media\power-bi-embedded-get-started\access-keys-2.png)

Készítsen másolatot a kulcsokról, és tárolja őket biztonságos helyen az alkalmazáson belül. Nagyon fontos, hogy ezeket a kulcsokat úgy kezelje, mintha jelszavak volnának, mivel a segítségükkel a **munkaterület-csoport** minden tartalmához hozzá lehet férni.

Annak ellenére, hogy két kulccsal rendelkezik, egyszerre csak egyet kell használnia. A második kulcsra azért van szükség, hogy a kulcsok rendszeres újragenerálása ne zavarja meg a szolgáltatáshoz való hozzáférést.

Most, hogy rendelkezik **elérési kulcsokkal** és az alkalmazáshoz való Power BI-példánnyal, jelentést importálhat az alkalmazásba. Mielőtt megismertetnénk a jelentések importálásának módjával, a következő szakasz azt mutatja be, hogyan hozhatók létre az alkalmazásba beágyazandó Power BI-adatkészletek és jelentések.

## Az alkalmazásba beágyazandó Power BI-adatkészletek és jelentések létrehozása

Most, hogy rendelkezik **elérési kulcsokkal**, és létrehozta az alkalmazáshoz való Power BI-példányt, el kell készítenie a beágyazni kívánt Power BI-adatkészleteket és jelentéseket. Az adatkészletek és a jelentések a **Power BI Desktop** segítségével hozhatók létre. A [Power BI Desktop ingyenesen](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/) letölthető. Vagy, ha gyorsan el szeretné kezdeni a munkát, töltse le a [Kiskereskedelmi elemzési minta PBIX-fájlt] (http://go.microsoft.com/fwlink/?LinkID=780547). A **Power BI Desktop** működéséről további információt a [Bevezetés a Power BI Desktop használatába](https://powerbi.microsoft.com/en-us/guided-learning/powerbi-learning-0-2-get-started-power-bi-desktop) című témakörben talál.

A **Power BI Desktop** segítségével úgy csatlakozhat az adatforráshoz, hogy a **Power BI Desktopba** importálja az adatok másolatát, de a **DirectQueryt** is használhatja ugyanerre a célra.

Az alábbiakban a két módszer (**importálás** és **DirectQuery**) közti különbségeket láthatja.

|Importálás | DirectQuery
|---|---
|Ezzel a módszerrel a táblákat, oszlopokat *és adatokat* is a **Power BI Desktopba** importálhatja vagy másolja. A megjelenítésekkel végzett munka során a **Power BI Desktop** lekéri az adatok másolatát. A mögöttes adatok módosulásainak megtekintéséhez újból frissítenie vagy importálnia kell a teljes aktuális adatkészletet.|Ezzel a módszerrel csak *a táblákat és oszlopokat* importálhatja vagy másolhatja a **Power BI Desktopba**. A megjelenítésekkel végzett munka során a **Power BI Desktop** lekéri a mögöttes adatokat, azaz Önnek mindig az aktuális adatok állnak a rendelkezésére.

Az adatforráshoz való csatlakozásról további információt a [Csatlakozás az adatforráshoz](power-bi-embedded-connect-datasource.md) című témakörben talál.

A **Power BI Desktopban** végzett munka mentésekor létrejön egy PBIX-fájl. Ez tartalmazza a jelentést. Ha importálja az adatokat, a PBIX-fájl a teljes adatkészletet tartalmazza, ha azonban a **DirectQueryt** használja, a PBIX-fájlban csak az adatkészlet sémája található. A [Power BI importálási API-jával](https://msdn.microsoft.com/library/mt711504.aspx) programozott módon telepítheti a PBIX-fájlt a munkaterületre.

> [AZURE.NOTE] A **Power BI Embedded** által kínált további API-k segítségével módosíthatja, hogy az adatkészlet mely kiszolgálóra vagy adatbázisra mutasson, illetve beállíthatja a szolgáltatásfiók hitelesítő adatait, melyek segítségével az adatkészlet majd csatlakozhat az adatbázishoz. További információ: [Post SetAllConnections](https://msdn.microsoft.com/library/mt711505.aspx) és [Patch Gateway Datasource](https://msdn.microsoft.com/library/mt711498.aspx).

## Következő lépések
Az előző lépések során létrehozott egy munkaterület-csoportot, valamint egy jelentést és egy adatkészletet. Most azt is megismerheti, hogyan írhat kódot a **Power BI Embedded** szolgáltatáshoz. Segítségképpen létrehoztunk egy webalkalmazás-mintát: [Bevezetés a minta használatába](power-bi-embedded-get-started-sample.md). A minta segítségével az alábbi műveleteket sajátíthatja el:

  - Tartalom kiépítése
      - Munkaterület létrehozása
      - PBIX-fájl importálása
      - A kapcsolati karakterláncok frissítése, és hitelesítő adatok beállítása az adatkészletekhez.

  - Jelentés biztonságos beágyazása

## Lásd még:
- [Bevezetés a minta használatába](power-bi-embedded-get-started-sample.md)
- [A Power BI Embedded bemutatása](power-bi-embedded-what-is-power-bi-embedded.md)
- [Bevezetés a Power BI Desktop használatába](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started)
- [Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)
- [A Power BI Embedded díjszabása](http://go.microsoft.com/fwlink/?LinkID=760527)



<!--HONumber=Jun16_HO2-->


