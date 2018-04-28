---
title: A Tártallózó alkalmazással Azure Blob Storage-erőforrások kezelése |} Microsoft Docs
description: Az Azure Blob-tárolók és Blobok a Tártallózó alkalmazással kezelése
services: storage
documentationcenter: na
author: cawa
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
ms.openlocfilehash: 2207bac1ef7cb6393d7dc173f96ddda719bcdfdf
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="manage-azure-blob-storage-resources-with-storage-explorer"></a>A Tártallózó alkalmazással Azure Blob Storage-erőforrások kezelése
## <a name="overview"></a>Áttekintés
[Az Azure Blob Storage](storage/blobs/storage-dotnet-how-to-use-blobs.md) szolgáltatás nagy mennyiségű strukturálatlan adatok, például szövegek vagy bináris adatok, hozzáfér a bárhol a világon HTTP vagy HTTPS PROTOKOLLON keresztül tárolásához.
A Blob Storage segítségével bárki számára nyilvánosan elérhetővé tehet adatokat, vagy privát módon tárolhat alkalmazásadatokat. Ebből a cikkből megtudhatja, hogyan blob tárolók és blobok a Storage-kezelővel.

## <a name="prerequisites"></a>Előfeltételek
A cikkben leírt lépések elvégzéséhez a következőkre lesz szüksége:

