---
title: Az Azure-fájlmegosztások kiépítésének megfontolásai.
description: Azure-fájlmegosztások kiépítése az Azure File Sync használatával való használatra. Közös szövegterület, amely et megosztanak az áttelepítési dokumentumok között.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 8cb398d1b1ec14f52d9c5fa5c122dc2e4ba4376d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78209427"
---
Az Azure-fájlmegosztás a felhőben egy Azure-tárfiókban tárolódik.
Van egy másik szintű teljesítmény megfontolások itt.

Ha nagyon aktív megosztásokkal rendelkezik – a megosztásokat sok felhasználó és/vagy alkalmazás használja, akkor két Azure-fájlmegosztás elérheti a tárfiók teljesítménykorlátját.

Ajánlott eljárás: a tárfiókok üzembe helyezése egy fájlmegosztással minden.
Több Azure-fájlmegosztást is felvehet ugyanabba a tárfiókba, ha archiválási megosztásokkal rendelkezik, vagy alacsony napi tevékenységre számít.

Ezek a szempontok inkább a közvetlen felhőbeli hozzáférésre vonatkoznak (egy Azure virtuális gépen keresztül), mint az Azure File Sync-re. Ha azt tervezi, hogy csak az Azure File Sync ezeket a megosztásokat használja, majd több csoportosítása egyetlen Azure storage-fiókba rendben van.

Ha készített egy listát a megosztások, akkor le kell képeznie az egyes megosztások a tárfiók fognak lakni.

Az előző fázisban meghatározta a megfelelő számú részvényt. Ebben a lépésben létrehozott egy leképezést a tárfiókok fájlmegosztások. Telepítse a megfelelő számú Azure storage-fiókok a megfelelő számú Azure-fájlmegosztások bennük.

Győződjön meg arról, hogy a régió az egyes tárfiókok azonos, és megegyezik a régió a Storage Sync Service erőforrás már telepített.

> [!CAUTION]
> Ha 100 TiB-szintű Azure-fájlmegosztást hoz létre, akkor a megosztás csak helyileg redundáns tárolást vagy zónaredundáns tárolási redundancia-beállításokat használhat. A 100 TiB-fájlmegosztás használata előtt vegye figyelembe a tárolási redundancia igényeit.

Az Azure-fájlmegosztások alapértelmezés szerint továbbra is 5 TiB-korláttal jönnek létre. Mivel új tárfiókokat hoz létre, kövesse az útmutatást olyan [tárfiókok létrehozásához, amelyek 100 TiB-korláttal engedélyezik az Azure-fájlmegosztásokat.](../articles/storage/files/storage-files-how-to-create-large-file-share.md)

Egy másik szempont a tárfiók üzembe helyezésekor, az Azure storage redundanciája. Lásd: [Az Azure Storage redundancia beállításai.](../articles/storage/common/storage-redundancy.md)

Az erőforrások neve is fontos. Ha például a HR-részleg több megosztását csoportosítja egy Azure-tárfiókba, megfelelően kell elneveznie a tárfiókot. Hasonlóképpen az Azure-fájlmegosztások elnevezésekénél a helyszíni megfelelőikhöz hasonló neveket kell használnia.