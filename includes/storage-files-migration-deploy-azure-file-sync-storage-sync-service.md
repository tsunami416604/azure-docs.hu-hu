---
title: Storage Sync szolgáltatás üzembe helyezése
description: A Azure File Sync felhőalapú erőforrás üzembe helyezése a Storage Sync szolgáltatásban. Közös szöveges blokk, amely az áttelepítési dokumentumok között meg van osztva.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 248b61530b80a00aa10272dcb3d28b85c6ee04e5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "82143637"
---
Ebben a lépésben szüksége lesz az Azure-előfizetés hitelesítő adataira.

Az Azure File Sync számára konfigurálandó alapvető erőforrást *Storage Sync szolgáltatásnak*nevezzük. Azt javasoljuk, hogy csak egyet helyezzen üzembe az összes olyan kiszolgálón, amely most vagy a későbbiekben is szinkronizálja ezeket a fájlokat. Csak akkor hozzon létre több tárolási szinkronizációs szolgáltatást, ha olyan kiszolgálókat tartalmaz, amelyeknek soha nem kell cserélniük az adatcserét (például szinkronizálni ugyanazt az Azure-fájlmegosztást). Ellenkező esetben az ajánlott eljárás egyetlen Storage Sync szolgáltatás.

Válasszon egy Azure-régiót a Storage Sync szolgáltatáshoz, amely közel van a helyhez. Minden más Felhőbeli erőforrást ugyanabban a régióban kell üzembe helyezni.
A felügyelet egyszerűsítése érdekében hozzon létre egy új erőforráscsoportot az előfizetésben, amely a szinkronizálási és tárolási erőforrásokat is otthont ad.

További információ: a [Storage Sync szolgáltatás központi telepítése című rész](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service) a Azure file Sync üzembe helyezéséről szóló cikkben. Csak a cikk ezen részének követése. A későbbi lépésekben a cikk további szakaszaira mutató hivatkozásokat talál.