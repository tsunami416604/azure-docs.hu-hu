---
title: Futtatás az Azure Data Lake U-SQL-parancsfájlokat a helyi gépen
description: Útmutató U-SQL-feladatok futtatása a helyi gépen az Azure Data Lake Tools for Visual Studio használatával.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/03/2018
ms.openlocfilehash: 8f110a88558b4479d8fdadb2967c7dedeca60ce9
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43043557"
---
# <a name="run-u-sql-scripts-on-your-local-machine"></a>U-SQL-szkriptek futtatása helyi gépen

Ha a U-SQL-parancsfájlok fejlesztése, időt és költséget takaríthat helyileg futtatja a parancsfájlokat. Az Azure Data Lake Tools for Visual Studio támogatja a U-SQL-parancsfájlok futtatását a helyi gépen. 

## <a name="basic-concepts-for-local-runs"></a>Helyi Futtatás alapvető fogalmai

A következő diagram mutatja az összetevők a helyi Futtatás és hogyan ezeket az összetevőket leképezése a felhőben futtassa.

|Összetevő|Helyi Futtatás|Felhőbeli futtatása|
|---------|---------|---------|
|Storage|Helyi adatok gyökérmappa|Alapértelmezett Azure Data Lake Store-fiók|
|Compute|U-SQL helyi futtatási motor|Az Azure Data Lake analitikai szolgáltatás|
|Futtatási környezetet|A helyi számítógépen munkakönyvtár|Az Azure Data Lake Analytics-fürt|

Az alábbi szakaszok helyi futtatási összetevőivel kapcsolatos további információk.

### <a name="local-data-root-folders"></a>Helyi adatok legfelső szintű mappák

A helyi adatok legfelső szintű mappa egy **helyi tároló** a helyi a számítási fiókhoz. Minden mappa a helyi gépen a helyi fájlrendszerben lehet egy helyi adatok legfelső szintű mappát. Ugyanaz, mint az alapértelmezett Azure Data Lake Store-fiókot egy Data Lake Analytics-fiók. Csakúgy, mint egy másik alapértelmezett store-fiók vált átvált egy másik data gyökérmappa van. 

Az adatok legfelső szintű mappát a következőképpen használja:
- Metaadatok Store. Példák olyan adatbázisok, táblák, táblázat értékű függvények és szerelvényeket.
- Keresse meg a bemeneti és kimeneti elérési utak relatív elérési utakat a U-SQL-parancsfájlok vannak meghatározva. Relatív útvonalakat használ, célszerűbb a U-SQL-szkriptek üzembe Azure-ban.

### <a name="u-sql-local-run-engines"></a>U-SQL helyi futtatásakor motorok

A U-SQL helyi futtatási motorja egy **helyi számítási fiók** U-SQL-feladatok. Felhasználók U-SQL feladatok futtatásához helyi keresztül az Azure Data Lake Tools for Visual Studio. Helyi Futtatás is támogatják az Azure Data Lake U-SQL SDK parancssori és programozási felületek. Tudjon meg többet a [Azure Data Lake U-SQL SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/).

### <a name="working-directories"></a>Munkakönyvtárával

Egy U-SQL-szkript futtatásakor a könyvtár munkamappában fordítási eredmények gyorsítótár, a naplók futtatására és a többi funkció végrehajtására van szükség. Az Azure Data Lake Tools for Visual Studio a munkakönyvtárban történik az U-SQL projekt munkakönyvtár. Alatt található `<U-SQL project root path>/bin/debug>`. A munkakönyvtárban törlődik minden alkalommal aktiválódik, új futtatását.

## <a name="local-runs-in-microsoft-visual-studio"></a>A Microsoft Visual Studio helyi futtatása

Az Azure Data Lake Tools for Visual Studio egy beépített helyi futtatási motor rendelkezik. Az eszközök a motor, amely a helyi számítási fiók tervezőfelületére. Helyileg egy U-SQL-szkript futtatásához válassza ki a **helyi gép** vagy **helyi-projekt** fiók a parancsfájl szerkesztő margó legördülő menü. Válassza ki **küldés**.

![Helyi fiók U-SQL parancsfájl elküldése](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-submit-script-to-local-account.png) 
 
## <a name="local-runs-with-a-local-machine-account"></a>Helyi Futtatás és a egy helyi számítógépfiók