* [A Storage Explorer letöltése és telepítése](http://www.storageexplorer.com)
* [Csatlakozás egy Azure-tárfiókhoz vagy -szolgáltatáshoz](vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service)

## <a name="create-a-blob-container"></a>Blobtároló létrehozása
Minden BLOB egy blob tároló, amely egyszerűen blobok logikai csoportosítása kell lennie. Egy fiók korlátlan számú tárolót tartalmazhat, és minden egyes tároló korlátlan számú BLOB tárolhatja.

A következő lépések bemutatják, hogyan lehet belül Tártallózó blob tárolókat hozhat létre.

1. Nyissa meg a Storage Explorert.
2. A bal oldali ablaktáblán bontsa ki a tárfiók, amelyen belül a blob-tároló létrehozása kívánja.
3. Kattintson a jobb gombbal **Blobtárolók**, és válassza – a helyi menüből – a **Blob-tároló létrehozása**.

   ![Blob tárolók a helyi menü létrehozása][0]
4. Szövegmező alatt megjelenik a **Blobtárolók** mappa. Adja meg a blobtároló nevét. Tekintse meg a [létrehozni a tárolót, és az engedélyek beállítása](storage/blobs/storage-quickstart-blobs-dotnet.md#create-the-container-and-set-permissions) szabályok és a blob tárolók elnevezési korlátozásai információt.

   ![Szövegmező Blob tárolók létrehozása][1]
5. Nyomja le az **Enter** végzett a blob-tároló létrehozásához vagy **Esc** megszakítja a műveletet. A blob-tároló sikeres létrehozását követően megjelenik a a **Blobtárolók** a kiválasztott tárolási fiók mappáját.

   ![BLOB-tároló létrehozása][2]

## <a name="view-a-blob-containers-contents"></a>A blob-tároló tartalmának megtekintése
BLOB tárolók blobok és mappák (BLOB is tartalmazhat) tartalmazhat.

A következő lépések bemutatják egy blob tároló belül Tártallózó tartalmának megtekintése:

1. Nyissa meg a Storage Explorert.
2. A bal oldali ablaktáblán bontsa ki a tárfiók a blob-tároló tartalmazó meg szeretné tekinteni.
3. Bontsa ki a tárfiók **Blobtárolók**.
4. Kattintson a jobb gombbal a blob-tároló kíván megtekinteni, és válassza – a helyi menüből – **Blob tároló szerkesztő megnyitása**.
   Kattintson duplán a megtekinteni kívánt blobtárolóban is.

   ![Nyissa meg a blob tároló szerkesztő helyi menü][19]
5. A fő ablaktáblán jelenik meg a blob-tároló tartalmának.

   ![A BLOB-tároló szerkesztő][3]

## <a name="delete-a-blob-container"></a>A blob-tároló törlése
BLOB tárolók egyszerűen hozható létre és igény szerint törölve. (Egyes blobok törlése, tekintse át a részt történő [kezelése a blob-tárolóban lévő blobok](#managing-blobs-in-a-blob-container).)

A következő lépések bemutatják, hogyan lehet Tártallózó belül egy blob-tároló törlése:

1. Nyissa meg a Storage Explorert.
2. A bal oldali ablaktáblán bontsa ki a tárfiók a blob-tároló tartalmazó meg szeretné tekinteni.
3. Bontsa ki a tárfiók **Blobtárolók**.
4. Kattintson a jobb gombbal a blob-tároló törlése, és válassza – a helyi menüből – kíván **törlése**.
   Is **törlése** a jelenleg kijelölt blob-tároló törlése.

   ![A blob tároló helyi menü törlése][4]
5. Válassza az **Igen** lehetőséget a megerősítési párbeszédpanelen.

   ![A blob tároló megerősítő törlése][5]

## <a name="copy-a-blob-container"></a>Másolja a blob-tároló
A Tártallózó lehetővé teszi egy blob-tároló másolása a vágólapra, majd, hogy a blob-tároló illessze be egy másik tárfiókhoz. (Egyes blobot másolni, tekintse át a részt, hogyan [kezelése a blob-tárolóban lévő blobok](#managing-blobs-in-a-blob-container).)

A következő lépések bemutatják, hogyan lehet egy blob-tároló másolhat egy tárfiók egy másikra.

1. Nyissa meg a Storage Explorert.
2. A bal oldali ablaktáblán bontsa ki a tárfiók a blob-tároló tartalmazó másolni kívánja.
3. Bontsa ki a tárfiók **Blobtárolók**.
4. Kattintson a jobb gombbal a blob-tároló kívánja másolni, és válassza – a helyi menüből – **másolási Blob tároló**.

   ![Másolja a blob tároló helyi menü][6]
5. Kattintson a jobb gombbal a kívánt "target" tárfiók, amelybe a illessze be a blob-tároló, és válassza – a helyi menüből – szeretne **illessze be a Blob-tároló**.

   ![Beillesztés blob tároló helyi menü][7]

## <a name="get-the-sas-for-a-blob-container"></a>SAS lekérése blob tárolóhoz
A [közös hozzáférésű jogosultságkód (SAS)](storage/common/storage-dotnet-shared-access-signature-part-1.md) delegált hozzáférést biztosít a tárfiókon lévő erőforrásokhoz.
Ez azt jelenti, hogy egy adott időszakra megadhatja az ügyfeleknek a tárfiókban lévő objektumokra vonatkozó engedélyek bizonyos készletét a tár hozzáférési kulcsainak megosztása nélkül.

A következő lépések bemutatják egy SAS-t egy blob-tároló létrehozása:

1. Nyissa meg a Storage Explorert.
2. A bal oldali ablaktáblán bontsa ki a tárfiók a blob tároló, amely a SAS-kód lekérése kíván tartalmazó.
3. Bontsa ki a tárfiók **Blobtárolók**.
4. Kattintson a jobb gombbal a kívánt blob tároló, és válassza – a helyi menüből – **közös hozzáférésű Jogosultságkód beolvasása**.

   ![A helyi menü SAS lekérése][8]
5. A **Közös hozzáférésű jogosultságkód** párbeszédpanelen adja meg a szabályzatot, a kezdési és a lejárati dátumokat, az időzónát és az erőforrás kívánt hozzáférési szintjeit.

   ![SAS-beállítások beolvasása][9]
6. Az SAS-beállítások megadása után válassza a **Létrehozás** lehetőséget.
7. Egy második **közös hozzáférésű Jogosultságkód** párbeszédpanel fogja tartalmazni, amely tartalmazza a blob-tároló URL-cím és a tárolási erőforrások eléréséhez használhatja QueryStrings együtt.
   Válassza a **Másolás** parancsot a vágólapra másolni kívánt URL-cím mellett.

   ![SAS URL-címének másolása][10]
8. Ha elkészült, válassza a **Bezárás** lehetőséget.

## <a name="manage-access-policies-for-a-blob-container"></a>Egy blob tároló a hozzáférési házirendek kezelése
A következő lépések bemutatják, hogyan kezelheti (hozzáadása és eltávolítása) hozzáférési házirendek egy blob-tároló:

1. Nyissa meg a Storage Explorert.
2. A bal oldali ablaktáblán bontsa ki a tárfiók a blob tároló, amelynek hozzáférési házirendeket, kezelni akarja tartalmazó.
3. Bontsa ki a tárfiók **Blobtárolók**.
4. Válassza ki a kívánt blob tároló, és válassza – a helyi menüből – **hozzáférési házirendek kezelése**.

   ![Hozzáférési szabályzatok kezelése helyi menü][11]
5. A **hozzáférési házirendek** párbeszédpanel felsorolja a kijelölt blob-tároló már létre hozzáférési házirendekben.

   ![Hozzáférési házirend beállítása][12]        
6. A hozzáférésiszabályzat-kezelési feladattól függően kövesse az alábbi lépéseket:

   * **Új hozzáférési szabályzat hozzáadása** – Válassza a **Hozzáadás** lehetőséget. A **Hozzáférési szabályzatok** megjeleníti az újonnan létrehozott és hozzáadott hozzáférési szabályzatot (az alapértelmezett beállításokkal).
   * **A hozzáférési házirendek szerkesztése** – végezze el a kívánt módosításokat, és válassza ki **mentése**.
   * **Hozzáférési szabályzat eltávolítása** – Válassza az **Eltávolítás** parancsot az eltávolítani kívánt hozzáférési szabályzat mellett.

## <a name="set-the-public-access-level-for-a-blob-container"></a>A nyilvános hozzáférési szint beállítása a blob-tároló
Alapértelmezés szerint minden blob tároló "Nem nyilvános hozzáférés" értéke.

A következő lépések bemutatják, hogyan lehet egy blob-tároló nyilvános hozzáférés szintet adjon meg.

1. Nyissa meg a Storage Explorert.
2. A bal oldali ablaktáblán bontsa ki a tárfiók a blob tároló, amelynek hozzáférési házirendeket, kezelni akarja tartalmazó.
3. Bontsa ki a tárfiók **Blobtárolók**.
4. Válassza ki a kívánt blob tároló, és válassza – a helyi menüből – **nyilvános hozzáférési szint beállítása**.

   ![Állítsa be a nyilvános hozzáférés szint a helyi menü][13]
5. Az a **tároló nyilvános hozzáférési szint beállítása** párbeszédpanelen adja meg a kívánt hozzáférési szintjét.

   ![Nyilvános hozzáférés szint beállításainak megadása][14]
6. Kattintson az **Alkalmaz** gombra.

## <a name="managing-blobs-in-a-blob-container"></a>A blob-tárolóban lévő blobok kezelése
Egy blob-tároló létrehozását követően egy blob feltöltése a blob-tárolóhoz, blob letöltése a helyi számítógépen, nyissa meg a blob a helyi számítógépen, és még sok más.

A következő lépések bemutatják a blobok (és a mappák) kezelése a blob-tárolóban.

1. Nyissa meg a Storage Explorert.
2. A bal oldali ablaktáblán bontsa ki a tárfiók a blob-tároló tartalmazó felügyelni kíván.
3. Bontsa ki a tárfiók **Blobtárolók**.
4. Kattintson duplán a blob-tároló meg szeretné tekinteni.
5. A fő ablaktáblán jelenik meg a blob-tároló tartalmának.

   ![Nézet blob tároló][3]
6. A fő ablaktáblán jelenik meg a blob-tároló tartalmának.
7. Kövesse az alábbi lépéseket a végrehajtani kívánt feladattól függően:

   * **Fájlok feltöltése a blob-tároló**

     1. A fő ablaktábla eszköztárán válassza a **Feltöltés**, majd a legördülő menüből a **Fájlok feltöltése** lehetőséget.

        ![Fájlok feltöltése menü][15]
     2. A **Fájlok feltöltése** párbeszédpanelen válassza a **Fájlok** szövegbeviteli mező jobb oldalán lévő, három pontot (**…**) ábrázoló gombot a feltölteni kívánt fájl(ok) kiválasztásához.

        ![Töltse fel a fájlok beállítások][16]
     3. Adja meg, milyen típusú **Blob-típusú**. Lásd: [létrehozni a tárolót, és az engedélyek beállítása](storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-the-container) további információt.
     4. Szükség esetén adja meg, hogy a célmappa, amelybe a kijelölt fájlok lesz feltöltve. Ha a célmappa nem létezik, a rendszer létrehozza.
     5. Válassza a **Feltöltés** lehetőséget.
   * **Töltse fel egy mappát egy blob-tárolóba**

     1. A fő ablaktábla eszköztárán válassza a **Feltöltés**, majd a legördülő menüből a **Mappa feltöltése** lehetőséget.

        ![Mappa feltöltése menü][17]
     2. A **Mappa feltöltése** párbeszédpanelen a **Mappa** szövegbeviteli mező jobb oldalán lévő, három pontot (**…**) ábrázoló gombbal válassza ki a mappát, amelynek a tartalmát fel kívánja tölteni.

        ![Töltse fel a mappa beállításai][18]
     3. Adja meg, milyen típusú **Blob-típusú**. Lásd: [létrehozni a tárolót, és az engedélyek beállítása](storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-the-container) további információt.
     4. Igény szerint megadhat egy célmappát, amelybe a kiválasztott mappa tartalma fel lesz töltve. Ha a célmappa nem létezik, a rendszer létrehozza.
     5. Válassza a **Feltöltés** lehetőséget.
   * **Egy blob letöltése a helyi számítógépen**

     1. Válassza ki a letölteni kívánt blob.
     2. A fő ablaktábla eszköztárán válassza a **Letöltés** elemet.
     3. Az a **hová szeretné menteni a letöltött blob** párbeszédpanelen adja meg a helyét, a letöltött blob, és adjon neki kívánt nevét.  
     4. Kattintson a **Mentés** gombra.
   * **Nyissa meg a helyi számítógépen blob**

     1. Válassza ki a blob, nyissa meg a kívánt.
     2. A fő ablaktábla eszköztárán válassza a **Megnyitás** lehetőséget.
     3. A blob le lesznek töltve, és a blob fájl alaptípusának társított alkalmazás használatával megnyitni.
   * **A blob másolása a vágólapra**

     1. Válassza ki a másolni kívánt blob.
     2. A fő ablaktábla eszköztárán válassza a **Másolás** lehetőséget.
     3. A bal oldali ablaktáblán keresse meg egy másik blob-tároló, és azt a fő ablaktáblán megtekintéséhez kattintson rá duplán.
     4. Válassza a fő ablaktáblán eszköztár **Beillesztés** a blob másolatának létrehozásához.
   * **Blobok törléséhez**

     1. Válassza ki a törölni kívánt blob.
     2. A fő ablaktábla eszköztárán válassza a **Törlés** parancsot.
     3. Válassza az **Igen** lehetőséget a megerősítési párbeszédpanelen.

## <a name="next-steps"></a>További lépések
* A [Storage Explorer legújabb kibocsátási megjegyzéseinek és videóinak megtekintése](http://www.storageexplorer.com).
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
