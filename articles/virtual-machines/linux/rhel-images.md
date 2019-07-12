---
title: Red Hat Enterprise Linux-rendszerképeket az Azure-ban |} A Microsoft Docs
description: További tudnivalók a Red Hat Enterprise Linux-rendszerképeket a Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 6/6/2019
ms.author: borisb
ms.openlocfilehash: f7ae82b0376489e21b35e4e94dce32805bea69c6
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67708383"
---
# <a name="red-hat-enterprise-linux-images-in-azure"></a>Red Hat Enterprise Linux-rendszerképeket az Azure-ban
Ez a cikk ismerteti az elérhető Red Hat Enterprise Linux (RHEL) rendszerképek mellett saját elnevezési és adatmegőrzési szabályzatok az Azure piactéren.

Red Hat támogatási irányelveik RHEL összes verziója információk találhatók a [Red Hat Enterprise Linux életciklusának](https://access.redhat.com/support/policy/updates/errata) lapot.

>[!Important]
> RHEL-lemezképekhez érhető el az Azure Marketplace-en Bring-Your-saját-előfizetés (saját) vagy a használatalapú fizetéssel szemben licencelési modellt támogatja. A [Azure Hybrid Use Benefit](../windows/hybrid-use-benefit-licensing.md) és dinamikus váltás saját és a Használatalapú között nem támogatott. Licencelési mód váltása szükséges újbóli üzembe helyezés a virtuális Gépet a megfelelő lemezképet.

>[!Note]
> Az RHEL-lemezképek az Azure marketplace-katalógusában kapcsolatos nyújtson be támogatási jegyet a Microsoft.

## <a name="images-available-in-the-ui"></a>A felhasználói felületen hozzáférhető lemezképeket
Keressen a "Red Hat" a Marketplace-en, vagy az Azure portal felhasználói felületén hozzon létre egy erőforrást, amikor látni fogja a rendelkezésre álló RHEL-lemezképek és a kapcsolódó Red Hat-termékek egy része. Mindig az elérhető Virtuálisgép-rendszerképek használata az Azure CLI és PowerShell vagy az API teljes körű szerezheti be.

Az Azure-ban elérhető Red Hat-lemezképeit a teljes körű megtekintéséhez futtassa a következő parancsot

```azurecli-interactive
az vm image list --publisher RedHat --all
```

## <a name="naming-convention"></a>Az elnevezési konvenciót
Az Azure-beli Virtuálisgép-rendszerképek közzétevő, ajánlat, Termékváltozat és verzió szerint vannak rendszerezve. Közzétevő: az ajánlat: SKU:Version kombinációja a kép URN, amely egyedileg azonosítja a használni kívánt kép.

Ha például `RedHat:RHEL:7-RAW:7.6.2018103108` hivatkozik egy RHEL 7.6 nyers particionált lemezképen alapuló 2018. október 31-ig.

Az alábbiakban látható egy minta bemutatja, hogyan hozzon létre egy RHEL 7.6 virtuális Gépet.
```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-RAW:7.6.2018103108 --no-wait
```

### <a name="the-latest-moniker"></a>A "legutóbbi" kézjegy
Az Azure REST API lehetővé teszi a "legutóbbi" helyett az adott verziót a kézjegy használatát. "Legújabb" fog üzembe helyezhető a legújabb elérhető lemezkép a megadott közzétevő, ajánlat és Termékváltozat.

Ha például `RedHat:RHEL:7-RAW:latest` a legújabb RHEL 7 családba tartozó nyers particionált lemezkép hivatkozik.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-RAW:latest --no-wait
```

>[!NOTE]
> Általánosságban véve a meghatározásához a legújabb verziók összehasonlítása szabályainak követi a [compareto metódus végrehajtása metódus](https://msdn.microsoft.com/library/a5ts8tb6.aspx).

### <a name="current-naming-convention"></a>Aktuális elnevezési egyezmény
Az összes jelenleg közzétett RHEL-lemezképek a használatalapú modellt használja, és csatlakozik [Red Hat frissítési infrastruktúrához (RHUI) az Azure-ban](https://aka.ms/rhui-update). Egy új elnevezési konvenciót elfogadott RHEL 7, amelyben a lemez particionálási séma családba tartozó képek (nyers, LVM) van megadva a termékváltozat a helyett. Az RHEL-lemezkép verziója tartalmazza vagy 7-nyers vagy 7-LVM. Az RHEL-6 családba tartozó elnevezési jelenleg még nem változott.

Ezt az elnevezési konvenciót RHEL 7 kép termékváltozatok 2 típusú lesz: SKU-k, hogy az alverzió, és az SKU-k, amelyek nem. Ha azt szeretné, 7-LVM Termékváltozat vagy a 7-nyers használja, megadhatja az RHEL-alverzió, verziójában telepíteni kívánja. Ha úgy dönt, hogy a "legutóbbi" verzió, akkor lesz kiépítve RHEL kisebb legújabb kiadása.

>[!NOTE]
> Az RHEL for SAP képkészlet az RHEL-verzió rögzített marad. Mint ilyen az elnevezési konvenciót termékváltozatban egy adott verziót is tartalmazza.

>[!NOTE]
> RHEL 6 képkészlet nem lett áthelyezve, az új nevezéktannak.

## <a name="extended-update-support-eus"></a>A kiterjesztett támogatásának (EUS)
Április 2019, az RHEL-lemezképek rendelkezésre állnak, amely kapcsolódik a kiterjesztett frissítés támogatása (EUS) tárházak alapértelmezés szerint. További részleteket az RHEL EUS [Red Hat-dokumentáció](https://access.redhat.com/articles/rhel-eus).

Útmutatást nyújt a virtuális gép váltani EUS és EUS támogatási életciklusa végét záró dátumok kapcsolatos további részletekért érhetők el [Itt](https://aka.ms/rhui-update#rhel-eus-and-version-locking-rhel-vms).

>[!NOTE]
> EUS RHEL kiegészítő funkciók a nem támogatott. Ez azt jelenti, hogy telepít egy csomagot, amely általában a RHEL kiegészítő funkciók csatorna érhető el, ha nem tudja ehhez a EUS. A Red Hat kiegészítő funkciók életciklusáról részletes [Itt](https://access.redhat.com/support/policy/updates/extras/).

### <a name="for-customers-that-want-to-use-eus-images"></a>Ügyfelek számára, EUS lemezképeket használni szeretne:
Ügyfelek EUS tárházak csatlakoztatott rendszerképek használni kívánt kell használnia, amely egy RHEL alverziószámát a termékváltozat tartalmazza az RHEL-lemezkép. Ezek a lemezképek lesz nyers particionált (azaz nem LVM).

Például előfordulhat, hogy tekintse meg a következő 2 RHEL 7.4 lemezkép érhető el:
```bash
RedHat:RHEL:7-RAW:7.4.2018010506
RedHat:RHEL:7.4:7.4.2019041718
```
Ebben az esetben `RedHat:RHEL:7.4:7.4.2019041718` csatolva lesz a EUS tárházak alapértelmezés szerint és `RedHat:RHEL:7-RAW:7.4.2018010506` alapértelmezés szerint nem EUS tárházak legyen csatolva.

### <a name="for-customers-that-dont-want-to-use-eus-images"></a>Ügyfelek számára, hogy nem szeretné használni a EUS lemezképek:
Ha nem szeretné használni, amely alapértelmezés szerint csatlakozik EUS képet, az üzembe helyezés alverziószámát a termékváltozat nem tartalmaz egy képet.

#### <a name="rhel-images-with-eus"></a>EUS az RHEL-lemezképek
Az alábbi táblázat az RHEL-lemezképekhez, amelyek tartalmazzák a termékváltozat alverzió fog alkalmazni.

>[!NOTE]
> A írása idején csak az RHEL 7.4 és a későbbi alverziót rendelkezik EUS támogatja. EUS már nem támogatott az RHEL < = 7.3.

Alverzió |EUS képen példa              |EUS állapota                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
7\.4 RHEL      |RedHat:RHEL:7.4:7.4.2019041718 | Képek április 2019 közzé, és később lesz EUS alapértelmezés szerint|
RHEL 7.5      |RedHat:RHEL:7.5:7.5.2019060305 | Képek június 2019 közzé, és később lesz EUS alapértelmezés szerint |
RHEL 7.6      |RedHat:RHEL:7.6:7.6.2019052206 | Képek közzétett 2019. május, és később lesz EUS alapértelmezés szerint  |
RHEL 8.0      |–                            | Nincs EUS jelenleg jelenleg elérhető lemezképek                 |


## <a name="list-of-rhel-images-available"></a>Rendelkezésre álló RHEL-lemezképek listája
Az alábbi ajánlatok a következők általános felhasználású jelenleg rendelkezésre álló termékváltozatok a következők:

Ajánlat| SKU | Particionálás | Kiépítés | Megjegyzések
:----|:----|:-------------|:-------------|:-----
RHEL          | 7 – NYERS    | RAW    | Linux-ügynök | RHEL 7 családba tartozó képek. <br> Nincs csatolva EUS tárházakba alapértelmezés szerint.
|             | 7-LVM    | LVM    | Linux-ügynök | RHEL 7 családba tartozó képek. <br> Nincs csatolva EUS tárházakba alapértelmezés szerint.
|             | 7-RAW-CI | RAW-CI | Cloud-init  | RHEL 7 családba tartozó képek. <br> Nincs csatolva EUS tárházakba alapértelmezés szerint.
|             | 6.7      | RAW    | Linux-ügynök | RHEL 6.7 lemezképek, a régi elnevezési egyezmény
|             | 6.8      | RAW    | Linux-ügynök | Azonos RHEL 6.8, a fentiek szerint
|             | 6.9      | RAW    | Linux-ügynök | Ugyanezt a fentiek szerint RHEL 6.9.
|             | 6.10     | RAW    | Linux-ügynök | Azonos RHEL 6.10, a fentiek szerint
|             | 7.2      | RAW    | Linux-ügynök | Ugyanezt a fentiek szerint RHEL 7.2
|             | 7.3      | RAW    | Linux-ügynök | Ugyanezt a fentiek szerint RHEL 7.3.
|             | 7.4      | RAW    | Linux-ügynök | Ugyanaz a fentiek szerint RHEL 7.4-hez. <br> Csatolt EUS adattárak tól április 2019 alapértelmezés szerint
|             | 7.5      | RAW    | Linux-ügynök | Ugyanaz a fentiek szerint RHEL 7.5-hez. <br> Csatolt EUS adattárak tól június 2019 alapértelmezés szerint
|             | 7.6      | RAW    | Linux-ügynök | Ugyanaz a fentiek szerint RHEL 7.6-hez. <br> Csatolt EUS tárházak 2019. május kezdődően alapértelmezés szerint
RHEL-SAP      | 7.4      | LVM    | Linux-ügynök | 7\.4 RHEL for SAP HANA és az üzleti alkalmazások
|             | 7.5      | LVM    | Linux-ügynök | 7\.5 RHEL for SAP HANA és az üzleti alkalmazások
RHEL-SAP-HANA | 6.7      | RAW    | Linux-ügynök | RHEL 6.7 az SAP Hana-hoz
|             | 7.2      | LVM    | Linux-ügynök | Az RHEL 7.2 az SAP Hana-hoz
|             | 7.3      | LVM    | Linux-ügynök | RHEL for SAP HANA 7.3.
RHEL-SAP-APPS | 6.8      | RAW    | Linux-ügynök | 6\.8 RHEL for SAP Business Applications
|             | 7.3      | LVM    | Linux-ügynök | 7\.3 RHEL for SAP Business Applications

### <a name="old-naming-convention"></a>Régi elnevezési egyezmény
A RHEL 7 operációsrendszer-lemezképeket és az RHEL 6 operációsrendszer-lemezképek pillanatáig az elnevezési konvenciót módosítása azt fent kifejtettük tartozó termékváltozatokat használt verzióját.

Numerikus termékváltozatok megtalálja a teljes lemezkép-listában. A Microsoft és a Red Hat a új kisebb kiadás amikor létrehozza a numerikus új termékváltozatokra.

### <a name="other-available-offers-and-skus"></a>Egyéb elérhető ajánlatok és termékváltozatok
Elérhető ajánlatok és termékváltozatok teljes listáját tartalmazhat további rendszerképeket felsoroltakon kívül a fenti táblázatban, például `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`. Ezeket az ajánlatokat használhatók, amelyek biztosítják az adott marketplace-megoldások támogatását, vagy azok előzetes verziók és tesztelési célokra lehetett közzétenni. Ezek módosítani vagy eltávolítani figyelmeztetés nélkül bármikor előfordulhat, hogy. Ne használja őket, ha ezek jelenléte nyilvánosan dokumentált Microsoft vagy a Red Hat.

## <a name="publishing-policy"></a>A házirend közzététele
A Microsoft és a Red Hat lemezképek frissítéséhez a új alverziót kiadott, adott CVEs megoldása érdekében szükség szerint, vagy alkalmi konfiguráció módosításainak/frissítéseinek. Adja meg a frissített rendszerképek, minél hamarabb – egy kiadott vagy CVE javítás a következő három munkanapon belül igyekeztünk.

Frissítjük csak egy adott rendszerkép család jelenlegi kisebb kiadásban. Az egy kisebb újabb kiadással azt állítsa le a régebbi alverzió frissítése. Például az RHEL 7.6 kiadással RHEL 7.5-lemezképek már nem fog frissülni.

>[!NOTE]
> Aktív Azure virtuális gépek üzembe a RHEL használatalapú fizetéses rendszerképek az Azure RHUI csatlakozik, és képes fogadni a frissítések és javítások, amint a Red Hat által kiadott, és replikálja az Azure RHUI (általában kevesebb, mint 24 órában a hivatalos kiadás alábbi by Red Hat) . Ezek a virtuális gépek nem szükséges új közzétett lemezkép lekérhesse a frissítéseket, és az ügyfél rendelkezik teljes hozzáféréssel a frissítés indításáról való.

## <a name="image-retention-policy"></a>Kép adatmegőrzési szabályzat
Az aktuális házirendet, hogy az összes korábban közzétett rendszerképek tartani. Fenntartjuk a jogot arra, hogy távolítsa el az ismert problémákhoz bármiféle lemezképek. Például ezt követő platform vagy összetevő-frissítései miatt nem megfelelő konfigurációkat tartalmazó lemezképeket is távolítható el. Eltávolíthatja a lemezképbe értesítéseket biztosít be a rendszerkép eltávolítása előtt 30 nappal az aktuális Marketplace házirend követi.

## <a name="next-steps"></a>További lépések
* További információ az Azure Red Hat frissítési infrastruktúrához [Itt](https://aka.ms/rhui-update).
* Red Hat támogatási irányelveik RHEL összes verziója információk találhatók a [Red Hat Enterprise Linux életciklusának](https://access.redhat.com/support/policy/updates/errata) lapot.
