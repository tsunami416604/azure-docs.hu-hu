---
title: A Red Hat frissítési infrastruktúrája
description: A Red Hat frissítési infrastruktúrájának megismerése az igény szerinti Red Hat Enterprise Linux-példányokhoz Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 6/6/2019
ms.author: borisb
ms.openlocfilehash: 34a2742c752259fec5859af1681da2429276ea41
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75941864"
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Red Hat frissítési infrastruktúra az igény szerinti Red Hat Enterprise Linux virtuális gépek számára az Azure-ban
 A [Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) (RHUI) lehetővé teszi, hogy a felhőalapú szolgáltatók, például az Azure, a Red Hat-ban üzemeltetett tárház tartalmait tükrözze, egyéni tárházat hozzon létre az Azure-specifikus tartalommal, és elérhetővé tegye a végfelhasználói virtuális gépek számára.

A Red Hat Enterprise Linux (RHEL) TB-lemezképek előre konfigurálva vannak az Azure RHUI eléréséhez. Nincs szükség további konfigurálásra. A legújabb frissítések beszerzéséhez futtassa a `sudo yum update`t, miután elkészült a RHEL-példány. Ez a szolgáltatás része a RHEL TB.

További információ az Azure-beli RHEL-lemezképekről, beleértve a közzétételi és adatmegőrzési szabályzatokat is [itt](./rhel-images.md)érhető el.

