---
title: App Service-csomagok
description: Ismerje meg, hogyan működnek az App Service-csomagok az Azure App Service-ben, hogyan kell fizetni ük az ügyfélnek, és hogyan méretezheti őket az Igényeinek megfelelően.
keywords: app szolgáltatás, azure app service, méretezés, méretezhető, méretezhetőség, app service terv, app szolgáltatás költsége
ms.assetid: dea3f41e-cf35-481b-a6bc-33d7fc9d01b1
ms.topic: article
ms.date: 11/09/2017
ms.custom: seodec18
ms.openlocfilehash: f1012f8c00de4b19bbf6206408ec1a806e09e54f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77482343"
---
# <a name="azure-app-service-plan-overview"></a>Az Azure App Service-csomagok áttekintése

Az App Service-ben az alkalmazások _App Service-csomagban_ futnak. Az App Service-csomagok határozzák meg a futtatni kívánt webalkalmazások számítási erőforrásait. Ezek a számítási erőforrások hasonlóak a [_szerver farm_](https://wikipedia.org/wiki/Server_farm) a hagyományos web hosting. Egy vagy több alkalmazás konfigurálható úgy, hogy ugyanazon a számítási erőforráson (vagy ugyanabban az App Service-csomagban) fusson.

Amikor egy adott régióban (például Nyugat-Európában) létrehoz egy App Service-csomagot, az adott régióban az adott tervhez számítási erőforrások at hoz létre. Bármilyen alkalmazásokat is helyez el ebben az App Service-csomagban, az App Service-csomag által meghatározott számítási erőforrásokon fusson. Az App Service-csomagok a következőket határozzák meg:

- Régió (USA nyugati régiója, USA keleti régiója, stb.)
- Virtuálisgép-példányok száma
- Virtuálisgép-példányok mérete (kicsi, közepes, nagy)
- Tarifacsomag (ingyenes, megosztott, alapszintű, standard, prémium, premiumv2, elszigetelt)

Az App Service-csomag _tarifacsomagja_ határozza meg, hogy milyen App Service-funkciókat kap, és mennyit fizet a csomagért. A tarifacsomagok a következő kategóriákba sorolhatók:

- **Megosztott számítási:** **Ingyenes** és **megosztott**, a két alapréteg, fut egy alkalmazás ugyanazon az Azure-beli virtuális gép, mint más App Service-alkalmazások, beleértve a más ügyfelek alkalmazásait. Ezek a csomagok CPU-kvótákat rendelnek mindegyik, a közös erőforrásokon futó alkalmazáshoz, és ezek az erőforrások nem skálázhatóak fel horizontálisan.
- **Dedikált számítási:** Az **alapszintű,** **standard,** **prémium**és **premiumv2** szintek dedikált Azure-virtuális gépeken futtatnak alkalmazásokat. A számítási erőforrások csak az ugyanabban az App Service-csomagban lévő alkalmazások között kerülnek elosztásra. Minél magasabb szintű a csomag, annál több virtuálisgép-példány áll rendelkezésére a horizontális felskálázáshoz.
- **Elkülönített:** Ez a réteg dedikált Azure virtuális gépeket futtat dedikált Azure virtuális hálózatokon. Hálózati elkülönítést biztosít az alkalmazások számítási elkülönítésén felül. Ez a szint biztosítja a legnagyobb horizontális felskálázási lehetőségeket.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Minden egyes szint az App Service-funkciók egy adott részhalmazát is biztosítja. Ezek közé tartoznak az egyéni tartományok és SSL-tanúsítványok, automatikus skálázás, telepítési tárolóhelyek, biztonsági mentések, Traffic Manager-integráció stb. Minél magasabb a szint, annál több funkció érhető el. Ha meg szeretné tudni, hogy mely funkciók támogatottak az egyes tarifacsomagokban, olvassa el [az App Service-csomag részletei című témakört.](https://azure.microsoft.com/pricing/details/app-service/plans/)

<a name="new-pricing-tier-premiumv2"></a>

> [!NOTE]
> Az új **PremiumV2-díjszabási** szint gyorsabb processzorokkal, SSD-tárolóval és dupla memória-mag aránynal rendelkezik [a Dv2 sorozatú virtuális gépek](../virtual-machines/dv2-dsv2-series.md) számára a **standard** szinthez képest. **A PremiumV2** a nagyobb példányszám növelésével is támogatja a nagyobb példányok számát, miközben továbbra is biztosítja a Standard csomagban található összes speciális képességet. A **premiumv2** tartalmazza a meglévő **PremiumV2**prémium szinten elérhető összes funkciót.
>
> A többi dedikált réteghez hasonlóan három virtuálisgép-méret érhető el ehhez a réteghez:
>
> - Kicsi (egy CPU mag, 3,5 GiB memória) 
> - Közepes (két CPU mag, 7 GiB memória) 
> - Nagy (négy CPU mag, 14 GiB memória)  
>
> A **PremiumV2** díjszabási információkért lásd: [App Service díjszabása](https://azure.microsoft.com/pricing/details/app-service/).
>
> Az új **PremiumV2** tarifacsomag galibából az [App Service PremiumV2 szint konfigurálása című témakörben kaphat.](app-service-configure-premium-tier.md)

## <a name="how-does-my-app-run-and-scale"></a>Hogyan fut és méretez az alkalmazásom?

Az **ingyenes** és **a megosztott** szinteken egy alkalmazás kap CPU-perc egy megosztott virtuálisgép-példány, és nem lehet horizontális felskálázás. Más szinteken egy alkalmazás fut, és az alábbiak szerint méretezhető.

Amikor létrehoz egy alkalmazást az App Service-ben, az egy App Service-csomagba kerül. Amikor az alkalmazás fut, az Alkalmazásszolgáltatás-csomagban konfigurált összes virtuálisgép-példányon fut. Ha több alkalmazás ugyanabban az App Service-csomagban van, mindegyik ugyanazt a virtuálisgép-példányt tartalmazza. Ha egy alkalmazáshoz több központi telepítési hely is van, az összes központi telepítési bővítőhely ugyanazon a virtuálisgép-példányon is fut. Ha engedélyezi a diagnosztikai naplókat, biztonsági mentéseket hajt végre, vagy WebJobs-ot futtat, cpu-ciklusokat és memóriát is használnak ezeken a virtuálisgép-példányokon.

Ily módon az App Service-csomag az App Service-alkalmazások méretezési egysége. Ha a csomag öt virtuálisgép-példány futtatására van konfigurálva, akkor a csomagban lévő összes alkalmazás mind az öt példányon fut. Ha a csomag automatikus skálázás, majd a csomag összes alkalmazás a csomagban vannak kiskálázva együtt az automatikus skálázási beállítások alapján.

Az alkalmazások horizontális felskálázásáról a [Példányszám manuális vagy automatikus méretezése című témakörben](../monitoring-and-diagnostics/insights-how-to-scale.md)talál további információt.

<a name="cost"></a>

## <a name="how-much-does-my-app-service-plan-cost"></a>Mennyibe kerül az App Service-csomag?

Ez a szakasz azt ismerteti, hogyan számláznak az App Service-alkalmazások. Részletes, régióspecifikus díjszabási információkért lásd: [App Service Díjszabása](https://azure.microsoft.com/pricing/details/app-service/).

Az **ingyenes** szint kivételével az App Service-csomag óránkénti díjat számít fel az általa használt számítási erőforrásokon.

- A **megosztott** szinten minden alkalmazás kap egy kvótát a CPU perc, így _minden alkalmazás_ óránként kell fizetnie a CPU-kvóta.
- A dedikált számítási szinteken **(Alapszintű,** **Standard**, **Prémium**, **PremiumV2**), az App Service-csomag határozza meg, hogy az alkalmazások hány virtuálisgép-példányra vannak méretezve, így az App Service-csomag _minden virtuálisgép-példánya_ óránkénti díjat számít fel. Ezek a virtuálisgép-példányok díja ugyanaz, függetlenül attól, hogy hány alkalmazás fut rajtuk. A váratlan díjak elkerülése érdekében olvassa el [az App Service-csomag karbantartása című témakört.](app-service-plan-manage.md#delete)
- Az **Elkülönített** rétegben az App Service-környezet határozza meg az alkalmazásokat futtató elkülönített dolgozók számát, és _minden egyes dolgozónak_ óránként kell fizetnie. Emellett az App Service-környezet futtatásáért óránkénti alapdíjat is felszámítunk.

Nem kell fizetnie az Ön számára elérhető App Service-szolgáltatások használatáért (egyéni tartományok, SSL-tanúsítványok, telepítési tárolóhelyek, biztonsági mentések stb.) konfigurálása. A kivételek a következők:

- App Service-tartományok – akkor fizet, ha vásárol egyet az Azure-ban, és ha minden évben megújítja.
- App Service-tanúsítványok – akkor fizet, ha vásárol egyet az Azure-ban, és ha minden évben megújítja.
- IP-alapú SSL-kapcsolatok – Minden IP-alapú SSL-kapcsolatóránként imperális díjat számít fel, de néhány **standard** szintű vagy annál magasabb szintű csomag egy IP-alapú SSL-kapcsolatot biztosít ingyenesen. Az SNI-alapú SSL-kapcsolatok ingyenesek.

> [!NOTE]
> Ha integrálja az App Service-t egy másik Azure-szolgáltatással, előfordulhat, hogy figyelembe kell vennie az egyéb szolgáltatások díjait. Ha például az Azure Traffic Manager használatával méretezi az alkalmazást földrajzilag, az Azure Traffic Manager a használat alapján is díjat számít fel. A szolgáltatások közötti költségek azure-beli becsléséhez olvassa el [a Díjkalkulátor](https://azure.microsoft.com/pricing/calculator/). 
>
>

## <a name="what-if-my-app-needs-more-capabilities-or-features"></a>Mi a teendő, ha az alkalmazásomnak több képességre vagy funkcióra van szüksége?

App Service-csomagját bármikor skálázhatja felfelé és lefelé is. Ez olyan egyszerű, mint a csomag árképzési szintjének módosítása. Elsőre választhat egy alacsonyabb tarifájú csomagot is, amelyet később felskálázhat, amikor már további App Service-szolgáltatásokra van szüksége.

Például elkezdheti tesztelni a webalkalmazást egy **ingyenes** App Service-csomagban, és nem fizet semmit. Ha hozzá szeretné adni az [egyéni DNS-nevét](app-service-web-tutorial-custom-domain.md) a webalkalmazáshoz, csak méretezze fel a csomagot **a Megosztott** szintre. Később, ha [ssl-kötést](configure-ssl-bindings.md)szeretne létrehozni, méretezheti a tervet **alapszintre.** Ha átmeneti [környezeteket](deploy-staging-slots.md)szeretne, skálázás **standard** szintre. Ha több magra, memóriára vagy tárolóra van szüksége, nagyobb virtuálisgép-méretre skálázható ugyanabban a rétegben.

Ugyanez működik fordítva. Ha úgy érzi, hogy már nincs szüksége egy magasabb szintű szint képességeire vagy funkcióira, alacsonyabb szintre skálázható, ami pénzt takarít meg.

Az App Service-csomag felskálázásáról az [Azure-beli alkalmazások felskálázása](manage-scale-up.md)című témakörben talál további információt.

Ha az alkalmazás ugyanabban az App Service-csomagban van más alkalmazásokkal, érdemes lehet javítani az alkalmazás teljesítményét a számítási erőforrások elkülönítésével. Ezt úgy teheti meg, hogy áthelyezi az alkalmazást egy külön App Service-csomagba. További információt az [Alkalmazás áthelyezése másik App Service-csomagba](app-service-plan-manage.md#move)című témakörben talál.

## <a name="should-i-put-an-app-in-a-new-plan-or-an-existing-plan"></a>Új vagy meglévő csomagba helyezzek el egy alkalmazást?

Mivel ön fizet az App Service-csomag által leosztott számítási erőforrásokért (lásd: [Mennyibe kerül az App Service-csomag költsége?](#cost)), potenciálisan pénzt takaríthat meg, ha több alkalmazást helyez el egyetlen App Service-csomagban. Továbbra is hozzáadhat alkalmazásokat egy meglévő csomaghoz, amíg a csomag elegendő erőforrással rendelkezik a terhelés kezeléséhez. Ne feledje azonban, hogy az ugyanabban az App Service-csomagban lévő alkalmazások mindegyike ugyanazokkal a számítási erőforrásokkal rendelkezik. Annak megállapításához, hogy az új alkalmazás rendelkezik-e majd a szükséges számítási erőforrásokkal, ismernie kell a meglévő App Service-csomag kapacitását, és a hozzáadni kívánt alkalmazás várható terheltségét. Az App Service-csomag túlterhelése üzemkimaradást okozhat az új és a meglévő alkalmazások esetében is.

Alkalmazását akkor érdemes egy új önálló App Service-csomagba elkülönítenie ha:

- Az alkalmazás erőforrás-igényes.
- Az alkalmazást a meglévő csomag többi alkalmazásától függetlenül szeretné méretezni.
- Az alkalmazásnak szüksége van egy másik földrajzi régióban lévő erőforrásra.

Így új erőforrásokat rendelhet le az alkalmazáshoz, és nagyobb ellenőrzést vehet át az alkalmazások felett.

## <a name="manage-an-app-service-plan"></a>App Szolgáltatási csomag kezelése

> [!div class="nextstepaction"]
> [App Szolgáltatási csomag kezelése](app-service-plan-manage.md)
