---
title: Red Hat frissítési infrastruktúra | Microsoft dokumentumok
description: Ismerje meg a Red Hat update infrastruktúrát az igény szerinti Red Hat Enterprise Linux-példányokhoz a Microsoft Azure-ban
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: aa9fd230f59b5e46576e78beb0436c85449d3c5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80256912"
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Red Hat frissítési infrastruktúra az igény szerinti Red Hat Enterprise Linux-virtuális gépekhez az Azure-ban
 [A Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) (RHUI) lehetővé teszi a felhőszolgáltatók, például az Azure számára, hogy tükrözzék a Red Hat által üzemeltetett tárház tartalmakat, egyéni adattárakat hozzanak létre az Azure-specifikus tartalommal, és elérhetővé tegyék a végfelhasználói virtuális gépek számára.

A Red Hat Enterprise Linux (RHEL) pay-as-you-go (PAYG) lemezképei előre konfigurálva vannak az Azure RHUI eléréséhez. Nincs szükség további konfigurációra. A legújabb frissítések beszerezéséhez futtassa, `sudo yum update` miután az RHEL-példány készen áll. Ez a szolgáltatás az RHEL PAYG szoftverdíjak részét képezi.

Az Azure-beli RHEL-lemezképekről, beleértve a közzétételi és adatmegőrzési szabályzatokat is, [itt](./redhat-images.md)talál további információt.

