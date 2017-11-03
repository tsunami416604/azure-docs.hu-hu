---
title: "Ismerkedés az Azure-ban automatikus skálázás |} Microsoft Docs"
description: "Ismerje meg, hogy az erőforrás méretezése az Azure-ban."
author: rajram
manager: rboucher
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: d37d3fda-8ef1-477c-a360-a855b418de84
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2017
ms.author: rajram
ms.openlocfilehash: 84ce3af2bef1e3204ad91567d155d7cbf90e9c7e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-autoscale-in-azure"></a>Ismerkedés az Azure-ban automatikus skálázás
A cikkből megtudhatja, hogyan állíthat be az automatikus skálázási beállításai a Microsoft Azure-portálon az erőforrás.

Az Azure a figyelő automatikus skálázás csak a virtuálisgép-méretezési csoportok, felhőalapú szolgáltatások, Azure App Service-csomagokról és App Service-környezetek vonatkozik. 

## <a name="discover-the-autoscale-settings-in-your-subscription"></a>Az automatikus skálázási beállításokat az előfizetésében felderítése
Is felderítheti, amelyek automatikus skálázás esetében alkalmazható Azure figyelőben összes erőforrást. Részletes útmutató a következő lépéseket használhatja:

1. Nyissa meg a [Azure-portálon.][1]
2. Kattintson az Azure-figyelő ikonra a bal oldali ablaktáblán.
  ![Nyissa meg az Azure-figyelő][2]
3. Kattintson a **automatikus skálázás** megtekintéséhez, amelynek automatikus skálázás kell alkalmazni, és azok állapotának automatikus skálázás összes erőforrást.
  ![Az Azure a figyelő automatikus skálázás felderítése][3]

A keresőablak lefelé a listában hatókör legfelül segítségével válassza ki azokat az erőforrásokat egy adott erőforráscsoport, adott erőforrástípusokra, vagy egy adott erőforrás.

Minden erőforrás található az aktuális példányszám és az automatikus skálázás állapotát. Az automatikus skálázás állapota lehet:

- **Nincs konfigurálva**: nincs engedélyezve automatikus skálázás még ennél az erőforrásnál.
- **Engedélyezett**: automatikus skálázás engedélyezett ehhez az erőforráshoz.
- **Letiltott**: automatikus skálázás le van tiltva ennél az erőforrásnál.

## <a name="create-your-first-autoscale-setting"></a>Az első automatikus skálázási beállítás létrehozása

Most már halad át egy egyszerű lépésenkénti útmutató az első automatikus skálázási beállítás létrehozásához.

1. Nyissa meg a **automatikus skálázás** panel az Azure-figyelő, és válassza a skálázni kívánt erőforrás. (Az alábbi lépéseket használni a webalkalmazáshoz tartozó App Service-csomagot. Is [az első ASP.NET-webalkalmazás létrehozása az Azure-ban 5 perc múlva.] [4])
2. Vegye figyelembe, hogy az aktuális példányszám 1. Kattintson a **engedélyezze az automatikus skálázás**.
  ![Új webalkalmazás skálázási beállítást][5]
3. Adjon meg egy nevet a skálázási beállítást, és kattintson **szabály hozzáadása**. Nyissa meg a figyelmeztetés a skálázási szabályhoz, amelyet a környezet ablaktáblán a jobb oldalon. Alapértelmezés szerint ez a beállítás méretezése a példányok száma 1, ha az erőforrás CPU aránya meghaladja a 70 %-os állítja be. Az alapértelmezett értéken hagyja, és kattintson a **Hozzáadás**.
  ![A webes alkalmazás skálázási beállítás létrehozása][6]
4. Most létrehozott az első skálázási szabályhoz. Vegye figyelembe, hogy a UX ajánlott eljárások azt javasolja, és jelzi, hogy "javasoljuk, hogy van legalább egy skálázási szabály." Ehhez tegye a következőket:
  
    a. Kattintson a **szabály hozzáadása**. 

    b. Állítsa be **operátor** való **kisebb, mint**.

    c. Állítsa be **küszöbérték** való **20**.

    d. Állítsa be **művelet** való **számolva csökkentése**.

   Most már rendelkeznie kell a skálázási beállítást, hogy méretezik out/méretezik a CPU-használat alapján.
   ![A skála CPU alapján][8]
5. Kattintson a **Save** (Mentés) gombra.

Gratulálunk! Most már sikeresen létrehozta a CPU-használat alapján a webalkalmazás automatikus skálázásra az első skálázási beállítást.

> [!NOTE] 
> Ugyanezek a lépések Ismerkedjen meg a virtuálisgép-méretezési csoport vagy szerepkör-szolgáltatás a felhő vonatkoznak.

