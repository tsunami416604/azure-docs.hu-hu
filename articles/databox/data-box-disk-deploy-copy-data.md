---
title: Adatok másolása Microsoft Azure Data Box Diskre | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan másolhat adatokat egy Azure Data Box Diskre.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 10/31/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: ade409760e3946e9d574a304afc5b48ec4279157
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2018
ms.locfileid: "50740263"
---
# <a name="tutorial-copy-data-to-azure-data-box-disk-and-verify"></a>Oktatóanyag: Adatok másolása Azure Data Box Diskre, majd azok ellenőrzése

A jelen oktatóanyag ismerteti, hogyan másolhat adatokat a gazdagépről, majd hogyan hozhat létre ellenőrzőösszegeket az adatok integritásának ellenőrzéséhez.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Adatok másolása Data Box Diskre
> * Az adatok ellenőrzése

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg az alábbiakról:
- Befejezte az [Azure Data Box Disk üzembe helyezését és konfigurálását ismertető oktatóanyagot](data-box-disk-deploy-set-up.md).
- Lemezeit feloldotta és csatlakoztatta az ügyfélszámítógéphez.
- Az ügyfélszámítógépnek, amelyet az adatok a lemezekre történő másolásához használ, [támogatott operációs rendszert](data-box-disk-system-requirements.md) kell futtatnia.


## <a name="copy-data-to-disks"></a>Adatok másolása a lemezekre

Az alábbi lépések elvégzésével csatlakoztathatja, majd másolhatja át az adatokat a Data Box Diskre.

1. Tekintse meg a zárolásfeloldás után a meghajtó tartalmát.

    ![Meghajtó tartalmának megtekintése](media/data-box-disk-deploy-copy-data/data-box-disk-content.png)
 
