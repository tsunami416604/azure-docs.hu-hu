---
title: "Linux virtuális gépek DHCPv6 konfigurálása |} Microsoft Docs"
description: "Hogyan kell konfigurálni a DHCPv6 Linux virtuális gépekhez."
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
keywords: "IPv6-alapú, azure load balancer, kettős verem, nyilvános IP-cím, natív ipv6, mobil, iot"
ms.assetid: b32719b6-00e8-4cd0-ba7f-e60e8146084b
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 84558cb6e3a5524969f590eb0272a64ad8839ab5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="configuring-dhcpv6-for-linux-vms"></a>A DHCPv6 konfigurálása Linux rendszerű virtuális gépekhez

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Egyes Linux virtuális gép képek az Azure piactéren DHCPv6 alapértelmezés szerint nem rendelkeznek. IPv6 támogatása érdekében DHCPv6 meg kell adni az Ön által használt Linux operációs rendszert futtató terjesztési belül. Különböző Linux terjesztések átviteli DHCPv6 konfigurálása, mert használnak a különböző csomagok különböző módokat rendelkezik.

> [!NOTE]
> Az Azure piactéren legutóbbi SUSE Linux és a CoreOS képek előre konfigurált a DHCPv6 törölték. Nincs további módosítások szükségesek, ha használja ezeket a képeket.

Ez a dokumentum ismerteti a DHCPv6 engedélyezése, hogy a Linux virtuális gép IPv6-címet kap.

> [!WARNING]
> Helytelen a hálózati konfigurációs fájlok szerkesztésével okozhat, hogy a virtuális gép hálózati megszakadna. Azt javasoljuk, hogy tesztelje a konfigurációs módosítások nem éles rendszerek esetén. A jelen cikkben lévő utasítások a Linux-lemezképek, az Azure piactéren legújabb verziói lettek tesztelve. Részletes útmutatás Linux adott verziójának dokumentációjában.

## <a name="ubuntu"></a>Ubuntu

1. A fájl szerkesztése `/etc/dhcp/dhclient6.conf` és adja hozzá a következő sort:

        timeout 10;

2. Szerkessze a hálózati konfigurációt a eth0 kapcsolat a következő beállításokkal:

   * A **Ubuntu 12.04 és 14.04**, a fájl szerkesztése`/etc/network/interfaces.d/eth0.cfg`
   * A **Ubuntu 16.04**, a fájl szerkesztése`/etc/network/interfaces.d/50-cloud-init.cfg`

         iface eth0 inet6 auto
             up sleep 5
             up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Újítsa meg az IPv6-cím:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="debian"></a>Debian

1. A fájl szerkesztése `/etc/dhcp/dhclient6.conf` és adja hozzá a következő sort:

        timeout 10;

2. A fájl szerkesztése `/etc/network/interfaces` , és adja hozzá a következő konfigurációt:

        iface eth0 inet6 auto
            up sleep 5
            up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Újítsa meg az IPv6-cím:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="rhel--centos--oracle-linux"></a>RHEL / CentOS / Oracle Linux

1. A fájl szerkesztése `/etc/sysconfig/network` és adja hozzá a következő paramétert:

        NETWORKING_IPV6=yes

2. A fájl szerkesztése `/etc/sysconfig/network-scripts/ifcfg-eth0` , és adja hozzá az alábbi két paramétert:

        IPV6INIT=yes
        DHCPV6C=yes

3. Újítsa meg az IPv6-cím:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-11--opensuse-13"></a>SLES 11 & openSUSE 13

Az Azure-ban legutóbbi SLES és openSUSE képek előre konfigurált a DHCPv6 törölték. Nincs további módosítások szükségesek, ha használja ezeket a képeket. Ha egy virtuális Gépet egy korábbi vagy egyéni SUSE lemezképen alapuló, tegye a következőket:

1. Telepítse a `dhcp-client` csomagot, ha szükséges:

    ```bash
    sudo zypper install dhcp-client
    ```

2. A fájl szerkesztése `/etc/sysconfig/network/ifcfg-eth0` és adja hozzá a következő paramétert:

        DHCLIENT6_MODE='managed'

3. Újítsa meg az IPv6-cím:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>SLES 12 és openSUSE termékek

Az Azure-ban legutóbbi SLES és openSUSE képek előre konfigurált a DHCPv6 törölték. Nincs további módosítások szükségesek, ha használja ezeket a képeket. Ha egy virtuális Gépet egy korábbi vagy egyéni SUSE lemezképen alapuló, tegye a következőket:

1. A fájl szerkesztése `/etc/sysconfig/network/ifcfg-eth0` , és cserélje le ezt a paramétert

        #BOOTPROTO='dhcp4'

    a következő értékkel:

        BOOTPROTO='dhcp'

2. Adja hozzá a következő paraméter `/etc/sysconfig/network/ifcfg-eth0`:

        DHCLIENT6_MODE='managed'

3. Újítsa meg az IPv6-cím:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

Az Azure-ban legutóbbi CoreOS képek előre konfigurált a DHCPv6 törölték. Nincs további módosítások szükségesek, ha használja ezeket a képeket. Ha egy virtuális Gépet egy korábbi vagy egyéni CoreOS lemezképen alapuló, tegye a következőket:

1. A fájl szerkesztése`/etc/systemd/network/10_dhcp.network`

        [Match]
        eth0

        [Network]
        DHCP=ipv6

2. Újítsa meg az IPv6-cím:

    ```bash
    sudo systemctl restart systemd-networkd
    ```
