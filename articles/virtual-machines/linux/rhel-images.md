---
title: Red Hat Enterprise Linux rendszerképek az Azure-ban | Microsoft Docs
description: Ismerkedjen meg Red Hat Enterprise Linux rendszerképekkel Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 8/14/2019
ms.author: borisb
ms.openlocfilehash: 25b0ccfa5ed2b7b9ff031565fe7f14b1477769c4
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69543850"
---
# <a name="red-hat-enterprise-linux-images-in-azure"></a>Red Hat Enterprise Linux rendszerképek az Azure-ban
Ez a cikk az Azure Marketplace-en elérhető Red Hat Enterprise Linux-(RHEL-) rendszerképeket ismerteti, valamint az elnevezési és adatmegőrzési szabályzatok körét.

A Red Hat-támogatási házirendekkel kapcsolatos információk a RHEL összes verziójára vonatkozóan a [Red Hat Enterprise Linux életciklus](https://access.redhat.com/support/policy/updates/errata) oldalon találhatók.

>[!Important]
> Az Azure Marketplace-en jelenleg elérhető RHEL-lemezképek a saját előfizetéssel (BYOS) vagy az utólagos elszámolású (TB) licencelési modellekkel rendelkeznek. A BYOS és a TB közötti [Azure Hybrid use Benefit](../windows/hybrid-use-benefit-licensing.md) és dinamikus váltás nem támogatott. A licencelési mód váltásához újra kell telepíteni a virtuális gépet a megfelelő rendszerképből.

>[!Note]
> Az Azure Marketplace katalógusban található RHEL-lemezképekkel kapcsolatos bármilyen probléma esetén küldjön egy támogatási jegyet a Microsofttal.

## <a name="images-available-in-the-ui"></a>A felhasználói felületen elérhető rendszerképek
Ha a piactéren a "Red Hat" kifejezésre keres, vagy ha Azure Portal felhasználói felületen hoz létre erőforrást, akkor a rendelkezésre álló RHEL-lemezképek és a kapcsolódó Red Hat-termékek részhalmaza jelenik meg. Az Azure CLI/PowerShell/API használatával bármikor megszerezheti a rendelkezésre álló virtuálisgép-lemezképek teljes készletét.

Az Azure-ban elérhető Red Hat-lemezképek teljes készletének megtekintéséhez futtassa a következő parancsot:

```azurecli-interactive
az vm image list --publisher RedHat --all
```

## <a name="naming-convention"></a>Elnevezési konvenció
Az Azure-beli virtuálisgép-rendszerképeket a kiadó, az ajánlat, az SKU és a verzió rendezi. A kiadó: ajánlat: SKU: Version a (z) a rendszerkép URN kombinációja, amely egyedileg azonosítja a használni kívánt rendszerképet.

Például egy RHEL `RedHat:RHEL:7-RAW:7.6.2018103108` 7,6 nyers particionálású rendszerképre hivatkozik, amely 2018 október 31-ig épült.

Alább látható egy példa arra, hogyan hozhat létre RHEL 7,6 virtuális gépet.
```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-RAW:7.6.2018103108 --no-wait
```

### <a name="the-latest-moniker"></a>A "legfrissebb" moniker
Az Azure REST API lehetővé teszi a "legújabb" moniker használatát az adott verzió helyett. A "legfrissebb" használatával kiépítheti az adott közzétevő, ajánlat és SKU legújabb elérhető lemezképét.

Például a legújabb `RedHat:RHEL:7-RAW:latest` RHEL 7 családhoz tartozó nyers particionálású lemezképre hivatkozik.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-RAW:latest --no-wait
```

>[!NOTE]
> Általánosságban elmondható, hogy a verziók összehasonlítása a legújabbat követi a [compareto metódus végrehajtása metódus](https://msdn.microsoft.com/library/a5ts8tb6.aspx)szabályainak megfelelően.

### <a name="current-naming-convention"></a>Aktuális elnevezési konvenció
A jelenleg közzétett RHEL-lemezképek az utólagos elszámolású modellt használják, és a [Red Hat Update Infrastructure (RHUI)](https://aka.ms/rhui-update)szolgáltatáshoz csatlakoznak az Azure-ban. Új elnevezési konvenció lett elfogadva a RHEL 7 operációsrendszer-lemezképekben, amelyekben a lemez particionálási sémája (nyers, LVM) a verzió helyett a SKU-ban van megadva. A RHEL-rendszerkép verziója 7 nyers vagy 7-LVM lesz. A RHEL 6 családjának elnevezése jelenleg nem változott.

Ebben az elnevezési konvencióban két RHEL 7 rendszerkép található: Olyan SKU-ket, amelyek nem a másodlagos verziót és a SKU-t listázza. Ha 7 vagy 7 – LVM SKU-t szeretne használni, megadhatja a verzióban telepíteni kívánt RHEL alverziót. Ha a "legújabb" verziót választja, a RHEL legújabb kiadását fogja kiépíteni.

>[!NOTE]
> A RHEL for SAP-készletben a RHEL verziója rögzített marad. Ilyen esetben az elnevezési konvenció egy adott verziót tartalmaz az SKU-ban.

>[!NOTE]
> A RHEL 6 rendszerkép nem lett áthelyezve az új elnevezési konvencióba.

## <a name="extended-update-support-eus"></a>Bővített frissítési támogatás (EUS)
Április 2019-én a RHEL-lemezképek elérhetők, amelyek alapértelmezés szerint a kiterjesztett frissítési támogatási (EUS) adattárakhoz vannak csatolva. A RHEL EUS kapcsolatos további részletek a [Red Hat dokumentációjában](https://access.redhat.com/articles/rhel-eus)olvashatók.

[Itt](https://aka.ms/rhui-update#rhel-eus-and-version-locking-rhel-vms)találhat útmutatást arról, hogyan válthat a virtuális gép EUs, és további részletekkel szolgálhat a EUs támogatásáról.

>[!NOTE]
> A EUS nem támogatott a RHEL-extrák esetében. Ez azt jelenti, hogy ha olyan csomagot telepít, amely általában az RHEL extrák csatornából érhető el, akkor a EUS-on nem fogja tudni elvégezni. [Itt](https://access.redhat.com/support/policy/updates/extras/)részletesen ismertetjük a Red Hat extrák termék életciklusát.

### <a name="for-customers-that-want-to-use-eus-images"></a>EUS-lemezképeket használni kívánó ügyfelek esetén:
Azok az ügyfelek, akik EUS-adattárakhoz csatolt képeket szeretnének használni, az SKU-ban található RHEL alverziószámot tartalmazó RHEL-lemezképet kell használniuk. Ezek a lemezképek nyers particionálva lesznek (azaz nem LVM).

Előfordulhat például, hogy a következő 2 RHEL 7,4 lemezkép elérhető:
```bash
RedHat:RHEL:7-RAW:7.4.2018010506
RedHat:RHEL:7.4:7.4.2019041718
```
Ebben az esetben `RedHat:RHEL:7.4:7.4.2019041718` alapértelmezés szerint a EUs-adattárakhoz lesz csatolva, `RedHat:RHEL:7-RAW:7.4.2018010506` és alapértelmezés szerint a nem EUs adattárakhoz lesz csatolva.

### <a name="for-customers-that-dont-want-to-use-eus-images"></a>Azon ügyfelek esetében, akik nem szeretnék EUS-lemezképeket használni:
Ha nem szeretne olyan rendszerképet használni, amely alapértelmezés szerint a EUS van csatlakoztatva, telepítsen olyan lemezképet, amely nem tartalmaz alverziószámot az SKU-ban.

#### <a name="rhel-images-with-eus"></a>RHEL-lemezképek a EUS
A következő táblázat a SKU-ban található alverziót tartalmazó RHEL-lemezképekre vonatkozik.

>[!NOTE]
> Az írás időpontjában csak a 7,4-es és újabb alverziók RHEL EUS-támogatással rendelkeznek. A EUS már nem támogatott a RHEL < = 7,3 esetében.
>
> A RHEL EUS rendelkezésre állásáról további részleteket [itt](https://access.redhat.com/support/policy/updates/errata)találhat.

Másodlagos verzió |EUS-rendszerkép – példa              |EUS állapota                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7,4      |RedHat:RHEL:7.4:7.4.2019041718 | A 2019. április és újabb kiadású lemezképek alapértelmezés szerint EUS lesznek|
RHEL 7.5      |RedHat:RHEL:7.5:7.5.2019060305 | A 2019-es és újabb kiadású lemezképek alapértelmezés szerint EUS lesznek. |
RHEL 7,6      |RedHat:RHEL:7.6:7.6.2019052206 | Az 2019-es és újabb verziókon közzétett lemezképek alapértelmezés szerint EUS lesznek  |
RHEL 8,0      |–                            | Nem érhető el EUS a Red hat-ból                               |


## <a name="list-of-rhel-images-available"></a>Az elérhető RHEL-lemezképek listája
A következő ajánlatok jelenleg általános használatra elérhetők:

Ajánlat| SKU | Particionálás | Kiépítés | Megjegyzések
:----|:----|:-------------|:-------------|:-----
RHEL          | 7 – NYERS    | RAW    | Linux-ügynök | RHEL 7 operációsrendszer-család. <br> Alapértelmezés szerint nincs csatolva a EUS-adattárakhoz.
|             | 7-LVM    | LVM    | Linux-ügynök | RHEL 7 operációsrendszer-család. <br> Alapértelmezés szerint nincs csatolva a EUS-adattárakhoz.
|             | 7-RAW-CI | RAW-CI | Cloud-init  | RHEL 7 operációsrendszer-család. <br> Alapértelmezés szerint nincs csatolva a EUS-adattárakhoz.
|             | 6.7      | RAW    | Linux-ügynök | RHEL 6,7 lemezképek, régi elnevezési konvenció
|             | 6.8      | RAW    | Linux-ügynök | Ugyanaz, mint a fentiekben a RHEL 6,8
|             | 6.9      | RAW    | Linux-ügynök | Ugyanaz, mint a fentiekben a RHEL 6,9
|             | 6.10     | RAW    | Linux-ügynök | Ugyanaz, mint a fentiekben a RHEL 6,10
|             | 7.2      | RAW    | Linux-ügynök | Ugyanaz, mint a fentiekben a RHEL 7,2
|             | 7.3      | RAW    | Linux-ügynök | Ugyanaz, mint a fentiekben a RHEL 7,3
|             | 7.4      | RAW    | Linux-ügynök | Ugyanaz, mint a fentiekben a RHEL 7,4 esetében. <br> EUS-tárházhoz csatolva, alapértelmezés szerint április 2019
|             | 7.5      | RAW    | Linux-ügynök | Ugyanaz, mint a fentiekben a RHEL 7,5 esetében. <br> EUS-tárházhoz csatolva, alapértelmezés szerint június 2019
|             | 7,6      | RAW    | Linux-ügynök | Ugyanaz, mint a fentiekben a RHEL 7,6 esetében. <br> EUS-tárházhoz csatolva, alapértelmezés szerint a 2019-as számú
RHEL-SAP      | 7.4      | LVM    | Linux-ügynök | RHEL 7,4 SAP HANA és üzleti alkalmazások számára
|             | 7.5      | LVM    | Linux-ügynök | RHEL 7,5 SAP HANA és üzleti alkalmazások számára
RHEL-SAP-HANA | 6.7      | RAW    | Linux-ügynök | RHEL 6,7 SAP HANA
|             | 7.2      | LVM    | Linux-ügynök | RHEL 7,2 SAP HANA
|             | 7.3      | LVM    | Linux-ügynök | RHEL 7,3 SAP HANA
RHEL-SAP-APPS | 6.8      | RAW    | Linux-ügynök | RHEL 6,8 for SAP Business Applications
|             | 7.3      | LVM    | Linux-ügynök | RHEL 7,3 for SAP Business Applications
RHEL – HA       | 7.4      | RAW    | Linux-ügynök | RHEL 7,4 és HA bővítmény
|             | 7.5      | RAW    | Linux-ügynök | RHEL 7,5 és HA bővítmény
|             | 7,6      | RAW    | Linux-ügynök | RHEL 7,6 és HA bővítmény
RHEL – SAP-HA   | 7.4      | RAW    | Linux-ügynök | RHEL 7,4 for SAP és HA bővítmény
|             | 7.5      | RAW    | Linux-ügynök | RHEL 7,5 for SAP és HA bővítmény
|             | 7,6      | RAW    | Linux-ügynök | RHEL 7,6 for SAP és HA bővítmény

### <a name="old-naming-convention"></a>Régi elnevezési konvenció
A RHEL 7 rendszerképek és a RHEL 6 operációsrendszer-lemezképek az SKU-ban meghatározott verziókat használnak, egészen addig, amíg az elnevezési konvenció fentebb ismertetett változása nem következett be.

A teljes rendszerkép listában található numerikus SKU-ket fogja megtalálni. A Microsoft és a Red hat új, numerikus SKU-t hoz létre, ha új másodlagos kiadás érkezik.

### <a name="other-available-offers-and-skus"></a>Egyéb elérhető ajánlatok és SKU-ket
A rendelkezésre álló ajánlatok és SKU-elemek teljes listája tartalmazhat további rendszerképeket, mint például `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`a fenti táblázatban láthatók. Ezek az ajánlatok bizonyos Piactéri megoldások támogatásához használhatók, vagy az előzetes verziójú és tesztelési célokra közzétehető. Előfordulhat, hogy a figyelmeztetés nélkül bármikor módosíthatók vagy eltávolíthatók. Ne használja őket, hacsak a Microsoft vagy a Red Hat nem nyilvánosan dokumentálta a jelenlétét.

## <a name="publishing-policy"></a>Közzétételi szabályzat
A Microsoft és a Red Hat frissíti a lemezképeket, mivel a rendszer új alverziókat szabadít fel, amelyek meghatározott CVEs, vagy esetenként konfigurációs változások/frissítések esetén szükségesek. Arra törekszünk, hogy a lehető legrövidebb időn belül elérhetővé váljon a frissített rendszerképek – egy adott CVE-javítás kiadása vagy rendelkezésre állása után három munkanapon belül.

Egy adott rendszerkép-családban csak az aktuális alverziót frissítjük. Egy újabb alverzió kiadásával a régebbi alverzió frissítése nem áll le. Például a RHEL 7,6 kiadásával a RHEL 7,5 lemezképek már nem lesznek frissítve.

>[!NOTE]
> Az RHEL-as elszámolású képekből kiépített aktív Azure-beli virtuális gépek az Azure-RHUI csatlakoznak, és a Red Hat által kiadott és az Azure RHUI-ba replikált (általában kevesebb, mint 24 órán belül), a Red Hat-os kiadást követő 24 óránál rövidebb idő alatt is kaphatnak frissítéseket és javításokat. . Ezeknek a virtuális gépeknek nincs szüksége új közzétett rendszerképre a frissítések beszerzéséhez, és az ügyfelek teljes körű vezérléssel rendelkeznek a frissítés elindításához.

## <a name="image-retention-policy"></a>Képmegőrzési szabályzat
Aktuális szabályzatunk az összes korábban közzétett rendszerkép megőrzése. Fenntartjuk a jogot arra, hogy olyan rendszerképeket távolítson el, amelyek bármilyen típusú problémát okozhatnak. Előfordulhat például, hogy a rendszer a következő platform vagy összetevő frissítései miatt helytelen konfigurációval rendelkező lemezképeket távolít el. Az eltávolítható rendszerképeket az aktuális Piactéri szabályzat szerint kell megadni, hogy a kép eltávolítása előtt akár 30 napig is biztosítson értesítéseket.

## <a name="next-steps"></a>További lépések
* További információ az Azure Red Hat frissítési infrastruktúráról [](https://aka.ms/rhui-update).
* A Red Hat-támogatási házirendekkel kapcsolatos információk a RHEL összes verziójára vonatkozóan a [Red Hat Enterprise Linux életciklus](https://access.redhat.com/support/policy/updates/errata) oldalon találhatók.
