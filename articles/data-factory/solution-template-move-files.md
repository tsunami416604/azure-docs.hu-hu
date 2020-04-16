---
title: Fájlok áthelyezése fájlalapú tárolók között
description: Ismerje meg, hogyan használhat megoldássablont a fájlok fájlalapú tárolás a fájlalapú tárolás között az Azure Data Factory használatával.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 7/12/2019
ms.openlocfilehash: b36eb2615e98ee8ea7751c836fd43e81a5a0f4e2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414744"
---
# <a name="move-files-with-azure-data-factory"></a>Fájlok áthelyezése az Azure Data Factory segítségével
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk egy olyan megoldássablont ismertet, amelyekkel fájlokat helyezhet át egyik mappából a másikba a fájlalapú tárolók között. A sablon használatának egyik gyakori forgatókönyve: A fájlok folyamatosan a forrástároló egy célmappájába kerülnek. Ütemezési eseményindító létrehozásával az ADF-folyamat rendszeresen áthelyezheti ezeket a fájlokat a forrásból a céltárolóba.  Az ADF-folyamat "fájlok áthelyezésének" módja a fájlok beszerzése a célmappából, mindegyik másolása a céltároló egy másik mappájába, majd ugyanazokat a fájlokat a forrástároló célmappájából.

> [!NOTE]
> Ne feledje, hogy ez a sablon a mappák áthelyezése helyett a fájlok áthelyezésére szolgál.  Ha úgy szeretné áthelyezni a mappát, hogy az adatkészlet et úgy szeretné áthelyezni, hogy az csak egy mappa elérési útját tartalmazza, majd a másolási tevékenység és a törlési tevékenység használatával ugyanarra az adatkészletre hivatkozik, amely egy mappát ábrázol, nagyon óvatosnak kell lennie. Ez azért van, mert meg kell győződnie arról, hogy nem lesz új fájlok érkeznek a mappába a másolási művelet és a törlési művelet között. Ha vannak új fájlok érkeznek a mappába abban a pillanatban, amikor a másolási tevékenység csak befejezte a másolási feladatot, de a Törlés tevékenység nem nézett, lehetséges, hogy a Törlés tevékenység törli ezt az új érkező fájlt, amely még nem másolt a célba a teljes mappa törlésével.

## <a name="about-this-solution-template"></a>A megoldássablon – kapcsolat

Ez a sablon leveszi a fájlokat a forrásfájl-alapú tárolóból. Ezután áthelyezi mindegyiket a céltárolóba.

A sablon öt tevékenységet tartalmaz:
- **A GetMetadata** leveszi az objektumok listáját, beleértve a fájlokat és az almappákat is a forrástárolómappából. Nem fogja rekurzívan beolvasni az objektumokat. 
- **Szűrje** az objektumok listáját a **GetMetadata** tevékenységből, hogy csak a fájlokat jelölje ki. 
- **ForEach** lekéri a fájllistát a **Szűrő** tevékenységből, majd áttevé teszi a listát, és minden fájlt átad a Másolás tevékenységnek és a Törlés tevékenységnek.
- Egy fájl **másolása** a forrásból a céltárolóba.
- **A Törlés** törli ugyanazt a fájlt a forrástárolóból.

A sablon négy paramétert határoz meg:
- *SourceStore_Location* a forrástároló azon mappaelérési útja, ahonnan fájlokat szeretne áthelyezni. 
- *SourceStore_Directory* a forrástároló azon almappája, ahonnan fájlokat szeretne áthelyezni.
- *DestinationStore_Location* a céltároló azon mappájának elérési útja, ahová a fájlokat át szeretné helyezni. 
- *DestinationStore_Directory* a céltároló azon almappája, ahová fájlokat szeretne áthelyezni.

## <a name="how-to-use-this-solution-template"></a>A megoldássablon használata

1. Nyissa meg a **Fájlok áthelyezése sablont.** Jelölje ki a meglévő kapcsolatot, vagy hozzon létre **egy új** kapcsolatot a forrásfájltárolóval, ahonnan fájlokat szeretne áthelyezni. Ne feledje, hogy **DataSource_Folder** és **DataSource_File** a forrásfájl-tároló ugyanazon kapcsolatára hivatkoznak.

    ![Új kapcsolat létrehozása a forrással](media/solution-template-move-files/move-files1.png)

2. Jelölje ki a meglévő kapcsolatot, vagy hozzon létre **egy új** kapcsolatot a célfájltárolóhoz, ahová fájlokat szeretne áthelyezni.

    ![Új kapcsolat létrehozása a célhoz](media/solution-template-move-files/move-files2.png)

3. Válassza **a Sablon használata** lapot.
    
4. A következő példában látható a folyamat:

    ![A folyamat megjelenítése](media/solution-template-move-files/move-files4.png)

5. Válassza **a Hibakeresés**lehetőséget, írja be a **Paraméterek**, majd a **Befejezés**lehetőséget.   A paraméterek a mappa elérési útja, amelyből fájlokat szeretne áthelyezni, és az a mappaelérési út, ahová a fájlokat át szeretné helyezni. 

    ![A folyamat futtatása](media/solution-template-move-files/move-files5.png)

6. Tekintse át az eredményt.

    ![Az eredmény áttekintése](media/solution-template-move-files/move-files6.png)

## <a name="next-steps"></a>További lépések

- [Új és módosított fájlok másolása lastmodifieddate segítségével az Azure Data Factory](solution-template-copy-new-files-lastmodifieddate.md)

- [Fájlok másolása több tárolóból az Azure Data Factory segítségével](solution-template-copy-files-multiple-containers.md)