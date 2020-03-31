---
title: Ismerkedés az Azure Data Lake Storage Gen1 portállal
description: Az Azure Portal használatával hozzon létre egy Data Lake Storage Gen1-fiókot, és alapvető műveleteket hajtson végre a fiókban.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 1bfb9362aa0e893a8bf1a758e5a0069e85ab227b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265583"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-the-azure-portal"></a>Ismerkedés az Azure Data Lake Storage Gen1 alkalmazással az Azure Portal használatával

> [!div class="op_single_selector"]
> * [Portál](data-lake-store-get-started-portal.md)
> * [Powershell](data-lake-store-get-started-powershell.md)
> * [Azure CLI](data-lake-store-get-started-cli-2.0.md)
>
>

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Megtudhatja, hogy az Azure Portal használatával hogyan hozhat létre Data Lake Storage Gen1-fiókot, és hogyan hajthat végre olyan alapvető műveleteket, mint például mappák létrehozása, feltöltése és letöltése adatfájlokat, fiók törlése stb. További információ: [Az Azure Data Lake Storage Gen1 áttekintése.](data-lake-store-overview.md)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-data-lake-storage-gen1-account"></a>Data Lake Storage Gen1-fiók létrehozása

1. Jelentkezzen be az új [Azure Portalra](https://portal.azure.com).
2. Kattintson **az Erőforrás > tárolás > a Data Lake Storage Gen1**elemre.
3. A **New Data Lake Storage Gen1** panelen adja meg az értékeket az alábbi képernyőképen látható módon:

    ![Új Data Lake Storage Gen1 fiók létrehozása](./media/data-lake-store-get-started-portal/ADL.Create.New.Account.png "Új Data Lake Storage Gen1 fiók létrehozása")

   * **Név**. Adja meg a Data Lake Storage Gen1 fiók egyedi nevét.
   * **Előfizetés**. Válassza ki azt az előfizetést, amely alatt új Data Lake Storage Gen1 fiókot szeretne létrehozni.
   * **Erőforráscsoport**. Válasszon ki egy meglévő erőforráscsoportot, vagy válassza a **Create new** (Új létrehozása) elemre, ha szeretne létrehozni egyet. Az erőforráscsoport egy tároló, amely alkalmazásokhoz kapcsolódó erőforrásokat tárol. További információk: [Erőforráscsoportok az Azure-ban](../azure-resource-manager/management/overview.md#resource-groups).
   * **Hely**: Válassza ki azt a helyet, ahol létre szeretné hozni a Data Lake Storage Gen1 fiókot.
   * **Titkosítási beállítások**. Három beállítás érhető el:

     * **A titkosítás letiltása**.
     * **Használja a Data Lake Storage Gen1 által kezelt kulcsokat,** ha azt szeretné, hogy a Data Lake Storage Gen1 kezelje a titkosítási kulcsokat.
     * **Saját tulajdonú Key Vault-kulcsok használata**. Kiválaszthat egy meglévő Azure Key Vault-tárolót, vagy létrehozhat egy újat. A key vaultból származó kulcsok használatához engedélyeket kell hozzárendelnie a Data Lake Storage Gen1 fiókhoz az Azure Key Vault eléréséhez. Az utasításokért lásd [az engedélyek az Azure Key Vaulthoz történő hozzárendelését](#assign-permissions-to-azure-key-vault) ismertető cikket.

        ![Data Lake Storage Gen1 titkosítás](./media/data-lake-store-get-started-portal/adls-encryption-2.png "Data Lake Storage Gen1 titkosítás")

        Kattintson az **OK** gombra a **Titkosítási beállítások** panelen.

        További információ: [Az adatok titkosítása az Azure Data Lake Storage Gen1](./data-lake-store-encryption.md)alkalmazásban.

4. Kattintson **a Létrehozás gombra.** Ha úgy döntött, hogy a fiókot az irányítópulton rögzíti, visszakerül az irányítópultra, és láthatja a Data Lake Storage Gen1 fiók kiépítésének előrehaladását. A Data Lake Storage Gen1 fiók kiépítése után a fiók panel jelenik meg.

## <a name="assign-permissions-to-azure-key-vault"></a><a name="assign-permissions-to-azure-key-vault"></a>Engedélyek hozzárendelése az Azure Key Vaulthoz

Ha egy Azure Key Vault kulcsait használta a Data Lake Storage Gen1 fiók titkosításának konfigurálásához, konfigurálnia kell a Data Lake Storage Gen1 fiók és az Azure Key Vault-fiók közötti hozzáférést. Ehhez kövesse az alábbi lépéseket.

1. Ha az Azure Key Vault kulcsait használta, a Data Lake Storage Gen1 fiók panelje figyelmeztetést jelenít meg a tetején. Kattintson a figyelmeztetésre a **Titkosítás** panel megnyitásához.

    ![Data Lake Storage Gen1 titkosítás](./media/data-lake-store-get-started-portal/adls-encryption-3.png "Data Lake Storage Gen1 titkosítás")
2. A panelen két lehetőség szerepel a hozzáférés konfigurálásához.

    ![Data Lake Storage Gen1 titkosítás](./media/data-lake-store-get-started-portal/adls-encryption-4.png "Data Lake Storage Gen1 titkosítás")

   * Az első lehetőségnél kattintson az **Engedélyek megadása** gombra a hozzáférés konfigurálásához. Az első lehetőség csak akkor engedélyezett, ha a Data Lake Storage Gen1 fiókot létrehozó felhasználó is az Azure Key Vault rendszergazdája.
   * A másik lehetőség a panelen megjelenő PowerShell parancsmag futtatása. Az Azure Key Vault tulajdonosának kell lennie, vagy képesnek kell lennie engedélyeket hozzárendelni az Azure Key Vaulton. A parancsmag futtatása után térjen vissza a panelre, és kattintson az **Engedélyezés** gombra a hozzáférés konfigurálásához.

> [!NOTE]
> A Data Lake Storage Gen1-fiókot is létrehozhat az Azure Resource Manager-sablonok használatával. Ezek a sablonok az [Azure gyors üzembehelyezési sablonokból](https://azure.microsoft.com/resources/templates/?term=data+lake+store) érhetők el:
> * Adattitkosítás nélkül: [Telepítse az Azure Data Lake Storage Gen1 fiókot adattitkosítás nélkül.](https://azure.microsoft.com/resources/templates/101-data-lake-store-no-encryption/)
> * Adattárolási gen1 használatával az adattitkosítás használatával: [A Data Lake Storage Gen1 fiók telepítése titkosítással(Data Lake)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/).
> * Az Azure Key Vault használatával történő adattitkosítással: [A Data Lake Storage Gen1 fiók telepítése titkosítással(Key Vault)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/).
>
>

## <a name="create-folders"></a><a name="createfolder"></a>Mappák létrehozása

A Data Lake Storage Gen1 fiók alatt mappákat hozhat létre az adatok kezeléséhez és tárolásához.

1. Nyissa meg a létrehozott Data Lake Storage Gen1 fiókot. A bal oldali ablaktáblában kattintson a **Minden erőforrás**elemre, majd a **Minden erőforrás** panelen kattintson arra a fióknévre, amely alatt mappákat szeretne létrehozni. Ha rögzítette a fiókot a kezdőpulton, kattintson a fiók csempéjére.
2. A Data Lake Storage Gen1 fiókpanelen kattintson az **Adatkezelő**elemre.

    ![Mappák létrehozása Data Lake Storage Gen1 fiókban](./media/data-lake-store-get-started-portal/ADL.Create.Folder.png "Mappák létrehozása Data Lake Storage Gen1 fiókban")
3. A Data Explorer (Adatkezelő) panelen kattintson a **New Folder** (Új mappa) elemre, adjon nevet az új mappának, majd kattintson az **OK** gombra.

    ![Mappák létrehozása Data Lake Storage Gen1 fiókban](./media/data-lake-store-get-started-portal/ADL.Folder.Name.png "Mappák létrehozása Data Lake Storage Gen1 fiókban")

    Az újonnan létrehozott mappa megjelenik az **Adatkezelő** panelen lévő listában. Egymásba ágyazott mappákat bármilyen szinten létrehozhat.

    ![Mappák létrehozása Data Lake-fiókban](./media/data-lake-store-get-started-portal/ADL.New.Directory.png "Mappák létrehozása Data Lake-fiókban")

## <a name="upload-data"></a><a name="uploaddata"></a>Adatok feltöltése

Az adatokat feltöltheti egy Data Lake Storage Gen1 fiókba közvetlenül a gyökérszinten, vagy egy mappába, amelyet a fiókon belül hozott létre.

1. A **Data Explorer** (Adatkezelő) panelen kattintson az **Upload** (Feltöltés) elemre.
2. Az **Upload files** (Fájlok feltöltése) panelen keresse meg a feltölteni kívánt fájlokat, majd kattintson az **Add selected files** (Kiválasztott fájlok hozzáadása) gombra. Egyszerre több fájlt is kijelölhet a feltöltéshez.

    ![Adatok feltöltése](./media/data-lake-store-get-started-portal/ADL.New.Upload.File.png "Adatok feltöltése")

Ha feltölthető mintaadatokra van szüksége, használhatja az [Azure Data Lake Git-tárában](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData) található **Ambulance Data** mappát.

## <a name="actions-available-on-the-stored-data"></a><a name="properties"></a>A tárolt adatokhoz elérhető műveletek

Kattintson a fájl melletti, három pontot ábrázoló ikonra, majd az előugró menüben kattintson arra a műveletre, amelyet el kíván végezni az adatokon.

![Az adatok tulajdonságai](./media/data-lake-store-get-started-portal/ADL.File.Properties.png "Az adatok tulajdonságai")

## <a name="secure-your-data"></a>Az adatok védelme

A Data Lake Storage Gen1-fiókjában tárolt adatokat az Azure Active Directory és a hozzáférés-vezérlés (ACL) használatával biztosíthatja. Ennek módjáról az Azure [Data Lake Storage Gen1 szolgáltatásban az adatok védelme 1.](data-lake-store-secure-data.md)

## <a name="delete-your-account"></a>Fiók törlése

A Data Lake Storage Gen1 fiók törléséhez kattintson a Data Lake Storage Gen1 panelen a **Törlés gombra.** A művelet megerősítéséhez a rendszer a törölni kívánt fiók nevének megadását kéri. Adja meg a fiók nevét, majd kattintson a **Delete** (Törlés) gombra.

![Data Lake Storage Gen1 fiók törlése](./media/data-lake-store-get-started-portal/ADL.Delete.Account.png "Data Lake-fiók törlése")

## <a name="next-steps"></a>További lépések

* [Az Azure Data Lake Storage Gen1 használata big data-követelményekhez](data-lake-store-data-scenarios.md)
* [Az adatok védelme az 1. generációs Data Lake Storage-ban](data-lake-store-secure-data.md)
* [Az Azure Data Lake Analytics használata a Data Lake Storage Gen1 szolgáltatással](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Az Azure HDInsight használata a Data Lake Storage Gen1 szolgáltatással](data-lake-store-hdinsight-hadoop-use-portal.md)
