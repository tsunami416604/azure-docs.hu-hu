---
title: Azure Monitor munkafüzetek hozzáférés-vezérlés
description: Az összetett jelentések egyszerűsítése előre összeállított és egyéni paraméterezett munkafüzetekkel szerepköralapú hozzáférés-vezérléssel
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 20116ab105e4eb12875ba3cb279fb261eb5c70e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658421"
---
# <a name="access-control"></a>Hozzáférés-vezérlés

A munkafüzetek hozzáférés-vezérlése két dologra utal:

* Hozzáférés a munkafüzet adatainak olvasásához. Ezt a hozzáférést a munkafüzetben használt erőforrások szabványos [Azure-szerepkörei](https://docs.microsoft.com/azure/role-based-access-control/overview) szabályozzák. A munkafüzetek nem adják meg és nem konfigurálják az erőforrásokhoz való hozzáférést. A felhasználók általában kap ez a hozzáférés az erőforrásokhoz a [Figyelési olvasó](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) szerepkör használatával ezeket az erőforrásokat.

* A munkafüzetek mentéséhez szükséges hozzáférés

    - A `("My")` magánmunkafüzetek mentéséhez nincs szükség további jogosultságokra. Minden felhasználó mentheti a saját munkafüzeteket, és csak ők láthatják ezeket a munkafüzeteket.
    - A közös munkafüzetek mentéséhez írási jogosultságokra van szükség az erőforráscsoportban a munkafüzet mentéséhez. Ezeket a jogosultságokat általában a [Közreműködőfigyelés](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) szerepkör határozza meg, de a *Munkafüzetközreműködő* szerepkörsegítségével is beállítható.
    
## <a name="standard-roles-with-workbook-related-privileges"></a>Normál szerepkörök munkafüzethez kapcsolódó jogosultságokkal

[A Figyelőolvasó](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) szabványos /olvasási jogosultságokat tartalmaz, amelyeket a figyelőeszközök (beleértve a munkafüzeteket is) használnak az erőforrásokból származó adatok olvasására.

[A Közreműködő figyelése](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) magában foglalja az elemek mentéséhez használt különböző figyelési eszközök által használt általános `/write` jogosultságokat (beleértve `workbooks/write` a megosztott munkafüzetek mentéséhez való jogosultságot is).
A "Munkafüzet-közreműködő" "munkafüzetek/írás" jogosultságokat ad egy objektumhoz a közös munkafüzetek mentéséhez.
Nincs szükség különleges jogosultságokra ahhoz, hogy a felhasználók olyan saját munkafüzeteket mentsenek, amelyeket csak ők láthatnak.

Egyéni szerepköralapú hozzáférés-vezérlés esetén:

Hozzáadás `microsoft.insights/workbooks/write` a közös munkafüzetek mentéséhez. További információt a [Munkafüzet közreműködői](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) szerepkörben talál.

## <a name="next-steps"></a>További lépések

* [Ismerkedés a](workbooks-visualizations.md) munkafüzetekkel, számos gazdag vizualizációs lehetőséggel.
