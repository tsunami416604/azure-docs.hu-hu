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
ms.date: 1/7/2019
ms.author: borisb
ms.openlocfilehash: 1a1038bec66cd90e2cd0cbc8b125857403317d89
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54078252"
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Az igény szerinti Red Hat Enterprise Linux virtuális gépek az Azure-beli Red Hat frissítési infrastruktúrája
 [Red Hat frissítési infrastruktúrához](https://access.redhat.com/products/red-hat-update-infrastructure) (RHUI) lehetővé teszi a felhőszolgáltatók, például az Azure Red Hat-ban üzemeltetett tárház tartalmának tükrözik, az Azure-ra vonatkozó egyéni adattárak tartalom létrehozása és végfelhasználói virtuális gépek számára elérhető legyen.

Red Hat Enterprise Linux (RHEL) használatalapú fizetéssel szemben lemezképek származnak, előre konfigurált Azure RHUI eléréséhez. További konfiguráció nélkül van szükség. A legújabb frissítések lekéréséhez futtassa `sudo yum update` után készen áll az RHEL-példányhoz. Ez a szolgáltatás részét képezi részét képezi a RHEL Használatalapú szoftver díjak.

További információk az Azure-ban, közzététel és adatmegőrzési szabályzatok, például az RHEL-lemezképekhez érhető el [Itt](./rhel-images.md).

## <a name="important-information-about-azure-rhui"></a>Azure RHUI vonatkozó fontos információk
* Azure RHUI jelenleg csak a legújabb kisebb kiadás minden RHEL termékcsalád (bites RHEL6 vagy RHEL7). A kisebb legújabb RHUI csatlakozik egy RHEL Virtuálisgép-példány frissítéséhez futtassa `sudo yum update`.

    Például, ha egy RHEL 7.4 PAYG rendszerképből egy virtuális gép létrehozása és futtatása `sudo yum update`, akkor megtörténhet egy RHEL 7.6 virtuális gép (a legújabb alverzió a RHEL7 termékcsalád).

    Ez a viselkedés elkerülése érdekében a saját rendszerkép összeállítása a leírtak szerint kell az [létrehozása és feltöltése a Red Hat-alapú virtuális gépek az Azure-ban](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) cikk. Akkor kell csatlakoztathatja azt egy másik frissítési infrastruktúrája ([közvetlenül a Red Hat content delivery kiszolgálók](https://access.redhat.com/solutions/253273) vagy egy [Red Hat műholdas kiszolgáló](https://access.redhat.com/products/red-hat-satellite)).

* Az Azure-ban üzemeltetett RHUI a hozzáférést a RHEL Használatalapú lemezképének díja tartalmazza. Ha akkor regisztrációját, az Azure-ban üzemeltetett RHUI PAYG RHEL virtuális Gépet, amely nem átalakítása a virtuális gép egy virtuális gép bring-your-saját licenc (használata BYOL) típusú. Ha ugyanazon a virtuális Gépen egy másik forrása a frissítések regisztrál, vonatkozhatnak _közvetett_ díjak duplán. Az első alkalommal az Azure RHEL szoftvert díjat fizetnie. A második alkalommal a Red Hat-előfizetéseit, korábban beszerzett díjkötelesek. Ha folyamatosan szeretné használni egy frissítési infrastruktúra eltérő Azure-ban üzemeltetett RHUI, fontolja meg a létrehozása és telepítése a saját (BYOL-típus) rendszerképeit. Ez a folyamat leírását [létrehozása és feltöltése a Red Hat-alapú virtuális gépek az Azure-ban](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

* RHEL SAP PAYG-lemezképek az Azure-ban (RHEL for SAP, az RHEL for SAP HANA és az RHEL for SAP Business Applications) dedikált RHUI csatornák, amelyek az adott RHEL alverzió SAP minősítésre szükség szerint továbbra is csatlakozik.

* Az Azure-ban üzemeltetett RHUI, korlátozott a virtuális gépeket a [Azure adatközpont IP-címtartományait](https://www.microsoft.com/download/details.aspx?id=41653). Ha Ön a proxyhasználat minden virtuális gép forgalom keresztül egy helyszíni hálózati infrastruktúrát, szüksége lehet a RHEL Használatalapú virtuális gépeket az Azure RHUI eléréséhez a felhasználó által megadott útvonalak beállítása.

### <a name="rhel-eus-and-version-locking-rhel-vms"></a>RHEL EUS és RHEL rendszerű virtuális géphez verzió zárolása
Előfordulhat, hogy egyes ügyfeleknek szeretné zárolni az RHEL rendszerű virtuális géphez egy bizonyos RHEL kisebb kiadásra. Így verzió-zárolási az RHEL virtuális gép egy adott alverzió, hogy a kiterjesztett támogatásának tárházak mutasson az adattárak frissítése. Zárolhatja az RHEL virtuális gép egy adott kisebb kiadás, használja az alábbi utasításokat:

>[!NOTE]
> Ez csak érvényes RHEL-verziók, amelynek EUS érhető el. A cikk írásának időpontjában RHEL 7.2-7.6 ide tartoznak. További részletek a [Red Hat Enterprise Linux életciklusának](https://access.redhat.com/support/policy/updates/errata) lapot.

1. Nem EUS adattárakkal letiltása:
    ```bash
    sudo yum --disablerepo=* remove rhui-azure-rhel7
    ```

1. Adja hozzá a EUS adattárakkal:
    ```bash
    yum --config=https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7-eus.config install rhui-azure-rhel7-eus
    ```

1. Zárolási releasever változó:
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > A fenti utasítás zárolja a RHEL kisebb kiadás, az aktuális kisebb kiadásra. Adjon meg egy adott kisebb kiadás, ha arra kíváncsi, frissítése és a egy újabb kisebb kiadás, amely nem a legújabb zárolása. Ha például `echo 7.5 > /etc/yum/vars/releasever` az RHEL-verzió az RHEL 7.5 lesz zárolva.

1. Az RHEL virtuális gép frissítése
    ```bash
    sudo yum update
    ```

### <a name="the-ips-for-the-rhui-content-delivery-servers"></a>A tartalomkézbesítési kiszolgálók RHUI IP-címek

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

# Azure Germany
51.5.243.77
51.4.228.145
```

## <a name="rhui-azure-infrastructure-update"></a>RHUI Azure infrastruktúra frissítése

2016 szeptemberétől üzembe helyeztünk egy frissített Azure RHUI. A 2017 április hogy állítsa le a régi Azure RHUI. Ha már használja a RHEL Használatalapú képet (vagy a pillanatképek) 2016. szeptember vagy újabb, automatikusan csatlakozik az új Azure RHUI. Ha azonban rendelkezik a korábbi pillanatképek a virtuális gépeken, a konfigurációt az Azure RHUI eléréséhez a következő szakaszban leírtak szerint manuálisan frissíteni szeretné.

Az új Azure RHUI kiszolgálókra telepítik [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/). A Traffic Manager egy végpontot (rhui-1.microsoft.com) minden virtuális gép, függetlenül attól, régió használható.

### <a name="update-expired-rhui-client-certificate-on-a-vm"></a>Frissítés a virtuális gép RHUI ügyféltanúsítvány lejárt

Ha egy régebbi RHEL Virtuálisgép-rendszerképet, például az RHEL-7.4 használ (kép URN: `RedHat:RHEL:7.4:7.4.2018010506`), egy SSL-ügyfél (a november 21 2018) lejárt tanúsítvány miatt RHUI való csatlakozás hibáinak fog tapasztalni. A probléma megoldásához frissítse az RHUI ügyfélcsomag, a virtuális gépen a következő paranccsal:

```bash
sudo yum update -y --disablerepo=* --enablerepo=rhui-microsoft-* rhui-azure-rhel7
```

Ha az RHEL virtuális gép az USA kormányzati felhőkörnyezetében, használja a következő parancsot:
```bash
sudo yum update -y --disablerepo=* --enablerepo=rhui-microsoft-* rhui-usgov-rhel7
```

Másik lehetőségként futó `sudo yum update` is frissíti a más tárházakban megjelenik "a SSL-tanúsítvány lejárt" hibák ellenére ügyfél-csomag. A frissítés a következő más RHUI tárházakban normál kapcsolatot kell visszaállítani, ezért lesz futtatható `sudo yum update` sikeresen megtörtént.


### <a name="troubleshoot-connection-problems-to-azure-rhui"></a>Az Azure RHUI csatlakozási problémák elhárítása
Ha Azure RHUI csatlakozik az Azure RHEL Használatalapú virtuális gépből problémákat tapasztal, kövesse az alábbi lépéseket:

1. Vizsgálja meg az Azure RHUI végpont a Virtuálisgép-konfiguráció:

    a. Ellenőrizze, hogy a `/etc/yum.repos.d/rh-cloud.repo` fájl tartalmaz egy hivatkozást `rhui-[1-3].microsoft.com` a a `baseurl` , a `[rhui-microsoft-azure-rhel*]` fájl szakaszában. Ha igen, az új Azure RHUI használ.

    b. A következő mintával rendelkező helyre mutat `mirrorlist.*cds[1-4].cloudapp.net`, a konfiguráció frissítése megadása kötelező. A régi virtuális gép pillanatképét használ, és frissítenie kell, hogy az új Azure RHUI mutasson.

1. Az Azure-ban üzemeltetett RHUI, korlátozott belüli virtuális gépek a [Azure adatközpont IP-címtartományait](https://www.microsoft.com/download/details.aspx?id=41653).

1. Az új konfigurációt használja, ha ellenőrizte, hogy a virtuális gép csatlakozik az Azure IP-címtartományból, és továbbra sem tud kapcsolódni az Azure RHUI, egy támogatási esetet, amelyben a Microsoft vagy a Red Hat fájl.

### <a name="manual-update-procedure-to-use-the-azure-rhui-servers"></a>Manuális frissítés eljárást használja az Azure RHUI kiszolgálók
Ez az eljárás csak referenciaként van megadva. RHEL Használatalapú lemezképek már rendelkezik a megfelelő konfigurációt szeretne csatlakozni az Azure RHUI. Manuálisan frissítse a konfigurációt az Azure RHUI kiszolgálókat használ, hajtsa végre az alábbi lépéseket:

1. Töltse le a curl-n keresztül a nyilvános kulcs aláírását.

   ```bash
   curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc
   ```

1. A letöltött kulcs érvényességének ellenőrzése.

   ```bash
   gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release
   ```

1. Ellenőrizze a kimenetet, és ellenőrizze a `keyid` és a `user ID packet`.

   ```bash
   Version: GnuPG v1.4.7 (GNU/Linux)
   :public key packet:
           version 4, algo 1, created 1446074508, expires 0
           pkey[0]: [2048 bits]
           pkey[1]: [17 bits]
           keyid: EB3E94ADBE1229CF
   :user ID packet: "Microsoft (Release signing) <gpgsecurity@microsoft.com>"
   :signature packet: algo 1, keyid EB3E94ADBE1229CF
           version 4, created 1446074508, md5len 0, sigclass 0x13
           digest algo 2, begin of digest 1a 9b
           hashed subpkt 2 len 4 (sig created 2015-10-28)
           hashed subpkt 27 len 1 (key flags: 03)
           hashed subpkt 11 len 5 (pref-sym-algos: 9 8 7 3 2)
           hashed subpkt 21 len 3 (pref-hash-algos: 2 8 3)
           hashed subpkt 22 len 2 (pref-zip-algos: 2 1)
           hashed subpkt 30 len 1 (features: 01)
           hashed subpkt 23 len 1 (key server preferences: 80)
           subpkt 16 len 8 (issuer key ID EB3E94ADBE1229CF)
           data: [2047 bits]
   ```

1. Telepítse a nyilvános kulcsot.

   ```bash
   sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
   sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release
   ```

1. Töltse le, győződjön meg arról, és telepítse az RPM Package Manager (RPM) ügyfél.

    >[!NOTE]
    >Alkalmazáscsomag-verziók módosíthatja. Azure RHUI manuálisan csatlakozik, megkeresni az ügyfél-csomag legújabb verziójának minden egyes RHEL-család a galériából a legújabb rendszerképet kiépítésével.

   a. Töltse le.

    - Az RHEL 6:
        ```bash
        curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel6/rhui-azure-rhel6-2.2-74.noarch.rpm
        ```

    - Az RHEL 7:
        ```bash
        curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel7/rhui-azure-rhel7-2.2-74.noarch.rpm
        ```

   b. Ellenőrizze-e.

   ```bash
   rpm -Kv azureclient.rpm
   ```

   c. Ellenőrizze a kimeneti annak biztosításához, hogy a csomag aláírása OK.

   ```bash
   azureclient.rpm:
       Header V3 RSA/SHA256 Signature, key ID be1229cf: OK
       Header SHA1 digest: OK (927a3b548146c95a3f6c1a5d5ae52258a8859ab3)
       V3 RSA/SHA256 Signature, key ID be1229cf: OK
       MD5 digest: OK (c04ff605f82f4be8c96020bf5c23b86c)
   ```

   d. Telepítse az RPM.

    ```bash
    sudo rpm -U azureclient.rpm
    ```

1. Miután végzett, győződjön meg arról, hogy a virtuális gépről az Azure RHUI elérhető.

## <a name="next-steps"></a>További lépések
* Red Hat Enterprise Linux virtuális gép létrehozása az Azure Marketplace-en PAYG rendszerképből, és használata az Azure-ban üzemeltetett RHUI, nyissa meg a [Azure Marketplace-en](https://azure.microsoft.com/marketplace/partners/redhat/).
* Az Azure-beli Red Hat-lemezképeit kapcsolatos további tudnivalókért keresse fel a [dokumentációs oldalon](./rhel-images.md).

