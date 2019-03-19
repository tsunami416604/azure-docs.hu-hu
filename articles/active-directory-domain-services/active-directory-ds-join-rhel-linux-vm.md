---
title: 'Az Azure Active Directory tartományi szolgáltatások: RHEL virtuális gép csatlakoztatása felügyelt tartományokhoz |} A Microsoft Docs'
description: A Red Hat Enterprise Linux virtuális gépek csatlakoztatása az Azure AD tartományi szolgáltatások
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: d76ae997-2279-46dd-bfc5-c0ee29718096
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: ergreenl
ms.openlocfilehash: 1102a8c80b0040c7044d64c674d956d339238623
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58086649"
---
# <a name="join-a-red-hat-enterprise-linux-7-virtual-machine-to-a-managed-domain"></a>Red Hat Enterprise Linux 7 virtuális gépek csatlakoztatása felügyelt tartományokhoz
Ez a cikk bemutatja, hogyan Red Hat Enterprise Linux (RHEL) 7 virtuális gép csatlakoztatása az Azure AD tartományi szolgáltatásokkal felügyelt tartományban.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Előkészületek
A cikkben szereplő feladatok elvégzéséhez szüksége:  
1. Egy érvényes **Azure-előfizetés**.
2. Egy **Azure AD-címtár** -vagy az egy helyszíni címtár vagy egy csak felhőalapú címtárral szinkronizálja.
3. **Az Azure AD Domain Services** engedélyezve kell lennie az Azure AD-címtárban. Ha még nem tette, minden ismertetett feladatok végrehajtásával a [a kezdeti lépések útmutatóban](active-directory-ds-getting-started.md).
4. Győződjön meg arról, hogy már konfigurálta az IP-címek a felügyelt tartomány a virtuális hálózat DNS-kiszolgálóként. További információkért lásd: [az Azure virtuális hálózat DNS-beállításainak frissítése](active-directory-ds-getting-started-dns.md)
5. Végezze el a szükséges lépéseket [szinkronizálja a jelszavakat az Azure AD tartományi szolgáltatásokkal felügyelt tartományban](active-directory-ds-getting-started-password-sync.md).


## <a name="provision-a-red-hat-enterprise-linux-virtual-machine"></a>Red Hat Enterprise Linux virtuális gép kiépítése
Az Azure-ban, az alábbi módszerek bármelyikével RHEL 7 virtuális gép kiépítése:
* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

> [!IMPORTANT]
> * Telepítse a virtuális gépet, azokat a **azonos virtuális hálózatban, amelyiken engedélyezte az Azure AD Domain Services**.
> * Válasszon ki egy **másik alhálózatot** fut, amelyiken engedélyezte az Azure AD tartományi szolgáltatásokat.
>


## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>Távoli csatlakozás az újonnan létrehozott Linux virtuális gép
Az RHEL 7.2 virtuális gép az Azure-ban van kiépítve. A következő feladata távolról csatlakozni a virtuális gépet a virtuális gép kiépítése során létrehozott helyi rendszergazdai fiókkal.

Kövesse a cikk a [bejelentkezés egy Linux rendszerű virtuális gép](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>A Linux rendszerű virtuális gép hosts fájljának konfigurálása
A terminálban SSH a Hosts fájl szerkesztése, és frissítse a gép IP-cím és az állomásnevet.

```
sudo vi /etc/hosts
```

Adja meg a hosts fájl a következő értéket:

```
127.0.0.1 contoso-rhel.contoso100.com contoso-rhel
```
"Contoso100.com" Íme a felügyelt tartomány DNS-tartomány nevét. "contoso-rhel", amelyhez csatlakozik, a felügyelt tartományhoz RHEL virtuális gép állomásnevét.


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Telepítse a szükséges csomagokat a Linux rendszerű virtuális gépen
Ezután telepítse a virtuális gép tartományhoz való csatlakozás a szükséges csomagokat. Az SSH terminálon írja be a következő parancsot a szükséges csomagok telepítéséhez:

    ```
    sudo yum install realmd sssd krb5-workstation krb5-libs samba-common-tools
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
    sudo realm join --verbose CONTOSO100.COM -U 'bob@CONTOSO100.COM'
    ```

Meg kell hibaüzenet jelenik meg ("sikeresen regisztrált számítógép tartomány") Ha a gép sikeresen csatlakozott a felügyelt tartományhoz.


## <a name="verify-domain-join"></a>A tartományhoz való csatlakozás ellenőrzéséhez
Győződjön meg arról, hogy a gép sikeresen csatlakoztatva lett a felügyelt tartományhoz. Csatlakozhat a tartományhoz csatlakoztatott RHEL virtuális gép egy másik SSH-kapcsolat használatával. Egy tartományi fiókot használjon, és ezután ellenőrizze, hogy ha a felhasználói fióknak megfelelően megoldódott-e.

1. Az SSH a terminál írja be a következő parancsot a csatlakozás tartományhoz csatlakoztatott RHEL virtuális gép SSH-val. A felügyelt tartományhoz tartozó tartományi fiók használata (például "bob@CONTOSO100.COM" Ebben az esetben.)
    ```
    ssh -l bob@CONTOSO100.COM contoso-rhel.contoso100.com
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
