---
title: 'Azure Active Directory Domain Services: Ubuntu virtuális gép csatlakoztatása felügyelt tartományhoz | Microsoft Docs'
description: Ubuntu Linux virtuális gép csatlakoztatása Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 804438c4-51a1-497d-8ccc-5be775980203
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: 29a6cb69a818ed11e5f20dddd7299c01fbefbf47
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234021"
---
# <a name="join-an-ubuntu-virtual-machine-in-azure-to-a-managed-domain"></a>Ubuntu virtuális gép csatlakoztatása az Azure-ban egy felügyelt tartományhoz
Ez a cikk bemutatja, hogyan csatlakozhat egy Ubuntu Linux virtuális géphez egy Azure AD Domain Services felügyelt tartományhoz.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Előkészületek
A cikkben felsorolt feladatok elvégzéséhez a következőkre lesz szüksége:  
1. Érvényes **Azure-előfizetés**.
2. Egy **Azure ad-címtár** – szinkronizálva van egy helyszíni címtárral vagy egy csak felhőalapú címtárral.
3. **Azure ad Domain Services** engedélyezni kell az Azure ad-címtárat. Ha még nem tette meg, kövesse az [első lépések útmutatóban](create-instance.md)ismertetett összes feladatot.
4. Győződjön meg arról, hogy a felügyelt tartomány IP-címeit a virtuális hálózat DNS-kiszolgálóinak megfelelően konfigurálta. További információ: [Az Azure-beli virtuális hálózat DNS-beállításainak frissítése](active-directory-ds-getting-started-dns.md)
5. Hajtsa végre a [jelszavak Azure ad Domain Services felügyelt tartományhoz](active-directory-ds-getting-started-password-sync.md)való szinkronizálásához szükséges lépéseket.


## <a name="provision-an-ubuntu-linux-virtual-machine"></a>Ubuntu Linux virtuális gép kiépítése
Hozzon létre egy Ubuntu Linux virtuális gépet az Azure-ban az alábbi módszerek bármelyikével:
* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

> [!IMPORTANT]
> * Telepítse a virtuális gépet ugyanabba a **virtuális hálózatba, amelyben engedélyezte a Azure ad Domain Services**.
> * Válasszon egy **másik** alhálózatot, amelynél engedélyezte a Azure ad Domain Services.
>


## <a name="connect-remotely-to-the-ubuntu-linux-virtual-machine"></a>Távoli kapcsolódás a Ubuntu Linux virtuális géphez
Az Ubuntu virtuális gép üzembe helyezése az Azure-ban történt. A következő feladat a virtuális gép távoli kapcsolódása a virtuális géphez a virtuális gép üzembe helyezése során létrehozott helyi rendszergazdai fiók használatával.

