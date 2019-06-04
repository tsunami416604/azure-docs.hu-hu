---
title: Az oktatóanyag az Azure Data Box nehéz SMB-n keresztül az adatok másolása |} A Microsoft Docs
description: Ismerje meg, hogyan másolhat adatokat az Azure Data Box (nagy erőforrásigényű) az SMB-n keresztül
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 05/28/2019
ms.author: alkohli
ms.openlocfilehash: 8ee96f2e06071d60eb97596687387fd80ba14cc3
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66496273"
---
# <a name="tutorial-copy-data-to-azure-data-box-heavy-via-smb-preview"></a>Oktatóanyag: Adatok másolása az Azure Data Box nehéz SMB (előzetes verzió) használatával

Ez az oktatóanyag ismerteti csatlakozik, és adatokat másol a gazdaszámítógépet, a helyi webes felhasználói felület.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Csatlakozhat a Data Box (nagy erőforrásigényű)
> * Másolja az adatokat a Data Box (nagy erőforrásigényű)


## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg az alábbiakról:

1. Ön teljesítette a [oktatóanyag: Állítsa be az Azure Data Box nehéz](data-box-deploy-set-up.md).
2. A Data Box nehéz kapott, és a rendelés állapota a portálon **kézbesítések**.
3. Másolja át a Data Box nehéz kívánt adatokat a gazdaszámítógép rendelkezik. A gazdaszámítógépen:
    - egy [támogatott operációs rendszernek](data-box-system-requirements.md) kell futnia;
    - egy nagy sebességű hálózathoz kell csatlakoznia. A leggyorsabb másolási megbízhatóbbak két 40-GbE kapcsolatot (egy csomópontonkénti), amellyel párhuzamosan. Ha nincs elérhető kapcsolat 40-GbE, javasoljuk, hogy legalább két 10-GbE kapcsolatot (egy csomópontonkénti).

## <a name="connect-to-data-box-heavy-shares"></a>Csatlakozhat a Data Box nehéz megosztások

A kiválasztott tárfiók alapján a Data Box nehéz hoz létre, akár:
- Három megosztás minden társított tárfiókhoz, GPv1-hez és GPv2-höz.
- Egy prémium szintű Storage-megosztást.
- Blob storage-fiók egy megosztást.

Az eszköz a csomópontok mindkét ezeken a megosztásokon jönnek létre.

A block blob és az oldal blob megosztások:
- Az első szintű entitások tárolói.
- Második szintű entitások blobok legyenek.

A fájlmegosztások az Azure Files számára:
- Az első szintű entitások megosztások.
- Második szintű entitásokat a fájlok.

Az alábbi táblázat az UNC elérési utat a megosztásokat, ahol az adatok feltöltése a Data Box (nagy erőforrásigényű) és az Azure Storage elérési út URL. Az Azure Storage elérési út utolsó URL-cím származtatható megosztás UNC elérési útját.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Az Azure Block blobs | <li>Megosztás UNC elérési útja: `\\<DeviceIPAddress>\<StorageAccountName_BlockBlob>\<ContainerName>\files\a.txt`</li><li>Az Azure Storage URL-címe: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure-lapblobok  | <li>Megosztás UNC elérési útja: `\\<DeviceIPAddres>\<StorageAccountName_PageBlob>\<ContainerName>\files\a.txt`</li><li>Az Azure Storage URL-címe: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>Megosztás UNC elérési útja: `\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>\files\a.txt`</li><li>Az Azure Storage URL-címe: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |      

A lépéseket egy Windows vagy Linux-ügyfél használatával történő kapcsolódás eltérőek.

> [!NOTE]
> Kövesse a lépéseket, mind a csomópontok párhuzamosan az eszköz csatlakozni.

### <a name="connect-on-a-windows-system"></a>Csatlakozás a Windows rendszeren

Ha egy Windows Server-állomás számítógépen használja, kövesse az alábbi lépéseket a Data Box (nagy erőforrásigényű) való kapcsolódáshoz.

1. Az első lépés a hitelesítés elvégzése, majd a munkamenet elindítása. Lépjen a **Connect and copy** (Kapcsolódás és másolás) elemre. Kattintson a **Get credentials** (Hitelesítő adatok beszerzése) lehetőségre a tárfiókhoz társított megosztások hitelesítő adataihoz való hozzáféréshez.

    ![Megosztások hitelesítő adatainak beszerzése 1](media/data-box-heavy-deploy-copy-data/get-share-credentials-1.png)

2. Az Access share and copy data (Megosztási és másolási adatok másolása) párbeszédpanelen másolja ki a megosztásnak megfelelő **Username** (Felhasználónév) és **Password** (Jelszó) értékeket. Kattintson az **OK** gombra.
    
    ![Megosztások hitelesítő adatainak beszerzése 1](media/data-box-heavy-deploy-copy-data/get-share-credentials-2.png)

