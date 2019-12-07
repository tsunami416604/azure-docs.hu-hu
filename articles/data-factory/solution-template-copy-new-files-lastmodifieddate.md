---
title: Új és módosított fájlok másolása a LastModifiedDate
description: Megtudhatja, hogyan másolhat új és módosított fájlokat a LastModifiedDate és a Azure Data Factory használatával a megoldási sablonnal.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 3/8/2019
ms.openlocfilehash: ca752fb75b8e151de925d3b5604a7e7182d82e92
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896292"
---
# <a name="copy-new-and-changed-files-by-lastmodifieddate-with-azure-data-factory"></a>Új és módosított fájlok másolása a LastModifiedDate használatával Azure Data Factory

Ez a cikk egy megoldási sablont mutat be, amellyel az új és módosított fájlokat csak a LastModifiedDate egy fájl alapú tárolóból a célhelyre másolhatja. 

## <a name="about-this-solution-template"></a>Tudnivalók a megoldási sablonról

Ez a sablon először kiválasztja az új és módosított fájlokat az attribútumok **LastModifiedDate**, majd átmásolja ezeket a kiválasztott fájlokat az adatforrás-tárolóból az adatok célhelyére.

A sablon egyetlen tevékenységet tartalmaz:
- **Másolja** az új és módosított fájlok másolását a LastModifiedDate egy adattárból a célhelyre.

A sablon négy paramétert határoz meg:
-  *FolderPath_Source* a mappa elérési útja, ahol a forrás-áruházból beolvashatja a fájlokat. Az alapértelmezett értéket a saját mappa elérési útjára kell cserélnie.
-  *FolderPath_Destination* a mappa elérési útja, ahová a fájlokat másolni szeretné a célhelyre. Az alapértelmezett értéket a saját mappa elérési útjára kell cserélnie.
-  *LastModified_From* használatával kiválaszthatja azokat a fájlokat, amelyek LastModifiedDate attribútuma az adott datetime érték után vagy azzal egyenlő.  Ahhoz, hogy csak az új fájlok legyenek kiválasztva, amelyek nem lettek átmásolva a legutóbbi időpontra, ez a DateTime érték lehet a folyamat utolsó indításakor elindított idő. Az alapértelmezett "2019-02-01T00:00:00Z" értéket lecserélheti a várt LastModifiedDate az UTC időzónában. 
-  *LastModified_To* használatával kiválaszthatja azokat a fájlokat, amelyek LastModifiedDate attribútuma a DateTime érték előtt van. Ha csak az új fájlokat szeretné kijelölni, amelyek nem lettek átmásolva a legutóbbi időpontra, akkor ez a DateTime érték lehet a jelenlegi időpont.  Az alapértelmezett "2019-02-01T00:00:00Z" értéket lecserélheti a várt LastModifiedDate az UTC időzónában. 

## <a name="how-to-use-this-solution-template"></a>A megoldás sablonjának használata

1. Ugrás a sablonra **új fájlok másolása csak a LastModifiedDate használatával**. Hozzon létre egy **új** kapcsolódást a forrás Storage-tárolóhoz. A forrásként szolgáló tár, ahová a fájlokat másolni kívánja.

    ![Új kapcsolódás létrehozása a forráshoz](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate1.png)
    
2. Először válassza ki a tárolási **típust**. Ezután adja meg a Storage- **fiók nevét** és a **fiók kulcsát**. Végül válassza a **Befejezés**lehetőséget.

    ![Adja meg a kapcsolatok karakterláncát](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate2.png)
    
3. Hozzon létre egy **új** kapcsolódást a célhelyhez. A célhely, ahová a fájlokat másolni kívánja. Emellett a 2. lépésben hasonló módon kell megadnia az adatok célhelyének elérhetőségét.

    ![Új kapcsolódás létrehozása a célhoz](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate3.png)

4. Kattintson a **Sablon használata** lehetőségre.

    ![A sablon használata](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate4.png)
    
