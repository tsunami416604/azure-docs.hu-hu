---
title: 'Az Azure Active Directory tartományi szolgáltatások: CoreOS Linux virtuális gép csatlakoztatása felügyelt tartományokhoz |} A Microsoft Docs'
description: CoreOS Linux rendszerű virtuális gép csatlakoztatása az Azure AD tartományi szolgáltatások
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 5db65f30-bf69-4ea3-9ea5-add1db83fdb8
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: ergreenl
ms.openlocfilehash: a2b8c3c0c38349b4c68f85a32bddb2c9b3e60c6f
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54850684"
---
# <a name="join-a-coreos-linux-virtual-machine-to-a-managed-domain"></a>CoreOS Linux rendszerű virtuális gép csatlakoztatása felügyelt tartományokhoz
Ez a cikk bemutatja, hogyan az Azure-beli CoreOS Linux rendszerű virtuális gép csatlakoztatása az Azure AD tartományi szolgáltatásokkal felügyelt tartományban.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Előkészületek
A cikkben szereplő feladatok elvégzéséhez szüksége:
1. Egy érvényes **Azure-előfizetés**.
2. Egy **Azure AD-címtár** -vagy az egy helyszíni címtár vagy egy csak felhőalapú címtárral szinkronizálja.
3. **Az Azure AD Domain Services** engedélyezve kell lennie az Azure AD-címtárban. Ha még nem tette, minden ismertetett feladatok végrehajtásával a [a kezdeti lépések útmutatóban](active-directory-ds-getting-started.md).
4. Győződjön meg arról, hogy már konfigurálta az IP-címek a felügyelt tartomány a virtuális hálózat DNS-kiszolgálóként. További információkért lásd: [az Azure virtuális hálózat DNS-beállításainak frissítése](active-directory-ds-getting-started-dns.md)
5. Végezze el a szükséges lépéseket [szinkronizálja a jelszavakat az Azure AD tartományi szolgáltatásokkal felügyelt tartományban](active-directory-ds-getting-started-password-sync.md).


## <a name="provision-a-coreos-linux-virtual-machine"></a>CoreOS Linux virtuális gép kiépítése
Az Azure-ban, az alábbi módszerek bármelyikével CoreOS rendszerű virtuális gép kiépítése:
* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

Ez a cikk a **CoreOS Linux (Stable)** virtuálisgép-lemezképet az Azure-ban.

> [!IMPORTANT]
> * Telepítse a virtuális gépet, azokat a **azonos virtuális hálózatban, amelyiken engedélyezte az Azure AD Domain Services**.
> * Válasszon ki egy **másik alhálózatot** fut, amelyiken engedélyezte az Azure AD tartományi szolgáltatásokat.
>


## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>Távoli csatlakozás az újonnan létrehozott Linux virtuális gép
A CoreOS virtuális gépet az Azure-ban van kiépítve. A következő feladata távolról csatlakozni a virtuális gépet a virtuális gép kiépítése során létrehozott helyi rendszergazdai fiókkal.

Kövesse a cikk a [bejelentkezés egy Linux rendszerű virtuális gép](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>A Linux rendszerű virtuális gép hosts fájljának konfigurálása
A terminálban SSH a Hosts fájl szerkesztése, és frissítse a gép IP-cím és az állomásnevet.

```
sudo vi /etc/hosts
```

Adja meg a hosts fájl a következő értéket:

```
127.0.0.1 contoso-coreos.contoso100.com contoso-coreos
```
"Contoso100.com" Íme a felügyelt tartomány DNS-tartomány nevét. a "contoso – coreos", amelyhez a felügyelt tartományhoz csatlakozik, a CoreOS virtuális gép állomásnevét.


## <a name="configure-the-sssd-service-on-the-linux-virtual-machine"></a>A SSSD szolgáltatás konfigurálása a Linux rendszerű virtuális gépen
Ezután frissítse az SSSD konfigurációs fájlhoz ('/ etc/sssd/sssd.conf ") a következő mintának megfelelően:

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
Cserélje le ' CONTOSO100. COM "a felügyelt tartomány DNS-tartománynévvel. Ellenőrizze, hogy a conf fájlban befektetési esetben adja meg a tartomány nevét.


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>A Linux rendszerű virtuális gép csatlakoztatása a felügyelt tartományhoz
Most, hogy a szükséges csomagok telepítve vannak a Linux rendszerű virtuális gép, a következő feladata a virtuális gép csatlakoztatása a felügyelt tartományhoz.

```
sudo adcli join -D CONTOSO100.COM -U bob@CONTOSO100.COM -K /etc/krb5.keytab -H contoso-coreos.contoso100.com -N coreos
```


> [!NOTE]
> **Hibaelhárítás:** Ha *adcli* nem találja a felügyelt tartomány:
  * Győződjön meg arról, hogy a tartomány érhető el a virtuális gépről (ping. Próbálja meg).
  * Ellenőrizze, hogy a virtuális gép valóban lett telepítve, az azonos virtuális hálózatban, amely a felügyelt tartomány érhető el.
  * Ellenőrizze, hogy ha frissítette a DNS-kiszolgáló beállításainak a virtuális hálózathoz, hogy a felügyelt tartomány tartományvezérlőit mutasson.
>

Indítsa el a SSSD szolgáltatást. Az SSH terminálon írja be a következő parancsot:
  ```
  sudo systemctl start sssd.service
  ```


## <a name="verify-domain-join"></a>A tartományhoz való csatlakozás ellenőrzéséhez
Győződjön meg arról, hogy a gép sikeresen csatlakoztatva lett a felügyelt tartományhoz. Csatlakozhat a tartományhoz csatlakoztatott CoreOS virtuális Gépet egy másik SSH-kapcsolat használatával. Egy tartományi fiókot használjon, és ezután ellenőrizze, hogy ha a felhasználói fióknak megfelelően megoldódott-e.

1. A Terminálszolgáltatások az SSH-típus a következő parancsot a csatlakozás tartományhoz csatlakoztatott CoreOS rendszerű virtuális gép SSH-val. A felügyelt tartományhoz tartozó tartományi fiók használata (például "bob@CONTOSO100.COM" Ebben az esetben.)
    ```
    ssh -l bob@CONTOSO100.COM contoso-coreos.contoso100.com
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