A Red Hat támogatási irányelveiről az RHEL összes verziójára vonatkozó információk a [Red Hat Enterprise Linux Életciklus](https://access.redhat.com/support/policy/updates/errata) oldalán találhatók.

> [!IMPORTANT]
> Az RHUI csak használatalapú (PAYG) kiosztós (PAYG) képekhez készült. Az egyéni és arany színű képek, más néven bring-your-own-subscription (BYOS) esetén a rendszert csatolni kell az RHSM-hez vagy a Satellitehez a frissítések fogadásához. További részletek a [Red Hat cikkben.](https://access.redhat.com/solutions/253273)


## <a name="important-information-about-azure-rhui"></a>Fontos információk az Azure RHUI-ról

* Az Azure RHUI az azure-ban létrehozott összes RHEL PAYG virtuális gépet támogató frissítési infrastruktúra. Ez nem zárja ki, hogy regisztrálja payg RHEL virtuális gépek et az Előfizetés-kezelőben vagy a Műholdas vagy más frissítési forrásban, de ha ezt egy PAYG virtuális géptel teszi, az közvetett kettős számlázást eredményez. A részleteket lásd a következő pontban.
* Az Azure által üzemeltetett RHUI-hoz való hozzáférés az RHEL PAYG lemezkép ára tartalmazza. Ha nem regisztrál egy PAYG RHEL virtuális gépet az Azure által üzemeltetett RHUI-ból, amely nem konvertálja a virtuális gépet a saját licenccel (BYOL) típusú virtuális gépre. Ha ugyanazt a virtuális gépet egy másik frissítési forrással regisztrálja, _közvetett_ kettős díjakmerülhetnek fel. Az Azure RHEL szoftverdíja első alkalommal kerül felszámításra. A korábban megvásárolt Red Hat-előfizetések ért ünk másodszor is díjat számítunk fel. Ha következetesen az Azure által üzemeltetett RHUI-tól eltérő frissítési infrastruktúrát kell használnia, fontolja meg a [RHEL BYOS-lemezképek](./byos.md)használatának regisztrálását.

* RHEL SAP PAYG-lemezképek az Azure-ban (RHEL az SAP, RHEL az SAP HANA és RHEL sap üzleti alkalmazásokhoz) dedikált RHUI csatornákhoz csatlakoznak, amelyek az SAP-minősítéshez szükséges adott RHEL-alverzión maradnak.

* Az Azure által üzemeltetett RHUI-hoz való hozzáférés az [Azure adatközpont IP-tartományain](https://www.microsoft.com/download/details.aspx?id=41653)belüli virtuális gépekre korlátozódik. Ha az összes virtuálisgép-forgalmat egy helyszíni hálózati infrastruktúrán keresztül proxy, előfordulhat, hogy be kell állítania a felhasználó által definiált útvonalak at RHEL PAYG virtuális gépek az Azure RHUI eléréséhez. Ebben az esetben a felhasználó által definiált útvonalakat _minden_ RHUI IP-címhez hozzá kell adni.


## <a name="image-update-behavior"></a>A kép frissítésének viselkedése

2019 áprilisától az Azure alapértelmezés szerint az EUS-tárolókhoz kapcsolódó RHEL-lemezképeket, alapértelmezés szerint a normál (nem EUS-alapú) adattárakhoz kapcsolódó RHEL-lemezképeket kínál. Az RHEL EUS-ról további részletek a Red Hat [életciklus-dokumentációjában](https://access.redhat.com/support/policy/updates/errata) és [az EUS dokumentációjában](https://access.redhat.com/articles/rhel-eus)találhatók. Az alapértelmezett `sudo yum update` viselkedés attól függően változik, hogy melyik RHEL-lemezképből kiépített, mivel a különböző lemezképek különböző tárolókhoz kapcsolódnak.

A teljes rendszerkép-lista futtatásához futtassa `az vm image list --publisher redhat --all` az Azure CLI használatával.

### <a name="images-connected-to-non-eus-repositories"></a>Nem EUS-adattárakhoz kapcsolódó képek

Ha egy virtuális gép egy RHEL-lemezkép, amely nem EUS-adattárak, akkor a rendszer frissíti `sudo yum update`a legújabb RHEL alverzió futtatásakor. Ha például egy virtuális gép egy RHEL 7.4 PAYG-lemezképből épít ki, és futtatja `sudo yum update`a virtuális gép, a végén egy RHEL 7.7 virtuális gép (az RHEL7 család legújabb alverziója) kerül ki.

A nem EUS-adattárakhoz kapcsolódó képek nem tartalmaznak alverziószámot a termékváltozatban. A termékváltozat az URN harmadik eleme (a kép teljes neve). Az alábbi képek például nem EUS-adattárakhoz kapcsolódnak:

```text
RedHat:RHEL:7-LVM:7.4.2018010506
RedHat:RHEL:7-LVM:7.5.2018081518
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7-RAW:7.4.2018010506
RedHat:RHEL:7-RAW:7.5.2018081518
RedHat:RHEL:7-RAW:7.6.2019062120
```

Vegye figyelembe, hogy a sú-k vagy 7-LVM vagy 7-RAW. Az alverzió a képek verziójában (az URN negyedik eleme) látható.

### <a name="images-connected-to-eus-repositories"></a>Az EUS-adattárakhoz kapcsolódó képek

Ha egy virtuális gép egy RHEL-lemezkép, amely kapcsolódik az EUS-adattárak, nem lesz frissítve a legújabb RHEL alverzió futtatásakor. `sudo yum update` Ennek az az oka, hogy az EUS-adattárakhoz kapcsolódó képek is verzió-zárolva vannak a saját alverziójukhoz.

Az EUS-adattárakhoz kapcsolódó képek egy alverziószámot fognak tartalmazni a termékváltozatban. Az alábbi képek például az EUS-adattárakhoz kapcsolódnak:

```text
RedHat:RHEL:7.4:7.4.2019062107
RedHat:RHEL:7.5:7.5.2019062018
RedHat:RHEL:7.6:7.6.2019062116
```

## <a name="rhel-eus-and-version-locking-rhel-vms"></a>RHEL EUS és verziózáró RHEL virtuális gépek

Extended Update Support (EUS) adattárak érhetők el az ügyfelek számára, akik esetleg szeretné, hogy zár a RHEL virtuális gépek egy bizonyos RHEL kisebb kiadás kiépítése után a virtuális gép. Az RHEL virtuális gép verziózárolását egy adott alverzióra zárolhatja, ha frissíti az adattárakat, hogy a kiterjesztett frissítési támogatási tárolókra mutasson. Az EUS verziózárolási műveletet is visszavonhatja.

>[!NOTE]
> Az EUS nem támogatott az RHEL Extrákon. Ez azt jelenti, hogy ha olyan csomagot telepít, amely általában az RHEL Extrák csatornáról érhető el, akkor ezt nem fogja tudni megtenni az EUS-en. A Red Hat Extrák termék életciklusa [itt](https://access.redhat.com/support/policy/updates/extras/)részleteződik.

Ennek az írásnak az időpontjában az RHEL támogatása <= 7,4. További részleteket a [Red Hat dokumentációjában](https://access.redhat.com/support/policy/updates/errata/) található "Red Hat Enterprise Linux hosszabb támogatási bővítmények" című részben talál.
* RHEL 7.4 EuS támogatás véget ér augusztus 31, 2019
* RHEL 7,5 EUS támogatás véget ér április 30, 2020
* RHEL 7.6 EUS támogatás véget ér október 31, 2020
* RHEL 7.7 EUS támogatás véget ér augusztus 30, 2021

### <a name="switch-a-rhel-vm-to-eus-version-lock-to-a-specific-minor-version"></a>RHEL virtuális gép váltása EUS-re (verziózár egy adott alverzióra)
Az alábbi utasításokat követve zárolhatja az RHEL virtuális gép egy adott kisebb kiadáshoz (gyökérként futva):

>[!NOTE]
> Ez csak azokra az RHEL-verziókra vonatkozik, amelyekhez eus áll rendelkezésre. Abban az időben az írás, ez magában foglalja RHEL 7.2-7.7. További részletek a [Red Hat Enterprise Linux életciklus](https://access.redhat.com/support/policy/updates/errata) oldalán találhatók.

1. Nem EUS-alapú adatfájlok letiltása:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7'
    ```

1. EuS-repók hozzáadása:
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7-eus.config' install 'rhui-azure-rhel7-eus'
    ```

1. A `releasever` változó zárolása (futtatás gyökérként):
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > A fenti utasítás zárolja az RHEL kisebb kiadását az aktuális kisebb kiadáshoz. Adjon meg egy adott kisebb kiadást, ha frissíteni szeretne, és egy későbbi kisebb kiadásra szeretne zárolni, amely nem a legújabb. Például `echo 7.5 > /etc/yum/vars/releasever` zárolja az RHEL verziót az RHEL 7.5

1. Az RHEL vm frissítése
    ```bash
    sudo yum update
    ```

### <a name="switch-a-rhel-vm-back-to-non-eus-remove-a-version-lock"></a>Rhel virtuális gép visszakapcsolása nem EUS-re (verziózár eltávolítása)
Futtassa a következőket gyökérként:
1. Távolítsa `releasever` el a fájlt:
    ```bash
    rm /etc/yum/vars/releasever
     ```

1. EuS-adatfájlok letiltása:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7-eus'
   ```

1. Rhel VM konfigurálása
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
    ```

1. Az RHEL vm frissítése
    ```bash
    sudo yum update
    ```

## <a name="the-ips-for-the-rhui-content-delivery-servers"></a>Az RHUI tartalomkézbesítési kiszolgálók IP-je

Az RHUI minden olyan régióban elérhető, ahol igény szerinti RHEL-lemezképek érhetők el. Jelenleg az [Azure status dashboard](https://azure.microsoft.com/status/) lapján, az Azure US Government és a Microsoft Azure Germany régióiban felsorolt összes nyilvános régiót tartalmazza.

Ha hálózati konfigurációt használ az RHEL PAYG virtuális gépek hozzáférésének további korlátozására, `yum update` győződjön meg arról, hogy a következő IP-k a környezettől függően dolgozhatnak:


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

## <a name="azure-rhui-infrastructure"></a>Azure RHUI infrastruktúra


### <a name="update-expired-rhui-client-certificate-on-a-vm"></a>Lejárt RHUI-ügyféltanúsítvány frissítése virtuális gépen

Ha egy régebbi RHEL virtuálisgép-lemezképet használ, például RHEL 7.4 (image URN: `RedHat:RHEL:7.4:7.4.2018010506`) , a már lejárt TLS/SSL ügyféltanúsítvány miatt kapcsolódási problémákat fog tapasztalni az RHUI-val. A jelenlévő hiba a következőképpen nézhet ki: _"Az SSL-társ lejártként utasította el a tanúsítványt"_ vagy _"Hiba: Nem lehet beolvasni a tárház metaadatait (repomd.xml) a tárházhoz: ... Ellenőrizze az elérési útját, majd próbálkozzon újra"_. A probléma megoldásához frissítse az RHUI ügyfélcsomagot a virtuális gépen a következő paranccsal:

```bash
sudo yum update -y --disablerepo='*' --enablerepo='*microsoft*'
```

Másik lehetőségként `sudo yum update` a futás is frissítheti az ügyfél tanúsítványcsomag (attól függően, hogy az RHEL verzió), annak ellenére, hogy "lejárt SSL tanúsítvány" hibák at látni fogja a többi tárolók. Ha a frissítés sikeres, a többi RHUI-adattárhoz való normál kapcsolatot `sudo yum update` vissza kell állítani, így sikeresen futtatható lesz.

Ha egy 404-es hibaüzenetet `yum update`ad a futtatása közben, próbálja meg az alábbiakat a yum-gyorsítótár frissítéséhez:
```bash
sudo yum clean all;
sudo yum makecache
```

### <a name="troubleshoot-connection-problems-to-azure-rhui"></a>Az Azure RHUI-val kapcsolatos csatlakozási problémák elhárítása
Ha problémákat tapasztal az Azure RHEL PAYG virtuális gépről az Azure RHUI-hoz való csatlakozáskor, kövesse az alábbi lépéseket:

1. Vizsgálja meg a virtuális gép konfigurációját az Azure RHUI-végpont:

    1. Ellenőrizze, `/etc/yum.repos.d/rh-cloud.repo` hogy `rhui-[1-3].microsoft.com` a fájl tartalmaz-e `[rhui-microsoft-azure-rhel*]` hivatkozást a fájl `baseurl` szakaszában. Ha igen, akkor az új Azure RHUI-t használja.

    1. Ha a következő mintával rendelkező `mirrorlist.*cds[1-4].cloudapp.net`helyre mutat, konfigurációs frissítésre van szükség. A régi virtuális gép pillanatképét használja, és frissítenie kell, hogy az új Azure RHUI-ra mutasson.

1. Az Azure által üzemeltetett RHUI-hoz való hozzáférés az [Azure adatközpont IP-tartományain](https://www.microsoft.com/download/details.aspx?id=41653)belüli virtuális gépekre korlátozódik.

1. Ha az új konfigurációt használja, ellenőrizte, hogy a virtuális gép csatlakozik az Azure IP-tartományból, és továbbra sem tud csatlakozni az Azure RHUI-hoz, nyújtson be támogatási esetet a Microsoftvagy a Red Hat szolgáltatáshoz.

### <a name="infrastructure-update"></a>Infrastruktúra frissítése

2016 szeptemberében üzembe helyeztünk egy frissített Azure RHUI-t. 2017 áprilisában leállítottuk a régi Azure RHUI-t. Ha 2016 szeptemberétől vagy azok pillanatképeit használta, automatikusan csatlakozik az új Azure RHUI-hoz. Ha azonban régebbi pillanatképek et a virtuális gépeken, manuálisan kell frissítenia a konfigurációt az Azure RHUI eléréséhez a következő szakaszban leírtak szerint.

Az új Azure RHUI-kiszolgálók at az [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/)telepíti. A Traffic Manager egyetlen végpont (rhui-1.microsoft.com) bármely virtuális gép által használható, régiótól függetlenül.

### <a name="manual-update-procedure-to-use-the-azure-rhui-servers"></a>Manuális frissítési eljárás az Azure RHUI-kiszolgálók használatához
Erre az eljárásra csak tájékoztató jellegű. AZ RHEL PAYG-lemezképek már rendelkeznek a megfelelő konfigurációval az Azure RHUI-hoz való csatlakozáshoz. Ha manuálisan szeretné frissíteni a konfigurációt az Azure RHUI-kiszolgálók használatára, hajtsa végre az alábbi lépéseket:

- Az RHEL 6 esetében:
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel6.config' install 'rhui-azure-rhel6'
  ```

- Az RHEL 7 esetében:
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
  ```

- Az RHEL 8 esetében:
    1. Konfigurációs fájl létrehozása:
        ```bash
        vi rhel8.config
        ```
    1. Adja hozzá a következő tartalmat a konfigurációs fájlhoz:
        ```bash
        [rhui-microsoft-azure-rhel8]
        name=Microsoft Azure RPMs for Red Hat Enterprise Linux 8
        baseurl=https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel8 https://rhui-2.microsoft.com/pulp/repos/microsoft-azure-rhel8 https://rhui-3.microsoft.com/pulp/repos/microsoft-azure-rhel8
        enabled=1
        gpgcheck=1
        gpgkey=https://rhelimage.blob.core.windows.net/repositories/RPM-GPG-KEY-microsoft-azure-release sslverify=1
        ```
    1. Mentse a fájlt, és futtassa a következő parancsot:
        ```bash
        dnf --config rhel8.config install 'rhui-azure-rhel8'
        ```
    1. A virtuális gép frissítése
        ```bash
        sudo dnf update
        ```


## <a name="next-steps"></a>További lépések
* Ha egy Azure Marketplace PAYG-lemezképből szeretne Red Hat Enterprise Linux virtuális géphez, és az Azure által üzemeltetett RHUI-t szeretné használni, nyissa meg az [Azure Piacteret.](https://azure.microsoft.com/marketplace/partners/redhat/)
* Ha többet szeretne megtudni a Red Hat-képekről az Azure-ban, látogasson el a [dokumentációs lapra.](./redhat-images.md)
* A Red Hat támogatási irányelveiről az RHEL összes verziójára vonatkozó információk a [Red Hat Enterprise Linux Életciklus](https://access.redhat.com/support/policy/updates/errata) oldalán találhatók.
