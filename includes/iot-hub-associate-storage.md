---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 01/22/2019
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: ebc23ce4238c736442fbc4507e858876f9192fd9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76021072"
---
## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Azure Storage-fiók társítása az IoT Hubhoz

Mivel a szimulált eszközalkalmazás feltölt egy fájlt egy blobba, az IoT-központhoz társított [Azure Storage-fiókkal](../articles/storage/common/storage-account-create.md) kell rendelkeznie. Amikor egy Azure Storage-fiókot társít egy IoT-központtal, az IoT hub sAS URI-t hoz létre. Egy eszköz használhatja ezt a SAS URI biztonságosan feltölteni egy fájlt egy blob tárolóba. Az IoT Hub-szolgáltatás és az eszköz SDK-k koordinálja a folyamatot, amely létrehozza a SAS URI-t, és elérhetővé teszi az eszköz számára egy fájl feltöltésére.

Kövesse a [Fájlfeltöltések konfigurálása az Azure Portalhasználatával című](../articles/iot-hub/iot-hub-configure-file-upload.md)útmutató utasításait. Győződjön meg arról, hogy egy blob tároló van társítva az IoT hub, és hogy a fájlértesítések engedélyezve vannak.

![Fájlértesítések engedélyezése a portálon](./media/iot-hub-associate-storage/file-notifications-vs2019.png)
