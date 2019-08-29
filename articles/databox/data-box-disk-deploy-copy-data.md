---
title: Az Adatmásolás Azure Data Box Diskba való másolásának oktatóanyaga | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan másolhat adatokat egy Azure Data Box Diskre.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 08/28/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: a0c34e30e52bd2a6d57e2cf8299f231f7f2960d9
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147939"
---
::: zone target="docs"

# <a name="tutorial-copy-data-to-azure-data-box-disk-and-verify"></a>Oktatóanyag: Adatmásolás Azure Data Box Diskre és ellenőrzés

::: zone-end

::: zone target="chromeless"

## <a name="copy-data-to-azure-data-box-disk-and-validate"></a>Adatmásolás Azure Data Box Diskba és ellenőrzés

A lemezek csatlakoztatása és zárolása után az adatok a forrás adatkiszolgálóról a lemezekre másolhatók. Az Adatmásolás befejezése után a másolt Adatmásolást kell végrehajtania. Az érvényesítés biztosítja, hogy az adatok sikeresen fel lesznek feltöltve az Azure-ba később.

::: zone-end

::: zone target="docs"

A jelen oktatóanyag ismerteti, hogyan másolhat adatokat a gazdagépről, majd hogyan hozhat létre ellenőrzőösszegeket az adatok integritásának ellenőrzéséhez.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Adatok másolása Data Box Diskre
> * Az adatok ellenőrzése

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg az alábbiakról:
- Elvégezte az [oktatóanyagot: Telepítse és konfigurálja a Azure Data Box Disk](data-box-disk-deploy-set-up.md).
- Lemezeit feloldotta és csatlakoztatta az ügyfélszámítógéphez.
- Az ügyfélszámítógépnek, amelyet az adatok a lemezekre történő másolásához használ, [támogatott operációs rendszert](data-box-disk-system-requirements.md##supported-operating-systems-for-clients) kell futtatnia.
- Győződjön meg arról, hogy az adatok választott tárolótípusa egyezik valamelyik [támogatott tárolótípussal](data-box-disk-system-requirements.md#supported-storage-types-for-upload).
- Tekintse át [a felügyelt lemezek korlátozásait az Azure-objektumok méretének korlátaiban](data-box-disk-limits.md#azure-object-size-limits).


## <a name="copy-data-to-disks"></a>Adatok másolása a lemezekre

Az adatlemezekre történő másolás előtt tekintse át az alábbi szempontokat:

- Arról Önnek kell gondoskodnia, hogy az adatokat a helyes adatformátumnak megfelelő mappákba másolja. A blokkblob-adatokat például másolja a blokkbloboknak fenntartott mappába. Ha az adatok formátuma nem egyezik a megfelelő mappával (annak tárolási típusával), akkor egy későbbi lépés során az Azure-ba történő adatfeltöltés sikertelen lesz.
- Adatok másolása közben győződjön meg arról, hogy az adatok mérete megfelel az [Azure Storage és a Data Box Disk korlátaival](data-box-disk-limits.md) foglalkozó cikkben ismertetett méretkorlátoknak.
- Ha a Data Box Disk által éppen feltöltés alatt álló adatokat egyidejűleg egy másik alkalmazás is feltölti a Data Box Disken kívül, ez a feltöltési feladatok meghiúsulásához és az adatok meghibásodásához vezethet.

   > [!IMPORTANT]
   >  Ha a rendelés létrehozása során a felügyelt lemezeket az egyik tárolási célhelyként adta meg, akkor a következő szakasz alkalmazható.

- Csak egy felügyelt lemez tartozhat egy adott névvel egy erőforráscsoporthoz az összes előkészített mappában és az összes Data Box Diskon. Ez azt jelenti, hogy az előlétrehozott mappákba feltöltött virtuális merevlemezeknek egyedi névvel kell rendelkezniük. Győződjön meg arról, hogy a megadott név nem egyezik egy erőforráscsoport már meglévő felügyelt lemezével. Ha a VHD-k nevei azonosak, akkor csak egy VHD-t konvertál a rendszer a névvel ellátott felügyelt lemezre. A többi virtuális merevlemezt a rendszer oldal blobként feltölti az átmeneti tárolási fiókba.
- Mindig másolja a virtuális merevlemezeket az egyik előlétrehozott mappába. Ha a virtuális merevlemezeket ezen mappákon kívül vagy egy Ön által létrehozott mappában másolja, a virtuális merevlemezek az Azure Storage-fiókba lesznek feltöltve, és nem felügyelt lemezként.
- A felügyelt lemezek létrehozásához csak a rögzített VHD-k tölthetők fel. A dinamikus virtuális merevlemezek, a különbséglemezek VHD-k vagy a VHDX-fájlok nem támogatottak.


Az alábbi lépések elvégzésével csatlakoztathatja, majd másolhatja át az adatokat a Data Box Diskre.

1. Tekintse meg a zárolásfeloldás után a meghajtó tartalmát. A meghajtón lévő előlétrehozott mappák és almappák listája eltér attól függően, hogy milyen beállítások vannak kiválasztva a Data Box Disk rendelés elhelyezésekor.

    |Kijelölt tároló célhelye  |Tárfiók típusa|Előkészítési tár fióktípus |Mappák és almappák  |
    |---------|---------|---------|------------------|
    |Tárfiók     |GPv1 vagy GPv2                 | NA | BlockBlob <br> PageBlob <br> AzureFile        |
    |Tárfiók     |BLOB Storage-fiók         | NA | BlockBlob        |
    |Felügyelt lemezek     |NA | GPv1 vagy GPv2         | ManagedDisk<ul> <li>PremiumSSD</li><li>StandardSSD</li><li>StandardHDD</li></ul>        |
    |Tárfiók <br> Felügyelt lemezek     |GPv1 vagy GPv2 | GPv1 vagy GPv2         |BlockBlob <br> PageBlob <br> AzureFile <br> ManagedDisk<ul> <li> PremiumSSD </li><li>StandardSSD</li><li>StandardHDD</li></ul>         |
    |Tárfiók <br> Felügyelt lemezek    |BLOB Storage-fiók | GPv1 vagy GPv2         |BlockBlob <br> ManagedDisk<ul> <li>PremiumSSD</li><li>StandardSSD</li><li>StandardHDD</li></ul>         |

    Alább látható egy képernyőkép egy olyan rendelésről, amelyben meg van adva egy GPv2 Storage-fiók:

    ![A lemezmeghajtó tartalma](media/data-box-disk-deploy-copy-data/data-box-disk-content.png)
 
2. Másolja az importálni kívánt adatfájlokat a *BlockBlob* mappába. Hasonlóképpen másolja az Adatmásolás, például a VHD/VHDX *PageBlob* mappát és az *AzureFile* mappába.

    A rendszer a BlockBlob és a PageBlob mappa alatt található minden almappához létrehoz egy tárolót az Azure Storage-fiókban. A BlockBlob és a PageBlob mappa alatt található összes fájl az Azure Storage-fiók alatti alapértelmezett `$root` tárolóba lesz átmásolva. A `$root` tárolóban található összes fájl mindig blokkblobként lesz feltöltve.

   Fájlok másolása egy mappába a *AzureFile* mappában. A *AzureFile* mappában található almappa létrehoz egy fájlmegosztás. A közvetlenül a *AzureFile* mappába másolt fájlok sikertelenek lesznek, és a rendszer blokk blobként feltölti őket.

    Ha vannak fájlok és mappák a gyökérkönyvtárban, akkor át kell azokat helyeznie egy másik mappába az adatok másolásának megkezdése előtt.

    > [!IMPORTANT]
    > A tárolók, blobok és fájlnevek megfelelnek az [Azure elnevezési konvencióinak](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Ha a szabályok nem teljesülnek, az adatok az Azure-ba való feltöltése meghiúsul.

3. Fájlok másolásakor ügyeljen arra, hogy a fájlok ne lépjék túl a ~ 4,7 TiB-ot a blokk Blobok esetében, ~ 8 TiB a Page Blobok esetében, és ~ 1 TiB a Azure Files. 
4. Az adatok a Fájlkezelővel és húzással is másolhatók. Használhat bármilyen SMB-kompatibilis fájlmásoló eszközt (pl.: Robocopy) is az adatok másolására. Az alábbi Robocopy-paranccsal több másolási feladat is elindítható:

    `Robocopy <source> <destination>  * /MT:64 /E /R:1 /W:1 /NFL /NDL /FFT /Log:c:\RobocopyLog.txt` 
    
    A parancshoz használható paraméterek és beállítások az alábbi táblázatban láthatók:
    
    |Paraméterek/beállítások  |Leírás |
    |--------------------|------------|
    |Source            | Megadja a forráskönyvtár elérési útját.        |
    |Cél       | Megadja a célkönyvtár elérési útját.        |
    |/E                  | Átmásolja az alkönyvtárakat, az üres könyvtárakkal együtt. |
    |/MT[:N]             | Többszálas másolatokat hoz létre N szállal, ahol az N egy 1 és 128 közötti egész szám. <br>Az N alapértelmezett értéke 8.        |
    |R \<N >             | A meghiúsult másolások esetén indított újrapróbálkozások számát határozza meg. Az N alapértelmezett értéke 1 000 000 (egymillió újrapróbálkozás).        |
    |/W: \<N >             | Az újrapróbálkozások közötti várakozási időt határozza meg másodpercben. Az N alapértelmezett értéke 30 (30 másodperc várakozási idő).        |
    |/NFL                | Azt adja meg, hogy a fájlnevek ne legyenek naplózva.        |
    |/NDL                | Azt adja meg, hogy a könyvtárnevek ne legyenek naplózva.        |
    |/FFT                | FAT-fájlidőket feltételez (két másodperces pontosság).        |
    |/Log:\<naplófájl >     | Beírja az állapotkimenetet a naplófájlba (felülírja a már létező naplófájlt).         |

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
 
    A teljesítmény optimalizálása érdekében használja a következő Robocopy-paramétereket az adatok másolásához.

    |    Platform    |    Többnyire kis méretű fájlok < 512 kB                           |    Többnyire közepes méretű fájlok 512 kB – 1 MB                      |    Többnyire nagy méretű fájlok > 1 MB                             |   
    |----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|
    |    Data Box Disk        |    4 Robocopy-munkamenet * <br> 16 szál munkamenetenként    |    2 Robocopy-munkamenet * <br> 16 szál munkamenetenként    |    2 Robocopy-munkamenet * <br> 16 szál munkamenetenként    |
    
    **Minden Robocopy-munkamenet legfeljebb 7 000 könyvtárat és 150 000 000 fájlt tartalmazhat.*
    
    >[!NOTE]
    > A fent javasolt paraméterek a házon belüli tesztelés során használt környezeten alapulnak.
    
    A Robocopy-paranccsal kapcsolatos további információért lásd [a Robocopyt és néhány példát](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx) bemutató témakört.

6. Nyissa meg a célmappát a másolt fájlok megtekintéséhez és ellenőrzéséhez. Ha hibába ütközik a másolási folyamat során, töltse le a naplófájlokat a hibaelhárításhoz. A naplófájlok a Robocopy parancsban megadott módon találhatók.
 
### <a name="split-and-copy-data-to-disks"></a>Adatok szétosztása és másolása lemezekre

Ez a választható eljárás akkor lehet hasznos, ha több lemezt használ, és nagy adatkészletet kell szétosztania és átmásolnia ezekre a lemezre. A Data Box másolásfelosztó eszköze segítséget nyújt az adatok felosztásában és másolásában Windows rendszerű számítógépen.

>[!IMPORTANT]
> Data Box a felosztott másolási eszköz az adatait is ellenőrzi. Ha Data Box felosztott másolási eszközt használ az adatmásoláshoz, akkor kihagyhatja az [ellenőrzési lépést](#validate-data).
> A felosztott másolási eszköz felügyelt lemezek esetén nem támogatott.

1. Ellenőrizze Windows rendszerű számítógépén, hogy a Data Box másolásfelosztó eszköze le lett-e töltve és ki lett-e csomagolva egy helyi mappába. Ezt az eszközt a windowsos Data Box Disk-eszközkészlet részeként töltötte le.
2. Nyissa meg a Fájlkezelőt. Jegyezze fel az adatforrásként szolgáló meghajtó nevét és a Data Box Diskhez rendelt meghajtóbetűjeleket. 

     ![Felosztásos adatmásolás](media/data-box-disk-deploy-copy-data/split-copy-1.png)
 
3. Keresse meg a másolni kívánt forrásadatokat. Ebben az esetben például a következőt:
    - A rendszer az alábbi blokkblobadatokat azonosította.

         ![Felosztásos adatmásolás](media/data-box-disk-deploy-copy-data/split-copy-2.png)    

    - A rendszer az alábbi lapblobadatokat azonosította.

         ![Felosztásos adatmásolás](media/data-box-disk-deploy-copy-data/split-copy-3.png)
 
4. Nyissa meg azt a mappát, amelybe a szoftver ki lett csomagolva. Keresse meg `SampleConfig.json` a fájlt a mappában. Ez egy írásvédett fájl, amelyet módosíthat és menthet.

   ![Felosztásos adatmásolás](media/data-box-disk-deploy-copy-data/split-copy-4.png)
 
5. Módosítsa a `SampleConfig.json` fájlt.
 
   - Adja meg a feladat nevét. Ezzel létrehoz egy mappát a Data Box Diskben, amely a későbbiekben tárolóként fog szolgálni a lemezekhez társított Azure Storage-fiókban. A feladat nevének meg kell felelnie az Azure-tárolókra vonatkozó elnevezési konvencióknak. 
   - Adja meg a forrás elérési útját, `SampleConfigFile.json`és jegyezze fel az elérési út formátumát. 
   - Adja meg a céllemezeknek megfelelő meghajtóbetűjeleket. Ekkor elkezdődik a forrásadatok másolása a lemezekre.
   - Adja meg a naplófájlok elérési útvonalát. Alapértelmezés szerint a rendszer az aktuális könyvtárba továbbítja, ahol a `.exe` található.

     ![Felosztásos adatmásolás](media/data-box-disk-deploy-copy-data/split-copy-5.png)

6. A fájl formátumának ellenőrzéséhez nyissa `JSONlint`meg a következőt:. Mentse a fájlt másként `ConfigFile.json`. 

     ![Felosztásos adatmásolás](media/data-box-disk-deploy-copy-data/split-copy-6.png)
 
7. Nyisson meg egy parancssori ablakot. 

8. Futtassa a `DataBoxDiskSplitCopy.exe`parancsot. Type

    `DataBoxDiskSplitCopy.exe PrepImport /config:<Your-config-file-name.json>`

     ![Felosztásos adatmásolás](media/data-box-disk-deploy-copy-data/split-copy-7.png)
 
9. Nyomja le az Enter billentyűt a szkript folytatásához.

    ![Felosztásos adatmásolás](media/data-box-disk-deploy-copy-data/split-copy-8.png)
  
10. Az adatkészlet felosztását és másolását követően a másolásfelosztó eszköz összegzést készít a másolási munkamenetről. Az alábbiakban egy példa látható a kimenetre.

    ![Felosztásos adatmásolás](media/data-box-disk-deploy-copy-data/split-copy-9.png)
 
11. Ellenőrizze, hogy az adatok sikeresen szét lettek-e osztva a céllemezekre. 
 
    ![Felosztott Adatmásolás](media/data-box-disk-deploy-copy-data/split-copy-10.png)
    ![– az Adatmásolás másolása](media/data-box-disk-deploy-copy-data/split-copy-11.png)
     
    Ha továbbra is megvizsgálja a `n:` meghajtó tartalmát, láthatja, hogy két almappa jön létre, amely a blob és az oldal blob-formátumának megfelelő formátumú.
    
     ![Felosztásos adatmásolás](media/data-box-disk-deploy-copy-data/split-copy-12.png)

12. Ha a másolási munkamenet meghiúsul, a következő paranccsal állíthatja helyre és folytathatja azt:

    `DataBoxDiskSplitCopy.exe PrepImport /config:<configFile.json> /ResumeSession`

Ha a felosztott másolási eszközzel hibákat lát, lépjen a következő témakörre: a [felosztott másolási eszköz hibáinak elhárítása](data-box-disk-troubleshoot-data-copy.md).

Az Adatmásolás befejeződése után továbbra is ellenőrizheti az adatait. Ha a felosztott másolás eszközt használta, ugorja át az érvényesítést (a másolási eszköz érvényesítése is), és folytassa a következő oktatóanyaggal.


## <a name="validate-data"></a>Az adatok ellenőrzése

Ha nem az Adatmásolás másolása eszközt használta, akkor ellenőriznie kell az adatait. Az adatok ellenőrzéséhez hajtsa végre a következő lépéseket.

1. Futtassa a `DataBoxDiskValidation.cmd` parancsot az ellenőrzőösszeg-érvényesítéshez a meghajtó *DataBoxDiskImport* mappájában.
    
    ![A Data Box Disk érvényesítési eszköz kimenete](media/data-box-disk-deploy-copy-data/data-box-disk-validation-tool-output.png)

2. Válassza ki a megfelelő lehetőséget. **Javasoljuk, hogy a fájlok érvényesítéséhez és az ellenőrzőösszegek létrehozásához mindig a 2. lehetőséget válassza**. Az adatok méretétől függően a lépésben írtak elvégzése hosszabb időt is igénybe vehet. Ha a szkript futtatása befejeződött, zárja be a parancsablakot. Ha hiba történik az érvényesítés és az ellenőrzőösszeg létrehozása során, a rendszer értesíti, és megjelenít egy, a hibanaplóra mutató hivatkozást.

    ![Ellenőrzőösszeg kimenete](media/data-box-disk-deploy-copy-data/data-box-disk-checksum-output.png)

    > [!TIP]
    > - Az eszköz alaphelyzetbe állítása két Futtatás között.
    > - Használja az 1. lehetőséget, ha kis méretű fájlokat (~ Tudásbázis) tartalmazó nagyméretű adathalmazt szeretne kezelni. Ez a beállítás csak a fájlokat ellenőrzi, mivel az ellenőrzőösszeg létrehozása nagyon hosszú időt vehet igénybe, és a teljesítmény nagyon lassú lehet.

3. Több lemez használata esetén futtassa a parancsot minden lemezen.

Ha az érvényesítés során hibákat lát, tekintse meg az [érvényesítési hibák elhárítása](data-box-disk-troubleshoot.md)című témakört.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure Data Box Diskkel kapcsolatos alábbi témakörökkel ismerkedett meg:

> [!div class="checklist"]
> * Adatok másolása Data Box Diskre
> * Adatok integritásának ellenőrzése

A következő oktatóanyag azt ismerteti, hogyan küldheti vissza a Data Box Disket, valamint hogyan ellenőrizheti az Azure-ba történő adatfeltöltést.

> [!div class="nextstepaction"]
> [Az Azure Data Box visszaküldése a Microsoftnak](./data-box-disk-deploy-picked-up.md)

::: zone-end

::: zone target="chromeless"

### <a name="copy-data-to-disks"></a>Adatok másolása a lemezekre

A következő lépésekkel csatlakozhat a számítógépről, és másolhatja azokat a Data Box Diskba.

1. Tekintse meg a zárolásfeloldás után a meghajtó tartalmát. A meghajtón lévő előlétrehozott mappák és almappák listája eltér attól függően, hogy milyen beállítások vannak kiválasztva a Data Box Disk rendelés elhelyezésekor.
2. Másolja az olyan mappákba, amelyek megfelelnek a megfelelő adatformátumnak. Másolja például a strukturálatlan adat mappát a *BlockBlob* mappa, VHD vagy VHDX adat mappájába, hogy *PageBlob* a mappát és a fájlokat a *AzureFile*. Ha az adatformátum nem egyezik a megfelelő mappával (tárolási típus), akkor egy későbbi lépésben az adatok feltöltése az Azure-ba meghiúsul.

    - Győződjön meg arról, hogy a tárolók, a blobok és a fájlok megfelelnek az [Azure elnevezési konvencióinak](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions) és az [Azure-objektumok méretének](data-box-disk-limits.md#azure-object-size-limits). Ha nem követi ezeket a szabályokat vagy korlátozásokat, az Azure-ba történő adatfeltöltés sikertelen lesz.     
    - Ha a megrendelés Managed Disks a tárolási célhelyek egyike, tekintse meg a felügyelt [lemezek](data-box-disk-limits.md#managed-disk-naming-conventions)elnevezési konvencióit.
    - A rendszer a BlockBlob és a PageBlob mappa alatt található minden almappához létrehoz egy tárolót az Azure Storage-fiókban. A rendszer az *BlockBlob* és a *PageBlob* mappában található összes fájlt átmásolja az Azure Storage-fiókba $root alapértelmezett tárolóba. A $root tárolóban lévő fájlokat a rendszer mindig blokkolja blobként feltölti.
    - Hozzon létre egy almappát a *AzureFile* mappában. Ez az almappa leképezi a felhőben lévő fájlmegosztás. Másolja a fájlokat az almappába. A közvetlenül a *AzureFile* mappába másolt fájlok sikertelenek lesznek, és a rendszer blokk blobként feltölti őket.
    - Ha vannak fájlok és mappák a gyökérkönyvtárban, akkor át kell azokat helyeznie egy másik mappába az adatok másolásának megkezdése előtt.

3. A fogd és vidd paranccsal a Fájlkezelőben vagy bármely SMB-kompatibilis fájlmásolás eszközzel, például a Robocopy használatával másolhatók az adatai. A következő parancs használatával több másolási feladat is kezdeményezhető:

    ```
    Robocopy <source> <destination>  * /MT:64 /E /R:1 /W:1 /NFL /NDL /FFT /Log:c:\RobocopyLog.txt
    ```
4. Nyissa meg a célmappát a másolt fájlok megtekintéséhez és ellenőrzéséhez. Ha hibába ütközik a másolási folyamat során, töltse le a naplófájlokat a hibaelhárításhoz. A naplófájlok a Robocopy parancsban megadott módon találhatók.

Ha több lemezt használ, használja a [felosztás és másolás](data-box-disk-deploy-copy-data.md#split-and-copy-data-to-disks) választható eljárását, és olyan nagy adatkészletet használ, amelyet az összes lemezre el kell osztani és át kell másolni.

### <a name="validate-data"></a>Az adatok ellenőrzése

Az alábbi lépéseket követve ellenőrizheti adatait.

1. Futtassa a `DataBoxDiskValidation.cmd` parancsot az ellenőrzőösszeg-érvényesítéshez a meghajtó *DataBoxDiskImport* mappájában.
2. A 2. lehetőség használatával ellenőrizze a fájlokat, és állítson be ellenőrzőösszegeket. Az adatok méretétől függően a lépésben írtak elvégzése hosszabb időt is igénybe vehet. Ha hiba történik az érvényesítés és az ellenőrzőösszeg létrehozása során, a rendszer értesíti, és megjelenít egy, a hibanaplóra mutató hivatkozást.

    Az adatok ellenőrzésével kapcsolatos további információkért lásd: [adatok ellenőrzése](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-copy-data#validate-data). Ha az érvényesítés során hibák merülnek fel, tekintse meg az [érvényesítési hibák elhárítása](https://docs.microsoft.com/en-us/azure/databox/data-box-disk-troubleshoot){: Target = "_blank"} című témakört.

::: zone-end
