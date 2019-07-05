---
title: adatfájl (eszköz Streamek)
description: fájl belefoglalása
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 01/15/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 46ab75f161692dd048c19698af7027d0e0622b37
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446034"
---
Ha azt tervezi, hogy a következő ajánlott cikkre, tartsa, és újra felhasználhatja a már létrehozott erőforrásokat.

Ellenkező esetben költségek elkerülése érdekében törölheti az Azure-ebben a cikkben létrehozott erőforrásokat.

> [!IMPORTANT]
> Az erőforráscsoport törlése nem vonható vissza. Az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen, nehogy véletlenül rossz erőforráscsoportot vagy erőforrásokat töröljön. Létrehozta az IoT hubon belüli egy meglévő erőforráscsoportot, amely tartalmazza az erőforrásokat, amelyeket meg szeretne tartani, csak az IoT hub erőforrás magát, nem az erőforráscsoport törlése
>

Erőforráscsoport törlése név alapján:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com), és válassza az **Erőforráscsoportok** elemet.

1. Az a **Szűrés név alapján** mezőbe írja be az erőforráscsoport, amely tartalmazza az IoT hub nevét.

1. A jobb oldalon az erőforráscsoport, a találatok listájában válassza a három pontot ( **...** ), majd válassza ki **erőforráscsoport törlése**.

    ![A "Erőforráscsoport törlése" gomb](./media/iot-hub-quickstarts-clean-up-resources-device-streams/iot-hub-delete-resource-group.png)

1. Az erőforráscsoport törlésének megerősítéséhez írja be újból az erőforráscsoport nevét, és válassza ki **törlése**. Néhány pillanat múlva az erőforráscsoportot és a benne foglalt erőforrásokat törlődnek.
