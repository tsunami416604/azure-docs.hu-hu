---
title: Az Azure-fájlmegosztás kiépítési szempontjai.
description: Azure-fájlmegosztás kiépítése a Azure File Sync használatával való használatra. Egy közös szöveges blokk, amely az áttelepítési dokumentumok között meg van osztva.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 8cb398d1b1ec14f52d9c5fa5c122dc2e4ba4376d
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209427"
---
Az Azure-fájlmegosztás tárolása a felhőben történik egy Azure Storage-fiókban.
További teljesítménnyel kapcsolatos szempontokat itt talál.

Ha magas szintű aktív megosztásokkal rendelkezik, számos felhasználó és/vagy alkalmazás használja a megosztást, akkor két Azure-fájlmegosztás elérheti a Storage-fiók teljesítményének korlátját.

Ajánlott eljárás a Storage-fiókok üzembe helyezése egy fájlmegosztás használatával.
Több Azure-fájlmegosztás is egyesíthető ugyanabba a Storage-fiókba, ha archiválási megosztást használ, vagy ha a napi tevékenység várható.

Ezek a szempontok többek között a Felhőbeli hozzáférésre (Azure-beli virtuális gépekre) vonatkoznak, mint a Azure File Syncra. Ha azt tervezi, hogy csak Azure File Sync használja ezeket a megosztásokat, akkor a többit egyetlen Azure Storage-fiókba csoportosíthatja.

Ha elkészítette a megosztások listáját, akkor az egyes megosztásokat le kell képeznie arra a Storage-fiókra, amelyben a rendszer tárolja őket.

Az előző fázisban a megfelelő számú megosztást állapította meg. Ebben a lépésben létrehozta a Storage-fiókok fájlmegosztás számára való hozzárendelését. Telepítse a most már megfelelő számú Azure Storage-fiókot a megfelelő számú Azure-fájlmegosztás esetében.

Győződjön meg arról, hogy az egyes Storage-fiókok régiója azonos, és megfelel a már üzembe helyezett Storage Sync Service-erőforrás régiójának.

> [!CAUTION]
> Ha 100 TiB-korlátot hoz létre az Azure-fájlmegosztás számára, akkor ez a megosztás csak a helyileg redundáns tárolás vagy a zóna redundáns tárolási redundancia-lehetőségeket használhatja. A 100 TiB-fájlmegosztás használata előtt gondolja át, hogy a tárolási redundancia szükséges-e.

Alapértelmezés szerint az Azure-fájlmegosztás még mindig 5 TiB-korláttal lett létrehozva. Mivel új Storage-fiókokat hoz létre, kövesse az útmutatást, hogy olyan [Storage-fiókokat hozzon létre, amelyek engedélyezik az Azure-fájlmegosztás 100 TiB-korlátait](../articles/storage/files/storage-files-how-to-create-large-file-share.md).

A Storage-fiókok telepítésekor egy másik szempont az Azure Storage redundancia. Lásd: az [Azure Storage-redundancia beállításai](../articles/storage/common/storage-redundancy.md).

Az erőforrások nevei szintén fontosak. Ha például egy Azure Storage-fiókba csoportosítja a HR-részleg több megosztását, akkor a megfelelő nevet adja a Storage-fióknak. Hasonlóképpen, az Azure-fájlmegosztás elnevezése esetén a helyszíni partnereknek használt nevekhez hasonló neveket kell használnia.