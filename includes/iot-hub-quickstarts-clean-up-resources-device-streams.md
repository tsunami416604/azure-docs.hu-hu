---
title: fájl (eszközadatfolyamok) felvétele
description: fájl belefoglalása
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 01/15/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 46ab75f161692dd048c19698af7027d0e0622b37
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "67446034"
---
Ha azt tervezi, hogy folytatja a következő ajánlott cikket, megtarthatja és újra felhasználhatja a már létrehozott erőforrásokat.

Ellenkező esetben a költségek elkerülése érdekében törölheti a cikkben létrehozott Azure-erőforrásokat.

> [!IMPORTANT]
> Az erőforráscsoport törlése nem vonható vissza. Az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen, nehogy véletlenül rossz erőforráscsoportot vagy erőforrásokat töröljön. Ha az IoT hubot egy meglévő erőforráscsoportban hozta létre, amely meg szeretné tartani, csak magát az IoT hub-erőforrást törölje, az erőforráscsoportot ne.
>

Erőforráscsoport törlése név alapján:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com), és válassza az **Erőforráscsoportok** elemet.

1. A **Név szerint szűrés** mezőbe írja be az IoT-központot tartalmazó erőforráscsoport nevét.

1. Az eredménylistában az erőforráscsoport tól jobbra jelölje ki a három pontot (**...**), majd az **Erőforráscsoport törlése**lehetőséget.

    ![Az "Erőforráscsoport törlése" gomb](./media/iot-hub-quickstarts-clean-up-resources-device-streams/iot-hub-delete-resource-group.png)

1. Az erőforráscsoport törlésének megerősítéséhez adja meg újra az erőforráscsoport nevét, majd kattintson a **Törlés gombra.** Néhány pillanat múlva az erőforráscsoport és az összes tartalmazott erőforrás törlődik.
