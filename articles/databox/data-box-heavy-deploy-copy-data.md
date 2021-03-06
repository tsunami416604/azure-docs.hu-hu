---
title: Oktatóanyag az adatok SMB-n keresztül történő másolásához az Azure Data Box Heavyre | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan csatlakozhat a gazdagéphez és hogyan másolhat onnan adatokat az Azure Data Box Heavyre az SMB és a helyi webes felhasználói felület használatával.
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 77dc64d9660f9a0bf66559c4a5a976362cf1acd0
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/12/2020
ms.locfileid: "91951638"
---
::: zone target = "docs"

# <a name="tutorial-copy-data-to-azure-data-box-heavy-via-smb"></a>Oktatóanyag: Adatok másolása az Azure Data Box Heavyre SMB-n keresztül

::: zone-end

::: zone target = "chromeless"

## <a name="copy-data-to-azure-data-box-heavy"></a>Adatok másolása az Azure Data Box Heavyre

::: zone-end

::: zone target = "docs"

Ez az oktatóanyag azt ismerteti, hogyan csatlakozhat a gazdagéphez és hogyan másolhat onnan adatokat a helyi webes felhasználói felület használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Csatlakozás a Data Box Heavyhez
> * Adatok másolása a Data Box Heavyre

::: zone-end

::: zone target = "chromeless"

A forráskiszolgálóról SMB, NFS, REST és adatmásolási szolgáltatás segítségével másolhatja az adatokat a Data Boxra vagy felügyelt lemezekre.

Minden esetben gondoskodjon róla, hogy a megosztások és a mappák neve, valamint az adatok mérete megfeleljen [az Azure Storage és a Data Box Heavy szolgáltatás korlátaival](data-box-heavy-limits.md) foglalkozó cikkben foglaltaknak.

::: zone-end

::: zone target = "docs"

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg az alábbiakról:

1. Az [ Az Azure Data Box Heavy beállítása](data-box-deploy-set-up.md) című oktatóanyagot.
2. Megkapta a Data Box Heavyt, és a portálon a megrendelés **Kézbesítve** állapotú.
3. Rendelkezik egy gazdagéppel, amelyen a Data Box Heavyre másolni kívánt adatok találhatók. A gazdaszámítógépen:
    - egy [támogatott operációs rendszernek](data-box-system-requirements.md) kell futnia;
    - egy nagy sebességű hálózathoz kell csatlakoznia. A legnagyobb másolási sebesség érdekében két 40 GbE sebességű kapcsolat (csomópontonként egy) használható egyidejűleg. Ha nem rendelkezik 40 GbE sebességű kapcsolattal, javasoljuk, hogy legalább két 10 GbE sebességű kapcsolattal (csomópontonként eggyel) rendelkezzen.
   

## <a name="connect-to-data-box-heavy-shares"></a>Csatlakozás a Data Box Heavyn található megosztásokhoz

A kiválasztott tárfióktól függően a Data Box Heavy a következőket hozhatja létre:
- Három megosztás minden társított tárfiókhoz, GPv1-hez és GPv2-höz.
- Egy megosztás a prémium szintű Storage-hoz.
- Egy megosztás a Blob Storage-fiókhoz.

Ezek a megosztások az eszköz mindkét csomópontján létrejönnek.

A blokkblob- és lapblobmegosztások alatt:
- Az első szintű entitások a tárolók.
- A második szintű entitások a blobok.

A megosztások alatt az Azure Files esetében:
- Az első szintű entitások a megosztások.
- A második szintű entitások a fájlok.

Az alábbi táblázat a Data Box Heavyn található megosztások UNC elérési útját és az adatok feltöltéséhez használt Azure Storage elérési útjának URL-címét mutatja. Az Azure Storage elérési útjának végső URL-címe a megosztás UNC elérési útjából származik.
 
| Storage           | UNC elérési út                                                                       |
|-------------------|--------------------------------------------------------------------------------|
| Azure-blokkblobok | <li>A megosztások UNC elérési útja: `\\<DeviceIPAddress>\<StorageAccountName_BlockBlob>\<ContainerName>\files\a.txt`</li><li>Az Azure Storage URL-címe: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure-lapblobok  | <li>A megosztások UNC elérési útja: `\\<DeviceIPAddres>\<StorageAccountName_PageBlob>\<ContainerName>\files\a.txt`</li><li>Az Azure Storage URL-címe: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>A megosztások UNC elérési útja: `\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>\files\a.txt`</li><li>Az Azure Storage URL-címe: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |      

