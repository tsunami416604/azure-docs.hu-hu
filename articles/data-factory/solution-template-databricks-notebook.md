---
title: Adatok átalakítása Databricks használata az Azure Data Factoryban |} A Microsoft Docs
description: Megtudhatja, hogyan megoldás sablon használata az adatok átalakításához egy Databricks-jegyzetfüzetet az Azure Data Factory használatával.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/10/2018
ms.author: abnarain
ms.reviewer: douglasl
ms.openlocfilehash: 54b440ee76fe36a83284b8ce769bb31012781a35
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58295759"
---
# <a name="transform-data-by-using-databricks-in-azure-data-factory"></a>Adatok átalakítása a Databricks az Azure Data Factoryban való használatával

Ebben az oktatóanyagban létrehozhat egy teljes körű folyamatot tartalmazó **keresési**, **másolási**, és **Databricks-Jegyzetfüzet** tevékenységek a Data Factoryban.

-   **Keresési** vagy GetMetadata tevékenység biztosítására szolgál, a forrás adatkészlet készen áll a alsóbb rétegbeli felhasználásra, mielőtt elindítaná a másolás és elemzési feladatot.

-   **Másolási tevékenység** átmásolja a forrásfájl és a fogadó Storage adatkészlet. A fogadó tárolási van csatlakoztatva, DBFS a Databricks-jegyzetfüzet, hogy az adatkészletet képes közvetlenül használni Spark.

-   **Databricks-jegyzetfüzetek tevékenységeinek** elindítja a Databricks-jegyzetfüzetnek alakítja át az adatkészlet, és hozzáadja azt feldolgozott mappa / SQL dw-ben.

Ez a sablon egyszerű megőrzéséhez a sablon nem hoz létre ütemezett eseményindítóként. Hozzáadhat, ha szükséges.

![1](media/solution-template-Databricks-notebook/Databricks-tutorial-image01.png)

## <a name="prerequisites"></a>Előfeltételek

1.  Hozzon létre egy **blob storage-fiók** és a egy tároló `sinkdata` , használható **fogadó**. Jegyezze a **tárfióknevet**, **Tárolónév**, és **hívóbetű**, mivel ezek később a sablonban hivatkozott.

2.  Ellenőrizze, hogy egy **Azure Databricks-munkaterület** , vagy hozzon létre egy újat.

1.  **A notebook importálása az ETL-**. Importálás az alábbi átalakítási jegyzetfüzetet a Databricks-munkaterülethez. (Nincs, de ne feledje, hogy később válassza az elérési ugyanazon a helyen, mint az alábbi is.) A notebook importálása a következő URL-címet az URL-cím mezőben írja be az URL-cím: `https://DataFactorylabstaging1.blob.core.windows.net/share/Transformations.html`. Válassza ki **importálás**.

    ![2](media/solution-template-Databricks-notebook/Databricks-tutorial-image02.png)

    ![3](media/solution-template-Databricks-notebook/Databricks-tutorial-image03.png)  

1.  Most frissítsük a **átalakítási** jegyzetfüzet készítése a a **tárolási kapcsolatadatok** (nevét és hívóbetűjét). Lépjen a **5 parancs** a fenti importált jegyzetfüzet, cserélje le az alábbi kódrészlet a kijelölt értékek lecserélése után. Győződjön meg, hogy ez a fiók korábban létrehozott ugyanazt a tárfiókot, és tartalmazza a `sinkdata` tároló.

    ```python
    # Supply storageName and accessKey values  
    storageName = "<storage name>"  
    accessKey = "<access key>"  

    try:  
      dbutils.fs.mount(  
        source = "wasbs://sinkdata\@"+storageName+".blob.core.windows.net/",  
        mount_point = "/mnt/Data Factorydata",  
        extra_configs = {"fs.azure.account.key."+storageName+".blob.core.windows.net": accessKey})  

    except Exception as e:  
      # The error message has a long stack track. This code tries to print just the relevant line indicating what failed.

    import re
    result = re.findall(r"\^\s\*Caused by:\s*\S+:\s\*(.*)\$", e.message, flags=re.MULTILINE)
    if result:
      print result[-1] \# Print only the relevant error message
    else:  
      print e \# Otherwise print the whole stack trace.  
    ```

1.  Hozzon létre egy **Databricks hozzáférési jogkivonat** adat-előállító Databricks eléréséhez. **A hozzáférési jogkivonatának mentéset** későbbi használatra létrehozásakor egy Databricks társított szolgáltatást, amely a következőhöz hasonló "dapi32db32cbb4w6eee18b7d87e45exxxxxx"

    ![4](media/solution-template-Databricks-notebook/Databricks-tutorial-image04.png)

    ![5](media/solution-template-Databricks-notebook/Databricks-tutorial-image05.png)

## <a name="create-linked-services-and-datasets"></a>Társított szolgáltatásokat és adatkészleteket létrehozása

