---
title: Adatok másolása az SMB-n keresztül a Microsoft Azure Data Box |} A Microsoft Docs
description: Ismerje meg, hogyan másolhat adatokat az Azure Data Box SMB-n keresztül
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 11/20/2018
ms.author: alkohli
ms.openlocfilehash: e5219a0ade610a41d316970aecda06d4020b37f2
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53546181"
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

1. Befejeződött a [oktatóanyag: Állítsa be az Azure Data Box](data-box-deploy-set-up.md).
2. Megkapta a Data Boxot, és a portálon a megrendelés **Kézbesítve** állapotú.
3. Rendelkezik egy gazdagéppel, amelyen a Data Boxra másolni kívánt adatok találhatók. A gazdaszámítógépen:
    - egy [támogatott operációs rendszernek](data-box-system-requirements.md) kell futnia;
    - egy nagy sebességű hálózathoz kell csatlakoznia. Határozottan javasoljuk, hogy legalább 10 GbE sebességű kapcsolattal rendelkezzen. Ha 10 GbE sebességű kapcsolat nem áll rendelkezésre, 1 GbE sebességű adatkapcsolat is használható, azonban ez rontja a másolási sebességet. 

## <a name="connect-to-data-box"></a>Csatlakozás a Data Boxhoz

A kiválasztott tárfióktól függően a Data Box a következőket hozhatja létre:
- Három megosztás minden társított tárfiókhoz, GPv1-hez és GPv2-höz.
- Egy megosztás a prémium vagy Blob Storage-fiók számára. 

A blokkblob- és lapblobmegosztások alatti első szintű entitások tárolók, a második szintű entitások pedig blobok. Az Azure Files-megosztások alatti első szintű entitások megosztások, a második szintű entitások pedig fájlok.

Lásd az alábbi példát. 

- Tárfiók: *Mystoracct*
- Megoszthatja a blokkblobok: *Mystoracct_BlockBlob/my-tároló/blob*
- Megoszthatja a lapblob: *Mystoracct_PageBlob/my-tároló/blob*
- Megoszthatja a fájlt: *Mystoracct_AzFile/my-megosztás*

Amennyiben Windows Server rendszerű gazdagépet használ, a következő módon csatlakozhat a Data Boxhoz.

1. Az első lépés a hitelesítés elvégzése, majd a munkamenet elindítása. Lépjen a **Connect and copy** (Kapcsolódás és másolás) elemre. Kattintson a **Get credentials** (Hitelesítő adatok beszerzése) lehetőségre a tárfiókhoz társított megosztások hitelesítő adataihoz való hozzáféréshez. 

    ![Megosztások hitelesítő adatainak beszerzése 1](media/data-box-deploy-copy-data/get-share-credentials1.png)

2. Az Access share and copy data (Megosztási és másolási adatok másolása) párbeszédpanelen másolja ki a megosztásnak megfelelő **Username** (Felhasználónév) és **Password** (Jelszó) értékeket. Kattintson az **OK** gombra.
    
    ![Megosztások hitelesítő adatainak beszerzése 1](media/data-box-deploy-copy-data/get-share-credentials2.png)

3. Férjen hozzá a tárfiókjához társított megosztásokhoz (a következő példában: Mystoracct). A megosztások elérési útja: `\\<IP of the device>\ShareName`. Az adatok formátumától függően (a megosztás nevét használva) az alábbi címeken kapcsolódhat a megosztásokhoz: 
    - *\\<IP address of the device>\Mystoracct_Blob*
    - *\\<IP address of the device>\Mystoracct_Page*
    - *\\<IP address of the device>\Mystoracct_AzFile*
    
    Ha a gazdagépről szeretne kapcsolódni a megosztásokhoz, nyisson meg egy parancsablakot. A parancssorba írja be a következőt:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Ha a rendszer kéri, adja meg a megosztás jelszavát. A következő példa bemutatja, hogyan kell csatlakozni egy megosztáshoz a fenti parancs használatával.

    ```
    C:\Users\Databoxuser>net use \\10.126.76.172\devicemanagertest1_BlockBlob /u:devicemanagertest1
    Enter the password for 'devicemanagertest1' to connect to '10.126.76.172':
    The command completed successfully.
    ```

