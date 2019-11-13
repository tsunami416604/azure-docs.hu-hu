---
title: Erőforrások kezelése & kvóták
titleSuffix: Azure Machine Learning
description: Ismerje meg a Azure Machine Learning erőforrásokra vonatkozó kvótákat, valamint a további kvóták igénylését.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 11/04/2019
ms.openlocfilehash: d44fb94978c450808c8a1c0852d4c771a100857e
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961720"
---
# <a name="manage-and-request-quotas-for-azure-resources"></a>Kezelését és kérését kvóták az Azure-erőforrásokhoz
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ez a cikk részletesen ismerteti az előfizetéshez tartozó Azure-erőforrások előre konfigurált korlátait. Emellett az egyes típusú erőforrásokra vonatkozó kvóta-fejlesztések igénylésének utasításait is tartalmazza. Ezek a korlátok a költségvetés túlzott futtatások miatt csalás megelőzése érdekében, és tartsa tiszteletben az Azure kapacitásbeli megszorításokon kerüljenek.

A többi Azure-szolgáltatáshoz hasonlóan a Azure Machine Learninghöz kapcsolódó erőforrások bizonyos korlátai is vannak. Ezek a korlátok a munkaterületek számának korlátján alapulnak, amelyek a modell betanításához vagy a következtetésekhez vagy pontozáshoz használt tényleges számítási feladatokra korlátozzák. 

A Azure Machine Learning-erőforrások éles számítási feladatokhoz való tervezése és méretezése során ezeket a korlátokat érdemes figyelembe venni. Ha például a fürt nem éri el a csomópontok megcélzott számát, akkor előfordulhat, hogy elérte az előfizetéséhez Azure Machine Learning számítási magokra vonatkozó korlátot. Ha szeretné a határértéket vagy meghaladja az alapértelmezett kvóta emelése, nyissa meg egy online támogatási kérést díjmentesen. A korlátok nem léptethető feletti Azure-kapacitásokhoz megkötések miatt az alábbi táblázatban látható a maximális határértéket. Ha egyetlen oszlop sincs maximális korlátot, az erőforrás állítható korlátok nem rendelkezik.

## <a name="special-considerations"></a>Különleges szempontok

+ Kvóta kreditjét, nem kapacitás garantálja. Ha nagyméretű kapacitásigények, forduljon az Azure ügyfélszolgálatához.

+ A kvóta az előfizetésekben található összes szolgáltatás között meg van osztva, beleértve a Azure Machine Learningt is. Az egyetlen kivétel Azure Machine Learning számítás, amely az alapszintű számítási kvótától eltérő kvótával rendelkezik. Ügyeljen arra, hogy a kvótahasználat kiszámítása az összes szolgáltatásban a kapacitásigények kiértékelése során.

+ Az alapértelmezett korlátok az ajánlati kategória típusa szerint változnak, például az ingyenes próbaverzió, az utólagos elszámolású és a virtuálisgép-sorozatok, például a Dv2, az F, a G stb.

## <a name="default-resource-quotas"></a>Alapértelmezett erőforráskvóták

Íme egy bontása a kvótát, az Azure-előfizetésen belül a különböző erőforrástípusok szerint.

> [!Important]
> Korlátok változhatnak. A legújabb mindig találhatók a szolgáltatásiszint-kvóta [dokumentum](https://docs.microsoft.com/azure/azure-subscription-service-limits/) az összes Azure.

### <a name="virtual-machines"></a>Virtual machines (Virtuális gépek)
Az egyes Azure-előfizetések esetében korlátozott számú virtuális gép érhető el a szolgáltatásban vagy önálló rendszeren. A régió szinten, mind a magok száma összesen és is alapon kiszolgálónként család el ezt a korlátot.

A virtuális gépek magjai a regionális teljes korláttal és a (Dv2, F stb.) korláttal rendelkeznek, és mindkettő külön kényszerített. Például tegyük fel, hogy egy előfizetés az USA keleti régiójára vonatkozó teljes magkorlátja 30, az A sorozatú magkorlátja 30, és a D sorozatú magkorlátja is 30. Ez az előfizetés üzembe helyezhet 30 A1 virtuális gépet, 30 D1 virtuális gépet, vagy a kettő valamilyen kombinációját, amely nem haladja meg az összesen 30 magot (pl. 10 A1 virtuális gép és 20 D1 virtuális gép).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../../includes/azure-subscription-limits-azure-resource-manager.md)]

