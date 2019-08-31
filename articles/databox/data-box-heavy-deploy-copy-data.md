---
title: Útmutató az adatmásoláshoz az SMB-n keresztül a Azure Data Box Heavyon | Microsoft Docs
description: Megtudhatja, hogyan másolhat adatait az Azure Data Box Heavy SMB-n keresztül
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: alkohli
ms.openlocfilehash: 8cb763766ebb151ad1c59b63a33a63493a4f0069
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70164366"
---
::: zone target = "docs"

# <a name="tutorial-copy-data-to-azure-data-box-heavy-via-smb"></a>Oktatóanyag: Adatmásolás Azure Data Box Heavy SMB-n keresztül

::: zone-end

::: zone target = "chromeless"

## <a name="copy-data-to-azure-data-box-heavy"></a>Adatmásolás Azure Data Box Heavyba

::: zone-end

::: zone target = "docs"

Ez az oktatóanyag azt ismerteti, hogyan csatlakozhat a gazdagépről, és hogyan másolhatja azokat a helyi webes felhasználói felület használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Kapcsolódás Data Box Heavyhoz
> * Adatmásolás Data Box Heavyba

::: zone-end

::: zone target = "chromeless"

Az adatok másolása a forráskiszolgálóról a Data Box SMB, NFS, REST, adatmásolási szolgáltatás vagy felügyelt lemezek használatával.

Minden esetben győződjön meg arról, hogy a megosztási és a mappanév, valamint az adatméret követi az [Azure Storage és a Data Box Heavy szolgáltatás korlátai](data-box-heavy-limits.md)című témakör útmutatását.

::: zone-end

::: zone target = "docs"

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg az alábbiakról:

1. Elvégezte az [oktatóanyagot: Azure Data Box Heavy](data-box-deploy-set-up.md)beállítása.
2. Megkapta a Data Box Heavyt, és a portálon megjelenőmegrendelés állapota kézbesítve.
3. Rendelkezik egy gazdagéptel, amely a Data Box Heavyba másolni kívánt adattal rendelkezik. A gazdaszámítógépen:
    - egy [támogatott operációs rendszernek](data-box-system-requirements.md) kell futnia;
    - egy nagy sebességű hálózathoz kell csatlakoznia. A leggyorsabb másolási sebességhez a 2 40-GbE kapcsolatok (egy csomóponton eggyel) párhuzamosan használhatók. Ha nincs elérhető 40-GbE-kapcsolat, javasoljuk, hogy legalább 2 10-GbE kapcsolattal rendelkezzen (egy-egy csomóponton).
   

## <a name="connect-to-data-box-heavy-shares"></a>Kapcsolódás Data Box Heavy megosztásokhoz

A kiválasztott Storage-fiók alapján Data Box Heavy a következőt hozza létre:
- Három megosztás minden társított tárfiókhoz, GPv1-hez és GPv2-höz.
- Egy megosztás a Premium Storage szolgáltatásban.
- Egy megosztás a blob Storage-fiókhoz.

Ezek a megosztások az eszköz csomópontjain jönnek létre.

A blob és az oldal blob megosztásának letiltása területen:
- Az első szintű entitások tárolók.
- A második szintű entitások Blobok.

Azure Files megosztások alatt:
- Az első szintű entitások megosztások.
- A második szintű entitások fájlok.

