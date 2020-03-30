---
title: Red Hat Enterprise Linux-lemezképek az Azure-ban | Microsoft dokumentumok
description: Ismerje meg a Red Hat Enterprise Linux-lemezképeket a Microsoft Azure-ban.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: f06c4304be67fbc2f3116375dae33b10228723a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239879"
---
# <a name="overview-of-red-hat-enterprise-linux-images"></a>A Red Hat Enterprise Linux-képek áttekintése

Ez a cikk ismerteti a rendelkezésre álló Red Hat Enterprise Linux (RHEL) rendszerképek az Azure Marketplace-en és a szabályzatok körül a névadási és megőrzési.

A Red Hat támogatási irányelveiről az RHEL összes verziójára vonatkozóan a [Red Hat Enterprise Linux életciklusa című](https://access.redhat.com/support/policy/updates/errata)témakörben talál további információt. A díjszabással kapcsolatos részletekről az [Azure díjkalkulátora](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

>[!IMPORTANT]
> Az Azure Marketplace-en jelenleg elérhető RHEL-lemezképek támogatják a bring-your-own-subscription (BYOS) vagy a felosztó-kidolgozási alapú licencelési modelleket. Az [Azure Hybrid Use Benefit](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) és a BYOS és a használatalapú fizetéses licencelés közötti dinamikus váltás nem támogatott. A licencelési mód közötti váltáshoz újra kell telepítenie a virtuális gép a megfelelő lemezképről.

>[!NOTE]
> Az Azure Marketplace-en az RHEL-rendszerképekhez kapcsolódó problémák esetén nyújtson be támogatási jegyet a Microsoftnak.

## <a name="view-images-available-in-azure"></a>Az Azure-ban elérhető képek megtekintése

Amikor a "Red Hat" kifejezésre keres az Azure Marketplace-en, vagy amikor létrehoz egy erőforrást az Azure Portal felhasználói felületén, az összes elérhető RHEL-lemezképnek csak egy részhalmaza jelenik meg. Az Azure CLI, a PowerShell és az API használatával mindig beszerezheti a rendelkezésre álló virtuálisgép-lemezképek teljes készletét.

Az Azure-ban elérhető Red Hat-lemezképek teljes készletének megtekintéséhez futtassa a következő parancsot:

```azurecli-interactive
az vm image list --publisher RedHat --all
```

### <a name="naming-convention"></a>Elnevezési konvenció

Az Azure-beli virtuálisgép-lemezképek et közzétevő, ajánlat, termékváltozat és verzió szervezi. A Publisher:Offer:SKU:Version kombinációja a kép URN-je, és egyedileg azonosítja a használni hozandó képet.

Például `RedHat:RHEL:8-LVM:8.1.20200318` egy RHEL 8.1 LVM particionált lemezkép re épül március 18, 2020.

Az RHEL 8.1 virtuális gép létrehozásának mintája itt látható.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:8-LVM:8.1.20200318 --no-wait
```

### <a name="the-latest-moniker"></a>A "legújabb" becenév

Az Azure REST API lehetővé teszi a "legújabb" becenév használatát az adott verzió helyett. A "legújabb" rendelkezések az adott közzétevő, ajánlat és termékváltozat legújabb elérhető rendszerképének használatával.

Például `RedHat:RHEL:8-LVM:latest` a legújabb RHEL 8 család LVM-particionált lemezkép érhető el.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:8-LVM:latest --no-wait
```

>[!NOTE]
> Általánosságban elmondható, hogy a legújabb verziók összehasonlítása az [CompareTo módszer](https://msdn.microsoft.com/library/a5ts8tb6.aspx)szabályait követi .
Ez a képverzió-összehasonlítás az értékek [Version](https://docs.microsoft.com/dotnet/api/system.version.-ctor?view=netframework-4.8) verzióobjektumként, nem pedig karakterláncként való összehasonlításával történik.

## <a name="rhel-6-image-types"></a>RHEL 6 képtípusok

Rhel 6.x képek esetén a képtípusok az alábbi táblázatban láthatók.

|Közzétevő | Ajánlat | Termékváltozat értéke | Verzió | Részletek
|----------|-------|-----------|---------|--------
|RedHat | RHEL | Alverzió (például 6.9) | Az RHEL alváltozat és a közzététel dátumának összefont értékei (például 2018010506.6.9.) | Minden szabványos RHEL 6.x kép követi ezt az egyezményt.
|RedHat | rhel-byos | rhel-raw69 | Az RHEL alváltozat és a közzététel dátumának összefont értékei (például 20181023.9.6.) | Ez a kép egy RHEL 6.9 BYOS kép.
|RedHat | RHEL | RHEL-SAP-ALKALMAZÁSOK | Az RHEL alváltozat és a közzététel dátumának összefont értékei (például 2017053118.6.6.) | Ez a lemezkép egy RHEL 6.8 AZ SAP Applications lemezkép. Jogosult az SAP-alkalmazások tárházaihoz és az RHEL-tárolókhoz való hozzáférésre.
|RedHat | RHEL | RHEL-SAP-HANA | Az RHEL alváltozat és a közzététel dátumának összefont értékei (például 2017053121.6.) | Ez a lemezkép egy RHEL 6.7 AZ SAP HANA-lemezkép. Az SAP HANA-tárolók és az alap RHEL-adattárak elérésére jogosult.

## <a name="rhel-7-image-types"></a>RHEL 7 képtípusok

Rhel 7.x képek esetén többféle képtípus létezik. Az alábbi táblázat az általunk kínált különböző képkészleteket mutatja be. A teljes lista megtekintéséhez használja az `az vm image list --publisher redhat --all`Azure CLI parancsot.

>[!NOTE]
> Eltérő rendelkezés hiányában az összes lemezkép LVM particionált, és rendszeres RHEL-tárolókhoz csatlakozik. Ez azt, hogy az adattárak nem kiterjesztett frissítési támogatás (EUS), és nem az SAP frissítése (E4S) szolgáltatások. A továbbiakban csak LVM-particionált képek közzétételére térünk át, de nyitottak a döntéssel kapcsolatos visszajelzésekre. Az SAP kiterjesztett frissítési támogatásáról és frissítési szolgáltatásairól a [Red Hat Enterprise Linux életciklusa](https://access.redhat.com/support/policy/updates/errata)című témakörben talál további információt.

|Közzétevő | Ajánlat | Termékváltozat értéke | Verzió | Részletek
|----------|-------|------------|---------|--------
|RedHat | RHEL | Alverzió (például 7.6) | Az RHEL alváltozat és a közzététel dátumának összefont értékei (például 2019102813.6.7.) | A 2019 áprilisa előtt közzétett képek a szabványos RHEL-adattárakhoz kapcsolódnak. A 2019 áprilisa után közzétett képek a Red Hat EUS-adattáraihoz kapcsolódnak, hogy lehetővé tegyék egy adott alverzió verziózárolását. Azok az ügyfelek, akik rendszeres adattárakat szeretnének használni, a 7 LVM vagy 7-RAW-t tartalmazó lemezképeket kell használniuk a Termékváltozat értékében (a részletek a következők). Az RHEL 7.7 és újabb képek LVM particionálva vannak. Az összes többi kép ebben a kategóriában nyers particionált.
|RedHat | RHEL | 7-RAW | Az RHEL alváltozat és a közzététel dátumának összefont értékei (például 2019102813.6.7.) | Ezek a képek nyers particionált (például nem logikai kötetek hozzáadása).
|RedHat | RHEL | 7-RAW-CI | Az RHEL alváltozat és a közzététel dátumának összefont értékei (például 2019072418.7.) | Ezek a képek nyers particionált (például nincs logikai kötetek hozzáadása), és a felhő-init kiépítése.
|RedHat | RHEL | 7-LVM | Az RHEL alváltozat és a közzététel dátumának összefont értékei (például 2019062414.6.7.) | Ezek a képek LVM particionált.
|RedHat | rhel-byos | rhel-{lvm,nyers} | Az RHEL alváltozat és a közzététel dátumának összefont értékei (például 201908.7.7) | Ezek a képek a RHEL 7 BYOS képek. Nem kapcsolódnak semmilyen adattárakhoz, és nem számítják fel az RHEL prémium díjat. Ha érdeklik a RHEL BYOS-képek, [kérjen hozzáférést.](https://aka.ms/rhel-byos) A termékváltozat értékei az alverzióval végződnek, és jelzik, hogy a lemezkép nyers vagy LVM particionált.SKU values end with the al version and or or or a image is raw or LVM partitioned. Például egy rhel-lvm77 Termékváltozat értéke lvm-peltacionált RHEL 7.7-es lemezképet jelez.
|RedHat | RHEL | RHEL-NEDV | Az RHEL alváltozat és a közzététel dátumának összefont értékei (például 2019071300.6.7.) | Ezek a képek RHEL SAP-képek. Jogosultak az SAP HANA és alkalmazások adattárak, valamint az RHEL E4S-adattárak elérésére. A számlázás magában foglalja az RHEL prémiumot és az SAP prémiumot az alap számítási díjon felül.
|RedHat | RHEL | RHEL-SAP-HA | Az RHEL alváltozat és a közzététel dátumának összefont értékei (például 2019062320. 7.7.) | Ezek a lemezképek RHEL sap magas rendelkezésre állású és frissítési szolgáltatások lemezképek. Jogosultak az SAP HANA és alkalmazások adattárak, valamint a magas rendelkezésre állású adattárak, valamint az RHEL E4S-adattárak elérésére. A számlázás magában foglalja az RHEL prémiumot, az SAP prémiumot és a magas rendelkezésre állású prémiumot az alap számítási díjon felül.
|RedHat | RHEL | RHEL-HA | Az RHEL alváltozat és a közzététel dátumának összefont értékei (például 2019062019.6.7.) | Ezek a képek RHEL-lemezképek, amelyek szintén jogosultak a magas rendelkezésre állású bővítmény elérésére. Az RHEL-en felül kissé extra díjat számítanak fel, és az alap számítási díjat a magas rendelkezésre állású kiegészítő prémiumnak köszönhetően.
|RedHat | RHEL | RHEL-SAP-ALKALMAZÁSOK | Az RHEL alváltozat és a közzététel dátumának összefont értékei (például 2017053118. 7.3. ) | Ezek a rendszerképek elavultak, mert az SAP-alkalmazások és az SAP HANA-tárolók az SAP-tárolókban vannak kombinálva. Ezek a képek RHEL AZ SAP-alkalmazások lemezképek. Jogosultak az SAP-alkalmazások adattáraihoz és az RHEL-tárolókhoz való hozzáférésre.
|RedHat | RHEL | RHEL-SAP-HANA | Az RHEL alváltozat és a közzététel dátumának összefont értékei (például 2018051421. 7.3.) | Ezek a rendszerképek elavultak, mert az SAP-alkalmazások és az SAP HANA-tárolók az SAP-tárolókban vannak kombinálva. Ezek a képek RHEL SAP HANA-lemezképek. Jogosultak az SAP HANA-adattárak és az rhel-tárolók elérésére.

## <a name="rhel-8-image-types"></a>RHEL 8 képtípusok

Az RHEL 8 képtípusok részletei az alábbiakban találhatók.

|Közzétevő | Ajánlat | Termékváltozat értéke | Verzió | Részletek
|----------|-------|------------|---------|--------
|RedHat | RHEL | 8 | Az RHEL alváltozat és a közzététel dátumának összefont értékei (például 20191023.0.0.8.) | Ezek a képek RHEL 8 LVM-particionált lemezképek, amelyek szabványos Red Hat-tárolókhoz kapcsolódnak.
|RedHat | RHEL | 8 generációs2 | Az RHEL alváltozat és a közzététel dátumának összefont értékei (például 20191024.0.8.) | Ezek a képek hyper-V generation 2 RHEL 8 LVM-particionált képek, amelyek szabványos Red Hat-tárolókhoz kapcsolódnak. Az Azure-ban a 2. [Support for Generation 2 VMs on Azure](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2)

## <a name="rhel-longer-support-add-ons"></a>RHEL hosszabb támogatási bővítmények

### <a name="extended-update-support"></a>Bővített frissítés támogatása

2019 áprilisától rhel-képek állnak rendelkezésre, amelyek alapértelmezés szerint az EUS-adattárakhoz kapcsolódnak. Az RHEL EUS-ról további információk a [Red Hat dokumentációjában](https://access.redhat.com/articles/rhel-eus)találhatók.

Az EUS-adattárakra való áttérés lehetséges és támogatott. A virtuális gép EUS-ra való váltásával kapcsolatos tudnivalókért, valamint az EUS támogatási élettartama megszűnésének dátumáról az [RHEL EUS és a verziózáras RHEL virtuális gépek](https://aka.ms/rhui-update#rhel-eus-and-version-locking-rhel-vms)című témakörben talál.

>[!NOTE]
> Az EUS nem támogatott az RHEL Extrákon. Ez azt jelenti, hogy ha olyan csomagot telepít, amely általában az RHEL Extrák csatornáról érhető el, akkor ezt nem fogja tudni megtenni az EUS-en. A Red Hat Extrák termékéletciklusáról a [Red Hat Enterprise Linux Extrák életciklusában](https://access.redhat.com/support/policy/updates/extras/)talál további információt.

#### <a name="differentiate-between-regular-and-eus-images"></a>Különbséget a rendszeres és az EUS képek

Azok az ügyfelek, akik EUS-adattárakhoz csatolt lemezképeket szeretnének használni, a termékváltozatban RHEL-alverziószámot tartalmazó RHEL-verziószámot tartalmazó RHEL-lemezképet kell használniuk.

Előfordulhat például, hogy a következő két RHEL 7.4 kép érhető el.

```bash
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7.6:7.6.2019102813
```

Ebben az `RedHat:RHEL:7.6:7.6.2019102813` esetben alapértelmezés szerint csatolva van az EUS-adattárakhoz. A termékváltozat értéke 7.4. Alapértelmezés `RedHat:RHEL:7-LVM:7.6.2019062414` szerint nem EUS-adattárakhoz kapcsolódik. A termékváltozat értéke 7-LVM.

A rendszeres (eus-alapú) adattárak használatához használjon olyan lemezképet, amely nem tartalmaz alverziószámot a termékváltozatban.

#### <a name="rhel-images-with-eus"></a>RHEL-képek az EUS-sel

Az alábbi táblázatban szereplő információk az EUS-adattárakhoz kapcsolódó RHEL-képekre vonatkoznak.

>[!NOTE]
> Az írás időpontjában csak az RHEL 7.4 és a későbbi alverziók támogatják az EUS-t. Az EUS már nem támogatott az RHEL <=7.3.
>
> Az RHEL EUS elérhetőségéről a [Red Hat Enterprise Linux életciklusa](https://access.redhat.com/support/policy/updates/errata)című témakörben talál további információt.

Alverzió |Példa az EUS-képre              |EUS státusz                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7,4      |RedHat:RHEL:7.4:7.4.2019041718 | A 2019 áprilisában és később közzétett képek alapértelmezés szerint EUS-t jelentenek.|
RHEL 7.5      |RedHat:RHEL:7.5:7.5.2019060305 | A 2019 júniusa és később közzétett képek alapértelmezés szerint EUS-t jelentenek. |
RHEL 7,6      |RedHat:RHEL:7.6:7.6.2019052206 | A 2019 májusát és később közzétett képek alapértelmezés szerint EUS-t jelentenek. |
RHEL 8,0      |N/A                            | A Red Hat-ban nem érhető el EUS.                               |

### <a name="update-services-for-sap"></a>Sap-szolgáltatások frissítése

Az SAP-lemezképek legújabb RHEL-lemezei csatlakoznak az SAP Solutions-előfizetések frissítése szolgáltatáshoz (E4S). Az E4S-ről további információt a Red Hat [dokumentációjában](https://access.redhat.com/support/policy/updates/errata#Update_Services_for_SAP_Solutions)talál.

#### <a name="rhel-images-with-e4s"></a>RHEL-képek E4S-sel

A 2019 decembere után létrehozott alábbi ajánlatok képei az E4S-adattárakhoz kapcsolódnak:

* RHEL-SAP (RHEL SAP-hoz)
* RHEL-SAP-HA (RHEL sap-hoz magas rendelkezésre állású és frissítési szolgáltatásokkal)

## <a name="other-available-offers-and-skus"></a>Egyéb elérhető ajánlatok és suk-ok

Az elérhető ajánlatok és a sk-ek teljes listája az előző táblázatban felsoroltakon túl további képeket is tartalmazhat. Például: `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`. Ezek az ajánlatok felhasználhatók adott piactéri megoldások támogatására. Vagy lehet közzé tenni az előzetesek és tesztelési célokra. Ezek bármikor, figyelmeztetés nélkül módosíthatók vagy eltávolíthatók. Csak akkor használja őket, ha a microsoft vagy a Red Hat nyilvánosan dokumentálja a jelenlétüket.

## <a name="publishing-policy"></a>Közzétételi házirend

A Microsoft és a Red Hat frissítési lemezképei új alverziók formájában jelennek meg, ahogy az bizonyos gyakori biztonsági rések és kitettségek (CVE-k) vagy alkalmi konfigurációs módosítások vagy frissítések kezelésére van szükség. Arra törekszünk, hogy a CVE-javítás kiadását vagy elérhetőségét követő három munkanapon belül a lehető leghamarabb naprakész képeket biztosítsunk.

Csak az aktuális kisebb kiadást frissítjük egy adott képcsaládban. Egy újabb alverzió kiadásával leállítjuk a régebbi alverzió frissítését. Például az RHEL 7.6 megjelenésével az RHEL 7.5 képek már nem frissülnek.

>[!NOTE]
> Az RHEL-alapú, felosztó-kiosztó rendszerképekből kiépített aktív Azure-virtuális gépek az Azure RHUI-hoz kapcsolódnak, és frissítéseket és javításokat kaphatnak, amint a Red Hat felszabadítja őket, és replikálják őket az Azure RHUI-ra. Az időzítés általában kevesebb, mint 24 órával a Red Hat hivatalos kiadása után. Ezek a virtuális gépek nem igényelnek új közzétett lemezképet a frissítések beszerzéséhez. Az ügyfelek teljes mértékben szabályozhatják, hogy mikor kezdeményezik a frissítést.

## <a name="image-retention-policy"></a>Képmegőrzési házirend

A jelenlegi szabályzat az összes korábban közzétett kép megtartása. Fenntartjuk a jogot, hogy eltávolítsuk azolyan képeket, amelyekről ismert, hogy bármilyen problémát okoznak. Például a későbbi platform- vagy összetevőfrissítések miatt helytelen konfigurációval rendelkező képek et eltávolíthatja a rendszer. Eltávolítható képek kövesse a jelenlegi Azure Marketplace-szabályzatot, hogy a kép eltávolítása előtt legfeljebb 30 nappal értesítéseket biztosítson.

## <a name="next-steps"></a>További lépések

* Az Azure-ban elérhető RHEL-lemezképek teljes listájának megtekintéséhez tekintse meg [az Azure-ban elérhető Red Hat Enterprise Linux (RHEL) lemezképeket.](./redhat-imagelist.md)
* Ha többet szeretne megtudni az Azure Red Hat update infrastruktúrájáról, olvassa el a [Red Hat frissítési infrastruktúra az Azure-ban igény szerinti RHEL virtuális gépekhez című témakört.](https://aka.ms/rhui-update)
* Ha többet szeretne megtudni az RHEL BYOS ajánlatról, olvassa el a [Red Hat Enterprise Linux bring-your-own-subscription Gold Images az Azure-ban című témakört.](./byos.md)
* A Red Hat támogatási irányelveiről az RHEL összes verziójára vonatkozóan a [Red Hat Enterprise Linux életciklusa című](https://access.redhat.com/support/policy/updates/errata)témakörben talál további információt.
