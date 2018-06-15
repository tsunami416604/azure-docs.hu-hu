---
title: A StorSimple-kötet tárolók a StorSimple 8000 series eszközön kezelése |} Microsoft Docs
description: Ismerteti, hogyan használhatja a StorSimple Device Manager szolgáltatás kötet tárolók lap hozzáadása, módosítása vagy törlése egy kötettárolót.
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
ms.openlocfilehash: 0f8e00d6d07224f56625482f339e612e68914be2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874842"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-volume-containers"></a>A StorSimple Device Manager szolgáltatással a StorSimple-kötet tárolók kezelése

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag ismerteti, hogyan hozhatja létre és kezelheti a StorSimple kötettárolók a StorSimple Device Manager szolgáltatás.

A Microsoft Azure StorSimple eszköz a kötettároló tárfiók, a titkosítás és a sávszélesség-használat beállításokat használó egy vagy több kötetet tartalmazza. Egy eszköz több kötet tároló az összes kötet lehet. 

A kötettároló a következő attribútumokat:

* **Kötetek** – a rétegzett, vagy helyileg rögzített kötet tárolóban található StorSimple-köteteket. 
* **Titkosítási** – titkosítási kulcsot, amelyek az egyes mennyiségi tároló. Ezt a kulcsot a saját StorSimple eszközön a felhőbe küldött adatok titkosításához használt. Egy katonai szintű AES-256 bites kulcsot használ a felhasználó által megadott kulccsal. Az adatok védelme érdekében javasoljuk, hogy mindig engedélyezze felhőalapú tárolás titkosításának.
* **A tárfiók** – az Azure storage-fiók, amely az adatok tárolására szolgál. A kötettároló szereplő összes kötet ossza meg ezt a tárfiókot. A storage-fiók közül választhat egy meglévő listájához, vagy hozzon létre egy új fiókot, a kötettároló létrehozása, és adja meg a fiókhoz tartozó hozzáférési hitelesítő adatokat.
* **A felhő sávszélesség** – amikor az adatok az eszközről a felhőbe küldi el az eszköz által felhasznált sávszélesség. Ez a tároló létrehozásakor 1 és 1000 közötti érték megadásával kényszerítheti a sávszélesség-vezérlést. Ha azt szeretné, hogy az eszköz teljes elérhető sávszélesség, a mezőt állítsa **korlátlan**. Hozhat létre, és a ütemezés szerint sávszélesség sávszélesség sablon alkalmazása.

A következő eljárások azt ismertetik, hogyan használható a StorSimple **kötettárolók** panelen a következő gyakori műveletek végrehajtásához:

* A kötettároló hozzáadása
* A kötettároló módosítása
* A kötettároló törlése

## <a name="add-a-volume-container"></a>A kötettároló hozzáadása
A következő lépésekkel kötettároló hozzáadása.

[!INCLUDE [storsimple-8000-add-volume-container](../../includes/storsimple-8000-create-volume-container.md)]

## <a name="modify-a-volume-container"></a>A kötettároló módosítása
A következő lépésekkel módosíthatja egy kötettárolót.

[!INCLUDE [storsimple-8000-modify-volume-container](../../includes/storsimple-8000-modify-volume-container.md)]

## <a name="delete-a-volume-container"></a>A kötettároló törlése
A kötettároló kötetek belül. Csak akkor, ha minden benne tárolt kötet először el kell hagyni törölhetők. A következő lépésekkel kötettároló törlése.

[!INCLUDE [storsimple-8000-delete-volume-container](../../includes/storsimple-8000-delete-volume-container.md)]

## <a name="next-steps"></a>Következő lépések
* További információ [felügyelete a StorSimple-köteteket](storsimple-8000-manage-volumes-u2.md). 
* További információ [felügyelete a StorSimple eszközt a StorSimple Device Manager szolgáltatással](storsimple-8000-manager-service-administration.md).

