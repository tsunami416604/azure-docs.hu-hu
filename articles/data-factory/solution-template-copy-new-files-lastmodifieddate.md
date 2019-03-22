---
title: Az Azure Data Factoryvel LastModifiedDate új és módosított fájlok másolása |} A Microsoft Docs
description: Útmutató az Azure Data Factoryvel LastModifiedDate új és módosított fájlok másolása megoldás sablon használatával.
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
ms.openlocfilehash: cae75f4d64c8b3f74cc40e94a675c0f10a6bd9ec
ms.sourcegitcommit: f596d88d776a3699f8c8cf98415eb874187e2a48
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2019
ms.locfileid: "58111939"
---
# <a name="copy-new-and-changed-files-by-lastmodifieddate-with-azure-data-factory"></a>Az Azure Data Factoryvel LastModifiedDate új és módosított fájlok másolása

Ez a cikk ismerteti, amelyek segítségével LastModifiedDate csak az új és módosított fájlok másolása a fájlalapú tároló egy céltár megoldássablon. 

## <a name="about-this-solution-template"></a>Ez a megoldássablon kapcsolatban

Ez a sablon első kiválasztja az új és módosított fájlok csak az attribútumaik **LastModifiedDate**, és ezután átmásolja a forrásadattár az adattár cél ezeket a kijelölt fájlokat.

A sablon egy tevékenységet tartalmaz:
- **Másolás** LastModifiedDate csak az új és megváltozott fájlok másolása egy fájl áruházból egy céltár.

A sablon négy paraméter határozza meg:
-  *FolderPath_Source* van a mappa elérési útja, ahol áttekintheti a fájlokat a forrás-tárolóból. Az alapértelmezett értéket cserélje le a saját mappa elérési útját kell.
-  *FolderPath_Destination* kívánja másolja a fájlokat a cél tároló elérési útja. Az alapértelmezett értéket cserélje le a saját mappa elérési útját kell.
-  *LastModified_From* -e, válassza ki a fájlokat, amelyek LastModifiedDate attribútuma, mint vagy egyenlő a dátum/idő értékhez.  Annak érdekében, hogy az új fájlok csak, amely sem lett átmásolva legutóbbi időpontja, válassza ki a dátum/idő érték lehet az idő, amikor a folyamat legutóbbi lett elindítva. Lecserélheti az alapértelmezett érték "2019-02-01T00:00:00Z", a várt LastModifiedDate az UTC időzónára. 
-  *LastModified_To* segítségével válassza ki a fájlokat, amelyek LastModifiedDate attribútuma van, a dátum/idő érték előtt. Annak érdekében, hogy az új fájlok csak, amely sem lett átmásolva legutóbbi időpontja, válassza ki a dátum/idő érték lehet a jelenlegi idő.  Lecserélheti az alapértelmezett érték "2019-02-01T00:00:00Z", a várt LastModifiedDate az UTC időzónára. 

## <a name="how-to-use-this-solution-template"></a>Ez a megoldássablon használata

1. Lépjen a sablon **LastModifiedDate csak az új fájlok másolása**. Hozzon létre egy **új** a forrásadattárba a tárolási kapcsolat. A forrásadattárba a storage, ahol szeretné másolni az fájlokat.

    ![A forrás egy új kapcsolat létrehozása](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate1.png)
    
2. Először válassza ki a tároló **típus**. Miután, adjon meg a storage **fióknév** és a **fiókkulcs**. Végül válassza **Befejezés**.

    ![Adjon meg egy kapcsolati karakterláncot](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate2.png)
    
3. Hozzon létre egy **új** a céltár kapcsolatot. A cél tároló, ahol fájlokat másolni kívánt. Mivel Ön volt az 2. lépés is kell bemeneti hasonló cél tároló kapcsolódási adatainak.

    ![Hozzon létre egy új kapcsolatot a célhelyre](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate3.png)

4. Válassza ki **ezzel a sablonnal**.

    ![Sablon használata](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate4.png)
    
5. A folyamat a panelen érhető el az alábbi példában látható módon jelenik meg:

    ![A folyamat megjelenítése](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate5.png)

6. Válassza ki **Debug**, értékének írása a **paraméterek** válassza **Befejezés**.  Az alábbi képen a következőképpen elkészülünk a paramétereket.
   - **FolderPath_Source** = **/source/**
   - **FolderPath_Destination** = **/destination/**
   - **LastModified_From** =  **2019-02-01T00:00:00Z**
   - **LastModified_To** = **2019-03-01T00:00:00Z**
    
     A példában a fájlokat, amely utoljára módosítva lett belül közötti időt jelzi a *2019-02-01T00:00:00Z* és *2019-03-01T00:00:00Z* egy mappába másolja */source/*  mappába */destination/*.  Ezek a saját paramétereire lecserélheti.
    
     ![A folyamat futtatása](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate6.png)

7. Tekintse át az eredményt. Láthatja, hogy csak az utolsó módosítás a beállított belül fájlokat timespan céltár másolta.

    ![Tekintse át az eredmény](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate7.png)
    
8. Ez a folyamat automatizálására átfedésmentes windows eseményindító most, hogy a folyamat rendszeres időközönként bármikor másolhat LastModifiedDate csak az új és módosított fájlok is hozzáadhat.  Válassza ki **Hozzáadás eseményindító**, és válassza ki **új/Szerkesztés**.

    ![Tekintse át az eredmény](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate8.png)
    
9. Az a **eseményindítók hozzáadása** ablakban válassza **+ új**.

    ![Tekintse át az eredmény](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate9.png)

10. Válassza ki **Átfedésmentes ablak** típusú trigger esetén állítsa **minden 15 percben** , az ismétlődés (módosíthatja bármely időtartam), majd válassza ki **tovább**.

    ![Eseményindító létrehozása](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate10.png)    
    
11. Az érték írása a **eseményindító futtatási paraméterei** , a következőket, majd válassza ki **Befejezés**.
    - **FolderPath_Source** = **/source/**.  Lecserélheti a forrásadattár mappájában.
    - **FolderPath_Destination** = **/destination/**.  Lecserélheti a célként megadott adattárba mappájában.
    - **LastModified_From** =  **@trigger().outputs.windowStartTime**.  Egy Rendszerváltozó, az eseményindító, amely meghatározza, hogy az idő, amikor a folyamat aktiválódott utoljára.
    - **LastModified_To** = **@trigger().outputs.windowEndTime**.  Egy Rendszerváltozó, az eseményindító, amely meghatározza, hogy az idő, amikor a folyamat akkor aktiválódik, ezúttal a.
    
    ![Bemeneti paraméterek](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate11.png)
    
12. Kattintson **Az összes közzététele** gombra.
    
    ![Összes közzététele](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate12.png)

13. Hozzon létre fájlokat a forrásmappa a forrásadattár.  Most Várakozás a folyamat automatikusan elindul, és csak az új fájlok lesznek másolva a céltár.

14. Válassza ki **figyelés** lapra a bal oldali navigációs panelen, majd várja körülbelül 15 perc, ha az eseményindító ismétlődésének 15 percenként értékre lett állítva. 

    ![Válassza a figyelés](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate14.png)

15. Tekintse át az eredményt. Látni fogja a folyamat akkor aktiválódik, automatikusan 15 percenként, és csak az új vagy módosított fájlokat forrás áruházból lesznek másolva a céltárolót az egyes folyamatokhoz.

    ![Tekintse át az eredmény](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate15.png)
    
## <a name="next-steps"></a>További lépések

- [Az Azure Data Factory bemutatása](introduction.md)