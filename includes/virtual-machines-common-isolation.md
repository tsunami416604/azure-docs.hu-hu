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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77123208"
---
Az Azure Compute olyan virtuálisgép-méreteket kínál, amelyek egy adott hardvertípusra vannak elkülönítve, és egyetlen ügyfélnek vannak szentelve.  Ezek a virtuális gépméretek a legalkalmasabbak olyan számítási feladatokhoz, amelyek nagyfokú elkülönítést igényelnek más ügyfelektől olyan számítási feladatokhoz, amelyek olyan elemeket tartalmaznak, mint a megfelelőségi és szabályozási követelmények.  Az ügyfelek dönthetnek úgy is, hogy tovább osztják az elkülönített virtuális gépek erőforrásait a [beágyazott virtuális gépek Azure-támogatásával.](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)

Egy elkülönített méret kihasználva garantálja, hogy a virtuális gép lesz az egyetlen, amely az adott kiszolgálópéldányon fut.  Az aktuális elkülönített virtuálisgép-ajánlatok a következők:
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5
* Standard_DS15_v2
* Standard_D15_v2
* Standard_F72s_v2

Az egyes rendelkezésre álló elszigetelt méretekről [itt](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)olvashat bővebben.

## <a name="retiring-d15_v2ds15_v2-isolation-on-may-15-2020"></a>Nyugdíjba vonulási D15_v2/DS15_v2 elszigeteltség május 15,2020
**Frissítés 2020. február 10-én: Az "elszigeteltség" nyugdíjazási ütemtervét 2020.**

Az Azure dedikált gazdagép most GA, amely lehetővé teszi a szervezet Linux és Windows virtuális gépek futtatásához egy-bérlős fizikai kiszolgálókon. Azt tervezzük, hogy teljes mértékben lecseréli az elkülönített Azure-beli virtuális gépeket az Azure dedikált gazdagépre. **2020. május 15.-e** után a D15_v2/DS15_v2 Az Azure-beli virtuális gépek már nem lesznek hardvertől elkülönítve.

## <a name="how-does-this-affect-me"></a>Hogyan érint ez engem?
2020. május 15.-e után már nem biztosítunk elkülönítési garanciát a D15_v2/DS15_v2 Azure virtuális gépei számára. 

## <a name="what-actions-should-i-take"></a>Milyen lépéseket kell tetsbennem?
Ha a hardverelkülönítés nem szükséges az Ön számára, nincs szükség műveletre. 

Ha az elkülönítés szükséges az Ön számára, május 15, 2020, akkor kell vagy:

