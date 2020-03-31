---
title: Gyakori kódcsomag-hibák diagnosztizálása a Service Fabric használatával
description: Ismerje meg, hogyan háríthatók el a gyakori kódcsomag-hibák az Azure Service Fabric szolgáltatással
author: grzuber
ms.topic: article
ms.date: 05/09/2019
ms.author: grzuber
ms.openlocfilehash: 344fef70522240da2236a020c96308c472c9c545
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75463114"
---
# <a name="diagnose-common-code-package-errors-by-using-service-fabric"></a>Gyakori kódcsomag-hibák diagnosztizálása a Service Fabric használatával

Ez a cikk azt ismerteti, hogy mit jelent egy kódcsomag váratlanul leállítani. Betekintést nyújt a gyakori hibakódok lehetséges okaiba, valamint hibaelhárítási lépéseket.

## <a name="when-does-a-process-or-container-terminate-unexpectedly"></a>Mikor fejeződik be váratlanul egy folyamat vagy tároló?

Amikor az Azure Service Fabric kap egy kérést egy kódcsomag elindítására, megkezdi a környezet előkészítését a helyi rendszeren az alkalmazás és a szolgáltatás jegyzékfájljaiban beállított beállításoknak megfelelően. Ezek az előkészületek magukban foglalhatják a hálózati végpontok vagy erőforrások lefoglalását, a tűzfalszabályok konfigurálását vagy az erőforrás-irányítási megkötések beállítását. 

A környezet megfelelő konfigurálása után a Service Fabric megpróbálja előhozni a kódcsomagot. Ez a lépés akkor tekinthető sikeresnek, ha az operációs rendszer vagy a tároló futásidejű jelenti, hogy a folyamat vagy a tároló sikeresen aktiválva lett. Ha az aktiválás sikertelen, az SFX-ben az alábbihoz hasonló állapotjelző üzenet jelenik meg:

```
There was an error during CodePackage activation. Service host failed to activate. Error: 0xXXXXXXXX
```

A kódcsomag sikeres aktiválása után a Service Fabric megkezdi a szolgáltatás élettartamának figyelését. Ezen a ponton egy folyamat vagy tároló bármikor leszakítható több okból is. Előfordulhat például, hogy nem sikerült inicializálnia egy DLL-t, vagy az operációs rendszer ből elfogyott az asztal halomterület. Ha a kódcsomag levan állítva, az SFX-ben a következő állapotüzenetnek kell megjelennie:

```
The process/container terminated with exit code: XXXXXXXX. Please look at your application logs/dump or debug your code package for more details. For information about common termination errors, please visit https://aka.ms/service-fabric-termination-errors
```

A kilépési kódot ebben az állapotüzenetben az egyetlen nyom, hogy a folyamat vagy tároló biztosítja, hogy miért végződött. Ez lehet generálni bármilyen szinten a verem. Lehet például, hogy ez a kilépési kód egy operációs rendszer hibájához vagy egy .NET-problémához kapcsolódik, vagy a kód vetette fel. Használja ezt a cikket kiindulási pontként a megszüntetési kilépési kódok és a lehetséges megoldások forrásának diagnosztizálásához. Ne feledje azonban, hogy ezek általános megoldások a gyakori forgatókönyvekre, és előfordulhat, hogy nem vonatkoznak a megjelenő hibára.

## <a name="how-can-i-tell-if-service-fabric-terminated-my-code-package"></a>Hogyan állapítható meg, hogy a Service Fabric megszüntette a kódcsomagot?

A Service Fabric több okból is felelős lehet a kódcsomag megszüntetéséért. Például dönthet úgy, hogy a kódcsomagot egy másik csomóponton helyezi el terheléselosztási célokra. Ellenőrizheti, hogy a Service Fabric megszüntette-e a kódcsomagot, ha az alábbi táblázatban a kilépési kódok bármelyikét látja.

>[!NOTE]
> Ha a folyamat vagy a tároló az alábbi táblázatban szereplő kódoktól eltérő kilépési kóddal fejeződik be, a Service Fabric nem felelős annak megszüntetéséért.

Kilépési kód | Leírás
--------- | -----------
7147 | Azt jelzi, hogy a Service Fabric szabályosan állítsa le a folyamatot vagy a tárolót egy Ctrl+C jel elküldésével.
7148 | Azt jelzi, hogy a Service Fabric leállította a folyamatot vagy a tárolót. Néha ez a hibakód azt jelzi, hogy a folyamat vagy a tároló nem válaszolt időben a Ctrl+C jel elküldése után, és meg kellett szüntetni.


## <a name="other-common-error-codes-and-their-potential-fixes"></a>Egyéb gyakori hibakódok és azok lehetséges javításai

Kilépési kód | Hexadecimális érték | Rövid leírás | Gyökérok | Lehetséges javítás
--------- | --------- | ----------------- | ---------- | -------------
3221225794 | 0xc0000142 | STATUS_DLL_INIT_FAILED | Ez a hiba néha azt jelenti, hogy a gép ből kifogyott az asztal halomterület. Ez az ok különösen akkor valószínű, ha számos folyamat tartozik az alkalmazás hoz futó a csomóponton. | Ha a program nem úgy lett kialakítva, hogy reagáljon a Ctrl+C jelekre, engedélyezheti az **EnableActivateNoWindow** beállítást a fürtjegyzékben. A beállítás engedélyezése azt jelenti, hogy a kódcsomag gui ablak nélkül fog futni, és nem fogadja a Ctrl+C jeleket. Ez a művelet csökkenti az egyes folyamatok által felhasznált asztali halomterület nagy részét is. Ha a kódcsomagnak Szüksége van a Ctrl+C jelek fogadására, növelheti a csomópont asztali halomméretét.
3762504530 | 0xe0434352 | N/A | Ez az érték a felügyelt kód (azaz a .NET) nem kezelt kivételének hibakódját jelöli. | Ez a kilépési kód azt jelzi, hogy az alkalmazás kivételt emelt, amely kezeletlen marad, és amely leállította a folyamatot. Az első lépés annak meghatározásában, hogy mi váltotta ki ezt a hibát, hibakeresés az alkalmazás naplók és dump fájlokat.

## <a name="next-steps"></a>További lépések

* További információ [az egyéb gyakori forgatókönyvek diagnosztizálásáról.](service-fabric-diagnostics-common-scenarios.md)
* Az Azure Monitor naplóinak részletesebb áttekintését és kínálatát az [Azure Monitor áttekintésének áttekintésével tekintheti meg.](../operations-management-suite/operations-management-suite-overview.md)
* További információ az Azure [alerting](../log-analytics/log-analytics-alerts.md) Monitor naplóiról, amelyek segítséget kapnak az észleléshez és a diagnosztikához.
* Ismerkedjen meg az Azure Monitor naplóinak részeként kínált [naplókeresési és lekérdezési](../log-analytics/log-analytics-log-searches.md) funkciókkal.
