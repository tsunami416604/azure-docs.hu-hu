## <a name="os-config"></a>IP-címek hozzáadása a virtuális gép operációs rendszeréhez

Csatlakozzon, és jelentkezzen be egy több magánhálózati IP-címmel létrehozott virtuális gépbe. A virtuális géphez hozzáadott összes magánhálózati IP-címet (az elsődlegest is beleértve) manuálisan kell hozzáadnia. Végezze el az alábbi lépések közül azokat, amelyek a virtuális gép operációs rendszerére vonatkoznak:

### <a name="windows"></a>Windows

1. A parancssorba írja be az *ipconfig /all* parancsot.  Csak az *elsődleges* magánhálózati IP-cím látható (DHCP-vel).
2. Írja be a parancssorba az *ncpa.cpl* parancsot a **Hálózati kapcsolatok** ablak megnyitásához.
3. Nyissa meg a megfelelő adapter tulajdonságait: **Helyi kapcsolat**.
4. Kattintson duplán A TCP/IP protokoll 4-es verziója (IPv4) elemre.
5. Jelölje be **A következő IP-cím használata:** elemet, és írja be a következő értékeket:

    * **IP-cím**: Adja meg az *elsődleges* magánhálózati IP-címet
    * **Alhálózati maszk**: Állítsa be az alhálózatának megfelelően. Például ha az alhálózat egy /24 alhálózat, akkor az alhálózati maszk 255.255.255.0.
    * **Alapértelmezett átjáró**: Az alhálózat első IP-címe. Ha az alhálózata 10.0.0.0/24, akkor az átjáró IP-címe 10.0.0.1.
    * Kattintson **A következő DNS-kiszolgálócímek használata:** elemre, és írja be a következő értékeket:
        * **Elsődleges DNS-kiszolgáló**: Ha nem a saját DNS-kiszolgálóját használja, adja meg a következőt: 168.63.129.16.  Ha a saját DNS-kiszolgálóját használja, adja meg a kiszolgáló IP-címét.
    * Kattintson a **Speciális** gombra, és vegyen fel további IP-címeket. Adja hozzá a 8. lépésben felsorolt másodlagos magánhálózati IP-címeket a hálózati adapterhez ugyanazon alhálózat megadásával az elsődleges IP-címhez.
        >[!WARNING] 
        >Ha nem megfelelően követi a fenti lépéseket, elveszítheti a kapcsolatot a virtuális géppel. A továbblépés előtt ellenőrizze, hogy az 5. lépésben megadott információk pontosak-e.

    * Kattintson az **OK** gombra a TCP/IP-beállításokból való kilépéshez, majd kattintson ismét az **OK** gombra az adapterbeállítások bezárásához. A rendszer újból létesíti az RDP-kapcsolatot.

6. A parancssorba írja be az *ipconfig /all* parancsot. Megjelenik az összes hozzáadott IP-cím, és a DHCP ki van kapcsolva.


### <a name="validation-windows"></a>Ellenőrzés (Windows)

Ellenőrizze, hogy tud-e csatlakozni az internethez a másodlagos IP-konfigurációról az ahhoz tartozó nyilvános IP-címen keresztül. Ehhez a fenti lépésekkel történő hozzáadás után használja a következő parancsot:

```bash
ping -S 10.0.0.5 hotmail.com
```
>[!NOTE]
>Másodlagos IP-konfigurációk esetén csak a ping paranccsal az interneten egy nyilvános IP-cím hozzárendelve a konfiguráció-e. Az elsődleges IP-konfiguráció egy nyilvános IP-cím nem szükséges az interneten a ping paranccsal.

### <a name="linux-ubuntu"></a>Linux (Ubuntu)

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

### <a name="linux-redhat-centos-and-others"></a>Linux (Redhat, CentOS és egyebek)

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
>Másodlagos IP-konfigurációk esetén csak a ping paranccsal az interneten egy nyilvános IP-cím hozzárendelve a konfiguráció-e. Az elsődleges IP-konfiguráció egy nyilvános IP-cím nem szükséges az interneten a ping paranccsal.

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
