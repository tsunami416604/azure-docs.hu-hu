---
title: 'Az Azure Active Directory tartományi szolgáltatások: CoreOS Linux virtuális gép csatlakoztatása felügyelt tartományhoz |} Microsoft Docs'
description: A CoreOS Linux virtuális gépek csatlakoztatása az Azure AD tartományi szolgáltatások
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 5db65f30-bf69-4ea3-9ea5-add1db83fdb8
ms.service: active-directory
ms.component: domains
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: maheshu
ms.openlocfilehash: bd323faf7fbc42d7b85fdaf87311c3f133045e6f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34589134"
---
# <a name="join-a-coreos-linux-virtual-machine-to-a-managed-domain"></a>A CoreOS Linux virtuális gép csatlakoztatása felügyelt tartományhoz
Ez a cikk bemutatja, hogyan CoreOS Linux virtuális gépek csatlakoztatása az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz az Azure-ban.

## <a name="before-you-begin"></a>Előkészületek
A cikkben szereplő feladatok elvégzéséhez szüksége:
1. Egy érvényes **Azure-előfizetés**.
2. Egy **Azure AD-címtár** -vagy egy helyszíni címtár vagy egy csak felhőalapú directory szinkronizálva.
3. **Azure AD tartományi szolgáltatások** az Azure AD-címtár engedélyezni kell. Ha még nem tette meg, az összes ismertetett feladatok végrehajtásával a [első lépések útmutató](active-directory-ds-getting-started.md).
4. Győződjön meg arról, hogy a virtuális hálózat DNS-kiszolgálóként konfigurálta a felügyelt tartományra IP-címét. További információkért lásd: [az Azure virtuális hálózat DNS-beállításainak frissítése](active-directory-ds-getting-started-dns.md)
5. Végezze el a szükséges lépéseket [szinkronizálja a jelszavakat az Azure AD tartományi szolgáltatások által kezelt tartomány](active-directory-ds-getting-started-password-sync.md).


## <a name="provision-a-coreos-linux-virtual-machine"></a>Rendszerű CoreOS Linux virtuális gép
Az Azure, a következő módszerekkel történő CoreOS virtuális gép kiépítése:
* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

Ebben a cikkben az a **CoreOS Linux (stabil)** virtuálisgép-lemezkép az Azure-ban.

> [!IMPORTANT]
> * A virtuális gép telepítése a **ugyanazt a virtuális hálózatot, amelyben engedélyezte az Azure AD tartományi szolgáltatások**.
> * Válasszon egy **másik alhálózat** fut, amelyen engedélyezte az Azure AD tartományi szolgáltatásokat.
>


## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>Távoli csatlakozás az újonnan kiépített Linux virtuális gép
A CoreOS virtuális gép az Azure-ban van kiépítve. A következő feladata távolról csatlakozni a virtuális gépet a virtuális gép kiépítése során létrehozott helyi rendszergazdai fiók használatával.

