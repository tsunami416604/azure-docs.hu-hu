---
title: Az Azure Data Lake Storage Gen1 használatának első lépései az Azure portal használatával |} A Microsoft Docs
description: Az Azure portal használatával hozzon létre egy Azure Data Lake Storage Gen1 fiókot, és alapszintű műveletek végrehajtása a Data Lake Storage Gen1 fiókban.
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: nitinme
ms.openlocfilehash: a99d8a265cdc72b6bd1dd45ab670f514d828f808
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58005705"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-the-azure-portal"></a>Az Azure Data Lake Storage Gen1 használatának első lépései az Azure portal használatával

> [!div class="op_single_selector"]
> * [Portál](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Azure CLI](data-lake-store-get-started-cli-2.0.md)
>
> 

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Útmutató az Azure portal használatával hozzon létre egy Azure Data Lake Storage Gen1 fiókot, és alapszintű műveleteket, mint például mappák létrehozása, és feltöltése adatfájlok le, a fiók törlése stb. További információkért lásd: [áttekintése az Azure Data Lake Storage Gen1](data-lake-store-overview.md).

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-data-lake-storage-gen1-account"></a>Hozzon létre egy Data Lake Storage Gen1 fiókot

1. Jelentkezzen be az új [Azure Portalra](https://portal.azure.com).
2. Kattintson a **erőforrás létrehozása > Storage > Data Lake Storage Gen1**.
3. Az a **új Data Lake Storage Gen1** panelen adja meg az értékeket az alábbi képernyőképen látható módon:
   
    ![Hozzon létre egy új Data Lake Storage Gen1 fiókot](./media/data-lake-store-get-started-portal/ADL.Create.New.Account.png "hozzon létre egy új Data Lake Storage Gen1 fiókot")
   
   * **Név**. Adjon meg egy egyedi nevet a Data Lake Storage Gen1 fiók.
   * **Előfizetés**. Válassza ki az előfizetést, amely alatt hozzon létre egy új Data Lake Storage Gen1 fiókot szeretne.
   * **Erőforráscsoport**. Válasszon ki egy meglévő erőforráscsoportot, vagy válassza a **Create new** (Új létrehozása) elemre, ha szeretne létrehozni egyet. Az erőforráscsoport egy tároló, amely alkalmazásokhoz kapcsolódó erőforrásokat tárol. További információk: [Erőforráscsoportok az Azure-ban](../azure-resource-manager/resource-group-overview.md#resource-groups).
   * **Hely**: Válasszon egy helyet, ahol szeretné létrehozni a Data Lake Storage Gen1 fiókot.
   * **Titkosítási beállítások**. Három beállítás érhető el:
     
     * **A titkosítás letiltása**.
     * **Data Lake Storage Gen1 által kezelt kulcsok használata**, ha azt szeretné, hogy a Data Lake Storage Gen1 kezelje a titkosítási kulcsokat.
     * **Saját tulajdonú Key Vault-kulcsok használata**. Kiválaszthat egy meglévő Azure Key Vault-tárolót, vagy létrehozhat egy újat. A Key Vaultban lévő kulcsok használatához hozzá kell rendelnie az Azure Key Vault elérése érdekében a Data Lake Storage Gen1 fiók engedélyeit. Az utasításokért lásd [az engedélyek az Azure Key Vaulthoz történő hozzárendelését](#assign-permissions-to-azure-key-vault) ismertető cikket.
       
        ![Data Lake Storage Gen1 titkosítási](./media/data-lake-store-get-started-portal/adls-encryption-2.png "titkosítást a Data Lake Storage Gen1")
       
        Kattintson az **OK** gombra a **Titkosítási beállítások** panelen.

        További információkért lásd: [Azure Data Lake Storage Gen1 adatok titkosítása az](./data-lake-store-encryption.md).

4. Kattintson a **Create** (Létrehozás) gombra. Ha úgy döntött, hogy rögzíti a fiókot az irányítópulton, visszakerül az irányítópultra, és láthatja, hogy a Data Lake Storage Gen1 fiók üzembe helyezésének folyamatát. A Data Lake Storage Gen1 fiók ki van építve, miután a fiók panelen megjelenik-e.

## <a name="assign-permissions-to-azure-key-vault"></a>Engedélyek hozzárendelése az Azure Key Vaulthoz
Ha konfigurálta a titkosítást a Data Lake Storage Gen1 fiók az Azure Key Vault kulcsait használta, konfigurálnia kell a Data Lake Storage Gen1 fiók és az Azure Key Vault-fiók közötti hozzáférést. Ehhez kövesse az alábbi lépéseket.

1. Az Azure Key Vault-kulcsok használata esetén a Data Lake Storage Gen1 fiók panel egy figyelmeztetést jelenít meg az oldal tetején. Kattintson a figyelmeztetésre a **Titkosítás** panel megnyitásához.
   
    ![Data Lake Storage Gen1 titkosítási](./media/data-lake-store-get-started-portal/adls-encryption-3.png "titkosítást a Data Lake Storage Gen1")
2. A panelen két lehetőség szerepel a hozzáférés konfigurálásához.

    ![Data Lake Storage Gen1 titkosítási](./media/data-lake-store-get-started-portal/adls-encryption-4.png "titkosítást a Data Lake Storage Gen1")
   
   * Az első lehetőségnél kattintson az **Engedélyek megadása** gombra a hozzáférés konfigurálásához. Az első lehetőség csak akkor, ha a Data Lake Storage Gen1 fiókot létrehozó felhasználó egyben rendszergazda az Azure Key Vault számára engedélyezve van.
   * A másik lehetőség a panelen megjelenő PowerShell parancsmag futtatása. Az Azure Key Vault tulajdonosának kell lennie, vagy képesnek kell lennie engedélyeket hozzárendelni az Azure Key Vaulton. A parancsmag futtatása után térjen vissza a panelre, és kattintson az **Engedélyezés** gombra a hozzáférés konfigurálásához.

> [!NOTE]
> Egy Data Lake Storage Gen1 fiókot az Azure Resource Manager-sablonok használatával is létrehozhat. Ezek a sablonok az [Azure gyors üzembehelyezési sablonokból](https://azure.microsoft.com/resources/templates/?term=data+lake+store) érhetők el:
> - Adattitkosítás: nélkül [Üzembe helyezése adattitkosítás nélkül az Azure Data Lake Storage Gen1 fiók](https://azure.microsoft.com/resources/templates/101-data-lake-store-no-encryption/).
> - Adattitkosítás használatával a Data Lake Storage Gen1: [Üzembe helyezése titkosítással (Data Lake) a Data Lake Storage Gen1 fiók](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/).
> - Az Azure Key Vaulttal adattitkosítás: [Üzembe helyezése titkosítással (Key Vault) a Data Lake Storage Gen1 fiók](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/).
> 
> 



## <a name="createfolder"></a>Mappák létrehozása a Data Lake Storage Gen1-fiókban
A Data Lake Storage Gen1-adatok kezelésére és tárolására fiókjában mappákat hozhat létre.

1. Nyissa meg a létrehozott Data Lake Storage Gen1 fiókot. A bal oldali ablaktáblában kattintson az **Összes erőforrás** elemre, majd az Összes erőforrás panelen kattintson a fiók nevére, amelyben mappákat kíván létrehozni. Ha rögzítette a fiókot a kezdőpulton, kattintson a fiók csempéjére.
2. A Data Lake Storage Gen1 fiók panelén kattintson **adatkezelő**.
   
    ![Mappák létrehozása a Data Lake Storage Gen1 fiókban](./media/data-lake-store-get-started-portal/ADL.Create.Folder.png "mappák létrehozása a Data Lake Storage Gen1-fiókban")
3. A Data Explorer (Adatkezelő) panelen kattintson a **New Folder** (Új mappa) elemre, adjon nevet az új mappának, majd kattintson az **OK** gombra.
   
    ![Mappák létrehozása a Data Lake Storage Gen1 fiókban](./media/data-lake-store-get-started-portal/ADL.Folder.Name.png "mappák létrehozása a Data Lake Storage Gen1-fiókban")
   
    Az újonnan létrehozott mappa megjelenik az **Adatkezelő** panelen lévő listában. Bármilyen szintű legfeljebb beágyazott mappákat hozhat létre.
   
    ![Mappák létrehozása a Data Lake-fiók](./media/data-lake-store-get-started-portal/ADL.New.Directory.png "mappák létrehozása a Data Lake-fiók")

## <a name="uploaddata"></a>Adatok feltöltése a Data Lake Storage Gen1-fiókhoz
Feltöltheti az adatokat, egy Data Lake Storage Gen1 fiókhoz közvetlenül, gyökérszinten, vagy a fiókon belül létrehozott mappába. 

1. A **Data Explorer** (Adatkezelő) panelen kattintson az **Upload** (Feltöltés) elemre. 
2. Az **Upload files** (Fájlok feltöltése) panelen keresse meg a feltölteni kívánt fájlokat, majd kattintson az **Add selected files** (Kiválasztott fájlok hozzáadása) gombra. Egyszerre több fájlt is kijelölhet a feltöltéshez.

    ![Adatok feltöltése](./media/data-lake-store-get-started-portal/ADL.New.Upload.File.png "Adatok feltöltése")

Ha feltölthető mintaadatokra van szüksége, használhatja az [Azure Data Lake Git-tárában](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData) található **Ambulance Data** mappát.

## <a name="properties"></a>A tárolt adatokhoz elérhető műveletek
Kattintson a fájl melletti, három pontot ábrázoló ikonra, majd az előugró menüben kattintson arra a műveletre, amelyet el kíván végezni az adatokon.

![Az adatok tulajdonságai](./media/data-lake-store-get-started-portal/ADL.File.Properties.png "Az adatok tulajdonságai") 

## <a name="secure-your-data"></a>Az adatok védelme
Az Azure Active Directory- és hozzáférés-vezérlés (ACL-ek) használatával a Data Lake Storage Gen1 fiókban tárolt adatok biztonságát. Hogyan valósítható meg, lásd: [az adatok védelme az Azure Data Lake Storage Gen1](data-lake-store-secure-data.md).

## <a name="delete-a-data-lake-storage-gen1-account"></a>Törölheti a Data Lake Storage Gen1
A Data Lake Storage Gen1 panelen egy Data Lake Storage Gen1 fiók törléséhez kattintson **törlése**. A művelet megerősítéséhez a rendszer a törölni kívánt fiók nevének megadását kéri. Adja meg a fiók nevét, majd kattintson a **Delete** (Törlés) gombra.

![Data Lake Storage Gen1 fiók törlése](./media/data-lake-store-get-started-portal/ADL.Delete.Account.png "Data Lake-fiók törlése")

## <a name="next-steps"></a>További lépések
* [Az Azure Data Lake Storage Gen1 használata big data-követelményekhez](data-lake-store-data-scenarios.md) 
* [Az adatok védelme az 1. generációs Data Lake Storage-ban](data-lake-store-secure-data.md)
* [Az Azure Data Lake Analytics használata a Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Az Azure HDInsight használata a Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

