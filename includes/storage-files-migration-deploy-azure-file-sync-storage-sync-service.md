---
title: Storage Sync szolgáltatás üzembe helyezése
description: A Azure File Sync felhőalapú erőforrás üzembe helyezése. Egy Storage Sync szolgáltatás. Egy közös szöveges blokk, amely az áttelepítési dokumentumok között meg van osztva.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 4468d8c6b57b38bdbaaa01208cfc4915f734f6bb
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209453"
---
Ebben a lépésben szüksége lesz az Azure-előfizetés hitelesítő adataira. Az Ön által használt Azure-előfizetés eltérhet a StorSimple használt szolgáltatástól.

Az Azure File Sync konfigurálásához szükséges alapvető erőforrás neve "Storage Sync Service".
Javasoljuk, hogy csak egyet helyezzen üzembe a vállalat összes olyan kiszolgálójára, amely most vagy a későbbiekben is szinkronizálja ugyanezeket a fájlokat. Ha több StorSimple-berendezéssel rendelkezik, érdemes lehet Storage Sync Service-erőforrást létrehozni mindegyikhez. Azonban csak akkor hozzon létre több tárolási szinkronizációs szolgáltatást, ha olyan kiszolgálókat használ, amelyeknek soha nem kell cserélniük az adatcserét. Ellenkező esetben az ajánlott eljárás a Storage Sync szolgáltatás használata.

Válasszon ki egy Azure-régiót a Storage Sync szolgáltatáshoz, amely közel van az iroda helyéhez. Minden más Felhőbeli erőforrást ugyanabban a régióban kell üzembe helyezni.
Az ajánlott eljárás egy új erőforráscsoport létrehozása az előfizetésben, amely megkönnyíti a kezelést a szinkronizálási és tárolási erőforrások tárolásához.

A következő cikk a Storage Sync szolgáltatás üzembe helyezését ismerteti. Csak a doc ezen részének követése. A későbbi lépésekben a jelen dokumentum más alszakaszaira mutató hivatkozásokat talál.

[Tudnivalók a Storage Sync szolgáltatás üzembe helyezéséről.](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service)
