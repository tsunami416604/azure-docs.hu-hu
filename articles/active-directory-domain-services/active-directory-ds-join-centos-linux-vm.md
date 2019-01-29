---
title: 'Az Azure Active Directory tartományi szolgáltatások: CentOS virtuális gépek csatlakoztatása felügyelt tartományokhoz |} A Microsoft Docs'
description: CentOS Linux rendszerű virtuális gép csatlakoztatása az Azure AD tartományi szolgáltatások
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 16100caa-f209-4cb0-86d3-9e218aeb51c6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: ergreenl
ms.openlocfilehash: 9ea236718b7fd9b808a5092dddec9574f6b93b48
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55167155"
---
# <a name="join-a-centos-linux-virtual-machine-to-a-managed-domain"></a>CentOS Linux rendszerű virtuális gép csatlakoztatása felügyelt tartományokhoz
Ez a cikk bemutatja, hogyan CentOS Linux rendszerű virtuális gép csatlakoztatása az Azure AD tartományi szolgáltatásokkal felügyelt tartományban az Azure-ban.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Előkészületek
A cikkben szereplő feladatok elvégzéséhez szüksége:
1. Egy érvényes **Azure-előfizetés**.
2. Egy **Azure AD-címtár** -vagy az egy helyszíni címtár vagy egy csak felhőalapú címtárral szinkronizálja.
3. **Az Azure AD Domain Services** engedélyezve kell lennie az Azure AD-címtárban. Ha még nem tette, minden ismertetett feladatok végrehajtásával a [a kezdeti lépések útmutatóban](active-directory-ds-getting-started.md).
4. Győződjön meg arról, hogy már konfigurálta az IP-címek a felügyelt tartomány a virtuális hálózat DNS-kiszolgálóként. További információkért lásd: [az Azure virtuális hálózat DNS-beállításainak frissítése](active-directory-ds-getting-started-dns.md)
5. Végezze el a szükséges lépéseket [szinkronizálja a jelszavakat az Azure AD tartományi szolgáltatásokkal felügyelt tartományban](active-directory-ds-getting-started-password-sync.md).


## <a name="provision-a-centos-linux-virtual-machine"></a>CentOS Linux rendszerű virtuális gép kiépítése
Az Azure-ban, az alábbi módszerek bármelyikével CentOS virtuális gép kiépítése:
* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

> [!IMPORTANT]
> * Telepítse a virtuális gépet, azokat a **azonos virtuális hálózatban, amelyiken engedélyezte az Azure AD Domain Services**.
> * Válasszon ki egy **másik alhálózatot** fut, amelyiken engedélyezte az Azure AD tartományi szolgáltatásokat.
>


## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>Távoli csatlakozás az újonnan létrehozott Linux virtuális gép
A CentOS virtuális gépet az Azure-ban van kiépítve. A következő feladata távolról csatlakozni a virtuális gépet a virtuális gép kiépítése során létrehozott helyi rendszergazdai fiókkal.