A **helyi gép** fiók egy közös helyi számítási fiók egy helyi adatok legfelső szintű mappát a helyi tároló fiókot. Alapértelmezés szerint az adatok legfelső szintű mappa a következő helyen található **C:\Users\<username > \AppData\Local\USQLDataRoot**. Akkor is konfigurálható **eszközök** > **Data Lake** > **lehetőségek és beállítások**.

![Helyi adatok legfelső szintű mappa konfigurálása](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-local-data-root.png)
  
A U-SQL projekt helyi futtatásához szükség. A U-SQL projekt munkakönyvtár a U-SQL helyi futtatási munkakönyvtár szolgál. Fordítási eredményeket, futtassa a naplók és egyéb feladat futtatása – kapcsolatos fájlok jönnek létre és a helyi futtatás során a munkamappában könyvtár alatt tárolja. Minden alkalommal újra futtatja a szkriptet, a munkakönyvtárban lévő fájlok megtisztítják és újbóli létrehozása.

## <a name="local-runs-with-a-local-project-account"></a>Helyi futtatás helyi-projekt-fiókkal

A **helyi-projekt** fiók egy projekt logikailag helyi számítási fiók egyes egy elkülönített helyi adatok legfelső szintű mappát a projekt. Minden aktív U-SQL projekt a Visual Studio megoldáskezelőjében megnyíló rendelkezik egy megfelelő `(Local-project: <project name>)` fiókot. A fiókok listáját a Visual Studio Server Explorer és a U-SQL parancsfájl szerkesztő margó is.  

A **helyi-projekt** fiók letisztult és elkülönített fejlesztési környezetet biztosít. A **helyi gép** fiók az összes helyi feladat metaadatok és bemeneti és kimeneti adatokat tároló megosztott helyi adatok legfelső szintű mappát tartalmaz. De a **helyi-projekt** a fiók létrehoz egy ideiglenes helyi adatok legfelső szintű mappát egy U-SQL projekt munkakönyvtár minden alkalommal, amikor egy U-SQL-parancsfájl futtatása. Az ideiglenes adatokat gyökérmappa törlődik, amikor az újjáépítést vagy történik, futtassa újra. 

A U-SQL projekt egy hivatkozást a projekt és a tulajdonság az elkülönített helyi futtatási környezet kezeli. A projekt és a hivatkozott adatbázis környezetben konfigurálhatja úgy a U-SQL-parancsfájlok a bemeneti adatforrásokat.

### <a name="manage-the-input-data-source-for-a-local-project-account"></a>A bemeneti adatforrás egy helyi-projekt fiók kezelése 

A U-SQL projekt helyi adatok legfelső szintű mappát hoz létre, és az adatok beállít egy **helyi-projekt** fiókot. Egy ideiglenes adatokat gyökérmappa tisztítani és újra létre kell hozni a U-SQL projekt munkakönyvtár alatt minden olyan alkalommal, amikor egy újraépítése és a helyi Futtatás történik. A U-SQL projekt által konfigurált összes adatforrás lesz másolva a helyi ideiglenes adatok legfelső szintű mappa a helyi feladat futtatása előtt. 

Beállíthatja, hogy az adatforrások gyökérmappájában. Kattintson a jobb gombbal **U-SQL projekt** > **tulajdonság** > **tesztelése adatforrás**. Ha egy U-SQL-parancsfájlt futtat a egy **helyi-projekt** fiók, az összes lévő fájlok és almappák a **tesztelése adatforrás** mappát a helyi ideiglenes adatok legfelső szintű mappába másolja. Fájlok alatt almappák szerepelnek. Helyi feladat futtatása után a kimeneti eredmények is található az ideiglenes helyi adatok legfelső szintű mappa alatt a projekt munkakönyvtárban. Ez a kimenet törlődik, és a projekt beolvassa az újonnan létrehozott és tisztítani törölve. 

