---
title: Az automatikus skálázás használatának első lépései az Azure-ban
description: Ismerje meg, hogyan skálázhatja az erőforrás-webalkalmazást, a felhőszolgáltatást, a virtuális gépet vagy a virtuálisgép-méretezési készletet az Azure-ban.
ms.topic: conceptual
ms.date: 07/07/2017
ms.subservice: autoscale
ms.openlocfilehash: 0a40496e4d496d0062c6ee7a6ab935c1ad9b35a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75396354"
---
# <a name="get-started-with-autoscale-in-azure"></a>Ismerkedés az automatikus skálázással az Azure-ban
Ez a cikk bemutatja, hogyan állíthatja be az erőforrás automatikus skálázási beállításait a Microsoft Azure Portalon.

Az Azure Monitor automatikus skálázása csak [a virtuális gép méretezési készleteire,](https://azure.microsoft.com/services/virtual-machine-scale-sets/) [a felhőszolgáltatásokra,](https://azure.microsoft.com/services/cloud-services/) [az appszolgáltatásra – webalkalmazásokra](https://azure.microsoft.com/services/app-service/web/)és [az API-kezelési szolgáltatásokra](https://docs.microsoft.com/azure/api-management/api-management-key-concepts)vonatkozik.

## <a name="discover-the-autoscale-settings-in-your-subscription"></a>Az előfizetés automatikus skálázási beállításainak felfedezése
Az Azure Monitorban felderítheti az összes olyan erőforrást, amelyre az automatikus skálázás alkalmazható. Az alábbi lépésekkel részletes forgatókönyvet hajthat végre:

1. Nyissa meg az [Azure Portalt.][1]
1. Kattintson az Azure Monitor ikonra a bal oldali ablaktáblában.
  ![Az Azure-figyelő megnyitása][2]
1. Kattintson az **Automatikus skálázás gombra** az összes olyan erőforrás megtekintéséhez, amelyre az automatikus skálázás alkalmazható, valamint az aktuális automatikus skálázási állapotuk megtekintéséhez.
  ![Automatikus skálázás felderítése az Azure Monitorban][3]

A felső sarokban lévő szűrőablaksegítségével a lista hatókörét használhatja egy adott erőforráscsoport, adott erőforrástípusok vagy egy adott erőforrás erőforrásainak kiválasztásához.

Minden erőforráshoz megtalálja az aktuális példányok számát és az automatikus skálázás állapotát. Az automatikus skálázás állapota a következő lehet:

- **Nincs konfigurálva**: Még nem engedélyezte az automatikus skálázást ehhez az erőforráshoz.
- **Engedélyezve**: Engedélyezte az automatikus skálázást ehhez az erőforráshoz.
- **Letiltva**: Letiltotta az automatikus skálázást ehhez az erőforráshoz.

## <a name="create-your-first-autoscale-setting"></a>Az első automatikus skálázási beállítás létrehozása

Most egy egyszerű, lépésről-lépésre forgatókönyv, hogy megteremtse az első automatikus skálázási beállítást.

1. Nyissa meg az **Automatikus skálázás** panelt az Azure Monitorban, és válasszon ki egy erőforrást, amelyet fel szeretne skálázni. (A következő lépések egy webalkalmazáshoz társított App Service-csomag használatával. Az [első ASP.NET webalkalmazást 5 perc alatt létrehozhatja az Azure-ban.][4])
1. Vegye figyelembe, hogy az aktuális példányszám 1. Kattintson **az Automatikus skálázás engedélyezése gombra.**
  ![Új webalkalmazás méretezési beállítása][5]
1. Adja meg a méretezési beállítás nevét, majd kattintson **a Szabály hozzáadása**gombra. Figyelje meg a méretezési szabály beállításait, amelyek a jobb oldalon környezetablaktáblaként nyílnak meg. Alapértelmezés szerint ez beállítja a lehetőséget, hogy a példányok számát 1-gyel méretezze, ha az erőforrás CPU-százaléka meghaladja a 70 százalékot. Hagyja az alapértelmezett értékeken, és kattintson a **Hozzáadás**gombra.
  ![Méretezési beállítás létrehozása webalkalmazáshoz][6]
1. Most létrehozta az első léptékszabályt. Vegye figyelembe, hogy a felhasználói felület ajánlott eljárásokat javasol, és kimondja, hogy "Ajánlott legalább egy skálát szabályban tartani." Ehhez tegye a következőket:

    a. Kattintson a **Szabály hozzáadása** elemre.

    b. Állítsa **az Operátor** t **kevesebb, mint**.

    c. Állítsa **a küszöbértéket** **20-ra.**

    d. Állítsa a **Művelet beállítását** **a Szám csökkentése értékre.**

   Most már rendelkeznie kell egy méretezési beállítással, amely a CPU-használat alapján horizontális felskálázódik/skálázódik.
   ![Méretezés a CPU alapján][8]
1. Kattintson a **Mentés** gombra.

Gratulálunk! Most már sikeresen létrehozta az első méretezési beállítást a webalkalmazás cpu-használat alapján imaspióautomatikus méretezéséhez.

> [!NOTE]
> Ugyanezek a lépések a virtuálisgép-méretezési csoport vagy a felhőalapú szolgáltatás szerepkör első lépésekre alkalmazhatók.

## <a name="other-considerations"></a>Egyéb szempontok
### <a name="scale-based-on-a-schedule"></a>Méretezés ütemezés alapján
A cpu-n alapuló méretezés mellett a méretezést a hét adott napjaihoz másképp is beállíthatja.

1. Kattintson **a Léptékfeltétel hozzáadása gombra.**
1. A méretezési mód és a szabályok beállítása megegyezik az alapértelmezett feltételével.
1. Válassza **a Meghatározott napok ismétlése** lehetőséget az ütemezéshez.
1. Válassza ki a nap és a kezdési/befejezési időpont, amikor a lépték feltételt kell alkalmazni.

![Állapot méretezése ütemezés alapján][9]
### <a name="scale-differently-on-specific-dates"></a>Méretezés eltérően adott időpontokban
A processzoron alapuló méretezés mellett a skálát másként is beállíthatja adott dátumokhoz.

1. Kattintson **a Léptékfeltétel hozzáadása gombra.**
1. A méretezési mód és a szabályok beállítása megegyezik az alapértelmezett feltételével.
1. Válassza **a Kezdési/befejezési dátumok megadása** lehetőséget az ütemezéshez.
1. Válassza ki a kezdési/befejezési dátumokat és a méretezési feltétel alkalmazásának kezdő/záró időpontját.

![Feltétel méretezése dátumok alapján][10]

### <a name="view-the-scale-history-of-your-resource"></a>Az erőforrás méretezési előzményeinek megtekintése
Az erőforrás fel- vagy leskálázásakor egy esemény kerül a tevékenységnaplóba. Az erőforrás méretezési előzményeit az elmúlt 24 órában megtekintheti, ha átvált a **Futtatási előzmények** lapra.

![Előzmények][11]

Ha meg szeretné tekinteni a teljes skálaelőzményeket (legfeljebb 90 napig), válassza a Kattintson ide lehetőséget **a további részletek megtekintéséhez.** Megnyílik a tevékenységnapló, és az automatikus skálázás elő van kiválasztva az erőforráshoz és a kategóriához.

### <a name="view-the-scale-definition-of-your-resource"></a>Az erőforrás méretezési definíciójának megtekintése
Az automatikus skálázás egy Azure Resource Manager-erőforrás. A Méretezésdefiníció a JSON-ban a **JSON** lapra való váltással tekinthető meg.

![Méretezés definíciója][12]

Szükség esetén a JSON-ban is módosíthatja a módosításokat. Ezek a módosítások a mentés után is megjelennek.

### <a name="disable-autoscale-and-manually-scale-your-instances"></a>Az automatikus skálázás letiltása és a példányok manuális méretezése
Előfordulhat, hogy le szeretné tiltani az aktuális méretezési beállítást, és manuálisan szeretné méretezni az erőforrást.

Kattintson az **automatikus skálázás letiltása** gombra a tetején.
![Automatikus skálázás letiltása][13]

> [!NOTE]
> Ez a beállítás letiltja a konfigurációt. Az automatikus skálázás ismételt engedélyezése után azonban visszatérhet hozzá.

Most már beállíthatja, hogy hány példányt szeretne manuálisan méretezni.

![Kézi lépték beállítása][14]

Az automatikus skálázáshoz bármikor visszatérhet az **Automatikus skálázás engedélyezése,** majd a **Mentés gombra**kattintva.

## <a name="next-steps"></a>További lépések
- [Tevékenységnapló-riasztás létrehozása az előfizetés összes automatikus skálázási motor-műveletének figyeléséhez](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Tevékenységnapló-riasztás létrehozása az előfizetés összes sikertelen automatikus skálázási horizontális felskálázási/horizontális felskálázási műveletének figyeléséhez](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

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

