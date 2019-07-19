---
title: 'Azure Active Directory Domain Services: CentOS virtuális gép csatlakoztatása felügyelt tartományhoz | Microsoft Docs'
description: Csatlakozás CentOS Linux rendszerű virtuális géphez Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 16100caa-f209-4cb0-86d3-9e218aeb51c6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: c4a04f55f4f69521f00ed450a2d3d1a80b56761c
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234080"
---
# <a name="join-a-centos-linux-virtual-machine-to-a-managed-domain"></a>CentOS Linux rendszerű virtuális gép csatlakoztatása felügyelt tartományhoz
Ez a cikk bemutatja, hogyan csatlakozhat egy CentOS Linux rendszerű virtuális géphez az Azure-ban egy Azure AD Domain Services felügyelt tartományhoz.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Előkészületek
A cikkben felsorolt feladatok elvégzéséhez a következőkre lesz szüksége:
1. Érvényes **Azure-előfizetés**.
2. Egy **Azure ad-címtár** – szinkronizálva van egy helyszíni címtárral vagy egy csak felhőalapú címtárral.
3. **Azure ad Domain Services** engedélyezni kell az Azure ad-címtárat. Ha még nem tette meg, kövesse az [első lépések útmutatóban](create-instance.md)ismertetett összes feladatot.
4. Győződjön meg arról, hogy a felügyelt tartomány IP-címeit a virtuális hálózat DNS-kiszolgálóinak megfelelően konfigurálta. További információ: [Az Azure-beli virtuális hálózat DNS-beállításainak frissítése](active-directory-ds-getting-started-dns.md)
5. Hajtsa végre a [jelszavak Azure ad Domain Services felügyelt tartományhoz](active-directory-ds-getting-started-password-sync.md)való szinkronizálásához szükséges lépéseket.


## <a name="provision-a-centos-linux-virtual-machine"></a>CentOS Linux rendszerű virtuális gép kiépítése
Hozzon létre egy CentOS virtuális gépet az Azure-ban az alábbi módszerek bármelyikével:
* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

> [!IMPORTANT]
> * Telepítse a virtuális gépet ugyanabba a **virtuális hálózatba, amelyben engedélyezte a Azure ad Domain Services**.
> * Válasszon egy **másik** alhálózatot, amelynél engedélyezte a Azure ad Domain Services.
>


## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>Távoli kapcsolódás az újonnan kiosztott linuxos virtuális géphez
A CentOS virtuális gép üzembe helyezése az Azure-ban történt. A következő feladat a virtuális gép távoli kapcsolódása a virtuális géphez a virtuális gép üzembe helyezése során létrehozott helyi rendszergazdai fiók használatával.

Kövesse a cikk utasításait a [Linux rendszerű virtuális gépekre való bejelentkezéshez](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>A Hosts fájl konfigurálása a Linux rendszerű virtuális gépen
Az SSH-terminálban szerkessze a/etc/hosts fájlt, és frissítse a gép IP-címét és állomásnevét.

```console
sudo vi /etc/hosts
```

A Hosts fájlban adja meg a következő értéket:

```console
127.0.0.1 contoso-centos.contoso100.com contoso-centos
```

Itt a "contoso100.com" a felügyelt tartomány DNS-tartományneve. a "contoso-CentOS" a felügyelt tartományhoz csatlakozó CentOS virtuális gép állomásneve.


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>A szükséges csomagok telepítése a linuxos virtuális gépen
Ezután telepítse a virtuális gépen a tartományhoz való csatlakozáshoz szükséges csomagokat. Az SSH-terminálon írja be a következő parancsot a szükséges csomagok telepítéséhez:

```console
sudo yum install realmd sssd krb5-workstation krb5-libs oddjob oddjob-mkhomedir samba-common-tools
```


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>A linuxos virtuális gép csatlakoztatása a felügyelt tartományhoz
Most, hogy a szükséges csomagok telepítve vannak a linuxos virtuális gépen, a következő feladat a virtuális gép csatlakoztatása a felügyelt tartományhoz.

1. Fedezze fel a HRE Domain Services által felügyelt tartományt. Az SSH-terminálban írja be a következő parancsot:

    ```console
    sudo realm discover CONTOSO100.COM
    ```

   > [!NOTE]
   > **Hibaelhárítás** Ha a *tartomány felderítése* nem találja a felügyelt tartományt:  
   >    * Győződjön meg arról, hogy a tartomány elérhető a virtuális gépről (ping kipróbálása).  
   >    * Győződjön meg arról, hogy a virtuális gép valóban telepítve van ugyanazon a virtuális hálózaton, amelyben a felügyelt tartomány elérhető.
   >    * Ellenőrizze, hogy frissítette-e a virtuális hálózat DNS-kiszolgálójának beállításait, hogy a felügyelt tartomány tartományvezérlőjére mutasson.  

2. Kerberos inicializálása. Az SSH-terminálban írja be a következő parancsot:

    > [!TIP]
    > * A "HRE DC rendszergazdák" csoportba tartozó felhasználó meghatározása.
    > * Adja meg a tartománynevet nagybetűvel, különben a kinit parancsot sikertelen lesz.

    ```console
    kinit bob@CONTOSO100.COM
    ```

3. Csatlakoztassa a gépet a tartományhoz. Az SSH-terminálban írja be a következő parancsot:

    > [!TIP]
    > Használja ugyanazt a felhasználói fiókot, amelyet az előző lépésben ("kinit parancsot") adott meg.

    ```console
    sudo realm join --verbose CONTOSO100.COM -U 'bob@CONTOSO100.COM'
    ```

Ha a gép sikeresen csatlakoztatva van a felügyelt tartományhoz, egy üzenetet kell kapnia ("a számítógép sikeres regisztrálása a tartományban").


## <a name="verify-domain-join"></a>Tartományhoz való csatlakozás ellenőrzése
Ellenőrizze, hogy a gép sikeresen csatlakozott-e a felügyelt tartományhoz. Csatlakozás a tartományhoz csatlakoztatott CentOS virtuális géphez egy másik SSH-kapcsolat használatával. Használjon tartományi felhasználói fiókot, és ellenőrizze, hogy a felhasználói fiók megfelelően van-e feloldva.

1. Az SSH-terminálon írja be a következő parancsot a tartományhoz csatlakozó CentOS virtuális géphez az SSH használatával való csatlakozáshoz. Használjon olyan tartományi fiókot, amely a felügyelt tartományhoz tartozik (ebben azbob@CONTOSO100.COMesetben például "").
    
    ```console
    ssh -l bob@CONTOSO100.COM contoso-centos.contoso100.com
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
Tekintse meg a tartományhoz való [Csatlakozás hibaelhárítása](join-windows-vm.md#troubleshoot-joining-a-domain) című cikket.

## <a name="related-content"></a>Kapcsolódó tartalom
* [Azure AD Domain Services – Első lépések útmutató](create-instance.md)
* [Windows Server rendszerű virtuális gép csatlakoztatása Azure AD Domain Services felügyelt tartományhoz](active-directory-ds-admin-guide-join-windows-vm.md)
* [Bejelentkezés Linux rendszerű virtuális gépre](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* [A Kerberos telepítése](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Managing_Smart_Cards/installing-kerberos.html)
* [Red Hat Enterprise Linux 7 – Windows-integrációs útmutató](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Windows_Integration_Guide/index.html)
