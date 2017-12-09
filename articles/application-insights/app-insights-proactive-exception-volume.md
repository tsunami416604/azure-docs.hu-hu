---
title: "Észlelési - szokatlan mértékben megnőtt a kivétel köteten, az Azure Application Insights intelligens |} Microsoft Docs"
description: "Az alkalmazáskivételek az Azure Application insights szolgáltatással kivétel kötetén szokatlan mintára figyelése."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: mbullwin
ms.openlocfilehash: 8030f3331a03170bb265c417a57725544bdc7d3f
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2017
---
# <a name="abnormal-rise-in-exception-volume-preview"></a>Szokatlan mértékben megnőtt a kivétel kötet (előzetes verzió)

Az Application Insights automatikusan elemzi az alkalmazást a kivételek, és képes figyelmeztessen a szokatlan minták a kivételtelemetria.

Ehhez a szolgáltatáshoz nem speciális beállítási eltérő [kivétel a jelentéskészítés konfigurálása](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-exceptions#set-up-exception-reporting) az alkalmazásra vonatkozóan. Azt akkor aktív, ha az alkalmazás elég kivételtelemetria állít elő.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Ha visszajelzést kap az ilyen típusú intelligens észlelési értesítést?
Az ilyen típusú értesítést kaphat, ha az alkalmazás tapasztal szokatlan mértékben megnőtt az adott típusú kivételek száma egy nap alatt az előző hét napban számított alapterv képest.
A kivétel számának növekedését észlelése közben figyelembe véve az alkalmazás használatának természetes növekedése gépi tanulási algoritmusok használatban van.

## <a name="does-my-app-definitely-have-a-problem"></a>Saját alkalmazás mindenképpen rendelkezik probléma?
Nem, egy értesítés nem jelenti azt, hogy az alkalmazás véglegesen rendelkezik-e probléma. Kivételek száma általában azt jelzi, alkalmazásproblémák, bár ezeket a kivételeket lehet ártalmatlanok, és megfelelően az alkalmazás által kezelt.

## <a name="how-do-i-fix-it"></a>Hogyan tegye megjavítani?
Az értesítések tárgya lehet a diagnosztikai adatokat küld a diagnosztika folyamat támogatja:
1. **Osztályozás.** Az értesítés elsajátíthatja, hogy hány felhasználó, vagy azt, hogy hány kérésnek érintett. Ez segíthet a probléma megoldásának prioritást.
2. **Hatókör.** A hiba érinti az összes forgalom, vagy csak egy művelet? Ez az információ az értesítés lehet lekérni.
3. **Diagnosztizálásához.** Az észlelési, amelyből a kivétel lépett fel, valamint a kivétel típusát metódus adatait tartalmazza. Használhatja a kapcsolódó elemeket, és jelentések csatolása támogató adatokat, további segítséget a probléma diagnosztizálásához.