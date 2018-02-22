---
title: "Azure Data Lake Store-erőforrások kezelése az Azure Storage Explorerben"
description: "Lehetővé teszi a felhasználók számára az ADLS-adatok és -erőforrások elérését és kezelését az Azure Storage Explorerben"
Keywords: Azure Data Lake Store, Azure Storage Explorer
services: Data Lake Store
documentationcenter: 
author: jejiang
manager: DJ
editor: Jenny Jiang
ms.assetid: 
ms.service: Data Lake Store
ms.custom: Azure Data Lake Store
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 02/05/2018
ms.author: jejiang
ms.openlocfilehash: 4c69acebceac6719cf3f12ff0a93879f1b5a8943
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="manage-azure-data-lake-store-resources-with-storage-explorer-preview"></a>Azure Data Lake Store-erőforrások kezelése a Storage Explorer (előzetes verzió) használatával

Az [Azure Data Lake Store (ADLS)](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-overview) szolgáltatás nagy mennyiségű strukturálatlan adat, például szövegek vagy bináris adatok tárolására használható. A felhasználók HTTP-n vagy HTTPS-en keresztül bárhonnan hozzáférhetnek az adataikhoz. Az ADLS az Azure Storage Explorerben lehetővé teszi a felhasználók számára, hogy elérjék és kezeljék az ADLS-adataikat és -erőforrásaikat az egyéb Azure-entitásokkal (pl. blob és üzenetsor) együtt. Mostantól a felhasználók ugyanazon eszközzel, egy helyen kezelhetik a különböző Azure-entitásokat.

A megoldás másik előnye, hogy a felhasználóknak nincs szükségük feliratkozási engedélyre az ADLS-adatok kezeléséhez. A Storage Explorerben a felhasználók csatolhatják az ADLS-útvonalat a „helyi és csatolt” csomóponthoz, ha a többiek ezt engedélyezik.

## <a name="prerequisites"></a>Előfeltételek
A cikkben leírt lépések elvégzéséhez a következő előfeltételekre lesz szüksége:

*   Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial).
*   Azure Data Lake Store-fiók. A fióklétrehozás módjával kapcsolatban tekintse meg [az Azure Data Lake Store használatának első lépéseit ismertető](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-get-started-portal) témakört.

## <a name="installation"></a>Telepítés

Az Azure Storage Explorer legújabb összetevői innen telepíthetők: [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). Mostantól a Windows-, Linux- és MAC-alapú verzió is támogatott.

## <a name="connect-to-an-azure-subscription"></a>Csatlakozás Azure-előfizetéshez

1. Az **Azure Storage Explorer** telepítését követően kattintson a bal oldali **beépülő modul** ikonra, ahogy az az alábbi képen is látható.
       
   ![Beépülő modul ikon](./media/data-lake-store-in-storage-explorer/plug-in-icon.png)
 
2. Válassza az **Azure-fiók hozzáadása** elemet, majd kattintson a **Bejelentkezés** gombra.

   ![Csatlakozás Azure-előfizetéshez](./media/data-lake-store-in-storage-explorer/connect-to-azure-subscription.png)

2. Az **Azure bejelentkezési** párbeszédpanelen válassza a **Bejelentkezés** elemet, majd adja meg Azure hitelesítő adatait.

    ![Bejelentkezés](./media/data-lake-store-in-storage-explorer/sign-in.png)

3. Válassza ki az előfizetést a listából, majd kattintson az **Alkalmaz** gombra.

    ![Alkalmaz](./media/data-lake-store-in-storage-explorer/apply-subscription.png)

    Az Explorer panel frissül, és megjeleníti a kiválasztott előfizetéshez tartozó fiókokat.

    ![Fióklista](./media/data-lake-store-in-storage-explorer/account-list.png)

    Sikeresen csatlakoztatta az **Azure Data Lake Store-t** az Azure-előfizetéshez.

## <a name="connect-to-data-lake-store"></a>Csatlakozás a Data Lake Store-hoz
Előfordulhat, hogy szeretne hozzáférni olyan erőforrásokhoz, amelyek nem szerepelnek az előfizetésében, de mások biztosítják az erőforrások URI-jének lekéréséhez szükséges engedélyt. Ilyen esetben a bejelentkezés után csatlakozhat a Data Lake Store-hoz az URI segítségével. Tekintse át a következő lépéseket.
1. Nyissa meg a Tártallózót (előzetes verzió).
2. A bal oldali panelen bontsa ki a **Helyi és csatolt** elemet.
3. Kattintson a jobb gombbal a **Data Lake Store** elemre, és a helyi menüből válassza a **Csatlakozás a Data Lake Store-hoz…** lehetőséget.

      ![csatlakozás a Data Lake Store-hoz a helyi menüben](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-uri-attach.png)