5. Ekkor megjelenik a panelen elérhető folyamat, ahogy az az alábbi példában is látható:

    ![A folyamat megjelenítése](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate5.png)

6. Válassza a **hibakeresés**elemet, írja be a **Paraméterek** értékét, majd válassza a **Befejezés**lehetőséget.  Az alábbi képen a paramétereket az alábbiak szerint állítja be.
   - **FolderPath_Source** =  **/Source/**
   - **FolderPath_Destination** =  **/Destination/**
   - **LastModified_From** =  **2019-02-01T00:00:00Z**
   - **LastModified_To** = **2019-03-01T00:00:00Z**
    
     Ez a példa azt jelzi, hogy a TimeSpan a *2019-02-01T00:00:00Z* és *2019-03-01T00:00:00Z* között utoljára módosított fájlok a */Source/mappából* */Destination/mappába kerülnek.*  Ezeket lecserélheti a saját paramétereinek használatával.
    
     ![A folyamat futtatása](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate6.png)

7. Tekintse át az eredményt. A rendszer csak a konfigurált TimeSpan belül utoljára módosított fájlokat másolja a célhelyre.

    ![Az eredmény áttekintése](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate7.png)
    
8. Most hozzáadhat egy kieséses Windows-triggert a folyamat automatizálásához, így a folyamat a LastModifiedDate rendszeres időközönként csak az új és módosított fájlokat tudja másolni.  Válassza az **trigger hozzáadása**lehetőséget, és válassza az **új/szerkesztés**lehetőséget.

    ![Az eredmény áttekintése](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate8.png)
    
9. Az **Eseményindítók hozzáadása** ablakban válassza az **+ új**lehetőséget.

    ![Az eredmény áttekintése](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate9.png)

10. Az eseményindító típushoz válassza a leválasztási időszak lehetőséget, majd **15 percenként** **állítsa be az** ismétlődést (bármely intervallumra módosítható), majd kattintson a **tovább**gombra.

    ![Eseményindító létrehozása](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate10.png)    
    
11. Írja be az **trigger futtatási paramétereinek** értékét az alábbiak szerint, majd válassza a **Befejezés**lehetőséget.
    - **FolderPath_Source** =  **/Source/** .  A mappát lecserélheti a forrás adattárban.
    - **FolderPath_Destination** =  **/Destination/** .  Lecserélheti a mappát a célhely adattárában.
    - **LastModified_From** =   **\@trigger (). outputs. windowStartTime**.  Ez az a rendszerváltozó, amely azt határozza meg, hogy mikor indította el a folyamat az utolsó alkalommal.
    - **LastModified_To** =  **\@trigger (). outputs. windowEndTime**.  Ez egy rendszerváltozó az Indítóban, amely meghatározza, hogy a folyamat mikor aktiválja ezt az időt.
    
    ![Bemeneti paraméterek](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate11.png)
    
12. Kattintson **Az összes közzététele** gombra.
    
    ![Az összes közzététele](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate12.png)

13. Hozzon létre új fájlokat a forrás mappában az adatforrás-tárolóban.  Most arra vár, hogy a folyamat automatikusan aktiválódik, és csak az új fájlok lesznek másolva a célhelyre.

14. Válassza a **figyelés** fület a bal oldali navigációs panelen, és várjon körülbelül 15 percet, ha az eseményindító ismétlődése 15 percenként van beállítva. 

    ![Figyelés kiválasztása](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate14.png)

15. Tekintse át az eredményt. A folyamat 15 percenként automatikusan elindul, és csak a forrás-áruházból származó új vagy módosított fájlok lesznek átmásolva a célhelyre minden egyes folyamat futtatásakor.

    ![Az eredmény áttekintése](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate15.png)
    
## <a name="next-steps"></a>Következő lépések

- [Az Azure Data Factory bemutatása](introduction.md)
