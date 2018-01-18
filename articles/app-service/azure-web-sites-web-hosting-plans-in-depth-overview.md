---
title: "Az Azure App Service-csomag áttekintése |} Microsoft Docs"
description: "Ismerje meg, hogyan App Service-csomagok Azure App Service munka, és hogyan azok előnyeit, a kezelhetőséget."
keywords: "az App service, az azure app service, a méretezési, méretezhető, méretezhetőség, az app service-csomag, az app service költség"
services: app-service
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.assetid: dea3f41e-cf35-481b-a6bc-33d7fc9d01b1
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: cephalin
ms.openlocfilehash: 268844eae8dc06937529e79d52515cad2f6da3f4
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2018
---
# <a name="azure-app-service-plan-overview"></a>Az Azure App Service-csomag áttekintése

Az App Service-ben egy alkalmazást futtat egy _App Service-csomag_. Az App Service-csomag meghatároz egy számítási erőforrásokat egy webalkalmazás futtatásához. Ezek a számítási erőforrások megfelel a rendszer a [ _kiszolgálófarm_ ](https://wikipedia.org/wiki/Server_farm) a hagyományos webtároláshoz. Egy vagy több alkalmazás beállítható úgy, hogy az azonos számítási erőforrások (vagy ugyanazon App Service-csomag).

Az App Service-csomag létrehozásakor egy bizonyos régióban (például Nyugat-Európában) számítási erőforrások olyan készletét, hogy az adott régióban terv jön létre. Bármilyen alkalmazás, kerüljenek az App Service-csomag futtatása ezekkel a számítási erőforrásokat, az App Service-csomag által definiált konfigurációjának kialakításához. Minden egyes App Service-csomag határozza meg:

- A régióban (USA nyugati régiója, USA keleti régiója, stb.)
- Virtuálisgép-példányok száma
- Virtuálisgép-példányok (Small, Medium, Large) mérete
- Tarifacsomag (szabad, megosztott, Basic, Standard, Premium, PremiumV2, elszigetelt, felhasználása)

A _tarifacsomag_ egy App Service csomag határozza meg, milyen App Service-szolgáltatások elérhetővé, és hogy mennyit kell fizetnie a terv. Tarifacsomagok néhány kategóriába sorolhatók:

- **Megosztott számítási**: **szabad** és **megosztott**, a két kiinduló rétegek, az azonos Azure virtuális gépen, mint más App Service-alkalmazás, illetve más ügyfelek alkalmazások egy alkalmazást futtat. Ezek a rétegek foglal le Processzor kvóták minden alkalmazásra, amely a megosztott erőforrások fut, és az erőforrások kiterjesztése nem.
- **Dedikált számítási**: A **alapvető**, **szabványos**, **prémium**, és **PremiumV2** rétegek alkalmazásokat futtatnak dedikált Azure Virtuális gépek. App Service-csomag csak alkalmazások közös számítási erőforrásokat. Minél nagyobb a réteg a kibővített rendelkezésére álljanak a további Virtuálisgép-példányok.
- **Elkülönített**: Ebben a rétegben dedikált Azure virtuális gépeken futó dedikált Azure virtuális hálózatot, amely felett az alkalmazásokhoz számítási elkülönítési hálózati elkülönítést is biztosít. A maximális kibővített képességeket biztosít.
- **Felhasználás**: Ebben a rétegben csak rendelkezésére áll [működnek az alkalmazások](../azure-functions/functions-overview.md). A funkciók dinamikusan attól függően, hogy munkaterhelés méretezés. További információkért lásd: [összehasonlítása az Azure Functions üzemeltetési tervek](../azure-functions/functions-scale.md).

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Minden egyes réteg is biztosít az App Service-szolgáltatások egy adott részhalmazát. A funkciók közé tartoznak az egyéni tartományok és SSL-tanúsítványok automatikus skálázást, üzembe helyezési, biztonsági mentések, Traffic Manager-integráció és több. Minél nagyobb a réteg a további funkciók érhetők el. Mely szolgáltatásokat támogatja az egyes tarifacsomagok regisztrációval, lásd: [App Service-csomag részletei](https://azure.microsoft.com/pricing/details/app-service/plans/).

<a name="new-pricing-tier-premiumv2"></a>

> [!NOTE]
> Az új **PremiumV2** biztosít IP-címek [Dv2 sorozatú virtuális gépek](../virtual-machines/windows/sizes-general.md#dv2-series) gyorsabb processzorok, a SSD-re és a dupla memória-core arány képest **szabványos** réteg. **PremiumV2** is támogatja a nagyobb mértékű keresztül nagyobb a példányok száma így biztosít az összes speciális képességei a Standard csomag. A meglévő összes szolgáltatásához **prémium** réteg szerepelnek **PremiumV2**.
>
> Hasonló más dedikált szintekre, három Virtuálisgép-méretek érhetők el a réteg:
>
> - Kis (processzormag, 3.5-ös GiB memória) 
> - Közepes (két processzormag, 7 GiB memória) 
> - Nagy (négy processzormag, 14 GiB memória)  
>
> A **PremiumV2** díjszabási információkért lásd: [App Service szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/app-service/).
>
> Az új használatába **PremiumV2** IP-címek, lásd: [az App Service konfigurálása PremiumV2 réteg](app-service-configure-premium-tier.md).

## <a name="how-does-my-app-run-and-scale"></a>Hogyan nem az alkalmazás futtatásához és méretezéséhez?

Az a **szabad** és **megosztott** rétegek, egy alkalmazás CPU perc, a megosztott Virtuálisgép-példány kap, és nem kiterjesztése. A más szintekre egy alkalmazás fut, és az alábbiak szerint arányosan.

Alkalmazás létrehozása az App Service-ben, ha azt kell helyezni az App Service-csomag. Az alkalmazás futtatásakor az App Service-csomag konfigurált összes Virtuálisgép-példány fut. Ha több alkalmazás az App Service-csomag, mindannyian az ugyanazon Virtuálisgép-példány megosztása. Ha egy alkalmazás több üzembe helyezési, minden üzembe helyezési tárhely is futtathatja az ugyanazon Virtuálisgép-példányok. Ha engedélyezi a diagnosztikai naplók, biztonsági mentést, vagy webjobs-feladatok futtatása, azok CPU-ciklusok a és is használható memória a Virtuálisgép-példányok.

Így az App Service-csomag a skálázási egység az App Service-alkalmazások. Ha a terv öt Virtuálisgép-példányok futtatásra van beállítva, a tervben szereplő összes alkalmazás futtassa a öt példányait. Ha a csomag az automatikus skálázást, van konfigurálva, akkor a tervben szereplő összes alkalmazás együtt is kiterjeszthető az automatikus skálázás beállításai alapján.

Információ az alkalmazások felskálázással: [méretezése példányszám manuális vagy automatikus](../monitoring-and-diagnostics/insights-how-to-scale.md).

<a name="cost"></a>

## <a name="how-much-does-my-app-service-plan-cost"></a>Milyen mértékű költségeket, az App Service-csomag nem?

Ez a szakasz ismerteti, hogyan vannak számlázva App Service-alkalmazásokhoz. Részletes, régióspecifikus díjszabási információkért lásd: [App Service szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/app-service/).

Kivéve az **szabad** réteg, az App Service-csomag folytat egy óránkénti kell fizetni a számítási erőforrásokat használ.

- Az a **megosztott** réteg, egyes alkalmazások így megkapja CPU perc, a kvóta _minden alkalmazás_ óránkénti feladata a CPU-kvótához.
- A dedikált a számítási rétegek (**alapvető**, **szabványos**, **prémium**, **PremiumV2**), az App Service-csomag határozza meg a virtuális gép száma példányok az alkalmazások is méretezhető, így _minden egyes Virtuálisgép-példány_ az App Service csomag rendelkezik egy óránkénti kell fizetni. Ezek a Virtuálisgép-példányok van szó, az függetlenül attól, hogy hány alkalmazásokat futtatja őket. Váratlan díjak elkerülése érdekében tekintse meg a [az App Service-csomag törlése](app-service-plan-manage.md#delete).
- Az a **elszigetelt** réteg, az App Service Environment-környezet az alkalmazásokat futtató elkülönített munkavállalók számát határozza meg és _minden worker_ óránkénti feladata. A futtatásához egy óránkénti – alapdíj nincs emellett az App Service Environment-környezet magát. 
- (Csak az azure Functions) A **fogyasztás** réteg dinamikusan osztja ki a Virtuálisgép-példány egy függvény app munkaterhelés kiszolgálásához, és a terheli dinamikusan másodpercenként az Azure-ban. További információkért lásd: [Azure Functions díjszabási](https://azure.microsoft.com/pricing/details/functions/).

Meg nem get szó, a használatával az App Service-szolgáltatások, amelyek elérhetők a (konfigurálása az egyéni tartományok, SSL-tanúsítványokat, üzembe helyezési, biztonsági másolatok stb.). A kivételek a következők:

- App Service tartományok - kell fizetnie során vásárol egy Azure-ban és amikor megújítja azt minden évben.
- App Service-tanúsítványokkal - kell fizetnie során vásárol egy Azure-ban és amikor megújítja azt minden évben.
- IP-alapú SSL-kapcsolatok - ott csomagazonosítóját az óránkénti kell fizetni az egyes IP-alapú SSL-kapcsolatot, de néhány **szabványos** szintjüket, vagy újabb lehetővé teszi egy IP-alapú SSL-kapcsolat szabad. Az SNI-alapú SSL-kapcsolatok szabadon.

> [!NOTE]
> Ha az App Service integrálható egy másik Azure szolgáltatás, szükség lehet figyelembe venni ezeket a szolgáltatásokat a költségek. Például ha Azure Traffic Manager méretezési földrajzi hely, az alkalmazás Azure Traffic Manager is költségek, használati mutatói alapján a. Az Azure-ban a kereszt-szolgáltatások költség becslése, lásd: [árazás Számológép](https://azure.microsoft.com/pricing/calculator/). 
>
>

## <a name="what-if-my-app-needs-more-capabilities-or-features"></a>Mi történik, ha saját alkalmazás van, további képességeket vagy a szolgáltatások?

Az App Service-csomag bármikor felfelé és lefelé méretezhetők. Használata éppolyan egyszerű, mint a terv árképzési szintjének módosítása. Válasszon egy alacsonyabb tarifacsomagot először, és növelheti később Ha szüksége több App Service szolgáltatást.

Például elindíthatja a webes alkalmazás tesztelése a **szabad** App Service tervezze meg, és semmi sem kell fizetnie. Ha hozzá szeretne adni a [egyéni DNS-név](app-service-web-tutorial-custom-domain.md) azt a webalkalmazást, csak a csomag skálázása legfeljebb **megosztott** réteg. Később, ha hozzá szeretne adni egy [egyéni SSL-tanúsítvány](app-service-web-tutorial-custom-ssl.md), akár csomag skálázása **alapvető** réteg. Ha azt szeretné, hogy [átmeneti környezet](web-sites-staged-publishing.md), akár méretezhető **szabványos** réteg. Ha több mag, a memória vagy a tárolást, van szüksége, vertikális felskálázás azonos tartozó nagyobb Virtuálisgép-méretet.

Ugyanez fordítva működik. Ha úgy érzi, hogy Ön már nem kell funkcióira vagy szolgáltatások egy magasabb réteg, méretezhet alacsonyabb réteghez, és menti a pénz.

Az App Service-csomag vertikális felskálázásával információkért lásd: [vertikális felskálázás az Azure alkalmazásban](web-sites-scale.md).

Ha az alkalmazás a azonos App Service-csomag más alkalmazásokkal, érdemes lehet az alkalmazás teljesítményének javításával ha elkülöníti a számítási erőforrásokat. Erre az alkalmazás áthelyezése egy külön App Service-csomagot. További információkért lásd: [az alkalmazások áthelyezése egy másik App Service-csomag](app-service-plan-manage.md#move).

## <a name="should-i-put-an-app-in-a-new-plan-or-an-existing-plan"></a>Érdemes egy alkalmazást is helyezni az egy új vagy egy meglévő tervet?

Lefoglalja mivel kell fizetnie a számítási erőforrások az App Service-csomaghoz (lásd: [mennyibe saját App Service-csomag költség?](#cost)), esetleg spórolhat a pénz több alkalmazás üzembe egy App Service-csomag. Továbbra is hozzáadhat alkalmazásokat egy meglévő csomaggal való mindaddig, amíg a terv van elegendő erőforrás a terhelés kezelésére. Vegye azonban figyelembe, hogy az összes fájlmegosztás azonos azonos App Service-csomag alkalmazások számítási erőforrásokat. Annak meghatározása, hogy az új alkalmazás rendelkezik-e a szükséges erőforrásokat, a kapacitás, a meglévő App Service-csomagot, és a várható terhelést az új alkalmazás tisztában kell. Az App Service-csomag túlterhelés is okozhat a meglévő és új alkalmazások állásidő.

Az app be egy új App Service-csomag mikor izolátumának:

- Az alkalmazás erőforrás-igényes.
- A meglévő csomag egymástól függetlenül a más alkalmazásokból származó alkalmazás skálázása szeretné.
- Az alkalmazás egy másik földrajzi régióban erőforrásra van szüksége.

Ezzel a módszerrel lehet új készletét erőforrásokat lefoglalni az alkalmazás és az alkalmazások nagyobb ellenőrzést.

## <a name="manage-an-app-service-plan"></a>Az App Service-csomag kezelése

> [!div class="nextstepaction"]
> [Az App Service-csomag kezelése](app-service-plan-manage.md)