A cikk utasításait [Linuxot futtató virtuális gép bejelentkezés](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>A Linux virtuális gép a gazdafájl konfigurálása
Az SSH terminálban az/etc/hosts fájl szerkesztése, és frissítse a gép IP-cím és állomásnevet.

```
sudo vi /etc/hosts
```

A hosts fájlt adja meg a következő értéket:

```
127.0.0.1 contoso-coreos.contoso100.com contoso-coreos
```
"Contoso100.com" Íme a felügyelt tartományok DNS-tartomány nevét. "contoso-coreos" az állomásnevet, amelyhez csatlakozik, a felügyelt tartományra CoreOS virtuális gép.


## <a name="configure-the-sssd-service-on-the-linux-virtual-machine"></a>A SSSD szolgáltatás konfigurálása a Linux virtuális gépen
Következő lépésként frissítse a SSSD konfigurációs fájl ("/ etc/sssd/sssd.conf") a következő mintának megfelelően:

 ```
 [sssd]
 config_file_version = 2
 services = nss, pam
 domains = CONTOSO100.COM

 [domain/CONTOSO100.COM]
 id_provider = ad
 auth_provider = ad
 chpass_provider = ad

 ldap_uri = ldap://contoso100.com
 ldap_search_base = dc=contoso100,dc=com
 ldap_schema = rfc2307bis
 ldap_sasl_mech = GSSAPI
 ldap_user_object_class = user
 ldap_group_object_class = group
 ldap_user_home_directory = unixHomeDirectory
 ldap_user_principal = userPrincipalName
 ldap_account_expire_policy = ad
 ldap_force_upper_case_realm = true
 fallback_homedir = /home/%d/%u

 krb5_server = contoso100.com
 krb5_realm = CONTOSO100.COM
 ```
Cserélje le "CONTOSO100. A COM "a DNS-tartománynévvel a felügyelt tartomány. Ellenőrizze, hogy a conf fájlban beruházási esetben adja meg a tartomány nevét.


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>A Linux virtuális gép csatlakoztatása felügyelt tartományhoz
Most, hogy a szükséges csomagokat a Linux virtuális gépek vannak telepítve, a következő feladat a virtuális gép csatlakoztatása felügyelt tartományhoz.

```
sudo adcli join -D CONTOSO100.COM -U bob@CONTOSO100.COM -K /etc/krb5.keytab -H contoso-coreos.contoso100.com -N coreos
```


> [!NOTE]
> **Hibaelhárítás:** Ha *adcli* nem találja a felügyelt tartományok:
  * Győződjön meg arról, hogy a tartomány elérhető-e a virtuális gépről (próbálja ping).
  * Ellenőrizze, hogy a virtuális gép valóban már alkalmazva van az azonos virtuális hálózatban, amelyben a felügyelt tartományra érhető el.
  * Ellenőrizze, hogy ha a DNS-kiszolgáló beállításait, a virtuális hálózat úgy, hogy a tartományvezérlők, a felügyelt tartományra mutasson frissítése befejeződött.
>

Indítsa el a SSSD szolgáltatást. Az SSH terminálban írja be a következő parancsot:
  ```
  sudo systemctl start sssd.service
  ```


## <a name="verify-domain-join"></a>Ellenőrizze a tartományhoz való csatlakozást
Győződjön meg arról, hogy a gép sikeresen csatlakoztatva lett a felügyelt tartományra. Csatlakozás a tartományhoz csatlakoztatott CoreOS virtuális gép egy másik SSH-kapcsolat használatával. Tartományi felhasználói fiókot használja, és ellenőrizze, hogy megoldódott a felhasználói fiók megfelelően.

1. A Terminálszolgáltatások az SSH-típus a következő parancsot a tartományhoz való csatlakozáshoz csatlakoztatott CoreOS virtuális gép SSH használatával. A felügyelt tartományhoz tartozó tartományi fiókot használni (például "bob@CONTOSO100.COM" Ebben az esetben.)
    ```
    ssh -l bob@CONTOSO100.COM contoso-coreos.contoso100.com
    ```

2. Az SSH terminált írja be a következő parancsot, hogy ha a kezdőkönyvtár megfelelően inicializálva.
    ```
    pwd
    ```

3. Az SSH terminálban az alábbi paranccsal tekintheti meg, ha a csoporttagság megfelelően lehet megoldani.
    ```
    id
    ```


## <a name="troubleshooting-domain-join"></a>Hibaelhárítás a tartományhoz való csatlakozást
Tekintse meg a [hibaelhárítás tartományhoz való csatlakozást](active-directory-ds-admin-guide-join-windows-vm-portal.md#troubleshoot-joining-a-domain) cikk.

## <a name="related-content"></a>Kapcsolódó tartalom
* [Azure AD tartományi szolgáltatások – első lépések útmutató](active-directory-ds-getting-started.md)
* [Windows Server virtuális gép csatlakoztatása az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz](active-directory-ds-admin-guide-join-windows-vm.md)
* [Bejelentkezés a Linux rendszerű virtuális gép](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
