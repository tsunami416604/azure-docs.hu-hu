---
title: Inaktiválás és törlés egy Microsoft Azure StorSimple Virtual Array |} A Microsoft Docs
description: Ismerteti a StorSimple-eszköz eltávolítása a szolgáltatás inaktiválása először, és majd törlésével.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: a929f5bc-03e2-4b01-b925-973db236f19f
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: alkohli
ms.openlocfilehash: bb1a56d204a46f89213f20e317494120f0ea565e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60580598"
---
# <a name="deactivate-and-delete-a-storsimple-virtual-array"></a>Inaktiválás és törlés a StorSimple Virtual Array

## <a name="overview"></a>Áttekintés

A StorSimple Virtual Array inaktiválása, megszünteti az eszköz és a megfelelő StorSimple-Eszközkezelő szolgáltatás közötti kapcsolat. Ez az oktatóanyag a következőket ismerteti:

* Eszköz inaktiválása 
* Inaktivált eszköz törlése

Ebben a cikkben található információk csak a StorSimple Virtual Arrayt vonatkozik. A 8000-es sorozat további információkért tekintse meg, hogyan [inaktiválja vagy törölje az eszköz](storsimple-deactivate-and-delete-device.md).

## <a name="when-to-deactivate"></a>Mikor inaktiválása?

Inaktiválási állandó művelet, és nem vonható vissza. Inaktivált eszköz újra nem lehet regisztrálni a StorSimple-Eszközkezelő szolgáltatással. Szükség lehet inaktiválása és törlése a StorSimple Virtual Array a következő esetekben:

* **Tervezett feladatátvétel** : Az eszköz online állapotban, és azt tervezi, hogy az eszköz feladatainak átadása. Ha azt tervezi, egy nagyobb eszköz frissítése, szükség lehet az eszköz feladatainak átadása. Miután az adatok tulajdonjoga és a feladatátvétel befejeződött, a rendszer automatikusan törli a forráseszközt.
* **Nem tervezett feladatátvétel** : Az eszköz offline állapotban, és az eszköz feladatainak átadása kell. Egy katasztrófa során ez a forgatókönyv fordulhat elő, ha egy kimaradás az adatközpontban, és az elsődleges eszköz nem működik. Azt tervezi, hogy egy másodlagos eszközre az eszköz feladatainak átadása. Miután az adatok tulajdonjoga és a feladatátvétel befejeződött, a rendszer automatikusan törli a forráseszközt.
* **Leszerelése** : Szeretné az eszköz leszerelése. Ez megköveteli, hogy az eszköz először inaktiválja és törölje azt. Ha az eszköz inaktiválása, már nem érhető el helyben tárolt adatokat. Csak eléréséhez, és a felhőben tárolt adatok helyreállítását. Ha azt tervezi, hogy az eszközön lévő adatokat annak inaktiválása után, majd meg kell felhőbeli pillanatkép készítése az összes eszköz inaktiválása előtt. A felhőbeli pillanatképet minden az adatok helyreállítása egy későbbi szakaszban teszi lehetővé.

## <a name="deactivate-a-device"></a>Eszköz inaktiválása

Az eszköz inaktiválása, hajtsa végre az alábbi lépéseket.

#### <a name="to-deactivate-the-device"></a>Az eszköz inaktiválása

1. A szolgáltatáshoz, lépjen a **felügyeleti > eszközök**. Az a **eszközök** panelen kattintson, és válassza ki az eszközt, akinél szeretné inaktiválni.
   
    ![Válassza ki az eszköz inaktiválása](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete7.png)
2. Az a **irányítópultját** panelen kattintson a **... További** , és válassza ki a listából **inaktiválás**.
   
    ![Kattintson az Inaktiválás](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete8.png)
3. Az a **inaktiválás** panelen írja be az eszköz nevét, majd kattintson a **inaktiválás**. 
   
    ![Győződjön meg róla inaktiválása](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete1.png)
   
    Az inaktiválás folyamat elindul, és néhány percet vesz igénybe.
   
    ![Inaktiválás folyamatban](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete2.png)
4. Inaktiválási, miután az eszközök listája frissül.
   
    ![Teljes inaktiválása](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete3.png)
   
    Most törölheti az eszközt.

## <a name="delete-the-device"></a>Az eszköz törlése

Egy eszköz lehet való törléséhez először inaktiválni rendelkezik. Eszköz törlése eltávolítja a szolgáltatáshoz csatlakozó eszközök listájából. A szolgáltatás már nem kezelhetők a törölt eszközt. Az eszközhöz társított adatokat azonban továbbra is a felhőben. Ezek az adatok ezután díjai vizualizációtól elvárható.

Az eszköz törléséhez hajtsa végre az alábbi lépéseket.

#### <a name="to-delete-the-device"></a>Az eszköz törlése

1. Nyissa meg a StorSimple Device Manager **felügyeleti > eszközök**. Az a **eszközök** panelen válassza ki a törölni kívánt inaktív eszközt.
2. Az a **irányítópultját** panelen kattintson a **... További** majd **törlése**.
   
   ![Válassza ki az eszköz törlése](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete4.png)
3. Az a **törlése** panelen adja meg az eszköz a törlés megerősítéséhez, majd kattintson a **törlése**. Az eszköz törlése nem törli az eszközhöz társított felhőalapú adatokat. 
   
   ![Törlés megerősítése](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete5.png) 
4. A törlés elindul, és néhány percet vesz igénybe.
   
   ![A Törlés folyamatban](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete6.png)
   
    Az eszköz törlését követően megtekintheti a eszközök frissített listáját.

## <a name="next-steps"></a>További lépések

* Hogyan végezhet feladatátvételt információkért látogasson el [feladatátvétel és vészhelyreállítás a StorSimple Virtual Array helyreállítása](storsimple-virtual-array-failover-dr.md).

* A StorSimple-Eszközkezelő szolgáltatás használatával kapcsolatos további tudnivalókért keresse fel [a StorSimple-Eszközkezelő szolgáltatás segítségével felügyelheti a StorSimple Virtual Array](storsimple-virtual-array-manager-service-administration.md). 

