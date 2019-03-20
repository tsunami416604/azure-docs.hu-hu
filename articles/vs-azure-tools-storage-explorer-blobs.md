---
title: Azure Blob Storage-erőforrások kezelése a Storage Explorer |} A Microsoft Docs
description: Kezelheti az Azure Blob-tárolók és Blobok a Storage Explorer használatával
services: storage
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: ''
ms.assetid: 2f09e545-ec94-4d89-b96c-14783cc9d7a9
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/18/2016
ms.author: cawa
ms.openlocfilehash: f46467871a5ae0147b5dc60881bda4175eabac56
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57858167"
---
# <a name="manage-azure-blob-storage-resources-with-storage-explorer"></a>Azure Blob Storage-erőforrások kezelése a Storage Explorerben
## <a name="overview"></a>Áttekintés
[Az Azure Blob Storage](storage/blobs/storage-dotnet-how-to-use-blobs.md) szolgáltatás nagy mennyiségű strukturálatlan adat, például szöveg vagy bináris adatot, amely segítségével bárhonnan elérhetők HTTP- vagy HTTPS keresztül a világon tárolására használható.
A Blob Storage segítségével bárki számára nyilvánosan elérhetővé tehet adatokat, vagy privát módon tárolhat alkalmazásadatokat. Ebből a cikkből megismerheti, hogyan használhatja a Storage Explorer a blob-tárolók és blobok lesz.

## <a name="prerequisites"></a>Előfeltételek
A cikkben leírt lépések elvégzéséhez a következőkre lesz szüksége:

