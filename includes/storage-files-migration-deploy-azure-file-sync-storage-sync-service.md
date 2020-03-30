---
title: Tárolószinkronizálási szolgáltatás telepítése
description: Az Azure File Sync felhőalapú erőforrás üzembe helyezése. Egy tárolószinkronizálási szolgáltatás. Közös szövegterület, amely et megosztanak az áttelepítési dokumentumok között.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: fe74bae95784dcb63a80ee7e280c02de2eadeb5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124718"
---
Ebben a lépésben szüksége van az Azure-előfizetés hitelesítő adatait.

Az Azure File Sync konfigurálásához szükséges alapvető erőforrást "Storage Sync Service"-nek nevezzük.
Azt javasoljuk, hogy csak egyet telepítsen a vállalat összes olyan kiszolgálójára, amely most vagy a jövőben szinkronizálja ugyanazokat a fájlokat. Csak akkor hozzon létre több storage-szinkronizálási szolgáltatást, ha különböző kiszolgálókészletekkel rendelkezik, amelyeknek soha nem szabad adatokat cserélniük. (például: szinkronizálja ugyanazt az Azure-fájlmegosztást). Ellenkező esetben egyetlen storage-szinkronizálási szolgáltatás az ajánlott eljárás.

Válasszon egy Azure-régiót a storage sync szolgáltatáshoz, amely közel van az irodához. Az összes többi felhőbeli erőforrást ugyanabban a régióban kell telepíteni.
A felügyelet egyszerűsítése érdekében hozzon létre egy új erőforráscsoportot az előfizetésben, amely szinkronizálási és tárolási erőforrásokat tárol.

A következő cikk ismerteti, hogyan telepítheti a Storage Sync Service. Csak a dokinak ezt a részét kövesd. A dokumentum más alszakaszaira mutató hivatkozások a későbbi lépésekben jelennek meg.

[További információ a storage-szinkronizálási szolgáltatások üzembe helyezéséről.](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service)
