---
title: Erőforrások & kvóták kezelése
titleSuffix: Azure Machine Learning
description: Ismerje meg az Azure Machine Learning erőforrásaira vonatkozó kvótákat, és hogyan kérhet további kvótát.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 03/05/2020
ms.openlocfilehash: 17a4652604c0faa804d24530869aac0848c972b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399141"
---
# <a name="manage-and-request-quotas-for-azure-resources"></a>Azure-erőforrások kvótáinak kezelése és kérése
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ez a cikk az előfizetéshez szükséges Azure-erőforrások előre konfigurált korlátainak részleteit ismerteti. Az egyes erőforrástípusokhoz tartozó kvótafejlesztések re vonatkozó utasítások is megtalálhatók. Ezek a korlátok azért vannak érvényben, hogy megakadályozzák a költségvetési túlfuttatások csalás miatt, és hogy az Azure kapacitáskorlátok.

Más Azure-szolgáltatásokhoz is csakúgy, mint az Azure Machine Learninghez társított bizonyos erőforrások. Ezek a korlátok a munkaterületek számának korlátjától a modellbetanításhoz vagy következtetéshez/pontozáshoz használt tényleges számítási korlátig terjednek. 

Az Azure Machine Learning-erőforrások éles számítási feladatokhoz való tervezése és méretezése során vegye figyelembe ezeket a korlátokat. Például ha a fürt nem éri el a csomópontok célszámát, akkor lehet, hogy elérte az Azure Machine Learning-magok korlátját az előfizetéshez. Ha az alapértelmezett korlát fölé szeretné emelni a korlátot vagy kvótát, nyisson meg egy online ügyfélszolgálati kérelmet díjmentesen. A korlátok nem emelhetők az alábbi táblázatokban látható maximális határérték fölé az Azure-kapacitás korlátai miatt. Ha nincs Maximális korlát oszlop, akkor az erőforrás nem rendelkezik állítható korlátokkal.

## <a name="special-considerations"></a>Különleges szempontok

+ A kvóta hitelkeret, nem pedig kapacitásgarancia. Ha nagy kapacitásra van szüksége, forduljon az Azure támogatási szolgálatához.

+ A kvóta meg van osztva az előfizetésekben, így az Azure Machine Learningben található összes szolgáltatás között. Az egyetlen kivétel az Azure Machine Learning-számítás, amely külön kvótát az alapvető számítási kvótát. Ügyeljen arra, hogy a kapacitásigények kiértékelésekor számítsa ki a kvótahasználatot az összes szolgáltatás között.

+ Az alapértelmezett korlátok az ajánlat kategóriatípusától függően változnak, például az ingyenes próbaverzió, a csak annyit tudható be, hogy a virtuális gép sorozatai(k), például A Dv2, F, G és így tovább.

## <a name="default-resource-quotas"></a>Alapértelmezett erőforráskvóták

Az alábbiakban a kvótakorlátok különböző erőforrástípusok szerinti bontását olvashatja az Azure-előfizetésen belül.

> [!IMPORTANT]
> A korlátok változhatnak. A legújabb mindig megtalálható a szolgáltatásszintű kvóta [dokumentum](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits/) az egész Azure-ban.

### <a name="virtual-machines"></a>Virtuális gépek
Minden Egyes Azure-előfizetésesetén korlátozva van a szolgáltatások között vagy önálló virtuális gépek száma. Ez a korlát a régió szintjén, mind a teljes magok, mind a családonkénti alapon.

A virtuálisgép-magok regionális teljes korláttal és méretsorozatonkénti regionális korláttal (Dv2, F stb.) rendelkeznek, mindkettő külön-külön van érvényben. Például tegyük fel, hogy egy előfizetés az USA keleti régiójára vonatkozó teljes magkorlátja 30, az A sorozatú magkorlátja 30, és a D sorozatú magkorlátja is 30. Ez az előfizetés üzembe helyezhet 30 A1 virtuális gépet, 30 D1 virtuális gépet, vagy a kettő valamilyen kombinációját, amely nem haladja meg az összesen 30 magot (pl. 10 A1 virtuális gép és 20 D1 virtuális gép).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

