---
title: 'Az Azure Active Directory tartományi szolgáltatások: Egy Ubuntu virtuális gép csatlakoztatása felügyelt tartományokhoz |} A Microsoft Docs'
description: Egy Ubuntu Linux virtuális gépek csatlakoztatása az Azure AD tartományi szolgáltatások
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 804438c4-51a1-497d-8ccc-5be775980203
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: ergreenl
ms.openlocfilehash: 8699585a7f8e5cdfc81a40b94fbe10fa677a0030
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58120992"
---
# <a name="join-an-ubuntu-virtual-machine-in-azure-to-a-managed-domain"></a>Ubuntus virtuális gép csatlakoztatása felügyelt tartományhoz az Azure-ban
Ez a cikk bemutatja, hogyan lehet egy Ubuntu Linux virtuális gépek csatlakoztatása az Azure AD tartományi szolgáltatásokkal felügyelt tartományban.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Előkészületek
A cikkben szereplő feladatok elvégzéséhez szüksége:  
1. Egy érvényes **Azure-előfizetés**.
2. Egy **Azure AD-címtár** -vagy az egy helyszíni címtár vagy egy csak felhőalapú címtárral szinkronizálja.
3. **Az Azure AD Domain Services** engedélyezve kell lennie az Azure AD-címtárban. Ha még nem tette, minden ismertetett feladatok végrehajtásával a [a kezdeti lépések útmutatóban](active-directory-ds-getting-started.md).
4. Győződjön meg arról, hogy már konfigurálta az IP-címek a felügyelt tartomány a virtuális hálózat DNS-kiszolgálóként. További információkért lásd: [az Azure virtuális hálózat DNS-beállításainak frissítése](active-directory-ds-getting-started-dns.md)
5. Végezze el a szükséges lépéseket [szinkronizálja a jelszavakat az Azure AD tartományi szolgáltatásokkal felügyelt tartományban](active-directory-ds-getting-started-password-sync.md).


## <a name="provision-an-ubuntu-linux-virtual-machine"></a>Egy Ubuntu Linux virtuális gép üzembe helyezése
Egy Ubuntu Linux virtuális gép az Azure-ban, az alábbi módszerek bármelyikével üzembe helyezése:
* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

> [!IMPORTANT]
> * Telepítse a virtuális gépet, azokat a **azonos virtuális hálózatban, amelyiken engedélyezte az Azure AD Domain Services**.
> * Válasszon ki egy **másik alhálózatot** fut, amelyiken engedélyezte az Azure AD tartományi szolgáltatásokat.
>


## <a name="connect-remotely-to-the-ubuntu-linux-virtual-machine"></a>Távoli csatlakozás az Ubuntu Linux virtuális gép
Az Ubuntu virtuális gép az Azure-ban van kiépítve. A következő feladata távolról csatlakozni a virtuális gépet a virtuális gép kiépítése során létrehozott helyi rendszergazdai fiókkal.

