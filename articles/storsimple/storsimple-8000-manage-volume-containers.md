---
title: A StorSimple 8000 sorozatú eszközök kötettárolóinak kezelése
description: Bemutatja, hogyan használhatja a StorSimple Eszközkezelő szolgáltatás kötettárolók lapját kötettárolók hozzáadásához, módosításához vagy törléséhez.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/19/2017
ms.author: alkohli
ms.openlocfilehash: cbdad3c68848ce552811ee658bb29df74a6fad19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267676"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-volume-containers"></a>StorSimple-kötettárolók kezelése a StorSimple-eszközkezelő szolgáltatással

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag bemutatja, hogyan használhatja a StorSimple Eszközkezelő szolgáltatást a StorSimple kötettárolók létrehozásához és kezeléséhez.

A Microsoft Azure StorSimple-eszköz kötettárolója egy vagy több olyan kötetet tartalmaz, amelyek megosztják a tárfiókot, a titkosítást és a sávszélesség-felhasználási beállításokat. Egy eszköz több kötettárolóval is rendelkezhet az összes kötetéhez. 

A kötettároló attribútumai a következő tulajdonságokkal rendelkeznek:

* **Kötetek** – A rétegzett vagy helyileg rögzített StorSimple kötetek, amelyek a kötettárolóban találhatók. 
* **Titkosítás** – olyan titkosítási kulcs, amely minden kötettárolóhoz definiálható. Ez a kulcs a StorSimple-eszközről a felhőbe küldött adatok titkosítására szolgál. A felhasználó által megadott kulcshoz katonai szintű AES-256 bites kulcsot használ. Az adatok védelme érdekében azt javasoljuk, hogy mindig engedélyezze a felhőalapú tárolás titkosítását.
* **Tárfiók** – Az adatok tárolására használt Azure storage-fiók. A kötettárolóban található összes kötet megosztja ezt a tárfiókot. Választhat egy tárfiókot egy meglévő listából, vagy hozzon létre egy új fiókot, amikor létrehozza a kötettárolót, majd megadja a fiók hozzáférési hitelesítő adatait.
* **Felhőbeli sávszélesség** – Az eszköz által felhasznált sávszélesség, amikor az eszközről származó adatokat a felhőbe küldi. A sávszélesség-szabályozás t1 Mbps és 1000 Mbps közötti érték megadásával kényszerítheti ki a tároló létrehozásakor. Ha azt szeretné, hogy az eszköz az összes rendelkezésre álló sávszélességet felhasználja, állítsa ezt a mezőt **Korlátlan**beállításra. Sávszélesség-sablont is létrehozhat és alkalmazhat a sávszélesség ütemezés szerinti lefoglalásához.

Az alábbi eljárások bemutatják, hogyan használhatja a StorSimple **kötettárolók** panelt a következő gyakori műveletek végrehajtásához:

* Kötettároló hozzáadása
* Kötettároló módosítása
* Kötettároló törlése

## <a name="add-a-volume-container"></a>Kötettároló hozzáadása
Kötettároló hozzáadásához hajtsa végre az alábbi lépéseket.

[!INCLUDE [storsimple-8000-add-volume-container](../../includes/storsimple-8000-create-volume-container.md)]

## <a name="modify-a-volume-container"></a>Kötettároló módosítása
A kötettároló módosításához hajtsa végre az alábbi lépéseket.

[!INCLUDE [storsimple-8000-modify-volume-container](../../includes/storsimple-8000-modify-volume-container.md)]

## <a name="delete-a-volume-container"></a>Kötettároló törlése
A kötettárolókötetek kötetek vannak benne. Csak akkor törölhető, ha a benne lévő összes kötetet először törli. Kötettároló törléséhez hajtsa végre az alábbi lépéseket.

[!INCLUDE [storsimple-8000-delete-volume-container](../../includes/storsimple-8000-delete-volume-container.md)]

## <a name="next-steps"></a>További lépések
* További információ [a StorSimple kötetek kezeléséről.](storsimple-8000-manage-volumes-u2.md) 
* További információ [a StorSimple Eszközkezelő szolgáltatás használatáról a StorSimple-eszköz felügyeletéhez.](storsimple-8000-manager-service-administration.md)

