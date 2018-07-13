---
title: A StorSimple kötettároló a StorSimple 8000 sorozatú eszköz kezelése |} A Microsoft Docs
description: Ismerteti, hogyan használhatja a StorSimple-Eszközkezelő szolgáltatás kötet tárolók lap hozzáadása, módosítása vagy törölni a kötettárolót.
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38606589"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-volume-containers"></a>A StorSimple-Eszközkezelő szolgáltatás segítségével a StorSimple kötettárolók kezelése

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag azt ismerteti, hogyan hozhat létre és kezelhet a StorSimple-kötettároló a StorSimple-Eszközkezelő szolgáltatás használata.

A Microsoft Azure StorSimple-eszköz kötettároló egy vagy több olyan kötetet, amelyek megosztása a storage-fiókot, a titkosítás és a sávszélesség-használat beállításokat tartalmazza. Egy eszköz rendelkezhet több kötettárolóba tartozó összes kötetet. 

A kötettároló a következő attribútumokat:

* **Kötetek** – a rétegzett, vagy a kötettároló belüli lemezképcsomagban StorSimple-köteteket a helyileg rögzített. 
* **Titkosítási** – egy titkosítási kulcsot, amelyek az egyes kötettároló. Ezt a kulcsot a StorSimple-eszközről a felhőbe küldött adatok titkosításához használatos. A felhasználó által megadott kulcs egy katonai szintű AES-256 bites kulcsot használnak. Az adatok biztonságos, azt javasoljuk, hogy mindig engedélyezze a felhős társzolgáltatás titkosítása.
* **Storage-fiók** – az Azure storage-fiók, amely az adatok tárolására szolgál. A kötettároló szereplő összes kötet ossza meg ezt a tárfiókot. Egy meglévő listából válasszon ki egy tárfiókot, vagy létrehozhat egy új fiókot, amikor a kötettároló létrehozásához, és adja meg a fiókhoz tartozó hozzáférési hitelesítő adatokat.
* **A felhő sávszélesség** – Ha az elküldött az adatok az eszközről a felhőbe az eszközök által felhasznált sávszélesség. 1 MB/s és 1000 MB/s közötti értéket adjon meg, ez a tároló létrehozásakor kényszerítheti a sávszélesség-vezérlést. Ha azt szeretné, hogy az eszköz felhasználja az összes rendelkezésre álló sávszélességet, adja meg a mezőben **korlátlan**. Is létrehozhat és sávszélességsablon ütemezés alapján sávszélességet kell kiosztania a alkalmazni.

A következő eljárások bemutatják, hogyan használhatja a StorSimple **kötettárolók** panelen a következő gyakori műveletek végrehajtásához:

* Kötettároló hozzáadása
* A kötettároló módosítása
* A kötettároló törlése

## <a name="add-a-volume-container"></a>Kötettároló hozzáadása
A következő lépésekkel kötettároló hozzáadása.

[!INCLUDE [storsimple-8000-add-volume-container](../../includes/storsimple-8000-create-volume-container.md)]

## <a name="modify-a-volume-container"></a>A kötettároló módosítása
A következő lépésekkel kötettároló módosítása.

[!INCLUDE [storsimple-8000-modify-volume-container](../../includes/storsimple-8000-modify-volume-container.md)]

## <a name="delete-a-volume-container"></a>A kötettároló törlése
A kötettároló benne lévő köteteket. Csak akkor, ha minden benne tárolt kötet először el kell hagyni törölhetők. A következő lépésekkel törölni a kötettárolót.

[!INCLUDE [storsimple-8000-delete-volume-container](../../includes/storsimple-8000-delete-volume-container.md)]

## <a name="next-steps"></a>További lépések
* Tudjon meg többet [kezelése a StorSimple-kötetek](storsimple-8000-manage-volumes-u2.md). 
* Tudjon meg többet [a StorSimple-Eszközkezelő szolgáltatás használata a StorSimple-eszköz felügyeletéhez](storsimple-8000-manager-service-administration.md).

