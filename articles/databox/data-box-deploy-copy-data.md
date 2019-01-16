---
title: Adatok másolása az SMB-n keresztül a Microsoft Azure Data Box |} A Microsoft Docs
description: Ismerje meg, hogyan másolhat adatokat az Azure Data Box SMB-n keresztül
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: alkohli
ms.openlocfilehash: 5f3e7164c0569422fe164283efaa8f282ccfe9f8
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2019
ms.locfileid: "54318943"
---
# <a name="tutorial-copy-data-to-azure-data-box-via-smb"></a>Oktatóanyag: Adatok másolása az Azure Data Box SMB-n keresztül

Ez az oktatóanyag azt ismerteti, hogyan csatlakozhat a Data Boxhoz, hogyan másolhat ki adatokat a gazdagépről a helyi webes felület használatával, és hogyan készítheti elő a Data Box elküldését.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Csatlakozás a Data Boxhoz
> * Adatok másolása a Data Boxra
> * Data Box szállításának előkészítése

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg az alábbiakról:

1. Ön teljesítette a [oktatóanyag: Állítsa be az Azure Data Box](data-box-deploy-set-up.md).
2. A Data Box kapott, és a rendelés állapota a portálon **kézbesítések**.
3. Rendelkezik egy gazdagéppel, amelyen a Data Boxra másolni kívánt adatok találhatók. A gazdaszámítógépen:
    - egy [támogatott operációs rendszernek](data-box-system-requirements.md) kell futnia;
    - egy nagy sebességű hálózathoz kell csatlakoznia. Határozottan javasoljuk, hogy legalább 10 GbE sebességű kapcsolattal rendelkezzen. 10-GbE kapcsolatot nem érhető el, ha 1-GbE adatok hivatkozás használata, de a másolási sebességek érinti. 

## <a name="connect-to-data-box"></a>Csatlakozás a Data Boxhoz

A kiválasztott tárfiók alapján a Data Box hoz létre, akár:
- Három megosztás minden társított tárfiókhoz, GPv1-hez és GPv2-höz.
- Egy megosztás a prémium vagy Blob Storage-fiók számára.

A blokkblob- és lapblobmegosztások alatti első szintű entitások tárolók, a második szintű entitások pedig blobok. Az Azure Files-megosztások alatti első szintű entitások megosztások, a második szintű entitások pedig fájlok.

Az alábbi táblázat az UNC elérési utat a megosztásokat, ahol az adatok feltöltése a Data Box és az Azure Storage elérési út URL. Az Azure Storage elérési út utolsó URL-cím származtatható megosztás UNC elérési útját.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Az Azure Block blobs | <li>Megosztás UNC elérési útja: `\\<DeviceIPAddress>\<StorageAccountName_BlockBlob>\<ContainerName>\files\a.txt`</li><li>Az Azure Storage URL-címe: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure-lapblobok  | <li>Megosztás UNC elérési útja: `\\<DeviceIPAddres>\<StorageAccountName_PageBlob>\<ContainerName>\files\a.txt`</li><li>Az Azure Storage URL-címe: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>Megosztás UNC elérési útja: `\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>\files\a.txt`</li><li>Az Azure Storage URL-címe: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |      

Ha a gazdagép Windows Server számítógépet használ, kövesse az alábbi lépéseket a Data Box csatlakozni.

1. Az első lépés a hitelesítés elvégzése, majd a munkamenet elindítása. Lépjen a **Connect and copy** (Kapcsolódás és másolás) elemre. Kattintson a **Get credentials** (Hitelesítő adatok beszerzése) lehetőségre a tárfiókhoz társított megosztások hitelesítő adataihoz való hozzáféréshez. 

    ![Megosztások hitelesítő adatainak beszerzése 1](media/data-box-deploy-copy-data/get-share-credentials1.png)

2. Az Access share and copy data (Megosztási és másolási adatok másolása) párbeszédpanelen másolja ki a megosztásnak megfelelő **Username** (Felhasználónév) és **Password** (Jelszó) értékeket. Kattintson az **OK** gombra.
    
    ![Megosztások hitelesítő adatainak beszerzése 1](media/data-box-deploy-copy-data/get-share-credentials2.png)

3. A tárfiók társított megosztás eléréséhez (*devicemanagertest1* az alábbi példában) a gazdagép számítógépről, nyisson meg egy parancsablakot. A parancssorba írja be a következőt:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Az adatok formátuma függően, a megosztás elérési utak a következők:
    - Az Azure blokkblob- `\\10.126.76.172\devicemanagertest1_BlockBlob`
    - Az Azure lapblob- `\\10.126.76.172\devicemanagertest1_PageBlob`
    - Az Azure Files- `\\10.126.76.172\devicemanagertest1_AzFile`
    
4. Ha a rendszer kéri, adja meg a megosztás jelszavát. A következő példa bemutatja, hogyan kell csatlakozni egy megosztáshoz a fenti parancs használatával.

    ```
    C:\Users\Databoxuser>net use \\10.126.76.172\devicemanagertest1_BlockBlob /u:devicemanagertest1
    Enter the password for 'devicemanagertest1' to connect to '10.126.76.172':
    The command completed successfully.
    ```

