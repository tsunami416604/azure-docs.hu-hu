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
ms.date: 03/05/2020
ms.openlocfilehash: 530647c3d32b62f0cac250795ccce580b182fa92
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80756603"
---
# <a name="manage-and-request-quotas-for-azure-resources"></a>Azure-erőforrásokra vonatkozó kvóták kezelése és igénylése
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ez a cikk részletesen ismerteti az előfizetéshez tartozó Azure-erőforrások előre konfigurált korlátait. Emellett az egyes típusú erőforrásokra vonatkozó kvóta-fejlesztések igénylésének utasításait is tartalmazza. Ezeket a korlátozásokat a rendszer a csalás miatt, illetve az Azure-kapacitás megkötések tiszteletben tartásának megelőzése érdekében helyezi el.

A többi Azure-szolgáltatáshoz hasonlóan a Azure Machine Learninghöz kapcsolódó erőforrások bizonyos korlátai is vannak. Ezek a korlátok a munkaterületek számának korlátján alapulnak, amelyek a modell betanításához vagy a következtetésekhez vagy pontozáshoz használt tényleges számítási feladatokra korlátozzák. 

A Azure Machine Learning-erőforrások éles számítási feladatokhoz való tervezése és méretezése során ezeket a korlátokat érdemes figyelembe venni. Ha például a fürt nem éri el a csomópontok megcélzott számát, akkor előfordulhat, hogy elérte az előfizetéséhez Azure Machine Learning számítási magokra vonatkozó korlátot. Ha az alapértelmezett korlátnál magasabb korlátot vagy kvótát szeretne növelni, nyisson meg egy online ügyfélszolgálati kérést díjmentesen. Az Azure-kapacitás megkötései miatt a határértékek nem állíthatók be az alábbi táblázatokban látható maximális határérték fölé. Ha nincs maximális korlát oszlop, akkor az erőforrás nem rendelkezik állítható korlátokkal.

## <a name="special-considerations"></a>Különleges szempontok

+ A kvóta egy hitelkeret, nem pedig a kapacitási garancia. Ha nagy méretű kapacitásra van szüksége, forduljon az Azure ügyfélszolgálatához.

+ A kvóta az előfizetésekben található összes szolgáltatás között meg van osztva, beleértve a Azure Machine Learningt is. Az egyetlen kivétel Azure Machine Learning számítás, amely az alapszintű számítási kvótától eltérő kvótával rendelkezik. Ügyeljen arra, hogy a kapacitási igények kiértékelése során számítsa ki a kvóta használatát az összes szolgáltatásban.

+ Az alapértelmezett korlátok az ajánlati kategória típusa szerint változnak, például az ingyenes próbaverzió, az utólagos elszámolású és a virtuálisgép-sorozatok, például a Dv2, az F, a G stb.

## <a name="default-resource-quotas"></a>Alapértelmezett erőforrás-kvóták

Itt látható az Azure-előfizetésben lévő különböző erőforrástípusok kvótáinak korlátozása.

> [!IMPORTANT]
> A korlátozások változhatnak. A legújabbak mindig az összes Azure szolgáltatási szintű kvóta [dokumentumában](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits/) találhatók.

### <a name="virtual-machines"></a>Virtual machines (Virtuális gépek)
Az egyes Azure-előfizetések esetében korlátozott számú virtuális gép érhető el a szolgáltatásban vagy önálló rendszeren. Ez a korlát a teljes magokon, valamint a családon belül is a régió szintjén érhető el.

A virtuális gépek magjai a regionális teljes korláttal és a (Dv2, F stb.) korláttal rendelkeznek, és mindkettő külön kényszerített. Például tegyük fel, hogy egy előfizetés az USA keleti régiójára vonatkozó teljes magkorlátja 30, az A sorozatú magkorlátja 30, és a D sorozatú magkorlátja is 30. Ez az előfizetés üzembe helyezhet 30 A1 virtuális gépet, 30 D1 virtuális gépet, vagy a kettő valamilyen kombinációját, amely nem haladja meg az összesen 30 magot (pl. 10 A1 virtuális gép és 20 D1 virtuális gép).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

