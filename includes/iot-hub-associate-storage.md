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
ms.openlocfilehash: 8693c48905155ed757bb727e42f4180f36c015f1
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55513987"
---
## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Társítsa Azure Storage-fiókot, az IoT hubhoz

Mivel a szimulált eszközalkalmazás feltölt egy fájlt egy blobba, rendelkeznie kell egy [Azure Storage](../articles/storage/common/storage-quickstart-create-account.md) társított az IoT hub-fiók. Ha egy Azure Storage-fiókot társítja az IoT hub, az IoT hub egy SAS URI-t hoz létre. Egy eszköz a SAS URI használatával biztonságosan fájl feltöltése a blob-tárolóba. Az IoT Hub szolgáltatás és az eszköz SDK-kat koordinálja a folyamat, amely létrehozza az SAS URI-t, és lehetővé teszi az eszköz egy fájlt tölthet fel.

Kövesse a [fájlfeltöltések konfigurálása az Azure portal használatával](../articles/iot-hub/iot-hub-configure-file-upload.md). Győződjön meg arról, hogy az IoT hub társítva-e a blobtárolót és, hogy a fájl értesítések engedélyezve vannak-e.

![Fájl értesítések engedélyezése a portálon](./media/iot-hub-associate-storage/enable-file-notifications.png)