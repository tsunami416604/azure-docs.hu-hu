---
title: Új és módosított fájlok másolása a LastModifiedDate programmal
description: Ismerje meg, hogyan használhat megoldássablont a LastModifiedDate új és módosított fájlok másolásához az Azure Data Factory segítségével.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 3/8/2019
ms.openlocfilehash: 971d311dfb54d417a8f66f504d01f08f8bcfc48b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74942099"
---
# <a name="copy-new-and-changed-files-by-lastmodifieddate-with-azure-data-factory"></a>Új és módosított fájlok másolása lastmodifieddate segítségével az Azure Data Factory

Ez a cikk egy olyan megoldássablont ismerteti, amelyet csak a LastModifiedDate segítségével másolhat fájlalapú tárolóból céltárolóba. 

## <a name="about-this-solution-template"></a>A megoldássablon – kapcsolat

Ez a sablon először csak a **LastModifiedDate**attribútumaik alapján jelöli ki az új és módosított fájlokat, majd a kijelölt fájlokat az adatforrás-tárolóból az adatcéltárolóba másolja.

A sablon egy tevékenységet tartalmaz:
- **Másoláshoz** csak a LastModifiedDate által imitáltak fájlokat másolja egy fájltárolóból a céltárolóba.

A sablon hat paramétert határoz meg:
-  *FolderPath_Source* az a mappaelérési út, ahol a forrástárolóból olvashatja a fájlokat. Az alapértelmezett értéket a saját mappaelérési útjára kell cserélnie.
-  *Directory_Source* az almappa elérési útja, ahol a forrástárolóból olvashatja a fájlokat. Az alapértelmezett értéket saját almappa elérési útjára kell cserélnie.
-  *FolderPath_Destination* az a mappaelérési út, amelyen fájlokat szeretne másolni a céltárolóba. Az alapértelmezett értéket a saját mappaelérési útjára kell cserélnie.
-  *Directory_Destination* az az almappa elérési útja, amelyen fájlokat szeretne másolni a céltárolóba. Az alapértelmezett értéket saját almappa elérési útjára kell cserélnie.
-  *LastModified_From* azon fájlok kiválasztására szolgál, amelyek LastModifiedDate attribútuma a datetime érték után vagy azzal egyenlő.  Annak érdekében, hogy csak az új fájlokat jelölje ki, amelyeket legutóbb nem másolt, ez a datetime érték lehet a folyamat legutóbbi aktiválásának időpontja. Az alapértelmezett értéket lecserélheti a "2019-02-01T00:00:00Z" alapértelmezett értékre a várt LastModifiedDate értékre UTC időzónában. 
-  *LastModified_To* azon fájlok kijelölésére szolgál, amelyek LastModifiedDate attribútuma megelőzi ezt a datetime értéket. Annak érdekében, hogy csak az új fájlokat jelölje ki, amelyeket legutóbb nem másolt, ez a datetime érték lehet az jelen idő.  Az alapértelmezett értéket lecserélheti a "2019-02-01T00:00:00Z" alapértelmezett értékre a várt LastModifiedDate értékre UTC időzónában. 

## <a name="how-to-use-this-solution-template"></a>A megoldássablon használata

1. Ugrás a **sablonra: Új fájlok másolása csak lastmodifieddate**segítségével. **Hozzon** létre egy új kapcsolatot a forrástárolóval. A forrástároló az a hely, ahonnan fájlokat szeretne másolni.

    ![Új kapcsolat létrehozása a forrással](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate1.png)
    
2. **Hozzon** létre egy új kapcsolatot a céltárban. A céltárolóaz a hely, ahová fájlokat szeretne másolni. 

    ![Új kapcsolat létrehozása a célhoz](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate3.png)

3. Válassza **a Sablon használata lehetőséget.**

    ![Sablon használata](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate4.png)
    
4. A panelen elérhető folyamat látható lesz, ahogy az a következő példában látható:

    ![A folyamat megjelenítése](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate5.png)