4. Nyomja le a Windows + R billentyűkombinációt. A **Futtatás** ablakban adja meg a következőt: `\\<device IP address>`. Kattintson az **OK** gombra. Ekkor megnyílik a Fájlkezelő. A megosztásoknak ezután mappaként kell megjelenniük.
    
    ![Kapcsolódás a megosztáshoz a Fájlkezelővel 2](media/data-box-deploy-copy-data/connect-shares-file-explorer1.png)

5.  **Mindig hozzon létre egy mappát azokhoz a fájlokhoz, amelyeket másolni szeretne a megosztás alatt, majd másolja a fájlokat a létrehozott mappába**. Néha egy szürke kereszt jelenik meg a mappákon. Ez a kereszt nem hibaállapotot jelez. A mappákat az alkalmazás jelölte meg, hogy nyomon követhesse az állapotukat.
    
    ![Kapcsolódás a megosztáshoz a Fájlkezelővel 2](media/data-box-deploy-copy-data/connect-shares-file-explorer2.png) ![Kapcsolódás a megosztáshoz a Fájlkezelővel 2](media/data-box-deploy-copy-data/connect-shares-file-explorer2.png) 

## <a name="copy-data-to-data-box"></a>Adatok másolása a Data Boxra

A Data Box-megosztáshoz való kapcsolódás után a következő lépés az adatok másolása. Ennek megkezdése előtt tekintse át az alábbi szempontokat:

- Győződjön meg arról, hogy az adatokat a helyes adatformátumnak megfelelő megosztásokba másolja. A blokkblobadatokat például másolja a blokkbloboknak fenntartott megosztásba. Ha az adatok formátuma nem egyezik a megfelelő megosztástípussal, akkor egy későbbi lépés során az Azure-ba történő adatfeltöltés sikertelen lesz.
-  Adatok másolása közben győződjön meg arról, hogy az adatok mérete megfelel az [Azure Storage és a Data Box korlátaival](data-box-limits.md) foglalkozó cikkben ismertetett méretkorlátoknak. 
- Ha a Data Box által éppen feltöltés alatt álló adatokat egyidejűleg egy másik alkalmazás is feltölti a Data Boxon kívül, ez a feltöltési feladatok meghiúsulásához és az adatok meghibásodásához vezethet.
- Azt javasoljuk, hogy ne használjon egyidejűleg SMB-t és NFS-t az Azure-ban, illetve ne másolja ugyanazokat az adatokat ugyanarra a célhelyre. Ilyen esetekben a végeredmény nem garantálható.

Az SMB-megosztáshoz való kapcsolódás után indítsa el az adatok másolását. 

Az adatok másolásához bármilyen SMB-kompatibilis fájlmásoló eszközt használhat (ilyen például a Robocopy). A Robocopyval több másolási feladat is elindítható. Használja az alábbi parancsot:
    
    robocopy <Source> <Target> * /e /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /Log+:<LogFile> 
  
 Az attribútumok leírását az alábbi táblázatban találja meg.
    
|Attribútum  |Leírás  |
|---------|---------|
|/e     |Átmásolja az alkönyvtárakat, az üres könyvtárakkal együtt.         |
|/r:     |A meghiúsult másolások esetén indított újrapróbálkozások számát határozza meg.         |
|/w:     |Az újrapróbálkozások közötti várakozási időt határozza meg másodpercben.         |
|/is     |A fájlok nevét tartalmazza.         |
|/nfl     |Azt adja meg, hogy a fájlnevek ne legyenek naplózva.         |
|/ndl    |Azt adja meg, hogy a könyvtárnevek ne legyenek naplózva.        |
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

