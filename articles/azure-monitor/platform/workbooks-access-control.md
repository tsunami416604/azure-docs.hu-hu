---
title: Azure Monitor munkafüzetek hozzáférés-vezérlése
description: A szerepköralapú hozzáférés-vezérléssel rendelkező, előre elkészített és egyéni paraméteres munkafüzetek összetett jelentéskészítésének egyszerűbbé tétele
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: dc6e1d738bf255fe7baa244556bad4519979b1df
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86539295"
---
# <a name="access-control"></a>Hozzáférés-vezérlés

A munkafüzetek hozzáférés-vezérlése két dologra utal:

* A munkafüzetben tárolt adatolvasáshoz szükséges hozzáférés. Ezt a hozzáférést a munkafüzetben használt erőforrások szabványos [Azure-szerepkörei](../../role-based-access-control/overview.md) vezérlik. A munkafüzetek nem határozzák meg és nem konfigurálhatják a hozzáférést ezekhez az erőforrásokhoz. A felhasználók általában a [figyelés olvasó](../../role-based-access-control/built-in-roles.md#monitoring-reader) szerepkörrel érhetik el ezeket az erőforrásokat.

* A munkafüzetek mentéséhez szükséges hozzáférés

    - A privát `("My")` munkafüzetek mentéséhez nincs szükség további jogosultságokra. Minden felhasználó menthet privát munkafüzeteket, és csak azok láthatják ezeket a munkafüzeteket.
    - A megosztott munkafüzetek mentésekor írási jogosultságok szükségesek egy erőforráscsoporthoz a munkafüzet mentéséhez. Ezeket a jogosultságokat általában a [figyelési közreműködő](../../role-based-access-control/built-in-roles.md#monitoring-contributor) szerepkör adja meg, de a *munkafüzetek közreműködői* szerepkörén keresztül is beállítható.
    
## <a name="standard-roles-with-workbook-related-privileges"></a>A munkafüzettel kapcsolatos jogosultságokkal rendelkező standard szerepkörök

A [monitorozási olvasó](../../role-based-access-control/built-in-roles.md#monitoring-reader) olyan szabványos/Read jogosultságokat tartalmaz, amelyeket a figyelési eszközök (például a munkafüzetek) használnak az erőforrások adatainak olvasásához.

A [figyelő közreműködői](../../role-based-access-control/built-in-roles.md#monitoring-contributor) `/write` a különböző figyelési eszközök által az elemek mentéséhez használt általános jogosultságokat tartalmazza (beleértve `workbooks/write` a megosztott munkafüzetek mentésére vonatkozó jogosultságot).
A "munkafüzetek közreműködői" felveszi a "munkafüzetek/írás" jogosultságokat egy objektumba a megosztott munkafüzetek mentéséhez.
Nincs szükség különleges jogosultságra ahhoz, hogy a felhasználók olyan privát munkafüzeteket mentsenek, amelyeket csak láthatnak.

Egyéni szerepköralapú hozzáférés-vezérléshez:

Hozzáadás `microsoft.insights/workbooks/write` a megosztott munkafüzetek mentéséhez. További részletekért tekintse meg a [munkafüzet közreműködői](../../role-based-access-control/built-in-roles.md#monitoring-contributor) szerepkört.

## <a name="next-steps"></a>Következő lépések

* [Ismerkedjen](workbooks-visualizations.md) meg a munkafüzetek számos gazdag vizualizációs lehetőségével.
