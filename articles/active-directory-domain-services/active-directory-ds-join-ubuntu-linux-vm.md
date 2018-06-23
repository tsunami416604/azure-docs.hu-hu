---
title: 'Az Azure Active Directory tartományi szolgáltatások: Az Ubuntu virtuális gép csatlakoztatása felügyelt tartományhoz |} Microsoft Docs'
description: Az Ubuntu Linux virtuális gépek csatlakoztatása az Azure AD tartományi szolgáltatások
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 804438c4-51a1-497d-8ccc-5be775980203
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: maheshu
ms.openlocfilehash: d9f4dc0883ced599dd13d0c5d52ff865e03b73ed
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "36332911"
---
# <a name="join-an-ubuntu-virtual-machine-in-azure-to-a-managed-domain"></a>Az Azure-ban egy Ubuntu virtuális gép csatlakoztatása felügyelt tartományhoz
Ez a cikk bemutatja, hogyan egy Ubuntu Linux virtuális gépek csatlakoztatása az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Előkészületek
A cikkben szereplő feladatok elvégzéséhez szüksége:  
1. Egy érvényes **Azure-előfizetés**.
2. Egy **Azure AD-címtár** -vagy egy helyszíni címtár vagy egy csak felhőalapú directory szinkronizálva.
3. **Azure AD tartományi szolgáltatások** az Azure AD-címtár engedélyezni kell. Ha még nem tette meg, az összes ismertetett feladatok végrehajtásával a [első lépések útmutató](active-directory-ds-getting-started.md).
4. Győződjön meg arról, hogy a virtuális hálózat DNS-kiszolgálóként konfigurálta a felügyelt tartományra IP-címét. További információkért lásd: [az Azure virtuális hálózat DNS-beállításainak frissítése](active-directory-ds-getting-started-dns.md)
5. Végezze el a szükséges lépéseket [szinkronizálja a jelszavakat az Azure AD tartományi szolgáltatások által kezelt tartomány](active-directory-ds-getting-started-password-sync.md).


## <a name="provision-an-ubuntu-linux-virtual-machine"></a>Az Ubuntu Linux virtuális gép kiépítése
Egy Ubuntu Linux virtuális gépet az Azure, a következő módszerekkel történő telepítéséhez:
* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

> [!IMPORTANT]
> * A virtuális gép telepítése a **ugyanazt a virtuális hálózatot, amelyben engedélyezte az Azure AD tartományi szolgáltatások**.
> * Válasszon egy **másik alhálózat** fut, amelyen engedélyezte az Azure AD tartományi szolgáltatásokat.
>


## <a name="connect-remotely-to-the-ubuntu-linux-virtual-machine"></a>Távoli csatlakozás az Ubuntu Linux virtuális gép
Az Ubuntu virtuális gép az Azure-ban van kiépítve. A következő feladata távolról csatlakozni a virtuális gépet a virtuális gép kiépítése során létrehozott helyi rendszergazdai fiók használatával.

