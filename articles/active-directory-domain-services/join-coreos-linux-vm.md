---
title: Csatlakozás CoreOS virtuális géphez az Azure AD tartományi szolgáltatásokhoz | Microsoft dokumentumok
description: Megtudhatja, hogyan konfigurálhat és csatlakozhat egy CoreOS virtuális géphez egy Azure AD tartományi szolgáltatások által felügyelt tartományhoz.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 5db65f30-bf69-4ea3-9ea5-add1db83fdb8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/23/2020
ms.author: iainfou
ms.openlocfilehash: 63dfe39b986125abc9cacf6c1a6556876bbd3a99
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655192"
---
# <a name="join-a-coreos-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>Csatlakozás CoreOS virtuális géphez egy Azure AD tartományi szolgáltatások által kezelt tartományhoz

Ha azt szeretné, hogy a felhasználók egyetlen hitelesítő adatok használatával jelentkezzenek be az Azure-beli virtuális gépekre, csatlakozhat a virtuális gépekhez egy Azure Active Directory tartományi szolgáltatások (AD DS) felügyelt tartományhoz. Amikor csatlakozik egy virtuális gépegy Azure AD DS felügyelt tartományhoz, felhasználói fiókok és hitelesítő adatok a tartományból lehet használni a bejelentkezéshez és a kiszolgálók kezeléséhez. Az Azure AD DS felügyelt tartományból származó csoporttagságok is alkalmazva vannak, hogy szabályozhatja a virtuális gép fájljaihoz vagy szolgáltatásaihoz való hozzáférést.

Ez a cikk bemutatja, hogyan csatlakozhat egy CoreOS virtuális géphez egy Azure AD DS felügyelt tartományhoz.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag végrehajtásához a következő erőforrásokra és jogosultságokra van szükség:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Az előfizetéshez társított Azure Active Directory-bérlő, amely et egy helyszíni könyvtárral vagy egy csak felhőbeli könyvtárral szinkronizált.
    * Szükség esetén [hozzon létre egy Azure Active Directory-bérlőt,][create-azure-ad-tenant] vagy [társítson egy Azure-előfizetést a fiókjához.][associate-azure-ad-tenant]
* Az Azure Active Directory tartományi szolgáltatások felügyelt tartomány a konfigurált és konfigurált az Azure AD-bérlő.
    * Szükség esetén az első oktatóanyag [létrehoz és konfigurál egy Azure Active Directory tartományi szolgáltatások példányát.][create-azure-ad-ds-instance]
* Egy felhasználói fiók, amely az Azure AD DS felügyelt tartomány ának része.

## <a name="create-and-connect-to-a-coreos-linux-vm"></a>CoreOS Linux virtuális gép létrehozása és csatlakozás

