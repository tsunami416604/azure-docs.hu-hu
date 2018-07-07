---
title: A helyi gépen futtassa az Azure Data Lake U-SQL-parancsfájl |} A Microsoft Docs
description: Útmutató U-SQL-feladatok futtatása a helyi gépen az Azure Data Lake Tools for Visual Studio használatával.
services: data-lake-analytics
documentationcenter: ''
author: yanancai
manager: ''
editor: ''
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/03/2018
ms.author: yanacai
ms.openlocfilehash: a7f43c7e17f36d9b4e0767744eee9604c2628ea8
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/06/2018
ms.locfileid: "37888966"
---
# <a name="run-u-sql-script-on-your-local-machine"></a>U-SQL-szkript futtatása helyi gépen

U-SQL parancsfájl fejlesztésekor szokás U-SQL parancsfájl helyi futtatását, a költségek és idő menti. Az Azure Data Lake Tools for Visual Studio támogatja a U-SQL-szkriptek futtatása helyi gépen. 

## <a name="basic-concepts-for-local-run"></a>Helyi Futtatás alapvető fogalmai

Alábbi diagram bemutatja, az összetevők a helyi Futtatás és hogyan ezeket az összetevőket leképezése a felhőben futtassa.

|Összetevő|Helyi Futtatás|Futtatási felhő|
|---------|---------|---------|
|Storage|Helyi adatok gyökérmappa|Alapértelmezett Azure Data Lake Store-fiók|
|Compute|U-SQL helyi futtatási motor|Az Azure Data Lake analitikai szolgáltatás|
|Végrehajtási környezet|A helyi számítógépen munkakönyvtár|Az Azure Data Lake Analytics-fürt|

További magyarázat a helyi Futtatás összetevők:

### <a name="local-data-root-folder"></a>Helyi adatok gyökérmappa

Helyi adatok legfelső szintű mappát a helyi számítási fiók "helyi tároló". Minden mappa a helyi gépen a helyi fájlrendszerben lehet egy helyi adatok legfelső szintű mappát. Ez megegyezik a Data Lake Analytics-fiók alapértelmezett Azure Data Lake Store-fiókjába. Váltás másik adatok legfelső szintű mappa van csakúgy, mint egy másik alapértelmezett store-fiók vált. 

Az adatok legfelső szintű mappa a következőkre használható:
- Store metaadatokat, például adatbázisok, táblák, táblázat értékű függvények és szerelvényeket.
- Keresse meg a bemeneti és kimeneti elérési utak relatív elérési utakat a U-SQL parancsfájl vannak meghatározva. Relatív elérési utak használata megkönnyíti az U-SQL-szkriptek üzembe Azure-ban.

### <a name="u-sql-local-run-engine"></a>U-SQL helyi futtatási motor

U-SQL helyi futtatási motor egy "helyi számítási fiók" az U-SQL-feladatok. Felhasználók U-SQL feladatok futtatásához helyi keresztül az Azure Data Lake Tools for Visual Studio. Azure Data Lake U-SQL SDK parancssori és programozási felületek segítségével a helyi végrehajtásra használata is támogatott. [További információ az Azure Data Lake U-SQL SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/).

### <a name="working-directory"></a>Munkakönyvtár

Amikor egy U-SQL-szkript futtatása directory munkamappában fordítási eredmények, a feladatvégrehajtási naplók és így tovább gyorsítótárazáshoz van szükség. Az Azure Data Lake Tools for Visual Studio, a munkakönyvtárban történik az U-SQL projekt működő könyvtárba (általában a csoportban található `<U-SQL project root path>/bin/debug>`). A munkakönyvtárban törlődnek, minden alkalommal, amikor a kiváltott új futtatni.

## <a name="local-run-in-visual-studio"></a>Helyi Futtatás a Visual Studióban

Az Azure Data Lake Tools for Visual Studio egy beépített helyi futtatáskor a motor rendelkezik, és feltárásával, mint a helyi számítási fiókhoz. Futtathat U-SQL parancsfájl helyi, válassza ki (helyi-gép) vagy (helyi-projekt) fiók szkript-szerkesztő margón legördülő, és kattintson a **küldés**.

![A Data Lake Tools a Visual Studio küldés parancsfájl helyi fiók](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-submit-script-to-local-account.png) 
 
## <a name="local-run-with-local-machine-account"></a>Helyi fiók (helyi-gép) futtatja

