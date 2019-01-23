---
title: Az automatikus méretezés az Azure-ban – első lépések
description: Az erőforrás webalkalmazás méretezése, Felhőszolgáltatások, virtuális gép vagy virtuálisgép-méretezési csoport beállítása az Azure-ban.
author: rajram
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/07/2017
ms.author: rajram
ms.subservice: autoscale
ms.openlocfilehash: 0535c84a8ee0776c2c35a46d3c7510a2cd615cf6
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54469491"
---
# <a name="get-started-with-autoscale-in-azure"></a>Az automatikus méretezés az Azure-ban – első lépések
Ez a cikk azt ismerteti, hogyan állítható be az automatikus skálázási beállítások a Microsoft Azure Portalon az erőforrás.

Az Azure Monitor automatikus skálázása csak érvényes [Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [App Service - webalkalmazások](https://azure.microsoft.com/services/app-service/web/), és [APIManagement-szolgáltatások](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

## <a name="discover-the-autoscale-settings-in-your-subscription"></a>Fedezze fel az előfizetés az automatikus méretezési beállításokkal
Felfedezheti, hogy, amelynek automatikus skálázás alkalmazható az Azure monitorban összes erőforrást. Részletes útmutató használja az alábbi lépéseket:

1. Nyissa meg a [Azure Portalon.][1]
1. Kattintson az Azure Monitor ikonra a bal oldali panelen.
  ![Nyissa meg az Azure Monitor][2]
1. Kattintson a **automatikus skálázási** , amelynek automatikus skálázási kell alkalmazni, valamint azok állapotának automatikus skálázási összes erőforrás megtekintéséhez.
  ![Fedezze fel az Azure Monitor automatikus méretezés][3]

Használhatja a szűrő ablaktábla felső hatókörhöz lefelé a listában válassza ki az erőforrásokat egy adott erőforráscsoportban, adott erőforrástípusokra vagy egy adott erőforrást.

Az egyes erőforrások található a jelenlegi példányszám, és az automatikus skálázás állapota. Az automatikus skálázás állapota lehet:

- **Nincs konfigurálva**: Automatikus skálázási még nincs engedélyezve ehhez az erőforráshoz.
- **Engedélyezett**: Engedélyezte az automatikus méretezés ehhez az erőforráshoz.
- **Letiltott**: Az erőforrás le van tiltva az automatikus méretezés.

## <a name="create-your-first-autoscale-setting"></a>Az első automatikus skálázási beállítás létrehozása

Vegyük most fel keresztül egyszerű lépésenkénti útmutató az első automatikus skálázási beállítás létrehozásához.

1. Nyissa meg a **automatikus skálázási** panel az Azure Monitor, és válasszon egy erőforrás, amely méretezésére. (Az alábbi lépéseket használja egy webes alkalmazáshoz társított App Service-csomagot. Is [az első ASP.NET-webalkalmazás létrehozása az Azure-ban 5 perc múlva.] [4])
1. Vegye figyelembe, hogy a jelenlegi példányszám 1. Kattintson a **automatikus skálázás engedélyezése**.
  ![Új webalkalmazás számára a skálázási beállítás][5]
1. Adjon meg egy nevet a skálázási beállítás, és kattintson a **egy szabály hozzáadásához**. Figyelje meg, nyisson meg egy helyi panel jobb oldalán skálázási szabály beállításainak. Alapértelmezés szerint ez a beállítás arra, hogy a példányok száma 1 méretezhető, ha az erőforrás Processzorhasználat aránya meghaladja a 70 %-os. Meghagyhatja az alapértelmezett értékeit, és kattintson a **Hozzáadás**.
  ![A skálázási beállítás egy webalkalmazás létrehozása][6]
1. Sikeresen létrehozott az első skálázási szabályhoz. Vegye figyelembe, hogy a felhasználói felület azt javasolja, ajánlott eljárások és kijelenti, hogy "azt javasoljuk, hogy legalább egy skálázási szabály." Ehhez tegye a következőket:

    a. Kattintson a **egy szabály hozzáadásához**.

    b. Állítsa be **operátor** való **kevesebb mint**.

    c. Állítsa be **küszöbérték** való **20**.

    d. Állítsa be **művelet** való **csökkentése a következővel**.

   Most már a skálázási beállítás, hogy méretezhető out/méretezi a processzorhasználat alapján.
   ![A CPU kihasználtságához][8]
1. Kattintson a **Save** (Mentés) gombra.

Gratulálunk! Most már sikeresen létrehozta az első, CPU-használat alapján a webalkalmazás automatikus skálázási beállítás.

> [!NOTE]
> Ugyanazokat a lépéseket kell a alkalmazni egy virtuálisgép-méretezési csoportot használatának első lépései, vagy a felhőbeli szerepkör-szolgáltatás.

## <a name="other-considerations"></a>Egyéb szempontok
### <a name="scale-based-on-a-schedule"></a>Méretezési csoport egy ütemezés alapján
A CPU kihasználtságához mellett beállíthatja a méretezési csoport eltérően a hét adott napjaira.

1. Kattintson a **skálázási feltétel hozzáadása**.
1. A skálázási mód és a szabályok beállítása ugyanaz, mint az alapértelmezett feltétel.
1. Válassza ki **adott napokon ismétlődik** az ütemezés számára.
1. Válassza ki a napokat és mikor kell alkalmazni a skálázási feltétel kezdő és befejező időpontját.

![Az ütemezés alapján a skálázási feltétel][9]
### <a name="scale-differently-on-specific-dates"></a>A konkrét dátumokat eltérően méretezése
A CPU kihasználtságához mellett beállíthatja a méretezési csoport eltérően a konkrét dátumokat.

1. Kattintson a **skálázási feltétel hozzáadása**.
1. A skálázási mód és a szabályok beállítása ugyanaz, mint az alapértelmezett feltétel.
1. Válassza ki **kezdő/záró dátumok megadása** az ütemezés számára.
1. Válassza ki a kezdő és záró dátumát, és mikor kell alkalmazni a skálázási feltétel kezdő és befejező időpontját.

![A skálázási feltétel dátuma alapján][10]

### <a name="view-the-scale-history-of-your-resource"></a>Az erőforrás-méretezési csoport előzményeinek megtekintése
Minden alkalommal, amikor az erőforrás méretezése felfelé és lefelé, egy eseményt a rendszer naplózza a tevékenységnaplóban. Megtekintheti a méretezési csoport előzményeinek az erőforrás az elmúlt 24 órában átvált a **futtatási előzmények** fülre.

![Előzmények][11]

Ha a teljes méretezési csoport előzményeinek megtekintése (legfeljebb 90 napig), jelölje be **Ide kattintva további részleteket**. A tevékenységnapló megnyílik, és az Automatikus méretezéssel, előre kiválasztott erőforrás és kategória.

### <a name="view-the-scale-definition-of-your-resource"></a>Az erőforrás a méretezési csoport definíciójának megtekintése
Automatikus skálázási egy Azure Resource Manager-erőforrást. Megtekintheti a méretezési csoport definícióját JSON átvált a **JSON** fülre.

![Méretezési csoport meghatározása][12]

Módosíthatja a JSON-ban közvetlenül, ha szükséges. Ezek a módosítások megjelennek, miután menti őket.

### <a name="disable-autoscale-and-manually-scale-your-instances"></a>Automatikus skálázás letiltása, és méretezzen manuálisan a példányok
Előfordulhat, hogy azt szeretné, tiltsa le az aktuális skálázási beállítás, és méretezzen manuálisan az erőforrást.

Kattintson a **automatikus skálázás letiltása** gombra az oldal tetején.
![Automatikus skálázás letiltása][13]

> [!NOTE]
> Ez a beállítás letiltja a konfigurációt. Azonban visszatérhet azt követően újra engedélyezni az automatikus méretezés.

Most már beállíthatja a manuális skálázása kívánt példányok számát.

![Manuális skálázás beállítása][14]

Kattintva mindig visszatérhet az automatikus méretezés **automatikus skálázás engedélyezése** , majd **mentése**.

## <a name="next-steps"></a>További lépések
- [Hozzon létre egy tevékenység Log riasztási az előfizetés összes automatikus skálázási motor műveletek figyelése](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Hozzon létre egy tevékenység Log riasztási az előfizetés az összes sikertelen automatikus skálázási méretezési-a/horizontális felskálázás műveletek figyelése](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

<!--Reference-->
[1]:https://portal.azure.com
[2]: ./media/autoscale-get-started/azure-monitor-launch.png
[3]: ./media/autoscale-get-started/discover-autoscale-azure-monitor.png
[4]: https://docs.microsoft.com/azure/app-service/app-service-web-get-started-dotnet
[5]: ./media/autoscale-get-started/scale-setting-new-web-app.png
[6]: ./media/autoscale-get-started/create-scale-setting-web-app.png
[7]: ./media/autoscale-get-started/scale-in-recommendation.png
[8]: ./media/autoscale-get-started/scale-based-on-cpu.png
[9]: ./media/autoscale-get-started/scale-condition-schedule.png
[10]: ./media/autoscale-get-started/scale-condition-dates.png
[11]: ./media/autoscale-get-started/scale-history.png
[12]: ./media/autoscale-get-started/scale-definition-json.png
[13]: ./media/autoscale-get-started/disable-autoscale.png
[14]: ./media/autoscale-get-started/set-manualscale.png

