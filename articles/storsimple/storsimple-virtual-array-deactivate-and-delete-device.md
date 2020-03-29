---
title: Microsoft Azure StorSimple virtuális tömb inaktiválása és törlése | Microsoft dokumentumok
description: A StorSimple-eszköz eltávolítását ismerteti, ha először inaktiválja, majd törli azt.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60580598"
---
# <a name="deactivate-and-delete-a-storsimple-virtual-array"></a>Virtuális StorSimple-tömb inaktiválása és törlése

## <a name="overview"></a>Áttekintés

A StorSimple virtuális tömb inaktiválásakor megszakítja az eszköz és a megfelelő StorSimple Eszközkezelő szolgáltatás közötti kapcsolatot. Ez az oktatóanyag a következőket ismerteti:

* Eszköz inaktiválása 
* Inaktivált eszköz törlése

A cikkben szereplő információk csak a StorSimple virtuális tömbökre vonatkoznak. A 8000-es sorozattal kapcsolatos információkért látogasson el az [eszköz inaktiválásához vagy törléséhez.](storsimple-deactivate-and-delete-device.md)

## <a name="when-to-deactivate"></a>Mikor kell kikapcsolni?

Az inaktiválás állandó művelet, és nem lehet visszavonni. Nem regisztrálhat újra inaktivált eszközt a StorSimple Eszközkezelő szolgáltatással. Előfordulhat, hogy inaktiválnia kell és törölnie kell egy StorSimple virtuális tömböt a következő esetekben:

* **Tervezett feladatátvétel:** Az eszköz online állapotban van, és azt tervezi, hogy feladatátvételt. Ha nagyobb eszközre szeretne frissíteni, előfordulhat, hogy át kell adnia az eszközt. Az adatok tulajdonjogának átvitele és a feladatátvétel befejezése után a forráseszköz automatikusan törlődik.
* **Nem tervezett feladatátvétel** : Az eszköz offline állapotban van, és át kell adnia az eszközt. Ez a forgatókönyv akkor fordulhat elő, egy katasztrófa, ha van egy kimaradás az adatközpontban, és az elsődleges eszköz nem érhető el. Azt tervezi, hogy egy másodlagos eszközre átad egy eszközt. Az adatok tulajdonjogának átvitele és a feladatátvétel befejezése után a forráseszköz automatikusan törlődik.
* **Leszerelés:** Le szeretné szerelni az eszközt. Ehhez először inaktiválnia kell az eszközt, majd törölnie kell azt. Az eszköz inaktiválásakor a továbbiakban nem férhet hozzá a helyileg tárolt adatokhoz. Csak a felhőben tárolt adatokat érheti el és állíthatja helyre. Ha azt tervezi, hogy az eszköz adatait az inaktiválás után megtartja, akkor az eszköz inaktiválása előtt felhőbeli pillanatképet kell készítenie az összes adatról. Ez a felhőbeli pillanatkép lehetővé teszi az összes adat helyreállítását egy későbbi szakaszban.

## <a name="deactivate-a-device"></a>Eszköz inaktiválása

Az eszköz inaktiválásához hajtsa végre az alábbi lépéseket.

#### <a name="to-deactivate-the-device"></a>Az eszköz inaktiválása

1. A szolgáltatásban nyissa meg **a Felügyeleti > eszközök**lehetőséget. Az **Eszközök** panelen kattintson és válassza ki az inaktiválni kívánt eszközt.
   
    ![Az inaktiválandó eszköz kiválasztása](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete7.png)
2. Az **Eszköz irányítópultjának** paneljén kattintson **a ... Több,** és a listából válassza **az Inaktiválás**lehetőséget.
   
    ![Kattintson az inaktiválásra](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete8.png)
3. Az **Inaktiválás** panelen írja be az eszköz nevét, majd kattintson **az Inaktiválás gombra.** 
   
    ![Inaktiválás megerősítése](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete1.png)
   
    A folyamat inaktiválása és néhány percet vesz igénybe.
   
    ![Inaktiválás folyamatban](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete2.png)
4. Az inaktiválás után frissül az eszközök listája.
   
    ![Teljes inaktiválás](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete3.png)
   
    Most már törölheti ezt az eszközt.

## <a name="delete-the-device"></a>Eszköz törlése

Az eszköz törléséhez először inaktiválni kell. Az eszköz törlésével eltávolítja azt a szolgáltatáshoz csatlakoztatott eszközök listájáról. A szolgáltatás ezután már nem tudja kezelni a törölt eszközt. Az eszközhöz társított adatok azonban a felhőben maradnak. Ezek az adatok ezután felhalmozódnak a költségek.

Az eszköz törléséhez hajtsa végre az alábbi lépéseket.

#### <a name="to-delete-the-device"></a>Az eszköz törlése

1. A StorSimple Eszközkezelőben nyissa meg **a Felügyeleti > eszközök**lehetőséget. Az **Eszközök** panelen válassza ki a törölni kívánt inaktivált eszközt.
2. Az **Eszköz irányítópultjának** paneljén kattintson **a ... Egyezés** t, majd kattintson **a Törlés gombra.**
   
   ![A törölni kívánt eszköz kiválasztása](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete4.png)
3. A **Törlés** panelen írja be az eszköz nevét a törlés megerősítéséhez, majd kattintson a **Törlés gombra.** Az eszköz törlése nem törli az eszközhöz társított felhőadatokat. 
   
   ![Törlés megerősítése](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete5.png) 
4. A törlés elindul, és néhány percet vesz igénybe.
   
   ![Törlés folyamatban](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete6.png)
   
    Az eszköz törlése után megtekintheti az eszközök frissített listáját.

## <a name="next-steps"></a>További lépések

* A feladatátvételről a [StorSimple virtuális tömb feladatátvételi és vészhelyreállításával](storsimple-virtual-array-failover-dr.md)kapcsolatos információkért látogasson el a feladatátvételre és a vészhelyreállításra.

* Ha többet szeretne megtudni a StorSimple Eszközkezelő szolgáltatás használatáról, olvassa el [a StorSimple Eszközkezelő szolgáltatás használata a StorSimple virtuális tömb felügyeletéhez című webhelyet.](storsimple-virtual-array-manager-service-administration.md) 

