---
title: Azure Data Lake U-SQL-parancsfájlok futtatása a helyi számítógépen
description: Ismerje meg, hogyan használhatja az Azure Data Lake Tools for Visual Studio u-SQL-feladatok futtatását a helyi számítógépen.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/03/2018
ms.openlocfilehash: 42e58125fcbc3ab411c0d7503c42c14c28178428
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "62113935"
---
# <a name="run-u-sql-scripts-on-your-local-machine"></a>U-SQL-parancsfájlok futtatása a helyi számítógépen

U-SQL-parancsfájlok fejlesztése kor időt és költséget takaríthat meg a parancsfájlok helyi futtatásával. Az Azure Data Lake Tools for Visual Studio támogatja az U-SQL-parancsfájlok futtatását a helyi számítógépen. 

## <a name="basic-concepts-for-local-runs"></a>Alapvető fogalmak a helyi futtatásokhoz

Az alábbi diagram a helyi futtatás összetevőit mutatja be, és azt, hogy ezek az összetevők hogyan felelnek meg a felhőhöz való futtatáshoz.

|Összetevő|Helyi futtatás|Felhőalapú futtatás|
|---------|---------|---------|
|Storage|Helyi adatgyökérmappa|Alapértelmezett Azure Data Lake Áruházbeli fiók|
|Compute|U-SQL helyi futtatási motor|Az Azure Data Lake Analytics szolgáltatás|
|Környezet futtatása|Munkakönyvtár a helyi számítógépen|Azure Data Lake Analytics-fürt|

Az alábbi szakaszok további információt nyújtanak a helyi futtatási összetevőkről.

### <a name="local-data-root-folders"></a>Helyi adatgyökérmappák

A helyi adatgyökér mappa a helyi számítási fiók **helyi tárolója.** A helyi számítógép helyi fájlrendszerében lévő mappák lehetnek helyi adatgyökérmappa. Ez megegyezik a Data Lake Analytics-fiók alapértelmezett Azure Data Lake Store-fiókjával. Egy másik adatgyökérmappára való váltás olyan, mintha egy másik alapértelmezett áruházi fiókra váltana. 

Az adatgyökér mappa a következőképpen használatos:
- Metaadatok tárolása. Ilyenek például az adatbázisok, táblák, táblaértékű függvények és szerelvények.
- Keresse meg az U-SQL-parancsfájlokban relatív elérési utakként definiált bemeneti és kimeneti elérési utakat. A relatív elérési utak használatával egyszerűbb en üzembe helyezni az U-SQL-parancsfájlokat az Azure-ba.

### <a name="u-sql-local-run-engines"></a>U-SQL helyi futtatási motorok

Az U-SQL helyi futtatási motor egy **helyi számítási fiók** u-SQL feladatokhoz. A felhasználók helyileg futtathatják az U-SQL-feladatokat az Azure Data Lake Tools for Visual Studio segítségével. A helyi futtatások az Azure Data Lake U-SQL SDK parancssori és programozási felületeken keresztül is támogatottak. További információ az [Azure Data Lake U-SQL SDK-ról.](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/)

### <a name="working-directories"></a>Munkakönyvtárak

U-SQL parancsfájl futtatásakor működő könyvtármappára van szükség a fordítási eredmények gyorsítótárazásához, a naplók futtatásához és egyéb funkciók végrehajtásához. Az Azure Data Lake Tools for Visual Studio, a munkakönyvtár az U-SQL projekt munkakönyvtára. A . `<U-SQL project root path>/bin/debug>` A munkakönyvtár minden alkalommal törlődik, amikor új futtatás indul.

## <a name="local-runs-in-microsoft-visual-studio"></a>Helyi futtatások a Microsoft Visual Studio alkalmazásban

Az Azure Data Lake Tools for Visual Studio beépített helyi futtatási motorral rendelkezik. A szerszámok helyi számítási fiókként felületezik a motort. U-SQL-parancsfájl helyi futtatásához válassza a **Helyi gép** vagy a **Helyi projekt** fiókot a parancsfájl szerkesztőmargó legördülő menüjében. Ezután válassza **a Küldés lehetőséget.**

![U-SQL-parancsfájl küldése helyi fiókba](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-submit-script-to-local-account.png) 
 
## <a name="local-runs-with-a-local-machine-account"></a>Helyi futtatások helyi számítógép-fiókkal

