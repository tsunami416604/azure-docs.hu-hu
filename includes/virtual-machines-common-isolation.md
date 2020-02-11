---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: include
ms.date: 09/18/2019
ms.author: azcspmt;ayshak;cynthn
ms.custom: include file
ms.openlocfilehash: 3aeb0369ee4a04dbbe89a4d0684b2a6c97378d13
ms.sourcegitcommit: d12880206cf9926af6aaf3bfafda1bc5b0ec7151
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/10/2020
ms.locfileid: "77123208"
---
Az Azure-beli számítások olyan virtuálisgép-méreteket biztosítanak, amelyek egy adott hardvereszközhöz vannak elkülönítve, és egyetlen ügyfélhez vannak hozzárendelve.  Ezek a virtuálisgép-méretek olyan számítási feladatokhoz ideálisak, amelyek nagy fokú elkülönítést igényelnek más ügyfelektől olyan munkaterhelések esetén, mint például a megfelelőségi és szabályozási követelmények.  Az ügyfelek emellett dönthetnek úgy is, hogy a [beágyazott virtuális gépek Azure-támogatásának](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)használatával tovább alcsoportba helyezik az elkülönített virtuális gépek erőforrásait.

Az elkülönített méret kihasználása garantálja, hogy a virtuális gép az adott kiszolgálópéldány esetében csak egy fut.  A jelenlegi elkülönített virtuálisgép-ajánlatok a következők:
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5
* Standard_DS15_v2
* Standard_D15_v2
* Standard_F72s_v2

Az összes rendelkezésre álló elszigetelt méretről [itt](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)olvashat bővebben.

## <a name="retiring-d15_v2ds15_v2-isolation-on-may-15-2020"></a>D15_v2/DS15_v2 elkülönítés kivonása 2020. május 15-én
**Frissítés a 2020. február 10-én: az "elkülönítés" nyugdíjazási idővonala a 2020. május 15-én lett kiterjesztve.**

Az Azure dedikált gazdagép most már GA, amely lehetővé teszi a szervezet Linux és Windows rendszerű virtuális gépei futtatását egybérlős fizikai kiszolgálókon. Azt tervezzük, hogy az elkülönített Azure-beli virtuális gépeket teljesen lecseréljük az Azure dedikált gazdagépre. **2020. május 15** . után a D15_v2/DS15_v2 Azure-beli virtuális gépek többé nem lesznek elkülönítve.

## <a name="how-does-this-affect-me"></a>Hogyan érint ez engem?
A 2020. május 15. után már nem biztosítunk elkülönítési garanciát a D15_v2/DS15_v2 Azure-beli virtuális gépekhez. 

## <a name="what-actions-should-i-take"></a>Milyen műveleteket kell elvégeznie?
Ha a hardveres elkülönítés nem szükséges az Ön számára, nincs szükség beavatkozásra. 

Ha elkülönítésre van szükség, a 2020. május 15. előtt a következőkre lesz szüksége:

• A számítási feladatok [áttelepíthetők](https://azure.microsoft.com/blog/introducing-azure-dedicated-host) az Azure dedikált gazdagépre.

• [Hozzáférés kérése](https://aka.ms/D15iRequestAccess) egy D15i_v2hoz és DS15i_v2 Azure-beli virtuális géphez, hogy ugyanazt az árat lehessen elérni. Ez a lehetőség csak az utólagos elszámolású és az egy éves fenntartott példányok esetében érhető el.    

• A számítási feladatok [áttelepíthetők](https://azure.microsoft.com/blog/resize-virtual-machines/) egy másik Azure elkülönített virtuális gépre. 

Részletekért lásd alább:

## <a name="timeline"></a>Idővonal
| Dátum | Műveletek | 
| --- | --- |
| November 18., 2019 | D/DS15i_v2 rendelkezésre állása (TB, 1 éves RI) |
| Május 14., 2020  | Az elmúlt nap, hogy megvásárolja a D/DS15i_v2 1 éves RI | 
| Május 15., 2020   | D/DS15_v2 elkülönítési garancia eltávolítva | 
| Május 15., 2021  | A D/DS15i_v2 kivonása (az összes ügyfél, kivéve, ha a D/DS15_v2 3 éves RI-t vásárolt, 2019. november 18. előtt)| 
| November 17., 2022  | A (z) D/DS15i_v2 kivonása a 3 éves beérkező 2019 DS15_v2 példányok esetében | 

## <a name="faq"></a>GYIK
### <a name="q-is-the-size-dds15_v2-going-to-get-retired"></a>K: a D/DS15_v2 méret kivonásra kerül?
**A**: nem, a rendszer csak az "elkülönítés" funkciót fogja felvenni. Ha nincs szüksége elkülönítésre, semmilyen műveletet nem kell elvégeznie.

### <a name="q-is-the-size-dds15i_v2-going-to-get-retired"></a>K: a D/DS15i_v2 méret kivonásra kerül?
Válasz **: igen**, a méret csak a 2021. május 15-ig érhető el. Azon ügyfeleink számára, akik a 2019 november 18. előtt vásároltak a 3 éves RIs-t a D/DS15_v2-on, 2022 november 17-én elérik a D/DS15i_v2.

### <a name="q-why-am-i-not-seeing-the-new-dds15i_v2-sizes-in-the-portal"></a>K: Miért nem látom az új D/DS15i_v2 méreteket a portálon?
**A**: Ha aktuális d/DS15_v2 ügyfél, és az új d/DS15i_v2 méretet szeretné használni, töltse ki ezt az [űrlapot](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0FTPNXHdWpJlO27GE-bHitUMkZUWEFPNjFPNVgyMkhZS05FSzlPTzRIOS4u)

### <a name="q-why-i-am-not-seeing-any-quota-for-the-new-dds15i_v2-sizes"></a>K: Miért nem látok kvótát az új D/DS15i_v2 méretekhez?
**A**: Ha aktuális d/DS15_v2 ügyfél, és az új d/DS15i_v2 méretet szeretné használni, töltse ki ezt az [űrlapot](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0FTPNXHdWpJlO27GE-bHitUNU1XUkhZWkNXQUFMNEJWUk9VWkRRVUJPMy4u)

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>K: Mikor kell kivonni a többi elszigetelt méretet?
**A**: a méretek hivatalos leszerelése előtt 12 hónappal a rendszer emlékeztetőket biztosít.

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>K: van olyan állásidő, amikor a virtuális gép nem elszigetelt hardveren landol?
**A**: Ha nincs szüksége elkülönítésre, semmilyen műveletet nem kell elvégeznie, és nem fog semmilyen állásidőt látni.

### <a name="q-are-there-any-cost-changes-for-moving-to-a-non-isolated-virtual-machine"></a>K: módosulnak a nem elkülönített virtuális gépekre való áttérés?
**A**: nem 

### <a name="q-i-already-purchased-1--or-3-year-reserved-instance-for-d15_v2-or-ds15_v2-how-will-the-discount-be-applied-to-my-vm-usage"></a>K: már vásárolt 1 vagy 3 éves fenntartott példányt D15_v2 vagy Ds15_v2. Hogyan történik a kedvezmény alkalmazása a virtuális gépek használatára?
**A**: a 2019. november 18. előtt megvásárolt RIs-példányok automatikusan kiterjesztik a lefedettséget az új elkülönített virtuálisgép-sorozatra. 

| FENNTARTOTT példányok |  Példány méretének rugalmassága | Juttatások támogathatósága |   
| --- | --- | --- |
|   D15_v2  |   Ki     |   D15_v2 és D15i_v2 |    
|   D15_v2  |   Bekapcsolva  |   D15_v2 adatsorozat-és D15i_v2 az RI-juttatást is megkapja. |    
|   D14_v2  |   Bekapcsolva  |   D15_v2 adatsorozat-és D15i_v2 az RI-juttatást is megkapja. |    
 
Hasonlóképpen a Dsv2 sorozathoz.
 
### <a name="q-i-want-to-purchase-additional-reserved-instances-for-dv2-which-one-should-i-choose"></a>K: szeretnék további fenntartott példányokat vásárolni a Dv2. Melyiket válasszam?
**A**: a 2019. november 18. után megvásárolt összes RIs a következő viselkedéssel rendelkezik. 

| FENNTARTOTT példányok |  Példány méretének rugalmassága | Juttatások támogathatósága |   
| --- | --- | --- |
| D15_v2 |  Ki |   Csak D15_v2  
| D15_v2 |  Bekapcsolva |    A D15_v2 sorozat az RI juttatást fogja kapni. Az új D15i_v2 nem jogosult a ri-típusból származó RI juttatásra. | 
| D15i_v2 |     Ki | Csak D15i_v2 |  
| D15i_v2 |     Bekapcsolva  | Csak D15i_v2 | 
 
A példányok méretének rugalmassága nem használható más méretekre, például D2_v2re, D4_v2ra vagy D15_v2re. Hasonlóképpen, a Dsv2 sorozathoz.  
 
### <a name="q-can-i-buy-a-new-3-year-ri-for-d15i_v2-and-ds15i_v2"></a>K: vásárolhatok új, 3 éves D15i_v2 és DS15i_v2?
**A**: sajnos nem, csak 1 éves ri érhető el az új vásárláshoz.
 
### <a name="q-can-i-move-my-existing-d15_v2ds15_v2-reserve-instance-to-an-isolated-size-reserved-instance"></a>K: áthelyezhetem a meglévő D15_v2/DS15_v2 Reserve-példányt egy elkülönített méretre fenntartott példányra?
**A**: Ez a művelet nem szükséges, mivel a juttatás az elkülönített és a nem elkülönített méretekre is érvényes. Az Azure azonban támogatni fogja a meglévő D15_v2/DS15_v2 fenntartott példányok módosítását D15i_v2/DS15i_v2ra. Az összes többi Dv2/Dsv2 fenntartott példány esetében használja a meglévő fenntartott példányt, vagy vásároljon új fenntartott példányt az elkülönített méretekhez.

### <a name="q-im-an-azure-service-fabric-customer-relying-on-the-silver-or-gold-durability-tiers-does-this-change-impact-me"></a>K: Azure Service Fabric ügyfél vagyok az ezüst vagy az arany tartóssági szintjein. Hatással van ez a változás?
**A**: nem. A Service Fabric [tartóssági szintjei](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) által biztosított garanciák még a változás után is működni fognak. Ha más okokból fizikai hardveres elkülönítésre van szüksége, akkor továbbra is szükség lehet a fent ismertetett műveletek egyikére. 
