<properties 
   pageTitle="Bevezetés a Data Lake Store használatába | Azure" 
   description="Data Lake Store-fiók létrehozása és alapszintű műveletek végrehajtása a Data Lake Store-ban a portál használatával" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/11/2016"
   ms.author="nitinme"/>

# Az Azure Data Lake Store használatának első lépései az Azure portál használatával

> [AZURE.SELECTOR]
- [Portál](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [REST API](data-lake-store-get-started-rest-api.md)
- [Azure CLI](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

Ismerje meg, hogyan hozhat létre Azure Data Lake Store-fiókot az Azure portál használatával, illetve hogyan végezhet el olyan alapvető műveleteket, mint például a mappák létrehozása, adatfájlok le- és feltöltése, a fiók törlése stb. További információk a Data Lake Store-ról: [Overview of Azure Data Lake Store](data-lake-store-overview.md) (Az Azure Data Lake Store áttekintése).

## Előfeltételek

Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

- **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

## <a name="signup"></a>Engedélyezze Azure-előfizetését a Data Lake Store nyilvános előzetes verziójához

Először igényelnie kell az Azure-előfizetésének engedélyezését a Data Lake Store nyilvános előzetes verziójához. Kövesse az alábbi lépéseket.

1. Jelentkezzen be az új [Azure portálra](https://portal.azure.com).
2. Kattintson a **NEW** (új) **Data + Storage** (Adatok és tárolás), majd az **Azure Data Lake Store** elemre.
3. A **New Data Lake Store** (Új Data Lake Store) panelen kattintson a **Sign up to preview** (Feliratkozás az előzetes verzióra) elemre. Olvassa el az információkat, majd kattintson az **OK** gombra. Amint megtörtént az előfizetés engedélyezése a Nyilvános előzetes verzióhoz, egy e-mailt fog kapni.

    ![Regisztráció a Nyilvános előzetes verzióra](./media/data-lake-store-get-started-portal/preview-signup.png "Create a new Azure Data Lake account")

## Gyorsan tanul videók segítségével?

Tekintse meg az alábbi videókat a Data Lake Store megismeréséhez.

* [Data Lake Store-fiók létrehozása](https://mix.office.com/watch/1k1cycy4l4gen)
* [Adatkezelés a Data Lake Store-ban az Adatkezelő használatával](https://mix.office.com/watch/icletrxrh6pc)

## Azure Data Lake Store-fiók létrehozása

1. Jelentkezzen be az új [Azure portálra](https://portal.azure.com).

2. Kattintson a **NEW** (új) **Data + Storage** (Adatok és tárolás), majd az **Azure Data Lake Store** elemre. Olvassa el az **Azure Data Lake Store** panelen látható információkat, majd kattintson a panel bal alsó sarkában található **Create** (Létrehozás) elemre.

3. A **New Data Lake Store** (Új Data Lake Store) panelen adja meg az értékeket az alábbi képernyőfelvételen látható módon:

    ![Új Azure Data Lake Store-fiók létrehozása](./media/data-lake-store-get-started-portal/ADL.Create.New.Account.png "Create a new Azure Data Lake account")

    - **Előfizetés**. Válassza ki az előfizetést, amelyhez létre kíván hozni egy új Data Lake Store-fiókot.
    - **Erőforráscsoport**. Válasszon ki egy meglévő erőforráscsoportot, vagy kattintson a **Create a resource group** (Erőforráscsoport létrehozása) elemre, ha szeretne létrehozni egyet. Az erőforráscsoport egy tároló, amely alkalmazásokhoz kapcsolódó erőforrásokat tárol. További információk: [Erőforráscsoportok az Azure-ban](resource-group-overview.md#resource-groups).
    - **Hely**: Válassza ki, hol szeretné létrehozni a Data Lake Store-fiókot.

4. Válassza a **Pin to Startboard** (Rögzítés a kezdőpulton) lehetőséget, ha szeretné, hogy a Data Lake Store-fiók a kezdőpultról is elérhető legyen.

5. Kattintson a ** Create** (Létrehozás) gombra. Ha úgy döntött, hogy rögzíti a fiókot a kezdőpulton, visszakerül a kezdőpultra, ahol láthatja a Data Lake Store-fiók üzembe helyezésének folyamatát. A Data Lake Store-fiók üzembe helyezése után megjelenik a fiókpanel.

6. Bontsa ki az **Essentials** (Alapvető adatok) legördülő menüt a Data Lake Store-fiók részleteinek (pl. az erőforráscsoport, amelynek része, a helye stb.) megtekintéséhez. Kattintson a**Quick Start** (Gyors üzembe helyezés) ikonra a Data Lake Store-hoz kapcsolódó egyéb forrásokra mutató hivatkozások megtekintéséhez.

    ![Saját Azure Data Lake Store-fiók](./media/data-lake-store-get-started-portal/ADL.Account.QuickStart.png "Your Azure Data Lake account")

## <a name="createfolder"></a>Mappák létrehozása az Azure Data Lake Store-fiókban

A Data Lake Store-fiókjában mappákat hozhat létre az adatok kezelésére és tárolására.

1. Nyissa meg az imént létrehozott Data Lake Store-fiókot. A bal oldali ablaktáblában kattintson a **Browse** (Tallózás), **Data Lake Store** elemre, majd a Data Lake Store panelen kattintson a fiók nevére, amelyben mappákat kíván létrehozni. Ha rögzítette a fiókot a kezdőpulton, kattintson a fiók csempéjére.

2. A Data Lake Store-fiók panelén kattintson a **Data Explorer** (Adatkezelő) elemre.

    ![Mappák létrehozása a Data Lake Store-fiókban](./media/data-lake-store-get-started-portal/ADL.Create.Folder.png "Create folders in Data Lake Store account")

3. A Data Lake Store-fiók panelén kattintson a **New Folder** (Új mappa) elemre, adjon nevet az új mappának, majd kattintson az **OK** gombra.
    
    ![Mappák létrehozása a Data Lake Store-fiókban](./media/data-lake-store-get-started-portal/ADL.Folder.Name.png "Create folders in Data Lake Store account")
    
    Az újonnan létrehozott mappa megjelenik a **Data Explorer** (Adatkezelő) panelen lesz listázva. Bármilyen szinten létrehozhat beágyazott mappákat.

    ![Mappák létrehozása a Data Lake-fiókban](./media/data-lake-store-get-started-portal/ADL.New.Directory.png "Create folders in Data Lake account")


## <a name="uploaddata"></a>Fájlok feltöltése az Azure Data Lake Store-fiókba

Adatait közvetlenül, gyökérszinten is feltöltheti az Azure Data Lake Store-fiókba, vagy a fiókon belül létrehozott mappába. Az alábbi képernyőfelvételen látható lépésekkel feltölthet egy fájlt egy almappába a **Data Explorer** (Adatkezelő) panelről. A képernyőfelvételen a fájl az útkövetésben jelölt almappába lesz feltöltve (vörös téglalappal kijelölve).

Ha feltölthető mintaadatokra van szüksége, használhatja az [Azure Data Lake Git-tárában](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData) található **Ambulance Data** mappát.

![Adatok feltöltése](./media/data-lake-store-get-started-portal/ADL.New.Upload.File.png "Upload data")


## <a name="properties"></a>A tárolt adatokra vonatkozó tulajdonságok és műveletek

Kattintson az újonnan hozzáadott fájlra a **Properties** (Tulajdonságok) panel megnyitásához. Ezen a panelen érhetők el a fájlhoz kapcsolódó tulajdonságok és a rajta a panelben elvégezhető műveletek. Másolhatja az Azure Data Lake Store-fiókban lévő fájl teljes elérési útját is, amely az alábbi képernyőfelvételen vörös téglalappal van kijelölve.

![Az adatok tulajdonságai](./media/data-lake-store-get-started-portal/ADL.File.Properties.png "Properties on the data")

* Kattintson a **Preview** (Előnézet) elemre a fájl előnézetének közvetlenül a böngészőből történő megtekintéséhez. Az előnézet formátumát is meghatározhatja. Kattintson a **Preview** (Előnézet), majd a **Format** (Formátum) elemre a **File Preview** (Fájl előnézete) panelen, és a **File Preview Format** (Fájl előnézetének formátuma) panelen adja meg az olyan beállításokat, mint a megjelenítendő sorok száma, a használni kívánt kódolás és elválasztók stb.

  ![A fájl előnézetének formátuma](./media/data-lake-store-get-started-portal/ADL.File.Preview.png "File preview format")

* Kattintson a **Download** (Letöltés) elemre a fájl letöltéséhez a számítógépére.

* Kattintson a **Rename file** (Fájl átnevezése) elemre a fájl átnevezéséhez.

* Kattintson a **Delete file** (Fájl törlése) elemre a fájl törléséhez.


## Az adatok védelme

Az Azure Active Directory és a hozzáférés-vezérlés (ACLs) segítségével gondoskodhat az Azure Data Lake Store-fiókban tárolt adatok védelméről. Az erre vonatkozó utasításokért lásd: [Az adatok védelme az Azure Data Lake Store-ban](data-lake-store-secure-data.md).


## Az Azure Data Lake Store-fiók törlése

Az Azure Data Lake Store-fiók törléséhez kattintson a **Delete** (Törlés) elemre a Data Lake Store panelen. A művelet megerősítéséhez a rendszer a törölni kívánt fiók nevének megadását kéri. Adja meg a fiók nevét, majd kattintson a **Delete** (Törlés) gombra.

![Data Lake-fiók törlése](./media/data-lake-store-get-started-portal/ADL.Delete.Account.png "Delete Data Lake account")


## További lépések

- [Secure data in Data Lake Store (Az adatok védelme a Data Lake Store-ban)](data-lake-store-secure-data.md)
- [Use Azure Data Lake Analytics with Data Lake Store (Az Azure Data Lake Analytics használata a Data Lake Store-ral)](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Use Azure HDInsight with Data Lake Store (Az Azure HDInsight használata a Data Lake Store-ral)](data-lake-store-hdinsight-hadoop-use-portal.md)



<!--HONumber=Jun16_HO2-->


