---
title: "Dinamikus DNS segítségével hostnames regisztrálása"
description: "Ezen a lapon részleteket nyújt a saját DNS-kiszolgálók regisztrálása állomásnevek dinamikus DNS beállítása."
services: dns
documentationcenter: na
author: GarethBradshawMSFT
manager: timlt
editor: 
ms.assetid: c315961a-fa33-45cf-82b9-4551e70d32dd
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2017
ms.author: garbrad
ms.openlocfilehash: 440a062e5fff73526b2d77d7d0a7c52ca72a66f1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="using-dynamic-dns-to-register-hostnames-in-your-own-dns-server"></a>Dinamikus DNS segítségével hostnames regisztrálni a saját DNS-kiszolgáló
[Azure biztosít névfeloldás](virtual-networks-name-resolution-for-vms-and-role-instances.md) a virtuális gépek (VM) és a szerepkörpéldányok. Amikor a névfeloldás kell túlmutató Azure által biztosított naplókon, megadhatja a saját DNS-kiszolgálók. Ez lehetővé teszi a kitevőt, a DNS-megoldást a saját igényeinek megfelelően testre szabni. Például szükség lehet keresztül férnek hozzá a helyszíni erőforrásokhoz az Active Directory-tartományvezérlőhöz.

Az egyéni DNS-kiszolgálók működtetnek Azure virtuális gépeken, ha ugyanazt a virtuális hálózatot az állomásnév lekérdezések továbbíthatja az Azure állomásnevek megoldásához. Ha nem szeretne használni, ez az útvonal, a virtuális gép állomásnevek regisztrálhatja a dinamikus DNS használatával DNS-kiszolgáló.  Azure közvetlenül bejegyzéseket létrehozni a DNS-kiszolgálók alternatív megoldások gyakran van szükség, nem rendelkezik (pl. a hitelesítő adatok) lehetőséget. Az alábbiakban néhány gyakori helyzetek lehetőségeket.

## <a name="windows-clients"></a>Windows-ügyfelek
A nem tartományhoz csatlakoztatott Windows-ügyfelek nem biztonságos dinamikus DNS (DDNS) frissítések történt kísérlet, ha indulnak, vagy ha megváltoztatja az IP-címet. A DNS-neve az állomásnév és az elsődleges DNS-utótag. Azure az elsődleges DNS-utótag üresen hagyja, de ez a beállíthatja a virtuális gép keresztül a [felhasználói felület](https://technet.microsoft.com/library/cc794784.aspx) vagy [automatizálás segítségével](https://social.technet.microsoft.com/forums/windowsserver/3720415a-6a9a-4bca-aa2a-6df58a1a47d7/change-primary-dns-suffix).

A tartományhoz csatlakoztatott Windows-ügyfelek regisztrálja az IP-címét a tartományvezérlő biztonságos dinamikus DNS használatával. A tartományhoz való csatlakozást folyamat az ügyfél az elsődleges DNS-utótag be és hoz létre, és a megbízhatósági kapcsolatot tart fenn.

## <a name="linux-clients"></a>Linux-ügyfelek
Linux-ügyfelek általában nem regisztrálják magukat a DNS-kiszolgálóval a következő indításakor, akkor azt feltételezik, hogy a DHCP-kiszolgáló minderre. Azure DHCP-kiszolgálók nem rendelkeznek a rekordokat a DNS-kiszolgáló regisztrálása képességét vagy hitelesítő adatokat.  Használhatja a nevű eszköz *nsupdate*, amely a Bind csomagban található, dinamikus DNS küldendő frissíti. Mivel a dinamikus DNS protokoll szabványosított, használhatja *nsupdate* még ha nem a kötés a DNS-kiszolgálón.

A hurkok hozhatja létre és kezelheti a gazdagépnév-bejegyzés, a DNS-kiszolgáló a DHCP-ügyfél által biztosított is használhatja. A DHCP ciklus során az ügyfél végrehajtja a parancsfájlok */etc/dhcp/dhclient-exit-hooks.d/*. Ennek segítségével regisztrálja az új IP-cím használatával *nsupdate*. Példa:

        #!/bin/sh
        requireddomain=mydomain.local

        # only execute on the primary nic
        if [ "$interface" != "eth0" ]
        then
            return
        fi

        # when we have a new IP, perform nsupdate
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

        
        

Használhatja a *nsupdate* biztonságos dinamikus DNS-frissítések végrehajtásához parancsot. Például egy kötés DNS-kiszolgálót használ, egy nyilvános-titkos kulcsból álló kulcspárt esetén [generált](http://linux.yyz.us/nsupdate/).  A DNS-kiszolgáló [konfigurált](http://linux.yyz.us/dns/ddns-server.html) úgy, hogy ellenőrizni tudja a kérelem aláírása a kulcs nyilvános része. Kell használnia a *-k* lehetőséget, ha a kulcs-két *nsupdate* ahhoz, hogy a dinamikus DNS frissítése kell aláírni.

A Kerberos-hitelesítést használhatja a Windows DNS-kiszolgálót használ, a *-g* paraméterének *nsupdate* (nem érhető el a Windows-verzión *nsupdate*). Ehhez használja *kinit* tölthető be a hitelesítő adatokat (pl. egy [keytab fájl](http://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html)). Majd *nsupdate -g* felveszi a hitelesítő adatokat a gyorsítótárból.

Ha szükséges, hozzáadhat egy DNS-keresési utótagot a virtuális gépek. A DNS-utótag van megadva a */etc/resolv.conf* fájlt. A legtöbb Linux disztribúciókkal automatikusan kezeli a tartalmát a fájl, ezért általában nem szerkeszthető. Azonban a DHCP-ügyfél segítségével felülírhatja az utótag *felülír* parancsot. Ehhez a */etc/dhcp/dhclient.conf*, adja hozzá:

        supersede domain-name <required-dns-suffix>;