Kövesse a cikk a [bejelentkezés egy Linux rendszerű virtuális gép](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>A Linux rendszerű virtuális gép hosts fájljának konfigurálása
A terminálban SSH a Hosts fájl szerkesztése, és frissítse a gép IP-cím és az állomásnevet.

```
sudo vi /etc/hosts
```

Adja meg a hosts fájl a következő értéket:

```
127.0.0.1 contoso-ubuntu.contoso100.com contoso-ubuntu
```
"Contoso100.com" Íme a felügyelt tartomány DNS-tartomány nevét. "contoso-ubuntu", amelyhez csatlakozik, a felügyelt tartományhoz Ubuntu virtuális gép állomásnevét.


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Telepítse a szükséges csomagokat a Linux rendszerű virtuális gépen
Ezután telepítse a virtuális gép tartományhoz való csatlakozás a szükséges csomagokat. Hajtsa végre az alábbi lépéseket:

1.  Az SSH terminálon írja be a következő parancsot a tárházban töltheti le a csomag listák. Ez a parancs a csomag listákat, az információ a csomagok és azok függőségeit a legújabb verzióra frissíti.

    ```
    sudo apt-get update
    ```

2. Írja be a következő parancsot a szükséges csomagok telepítéséhez.
    ```
      sudo apt-get install krb5-user samba sssd sssd-tools libnss-sss libpam-sss ntp ntpdate realmd adcli
    ```

3. A Kerberos-telepítés során megjelenik egy rózsaszín képernyő. A "krb5 – felhasználó" csomag telepítése kérni fogja a tartomány nevét (az összes NAGYBETŰT). A telepítés ír a [tartomány] és [domain_realm] /etc/krb5.conf szakaszait.

    > [!TIP]
    > Ha a felügyelt tartomány neve contoso100.com, adja meg a CONTOSO100.COM, a tartományt. Ne feledje, hogy a tartománynevet kötelező nagybetűs.
    >
    >


## <a name="configure-the-ntp-network-time-protocol-settings-on-the-linux-virtual-machine"></a>A Linux rendszerű virtuális gépen az NTP (Network Time Protocol) beállításainak konfigurálása
A dátum és idő, az Ubuntu virtuális gép kell szinkronizálni a felügyelt tartományhoz. A felügyelt tartomány NTP gazdagépnév hozzáadása a /etc/ntp.conf fájlban.

```
sudo vi /etc/ntp.conf
```

Ntp.conf a fájlban a következő értéket adja meg, és mentse a fájlt:

```
server contoso100.com
```
"Contoso100.com" Íme a felügyelt tartomány DNS-tartomány nevét.

Szinkronizálja az Ubuntu rendszerű virtuális gép dátum és idő NTP-kiszolgálóval, és indítsa el az NTP-szolgáltatás:

```
sudo systemctl stop ntp
sudo ntpdate contoso100.com
sudo systemctl start ntp
```


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>A Linux rendszerű virtuális gép csatlakoztatása a felügyelt tartományhoz
Most, hogy a szükséges csomagok telepítve vannak a Linux rendszerű virtuális gép, a következő feladata a virtuális gép csatlakoztatása a felügyelt tartományhoz.

1. Fedezze fel az AAD tartományi szolgáltatásokkal felügyelt tartományban. Az SSH terminálon írja be a következő parancsot:

    ```
    sudo realm discover CONTOSO100.COM
    ```

   > [!NOTE]
   > **Hibaelhárítás:** Ha *a kezdőtartomány felderítése* nem találja a felügyelt tartomány:
   >   * Győződjön meg arról, hogy a tartomány érhető el a virtuális gépről (ping. Próbálja meg).
   >   * Ellenőrizze, hogy a virtuális gép valóban lett telepítve, az azonos virtuális hálózatban, amely a felügyelt tartomány érhető el.
   >   * Ellenőrizze, hogy ha frissítette a DNS-kiszolgáló beállításainak a virtuális hálózathoz, hogy a felügyelt tartomány tartományvezérlőit mutasson.

2. A Kerberos inicializálása. Az SSH terminálon írja be a következő parancsot:

    > [!TIP]
    > * Győződjön meg arról, hogy megadja a felhasználó, aki az "AAD DC rendszergazdák" csoportba tartozik.
    > * Adja meg a tartomány neve nagybetűvel, más kinit sikertelen lesz.
    >

    ```
    kinit bob@CONTOSO100.COM
    ```

3. A számítógép csatlakoztatása a tartományhoz. Az SSH terminálon írja be a következő parancsot:

    > [!TIP]
    > Az azonos az előző lépésben (kinit) megadott felhasználói fiók használata.
    >

    ```
    sudo realm join --verbose CONTOSO100.COM -U 'bob@CONTOSO100.COM' --install=/
    ```

Meg kell hibaüzenet jelenik meg ("sikeresen regisztrált számítógép tartomány") Ha a gép sikeresen csatlakozott a felügyelt tartományhoz.


## <a name="update-the-sssd-configuration-and-restart-the-service"></a>Frissítse a SSSD konfigurációját, és indítsa újra a szolgáltatást
1. Az SSH terminálon írja be a következő parancsot. Nyissa meg a sssd.conf fájlt, és módosítsa a következőképpen
    ```
    sudo vi /etc/sssd/sssd.conf
    ```

2. Tegye megjegyzésbe a sor **use_fully_qualified_names = True** és mentse a fájlt.
    ```
    # use_fully_qualified_names = True
    ```

3. Indítsa újra a SSSD szolgáltatást.
    ```
    sudo service sssd restart
    ```


## <a name="configure-automatic-home-directory-creation"></a>Kezdőkönyvtár automatikus létrehozásának konfigurálása
Ahhoz, hogy a felhasználók a bejelentkezés után a kezdőkönyvtár automatikus létrehozását, a PuTTY terminálon írja be a következő parancsokat:
```
sudo vi /etc/pam.d/common-session
```

Adja hozzá a következő sort a fájl 'munkamenet nem kötelező pam_sss.so' vonala alatti, és mentse azt:
```
session required pam_mkhomedir.so skel=/etc/skel/ umask=0077
```


## <a name="verify-domain-join"></a>A tartományhoz való csatlakozás ellenőrzéséhez
Győződjön meg arról, hogy a gép sikeresen csatlakoztatva lett a felügyelt tartományhoz. Csatlakozhat a tartományhoz csatlakoztatott Ubuntu virtuális gép egy másik SSH-kapcsolatot. Egy tartományi fiókot használjon, és ezután ellenőrizze, hogy ha a felhasználói fióknak megfelelően megoldódott-e.

1. A terminálon az SSH-típus a következő parancsot a csatlakozás tartományhoz csatlakoztatott Ubuntu virtuális gép SSH-val. A felügyelt tartományhoz tartozó tartományi fiók használata (például "bob@CONTOSO100.COM" Ebben az esetben.)
    ```
    ssh -l bob@CONTOSO100.COM contoso-ubuntu.contoso100.com
    ```

2. A terminálban SSH írja be a következő paranccsal tekintse meg, ha a kezdőkönyvtár megfelelően inicializálva.
    ```
    pwd
    ```

3. Az SSH terminálon írja be a megtekintéséhez, ha a csoporttagság megfelelően vannak feloldva a következő parancsot.
    ```
    id
    ```


## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Az "AAD DC rendszergazdák" csoport sudo jogosultságok engedélyezése
Meg lehet adni az "AAD DC rendszergazdák" csoport rendszergazdai jogosultságokkal az Ubuntu rendszerű virtuális gép azon tagjai. A sudo fájl nem található: /etc/sudoers. Az új funkció a sudoers AD-csoportok tagjai hajthatják végre a sudo.

1. Az SSH a terminál győződjön meg arról, jelentkezzen be a SUPERUSER felhasználói jogosultságai. A helyi rendszergazdai fiók, a virtuális gép létrehozásakor megadott is használhatja. Hajtsa végre a következő parancsot:
    ```
    sudo vi /etc/sudoers
    ```

2. Adja hozzá a következő bejegyzést a /etc/sudoers fájlhoz, és mentse azt:
    ```
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators ALL=(ALL) NOPASSWD:ALL
    ```

3. Most már bejelentkezhet az "AAD DC rendszergazdák" csoport tagjaként, és a virtuális gép rendszergazdai jogosultságokkal kell rendelkeznie.


## <a name="troubleshooting-domain-join"></a>A tartományhoz való csatlakozás hibáinak elhárítása
Tekintse meg a [hibaelhárítás tartományhoz való csatlakozás](active-directory-ds-admin-guide-join-windows-vm-portal.md#troubleshoot-joining-a-domain) cikk.


## <a name="related-content"></a>Kapcsolódó tartalom
* [Az Azure AD tartományi szolgáltatások – első lépések útmutató](active-directory-ds-getting-started.md)
* [A Windows Server virtuális gépek csatlakoztatása az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz](active-directory-ds-admin-guide-join-windows-vm.md)
* [Bejelentkezés Linux rendszerű virtuális gép](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