Az alábbi táblázat a Data Box Heavy és az Azure Storage elérési útja URL-címén található megosztások UNC elérési útját mutatja, ahol az adatfeltöltés történik. Az Azure Storage végső elérési útjának URL-címe az UNC-megosztás elérési útjából származtatható.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Azure Block-Blobok | <li>Megosztások UNC elérési útja:`\\<DeviceIPAddress>\<StorageAccountName_BlockBlob>\<ContainerName>\files\a.txt`</li><li>Azure Storage URL-címe:`https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure-oldal Blobok  | <li>Megosztások UNC elérési útja:`\\<DeviceIPAddres>\<StorageAccountName_PageBlob>\<ContainerName>\files\a.txt`</li><li>Azure Storage URL-címe:`https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>Megosztások UNC elérési útja:`\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>\files\a.txt`</li><li>Azure Storage URL-címe:`https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |      

A Windows vagy Linux-ügyfél használatával történő kapcsolódás lépései eltérőek.

> [!NOTE]
> Ugyanezen lépések végrehajtásával csatlakozhat az eszköz csomópontjaihoz is párhuzamosan.

### <a name="connect-on-a-windows-system"></a>Kapcsolat Windows rendszeren

Ha Windows Server rendszert futtató számítógépet használ, a következő lépésekkel csatlakozhat a Data Box Heavyhoz.

1. Az első lépés a hitelesítés elvégzése, majd a munkamenet elindítása. Lépjen a **Connect and copy** (Kapcsolódás és másolás) elemre. Kattintson a **Get credentials** (Hitelesítő adatok beszerzése) lehetőségre a tárfiókhoz társított megosztások hitelesítő adataihoz való hozzáféréshez.

    ![Megosztások hitelesítő adatainak beszerzése 1](media/data-box-heavy-deploy-copy-data/get-share-credentials-1.png)

2. Az Access share and copy data (Megosztási és másolási adatok másolása) párbeszédpanelen másolja ki a megosztásnak megfelelő **Username** (Felhasználónév) és **Password** (Jelszó) értékeket. Kattintson az **OK** gombra.
    
    ![Megosztások hitelesítő adatainak beszerzése 1](media/data-box-heavy-deploy-copy-data/get-share-credentials-2.png)

3. A Storage-fiókhoz társított megosztások (a következő példában szereplő*databoxe2etest* ) eléréséhez nyisson meg egy parancssorablakot. A parancssorba írja be a következőt:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Az adatok formátumától függően a megosztások útvonalai a következők:
    - Azure Block blob –`\\10.100.10.100\databoxe2etest_BlockBlob`
    - Azure-oldal blobja –`\\10.100.10.100\databoxe2etest_PageBlob`
    - Azure Files –`\\10.100.10.100\databoxe2etest_AzFile`
    
4. Ha a rendszer kéri, adja meg a megosztás jelszavát. A következő példa bemutatja, hogyan kell csatlakozni egy megosztáshoz a fenti parancs használatával.

    ```
    C:\Users\Databoxuser>net use \\10.100.10.100\databoxe2etest_BlockBlob /u:databoxe2etest
    Enter the password for 'databoxe2etest' to connect to '10.100.10.100':
    The command completed successfully.
    ```

4. Nyomja le a Windows + R billentyűkombinációt. A **Futtatás** ablakban adja meg a következőt: `\\<device IP address>`. A fájlkezelő megnyitásához kattintson **az OK** gombra.
    
    ![Kapcsolódás a megosztáshoz a Fájlkezelővel 2](media/data-box-heavy-deploy-copy-data/connect-shares-file-explorer-1.png)

    Ekkor a megosztásokat mappákként kell látnia.
    
    ![Kapcsolódás a megosztáshoz a Fájlkezelővel 2](media/data-box-heavy-deploy-copy-data/connect-shares-file-explorer-2.png)

    **Mindig hozzon létre egy mappát azokhoz a fájlokhoz, amelyeket másolni szeretne a megosztás alatt, majd másolja a fájlokat a létrehozott mappába**. A blob és oldal blob megosztások alatt létrehozott mappa olyan tárolót jelöl, amelybe az adat blobként van feltöltve. A fájlok nem másolhatók közvetlenül a *gyökérmappa* mappájába a Storage-fiókban.
    
### <a name="connect-on-a-linux-system"></a>Linux rendszeren való kapcsolat

Linux-ügyfél használata esetén a következő paranccsal csatlakoztassa az SMB-megosztást.

```
sudo mount -t nfs -o vers=2.1 10.126.76.172:/databoxe2etest_BlockBlob /home/databoxubuntuhost/databox
```

A `vers` paraméter a Linux-gazdagép által támogatott SMB-verzió. A fenti parancsban illessze be a megfelelő verziót.

A Data Box Heavy által támogatott SMB-verziókkal kapcsolatban lásd: [támogatott fájlrendszerek Linux](data-box-heavy-system-requirements.md#supported-file-systems-for-linux-clients)-ügyfelekhez.

## <a name="copy-data-to-data-box-heavy"></a>Adatmásolás Data Box Heavyba

Ha csatlakozott a Data Box Heavy-megosztásokhoz, a következő lépés az Adatmásolás.

### <a name="copy-considerations"></a>A másolás szempontjai

Az Adatmásolás megkezdése előtt tekintse át a következő szempontokat:

- Ügyeljen arra, hogy az adott adatformátumnak megfelelő megosztásokra másolja az adatfájlokat. A blokkblobadatokat például másolja a blokkbloboknak fenntartott megosztásba. Másolja a VHD-ket az oldal blobba.

    Ha az adatformátum nem egyezik meg a megfelelő megosztási típussal, akkor egy későbbi lépésben az adatok feltöltése az Azure-ba sikertelen lesz.
-  Az adatok másolása során győződjön meg arról, hogy az adatok mérete megfelel az Azure Storage-ban és a [Data Box Heavy korlátokban](data-box-heavy-limits.md)ismertetett méretkorlát-korlátoknak.
- Ha az Data Box Heavy által feltöltött adatok párhuzamosan fel vannak töltve más alkalmazásokkal, a Data Box Heavyon kívül más alkalmazások is feltölthetik, így a feladatok sikertelenek és adatsérülést okozhatnak.
- Javasoljuk, hogy:
    - Egyszerre nem használja egyszerre az SMB-t és az NFS-t.
    - Ugyanazokat az adatfájlokat másolja át az Azure-ba.
     
  Ezekben az esetekben a végső végeredmény nem határozható meg.
- Mindig hozzon létre egy mappát a megosztás alatt másolni kívánt fájlokhoz, majd másolja a fájlokat a mappába. A blob és oldal blob megosztások alatt létrehozott mappa olyan tárolót jelöl, amelybe az adat blobként van feltöltve. A fájlok nem másolhatók közvetlenül a *gyökérmappa* mappájába a Storage-fiókban.

Miután csatlakozott az SMB-megosztáshoz, kezdje el az Adatmásolást.

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
    |/ndl    |Megadja, hogy a rendszer nem naplózza a címtárak nevét.        |
    |/np     |Azt határozza meg, hogy ne jelenjen meg a másolási művelet állapota (az addig átmásolt fájlok vagy könyvtárak száma). Az állapot megjelenítése jelentősen csökkenti a teljesítményt.         |
    |/MT     | Több szál használata. 32 vagy 64 szál használata ajánlott. Titkosított fájlokhoz ez a beállítás nem használható. Előfordulhat, hogy szét kell választania a titkosított és a nem titkosított fájlokat. Vegye figyelembe, hogy az egyszálas másolás jelentősen csökkenti a teljesítményt.           |
    |/fft    | Ennek használatával bármilyen fájlrendszerben csökkentheti a timestamp részletességét.        |
    |/b      | A fájlokat biztonsági mentési módban másolja.        |
    |/z      | A fájlokat újraindítási módban másolja; akkor lehet rá szükség, ha a környezet instabil. Ez a beállítás a fokozott naplózás miatt csökkenti az átviteli sebességet.      |
    | /zb    | Az újraindítási mód használata. A hozzáférés megtagadása esetén áttér a biztonsági mentési mód használatára. Ez a beállítás csökkenti az átviteli sebességet az ellenőrzőpontok létrehozása miatt.         |
    |/efsraw | Az összes titkosított fájlt EFS feldolgozatlan módban másolja át. Csak titkosított fájlokhoz használja.         |
    |napló +:\<logfile >| Hozzáfűzi a kimenetet a meglévő naplófájlhoz.|
    
 
    A következő minta a Robocopy parancs kimenetét mutatja, hogy fájlokat másoljon a Data Box Heavyba.

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

2. A teljesítmény optimalizálása érdekében használja a következő Robocopy-paramétereket az adatok másolásához. (Az alábbi számok a legjobb eseteket jelentik.)

    | Platform    | Többnyire kis méretű fájlok < 512 kB    | Többnyire közepes méretű fájlok 512 KB – 1 MB  | Többnyire nagy méretű fájlok > 1 MB                             |
    |-------------|--------------------------------|----------------------------|----------------------------|
    | Data Box Heavy | 6 Robocopy-munkamenet <br> 24 szál munkamenetenként | 6 Robocopy-munkamenet <br> 16 szál munkamenetenként | 6 Robocopy-munkamenet <br> 16 szál munkamenetenként |


    A Robocopy-paranccsal kapcsolatos további információért lásd [a Robocopyt és néhány példát](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx) bemutató témakört.

3. Nyissa meg a célmappát a másolt fájlok megtekintéséhez és ellenőrzéséhez.

    ![Másolt fájlok megtekintése](media/data-box-heavy-deploy-copy-data/view-copied-files-1.png)


4. Az Adatmásolás során:

    - A fájlneveket, a méreteket és a formátumot érvényesíti a rendszer, hogy azok megfeleljenek az Azure-objektumoknak és a tárolási korlátozásoknak, valamint az Azure-fájlok és a tárolók elnevezési konvencióinak.
    - Az adatok integritásának biztosítása érdekében a rendszer az ellenőrzőösszeget is inline-ként számítja ki.

    Ha hibába ütközik a másolási folyamat során, töltse le a hibafájlokat a hibaelhárításhoz. Kattintson a nyíl ikonra a hibaüzenetek letöltéséhez.

    ![Fájlok letöltése](media/data-box-heavy-deploy-copy-data/download-error-files.png)

    További információ: a [hibanapló megtekintése az adatok másolása során Data Box Heavy](data-box-logs.md#view-error-log-during-data-copy). Az adatmásolási hibák részletes listáját lásd: [Data Box Heavy problémák elhárítása](data-box-troubleshoot.md).

5. Nyissa meg a fájlt a Jegyzettömbben. A következő hibaüzenet azt jelzi, hogy az adathalmaz nem megfelelően van igazítva.

    ![Naplófájl megnyitása](media/data-box-heavy-deploy-copy-data/open-error-file.png)
    
    Az oldal blobja esetében az adatnak 512 bájtos igazítással kell rendelkeznie. Az adateltávolítást követően a hiba a következő képernyőképen látható módon oldódik fel.

    ![Hiba feloldva](media/data-box-heavy-deploy-copy-data/error-resolved.png)

6. A másolás befejezése után lépjen az **irányítópult megtekintése** lapra. Ellenőrizze a felhasznált területet és a szabad területet az eszközön.
    
    ![A szabad és a felhasznált tárhely ellenőrzése az irányítópulton](media/data-box-heavy-deploy-copy-data/verify-used-space-dashboard.png)

Ismételje meg a fenti lépéseket az adatnak az eszköz második csomópontjára való másolásához.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megAzure Data Box Heavy ismerte a következő témaköröket:

> [!div class="checklist"]
> * Kapcsolódás Data Box Heavyhoz
> * Adatmásolás Data Box Heavyba


Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan szállíthatja vissza a Data Box Heavyt a Microsoftnak.

> [!div class="nextstepaction"]
> [A Azure Data Box Heavy elszállítása a Microsoftnak](./data-box-heavy-deploy-picked-up.md)

::: zone-end

::: zone target = "chromeless"

### <a name="copy-data-via-smb"></a>Adatok másolása SMB-n keresztül

1. Ha Windows-gazdagépet használ, használja az alábbi parancsot az SMB-megosztásokhoz való kapcsolódáshoz:

    `\\<IP address of your device>\ShareName`

2. A megosztás eléréséhez szükséges hitelesítő adatokat a Data Box helyi webes kezelőfelületének **Connect & copy** (Kapcsolódás és másolás) lapján tekintheti meg.

3. Egy SMB-kompatibilis fájlmásolás-eszköz, például a Robocopy használatával másolhatja az adatfájlokat a megosztásokra.

Részletes útmutatásért lépjen az [oktatóanyag: Adatmásolás Azure Data Box SMB](data-box-heavy-deploy-copy-data.md)-n keresztül.

### <a name="copy-data-via-nfs"></a>Adatok másolása NFS-en keresztül

1. Ha NFS-gazdagépet használ, használja az alábbi parancsot az NFS-megosztások csatlakoztatásához:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

2. A megosztás hozzáférési hitelesítő adatainak beszerzéséhez nyissa meg a Data Box Heavy helyi webes FELÜLETének **kapcsolódás & másolása lapját** .
3. Az `cp` vagy`rsync` parancs használatával másolja az adatait. 
4. Ismételje meg ezeket a lépéseket a Data Box Heavy második csomópontjára való kapcsolódáshoz és az adatmásoláshoz.

Részletes útmutatásért lépjen az [oktatóanyag: Az Adatmásolás Azure Data Box NFS](data-box-heavy-deploy-copy-data-via-nfs.md)-en keresztül.

### <a name="copy-data-via-rest"></a>Adatok másolása REST használatával

1. Az adatok REST API-kon keresztüli Data Box blob Storage használatával történő másolásához *http* -vagy *https*-kapcsolaton keresztül is csatlakozhat.
2. Az Adatmásolás Data Box blob Storage-ba történő másolásához használhatja a AzCopy.
3. Ismételje meg ezeket a lépéseket a Data Box Heavy második csomópontjára való kapcsolódáshoz és az adatmásoláshoz.

Részletes útmutatásért lépjen az [oktatóanyag: Adatok másolása Azure Data Box blob Storage-ba REST API](data-box-heavy-deploy-copy-data-via-rest.md)-k használatával.

### <a name="copy-data-via-data-copy-service"></a>Adatmásolás az adatmásolási szolgáltatás használatával

1. Az adatok adatmásolási szolgáltatással történő másolásához létre kell hoznia egy feladatot. A Data Box Heavy helyi webes FELÜLETén lépjen a **kezelés > Adatmásolási > létrehozás**elemre.
2. Adja meg a paramétereket, és hozzon létre egy feladatot.
3. Ismételje meg ezeket a lépéseket a Data Box Heavy második csomópontjára való kapcsolódáshoz és az adatmásoláshoz.

Részletes útmutatásért lépjen az [oktatóanyag: Az adatmásolási szolgáltatás segítségével másolja át az adatAzure Data Box Heavyba](data-box-heavy-deploy-copy-data-via-copy-service.md).

### <a name="copy-data-to-managed-disks"></a>Az Adatmásolás a felügyelt lemezekre

1. A Data Box Heavy eszköz megrendelése esetén a felügyelt lemezeket a tároló célhelyként kell kiválasztani.
2. Az SMB-vagy NFS-megosztásokon keresztül csatlakozhat Data Box Heavyhoz.
3. Az Adatmásolás SMB-vagy NFS-eszközökön keresztül végezhető el.
4. Ismételje meg ezeket a lépéseket a Data Box Heavy második csomópontjára való kapcsolódáshoz és az adatmásoláshoz.

Részletes útmutatásért lépjen az [oktatóanyag: A Data Box használatával az Azure](data-box-heavy-deploy-copy-data-from-vhds.md)-ban felügyelt lemezként is nagy mennyiségű adatimportálást használhat.

::: zone-end


