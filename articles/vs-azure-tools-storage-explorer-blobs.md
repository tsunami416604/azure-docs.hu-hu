---
title: Az Azure Blob Storage erőforrásainak kezelése a Storage Explorer rel | Microsoft dokumentumok
description: Azure Blob-tárolók és -blobok kezelése a Storage Explorer segítségével
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
ms.date: 05/21/2019
ms.author: cawa
ms.openlocfilehash: 56c20c995a95058b5039b7268c7b7b1426e900fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244393"
---
# <a name="manage-azure-blob-storage-resources-with-storage-explorer"></a>Az Azure Blob Storage erőforrásainak kezelése a Storage Explorer segítségével

## <a name="overview"></a>Áttekintés

[Az Azure Blob Storage](storage/blobs/storage-dotnet-how-to-use-blobs.md) egy olyan szolgáltatás, amely nagy mennyiségű strukturálatlan adatok, például szöveges vagy bináris adatok tárolására szolgál, amelyek a világ bármely pontjáról http-n vagy HTTPS-en keresztül érhetők el.
A Blob Storage segítségével bárki számára nyilvánosan elérhetővé tehet adatokat, vagy privát módon tárolhat alkalmazásadatokat. Ebben a cikkben megtudhatja, hogyan használhatja a Storage Explorer blob tárolók és blobok használatával.

## <a name="prerequisites"></a>Előfeltételek

A cikkben leírt lépések elvégzéséhez a következőkre lesz szüksége:

* [A Storage Explorer letöltése és telepítése](https://www.storageexplorer.com)
* [Csatlakozás Azure-tárfiókhoz vagy -szolgáltatáshoz](vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service)

## <a name="create-a-blob-container"></a>Blobtároló létrehozása

Minden blobok kell tartózkodnia egy blob tárolóban, amely egyszerűen a blobok logikai csoportosítása. Egy fiók korlátlan számú tárolót tartalmazhat, és minden tároló korlátlan számú blobot tárolhat.

Az alábbi lépések bemutatják, hogyan hozhat létre egy blob tárolót a Storage Explorer ben.

1. Nyissa meg a Storage Explorert.
2. A bal oldali ablaktáblában bontsa ki a tárfiókot, amelyen belül létre kívánja hozni a blob tárolót.
3. Kattintson a jobb gombbal a **Blob Containers elemre,** és - a helyi menüben – válassza a **Blob Container létrehozása parancsot.**

   ![Blob-tárolók környezetmenüjének létrehozása][0]
4. Egy szövegdoboz jelenik meg a **Blob Containers** mappa alatt. Adja meg a blobtároló nevét. A blobtárolók elnevezésére vonatkozó szabályokról és korlátozásokról a [Tároló létrehozása című](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container) témakörben talál.

   ![Blob tárolók létrehozása szövegmező][1]
5. Nyomja **meg az Enter,** ha kész a blob tároló létrehozásához, vagy **esc** megszakíthatja. Miután a blob tároló sikeresen létrejött, a blob tárolók mappában jelenik meg a kijelölt tárfiók **blob tárolók** mappában.

   ![Blob tároló létrehozva][2]

## <a name="view-a-blob-containers-contents"></a>Blobtároló tartalmának megtekintése

Blob-tárolók blobokat és mappákat tartalmaznak (amelyek blobokat is tartalmazhatnak).

Az alábbi lépések bemutatják, hogyan tekintheti meg egy blobtároló tartalmát a Storage Explorer ben:

1. Nyissa meg a Storage Explorert.
2. A bal oldali ablaktáblában bontsa ki a megtekinteni kívánt blobtárolót tartalmazó tárfiókot.
3. Bontsa ki a tárfiók **Blob-tárolóit.**
4. Kattintson a jobb gombbal a megtekinteni kívánt blobtárolóra, és – a helyi menüből – válassza a **Blob-tárolószerkesztő megnyitása parancsot.**
   A megtekinteni kívánt blobtárolóra is duplán kattinthat.

   ![Blob tárolószerkesztő helyi menüjének megnyitása][19]
5. A fő ablaktábla megjeleníti a blob tároló tartalmát.

   ![Blob-tároló szerkesztő][3]

## <a name="delete-a-blob-container"></a>Blob-tároló törlése

Blob-tárolók könnyen létrehozható és törölhető, ha szükséges. (Az egyes blobok törlését a [Blobok kezelése a blobtárolóban](#managing-blobs-in-a-blob-container)című szakaszban tekintse meg.)

Az alábbi lépések bemutatják, hogyan törölhet egy blobtárolót a Storage Explorer ben:

1. Nyissa meg a Storage Explorert.
2. A bal oldali ablaktáblában bontsa ki a megtekinteni kívánt blobtárolót tartalmazó tárfiókot.
3. Bontsa ki a tárfiók **Blob-tárolóit.**
4. Kattintson a jobb gombbal a törölni kívánt blobtárolóra, és - a helyi menüből - válassza a **Törlés parancsot.**
   A Törlés billentyű **lenyomásával** is törölheti a jelenleg kijelölt blobtárolót.

   ![Blob-tároló helyi menüjének törlése][4]
5. Válassza az **Igen** lehetőséget a megerősítési párbeszédpanelen.

   ![Blob-tároló megerősítése][5]

## <a name="copy-a-blob-container"></a>Blob-tároló másolása

A Storage Explorer lehetővé teszi, hogy egy blobtárolót másoljon a vágólapra, majd illessze be a blobtárolót egy másik tárfiókba. (Az egyes blobok másolásának módjáról tekintse meg a [Blob-tárolóban lévő blobok kezelése](#managing-blobs-in-a-blob-container)című szakaszt.)

A következő lépések bemutatják, hogyan másolhat egy blob tárolóegyik tárfiókból a másikba.

1. Nyissa meg a Storage Explorert.
2. A bal oldali ablaktáblában bontsa ki a blobtárolót tartalmazó tárfiókot.
3. Bontsa ki a tárfiók **Blob-tárolóit.**
4. Kattintson a jobb gombbal a másolni kívánt blobtárolóra, és – a helyi menüből – válassza a **Blob-tároló másolása parancsot.**

   ![Blob-tároló helyi menüjének másolása][6]
5. Kattintson a jobb gombbal a kívánt "cél" tárfiókra, amelybe be szeretné illeszteni a blobtárolót, és – a helyi menüből – válassza a **Blob-tároló beillesztése parancsot.**

   ![Blob-tároló helyi menüjének beillesztése][7]

## <a name="get-the-sas-for-a-blob-container"></a>SAS lekérése blob tárolóhoz

A [közös hozzáférésű jogosultságkód (SAS)](storage/common/storage-dotnet-shared-access-signature-part-1.md) delegált hozzáférést biztosít a tárfiókon lévő erőforrásokhoz.
Ez azt jelenti, hogy egy adott időszakra megadhatja az ügyfeleknek a tárfiókban lévő objektumokra vonatkozó engedélyek bizonyos készletét a tár hozzáférési kulcsainak megosztása nélkül.

Az alábbi lépések bemutatják, hogyan hozhat létre Egy SAS egy blob tároló:

1. Nyissa meg a Storage Explorert.
2. A bal oldali ablaktáblában bontsa ki a blobtárolót tartalmazó tárfiókot, amelyhez SAS-t szeretne beszerezni.
3. Bontsa ki a tárfiók **Blob-tárolóit.**
4. Kattintson a jobb gombbal a kívánt blobtárolóra, és – a helyi menüből – válassza a **Megosztott hozzáférésű aláírás beírása parancsot.**

   ![A SAS helyi menüjének beszereznie][8]
5. A **Közös hozzáférésű jogosultságkód** párbeszédpanelen adja meg a szabályzatot, a kezdési és a lejárati dátumokat, az időzónát és az erőforrás kívánt hozzáférési szintjeit.

   ![SAS-beállítások beszereznie][9]
6. Az SAS-beállítások megadása után válassza a **Létrehozás** lehetőséget.
7. Ekkor megjelenik egy második **megosztott hozzáférésű aláírás párbeszédpanel,** amely felsorolja a blobtárolót a tárolóerőforrás eléréséhez használható URL-címmel és QueryStrings-szel együtt.
   Válassza a **Másolás** parancsot a vágólapra másolni kívánt URL-cím mellett.

   ![SAS-URL-ek másolása][10]
8. Ha elkészült, válassza a **Bezárás** lehetőséget.

## <a name="manage-access-policies-for-a-blob-container"></a>Hozzáférési házirendek kezelése blobtárolóhoz

Az alábbi lépések bemutatják, hogyan kezelheti (adhat hozzá és távolíthatja el) a blobtároló hozzáférési szabályzatait:

1. Nyissa meg a Storage Explorert.
2. A bal oldali ablaktáblában bontsa ki a blobtárolót tartalmazó tárfiókot, amelynek hozzáférési szabályzatait kezelni szeretné.
3. Bontsa ki a tárfiók **Blob-tárolóit.**
4. Válassza ki a kívánt blobtárolót, és – a helyi menüben – válassza a **Hozzáférési házirendek kezelése parancsot.**

   ![Hozzáférési szabályzatok kezelése helyi menü][11]
5. Az **Access Policies** párbeszédpanel felsorolja a kijelölt blobtárolóhoz már létrehozott hozzáférési házirendeket.

   ![Hozzáférési házirend beállításai][12]
6. A hozzáférésiszabályzat-kezelési feladattól függően kövesse az alábbi lépéseket:

   * **Új hozzáférési szabályzat hozzáadása** – Válassza a **Hozzáadás** lehetőséget. A **Hozzáférési szabályzatok** megjeleníti az újonnan létrehozott és hozzáadott hozzáférési szabályzatot (az alapértelmezett beállításokkal).
   * **Hozzáférési házirend szerkesztése** – A kívánt módosításokat elkell készíteni, és válassza a **Mentés gombot.**
   * **Hozzáférési szabályzat eltávolítása** – Válassza az **Eltávolítás** parancsot az eltávolítani kívánt hozzáférési szabályzat mellett.

## <a name="set-the-public-access-level-for-a-blob-container"></a>Blob-tároló nyilvános hozzáférési szintjének beállítása

Alapértelmezés szerint minden blob tároló van beállítva, hogy "Nincs nyilvános hozzáférés".

A következő lépések bemutatják, hogyan adhat meg egy nyilvános hozzáférési szintet egy blob tárolóhoz.

1. Nyissa meg a Storage Explorert.
2. A bal oldali ablaktáblában bontsa ki a blobtárolót tartalmazó tárfiókot, amelynek hozzáférési szabályzatait kezelni szeretné.
3. Bontsa ki a tárfiók **Blob-tárolóit.**
4. Válassza ki a kívánt blobtárolót, és - a helyi menüből - válassza a **Nyilvános hozzáférési szint beállítása**lehetőséget.

   ![Nyilvános hozzáférési szintű helyi menü beállítása][13]
5. A **Tároló nyilvános hozzáférési szintjének beállítása** párbeszédpanelen adja meg a kívánt hozzáférési szintet.

   ![Nyilvános hozzáférési szint beállításainak megadása][14]
6. Kattintson az **Alkalmaz** gombra.

## <a name="managing-blobs-in-a-blob-container"></a>Blobok kezelése egy blobtárolóban

Miután létrehozott egy blobtárolót, feltölthet egy blobot a blobtárolóba, letölthet egy blobot a helyi számítógépre, megnyithat egy blobot a helyi számítógépen és még sok más.

A következő lépések bemutatják, hogyan kezelheti a blobok (és mappák) egy blob tárolóban.

1. Nyissa meg a Storage Explorert.
2. A bal oldali ablaktáblában bontsa ki a kezelni kívánt blob-tárolót tartalmazó tárfiókot.
3. Bontsa ki a tárfiók **Blob-tárolóit.**
4. Kattintson duplán a megtekinteni kívánt blobtárolóra.
5. A fő ablaktábla megjeleníti a blob tároló tartalmát.

   ![Blob-tároló megtekintése][3]
6. A fő ablaktábla megjeleníti a blob tároló tartalmát.
7. Kövesse az alábbi lépéseket a végrehajtani kívánt feladattól függően:

   * **Fájlok feltöltése blobtárolóba**

     1. A fő ablaktábla eszköztárán válassza a **Feltöltés**, majd a legördülő menüből a **Fájlok feltöltése** lehetőséget.

        ![Fájlok feltöltése menü][15]
     2. A **Fájlok feltöltése** párbeszédpanelen válassza a **Fájlok** szövegbeviteli mező jobb oldalán lévő, három pontot (**…**) ábrázoló gombot a feltölteni kívánt fájl(ok) kiválasztásához.

        ![Fájlok feltöltési beállításai][16]
     3. Adja meg a **Blob típusának típusát.** További információt a [Tároló létrehozása](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container) című témakörben talál.
     4. Tetszés szerint adjon meg egy célmappát, amelybe a kijelölt fájl(oka)t feltölti a program. Ha a célmappa nem létezik, a rendszer létrehozza.
     5. Válassza a **Feltöltés** lehetőséget.
   * **Mappa feltöltése blobtárolóba**

     1. A fő ablaktábla eszköztárán válassza a **Feltöltés**, majd a legördülő menüből a **Mappa feltöltése** lehetőséget.

        ![Mappa feltöltése menü][17]
     2. A **Mappa feltöltése** párbeszédpanelen a **Mappa** szövegbeviteli mező jobb oldalán lévő, három pontot (**…**) ábrázoló gombbal válassza ki a mappát, amelynek a tartalmát fel kívánja tölteni.

        ![Mappa feltöltési beállításai][18]
     3. Adja meg a **Blob típusának típusát.** További információt a [Tároló létrehozása](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container) című témakörben talál.
     4. Igény szerint megadhat egy célmappát, amelybe a kiválasztott mappa tartalma fel lesz töltve. Ha a célmappa nem létezik, a rendszer létrehozza.
     5. Válassza a **Feltöltés** lehetőséget.
   * **Blob letöltése a helyi számítógépre**

     1. Válassza ki a letölteni kívánt blobot.
     2. A fő ablaktábla eszköztárán válassza a **Letöltés** elemet.
     3. A **Adja meg, hová mentse a letöltött blob** párbeszédablakban adja meg a helyet, ahol a blob ot le szeretné tölteni, és a nevet, amelyet megadni szeretne.  
     4. Kattintson a **Mentés** gombra.
   * **Blob megnyitása a helyi számítógépen**

     1. Válassza ki a megnyitni kívánt blobot.
     2. A fő ablaktábla eszköztárán válassza a **Megnyitás** lehetőséget.
     3. A blob lesz letöltve, és a blob alapjául szolgáló fájltípushoz társított alkalmazás használatával lesz letöltve és megnyitva.
   * **Blob másolása a vágólapra**

     1. Válassza ki a másolni kívánt blobot.
     2. A fő ablaktábla eszköztárán válassza a **Másolás** lehetőséget.
     3. A bal oldali ablaktáblában keresse meg egy másik blobtárolót, és kattintson rá duplán a főablaktáblán való megtekintéséhez.
     4. A fő ablaktábla eszköztárán válassza a **Beillesztés** lehetőséget a blob másolatának létrehozásához.
   * **Blob törlése**

     1. Válassza ki a törölni kívánt blobot.
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