A csatlakozás lépései eltérőek Windows- és Linux-ügyfél esetében.

> [!NOTE]
> Az eszköz mindkét csomópontjához történő egyidejű csatlakozáshoz kövesse ugyanazokat a lépéseket.

### <a name="connect-on-a-windows-system"></a>Csatlakozás Windows rendszeren

Ha Windows Server rendszerű gazdagépet használ, kövesse az alábbi lépéseket a Data Box Heavyhez történő csatlakozáshoz.

1. Az első lépés a hitelesítés elvégzése, majd a munkamenet elindítása. Lépjen a **Connect and copy** (Kapcsolódás és másolás) elemre. Kattintson a **Get credentials** (Hitelesítő adatok beszerzése) lehetőségre a tárfiókhoz társított megosztások hitelesítő adataihoz való hozzáféréshez.

    ![Megosztások hitelesítő adatainak beszerzése](media/data-box-heavy-deploy-copy-data/get-share-credentials-1.png)

2. Az Access share and copy data (Megosztási és másolási adatok másolása) párbeszédpanelen másolja ki a megosztásnak megfelelő **Username** (Felhasználónév) és **Password** (Jelszó) értékeket. Kattintson az **OK** gombra.
    
    ![Megosztások hitelesítő adatainak beszerzése 2](media/data-box-heavy-deploy-copy-data/get-share-credentials-2.png)

3. A tárfiókjához (a következő példában *databoxe2etest*) társított megosztások gazdagépről történő eléréséhez nyisson meg egy parancsablakot. A parancssorba írja be a következőt:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Az adatok formátumától függően a megosztások útvonalai a következők:
    - Azure-blokkblob – `\\10.100.10.100\databoxe2etest_BlockBlob`
    - Azure-lapblob – `\\10.100.10.100\databoxe2etest_PageBlob`
    - Azure Files – `\\10.100.10.100\databoxe2etest_AzFile`
    
4. Ha a rendszer kéri, adja meg a megosztás jelszavát. A következő példa bemutatja, hogyan kell csatlakozni egy megosztáshoz a fenti parancs használatával.

    ```
    C:\Users\Databoxuser>net use \\10.100.10.100\databoxe2etest_BlockBlob /u:databoxe2etest
    Enter the password for 'databoxe2etest' to connect to '10.100.10.100':
    The command completed successfully.
    ```

4. Nyomja le a Windows + R billentyűkombinációt. A **Futtatás** ablakban adja meg a következőt: `\\<device IP address>`. Kattintson az **OK** gombra a Fájlkezelő megnyitásához.
    
    ![Kapcsolódás a megosztáshoz a Fájlkezelővel](media/data-box-heavy-deploy-copy-data/connect-shares-file-explorer-1.png)

    A megosztásoknak ezután mappaként kell megjelenniük.
    
    ![Kapcsolódás a megosztáshoz a Fájlkezelővel 2](media/data-box-heavy-deploy-copy-data/connect-shares-file-explorer-2.png)

    **Mindig hozzon létre egy mappát azokhoz a fájlokhoz, amelyeket másolni szeretne a megosztás alatt, majd másolja a fájlokat a létrehozott mappába**. A blokkblob- és lapblobmegosztások alatt létrehozott mappa azt a tárolót jelöli, amelybe a rendszer feltölti az adatokat blobokként. Nem másolhat fájlokat közvetlenül a tárfiók *gyökér*mappájába.
    
### <a name="connect-on-a-linux-system"></a>Csatlakozás Linux rendszeren

Linux-ügyfél használata esetén csatlakoztassa az SMB-megosztást az alábbi parancs használatával.

```
sudo mount -t nfs -o vers=2.1 10.126.76.172:/databoxe2etest_BlockBlob /home/databoxubuntuhost/databox
```

A `vers` paraméter az SMB Linux-gazdagép által támogatott verziója. A fenti parancsban adja meg a megfelelő verziót.