(Helyi-gép) egy egy közös helyi számítási fiók egy helyi adatok legfelső szintű mappát a helyi tároló fiókként. Az adatok legfelső szintű mappa a következő helyen található alapértelmezés szerint "C:\Users\<username > \AppData\Local\USQLDataRoot", akkor is konfigurálható **eszközök > Data Lake > lehetőségek és beállítások**.

![A Data Lake Tools for Visual Studio helyi adatok legfelső szintű konfigurálása](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-local-data-root.png)
  
A U-SQL projekt helyi Futtatás szükség. A U-SQL projekt munkakönyvtár U-SQL helyi végrehajtási munkakönyvtár szolgál. Fordítási eredmények feladatvégrehajtási naplók és egyéb feladat-végrehajtási kapcsolatos fájlok jönnek létre és helyi futtatás során munkamappában könyvtár alatt tárolja. Vegye figyelembe, hogy minden alkalommal újra futtatja a szkriptet, ezeket a fájlokat a munkakönyvtárba meg kell tisztítani és újbóli létrehozása.

## <a name="local-run-with-local-project-account"></a>Helyi futtatás (helyi-projekt) fiókkal

(Helyi-projekt) egy egy helyi számítási projekt logikailag fiók minden egyes elkülönített helyi adatok legfelső szintű mappát a projekthez. Minden aktív U-SQL projekt megnyitása a Megoldáskezelőben rendelkezik egy megfelelő `(Local-project: <project name>)` fiók listázva a Server Explorerben és a U-SQL parancsfájl szerkesztő margó is. 

A (helyi-projekt) fiók letisztult és elkülönített fejlesztési környezetet biztosít a fejlesztők számára. Eltérően (helyi-) számítógépfiók, amely a metaadatokat és az összes helyi feladat bemeneti és kimeneti adatokat tároló megosztott helyi adatok legfelső szintű mappát tartalmaz (helyi-projekt) fiókot hoz létre egy ideiglenes helyi adatok legfelső szintű mappát U-SQL projekt munkakönyvtár minden alkalommal egy U-SQL parancsfájl Futtassa a beolvasása. Ez ideiglenes adatokat a gyökérmappa lekérdezi tisztítani újraépítése, vagy futtassa újra az esetben. 

U-SQL projekt kezeléséhez az elkülönített helyi környezetben haladjon végig a projekt referencia- és tulajdonság jó élményt nyújt. Is konfigurálhatja a bemeneti adatforrásokat a U-SQL-parancsfájlok a projekt, valamint a hivatkozott adatbázis környezetekben.

### <a name="manage-input-data-source-for-local-project-account"></a>A bemeneti adatforrás (helyi-projekt) fiók kezelése

A U-SQL projekt gondoskodik a helyi adatok legfelső szintű mappa létrehozása és adatok beállítása (helyi-projekt) fiók regisztrálásához. Adatok ideiglenes gyökérmappát tisztítani és újra létre kell hozni a U-SQL projekt munkakönyvtár minden alkalommal, amikor rebuild és a helyi Futtatás történik. A U-SQL projekt által konfigurált összes adatforrás lesz másolva a ideiglenes helyi adatok legfelső szintű mappa helyi feladat végrehajtása előtt. 

Konfigurálhatja a gyökérmappában található azon keresztül az adatforrások **kattintson jobb gombbal a U-SQL Projekt > tulajdonság > tesztelése adatforrás**. Ha fut (helyi-projekt) fiók, fájlokat és almappákat (beleértve az almappákat fájlokat) a U-SQL parancsfájl **tesztelése adatforrás** mappát a helyi adatok legfelső szintű ideiglenes mappába másolja. Helyi feladat végrehajtása után kimeneti eredmények is található az ideiglenes helyi adatok legfelső szintű mappát a projekt munkakönyvtárban. Vegye figyelembe, hogy összes kimenetének törölve lesz, majd törölni a projekt beolvassa az újonnan létrehozott, és törölve. 