4. Nyomja le a Windows + R billentyűkombinációt. A **Futtatás** ablakban adja meg a következőt: `\\<device IP address>`. Kattintson a **OK** , nyissa meg a Fájlkezelőt.
    
    ![Kapcsolódás a megosztáshoz a Fájlkezelővel 2](media/data-box-deploy-copy-data/connect-shares-file-explorer1.png)

    Meg kell jelennie a megosztások, mappák.
    
    **Mindig hozzon létre egy mappát azokhoz a fájlokhoz, amelyeket másolni szeretne a megosztás alatt, majd másolja a fájlokat a létrehozott mappába**. Blokkblob típusú a mappában létrehozott, és a blob megosztások lap egy tárolóban, amelyhez data nahrávají blobként jelöli. Nem lehet másolni a fájlokat közvetlenül a *$root* mappát a storage-fiókban.
    
    ![Kapcsolódás a megosztáshoz a Fájlkezelővel 2](media/data-box-deploy-copy-data/connect-shares-file-explorer2.png) 

## <a name="copy-data-to-data-box"></a>Adatok másolása a Data Boxra

Miután csatlakozott a Data Box-megosztáshoz, a következő lépés az adatok másolása. Mielőtt elkezdené az adatok másolását, tekintse át az alábbiakat:

- Győződjön meg arról, hogy az adatok másolása megosztások, amelyek megfelelnek a megfelelő adatok formátuma. A blokkblobadatokat például másolja a blokkbloboknak fenntartott megosztásba. Ha az adatok formátuma nem felel meg a megfelelő megosztási típusát, majd egy későbbi lépésben, az adatok feltöltése az Azure-bA sikertelen lesz.
-  Adatok másolása során győződjön meg arról, hogy megfelel-e az adatok mérete a méretbeli korlátokat ismertetett a [az Azure storage és a Data Box-korlátok](data-box-limits.md).
- Ha a Data Box által éppen feltöltés alatt álló adatokat egyidejűleg egy másik alkalmazás is feltölti a Data Boxon kívül, ez a feltöltési feladatok meghiúsulásához és az adatok meghibásodásához vezethet.
- Azt javasoljuk, hogy nem használja az SMB és NFS is egyszerre vagy másolni ugyanazon adatok azonos teljes célra az Azure-ban. Ezekben az esetekben nem lehet megállapítani a végső eredményt.
- Mindig hozzon létre egy mappát a fájlok másolása a megosztás alatt, és ezután másolja a fájlokat a mappában, melyet. Blokkblob típusú a mappában létrehozott, és a blob megosztások lap egy tárolóban, amelyhez az adatfeltöltés blobként jelöli. Nem lehet másolni a fájlokat közvetlenül a *$root* mappát a storage-fiókban.

Miután csatlakozott az SMB-megosztás, megkezdheti az adatok másolását. Az adatok másolásához bármilyen SMB-kompatibilis fájlmásoló eszközt használhat (ilyen például a Robocopy). A Robocopyval több másolási feladat is elindítható. Használja az alábbi parancsot:
    
    robocopy <Source> <Target> * /e /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /Log+:<LogFile> 
  
 Az attribútumok leírását az alábbi táblázatban találja meg.
    
|Attribútum  |Leírás  |
|---------|---------|
|/e     |Átmásolja az alkönyvtárakat, az üres könyvtárakkal együtt.         |
|/r:     |A meghiúsult másolások esetén indított újrapróbálkozások számát határozza meg.         |
|/w:     |Az újrapróbálkozások közötti várakozási időt határozza meg másodpercben.         |
|/is     |A fájlok nevét tartalmazza.         |
|/nfl     |Itt adhatja meg, hogy a fájlnevek nem naplózza.         |
|/ndl    |Meghatározza, hogy könyvtárának nevében nem.        |
|/np     |Azt határozza meg, hogy ne jelenjen meg a másolási művelet állapota (az addig átmásolt fájlok vagy könyvtárak száma). Az állapot megjelenítése jelentősen csökkenti a teljesítményt.         |
|/MT     | Több szál használata. 32 vagy 64 szál használata ajánlott. Titkosított fájlokhoz ez a beállítás nem használható. Előfordulhat, hogy szét kell választania a titkosított és a nem titkosított fájlokat. Vegye figyelembe, hogy az egyszálas másolás jelentősen csökkenti a teljesítményt.           |
|/fft     | Ennek használatával bármilyen fájlrendszerben csökkentheti a timestamp részletességét.        |
|/b     | A fájlokat biztonsági mentési módban másolja.        |
|/z    | A fájlokat újraindítási módban másolja; akkor lehet rá szükség, ha a környezet instabil. Ez a beállítás a fokozott naplózás miatt csökkenti az átviteli sebességet.      |
| /zb     | Az újraindítási mód használata. A hozzáférés megtagadása esetén áttér a biztonsági mentési mód használatára. Ez a beállítás csökkenti az átviteli sebességet az ellenőrzőpontok létrehozása miatt.         |
|/efsraw     | Az összes titkosított fájlt EFS feldolgozatlan módban másolja át. Csak titkosított fájlokhoz használja.         |
|log+:<LogFile>| Hozzáfűzi a kimenetet a meglévő naplófájlhoz.|    
 
