---
title: Fájlok áthelyezése a fájl alapú tárhely között Azure Data Factory használatával | Microsoft Docs
description: Megtudhatja, hogyan helyezhet át fájlokat a fájl-alapú tárolók között Azure Data Factory használatával a megoldás sablon használatával.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 7/12/2019
ms.openlocfilehash: 9eb82a23aac5a98a521976118c1e859d0be253d0
ms.sourcegitcommit: 1b7b0e1c915f586a906c33d7315a5dc7050a2f34
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "67881242"
---
# <a name="move-files-with-azure-data-factory"></a>Fájlok áthelyezése Azure Data Factory

Ez a cikk egy megoldás-sablont ismertet, amellyel a fájlokat áthelyezheti egyik mappából a másikba a fájl alapú tárolók között. A sablon használatának egyik leggyakoribb forgatókönyve: A fájlok folyamatosan el lesznek dobva a forrás-áruház egy leszállási mappájába. Az ütemezett triggerek létrehozásával az ADF-folyamat rendszeres időközönként áthelyezheti ezeket a fájlokat a forrásról a célhelyre.  Az ADF-folyamat a "fájlok áthelyezése" értékkel éri el a fájlokat a lekérési mappából, és mindegyiket egy másik mappába másolja a célhelyen, majd törli ugyanezeket a fájlokat a forrás-áruházban található kihelyezett mappából.

> [!NOTE]
> Vegye figyelembe, hogy a sablon úgy lett kialakítva, hogy a mappák áthelyezése helyett fájlokat helyezzen át.  Ha úgy szeretné áthelyezni a mappát, hogy úgy módosítja az adatkészletet, hogy csak a mappa elérési útját tartalmazza, majd a másolási tevékenység és a törlés tevékenység használatával ugyanarra az adatkészletre hivatkozzon, amely egy mappát jelképez, nagyon Körültekintőnek kell lennie. Ennek az az oka, hogy meg kell győződnie arról, hogy a másolási művelet és a törlési művelet között nem lesznek új fájlok a mappába. Ha a másolási tevékenység csak a másolási művelet befejezése után új fájlok érkeznek, de a törlési tevékenység nem lett kitöltve, lehetséges, hogy a törlési tevékenység törli ezt az új, a DESTINATI nem másolt beérkező fájlt. még a teljes mappa törlésével.

## <a name="about-this-solution-template"></a>Tudnivalók a megoldási sablonról

Ez a sablon lekéri a fájlokat a forrásfájl-alapú tárolóból. Ezután áthelyezi őket a célhelyre.

A sablon öt tevékenységet tartalmaz:
- A **GetMetaData** lekéri az objektumok listáját, beleértve a forrás-áruház mappájából származó fájlokat és almappákat is. Az objektumok rekurzív beolvasása nem történik meg. 
- **Szűrje** az objektumok listáját a **GetMetaData** tevékenységből a csak fájlok kiválasztásához. 
- A **foreach** lekéri a fájlok listáját a **szűrő** tevékenységből, majd megismétli a listát, és átadja az egyes fájlokat a másolási tevékenységnek és a törlési tevékenységnek.
- A másolás egy fájlt **másol** a forrásról a célhelyre.
- A **delete** parancs törli a forrás-áruházból származó azonos fájlt.

A sablon két paramétert határoz meg:
- A *FolderPath_SourceStore* annak a forrásnak a mappájának elérési útja, ahová át szeretné helyezni a fájlokat. 
- A *FolderPath_DestinationStore* a célhely azon mappájának elérési útja, ahová át szeretné helyezni a fájlokat. 

## <a name="how-to-use-this-solution-template"></a>A megoldás sablonjának használata

1. Lépjen a **fájlok áthelyezése** sablonhoz. Válassza a meglévő kapcsolatok lehetőséget, vagy hozzon létre egy **új** kapcsolódást a forrásfájl-tárolóhoz, ahová át szeretné helyezni a fájlokat. Ügyeljen arra, hogy a **DataSource_Folder** és a **DataSource_File** ugyanarra a kapcsolódásra hivatkozzon a forrásfájl-tárolóban.

    ![Új kapcsolódás létrehozása a forráshoz](media/solution-template-move-files/move-files1.png)

2. Válassza a meglévő kapcsolatok lehetőséget, vagy hozzon létre egy **új** kapcsolódást a célfájl tárolójához, ahová át szeretné helyezni a fájlokat.

    ![Új kapcsolódás létrehozása a célhoz](media/solution-template-move-files/move-files2.png)

3. Válassza **a sablon használata**lehetőséget.

    ![A sablon használata](media/solution-template-move-files/move-files3.png)
    
4. Ekkor megjelenik a folyamat, ahogy az alábbi példában is látható:

    ![A folyamat megjelenítése](media/solution-template-move-files/move-files4.png)

5. Válassza a **hibakeresés**lehetőséget, adja meg a **paramétereket**, majd kattintson a **Befejezés gombra**.   A paraméterek a mappa elérési útja, ahová át kívánja helyezni a fájlokat és a mappa elérési útját, ahová át szeretné helyezni a fájlokat. 

    ![A folyamat futtatása](media/solution-template-move-files/move-files5.png)

6. Tekintse át az eredményt.

    ![Az eredmény áttekintése](media/solution-template-move-files/move-files6.png)

## <a name="next-steps"></a>További lépések

- [Új és módosított fájlok másolása a LastModifiedDate használatával Azure Data Factory](solution-template-copy-new-files-lastmodifieddate.md)

- [Több tárolóból származó fájlok másolása Azure Data Factory](solution-template-copy-files-multiple-containers.md)