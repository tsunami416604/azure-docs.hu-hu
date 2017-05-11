---
title: "A Data Lake Store használatának első lépései az Azure Portalon | Microsoft Docs"
description: "Data Lake Store-fiók létrehozása és alapszintű műveletek végrehajtása a Data Lake Store-ban az Azure Portallal"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: fea324d0-ad1a-4150-81f0-8682ddb4591c
ms.service: data-lake-store
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/06/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: 988e7fe2ae9f837b661b0c11cf30a90644085e16
ms.openlocfilehash: efb9e68256765873eb2776637d1a9469af6e46d6
ms.contentlocale: hu-hu
ms.lasthandoff: 04/06/2017


---
# <a name="get-started-with-azure-data-lake-store-using-the-azure-portal"></a>Az Azure Data Lake Store használatának első lépései az Azure Portal használatával
> [!div class="op_single_selector"]
> * [Portál](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Azure CLI](data-lake-store-get-started-cli.md)
> * [Azure CLI 2.0](data-lake-store-get-started-cli-2.0.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
> 

Ismerje meg, hogyan hozhat létre Azure Data Lake Store-fiókot az Azure Portal használatával, illetve hogyan végezhet el olyan alapvető műveleteket, mint például a mappák létrehozása, adatfájlok le- és feltöltése, a fiók törlése stb. További információk a Data Lake Store-ról: [Overview of Azure Data Lake Store](data-lake-store-overview.md) (Az Azure Data Lake Store áttekintése).

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

## <a name="do-you-learn-faster-with-videos"></a>Gyorsabban tanul videók segítségével?
Tekintse meg az alábbi videókat a Data Lake Store megismeréséhez.

* [Data Lake Store-fiók létrehozása](https://mix.office.com/watch/1k1cycy4l4gen)
* [Adatkezelés a Data Lake Store-ban az Adatkezelő használatával](https://mix.office.com/watch/icletrxrh6pc)

## <a name="create-an-azure-data-lake-store-account"></a>Azure Data Lake Store-fiók létrehozása
1. Jelentkezzen be az új [Azure Portalra](https://portal.azure.com).
2. Kattintson a **NEW** (új) **Data + Storage** (Adatok és tárolás), majd az **Azure Data Lake Store** elemre. Olvassa el az **Azure Data Lake Store** panelen látható információkat, majd kattintson a panel bal alsó sarkában található **Create** (Létrehozás) elemre.
3. A **New Data Lake Store** (Új Data Lake Store) panelen adja meg az értékeket az alábbi képernyőfelvételen látható módon:
   
    ![Új Azure Data Lake Store-fiók létrehozása](./media/data-lake-store-get-started-portal/ADL.Create.New.Account.png "Új Azure Data Lake Store-fiók létrehozása")
   
   * **Név**. Adja meg a Data Lake Store-fiók egyedi nevét.
   * **Előfizetés**. Válassza ki az előfizetést, amelyhez létre kíván hozni egy új Data Lake Store-fiókot.
   * **Erőforráscsoport**. Válasszon ki egy meglévő erőforráscsoportot, vagy válassza a **Create new** (Új létrehozása) elemre, ha szeretne létrehozni egyet. Az erőforráscsoport egy tároló, amely alkalmazásokhoz kapcsolódó erőforrásokat tárol. További információk: [Erőforráscsoportok az Azure-ban](../azure-resource-manager/resource-group-overview.md#resource-groups).
   * **Hely**: Válassza ki, hol szeretné létrehozni a Data Lake Store-fiókot.
   * **Titkosítási beállítások**. Kiválaszthatja, hogy szeretné-e titkosítani a Data Lake Store-fiókot. Ha a titkosítást választja, azt is megadhatja, hogyan kívánja kezelni a fiókban lévő adatok titkosításához használni kívánt fő titkosítási kulcsot.
     
     * (Választható) Válassza **A titkosítás letiltása** elemet a legördülő listából, ha nem szeretne titkosítást.
     * (Alapértelmezett) Válassza az **Azure Data Lake által kezelt kulcsok használata** elemet, ha azt szeretné, hogy az Azure Data Lake Store kezelje a titkosítási kulcsokat.
       
         ![Data Lake Store-titkosítás](./media/data-lake-store-get-started-portal/adls-encryption-1.png "Data Lake Store-titkosítás")
     * (Választható) Válassza az **Azure Key Vault-kulcsok kiválasztása** elemet, ha az Azure Key Vaultban lévő saját kulcsokat szeretne használni. Ezzel a lehetőséggel Key Vault-fiókot és -kulcsokat is létrehozhat, ha még nem rendelkezik ezekkel.
       
         ![Data Lake Store-titkosítás](./media/data-lake-store-get-started-portal/adls-encryption-2.png "Data Lake Store-titkosítás")
       
       Kattintson az **OK** gombra a **Titkosítási beállítások** panelen.
       
       > [!NOTE]
       > Ha az Azure Key Vaultban lévő kulcsokkal konfigurálja a titkosítást a Data Lake Store-fiókhoz, engedélyeket kell rendelnie az Azure Data Lake Store-fiókhoz az Azure Key Vault elérése érdekében. Ennek módjáról útmutatóért lásd: [Engedélyek hozzárendelése az Azure Key Vaulthoz](#assign-permissions-to-the-azure-key-vault)
       > 
       > 
4. Kattintson a **Létrehozás** gombra. Ha úgy döntött, hogy rögzíti a fiókot az irányítópulton, visszakerül az irányítópultra, ahol láthatja a Data Lake Store-fiók üzembe helyezésének folyamatát. A Data Lake Store-fiók üzembe helyezése után megjelenik a fiókpanel.

## <a name="assign-permissions-to-the-azure-key-vault"></a>Engedélyek hozzárendelése az Azure Key Vaulthoz
Ha az Azure Key Vaultban lévő kulcsokkal konfigurálta a titkosítást a Data Lake Store-fiókban, konfigurálnia kell a Data Lake Store-fiók és az Azure Key Vault-fiók közötti hozzáférést. Ehhez kövesse az alábbi lépéseket.

1. Ha az Azure Key Vault kulcsait használta, a Data Lake Store panele felül egy figyelmeztetést jelenít meg. Kattintson a figyelmeztetésre a **Key Vault-engedélyek konfigurálása** panel megnyitásához.
   
    ![Data Lake Store-titkosítás](./media/data-lake-store-get-started-portal/adls-encryption-3.png "Data Lake Store-titkosítás")
2. A panelen két lehetőség szerepel a hozzáférés konfigurálásához.
   
   * Az első lehetőségnél kattintson az **Engedélyezés** gombra a hozzáférés konfigurálásához. Az első lehetőség csak akkor engedélyezett, ha a Data Lake Store-t létrehozó felhasználó egyben az Azure Key Vault rendszergazdája is.
   * A másik lehetőség a panelen megjelenő PowerShell parancsmag futtatása. Az Azure Key Vault tulajdonosának kell lennie, vagy képesnek kell lennie engedélyeket hozzárendelni az Azure Key Vaulton. A parancsmag futtatása után térjen vissza a panelre, és kattintson az **Engedélyezés** gombra a hozzáférés konfigurálásához.

## <a name="createfolder"></a>Mappák létrehozása az Azure Data Lake Store-fiókban
A Data Lake Store-fiókjában mappákat hozhat létre az adatok kezelésére és tárolására.

1. Nyissa meg az imént létrehozott Data Lake Store-fiókot. A bal oldali ablaktáblában kattintson a **Browse** (Tallózás), **Data Lake Store** elemre, majd a Data Lake Store panelen kattintson a fiók nevére, amelyben mappákat kíván létrehozni. Ha rögzítette a fiókot a kezdőpulton, kattintson a fiók csempéjére.
2. A Data Lake Store-fiók panelén kattintson a **Data Explorer** (Adatkezelő) elemre.
   
    ![Mappák létrehozása a Data Lake Store-fiókban](./media/data-lake-store-get-started-portal/ADL.Create.Folder.png "Mappák létrehozása a Data Lake Store-fiókban")
3. A Data Lake Store-fiók panelén kattintson a **New Folder** (Új mappa) elemre, adjon nevet az új mappának, majd kattintson az **OK** gombra.
   
    ![Mappák létrehozása a Data Lake Store-fiókban](./media/data-lake-store-get-started-portal/ADL.Folder.Name.png "Mappák létrehozása a Data Lake Store-fiókban")
   
    Az újonnan létrehozott mappa megjelenik a **Data Explorer** (Adatkezelő) panelen lesz listázva. Bármilyen szinten létrehozhat beágyazott mappákat.
   
    ![Mappák létrehozása a Data Lake-fiókban](./media/data-lake-store-get-started-portal/ADL.New.Directory.png "Mappák létrehozása a Data Lake-fiókban")

## <a name="uploaddata"></a>Adatok feltöltése az Azure Data Lake Store-fiókba
Adatait közvetlenül, gyökérszinten is feltöltheti az Azure Data Lake Store-fiókba, vagy a fiókon belül létrehozott mappába. Az alábbi képernyőfelvételen látható lépésekkel feltölthet egy fájlt egy almappába a **Data Explorer** (Adatkezelő) panelről. A képernyőfelvételen a fájl az útkövetésben jelölt almappába lesz feltöltve (vörös téglalappal kijelölve).

Ha feltölthető mintaadatokra van szüksége, használhatja az [Azure Data Lake Git-tárában](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData) található **Ambulance Data** mappát.

![Adatok feltöltése](./media/data-lake-store-get-started-portal/ADL.New.Upload.File.png "Adatok feltöltése")

## <a name="properties"></a>A tárolt adatokra vonatkozó tulajdonságok és műveletek
Kattintson az újonnan hozzáadott fájlra a **Properties** (Tulajdonságok) panel megnyitásához. Ezen a panelen érhetők el a fájlhoz kapcsolódó tulajdonságok és a rajta a panelben elvégezhető műveletek. Másolhatja az Azure Data Lake Store-fiókban lévő fájl teljes elérési útját is, amely az alábbi képernyőfelvételen vörös téglalappal van kijelölve.

![Az adatok tulajdonságai](./media/data-lake-store-get-started-portal/ADL.File.Properties.png "Az adatok tulajdonságai")

* Kattintson a **Preview** (Előnézet) elemre a fájl előnézetének közvetlenül a böngészőből történő megtekintéséhez. Az előnézet formátumát is meghatározhatja. Kattintson a **Preview** (Előnézet), majd a **Format** (Formátum) elemre a **File Preview** (Fájl előnézete) panelen, és a **File Preview Format** (Fájl előnézetének formátuma) panelen adja meg az olyan beállításokat, mint a megjelenítendő sorok száma, a használni kívánt kódolás és elválasztók stb.
  
  ![A fájl előnézetének formátuma](./media/data-lake-store-get-started-portal/ADL.File.Preview.png "A fájl előnézetének formátuma")
* Kattintson a **Download** (Letöltés) elemre a fájl letöltéséhez a számítógépére.
* Kattintson a **Rename file** (Fájl átnevezése) elemre a fájl átnevezéséhez.
* Kattintson a **Delete file** (Fájl törlése) elemre a fájl törléséhez.

## <a name="secure-your-data"></a>Az adatok védelme
Az Azure Active Directory és a hozzáférés-vezérlés (ACLs) segítségével gondoskodhat az Azure Data Lake Store-fiókban tárolt adatok védelméről. Az erre vonatkozó utasításokért lásd: [Az adatok védelme az Azure Data Lake Store-ban](data-lake-store-secure-data.md).

## <a name="delete-azure-data-lake-store-account"></a>Az Azure Data Lake Store-fiók törlése
Az Azure Data Lake Store-fiók törléséhez kattintson a **Delete** (Törlés) elemre a Data Lake Store panelen. A művelet megerősítéséhez a rendszer a törölni kívánt fiók nevének megadását kéri. Adja meg a fiók nevét, majd kattintson a **Delete** (Törlés) gombra.

![Data Lake-fiók törlése](./media/data-lake-store-get-started-portal/ADL.Delete.Account.png "Data Lake-fiók törlése")

## <a name="next-steps"></a>Következő lépések
* [Biztonságos adattárolás a Data Lake Store-ban](data-lake-store-secure-data.md)
* [Az Azure Data Lake Analytics használata a Data Lake Store-ral](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Az Azure HDInsight használata a Data Lake Store-ral](data-lake-store-hdinsight-hadoop-use-portal.md)
* [A Data Lake Store diagnosztikai naplóinak elérése](data-lake-store-diagnostic-logs.md)