5. Válassza **a Debug**lehetőséget, írja be a **Paraméterek** értékét, és válassza a **Befejezés**lehetőséget.  Az alábbi képen a következő ként állítjuk be a paramétereket.
   - **FolderPath_Source** = forrásmappa
   - **Directory_Source** = almappa
   - **FolderPath_Destination** = célmappa
   - **Directory_Destination** = almappa
   - **LastModified_From** = 2019-02-01T00:00:00Z
   - **LastModified_To** = 2019-03-01T00:00:00Z
    
    A példa azt jelzi, hogy a legutóbb módosított fájlok a megadott időtartamon belül (**2019-02-01T00:00:00Z -** **2019-03-01T00:00:00Z**) lesznek átmásolva a forrásútvonal **sourcefolder/alfolder** a célelérési út **célfolder /almappa**.  Ezeket lecserélheti a saját paramétereire.

    ![A folyamat futtatása](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate6.png)

6. Tekintse át az eredményt. Látni fogja, csak a fájlok utolsó módosított a beállított időtartományban már másolt a cél tárolóba.

    ![Az eredmény áttekintése](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate7.png)
    
7. Most már hozzáadhat egy bukdácsoló ablakok eseményindítót a folyamat automatizálásához, így a folyamat csak a LastModifiedDate által idomásolhatja az új és a módosított fájlokat.  Válassza **az Eseményindító hozzáadása**lehetőséget, majd az **Új/Szerkesztés lehetőséget.**

    ![Az eredmény áttekintése](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate8.png)
    
8. Az **Eseményindítók hozzáadása** ablakban válassza a **+ Új**lehetőséget.

9. Válassza a **Tumbling ablak** lehetőséget az eseményindító típusához, állítsa be **minden 15 perc(ek)** ismétlődésként (bármikor átválthat az időközre). Válassza az **Igen** lehetőséget az Aktivált mezőben, majd az **OK**gombot.

    ![Eseményindító létrehozása](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate10.png)    
    
10. Állítsa be az **eseményindító futtatási paramétereinek** értékét a következőképpen, majd válassza a **Befejezés**gombot.
    - **FolderPath_Source** = **forrásmappát**.  A mappát a forrásadattárban cserélheti le.
    - **Directory_Source** = **almappát.**  Az almappát lecserélheti a forrásadattárban.
    - **FolderPath_Destination** = **célmappát.**  A céladattárban a mappát helyettesítheti.
    - **Directory_Destination** = **almappát.**  A céladattárban az almappát lecserélheti.
    - **LastModified_From** =  **LastModified_From\@trigger().outputs.windowStartTime**.  Ez egy rendszerváltozó az eseményindítóból, amely meghatározza, hogy a folyamat mikor aktiválódott utoljára.
    - **LastModified_To** = **LastModified_To\@trigger().outputs.windowEndTime**.  Ez egy rendszerváltozó az eseményindítóból, amely meghatározza a folyamat ezúttal történő indításának idejét.
    
    ![Bemeneti paraméterek](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate11.png)
    
11. Kattintson **Az összes közzététele** gombra.
    
    ![Az összes közzététele](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate12.png)

12. Hozzon létre új fájlokat az adatforrástár forrásmappájában.  Most arra vár, hogy a folyamat automatikusan aktiválódjon, és csak az új fájlok lesznek átmásolva a céltárolóba.

13. Válassza a Bal oldali navigációs panel **Monitor** fülét, és várjon körülbelül 15 percet, ha az eseményindító ismétlődése 15 percenként van beállítva. 

14. Tekintse át az eredményt. Látni fogja, hogy a folyamat 15 percenként automatikusan aktiválódik, és csak a forrástárolóból származó új vagy módosított fájlok lesznek átmásolva a céltárolóba minden egyes folyamatfuttatáskor.

    ![Az eredmény áttekintése](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate15.png)
    
## <a name="next-steps"></a>További lépések

- [Az Azure Data Factory bemutatása](introduction.md)
