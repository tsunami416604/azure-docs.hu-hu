---
title: Adatok megosztása és fogadása az Azure Blob Storage és Azure Data Lake Storage
description: Ismerje meg, hogyan oszthat meg és fogadhat adatait az Azure Blob Storage és Azure Data Lake Storage
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 08/28/2020
ms.openlocfilehash: a54a9d4c50852fe78fd245723dd29f487d58f4b1
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89270121"
---
# <a name="share-and-receive-data-from-azure-blob-storage-and-azure-data-lake-storage"></a>Adatok megosztása és fogadása az Azure Blob Storage és Azure Data Lake Storage

[!INCLUDE[appliesto-storage](includes/appliesto-storage.md)]

Az Azure-beli adatmegosztás támogatja a Storage-fiók pillanatkép-alapú megosztását. Ez a cikk az alábbi forrásokból származó adatok megosztását és fogadását ismerteti: Azure Blob Storage, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2.

Az Azure-beli adatmegosztás támogatja a fájlok, mappák és fájlrendszerek megosztását Azure Data Lake Gen1 és Azure Data Lake Gen2. Emellett támogatja a Blobok, mappák és tárolók megosztását az Azure Blob Storage-ból. Jelenleg csak a Block blob támogatott. Az ezekből a forrásokból megosztott adatok beérkeznek Azure Data Lake Gen2 vagy az Azure Blob Storageba.

Ha a fájlrendszer, a tárolók vagy a mappák a pillanatkép-alapú megosztásban vannak megosztva, az adatfogyasztó dönthet úgy, hogy teljes másolatot készít a megosztási adatokról, vagy a növekményes pillanatkép-képességgel csak az új vagy frissített fájlokat másolja. A növekményes pillanatkép a fájlok utolsó módosításának időpontján alapul. A rendszer felülírja az azonos nevű meglévő fájlokat.

## <a name="share-data"></a>Adatok megosztása

### <a name="prerequisites-to-share-data"></a>Az adatmegosztásra vonatkozó előfeltételek