A kvótakorlátok részletesebb és naprakészebb listáját az Azure-szintű kvótacikkben [itt olvashatja.](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning Compute
Az Azure Machine Learning Compute esetében egy alapértelmezett kvótakorlát van mind a magok számára, mind az előfizetésben régiónként engedélyezett egyedi számítási erőforrások számára vonatkozóan. Ez a kvóta elkülönül a virtuális gép alapvető kvótájától, és az alapvető korlátok nem oszthatók meg a két erőforrástípus között, mivel az AmlCompute egy felügyelt szolgáltatás, amely erőforrásokat telepít egy üzemeltetett-a-nevében-modell.

Rendelkezésre álló források:
+ A régiónkénti dedikált magok alapértelmezett korlátja 24–300, attól függően, hogy előfizetési ajánlattípusa magasabb alapértelmezett értékekkel rendelkezik az EA és a CSP-ajánlattípusok esetében.  A dedikált magok előfizetésenként számával növelhető, és minden virtuálisgép-család eltérő. Bizonyos speciális virtuálisgép-családok, például az NCv2, Az NCv3 vagy az ND sorozat nulla magalapértelmezett értékkel kezdődnek. Lépjen kapcsolatba az Azure-támogatással egy kvótakérelem létrehozásával a növelési lehetőségek megvitatásához.

+ Az alacsony prioritású magok régiónként 100–3000-es alapértelmezett korláttal rendelkeznek, attól függően, hogy az előfizetési ajánlat típusa magasabb alapértelmezett értékekkel rendelkezik az EA és a CSP-ajánlattípusok esetében. Az előfizetésenkénti alacsony prioritású magok száma növelhető, és egyetlen érték a virtuálisgép-családok között. Lépjen kapcsolatba az Azure ügyfélszolgálatával a növelési lehetőségek megvitatásához.

+ A régiónkénti fürtök alapértelmezett korlátja 200. Ezek meg vannak osztva egy betanítási fürt és egy számítási példány (amely kvótacélokra egyetlen csomópont-fürtnek számít). Lépjen kapcsolatba az Azure-támogatással, ha ennél a korláton túli emelést szeretne kérni.

+ Vannak ** egyéb szigorú korlátok, amelyeket nem lehet túllépni, ha találatot.

| **Erőforrás** | **Maximális határérték** |
| --- | --- |
| Maximális munkaterületek erőforráscsoportonként | 800 |
| Egyetlen Azure Machine Learning Compute (AmlCompute) erőforrás maximális csomópontjai | 100 csomópont |
| Maximális GPU MPI folyamatok csomópontonként | 1-4 |
| Maximális GPU-dolgozók csomópontonként | 1-4 |
| A munka maximális élettartama | 90 nap<sup>1</sup> |
| A feladat maximális élettartama alacsony prioritású csomóponton | 7 nap<sup>2</sup> |
| Maximális paraméterkiszolgálók csomópontonként | 1 |

<sup>1</sup> A maximális élettartam arra az időre vonatkozik, amikor a futás elindul, és amikor befejeződik. A befejezett futtatások határozatlan ideig fennállnak; a maximális élettartamon belül be nem fejezett futtatások adatai nem érhetők el.
<sup>2</sup> Az alacsony prioritású csomóponton lévő feladatok bármikor előzetesen megtekinthetők, ha kapacitáskorlátozás van. Azt javasoljuk, hogy ellenőrzőpontokat valósítson meg a feladatában.

### <a name="azure-machine-learning-pipelines"></a>Azure Machine Learning-folyamatok
Az Azure Machine Learning-folyamatok, kvótakorlát van a lépések száma egy folyamatban, és a közzétett folyamatok ütemezésalapú futtatások száma régiónként egy előfizetésben.
- A folyamatban engedélyezett lépések maximális száma 30 000
- A közzétett folyamatok blogáltal aktivált ütemezési ütemezési és blob-lekérései összegének maximális száma előfizetésenként havonta 100 000

> [!NOTE]
> Ha növelni szeretné ezt a korlátot, forduljon [a Microsoft támogatási szolgálatához.](https://azure.microsoft.com/support/options/)

### <a name="container-instances"></a>Tárolópéldányok

A tárolópéldányok száma is korlátozva van, amelyek et egy adott időszakban (óránkénti hatókör) vagy a teljes előfizetésben felpörgethet.

[!INCLUDE [container-instances-limits](../../includes/container-instances-limits.md)]

A kvótakorlátok részletesebb és naprakészebb listáját az Azure-szintű kvótacikkben [itt olvashatja.](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#container-instances-limits)

### <a name="storage"></a>Storage
Egy adott előfizetésben is korlátozva van a tárfiókok száma régiónként. Az alapértelmezett korlát 250, és standard és prémium szintű storage-fiókokat is tartalmaz. Ha egy adott régióban 250-nél több tárfiókra van szüksége, kérjen kérelmet az [Azure-támogatáson](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/)keresztül. Az Azure Storage csapata áttekinti az üzleti esetet, és egy adott régióban legfeljebb 250 tárfiókot hagyhat jóvá.


## <a name="workspace-level-quota"></a>Munkaterületi kvóta

Az Amlcompute különböző munkaterületek közötti erőforrás-allokációinak jobb kezelése érdekében bevezettünk egy olyan szolgáltatást, amely lehetővé teszi az előfizetési szintű kvóták (virtuálisgép-család általi) elosztását és konfigurálását a munkaterület szintjén. Az alapértelmezett viselkedés az, hogy minden munkaterület rendelkezik ugyanazokkal a kvótával, mint az előfizetési szintű kvóta bármely virtuálisgép-család. Azonban a munkaterületek számának növekedésével és a különböző prioritású munkaterhelések azonos erőforrások megosztásával a felhasználók szeretnék a kapacitás jobb megosztását és az erőforrás-versengési problémák elkerülését. Az Azure Machine Learning megoldást kínál a felügyelt számítási ajánlattal, mivel lehetővé teszi a felhasználók számára, hogy minden munkaterületen egy adott virtuálisgép-család maximális kvótáját beállítsák. Ez hasonló a kapacitás elosztásához a munkaterületek között, és a felhasználók dönthetnek úgy is, hogy túlfoglalják a maximális kihasználtságot a meghajtó maximális kihasználásához. 

Ha munkaterületi szinten szeretné beállítani a kvótákat, lépjen az előfizetés bármely munkaterületére, és kattintson a bal oldali ablaktáblában a **Használat + kvóták** elemre. Ezután válassza a **Kvóták konfigurálása** fülre a kvóták megtekintéséhez, bontsa ki a virtuálisgép-családot, és állítson be kvótakorlátot az adott virtuálisgép-család ban felsorolt bármely munkaterületre. Ne feledje, hogy nem állíthat be negatív értéket vagy az előfizetési szint kvótánál magasabb értéket. Továbbá, ahogy azt megfigyelné, alapértelmezés szerint az összes munkaterület a teljes előfizetési kvótát rendeli hozzá, hogy lehetővé tegye a lefoglalt kvóta teljes kihasználását.

[![Az Azure Machine Learning munkaterületi szintjének kvótája](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)


> [!NOTE]
> Ez csak az Enterprise edition szolgáltatás. Ha az előfizetésben alapszintű és egy nagyvállalati kiadású munkaterület is található, ezzel csak kvótákat állíthat be a vállalati munkaterületeken. Az alapszintű munkaterületek továbbra is az előfizetési szintű kvótát, amely az alapértelmezett viselkedés.
>
> A kvóta munkaterületi szinten történő beállításához előfizetési szintű engedélyekre van szükség. Ez úgy van kikényszerítve, hogy az egyes munkaterület-tulajdonosok ne szerkeszthessék vagy növeljék a kvótáikat, és ne kezdjék meg a másik munkaterületre félretett erőforrásokra való betolakodást. Így az előfizetés rendszergazdája a legalkalmasabb a kvóták felosztására és elosztására a munkaterületek között.



## <a name="view-your-usage-and-quotas"></a>A használat és a kvóták megtekintése

A kvóta megtekintése a különböző erőforrások, például a virtuális gépek, tárolás, hálózat, az Azure Portalon keresztül egyszerű.

1. A bal oldali ablaktáblában válassza a **Minden szolgáltatás** lehetőséget, majd válassza az **Előfizetések** lehetőséget az Általános kategória alatt.

1. Az előfizetések listájából válassza ki azt az előfizetést, amelynek kvótáját keresi.

   **Van egy kikötés,** kifejezetten az Azure Machine Learning számítási kvótának megtekintéséhez. Mint már említettük, hogy a kvóta elkülönül a számítási kvótát az előfizetésben.

1. A bal oldali ablaktáblán válassza a **Machine Learning szolgáltatás** lehetőséget, majd válasszon ki egy munkaterületet a

1. A következő panelen a **Támogatás + hibaelhárítás szakaszban** válassza **a Használat + kvóták** lehetőséget az aktuális kvótakorlátok és -használat megtekintéséhez.

1. Válasszon ki egy előfizetést a kvótakorlátok megtekintéséhez. Ne felejtsen el arra a területre szűrni, amely érdekli.

1. Most válthat egy előfizetési szintű nézet és egy munkaterületi szintű nézet között:
    + **Előfizetés nézet:** Ez lehetővé teszi, hogy tekintse meg az alapvető kvóta használatát virtuálisgép-család szerint, munkaterület szerint bővítve azt, és tovább bővítve azt a tényleges fürtnevekkel. Ez a nézet optimális, hogy gyorsan beolvassa egy adott virtuálisgép-család alapvető használatának részleteit, hogy a munkaterületek és az alapul szolgáló fürtök által az egyes munkaterületek további bontását láthassa. Ebben a nézetben az általános konvenció (használat/kvóta), ahol a használat a felskálázott magok aktuális száma, és a kvóta az erőforrás által skálázható magok logikai maximális száma. Az egyes **munkaterületen**a kvóta a munkaterületi szint kvóta (a fentiekszerint), amely egy adott virtuálisgép-család maximális számú magját jelöli. A **fürt** höz hasonlóan a kvóta valójában a magok, amelyek megfelelnek a fürt által a max_nodes tulajdonság által definiált csomópontok maximális számának.

    + **Munkaterület nézet:** Ez lehetővé teszi, hogy tekintse meg az alapvető kvóta használatát a munkaterület szerint, bővítse ki a virtuális gép család, és tovább bővíti azt a tényleges fürtnevek. Ez a nézet optimális, hogy gyorsan beolvassa az alapvető használat részleteit egy adott munkaterületen, hogy a virtuális gép családok és tovább az alapul szolgáló fürtök az egyes családok.

## <a name="request-quota-increases"></a>Kvótanövelések kérése

Ha az alapértelmezett korlát vagy kvóta fölé szeretné emelni a korlátot vagy a kvótát, [nyisson meg egy online ügyfélszolgálati kérelmet](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) díjmentesen.

A korlátok nem emelhetők a táblázatokban látható maximális határérték fölé. Ha nincs maximális korlát, akkor az erőforrás nem rendelkezik állítható korlátokkal. [Ez](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors) a cikk részletesebben ismerteti a kvótanövelési folyamatot.

Kvótanövelés igénylésekor ki kell választania azt a szolgáltatást, amelyre a kvóta emelésére kér, amely lehet olyan szolgáltatás, mint a Machine Learning szolgáltatáskvóta, a tárolópéldányok vagy a storage-kvóta. Emellett az Azure Machine Learning Compute esetében a **Kvóta kérése gombra** kattinthat, miközben a fenti lépéseket követve megtekinti a kvótát.

> [!NOTE]
> [Az ingyenes próbaverziós előfizetések](https://azure.microsoft.com/offers/ms-azr-0044p) nem jogosultak a korlát vagy a kvótanövelésére. Ha ingyenes [próbaverziós előfizetéssel](https://azure.microsoft.com/offers/ms-azr-0044p)rendelkezik, frissíthet a [csak annyi-ki(ti)](https://azure.microsoft.com/offers/ms-azr-0003p/) előfizetésre, hogy az ingyenesen használható előfizetéssel rendelkezik. További információ: [Ingyenes Azure-próbaverzió frissítése használatra kiosztó](../billing/billing-upgrade-azure-subscription.md) és [ingyenes próbaverziós gyakori kérdések című témakörben.](https://azure.microsoft.com/free/free-account-faq)