![A projekt test-adatforrás konfigurálása](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-a-referenced-database-environment-for-a-local-project-account"></a>A hivatkozott adatbázis környezet kezelése egy **helyi-projekt** fiók 

Ha egy U-SQL lekérdezés vagy lekérdezéseket a U-SQL-adatbázis-objektumokat használ, gondoskodnia kell, az adatbázis-környezet készen áll helyileg a U-SQL parancsfájl helyi futtatása előtt. Az egy **helyi-projekt** függőségek felügyelhetik a U-SQL projekt referenciáihoz U-SQL-adatbázis-fiók. U-SQL database projekt referenciáihoz a U-SQL-projekthez is hozzáadhat. U-SQL-parancsfájlok futtatása előtt egy **helyi-projekt** minden hivatkozott fiók adatbázisok vannak telepítve a helyi ideiglenes adatokat gyökérmappájába. És a egy friss elkülönített környezet karbantartása az ideiglenes adatokat gyökérmappában található minden egyes futtatásához.

Kapcsolódó cikkben talál:
* Ismerje meg, hogyan kezelheti a U-SQL database-definíciók és a hivatkozások [U-SQL database-projektek](data-lake-analytics-data-lake-tools-develop-usql-database.md).

## <a name="the-difference-between-local-machine-and-local-project-accounts"></a>A különbség a között **helyi gép** és **helyi-projekt** fiókok

A **helyi gép** fiókot egy Azure Data Lake Analytics-fiók a felhasználók helyi gépek szimulálja. Az Azure Data Lake Analytics-fiókkal rendelkező közös ugyanazt a felhasználói élményt. A **helyi-projekt** fiók felhasználóbarát helyi fejlesztési környezetet biztosít. Ebben a környezetben segít az adatbázis-referenciák és a bemeneti adatokat, mielőtt azok parancsfájlok futtatása helyileg telepíteni. A **helyi gép** fiók, amely az összes projekt keresztül érhető el megosztott állandó környezetet biztosít. A **helyi-projekt** fiók minden projekt elkülönített fejlesztési környezetet biztosít. Az egyes futtatások frissül. A **helyi-projekt** fiók egy gyorsabb fejlesztői felületet kínál úgy, hogy gyorsan alkalmazza a módosításokat.

További különbségeit **helyi gép** és **helyi-projekt** fiókok az alábbi táblázatban láthatók:

|Különbség szög|Helyi gépen|Helyi-projekt|
|----------------|---------------|---------------|
|Helyi hozzáférési|Az összes projekt hozzáférhető.|Csak a megfelelő projektet hozzáférhet ehhez a fiókhoz.|
|Helyi adatok gyökérmappa|Egy állandó helyi mappába. Keresztül konfigurált **eszközök** > **a Data Lake** > **lehetőségek és beállítások**.|Minden helyi futtatáskor a munkakönyvtár U-SQL projekt keretében létrehozott egy ideiglenes mappába. A mappa lekérdezi tisztítani, amikor az újjáépítést vagy történik, futtassa újra.|
|A U-SQL parancsfájl bemeneti adatait|A relatív elérési út a helyi adatok állandó gyökérmappájában.|Beállítása keresztül **U-SQL projekt tulajdonság** > **tesztelése adatforrás**. A helyi futtatás előtt az ideiglenes adatokat gyökérmappában található összes fájlt és almappát lesz másolva.|
|A U-SQL parancsfájl kimeneti adatait|Relatív elérési útja a helyi adatok állandó gyökérmappájában.|Kimenet az ideiglenes adatokat gyökérmappájába. Az eredmények törölve lettek, amikor az újjáépítést vagy történik, futtassa újra.|
|Hivatkozott adatbázis üzembe helyezése|Hivatkozott adatbázisok automatikusan nem telepített, amikor futtat egy **helyi gép** fiókot. Ez megegyezik a elküldése egy Azure Data Lake Analytics-fiókhoz.|Hivatkozott adatbázisok telepítve vannak a **helyi-projekt** fiók automatikusan egy helyi futtatása előtt. Minden adatbázis-környezet megtisztítják és újratelepítése, amikor az újjáépítést vagy történik, futtassa újra.|

## <a name="a-local-run-with-the-u-sql-sdk"></a>A helyi Futtatás a U-SQL SDK-val

U-SQL-parancsfájlok helyileg futtathat a Visual Studióban, és is használhatja az Azure Data Lake U-SQL SDK helyileg futtathat U-SQL-szkriptek parancssori és programozási felületek. Ezeken a felületeken keresztül segítségével automatizálhatja az U-SQL helyi futtatási és tesztek.

Tudjon meg többet a [Azure Data Lake U-SQL SDK](data-lake-analytics-u-sql-sdk.md).

## <a name="next-steps"></a>További lépések

- [A CI/CD-folyamat beállítása az Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md).
- [Az Azure Data Lake Analytics kód tesztelése](data-lake-analytics-cicd-test.md).
