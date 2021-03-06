---
title: Oktatóanyag az adatoknak az Azure Data Boxról, SMB-n keresztül történő másolásához | Microsoft Docs
description: Tudnivalók adatok az Azure Data Boxra másolásáról SMB-n keresztül
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 07/10/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: ecc6e1e1a543f3190e9f73512ca0b9ae45cc3fe9
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335213"
---
# <a name="tutorial-copy-data-from-azure-data-box-via-smb-preview"></a>Oktatóanyag: Adatok másolása az Azure Data Boxból SMB-n keresztül (Előzetes verzió)

Ez az oktatóanyag azt ismerteti, hogyan csatlakozhat a Data Boxhoz, illetve hogyan másolhat onnan adatokat egy helyszíni kiszolgálóra a helyi webes felhasználói felület használatával. A Data Box eszköz az Azure Storage-fiókjából exportált adatokat tartalmazza.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Előfeltételek
> * Csatlakozás a Data Boxhoz
> * Adatok másolása a Data Boxról

[!INCLUDE [Data Box feature is in preview](../../includes/data-box-feature-is-preview-info.md)]

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg az alábbiakról:

1. Megrendelt egy Azure Data Boxot.
    - Az importálási rendelésekkel kapcsolatos információkért lásd [ Az Azure Data Box megrendelése](data-box-deploy-ordered.md) című oktatóanyagot.
    - Exportálási rendelésekkel kapcsolatos információkért lásd: [Oktatóanyag: Az Azure Data Box megrendelése](data-box-deploy-export-ordered.md) című oktatóanyagot.
2. Megkapta a Data Boxot, és a portálon a megrendelés **Kézbesítve** állapotú.
3. Rendelkezik egy gazdagéppel, amelyre át kívánja másolni az adatokat a Data Boxról. A gazdaszámítógépen:
   * egy [támogatott operációs rendszernek](data-box-system-requirements.md) kell futnia;
   * egy nagy sebességű hálózathoz kell csatlakoznia. Határozottan javasoljuk, hogy legalább 10 GbE sebességű kapcsolattal rendelkezzen. Ha 10 GbE sebességű kapcsolat nem áll rendelkezésre, 1 GbE sebességű adatkapcsolat is használható, azonban ez csökkenti a másolási sebességet.

## <a name="connect-to-data-box"></a>Csatlakozás a Data Boxhoz

[!INCLUDE [data-box-shares](../../includes/data-box-shares.md)]

Ha Windows Server rendszerű gazdagépet használ, kövesse az alábbi lépéseket a Data Boxhoz történő csatlakozáshoz.

1. Az első lépés a hitelesítés elvégzése, majd a munkamenet elindítása. Lépjen a **Connect and copy** (Kapcsolódás és másolás) elemre. Válassza a **Hitelesítő adatok beszerzése** lehetőséget a tárfiókhoz társított megosztások hitelesítő adatainak eléréséhez. 

    ![Megosztások hitelesítő adatainak beszerzése](media/data-box-deploy-export-copy-data/get-share-credentials-1.png)

2. Az Access share and copy data (Megosztási és másolási adatok másolása) párbeszédpanelen másolja ki a megosztásnak megfelelő **Username** (Felhasználónév) és **Password** (Jelszó) értékeket. Kattintson az **OK** gombra.
    
    ![Megosztás hitelesítő adatainak beszerzése, a hozzáférés megosztása és az adatok másolása](media/data-box-deploy-export-copy-data/get-share-credentials-2.png)

3. A tárfiókjához (a következő példában *exportbvtdataset2* ) társított megosztások gazdagépről történő eléréséhez nyisson meg egy parancsablakot. A parancssorba írja be a következőt:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Az adatok formátumától függően a megosztások útvonalai a következők:
    - Azure-blokkblob – `\\169.254.143.85\exportbvtdataset2_BlockBlob`
    - Azure-lapblob – `\\169.254.143.85\exportbvtdataset2_PageBlob`
    - Azure Files – `\\169.254.143.85\exportbvtdataset2_AzFile`

4. Ha a rendszer kéri, adja meg a megosztás jelszavát. A következő példa bemutatja, hogyan kell csatlakozni egy megosztáshoz a fenti parancs használatával.

    ```
    C:\Users\Databoxuser>net use \\169.254.143.85\exportbvtdataset2_BlockBlob /u:exportbvtdataset2
    Enter the password for 'exportbvtdataset2' to connect to '169.254.143.85':
    The command completed successfully.
    ```

5. Nyomja le a Windows + R billentyűkombinációt. A **Futtatás** ablakban adja meg a következőt: `\\<device IP address>`. Kattintson az **OK** gombra a Fájlkezelő megnyitásához.
    
    ![Kapcsolódás a megosztáshoz a Fájlkezelővel, az eszköz IP-címének megadása](media/data-box-deploy-export-copy-data/connect-shares-file-explorer-1.png)

    A megosztásoknak ezután mappaként kell megjelenniük.
    
    ![Kapcsolódás a megosztáshoz a Fájlkezelővel, megosztások megtekintése](media/data-box-deploy-export-copy-data/connect-shares-file-explorer-2.png)

    
Linux-ügyfél használata esetén csatlakoztassa az SMB-megosztást az alábbi parancs használatával. Az alábbi vers paraméter az SMB Linux-gazdagép által támogatott verziója. Az alábbi parancsban adja meg a megfelelő verziót. A Data Box által támogatott SMB-verziókkal kapcsolatban tekintse meg a [Linux-ügyfelek esetében támogatott fájlrendszereket](./data-box-system-requirements.md#supported-file-transfer-protocols-for-clients) ismertető cikket. 

```console
sudo mount -t nfs -o vers=2.1 169.254.143.85:/exportbvtdataset2_BlockBlob /home/databoxubuntuhost/databox
```

## <a name="copy-data-from-data-box"></a>Adatok másolása a Data Boxról

A Data Box-megosztásokhoz történő csatlakozás után a következő lépés az adatok másolása.

[!INCLUDE [data-box-export-review-logs](../../includes/data-box-export-review-logs.md)]


 Az SMB-megosztáshoz való csatlakozás után kezdje meg az adatok másolását. Az adatok másolásához bármilyen SMB-kompatibilis fájlmásoló eszközt használhat (ilyen például a Robocopy). A Robocopyval több másolási feladat is elindítható. 


A Robocopy-paranccsal kapcsolatos további információért lásd [a Robocopyt és néhány példát](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx) bemutató témakört.

A másolás befejezése után nyissa meg az **Irányítópultot** , majd ellenőrizze, hogy az eszközén lévő felhasznált és szabad tárhely mennyiségét.

Ezután elküldheti az Azure Data Boxot a Microsoftnak.


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure Data Box témaköréből ismerhette meg a következőket:

> [!div class="checklist"]
>
> * Előfeltételek
> * Csatlakozás a Data Boxhoz
> * Adatok másolása a Data Boxról

Folytassa a következő oktatóanyaggal, amelyben megismerheti, hogyan küldheti vissza a Data Boxot a Microsoftnak.

> [!div class="nextstepaction"]
> [Azure Data Box elküldése a Microsoftnak](./data-box-deploy-export-picked-up.md)