Magkvóta korlátozásának még részletesebb és naprakészebb listája, ellenőrizze az Azure kiterjedő kvóta cikk [Itt](https://docs.microsoft.com/azure/azure-subscription-service-limits).

### <a name="azure-machine-learning-compute"></a>Az Azure Machine Learning Compute
Az Azure Machine Learning Compute a magok számát és a egy előfizetésben régiónként engedélyezett egyedi számítási erőforrások számát az alapértelmezett kvóta korlátozva van. Ez a kvóta különbözik a fenti virtuálisgép-alapkvótatől, és az alapvető korlátok nincsenek megosztva a két erőforrástípus között, mivel a AmlCompute egy felügyelt szolgáltatás, amely erőforrásokat helyez üzembe egy szolgáltatott modellben.

Rendelkezésre álló erőforrások:
+ Régiónként a dedikált magok alapértelmezett korlátja 24-300, amely az előfizetési ajánlat típusától függően magasabb alapértékekkel rendelkezik az EA-és a CSP-típusok esetében.  Az előfizetéshez tartozó dedikált magok száma növelhető, és minden virtuálisgép-család esetében eltérő lehet. Bizonyos speciális virtuálisgép-családok (például a NCv2, a NCv3 vagy az ND sorozat) alapértelmezés szerint nullával kezdődnek. Vegye fel a kapcsolatot az Azure támogatási szolgálatával, és növelje a növelési lehetőségeket.

+ Az alacsony prioritású magok régiónként alapértelmezett korlátja a 100 – 3000, az előfizetési ajánlat típusától függően, amely magasabb alapértékekkel rendelkezik az EA és a CSP típusú ajánlatok esetében. Az előfizetések alacsony prioritású magok száma növelhető, és egyetlen érték a virtuálisgép-családokon belül. Lépjen kapcsolatba a növelési lehetőségek tekintetében az Azure ügyfélszolgálatától.

+ A fürtök régiónként alapértelmezett korlátja 200. Ezeket megosztják egy képzési fürt és egy számítási példány között (amely a kvóta szempontjából egyetlen csomópontos fürtnek számít). Ha meghaladja ezt a korlátot növelni szeretné, lépjen kapcsolatba az Azure-támogatás.

+ Vannak * * olyan szigorú korlátok, amelyek nem léphetik túl a találatot.

| **Erőforrás** | **Felső korlát** |
| --- | --- |
| Az adott erőforráscsoport esetében maximális munkaterületek | 800 |
| Egyetlen Azure Machine Learning Compute (AmlCompute) erőforrást a csomópontok maximális száma | 100 csomópont |
| Maximális GPU MPI folyamatok száma csomópontonként | 1 – 4 |
| Csomópontonkénti maximális GPU-feldolgozók | 1 – 4 |
| Maximális élettartama | 90 nap<sup>1</sup> |
| Feladat maximális élettartama alacsony prioritású csomóponton | 7 nap<sup>2</sup> |
| Csomópontonkénti maximális paraméter-kiszolgálók | 1 |

<sup>1</sup> maximális élettartamát hivatkozik, amely egy Futtatás elindításához, és a Befejezés után. Befejezett futtatások korlátlan ideig megőrződnek; a maximális élettartamon belül nem befejezett futtatások adatait nem érhető el.
<sup>2</sup> az alacsony prioritású csomóponton lévő feladatok bármikor előzik, ha van kapacitási korlátozás. Javasoljuk, hogy hajtsa végre az ellenőrzőpontok megvalósítását a feladatokban.

### <a name="azure-machine-learning-pipelines"></a>Azure Machine Learning folyamatok
Azure Machine Learning folyamatok esetében a folyamat lépéseinek száma, valamint az előfizetésben régiónként megjelenő közzétett folyamatok ütemezett futtatásának száma.
- A folyamatokban engedélyezett lépések maximális száma 30 000
- Az ütemezett futtatások és a blob-lekérések összegének maximális száma az előfizetéshez tartozó közzétett folyamatokra vonatkozóan, havonta 100 000

> [!NOTE]
> Ha szeretné megnövelni ezt a korlátot, forduljon a [Microsoft ügyfélszolgálatahoz](https://azure.microsoft.com/support/options/).

### <a name="container-instances"></a>Container Instances szolgáltatásban

Emellett van egy a container Instances szolgáltatásban, regisztrálhat egy adott időszakban (óránként hatóköre) vagy a teljes előfizetés számára vonatkozó határértéket.

[!INCLUDE [container-instances-limits](../../../includes/container-instances-limits.md)]

Magkvóta korlátozásának még részletesebb és naprakészebb listája, ellenőrizze az Azure kiterjedő kvóta cikk [Itt](https://docs.microsoft.com/azure/azure-subscription-service-limits#container-instances-limits).

### <a name="storage"></a>Tárolás
Storage-fiókok, valamint az adott előfizetésben régiónként száma korlátozva van. Az alapértelmezett korlát 250, és a standard és a Premium Storage fiókot is tartalmazza. Ha egy adott régióban több mint 250 Storage-fiókra van szüksége, az [Azure támogatási szolgálatán](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/)keresztül teheti meg a kérést. Az Azure Storage csapata áttekinti az üzleti esetekhez, és jóváhagyhat legfeljebb 250 tárfiókot az adott régió.


## <a name="workspace-level-quota"></a>Munkaterület-szintű kvóta

A különböző munkaterületek közötti Amlcompute erőforrás-kiosztásának jobb kezelése érdekében egy olyan funkciót vezettünk be, amely lehetővé teszi az előfizetési szint kvótáinak (VM-család szerinti) terjesztését és a munkaterület szintjén történő konfigurálását. Az alapértelmezett viselkedés az, hogy minden munkaterületnek ugyanaz a kvótája, mint bármely virtuálisgép-család előfizetési szintjének kvótája. Azonban ahogy a munkaterületek száma nő, és a különböző prioritású munkaterhelések megoszthatják ugyanazokat az erőforrásokat, a felhasználók szeretnék jobban megosztani a kapacitást, és elkerülni az erőforrás-tartalomkezelési problémákat. Azure Machine Learning megoldást biztosít a felügyelt számítási ajánlattal, mivel lehetővé teszi, hogy a felhasználók maximális kvótát állítsanak be egy adott virtuálisgép-család számára az egyes munkaterületeken. Ez hasonló a kapacitás munkaterületek közötti elosztásához, és a felhasználók dönthetnek úgy is, hogy túlterhelik a maximális kihasználtságot. 

Ha a kvótákat a munkaterület szintjén szeretné beállítani, lépjen az előfizetés bármely munkaterületére, majd a bal oldali ablaktáblán kattintson a **kihasználtságok + kvóták** elemre. Ezután a **kvóták konfigurálása** lapon megtekintheti a kvótákat, kibonthatja a virtuálisgép-családokat, és beállíthatja az adott virtuálisgép-családban felsorolt munkaterületekre vonatkozó kvóta korlátját. Ne feledje, hogy nem állítható be negatív érték, vagy az előfizetési szint kvótájának magasabb értéke. Emellett, ahogy azt is megfigyelheti, alapértelmezés szerint minden munkaterülethez a teljes előfizetési kvóta van hozzárendelve, hogy lehetővé váljon a lefoglalt kvóta teljes kihasználtsága.

[![Azure Machine Learning munkaterület szintjének kvótája](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)


> [!NOTE]
> Ez csak az Enterprise Edition funkció. Ha az előfizetésében egy alapszintű és egy Enterprise Edition-munkaterülettel is rendelkezik, akkor ez csak a vállalati munkaterületeken lévő kvóták beállítására használható. Az alapszintű munkaterületek továbbra is az előfizetési szint kvótáját fogják használni, amely az alapértelmezett viselkedés.
>
> Az előfizetési szintű engedélyekre van szükség a kvóta beállításához a munkaterület szintjén. Ezt úgy kell kikényszeríteni, hogy az egyes munkaterület-tulajdonosok ne szerkesszék vagy növeljék a kvótákat, és a csorbítaná a másik munkaterületre elkülönített erőforrásokra indítsák. Ennek megfelelően az előfizetés-adminisztrátor a legmegfelelőbb a kvóták munkaterületek közötti kiosztásához és terjesztéséhez.



## <a name="view-your-usage-and-quotas"></a>A használat és a kvóták megtekintése

A különböző erőforrások, például a virtuális gépek, tárolási, hálózati, a kvóta megtekintése is egyszerűen az Azure Portalon keresztül.

1. A bal oldali ablaktáblán válassza a **minden szolgáltatás** lehetőséget, majd az általános kategóriában válassza az **előfizetések** lehetőséget.

1. Előfizetések listájából válassza ki az előfizetést, amelynek Ön által keresett kvótát.

   **Van egy kikötést**, kifejezetten a megtekintése az Azure Machine Learning Compute kvótát. Ahogy említettük, a kvóta elkülönül az előfizetéshez, a számítási kvótával.

1. A bal oldali ablaktáblán válassza a **Machine learning szolgáltatás** lehetőséget, majd válasszon ki egy munkaterületet a listából.

1. A következő panelen alatt a **támogatás és hibaelhárítás szakaszhoz** kiválasztása **használat + kvóták** a jelenlegi kvóta korlátozásának emelése és használati megtekintéséhez.

1. Válasszon egy előfizetést a kvótakorlát megtekintéséhez. Ne felejtse el szűrése az Önt érdeklő régióba.

1. Mostantól válthat egy előfizetési szint nézet és egy munkaterület szintű nézet között:
    + **Előfizetési nézet:** Ez lehetővé teszi, hogy megtekintse az alapszintű kvóta használatát a virtuálisgép-család számára, bővítse azt munkaterületen, és bővítse azt a tényleges fürt nevével. Ez a nézet optimális megoldást nyújt az adott virtuálisgép-család alapvető használatának részleteire, így a munkaterületek által felhasználható munkaterületek és a mögöttes fürtök is megjelennek. A nézet általános egyezménye (használat/kvóta), ahol a használat a Felskálázási magok aktuális száma, a kvóta pedig az erőforrás által méretezhető magok logikai maximális száma. Az egyes **munkaterületek**esetében a kvóta a munkaterület szintjének kvóta (a fentebb leírtak szerint), amely az adott virtuálisgép-család számára méretezhető magok maximális számát jelöli. A **fürthöz** hasonlóan a kvóta valójában a fürt által a max_nodes tulajdonság által definiált csomópontok maximális számának megfelelő magok mérete.

    + **Munkaterület nézet:** Így megtekintheti az alapszintű kvóta használatát a munkaterület alapján, kiterjesztheti a virtuálisgép-családot, és tovább bővítheti a tényleges fürt neve alapján. Ez a nézet optimális megoldást nyújt arra, hogy gyorsan bejusson egy adott munkaterülethez tartozó alapvető használat részleteibe, hogy láthassa a virtuálisgép-családok felbomlását, valamint az egyes családokhoz tartozó mögöttes fürtöket.

## <a name="request-quota-increases"></a>Kérelem kvótanövelésre

Ha az alapértelmezett korlátnál magasabb korlátot vagy kvótát szeretne növelni, [Nyisson meg egy online ügyfélszolgálati kérést](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) díjmentesen.

A határértékek nem állíthatók be a táblákban megjelenő maximális határérték fölé. Ha nincs maximális korlát, az erőforrás nem rendelkezik állítható korlátokkal. [Ez](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors) a cikk ismerteti a kvóta növelését folyamat részletesebben.

Kvótanövelést kérésekor kell válassza ki a kérelmet a kvóta növeléséhez, szemben a szolgáltatást, amely lehet a szolgáltatások, mint a Machine Learning szolgáltatásra vonatkozó kvótáról, a Container Instances szolgáltatásban vagy a tárolási kvótát. A Azure Machine Learning számítás mellett **a kvóta megtekintése gombra is** kattinthat, miközben a fenti lépéseket követve megtekinti a kvótát.

> [!NOTE]
> [Az ingyenes próbaverziós előfizetésekben](https://azure.microsoft.com/offers/ms-azr-0044p) nem jogosultak korlátozása vagy a kvóta növekszik. Ha rendelkezik egy [ingyenes próba-előfizetésre](https://azure.microsoft.com/offers/ms-azr-0044p), frissíthet egy [használatalapú](https://azure.microsoft.com/offers/ms-azr-0003p/) előfizetés. További információkért lásd: [Azure ingyenes próbaverzió frissítése használatalapú fizetésre](../../billing/billing-upgrade-azure-subscription.md) és [ingyenes próba-előfizetéssel kapcsolatos gyakori kérdések](https://azure.microsoft.com/free/free-account-faq).
