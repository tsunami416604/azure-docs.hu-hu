---
title: Erőforrások kezelése & kvóták
titleSuffix: Azure Machine Learning
description: Ismerje meg a Azure Machine Learning erőforrásokra vonatkozó kvótákat, valamint a további kvóták igénylését.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 05/08/2020
ms.topic: conceptual
ms.custom: troubleshooting,contperfq4
ms.openlocfilehash: a25dcc187c1bb172106a3972c1cb57dfd473bc2f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91322376"
---
# <a name="manage--increase-quotas-for-resources-with-azure-machine-learning"></a>Erőforrások kezelése & az erőforrásokra vonatkozó kvóták növelése Azure Machine Learning


Ebből a cikkből megismerheti az Azure-erőforrások előre konfigurált korlátozásait a [Azure Machine learning](overview-what-is-azure-ml.md) -előfizetéshez, valamint a felügyelhető kvótákat. Ezeket a korlátozásokat a rendszer a csalás miatt, illetve az Azure-kapacitás megkötések tiszteletben tartásának megelőzése érdekében helyezi el. 

A többi Azure-szolgáltatáshoz hasonlóan a Azure Machine Learninghöz kapcsolódó erőforrások bizonyos korlátai is vannak. Ezek a korlátok a [munkaterületek](concept-workspace.md) számának korlátján alapulnak, amelyek a modell betanításához vagy a következtetésekhez vagy pontozáshoz használt tényleges számítási feladatokra korlátozzák. 

A Azure Machine Learning-erőforrások éles számítási feladatokhoz való tervezése és méretezése során ezeket a korlátokat érdemes figyelembe venni. Ha például a fürt nem éri el a csomópontok megcélzott számát, akkor előfordulhat, hogy elérte az előfizetéséhez Azure Machine Learning számítási magokra vonatkozó korlátot. Ha az alapértelmezett korlátnál magasabb korlátot vagy kvótát szeretne növelni, nyisson meg egy online ügyfélszolgálati kérést díjmentesen. Az Azure-kapacitás megkötései miatt a határértékek nem állíthatók be az alábbi táblázatokban látható maximális határérték fölé. Ha nincs maximális korlát oszlop, akkor az erőforrás nem rendelkezik állítható korlátokkal.


A kvóták kezelése mellett azt is megtudhatja, hogyan [tervezheti meg a Azure Machine learning költségeinek & kezelését](concept-plan-manage-cost.md).

## <a name="special-considerations"></a>Különleges szempontok

+ A kvóta egy hitelkeret, nem pedig a kapacitási garancia. Ha nagy méretű kapacitásra van szüksége, forduljon az Azure ügyfélszolgálatához. [Növelheti a kvótákat](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors)is.

+ A kvóta az előfizetésekben található összes szolgáltatás között meg van osztva, beleértve a Azure Machine Learningt is. Az egyetlen kivétel Azure Machine Learning számítás, amely az alapszintű számítási kvótától eltérő kvótával rendelkezik. Ügyeljen arra, hogy a kapacitási igények kiértékelése során számítsa ki a kvóta használatát az összes szolgáltatásban.

+ Az alapértelmezett korlátok az ajánlati kategória típusa szerint változnak, például az ingyenes próbaverzió, az utólagos elszámolású és a virtuálisgép-sorozatok, például a Dv2, az F, a G stb.

## <a name="default-resource-quotas"></a>Alapértelmezett erőforrás-kvóták

Itt látható az Azure-előfizetésben lévő különböző erőforrástípusok kvótáinak korlátozása.

> [!IMPORTANT]
> A korlátozások változhatnak. A legújabbak mindig az összes Azure szolgáltatási szintű kvóta [dokumentumában](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits/) találhatók.

