---
title: Red Hat Enterprise Linux rendszerképek az Azure-ban | Microsoft Docs
description: Ismerkedjen meg Red Hat Enterprise Linux rendszerképekkel Microsoft Azure
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
ms.openlocfilehash: 5ed5d9337dd4e7acdbba25c4cb66d2690793f250
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77134395"
---
# <a name="overview-of-red-hat-enterprise-linux-images"></a>Red Hat Enterprise Linux rendszerképek áttekintése
Ez a cikk az Azure Marketplace-en elérhető Red Hat Enterprise Linux-(RHEL-) rendszerképeket ismerteti, valamint az elnevezési és adatmegőrzési szabályzatok körét.

A Red Hat-támogatási házirendekkel kapcsolatos információk a RHEL összes verziójára vonatkozóan a [Red Hat Enterprise Linux életciklus](https://access.redhat.com/support/policy/updates/errata) oldalon találhatók. A díjszabással kapcsolatos információkért tekintse meg az [Azure díjszabási számológépét](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

>[!IMPORTANT]
> Az Azure Marketplace-en jelenleg elérhető RHEL-lemezképek a saját előfizetéssel (BYOS) vagy az utólagos elszámolású (TB) licencelési modellekkel rendelkeznek. A BYOS és a TB közötti [Azure Hybrid use Benefit](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) és dinamikus váltás nem támogatott. A licencelési mód váltásához újra kell telepíteni a virtuális gépet a megfelelő rendszerképből.

>[!NOTE]
> Az Azure Marketplace-en található RHEL-lemezképekkel kapcsolatos bármilyen probléma esetén küldjön egy támogatási jegyet a Microsofttal.

## <a name="viewing-images-available-in-azure"></a>Az Azure-ban elérhető rendszerképek megtekintése
Ha a piactéren a "Red Hat" kifejezésre keres, vagy ha Azure Portal felhasználói felületen hoz létre egy erőforrást, akkor csak az összes elérhető RHEL-lemezkép részhalmaza jelenik meg. Az Azure CLI/PowerShell/API használatával bármikor megszerezheti a rendelkezésre álló virtuálisgép-lemezképek teljes készletét.

Az Azure-ban elérhető Red Hat-lemezképek teljes készletének megtekintéséhez futtassa a következő parancsot:

```azurecli-interactive
az vm image list --publisher RedHat --all
```

### <a name="naming-convention"></a>Elnevezési konvenció
Az Azure-beli virtuálisgép-rendszerképeket a kiadó, az ajánlat, az SKU és a verzió rendezi. A kiadó: ajánlat: SKU: Version a (z) a rendszerkép URN kombinációja, amely egyedileg azonosítja a használni kívánt rendszerképet.

`RedHat:RHEL:7-LVM:7.6.2018103108` például egy RHEL 7,6 LVM-particionált, 2018. október 31-én készített rendszerképet jelöl.

Alább látható egy példa arra, hogyan hozhat létre RHEL 7,6 virtuális gépet.
```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-LVM:7.6.2018103108 --no-wait
```

### <a name="the-latest-moniker"></a>A "legfrissebb" moniker
Az Azure REST API lehetővé teszi a "legújabb" moniker használatát az adott verzió helyett. A "legfrissebb" használatával kiépítheti az adott közzétevő, ajánlat és SKU legújabb elérhető lemezképét.

A `RedHat:RHEL:7-LVM:latest` például a legújabb RHEL 7 családhoz tartozó LVM-particionált lemezképre hivatkozik.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-LVM:latest --no-wait
```

>[!NOTE]
> Általánosságban elmondható, hogy a verziók összehasonlítása a legújabbat követi a [compareto metódus végrehajtása metódus](https://msdn.microsoft.com/library/a5ts8tb6.aspx)szabályainak megfelelően.
Ennek a rendszerkép-verziónak az összehasonlítását úgy végezheti el, hogy az értékeket [verzió](https://docs.microsoft.com/dotnet/api/system.version.-ctor?view=netframework-4.8) objektumként hasonlítja össze – nem karakterláncként.

## <a name="rhel-6-image-types"></a>RHEL 6 Képtípus
RHEL 6. x rendszerképek esetén a következő képtípusok érhetők el:

|Közzétevő | Ajánlat | SKU-érték | Verzió | Részletek
|----------|-------|-----------|---------|--------
|RedHat | RHEL | Másodlagos verzió (például 6,9) | A RHEL alverzió és a közzétett dátum összefűzött értékei (például 6.9.2018010506) | Az összes standard RHEL 6. x lemezkép követi ezt az egyezményt
|RedHat | RHEL – BYOS | RHEL – raw69 | A RHEL alverzió és a közzétett dátum összefűzött értékei (például 6.9.20181023) | Ez a rendszerkép egy RHEL 6,9 BYOS-rendszerkép.
|RedHat | RHEL | RHEL-SAP-APPS | A RHEL alverzió és a közzétett dátum összefűzött értékei (például 6.8.2017053118) | Ez egy RHEL 6,8 for SAP Applications-rendszerkép. Jogosult az SAP-alkalmazások tárházait és a Base RHEL-Tárházak elérésére.
|RedHat | RHEL | RHEL-SAP-HANA | A RHEL alverzió és a közzétett dátum összefűzött értékei (például 6.7.2017053121) | Ez egy RHEL 6,7 SAP HANA rendszerképhez. Jogosult a SAP HANA-Tárházak és a Base RHEL-Tárházak elérésére.

## <a name="rhel-7-image-types"></a>RHEL 7 Képtípus
A RHEL 7. x rendszerképek esetében van néhány különböző képtípus. Az alábbi táblázat az általunk kínált rendszerképek különböző készleteit mutatja be. A teljes listát az az CLI paranccsal lehet megtekinteni `az vm image list --publisher redhat --all`.

>[!NOTE]
> Ha másként nincs jelezve, az összes rendszerkép LVM-particionált, és a normál RHEL-adattárakhoz fog csatlakozni (azaz nem EUS, nem E4S). A jövőben csak az LVM-particionált lemezképek közzététele zajlik, de erre a döntésre nyitottak. A kiterjesztett frissítési támogatás és az SAP frissítési szolgáltatásai a [Red Hat Enterprise Linux életciklus lapon](https://access.redhat.com/support/policy/updates/errata)érhetők el.

|Közzétevő | Ajánlat | SKU-érték | Verzió | Részletek
|----------|-------|------------|---------|--------
|RedHat | RHEL | Másodlagos verzió (például 7,6) | A RHEL alverzió és a közzétett dátum összefűzött értékei (például 7.6.2019102813) | A 2019. április előtt közzétett rendszerképek a standard RHEL-adattárakhoz lesznek csatolva. Az április 2019 után közzétett rendszerképeket a Red Hat Extended Update support (EUS) adattárakhoz csatoljuk, amelyek lehetővé teszik egy adott alverzió zárolását. Az ügyfeleknek, akik rendszeresen szeretnének adattárakat használni, az SKU-ban található 7-LVM vagy 7-nyers képeket kell használniuk (részletek alább). A RHEL 7,7-es és újabb verziói LVM-particionálva lesznek. A kategóriába tartozó összes többi rendszerkép nyers particionálású.
|RedHat | RHEL | 7 – NYERS | A RHEL alverzió és a közzétett dátum összefűzött értékei (például 7.6.2019102813) | Ezek a lemezképek nyers particionálású (azaz nem lettek hozzáadva logikai kötetek).
|RedHat | RHEL | 7-RAW-CI | A RHEL alverzió és a közzétett dátum összefűzött értékei (például 7.6.2019072418) | Ezek a lemezképek nyers particionálású (azaz nem lettek hozzáadva logikai kötetek), és a Cloud-init eszközt használják a kiépítés során.
|RedHat | RHEL | 7-LVM | A RHEL alverzió és a közzétett dátum összefűzött értékei (például 7.6.2019062414) | Ezeket a lemezképeket az LVM particionálja.
|RedHat | RHEL – BYOS | RHEL – {LVM, RAW} | A RHEL alverzió és a közzétett dátum összefűzött értékei (például 7.7.20190819) | Ezek a lemezképek a RHEL 7 BYOS rendszerképek. Nincsenek csatolva adattárakhoz, és nem számítanak fel díjat a prémium RHEL. Ha érdekli a RHEL BYOS-lemezképe, [kérjen hozzáférést](https://aka.ms/rhel-byos). Az SKU-értékek a másodlagos verzióval végződik, és azt jelzi, hogy a rendszerkép nyers vagy LVM-particionált. Például az RHEL-lvm77 SKU-értéke egy LVM-particionálású RHEL 7,7-rendszerképet jelez.
|RedHat | RHEL | RHEL-SAP | A RHEL alverzió és a közzétett dátum összefűzött értékei (például 7.6.2019071300) | Ezek a rendszerképek RHEL az SAP-lemezképekhez. Jogosultak a SAP HANA és az alkalmazások tárházára, valamint a RHEL E4S-Tárházak elérésére. A számlázás magában foglalja a RHEL Premiumot és az SAP Premiumot az alap számítási díj alapján.
|RedHat | RHEL | RHEL – SAP-HA | A RHEL alverzió és a közzétett dátum összefűzött értékei (például 7.6.2019062320) | Ezek a rendszerképek a magas rendelkezésre állású és a frissítési szolgáltatások RHEL az SAP számára. Jogosultak a SAP HANA és az alkalmazások tárházára, valamint a magas rendelkezésre állású Tárházak, valamint a RHEL E4S-adattárak elérésére. A számlázás magában foglalja a RHEL Premium, az SAP Premium és a prémium szintű díjat az alap számítási díj alapján.
|RedHat | RHEL | RHEL – HA | A RHEL alverzió és a közzétett dátum összefűzött értékei (például 7.6.2019062019) | Ezek olyan RHEL-lemezképek, amelyek a magas rendelkezésre állású bővítmény elérésére is jogosultak. A RHEL és az alap számítási díj után valamivel külön díjat számítunk fel, a prémium szintű bővítmény díja miatt.
|RedHat | RHEL | RHEL-SAP-APPS | A RHEL alverzió és a közzétett dátum összefűzött értékei (például 7.3.2017053118) | Ezek a rendszerképek elavultak, mivel az SAP-alkalmazások és a SAP HANA adattárak az SAP-adattárakba vannak egyesítve. Ezek RHEL az SAP-alkalmazások rendszerképeihez. Az SAP-alkalmazások tárházait, valamint az alap RHEL-Tárházak elérésére jogosultak.
|RedHat | RHEL | RHEL-SAP-HANA | A RHEL alverzió és a közzétett dátum összefűzött értékei (például 7.3.2018051421) | Ezek a rendszerképek elavultak, mivel az SAP-alkalmazások és a SAP HANA adattárak az SAP-adattárakba vannak egyesítve. Ezek RHEL for SAP HANA lemezképek. Jogosultak a SAP HANA-Tárházak és a Base RHEL-Tárházak elérésére.

## <a name="rhel-8-image-types"></a>RHEL 8 Képtípus
A RHEL 8 képtípusának részletei alább láthatók.

|Közzétevő | Ajánlat | SKU-érték | Verzió | Részletek
|----------|-------|------------|---------|--------
|RedHat | RHEL | 8 | A RHEL alverzió és a közzétett dátum összefűzött értékei (például 8.0.20191023) | Ezek a képek a standard Red Hat-tárházhoz csatlakoztatott RHEL 8,0 LVM-particionált képek.
|RedHat | RHEL | 8 – Gen2 | A RHEL alverzió és a közzétett dátum összefűzött értékei (például 8.0.20191024) | Ezek a lemezképek a Hyper-V 2. generációs RHEL 8,0 LVM-particionált rendszerképek a standard Red Hat-tárházhoz csatlakoztatva. További információ a 2. generációs virtuális gépekről az Azure-ban [itt](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2)érhető el.

## <a name="rhel-longer-support-add-ons"></a>A RHEL már támogatja a bővítményeket

### <a name="extended-update-support-eus"></a>Bővített frissítési támogatás (EUS)
Április 2019-én a RHEL-lemezképek elérhetők, amelyek alapértelmezés szerint a kiterjesztett frissítési támogatási (EUS) adattárakhoz vannak csatolva. A RHEL EUS kapcsolatos további részletek a [Red Hat dokumentációjában](https://access.redhat.com/articles/rhel-eus)olvashatók.

A EUS-tárházra való váltás lehetséges és támogatott. [Itt](https://aka.ms/rhui-update#rhel-eus-and-version-locking-rhel-vms)találhat útmutatást arról, hogyan válthat a virtuális gép EUs, és további részletekkel szolgálhat a EUs támogatásáról.

>[!NOTE]
> A EUS nem támogatott a RHEL-extrák esetében. Ez azt jelenti, hogy ha olyan csomagot telepít, amely általában az RHEL extrák csatornából érhető el, akkor a EUS-on nem fogja tudni elvégezni. [Itt](https://access.redhat.com/support/policy/updates/extras/)részletesen ismertetjük a Red Hat extrák termék életciklusát.

#### <a name="differentiating-between-regular-and-eus-images"></a>Különbséget tesz a normál és a EUS képek között.
Azok az ügyfelek, akik EUS-adattárakhoz csatolt képeket szeretnének használni, az SKU-ban található RHEL alverziószámot tartalmazó RHEL-lemezképet kell használniuk.

Előfordulhat például, hogy a következő két RHEL 7,4 lemezkép elérhető:
```bash
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7.6:7.6.2019102813
```
Ebben az esetben `RedHat:RHEL:7.6:7.6.2019102813` alapértelmezés szerint a EUS-adattárakhoz lesz csatolva (7,4-es SKU-érték), és a `RedHat:RHEL:7-LVM:7.6.2019062414` alapértelmezés szerint nem EUS adattárakhoz lesz csatolva (7-LVM SKU-érték).

Ha normál (nem EUS) adattárakat szeretne használni, helyezzen üzembe olyan lemezképet, amely nem tartalmaz alverziószámot az SKU-ban.

#### <a name="rhel-images-with-eus"></a>RHEL-lemezképek a EUS
A következő táblázat a EUS-tárakhoz kapcsolódó RHEL-lemezképekre vonatkozik.

>[!NOTE]
> Az írás időpontjában csak a 7,4-es és újabb alverziók RHEL EUS-támogatással rendelkeznek. A EUS már nem támogatott a RHEL < = 7,3 esetében.
>
> A RHEL EUS rendelkezésre állásáról további részleteket [itt](https://access.redhat.com/support/policy/updates/errata)találhat.

Másodlagos verzió |EUS-rendszerkép – példa              |EUS állapota                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7,4      |RedHat:RHEL:7.4:7.4.2019041718 | A 2019. április és újabb kiadású lemezképek alapértelmezés szerint EUS lesznek|
RHEL 7.5      |RedHat:RHEL:7.5:7.5.2019060305 | A 2019-es és újabb kiadású lemezképek alapértelmezés szerint EUS lesznek. |
RHEL 7,6      |RedHat:RHEL:7.6:7.6.2019052206 | Az 2019-es és újabb verziókon közzétett lemezképek alapértelmezés szerint EUS lesznek  |
RHEL 8,0      |N/A                            | Nem érhető el EUS a Red hat-ból                               |

### <a name="update-services-for-sap-e4s"></a>SAP-szolgáltatások (E4S) frissítése
Az SAP-lemezképek legújabb RHEL az SAP Solutions-előfizetések (E4S) frissítési szolgáltatásaihoz lesznek csatlakoztatva. A E4S kapcsolatos további információk a Red Hat [dokumentációjában](https://access.redhat.com/support/policy/updates/errata#Update_Services_for_SAP_Solutions)találhatók.

#### <a name="rhel-images-with-e4s"></a>RHEL-lemezképek a E4S
A 2019 december után létrehozott alábbi ajánlatokból származó lemezképek a E4S-adattárakhoz lesznek csatlakoztatva.

* RHEL-SAP (RHEL for SAP)
* RHEL-SAP-HA (RHEL az SAP-hez a HA-és frissítési szolgáltatásokkal)

## <a name="other-available-offers-and-skus"></a>Egyéb elérhető ajánlatok és SKU-ket
Az elérhető ajánlatok és SKU-ket teljes listája tartalmazhat további rendszerképeket, mint a fenti táblázatban láthatók, például `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`. Ezek az ajánlatok bizonyos Piactéri megoldások támogatásához használhatók, vagy az előzetes verziójú és tesztelési célokra közzétehető. Előfordulhat, hogy a figyelmeztetés nélkül bármikor módosíthatók vagy eltávolíthatók. Ne használja őket, hacsak a Microsoft vagy a Red Hat nem nyilvánosan dokumentálta a jelenlétét.

## <a name="publishing-policy"></a>Közzétételi szabályzat
A Microsoft és a Red Hat frissíti a lemezképeket, mivel a rendszer új alverziókat szabadít fel, amelyek meghatározott CVEs, vagy esetenként konfigurációs változások/frissítések esetén szükségesek. Arra törekszünk, hogy a lehető legrövidebb időn belül elérhetővé váljon a frissített rendszerképek – egy adott CVE-javítás kiadása vagy rendelkezésre állása után három munkanapon belül.

Egy adott rendszerkép-családban csak az aktuális alverziót frissítjük. Egy újabb alverzió kiadásával a régebbi alverzió frissítése nem áll le. Például a RHEL 7,6 kiadásával a RHEL 7,5 lemezképek már nem lesznek frissítve.

>[!NOTE]
> Az RHEL-as elszámolású képekből kiépített aktív Azure-beli virtuális gépek az Azure-RHUI csatlakoznak, és a Red Hat által kiadott és az Azure RHUI-ba replikált (általában kevesebb, mint 24 órán belül), a Red Hat-os kiadást követő 24 óránál rövidebb idő alatt is kaphatnak frissítéseket és javításokat. . Ezeknek a virtuális gépeknek nincs szüksége új közzétett rendszerképre a frissítések beszerzéséhez, és az ügyfelek teljes körű vezérléssel rendelkeznek a frissítés elindításához.

## <a name="image-retention-policy"></a>Képmegőrzési szabályzat
Aktuális szabályzatunk az összes korábban közzétett rendszerkép megőrzése. Fenntartjuk a jogot arra, hogy olyan rendszerképeket távolítson el, amelyek bármilyen típusú problémát okozhatnak. Előfordulhat például, hogy a rendszer a következő platform vagy összetevő frissítései miatt helytelen konfigurációval rendelkező lemezképeket távolít el. Az eltávolítható rendszerképeket az aktuális Piactéri szabályzat szerint kell megadni, hogy a kép eltávolítása előtt akár 30 napig is biztosítson értesítéseket.

## <a name="next-steps"></a>Következő lépések
* Tekintse meg a [RHEL-lemezképek teljes listáját az Azure-ban](./redhat-imagelist.md).
* További információ az Azure Red Hat [frissítési infrastruktúráról](https://aka.ms/rhui-update).
* További információ a [RHEL BYOS ajánlatáról](./byos.md).
* A Red Hat-támogatási házirendekkel kapcsolatos információk a RHEL összes verziójára vonatkozóan a [Red Hat Enterprise Linux életciklus](https://access.redhat.com/support/policy/updates/errata) oldalon találhatók.