A Red Hat-támogatási házirendekkel kapcsolatos információk a RHEL összes verziójára vonatkozóan a [Red Hat Enterprise Linux életciklus](https://access.redhat.com/support/policy/updates/errata) oldalon találhatók.

## <a name="important-information-about-azure-rhui"></a>Fontos információk az Azure RHUI

* Az Azure RHUI a frissítési infrastruktúra, amely támogatja az Azure-ban létrehozott összes RHEL TB virtuális gépet. Ez nem zárja ki, hogy regisztrálja a TB RHEL-alapú virtuális gépeket az előfizetés-kezelővel, illetve a műholdon vagy más frissítési forrásokkal, de a TB virtuális géppel való használata esetén a rendszer közvetett kettős számlázást eredményez. A részletekért tekintse meg a következő pontot.
* Az Azure által üzemeltetett RHUI a RHEL TB rendszerképének ára tartalmazza. Ha töröl egy TB RHEL virtuális gépet az Azure által üzemeltetett RHUI, amely nem alakítja át a virtuális gépet saját Licences (BYOL) virtuálisgép-típusra. Ha ugyanezt a virtuális gépet egy másik frissítési forrással regisztrálja, a rendszer _közvetett_ kettős díjat is felmerülhet. Az Azure RHEL szoftver díját először számoljuk fel. A korábban megvásárolt Red Hat-előfizetések esetében a második alkalommal kell fizetnie. Ha az Azure által üzemeltetett RHUI eltérő frissítési infrastruktúrát kell használnia, érdemes regisztrálni a [RHEL BYOS-lemezképek](../workloads/redhat/byos.md)használatára.

* Az Azure-ban elérhető SAP-TB (RHEL for SAP, RHEL for SAP HANA és RHEL for SAP Business Applications) olyan dedikált RHUI-csatornákhoz csatlakoznak, amelyek az adott RHEL alverzióban maradnak, és az SAP-minősítéshez szükségesek.

* Az Azure által üzemeltetett RHUI való hozzáférés az [Azure-adatközpont IP-tartományán](https://www.microsoft.com/download/details.aspx?id=41653)belüli virtuális gépekre korlátozódik. Ha az összes virtuális gép forgalmát egy helyszíni hálózati infrastruktúrán keresztül végzi, lehetséges, hogy a RHEL TB virtuális gépekhez felhasználó által megadott útvonalakat kell beállítania az Azure-RHUI eléréséhez. Ha ez a helyzet, a felhasználó által megadott útvonalakat hozzá kell adni az _összes_ RHUI IP-címhez.

## <a name="image-update-behavior"></a>Rendszerkép-frissítési viselkedés

Április 2019 az Azure olyan RHEL-rendszerképeket kínál, amelyek alapértelmezés szerint a kiterjesztett frissítési támogatási (EUS) adattárakhoz csatlakoznak, és RHEL a normál (nem EUS) adattárakhoz csatlakozó lemezképeket. A RHEL EUS kapcsolatos további részletek a Red Hat [verziójának életciklus-dokumentációjában](https://access.redhat.com/support/policy/updates/errata) és a [EUs dokumentációjában](https://access.redhat.com/articles/rhel-eus)találhatók. A `sudo yum update` alapértelmezett viselkedése attól függ, hogy melyik RHEL-lemezképből lett kiépítve, mert a különböző rendszerképek különböző adattárakhoz csatlakoznak.

A teljes rendszerkép listához futtassa az `az vm image list --publisher redhat --all` az Azure CLI használatával.

### <a name="images-connected-to-non-eus-repositories"></a>Nem EUS-tárházhoz csatlakoztatott képek

Ha olyan RHEL-lemezképből hoz létre virtuális gépet, amely nem EUS-adattárakhoz csatlakozik, akkor a `sudo yum update`futtatásakor a rendszer a legújabb RHEL alverzióra frissíti. Ha például RHEL 7,4 TB lemezképből helyez üzembe egy virtuális gépet, és `sudo yum update`futtat, akkor a RHEL 7,7 virtuális gép (a RHEL7-család legújabb alverziója) végén jelenik meg.

A nem EUS adattárakhoz csatlakoztatott lemezképek nem tartalmaznak alverziószámot az SKU-ban. Az SKU a harmadik eleme az URN-ben (a rendszerkép teljes neve). Az alábbi képek például a nem EUS adattárakhoz vannak csatolva:

```text
RedHat:RHEL:7-LVM:7.4.2018010506
RedHat:RHEL:7-LVM:7.5.2018081518
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7-RAW:7.4.2018010506
RedHat:RHEL:7-RAW:7.5.2018081518
RedHat:RHEL:7-RAW:7.6.2019062120
```

Vegye figyelembe, hogy az SKU-nak 7-LVM vagy 7-RAW értékűnek kell lennie. Az alverzió a lemezképek (URN negyedik eleme) verziójában szerepel.

### <a name="images-connected-to-eus-repositories"></a>EUS-adattárakhoz csatlakoztatott képek

Ha EUS-adattárakhoz csatlakoztatott RHEL-lemezképből helyez üzembe egy virtuális gépet, a `sudo yum update`futtatásakor a rendszer nem frissíti a legújabb RHEL alverzióra. Ennek az az oka, hogy a EUS-adattárakhoz csatlakoztatott rendszerképek szintén a megadott alverzióhoz vannak rögzítve.

A EUS-adattárakhoz csatlakozó rendszerképek az SKU-ban is szerepelni fognak. Az alábbi képek például az EUS-adattárakhoz vannak csatolva:

```text
RedHat:RHEL:7.4:7.4.2019062107
RedHat:RHEL:7.5:7.5.2019062018
RedHat:RHEL:7.6:7.6.2019062116
```

## <a name="rhel-eus-and-version-locking-rhel-vms"></a>RHEL EUS és verzió – RHEL virtuális gépek zárolása

Előfordulhat, hogy egyes ügyfelek a virtuális gép üzembe helyezése után le szeretnék zárni a RHEL virtuális gépeket egy bizonyos RHEL-kiadásra. A RHEL virtuális gép egy adott alverzióra való rögzítéséhez frissítse a tárházat, hogy az a kiterjesztett frissítés támogatási tárházára mutasson. A EUS-zárolási műveletet is visszavonhatja.

>[!NOTE]
> A EUS nem támogatott a RHEL-extrák esetében. Ez azt jelenti, hogy ha olyan csomagot telepít, amely általában az RHEL extrák csatornából érhető el, akkor a EUS-on nem fogja tudni elvégezni. [Itt](https://access.redhat.com/support/policy/updates/extras/)részletesen ismertetjük a Red Hat extrák termék életciklusát.

Az írás időpontjában a EUS-támogatás befejeződött a RHEL < = 7,3 esetében. További részletekért tekintse meg a [Red Hat dokumentációjának](https://access.redhat.com/support/policy/updates/errata/) "Red Hat Enterprise Linux a hosszabb támogatási bővítmények" című szakaszát.
* RHEL 7,4 EUS-támogatás vége augusztus 31., 2019
* RHEL 7,5 EUS támogatás vége április 30., 2020
* RHEL 7,6 EUS-támogatás vége 2020. október 31.
* RHEL 7,7 EUS-támogatás vége augusztus 30-ig 2021

### <a name="switch-a-rhel-vm-to-eus-version-lock-to-a-specific-minor-version"></a>RHEL virtuális gép átváltása a EUS-re (verzió – egy adott alverzióra való zárolás)
Az alábbi útmutatást követve zárolhat egy RHEL virtuális gépet egy adott másodlagos kiadásra (futtató gyökérként):

>[!NOTE]
> Ez csak azokra a RHEL-verziókra vonatkozik, amelyekhez EUS érhető el. Az írás időpontjában ez a 7.2-7.7 RHEL is magában foglalja. További részletek a [Red Hat Enterprise Linux életciklusa](https://access.redhat.com/support/policy/updates/errata) lapon érhetők el.

1. Nem EUS-alapú repók letiltása:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7'
    ```

1. EUS-repók hozzáadása:
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7-eus.config' install 'rhui-azure-rhel7-eus'
    ```

1. A releasever változó zárolása (futtató gyökér):
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > A fenti utasítás a RHEL-alverzió zárolását az aktuális másodlagos kiadáshoz fogja zárolni. Adjon meg egy adott másodlagos kiadást, ha a frissítést és a zárolást egy későbbi, nem a legújabb verzióra szeretné használni. A `echo 7.5 > /etc/yum/vars/releasever` például a RHEL-verziót RHEL 7,5-re fogja zárolni

1. A RHEL virtuális gép frissítése
    ```bash
    sudo yum update
    ```

### <a name="switch-a-rhel-vm-back-to-non-eus-remove-a-version-lock"></a>RHEL virtuális gép átváltása nem EUS (verziók zárolásának eltávolítása)
Futtassa a következőt root-ként:
1. Távolítsa el a releasever fájlt:
    ```bash
    rm /etc/yum/vars/releasever
     ```

1. EUS-repók letiltása:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7-eus'
   ```

1. RHEL virtuális gép konfigurálása
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
    ```
    
1. A RHEL virtuális gép frissítése
    ```bash
    sudo yum update
    ```

## <a name="the-ips-for-the-rhui-content-delivery-servers"></a>A RHUI-tartalomszolgáltató kiszolgálók IP-címei

A RHUI minden olyan régióban elérhető, ahol RHEL igény szerinti lemezképek érhetők el. A szolgáltatás jelenleg az [Azure status Dashboard](https://azure.microsoft.com/status/) oldalon, az Azure US governmentben és a Microsoft Azure Germany régiókban felsorolt összes nyilvános régiót tartalmazza.

Ha hálózati konfigurációt használ a RHEL TB-alapú virtuális gépekről való hozzáférés további korlátozására, akkor győződjön meg arról, hogy a következő IP-címek engedélyezettek-e a `yum update` számára, hogy azok a környezettől függően működjenek:


```
# Azure Global
13.91.47.76
40.85.190.91
52.187.75.218
52.174.163.213
52.237.203.198

# Azure US Government
13.72.186.193
13.72.14.155
52.244.249.194

# Azure Germany
51.5.243.77
51.4.228.145
```

## <a name="azure-rhui-infrastructure"></a>Azure RHUI-infrastruktúra


### <a name="update-expired-rhui-client-certificate-on-a-vm"></a>Lejárt RHUI-ügyféltanúsítvány frissítése egy virtuális gépen

Ha egy régebbi RHEL virtuálisgép-rendszerképet használ, például a RHEL 7,4 (rendszerkép URN: `RedHat:RHEL:7.4:7.4.2018010506`), a rendszer csatlakozási problémákba ütközik a RHUI miatt egy már lejárt SSL-ügyféltanúsítvány miatt. A megjelenített hiba a következőhöz hasonló lehet: _"az SSL-társ a tanúsítvány lejártként való elutasítása"_ vagy _"hiba: nem lehet lekérni a tárház metaadatait (repomd. xml) az adattár számára... Ellenőrizze az elérési útját, és próbálkozzon újra "_ . A probléma megoldásához frissítse a RHUI-ügyfélszoftvert a virtuális gépen a következő parancs használatával:

```bash
sudo yum update -y --disablerepo='*' --enablerepo='*microsoft*'
```

Azt is megteheti, hogy `sudo yum update` futtatja az ügyféltanúsítvány-csomagot is (a RHEL verziójától függően), a "lejárt SSL-tanúsítvány" hibaüzenetek ellenére, amelyeket más adattárakhoz fog látni. Ha a frissítés sikeres, a többi RHUI-tárházhoz való normál kapcsolatot vissza kell állítani, így a `sudo yum update` sikeresen futtatható.

Ha egy `yum update`futtatása során 404-as hibát észlel, próbálja meg a következőt a yum cache frissítéséhez:
```bash
sudo yum clean all;
sudo yum makecache
```

### <a name="troubleshoot-connection-problems-to-azure-rhui"></a>Az Azure RHUI kapcsolódási problémáinak elhárítása
Ha problémákat tapasztal az Azure RHUI az Azure RHEL TB virtuális gépről való csatlakoztatásával kapcsolatban, kövesse az alábbi lépéseket:

1. Ellenőrizze az Azure RHUI-végpont virtuálisgép-konfigurációját:

    1. Ellenőrizze, hogy a `/etc/yum.repos.d/rh-cloud.repo` fájl tartalmaz-e hivatkozást a `rhui-[1-3].microsoft.com` a fájl `[rhui-microsoft-azure-rhel*]` szakaszának `baseurl`. Ha igen, az új Azure-RHUI használja.

    1. Ha egy helyre mutat a következő mintával, `mirrorlist.*cds[1-4].cloudapp.net`a konfiguráció frissítésére van szükség. A régi virtuális gép pillanatképét használja, és frissítenie kell, hogy az új Azure-RHUI mutasson.

1. Az Azure által üzemeltetett RHUI való hozzáférés az [Azure-adatközpont IP-tartományán](https://www.microsoft.com/download/details.aspx?id=41653)belüli virtuális gépekre korlátozódik.

1. Ha az új konfigurációt használja, ellenőrizte, hogy a virtuális gép csatlakozik az Azure IP-tartományhoz, és továbbra sem tud csatlakozni az Azure RHUI-hoz, a Microsoft vagy a Red Hat támogatási esetét.

### <a name="infrastructure-update"></a>Infrastruktúra frissítése

2016 szeptemberében üzembe helyezettünk egy frissített Azure-RHUI. 2017 áprilisában leállítottuk a régi Azure-RHUI. Ha a RHEL TB-lemezképeit (vagy pillanatképeit) a szeptember 2016-es vagy újabb verziójával használta, automatikusan csatlakozik az új Azure-RHUI. Ha azonban régebbi pillanatképeket használ a virtuális gépeken, manuálisan kell frissítenie a konfigurációját, hogy hozzáférjen az Azure-RHUI a következő szakaszban leírtak szerint.

Az új Azure RHUI-kiszolgálók üzembe helyezése az [azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/). Traffic Manager egyetlen végpontot (rhui-1.microsoft.com) is használhat bármely virtuális gép, a régiótól függetlenül.

### <a name="manual-update-procedure-to-use-the-azure-rhui-servers"></a>Az Azure RHUI-kiszolgálók használatának manuális frissítési eljárása
Ez az eljárás csak referenciául szolgál. A RHEL TB-lemezképek már rendelkeznek a megfelelő konfigurációval az Azure RHUI való kapcsolódáshoz. Ha manuálisan szeretné frissíteni a konfigurációt az Azure RHUI-kiszolgálók használatára, hajtsa végre a következő lépéseket:

- RHEL 6 esetén:
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel6.config' install 'rhui-azure-rhel6'
  ```

- A RHEL 7 esetében:
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
  ```

## <a name="next-steps"></a>Következő lépések
* Red Hat Enterprise Linux virtuális gép Azure Marketplace-TB rendszerképből való létrehozásához, valamint az Azure által üzemeltetett RHUI használatához látogasson el az [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/)-re.
* Ha többet szeretne megtudni az Azure-beli Red Hat-lemezképekről, lépjen a [dokumentáció lapra](./rhel-images.md).
* A Red Hat-támogatási házirendekkel kapcsolatos információk a RHEL összes verziójára vonatkozóan a [Red Hat Enterprise Linux életciklus](https://access.redhat.com/support/policy/updates/errata) oldalon találhatók.
