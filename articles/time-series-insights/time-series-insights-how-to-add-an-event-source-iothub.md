---
title: "Az IoT-központ eseményforrást felvétele az Azure idő adatsorozat Insights környezet |} Microsoft Docs"
description: "Ez az oktatóanyag bemutatja, hogyan adhat hozzá egy eseményforrás, amely az IoT-központ az idő adatsorozat Insights-környezethez csatlakozik"
keywords: 
services: time-series-insights
documentationcenter: 
author: sandshadow
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/19/2017
ms.author: edett
ms.openlocfilehash: 3b98728ced81256d05b1bed2c92fc66c5ca61b98
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-add-an-iot-hub-event-source"></a>Az IoT-központ forrás hozzáadása

Ez az oktatóanyag bemutatja, hogyan adhat az Azure portál segítségével olvassa be az IoT-központ az az idő adatsorozat Insights környezethez eseményforrást hozzáadása.

## <a name="prerequisites"></a>Előfeltételek

Az IoT-központ hozott létre, és hozzá események írás. Az IoT-központok további információkért lásd: <https://azure.microsoft.com/services/iot-hub/>

> [A fogyasztói csoportok] Minden alkalommal adatsorozat Insights eseményforrás saját dedikált fogyasztói csoportot, amelyek nincsenek megosztva, más fogyasztóval rendelkeznie kell. Ha több olvasók ugyanazt a felhasználói csoportban lévő események felhasználásához, minden olvasók valószínűleg tekintse meg a hibákat. További információkért lásd: a [IoT Hub fejlesztői útmutató](../iot-hub/iot-hub-devguide.md).

## <a name="choose-an-import-option"></a>Válassza ki importálása

A következő eseményforrás beállításait is meg lehet adni manuálisan, vagy az IoT-központ az IoT-központok, amelyek elérhetők a lehet kiválasztani.
Az a **importálási lehetőség** választó, a következő lehetőségek közül választhat:

* Adja meg az IoT-központ beállításait manuálisan
* Az elérhető előfizetések IoT Hubjának használata

### <a name="select-an-available-iot-hub"></a>Válassza ki az elérhető IoT-központ

Az alábbi táblázat ismerteti az új eseményforrás lapon annak leírását az egyes lehetőségek lehetőséget választva az elérhető IoT-központ, egy Eseményforrás:

| TULAJDONSÁG NEVE | LEÍRÁS |
| --- | --- |
| Eseményforrás neve | A forrás neve. Ez a név a idő adatsorozat Insights környezeten belül egyedinek kell lennie.
| Forrás | Válasszon **IoT-központ** egy IoT-központ eseményforrás létrehozásához.
| Előfizetés-azonosító | Válassza ki az előfizetést, ahol ez az IoT-központ hozták létre.
| Az IoT-központ nevét | Válassza ki az IoT Hub nevét.
| Az IoT hub házirend neve | Válassza ki a megosztott elérési házirendet, amely az IoT-központ beállítások lapján található. Minden megosztott elérési házirend rendelkezik egy nevet, hogy Ön meghatározott engedélyekkel és hozzáférési kulcsokkal. A megosztott hozzáférési házirend a eseményforrás *kell* rendelkezik **service csatlakozás** engedélyek.
| Az IoT hub fogyasztói csoportot | A rendszer kiolvassa az eseményeket az IoT hubból fogyasztói csoportot. Ajánlott az eseményforrás dedikált fogyasztói csoportot használja.

### <a name="provide-iot-hub-settings-manually"></a>Adja meg az IoT-központ beállításait manuálisan

Az alábbi táblázat ismerteti az új esemény (forrás) lapot annak leírását az egyes tulajdonságai beállításainak megadásakor manuálisan:

| TULAJDONSÁG NEVE | LEÍRÁS |
| --- | --- |
| Eseményforrás neve | A forrás neve. Ez a név a idő adatsorozat Insights környezeten belül egyedinek kell lennie.
| Forrás | Válasszon **IoT-központ** egy IoT-központ eseményforrás létrehozásához.
| Előfizetés-azonosító | Az előfizetés, amelyben ez az IoT hub létrehozása történt.
| Erőforráscsoport | Az előfizetés, amelyben ez az IoT hub létrehozása történt.
| Az IoT-központ nevét | Az IoT Hub neve. Az IoT hub létrehozása után, akkor nevet is adott neki
| Az IoT hub házirend neve | A megosztott elérési házirendet, amelyet létrehozhat az IoT-központ beállítások lapon. Minden megosztott elérési házirend rendelkezik egy nevet, hogy Ön meghatározott engedélyekkel és hozzáférési kulcsokkal. A megosztott hozzáférési házirend a eseményforrás *kell* rendelkezik **service csatlakozás** engedélyek.
| Az IoT hub házirend kulcs | A Service Bus-névtér való hozzáférés hitelesítéséhez használt megosztott hozzáférési kulcs. Ide írja be az elsődleges vagy másodlagos kulcsot.
| Az IoT hub fogyasztói csoportot | A rendszer kiolvassa az eseményeket az IoT hubból fogyasztói csoportot. Ajánlott az eseményforrás dedikált fogyasztói csoportot használja.

## <a name="next-steps"></a>Következő lépések

1. Egy adat-hozzáférési házirend hozzá a környezethez [Define adat-hozzáférési házirendek](time-series-insights-data-access.md)
1. A környezet az elérni a [idő adatsorozat Insights portálon találja meg](https://insights.timeseries.azure.com)