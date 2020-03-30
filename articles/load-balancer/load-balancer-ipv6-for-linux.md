---
title: DHCPv6 konfigurálása Linuxos virtuális gépekhez
titleSuffix: Azure Load Balancer
description: Ebből a cikkből megtudhatja, hogyan konfigurálhatja a DHCPv6 linuxos virtuális gépekhez.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: ipv6, azúrkék terheléselosztó, kettős verem, nyilvános ip, natív ipv6, mobil, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2019
ms.author: allensu
ms.openlocfilehash: 6ea215b6aa826231e940f88c3687bb65591303f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74225325"
---
# <a name="configure-dhcpv6-for-linux-vms"></a>DHCPv6 konfigurálása Linuxos virtuális gépekhez


Az Azure Piactér néhány Linux virtuálisgép-lemezképe alapértelmezés szerint nem rendelkezik a Dynamic Host Configuration Protocol 6-os verziójával (DHCPv6). Az IPv6 támogatásához a DHCPv6-ot a használt Linux os disztribúcióban kell konfigurálni. A különböző Linux disztribúciók konfigurálják DHCPv6 a legkülönfélébb módokon, mert az általuk használt különböző csomagokat.

> [!NOTE]
> Az Azure Marketplace-en a legutóbbi SUSE Linux- és CoreOS-lemezképek előre konfigurálva vannak a DHCPv6-tal. A képek használata kor nincs szükség további módosításokra.

Ez a dokumentum azt ismerteti, hogyan engedélyezheti a DHCPv6 protokollt, hogy a Linux virtuális gép iPv6-címet kapjon.

> [!WARNING]
> A hálózati konfigurációs fájlok helytelen szerkesztésével elveszítheti a virtuális gép hez való hálózati hozzáférést. Azt javasoljuk, hogy tesztelje a konfigurációs módosításokat a nem éles rendszereken. Az ebben a cikkben található utasításokat tesztelték a Linux-lemezképek legújabb verzióiról az Azure Marketplace-en. Részletesebb utasításokat, olvassa el a dokumentációt a saját linuxos verzióját.

## <a name="ubuntu"></a>Ubuntu

1. Az */etc/dhcp/dhclient6.conf* fájl szerkesztése és a következő sor hozzáadása:

        timeout 10;

2. Az eth0-csatoló hálózati konfigurációjának szerkesztése a következő konfigurációval:

   * Az **Ubuntu 12.04 és 14.04,** edit the */etc/network/interfaces.d/eth0.cfg* file. 
   * Az **Ubuntu 16.04-en**szerkesztsd az */etc/network/interfaces.d/50-cloud-init.cfg* fájlt.

         iface eth0 inet6 auto
             up sleep 5
             up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Az IPv6-cím megújítása:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```
Az Ubuntu 17.10-től kezdve az alapértelmezett hálózati konfigurációs mechanizmus a [NETPLAN]( https://netplan.io).  A telepítés/példányosítás időpontjában a NETPLAN ezen a helyen olvassa be a hálózati konfigurációt a YAML konfigurációs fájljaiból: /{lib, etc,run}/netplan/*.yaml.

Kérjük, a konfigurációban minden ethernet-illesztőhez adjon meg egy *dhcp6:true* utasítást.  Példa:
  
        network:
          version: 2
          ethernets:
            eno1:
              dhcp6: true

A korai rendszerindítás során a netplan "network renderer" a konfigurációt a /run kapcsolóval írja, hogy átadja az https://netplan.io/referenceeszközök vezérlését a megadott hálózati démonnak A NETPLAN-ról szóló további információkért lásd: .
 
## <a name="debian"></a>Debian

1. Az */etc/dhcp/dhclient6.conf* fájl szerkesztése és a következő sor hozzáadása:

        timeout 10;

2. Az */etc/network/interfaces fájl szerkesztése,* és adja hozzá a következő konfigurációt:

        iface eth0 inet6 auto
            up sleep 5
            up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Az IPv6-cím megújítása:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="rhel-centos-and-oracle-linux"></a>RHEL, CentOS és Oracle Linux

1. Írja be az */etc/sysconfig/network* fájlt, és adja hozzá a következő paramétert:

        NETWORKING_IPV6=yes

2. Írja be az */etc/sysconfig/network-scripts/ifcfg-eth0* fájlt, és adja hozzá a következő két paramétert:

        IPV6INIT=yes
        DHCPV6C=yes

3. Az IPv6-cím megújítása:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-11-and-opensuse-13"></a>SLES 11 és openSUSE 13

A legutóbbi SUSE Linux Enterprise Server (SLES) és openSUSE-lemezképek az Azure-ban előre konfigurálva vannak a DHCPv6-tal. A képek használata kor nincs szükség további módosításokra. Ha egy régebbi vagy egyéni SUSE-lemezképen alapuló virtuális géppel rendelkezik, tegye a következőket:

1. Telepítse `dhcp-client` a csomagot, ha szükséges:

    ```bash
    sudo zypper install dhcp-client
    ```

2. Írja be az */etc/sysconfig/network/ifcfg-eth0* fájlt, és adja hozzá a következő paramétert:

        DHCLIENT6_MODE='managed'

3. Az IPv6-cím megújítása:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>SLES 12 és openSUSE Leap

A legutóbbi SLES és openSUSE-lemezképek az Azure-ban előre konfigurálva vannak a DHCPv6-tal. A képek használata kor nincs szükség további módosításokra. Ha egy régebbi vagy egyéni SUSE-lemezképen alapuló virtuális géppel rendelkezik, tegye a következőket:

1. Írja be az */etc/sysconfig/network/ifcfg-eth0* fájlt, és cserélje le a `#BOOTPROTO='dhcp4'` paramétert a következő értékre:

        BOOTPROTO='dhcp'

2. Az */etc/sysconfig/network/ifcfg-eth0* fájlhoz adja hozzá a következő paramétert:

        DHCLIENT6_MODE='managed'

3. Az IPv6-cím megújítása:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

Az Azure legutóbbi CoreOS-lemezképei előre konfigurálva vannak a DHCPv6-tal. A képek használata kor nincs szükség további módosításokra. Ha egy régebbi vagy egyéni CoreOS-lemezképen alapuló virtuális géppel rendelkezik, tegye a következőket:

1. Az */etc/systemd/network/10_dhcp.network* fájl szerkesztése:

        [Match]
        eth0

        [Network]
        DHCP=ipv6

2. Az IPv6-cím megújítása:

    ```bash
    sudo systemctl restart systemd-networkd
    ```