Ha rendelkezik egy meglévő CoreOS Linux virtuális géppel az Azure-ban, csatlakozzon hozzá az SSH használatával, majd folytassa a következő lépéssel [a virtuális gép konfigurálásának megkezdéséhez.](#configure-the-hosts-file)

Ha létre kell hoznia egy CoreOS Linux virtuális gép, vagy szeretne létrehozni egy teszt virtuális gép használható ezzel a cikkel, az alábbi módszerek egyikét használhatja:

* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

A virtuális gép létrehozásakor figyeljen a virtuális hálózati beállításokra, és győződjön meg arról, hogy a virtuális gép képes kommunikálni az Azure AD DS felügyelt tartományával:

* Telepítse a virtuális gépet ugyanabba a virtuális hálózatba, vagy egy társviszonyt létesített virtuális hálózatba, amelyben engedélyezte az Azure AD tartományi szolgáltatásokat.
* Telepítse a virtuális gép egy másik alhálózatba, mint az Azure AD tartományi szolgáltatások példányát.

A virtuális gép üzembe helyezése után kövesse a lépéseket a virtuális géphez az SSH használatával való csatlakozáshoz.

## <a name="configure-the-hosts-file"></a>A hosts fájl konfigurálása

Annak érdekében, hogy a virtuális gép állomásneve megfelelően legyen konfigurálva a felügyelt tartományhoz, szerkesztse az */etc/hosts* fájlt, és állítsa be az állomásnevet:

```console
sudo vi /etc/hosts
```

A *hosts* fájlban frissítse a *localhost* címet. A következő példában:

* *aaddscontoso.com* az Azure AD DS felügyelt tartományának DNS-tartományneve.
* *A coreos* a CoreOS virtuális gép állomásneve, amelyhez csatlakozik a felügyelt tartományhoz.

Frissítse ezeket a neveket a saját értékeivel:

```console
127.0.0.1 coreos coreos.aaddscontoso.com
```

Ha elkészült, mentse *hosts* és lépjen `:wq` ki a hosts fájlból a szerkesztő parancsával.

## <a name="configure-the-sssd-service"></a>Az SSSD szolgáltatás konfigurálása

Frissítse az */etc/sssd/sssd.conf* SSSD konfigurációt.

```console
sudo vi /etc/sssd/sssd.conf
```

Adja meg saját Azure AD DS felügyelt tartománynevét a következő paraméterekhez:

* *tartományok* nagybetűs tartományban
* *[domain/AADDS],* ahol az AADDS nagybetűs
* *ldap_uri*
* *ldap_search_base*
* *krb5_server*
* *krb5_realm* nagybetűs

```console
[sssd]
config_file_version = 2
services = nss, pam
domains = AADDSCONTOSO.COM

[domain/AADDSCONTOSO.COM]
id_provider = ad
auth_provider = ad
chpass_provider = ad

ldap_uri = ldap://aaddscontoso.com
ldap_search_base = dc=aaddscontoso,dc=com
ldap_schema = rfc2307bis
ldap_sasl_mech = GSSAPI
ldap_user_object_class = user
ldap_group_object_class = group
ldap_user_home_directory = unixHomeDirectory
ldap_user_principal = userPrincipalName
ldap_account_expire_policy = ad
ldap_force_upper_case_realm = true
fallback_homedir = /home/%d/%u

krb5_server = aaddscontoso.com
krb5_realm = AADDSCONTOSO.COM
```

## <a name="join-the-vm-to-the-managed-domain"></a>Csatlakozzon a virtuális géphez a felügyelt tartományhoz

Az SSSD konfigurációs fájl jának frissítésével most csatlakozzon a virtuális géphez a felügyelt tartományhoz.

1. Először is `adcli info` a paranccsal ellenőrizze, hogy az Azure AD DS felügyelt tartományával kapcsolatos információkat láthatja.First, use the command to verify you can see information about the Azure AD DS managed domain. A következő példa a tartomány AADDSCONTOSO.COM adatait kapja *meg.* Adja meg saját Azure AD DS felügyelt tartománynevét a NAGYBETŰS RÉSZBEN:

    ```console
    sudo adcli info AADDSCONTOSO.COM
    ```

   Ha `adcli info` a parancs nem találja az Azure AD DS felügyelt tartományát, tekintse át az alábbi hibaelhárítási lépéseket:

    * Győződjön meg arról, hogy a tartomány elérhető a virtuális gépről. Próbálja `ping aaddscontoso.com` meg, hogy ha a pozitív választ adott vissza.
    * Ellenőrizze, hogy a virtuális gép telepítve van-e az azonos, vagy egy társviszony-létesített, virtuális hálózat, amelyben az Azure AD DS felügyelt tartomány érhető el.
    * Ellenőrizze, hogy a virtuális hálózat DNS-kiszolgálójának beállításai frissültek-e, hogy az Azure AD DS felügyelt tartomány tartományvezérlőire mutassanak.

1. Most csatlakozzon a virtuális gép az Azure AD DS felügyelt tartománya a `adcli join` parancs használatával. Adja meg az Azure AD DS felügyelt tartományának részét használó felhasználót. Szükség esetén [adjon hozzá egy felhasználói fiókot egy csoporthoz az Azure AD-ben.](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md)

    Ismét az Azure AD DS felügyelt tartománynevet kell megadni a NAGYBETŰS. A következő példában a `contosoadmin@aaddscontoso.com` kerberos inicializálására használt fiók használatos. Adja meg saját felhasználói fiókját, amely az Azure AD DS felügyelt tartományának része.

    ```console
    sudo adcli join -D AADDSCONTOSO.COM -U contosoadmin@AADDSCONTOSO.COM -K /etc/krb5.keytab -H coreos.aaddscontoso.com -N coreos
    ```

    A `adcli join` parancs nem ad vissza semmilyen információt, ha a virtuális gép sikeresen csatlakozott az Azure AD DS felügyelt tartományhoz.

1. A tartomány-illesztési konfiguráció alkalmazásához indítsa el az SSSD szolgáltatást:
  
    ```console
    sudo systemctl start sssd.service
    ```

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Bejelentkezés a virtuális gépbe tartományi fiókkal

Annak ellenőrzéséhez, hogy a virtuális gép sikeresen csatlakozott-e az Azure AD DS felügyelt tartományához, indítson el egy új SSH-kapcsolatot egy tartományi felhasználói fiók használatával. Ellenőrizze, hogy létrejött-e kezdőkönyvtár, és hogy a tartomány csoporttagsága érvényesüljön-e.

1. Hozzon létre egy új SSH-kapcsolatot a konzolról. Használjon olyan tartományi fiókot, amely `ssh -l` a felügyelt `contosoadmin@aaddscontoso.com` tartományhoz tartozik a paranccsal, például adja meg a virtuális gép címét, például *coreos.aaddscontoso.com.* Ha az Azure Cloud Shell, használja a nyilvános IP-címét a virtuális gép, nem pedig a belső DNS-név.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com coreos.aaddscontoso.com
    ```

1. Most ellenőrizze, hogy a csoporttagságok feloldása megfelelő-e:

    ```console
    id
    ```

    A csoporttagságok az Azure AD DS felügyelt tartományból kell látnia.

## <a name="next-steps"></a>További lépések

Ha problémái vannak a virtuális gép nek az Azure AD DS által felügyelt tartományhoz való csatlakoztatásával vagy tartományi fiókkal való bejelentkezéssel, olvassa el [a Tartománycsatlakozási problémák elhárítása című témakört.](join-windows-vm.md#troubleshoot-domain-join-issues)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
