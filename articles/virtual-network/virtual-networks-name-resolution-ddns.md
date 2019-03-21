---
title: Dinamikus DNS használata a gazdagépek regisztrálásához az Azure-ban |} A Microsoft Docs
description: Ismerje meg, hogyan állíthatja be a dinamikus DNS-gazdagépek regisztrálásához a saját DNS-kiszolgálókat.
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
ms.openlocfilehash: c2ef842fd62ef060f06536d66387c3facd0627b5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57994689"
---
# <a name="use-dynamic-dns-to-register-hostnames-in-your-own-dns-server"></a>Dinamikus DNS használata a gazdagépek regisztrálásához a saját DNS-kiszolgáló

[Az Azure biztosít a névfeloldás](virtual-networks-name-resolution-for-vms-and-role-instances.md) (VM) virtuális gépek és szerepkörpéldányok. Ha a névfeloldás haladhatja meg az Azure alapértelmezett DNS által biztosított képességek, megadhatja a saját DNS-kiszolgálók. A saját DNS-kiszolgálók használatával teszi lehetővé, a DNS-megoldása, a saját igényeinek megfelelően. Például szükség lehet hozzáférni a helyszíni erőforrások elérése az Active Directory-tartományvezérlőhöz.

Ha az egyéni DNS-kiszolgálókat az Azure virtuális gépként futnak, az Azure-bA a gazdanév feloldása is továbbíthatja, állomásnév lekérdezések ugyanahhoz a virtuális hálózathoz. Ha nem szeretné ezt a beállítást használja, a virtuális gép állomásnevek regisztrálhatja a DNS-kiszolgáló, a dinamikus DNS (DDNS) használatával. Az Azure nem rendelkezik közvetlenül hozzon létre rekordokat a DNS-kiszolgálók, így alternatív megoldások gyakran van szükség a hitelesítő adatokat. Néhány általános esetben, alternatívákkal hajtsa végre:

## <a name="windows-clients"></a>Windows ügyfelek
A nem tartományhoz csatlakoztatott Windows-ügyfelek nem biztonságos DDNS frissítések próbálja meg, ha azok rendszerindító, vagy ha az IP-cím megváltozik. A DNS-név az állomásnév és az elsődleges DNS-utótag. Az Azure az elsődleges DNS-utótag üresen hagyja, de beállíthatja a virtuális gépen, az utótag-n keresztül a [felhasználói felület](https://technet.microsoft.com/library/cc794784.aspx) vagy [PowerShell](/powershell/module/dnsclient/set-dnsclient).

Tartományhoz csatlakoztatott Windows-ügyfelek IP-címeket a tartományvezérlő biztonságos DDNS használatával regisztrálja. A tartományhoz való csatlakozás folyamat állítja be az elsődleges DNS-utótag az ügyfélen, és hoz létre és tart fenn a megbízhatósági kapcsolat.

## <a name="linux-clients"></a>Linux-ügyfelek
Linux-ügyfelek általában nem regisztrálják magukat a DNS-kiszolgáló indításakor, elvégzi a DHCP-kiszolgáló azt feltételezik. Az Azure DHCP-kiszolgálók nem rendelkeznek a rekordokat a DNS-kiszolgáló regisztrálása a hitelesítő adatokat. Egy nevű eszközzel `nsupdate`, amely tartalmazza a kötés csomagban, DDNS-t küldeni a frissítések. Mivel a DDNS protokoll szabványosított, használhatja `nsupdate` még ha nem használ kötési a DNS-kiszolgálón.

A hurkok a hostname-bejegyzést a DNS-kiszolgáló létrehozásához és kezeléséhez a DHCP-ügyfél által biztosított is használhatja. A DHCP ciklus során az ügyfél a parancsfájlok végrehajtása */etc/dhcp/dhclient-exit-hooks.d/*. A hurkok használatával regisztrálja az új IP cím használatával `nsupdate`. Példa:

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

Is használhatja a `nsupdate` biztonságos DDNS végrehajtásához parancs frissíti. Például amikor egy kötés DNS-kiszolgálót használ, egy nyilvános-titkos kulcspárt, [generált](http://linux.yyz.us/nsupdate/). A DNS-kiszolgáló [konfigurált](http://linux.yyz.us/dns/ddns-server.html) a nyilvános kulcs része, és úgy, hogy az informatikai ellenőrizheti a kérelem aláírása. Adja meg a-kulcspárt a `nsupdate`, használja a `-k` beállítás, a DDNS frissítés csak az aláírt kérelmek.

Windows DNS-kiszolgálót használ, ha a Kerberos-hitelesítést is használhatja a `-g` paraméter `nsupdate`, azonban nem érhető el a Windows verziójában `nsupdate`. A Kerberos használatához `kinit` betölteni a hitelesítő adatokat. A hitelesítő adatok betöltése for example, egy [kulcstábla használatát fájl](https://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html)), majd `nsupdate -g` szerzi be a hitelesítő adatokat a gyorsítótárból.

Ha szükséges, a virtuális gépek is hozzáadhat egy DNS-keresési utótagot. A DNS-utótag van megadva a */etc/resolv.conf* fájlt. A legtöbb Linux-disztribúciók automatikusan kezelheti a tartalmakat a fájl, ezért általában nem szerkeszthető. Azonban a DHCP-ügyfél használatával felülírhatja az utótag `supersede` parancsot. Bírálja felül az utótagot, adja hozzá a következő sort a */etc/dhcp/dhclient.conf* fájlt:

```
supersede domain-name <required-dns-suffix>;
```