* Azure-előfizetés: Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) .
* A címzett Azure bejelentkezési e-mail-címe (az e-mail alias használata nem fog működni).
* Ha az Azure-beli adattár egy másik Azure-előfizetésben található, mint amelyet az adatmegosztási erőforrás létrehozásához fog használni, regisztrálja a [Microsoft. DataShare erőforrás-szolgáltatót](concepts-roles-permissions.md#resource-provider-registration) abban az előfizetésben, amelyben az Azure-adattár található. 

### <a name="prerequisites-for-source-storage-account"></a>A forrásként szolgáló Storage-fiók előfeltételei

* Azure Storage-fiók: Ha még nem rendelkezik ilyennel, létrehozhat egy [Azure Storage-fiókot](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* A Storage-fiókba való írásra vonatkozó engedély, amely megtalálható a *Microsoft. Storage/storageAccounts/Write*szolgáltatásban. Ez az engedély a közreműködő szerepkörben található.
* Jogosultság a szerepkör-hozzárendelés hozzáadásához a Storage-fiókhoz, amely megtalálható a *Microsoft. Authorization/szerepkör-hozzárendelésekben/írásban*. Ez az engedély létezik a tulajdonosi szerepkörben. 

### <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

### <a name="create-a-data-share-account"></a>Adatmegosztási fiók létrehozása

Azure-beli adatmegosztási erőforrás létrehozása Azure-erőforráscsoporthoz.

1. Kattintson a portál bal felső sarkában található menü gombra, majd válassza az **erőforrás létrehozása** (+) lehetőséget.

1. Keresse meg az *adatmegosztást*.

1. Válassza az adatmegosztás lehetőséget, majd válassza a **Létrehozás**lehetőséget.

1. Töltse ki az Azure-beli adatmegosztási erőforrás alapvető adatait az alábbi információkkal. 

     **Beállítás** | **Ajánlott érték** | **Mező leírása**
    |---|---|---|
    | Előfizetés | Az Ön előfizetése | Válassza ki az adatmegosztási fiókhoz használni kívánt Azure-előfizetést.|
    | Erőforráscsoport | *teszt – erőforrás-csoport* | Használjon meglévő erőforráscsoportot, vagy hozzon létre egy új erőforráscsoportot. |
    | Hely | *USA 2. keleti régiója* | Válassza ki az adatmegosztási fiókhoz tartozó régiót.
    | Name | *datashareaccount* | Adja meg az adatmegosztási fiók nevét. |
    | | |

1. Válassza a **felülvizsgálat + létrehozás**, majd a **Létrehozás** lehetőséget az adatmegosztási fiók kiépítéséhez. Az új adatmegosztási fiók üzembe helyezése általában körülbelül 2 percet vesz igénybe. 

1. Ha a telepítés befejeződött, válassza az **Ugrás erőforráshoz**lehetőséget.

### <a name="create-a-share"></a>Megosztás létrehozása

1. Navigáljon az adatmegosztás áttekintő oldalára.

    ![Az adatok megosztása](./media/share-receive-data.png "Az adatok megosztása") 

1. Válassza **az adatmegosztás megkezdése**lehetőséget.

1. Válassza a **Létrehozás** lehetőséget.   

1. Adja meg a megosztás részleteit. Adja meg a nevet, a megosztás típusát, a megosztási tartalmak leírását és a használati feltételeket (opcionális). 

    ![EnterShareDetails](./media/enter-share-details.png "Adja meg a megosztás részleteit") 

1. Válassza a **Folytatás**lehetőséget.

1. Az adatkészletek megosztáshoz való hozzáadásához válassza az **adatkészletek hozzáadása**elemet. 

    ![Adatkészletek hozzáadása a megosztáshoz](./media/datasets.png "Adathalmazok")

1. Válassza ki a hozzáadni kívánt adatkészlet típusát. Az adathalmazok eltérő listáját fogja látni az előző lépésben kiválasztott megosztási típustól (pillanatkép vagy helyben) függően. 

    ![AddDatasets](./media/add-datasets.png "Adatkészletek hozzáadása")    

1. Navigáljon a megosztani kívánt objektumhoz, és válassza az "adatkészletek hozzáadása" lehetőséget. 

    ![SelectDatasets](./media/select-datasets.png "Adatkészletek kiválasztása")    

1. A címzettek lapon adja meg az adatfogyasztó e-mail-címeit a "+ Címzett hozzáadása" lehetőség kiválasztásával. 

    ![AddRecipients](./media/add-recipient.png "Címzettek hozzáadása") 

1. Válassza a **Folytatás**lehetőséget.

1. Ha kiválasztotta a pillanatkép-megosztás típusát, beállíthatja a pillanatkép-ütemtervet, hogy az adatokra vonatkozó frissítéseket biztosítson az adatfogyasztónak. 

    ![EnableSnapshots](./media/enable-snapshots.png "Pillanatképek engedélyezése") 

1. Válassza ki a kezdési időt és az ismétlődési időközt. 

1. Válassza a **Folytatás**lehetőséget.

1. A felülvizsgálat + létrehozás lapon tekintse át a csomag tartalmát, a beállításokat, a címzetteket és a szinkronizálási beállításokat. Válassza a **Létrehozás** lehetőséget.

Az Azure-beli adatmegosztás már létrejött, és az adatmegosztás címzettje most már készen áll a meghívás elfogadására. 

## <a name="receive-data"></a>Adatfogadás

### <a name="prerequisites-to-receive-data"></a>Az Adatfogadás előfeltételei
Az adatmegosztási Meghívások elfogadása előtt számos Azure-erőforrást kell kiépíteni, amelyek alább láthatók. 

Az adatmegosztási Meghívások elfogadása előtt győződjön meg arról, hogy az összes előfeltétel befejeződik. 

* Azure-előfizetés: Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) .
* Egy adatmegosztás meghívása: Microsoft Azure egy meghívót, melynek tárgya "Azure-adatmegosztási meghívás **<yourdataprovider@domain.com>** ".
* Regisztrálja a [Microsoft. DataShare erőforrás-szolgáltatót](concepts-roles-permissions.md#resource-provider-registration) az Azure-előfizetésben, amelyben létre fog hozni egy adatmegosztási erőforrást és az Azure-előfizetést, ahol a cél Azure-adattárolók találhatók.

### <a name="prerequisites-for-target-storage-account"></a>A célként megadott Storage-fiók előfeltételei

* Azure Storage-fiók: Ha még nem rendelkezik ilyennel, létrehozhat egy [Azure Storage-fiókot](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)is. 
* A Storage-fiókba való írásra vonatkozó engedély, amely megtalálható a *Microsoft. Storage/storageAccounts/Write*szolgáltatásban. Ez az engedély a közreműködő szerepkörben található. 
* Jogosultság a szerepkör-hozzárendelés hozzáadásához a Storage-fiókhoz, amely megtalálható a *Microsoft. Authorization/szerepkör-hozzárendelésekben/írásban*. Ez az engedély létezik a tulajdonosi szerepkörben.  

### <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

### <a name="open-invitation"></a>Meghívás megnyitása

1. A meghívót megnyithatja e-mailben, vagy közvetlenül a Azure Portalból. 

   Ha meg szeretné nyitni a meghívót az e-mailből, akkor az adatszolgáltatótól érkező meghívót a Beérkezett üzenetek közül A meghívó Microsoft Azure származik, és az **Azure-beli adatmegosztási meghívást kapott <yourdataprovider@domain.com> **. Kattintson a **meghívás megtekintése** elemre, hogy megtekintse az Azure-beli meghívót. 

   Ha közvetlenül Azure Portal szeretné megnyitni a meghívót, keresse meg az **adatmegosztási meghívásokat** a Azure Portalban. Ekkor megjelenik az adatmegosztási meghívások listája.

   ![Meghívások listája](./media/invitations.png "Meghívások listája") 

1. Válassza ki a megtekinteni kívánt megosztást. 

### <a name="accept-invitation"></a>Meghívás elfogadása
1. Győződjön meg arról, hogy az összes mezőt felülvizsgálják, beleértve a használati **feltételeket**is. Ha elfogadja a használati feltételeket, a jelölőnégyzet bejelölésével jelezheti, hogy elfogadja. 

   ![Használati feltételek](./media/terms-of-use.png "Használati feltételek") 

1. A *cél adatmegosztási fiók*területen válassza ki azt az előfizetést és erőforráscsoportot, amelybe az adatmegosztást telepíteni fogja. 

   Az **adatmegosztási fiók** mezőben válassza az **új létrehozása** lehetőséget, ha nem rendelkezik meglévő adatmegosztási fiókkal. Ellenkező esetben válasszon ki egy meglévő adatmegosztási fiókot, amelyet el szeretne fogadni az adatmegosztásban. 

   A **Beérkezett megosztás neve** mezőben hagyhatja az alapértelmezett által megadott értéket, vagy megadhat egy új nevet a fogadott megosztáshoz. 

   Miután jóváhagyta a használati feltételeket, és megadott egy adatmegosztási fiókot a kapott megosztás kezeléséhez, válassza az **elfogadás és konfigurálás**lehetőséget. A rendszer létrehoz egy megosztási előfizetést. 

   ![Elfogadás beállításai](./media/accept-options.png "Elfogadás beállításai") 

   Ekkor a kapott megosztás szerepel az adatmegosztási fiókban. 

   Ha nem szeretné elfogadni a meghívót, válassza az *elutasítás*lehetőséget. 

### <a name="configure-received-share"></a>Fogadott megosztás konfigurálása
Az alábbi lépésekkel konfigurálhatja, hogy hová kívánja fogadni az adatgyűjtést.

1. Válassza az **adatkészletek** lapot. Jelölje be azon adatkészlet melletti jelölőnégyzetet, amelyhez hozzá szeretné rendelni a célhelyet. A cél adattár kiválasztásához válassza a **+ Térkép** lehetőséget. 

   ![Leképezés célhelyre](./media/dataset-map-target.png "Leképezés célhelyre") 

1. Válassza ki azt a cél adattárat, amelybe az adatterületet szeretné kijelölni. A célként megadott adattárban található, azonos elérési úttal és névvel rendelkező adatfájlok felül lesznek írva. 

   ![Cél Storage-fiók](./media/map-target.png "Cél tárterülete") 

1. A pillanatkép-alapú megosztáshoz, ha az adatszolgáltató pillanatkép-ütemtervet hozott létre az adatok rendszeres frissítéséhez, **akkor a pillanatkép-ütemterv** kiválasztásával is engedélyezheti a pillanatkép-ütemtervet. Jelölje be a pillanatkép-ütemterv melletti jelölőnégyzetet, majd válassza a **+ Engedélyezés**lehetőséget.

   ![Pillanatkép-ütemterv engedélyezése](./media/enable-snapshot-schedule.png "Pillanatkép-ütemterv engedélyezése")

### <a name="trigger-a-snapshot"></a>Pillanatkép indítása
Ezek a lépések csak a pillanatkép-alapú megosztásra vonatkoznak.

1. A pillanatképek elindításához válassza a **részletek** lapot, majd az **trigger pillanatképét**. Itt aktiválhatja az adatok teljes vagy növekményes pillanatképét. Ha az első alkalommal fogadja az adatait az adatszolgáltatótól, válassza a teljes másolás lehetőséget. 

   ![Pillanatkép indítása](./media/trigger-snapshot.png "Pillanatkép indítása") 

1. Ha a legutóbbi futtatási állapot *sikeres*, nyissa meg a cél adattárt a kapott adatértékek megtekintéséhez. Válassza az **adatkészletek**lehetőséget, majd kattintson a hivatkozásra a cél elérési úton. 

   ![Fogyasztói adatkészletek](./media/consumer-datasets.png "Fogyasztói adatkészlet megfeleltetése") 

### <a name="view-history"></a>Előzmények megtekintése
Ez a lépés csak a pillanatkép-alapú megosztásra vonatkozik. A pillanatképek előzményeinek megtekintéséhez válassza az **Előzmények** fület. Itt megtalálhatja az elmúlt 30 napban létrehozott összes pillanatkép előzményeit. 

## <a name="next-steps"></a>További lépések
Megtanulta, hogyan oszthat meg és fogadhat adatait a Storage-fiókból az Azure adatmegosztási szolgáltatás használatával. Ha többet szeretne megtudni a más adatforrásokból történő megosztásról, folytassa a [támogatott adattárakkal](supported-data-stores.md).

