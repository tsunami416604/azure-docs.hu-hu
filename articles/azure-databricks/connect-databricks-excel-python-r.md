---
title: Csatlakozás Azure Databricks Excel, Python vagy R |} Microsoft Docs
description: 'Útmutató: Azure Databricks kapcsolódni az Excel, Python vagy R. Simba illesztőprogramot használja'
services: azure-databricks
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: nitinme
ms.openlocfilehash: 333ff3ac3de053eae604ffeab600df7d35874f69
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2018
ms.locfileid: "37085232"
---
# <a name="connect-to-azure-databricks-from-excel-python-or-r"></a>Csatlakozás Azure Databricks Excel, Python vagy R

Ebből a cikkből megismerheti a Databricks ODBC-illesztőprogram Azure Databricks kapcsolódni a Microsoft Excel, a Python vagy az R nyelv használatával. Miután a kapcsolat, az Excel, Python vagy R ügyfelek Azure Databricks adatainak végezheti el. Az ügyfelek segítségével az adatok további elemzéséhez. 

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure Databricks munkaterülettel, egy Spark-fürt és a fürthöz tartozó mintaadatok kell rendelkeznie. Ha még nem rendelkezik az Előfeltételek, hajtsa végre: a gyors üzembe helyezés [készítsen másolatot a Spark az Azure portál használatával Azure Databricks](quickstart-create-databricks-workspace-portal.md).