Az alábbi minta a fájloknak a Data Boxra történő másolásához használt Robocopy-parancs kimenetét mutatja be.
    
    C:\Users>robocopy
        -------------------------------------------------------------------------------
        ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
        Started : Thursday, March 8, 2018 2:34:53 PM
            Simple Usage :: ROBOCOPY source destination /MIR
    
                    source :: Source Directory (drive:\path or \\server\share\path).
            destination :: Destination Dir  (drive:\path or \\server\share\path).
                    /MIR :: Mirror a complete directory tree.
    
        For more usage information run ROBOCOPY /?    
    
    ****  /MIR can DELETE files as well as copy them !
    
    C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.126.76.172\devicemanagertest1_AzFile\templates /MT:32
    -------------------------------------------------------------------------------
        ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
        Started : Thursday, March 8, 2018 2:34:58 PM
        Source : C:\Git\azure-docs-pr\contributor-guide\
            Dest : \\10.126.76.172\devicemanagertest1_AzFile\templates\
    
        Files : *.*
    
        Options : *.* /DCOPY:DA /COPY:DAT /MT:32 /R:5 /W:60
    
    ------------------------------------------------------------------------------
    
    100%        New File                 206        C:\Git\azure-docs-pr\contributor-guide\article-metadata.md
    100%        New File                 209        C:\Git\azure-docs-pr\contributor-guide\content-channel-guidance.md
    100%        New File                 732        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-index.md
    100%        New File                 199        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pr-criteria.md
                New File                 178        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-co100%  .md
                New File                 250        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-et100%  e.md
    100%        New File                 174        C:\Git\azure-docs-pr\contributor-guide\create-images-markdown.md
    100%        New File                 197        C:\Git\azure-docs-pr\contributor-guide\create-links-markdown.md
    100%        New File                 184        C:\Git\azure-docs-pr\contributor-guide\create-tables-markdown.md
    100%        New File                 208        C:\Git\azure-docs-pr\contributor-guide\custom-markdown-extensions.md
    100%        New File                 210        C:\Git\azure-docs-pr\contributor-guide\file-names-and-locations.md
    100%        New File                 234        C:\Git\azure-docs-pr\contributor-guide\git-commands-for-master.md
    100%        New File                 186        C:\Git\azure-docs-pr\contributor-guide\release-branches.md
    100%        New File                 240        C:\Git\azure-docs-pr\contributor-guide\retire-or-rename-an-article.md
    100%        New File                 215        C:\Git\azure-docs-pr\contributor-guide\style-and-voice.md
    100%        New File                 212        C:\Git\azure-docs-pr\contributor-guide\syntax-highlighting-markdown.md
    100%        New File                 207        C:\Git\azure-docs-pr\contributor-guide\tools-and-setup.md
    ------------------------------------------------------------------------------
    
                    Total    Copied   Skipped  Mismatch    FAILED    Extras
        Dirs :         1         1         1         0         0         0
        Files :        17        17         0         0         0         0
        Bytes :     3.9 k     3.9 k         0         0         0         0          
    C:\Users>
       

A teljesítmény optimalizálása érdekében használja a következő Robocopy-paramétereket az adatok másolásához.

|    Platform    |    Többnyire kis méretű fájlok < 512 kB                           |    Többnyire közepes méretű fájlok 512 kB – 1 MB                      |    Többnyire nagy méretű fájlok > 1 MB                             |   
|----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|---|
|    Data Box         |    2 Robocopy-munkamenet <br> 16 szál munkamenetenként    |    3 Robocopy-munkamenet <br> 16 szál munkamenetenként    |    2 Robocopy-munkamenet <br> 24 szál munkamenetenként    |  |


A Robocopy-paranccsal kapcsolatos további információért lásd [a Robocopyt és néhány példát](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx) bemutató témakört.

Nyissa meg a célmappát a másolt fájlok megtekintéséhez és ellenőrzéséhez. Ha hibába ütközik a másolási folyamat során, töltse le a hibafájlokat a hibaelhárításhoz.

Az adatok integritásának biztosítása érdekében az ellenőrzőösszeg kiszámítására beágyazva, az adatok másolása közben kerül sor. A másolás befejezése után ellenőrizze, hogy mekkora a felhasznált és a szabad tárhely az eszközén.
    
   ![A szabad és a felhasznált tárhely ellenőrzése az irányítópulton](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)


## <a name="prepare-to-ship"></a>A szállítás előkészítése

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure Data Box témaköréből ismerhette meg a következőket:

> [!div class="checklist"]
> * Csatlakozás a Data Boxhoz
> * Adatok másolása a Data Boxra
> * Data Box szállításának előkészítése

Folytassa a következő oktatóanyaggal, megtudhatja, hogyan tehetnek a Data Box elküldje a Microsoftnak.

> [!div class="nextstepaction"]
> [Azure Data Box elküldése a Microsoftnak](./data-box-deploy-picked-up.md)

