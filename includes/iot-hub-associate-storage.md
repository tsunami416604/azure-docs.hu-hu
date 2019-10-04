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
ms.openlocfilehash: f88f6aa6daec342b24d165c4c4cc0a89a0805f7a
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69558473"
---
## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Azure Storage-fiók hozzárendelése IoT Hubhoz

Mivel a szimulált eszköz alkalmazás feltölt egy fájlt egy blobba, rendelkeznie kell egy [Azure Storage](../articles/storage/common/storage-quickstart-create-account.md) -fiókkal, amely az IoT hubhoz van társítva. Ha egy Azure Storage-fiókot társít egy IoT hubhoz, az IoT hub létrehoz egy SAS URI-t. Az eszköz ezt az SAS URI-t használva biztonságosan tölthet fel egy fájlt egy blob-tárolóba. A IoT Hub szolgáltatás és az eszköz SDK-k összehangolják a SAS URI-t létrehozó folyamatot, és elérhetővé teszik azt egy olyan eszköz számára, amelyet a fájlok feltöltésére használhatnak.

Kövesse a [fájlfeltöltés konfigurálása a Azure Portal használatával](../articles/iot-hub/iot-hub-configure-file-upload.md)című témakör utasításait. Győződjön meg arról, hogy a blob-tároló társítva van az IoT hubhoz, és a fájl értesítései engedélyezve vannak.

![Fájlok értesítéseinek engedélyezése a portálon](./media/iot-hub-associate-storage/file-notifications-vs2019.png)
