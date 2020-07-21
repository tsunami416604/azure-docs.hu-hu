---
title: Adatátalakítás Azure Data Factory felügyelt VNet-leképezési adatfolyammal
description: Ez az oktatóanyag részletes útmutatást nyújt a Azure Data Factory használatával történő adatátalakításhoz a leképezési adatfolyammal
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/19/2019
ms.openlocfilehash: 5515f6c4dfbc5d3c0e391373e763597d7fdc89ed
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531756"
---
# <a name="transform-data-securely-using-mapping-data-flows"></a>Az adatforgalom biztonságos átalakítása a leképezési adatfolyamatokkal

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ha még csak ismerkedik az Azure Data Factory használatával, olvassa el [az Azure Data Factory használatának első lépéseit](https://docs.microsoft.com/azure/data-factory/introduction) ismertető cikket.

Ebben az oktatóanyagban a Azure Data Factory felhasználói felületét (UX) fogja használni egy olyan folyamat létrehozásához, amely egy Azure Data Lake Storage (ADLS) Gen2-forrásból származó adatok egy ADLS Gen2 fogadóba való másolására és átalakítására használható **(mindkettő lehetővé teszi a hozzáférést csak a kiválasztott hálózatokhoz)** a [Azure Data Factory felügyelt Virtual Network](managed-virtual-network-private-endpoint.md)leképezési folyamatával. Az oktatóanyagban szereplő konfigurációs minta kibontható az Adatleképezési folyamat használatával történő adatátalakításkor.

Az oktatóanyag során a következő lépéseket hajtja végre:

> [!div class="checklist"]
>
> * Adat-előállító létrehozása
> * Folyamat létrehozása adatfolyam-tevékenységgel.
> * Hozzon létre egy leképezési adatfolyamot négy átalakítással.
> * A folyamat próbafuttatása
> * Adatfolyam-tevékenység figyelése

## <a name="prerequisites"></a>Előfeltételek
* **Azure-előfizetés**. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* **Azure Storage-fiók**. A ADLS-tárolók *forrásaként* és fogadó *adattárakként* használhatók. Ha még nem rendelkezik tárfiókkal, tekintse meg az [Azure Storage-fiók létrehozásának](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal) lépéseit ismertető cikket. **Győződjön meg arról, hogy a Storage-fiók csak a "kiválasztott hálózatokból" engedélyezi a hozzáférést.** 

Az oktatóanyagban átalakított fájl MoviesDB.csv, amely [itt](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv)található. A fájl GitHubról történő lekéréséhez másolja a tartalmat egy tetszőleges szövegszerkesztőbe, és mentse helyileg a. csv-fájlként. A fájlnak a Storage-fiókba való feltöltéséhez lásd: [Blobok feltöltése a Azure Portal](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). Ez a példa egy "Sample-adat" nevű tárolóra hivatkozik.

## <a name="create-a-data-factory"></a>Adat-előállító létrehozása

Ebben a lépésben létrehoz egy adatelőállítót, és megnyitja a Data Factory UX-t egy folyamat létrehozásához az adatelőállítóban.

1. Nyissa meg a **Microsoft Edge** vagy a **Google Chrome böngészőt**. Jelenleg Data Factory felhasználói felület csak a Microsoft Edge és a Google Chrome böngészőben támogatott.
2. A bal oldali menüben válassza az **erőforrás létrehozása**  >  **elemzési**  >  **Data Factory**elemet.
3. Az **Új adat-előállító** lap **Név** mezőjében adja meg az **ADFTutorialDataFactory** értéket.

   Az Azure-beli adatgyár nevének *globálisan egyedinek*kell lennie. Ha a név értékével kapcsolatos hibaüzenet kap, adjon meg másik nevet az adat-előállítóhoz. (például Sajátneveadftutorialdatafactory). A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.

4. Válassza ki azt az **Azure-előfizetést**, amelyben az adat-előállítót létre szeretné hozni.
5. **Erőforráscsoport:** hajtsa végre a következő lépések egyikét:

    a. Válassza a **meglévő használata**lehetőséget, majd válasszon ki egy meglévő erőforráscsoportot a legördülő listából.

    b. Válassza az **új létrehozása**lehetőséget, és adja meg az erőforráscsoport nevét. 
         
    Az erőforráscsoportokkal kapcsolatos információkért tekintse meg az [Erőforráscsoportok használata az Azure-erőforrások kezeléséhez](../azure-resource-manager/management/overview.md) ismertető cikket. 
6. A **Verzió** résznél válassza a **V2** értéket.
7. A **Hely** területen válassza ki az adat-előállító helyét. A legördülő listán csak a támogatott helyek jelennek meg. Az adattárak (például az Azure Storage és a SQL Database) és a számítási erőforrások (például az Azure HDInsight) más régiókban is használhatók.

8. Kattintson a **Létrehozás** gombra.

9. A létrehozás befejezése után megjelenik az értesítési központban megjelenő értesítés. Válassza az **Ugrás az erőforráshoz** lehetőséget, hogy megnyissa az adatfeldolgozó lapot.
10. A Data Factory felhasználói felületének külön lapon történő elindításához válassza a **Létrehozás és figyelés** csempét.

## <a name="create-an-azure-integration-runtime-in-adf-managed-virtual-network"></a>Azure Integration Runtime létrehozása az ADF által felügyelt Virtual Network
Ebben a lépésben létrehoz egy Azure Integration Runtime, és engedélyezi a felügyelt Virtual Network.

1. Az ADF-portálon nyissa meg a **felügyelet hubot** , és kattintson az **új** elemre új Azure Integration Runtime létrehozásához.
   ![Új Azure Integration Runtime létrehozása](./media/tutorial-copy-data-portal-private/create-new-azure-ir.png)
2. Válassza az **Azure** -Integration Runtime létrehozása lehetőséget.
   ![Új Azure Integration Runtime](./media/tutorial-copy-data-portal-private/azure-ir.png)
3. **Virtual Network**engedélyezése.
   ![Új Azure Integration Runtime](./media/tutorial-copy-data-portal-private/enable-managed-vnet.png)
4. Kattintson a **Létrehozás** gombra.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Folyamat létrehozása adatfolyam-tevékenységgel

Ebben a lépésben olyan folyamatot hoz létre, amely egy adatfolyam-tevékenységet tartalmaz.

1. Az **Első lépések** lapon válassza a **Folyamat létrehozása** lehetőséget.

   ![Folyamat létrehozása](./media/doc-common-process/get-started-page.png)

1. A folyamat **Tulajdonságok** ablaktábláján adja meg a **TransformMovies** nevet a folyamat **neveként** .
1. A gyári felső sávban csúsztassa az **adatfolyam hibakeresési** csúszkáját. A hibakeresési mód lehetővé teszi az átalakítási logika interaktív tesztelését egy élő Spark-fürtön. Az adatfolyam-fürtök 5-7 percet vesznek igénybe, és a felhasználóknak javasoljuk, hogy először bekapcsolják a hibakeresést, ha az adatforgalom fejlesztését tervezik. További információ: [hibakeresési mód](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-debug-mode).

    ![Adatfolyam-hibakeresés](media/tutorial-data-flow-private/dataflow-debug.png)
1. A **tevékenységek** ablaktáblán bontsa ki az **áthelyezés és átalakítás** egyezést. Húzza az **adatfolyam** tevékenységet a panelről a folyamat vászonra.

1. Az **adatfolyam hozzáadása** felugró ablakban válassza az **új adatfolyam létrehozása** lehetőséget, majd válassza **az adatforgalom leképezése**lehetőséget. Ha elkészült, kattintson **az OK gombra** .

    ![Leképezési adatfolyam](media/tutorial-data-flow-private/mapping-dataflow.png)

1. Nevezze el az adatfolyam **TransformMovies** a Tulajdonságok ablaktáblán.

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Átalakítási logika létrehozása az adatfolyam-vásznon

Miután létrehozta az adatfolyamatot, a rendszer automatikusan elküldi az adatfolyam-vásznon. Ebben a lépésben egy olyan adatfolyamatot fog létrehozni, amely a ADLS-tárolóban lévő moviesDB.csv viszi, és összesíti a vígjátékok 1910 és 2000 közötti átlagos minősítését. Ezt a fájlt ezután vissza kell írnia a ADLS-tárolóba.

### <a name="add-the-source-transformation"></a>A forrás-átalakítás hozzáadása

Ebben a lépésben a Azure Data Lake Storage Gen2t állítja be forrásként.

1. Az adatfolyam-vásznon adja hozzá a forrást a **forrás hozzáadása** mezőre kattintva.

1. Nevezze el a forrás **MoviesDB**. Új forrás adatkészlet létrehozásához kattintson az **új** elemre.

1. Válassza a **Azure Data Lake Storage Gen2**lehetőséget. Kattintson a Folytatás gombra.

1. Válassza a **DelimitedText**lehetőséget. Kattintson a Folytatás gombra.

1. Nevezze el az adatkészlet **MoviesDB**. A társított szolgáltatás legördülő menüben válassza az **új**lehetőséget.

1. A társított szolgáltatás létrehozása képernyőn nevezze el a ADLS Gen2 társított szolgáltatás **ADLSGen2** , és adja meg a hitelesítési módszert. Ezután adja meg a kapcsolatok hitelesítő adatait. Ebben az oktatóanyagban a fiók kulcsát használjuk a Storage-fiókhoz való kapcsolódáshoz. 

1. Győződjön meg arról, hogy az **interaktív szerzői műveletek**engedélyezve vannak. Előfordulhat, hogy az engedélyezése körülbelül 1 percet vesz igénybe.

    ![Interaktív szerzői műveletek](./media/tutorial-data-flow-private/interactive-authoring.png)

1. Válassza a **kapcsolat tesztelése**lehetőséget, mert a Storage-fiók nem engedélyezi a hozzáférést egy privát végpont létrehozása és jóváhagyása nélkül. A hibaüzenetben egy olyan hivatkozást kell látnia, amely létrehoz egy **privát végpontot** , amelyet követve felügyelt privát végpontokat hozhat létre. *Egy másik lehetőség, hogy közvetlenül a kezelés lapra lép, és az [ebben a szakaszban](#create-a-managed-private-endpoint) szereplő utasításokat követve felügyelt privát végpontot hoz létre*

1. Tartsa nyitva a párbeszédpanelt, majd lépjen a fent kiválasztott Storage-fiókhoz.

1. A privát hivatkozás jóváhagyásához kövesse az [ebben a szakaszban](#approval-of-a-private-link-in-storage-account) található utasításokat.

1. Lépjen vissza a párbeszédpanelre. Próbálja megismételni a **kapcsolódást** , és válassza a **Létrehozás** lehetőséget a társított szolgáltatás telepítéséhez.

1. Miután visszatért az adatkészlet-létrehozási képernyőre, adja meg, hogy hol található a fájl **elérési útja** mezőben. Ebben az oktatóanyagban a fájl moviesDB.csv található a tároló mintája – adathalmazban. Ahogy a fájl fejléceket tartalmaz, tekintse meg az **első sort fejlécként**. Válassza a **Kapcsolódás/áruház** lehetőséget, hogy a fejléc-sémát közvetlenül a tárolóban lévő fájlból importálja. Ha elkészült, kattintson az OK gombra.

    ![Forrás elérési útja](media/tutorial-data-flow-private/source-file-path.png)

1. Ha a hibakeresési fürt elindult, lépjen a forrás-átalakítás **adatelőnézet** lapjára, és kattintson a **frissítés** gombra az adatok pillanatképének beolvasásához. Az adatelőnézet használatával ellenőrizheti, hogy az átalakítás megfelelően van-e konfigurálva.

    ![Adatelőnézet](media/tutorial-data-flow-private/data-preview.png)

#### <a name="create-a-managed-private-endpoint"></a>Felügyelt privát végpont létrehozása

Ha a fenti kapcsolat tesztelésekor nem kattintott a hiperhivatkozásra, kövesse az alábbi elérési utat. Most létre kell hoznia egy felügyelt magánhálózati végpontot, amelyhez csatlakozni fog a fent létrehozott társított szolgáltatáshoz.

1. Lépjen a kezelés lapra.
> [!NOTE]
> Előfordulhat, hogy a kezelés lap nem érhető el az összes adatfeldolgozó példányhoz. Ha nem látja, továbbra is elérheti a privát végpontokat a "**Szerző**" lapon – > "**Connections**"--> "**Private Endpoint**"
1. Lépjen a felügyelt privát végpontok szakaszra.
1. Válassza az **+ új** lehetőséget a felügyelt privát végpontok alatt.

    ![Új felügyelt magánhálózati végpont](./media/tutorial-data-flow-private/new-managed-private-endpoint.png) 

1. Válassza ki a listából a Azure Data Lake Storage Gen2 csempét, és válassza a **Folytatás**lehetőséget.
1. Adja meg a fent létrehozott Storage-fiók nevét.
1. Kattintson a **Létrehozás** gombra.
1. Néhány másodperc várakozás után kell megjelennie, hogy a privát kapcsolat létrehozása jóváhagyást igényel.
1. Válassza ki a fent létrehozott privát végpontot. Megtekintheti a hivatkozásokat, amelyekkel jóváhagyhatja a magánhálózati végpontot a Storage-fiók szintjén.

    ![Privát végpont kezelése](./media/tutorial-data-flow-private/manage-private-endpoint.png) 

#### <a name="approval-of-a-private-link-in-storage-account"></a>Privát hivatkozás jóváhagyása a Storage-fiókban

1. A Storage-fiókban válassza a **privát végponti kapcsolatok** lehetőséget a beállítások szakaszban.

1. Jelölje be a fent létrehozott privát végpontot, és válassza a **jóváhagyás**lehetőséget.

    ![Privát végpont jóváhagyása](./media/tutorial-data-flow-private/approve-private-endpoint.png)

1. Adjon meg egy leírást, és kattintson az **Igen**gombra.
1. Lépjen vissza a Azure Data Factory **kezelés** lapján található **felügyelt privát végpontok** szakaszra.
1. Ahhoz, hogy a jóváhagyás megjelenjen a privát végponton, körülbelül 1 percnek kell lennie.

### <a name="add-the-filter-transformation"></a>A szűrő átalakításának hozzáadása

1. Az adatfolyam-vászonon a forrás csomópont mellett kattintson a plusz ikonra egy új átalakítás hozzáadásához. A hozzáadott első átalakítás egy **szűrő**.

    ![Szűrő hozzáadása](media/tutorial-data-flow-private/add-filter.png)
1. Nevezze el a szűrő átalakítási **FilterYears**. Kattintson a **szűrés** elem melletti kifejezés mezőre a Kifejezésszerkesztő megnyitásához. Itt adja meg a szűrési feltételt.

    ![Év szűrése](media/tutorial-data-flow-private/filter-years.png)
1. Az adatfolyam-kifejezés-szerkesztővel interaktív módon hozhat létre kifejezéseket különböző átalakításokban való használatra. A kifejezések tartalmazhatnak beépített függvényeket, a bemeneti sémából származó oszlopokat és a felhasználó által definiált paramétereket. A kifejezések létrehozásával kapcsolatos további információkért lásd: [adatáramlási kifejezés-szerkesztő](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-expression-builder).

    * Ebben az oktatóanyagban a műfaji komédia azon filmjeit szeretné szűrni, amelyek a 1910-es és a 2000-as évek közötti időszakban jöttek létre. Az év jelenleg karakterlánc, a függvény használatával át kell alakítani egész számra ```toInteger()``` . Használja a nagyobb vagy egyenlő értéket (>=), és kisebb vagy egyenlő, mint a (<=) operátorok az 1910 és a 200 – literális Year értékekkel való összehasonlításhoz. Egyesítse ezeket a kifejezéseket a és a (&&) operátorral együtt. A kifejezés a következőképpen érkezik:

        ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    * Ha szeretné megkeresni, hogy mely filmek a vígjátékok, a ```rlike()``` függvény használatával megtalálhatja a "komédia" mintát az oszlopok műfajában. Union The rlike kifejezés az év összehasonlításával a Get:

        ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    * Ha a hibakeresési fürt aktív, a **frissítés** gombra kattintva ellenőrizheti, hogy a kifejezés kimenete a használt bemenetekhez képest látható-e. A logikát az adatáramlás kifejezésének nyelve alapján több, mint egy megfelelő választ kaphat.

        ![Szűrő kifejezése](media/tutorial-data-flow-private/filter-expression.png)

    * Kattintson a **Mentés és Befejezés** gombra, ha elkészült a kifejezéssel.

1. Egy **Adatelőnézet** beolvasása annak ellenőrzéséhez, hogy a szűrő megfelelően működik-e.

    ![Az adatelőnézet szűrése](media/tutorial-data-flow-private/filter-data.png)

### <a name="add-the-aggregate-transformation"></a>Az összesített átalakítás hozzáadása

1. A hozzáadni kívánt következő átalakítás a **séma-módosító**alatt létrehozott **összesített** transzformáció.

    ![Összesítés hozzáadása](media/tutorial-data-flow-private/add-aggregate.png)
1. Nevezze el az összesített átalakítási **AggregateComedyRatings**. A **Csoportosítás** lapon válassza az **év** lehetőséget a legördülő listából, hogy csoportosítsa az összesítéseket a film által kiváltott év alapján.

    ![Összesítő csoport](media/tutorial-data-flow-private/group-by-year.png)
1. Nyissa meg az **összesítések** lapot. A bal oldali szövegmezőben nevezze el az összesítő oszlop **AverageComedyRating**. Kattintson a jobb oldali kifejezés mezőre az összesítő kifejezés megadásához a Expression Builder használatával.

    ![Összesítő oszlop neve](media/tutorial-data-flow-private/name-column.png)
1. Az oszlop- **minősítés**átlagának lekéréséhez használja az ```avg()``` összesítő függvényt. Mivel a **minősítés** egy karakterlánc ```avg()``` , és egy numerikus bemenetet vesz igénybe, az értéket egy számra kell konvertálnia a ```toInteger()``` függvényen keresztül. A kifejezés a következőképpen néz ki:

    ```avg(toInteger(Rating))```

    Ha elkészült **, kattintson a Mentés és Befejezés** gombra.

    ![Összesítés mentése](media/tutorial-data-flow-private/save-aggregate.png)
1. Az átalakítás kimenetének megtekintéséhez nyissa meg az **adatelőnézet** lapot. Figyelje meg, hogy csak két oszlop van, az **év** és a **AverageComedyRating**.

### <a name="add-the-sink-transformation"></a>A fogadó átalakításának hozzáadása

1. Ezután hozzá kíván **adni egy** fogadó átalakítást a **célhely**területen.

    ![Fogadó hozzáadása](media/tutorial-data-flow-private/add-sink.png)
1. Nevezze el a **Sink**fogadó fogadót. A fogadó adatkészlet létrehozásához kattintson az **új** elemre.

    ![Fogadó létrehozása](media/tutorial-data-flow-private/create-sink.png)
1. Az új adatkészlet lapon válassza a **Azure Data Lake Storage Gen2**lehetőséget. Kattintson a Folytatás gombra.

1. A formátum kiválasztása lapon válassza a **DelimitedText**lehetőséget. Kattintson a Folytatás gombra.

1. Nevezze el a fogadó adatkészletet **MoviesSink**. A társított szolgáltatás esetében válassza ugyanazt a ADLSGen2 társított szolgáltatást, amelyet a forrás-átalakításhoz hozott létre. Adja meg azt a kimeneti mappát, ahová az adatokat írni kívánja. Ebben az oktatóanyagban a "kimenet" mappába írunk a "Sample-recontainer" tárolóban. A mappának nem kell előre megadnia, és dinamikusan létre lehet hozni. Állítsa az **első sort fejlécként** True (igaz) értékre, és válassza a **nincs lehetőséget** a **séma importálása**lehetőségnél. Kattintson az OK gombra.

    ![Fogadó útvonala](media/tutorial-data-flow-private/sink-file-path.png)

Most, hogy befejezte az adatfolyam felépítése. Készen áll arra, hogy a folyamaton fusson.

## <a name="running-and-monitoring-the-data-flow"></a>Az adatfolyam futtatása és figyelése

A folyamat a közzététel előtt hibakeresést végezhet. Ebben a lépésben az adatfolyam-folyamat hibakeresési futtatását fogja elindítani. Míg az adatelőnézet nem ír adatírást, a hibakeresési kísérlet az adatait a fogadó célhelyére fogja írni.

1. Nyissa meg a folyamat vászonját. Hibakeresési Futtatás indításához kattintson a **hibakeresés** gombra.

1. Az adatfolyam-tevékenységek feldolgozási folyamata az aktív hibakeresési fürtöt használja, de még legalább egy percet is igénybe kell vennie az inicializáláshoz. Az előrehaladást a **kimenet** lapon követheti nyomon. Ha a Futtatás sikeres, kattintson a szemüvegek ikonra a Futtatás részleteihez.

1. A Részletek lapon megtekintheti az egyes átalakítási lépésekben eltöltött sorok és időpontok számát.

    ![Figyelési Futtatás](media/tutorial-data-flow-private/run-details.png)
1. A transzformációra kattintva részletes információkat kaphat az oszlopokról és az adatok particionálásáról.

Ha ezt az oktatóanyagot helyesen követte, a fogadó mappájába írt 83 és 2 oszlopot kell írnia. A blob Storage ellenőrzésével ellenőrizheti, hogy helyesek-e az adatok.

## <a name="summary"></a>Összegzés

Ebben az oktatóanyagban a Azure Data Factory felhasználói felületét (UX) fogja használni egy olyan folyamat létrehozásához, amely egy Azure Data Lake Storage (ADLS) Gen2-forrásból származó adatok egy ADLS Gen2 fogadóba való másolására és átalakítására szolgál **(mindkettő csak a kiválasztott hálózatokhoz való hozzáférést engedélyezi)** [Azure Data Factory felügyelt Virtual Network](managed-virtual-network-private-endpoint.md).