A cikk utasításait [Linuxot futtató virtuális gép bejelentkezés](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>A Linux virtuális gép a gazdafájl konfigurálása
Az SSH terminálban az/etc/hosts fájl szerkesztése, és frissítse a gép IP-cím és állomásnevet.

```
sudo vi /etc/hosts
```

A hosts fájlt adja meg a következő értéket:

```
127.0.0.1 contoso-ubuntu.contoso100.com contoso-ubuntu
```
"Contoso100.com" Íme a felügyelt tartományok DNS-tartomány nevét. "contoso-ubuntu" az állomásnevet, amelyhez csatlakozik, a felügyelt tartományra Ubuntu virtuális gép.


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>A Linux virtuális gépre telepíti a szükséges csomagokat
Következő lépésként telepítse a virtuális gépen a tartományhoz való csatlakozást a szükséges csomagokat. Hajtsa végre a következő lépéseket:

1.  A SSH terminál írja be a következő parancsot a tárolóhelyekkel töltheti le a csomag listája. Ez a parancs a csomag listákat információ a csomagok és a függőségek a legújabb verzióra frissíti.

    ```
    sudo apt-get update
    ```

2. Írja be a szükséges csomagok telepítése a következő parancsot.
    ```
      sudo apt-get install krb5-user samba sssd sssd-tools libnss-sss libpam-sss ntp ntpdate realmd adcli
    ```

3. A Kerberos telepítése során egy rózsaszín képernyőt lát. A "krb5-user" csomag telepítése a tartománynevet (az összes nagybetűs) megadását kéri. A telepítés ír a [tartomány] és [domain_realm] részeiben /etc/krb5.conf.

    > [!TIP]
    > Ha a felügyelt tartomány nevét contoso100.com, adja meg a CONTOSO100.COM, a tartomány. Ne feledje, hogy a tartomány nevét kötelező megadni a nagybetűs.
    >
    >


## <a name="configure-the-ntp-network-time-protocol-settings-on-the-linux-virtual-machine"></a>Adja meg a NTP (protokoll) beállításait a Linux virtuális gép
A dátum és idő a Ubuntu virtuális gép szinkronizálni a felügyelt tartományra. Adja hozzá a felügyelt tartományok NTP állomásnév a /etc/ntp.conf fájlban.

```
sudo vi /etc/ntp.conf
```

A ntp.conf fájlban a következő értéket adja meg, és mentse a fájlt:

```
server contoso100.com
```
"Contoso100.com" Íme a felügyelt tartományok DNS-tartomány nevét.

Végezhetnek szinkronizálást a Ubuntu virtuális gép dátum és idő NTP-kiszolgálóval, és indítsa el az NTP-szolgáltatás:

```
sudo systemctl stop ntp
sudo ntpdate contoso100.com
sudo systemctl start ntp
```


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>A Linux virtuális gép csatlakoztatása felügyelt tartományhoz
Most, hogy a szükséges csomagokat a Linux virtuális gépek vannak telepítve, a következő feladat a virtuális gép csatlakoztatása felügyelt tartományhoz.

1. Az AAD tartományi szolgáltatásokra által kezelt tartomány felderítése. Az SSH terminálban írja be a következő parancsot:

    ```
    sudo realm discover CONTOSO100.COM
    ```

   > [!NOTE]
   > **Hibaelhárítás:** Ha *a kezdőtartomány felderítése* nem találja a felügyelt tartományok:
     * Győződjön meg arról, hogy a tartomány elérhető-e a virtuális gépről (próbálja ping).
     * Ellenőrizze, hogy a virtuális gép valóban már alkalmazva van az azonos virtuális hálózatban, amelyben a felügyelt tartományra érhető el.
     * Ellenőrizze, hogy ha a DNS-kiszolgáló beállításait, a virtuális hálózat úgy, hogy a tartományvezérlők, a felügyelt tartományra mutasson frissítése befejeződött.
   >

2. Kerberos inicializálni. Az SSH terminálban írja be a következő parancsot:

    > [!TIP]
    > * Adjon meg egy felhasználót, aki a "AAD DC rendszergazdák" csoportba tartozik.
    > * Adja meg a tartomány nevét nagybetűvel, más kinit sikertelen lesz.
    >

    ```
    kinit bob@CONTOSO100.COM
    ```

3. A számítógép csatlakoztatása a tartományhoz. Az SSH terminálban írja be a következő parancsot:

    > [!TIP]
    > Használja az előző lépést ("kinit") megadott ugyanazzal a fiókkal.
    >

    ```
    sudo realm join --verbose CONTOSO100.COM -U 'bob@CONTOSO100.COM' --install=/
    ```

Kell ("sikeresen regisztrált számítógép tartomány") üzenet jelenik meg, ha a gép sikeresen csatlakozott a felügyelt tartományra.


## <a name="update-the-sssd-configuration-and-restart-the-service"></a>Frissítse a SSSD konfigurációját, és indítsa újra a szolgáltatást
1. Az SSH terminált írja be a következő parancsot. Nyissa meg a sssd.conf fájlt, és végezze el a módosítást
    ```
    sudo vi /etc/sssd/sssd.conf
    ```

2. A sor megjegyzésbe **use_fully_qualified_names = True** , és mentse a fájlt.
    ```
    # use_fully_qualified_names = True
    ```

3. Indítsa újra a SSSD szolgáltatást.
    ```
    sudo service sssd restart
    ```


## <a name="configure-automatic-home-directory-creation"></a>Konfigurálja az automatikus kezdőkönyvtár létrehozása
Ahhoz, hogy a felhasználók bejelentkezés után a kezdőkönyvtár automatikus létrehozását, írja be a következő parancsokat a PuTTY terminál:
```
sudo vi /etc/pam.d/common-session
```

Adja meg a következő sort a fájl "munkamenet választható pam_sss.so" sora alatt, és mentse azt:
```
session required pam_mkhomedir.so skel=/etc/skel/ umask=0077
```


## <a name="verify-domain-join"></a>Ellenőrizze a tartományhoz való csatlakozást
Győződjön meg arról, hogy a gép sikeresen csatlakoztatva lett a felügyelt tartományra. Csatlakozás a tartományhoz csatlakoztatott Ubuntu virtuális gép egy másik SSH-kapcsolat használatával. Tartományi felhasználói fiókot használja, és ellenőrizze, hogy megoldódott a felhasználói fiók megfelelően.

1. A Terminálszolgáltatások az SSH-típus a következő parancsot a tartományhoz való csatlakozáshoz csatlakoztatott Ubuntu virtuális gép SSH használatával. A felügyelt tartományhoz tartozó tartományi fiókot használni (például "bob@CONTOSO100.COM" Ebben az esetben.)
    ```
    ssh -l bob@CONTOSO100.COM contoso-ubuntu.contoso100.com
    ```

2. Az SSH terminált írja be a következő parancsot, hogy ha a kezdőkönyvtár megfelelően inicializálva.
    ```
    pwd
    ```

3. Az SSH terminálban az alábbi paranccsal tekintheti meg, ha a csoporttagság megfelelően lehet megoldani.
    ```
    id
    ```


## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>A "AAD DC rendszergazdák" csoportba sudo jogosultságokat
A "AAD DC rendszergazdák" csoportba rendszergazdai jogosultságokkal azon a Ubuntu virtuális gép tagjai megadásához. A sudo fájl nem található: /etc/sudoers. A hozzáadott sudoers AD-csoportok tagjai hajthatják végre a sudo.

1. A Terminálszolgáltatások SSH győződjön meg arról jelentkezett be felügyelő jogosultságokkal. A helyi rendszergazdai fiókhoz, a virtuális gép létrehozása a megadott is használhatja. Hajtsa végre a következő parancsot:
    ```
    sudo vi /etc/sudoers
    ```

2. Adja hozzá a következő bejegyzést a /etc/sudoers fájlhoz, és mentse azt:
    ```
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators ALL=(ALL) NOPASSWD:ALL
    ```

3. Most már bejelentkezhet a "AAD DC rendszergazdák" csoport tagjaként, és a virtuális gép rendszergazdai jogosultságokkal kell rendelkeznie.


## <a name="troubleshooting-domain-join"></a>Hibaelhárítás a tartományhoz való csatlakozást
Tekintse meg a [hibaelhárítás tartományhoz való csatlakozást](active-directory-ds-admin-guide-join-windows-vm-portal.md#troubleshoot-joining-a-domain) cikk.


## <a name="related-content"></a>Kapcsolódó tartalom
* [Azure AD tartományi szolgáltatások – első lépések útmutató](active-directory-ds-getting-started.md)
* [Windows Server virtuális gép csatlakoztatása az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz](active-directory-ds-admin-guide-join-windows-vm.md)
* [Bejelentkezés a Linux rendszerű virtuális gép](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
