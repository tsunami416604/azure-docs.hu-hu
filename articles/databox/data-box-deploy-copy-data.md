---
title: Útmutató az adatmásoláshoz az SMB-n keresztül a Azure Data Boxon | Microsoft Docs
description: Megtudhatja, hogyan másolhat adatait az Azure Data Box SMB-n keresztül
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: d86da3013a3cb4573556bc14ea1e6a0fbab72623
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70240392"
---
::: zone target="docs"

# <a name="tutorial-copy-data-to-azure-data-box-via-smb"></a>Oktatóanyag: Adatmásolás Azure Data Box SMB-n keresztül

::: zone-end

::: zone target="chromeless"

# <a name="copy-data-to-azure-data-box"></a>Adatmásolás Azure Data Boxba

::: zone-end

::: zone target="docs"

Ez az oktatóanyag azt ismerteti, hogyan csatlakozhat a gazdagépről, és hogyan másolhatja azokat a helyi webes felhasználói felület használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Előfeltételek
> * Csatlakozás a Data Boxhoz
> * Adatok másolása a Data Boxra


## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg az alábbiakról:

1. Elvégezte az [oktatóanyagot: Azure Data Box](data-box-deploy-set-up.md)beállítása.
2. Megkapta a Data Boxt, és a portálon megjelenő megrendelés állapota **kézbesítve**.
3. Rendelkezik egy gazdagéppel, amelyen a Data Boxra másolni kívánt adatok találhatók. A gazdaszámítógépen:
    - egy [támogatott operációs rendszernek](data-box-system-requirements.md) kell futnia;
    - egy nagy sebességű hálózathoz kell csatlakoznia. Határozottan javasoljuk, hogy legalább 10 GbE sebességű kapcsolattal rendelkezzen. Ha egy 10 GbE-kapcsolat nem érhető el, használjon 1 GbE adatkapcsolatot, de a másolási sebesség hatással lesz rá.

## <a name="connect-to-data-box"></a>Csatlakozás a Data Boxhoz

A kiválasztott Storage-fiók alapján Data Box a következőt hozza létre:
- Három megosztás minden társított tárfiókhoz, GPv1-hez és GPv2-höz.
- Egy megosztás a Premium Storage szolgáltatásban.
- Egy megosztás a blob Storage-fiókhoz.

A blokkblob- és lapblobmegosztások alatti első szintű entitások tárolók, a második szintű entitások pedig blobok. Az Azure Files-megosztások alatti első szintű entitások megosztások, a második szintű entitások pedig fájlok.