Kövesse a cikk a [bejelentkezés egy Linux rendszerű virtuális gép](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>A Linux rendszerű virtuális gép hosts fájljának konfigurálása
A terminálban SSH a Hosts fájl szerkesztése, és frissítse a gép IP-cím és az állomásnevet.

```
sudo vi /etc/hosts
```

Adja meg a hosts fájl a következő értéket:

```
127.0.0.1 contoso-centos.contoso100.com contoso-centos
```
"Contoso100.com" Íme a felügyelt tartomány DNS-tartomány nevét. "contoso – centos", amelyhez a felügyelt tartományhoz csatlakozik, a CentOS virtuális gép állomásnevét.


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Telepítse a szükséges csomagokat a Linux rendszerű virtuális gépen
Ezután telepítse a virtuális gép tartományhoz való csatlakozás a szükséges csomagokat. Az SSH terminálon írja be a következő parancsot a szükséges csomagok telepítéséhez:

    ```
    sudo yum install realmd sssd krb5-workstation krb5-libs oddjob oddjob-mkhomedir samba-common-tools
    ```


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>A Linux rendszerű virtuális gép csatlakoztatása a felügyelt tartományhoz
Most, hogy a szükséges csomagok telepítve vannak a Linux rendszerű virtuális gép, a következő feladata a virtuális gép csatlakoztatása a felügyelt tartományhoz.

1. Fedezze fel az AAD tartományi szolgáltatásokkal felügyelt tartományban. Az SSH terminálon írja be a következő parancsot:

    ```
    sudo realm discover CONTOSO100.COM
    ```

    > [!NOTE]
    > **Hibaelhárítás:** Ha *a kezdőtartomány felderítése* nem találja a felügyelt tartomány:  
      * Győződjön meg arról, hogy a tartomány érhető el a virtuális gépről (ping. Próbálja meg).  
      * Ellenőrizze, hogy a virtuális gép valóban lett telepítve, az azonos virtuális hálózatban, amely a felügyelt tartomány érhető el.
      * Ellenőrizze, hogy ha frissítette a DNS-kiszolgáló beállításainak a virtuális hálózathoz, hogy a felügyelt tartomány tartományvezérlőit mutasson.  
      >

2. A Kerberos inicializálása. Az SSH terminálon írja be a következő parancsot:

    > [!TIP]
    > * Adja meg a felhasználó, aki az "AAD DC rendszergazdák" csoportba tartozik.
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
    sudo realm join --verbose CONTOSO100.COM -U 'bob@CONTOSO100.COM'
    ```

Meg kell hibaüzenet jelenik meg ("sikeresen regisztrált számítógép tartomány") Ha a gép sikeresen csatlakozott a felügyelt tartományhoz.


## <a name="verify-domain-join"></a>A tartományhoz való csatlakozás ellenőrzéséhez
Győződjön meg arról, hogy a gép sikeresen csatlakoztatva lett a felügyelt tartományhoz. Csatlakozhat a tartományhoz csatlakoztatott CentOS virtuális Gépet egy másik SSH-kapcsolat használatával. Egy tartományi fiókot használjon, és ezután ellenőrizze, hogy ha a felhasználói fióknak megfelelően megoldódott-e.

1. Az SSH a terminál írja be a következő parancsot a csatlakozás tartományhoz csatlakoztatott CentOS virtuális gépre SSH-val. A felügyelt tartományhoz tartozó tartományi fiók használata (például "bob@CONTOSO100.COM" Ebben az esetben.)
    ```
    ssh -l bob@CONTOSO100.COM contoso-centos.contoso100.com
    ```

2. A terminálban SSH írja be a következő paranccsal tekintse meg, ha a kezdőkönyvtár megfelelően inicializálva.
    ```
    pwd
    ```

3. Az SSH terminálon írja be a megtekintéséhez, ha a csoporttagság megfelelően vannak feloldva a következő parancsot.
    ```
    id
    ```


## <a name="troubleshooting-domain-join"></a>A tartományhoz való csatlakozás hibáinak elhárítása
Tekintse meg a [hibaelhárítás tartományhoz való csatlakozás](active-directory-ds-admin-guide-join-windows-vm-portal.md#troubleshoot-joining-a-domain) cikk.

## <a name="related-content"></a>Kapcsolódó tartalom
* [Az Azure AD tartományi szolgáltatások – első lépések útmutató](active-directory-ds-getting-started.md)
* [A Windows Server virtuális gépek csatlakoztatása az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz](active-directory-ds-admin-guide-join-windows-vm.md)
* [Bejelentkezés Linux rendszerű virtuális gép](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* [A Kerberos telepítése](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Managing_Smart_Cards/installing-kerberos.html)
* [Red Hat Enterprise Linux 7 – Windows-integrációs útmutató](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Windows_Integration_Guide/index.html)