## <a name="other-considerations"></a>Egyéb szempontok
### <a name="scale-based-on-a-schedule"></a>A skála a ütemezés szerint
Mellett a skála alapján a CPU beállíthatja a skála eltérően a hét adott napjaira.

1. Kattintson a **méretezési feltétel hozzáadása**.
2. A skála mód és a szabályok beállítását megegyezik az alapértelmezett feltétel.
3. Válassza ki **ismételje meg az adott napokra** az ütemezéshez.
4. Válassza ki a nap és a kezdő/záró idő, amikor a skála feltétel alkalmazni kívánja a.

![Skála feltétel ütemezésen alapuló][9]
### <a name="scale-differently-on-specific-dates"></a>Az adott dátumok másképp méretezése
Skála alapján a CPU, mellett adhatja meg a skála másképp konkrét dátumokat.

1. Kattintson a **méretezési feltétel hozzáadása**.
2. A skála mód és a szabályok beállítását megegyezik az alapértelmezett feltétel.
3. Válassza ki **adja meg a kezdő és záró dátum** az ütemezéshez.
4. Válassza ki a kezdő és záró dátum és a kezdő/záró idő, amikor a skála feltétel alkalmazni kívánja a.

![Skála feltétel dátumok alapján][10]

### <a name="view-the-scale-history-of-your-resource"></a>Az erőforrás méretezési előzményeinek megtekintése
Amikor az erőforrás méretezése felfelé vagy lefelé, eseményt naplózott, a műveletnaplóban. Megtekintheti az erőforrás méretezési előzményeinek vonatkozóan az elmúlt 24 óra átvált a **futtatási előzményei** fülre.

![futtatási előzményei][11]

Ha szeretné megtekinteni a teljes méretezési előzmények (90 nap), jelölje be **Ide kattintva további részleteket**. A műveletnapló nyílik meg, előre kiválasztott erőforrás és kategória automatikus skálázási.

### <a name="view-the-scale-definition-of-your-resource"></a>A skála meghatározása az erőforrás megtekintéséhez
Automatikus skálázás egy Azure Resource Manager szerinti erőforrás. Megtekintheti a skála definition a JSON-ban átvált a **JSON** fülre.

![Skála meghatározása][12]

Módosíthatja a JSON-ban közvetlenül, ha szükséges. Ezek a változások azok mentése után jelenik meg.

### <a name="disable-autoscale-and-manually-scale-your-instances"></a>Tiltsa le az automatikus skálázás, és manuálisan méretezhető, a példányok
Előfordulhat, hogy mikor szeretné letiltani az aktuális skálázási beállítást, és manuálisan méretezhető, az erőforrás.

Kattintson a **letiltásával** gombra az oldal tetején.
![Tiltsa le az automatikus skálázás][13]

> [!NOTE] 
> Ez a beállítás letiltja a konfigurációt. Azonban is vissza az automatikus skálázás megismételni engedélyezése után. 

Beállíthatja, hogy manuálisan méretezésére példányok száma.

![Manuális méretezési készlet][14]

Kattintva mindig visszatérhet automatikus skálázás **engedélyezze az automatikus skálázás** , majd **mentése**.

## <a name="next-steps"></a>Következő lépések
- [Hozzon létre egy tevékenység napló riasztási előfizetés minden automatikus skálázási motor műveletek figyelése](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Hozzon létre egy tevékenység napló a riasztást az előfizetés az összes sikertelen automatikus skálázás méretezési-a/kibővített műveletek figyelése](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

<!--Reference-->
[1]:https://portal.azure.com
[2]: ./media/monitoring-autoscale-get-started/azure-monitor-launch.png
[3]: ./media/monitoring-autoscale-get-started/discover-autoscale-azure-monitor.png
[4]: https://docs.microsoft.com/azure/app-service/app-service-web-get-started-dotnet
[5]: ./media/monitoring-autoscale-get-started/scale-setting-new-web-app.png
[6]: ./media/monitoring-autoscale-get-started/create-scale-setting-web-app.png
[7]: ./media/monitoring-autoscale-get-started/scale-in-recommendation.png
[8]: ./media/monitoring-autoscale-get-started/scale-based-on-cpu.png
[9]: ./media/monitoring-autoscale-get-started/scale-condition-schedule.png
[10]: ./media/monitoring-autoscale-get-started/scale-condition-dates.png
[11]: ./media/monitoring-autoscale-get-started/scale-history.png
[12]: ./media/monitoring-autoscale-get-started/scale-definition-json.png
[13]: ./media/monitoring-autoscale-get-started/disable-autoscale.png
[14]: ./media/monitoring-autoscale-get-started/set-manualscale.png

