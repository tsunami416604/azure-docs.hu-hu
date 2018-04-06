---
title: Red Hat frissítési infrastruktúra |} Microsoft Docs
description: További tudnivalók Red Hat frissítési infrastruktúra igény Red Hat Enterprise Linux-példányok a Microsoft Azure-ban
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: timlt
editor: ''
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/02/2018
ms.author: borisb
ms.openlocfilehash: 336990373027ae13688f41e453b9a262f120c6e0
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Red Hat frissítési infrastruktúra az igény szerinti Red Hat Enterprise Linux virtuális gépek Azure-ban
 [Red Hat frissítési infrastruktúra](https://access.redhat.com/products/red-hat-update-infrastructure) (RHUI) lehetővé teszi, hogy a szolgáltatók, például az Azure-tárház Red Hat által szolgáltatott tartalom tükrözik, hozzon létre egyéni adattárak Azure-specifikus tartalom és végfelhasználói virtuális gépek számára elérhető legyen.

Red Hat Enterprise Linux (RHEL) – használatalapú fizetés (PAYG) származnak előre konfigurált Azure RHUI eléréséhez. További konfigurációra van szükség. A legújabb frissítések beszerzéséhez futtassa `sudo yum update` után készen áll a RHEL példányát. Ez a szolgáltatás részeként az RHEL PAYG szoftver díjak.

## <a name="important-information-about-azure-rhui"></a>Azure RHUI vonatkozó fontos információk
* Az Azure RHUI jelenleg csak a legújabb kisebb kiadásának minden RHEL termékcsalád (bites RHEL6 vagy RHEL7). Egy csatlakozik a legújabb alverzió RHUI RHEL Virtuálisgép-példány frissítéséhez futtassa `sudo yum update`.

    Például, ha egy virtuális gép RHEL 7.2 PAYG lemezkép telepítéséhez és futtatásához `sudo yum update`, hogy végül egy RHEL 7.4 virtuális Gépre (a legújabb alverzió a RHEL7 termékcsalád).

    Ez a viselkedés elkerülése érdekében kell felépítenie saját rendszerkép leírtak szerint a [létrehozása és feltöltése a Red Hat-alapú virtuális gépek Azure-beli](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) cikk. A különböző frissítési infrastruktúra csatlakozni kell használnia ([közvetlenül is Red Hat kézbesítés tartalom](https://access.redhat.com/solutions/253273) vagy egy [Red Hat műholdas server](https://access.redhat.com/products/red-hat-satellite)).

* Az Azure által üzemeltetett RHUI a hozzáférést az RHEL PAYG kép ár tartalmazza. Ha Ön egy PAYG RHEL virtuális gép az Azure által üzemeltetett RHUI regisztrációjának törlése, amely nem konvertálja a virtuális gép egy virtuális gép bring your-saját-licencet (BYOL) típusú. Ha regisztrált az azonos virtuális gép egy másik, a frissítések forrásaként, előfordulhat, hogy függő díj _közvetett_ duplán díjakat. Az Azure RHEL szoftver díj első alkalommal van számítjuk fel. A második alkalommal korábban beszerzett Red Hat előfizetésekhez most számítjuk fel. Ha következetesen szeretné használni az Azure által üzemeltetett RHUI eltérő frissítési infrastruktúra, fontolja meg a létrehozása és telepítése a saját (BYOL-type) lemezképek. Ez a folyamat leírását [létrehozása és feltöltése a Red Hat-alapú virtuális gépek Azure-beli](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

* Az Azure (SAP Hana RHEL) és az SAP Business alkalmazások RHEL RHEL PAYG képek két osztály maradnak az adott RHEL alverzió az SAP-tanúsításhoz szükség szerint a dedikált RHUI csatornák csatlakoznak. 

* Hozzáférés az Azure által üzemeltetett RHUI korlátozódik a virtuális gépeken belül a [Azure datacenter IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653). Ha Ön a proxy használatát az ügynökökön minden a VM forgalmat egy a helyszíni hálózati infrastruktúrán keresztül, szükség lehet az RHEL PAYG virtuális gépeket az Azure RHUI eléréséhez a felhasználó által definiált útvonalak beállítása.

### <a name="the-ips-for-the-rhui-content-delivery-servers"></a>Az IP-címek RHUI tartalomkézbesítési kiszolgálók

RHUI minden régióban, ahol elérhetők az RHEL igény szerinti lemezképek érhető el. Jelenleg szerepel az összes nyilvános régió magában foglalja a [Azure állapota irányítópult](https://azure.microsoft.com/status/) lap, a Azure Amerikai Egyesült államokbeli kormányzati és a Microsoft Azure Németország régiók. 

Tovább korlátozhatja a hozzáférést az RHEL PAYG virtuális gépek használata a hálózati konfigurációt, győződjön meg arról, hogy a következő IP-címek használata engedélyezett `yum update` attól függően, hogy van-e a környezet működéséhez: 

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

2016 szeptemberétől a frissített Azure RHUI helyeztünk üzembe. 2017. április, az azt le a régi Azure RHUI is. Ha használta az RHEL PAYG képet (vagy a pillanatképek) 2016 szeptemberétől vagy újabb, automatikusan csatlakozik az új Azure RHUI. Ha azonban rendelkezik a korábbi pillanatképek a virtuális gépeken, kell manuálisan frissíthetik az Azure RHUI eléréséhez a következő szakaszban leírt módon.

Az új Azure RHUI kiszolgálókra telepített [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/). A Traffic Manager egy végpontot (rhui-1.microsoft.com) a virtuális gép, függetlenül attól, régió használható. 

### <a name="troubleshoot-connection-problems-to-azure-rhui"></a>Az Azure RHUI csatlakozási hibák elhárítása
Ha a csatlakozás az Azure RHEL PAYG virtuális gép Azure RHUI problémákat tapasztal, kövesse az alábbi lépéseket:

1. Vizsgálja meg a Virtuálisgép-konfiguráció Azure RHUI végpont:

    a. Ellenőrizze, hogy a `/etc/yum.repos.d/rh-cloud.repo` fájl tartalmaz egy hivatkozást `rhui-[1-3].microsoft.com` a a `baseurl` , a `[rhui-microsoft-azure-rhel*]` a fájl a szakaszban. Ha igen, az új Azure RHUI használ.

    b. A következő mintával helyre mutat `mirrorlist.*cds[1-4].cloudapp.net`, szükség egy konfigurációs frissítésre. A régi virtuális gép pillanatkép használ, és frissítenie kell, hogy az új Azure RHUI mutasson.

2. Hozzáférés az Azure által üzemeltetett RHUI korlátozódik virtuális gépek belül a [Azure datacenter IP-címtartományai] (https://www.microsoft.com/download/details.aspx?id=41653).
 
3. Az új konfigurációt használ, ha ellenőrizte, hogy a virtuális gép kapcsolódik az Azure IP-címtartomány, és továbbra sem sikerül kapcsolódni Azure RHUI, a Microsoft vagy a Red Hat támogatási esetet fájlt.

### <a name="manual-update-procedure-to-use-the-azure-rhui-servers"></a>Kézi frissítés eljárást használja az Azure RHUI kiszolgálók
Ez az eljárás csak referenciaként valósul meg. RHEL PAYG képek már rendelkezik a megfelelő konfigurációs Azure RHUI való kapcsolódáshoz. Manuálisan frissíteni a beállításait az Azure RHUI kiszolgálókat szeretné használni, kövesse az alábbi lépéseket:

1. Töltse le a nyilvános kulcs curl használatával.

   ```bash
   curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 
   ```

2. A letöltött kulcs érvényességének ellenőrzésére.

   ```bash
   gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release
   ```

3. Ellenőrizze a kimenetet, és ellenőrizze a `keyid` és a `user ID packet`.

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

4. Telepítse a nyilvános kulcsot.

   ```bash
   sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
   sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release
   ```

5. Töltse le, győződjön meg arról, és telepíteni egy ügyfelet RPM Package Manager (RPM).
    
    >[!NOTE]
    >Alkalmazáscsomag-verziók módosítása. Ha manuálisan csatlakozik Azure RHUI, megtalálhatja az ügyfél-csomag legújabb verzióját az egyes RHEL által a legújabb lemezképet a gyűjtemény kiépítése.
  
   a. Töltse le. 
   
    - Az RHEL 6:
        ```bash
        curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel6/rhui-azure-rhel6-2.1-32.noarch.rpm 
        ```
    
    - Az RHEL 7:
        ```bash
        curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel7/rhui-azure-rhel7-2.1-19.noarch.rpm  
        ```

   b. Győződjön meg arról.

   ```bash
   rpm -Kv azureclient.rpm
   ```

   c. Ellenőrizze, hogy a csomag aláírása OK kimeneti.

   ```bash
   azureclient.rpm:
       Header V3 RSA/SHA256 Signature, key ID be1229cf: OK
       Header SHA1 digest: OK (927a3b548146c95a3f6c1a5d5ae52258a8859ab3)
       V3 RSA/SHA256 Signature, key ID be1229cf: OK
       MD5 digest: OK (c04ff605f82f4be8c96020bf5c23b86c)
   ```

   d. Telepítse a RPM.

    ```bash
    sudo rpm -U azureclient.rpm
    ```

6. Befejezése után ellenőrizze, hogy elérhető Azure RHUI a virtuális gépről.

## <a name="next-steps"></a>További lépések
Red Hat Enterprise Linux virtuális gép létrehozása Azure piactér PAYG lemezképből és Azure által üzemeltetett RHUI használatára, lépjen a [Azure piactér](https://azure.microsoft.com/marketplace/partners/redhat/). 