4. Adja meg az URI-t. Ezután az eszköz a megadott URL helyére lép.

      ![csatlakozás a Data Lake Store-hoz – kontextus párbeszédpanel](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

      ![csatlakozás a Data Lake Store-hoz – eredmény](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-attach-finish.png)

## <a name="view-an-azure-data-lake-store-accounts-contents"></a>Azure Data Lake Store-fiók tartalmának megtekintése
Az Azure Data Lake Store-fiókok erőforrásai között mappák és fájlok találhatóak.

A következő lépések bemutatják, hogyan tekinthető meg egy ADLS-fiók tartalma a Storage Explorer előzetes verziójában:

1. Nyissa meg a Tártallózót (előzetes verzió).
2. A bal oldali panelen bontsa ki a megtekinteni kívánt Azure Data Lake Store-fiókot tartalmazó előfizetést.
3. Bontsa ki a **Data Lake Store** elemet.
4. Kattintson a jobb gombbal a megtekinteni kívánt Azure Data Lake Store-fiók csomópontjára, majd a helyi menüből válassza a **Megnyitás** lehetőséget. Vagy kattintson duplán az ADLS-fiókra a megnyitásához. 
5. A fő panel megjeleníti az ADLS-fiók tartalmát.

     ![fő panel](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-toolbar-mainpane.png) 

## <a name="azure-data-lake-store-resources-management"></a>Az Azure Data Lake Store-erőforrások kezelése

Az Azure Data Lake Store-erőforrásokat a következő műveletekkel kezelheti:
*   Az ADLS-erőforrások navigálása ADL-fiókok között.  
*   Kapcsolati karakterlánc használata az ADLS-hez való csatlakozáshoz és annak közvetlen kezeléséhez 
*   A mások által az ACL-lel megosztott ADLS-erőforrások megtekintése a Helyi és csatolt területen
*   CRUD-műveletek végrehajtása fájlokon vagy mappákon: rekurzív mappák és többszörösen kijelölt fájlok támogatása. 
*   Mappák húzása és hozzáadása a gyorselérési eszköztárhoz és a legutóbbi helyekhez, pont úgy, mint az asztali gépek fájlkezelőjében 
*   Az ADL-hiperhivatkozás másolása és megnyitása egyetlen kattintással a Storage Explorerben 
*   A tevékenységnapló megjelenítése a jobb alsó panelen a tevékenységek állapotának megtekintéséhez.
*   Mappastatisztikák és fájltulajdonságok megjelenítése

## <a name="manage-resources-in-azure-storage-explorer"></a>Erőforrások kezelése az Azure Storage Explorerben
Miután létrehozott egy Azure Data Lake Store-fiókot, feltölthet mappákat és fájlokat, valamint letölthet és megnyithat erőforrásokat a helyi számítógépen. Emellett elemeket rögzíthet a gyorselérési eszköztáron, új mappát hozhat létre, másolhatja az URL-t vagy kijelölhet mindent. Ezen felül lehetősége van másolásra, beillesztésre, átnevezésre, törlésre, a mappastatisztikák megtekintésére és frissítésre.

Az alábbi pontokban áttekintheti, hogyan kezelheti az erőforrásokat az Azure Data Lake Store-fiókokban. Kövesse az alábbi lépéseket a végrehajtani kívánt feladattól függően:
   * **Fájlok feltöltése**

     1. A fő ablaktábla eszköztárán válassza a **Feltöltés**, majd a legördülő menüből a **Fájlok feltöltése…** lehetőséget.

        ![Fájlok feltöltése menü](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-files-menu.png) 

     2. A **Feltölteni kívánt fájlok kiválasztása** párbeszédpanelen válassza ki a feltölteni kívánt fájlokat.

        ![Mappa feltöltése párbeszédpanel](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-files-dialog.png)

     3. A feltöltés elindításához válassza a **Megnyitás** elemet.
   * **Mappa feltöltése**

     1. A fő panel eszköztárán válassza a **Feltöltés**, majd a legördülő menüből a **Mappa feltöltése…** lehetőséget.

        ![Mappa feltöltése menü](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-menu.png) 
     2. A **Feltölteni kívánt mappa kiválasztása** párbeszédpanelen válassza ki a feltölteni kívánt mappát.

        ![Mappa feltöltése párbeszédpanel](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-dialog.png)      

     3. A mappa feltöltésének megkezdéséhez válassza a **Mappa kiválasztása** elemet.

        ![Mappa feltöltése párbeszédpanel](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-drag.png) 

      > [!NOTE] 
          > A feltöltéshez közvetlenül is húzhatja a mappákat és fájlokat a helyi számítógépen. 
       
   * **Mappák vagy fájlok letöltése a helyi számítógépre**

     1. Válassza ki a letölteni kívánt mappákat vagy fájlokat.
     2. A fő ablaktábla eszköztárán válassza a **Letöltés** elemet.
     3. A **Letöltött fájlok mentési mappájának kiválasztása** párbeszédpanelen adja meg a helyet, ahová le szeretné tölteni a mappákat vagy a fájlokat, és nevezze el őket.
     4. Kattintson a **Mentés** gombra.
   * **Mappa vagy fájl megnyitása a helyi számítógépről**

     1. Válassza ki a megnyitni kívánt mappát vagy fájlt.
     2. A fő panel eszköztárán válassza a **Megnyitás** lehetőséget, vagy kattintson a jobb gombbal a kijelölt mappára vagy fájlra, és a helyi menüben kattintson a **Megnyitás** lehetőségre.
     3. A rendszer letölti a fájlt, majd megnyitja a fájltípushoz társított alkalmazással. Vagy megnyílik a fájl a fő panelen.

        ![fájl megnyitása](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-open.png) 

   * **Mappák és fájlok másolása a vágólapra**

     1. Válassza ki a másolni kívánt mappákat vagy fájlokat.
     2. A fő panel eszköztárán válassza a **Másolás** lehetőséget, vagy kattintson a jobb gombbal a kijelölt mappákra vagy fájlokra, és a helyi menüben kattintson a **Másolás** lehetőségre.
     3. A bal oldali panelen lépjen egy másik ADLS-fiókra, és kattintson rá duplán a fő panelen való megtekintéséhez.
     4. A fő panel eszköztárán válassza a **Beillesztés** lehetőséget egy másolat létrehozásához. Vagy kattintson a helyi menü **Beillesztés** elemére a célon.

        ![mappa vagy fájl másolása és beillesztése](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-copy-paste.png)

      > [!NOTE] 
          > + A különböző tárolási típusok közötti másolás és beillesztés **nem** támogatott. Másolhatja az ADLS-mappákat és -fájlokat, és beillesztheti őket egy másik ADLS-fiókba. **Nem** másolhat azonban át ADLS-mappákat vagy -fájlokat egy Blob Storage-ba vagy fordítva. 
          > + A másolás és beillesztés során letölti a mappákat vagy fájlokat a helyi számítógépre, majd feltölti a célhelyre. Az eszköz **nem** a háttérben hajtja végre a műveletet. A nagyméretű fájlok másolása és beillesztése lassú folyamat. A nagy teljesítményű fájlmásolási és -áthelyezési funkció optimalizálása folyamatban van.
   * **Mappák vagy fájlok törlése**

     1. Válassza ki a törölni kívánt mappákat vagy a fájlokat.
     2. A fő panel eszköztárán válassza a **Törlés** lehetőséget, vagy kattintson a jobb gombbal a kijelölt mappákra vagy fájlokra, és a helyi menüben kattintson a **Törlés** lehetőségre.
     3. A megerősítési párbeszédpanelen válassza az **Igen** lehetőséget.

        ![mappa vagy fájl másolása és beillesztése](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-delete.png)

   * **Rögzítés gyorseléréshez**

     1. Válassza ki a rögzíteni kívánt mappát.
     2. A fő panel eszköztárán válassza a **Rögzítés gyorseléréshez** elemet.
     3. A bal oldali panelen láthatja, hogy a kiválasztott mappát a rendszer hozzáadja a **gyorselérési** csomóponthoz.

        ![rögzítés gyorseléréshez](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-quick-access.png)
     4. A gyorselérés létrehozása után könnyen hozzáférhet az erőforrásaihoz.
   * **Mélyhivatkozások**
     1. Az URL-címeket egyszerűen beírhatja a **fájlkezelő** vagy a böngésző címsorába.
     2. Ezután automatikusan elindul a **Storage Explorer.exe**, és arra a helyre lép, amelyre a megadott URL-cím mutat.

        ![mélyhivatkozás a fájlkezelőben](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-deep-link.png)


## <a name="next-steps"></a>További lépések
* A [Storage Explorer (előzetes verzió) legújabb kibocsátási megjegyzéseinek és videóinak megtekintése](http://www.storageexplorer.com).
* Tudnivalók az [Azure Cosmos DB kezeléséről az Azure Storage Explorerben](https://docs.microsoft.com/en-us/azure/cosmos-db/storage-explorer)
* További információk a Storage Explorerről: [Ismerkedés a Storage Explorer alkalmazással (előzetes verzió)](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* Ismerkedés az Azure Data Lake Store-ral (ADLS): [Az Azure Data Lake Store áttekintése](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-overview)
* Tekintse meg a következő videót [az Azure Cosmos DB használatáról az Azure Storage Explorerben](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be)
