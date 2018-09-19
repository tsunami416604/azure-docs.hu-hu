---
title: Az Azure Data Lake Storage Gen1-erőforrásait kezelheti az Azure Storage Explorerrel
description: Ismerje meg, hogyan eléréséhez és kezeléséhez az Azure Data Lake Storage Gen1 adatokat és erőforrásokat az Azure Storage Explorerben
Keywords: Azure Data Lake Store, Azure Storage Explorer
services: Data Lake Store
documentationcenter: ''
author: jejiang
manager: DJ
editor: Jenny Jiang
ms.assetid: ''
ms.service: data-lake-store
ms.custom: Azure Data Lake Store
ms.devlang: dotnet
ms.topic: article
ms.date: 02/05/2018
ms.author: jejiang
ms.openlocfilehash: 537c94976dbb4f7753b7fafb3b6c94e97cc2cc17
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46126326"
---
# <a name="manage-azure-data-lake-storage-gen1-resources-by-using-storage-explorer"></a>Az Azure Data Lake Storage Gen1-erőforrások kezelése a Storage Explorer használatával

[Az Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) szolgáltatás nagy mennyiségű strukturálatlan adat, például szövegek vagy bináris adatok tárolására használható. HTTP-n vagy HTTPS-en keresztül bárhonnan hozzáférhet az adataihoz. Data Lake Storage Gen1 az Azure Storage Explorerben lehetővé teszi, hogy a Data Lake Storage Gen1 adatokat és erőforrásokat, mint a blobok és üzenetsorok más Azure-entitásokkal együtt elérését és kezelését. Mostantól ugyanazon eszközzel, egy helyen kezelheti a különböző Azure-entitásokat.

Egy másik előnye, hogy, nincs szükség előfizetési engedélyre a Data Lake Storage Gen1 adatok kezelésére. A Storage Explorerben a Data Lake Storage Gen1 elérési útját is csatlakoztatható a **helyi és csatolt** csomóponthoz, valaki ezt engedélyezi.

## <a name="prerequisites"></a>Előfeltételek
A cikkben leírt lépések elvégzéséhez a következő előfeltételekre lesz szüksége:

