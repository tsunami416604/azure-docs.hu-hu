---
title: Linux virtuális gépek DHCPv6 konfigurálása |} Microsoft Docs
description: Hogyan kell konfigurálni a DHCPv6 Linux virtuális gépekhez.
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: ''
keywords: IPv6-alapú, azure load balancer, kettős verem, nyilvános IP-cím, natív ipv6, mobil, iot
ms.assetid: b32719b6-00e8-4cd0-ba7f-e60e8146084b
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 6248ed2f55fb5bbcc2061af6ce1dedf2bd31ccad
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2018
ms.locfileid: "30261847"
---
# <a name="configure-dhcpv6-for-linux-vms"></a>Linux virtuális gépek DHCPv6 konfigurálása


Egyes Linux virtuális gép képek az Azure piactéren Dynamic Host Configuration Protocol version 6 (DHCPv6) alapértelmezés szerint nem rendelkeznek. IPv6 támogatása érdekében az Ön által használt Linux operációs rendszert futtató terjesztési DHCPv6 kell konfigurálni. A különböző Linux terjesztésekről a DHCPv6 az sokféleképpen konfigurálása, mert a különböző csomagok használata.

> [!NOTE]
> Az Azure piactéren legutóbbi SUSE Linux és a CoreOS képek előre konfigurált a DHCPv6 törölték. Nincs további módosítások szükségesek, ha ezeket a lemezképeket használja.

Ez a dokumentum ismerteti a DHCPv6 engedélyezése, hogy a Linux virtuális gép IPv6-címet kap.

> [!WARNING]
> Nem megfelelően hálózati konfigurációs fájlok szerkesztésével, a virtuális géphez elveszíti a hálózati hozzáférést. Azt javasoljuk, hogy tesztelje a konfigurációs módosítások nem éles rendszerek esetén. A jelen cikkben lévő utasítások a Linux-lemezképek, az Azure piactéren legújabb verziói lettek tesztelve. További részletes útmutatásért tekintse meg a saját Linux verziójának a dokumentációjában.

## <a name="ubuntu"></a>Ubuntu

1. Szerkessze a */etc/dhcp/dhclient6.conf* fájlt, és adja hozzá a következő sort:

        timeout 10;

2. Szerkessze a hálózati konfigurációt a eth0 kapcsolat a következő beállításokkal:

   * A **Ubuntu 12.04 és 14.04**, szerkesztheti a */etc/network/interfaces.d/eth0.cfg* fájlt. 
   * A **Ubuntu 16.04**, szerkesztheti a */etc/network/interfaces.d/50-cloud-init.cfg* fájlt.

         iface eth0 inet6 auto
             up sleep 5
             up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Újítsa meg az IPv6-cím:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="debian"></a>Debian

1. Szerkessze a */etc/dhcp/dhclient6.conf* fájlt, és adja hozzá a következő sort:

        timeout 10;

2. Szerkessze a */etc/network/interfaces* fájlt, és adja hozzá a következő konfigurációt:

        iface eth0 inet6 auto
            up sleep 5
            up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Újítsa meg az IPv6-cím:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="rhel-centos-and-oracle-linux"></a>RHEL, CentOS és Oracle Linux

1. Szerkessze a */etc/sysconfig/network* fájlt, és adja hozzá a következő paramétert:

        NETWORKING_IPV6=yes

2. Szerkessze a */etc/sysconfig/network-scripts/ifcfg-eth0* fájlt, és adja hozzá az alábbi két paramétert:

        IPV6INIT=yes
        DHCPV6C=yes

3. Újítsa meg az IPv6-cím:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-11-and-opensuse-13"></a>SLES 11 és openSUSE 13

Legutóbbi SUSE Linux Enterprise Server (SLES) és az Azure-ban openSUSE képek előre konfigurált a DHCPv6 törölték. Nincs további módosítások szükségesek, ha ezeket a lemezképeket használja. Ha egy virtuális Gépet, amely régebbi vagy egyéni SUSE lemezkép alapul, tegye a következőket:

1. Telepítse a `dhcp-client` csomagot, ha szükséges:

    ```bash
    sudo zypper install dhcp-client
    ```

2. Szerkessze a */etc/sysconfig/network/ifcfg-eth0* fájlt, és adja hozzá a következő paramétert:

        DHCLIENT6_MODE='managed'

3. Újítsa meg az IPv6-cím:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>SLES 12 és openSUSE termékek

Az Azure-ban legutóbbi SLES és openSUSE képek előre konfigurált a DHCPv6 törölték. Nincs további módosítások szükségesek, ha ezeket a lemezképeket használja. Ha egy virtuális Gépet, amely régebbi vagy egyéni SUSE lemezkép alapul, tegye a következőket:

1. Szerkessze a */etc/sysconfig/network/ifcfg-eth0* fájlt, és cserélje le a `#BOOTPROTO='dhcp4'` paraméter a következő értékkel:

        BOOTPROTO='dhcp'

2. Az a */etc/sysconfig/network/ifcfg-eth0* fájlt, adja hozzá a következő paramétert:

        DHCLIENT6_MODE='managed'

3. Újítsa meg az IPv6-cím:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

Az Azure-ban legutóbbi CoreOS képek előre konfigurált a DHCPv6 törölték. Nincs további módosítások szükségesek, ha ezeket a lemezképeket használja. Ha egy virtuális Gépet egy korábbi vagy egyéni CoreOS lemezképen alapuló, tegye a következőket:

1. Szerkessze a */etc/systemd/network/10_dhcp.network* fájlt:

        [Match]
        eth0

        [Network]
        DHCP=ipv6

2. Újítsa meg az IPv6-cím:

    ```bash
    sudo systemctl restart systemd-networkd
    ```
