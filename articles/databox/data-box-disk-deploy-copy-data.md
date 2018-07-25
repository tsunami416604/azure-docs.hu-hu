---
title: Adatok másolása Microsoft Azure Data Box Diskre | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan másolhat adatokat egy Azure Data Box Diskre.
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/12/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: b0769ba70f495728df5c38b43bae4059b27de88b
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39010820"
---
# <a name="tutorial-copy-data-to-azure-data-box-disk-and-verify"></a>Oktatóanyag: Adatok másolása Azure Data Box Diskre, majd azok ellenőrzése

A jelen oktatóanyag ismerteti, hogyan másolhat adatokat a gazdagépről, majd hogyan hozhat létre ellenőrzőösszegeket az adatok integritásának ellenőrzéséhez.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Adatok másolása Data Box Diskre
> * Adatok integritásának ellenőrzése

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg az alábbiakról:
- Befejezte az [Azure Data Box Disk üzembe helyezését és konfigurálását ismertető oktatóanyagot](data-box-disk-deploy-set-up.md).
- A lemezek ki vannak csomagolva, és be vannak kapcsolva.
- Rendelkezik egy gazdagéppel, amelyről adatokat szeretne másolni a lemezekre. A gazdaszámítógépen:
    - egy [támogatott operációs rendszernek](data-box-disk-system-requirements.md) kell futnia;
    - a [Windows PowerShell 4-es verziójának telepítve kell lennie](https://www.microsoft.com/download/details.aspx?id=40855);
    - a [.NET-keretrendszer 4.5-ös verziójának telepítve kell lennie](https://www.microsoft.com/download/details.aspx?id=30653).


## <a name="copy-data-to-disks"></a>Adatok másolása a lemezekre

Az alábbi lépések elvégzésével csatlakoztathatja, majd másolhatja át az adatokat a Data Box Diskre.

1. Tekintse meg a zárolásfeloldás után a meghajtó tartalmát. 

    ![Meghajtó tartalmának megtekintése](media/data-box-disk-deploy-copy-data/data-box-disk-content.png)
 
2. Másolja át az importálni kívánt adatokat blokkblobok formájában a BlockBlob mappába. Hasonlóképpen másolja át a VHD/VHDX és hasonló típusú adatokat a PageBlob mappába. 

    A rendszer a BlockBlob és a PageBlob mappa alatt található minden almappához létrehoz egy tárolót az Azure Storage-fiókban. A BlockBlob és a PageBlob mappa alatt található összes fájl az Azure Storage-fiók alatti alapértelmezett `$root` tárolóba lesz átmásolva. A `$root` tárolóban található összes fájl mindig blokkblobként lesz feltöltve.

    Ha vannak fájlok és mappák a gyökérkönyvtárban, akkor át kell azokat helyeznie egy másik mappába az adatok másolásának megkezdése előtt.

    A tároló- és blobnevek esetében mindig kövesse az Azure elnevezési követelményeit.

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
    |<Source>            | Megadja a forráskönyvtár elérési útját.        |
    |<Destination>       | Megadja a célkönyvtár elérési útját.        |
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

## <a name="verify-data-integrity"></a>Adatok integritásának ellenőrzése

Az adatok integritásának ellenőrzéséhez hajtsa végre a következő lépéseket.

1. Futtassa az `AzureExpressDiskService.ps1` parancsot az ellenőrzőösszeg-érvényesítéshez. A Fájlkezelőben keresse meg a meghajtó *AzureImportExport* mappáját. Kattintson a jobb gombbal, és válassza a **Futtatás PowerShell-lel** lehetőséget. 

    ![Ellenőrzőösszeg futtatása](media/data-box-disk-deploy-copy-data/data-box-disk-checksum.png)

2. Az adatok méretétől függően a lépésben írtak elvégzése hosszabb időt is igénybe vehet. Az adatintegritás ellenőrzési folyamatának összegzése, valamint a folyamat befejezéséhez szükséges idő megjelenik a szkript futásának befejezésekor. A parancsablakból való kilépéshez nyomja le az **Enter** billentyűt.

    ![Ellenőrzőösszeg kimenete](media/data-box-disk-deploy-copy-data/data-box-disk-checksum-output.png)

3. Több lemez használata esetén futtassa a parancsot minden lemezen.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure Data Box Diskkel kapcsolatos alábbi témakörökkel ismerkedett meg:

> [!div class="checklist"]
> * Adatok másolása Data Box Diskre
> * Adatok integritásának ellenőrzése

A következő oktatóanyag azt ismerteti, hogyan küldheti vissza a Data Box Disket, valamint hogyan ellenőrizheti az Azure-ba történő adatfeltöltést.

> [!div class="nextstepaction"]
> [Az Azure Data Box visszaküldése a Microsoftnak](./data-box-disk-deploy-picked-up.md)