* Töltse le a Databricks ODBC illesztőprogram [Databricks illesztőprogram letöltési oldala](https://databricks.com/spark/odbc-driver-download). Attól függően, hogy az alkalmazás 32 bites vagy 64-bit-es verziójának telepítése ahonnan szeretné Azure Databricks való kapcsolódáshoz. Például az Excelből csatlakozni az illesztőprogram 32-bit-es verziójának telepítéséhez. Csatlakozhat a R és Python, telepítse a 64 bites verzióját az illesztőprogramot.

* Állítsa be a Databricks személyes hozzáférési jogkivonat. Útmutatásért lásd: [felügyeleti Token](https://docs.azuredatabricks.net/api/latest/authentication.html#token-management).

## <a name="set-up-a-dsn"></a>Állítsa be a DSN-NEVET

Egy adatforrás neve (DSN) adott adatforrásra vonatkozó információkat tartalmazza. Az ODBC-illesztőprogram kell ehhez a DSN-adatforráshoz való kapcsolódáshoz. Ebben a szakaszban beállítása egy DSN-NEVET, például a Microsoft Excel, Python vagy R. ügyfelek az Azure Databricks csatlakozni használható a Databricks ODBC-illesztőprogram

1. Az Azure Databricks munkaterületről keresse meg a Databricks fürt.

    ![Nyissa meg Databricks fürt](./media/connect-databricks-excel-python-r/open-databricks-cluster.png "nyitott Databricks fürt")

2. Az a **konfigurációs** lapra, majd a **JDBC-/ ODBC** lapra, és másolja a **kiszolgáló állomásneve** és **HTTP-útvonal**. Ezeket az értékeket a cikkben ismertetett befejezéséhez van szüksége.

    ![Első Databricks konfigurációs](./media/connect-databricks-excel-python-r/get-databricks-jdbc-configuration.png "beolvasása Databricks konfiguráció")

3. A számítógépen indítása **ODBC adatforrások** alkalmazás (32 bites vagy 64 bites) az alkalmazástól függően. Csatlakozhat a Excel, a 32 bites verzióját használja. Csatlakozhat a R és Python, 64 bites verzióját használja.

    ![Indítsa el a ODBC](./media/connect-databricks-excel-python-r/launch-odbc-app.png "ODBC-alkalmazás indítása")

4. Az a **felhasználói DSN** lapra, majd **Hozzáadás**. Az a **új adatforrás létrehozása** párbeszédpanelen jelölje ki a **Simba Spark ODBC-illesztőprogram**, és kattintson a **Befejezés**.

    ![Indítsa el a ODBC](./media/connect-databricks-excel-python-r/add-new-user-dsn.png "ODBC-alkalmazás indítása")

5. Az a **Simba Spark ODBC-illesztőprogram** párbeszédpanelen adja meg a következő értékeket:

    ![Konfigurálja a DSN](./media/connect-databricks-excel-python-r/odbc-dsn-setup.png "DSN konfigurálása")

    A következő táblázat tájékoztatást ad a megadott értékek a párbeszédpanelen.
    
    |Mező  | Érték  |
    |---------|---------|
    |**Adatforrás neve**     | Adja meg az adatforrás nevét.        |
    |**Host(s)**     | Adja meg az értéket, amelyet másolt a Databricks munkaterületről *kiszolgáló állomásneve*.        |
    |**Port**     | Adja meg *443-as*.        |
    |**Hitelesítési** > **mechanizmus**     | Válassza ki *felhasználónevet és jelszót*.        |
    |**Felhasználónév**     | Adja meg *token*.        |
    |**Jelszó**     | Adja meg a Databricks munkaterületről másolt jogkivonat értéket. |
    
    Hajtsa végre az alábbi kiegészítő lépéseket a DSN-beállítások párbeszédpanel.
    
    * Kattintson a **HTTP-beállítások**. A megnyíló párbeszédpanelen illessze be a következő *HTTP-útvonal* Databricks munkaterületről másolt. Kattintson az **OK** gombra.
    * Kattintson a **SSL-beállítások**. A megnyíló párbeszédpanelen válassza ki a **SSL engedélyezése** jelölőnégyzetet. Kattintson az **OK** gombra.
    * Kattintson a **tesztelése** Azure Databricks a kapcsolat ellenőrzéséhez. Kattintson a **OK** a konfiguráció mentéséhez.
    * Az a **ODBC-adatforrás felügyelő** párbeszédpanel, kattintson a **OK**.

Most már rendelkezik a DSN beállítása. A következő szakaszokban lévő Azure Databricks csatlakoztatja a Excel, Python vagy R. használhatja a DSN-NEVET

## <a name="connect-from-microsoft-excel"></a>Kapcsolódás Microsoft Excel

Ez a szakasz azt olvasnak be adatokat Azure Databricks Microsoft Excel programba a korábban létrehozott DSN. Mielőtt elkezdené, győződjön meg arról, hogy a számítógépen telepített Microsoft Excel. Használhatja az Excel próbaverziójának [Microsoft Excel próba hivatkozás](https://products.office.com/excel).

1. Nyissa meg a munkafüzetet a Microsoft Excel. Az a **adatok** menüszalag, kattintson a **adatok beolvasása**. Kattintson a **egyéb forrásokból származó** majd **az ODBC**.

    ![Indítsa el az Excelből ODBC](./media/connect-databricks-excel-python-r/launch-odbc-from-excel.png "indítsa el az Excelből ODBC")

2. Az a **az ODBC** párbeszédpanel. Válassza ki a korábban létrehozott DSN-NEVET, majd kattintson **OK**.

    ![Válassza ki a DSN](./media/connect-databricks-excel-python-r/excel-select-dsn.png "DSN kiválasztása")

3. Ha a hitelesítő adatokat kéri, a felhasználónevet adja meg **token**. A jelszó adja meg a token értékét, amely a Databricks munkaterület lekért.

    ![Hitelesítő adatok megadása Databricks](./media/connect-databricks-excel-python-r/excel-databricks-token.png "DSN kiválasztása")

4. A Navigátor ablakban válassza ki a tábla a Databricks betöltése az Excel programba, és kattintson a kívánt **betöltése**. 

    ![A dta betöltése az Excel](./media/connect-databricks-excel-python-r/excel-load-data.png "terhelés dta Excelbe")

Miután az adatok az Excel-munkafüzetet, rajta analitikai műveleteket végezhet.

## <a name="connect-from-r"></a>Az R csatlakozás

> [!NOTE]
> Ebben a szakaszban egy Azure Databricks az asztalon futó R Studio ügyfél integrációjával kapcsolatos információkat biztosít. R Studio használatáról az Azure Databricks fürtön maga útmutatásért lásd: [R Studiót a Azure Databricks](https://docs.azuredatabricks.net/spark/latest/sparkr/rstudio.html).

Ebben a szakaszban használhatja az R nyelv IDE referenciaadatok Azure Databricks érhető el. Mielőtt elkezdené, a következő, a számítógépen telepítve kell rendelkeznie.

* Az R nyelv egy IDE. Ez a cikk Rstudióból asztali verzióját használja. A későbbiekben telepítheti az [R Studio letöltési](https://www.rstudio.com/products/rstudio/download/).
* Rstudióból asztal a IDE használja, ha is telepítse a Microsoft R Client alkalmazást [ http://aka.ms/rclient/ ](http://aka.ms/rclient/). 

Nyissa meg az Rstudióból, és hajtsa végre a következő lépéseket:

- Hivatkozás a `RODBC` csomag. Ez lehetővé teszi, hogy a korábban létrehozott DSN Azure Databricks csatlakozni.
- A kapcsolatot a DSN.
- SQL-lekérdezés futtatható Azure Databricks lévő adatokat. A következő kódrészletet a *radio_sample_data* egy táblázatot, amely Azure Databricks már létezik.
- Bizonyos műveleteket a lekérdezés kimenetét ellenőrzése. 

A következő kódrészletet az alábbi feladatokat hajtja végre:

    # reference the 'RODBC' package
    require(RODBC)
    
    # establish a connection using the DSN you created earlier
    conn <- odbcConnect("<ENTER DSN NAME HERE>")
    
    # run a SQL query using the connection you created
    res <- sqlQuery(conn, "SELECT * FROM radio_sample_data")
    
    # print out the column names in the query output
    names(res) 
        
    # print out the number of rows in the query output
    nrow (res)

## <a name="connect-from-python"></a>Csatlakoztatja a Python

Ebben a szakaszban egy Python IDE a referenciaadatok Azure Databricks érhető el (például ÜRESJÁRATBAN) használja. Mielőtt elkezdené, fejezze be a következő előfeltételek teljesülését:

* Telepítse a Python [Itt](https://www.python.org/downloads/). Python a hivatkozás is telepíti inaktív.

* Egy parancs parancssori futtatásával a számítógépen, telepítse a `pyodbc` csomag. Futtassa az alábbi parancsot:

      pip install pyodbc

Nyissa meg az ÜRESJÁRATI, és hajtsa végre a következő lépéseket:

- Importálás a `pyodbc` csomag. Ez lehetővé teszi, hogy a korábban létrehozott DSN Azure Databricks csatlakozni.
- A korábban létrehozott DSN kapcsolatot létesíteni.
-  SQL-lekérdezés futtatható a kapcsolat használatával létrehozott. A következő kódrészletet a *radio_sample_data* egy táblázatot, amely Azure Databricks már létezik.
- A lekérdezés kimenetét ellenőrzése meg műveleteket elvégezni.

A következő kódrészletet az alábbi feladatokat hajtja végre:

```python
# import the `pyodbc` package:
import pyodbc

# establish a connection using the DSN you created earlier
conn = pyodbc.connect("DSN=<ENTER DSN NAME HERE>", autocommit = True)

# run a SQL query using the connection you created
cursor = conn.cursor()
cursor.execute("SELECT * FROM radio_sample_data")

# print the rows retrieved by the query.
for row in cursor.fetchall():
    print(row)

```

## <a name="next-steps"></a>További lépések

* Importálhat adatokat az Azure Databricks erőforrásait, lásd: [Azure Databricks adatforrása](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html#)


