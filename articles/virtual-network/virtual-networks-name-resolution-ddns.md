---
title: Dinamikus DNS segítségével hostnames regisztrálása az Azure-ban |} Microsoft Docs
description: 'Útmutató: a dinamikus DNS állomásnevek regisztrálja a saját DNS-kiszolgálók beállítása.'
services: dns
documentationcenter: na
author: subsarma
manager: vitinnan
editor: ''
ms.assetid: c315961a-fa33-45cf-82b9-4551e70d32dd
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2017
ms.author: subsarma
ms.openlocfilehash: bbbce45b7c321fd4934374c76f2a4421b125d46f
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
ms.locfileid: "31600954"
---
# <a name="use-dynamic-dns-to-register-hostnames-in-your-own-dns-server"></a>A DNS dinamikus segítségével hostnames regisztrálni a saját DNS-kiszolgáló

[Azure biztosít névfeloldás](virtual-networks-name-resolution-for-vms-and-role-instances.md) a virtuális gépeket (VM) és a szerepkörpéldányok. Ha a névfeloldás haladhatja meg az Azure alapértelmezett DNS által biztosított képességek, megadhatja a saját DNS-kiszolgálók. A saját DNS-kiszolgálókkal lehetőséget nyújt a saját igényeinek megfelelően a DNS-megoldást alakíthatja. Például szükség lehet keresztül férnek hozzá a helyszíni erőforrásokhoz az Active Directory-tartományvezérlőhöz.

Az egyéni DNS-kiszolgálók működtetnek, Azure virtuális gépeken, ha ugyanazt a virtuális hálózatot az állomásnév lekérdezések továbbíthatja az Azure állomásnevek megoldásához. Ha nem szeretné ezt a beállítást használja, a virtuális gép állomásnevek regisztrálhatja a DNS-kiszolgáló, a dinamikus DNS (DDNS) használatával. Azure nem rendelkezik közvetlenül bejegyzéseket létrehozni a DNS-kiszolgálók, így alternatív megoldások gyakran szükséges a hitelesítő adatokat. Hajtsa végre az olyan gyakori forgatókönyveket tartalmaz, lehetőségeket:

## <a name="windows-clients"></a>Windows-ügyfelek
A nem tartományhoz csatlakoztatott Windows-ügyfelek nem biztonságos DDNS frissítések történt kísérlet, ha indulnak, vagy ha megváltoztatja az IP-címet. A DNS-neve az állomásnév és az elsődleges DNS-utótag. Azure az elsődleges DNS-utótag üresen hagyja, de beállíthatja a virtuális gép utótag keresztül a [felhasználói felület](https://technet.microsoft.com/library/cc794784.aspx) vagy [PowerShell](/powershell/module/dnsclient/set-dnsclient).

A tartományhoz csatlakoztatott Windows-ügyfelek regisztrálja az IP-címét a tartományvezérlő biztonságos DDNS használatával. A tartományhoz való csatlakozást folyamat az ügyfél az elsődleges DNS-utótag be és hoz létre, és a megbízhatósági kapcsolatot tart fenn.

## <a name="linux-clients"></a>Linux-ügyfelek
Linux-ügyfelek általában nem regisztrálják magukat a DNS-kiszolgálóval a következő indításakor, akkor azt feltételezik, hogy a DHCP-kiszolgáló minderre. Azure DHCP-kiszolgálók a rekordokat a DNS-kiszolgáló regisztrálása a hitelesítő adatok nem rendelkeznek. Használhatja a nevű eszköz `nsupdate`, amely a Bind csomagban található, DDNS küldendő frissíti. Mivel a DDNS protokoll szabványosított, használhatja `nsupdate` még ha nem a kötés a DNS-kiszolgálón.

A hurkok hozhatja létre és kezelheti a gazdagépnév-bejegyzés, a DNS-kiszolgáló a DHCP-ügyfél által biztosított is használhatja. A DHCP ciklus során az ügyfél végrehajtja a parancsfájlok */etc/dhcp/dhclient-exit-hooks.d/*. A hurkok használatával regisztráljon az új IP cím `nsupdate`. Példa:

```bash
#!/bin/sh
requireddomain=mydomain.local

# only execute on the primary nic
if [ "$interface" != "eth0" ]
then
    return
fi

# When you have a new IP, perform nsupdate
if [ "$reason" = BOUND ] || [ "$reason" = RENEW ] ||
   [ "$reason" = REBIND ] || [ "$reason" = REBOOT ]
then
   host=`hostname`
   nsupdatecmds=/var/tmp/nsupdatecmds
     echo "update delete $host.$requireddomain a" > $nsupdatecmds
     echo "update add $host.$requireddomain 3600 a $new_ip_address" >> $nsupdatecmds
     echo "send" >> $nsupdatecmds

     nsupdate $nsupdatecmds
fi
```

Használhatja a `nsupdate` biztonságos DDNS végrehajtandó parancs frissíti. Például egy kötés DNS-kiszolgálót használ, egy nyilvános-titkos kulcsból álló kulcspárt esetén [generált](http://linux.yyz.us/nsupdate/). A DNS-kiszolgáló [konfigurált](http://linux.yyz.us/dns/ddns-server.html) a nyilvános kulcs része, és úgy, hogy az informatikai ellenőrizheti a kérelmek aláírása. Arra, hogy-kulcspár `nsupdate`, használja a `-k` beállítást, a DDNS frissítés kérelem kell aláírni.

Windows DNS-kiszolgáló használata, ha a Kerberos-hitelesítést használhatja a `-g` paraméterének `nsupdate`, de nem érhető el a Windows-verzión `nsupdate`. Kerberos használatához `kinit` tölthető be a hitelesítő adatokat. A hitelesítő adatok betöltése for example, egy [keytab fájl](http://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html)), majd `nsupdate -g` szerzi be a hitelesítő adatokat, a gyorsítótárból.

Ha szükséges, hozzáadhat egy DNS-keresési utótagot a virtuális gépek. A DNS-utótag van megadva a */etc/resolv.conf* fájlt. A legtöbb Linux disztribúciókkal automatikusan kezeli a tartalmát a fájl, ezért általában nem szerkeszthető. Azonban a DHCP-ügyfél segítségével felülírhatja az utótag `supersede` parancsot. Bírálja felül az utótagot, vegye fel a következő sort a */etc/dhcp/dhclient.conf* fájlt:

```
supersede domain-name <required-dns-suffix>;
```