3. A tárfiók társított megosztás eléréséhez (*databoxe2etest* az alábbi példában) a gazdagép számítógépről, nyisson meg egy parancsablakot. A parancssorba írja be a következőt:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Az adatok formátumától függően a megosztások útvonalai a következők:
    - Az Azure blokkblob- `\\10.100.10.100\databoxe2etest_BlockBlob`
    - Az Azure lapblob- `\\10.100.10.100\databoxe2etest_PageBlob`
    - Az Azure Files- `\\10.100.10.100\databoxe2etest_AzFile`
    
4. Ha a rendszer kéri, adja meg a megosztás jelszavát. A következő példa bemutatja, hogyan kell csatlakozni egy megosztáshoz a fenti parancs használatával.

    ```
    C:\Users\Databoxuser>net use \\10.100.10.100\databoxe2etest_BlockBlob /u:databoxe2etest
    Enter the password for 'databoxe2etest' to connect to '10.100.10.100':
    The command completed successfully.
    ```

4. Nyomja le a Windows + R billentyűkombinációt. A **Futtatás** ablakban adja meg a következőt: `\\<device IP address>`. Kattintson a **OK** , nyissa meg a Fájlkezelőt.
    
    ![Kapcsolódás a megosztáshoz a Fájlkezelővel 2](media/data-box-heavy-deploy-copy-data/connect-shares-file-explorer-1.png)

    Meg kell jelennie a megosztások, mappák.
    
    ![Kapcsolódás a megosztáshoz a Fájlkezelővel 2](media/data-box-heavy-deploy-copy-data/connect-shares-file-explorer-2.png)

    **Mindig hozzon létre egy mappát azokhoz a fájlokhoz, amelyeket másolni szeretne a megosztás alatt, majd másolja a fájlokat a létrehozott mappába**. Blokkblob típusú a mappában létrehozott, és a blob megosztások lap egy tárolóban, amelyhez data nahrávají blobként jelöli. Nem lehet másolni a fájlokat közvetlenül a *legfelső szintű* mappát a storage-fiókban.
    
### <a name="connect-on-a-linux-system"></a>Csatlakozás Linux rendszeren

Egy Linux-ügyfél használata esetén a következő paranccsal csatlakoztatni az SMB-fájlmegosztást.

```
sudo mount -t nfs -o vers=2.1 10.126.76.172:/databoxe2etest_BlockBlob /home/databoxubuntuhost/databox
```

A `vers` paraméter, amely támogatja a Linux-állomáshoz SMB verziója. Csatlakoztassa a megfelelő verzióját a fenti parancsban.

