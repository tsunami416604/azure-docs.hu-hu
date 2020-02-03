---
title: CoreOS virtuális gép csatlakoztatása Azure AD Domain Serviceshoz | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhat és csatlakoztathat egy CoreOS virtuális gépet egy Azure AD Domain Services felügyelt tartományhoz.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 5db65f30-bf69-4ea3-9ea5-add1db83fdb8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: iainfou
ms.openlocfilehash: ddf6c9238cabedfbdeeb8056864072edc543c342
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712611"
---
# <a name="join-a-coreos-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>CoreOS virtuális gép csatlakoztatása Azure AD Domain Services felügyelt tartományhoz

Annak érdekében, hogy a felhasználók egyetlen hitelesítő adat használatával jelentkezzenek be a virtuális gépekre az Azure-ban, csatlakoztathatja a virtuális gépeket egy Azure Active Directory Domain Services (AD DS) felügyelt tartományhoz. Amikor egy virtuális gépet csatlakoztat egy Azure AD DS felügyelt tartományhoz, a tartomány felhasználói fiókjai és hitelesítő adatai használhatók a kiszolgálók bejelentkezéséhez és kezeléséhez. Az Azure AD DS felügyelt tartományból származó csoporttagságok a virtuális gépen található fájlokhoz vagy szolgáltatásokhoz való hozzáférés szabályozására is vonatkoznak.

Ez a cikk bemutatja, hogyan csatlakozhat egy CoreOS virtuális géphez egy Azure AD DS felügyelt tartományhoz.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következő erőforrásokra és jogosultságokra van szüksége:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Az előfizetéshez társított Azure Active Directory bérlő, vagy egy helyszíni címtárral vagy egy csak felhőalapú címtárral van szinkronizálva.
    * Ha szükséges, [hozzon létre egy Azure Active Directory bérlőt][create-azure-ad-tenant] , vagy [rendeljen hozzá egy Azure-előfizetést a fiókjához][associate-azure-ad-tenant].
* Egy Azure Active Directory Domain Services felügyelt tartomány engedélyezve és konfigurálva van az Azure AD-bérlőben.
    * Ha szükséges, az első oktatóanyag [egy Azure Active Directory Domain Services példányt hoz létre és konfigurál][create-azure-ad-ds-instance].
* Egy felhasználói fiók, amely tagja az Azure ad *DC-rendszergazdák* csoportnak az Azure ad-bérlőben.

## <a name="create-and-connect-to-a-coreos-linux-vm"></a>CoreOS Linux rendszerű virtuális gép létrehozása és kapcsolódás

