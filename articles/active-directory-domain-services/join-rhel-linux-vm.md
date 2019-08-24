---
title: 'Azure Active Directory Domain Services: RHEL virtuális gép csatlakoztatása felügyelt tartományhoz | Microsoft Docs'
description: Red Hat Enterprise Linux virtuális gép csatlakoztatása Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: d76ae997-2279-46dd-bfc5-c0ee29718096
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: b59bd7c7196ceb87da087967498eca6dda7c212b
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "69990602"
---
# <a name="join-a-red-hat-enterprise-linux-7-virtual-machine-to-a-managed-domain"></a>Red Hat Enterprise Linux 7 virtuális gépek csatlakoztatása felügyelt tartományokhoz
Ez a cikk bemutatja, hogyan csatlakozhat egy Red Hat Enterprise Linux (RHEL) 7 virtuális géphez egy Azure AD Domain Services felügyelt tartományhoz.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Előkészületek
A cikkben felsorolt feladatok elvégzéséhez a következőkre lesz szüksége:  
1. Érvényes **Azure-előfizetés**.
2. Egy **Azure ad-címtár** – szinkronizálva van egy helyszíni címtárral vagy egy csak felhőalapú címtárral.
3. **Azure ad Domain Services** engedélyezni kell az Azure ad-címtárat. Ha még nem tette meg, kövesse az [első lépések útmutatóban](tutorial-create-instance.md)ismertetett összes feladatot.
4. Győződjön meg arról, hogy a felügyelt tartomány IP-címeit a virtuális hálózat DNS-kiszolgálóinak megfelelően konfigurálta. További információ: [Az Azure-beli virtuális hálózat DNS-beállításainak frissítése](tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network)
5. Hajtsa végre a [jelszavak Azure ad Domain Services felügyelt tartományhoz](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)való szinkronizálásához szükséges lépéseket.


## <a name="provision-a-red-hat-enterprise-linux-virtual-machine"></a>Red Hat Enterprise Linux virtuális gép kiépítése
Hozzon létre egy RHEL 7 virtuális gépet az Azure-ban az alábbi módszerek bármelyikével:
* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

> [!IMPORTANT]
> * Telepítse a virtuális gépet ugyanabba a **virtuális hálózatba, amelyben engedélyezte a Azure ad Domain Services**.
> * Válasszon egy **másik** alhálózatot, amelynél engedélyezte a Azure ad Domain Services.
>


## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>Távoli kapcsolódás az újonnan kiosztott linuxos virtuális géphez
Az RHEL 7,2 virtuális gép üzembe helyezése az Azure-ban történt. A következő feladat a virtuális gép távoli kapcsolódása a virtuális géphez a virtuális gép üzembe helyezése során létrehozott helyi rendszergazdai fiók használatával.

Kövesse a cikk utasításait a [Linux rendszerű virtuális gépekre](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)való bejelentkezéshez.


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>A Hosts fájl konfigurálása a Linux rendszerű virtuális gépen
Az SSH-terminálban szerkessze a/etc/hosts fájlt, és frissítse a gép IP-címét és állomásnevét.

```console
sudo vi /etc/hosts
```

A Hosts fájlban adja meg a következő értéket:

```console
127.0.0.1 contoso-rhel.contoso.com contoso-rhel
```

Itt a "contoso.com" a felügyelt tartomány DNS-tartományneve. a "contoso-RHEL" a felügyelt tartományhoz csatlakozó RHEL virtuális gép állomásneve.


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>A szükséges csomagok telepítése a linuxos virtuális gépen
Ezután telepítse a virtuális gépen a tartományhoz való csatlakozáshoz szükséges csomagokat. Az SSH-terminálon írja be a következő parancsot a szükséges csomagok telepítéséhez:

```console
sudo yum install realmd sssd krb5-workstation krb5-libs samba-common-tools
```


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>A linuxos virtuális gép csatlakoztatása a felügyelt tartományhoz
Most, hogy a szükséges csomagok telepítve vannak a linuxos virtuális gépen, a következő feladat a virtuális gép csatlakoztatása a felügyelt tartományhoz.

1. Fedezze fel a HRE Domain Services által felügyelt tartományt. Az SSH-terminálban írja be a következő parancsot:

    ```console
    sudo realm discover CONTOSO.COM
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

    ```console
    kinit bob@CONTOSO.COM
    ```

3. Csatlakoztassa a gépet a tartományhoz. Az SSH-terminálban írja be a következő parancsot:

    > [!TIP]
    > Használja ugyanazt a felhasználói fiókot, amelyet az előző lépésben ("kinit parancsot") adott meg.
    >
    > Ha a virtuális gép nem tud csatlakozni a tartományhoz, győződjön meg arról, hogy a virtuális gép hálózati biztonsági csoportja engedélyezi a kimenő Kerberos-forgalmat az 464-as TCP + UDP-porton az Azure AD DS felügyelt tartományának virtuális hálózati alhálózatán.

    ```console
    sudo realm join --verbose CONTOSO.COM -U 'bob@CONTOSO.COM'
    ```

Ha a gép sikeresen csatlakoztatva van a felügyelt tartományhoz, egy üzenetet kell kapnia ("a számítógép sikeres regisztrálása a tartományban").


## <a name="verify-domain-join"></a>Tartományhoz való csatlakozás ellenőrzése
Ellenőrizze, hogy a gép sikeresen csatlakozott-e a felügyelt tartományhoz. Kapcsolódjon a tartományhoz csatlakoztatott RHEL virtuális géphez egy másik SSH-kapcsolat használatával. Használjon tartományi felhasználói fiókot, és ellenőrizze, hogy a felhasználói fiók megfelelően van-e feloldva.

1. Az SSH-terminálon írja be a következő parancsot a tartományhoz csatlakoztatott RHEL virtuális géphez való csatlakozáshoz az SSH használatával. Használjon olyan tartományi fiókot, amely a felügyelt tartományhoz tartozik (ebben azbob@CONTOSO.COMesetben például "").
    
    ```console
    ssh -l bob@CONTOSO.COM contoso-rhel.contoso.com
    ```

2. Az SSH-terminálon írja be a következő parancsot annak ellenőrzéséhez, hogy a kezdőkönyvtár megfelelően lett-e inicializálva.
    
    ```console
    pwd
    ```

3. Az SSH-terminálon írja be a következő parancsot annak ellenőrzéséhez, hogy a csoporttagság megfelelően van-e feloldva.
    
    ```console
    id
    ```


## <a name="troubleshooting-domain-join"></a>Tartományhoz való csatlakozás hibaelhárítása
Tekintse meg a tartományhoz való [Csatlakozás hibaelhárítása](join-windows-vm.md#troubleshoot-domain-join-issues) című cikket.

## <a name="related-content"></a>Kapcsolódó tartalom
* [Azure AD Domain Services – Első lépések útmutató](tutorial-create-instance.md)
* [Windows Server rendszerű virtuális gép csatlakoztatása Azure AD Domain Services felügyelt tartományhoz](active-directory-ds-admin-guide-join-windows-vm.md)
* [Bejelentkezés egy Linux rendszerű virtuális gépre](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* [A Kerberos telepítése](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Managing_Smart_Cards/installing-kerberos.html)
* [Red Hat Enterprise Linux 7 – Windows-integrációs útmutató](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Windows_Integration_Guide/index.html)
