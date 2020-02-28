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
ms.openlocfilehash: 20116ab105e4eb12875ba3cb279fb261eb5c70e4
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77658421"
---
# <a name="access-control"></a>Hozzáférés-vezérlés

A munkafüzetek hozzáférés-vezérlése két dologra utal:

* A munkafüzetben tárolt adatolvasáshoz szükséges hozzáférés. Ezt a hozzáférést a munkafüzetben használt erőforrások szabványos [Azure-szerepkörei](https://docs.microsoft.com/azure/role-based-access-control/overview) vezérlik. A munkafüzetek nem határozzák meg és nem konfigurálhatják a hozzáférést ezekhez az erőforrásokhoz. A felhasználók általában a [figyelés olvasó](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) szerepkörrel érhetik el ezeket az erőforrásokat.

* A munkafüzetek mentéséhez szükséges hozzáférés

    - A privát `("My")` munkafüzetek mentésekor nincs szükség további jogosultságokra. Minden felhasználó menthet privát munkafüzeteket, és csak azok láthatják ezeket a munkafüzeteket.
    - A megosztott munkafüzetek mentésekor írási jogosultságok szükségesek egy erőforráscsoporthoz a munkafüzet mentéséhez. Ezeket a jogosultságokat általában a [figyelési közreműködő](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) szerepkör adja meg, de a *munkafüzetek közreműködői* szerepkörén keresztül is beállítható.
    
## <a name="standard-roles-with-workbook-related-privileges"></a>A munkafüzettel kapcsolatos jogosultságokkal rendelkező standard szerepkörök

A [monitorozási olvasó](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) olyan szabványos/Read jogosultságokat tartalmaz, amelyeket a figyelési eszközök (például a munkafüzetek) használnak az erőforrások adatainak olvasásához.

A [monitorozási közreműködő](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) általános `/write` jogosultságokat tartalmaz a különböző monitorozási eszközök által az elemek mentéséhez (beleértve `workbooks/write` jogosultságot a megosztott munkafüzetek mentéséhez).
A "munkafüzetek közreműködői" felveszi a "munkafüzetek/írás" jogosultságokat egy objektumba a megosztott munkafüzetek mentéséhez.
Nincs szükség különleges jogosultságra ahhoz, hogy a felhasználók olyan privát munkafüzeteket mentsenek, amelyeket csak láthatnak.

Egyéni szerepköralapú hozzáférés-vezérléshez:

`microsoft.insights/workbooks/write` hozzáadása a megosztott munkafüzetek mentéséhez. További részletekért tekintse meg a [munkafüzet közreműködői](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) szerepkört.

## <a name="next-steps"></a>Következő lépések

* [Ismerkedjen](workbooks-visualizations.md) meg a munkafüzetek számos gazdag vizualizációs lehetőségével.