Kövesse a cikk utasításait a [Linux rendszerű virtuális gépekre való bejelentkezéshez](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>A Hosts fájl konfigurálása a Linux rendszerű virtuális gépen
Az SSH-terminálban szerkessze a/etc/hosts fájlt, és frissítse a gép IP-címét és állomásnevét.

```console
sudo vi /etc/hosts
```

A Hosts fájlban adja meg a következő értéket:

```console
127.0.0.1 contoso-ubuntu.contoso100.com contoso-ubuntu
```

Itt a "contoso100.com" a felügyelt tartomány DNS-tartományneve. a "contoso-Ubuntu" a felügyelt tartományhoz csatlakozó Ubuntu-beli virtuális gép állomásneve.


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>A szükséges csomagok telepítése a linuxos virtuális gépen
Ezután telepítse a virtuális gépen a tartományhoz való csatlakozáshoz szükséges csomagokat. Hajtsa végre a következő lépéseket:

1.  Az SSH-terminálon írja be a következő parancsot a csomagok listájáról a tárházból való letöltéséhez. Ez a parancs frissíti a csomagok listáját, hogy információt kapjon a csomagok legújabb verzióiról és azok függőségeiről.

    ```console
    sudo apt-get update
    ```

2. A szükséges csomagok telepítéséhez írja be a következő parancsot.

    ```console
      sudo apt-get install krb5-user samba sssd sssd-tools libnss-sss libpam-sss ntp ntpdate realmd adcli
    ```

3. A Kerberos-telepítés során egy rózsaszín képernyő jelenik meg. A "krb5-user" csomag telepítése kéri a tartománynevet (minden nagybetűs). A telepítés a [Realm] és a [domain_realm] szakaszt írja a/etc/krb5.conf.-ben.

    > [!TIP]
    > Ha a felügyelt tartomány neve contoso100.com, adja meg a CONTOSO100.COM tartományt. Ne feledje, hogy a tartománynevet nagybetűvel kell megadni.


## <a name="configure-the-ntp-network-time-protocol-settings-on-the-linux-virtual-machine"></a>Az NTP (Network Time Protocol) beállításainak konfigurálása a Linux rendszerű virtuális gépen
Az Ubuntu-alapú virtuális gép dátumának és időpontjának szinkronizálnia kell a felügyelt tartománnyal. Adja hozzá a felügyelt tartomány NTP-állomásnevét a/etc/ntp.conf-fájlhoz.

```console
sudo vi /etc/ntp.conf
```

Az NTP. conf fájlban adja meg a következő értéket, és mentse a fájlt:

```console
server contoso100.com
```

Itt a "contoso100.com" a felügyelt tartomány DNS-tartományneve.

Most szinkronizálja az Ubuntu virtuális gép dátumát és idejét az NTP-kiszolgálóval, majd indítsa el az NTP szolgáltatást:

```console
sudo systemctl stop ntp
sudo ntpdate contoso100.com
sudo systemctl start ntp
```


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>A linuxos virtuális gép csatlakoztatása a felügyelt tartományhoz
Most, hogy a szükséges csomagok telepítve vannak a linuxos virtuális gépen, a következő feladat a virtuális gép csatlakoztatása a felügyelt tartományhoz.

1. Fedezze fel a HRE Domain Services által felügyelt tartományt. Az SSH-terminálban írja be a következő parancsot:

    ```console
    sudo realm discover CONTOSO100.COM
    ```

   > [!NOTE]
   > **Hibaelhárítás** Ha a *tartomány felderítése* nem találja a felügyelt tartományt:
   >   * Győződjön meg arról, hogy a tartomány elérhető a virtuális gépről (ping kipróbálása).
   >   * Győződjön meg arról, hogy a virtuális gép valóban telepítve van ugyanazon a virtuális hálózaton, amelyben a felügyelt tartomány elérhető.
   >   * Ellenőrizze, hogy frissítette-e a virtuális hálózat DNS-kiszolgálójának beállításait, hogy a felügyelt tartomány tartományvezérlőjére mutasson.

2. Kerberos inicializálása. Az SSH-terminálban írja be a következő parancsot:

    > [!TIP]
    > * Győződjön meg arról, hogy az "HRE DC-rendszergazdák" csoportba tartozó felhasználót ad meg.
    > * Adja meg a tartománynevet nagybetűvel, különben a kinit parancsot sikertelen lesz.
    >

    ```console
    kinit bob@CONTOSO100.COM
    ```

3. Csatlakoztassa a gépet a tartományhoz. Az SSH-terminálban írja be a következő parancsot:

    > [!TIP]
    > Használja ugyanazt a felhasználói fiókot, amelyet az előző lépésben ("kinit parancsot") adott meg.

    ```console
    sudo realm join --verbose CONTOSO100.COM -U 'bob@CONTOSO100.COM' --install=/
    ```

Ha a gép sikeresen csatlakoztatva van a felügyelt tartományhoz, egy üzenetet kell kapnia ("a számítógép sikeres regisztrálása a tartományban").


## <a name="update-the-sssd-configuration-and-restart-the-service"></a>Frissítse a SSSD konfigurációját, és indítsa újra a szolgáltatást.
1. Az SSH-terminálon írja be a következő parancsot. Nyissa meg a sssd. conf fájlt, és végezze el a következő módosítást
    
    ```console
    sudo vi /etc/sssd/sssd.conf
    ```

2. Tegye megjegyzésbe a **use_fully_qualified_names = True** sort, és mentse a fájlt.
    
    ```console
    # use_fully_qualified_names = True
    ```

3. Indítsa újra a SSSD szolgáltatást.
    
    ```console
    sudo service sssd restart
    ```


## <a name="configure-automatic-home-directory-creation"></a>Az automatikus kezdőkönyvtár létrehozásának konfigurálása
Ha engedélyezni szeretné a kezdőkönyvtár automatikus létrehozását a felhasználók bejelentkezése után, írja be a következő parancsokat a PuTTY-terminálba:

```console
sudo vi /etc/pam.d/common-session
```

Adja hozzá a következő sort a fájlhoz a "munkamenet választható pam_sss. so" sorban, és mentse:

```console
session required pam_mkhomedir.so skel=/etc/skel/ umask=0077
```


## <a name="verify-domain-join"></a>Tartományhoz való csatlakozás ellenőrzése
Ellenőrizze, hogy a gép sikeresen csatlakozott-e a felügyelt tartományhoz. Kapcsolódjon a tartományhoz csatlakoztatott Ubuntu virtuális géphez egy másik SSH-kapcsolat használatával. Használjon tartományi felhasználói fiókot, és ellenőrizze, hogy a felhasználói fiók megfelelően van-e feloldva.

1. Az SSH-terminálon írja be a következő parancsot a tartományhoz csatlakoztatott Ubuntu virtuális géphez való csatlakozáshoz az SSH használatával. Használjon olyan tartományi fiókot, amely a felügyelt tartományhoz tartozik (ebben azbob@CONTOSO100.COMesetben például "").
    
    ```console
    ssh -l bob@CONTOSO100.COM contoso-ubuntu.contoso100.com
    ```

2. Az SSH-terminálon írja be a következő parancsot annak ellenőrzéséhez, hogy a kezdőkönyvtár megfelelően lett-e inicializálva.
    
    ```console
    pwd
    ```

3. Az SSH-terminálon írja be a következő parancsot annak ellenőrzéséhez, hogy a csoporttagság megfelelően van-e feloldva.
    
    ```console
    id
    ```


## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>A "HRE DC Administrators" csoport sudo-jogosultságának megadása
Az "HRE DC rendszergazdák" csoport tagjainak rendszergazdai jogosultságokat adhat az Ubuntu virtuális gépen. A sudo fájl a következő helyen található:/etc/sudoers. A sudoers-ben hozzáadott AD-csoportok tagjai a sudo-t is elvégezhetik.

1. Az SSH-terminálon ellenőrizze, hogy rendszergazdai jogosultságokkal van-e bejelentkezve. Használhatja a virtuális gép létrehozásakor megadott helyi rendszergazdai fiókot. Hajtsa végre a következő parancsot:
    
    ```console
    sudo vi /etc/sudoers
    ```

2. Adja hozzá a következő bejegyzést a/etc/sudoers-fájlhoz, és mentse azt:
    
    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators ALL=(ALL) NOPASSWD:ALL
    ```

3. Most már bejelentkezhet az "HRE DC rendszergazdák" csoport tagjaként, és rendszergazdai jogosultságokkal kell rendelkeznie a virtuális gépen.


## <a name="troubleshooting-domain-join"></a>Tartományhoz való csatlakozás hibaelhárítása
Tekintse meg a tartományhoz való [Csatlakozás hibaelhárítása](join-windows-vm.md#troubleshoot-joining-a-domain) című cikket.


## <a name="related-content"></a>Kapcsolódó tartalom
* [Azure AD Domain Services – Első lépések útmutató](create-instance.md)
* [Windows Server rendszerű virtuális gép csatlakoztatása Azure AD Domain Services felügyelt tartományhoz](active-directory-ds-admin-guide-join-windows-vm.md)
* [Bejelentkezés Linux rendszerű virtuális gépre](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
