---
title: Red Hat frissítési infrastruktúrához |} A Microsoft Docs
description: További információk a Red Hat frissítési infrastruktúrához az igény szerinti Red Hat Enterprise Linux-példányok a Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: jeconnoc
editor: ''
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 6/6/2019
ms.author: borisb
ms.openlocfilehash: 4315a849f3f117633f5f6a9d93c995ece9f527a3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67077000"
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Az igény szerinti Red Hat Enterprise Linux virtuális gépek az Azure-beli Red Hat frissítési infrastruktúrája
 [Red Hat frissítési infrastruktúrához](https://access.redhat.com/products/red-hat-update-infrastructure) (RHUI) lehetővé teszi a felhőszolgáltatók, például az Azure Red Hat-ban üzemeltetett tárház tartalmának tükrözik, az Azure-ra vonatkozó egyéni adattárak tartalom létrehozása és végfelhasználói virtuális gépek számára elérhető legyen.

Red Hat Enterprise Linux (RHEL) használatalapú fizetéssel szemben lemezképek származnak, előre konfigurált Azure RHUI eléréséhez. További konfiguráció nélkül van szükség. A legújabb frissítések lekéréséhez futtassa `sudo yum update` után készen áll az RHEL-példányhoz. Ez a szolgáltatás részét képezi részét képezi a RHEL Használatalapú szoftver díjak.

További információk az Azure-ban, közzététel és adatmegőrzési szabályzatok, például az RHEL-lemezképekhez érhető el [Itt](./rhel-images.md).

Red Hat támogatási irányelveik RHEL összes verziója információk találhatók a [Red Hat Enterprise Linux életciklusának](https://access.redhat.com/support/policy/updates/errata) lapot.

## <a name="important-information-about-azure-rhui"></a>Azure RHUI vonatkozó fontos információk
* Az Azure RHUI a frissítési infrastruktúra, amely támogatja a RHEL Használatalapú létrehozott minden virtuális gép az Azure-ban. Ez nem zárja ki, Regisztráljon a Használatalapú RHEL rendszerű virtuális géphez az előfizetés-kezelő, műholdas vagy más forrásból, a frissítések, de ezzel a virtuális gép PAYG közvetett double-számlázási eredményez. Tekintse meg a részleteket a következő pontot.
* Az Azure-ban üzemeltetett RHUI a hozzáférést a RHEL Használatalapú lemezképének díja tartalmazza. Ha akkor regisztrációját, az Azure-ban üzemeltetett RHUI PAYG RHEL virtuális Gépet, amely nem átalakítása a virtuális gép egy virtuális gép bring-your-saját licenc (használata BYOL) típusú. Ha ugyanazon a virtuális Gépen egy másik forrása a frissítések regisztrál, vonatkozhatnak _közvetett_ díjak duplán. Az első alkalommal az Azure RHEL szoftvert díjat fizetnie. A második alkalommal a Red Hat-előfizetéseit, korábban beszerzett díjkötelesek. Ha folyamatosan szeretné használni egy frissítési infrastruktúra eltérő Azure-ban üzemeltetett RHUI, érdemes lehet használandó regisztrálása a [RHEL saját lemezképek](https://aka.ms/rhel-byos).
* RHUI alapértelmezett viselkedését, hogy az RHEL virtuális gép frissítése a legújabb alverzió futtatásakor `sudo yum update`.

    Például, ha egy RHEL 7.4 PAYG rendszerképből egy virtuális gép létrehozása és futtatása `sudo yum update`, akkor megtörténhet egy RHEL 7.6 virtuális gép (a legújabb alverzió a RHEL7 termékcsalád).

    Ez a viselkedés elkerülése érdekében átválthat az [kiterjesztett támogatásának csatornák](#rhel-eus-and-version-locking-rhel-vms) , vagy létrehozhatja a saját rendszerképét leírtak szerint a [létrehozása és feltöltése a Red Hat-alapú virtuális gépek az Azure-ban](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) cikk. Ha saját rendszerképet hoz létre, csatlakoztathatja azt egy másik frissítési infrastruktúra szeretne ([közvetlenül a Red Hat content delivery kiszolgálók](https://access.redhat.com/solutions/253273) vagy egy [Red Hat műholdas kiszolgáló](https://access.redhat.com/products/red-hat-satellite)).



* RHEL SAP PAYG-lemezképek az Azure-ban (RHEL for SAP, az RHEL for SAP HANA és az RHEL for SAP Business Applications) dedikált RHUI csatornák, amelyek az adott RHEL alverzió SAP minősítésre szükség szerint továbbra is csatlakozik.

* Az Azure-ban üzemeltetett RHUI, korlátozott a virtuális gépeket a [Azure adatközpont IP-címtartományait](https://www.microsoft.com/download/details.aspx?id=41653). Ha Ön a proxyhasználat minden virtuális gép forgalom keresztül egy helyszíni hálózati infrastruktúrát, szüksége lehet a RHEL Használatalapú virtuális gépeket az Azure RHUI eléréséhez a felhasználó által megadott útvonalak beállítása.

## <a name="rhel-eus-and-version-locking-rhel-vms"></a>RHEL EUS és RHEL rendszerű virtuális géphez verzió zárolása
Előfordulhat, hogy egyes ügyfeleknek szeretné zárolni az RHEL rendszerű virtuális géphez egy bizonyos RHEL kisebb kiadásra. Így verzió-zárolási az RHEL virtuális gép egy adott alverzió, hogy a kiterjesztett támogatásának tárházak mutasson az adattárak frissítése. A EUS verzió zárolása műveletet is visszavonhatja.

>[!NOTE]
> EUS RHEL kiegészítő funkciók a nem támogatott. Ez azt jelenti, hogy telepít egy csomagot, amely általában a RHEL kiegészítő funkciók csatorna érhető el, ha nem tudja ehhez a EUS. A Red Hat kiegészítő funkciók életciklusáról részletes [Itt](https://access.redhat.com/support/policy/updates/extras/).

A cikk írásának időpontjában EUS támogatása véget ért, az RHEL < = 7.3. A "Red Hat Enterprise Linux hosszabb támogatási bővítmények" című szakaszában talál a [Red Hat-dokumentáció](https://access.redhat.com/support/policy/updates/errata/) további részletekért.
* RHEL 7.4 EUS támogatás véget 2019. augusztus 31-ig.
* RHEL 7.5 EUS támogatás véget 2020. április 30.
* RHEL 7.6 EUS támogatás véget 2020. október 31-ig.

### <a name="switch-a-rhel-vm-to-eus-version-lock-to-a-specific-minor-version"></a>Váltson egy RHEL rendszerű virtuális Géphez EUS (verzió zárolás egy meghatározott kisebb verzióra)
Egy adott kisebb kiadás (Futtatás rendszergazdaként) az RHEL virtuális gép zárolását kövesse az alábbi utasításokat:

>[!NOTE]
> Ez csak érvényes RHEL-verziók, amelynek EUS érhető el. A cikk írásának időpontjában RHEL 7.2-7.6 ide tartoznak. További részletek a [Red Hat Enterprise Linux életciklusának](https://access.redhat.com/support/policy/updates/errata) lapot.

1. Nem EUS adattárakkal letiltása:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7'
    ```

1. Adja hozzá a EUS adattárakkal:
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7-eus.config' install 'rhui-azure-rhel7-eus'
    ```

1. Zárolási (Futtatás rendszergazdaként) releasever változó:
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > A fenti utasítás zárolja a RHEL kisebb kiadás, az aktuális kisebb kiadásra. Adjon meg egy adott kisebb kiadás, ha arra kíváncsi, frissítése és a egy újabb kisebb kiadás, amely nem a legújabb zárolása. Ha például `echo 7.5 > /etc/yum/vars/releasever` az RHEL-verzió az RHEL 7.5 lesz zárolva.

1. Az RHEL virtuális gép frissítése
    ```bash
    sudo yum update
    ```

### <a name="switch-a-rhel-vm-back-to-non-eus-remove-a-version-lock"></a>Váltson vissza egy RHEL rendszerű virtuális Géphez nem EUS (eltávolítása egy verzió zárolás)
Futtassa a következő legfelső szintű:
1. Távolítsa el a releasever fájlt:
    ```bash
    rm /etc/yum/vars/releasever
     ```

1. EUS adattárakkal letiltása:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7-eus'
   ```

1. Az RHEL virtuális gép frissítése
    ```bash
    sudo yum update
    ```

## <a name="the-ips-for-the-rhui-content-delivery-servers"></a>A tartalomkézbesítési kiszolgálók RHUI IP-címek

RHUI minden olyan régióban, ahol érhetők el az igényalapú RHEL-lemezképekhez érhető el. A felsorolt összes nyilvános régióban jelenleg tartalmazza a [Azure állapotjelző irányítópultján](https://azure.microsoft.com/status/) oldal, az Azure US Government és a Microsoft Azure Germany-régiókat.

Ha a hozzáférés további korlátozását RHEL Használatalapú virtuális gépekről egy hálózati konfigurációt használ, ellenőrizze, hogy a következő IP-címek engedélyezettek `yum update` használja-e a környezettől függően működéséhez:


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

## <a name="azure-rhui-infrastructure"></a>Az Azure RHUI infrastruktúra


### <a name="update-expired-rhui-client-certificate-on-a-vm"></a>Frissítés a virtuális gép RHUI ügyféltanúsítvány lejárt

Ha egy régebbi RHEL Virtuálisgép-rendszerképet, például az RHEL-7.4 használja (a kép URN: `RedHat:RHEL:7.4:7.4.2018010506`), kapcsolódási problémák miatt lejárt SSL-ügyféltanúsítvány RHUI fog tapasztalni. A hibát látja nézhet _"SSL társ visszautasította a tanúsítvány lejárt,"_ vagy _"hiba: Nem sikerült beolvasni a tárház metaadatok (repomd.xml) tárház:... Ellenőrizze az elérési útját, és próbálkozzon újra"_ . A probléma megoldásához frissítse az RHUI ügyfélcsomag, a virtuális gépen a következő paranccsal:

```bash
sudo yum update -y --disablerepo='*' --enablerepo='*microsoft*'
```

Másik lehetőségként futó `sudo yum update` is frissítheti a ügyfél-csomag (függően az RHEL verzió), annak ellenére, hogy más tárházakban megjelenik "a SSL-tanúsítvány lejárt" hibák. Ha a frissítés sikeres, más RHUI tárházakban normál kapcsolatot kell visszaállítani, ezért lesz futtatható `sudo yum update` sikeresen megtörtént.

Ha a 404-es hiba futtatása közben egy `yum update`, a yum használatával gyorsítótár a következő:
```bash
sudo yum clean all;
sudo yum makecache
```

### <a name="troubleshoot-connection-problems-to-azure-rhui"></a>Az Azure RHUI csatlakozási problémák elhárítása
Ha Azure RHUI csatlakozik az Azure RHEL Használatalapú virtuális gépből problémákat tapasztal, kövesse az alábbi lépéseket:

1. Vizsgálja meg az Azure RHUI végpont a Virtuálisgép-konfiguráció:

    1. Ellenőrizze, hogy a `/etc/yum.repos.d/rh-cloud.repo` fájl tartalmaz egy hivatkozást `rhui-[1-3].microsoft.com` a a `baseurl` , a `[rhui-microsoft-azure-rhel*]` fájl szakaszában. Ha igen, az új Azure RHUI használ.

    1. A következő mintával rendelkező helyre mutat `mirrorlist.*cds[1-4].cloudapp.net`, a konfiguráció frissítése megadása kötelező. A régi virtuális gép pillanatképét használ, és frissítenie kell, hogy az új Azure RHUI mutasson.

1. Az Azure-ban üzemeltetett RHUI, korlátozott belüli virtuális gépek a [Azure adatközpont IP-címtartományait](https://www.microsoft.com/download/details.aspx?id=41653).

1. Az új konfigurációt használja, ha ellenőrizte, hogy a virtuális gép csatlakozik az Azure IP-címtartományból, és továbbra sem tud kapcsolódni az Azure RHUI, egy támogatási esetet, amelyben a Microsoft vagy a Red Hat fájl.

### <a name="infrastructure-update"></a>Infrastruktúra frissítése

2016 szeptemberétől üzembe helyeztünk egy frissített Azure RHUI. A 2017 április hogy állítsa le a régi Azure RHUI. Ha már használja a RHEL Használatalapú képet (vagy a pillanatképek) 2016. szeptember vagy újabb, automatikusan csatlakozik az új Azure RHUI. Ha azonban rendelkezik a korábbi pillanatképek a virtuális gépeken, a konfigurációt az Azure RHUI eléréséhez a következő szakaszban leírtak szerint manuálisan frissíteni szeretné.

Az új Azure RHUI kiszolgálókra telepítik [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/). A Traffic Manager egy végpontot (rhui-1.microsoft.com) minden virtuális gép, függetlenül attól, régió használható.

### <a name="manual-update-procedure-to-use-the-azure-rhui-servers"></a>Manuális frissítés eljárást használja az Azure RHUI kiszolgálók
Ez az eljárás csak referenciaként van megadva. RHEL Használatalapú lemezképek már rendelkezik a megfelelő konfigurációt szeretne csatlakozni az Azure RHUI. Manuálisan frissítse a konfigurációt az Azure RHUI kiszolgálókat használ, hajtsa végre az alábbi lépéseket:

- Az RHEL 6:
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel6.config' install 'rhui-azure-rhel6'
  ```

- Az RHEL 7:
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
  ```

## <a name="next-steps"></a>További lépések
* Red Hat Enterprise Linux virtuális gép létrehozása az Azure Marketplace-en PAYG rendszerképből, és használata az Azure-ban üzemeltetett RHUI, nyissa meg a [Azure Marketplace-en](https://azure.microsoft.com/marketplace/partners/redhat/).
* Az Azure-beli Red Hat-lemezképeit kapcsolatos további tudnivalókért keresse fel a [dokumentációs oldalon](./rhel-images.md).
* Red Hat támogatási irányelveik RHEL összes verziója információk találhatók a [Red Hat Enterprise Linux életciklusának](https://access.redhat.com/support/policy/updates/errata) lapot.