A **helyi számítógép fiók** egy megosztott helyi számítási fiók egyetlen helyi adatgyökér mappával, mint a helyi áruházi fiók. Alapértelmezés szerint az adatgyökérmappa a **\<C:\Users felhasználóneve>\AppData\Local\USQLDataRoot**. Ez is konfigurálható **eszközök** > **en Data Lake** > **beállítások és beállítások**.

![Helyi adatgyökérmappa konfigurálása](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-local-data-root.png)
  
Egy U-SQL-projekt szükséges a helyi futtatáshoz. Az U-SQL-projekt munkakönyvtára az U-SQL helyi futtatási munkakönyvtárához használatos. A fordítási eredmények, a futtatási naplók és a feladatfuttatásával kapcsolatos egyéb fájlok a helyi futtatás során a munkakönyvtár mappája alatt jönnek létre és tárolódnak. Minden alkalommal, amikor újrafuttatja a parancsfájlt, a munkakönyvtárban lévő összes fájl törlődik és újragenerálódik.

## <a name="local-runs-with-a-local-project-account"></a>Helyi futtatás helyi projektfiókkal

A **helyi projekt** fiók egy projekt-elkülönített helyi számítási fiók minden projekt egy elkülönített helyi adat gyökérmappát. Minden aktív U-SQL projekt, amely megnyílik a `(Local-project: <project name>)` Visual Studio Megoldáskezelőjében, rendelkezik megfelelő fiókkal. A fiókok a Visual Studio Kiszolgálókezelőjében és az U-SQL parancsfájlszerkesztő margóján is megjelennek.  

A **Helyi projekt** fiók tiszta és elszigetelt fejlesztői környezetet biztosít. A **helyi számítógép fiók** rendelkezik egy megosztott helyi adatgyökér mappával, amely az összes helyi feladat metaadatait, valamint bemeneti és kimeneti adatait tárolja. A **Helyi projekt** fiók azonban létrehoz egy ideiglenes helyi adatgyökér-mappát egy U-SQL projekt munkakönyvtára alatt minden alkalommal, amikor egy U-SQL parancsfájl fut. Ez az ideiglenes adatgyökér-mappa törlődik, amikor újraépítés vagy újrafuttatás történik. 

Az U-SQL-projekt egy projekthivatkozáson és tulajdonságon keresztül kezeli az elkülönített helyi futtatási környezetet. Az U-SQL parancsfájlok bemeneti adatforrásait konfigurálhatja a projektben és a hivatkozott adatbázis-környezetekben is.

### <a name="manage-the-input-data-source-for-a-local-project-account"></a>Helyi projektfiók bemeneti adatforrásának kezelése 

Az U-SQL projekt létrehoz egy helyi adatgyökérmappát, és beállítja a **helyi projektfiók** adatait. Az ideiglenes adatgyökér-mappát az U-SQL projekt munkakönyvtára alatt minden alkalommal megtisztítja és újra létrehozza, amikor újraépíti és helyi futtatás történik. Az U-SQL projekt által konfigurált összes adatforrást a helyi feladat futtatása előtt ebbe az ideiglenes helyi adatgyökérmappába másolja a rendszer. 

Beállíthatja az adatforrások gyökérmappáját. Kattintson a jobb gombbal **az U-SQL projekt** > **tulajdonságtesztjéhez.****Property** >  Ha egy **Helyi projekt** fiókon u-SQL parancsfájlt futtat, a **Teszt adatforrás** mappában lévő összes fájl és almappa az ideiglenes helyi adatgyökérmappába kerül. Az almappák alá tartozó fájlok is szerepelnek. A helyi feladat futtatása után a kimeneti eredmények a projekt munkakönyvtárának ideiglenes helyi adatgyökérmappájában is megtalálhatók. Ez a kimenet törlődik, és törlődik, amikor a projekt újraépül és törlődik. 