*   Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial).
*   Egy Data Lake Storage Gen1 fiók. Létrehozásával kapcsolatos utasításokért lásd: [Ismerkedés az Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

## <a name="install-storage-explorer"></a>A Storage Explorer telepítése

Az Azure Storage Explorer legújabb összetevői a [termék weblapjáról](https://azure.microsoft.com/features/storage-explorer/) telepíthetők. A Windows-, Linux- és Mac-alapú verzió telepítése is támogatott.

## <a name="connect-to-an-azure-subscription"></a>Csatlakozás Azure-előfizetéshez

1. A Storage Explorerben kattintson a bal oldali beépülő modul ikonra.
       
   ![Beépülő modul ikonja](./media/data-lake-store-in-storage-explorer/plug-in-icon.png)
 
2. Válassza az **Azure-fiók hozzáadása** elemet, majd kattintson a **Bejelentkezés** gombra.

   ![„Csatlakozás az Azure Storage-hoz” párbeszédpanel](./media/data-lake-store-in-storage-explorer/connect-to-azure-subscription.png)

2. A **Bejelentkezés a fiókba** párbeszédpanelen adja meg Azure-beli hitelesítő adatait.

    ![Azure-bejelentkezés párbeszédpanele](./media/data-lake-store-in-storage-explorer/sign-in.png)

3. Válassza ki az előfizetést a listából, majd kattintson az **Alkalmaz** gombra.

    ![Előfizetési információ és „Alkalmaz” gomb](./media/data-lake-store-in-storage-explorer/apply-subscription.png)

    Az **EXPLORER** panel frissül, és megjeleníti a kiválasztott előfizetéshez tartozó fiókokat.

    ![Fióklista](./media/data-lake-store-in-storage-explorer/account-list.png)

Data Lake Storage Gen1 sikeresen csatlakozott az Azure-előfizetéshez.

## <a name="connect-to-data-lake-storage-gen1"></a>Csatlakozhat a Data Lake Storage Gen1
Az előfizetésében nem szereplő erőforrásokhoz is hozzáférhet, ha valaki megadja az erőforrások URI-ját. Majd csatlakozhat Data Lake Storage Gen1 bejelentkezés után az URI használatával.
1. Nyissa meg a Storage Explorert.
2. A bal oldali panelen bontsa ki a **Helyi és csatolt** elemet.
3. Kattintson a jobb gombbal a **Data Lake Store** elemre, és válassza a **Csatlakozás a Data Lake Store-hoz** lehetőséget.

      ![„Csatlakozás a Data Lake Store-hoz” a helyi menün](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-uri-attach.png)

4. Adja meg az URI-t. Az eszköz a megadott URL-címre lép.

      ![„Csatlakozás a Data Lake Store-hoz” párbeszédpanel, az URI beírására szolgáló szövegmezővel](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

      ![Data Lake Storage Gen1 eredménye](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-attach-finish.png)

## <a name="view-a-data-lake-storage-gen1-accounts-contents"></a>A Data Lake Storage Gen1 fiók tartalmának megtekintése
A Data Lake Storage Gen1-fiókok erőforrásai között mappák és fájlok.

A következő lépések bemutatják, hogyan tekinthető tartalma a Storage Explorerben a Data Lake Storage Gen1 fiók:

1. Nyissa meg a Storage Explorert.
2. A bal oldali ablaktáblán bontsa ki az előfizetést, amely tartalmazza a Data Lake Storage Gen1 fiókot, hogy meg szeretné jeleníteni.
3. Bontsa ki a **Data Lake Store** elemet.
4. Kattintson a jobb gombbal a Data Lake Storage Gen1-fiók csomópontjára, megtekintheti, és válassza ki a kívánt **nyílt**. Vagy kattintson duplán a Data Lake Storage Gen1 fiókra a megnyitásához. 
   
   A fő panel megjeleníti a Data Lake Storage Gen1-fiók tartalmát.

   ![Fő panel a mappák listájával](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-toolbar-mainpane.png) 

## <a name="manage-resources-in-data-lake-storage-gen1"></a>A Data Lake Storage Gen1-erőforrások kezelése

Data Lake Storage Gen1 erőforrások is kezelhetők a következő műveletekkel:
*   Data Lake Storage Gen1 erőforrások tallózása, több Data Lake Storage Gen1 fiókban.  
*   Kapcsolati karakterlánc használatával csatlakozhat, és közvetlenül kezelheti a Data Lake Storage Gen1. 
*   Tekintse meg a Data Lake Storage Gen1 erőforrásokat alatt az ACL-lel a mások által megosztott **helyi és csatolt**.
*   CRUD-műveletek végrehajtása fájlokon vagy mappákon: rekurzív mappák és több kijelölt fájl támogatása. 
*   Mappák húzása és hozzáadása a legutóbbi helyek gyors eléréséhez. Ez a művelet ugyanúgy működik, mint az asztali gépek fájlkezelőjében. 
*   Másolja ki és a egy Data Lake Storage Gen1 hivatkozás megnyitása egyetlen kattintással a Storage Explorerben. 
*   A tevékenységnapló megjelenítése a jobb alsó panelen a tevékenységek állapotának megtekintéséhez.
*   Mappastatisztikák és fájltulajdonságok megjelenítése.

## <a name="manage-resources-in-azure-storage-explorer"></a>Erőforrások kezelése az Azure Storage Explorerben
Miután létrehozott egy Data Lake Storage Gen1 fiókot, akkor a következőket teheti:

* Feltölthet mappákat és fájlokat, valamint letölthet és megnyithat erőforrásokat a helyi számítógépen.
* Elemeket rögzíthet a **gyorselérési eszköztáron**, új mappát hozhat létre, kimásolhatja az URL-címet, vagy kijelölhet mindent.
* Másolhat és beilleszthet, átnevezhet, törölhet, mappastatisztikákat tekinthet meg és frissíthet.

A következő cikkek bemutatják, hogyan kezelheti az erőforrásokat egy Data Lake Storage Gen1 fiókot. Kövesse a végrehajtani kívánt művelet lépéseit.

### <a name="upload-files"></a>Fájlok feltöltése

1. A fő ablaktábla eszköztárán válassza a **Feltöltés**, majd a legördülő menüből a **Fájlok feltöltése** lehetőséget.

   ![„Fájlok feltöltése” menüelem](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-files-menu.png) 

2. A **Feltölteni kívánt fájlok kiválasztása** párbeszédpanelen válassza ki a feltölteni kívánt fájlokat.

   ![Fájlfeltöltés párbeszédpanele](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-files-dialog.png)

3. A feltöltés elindításához válassza a **Megnyitás** elemet.

### <a name="upload-a-folder"></a>Mappa feltöltése

1. A fő ablaktábla eszköztárán válassza a **Feltöltés**, majd a legördülő menüből a **Mappák feltöltése** lehetőséget.

   ![„Mappa feltöltése” menüelem](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-menu.png) 
     
2. A **Feltölteni kívánt mappa kiválasztása** párbeszédpanelen válassza ki a feltölteni kívánt mappát. Kattintson a **Mappa kiválasztása** lehetőségre.

   ![Mappafeltöltés párbeszédpanele](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-dialog.png)      

   Megkezdődik a feltöltés.

   ![Feltöltés folyamatban párbeszédpanel](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-drag.png) 

> [!NOTE] 
> A feltöltés elindításához közvetlenül át is húzhatja a mappákat és fájlokat a helyi számítógépen. 
       
### <a name="download-folders-or-files-to-your-local-computer"></a>Mappák vagy fájlok letöltése a helyi számítógépre

1. Válassza ki a letölteni kívánt mappákat vagy fájlokat.
2. A fő ablaktábla eszköztárán válassza a **Letöltés** elemet.
3. A **Letöltött fájlok mentési mappájának kiválasztása** párbeszédpanelen adja meg a mappa helyét és nevét.
4. Kattintson a **Mentés** gombra.

### <a name="open-a-folder-or-file-from-your-local-computer"></a>Mappa vagy fájl megnyitása a helyi számítógépről

1. Válassza ki a megnyitni kívánt mappát vagy fájlt.
2. A fő ablaktábla eszköztárán válassza a **Megnyitás** lehetőséget. Vagy kattintson a jobb gombbal a kijelölt mappára vagy fájlra, és a helyi menüben kattintson a **Megnyitás** elemre.

A rendszer letölti a fájlt, majd megnyitja a fájltípushoz társított alkalmazással. Vagy megnyílik a fájl a fő panelen.

![Megnyitott fájl](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-open.png) 

### <a name="copy-folders-or-files-to-the-clipboard"></a>Mappák és fájlok másolása a vágólapra

1. Válassza ki a másolni kívánt mappákat vagy fájlokat.
2. A fő ablaktábla eszköztárán válassza a **Másolás** lehetőséget. Vagy kattintson a jobb gombbal a kijelölt mappákra vagy fájlokra, és a helyi menüben kattintson a **Másolás** elemre.
3. A bal oldali panelen keresse meg a Data Lake Storage Gen1 egy másik fiókot, és kattintson duplán a fő ablaktáblán való megtekintéshez.
4. A fő panel eszköztárán válassza a **Beillesztés** lehetőséget egy másolat létrehozásához. Vagy kattintson a cél helyi menüjének **Beillesztés** elemére.

![Mappa másolására szolgáló kiválasztások](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-copy-paste.png)

> [!NOTE] 
> A tárolási típusok közötti másolási és beillesztési művelet nem támogatott. Data Lake Storage Gen1 mappák és fájlok másolása, és illessze be őket egy másik Data Lake Storage Gen1 fiókot. De *nem* Data Lake Storage Gen1 mappák és fájlok másolása, majd illessze be őket az Azure Blob storage- vagy fordítva.
> 
> A másolási és beillesztési művelet során letölti a mappákat vagy fájlokat a helyi számítógépre, majd feltölti őket a célhelyre. Az eszköz *nem* a háttérben hajtja végre a műveletet. A nagyméretű fájlok másolása és beillesztése lassú folyamat. A nagy teljesítményű fájlmásolási és -áthelyezési funkció optimalizálása folyamatban van.

### <a name="delete-folders-or-files"></a>Mappák vagy fájlok törlése

1. Válassza ki a törölni kívánt mappákat vagy fájlokat.
2. A fő ablaktábla eszköztárán válassza a **Törlés** parancsot. Vagy kattintson a jobb gombbal a kijelölt mappákra vagy fájlokra, és a helyi menüben kattintson a **Törlés** elemre.
3. A megerősítési párbeszédpanelen válassza az **Igen** lehetőséget.

![Mappa törlésére szolgáló kiválasztások](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-delete.png)

### <a name="pin-to-quick-access"></a>Rögzítés gyorseléréshez

1. Válassza ki a rögzíteni kívánt mappát.
2. A fő panel eszköztárán válassza a **Rögzítés gyorseléréshez** elemet.

   A bal oldali panelen a kiválasztott mappát a rendszer hozzáadja a **gyorselérési** csomóponthoz.

   ![Mappa gyorseléréshez való rögzítésére szolgáló kiválasztások](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-quick-access.png)

Miután rögzített egy mappát a **gyorselérési** csomóponton, könnyen hozzáférhet az erőforrásokhoz.

### <a name="use-deep-links"></a>Mélyhivatkozások használata
Az URL-címeket egyszerűen beírhatja a Fájlkezelő vagy a böngésző címsorába. Ezután automatikusan elindul a Storage Explorer.exe, és megjeleníti az URL-cím helyét.

![Mélyhivatkozás a Fájlkezelőben](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-deep-link.png)


## <a name="next-steps"></a>További lépések
* A [Storage Explorer legújabb kibocsátási megjegyzéseinek és videóinak megtekintése](http://www.storageexplorer.com).
* Megismerheti, hogyan [kezelhető az Azure Cosmos DB az Azure Storage Explorerben](https://docs.microsoft.com/azure/cosmos-db/storage-explorer).
* [A Storage Explorer használatának első lépései](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).
* [Ismerkedés az Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview).
* Tekintse meg ezt a [YouTube-videót az Azure Cosmos DB az Azure Storage Explorerben történő használatáról](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be).
