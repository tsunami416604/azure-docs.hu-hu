---
title: Intelligens detektálás – rendellenes növekedése a kivételek mennyiségének, az Azure Application Insightsban |} A Microsoft Docs
description: Az alkalmazáskivételek az Azure Application Insights a kivételek mennyiségének szokatlan mintákat figyelésére.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/08/2017
ms.author: mbullwin
ms.openlocfilehash: a6e7e8e01ccb623a3ff340c318c9c238c919cb38
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54028566"
---
# <a name="abnormal-rise-in-exception-volume-preview"></a>Rendellenes növekedése a kivételek mennyiségének (előzetes verzió)

Az Application Insights automatikusan elemzi a az alkalmazásban fellépő kivételek, és képes figyelmeztessen a szokatlan mintákat a kivétel telemetria.

Ez a funkció nem speciális beállítás nem szükséges [kivétel a jelentéskészítés konfigurálása](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-exceptions#set-up-exception-reporting) az alkalmazáshoz. Ez akkor aktív, ha az alkalmazása elég kivételtelemetria hoz létre.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Ha szeretné be intelligens detektálás értesítést az ilyen típusú?
Ez az értesítés típusát fordulhatnak elő, ha az alkalmazás tapasztal az adott típusú kivételek számát rendellenes növekedése a nap folyamán vetítve az előző hét nap alapkonfigurációhoz képest.
Gépi tanulási algoritmusokat használ a megnövekedhet a kivételek száma, az alkalmazás használatának természetes növekedés figyelembe véve ugyanakkor kimutatására.

## <a name="does-my-app-definitely-have-a-problem"></a>Saját alkalmazás mindenképp rendelkezik a probléma?
Nem, egy értesítés nem jelenti azt, hogy az alkalmazás mindenképp van probléma. Bár a kivételek száma általában azt jelzi, hogy egy alkalmazás hibát, az ilyen kivételek lehet jóindulatú és megfelelően az alkalmazás által kezelt.

## <a name="how-do-i-fix-it"></a>Hogyan javíthatom?
Az értesítések közé tartoznak a diagnosztikai adatokat a diagnosztika során támogatja:
1. **Osztályozási.** Az értesítés bemutatja, hogy hány felhasználó, vagy hogy hány kérésnek érintett. Ez segíthet a probléma prioritást rendelhet.
2. **A hatókör.** A probléma érintő minden forgalmat, vagy csak néhány művelet? Ez az információ szerezhető az értesítést.
3. **Diagnosztizálja a.** Az észlelési módszer, amelyről a kivétel lépett fel, valamint a kivétel típusát kapcsolatos információt tartalmazza. Is használhatja a kapcsolódó elemek és jelentések összekapcsolása támogatási információk, további segítséget a probléma diagnosztizálásához.