A kvóta korlátainak részletesebb és naprakész listáját az Azure-ra vonatkozó kvóta [című cikkben találja](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning Compute
Azure Machine Learning számítás esetében az előfizetések régiónként legfeljebb a magok számának és az egyes régiókban engedélyezett egyedi számítási erőforrások számának az alapértelmezett kvótája. Ez a kvóta különbözik a fenti virtuálisgép-alapkvótatől, és az alapvető korlátok nincsenek megosztva a két erőforrástípus között, mivel a AmlCompute egy felügyelt szolgáltatás, amely erőforrásokat helyez üzembe egy szolgáltatott modellben.

Rendelkezésre álló erőforrások:
+ Régiónként a dedikált magok alapértelmezett korlátja 24-300, amely az előfizetési ajánlat típusától függően magasabb alapértékekkel rendelkezik az EA-és a CSP-típusok esetében.  Az előfizetéshez tartozó dedikált magok száma növelhető, és minden virtuálisgép-család esetében eltérő lehet. Bizonyos speciális virtuálisgép-családok (például a NCv2, a NCv3 vagy az ND sorozat) alapértelmezés szerint nullával kezdődnek. Vegye fel a kapcsolatot az Azure támogatási szolgálatával, és növelje a növelési lehetőségeket.

+ Az alacsony prioritású magok régiónként alapértelmezett korlátja a 100 – 3000, az előfizetési ajánlat típusától függően, amely magasabb alapértékekkel rendelkezik az EA és a CSP típusú ajánlatok esetében. Az előfizetések alacsony prioritású magok száma növelhető, és egyetlen érték a virtuálisgép-családokon belül. Vegye fel a kapcsolatot az Azure támogatási szolgálatával a lehetőségek növelésére.

+ A fürtök régiónként alapértelmezett korlátja 200. Ezeket megosztják egy képzési fürt és egy számítási példány között (amely a kvóta szempontjából egyetlen csomópontos fürtnek számít). Vegye fel a kapcsolatot az Azure támogatási szolgálatával, ha a korláton felül szeretne növekedni.

+ Vannak olyan szigorú korlátok, amelyek nem léphetik túl a találatot.

| **Erőforrás** | **Maximális korlát** |
| --- | --- |
| Munkaterületek maximális száma erőforráscsoport szerint | 800 |
| Egyetlen Azure Machine Learning számítási (AmlCompute) erőforrásban található csomópontok maximális száma | 100 csomópont |
| GPU MPI-folyamatok maximális száma/csomópont | 1-4 |
| GPU-feldolgozók maximális száma csomóponton | 1-4 |
| Feladatok maximális élettartama | 90 nap<sup>1</sup> |
| Feladat maximális élettartama alacsony prioritású csomóponton | 7 nap<sup>2</sup> |
| Paraméteres kiszolgálók maximális száma csomóponton | 1 |

<sup>1</sup> a maximális élettartam a Futtatás kezdetének és befejezésének időpontjára utal. A befejezett futtatások határozatlan ideig tartanak; nem érhető el a maximális élettartamon belül nem befejezett futtatások adatkészletei.
<sup>2</sup> az alacsony prioritású csomóponton lévő feladatok bármikor előzik, ha van kapacitási korlátozás. Javasoljuk, hogy hajtsa végre az ellenőrzőpontok megvalósítását a feladatokban.

### <a name="azure-machine-learning-pipelines"></a>Azure Machine Learning folyamatok
Azure Machine Learning folyamatok esetében a folyamat lépéseinek száma, valamint az előfizetésben régiónként megjelenő közzétett folyamatok ütemezett futtatásának száma.
- A folyamatokban engedélyezett lépések maximális száma 30 000
- Az ütemezett futtatások és a blob-lekérések összegének maximális száma az előfizetéshez tartozó közzétett folyamatokra vonatkozóan, havonta 100 000

> [!NOTE]
> Ha szeretné megnövelni ezt a korlátot, forduljon a [Microsoft ügyfélszolgálatahoz](https://azure.microsoft.com/support/options/).

### <a name="container-instances"></a>Tárolópéldányok

Az adott időszakban (óránkénti hatókörben) vagy a teljes előfizetésen belül elforgatható tároló-példányok száma is korlátozott.

[!INCLUDE [container-instances-limits](../../includes/container-instances-limits.md)]

A kvóta korlátainak részletesebb és naprakész listáját az Azure-ra vonatkozó kvóta [című cikkben találja](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#container-instances-limits).

### <a name="storage"></a>Storage
A Storage-fiókok száma régiónként és egy adott előfizetésben is korlátozott. Az alapértelmezett korlát 250, és a standard és a Premium Storage fiókot is tartalmazza. Ha egy adott régióban több mint 250 Storage-fiókra van szüksége, az [Azure támogatási szolgálatán](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/)keresztül teheti meg a kérést. Az Azure Storage csapata áttekinti az üzleti ügyeit, és akár 250 Storage-fiókot is jóváhagyhat egy adott régióban.


## <a name="workspace-level-quota"></a>Munkaterület-szintű kvóta

A különböző munkaterületek közötti Amlcompute erőforrás-kiosztásának jobb kezelése érdekében egy olyan funkciót vezettünk be, amely lehetővé teszi az előfizetési szint kvótáinak (VM-család szerinti) terjesztését és a munkaterület szintjén történő konfigurálását. Az alapértelmezett viselkedés az, hogy minden munkaterületnek ugyanaz a kvótája, mint bármely virtuálisgép-család előfizetési szintjének kvótája. Azonban ahogy a munkaterületek száma nő, és a különböző prioritású munkaterhelések megoszthatják ugyanazokat az erőforrásokat, a felhasználók szeretnék jobban megosztani a kapacitást, és elkerülni az erőforrás-tartalomkezelési problémákat. Azure Machine Learning megoldást biztosít a felügyelt számítási ajánlattal, mivel lehetővé teszi, hogy a felhasználók maximális kvótát állítsanak be egy adott virtuálisgép-család számára az egyes munkaterületeken. Ez hasonló a kapacitás munkaterületek közötti elosztásához, és a felhasználók dönthetnek úgy is, hogy túlterhelik a maximális kihasználtságot. 

Ha a kvótákat a munkaterület szintjén szeretné beállítani, lépjen az előfizetés bármely munkaterületére, majd a bal oldali ablaktáblán kattintson a **kihasználtságok + kvóták** elemre. Ezután a **kvóták konfigurálása** lapon megtekintheti a kvótákat, kibonthatja a virtuálisgép-családokat, és beállíthatja az adott virtuálisgép-családban felsorolt munkaterületekre vonatkozó kvóta korlátját. Ne feledje, hogy nem állítható be negatív érték, vagy az előfizetési szint kvótájának magasabb értéke. Emellett, ahogy azt is megfigyelheti, alapértelmezés szerint minden munkaterülethez a teljes előfizetési kvóta van hozzárendelve, hogy lehetővé váljon a lefoglalt kvóta teljes kihasználtsága.

[![Azure Machine Learning munkaterület-szintű kvóta](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)


> [!NOTE]
> Ez csak az Enterprise Edition funkció. Ha az előfizetésében egy alapszintű és egy Enterprise Edition-munkaterülettel is rendelkezik, akkor ez csak a vállalati munkaterületeken lévő kvóták beállítására használható. Az alapszintű munkaterületek továbbra is az előfizetési szint kvótáját fogják használni, amely az alapértelmezett viselkedés.
>
> Az előfizetési szintű engedélyekre van szükség a kvóta beállításához a munkaterület szintjén. Ezt úgy kell kikényszeríteni, hogy az egyes munkaterület-tulajdonosok ne szerkesszék vagy növeljék a kvótákat, és a csorbítaná a másik munkaterületre elkülönített erőforrásokra indítsák. Ennek megfelelően az előfizetés-adminisztrátor a legmegfelelőbb a kvóták munkaterületek közötti kiosztásához és terjesztéséhez.



## <a name="view-your-usage-and-quotas"></a>A használat és a kvóták megtekintése

A kvóta megtekinthető a különböző erőforrások, például a Virtual Machines, a tárterület, a hálózat és a Azure Portal használatával.

1. A bal oldali ablaktáblán válassza a **minden szolgáltatás** lehetőséget, majd az általános kategóriában válassza az **előfizetések** lehetőséget.

1. Az előfizetések listájából válassza ki azt az előfizetést, amelynek a kvótáját keresi.

   Van **egy kikötés**, különösen a Azure Machine learning számítási kvóta megtekintéséhez. A fentiekben leírtaknak megfelelően ez a kvóta eltér az előfizetésében szereplő számítási kvótától.

1. A bal oldali ablaktáblán válassza a **Machine learning szolgáltatás** lehetőséget, majd válasszon ki egy munkaterületet a listából.

1. A következő **panelen a támogatás + hibaelhárítás szakaszban** válassza a **használat + kvóták** lehetőséget a jelenlegi kvóta-korlátok és-használat megtekintéséhez.

1. Válasszon egy előfizetést a kvóta korlátainak megtekintéséhez. Ne felejtse el szűrni az Önt érdeklő régiót.

1. Mostantól válthat egy előfizetési szint nézet és egy munkaterület szintű nézet között:
    + **Előfizetési nézet:** Ez lehetővé teszi, hogy megtekintse az alapszintű kvóta használatát a virtuálisgép-család számára, bővítse azt munkaterületen, és bővítse azt a tényleges fürt nevével. Ez a nézet optimális megoldást nyújt az adott virtuálisgép-család alapvető használatának részleteire, így a munkaterületek által felhasználható munkaterületek és a mögöttes fürtök is megjelennek. A nézet általános egyezménye (használat/kvóta), ahol a használat a Felskálázási magok aktuális száma, a kvóta pedig az erőforrás által méretezhető magok logikai maximális száma. Az egyes **munkaterületek**esetében a kvóta a munkaterület szintjének kvóta (a fentebb leírtak szerint), amely az adott virtuálisgép-család számára méretezhető magok maximális számát jelöli. A **fürthöz** hasonlóan a kvóta valójában a fürt által a max_nodes tulajdonság által definiált csomópontok maximális számának megfelelő magok mérete.

    + **Munkaterület nézet:** Így megtekintheti az alapszintű kvóta használatát a munkaterület alapján, kiterjesztheti a virtuálisgép-családot, és tovább bővítheti a tényleges fürt neve alapján. Ez a nézet optimális megoldást nyújt arra, hogy gyorsan bejusson egy adott munkaterülethez tartozó alapvető használat részleteibe, hogy láthassa a virtuálisgép-családok felbomlását, valamint az egyes családokhoz tartozó mögöttes fürtöket.

## <a name="request-quota-increases"></a>Kvótanövelések kérése

Ha az alapértelmezett korlátnál magasabb korlátot vagy kvótát szeretne növelni, [Nyisson meg egy online ügyfélszolgálati kérést](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) díjmentesen.

A határértékek nem állíthatók be a táblákban megjelenő maximális határérték fölé. Ha nincs maximális korlát, az erőforrás nem rendelkezik állítható korlátokkal. [Ez](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors) a cikk részletesebben ismerteti a kvóta növelésének folyamatát.

Ha a kvóta megnövekedését kéri, ki kell választania azt a szolgáltatást, amelyre a kvótát fel kívánja emelni, ami olyan szolgáltatások, mint például a Machine Learning szolgáltatási kvóta, a tároló példányai vagy a tárolási kvóta. A Azure Machine Learning számítás mellett **a kvóta megtekintése gombra is** kattinthat, miközben a fenti lépéseket követve megtekinti a kvótát.

> [!NOTE]
> Az [ingyenes próbaverziós előfizetések](https://azure.microsoft.com/offers/ms-azr-0044p) nem jogosultak a korlát vagy a kvóta növelésére. Ha [ingyenes próbaverziós előfizetéssel](https://azure.microsoft.com/offers/ms-azr-0044p)rendelkezik, [az utólagos](https://azure.microsoft.com/offers/ms-azr-0003p/) elszámolású előfizetésre válthat. További információ: az [Azure ingyenes próbaverziójának frissítése az](../billing/billing-upgrade-azure-subscription.md) utólagos elszámolású és az [ingyenes próbaverziós előfizetésre vonatkozó gyakori kérdések](https://azure.microsoft.com/free/free-account-faq).