### <a name="virtual-machines"></a>Virtual machines (Virtuális gépek)
Az egyes Azure-előfizetések esetében korlátozva van a virtuális gépek száma a szolgáltatásokban vagy az önálló szolgáltatások között. A virtuális gépek magjai a regionális teljes korláttal és a (Dv2, F stb.) korláttal rendelkeznek, és mindkettő külön kényszerített. Például tegyük fel, hogy egy előfizetés az USA keleti régiójára vonatkozó teljes magkorlátja 30, az A sorozatú magkorlátja 30, és a D sorozatú magkorlátja is 30. Ez az előfizetés üzembe helyezhet 30 A1 virtuális gépet, 30 D1 virtuális gépet, vagy a kettő valamilyen kombinációját, amely nem haladja meg az összesen 30 magot (pl. 10 A1 virtuális gép és 20 D1 virtuális gép).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning Compute
[Azure Machine learning számítás](concept-compute-target.md#azure-machine-learning-compute-managed)esetében az előfizetések régiónként legfeljebb a magok számának és az egyes régiókban engedélyezett egyedi számítási erőforrások számának az alapértelmezett kvótája. Ez a kvóta különbözik a fenti virtuálisgép-alapkvótatől, és az alapvető korlátok nincsenek megosztva a két erőforrástípus között, mivel a AmlCompute egy felügyelt szolgáltatás, amely erőforrásokat helyez üzembe egy szolgáltatott modellben.

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
| Feladatok maximális élettartama | 21 nap<sup>1</sup> |
| Feladat maximális élettartama alacsony prioritású csomóponton | 7 nap<sup>2</sup> |
| Paraméteres kiszolgálók maximális száma csomóponton | 1 |

<sup>1</sup> a maximális élettartam a Futtatás kezdetének és befejezésének időpontjára utal. A befejezett futtatások határozatlan ideig tartanak; nem érhető el a maximális élettartamon belül nem befejezett futtatások adatkészletei.
<sup>2</sup> az alacsony prioritású csomóponton lévő feladatok bármikor előzik, ha van kapacitási korlátozás. Javasoljuk, hogy hajtsa végre az ellenőrzőpontok megvalósítását a feladatokban.

### <a name="azure-machine-learning-pipelines"></a>Azure Machine Learning folyamatok
[Azure Machine learning folyamatok](concept-ml-pipelines.md)esetében a folyamat lépéseinek száma, valamint az előfizetésben régiónként megjelenő közzétett folyamatok ütemezett futtatásának száma.
- A folyamatokban engedélyezett lépések maximális száma 30 000
- Az ütemezett futtatások és a blob-lekérések összegének maximális száma az előfizetéshez tartozó közzétett folyamatokra vonatkozóan, havonta 100 000

### <a name="container-instances"></a>Tárolópéldányok

Az adott időszakban (óránkénti hatókörben) vagy a teljes előfizetésen belül elforgatható tároló-példányok száma is korlátozott.
A korlátokat lásd: [Container instances korlátok](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#container-instances-limits).

### <a name="storage"></a>Tárolás
A Storage-fiókok száma régiónként és egy adott előfizetésben is korlátozott. Az alapértelmezett korlát 250, és a standard és a Premium Storage fiókot is tartalmazza. Ha egy adott régióban több mint 250 Storage-fiókra van szüksége, az [Azure támogatási szolgálatán](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/)keresztül teheti meg a kérést. Az Azure Storage csapata áttekinti az üzleti ügyeit, és akár 250 Storage-fiókot is jóváhagyhat egy adott régióban.


## <a name="workspace-level-quota"></a>Munkaterület-szintű kvóta

A különböző [munkaterületek](concept-workspace.md)közötti Azure Machine learning számítási cél (Amlcompute) erőforrás-kiosztásának jobb kezelése érdekében egy olyan funkciót vezettünk be, amely lehetővé teszi az előfizetési szintű kvóták (VM-család) terjesztését, és a munkaterület szintjén történő konfigurálását. Az alapértelmezett viselkedés az, hogy minden munkaterületnek ugyanaz a kvótája, mint bármely virtuálisgép-család előfizetési szintjének kvótája. Azonban ahogy a munkaterületek száma nő, és a különböző prioritású munkaterhelések megoszthatják ugyanazokat az erőforrásokat, a felhasználók szeretnék jobban megosztani a kapacitást, és elkerülni az erőforrás-tartalomkezelési problémákat. Azure Machine Learning megoldást biztosít a felügyelt számítási ajánlattal, mivel lehetővé teszi, hogy a felhasználók maximális kvótát állítsanak be egy adott virtuálisgép-család számára az egyes munkaterületeken. Ez hasonló a kapacitás munkaterületek közötti elosztásához, és a felhasználók dönthetnek úgy is, hogy túlterhelik a maximális kihasználtságot. 

Ha a kvótákat a munkaterület szintjén szeretné beállítani, lépjen az előfizetés bármely munkaterületére, majd a bal oldali ablaktáblán kattintson a **kihasználtságok + kvóták** elemre. Ezután a **kvóták konfigurálása** lapon megtekintheti a kvótákat, kibonthatja a virtuálisgép-családokat, és beállíthatja az adott virtuálisgép-családban felsorolt munkaterületekre vonatkozó kvóta korlátját. Ne feledje, hogy nem állítható be negatív érték, vagy az előfizetési szint kvótájának magasabb értéke. Emellett, ahogy azt is megfigyelheti, alapértelmezés szerint minden munkaterülethez a teljes előfizetési kvóta van hozzárendelve, hogy lehetővé váljon a lefoglalt kvóta teljes kihasználtsága.

[![Azure Machine Learning munkaterület-szintű kvóta](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)


> [!NOTE]
> Az előfizetési szintű engedélyekre van szükség a kvóta beállításához a munkaterület szintjén. Ezt úgy kell kikényszeríteni, hogy az egyes munkaterület-tulajdonosok ne szerkesszék vagy növeljék a kvótákat, és a csorbítaná a másik munkaterületre elkülönített erőforrásokra indítsák. Ennek megfelelően az előfizetés-adminisztrátor a legmegfelelőbb a kvóták munkaterületek közötti kiosztásához és terjesztéséhez.



## <a name="view-your-usage-and-quotas"></a>A használat és a kvóták megtekintése

Az előfizetéshez Azure Machine Learning számítási kvótát a többi Azure-erőforrás kvótától függetlenül kezeljük. A kvóta megtekintéséhez le kell bontania Machine Learning szolgáltatásokat.  

1. A bal oldali ablaktáblán válassza a **Machine learning szolgáltatás** lehetőséget, majd válassza ki a kívánt munkaterületet a listából.

2. A következő **panelen a támogatás + hibaelhárítás szakaszban** válassza a **használat + kvóták** lehetőséget a jelenlegi kvóta-korlátok és-használat megtekintéséhez.

3. Válasszon egy előfizetést a kvóta korlátainak megtekintéséhez. Ne felejtse el szűrni az Önt érdeklő régiót.

4. Mostantól válthat egy előfizetési szint nézet és egy munkaterület szintű nézet között:
    + **Előfizetési nézet:** Ez lehetővé teszi, hogy megtekintse az alapszintű kvóta használatát a virtuálisgép-család számára, bővítse azt munkaterületen, és bővítse azt a tényleges fürt nevével. Ez a nézet optimális megoldást nyújt az adott virtuálisgép-család alapvető használatának részleteire, így a munkaterületek által felhasználható munkaterületek és a mögöttes fürtök is megjelennek. A nézet általános egyezménye (használat/kvóta), ahol a használat a Felskálázási magok aktuális száma, a kvóta pedig az erőforrás által méretezhető magok logikai maximális száma. Az egyes **munkaterületek**esetében a kvóta a munkaterület szintjének kvóta (a fentebb leírtak szerint), amely az adott virtuálisgép-család számára méretezhető magok maximális számát jelöli. A **fürthöz** hasonlóan a kvóta valójában a fürt által a max_nodes tulajdonság által definiált csomópontok maximális számának megfelelő magok mérete.
    
    + **Munkaterület nézet:** Így megtekintheti az alapszintű kvóta használatát a munkaterület alapján, kiterjesztheti a virtuálisgép-családot, és tovább bővítheti a tényleges fürt neve alapján. Ez a nézet optimális megoldást nyújt arra, hogy gyorsan bejusson egy adott munkaterülethez tartozó alapvető használat részleteibe, hogy láthassa a virtuálisgép-családok felbomlását, valamint az egyes családokhoz tartozó mögöttes fürtöket.

A kvóta megtekinthető a különböző Azure-erőforrások, például a Virtual Machines, a tárterület, a hálózat és a Azure Portal használatával.

1. A bal oldali ablaktáblán válassza a **minden szolgáltatás** lehetőséget, majd az általános kategóriában válassza az **előfizetések** lehetőséget.

2. Az előfizetések listájából válassza ki azt az előfizetést, amelynek a kvótáját keresi.

3. Válassza a **használat + kvóták** lehetőséget a jelenlegi kvóták korlátainak és használatának megtekintéséhez. A szűrők használatával válassza ki a szolgáltatót és a helyet. 

## <a name="request-quota-increases"></a>Kvótanövelések kérése

Ha az alapértelmezett korlátnál magasabb korlátot vagy kvótát szeretne növelni, [Nyisson meg egy online ügyfélszolgálati kérést](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) díjmentesen.

A határértékek nem állíthatók be a táblákban megjelenő maximális határérték fölé. Ha nincs maximális korlát, az erőforrás nem rendelkezik állítható korlátokkal. A [kvóta növelésével kapcsolatban lásd: lépésenkénti útmutató](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors).

Ha a kvóta megnövekedését kéri, ki kell választania azt a szolgáltatást, amelyre a kvótát fel kívánja emelni, ami olyan szolgáltatások, mint például a Machine Learning szolgáltatási kvóta, a tároló példányai vagy a tárolási kvóta. A Azure Machine Learning számítás mellett **a kvóta megtekintése gombra is** kattinthat, miközben a fenti lépéseket követve megtekinti a kvótát.

> [!NOTE]
> Az [ingyenes próbaverziós előfizetések](https://azure.microsoft.com/offers/ms-azr-0044p) nem jogosultak a korlát vagy a kvóta növelésére. Ha [ingyenes próbaverziós előfizetéssel](https://azure.microsoft.com/offers/ms-azr-0044p)rendelkezik, [az utólagos](https://azure.microsoft.com/offers/ms-azr-0003p/) elszámolású előfizetésre válthat. További információ: az [Azure ingyenes próbaverziójának frissítése az](../billing/billing-upgrade-azure-subscription.md) utólagos elszámolású és az  [ingyenes próbaverziós előfizetésre vonatkozó gyakori kérdések](https://azure.microsoft.com/free/free-account-faq).

## <a name="private-endpoint-and-private-dns-quota-increases"></a>A magánhálózati végpont és a magánhálózati DNS-kvóta növekszik

Az előfizetésben létrehozható privát végpontok és saját DNS-zónák száma korlátozott. Míg a Azure Machine Learning erőforrásokat hoz létre az (ügyfél-) előfizetésben, bizonyos esetekben létrehozhat erőforrásokat egy Microsoft tulajdonú előfizetésben. A következő esetekben előfordulhat, hogy kvótát kell igényelnie a Microsoft tulajdonában lévő előfizetésben:

* __Privát hivatkozás engedélyezve munkaterülete ügyfél által felügyelt kulccsal (CMK)__
* __Azure Container Registry a virtuális hálózat mögötti munkaterülethez__
* Az __Azure Kubernetes Service-fürt saját munkaterülethez való csatolása__.

Ezen forgatókönyvek esetében a következő lépések végrehajtásával igényelhet támogatást:

1. [Hozzon létre egy Azure-támogatási kérést](/azure/azure-portal/supportability/how-to-create-azure-support-request#create-a-support-request) , és válassza ki az alábbi beállításokat az __alapok__ szakaszban:

    | Mező | Kiválasztás |
    | ----- | ----- |
    | Probléma típusa | Technikai |
    | Szolgáltatás | Saját szolgáltatások. A legördülő listában válassza a __Machine learning__ lehetőséget. |
    | Probléma típusa | Munkaterület beállítása, SDK és parancssori felület |
    | Probléma altípusa | Probléma a munkaterület kiépítésével vagy kezelésével |

2. A __részletek__ szakaszban a __Leírás__ mező használatával adja meg a használni kívánt Azure-régiót és a használni kívánt forgatókönyvet. Ha több előfizetés esetében is kvótát kell megnövelnie, akkor ebben a mezőben az előfizetési azonosítók is szerepelnek.

3. A kérelem létrehozásához használja a __create (létrehozás__ ) lehetőséget.

## <a name="next-steps"></a>Következő lépések

+ [A Azure Machine Learning költségeinek megtervezése & kezeléséhez](concept-plan-manage-cost.md)
