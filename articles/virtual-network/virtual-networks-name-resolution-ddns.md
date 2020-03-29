---
title: Dinamikus DNS használata állomásnevek regisztrálásához az Azure-ban | Microsoft dokumentumok
description: Útmutató a dinamikus DNS beállításához az állomásnevek regisztrálásához a saját DNS-kiszolgálóin.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60640378"
---
# <a name="use-dynamic-dns-to-register-hostnames-in-your-own-dns-server"></a>Dinamikus DNS használata a gazdagépek regisztrálásához a saját DNS-kiszolgálón

[Az Azure névfeloldást biztosít](virtual-networks-name-resolution-for-vms-and-role-instances.md) a virtuális gépek (VM) és a szerepkörpéldányok számára. Ha a névfeloldási igények meghaladják az Azure alapértelmezett DNS-e által biztosított képességeket, saját DNS-kiszolgálókat is megadhat. A saját DNS-kiszolgálók használatával személyre szabhatja DNS-megoldását saját egyedi igényeihez. Előfordulhat például, hogy az Active Directory tartományvezérlőn keresztül kell elérnie a helyszíni erőforrásokat.

Ha az egyéni DNS-kiszolgálók Azure-beli virtuális gépekként vannak üzemeltetve, továbbíthatja az azonos virtuális hálózat állomásnév-lekérdezéseit az Azure-ba az állomásnevek feloldásához. Ha nem szeretné használni ezt a lehetőséget, a virtuális gép állomásneveit regisztrálhatja a DNS-kiszolgálón a dinamikus DNS (DDNS) használatával. Az Azure nem rendelkezik a dns-kiszolgálókon lévő rekordok közvetlen létrehozásához szükséges hitelesítő adatokkal, ezért gyakran szükség van alternatív megoldásokra. Néhány gyakori forgatókönyv, alternatívákkal:

## <a name="windows-clients"></a>Windows-ügyfelek
A nem tartományhoz csatlakozó Windows-ügyfelek nem biztonságos DDNS-frissítéseket kísérelnek meg a rendszerindításkor vagy az IP-címük változásakor. A DNS-név az állomásnév és az elsődleges DNS-utótag. Az Azure üresen hagyja az elsődleges DNS-utótagot, de a felhasználói [felületen](https://technet.microsoft.com/library/cc794784.aspx) vagy a [PowerShellen](/powershell/module/dnsclient/set-dnsclient)keresztül beállíthatja az utótagot a virtuális gépben.

A tartományhoz csatlakozó Windows-ügyfelek biztonságos DDNS használatával regisztrálják IP-címüket a tartományvezérlőn. A tartomány-csatlakozási folyamat beállítja az elsődleges DNS-utótagot az ügyfélen, és létrehozza és fenntartja a bizalmi kapcsolatot.

## <a name="linux-clients"></a>Linux-ügyfelek
A Linux-ügyfelek általában nem regisztrálják magukat a DNS-kiszolgálón indításkor, feltételezik, hogy a DHCP-kiszolgáló teszi. Az Azure DHCP-kiszolgálói nem rendelkeznek a DNS-kiszolgálón lévő rekordok regisztrálásához szükséges hitelesítő adatokkal. A Bind csomagban `nsupdate`található , nevű eszköz segítségével DDNS-frissítéseket küldhet. Mivel a DDNS protokoll szabványosított, `nsupdate` akkor is használható, ha nem használja a Bind-et a DNS-kiszolgálón.

A DHCP-ügyfél által biztosított horgok segítségével létrehozhatja és karbantarthatja a gazdanév bejegyzést a DNS-kiszolgálón. A DHCP-ciklus során az ügyfél az */etc/dhcp/dhclient-exit-hooks.d/* nyelven hajtja végre a parancsfájlokat. A horgok segítségével regisztrálhatja az `nsupdate`új IP-címet a használatával. Példa:

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

A `nsupdate` paranccsal biztonságos DDNS-frissítéseket is végrehajthat. Bind DNS-kiszolgáló használata esetén például egy nyilvános-titkos kulcspár [jön létre.](http://linux.yyz.us/nsupdate/) A DNS-kiszolgáló a kulcs nyilvános részével van [konfigurálva,](http://linux.yyz.us/dns/ddns-server.html) így ellenőrizheti a kérelem aláírását. A kulcspár biztosításához `nsupdate`használja `-k` a ddns-frissítési kérelem aláírási lehetőségét.

Windows DNS-kiszolgáló használata esetén a Kerberos-hitelesítést `-g` a `nsupdate`paraméterrel együtt használhatja a alkalmazásban, `nsupdate`de az nem érhető el a Windows verziójában. A Kerberos használatához `kinit` töltse be a hitelesítő adatokat. Például betöltheti a hitelesítő adatokat egy `nsupdate -g` [keytab fájlból),](https://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html)majd felveszi a hitelesítő adatokat a gyorsítótárból.

Szükség esetén dns-keresési utótagot adhat a virtuális gépekhez. A DNS-utótag az */etc/resolv.conf* fájlban van megadva. A legtöbb Linux disztribúció automatikusan kezeli a fájl tartalmát, így általában nem szerkesztheti. Az utótagot azonban felülírhatja a DHCP-ügyfél `supersede` parancsával. Az utótag felülbírálásához adja hozzá a következő sort az */etc/dhcp/dhclient.conf* fájlhoz:

```
supersede domain-name <required-dns-suffix>;
```
