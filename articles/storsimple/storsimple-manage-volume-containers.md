---
title: "A StorSimple-kötet tárolók kezelése |} Microsoft Docs"
description: "Ismerteti, hogyan használhatja a StorSimple Manager szolgáltatás kötet tárolók lap hozzáadása, módosítása vagy törlése egy kötettárolót."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: 1c64ce75-1fd3-4d3b-9304-d4dc0fc2b069
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/24/2016
ms.author: v-sharos
ms.openlocfilehash: bb55a7a4bff0fd4319de6f6ce958686ad8a4142b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-manager-service-to-manage-storsimple-volume-containers"></a>A StorSimple Manager szolgáltatással a StorSimple-kötet tárolók kezelése
## <a name="overview"></a>Áttekintés
Ez az oktatóanyag ismerteti, hogyan hozhatja létre és kezelheti a StorSimple-kötet tárolók a StorSimple Manager szolgáltatás.

A Microsoft Azure StorSimple eszköz a kötettároló tárfiók, a titkosítás és a sávszélesség-használat beállításokat használó egy vagy több kötetet tartalmazza. Egy eszköz több kötet tároló az összes kötet lehet. 

A kötettároló a következő attribútumokat:

* **Kötetek** – a rétegzett, vagy helyileg rögzített kötet tárolóban található StorSimple-köteteket. A kötettároló tartalmazhat 256 StorSimple-köteteket.
* **Titkosítási** – titkosítási kulcsot, amelyek az egyes mennyiségi tároló. Ezt a kulcsot a saját StorSimple eszközön a felhőbe küldött adatok titkosításához használt. Egy katonai szintű AES-256 bites kulcsot használ a felhasználó által megadott kulccsal. Az adatok védelme érdekében javasoljuk, hogy mindig engedélyezze felhőalapú tárolás titkosításának.
* **A tárfiók** – a tárfiókot, amely csatolva van a tároló felhőszolgáltatást. A kötettároló szereplő összes kötet ossza meg ezt a tárfiókot. A storage-fiók közül választhat egy meglévő listájához, vagy hozzon létre egy új fiókot, a kötettároló létrehozása, és adja meg a fiókhoz tartozó hozzáférési hitelesítő adatokat.
* **A felhő sávszélesség** – amikor az adatok az eszközről a felhőbe küldi el az eszköz által felhasznált sávszélesség. 1 és 1000 MB/s ha ez a tároló közötti érték megadásával kényszerítheti a sávszélesség-vezérlést. Ha azt szeretné, hogy az eszköz összes rendelkezésre álló sávszélességet, állítsa be ezt a mezőt a korlátlan. Hozhat létre, és a ütemezés szerint sávszélesség sávszélesség sablon alkalmazása.

![Kötet tárolók lap](./media/storsimple-manage-volume-containers/HCS_VolumeContainersPage.png)

A következő eljárások azt ismertetik, hogyan használható a StorSimple **kötettárolók** lap a következő gyakori műveletek végrehajtásához:

* A kötettároló hozzáadása 
* A kötettároló módosítása 
* A kötettároló törlése 

## <a name="add-a-volume-container"></a>A kötettároló hozzáadása
A következő lépésekkel kötettároló hozzáadása.

[!INCLUDE [storsimple-add-volume-container](../../includes/storsimple-add-volume-container.md)]

## <a name="modify-a-volume-container"></a>A kötettároló módosítása
A következő lépésekkel módosíthatja egy kötettárolót.

[!INCLUDE [storsimple-modify-volume-container](../../includes/storsimple-modify-volume-container.md)]

## <a name="delete-a-volume-container"></a>A kötettároló törlése
A kötettároló kötetek belül. Csak akkor, ha minden benne tárolt kötet először el kell hagyni törölhetők. A következő lépésekkel kötettároló törlése.

[!INCLUDE [storsimple-delete-volume-container](../../includes/storsimple-delete-volume-container.md)]

## <a name="next-steps"></a>Következő lépések
* További információ [felügyelete a StorSimple-köteteket](storsimple-manage-volumes.md). 
* További információ [a StorSimple Manager szolgáltatás használata a StorSimple eszköz felügyeletéhez](storsimple-manager-service-administration.md).