A Data Box Heavy által támogatott SMB-verziókkal kapcsolatban tekintse meg a [Linux-ügyfelek esetében támogatott fájlrendszereket](data-box-heavy-system-requirements.md#supported-file-systems-for-linux-clients) ismertető cikket.

## <a name="copy-data-to-data-box-heavy"></a>Adatok másolása a Data Box Heavyre

A Data Box Heavy-megosztásokhoz történő csatlakozás után a következő lépés az adatok másolása.

### <a name="copy-considerations"></a>A másolás szempontjai

Az adatok másolásának megkezdése előtt tekintse át a következőket:

- Ügyeljen rá, hogy az adatokat a helyes adatformátumnak megfelelő megosztásokba másolja. A blokkblobadatokat például másolja a blokkbloboknak fenntartott megosztásba. Másolja a VHD-kat a lapblobba.

    Ha az adatok formátuma nem egyezik a megfelelő megosztástípussal, akkor egy későbbi lépés során az Azure-ba történő adatfeltöltés sikertelen lesz.
-  Az adatok másolása közben győződjön meg arról, hogy az adatok mérete megfelel az [Azure Storage és a Data Box Heavy korlátaival](data-box-heavy-limits.md) foglalkozó cikkben ismertetett méretkorlátoknak.
- Ha a Data Box Heavy által éppen feltöltés alatt álló adatokat egyidejűleg más alkalmazások is feltöltik a Data Box Heavyn kívül, az a feltöltési feladat meghiúsulásához és az adatok sérüléséhez vezethet.
- A következő megoldást javasoljuk:
    - Ne használjon egyidejűleg SMB-t és NFS-t.
    - Ne másolja ugyanazokat az adatokat ugyanarra a célhelyre az Azure-ban.
     
  Ilyen esetekben a végeredmény nem garantálható.
- Mindig hozzon létre egy mappát azokhoz a fájlokhoz, amelyeket másolni szeretne a megosztás alatt, majd másolja a fájlokat a létrehozott mappába. A blokkblob- és lapblobmegosztások alatt létrehozott mappa azt a tárolót jelöli, amelybe a rendszer feltölti az adatokat blobokként. Nem másolhat fájlokat közvetlenül a tárfiók *gyökér*mappájába.

Az SMB-megosztáshoz való csatlakozás után kezdje meg az adatok másolását.

1. Az adatok másolásához bármilyen SMB-kompatibilis fájlmásoló eszközt használhat (ilyen például a Robocopy). A Robocopyval több másolási feladat is elindítható. Használja az alábbi parancsot:
    
    ```
    robocopy <Source> <Target> * /e /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /Log+:<LogFile>
    ```
    Az attribútumok leírását az alábbi táblázatban találja meg.
    
    |Attribútum  |Leírás  |
    |---------|---------|
    |/e      |Átmásolja az alkönyvtárakat, az üres könyvtárakkal együtt.         |
    |/r:     |A meghiúsult másolások esetén indított újrapróbálkozások számát határozza meg.         |
    |/w:     |Az újrapróbálkozások közötti várakozási időt határozza meg másodpercben.         |
    |/is     |A fájlok nevét tartalmazza.         |
    |/nfl    |Megadja, hogy a fájlnevek ne legyenek naplózva.         |
    |/ndl    |Megadja, hogy a könyvtárnevek ne legyenek naplózva.        |
    |/np     |Azt határozza meg, hogy ne jelenjen meg a másolási művelet állapota (az addig átmásolt fájlok vagy könyvtárak száma). Az állapot megjelenítése jelentősen csökkenti a teljesítményt.         |
    |/MT     | Több szál használata. 32 vagy 64 szál használata ajánlott. Titkosított fájlokhoz ez a beállítás nem használható. Előfordulhat, hogy szét kell választania a titkosított és a nem titkosított fájlokat. Vegye figyelembe, hogy az egyszálas másolás jelentősen csökkenti a teljesítményt.           |
    |/fft    | Ennek használatával bármilyen fájlrendszerben csökkentheti a timestamp részletességét.        |
    |/b      | A fájlokat biztonsági mentési módban másolja.        |
    |/z      | A fájlokat újraindítási módban másolja; akkor lehet rá szükség, ha a környezet instabil. Ez a beállítás a fokozott naplózás miatt csökkenti az átviteli sebességet.      |
    | /zb    | Az újraindítási mód használata. A hozzáférés megtagadása esetén áttér a biztonsági mentési mód használatára. Ez a beállítás csökkenti az átviteli sebességet az ellenőrzőpontok létrehozása miatt.         |
    |/efsraw | Az összes titkosított fájlt EFS feldolgozatlan módban másolja át. Csak titkosított fájlokhoz használja.         |
    |log+:\<LogFile>| Hozzáfűzi a kimenetet a meglévő naplófájlhoz.|
    
 
    Az alábbi minta a fájloknak a Data Box Heavyre történő másolásához használt Robocopy-parancs kimenetét mutatja be.

    ```   
    C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.100.10.100\devicemanagertest1_AzFile\templates /MT:24
    -------------------------------------------------------------------------------
        ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
        Started : Thursday, April 4, 2019 2:34:58 PM
        Source : C:\Git\azure-docs-pr\contributor-guide\
        Dest : \\10.100.10.100\devicemanagertest1_AzFile\templates\
        Files : *.*
        Options : *.* /DCOPY:DA /COPY:DAT /MT:24 /R:5 /W:60
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
    ```       

2. A teljesítmény optimalizálása érdekében használja a következő Robocopy-paramétereket az adatok másolásához. (Az alábbi számok a legjobb forgatókönyveket jelölik.)

    | Platform    | Többnyire kis méretű fájlok < 512 kB    | Többnyire közepes méretű fájlok, 512 kB–1 MB  | Többnyire nagy méretű fájlok > 1 MB                             |
    |-------------|--------------------------------|----------------------------|----------------------------|
    | Data Box Heavy | 6 Robocopy-munkamenet <br> 24 szál munkamenetenként | 6 Robocopy-munkamenet <br> 16 szál munkamenetenként | 6 Robocopy-munkamenet <br> 16 szál munkamenetenként |


    A Robocopy-paranccsal kapcsolatos további információért lásd [a Robocopyt és néhány példát](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx) bemutató témakört.

3. Nyissa meg a célmappát a másolt fájlok megtekintéséhez és ellenőrzéséhez.

    ![Másolt fájlok megtekintése](media/data-box-heavy-deploy-copy-data/view-copied-files-1.png)


4. Az adatok másolása során:

    - A rendszer ellenőrzi a fájlneveket, -méreteket és -formátumokat annak érdekében, hogy azok megfeleljenek az Azure objektum- és tárolási korlátainak és az Azure fájl- és tárolóelnevezési konvencióinak.
    - Emellett az adatok integritásának biztosítása érdekében az ellenőrzőösszeg kiszámítására beágyazva kerül sor.

    Ha hibába ütközik a másolási folyamat során, töltse le a hibafájlokat a hibaelhárításhoz. Válassza a nyíl ikont a hibafájlok letöltéséhez.

    ![Hibafájlok letöltése](media/data-box-heavy-deploy-copy-data/download-error-files.png)

    További információkért lásd: [Az adatok Data Box Heavyre másolása során készült hibanaplók megtekintése](data-box-logs.md#view-error-log-during-data-copy). Az adatok másolása során felmerülő hibák részletes listájáért tekintse meg a [Data Box Heavy-problémák elhárításával](data-box-troubleshoot.md) kapcsolatos cikket.

5. Nyissa meg a hibafájlt a Jegyzettömbben. Az alábbi hibafájl azt jelzi, hogy az adatok nincsenek megfelelően igazítva.

    ![Hibafájl megnyitása](media/data-box-heavy-deploy-copy-data/open-error-file.png)
    
    Lapblobok esetében az adatoknak 512 bájtos igazítással kell rendelkezniük. Az adatok eltávolítása után a hiba megoldódik, ahogyan az a következő képernyőképen látható.

    ![Hiba megoldva](media/data-box-heavy-deploy-copy-data/error-resolved.png)

6. A másolás befejeződése után lépjen az **Irányítópult megtekintése** lapra. Ellenőrizze, hogy mekkora a felhasznált és a szabad tárhely az eszközén.
    
    ![A szabad és a felhasznált tárhely ellenőrzése az irányítópulton](media/data-box-heavy-deploy-copy-data/verify-used-space-dashboard.png)

Ismételje meg a fenti lépéseket az adatoknak az eszköz második csomópontjára történő másolásához.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure Data Box Heavyvel kapcsolatos témakörökkel ismerkedett meg, például a következőkkel:

> [!div class="checklist"]
> * Csatlakozás a Data Box Heavyhez
> * Adatok másolása a Data Box Heavyre


Folytassa a következő oktatóanyaggal, amelyben megismerheti, hogyan küldheti vissza a Data Box Heavyt a Microsoftnak.

> [!div class="nextstepaction"]
> [Az Azure Data Box Heavy visszaküldése a Microsoftnak](./data-box-heavy-deploy-picked-up.md)

::: zone-end

::: zone target = "chromeless"

### <a name="copy-data-via-smb"></a>Adatok másolása SMB-n keresztül

1. Windows rendszerű gazdagép használata esetén csatlakoztassa az SMB-megosztásokat az alábbi parancs használatával:

    `\\<IP address of your device>\ShareName`

2. A megosztás eléréséhez szükséges hitelesítő adatokat a Data Box helyi webes kezelőfelületének **Connect & copy** (Kapcsolódás és másolás) lapján tekintheti meg.

3. Az adatok megosztásokba történő másolásához használjon SMB-kompatibilis fájlmásoló eszközt, mint amilyen a Robocopy.

Részletes útmutatásért lásd: [Oktatóanyag: Adatok másolása az Azure Data Boxra SMB-n keresztül](data-box-heavy-deploy-copy-data.md).

### <a name="copy-data-via-nfs"></a>Adatok másolása NFS-en keresztül

1. NFS-gazdagép használata esetén csatlakoztassa az NFS-megosztásokat az alábbi parancs használatával:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

2. A megosztás eléréséhez szükséges hitelesítő adatokat a Data Box Heavy helyi webes felhasználói felületének **Csatlakozás és másolás** lapján tekintheti meg.
3. Az adatok másolásához használja a `cp` vagy az `rsync` parancsot. 
4. Ismételje meg ezeket a lépéseket a Data Box Heavy második csomópontjához történő csatlakoztatáshoz és az adatok másolásához.

Részletes útmutatásért lásd: [Oktatóanyag: Adatok másolása az Azure Data Boxra NFS-en keresztül](data-box-heavy-deploy-copy-data-via-nfs.md).

### <a name="copy-data-via-rest"></a>Adatok másolása REST-n keresztül

1. Az adatok Data Box Blob Storage és REST API-k segítségével történő másolásához csatlakozhat *http* vagy *https* protokollal is.
2. Az adatok Data Box Blob Storage-ba másolásához használhatja az AzCopyt.
3. Ismételje meg ezeket a lépéseket a Data Box Heavy második csomópontjához történő csatlakoztatáshoz és az adatok másolásához.

Részletes útmutatásért lásd: [Oktatóanyag: Adatok másolása a Data Box Blob Storage-ba REST API-k segítségével](data-box-heavy-deploy-copy-data-via-rest.md).

### <a name="copy-data-via-data-copy-service"></a>Adatok másolása adatmásolási szolgáltatással

1. Az adatok adatmásolási szolgáltatás használatával történő másolásához létre kell hoznia egy feladatot. A Data Box Heavy helyi webes felhasználói felületén lépjen a **Kezelés > Adatok másolása > Létrehozás** lehetőségre.
2. Adja meg a paramétereket, és hozzon létre egy feladatot.
3. Ismételje meg ezeket a lépéseket a Data Box Heavy második csomópontjához történő csatlakoztatáshoz és az adatok másolásához.

Részletes útmutatásért lásd: [Oktatóanyag: Adatok másolása az Azure Data Box Heavyre az adatmásolási szolgáltatással](data-box-heavy-deploy-copy-data-via-copy-service.md).

### <a name="copy-data-to-managed-disks"></a>Adatok másolása felügyelt lemezekre

1. A Data Box Heavy eszköz megrendelésekor a felügyelt lemezeket kellett kiválasztania tárolási célként.
2. A Data Box Heavyhez SMB- és NFS-megosztásokon keresztül is csatlakozhat.
3. Ezután SMB- vagy NFS-eszközökkel másolhatja az adatokat.
4. Ismételje meg ezeket a lépéseket a Data Box Heavy második csomópontjához történő csatlakoztatáshoz és az adatok másolásához.

Részletes útmutatásért lásd: [Oktatóanyag: Adatok importálása felügyelt lemezekként az Azure-ban a Data Box Heavy használatával](data-box-heavy-deploy-copy-data-from-vhds.md).

::: zone-end


