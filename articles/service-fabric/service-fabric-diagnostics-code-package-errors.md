---
title: Az Azure Service Fabric code csomag hibák diagnosztizálása |} A Microsoft Docs
description: Ismerje meg, hogyan gyakran kód csomag előforduló hibák elhárítása az Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: grzuber
manager: gkhanna
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/09/2019
ms.author: grzuber
ms.openlocfilehash: 235952388d2c044cc141b3020c67944c4250ea3d
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276950"
---
# <a name="diagnose-common-code-package-errors-with-service-fabric"></a>A Service Fabric közös kód csomag hibák diagnosztizálása

Ez a cikk bemutatja, hogy mit jelent egy kódcsomaghoz váratlanul leáll, és lehetséges okait és a gyakori hibakódok, valamint a hibaelhárítási lépések, amelyek vélhetően lehet elhárítani a problémát betekintést nyújt.

## <a name="when-does-a-process-or-container-terminate-unexpectedly"></a>Ha nem a folyamat vagy a tároló váratlanul leáll?

A helyi rendszer állítsa be az alkalmazás és szolgáltatás jegyzékfájljainak konfigurációs beállítások alapján a környezet előkészítése a Service Fabric egy kódcsomaghoz start kérést kap, kezdődik. Ezek az előkészített tartalmazhat hálózati végpont vagy az erőforrások lefoglalását, a tűzfalszabályok konfigurálása vagy cégirányítási korlátozott erőforrások beállítása. Ha a környezet megfelelően van konfigurálva, a Service Fabric próbál viszi, megjelenik a kódcsomag. Ebben a lépésben számít sikeres, ha az operációs rendszer vagy a tároló-futtatókörnyezettel jelent, hogy a folyamat vagy a tároló sikerült aktiválni. Ha az aktiválás sikertelen volt, üzenetnek kell megjelennie a health az SFX arról, hogy a

```
There was an error during CodePackage activation. Service host failed to activate. Error: 0xXXXXXXXX
```

A kódcsomag sikeresen megtörtént a rendszer visszaállította, miután a Service Fabric élettartamuk figyelési kezdődik. Ezen a ponton egy folyamatban vagy tárolóban leállíthatja számos oka bármikor. Sikerült rendelkezik nem sikerült inicializálni a DLL-t, az operációs rendszer sikerült elfogyott asztali halommemória lemezterület stb. Ha a kódcsomag, üzenetnek kell megjelennie a health az SFX arról, hogy a

```
The process/container terminated with exit code: XXXXXXXX. Please look at your application logs/dump or debug your code package for more details. For information about common termination errors, please visit https://aka.ms/service-fabric-termination-errors
```

A kilépési kód megadva ez az állapot üzenet a parancs számára, miért szüntetni folyamat/tároló által biztosított csak valami, és azt sikerült által létrehozott bármely a verem szintjét. Célszerű a nehezen érthető, ha a kilépési kód volt kapcsolatos, például az operációs rendszer hiba, a .NET, a probléma, vagy a kód okozta. Ennek eredményeképpen Ez a cikk kiindulási pontként használható diagnosztizálásához lezárást kilépési kódot forrását, és lehetséges megoldásokat, figyelembe véve, hogy ezek általános megoldások gyakori forgatókönyveket, és nem alkalmazható a hiba akkor jelent meg.

## <a name="how-can-i-tell-if-service-fabric-terminated-my-code-package"></a>Hogyan állapítható meg, ha a Service Fabric leállt a kódcsomag?

Lehet, hogy a Service Fabric felelős a kódcsomag különböző okok miatt leáll. Például dönthet a kódcsomag elhelyezése egy másik csomópontra terheléselosztás céljából. Megadható, hogy ha a kódcsomag leállt a Service Fabric által, ha a kilépési kódot az alábbi táblázatban látható:

>[!NOTE]
> Ha a folyamat/tároló leáll, megjelennek az alábbi táblázatban eltérő kilépési kóddal, a Service Fabric, nem felelős leállítja azt.

Kilépési kód | Leírás
--------- | -----------
7147 | Az ezen hibakódok azt jelzik, hogy a Service Fabric le szabályosan a folyamat/tároló Ctrl + C jel küldésével.
7148 | Az ezen hibakódok azt jelzik, hogy a Service Fabric a folyamat/tároló megszakadt. Egyes esetekben erről a hibakódról azt jelzi, hogy a folyamat/tároló nem válaszolt időben küldése a Ctrl + C jelet, így le fognak állni kellett után.


## <a name="other-common-error-codes-and-their-potential-fixes"></a>Egyéb gyakori hibakódok és azok lehetséges javításai

Kilépési kód | Hexadecimális értékként | Rövid leírás | Kiváltó ok | Lehetséges javítás
--------- | --------- | ----------------- | ---------- | -------------
3221225794 | 0xc0000142 | STATUS_DLL_INIT_FAILED | Ez a hiba esetleg jelenti, hogy a gép futott-e elegendő halommemória asztali áll. Ez különösen akkor valószínűséggel, ha az a csomóponton futó alkalmazáshoz tartozó folyamatokat nagy számú oka. | Ha a program nem lett építve válaszolni a Ctrl + C jelek, a fürt Manifest beállítás a "EnableActivateNoWindow" engedélyezheti. A beállítás engedélyezése azt a kódcsomag kellene futtatnia egy grafikus felhasználói felület ablak nélkül és nem jelenít meg Ctrl + C jelek, de csökkentené a használ az egyes folyamatok asztali halommemória terület mennyiségét. Ha a kódcsomag kell fogadni a Ctrl + C jelek, majd, növelheti a csomópont asztali halommemória mérete.
3762504530 | 0xe0434352 | – | Ez az érték a felügyelt kódból (például .NET) nem kezelt kivétel hibakód. | A kilépési kód jelent meg, azt jelenti, hogy az alkalmazás kivételt, amely nem kezelt maradt, és a folyamat leállt. Az alkalmazás naplók és a memóriaképek hibakeresés kell lennie az első lépés, amely meghatározza, hogy mi okozta a hibát.

## <a name="next-steps"></a>További lépések

* Tudjon meg többet [más gyakori feladatok diagnosztizálása](service-fabric-diagnostics-common-scenarios.md)
* Az Azure Monitor naplóira, és mit kínál olvassa el részletes áttekintést [Mi az Azure Monitor naplóira?](../operations-management-suite/operations-management-suite-overview.md)
* További információ az Azure Monitor naplóira [riasztási](../log-analytics/log-analytics-alerts.md) , ezzel elősegítve az észlelési és a diagnosztikát.
* Ismerkedjen meg a [naplókeresési és lekérdezési](../log-analytics/log-analytics-log-searches.md) szolgáltatásai által kínált Azure Monitor naplóira
