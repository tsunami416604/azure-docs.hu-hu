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
ms.openlocfilehash: 2251725597e44a15e421f33cc315be3fb1c7846f
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74485450"
---
Az Azure Compute olyan virtuálisgép-méreteket kínál, amelyek egy meghatározott hardvertípusban vannak elkülönítve, és egyetlen ügyfél számára vannak fenntartva.  Ezek a virtuálisgép-méretek olyan számítási feladatokhoz megfelelőek, amelyeket magas szintű izolációval szükséges elkülöníteni más ügyfelek számítási feladataitól, beleértve olyan elemeket is mint a megfelelőség vagy a jogszabályi előírások betartása.  Az ügyfelek emellett dönthetnek úgy is, hogy a [beágyazott virtuális gépek Azure-támogatásának](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)használatával tovább alcsoportba helyezik az elkülönített virtuális gépek erőforrásait.

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

## <a name="retiring-d15_v2ds15_v2-isolation-on-february-15-2020"></a>D15_v2/DS15_v2 elkülönítés kivonása 2020. február 15-én
Nemrég bejelentettük az Azure dedikált gazdagép előzetes verzióját, amely lehetővé teszi a szervezet Linux és Windows rendszerű virtuális gépei futtatását egybérlős fizikai kiszolgálókon. Azt tervezzük, hogy az elkülönített Azure-beli virtuális gépeket teljesen lecseréljük az Azure dedikált gazdagépre. A **2020. február 15** . után az Azure-beli virtuális gépek D15_v2/DS15_v2 már nem különíthető el a hardver.

## <a name="how-does-this-affect-me"></a>Hogyan érint ez engem?
2020. február 15-én már nem biztosítunk elkülönítési garanciát a D15_v2/DS15_v2 Azure-beli virtuális gépekhez. 

## <a name="what-actions-should-i-take"></a>Milyen műveleteket kell elvégeznie?
Ha a hardveres elkülönítés nem szükséges az Ön számára, nincs szükség beavatkozásra. 

Ha elkülönítésre van szükség, a 2020. február 15. előtt a következőkre lesz szüksége:

• A számítási feladatok [migrálása](https://azure.microsoft.com/blog/introducing-azure-dedicated-host) az Azure dedikált gazdagép előzetes verziójára

• [Hozzáférés kérése](https://aka.ms/D15iRequestAccess) egy D15i_v2hoz és DS15i_v2 Azure-beli virtuális géphez, hogy ugyanazt az árat lehessen elérni. Ez a lehetőség csak az utólagos elszámolású és az egy éves fenntartott példányok esetében érhető el.    

• A számítási feladatok [áttelepíthetők](https://azure.microsoft.com/blog/resize-virtual-machines/) egy másik Azure elkülönített virtuális gépre. 

Részletekért lásd alább:

## <a name="timeline"></a>Idővonal
| Dátum | Műveletek | 
| --- | --- |
| November 18., 2019  | D/DS15i_v2 rendelkezésre állása (TB, 1 éves RI) |
| Február 14., 2020  | Az elmúlt nap, hogy megvásárolja a D/DS15i_v2 1 éves RI | 
| Február 15., 2020   | D/DS15_v2 elkülönítési garancia eltávolítva | 
| Május 15., 2021  | A D/DS15i_v2 kivonása (az összes ügyfél, kivéve, ha a D/DS15_v2 3 éves RI-t vásárolt, 2019. november 18. előtt)| 
| November 17, 2022   | A (z) D/DS15i_v2 kivonása a 3 éves beérkező 2019 DS15_v2 példányok esetében | 

## <a name="faq"></a>GYIK
### <a name="q-why-am-i-not-seeing-the-new-dds15i_v2-sizes-in-the-portal"></a>K: Miért nem látom az új D/DS15i_v2 méreteket a portálon?
**A**: Ha aktuális d/DS15_v2 ügyfél, és az új d/DS15i_v2 méretet szeretné használni, töltse ki ezt az [űrlapot](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0FTPNXHdWpJlO27GE-bHitUMkZUWEFPNjFPNVgyMkhZS05FSzlPTzRIOS4u)

### <a name="q-why-i-am-not-seeing-any-quota-for-the-new-dds15i_v2-sizes"></a>K: Miért nem látok kvótát az új D/DS15i_v2 méretekhez?
**A**: Ha aktuális d/DS15_v2 ügyfél, és az új d/DS15i_v2 méretet szeretné használni, töltse ki ezt az [űrlapot](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0FTPNXHdWpJlO27GE-bHitUNU1XUkhZWkNXQUFMNEJWUk9VWkRRVUJPMy4u)

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>K: Mikor kell kivonni a többi elszigetelt méretet?
**A**: a méretek hivatalos leszerelése előtt 12 hónappal a rendszer emlékeztetőket biztosít.

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>K: van olyan állásidő, amikor a virtuális gép nem elszigetelt hardveren landol?
**A**: Ha nincs szüksége elkülönítésre, nincs szükség semmilyen műveletre, és nem fog semmilyen állásidőt látni.

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
**A**: ez nem szükséges, mivel a juttatás az elkülönített és a nem elkülönített méretekre is érvényes. Az Azure azonban támogatni fogja a meglévő D15_v2/DS15_v2 fenntartott példányok módosítását D15i_v2/DS15i_v2ra. Az összes többi Dv2/Dsv2 fenntartott példány esetében használja a meglévő fenntartott példányt, vagy vásároljon új fenntartott példányt az elkülönített méretekhez.

### <a name="q-im-a-azure-service-fabric-customer-relying-on-the-silver-or-gold-durability-tiers-does-this-change-impact-me"></a>K: Azure-Service Fabric ügyfél vagyok, amely az ezüst vagy az arany tartóssági szintjére támaszkodik. Hatással van ez a változás?
**A**: nem. A Service Fabric [tartóssági szintjei](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) által biztosított garanciák még a változás után is működni fognak. Ha más okokból fizikai hardveres elkülönítésre van szüksége, akkor továbbra is szükség lehet a fent ismertetett műveletek egyikére. 
