---
title: App Service-csomagok áttekintése – Azure |} A Microsoft Docs
description: Ismerje meg, hogyan App Service-csomagok az Azure App Service munka, és hogyan azokat a kezelést.
keywords: App Service-ben, az azure app service, méret, méretezhető, méretezhetőség, az app service-csomag, az app service ára
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: dea3f41e-cf35-481b-a6bc-33d7fc9d01b1
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: ab04d1288eb3a851774128b8aaaae03868c2ffa7
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53731366"
---
# <a name="azure-app-service-plan-overview"></a>Az Azure App Service-csomag – áttekintés

Az App Service-ben az alkalmazások _App Service-csomagban_ futnak. Az App Service-csomagok határozzák meg a futtatni kívánt webalkalmazások számítási erőforrásait. Ezek a számítási erőforrások csatlakoztatja a [ _kiszolgálófarm_ ](https://wikipedia.org/wiki/Server_farm) a hagyományos webes üzemeltetés. Egy vagy több alkalmazást beállítható úgy, hogy az azonos számítási erőforrások (vagy az App Service-csomag).

App Service-csomag létrehozásakor egy adott régióban (például Nyugat-Európa) egy készletet a számítási erőforrások létrejön a csomagot az adott régióban. Bármilyen alkalmazások ezek futó App Service-csomag mindössze a számítási erőforrásokat határozzák meg az App Service-csomag. Minden App Service-csomag határozza meg:

- Régió (USA nyugati RÉGIÓJA, USA keleti RÉGIÓJA stb.)
- Virtuálisgép-példányok száma
- Virtuálisgép-példányok (kicsi, közepes, nagy) mérete
- Tarifacsomag (ingyenes, közös, alapszintű, Standard, prémium szintű, PremiumV2, Isolated, használat)

A _tarifacsomag_ App Service-csomag határozza meg, milyen kap az App Service-szolgáltatások és az IP-címek fenntartási kell fizetnie a csomag számára. Árképzési szintek néhány kategóriába sorolhatók:

- **A megosztott számítási**: **Ingyenes** és **megosztott**, a két kiinduló rétegek, az azonos Azure virtuális gépen, mint más App Service-alkalmazások, beleértve a más ügyfelek alkalmazások alkalmazást futtat. Ezek a rétegek foglal le Processzor kvóták minden alkalmazáshoz, amely a megosztott erőforrások futtat, és az erőforrások nem skálázhatja fel horizontálisan.
- **Dedikált számítási**: A **alapszintű**, **Standard**, **prémium**, és **PremiumV2** rétegek alkalmazások futtatása a dedikált Azure-beli virtuális gépeken. Csak azonos App Service-csomag az alkalmazások azonos számítási erőforrásokon osztozik. A magasabb szintű további Virtuálisgép-példányok lesznek elérhetők a horizontális felskálázást.
- **Elkülönített**: Ezen a szinten dedikált Azure-beli virtuális gépek dedikált Azure virtuális hálózatok, hálózati elkülönítési számítási elkülönítés használatával az alkalmazások felül biztosító futtatja. Biztosítja a maximális horizontális felskálázhatóságát.
- **Felhasználás**: Ez a szint csak érhető el az [alkalmazások függvény](../azure-functions/functions-overview.md). Méretezi a számítási feladatok függően dinamikusan a függvényeket. További információkért lásd: [Azure Functions szolgáltatási csomagok összehasonlítása](../azure-functions/functions-scale.md).

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Minden egyes réteghez is biztosít az App Service-szolgáltatások egy adott részéhez. Ezek a funkciók közé tartozik az egyéni tartományok és SSL-tanúsítványok, az automatikus skálázási, üzembe helyezési pontok, biztonsági másolatok, a Traffic Manager integrálása és egyéb. Minél nagyobb a szint, a további funkciók érhetők el. Minden egyes tarifacsomagja támogatott szolgáltatásokat, lásd: [App Service-csomag részletei](https://azure.microsoft.com/pricing/details/app-service/plans/).

<a name="new-pricing-tier-premiumv2"></a>

> [!NOTE]
> Az új **PremiumV2** tarifacsomag biztosít [Dv2 sorozatú virtuális gépek](../virtual-machines/windows/sizes-general.md#dv2-series) gyorsabb processzorokkal, SSD-tárolóval és képest kétszeres memória-mag aránnyal rendelkeznek **Standard** szint. **PremiumV2** megnövelt példányszámmal nagyobb méreteket is támogat, miközben továbbra is biztosít az a Standard csomagban megtalálható fejlett képességet. A meglévő összes funkcióját **prémium** szint szerepelnek **PremiumV2**.
>
> Hasonló más dedikált szintekre, három Virtuálisgép-méret érhető el ezen a szinten:
>
> - Kicsi (egy CPU mag, 3,5 GB memória) 
> - Közepes (két processzormag, 7 GB memória) 
> - Nagy (négy processzormag, 14 GB memória)  
>
> A **PremiumV2** díjszabási információkért lásd: [App Service díjszabását](https://azure.microsoft.com/pricing/details/app-service/).
>
> Első lépések az új **PremiumV2** tarifacsomag, lásd: [az App Service konfigurálása PremiumV2 szintjének](app-service-configure-premium-tier.md).

## <a name="how-does-my-app-run-and-scale"></a>Hogyan nem az alkalmazás futtatásához és méretezéséhez?

Az a **ingyenes** és **megosztott** rétegek, egy alkalmazás CPU perc megosztott VM-példány megkapja, és nem skálázhatja fel horizontálisan. A többi szint egy alkalmazás fut, és méretezhető módon.

Egy alkalmazás az App Service-ben való létrehozásakor, a rendszer elhelyezi egy App Service-csomagot. Az alkalmazás futtatásakor az App Service-csomag konfigurált összes Virtuálisgép-példányt futtat. Ha több alkalmazás az App Service-csomag, az összes oszthatnak meg az ugyanazon Virtuálisgép-példányok. Ha egy alkalmazás több telepítési ponttal rendelkezik, az összes üzembe helyezési pontok is futtathatja az ugyanazon Virtuálisgép-példányokon. Engedélyezze a diagnosztikát, biztonsági mentéshez, vagy webjobs-feladatok futtatásához, akkor is használni CPU-ciklusokat és a memória a Virtuálisgép-példány.

Ezzel a módszerrel az App Service-csomag az App Service-alkalmazások, a skálázási egység. Ha a csomag öt Virtuálisgép-példányok futtatásra van konfigurálva, a tervben szereplő összes alkalmazás az összes öt példányban futtatunk. Ha a csomagot az automatikus skálázáshoz, van konfigurálva, majd a csomag összes alkalmazások horizontálisan felskálázott együtt automatikus skálázási beállítások alapján.

Egy méretezéssel kapcsolatos információkért lásd: [példányszám manuális vagy automatikus méretezése](../monitoring-and-diagnostics/insights-how-to-scale.md).

<a name="cost"></a>

## <a name="how-much-does-my-app-service-plan-cost"></a>Az App Service-csomag mennyibe?

Ez a szakasz ismerteti, hogyan App Service-alkalmazások számlázzuk. Részletes, régióspecifikus díjszabási információkért lásd: [App Service díjszabását](https://azure.microsoft.com/pricing/details/app-service/).

Az alábbiakat kivéve **ingyenes** szint, az App Service-csomag folytat óradíjat számítunk fel a számítási erőforrásokat használ.

- Az a **megosztott** szint, minden egyes alkalmazás-kvótát CPU perc, tehát kap _minden alkalmazás_ óránként fizet a CPU-kvótát.
- Dedikált számítási szintek (**alapszintű**, **Standard**, **prémium**, **PremiumV2**), az App Service-csomag határozza meg a virtuális gépek száma példányok, az alkalmazásokat úgy vannak méretezve, hogy, így _egyes Virtuálisgép-példányok_ az App Service-ben tervnek óradíjat számítunk fel. Virtuálisgép-példány számlázzuk, függetlenül attól, hogy hány alkalmazások futtatja őket. Váratlan költségek elkerülése érdekében tekintse meg a [App Service-csomag törlése](app-service-plan-manage.md#delete).
- Az a **elkülönített** szint, az App Service Environment számát határozza meg az alkalmazásokat futtató elkülönített feldolgozók és _minden egyes feldolgozóhoz_ óradíjat kell fizetnie. Emellett nincs a futtatásához alap óradíj az App Service-környezet magát. 
- (Csak az azure Functions) A **fogyasztás** szint dinamikusan foglalja le a Virtuálisgép-példányok egy függvényalkalmazás számítási szolgáltatás, és díját dinamikusan másodpercenként az Azure-ban. További információkért lásd: [Azure Functions árképzése](https://azure.microsoft.com/pricing/details/functions/).

Akkor nem kell fizetnie az App Service-szolgáltatások, amelyek elérhetők a (konfigurálása egyéni tartományokat, SSL-tanúsítványok, az üzembe helyezési pontok, biztonsági másolatok stb.) használatával. A kivételek a következők:

- App Service-tartományok – meg kell fizetnie, amikor vásárol egy Azure-os, és ha meg kell újítani minden évben.
- App Service-tanúsítványok – meg kell fizetnie, amikor vásárol egy Azure-os, és ha meg kell újítani minden évben.
- IP-alapú SSL-kapcsolatok – Itt a óradíjat számítunk fel az egyes IP-alapú SSL-kapcsolatot, de néhány **Standard** díjcsomag vagy újabb egy IP-alapú SSL kapcsolatot teremt az ingyenes. Az SNI-alapú SSL-kapcsolatok használata ingyenes.

> [!NOTE]
> Ha az App Service integrálható egy másik Azure-szolgáltatás, szükség lehet figyelembe venni ezeket a szolgáltatásokat a költségek. Például ha használja az Azure Traffic Manager skálázhatja a földrajzi hely, az Azure Traffic Manager is díjak, a használat alapján. Díjbecslés cross-szolgáltatások az Azure-ban, tekintse meg [díjkalkulátor](https://azure.microsoft.com/pricing/calculator/). 
>
>

## <a name="what-if-my-app-needs-more-capabilities-or-features"></a>Mi történik, ha az alkalmazás van szüksége, további funkciókat vagy szolgáltatásokat?

Az App Service-csomag felfelé és lefelé skálázhatók bármikor. Használata éppolyan egyszerű, mint a terv a tarifacsomag módosítása. Először egy kisebb tarifacsomag kiválasztása, és méretezhetően később további App Service-szolgáltatások.

A webalkalmazás a tesztelés megkezdése például egy **ingyenes** App Service tervezése és semmit sem kell fizetnie. Ha hozzá szeretne a [egyéni DNS-név](app-service-web-tutorial-custom-domain.md) paranccsal a webalkalmazást, csak a méretezési csoport a csomag akár **megosztott** szint. Később, ha szeretne hozzáadni egy [egyéni SSL-tanúsítvány](app-service-web-tutorial-custom-ssl.md), a csomag akár méretezhető **alapszintű** szint. Ha szeretne rendelkezik [átmeneti környezet](deploy-staging-slots.md), fel **Standard** szint. Ha további magok, memória, vagy a storage van szüksége, vertikális felskálázás a nagyobb méretű virtuális gép méretét az azonos szinten.

A fordított azonos működik. Ha úgy gondolja, hogy Ön már nem kell az a funkciók vagy magasabb szintű szolgáltatásai, alacsonyabb tarifacsomagra, pénzt takaríthat meg, amelyek vertikális.

Az App Service-csomag vertikális felskálázásával információkért lásd: [az Azure-beli alkalmazás vertikális felskálázása](web-sites-scale.md).

Ha az alkalmazás más alkalmazásokkal azonos App Service-csomag, érdemes elkülöníti a számítási erőforrásokat az alkalmazás teljesítményének javítása érdekében. Ez az alkalmazás megéri egy külön App Service-csomag úgy teheti meg. További információkért lásd: [alkalmazás áthelyezése egy másik App Service-csomag](app-service-plan-manage.md#move).

## <a name="should-i-put-an-app-in-a-new-plan-or-an-existing-plan"></a>Érdemes egy alkalmazást is helyezni az új csomagot, vagy egy meglévő csomagot?

Mivel után kell fizetni, a számítási erőforrásokat az App Service-csomag foglalja le (lásd: [mennyibe saját App Service-csomag szolgáltatás?](#cost)), potenciálisan pénzt takaríthat meg több alkalmazás üzembe helyezése egy App Service-csomag szerint. Alkalmazások hozzáadása egy meglévő csomagot, mindaddig, amíg a terv a terhelés kezeléséhez elegendő erőforrással rendelkezik továbbra is. Ugyanakkor vegye figyelembe, hogy az alkalmazások megosztása az összes azonos azonos App Service-csomag a számítási erőforrásokat. Annak megállapításához, hogy rendelkezik-e a szükséges erőforrásokat az új alkalmazást, szüksége kapacitását, a meglévő App Service-csomagot, és a várható terhelést az új alkalmazás megismeréséhez. App Service-csomag terhelve teljesítményproblémákat okozhat, állásidő a meglévő és új alkalmazások.

Tervezi az alkalmazás egy új App Service-be, mikor elkülönítése:

- Az alkalmazás az erőforrás-igényes.
- Szeretné az alkalmazást, az egyéb alkalmazásokból egymástól függetlenül meglévő csomag skálázására.
- Az alkalmazás-erőforrást egy másik földrajzi régióban kell.

Ezzel a módszerrel is foglaljon le egy új erőforráskészlettel az alkalmazás és az alkalmazások nagyobb irányítást.

## <a name="manage-an-app-service-plan"></a>App Service-csomag kezelése

> [!div class="nextstepaction"]
> [App Service-csomag kezelése](app-service-plan-manage.md)
