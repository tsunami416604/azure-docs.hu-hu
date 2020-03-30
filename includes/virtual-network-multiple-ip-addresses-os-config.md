---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-network
author: jimdial
ms.service: virtual-network
ms.topic: include
ms.date: 05/10/2019
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: a9473f69d600a86ff71da69c7efe0dea3f2b0a08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76159424"
---
## <a name="add-ip-addresses-to-a-vm-operating-system"></a><a name="os-config"></a>IP-címek hozzáadása a virtuális gép operációs rendszeréhez

Csatlakozzon és jelentkezzen be egy több privát IP-címmel létrehozott virtuális gépre. A virtuális géphez hozzáadott összes magánhálózati IP-címet (az elsődlegest is beleértve) manuálisan kell hozzáadnia. Hajtsa végre a virtuális gép operációs rendszerének következő lépéseit.

### <a name="windows"></a>Windows

1. A parancssorba írja be az *ipconfig /all* parancsot.  Csak az *elsődleges* magánhálózati IP-cím látható (DHCP-vel).
2. Írja be a parancssorba az *ncpa.cpl* parancsot a **Hálózati kapcsolatok** ablak megnyitásához.
3. Nyissa meg a megfelelő adapter tulajdonságait: **Helyi kapcsolat**.
4. Kattintson duplán A TCP/IP protokoll 4-es verziója (IPv4) elemre.
5. Jelölje be **A következő IP-cím használata:** elemet, és írja be a következő értékeket:

    * **IP-cím**: Adja meg az *elsődleges* magánhálózati IP-címet
    * **Alhálózati maszk**: Állítsa be az alhálózatának megfelelően. Például ha az alhálózat egy /24 alhálózat, akkor az alhálózati maszk 255.255.255.0.
    * **Alapértelmezett átjáró**: Az alhálózat első IP-címe. Ha az alhálózata 10.0.0.0/24, akkor az átjáró IP-címe 10.0.0.1.
    * Válassza **a Használja a következő DNS-kiszolgálócímeket,** és adja meg a következő értékeket:
        * **Elsődleges DNS-kiszolgáló**: Ha nem a saját DNS-kiszolgálóját használja, adja meg a következőt: 168.63.129.16.  Ha a saját DNS-kiszolgálóját használja, adja meg a kiszolgáló IP-címét.
    * Válassza a **Speciális gombot,** és adjon hozzá további IP-címeket. Adja hozzá az azure-i hálózati csatolóhoz egy előző lépésben hozzáadott másodlagos magánhálózati IP-címeket a Windows hálózati adapterhez, amely az Azure hálózati adapterhez rendelt elsődleges IP-címet rendeli hozzá.

        Soha ne rendelje hozzá manuálisan az Azure virtuális géphez rendelt nyilvános IP-címet a virtuális gép operációs rendszerén belül. Ha manuálisan állítja be az IP-címet az operációs rendszeren belül, győződjön meg arról, hogy az megegyezik az Azure [hálózati adapterhez](../articles/virtual-network/virtual-network-network-interface-addresses.md#change-ip-address-settings)rendelt privát IP-címmel, vagy elveszítheti a virtuális géphez való kapcsolódást. További információ a [privát IP-címbeállításokról.](../articles/virtual-network/virtual-network-network-interface-addresses.md#private) Soha ne rendeljen hozzá egy Azure nyilvános IP-címet az operációs rendszeren belül.

    * Kattintson az **OK** gombra a TCP/IP-beállításokból való kilépéshez, majd kattintson ismét az **OK** gombra az adapterbeállítások bezárásához. A rendszer újból létesíti az RDP-kapcsolatot.

6. A parancssorba írja be az *ipconfig /all* parancsot. Megjelenik az összes hozzáadott IP-cím, és a DHCP ki van kapcsolva.
7. Állítsa be a Windows-t úgy, hogy az elsődleges IP-konfiguráció privát IP-címét használja az Azure-ban a Windows elsődleges IP-címeként. A részleteket lásd: [Nincs internet-hozzáférés az Azure Windows virtuális gépről, amely több IP-címmel rendelkezik.](https://support.microsoft.com/help/4040882/no-internet-access-from-azure-windows-vm-that-has-multiple-ip-addresse) 

### <a name="validation-windows"></a>Ellenőrzés (Windows)

Ellenőrizze, hogy tud-e csatlakozni az internethez a másodlagos IP-konfigurációról az ahhoz tartozó nyilvános IP-címen keresztül. Ehhez a fenti lépésekkel történő hozzáadás után használja a következő parancsot:

```bash
ping -S 10.0.0.5 hotmail.com
```
>[!NOTE]
>Másodlagos IP-konfigurációk esetén csak akkor pingelhet az interneten, ha a konfigurációhoz nyilvános IP-cím van társítva. Az elsődleges IP-konfigurációk esetében nem szükséges nyilvános IP-cím az interneten pingeléshez.

### <a name="linux-ubuntu-1416"></a>Linux (Ubuntu 14/16)

Javasoljuk, hogy nézze meg a Linux disztribúció legújabb dokumentációját. 

1. Nyisson meg egy terminálablakot.
2. Győződjön meg arról, hogy Ön a gyökér szintű felhasználó. Ha nem Ön az, írja be a következő parancsot:

   ```bash
   sudo -i
   ```

3. Frissítse a hálózati adapter (feltehetőleg „eth0”) konfigurációs fájlját.

   * A dhcp esetében hagyja meg a meglévő sortételt. Az elsődleges IP-cím konfigurációja nem változik.
   * Adjon hozzá egy konfigurációt egy további statikus IP-cím számára a következő parancsokkal:

     ```bash
     cd /etc/network/interfaces.d/
     ls
     ```

     Egy .cfg fájlnak kell megjelennie.
4. Nyissa meg a fájlt. A következő soroknak kell megjelennie a fájl végén:

   ```bash
   auto eth0
   iface eth0 inet dhcp
   ```

5. Írja be a következő sorokat a fájl meglévő sorai alá:

   ```bash
   iface eth0 inet static
   address <your private IP address here>
   netmask <your subnet mask>
   ```

6. Mentse a fájlt a következő paranccsal:

   ```bash
   :wq
   ```

7. Állítsa alaphelyzetbe a hálózati adaptert a következő paranccsal:

   ```bash
   sudo ifdown eth0 && sudo ifup eth0
   ```

   > [!IMPORTANT]
   > Ha távoli kapcsolatot használ, az ifdown és az ifup parancsokat ugyanabban a sorban futtassa.
   >

8. A következő paranccsal ellenőrizze, hogy az IP-cím hozzá van-e adva a hálózati adapterhez:

   ```bash
   ip addr list eth0
   ```

   A listán meg kell jelennie a hozzáadott IP-címnek.

### <a name="linux-ubuntu-1804"></a>Linux (Ubuntu 18.04+)

Ubuntu 18.04 és a `netplan` fenti megváltozott az operációs rendszer hálózati kezelése. Javasoljuk, hogy nézze meg a Linux disztribúció legújabb dokumentációját. 

1. Nyisson meg egy terminálablakot.
2. Győződjön meg arról, hogy Ön a gyökér szintű felhasználó. Ha nem Ön az, írja be a következő parancsot:

    ```bash
    sudo -i
    ```

3. Hozzon létre egy fájlt a második felülethez, és nyissa meg egy szövegszerkesztőben:

    ```bash
    vi /etc/netplan/60-static.yaml
    ```

4. Adja hozzá a következő sorokat a fájlhoz, és cserélje ki `10.0.0.6/24` az IP/netmask-ot:

    ```bash
    network:
        version: 2
        ethernets:
            eth0:
                addresses:
                    - 10.0.0.6/24
    ```

5. Mentse a fájlt a következő paranccsal:

    ```bash
    :wq
    ```

6. Tesztelje a módosításokat a [netplan segítségével](http://manpages.ubuntu.com/manpages/cosmic/man8/netplan-try.8.html) próbálja meg megerősíteni a szintaxist:

    ```bash
    netplan try
    ```

> [!NOTE]
> `netplan try`ideiglenesen alkalmazza a módosításokat, és 120 másodperc után visszagörgeti a módosításokat. Ha megszakadt a kapcsolat, várjon 120 másodpercet, majd csatlakozzon újra. Ekkor a módosítások vissza lesznek állítva.

7. Feltéve, `netplan try`hogy nincs probléma a, alkalmazza a konfigurációs módosításokat:

    ```bash
    netplan apply
    ```

8. A következő paranccsal ellenőrizze, hogy az IP-cím hozzá van-e adva a hálózati adapterhez:

    ```bash
    ip addr list eth0
    ```

    A listán meg kell jelennie a hozzáadott IP-címnek. Példa:

    ```bash
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
        valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host
        valid_lft forever preferred_lft forever
    2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
        link/ether 00:0d:3a:8c:14:a5 brd ff:ff:ff:ff:ff:ff
        inet 10.0.0.6/24 brd 10.0.0.255 scope global eth0
        valid_lft forever preferred_lft forever
        inet 10.0.0.4/24 brd 10.0.0.255 scope global secondary eth0
        valid_lft forever preferred_lft forever
        inet6 fe80::20d:3aff:fe8c:14a5/64 scope link
        valid_lft forever preferred_lft forever
    ```
    
### <a name="linux-red-hat-centos-and-others"></a>Linux (Red Hat, CentOS és mások)

1. Nyisson meg egy terminálablakot.
2. Győződjön meg arról, hogy Ön a gyökér szintű felhasználó. Ha nem Ön az, írja be a következő parancsot:

    ```bash
    sudo -i
    ```

3. Adja meg a jelszavát, és kövesse a megjelenő utasításokat. Miután Ön lett a gyökér szintű felhasználó, lépjen a hálózati parancsfájlok mappájába a következő paranccsal:

    ```bash
    cd /etc/sysconfig/network-scripts
    ```

4. Listázza a kapcsolódó ifcfg-fájlokat a következő paranccsal:

    ```bash
    ls ifcfg-*
    ```

    A fájlok között meg kell jelennie az *ifcfg-eth0* fájlnak.

5. IP-cím hozzáadásához hozzon létre egy hozzá tartozó konfigurációs fájlt az alábbiakban látható módon. Vegye figyelembe, hogy minden IP-konfigurációhoz létre kell hozni egy fájlt.

    ```bash
    touch ifcfg-eth0:0
    ```

6. Nyissa meg az *ifcfg-eth0:0* fájlt a következő paranccsal:

    ```bash
    vi ifcfg-eth0:0
    ```

7. Adjon tartalmat a fájlhoz (ebben az esetben az *eth0:0* fájlhoz) a következő paranccsal. Ne felejtse el frissíteni az információkat az IP-címe alapján.

    ```bash
    DEVICE=eth0:0
    BOOTPROTO=static
    ONBOOT=yes
    IPADDR=192.168.101.101
    NETMASK=255.255.255.0
    ```

8. Mentse a fájlt a következő paranccsal:

    ```bash
    :wq
    ```

9. Indítsa újra a hálózati szolgáltatásokat, és a következő parancsok futtatásával győződjön meg arról, hogy a módosítások sikeresen végre lettek hajtva:

    ```bash
    /etc/init.d/network restart
    ifconfig
    ```

    A hozzáadott *eth0:0* IP-címnek meg kell jelennie a rendszer által visszaadott listában.

### <a name="validation-linux"></a>Ellenőrzés (Linux)

Ellenőrizze, hogy tud-e csatlakozni az internethez a másodlagos IP-konfigurációról az ahhoz tartozó nyilvános IP-címen keresztül. Ehhez használja a következő parancsot:

```bash
ping -I 10.0.0.5 hotmail.com
```
>[!NOTE]
>Másodlagos IP-konfigurációk esetén csak akkor pingelhet az interneten, ha a konfigurációhoz nyilvános IP-cím van társítva. Az elsődleges IP-konfigurációk esetében nem szükséges nyilvános IP-cím az interneten pingeléshez.

Linux rendszerű virtuális gépek esetében a másodlagos hálózati adapterről kimenő kapcsolatok ellenőrzésekor szükség lehet a megfelelő útvonalak hozzáadására. Ezt többféleképpen teheti meg. Tekintse át a Linux-disztribúciójára vonatkozó megfelelő dokumentációt. Az egyik lehetséges módszer a következő:

```bash
echo 150 custom >> /etc/iproute2/rt_tables 

ip rule add from 10.0.0.5 lookup custom
ip route add default via 10.0.0.1 dev eth2 table custom

```
- Cserélje le:
    - a **10.0.0.5**-öt arra a magánhálózati IP-címre, amelyhez nyilvános IP-cím is tartozik
    - a **10.0.0.1**-et az alapértelmezett átjáróra
    - az **eth2**-t a másodlagos hálózati adapter nevére