Az alábbi táblázat a Data Box és az Azure Storage elérési útja URL-címén található megosztások UNC elérési útját mutatja, ahol az adatfeltöltés történik. Az Azure Storage végső elérési útjának URL-címe az UNC-megosztás elérési útjából származtatható.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Azure Block-Blobok | <li>Megosztások UNC elérési útja:`\\<DeviceIPAddress>\<StorageAccountName_BlockBlob>\<ContainerName>\files\a.txt`</li><li>Azure Storage URL-címe:`https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure-oldal Blobok  | <li>Megosztások UNC elérési útja:`\\<DeviceIPAddres>\<StorageAccountName_PageBlob>\<ContainerName>\files\a.txt`</li><li>Azure Storage URL-címe:`https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>Megosztások UNC elérési útja:`\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>\files\a.txt`</li><li>Azure Storage URL-címe:`https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |      

Ha Windows Server rendszert futtató számítógépet használ, a következő lépésekkel csatlakozhat a Data Boxhoz.

1. Az első lépés a hitelesítés elvégzése, majd a munkamenet elindítása. Lépjen a **Connect and copy** (Kapcsolódás és másolás) elemre. Kattintson a **Get credentials** (Hitelesítő adatok beszerzése) lehetőségre a tárfiókhoz társított megosztások hitelesítő adataihoz való hozzáféréshez. 

    ![Megosztások hitelesítő adatainak beszerzése 1](media/data-box-deploy-copy-data/get-share-credentials1.png)

2. Az Access share and copy data (Megosztási és másolási adatok másolása) párbeszédpanelen másolja ki a megosztásnak megfelelő **Username** (Felhasználónév) és **Password** (Jelszó) értékeket. Kattintson az **OK** gombra.
    
    ![Megosztások hitelesítő adatainak beszerzése 1](media/data-box-deploy-copy-data/get-share-credentials2.png)

3. A Storage-fiókhoz társított megosztások (a következő példában szereplő*devicemanagertest1* ) eléréséhez nyisson meg egy parancssorablakot. A parancssorba írja be a következőt:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Az adatok formátumától függően a megosztások útvonalai a következők:
    - Azure Block blob –`\\10.126.76.172\devicemanagertest1_BlockBlob`
    - Azure-oldal blobja –`\\10.126.76.172\devicemanagertest1_PageBlob`
    - Azure Files –`\\10.126.76.172\devicemanagertest1_AzFile`
    
4. Ha a rendszer kéri, adja meg a megosztás jelszavát. A következő példa bemutatja, hogyan kell csatlakozni egy megosztáshoz a fenti parancs használatával.

    ```
    C:\Users\Databoxuser>net use \\10.126.76.172\devicemanagertest1_BlockBlob /u:devicemanagertest1
    Enter the password for 'devicemanagertest1' to connect to '10.126.76.172':
    The command completed successfully.
    ```

4. Nyomja le a Windows + R billentyűkombinációt. A **Futtatás** ablakban adja meg a következőt: `\\<device IP address>`. A fájlkezelő megnyitásához kattintson **az OK** gombra.
    
    ![Kapcsolódás a megosztáshoz a Fájlkezelővel 2](media/data-box-deploy-copy-data/connect-shares-file-explorer1.png)

    Ekkor a megosztásokat mappákként kell látnia.
    
    ![Kapcsolódás a megosztáshoz a Fájlkezelővel 2](media/data-box-deploy-copy-data/connect-shares-file-explorer2.png)    

    **Mindig hozzon létre egy mappát azokhoz a fájlokhoz, amelyeket másolni szeretne a megosztás alatt, majd másolja a fájlokat a létrehozott mappába**. A blob és oldal blob megosztások alatt létrehozott mappa olyan tárolót jelöl, amelybe az adat blobként van feltöltve. A fájlok nem másolhatók közvetlenül a *gyökérmappa* mappájába a Storage-fiókban.
    
Linux-ügyfél használata esetén a következő paranccsal csatlakoztassa az SMB-megosztást. Az alábbi "vers" paraméter a Linux-gazdagép által támogatott SMB-verzió. Csatlakoztassa a megfelelő verziót az alábbi parancsban. A Data Box által támogatott SMB-verziók esetében lásd: [támogatott fájlrendszerek a Linux-ügyfelek számára](https://docs.microsoft.com/azure/databox/data-box-system-requirements#supported-file-systems-for-linux-clients) 

    `sudo mount -t nfs -o vers=2.1 10.126.76.172:/devicemanagertest1_BlockBlob /home/databoxubuntuhost/databox`
    


## <a name="copy-data-to-data-box"></a>Adatok másolása a Data Boxra

Ha csatlakozott a Data Box-megosztásokhoz, a következő lépés az Adatmásolás. Az Adatmásolás megkezdése előtt tekintse át a következő szempontokat:

- Ügyeljen arra, hogy az adott adatformátumnak megfelelő megosztásokra másolja az adatfájlokat. A blokkblobadatokat például másolja a blokkbloboknak fenntartott megosztásba. Másolja a VHD-ket az oldal blobba. Ha az adatformátum nem egyezik meg a megfelelő megosztási típussal, akkor egy későbbi lépésben az adatok feltöltése az Azure-ba sikertelen lesz.
-  Az adatok másolása során győződjön meg arról, hogy az adatok mérete megfelel az Azure Storage-ban és a [Data Box korlátokban](data-box-limits.md)ismertetett méretkorlát-korlátoknak.
- Ha a Data Box által éppen feltöltés alatt álló adatokat egyidejűleg egy másik alkalmazás is feltölti a Data Boxon kívül, ez a feltöltési feladatok meghiúsulásához és az adatok meghibásodásához vezethet.
- Javasoljuk, hogy:
    - Egyszerre nem használja egyszerre az SMB-t és az NFS-t.
    - Ugyanazokat az adatfájlokat másolja át az Azure-ba. 
     
  Ezekben az esetekben a végső végeredmény nem határozható meg.
- Mindig hozzon létre egy mappát a megosztás alatt másolni kívánt fájlokhoz, majd másolja a fájlokat a mappába. A blob és oldal blob megosztások alatt létrehozott mappa olyan tárolót jelöl, amelybe az adat blobként van feltöltve. A fájlok nem másolhatók közvetlenül a *gyökérmappa* mappájába a Storage-fiókban.

Miután csatlakozott az SMB-megosztáshoz, kezdje el az Adatmásolást. Az adatok másolásához bármilyen SMB-kompatibilis fájlmásoló eszközt használhat (ilyen például a Robocopy). A Robocopyval több másolási feladat is elindítható. Használja az alábbi parancsot:
    
    robocopy <Source> <Target> * /e /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /Log+:<LogFile> 
  
 Az attribútumok leírását az alábbi táblázatban találja meg.
    
|Attribútum  |Leírás  |
|---------|---------|
|/e     |Átmásolja az alkönyvtárakat, az üres könyvtárakkal együtt.         |
|/r:     |A meghiúsult másolások esetén indított újrapróbálkozások számát határozza meg.         |
|/w:     |Az újrapróbálkozások közötti várakozási időt határozza meg másodpercben.         |
|/is     |A fájlok nevét tartalmazza.         |
|/nfl     |Megadja, hogy a fájlnevek ne legyenek naplózva.         |
|/ndl    |Megadja, hogy a rendszer nem naplózza a címtárak nevét.        |
|/np     |Azt határozza meg, hogy ne jelenjen meg a másolási művelet állapota (az addig átmásolt fájlok vagy könyvtárak száma). Az állapot megjelenítése jelentősen csökkenti a teljesítményt.         |
|/MT     | Több szál használata. 32 vagy 64 szál használata ajánlott. Titkosított fájlokhoz ez a beállítás nem használható. Előfordulhat, hogy szét kell választania a titkosított és a nem titkosított fájlokat. Vegye figyelembe, hogy az egyszálas másolás jelentősen csökkenti a teljesítményt.           |
|/fft     | Ennek használatával bármilyen fájlrendszerben csökkentheti a timestamp részletességét.        |
|/b     | A fájlokat biztonsági mentési módban másolja.        |
|/z    | A fájlokat újraindítási módban másolja; akkor lehet rá szükség, ha a környezet instabil. Ez a beállítás a fokozott naplózás miatt csökkenti az átviteli sebességet.      |
| /zb     | Az újraindítási mód használata. A hozzáférés megtagadása esetén áttér a biztonsági mentési mód használatára. Ez a beállítás csökkenti az átviteli sebességet az ellenőrzőpontok létrehozása miatt.         |
|/efsraw     | Az összes titkosított fájlt EFS feldolgozatlan módban másolja át. Csak titkosított fájlokhoz használja.         |
|napló +:\<logfile >| Hozzáfűzi a kimenetet a meglévő naplófájlhoz.|    
 
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
|----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|
|    Data Box         |    2 Robocopy-munkamenet <br> 16 szál munkamenetenként    |    3 Robocopy-munkamenet <br> 16 szál munkamenetenként    |    2 Robocopy-munkamenet <br> 24 szál munkamenetenként    |


A Robocopy-paranccsal kapcsolatos további információért lásd [a Robocopyt és néhány példát](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx) bemutató témakört.

Nyissa meg a célmappát a másolt fájlok megtekintéséhez és ellenőrzéséhez. Ha hibába ütközik a másolási folyamat során, töltse le a hibafájlokat a hibaelhárításhoz. További információ: a [hibanapló megtekintése az adatok másolása során Data Box](data-box-logs.md#view-error-log-during-data-copy). Az adatmásolási hibák részletes listáját lásd: [Data Box problémák elhárítása](data-box-troubleshoot.md).

Az adatok integritásának biztosítása érdekében az ellenőrzőösszeg kiszámítására beágyazva, az adatok másolása közben kerül sor. A másolás befejezése után ellenőrizze, hogy mekkora a felhasznált és a szabad tárhely az eszközén.
    
   ![A szabad és a felhasznált tárhely ellenőrzése az irányítópulton](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)

::: zone-end

::: zone target="chromeless"

Az adatok másolása a forráskiszolgálóról a Data Box SMB, NFS, REST, adatmásolási szolgáltatás vagy felügyelt lemezek használatával.

Minden esetben győződjön meg arról, hogy a megosztási és a mappanév, valamint az adatméret követi az [Azure Storage és a Data Box szolgáltatás korlátai](data-box-limits.md)című témakör útmutatását.

## <a name="copy-data-via-smb"></a>Adatok másolása SMB-n keresztül

1. Ha Windows-gazdagépet használ, használja az alábbi parancsot az SMB-megosztásokhoz való kapcsolódáshoz:

    `\\<IP address of your device>\ShareName`

2. A megosztás eléréséhez szükséges hitelesítő adatokat a Data Box helyi webes kezelőfelületének **Connect & copy** (Kapcsolódás és másolás) lapján tekintheti meg.
3. Egy SMB-kompatibilis fájlmásolás-eszköz, például a Robocopy használatával másolhatja az adatfájlokat a megosztásokra. 

Részletes útmutatásért lépjen az [oktatóanyag: Adatmásolás Azure Data Box SMB](data-box-deploy-copy-data.md)-n keresztül.

## <a name="copy-data-via-nfs"></a>Adatok másolása NFS-en keresztül

1. Ha NFS-gazdagépet használ, a következő paranccsal csatlakoztassa az NFS-megosztásokat a Data Box:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

2. A megosztás eléréséhez szükséges hitelesítő adatokat a Data Box helyi webes kezelőfelületének **Connect & copy** (Kapcsolódás és másolás) lapján tekintheti meg.
3. Az `cp` vagy`rsync` parancs használatával másolja az adatait.

Részletes útmutatásért lépjen az [oktatóanyag: Az Adatmásolás Azure Data Box NFS](data-box-deploy-copy-data-via-nfs.md)-en keresztül.

## <a name="copy-data-via-rest"></a>Adatok másolása REST használatával

1. Az adatok REST API-kon keresztüli Data Box blob Storage használatával történő másolásához *http* -vagy *https*-kapcsolaton keresztül is csatlakozhat.
2. Az Adatmásolás Data Box blob Storage-ba történő másolásához használhatja a AzCopy.

Részletes útmutatásért lépjen az [oktatóanyag: Adatok másolása Azure Data Box blob Storage-ba REST API](data-box-deploy-copy-data-via-nfs.md)-k használatával.

## <a name="copy-data-via-data-copy-service"></a>Adatmásolás az adatmásolási szolgáltatás használatával

1. Az adatok adatmásolási szolgáltatással történő másolásához létre kell hoznia egy feladatot. A Data Box helyi webes FELÜLETén lépjen a **kezelés > Adatmásolási > létrehozás**elemre. 
2. Adja meg a paramétereket, és hozzon létre egy feladatot.

Részletes útmutatásért lépjen az [oktatóanyag: Az adatmásolási szolgáltatás segítségével másolja át az adatAzure Data Boxba](data-box-deploy-copy-data-via-copy-service.md).

## <a name="copy-data-to-managed-disks"></a>Az Adatmásolás a felügyelt lemezekre

1. A Data Box eszköz megrendelése esetén a felügyelt lemezeket a tároló célhelyként kell kiválasztani.
2. Az SMB-vagy NFS-megosztásokon keresztül csatlakozhat Data Boxhoz.
3. Az Adatmásolás SMB-vagy NFS-eszközökön keresztül végezhető el.

Részletes útmutatásért lépjen az [oktatóanyag: A Data Box segítségével importálhatja az Azure](data-box-deploy-copy-data-from-vhds.md)-ban felügyelt lemezként tárolt adatimportálást.

::: zone-end


::: zone target="docs"

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure Data Box témaköréből ismerhette meg a következőket:

> [!div class="checklist"]
> * Előfeltételek
> * Csatlakozás a Data Boxhoz
> * Adatok másolása a Data Boxra


Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan szállíthatja vissza a Data Boxt a Microsoftnak.

> [!div class="nextstepaction"]
> [Azure Data Box elküldése a Microsoftnak](./data-box-deploy-picked-up.md)

::: zone-end

