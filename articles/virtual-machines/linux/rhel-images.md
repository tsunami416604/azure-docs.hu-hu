---
title: Red Hat Enterprise Linux-rendszerképeket az Azure-ban |} A Microsoft Docs
description: További tudnivalók a Red Hat Enterprise Linux-rendszerképeket a Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/18/2018
ms.author: borisb
ms.openlocfilehash: f178ac06b4ebfeef534233c2782423a7358f0c10
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53719629"
---
# <a name="red-hat-enterprise-linux-images-in-azure"></a>Red Hat Enterprise Linux-rendszerképeket az Azure-ban
Ez a cikk ismerteti a kiosztási és megőrzési szabályzatokkal együtt az Azure piactéren elérhető Red Hat Enterprise Linux (RHEL) rendszerképek

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
Az összes jelenleg közzétett RHEL-lemezképek a használatalapú modellt használja, és csatlakozik [Red Hat frissítési infrastruktúrához (RHUI) az Azure-ban](https://aka.ms/rhui-update). RHUI tervezési korlátai miatt egy új elnevezési konvenciót RHEL 7 rendszerű rendszerképek elfogadott. Az RHEL-6 családba tartozó elnevezési jelenleg még nem változott.

A korlátozás van az a tény, hogy amikor egy nem szelektív `yum update` vonatkozóan egy virtuális gép fut RHUI csatlakozik, az RHEL-verzió frissül a legfrissebb a jelenlegi család. További információkért lásd: [ezt a hivatkozást](https://aka.ms/rhui-udate). Előfordulhat, hogy emiatt a keveredési, amikor egy RHEL 7.2 üzembe helyezett rendszerképet RHEL 7.6 válik a frissítés után. Továbbra is telepíthet egy régebbi rendszerképből explicit módon adja meg a szükséges verzió a fenti példákban szemléltetett módon. Ha nincs megadva a szükséges verzió új RHEL 7 lemezkép kiépítése során, majd a legújabb rendszerképet lesznek üzembe helyezve.

>[!NOTE]
> Az RHEL for SAP képkészlet az RHEL-verzió rögzített marad. Mint ilyen az elnevezési konvenciót termékváltozatban egy adott verziót is tartalmazza.

>[!NOTE]
> RHEL 6 képkészlet nem lett áthelyezve, az új nevezéktannak.

Az alábbi ajánlatok a következők általános felhasználású jelenleg rendelkezésre álló termékváltozatok a következők:
Ajánlat| SKU | Particionálás | Kiépítés | Megjegyzések
:----|:----|:-------------|:-------------|:-----
RHEL | 7 – NYERS | RAW | Linux-ügynök | RHEL 7 operációsrendszer-lemezképek
| | 7 – LVM | LVM | Linux-ügynök | RHEL 7 operációsrendszer-lemezképek
| | 7-RAW-CI | NYERS CI | Cloud-init | RHEL 7 operációsrendszer-lemezképek
| | 6.7 | RAW | Linux-ügynök | RHEL 6.7 lemezképek, a régi elnevezési egyezmény
| | 6.8 | RAW | Linux-ügynök | Azonos RHEL 6.8, a fentiek szerint
| | 6.9 | RAW | Linux-ügynök | Ugyanezt a fentiek szerint RHEL 6.9.
| | 6.10 | RAW | Linux-ügynök | Azonos RHEL 6.10, a fentiek szerint
| | 7.2 | RAW | Linux-ügynök | Ugyanezt a fentiek szerint RHEL 7.2
| | 7.3 | RAW | Linux-ügynök | Ugyanezt a fentiek szerint RHEL 7.3.
| | 7.4 | RAW | Linux-ügynök | Azonos RHEL 7.4, a fentiek szerint
| | 7.5 | RAW | Linux-ügynök | Ugyanezt a fentiek szerint RHEL 7.5
RHEL-SAP | 7.4 | LVM | Linux-ügynök | 7.4 RHEL for SAP HANA és az üzleti alkalmazások
| | 7.5 | LVM | Linux-ügynök | 7.5 RHEL for SAP HANA és az üzleti alkalmazások
RHEL-SAP-HANA | 6.7 | RAW | Linux-ügynök | RHEL 6.7 az SAP Hana-hoz
| | 7.2 | LVM | Linux-ügynök | Az RHEL 7.2 az SAP Hana-hoz
| | 7.3 | LVM | Linux-ügynök | RHEL for SAP HANA 7.3.
RHEL-SAP-ALKALMAZÁSOK | 6.8 | RAW | Linux-ügynök | 6.8 RHEL for SAP Business Applications
| | 7.3 | LVM | Linux-ügynök | 7.3 RHEL for SAP Business Applications

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