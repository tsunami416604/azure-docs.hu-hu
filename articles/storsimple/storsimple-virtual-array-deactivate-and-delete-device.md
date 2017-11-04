---
title: "A Microsoft Azure StorSimple virtuális tömb inaktiváljon |} Microsoft Docs"
description: "Ismerteti a StorSimple eszköz eltávolítása a szolgáltatás első inaktiválása és törlését is."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: a929f5bc-03e2-4b01-b925-973db236f19f
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: alkohli
ms.openlocfilehash: 8dea36f92b034f8c6cdb6875634848d37f4c6606
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="deactivate-and-delete-a-storsimple-virtual-array"></a>A StorSimple virtuális tömb inaktiváljon

## <a name="overview"></a>Áttekintés

A StorSimple virtuális tömb inaktiválásakor megszakítja a kapcsolatot az eszköz és a megfelelő StorSimple Device Manager szolgáltatáshoz. Ez az oktatóanyag azt ismerteti, hogyan:

* Eszköz inaktiválása 
* Deaktivált eszköz törlése

A cikkben szereplő információk kizárólag a StorSimple virtuális tömbök. 8000 Series információkért nyissa meg a hogyan [inaktiválja vagy törölje az eszköz](storsimple-deactivate-and-delete-device.md).

## <a name="when-to-deactivate"></a>Ha inaktiválni?

Az inaktiválást állandó művelet, és nem vonható vissza. Deaktivált eszköz újra nem regisztrálja a StorSimple eszköz Manager szolgáltatásban. Szükség lehet inaktiváljon egy StorSimple virtuális tömb a következő esetekben:

* **A tervezett feladatátvételt** : az eszköz online állapotban, és azt tervezi, hogy áthelyezze a feladatokat az eszközt. Ha azt tervezi, és egy nagyobb eszközt frissítse, szükség lehet az eszköz a feladatátvétel. Miután az adatok tulajdonjoga, és a feladatátvétel befejeződött, a rendszer automatikusan törli a forráseszközt.
* **Nem tervezett feladatátvétel** : az eszköz offline állapotban, és meg kell az eszközt a feladatátvétel. Ez a forgatókönyv során egy olyan vészhelyzet esetén fordulhat, ha nincs kimaradás az adatközpontban, és az elsődleges eszköz nem működik. Tervezi az eszközt, hogy egy másodlagos eszköz feladatátvételt. Miután az adatok tulajdonjoga, és a feladatátvétel befejeződött, a rendszer automatikusan törli a forráseszközt.
* **Szerelje le** : az eszköz leszerelése szeretné. Ehhez telepítenie kell az eszköz először inaktiválja és törölje azt. Ha egy eszköz inaktiválja, már nem érheti el helyben tárolt adatokat. Csak is elérheti, és a felhőben tárolt adatok helyreállításához. Ha az eszköz adatok megőrzéséhez inaktiválás után szeretne, majd kell pillanatképet készít felhő az összes adat eszköz inaktiválása előtt. A felhő-pillanatfelvételt segítségével visszaállíthatja az adatokat egy későbbi időpontban.

## <a name="deactivate-a-device"></a>Eszköz inaktiválása

Az eszköz inaktiválásához hajtsa végre a következő lépéseket.

#### <a name="to-deactivate-the-device"></a>Az eszköz inaktiválása

1. A szolgáltatást, lépjen **felügyeleti > eszközök**. Az a **eszközök** paneljén kattintson, és válassza ki az eszközt, amelyet meg kíván inaktiválása.
   
    ![Válassza ki az eszköz inaktiválása](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete7.png)
2. Az a **eszköz irányítópult** paneljén kattintson **... További** , és válassza ki a listáról, **Deactivate**.
   
    ![Kattintson az Inaktiválás gombra](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete8.png)
3. Az a **Deactivate** panelen írja be az eszköz nevét, és kattintson a **Deactivate**. 
   
    ![Erősítse meg inaktiválása](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete1.png)
   
    Az inaktiválás folyamat elindul, és néhány percet is igénybe vehet.
   
    ![A folyamatban lévő inaktiválása](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete2.png)
4. Deaktiválás, után az eszközök listájának frissítése.
   
    ![Teljes inaktiválása](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete3.png)
   
    Most törölheti ezt az eszközt.

## <a name="delete-the-device"></a>Az eszköz törlése

Egy eszköz regisztrációját törölni először inaktiválja. Egy eszköz törlése eltávolítja a listán szereplő eszközök kapcsolódik a szolgáltatáshoz. A szolgáltatás már nem kezelhetők a törölt eszköz. Az eszközzel kapcsolatos adatok azonban továbbra is a felhőben. Ezek az adatok a díjak majd fizetni.

Az eszköz törlése, hajtsa végre az alábbi lépéseket.

#### <a name="to-delete-the-device"></a>Az eszköz törlése

1. Ugrás a StorSimple Device Manager **felügyeleti > eszközök**. Az a **eszközök** panelen válassza ki a törölni kívánt inaktív eszközök.
2. Az a **eszköz irányítópult** paneljén kattintson **... További** majd **törlése**.
   
   ![Válassza ki az eszköz törlése](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete4.png)
3. Az a **törlése** panelen írja be a törlés jóváhagyásához, és kattintson az eszköz nevét **törlése**. Az eszköz törlése nem érinti az eszközhöz társított felhő adatokat. 
   
   ![Törlés megerősítése](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete5.png) 
4. A törlés indul, és néhány percet is igénybe vehet.
   
   ![Törlése folyamatban](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete6.png)
   
    Az eszköz törlését követően megtekintheti a eszközök frissített listáját.

## <a name="next-steps"></a>Következő lépések

* Feladatátvételt információkért látogasson el [feladatátvétel és a katasztrófa utáni helyreállítás a StorSimple virtuális tömb](storsimple-virtual-array-failover-dr.md).

* A StorSimple eszköz Manager szolgáltatással kapcsolatos további tudnivalókért keresse fel [a StorSimple Device Manager szolgáltatás segítségével felügyelheti a StorSimple virtuális tömb](storsimple-virtual-array-manager-service-administration.md). 