![A Data Lake Tools for Visual Studio projekt test-adatforrás konfigurálása](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-referenced-database-environment-for-local-project-account"></a>Hivatkozott adatbázis-környezet (Local-projekt) fiók kezelése 

Egy U-SQL lekérdezés vagy lekérdezéseket a U-SQL-adatbázis-objektumokat használ, ha gondoskodnia kell, az adatbázis-környezet készen áll helyileg a U-SQL parancsfájl futtatása előtt. A fiók (helyi-projekt) U-SQL projekt referenciáihoz U-SQL-adatbázis függőségei is kezelhető. U-SQL database projekt referenciáihoz a U-SQL-projekthez is hozzáadhat. Mielőtt futtatná az U-SQL-parancsfájlok (helyi-projekt) fiók, minden hivatkozott adatbázis telepített helyi ideiglenes adatok legfelső szintű mappához. És minden egyes futtatásához az ideiglenes adatok legfelső szintű mappát egy friss elkülönített környezet törölve.

Kapcsolódó cikkek:
* [Ismerje meg, hogyan kezelheti a U-SQL-adatbázis definíciója U-SQL-adatbázis projekten keresztül](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project)
* [Ismerje meg, hogyan kezelheti a U-SQL projekt U-SQL database-referencia](data-lake-analytics-data-lake-tools-develop-usql-database.md)

## <a name="difference-between-local-machine-and-local-project-account"></a>Különbség a (helyi-számítógép) és a (helyi-projekt) fiók

(Helyi-gép) fiók az a célja, hogy szimulálja egy Azure Data Lake Analytics-fiók a felhasználók helyi számítógépen. Az Azure Data Lake Analytics-fiók közös ugyanazt a felhasználói élményt. (Helyi-projekt) célja, hogy adjon meg egy felhasználóbarát megfelelő helyi fejlesztőkörnyezet, amellyel a felhasználók üzembe hivatkozás adatbázisok és a bemeneti adatok helyben a parancsfájl futtatása előtt. (Helyi-) számítógépfiók biztosít megosztott állandó környezetben, amely az összes projekt keresztül érhetők el. (Helyi-projekt) fiók minden projekt elkülönített fejlesztési környezetet biztosít, és az egyes futtatások frissül. Fent alapján (helyi-projekt) fiók egy gyorsabb fejlesztői felületet kínál úgy, hogy gyorsan alkalmazza a módosításokat.

További különbség a (helyi-gépen) található és a (helyi-projekt) fiók diagramon az alábbiak szerint:

|Különbség szög|(Helyi-gép)|(Helyi-projekt)|
|----------------|---------------|---------------|
|Helyi hozzáférési|Érhetik el az összes projekt|Csak a megfelelő projektet hozzáférhet ehhez a fiókhoz|
|Helyi adatok gyökérmappa|Egy állandó helyi mappába. Keresztül konfigurált **eszközök > a Data Lake > lehetőségek és beállítások**|Minden helyi futtatáskor munkakönyvtár U-SQL projekt keretében létrehozott egy ideiglenes mappába. A mappa újraépítése, vagy futtassa újra az esetben lekérdezi megtisztítva.|
|U-SQL parancsfájl bemeneti adatait|Az állandó helyi adatok legfelső szintű mappa alatti relatív elérési út|Beállítása keresztül **U-SQL projekt tulajdonság > tesztelése adatforrás**. Összes fájl, almappák másolja az ideiglenes adatok legfelső szintű mappát a helyi végrehajtása előtt|
|U-SQL parancsfájl kimeneti adatait|Az állandó helyi adatok legfelső szintű mappa alatti relatív elérési út|Használt kimeneti adattípus, az ideiglenes adatokat gyökérmappájába. Az eredmények törölve lettek, újraépítése vagy futtassa újra az esetben.|
|Hivatkozott adatbázis üzembe helyezése|Hivatkozott adatbázisok nem települnek automatikusan, amikor fut (helyi-gép) fiók ellen. Ugyanaz a elküldése az Azure Data Lake Analytics-fiókhoz.|A (helyi-projekt) fiók helyi végrehajtása előtt automatikusan hivatkozott adatbázisok vannak telepítve. Minden adatbázis-környezet megtisztítják és újratelepítése során történik, újraépítése vagy futtassa újra.|

## <a name="local-run-with-u-sql-sdk"></a>Helyi Futtatás U-SQL SDK-val

Emellett az U-SQL-parancsfájlok helyileg futtatja a Visual Studióban, használhatja az Azure Data Lake U-SQL SDK helyileg futtathat U-SQL-szkriptek parancssori és programozási felületek. Ezeken a felületeken keresztül automatizálhatja a U-SQL helyi futtatása és tesztelése.

[További információ az Azure Data Lake U-SQL SDK](data-lake-analytics-u-sql-sdk.md).

## <a name="next-steps"></a>További lépések

- [Azure Data Lake U-SQL SDK](data-lake-analytics-u-sql-sdk.md)
- [Az Azure Data Lake Analytics CI/CD-folyamat beállítása](data-lake-analytics-cicd-overview.md)
- [Használata U-SQL database-projekt U-SQL-adatbázis fejlesztéséhez](data-lake-analytics-data-lake-tools-develop-usql-database.md)
- [Az Azure Data Lake Analytics kód tesztelése](data-lake-analytics-cicd-test.md)