Ha rendelkezik egy meglévő CoreOS Linux virtuális géppel az Azure-ban, csatlakozzon az SSH-val, majd folytassa a következő lépéssel a [virtuális gép konfigurálásának megkezdéséhez](#configure-the-hosts-file).

Ha létre kell hoznia egy CoreOS Linux rendszerű virtuális gépet, vagy létre szeretne hozni egy tesztelési virtuális gépet, amely a jelen cikkben használható, a következő módszerek egyikét használhatja:

* [Azure Portalra](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

A virtuális gép létrehozásakor ügyeljen arra, hogy a virtuális gép képes legyen kommunikálni az Azure AD DS felügyelt tartománnyal:

* Telepítse a virtuális gépet ugyanabba vagy egy olyan virtuális hálózatba, amelyben engedélyezte a Azure AD Domain Services.
* Telepítse a virtuális gépet egy másik alhálózatra, mint a Azure AD Domain Services példánya.

A virtuális gép üzembe helyezését követően hajtsa végre a lépéseket, hogy SSH-kapcsolaton keresztül csatlakozzon a virtuális géphez.

## <a name="configure-the-hosts-file"></a>A Hosts fájl konfigurálása

Győződjön meg arról, hogy a virtuális gép állomásneve helyesen van konfigurálva a felügyelt tartományhoz, szerkessze a */etc/hosts* fájlt, és állítsa be a hostname:

```console
sudo vi /etc/hosts
```

A *gazdagépek* fájlban frissítse a *localhost* -címeket. A következő példában:

* a *aadds.contoso.com* az Azure AD DS felügyelt tartományának DNS-tartományneve.
* a *CoreOS* a felügyelt tartományhoz csatlakozó CoreOS-alapú virtuális gép állomásneve.

Frissítse ezeket a neveket a saját értékeivel:

```console
127.0.0.1 coreos coreos.aadds.contoso.com
```

Ha elkészült, mentse és zárja be a *hosts* fájlt a szerkesztő `:wq` parancsának használatával.

## <a name="configure-the-sssd-service"></a>A SSSD szolgáltatás konfigurálása

Frissítse a */etc/sssd/sssd.conf* -sssd konfigurációját.

```console
sudo vi /etc/sssd/sssd.conf
```

Adja meg saját Azure AD DS felügyelt tartománynevét a következő paraméterekhez:

* *tartományok* az összes nagybetű
* *[domain/AADDS]* , ahol a AADDS minden nagybetűs
* *ldap_uri*
* *ldap_search_base*
* *krb5_server*
* *krb5_realm* az összes nagybetű

```console
[sssd]
config_file_version = 2
services = nss, pam
domains = AADDS.CONTOSO.COM

[domain/AADDS.CONTOSO.COM]
id_provider = ad
auth_provider = ad
chpass_provider = ad

ldap_uri = ldap://aadds.contoso.com
ldap_search_base = dc=aadds.contoso,dc=com
ldap_schema = rfc2307bis
ldap_sasl_mech = GSSAPI
ldap_user_object_class = user
ldap_group_object_class = group
ldap_user_home_directory = unixHomeDirectory
ldap_user_principal = userPrincipalName
ldap_account_expire_policy = ad
ldap_force_upper_case_realm = true
fallback_homedir = /home/%d/%u

krb5_server = aadds.contoso.com
krb5_realm = AADDS.CONTOSO.COM
```

## <a name="join-the-vm-to-the-managed-domain"></a>A virtuális gép csatlakoztatása a felügyelt tartományhoz

A SSSD konfigurációs fájljának frissítése után most csatlakoztassa a virtuális gépet a felügyelt tartományhoz.

1. Először a `adcli info` parancs használatával ellenőrizze, hogy látható-e az Azure AD DS felügyelt tartományával kapcsolatos információ. A következő példában a AADDS tartományra vonatkozó információk olvashatók be *. CONTOSO.COM*. Adja meg saját Azure AD DS felügyelt tartománynevét az összes nagybetűvel:

    ```console
    sudo adcli info AADDS.CONTOSO.COM
    ```

   Ha az `adcli info` parancs nem találja az Azure AD DS felügyelt tartományát, tekintse át a következő hibaelhárítási lépéseket:

    * Győződjön meg arról, hogy a tartomány elérhető a virtuális gépről. Próbálja meg `ping aadds.contoso.com` a pozitív válasz visszaadása.
    * Győződjön meg arról, hogy a virtuális gép üzembe helyezése ugyanarra a virtuális gépre történik, ahol az Azure AD DS felügyelt tartomány elérhető.
    * Győződjön meg arról, hogy a virtuális hálózat DNS-kiszolgálójának beállításai frissítve lettek, hogy az Azure AD DS felügyelt tartományának tartományvezérlőjére mutasson.

1. Most csatlakoztassa a virtuális gépet az Azure AD DS felügyelt tartományhoz a `adcli join` paranccsal. Olyan felhasználót válasszon, amely a *HRE DC-rendszergazdák* csoportjához tartozik. Ha szükséges, [vegyen fel egy felhasználói fiókot egy csoportba az Azure ad-ben](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Ismét az Azure AD DS felügyelt tartománynevet minden nagybetűvel meg kell adni. A következő példában az `contosoadmin@aadds.contoso.com` nevű fiók a Kerberos inicializálására szolgál. Adja meg saját felhasználói fiókját, amely az *HRE DC-rendszergazdák* csoport tagja.

    ```console
    sudo adcli join -D AADDS.CONTOSO.COM -U contosoadmin@AADDS.CONTOSO.COM -K /etc/krb5.keytab -H coreos.aadds.contoso.com -N coreos
    ```

    A `adcli join` parancs nem ad vissza semmilyen információt, ha a virtuális gép sikeresen csatlakozott az Azure AD DS felügyelt tartományhoz.

1. A tartományhoz való csatlakozás konfigurációjának alkalmazásához indítsa el a SSSD szolgáltatást:
  
    ```console
    sudo systemctl start sssd.service
    ```

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Bejelentkezés a virtuális gépre tartományi fiók használatával

Annak ellenőrzéséhez, hogy a virtuális gép sikeresen csatlakozott-e az Azure AD DS felügyelt tartományhoz, indítson el egy új SSH-kapcsolódást egy tartományi felhasználói fiók használatával. Győződjön meg arról, hogy a kezdőkönyvtár létrejött, és a rendszer a tartományból származó csoporttagság alkalmazását alkalmazza.

1. Hozzon létre egy új SSH-kapcsolatokat a konzolon. Használjon olyan tartományi fiókot, amely a felügyelt tartományhoz tartozik a `ssh -l` parancs használatával, például `contosoadmin@aadds.contoso.com`, majd adja meg a virtuális gép (például *CoreOS.aadds.contoso.com*) címeit. Ha a Azure Cloud Shell használja, a belső DNS-név helyett használja a virtuális gép nyilvános IP-címét.

    ```console
    ssh -l contosoadmin@AADDS.CONTOSO.com coreos.aadds.contoso.com
    ```

1. Most győződjön meg arról, hogy a csoporttagságok megfelelően vannak feloldva:

    ```console
    id
    ```

    Az Azure AD DS felügyelt tartományból kell megjelennie a csoporttagságok.

## <a name="next-steps"></a>Következő lépések

Ha problémái adódnak a virtuális gép Azure AD DS felügyelt tartományhoz való csatlakoztatásával vagy egy tartományi fiókkal való bejelentkezéssel kapcsolatban, olvassa el a [tartományhoz való csatlakozással kapcsolatos problémák elhárítása](join-windows-vm.md#troubleshoot-domain-join-issues)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
