---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 08/20/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 8aa4695ea1175fe9d558e02bae661c9610123299
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43087111"
---
## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Társítsa Azure Storage-fiókot, az IoT hubhoz

Mivel a szimulált eszközalkalmazás feltölt egy fájlt egy blobba, rendelkeznie kell egy [Azure Storage](../articles/storage/common/storage-quickstart-create-account.md) az IoT hubhoz tartozó fiókot. Ha egy Azure Storage-fiókot társítja az IoT hub, az IoT hub egy SAS URI-t hoz létre. Egy eszköz a SAS URI használatával biztonságosan fájl feltöltése a blob-tárolóba. Az IoT Hub szolgáltatás és az eszköz SDK-kat koordinálja a folyamat, amely létrehozza az SAS URI-t, és lehetővé teszi az eszköz egy fájlt tölthet fel.

Kövesse a [fájlfeltöltések konfigurálása az Azure portal használatával](../articles/iot-hub/iot-hub-configure-file-upload.md) rendelni az Azure Storage-fiókot, az IoT hubnak. Győződjön meg arról, hogy az IoT hub társítva-e a blobtárolót és, hogy a fájl értesítések engedélyezve vannak-e.

![Fájl értesítések engedélyezése a portálon](./media/iot-hub-associate-storage/enable-file-notifications.png)