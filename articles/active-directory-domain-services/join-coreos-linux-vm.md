---
title: 'Azure Active Directory Domain Services: Csatlakozás CoreOS Linux rendszerű virtuális géphez | Microsoft Docs'
description: CoreOS Linux rendszerű virtuális gép csatlakoztatása Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 5db65f30-bf69-4ea3-9ea5-add1db83fdb8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: c1f3d1ec7bb9e9f449cea3f9aa36ca8f80348c6e
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69612817"
---
# <a name="join-a-coreos-linux-virtual-machine-to-a-managed-domain"></a>CoreOS Linux rendszerű virtuális gép csatlakoztatása felügyelt tartományhoz
Ez a cikk bemutatja, hogyan csatlakozhat egy CoreOS Linux rendszerű virtuális géphez az Azure-ban egy Azure AD Domain Services felügyelt tartományhoz.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Előkészületek
A cikkben felsorolt feladatok elvégzéséhez a következőkre lesz szüksége:
1. Érvényes **Azure-előfizetés**.
2. Egy **Azure ad-címtár** – szinkronizálva van egy helyszíni címtárral vagy egy csak felhőalapú címtárral.
3. **Azure ad Domain Services** engedélyezni kell az Azure ad-címtárat. Ha még nem tette meg, kövesse az [első lépések útmutatóban](tutorial-create-instance.md)ismertetett összes feladatot.
4. Győződjön meg arról, hogy a felügyelt tartomány IP-címeit a virtuális hálózat DNS-kiszolgálóinak megfelelően konfigurálta. További információ: [Az Azure-beli virtuális hálózat DNS-beállításainak frissítése](tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network)
5. Hajtsa végre a [jelszavak Azure ad Domain Services felügyelt tartományhoz](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)való szinkronizálásához szükséges lépéseket.


## <a name="provision-a-coreos-linux-virtual-machine"></a>CoreOS Linux rendszerű virtuális gép kiépítése
Hozzon létre egy CoreOS virtuális gépet az Azure-ban az alábbi módszerek bármelyikével:
* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

Ez a cikk a **CoreOS Linux (stable)** virtuálisgép-rendszerképet használja az Azure-ban.

> [!IMPORTANT]
> * Telepítse a virtuális gépet ugyanabba a **virtuális hálózatba, amelyben engedélyezte a Azure ad Domain Services**.
> * Válasszon egy **másik** alhálózatot, amelynél engedélyezte a Azure ad Domain Services.
>


## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>Távoli kapcsolódás az újonnan kiosztott linuxos virtuális géphez
A CoreOS virtuális gép üzembe helyezése az Azure-ban történt. A következő feladat a virtuális gép távoli kapcsolódása a virtuális géphez a virtuális gép üzembe helyezése során létrehozott helyi rendszergazdai fiók használatával.

Kövesse a cikk utasításait a [Linux rendszerű virtuális gépekre](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)való bejelentkezéshez.


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>A Hosts fájl konfigurálása a Linux rendszerű virtuális gépen
Az SSH-terminálban szerkessze a/etc/hosts fájlt, és frissítse a gép IP-címét és állomásnevét.

```console
sudo vi /etc/hosts
```

A Hosts fájlban adja meg a következő értéket:

```console
127.0.0.1 contoso-coreos.contoso.com contoso-coreos
```

Itt a "contoso.com" a felügyelt tartomány DNS-tartományneve. a "contoso-CoreOS" a felügyelt tartományhoz csatlakozó CoreOS virtuális gép állomásneve.


## <a name="configure-the-sssd-service-on-the-linux-virtual-machine"></a>A SSSD szolgáltatás konfigurálása a Linux rendszerű virtuális gépen
Ezután frissítse a SSSD konfigurációs fájlját ("/etc/sssd/sssd.conf") a következő mintának megfelelően:

```console
[sssd]
config_file_version = 2
services = nss, pam
domains = contoso.COM

[domain/contoso.COM]
id_provider = ad
auth_provider = ad
chpass_provider = ad

ldap_uri = ldap://contoso.com
ldap_search_base = dc=contoso,dc=com
ldap_schema = rfc2307bis
ldap_sasl_mech = GSSAPI
ldap_user_object_class = user
ldap_group_object_class = group
ldap_user_home_directory = unixHomeDirectory
ldap_user_principal = userPrincipalName
ldap_account_expire_policy = ad
ldap_force_upper_case_realm = true
fallback_homedir = /home/%d/%u

krb5_server = contoso.com
krb5_realm = contoso.COM
```

Cserélje le a contosot. COM "a felügyelt tartomány DNS-tartománynevével. Győződjön meg arról, hogy az conf fájlban a tartománynevet nagybetűvel adja meg.


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>A linuxos virtuális gép csatlakoztatása a felügyelt tartományhoz
Most, hogy a szükséges csomagok telepítve vannak a linuxos virtuális gépen, a következő feladat a virtuális gép csatlakoztatása a felügyelt tartományhoz.

```console
sudo adcli join -D contoso.COM -U bob@contoso.COM -K /etc/krb5.keytab -H contoso-coreos.contoso.com -N coreos
```


> [!NOTE]
> **Hibaelhárítás** Ha a *adcli* nem találja a felügyelt tartományt:
>   * Győződjön meg arról, hogy a tartomány elérhető a virtuális gépről (ping kipróbálása).
>   * Győződjön meg arról, hogy a virtuális gép valóban telepítve van ugyanazon a virtuális hálózaton, amelyben a felügyelt tartomány elérhető.
>   * Ellenőrizze, hogy frissítette-e a virtuális hálózat DNS-kiszolgálójának beállításait, hogy a felügyelt tartomány tartományvezérlőjére mutasson.

Indítsa el a SSSD szolgáltatást. Az SSH-terminálban írja be a következő parancsot:
  
```console
sudo systemctl start sssd.service
```


## <a name="verify-domain-join"></a>Tartományhoz való csatlakozás ellenőrzése
Ellenőrizze, hogy a gép sikeresen csatlakozott-e a felügyelt tartományhoz. Kapcsolódjon a tartományhoz csatlakoztatott CoreOS virtuális géphez egy másik SSH-kapcsolat használatával. Használjon tartományi felhasználói fiókot, és ellenőrizze, hogy a felhasználói fiók megfelelően van-e feloldva.

1. Az SSH-terminálon írja be a következő parancsot a tartományhoz csatlakoztatott CoreOS virtuális géphez való csatlakozáshoz az SSH használatával. Használjon olyan tartományi fiókot, amely a felügyelt tartományhoz tartozik (ebben azbob@contoso.COMesetben például "").
    
    ```console
    ssh -l bob@contoso.COM contoso-coreos.contoso.com
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