• [Migrálja](https://azure.microsoft.com/blog/introducing-azure-dedicated-host) a munkaterhelést az Azure dedikált gazdagépre.

• [Kérjen hozzáférést](https://aka.ms/D15iRequestAccess) egy D15i_v2 és DS15i_v2 Azure virtuális géphez, hogy ugyanazt az árteljesítményt kapja. Ez a beállítás csak a felosztó-kirovó és egyéves fenntartott példányforgatókönyvek esetén érhető el.    

• [Migrálja](https://azure.microsoft.com/blog/resize-virtual-machines/) a számítási feladatokat egy másik Azure-beli elszigetelt virtuális gépre. 

A részleteket lásd alább:

## <a name="timeline"></a>Idővonal
| Dátum | Műveletek | 
| --- | --- |
| 2019. november 18. | A D/DS15i_v2 elérhetősége (PAYG, 1 éves RI) |
| 2020. május 14.  | Utolsó nap vásárolni D / DS15i_v2 1 éves RI | 
| 2020. május 15.   | D/DS15_v2 elkülönítési garancia eltávolítva | 
| 2021. május 15.  | D/DS15i_v2 nyugdíjba vonulása (minden ügyfél, kivéve, aki 2019. november 18 előtt vásárolt a D/DS15_v2 3 éves RI-t)| 
| 2022. január 25.  | A D/DS15i_v2 nyugdíjba vonulása, ha a 3 éves rit-ek elvégezhetők (azon ügyfelek esetében, akik 2019. november 18.-a előtt vásárolták a D/DS15_v2 3 éves RI-t) | 

## <a name="faq"></a>GYIK
### <a name="q-is-the-size-dds15_v2-going-to-get-retired"></a>K: A D/DS15_v2 mérete nyugdíjba megy?
**A**: Nem, csak az "elkülönítés" funkció lesz nyugdíjas. Ha nincs szüksége elkülönítésre, nem kell semmilyen műveletet végrehajtania.

### <a name="q-is-the-size-dds15i_v2-going-to-get-retired"></a>K: A D/DS15i_v2 mérete nyugdíjba megy?
**A**: Igen, a méret csak május 15-ig 2021. Azoknak az ügyfeleknek, akik 201 DS15i_v29. november 18.-ig vásároltak 3 éves ri-t d/DS15_v2,2022.

### <a name="q-why-am-i-not-seeing-the-new-dds15i_v2-sizes-in-the-portal"></a>K: Miért nem látom az új D/DS15i_v2 méretet a portálon?
**A**: Ha Ön aktuális D/DS15_v2 ügyfél, és az új D/DS15i_v2 méretet szeretné használni, töltse ki ezt az [űrlapot](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0FTPNXHdWpJlO27GE-bHitUMkZUWEFPNjFPNVgyMkhZS05FSzlPTzRIOS4u)

### <a name="q-why-i-am-not-seeing-any-quota-for-the-new-dds15i_v2-sizes"></a>K: Miért nem látok kvótát az új D/DS15i_v2 méretekre?
**A**: Ha Ön aktuális D/DS15_v2 ügyfél, és az új D/DS15i_v2 méretet szeretné használni, töltse ki ezt az [űrlapot](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0FTPNXHdWpJlO27GE-bHitUNU1XUkhZWkNXQUFMNEJWUk9VWkRRVUJPMy4u)

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>K: Mikor fog a többi elszigetelt méret nyugdíjba vonulni?
**A**: A méretek hivatalos leszerelése előtt 12 hónappal emlékeztetünk.

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>K: Van egy állásidő, amikor a virtuális gép egy nem elszigetelt hardveren landol?
**V:** Ha nincs szüksége elkülönítésre, nem kell semmilyen műveletet végrehajtania, és nem lát leállást.

### <a name="q-are-there-any-cost-changes-for-moving-to-a-non-isolated-virtual-machine"></a>K: Vannak-e költségváltozások a nem elkülönített virtuális gépre való áthelyezéshez?
**A**: Nem 

### <a name="q-i-already-purchased-1--or-3-year-reserved-instance-for-d15_v2-or-ds15_v2-how-will-the-discount-be-applied-to-my-vm-usage"></a>K: Már vásároltam 1- vagy 3 éves fenntartott példányt D15_v2 vagy Ds15_v2. Hogyan lesz a kedvezmény a virtuális gép használatára?
**A**: 2019. november 18-a előtt vásárolt ris automatikusan kiterjeszti a lefedettséget az új elkülönített virtuálisgép-sorozatra. 

| RI |  Példányméret rugalmassága | Juttatásra való jogosultság |   
| --- | --- | --- |
|   D15_v2  |   Ki     |   D15_v2 és D15i_v2 |    
|   D15_v2  |   Bekapcsolva  |   D15_v2 sorozat és D15i_v2 mind megkapják a RI kedvezményt. |    
|   D14_v2  |   Bekapcsolva  |   D15_v2 sorozat és D15i_v2 mind megkapják a RI kedvezményt. |    
 
Hasonlóképpen a Dsv2 sorozat.
 
### <a name="q-i-want-to-purchase-additional-reserved-instances-for-dv2-which-one-should-i-choose"></a>K: További fenntartott példányokat szeretnék vásárolni a Dv2-höz. Melyiket válasszam?
**V**: A 2019. 

| RI |  Példányméret rugalmassága | Juttatásra való jogosultság |   
| --- | --- | --- |
| D15_v2 |  Ki |   D15_v2 csak  
| D15_v2 |  Bekapcsolva |    D15_v2 sorozat megkapja a RI kedvezményt. Az új D15i_v2 nem lesz jogosult a RI-kedvezményre ebben a ri-típusban. | 
| D15i_v2 |     Ki | D15i_v2 csak |  
| D15i_v2 |     Bekapcsolva  | D15i_v2 csak | 
 
A példányméret rugalmassága nem használható más méretekre, például D2_v2, D4_v2 vagy D15_v2. Hasonlóképpen, a Dsv2 sorozat.  
 
### <a name="q-can-i-buy-a-new-3-year-ri-for-d15i_v2-and-ds15i_v2"></a>K: Vásárolhatok új 3 éves ri-t D15i_v2 és DS15i_v2?
**A**: Sajnos nem, csak 1 éves RI érhető el az új vásárláshoz.
 
### <a name="q-can-i-move-my-existing-d15_v2ds15_v2-reserve-instance-to-an-isolated-size-reserved-instance"></a>K: Áthelyezhetem a meglévő D15_v2/DS15_v2 foglalási példányomat egy elkülönített méretű fenntartott példányra?
**A**: Ez a művelet nem szükséges, mivel az előny mind az elkülönített, mind a nem elkülönített méretekre vonatkozik. Az Azure azonban támogatja a meglévő D15_v2/DS15_v2 fenntartott példányok D15i_v2/DS15i_v2-ra való módosítását. Az összes többi Dv2/Dsv2 fenntartott példányok, használja a meglévő fenntartott példány, vagy új fenntartott példányok az elkülönített méretek.

### <a name="q-im-an-azure-service-fabric-customer-relying-on-the-silver-or-gold-durability-tiers-does-this-change-impact-me"></a>K: Az Azure Service Fabric-ügyfél vagyok, aki az ezüst vagy arany tartóssági szintekre támaszkodik. Hatással van rám ez a változás?
**A**: Nem. A Service Fabric [tartóssági szintjei](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) által nyújtott garanciák a módosítás után is működni fognak. Ha más okból is fizikai hardverelkülönítésre van szüksége, akkor is szükség lehet a fent leírt műveletek egyikére. 
