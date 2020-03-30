---
title: Törölt irányítópult helyreállítása az Azure Portalon | Microsoft dokumentumok
description: Ha töröl egy közzétett irányítópultot az Azure Portalon, helyreállíthatja az irányítópultot.
services: azure-portal
author: mgblythe
ms.author: mblythe
ms.date: 01/21/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: af0c72f0bc5dd8f3a3cbae7b82b1ac56447cbb08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77133296"
---
# <a name="recover-a-deleted-dashboard-in-the-azure-portal"></a>Törölt irányítópult helyreállítása az Azure Portalon

Ha a nyilvános Azure-felhőben, és törli a _közzétett_ irányítópultot az Azure Portalon, a törléstől számított 14 napon belül helyreállíthatja az irányítópultot. Ha egy Azure kormányzati felhőben vagy az irányítópult nincs közzétéve, nem tudja helyreállítani, és újra kell építeni. Az irányítópultok közzétételéről az [Irányítópult közzététele](azure-portal-dashboard-share-access.md#publish-dashboard)című témakörben talál további információt. A közzétett irányítópult helyreállításához kövesse az alábbi lépéseket:

1. Az Azure Portal menüben válassza az **Erőforráscsoportok**lehetőséget, majd válassza ki azt az erőforráscsoportot, amelyben közzétette az irányítópultot (alapértelmezés szerint **irányítópultok).**

1. A **Tevékenységnapló**csoportban bontsa ki az **Irányítópult törlése** műveletet. Válassza az **Előzmények módosítása** lapot, majd a ** \<Törölt erőforrást\>**.

    ![Képernyőkép a változáselőzmények lapról](media/recover-shared-deleted-dashboard/change-history-tab.png)

1. Jelölje ki és másolja a bal oldali ablaktábla tartalmát, majd mentse egy _.json_ kiterjesztésű szövegfájlba. A portál a JSON-fájl segítségével hozza létre újra az irányítópultot.

    ![Képernyőkép a változási előzmények ről](media/recover-shared-deleted-dashboard/change-history-diff.png)

1. Az Azure Portal menüben válassza az **Irányítópultok menüt,** majd a **Feltöltés**lehetőséget.

    ![Képernyőkép az irányítópult feltöltéséről](media/recover-shared-deleted-dashboard/dashboard-upload.png)

1. Jelölje ki a mentett JSON-fájlt. A portál újra létrehozza az irányítópultot ugyanazzal a névvel és elemekkel, mint a törölt irányítópult.

1. Válassza a **Megosztás** lehetőséget az irányítópult közzétételéhez és a megfelelő hozzáférés-vezérlés visszaállításához.

    ![Képernyőkép az irányítópult megosztásáról](media/recover-shared-deleted-dashboard/dashboard-share.png)