![Projekt tesztadatforrásának konfigurálása](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-a-referenced-database-environment-for-a-local-project-account"></a>Helyi projektfiók hivatkozott **adatbáziskörnyezetének** kezelése 

Ha egy U-SQL-lekérdezés u-SQL adatbázis-objektumokkal használ vagy lekérdezéseket használ, az U-SQL parancsfájl helyi futtatása előtt helyileg el kell készítenie az adatbázis-környezeteket. Helyi **projektfiók** esetén az U-SQL adatbázis-függőségeket U-SQL projekthivatkozások is kezelhetők. Az U-SQL adatbázis-projekthivatkozásokat hozzáadhatja az U-SQL projekthez. Mielőtt U-SQL-parancsfájlokat **futtatna** egy helyi projektfiókon, az összes hivatkozott adatbázis az ideiglenes helyi adatgyökérmappába kerül. És minden futtatáskor az ideiglenes adatgyökér mappa friss elkülönített környezetként törlődik.

Lásd ezt a kapcsolódó cikket:
* Megtudhatja, hogy miként kezelheti az U-SQL adatbázis-definíciókat és hivatkozásokat [az U-SQL adatbázis-projektekben.](data-lake-analytics-data-lake-tools-develop-usql-database.md)

## <a name="the-difference-between-local-machine-and-local-project-accounts"></a>A **helyi és** a **helyi projektfiókok** közötti különbség

A **helyi-gépfiók** szimulálja az Azure Data Lake Analytics-fiókot a felhasználók helyi gépein. Az Azure Data Lake Analytics-fiókkal ugyanazt a felhasználói élményt osztja meg. A **helyi projektfiók** felhasználóbarát helyi fejlesztési környezetet biztosít. Ez a környezet segít a felhasználóknak az adatbázis-hivatkozások és a bemeneti adatok központi telepítésében, mielőtt helyileg futtatnák a parancsfájlokat. A **helyi számítógép fiók** egy közös állandó környezetet biztosít, amely az összes projekten keresztül elérhető. A **helyi projekt** fiók minden projekthez elkülönített fejlesztői környezetet biztosít. Minden futtatáshoz frissül. A **Helyi projekt** fiók gyorsabb fejlesztési élményt nyújt az új módosítások gyors alkalmazásával.

A **helyi és** a **helyi projektfiókok** közötti további különbségek az alábbi táblázatban találhatók:

|Különbség szöge|Helyi-gép|Helyi projekt|
|----------------|---------------|---------------|
|Helyi hozzáférés|Minden projekt elérhető.|Csak a megfelelő projekt férhet hozzá ehhez a fiókhoz.|
|Helyi adatgyökérmappa|Egy állandó helyi mappa. Konfigurálva **eszközök** > **Data Lake** > **beállítások és beállítások**.|Az U-SQL projekt munkakönyvtára alatt minden helyi futtatáshoz létrehozott ideiglenes mappa. A mappa törlődik, amikor újraépíti vagy újrafutja.|
|U-SQL parancsfájl bemeneti adatai|Az állandó helyi adatgyökér-mappa relatív elérési útja.|Az **U-SQL projekttulajdonság** > **tesztadatforrása**. A program a helyi futtatás előtt az összes fájlt és almappát az ideiglenes adatgyökérmappába másolja.|
|U-SQL parancsfájl kimeneti adatai|Relatív elérési út az állandó helyi adatgyökér-mappa alatt.|Kimenet az ideiglenes adatgyökérmappába. Az eredmények törlődnek, amikor újraépíti vagy újrafutja.|
|Hivatkozott adatbázis telepítése|A hivatkozott adatbázisok nem települnek automatikusan, ha egy **helyi számítógép-fiókon** fut. Ugyanez vonatkozik az Azure Data Lake Analytics-fiókba való küldésre is.|A hivatkozott adatbázisok telepítése a helyi futtatás előtt automatikusan a **Helyi projekt** fiókba történik. Minden adatbázis-környezettörlődik, és újraüzembe helyezése, amikor egy újraépítés vagy újrafuttatás történik.|

## <a name="a-local-run-with-the-u-sql-sdk"></a>Helyi futtatás az U-SQL SDK-val

Futhat U-SQL-parancsfájlokat helyileg a Visual Studióban, és az Azure Data Lake U-SQL SDK használatával helyileg futtathatja az U-SQL-parancsfájlokat parancssori és programozási felületekkel. Ezeken a felületeken keresztül automatizálhatja az U-SQL helyi futtatásokat és teszteket.

További információ az [Azure Data Lake U-SQL SDK-ról.](data-lake-analytics-u-sql-sdk.md)

## <a name="next-steps"></a>További lépések

- [Ci/CD-folyamat beállítása az Azure Data Lake Analytics szolgáltatáshoz.](data-lake-analytics-cicd-overview.md)
- [Az Azure Data Lake Analytics-kód tesztelése.](data-lake-analytics-cicd-test.md)