2. Másolja át az importálni kívánt adatokat blokkblobok formájában a BlockBlob mappába. Hasonlóképpen másolja át a VHD/VHDX és hasonló típusú adatokat a PageBlob mappába. 

    A rendszer a BlockBlob és a PageBlob mappa alatt található minden almappához létrehoz egy tárolót az Azure Storage-fiókban. A BlockBlob és a PageBlob mappa alatt található összes fájl az Azure Storage-fiók alatti alapértelmezett `$root` tárolóba lesz átmásolva. A `$root` tárolóban található összes fájl mindig blokkblobként lesz feltöltve.

    Ha vannak fájlok és mappák a gyökérkönyvtárban, akkor át kell azokat helyeznie egy másik mappába az adatok másolásának megkezdése előtt.

    A tároló- és blobnevek esetében mindig kövesse az Azure elnevezési követelményeit.

    #### <a name="azure-naming-conventions-for-container-and-blob-names"></a>A tárolókra és blobokra vonatkozó Azure elnevezési konvenciók
    |Entitás   |Konvenciók  |
    |---------|---------|
    |Tárolónevek blokkblob és lapblob esetén     |Betűvel vagy számmal kell kezdődnie, és csak kisbetűket, számokat és kötőjelet (-) tartalmazhat. Minden kötőjel (-) előtt és után közvetlenül egy betűnek vagy számnak kell állnia. A tárolók nevében nem szerepelhetnek egymást követő kötőjelek. <br>Érvényes DNS-névnek kell lennie, amely 3–63 karakter hosszúságú lehet.          |
    |Blobnevek blokkblob és lapblob esetén    |A blobnevek megkülönböztetik a kis- és nagybetűket, illetve bármilyen karakterkombinációt tartalmazhatnak <br>A blob nevének 1–1024 karakter hosszúságúnak kell lennie.<br>A fenntartott URL-karaktereket escape-karakterrel kell jelölni.<br>A blob nevét alkotó részleges útvonalak száma legfeljebb 254 lehet. A részleges útvonalak két elválasztókarakter (pl. a perjel „/”) közötti karakterláncok, amelyek megegyeznek egy virtuális könyvtár nevével.         |

    > [!IMPORTANT] 
    > Minden tároló és blob nevének követnie kell az [Azure elnevezési konvencióit](data-box-disk-limits.md#azure-block-blob-and-page-blob-naming-conventions). Ha a szabályok nem teljesülnek, az adatok az Azure-ba való feltöltése meghiúsul.

3. Fájlok másolása esetén győződjön meg arról, hogy a fájlok mérete nem haladja meg blokkblobok esetén a ~4,7 TiB-ot, lapblobok esetén pedig a ~8 TiB-ot. 
4. Az adatok a Fájlkezelővel és húzással is másolhatók. Használhat bármilyen SMB-kompatibilis fájlmásoló eszközt (pl.: Robocopy) is az adatok másolására. Az alábbi Robocopy-paranccsal több másolási feladat is elindítható:

    `Robocopy <source> <destination>  * /MT:64 /E /R:1 /W:1 /NFL /NDL /FFT /Log:c:\RobocopyLog.txt` 
    
    A parancshoz használható paraméterek és beállítások az alábbi táblázatban láthatók:
    
    |Paraméterek/beállítások  |Leírás |
    |--------------------|------------|
    |Forrás            | Megadja a forráskönyvtár elérési útját.        |
    |Cél       | Megadja a célkönyvtár elérési útját.        |
    |/E                  | Átmásolja az alkönyvtárakat, az üres könyvtárakkal együtt. |
    |/MT[:N]             | Többszálas másolatokat hoz létre N szállal, ahol az N egy 1 és 128 közötti egész szám. <br>Az N alapértelmezett értéke 8.        |
    |/R: <N>             | A meghiúsult másolások esetén indított újrapróbálkozások számát határozza meg. Az N alapértelmezett értéke 1 000 000 (egymillió újrapróbálkozás).        |
    |/W: <N>             | Az újrapróbálkozások közötti várakozási időt határozza meg másodpercben. Az N alapértelmezett értéke 30 (30 másodperc várakozási idő).        |
    |/NFL                | Azt adja meg, hogy a fájlnevek ne legyenek naplózva.        |
    |/NDL                | Azt adja meg, hogy a könyvtárnevek ne legyenek naplózva.        |
    |/FFT                | FAT-fájlidőket feltételez (két másodperces pontosság).        |
    |/Log:<Log File>     | Beírja az állapotkimenetet a naplófájlba (felülírja a már létező naplófájlt).         |

    Több lemez is használható párhuzamosan, és minden lemezen egyszerre több feladat futtatható. 

6. Ellenőrizze a másolás állapotát, amikor a feladat folyamatban van. Az alábbi minta a fájloknak a Data Box Diskre történő másolásához használt Robocopy-parancs kimenetét mutatja be.

    ```
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
    
    C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.126.76.172\devicemanagertest1_AzFile\templates /MT:64 /E /R:1 /W:1 /FFT 
    -------------------------------------------------------------------------------
       ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
      Started : Thursday, March 8, 2018 2:34:58 PM
       Source : C:\Git\azure-docs-pr\contributor-guide\
         Dest : \\10.126.76.172\devicemanagertest1_AzFile\templates\
    
        Files : *.*
    
      Options : *.* /DCOPY:DA /COPY:DAT /MT:8 /R:1000000 /W:30
    
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
       Times :   0:00:05   0:00:00                       0:00:00   0:00:00
        
       Speed :                5620 Bytes/sec.
       Speed :               0.321 MegaBytes/min.
       Ended : Thursday, March 8, 2018 2:34:59 PM
        
    C:\Users>
    ```
 
    
7. Nyissa meg a célmappát a másolt fájlok megtekintéséhez és ellenőrzéséhez. Ha hibába ütközik a másolási folyamat során, töltse le a naplófájlokat a hibaelhárításhoz. A naplófájlok a Robocopy-parancsban megadott helyen találhatók.
 


> [!IMPORTANT]
> - Arról Önnek kell gondoskodnia, hogy az adatokat a helyes adatformátumnak megfelelő mappákba másolja. A blokkblob-adatokat például másolja a blokkbloboknak fenntartott mappába. Ha az adatok formátuma nem egyezik a megfelelő mappával (annak tárolási típusával), akkor egy későbbi lépés során az Azure-ba történő adatfeltöltés sikertelen lesz.
> -  Adatok másolása közben győződjön meg arról, hogy az adatok mérete megfelel az [Azure Storage és a Data Box Disk korlátaival](data-box-disk-limits.md) foglalkozó cikkben ismertetett méretkorlátoknak. 
> - Ha a Data Box Disk által éppen feltöltés alatt álló adatokat egyidejűleg egy másik alkalmazás is feltölti a Data Box Disken kívül, ez a feltöltési feladatok meghiúsulásához és az adatok meghibásodásához vezethet.

### <a name="split-and-copy-data-to-disks"></a>Adatok szétosztása és másolása lemezekre

Ez a választható eljárás akkor lehet hasznos, ha több lemezt használ, és nagy adatkészletet kell szétosztania és átmásolnia ezekre a lemezre. A Data Box másolásfelosztó eszköze segítséget nyújt az adatok felosztásában és másolásában Windows rendszerű számítógépen.

1. Ellenőrizze Windows rendszerű számítógépén, hogy a Data Box másolásfelosztó eszköze le lett-e töltve és ki lett-e csomagolva egy helyi mappába. Ezt az eszközt a windowsos Data Box Disk-eszközkészlet részeként töltötte le.
2. Nyissa meg a Fájlkezelőt. Jegyezze fel az adatforrásként szolgáló meghajtó nevét és a Data Box Diskhez rendelt meghajtóbetűjeleket. 

     ![Felosztásos adatmásolás ](media/data-box-disk-deploy-copy-data/split-copy-1.png)
 
3. Keresse meg a másolni kívánt forrásadatokat. Ebben az esetben például a következőt:
    - A rendszer az alábbi blokkblobadatokat azonosította.

         ![Felosztásos adatmásolás ](media/data-box-disk-deploy-copy-data/split-copy-2.png)    

    - A rendszer az alábbi lapblobadatokat azonosította.

         ![Felosztásos adatmásolás ](media/data-box-disk-deploy-copy-data/split-copy-3.png)
 
4. Nyissa meg azt a mappát, amelybe a szoftver ki lett csomagolva. Keresse meg a mappában a SampleConfig.json fájlt. Ez egy írásvédett fájl, amelyet módosíthat és menthet.

   ![Felosztásos adatmásolás ](media/data-box-disk-deploy-copy-data/split-copy-4.png)
 
5. Módosítsa a SampleConfig.json fájlt.
 
    - Adja meg a feladat nevét. Ezzel létrehoz egy mappát a Data Box Diskben, amely a későbbiekben tárolóként fog szolgálni a lemezekhez társított Azure Storage-fiókban. A feladat nevének meg kell felelnie az Azure-tárolókra vonatkozó elnevezési konvencióknak. 
    - Adja meg a forrásútvonalat, és jegyezze fel annak a SampleConfigFile.json fájlban megadott formátumát. 
    - Adja meg a céllemezeknek megfelelő meghajtóbetűjeleket. Ekkor elkezdődik a forrásadatok másolása a lemezekre.
    - Adja meg a naplófájlok elérési útvonalát. Alapértelmezés szerint ezek a fájlok abba a könyvtárba kerülnek, ahol az .exe fájl is található.

     ![Felosztásos adatmásolás ](media/data-box-disk-deploy-copy-data/split-copy-5.png)

6. A fájlformátum ellenőrzéséhez nyissa meg a JSONlint eszközt. Mentse a fájlt ConfigFile.json néven. 

     ![Felosztásos adatmásolás ](media/data-box-disk-deploy-copy-data/split-copy-6.png)
 
7. Nyisson meg egy parancssori ablakot. 

8. Futtassa a DataBoxDiskSplitCopy.exe fájlt. Típus

    `DataBoxDiskSplitCopy.exe PrepImport /config:<Your-config-file-name.json>`

     ![Felosztásos adatmásolás ](media/data-box-disk-deploy-copy-data/split-copy-7.png)
 
9. Nyomja le az Enter billentyűt a szkript folytatásához.

    ![Felosztásos adatmásolás ](media/data-box-disk-deploy-copy-data/split-copy-8.png)
  
10. Az adatkészlet felosztását és másolását követően a másolásfelosztó eszköz összegzést készít a másolási munkamenetről. Az alábbiakban egy példa látható a kimenetre.

    ![Felosztásos adatmásolás ](media/data-box-disk-deploy-copy-data/split-copy-9.png)
 
11. Ellenőrizze, hogy az adatok sikeresen szét lettek-e osztva a céllemezekre. 
 
    ![Felosztásos adatmásolás ](media/data-box-disk-deploy-copy-data/split-copy-10.png)
    ![Felosztásos adatmásolás ](media/data-box-disk-deploy-copy-data/split-copy-11.png)
     
    Ha alaposabban megvizsgálja az n: meghajtó tartalmát, láthatja, hogy két almappa jött létre a blokkblob és a lapblob formátumú adatok számára.
    
     ![Felosztásos adatmásolás ](media/data-box-disk-deploy-copy-data/split-copy-12.png)

12. Ha a másolási munkamenet meghiúsul, a következő paranccsal állíthatja helyre és folytathatja azt:

    `DataBoxDiskSplitCopy.exe PrepImport /config:<configFile.json> /ResumeSession`


Az adatmásolást követő lépés az adatok ellenőrzése. 


## <a name="validate-data"></a>Az adatok ellenőrzése 

Az adatok ellenőrzéséhez hajtsa végre a következő lépéseket.

1. Futtassa a `DataBoxDiskValidation.cmd` parancsot az ellenőrzőösszeg-érvényesítéshez a meghajtó *DataBoxDiskImport* mappájában. 
    
    ![A Data Box Disk érvényesítési eszköz kimenete](media/data-box-disk-deploy-copy-data/data-box-disk-validation-tool-output.png)

2. Válassza ki a megfelelő lehetőséget. **Javasoljuk, hogy a fájlok érvényesítéséhez és az ellenőrzőösszegek létrehozásához mindig a 2. lehetőséget válassza**. Az adatok méretétől függően a lépésben írtak elvégzése hosszabb időt is igénybe vehet. Ha a szkript futtatása befejeződött, zárja be a parancsablakot. Ha hiba történik az érvényesítés és az ellenőrzőösszeg létrehozása során, a rendszer értesíti, és megjelenít egy, a hibanaplóra mutató hivatkozást.

    ![Ellenőrzőösszeg kimenete](media/data-box-disk-deploy-copy-data/data-box-disk-checksum-output.png)

    > [!TIP]
    > - Állítsa alaphelyzetbe az eszközt két futtatás között.
    > - Az 1. lehetőség kis méretű (néhány kB-os) fájlokat tartalmazó, nagy adatkészlet fájljainak ellenőrzésére szolgál. Ezekben az esetekben az ellenőrzőösszeg létrehozása nagyon hosszú időt vehet igénybe, és a folyamat jelentősen lelassíthatja a rendszert.

3. Több lemez használata esetén futtassa a parancsot minden lemezen.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure Data Box Diskkel kapcsolatos alábbi témakörökkel ismerkedett meg:

> [!div class="checklist"]
> * Adatok másolása Data Box Diskre
> * Adatok integritásának ellenőrzése

A következő oktatóanyag azt ismerteti, hogyan küldheti vissza a Data Box Disket, valamint hogyan ellenőrizheti az Azure-ba történő adatfeltöltést.

> [!div class="nextstepaction"]
> [Az Azure Data Box visszaküldése a Microsoftnak](./data-box-disk-deploy-picked-up.md)