1.  Hozzon létre új **társított szolgáltatásokat** a Data Factory felhasználói felületén *kapcsolatok társított szolgáltatások + új*

    1.  **Forrás** – forrás adatainak eléréséhez. Az ehhez a mintához a forrásfájlokat tartalmazó nyilvános blob-tárolót is használhatja.

        Válassza ki **Blob Storage**, használja az alábbi **SAS URI-t** szeretne csatlakozni a forrás tárolási (csak olvasható hozzáférést).

        `https://storagewithdata.blob.core.windows.net/?sv=2017-11-09&ss=b&srt=sco&sp=rl&se=2019-12-31T21:40:53Z&st=2018-10-24T13:40:53Z&spr=https&sig=K8nRio7c4xMLnUV0wWVAmqr5H4P3JDwBaG9HCevI7kU%3D`

        ![6](media/solution-template-Databricks-notebook/Databricks-tutorial-image06.png)

    1.  **Fogadó** – az adatok másolását.

        Válassza ki az Előfeltételek 1, a fogadó társított szolgáltatásban létrehozott egy tárfiókot.

        ![7](media/solution-template-Databricks-notebook/Databricks-tutorial-image07.png)

    1.  **Databricks** – a Databricks-fürtön való kapcsolódáshoz

        Hozzon létre egy Databricks társított szolgáltatást, az előfeltételként szükséges 2.c létrehozott hozzáférési kulccsal. Ha rendelkezik egy *interaktív fürt*, előfordulhat, hogy válassza ki, amely. (Ez a példa a *új feladatfürt* beállítás.)

        ![8](media/solution-template-Databricks-notebook/Databricks-tutorial-image08.png)

2.  Hozzon létre **adatkészletek**

    1.  Hozzon létre **"sourceAvailability_Dataset"** annak ellenőrzésére, ha a forrásadatok érhető el

    ![9](media/solution-template-Databricks-notebook/Databricks-tutorial-image09.png)

    1.  **Forrásadatkészlet –** céljából a forrás (bináris másolat segítségével)

    ![10](media/solution-template-Databricks-notebook/Databricks-tutorial-image10.png)

    1.  **Fogadó adatkészlet** – másolni a mobilkészülékét a lefolyóba / célhelye

        1.  Társított szolgáltatás – válassza ki a létrehozott 1.b sinkBlob_LS

        2.  Fájl elérési út – "sinkdata/staged_sink"

        ![11](media/solution-template-Databricks-notebook/Databricks-tutorial-image11.png)

## <a name="create-activities"></a>Tevékenységek létrehozása

1.  Keresési tevékenység létrehozása "**rendelkezésre állást jelző**" adatelemzésre egy adatforrás rendelkezésre állásának ellenőrzése (Lookup vagy GetMetadata is lehet használni). Válassza ki a létrehozott 2.a sourceAvailability_Dataset.

    ![12](media/solution-template-Databricks-notebook/Databricks-tutorial-image12.png)

1.  Hozzon létre egy másolási tevékenységgel "**fájl blob**" adatkészlet másolni a fogadó-forrásból. Ebben az esetben az adatok a bináris fájlt. Referencia az alábbi képernyőképek a másolási tevékenység a forrás- és fogadó konfigurációk esetén.

    ![13](media/solution-template-Databricks-notebook/Databricks-tutorial-image13.png)

    ![14](media/solution-template-Databricks-notebook/Databricks-tutorial-image14.png)

1.  Definiálása **folyamat-paraméterek**

    ![15](media/solution-template-Databricks-notebook/Databricks-tutorial-image15.png)

1.  Hozzon létre egy **Databricks tevékenység**

    Válassza ki az előző lépésben létrehozta a társított szolgáltatás.

    ![16](media/solution-template-Databricks-notebook/Databricks-tutorial-image16.png)

    Konfigurálja a **beállítások**. Hozzon létre **alap paraméterek** a képernyőképen látható módon, és hozza létre a Databricks-jegyzetfüzetnek a Data Factory átadandó paramétereket. Keresse meg és **kiválasztása** a **megfelelő jegyzetfüzet útvonalat** feltöltött **előfeltétel 2**.

    ![17](media/solution-template-Databricks-notebook/Databricks-tutorial-image17.png)

1.  **A folyamat futtatása**. Részletesebb Spark-naplók Databricks naplók mutató hivatkozást találhat meg.

    ![18](media/solution-template-Databricks-notebook/Databricks-tutorial-image18.png)

    Az adatfájl a storage explorer használatával is ellenőrizheti. (A Data Factory a folyamatfuttatások korrelálásához ebben a példában fűzi hozzá a folyamat futásának Azonosítóját a data factory-ből a kimeneti mappába. Ezzel a módszerrel követheti vissza minden egyes futtatásához keresztül létrehozott fájlokhoz.)

![19](media/solution-template-Databricks-notebook/Databricks-tutorial-image19.png)

## <a name="next-steps"></a>További lépések

- [Az Azure Data Factory bemutatása](introduction.md)
