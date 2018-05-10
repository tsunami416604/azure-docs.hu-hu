---
title: Azure Data Lake Store-erőforrások kezelése az Azure Storage Explorerben
description: Megismerheti az Azure Data Lake Store-adatok és -erőforrások elérésének és kezelésének módját az Azure Storage Explorerben
Keywords: Azure Data Lake Store, Azure Storage Explorer
services: Data Lake Store
documentationcenter: ''
author: jejiang
manager: DJ
editor: Jenny Jiang
ms.assetid: ''
ms.service: Data Lake Store
ms.custom: Azure Data Lake Store
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 02/05/2018
ms.author: jejiang
ms.openlocfilehash: 396452faf36bc43a185fd6492f952b7bfd9511cc
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="manage-azure-data-lake-store-resources-by-using-storage-explorer"></a>Azure Data Lake Store-erőforrások kezelése a Storage Explorerrel

Az [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) szolgáltatás nagy mennyiségű strukturálatlan adat, például szövegek vagy bináris adatok tárolására használható. HTTP-n vagy HTTPS-en keresztül bárhonnan hozzáférhet az adataihoz. A Data Lake Store az Azure Storage Explorerben lehetővé teszi Data Lake Store-adatok és -erőforrások elérését és kezelését, az egyéb Azure-entitásokkal (pl. blobokkal és üzenetsorokkal) együtt. Mostantól ugyanazon eszközzel, egy helyen kezelheti a különböző Azure-entitásokat.

A megoldás másik előnye, hogy nincs szükség előfizetési engedélyre a Data Lake Store-adatok kezeléséhez. A Storage Explorerben a Data Lake Store-útvonal csatolható a **Helyi és csatolt** csomóponthoz, ha a valaki ezt engedélyezi.

## <a name="prerequisites"></a>Előfeltételek
A cikkben leírt lépések elvégzéséhez a következő előfeltételekre lesz szüksége:

*   Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial).
*   Azure Data Lake Store-fiók. A fióklétrehozás módjával kapcsolatban tekintse meg [az Azure Data Lake Store használatának első lépéseit ismertető](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal) témakört.

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

Sikeresen csatlakoztatta az Azure Data Lake Store-t az Azure-előfizetéséhez.

## <a name="connect-to-data-lake-store"></a>Csatlakozás a Data Lake Store-hoz
Az előfizetésében nem szereplő erőforrásokhoz is hozzáférhet, ha valaki megadja az erőforrások URI-ját. Ezután csatlakozhat a Data Lake Store-hoz az URI segítségével, a bejelentkezés után.
1. Nyissa meg a Storage Explorert.
2. A bal oldali panelen bontsa ki a **Helyi és csatolt** elemet.
3. Kattintson a jobb gombbal a **Data Lake Store** elemre, és válassza a **Csatlakozás a Data Lake Store-hoz** lehetőséget.

      ![„Csatlakozás a Data Lake Store-hoz” a helyi menün](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-uri-attach.png)

4. Adja meg az URI-t. Az eszköz a megadott URL-címre lép.

      ![„Csatlakozás a Data Lake Store-hoz” párbeszédpanel, az URI beírására szolgáló szövegmezővel](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

      ![A Data Lake Store-hoz történő csatlakozás eredménye](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-attach-finish.png)

## <a name="view-an-azure-data-lake-store-accounts-contents"></a>Azure Data Lake Store-fiók tartalmának megtekintése
Az Azure Data Lake Store-fiókok erőforrásai között mappák és fájlok találhatóak.

A következő lépések bemutatják, hogyan tekinthető meg egy Data Lake Store-fiók tartalma a Storage Explorerben:

1. Nyissa meg a Storage Explorert.
2. A bal oldali panelen bontsa ki a megtekinteni kívánt Azure Data Lake Store-fiókot tartalmazó előfizetést.
3. Bontsa ki a **Data Lake Store** elemet.
4. Kattintson a jobb gombbal a megtekinteni kívánt Azure Data Lake Store-fiók csomópontjára, majd válassza a **Megnyitás** lehetőséget. Vagy kattintson duplán a Data Lake Store-fiókra a megnyitásához. 
   
   A fő panel megjeleníti a Data Lake Store-fiók tartalmát.

   ![Fő panel a mappák listájával](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-toolbar-mainpane.png) 

## <a name="manage-resources-in-azure-data-lake-store"></a>Az Azure Data Lake Store-ban található erőforrások kezelése

Az Azure Data Lake Store-erőforrásokat a következő műveletekkel kezelheti:
*   Data Lake Store-erőforrások tallózása, több Azure Data Lake-fiókon keresztül.  
*   Kapcsolati karakterlánc használata a Data Lake Store-hoz való csatlakozáshoz és annak közvetlen kezeléséhez. 
*   A mások által az ACL-lel megosztott Data Lake Store-erőforrások megtekintése a **Helyi és csatolt** területen.
*   CRUD-műveletek végrehajtása fájlokon vagy mappákon: rekurzív mappák és több kijelölt fájl támogatása. 
*   Mappák húzása és hozzáadása a legutóbbi helyek gyors eléréséhez. Ez a művelet ugyanúgy működik, mint az asztali gépek fájlkezelőjében. 
*   Azure Data Lake-hiperhivatkozás másolása és megnyitása egyetlen kattintással a Storage Explorerben. 
*   A tevékenységnapló megjelenítése a jobb alsó panelen a tevékenységek állapotának megtekintéséhez.
*   Mappastatisztikák és fájltulajdonságok megjelenítése.

## <a name="manage-resources-in-azure-storage-explorer"></a>Erőforrások kezelése az Azure Storage Explorerben
Egy Azure Data Lake Store-fiók létrehozása után a következőket teheti:

* Feltölthet mappákat és fájlokat, valamint letölthet és megnyithat erőforrásokat a helyi számítógépen.
* Elemeket rögzíthet a **gyorselérési eszköztáron**, új mappát hozhat létre, kimásolhatja az URL-címet, vagy kijelölhet mindent.
* Másolhat és beilleszthet, átnevezhet, törölhet, mappastatisztikákat tekinthet meg és frissíthet.

Az alábbi pontokban áttekintheti, hogyan kezelheti az erőforrásokat az Azure Data Lake Store-fiókokban. Kövesse a végrehajtani kívánt művelet lépéseit.

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
3. A bal oldali panelen lépjen egy másik Data Lake Store-fiókra, és kattintson rá duplán a fő panelen való megtekintéséhez.
4. A fő panel eszköztárán válassza a **Beillesztés** lehetőséget egy másolat létrehozásához. Vagy kattintson a cél helyi menüjének **Beillesztés** elemére.

![Mappa másolására szolgáló kiválasztások](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-copy-paste.png)

> [!NOTE] 
> A tárolási típusok közötti másolási és beillesztési művelet nem támogatott. Másolhatja a Data Lake Store-mappákat és -fájlokat, és beillesztheti őket egy másik Data Lake Store-fiókba. *Nem* másolhat azonban át Data Lake Store-mappákat vagy -fájlokat egy Azure Blob Storage-ba, vagy fordítva.
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
* [Az Azure Data Lake Store használatának első lépései](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview).
* Tekintse meg ezt a [YouTube-videót az Azure Cosmos DB az Azure Storage Explorerben történő használatáról](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be).