SMB, amely támogatja a Data Box nehéz verzióiért lásd: [fájlrendszerek támogatott Linux-ügyfelek](data-box-heavy-system-requirements.md#supported-file-systems-for-linux-clients).

## <a name="copy-data-to-data-box-heavy"></a>Másolja az adatokat a Data Box (nagy erőforrásigényű)

Miután csatlakozott a Data Box nehéz megosztásokra, a következő lépés az adatok másolása.

### <a name="copy-considerations"></a>Másolja a szempontok

Mielőtt elkezdené az adatok másolását, tekintse át az alábbiakat:

- Győződjön meg arról, hogy az adatok másolása megosztások, amelyek megfelelnek a megfelelő adatok formátuma. A blokkblobadatokat például másolja a blokkbloboknak fenntartott megosztásba. Másolja a VHD-k lapblob.

    Ha az adatok formátuma nem felel meg a megfelelő megosztási típusát, majd egy későbbi lépésben, az adatok feltöltése az Azure-bA sikertelen lesz.
-  Adatok másolása során győződjön meg arról, hogy megfelel-e az adatok mérete a méretbeli korlátokat ismertetett a [az Azure storage és a Data Box nehéz korlátok](data-box-heavy-limits.md).
- Ha a Data Box nehéz kívül más alkalmazás egyidejűleg fel az adatokat, amelyek feltöltése folyamatban van a Data Box nehéz, majd emiatt feltöltési feladat hibák és az adatok sérülését.
- Azt javasoljuk, hogy:
    - Egy időben ne használja az SMB és NFS is.
    - Azonos teljes cél Azure-beli ugyanazokat az adatokat másolja.
     
  Ezekben az esetekben nem lehet megállapítani a végső eredményt.
- Mindig hozzon létre egy mappát a fájlok másolása a megosztás alatt, és ezután másolja a fájlokat a mappában, melyet. Blokkblob típusú a mappában létrehozott, és a blob megosztások lap egy tárolóban, amelyhez az adatfeltöltés blobként jelöli. Nem lehet másolni a fájlokat közvetlenül a *legfelső szintű* mappát a storage-fiókban.

Miután csatlakozott az SMB-megosztás, megkezdheti az adatok másolását.

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
    |/nfl    |Itt adhatja meg, hogy a fájlnevek nem naplózza.         |
    |/ndl    |Meghatározza, hogy könyvtárának nevében nem.        |
    |/np     |Azt határozza meg, hogy ne jelenjen meg a másolási művelet állapota (az addig átmásolt fájlok vagy könyvtárak száma). Az állapot megjelenítése jelentősen csökkenti a teljesítményt.         |
    |/MT     | Több szál használata. 32 vagy 64 szál használata ajánlott. Titkosított fájlokhoz ez a beállítás nem használható. Előfordulhat, hogy szét kell választania a titkosított és a nem titkosított fájlokat. Vegye figyelembe, hogy az egyszálas másolás jelentősen csökkenti a teljesítményt.           |
    |/fft    | Ennek használatával bármilyen fájlrendszerben csökkentheti a timestamp részletességét.        |
    |/b      | A fájlokat biztonsági mentési módban másolja.        |
    |/z      | A fájlokat újraindítási módban másolja; akkor lehet rá szükség, ha a környezet instabil. Ez a beállítás a fokozott naplózás miatt csökkenti az átviteli sebességet.      |
    | /zb    | Az újraindítási mód használata. A hozzáférés megtagadása esetén áttér a biztonsági mentési mód használatára. Ez a beállítás csökkenti az átviteli sebességet az ellenőrzőpontok létrehozása miatt.         |
    |/efsraw | Az összes titkosított fájlt EFS feldolgozatlan módban másolja át. Csak titkosított fájlokhoz használja.         |
    |napló +:\<naplófájl >| Hozzáfűzi a kimenetet a meglévő naplófájlhoz.|
    
 
    A következő minta bemutatja a Data Box nagy fájlok másolása a robocopy parancs kimenetét.

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

2. A teljesítmény optimalizálása érdekében használja a következő Robocopy-paramétereket az adatok másolásához. (Az alábbi számok jelölik a legjobb használatieset-forgatókönyveinek.)

    | Platform    | Többnyire kis méretű fájlok < 512 kB    | Többnyire közepes méretű fájlok 512 KB – 1 MB  | Többnyire nagy méretű fájlok > 1 MB                             |
    |-------------|--------------------------------|----------------------------|----------------------------|
    | Data Box Heavy | 6 Robocopy munkamenet <br> 24 szál munkamenetenként | 6 Robocopy munkamenet <br> 16 szál munkamenetenként | 6 Robocopy munkamenet <br> 16 szál munkamenetenként |


    A Robocopy-paranccsal kapcsolatos további információért lásd [a Robocopyt és néhány példát](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx) bemutató témakört.

3. Nyissa meg a célmappát a másolt fájlok megtekintéséhez és ellenőrzéséhez.

    ![Másolt fájlok megtekintése](media/data-box-heavy-deploy-copy-data/view-copied-files-1.png)


4. Mivel a rendszer az adatokat másolja:

    - A fájlnevek, méretű és formátumú ellenőrzi annak érdekében, hogy azok megfelelnek az Azure objektum- és tárterületi korlátozásai, valamint az Azure-beli és a tároló elnevezési konvenciói.
    - Ahhoz, hogy az adatok integritásának megőrzése, ellenőrzőösszege is számított beágyazott.

    Ha hibába ütközik a másolási folyamat során, töltse le a hibafájlokat a hibaelhárításhoz. A nyíl ikonra a hiba-fájlok letöltésére.

    ![Hiba történt a fájlok letöltése](media/data-box-heavy-deploy-copy-data/download-error-files.png)

    További információkért lásd: [adatmásolás a Data Box nehéz során hiba történt a naplók megtekintéséhez](data-box-logs.md#view-error-log-during-data-copy). Adatok másolása során hibák részletes listájáért lásd: [Data Box nehéz hibaelhárítása problémák](data-box-troubleshoot.md).

5. Nyissa meg a hiba fájlt a Jegyzettömbben. A következő hiba történt a fájl azt jelzi, hogy az adatok nincs megfelelően igazítva.

    ![Nyissa meg a hiba történt a fájl](media/data-box-heavy-deploy-copy-data/open-error-file.png)
    
    Az adatok egy lapblobra kell lennie a igazítva 512 bájt. Ezek az adatok eltávolítása után a hiba szünteti meg az alábbi képernyőképen látható módon.

    ![A hiba feloldása](media/data-box-heavy-deploy-copy-data/error-resolved.png)

6. A Másolás befejezése után lépjen **irányítópult megtekintése** lapot. Ellenőrizze a használatban lévő tárterület és a szabad terület az eszközön.
    
    ![A szabad és a felhasznált tárhely ellenőrzése az irányítópulton](media/data-box-heavy-deploy-copy-data/verify-used-space-dashboard.png)

Ismételje meg a fenti lépéseket a második csomópontot az eszköz be adatmásolás céljából.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megismerkedett az Azure Data Box nehéz témakörök például:

> [!div class="checklist"]
> * Csatlakozhat a Data Box (nagy erőforrásigényű)
> * Másolja az adatokat a Data Box (nagy erőforrásigényű)


Folytassa a következő oktatóanyaggal, megtudhatja, hogyan tehetnek a Data Box nehéz a Microsoftnak.

> [!div class="nextstepaction"]
> [Az Azure Data Box (nagy erőforrásigényű) a Microsoft szállításra](./data-box-heavy-deploy-picked-up.md)