* [A Storage Explorer letöltése és telepítése](https://www.storageexplorer.com)
* [Csatlakozhat egy Azure storage-fiók vagy szolgáltatás](vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service)

## <a name="create-a-blob-container"></a>Blobtároló létrehozása
Minden BLOB egy blob-tárolóba, amely egyszerűen csak a blobok logikai csoportosítása kell lennie. Egy fiók korlátlan számú tárolót tartalmazhat, és minden tároló korlátlan számú BLOB tárolhatja.

A következő lépések bemutatják, hogyan hozhat létre egy blobtárolót a Storage Explorerben.

1. Nyissa meg a Storage Explorert.
2. A bal oldali ablaktáblán bontsa ki a tárfiókot, amelyen belül szeretné a blobtároló létrehozásához.
3. Kattintson a jobb gombbal **Blobtárolók**, majd válassza a helyi menüből – **Blobtároló létrehozása**.

   ![Místní nabídka objektu blob tárolók létrehozása][0]
4. Szövegmező alatt megjelenik a **Blobtárolók** mappát. Adja meg a blobtároló nevét. Tekintse meg a [hozza létre a tárolót, valamint engedélyeket állíthat be](storage/blobs/storage-quickstart-blobs-dotnet.md#create-the-container-and-set-permissions) információ a blobtárolók elnevezésére vonatkozó szabályokat és korlátozásokat.

   ![Blob-tárolók beviteli mező létrehozása][1]
5. Nyomja meg **Enter** billentyűt a blobtároló létrehozásához vagy **Esc** megszakítja. Ha a blob-tároló sikeresen létrejött, megjelenik a a **Blob-tárolók** a kiválasztott tárfiókhoz tartozó mappában.

   ![Blob Container created][2]

## <a name="view-a-blob-containers-contents"></a>A blob-tároló tartalmának megtekintése
BLOB-tárolók a blobok és mappák (blobok is tartalmazhat) tartalmaznak.

A következő lépések bemutatják, hogyan tekintheti meg a Storage Explorer blob-tároló tartalmát:

1. Nyissa meg a Storage Explorert.
2. A bal oldali ablaktáblán bontsa ki a megtekinteni kívánt blob-tárolóban tartalmazó tárfiókot.
3. Bontsa ki a tárfiókot **Blobtárolók**.
4. Kattintson a jobb gombbal a kívánt megtekintéséhez, majd válassza a helyi menüből – a blob-tároló **Blob-tároló szerkesztő megnyitása**.
   Kattintson duplán a megtekinteni kívánt blob-tárolóban is.

   ![Nyissa meg a blob tároló szerkesztő helyi menü][19]
5. A fő panel megjeleníti a blob-tároló tartalmát.

   ![A BLOB-tároló szerkesztő][3]

## <a name="delete-a-blob-container"></a>A blob-tároló törlése
BLOB-tárolók egyszerűen hozhatja létre és igény szerint törölve. (Az egyes blobok törlése, tekintse át a részt, hogyan [kezelése a blob-tárolóban lévő blobokat](#managing-blobs-in-a-blob-container).)

A következő lépések bemutatják, hogyan törölhet egy blobtárolót a Storage Explorerben:

1. Nyissa meg a Storage Explorert.
2. A bal oldali ablaktáblán bontsa ki a megtekinteni kívánt blob-tárolóban tartalmazó tárfiókot.
3. Bontsa ki a tárfiókot **Blobtárolók**.
4. Kattintson a jobb gombbal a kívánt törölje, majd válassza a helyi menüből – a blob-tároló **törlése**.
   Lenyomásával is **törlése** a jelenleg kijelölt blob-tároló törlése.

   ![Místní nabídka objektu blob tároló törlése][4]
5. Válassza az **Igen** lehetőséget a megerősítési párbeszédpanelen.

   ![Blob tároló megerősítő törlése][5]

## <a name="copy-a-blob-container"></a>Egy blob-tárolóba másolja.
Storage Explorer lehetővé teszi, hogy egy blob-tárolóba másolja a vágólapra, és a egy másik tárfiókba történő illessze be a blob-tárolóba. (Az egyes blobok másolása, tekintse át a részt, hogyan [kezelése a blob-tárolóban lévő blobokat](#managing-blobs-in-a-blob-container).)

A következő lépések bemutatják, hogyan másolhat át egy blob-tároló egyik tárfiókból a másikba.

1. Nyissa meg a Storage Explorert.
2. A bal oldali ablaktáblán bontsa ki a másolni kívánt blob-tárolóban tartalmazó tárfiókot.
3. Bontsa ki a tárfiókot **Blobtárolók**.
4. Kattintson a jobb gombbal a kívánt másolja, majd válassza a helyi menüből – a blobtároló **Blobtároló másolási**.

   ![Másolja a tároló místní nabídka objektu blob][6]
5. Kattintson a jobb gombbal a kívánt "cél" tárfiókra, amelybe a illessze be a blob-tárolóba, majd válassza a helyi menüből – a kívánt **Blobtároló beillesztése**.

   ![Illessze be a tároló místní nabídka objektu blob][7]

## <a name="get-the-sas-for-a-blob-container"></a>SAS lekérése blob tárolóhoz
A [közös hozzáférésű jogosultságkód (SAS)](storage/common/storage-dotnet-shared-access-signature-part-1.md) delegált hozzáférést biztosít a tárfiókon lévő erőforrásokhoz.
Ez azt jelenti, hogy egy adott időszakra megadhatja az ügyfeleknek a tárfiókban lévő objektumokra vonatkozó engedélyek bizonyos készletét a tár hozzáférési kulcsainak megosztása nélkül.

A következő lépések bemutatják, hogyan hozhat létre a blobtároló SAS:

1. Nyissa meg a Storage Explorert.
2. A bal oldali ablaktáblán bontsa ki a tárfiókot, amely tartalmazza a blob-tároló, amelyhez hozzá szeretne beszerezni egy SAS.
3. Bontsa ki a tárfiókot **Blobtárolók**.
4. Kattintson a jobb gombbal a kívánt blob-tárolóba, majd válassza a helyi menüből – **közös hozzáférési jogosultságkód igénylése**.

   ![Helyi menü SAS beszerzése][8]
5. A **Közös hozzáférésű jogosultságkód** párbeszédpanelen adja meg a szabályzatot, a kezdési és a lejárati dátumokat, az időzónát és az erőforrás kívánt hozzáférési szintjeit.

   ![SAS-beállítások beolvasása][9]
6. Az SAS-beállítások megadása után válassza a **Létrehozás** lehetőséget.
7. Egy második **közös hozzáférésű Jogosultságkód** párbeszédpanel majd jelenik meg, amely felsorolja a blob-tároló URL-cím és sorolva, használhatja a tárolási erőforrások eléréséhez.
   Válassza a **Másolás** parancsot a vágólapra másolni kívánt URL-cím mellett.

   ![Copy SAS URLs][10]
8. Ha elkészült, válassza a **Bezárás** lehetőséget.

## <a name="manage-access-policies-for-a-blob-container"></a>A blob-tároló hozzáférési szabályzatainak kezelése
A következő lépések bemutatják, hogyan kezelheti a (hozzáadása és eltávolítása) egy blob-tárolóba tartozó hozzáférési szabályzatok:

1. Nyissa meg a Storage Explorert.
2. A bal oldali ablaktáblán bontsa ki a tárfiókot, amely tartalmazza a blob-tároló, amelynek a kezelni kívánt hozzáférési szabályzatait.
3. Bontsa ki a tárfiókot **Blobtárolók**.
4. Válassza ki a kívánt blob-tárolót, és válassza a helyi menüből – **hozzáférési szabályzatok kezelése**.

   ![Hozzáférési szabályzatok kezelése helyi menü][11]
5. A **hozzáférési szabályzatok** párbeszédpanel megjelennek az összes kijelölt blob-tárolóban a már létrehozott hozzáférési szabályzatok.

   ![Hozzáférési házirend beállításai][12]        
6. A hozzáférésiszabályzat-kezelési feladattól függően kövesse az alábbi lépéseket:

   * **Új hozzáférési szabályzat hozzáadása** – Válassza a **Hozzáadás** lehetőséget. A **Hozzáférési szabályzatok** megjeleníti az újonnan létrehozott és hozzáadott hozzáférési szabályzatot (az alapértelmezett beállításokkal).
   * **Hozzáférési szabályzat szerkesztése** – győződjön meg a kívánt módosításokat, és válassza ki **mentése**.
   * **Hozzáférési szabályzat eltávolítása** – Válassza az **Eltávolítás** parancsot az eltávolítani kívánt hozzáférési szabályzat mellett.

## <a name="set-the-public-access-level-for-a-blob-container"></a>Állítsa be a nyilvános hozzáférés szintje blob tárolóhoz
Alapértelmezés szerint minden blob-tároló "Nincs nyilvános hozzáférés" van beállítva.

A következő lépések bemutatják, hogyan adja meg a blob-tároló egy nyilvános hozzáférés szintje.

1. Nyissa meg a Storage Explorert.
2. A bal oldali ablaktáblán bontsa ki a tárfiókot, amely tartalmazza a blob-tároló, amelynek a kezelni kívánt hozzáférési szabályzatait.
3. Bontsa ki a tárfiókot **Blobtárolók**.
4. Válassza ki a kívánt blob-tárolót, és válassza a helyi menüből – **nyilvános hozzáférési szint beállítása**.

   ![Állítsa be a nyilvános hozzáférési szint helyi menü][13]
5. Az a **tároló nyilvános hozzáférési szint beállítása** párbeszédpanelen adja meg a kívánt hozzáférési szinttel.

   ![Nyilvános hozzáférési szint beállításainak megadása][14]
6. Kattintson az **Alkalmaz** gombra.

## <a name="managing-blobs-in-a-blob-container"></a>A blob-tárolóban lévő blobok kezelése
Miután létrehozott egy blob-tároló, blob feltöltése a blob-tárolóba, letölt egy blobot a helyi számítógépen, nyisson meg egy blobot a helyi számítógépen, és még sok más.

A következő lépések bemutatják, hogyan kezelheti a blobokat (és mappák) egy blob tárolóból.

1. Nyissa meg a Storage Explorert.
2. A bal oldali ablaktáblán bontsa ki a kezelni kívánt blob-tárolóban tartalmazó tárfiókot.
3. Bontsa ki a tárfiókot **Blobtárolók**.
4. Kattintson duplán a megtekinteni kívánt blob-tárolóba.
5. A fő panel megjeleníti a blob-tároló tartalmát.

   ![Blobtároló megtekintése][3]
6. A fő panel megjeleníti a blob-tároló tartalmát.
7. Kövesse az alábbi lépéseket a végrehajtani kívánt feladattól függően:

   * **Fájlok feltöltése a blob-tárolóba**

     1. A fő ablaktábla eszköztárán válassza a **Feltöltés**, majd a legördülő menüből a **Fájlok feltöltése** lehetőséget.

        ![Fájlok feltöltése menü][15]
     2. A **Fájlok feltöltése** párbeszédpanelen válassza a **Fájlok** szövegbeviteli mező jobb oldalán lévő, három pontot (**…**) ábrázoló gombot a feltölteni kívánt fájl(ok) kiválasztásához.

        ![A fájl beállításai][16]
     3. Adja meg a típusát **Blob típusát**. Lásd: [hozza létre a tárolót, valamint engedélyeket állíthat be](storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-the-container) további információt.
     4. Nem kötelezően megadhat egy célmappát, amelybe fel lesz töltve a kijelölt fájlt vagy fájlokat. Ha a célmappa nem létezik, a rendszer létrehozza.
     5. Válassza a **Feltöltés** lehetőséget.
   * **Mappa feltöltése a blob-tárolóba**

     1. A fő ablaktábla eszköztárán válassza a **Feltöltés**, majd a legördülő menüből a **Mappa feltöltése** lehetőséget.

        ![Mappa feltöltése menü][17]
     2. A **Mappa feltöltése** párbeszédpanelen a **Mappa** szövegbeviteli mező jobb oldalán lévő, három pontot (**…**) ábrázoló gombbal válassza ki a mappát, amelynek a tartalmát fel kívánja tölteni.

        ![Töltse fel a mappa beállításai][18]
     3. Adja meg a típusát **Blob típusát**. Lásd: [hozza létre a tárolót, valamint engedélyeket állíthat be](storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-the-container) további információt.
     4. Igény szerint megadhat egy célmappát, amelybe a kiválasztott mappa tartalma fel lesz töltve. Ha a célmappa nem létezik, a rendszer létrehozza.
     5. Válassza a **Feltöltés** lehetőséget.
   * **Letölt egy blobot a helyi számítógépre**

     1. Válassza ki a letölteni kívánt blobot.
     2. A fő ablaktábla eszköztárán válassza a **Letöltés** elemet.
     3. Az a **adja meg, hová szeretné menteni a letöltött blob** párbeszédpanelen adja meg a helyet, ahová letöltötte a blob és a neki kívánt nevét.  
     4. Kattintson a **Mentés** gombra.
   * **Nyisson meg egy blobot a helyi számítógépen**

     1. Válassza ki a megnyitni kívánt blob.
     2. A fő ablaktábla eszköztárán válassza a **Megnyitás** lehetőséget.
     3. A blob lesznek letöltve, majd megnyitja a blob fájltípushoz társított alkalmazással.
   * **Blob másolása a vágólapra**

     1. Válassza ki a másolni kívánt blob.
     2. A fő ablaktábla eszköztárán válassza a **Másolás** lehetőséget.
     3. A bal oldali panelen lépjen egy másik blob-tárolóba, és kattintson duplán a fő ablaktáblán való megtekintéshez.
     4. A fő ablaktábla eszköztárán válassza **beillesztési** a blob egy másolatának létrehozásához.
   * **Blob törlése**

     1. Válassza ki a törölni kívánt blob.
     2. A fő ablaktábla eszköztárán válassza a **Törlés** parancsot.
     3. Válassza az **Igen** lehetőséget a megerősítési párbeszédpanelen.

## <a name="next-steps"></a>További lépések
* A [Storage Explorer legújabb kibocsátási megjegyzéseinek és videóinak megtekintése](https://www.storageexplorer.com).
* Annak megismerése, hogyan [hozhat létre alkalmazásokat Azure-blobok, -táblák, -üzenetsorok és -fájlok használatával](https://azure.microsoft.com/documentation/services/storage/).

[0]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-create-context-menu.png
[1]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create.png
[2]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create-done.png
[3]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-editor.png
[4]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-context-menu.png
[5]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-confirmation.png
[6]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-copy-context-menu.png
[7]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-paste-context-menu.png
[8]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-context-menu.png
[9]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-options.png
[10]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-urls.png
[11]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-context-menu.png
[12]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-options.png
[13]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-context-menu.png
[14]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-options.png
[15]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-menu.png
[16]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-options.png
[17]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-menu.png
[18]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-options.png
[19]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-open-editor-context-menu.png
