---
title: Csatlakozás RHEL virtuális géphez az Azure AD tartományi szolgáltatásokhoz | Microsoft dokumentumok
description: Ismerje meg, hogyan konfigurálhat és csatlakozhat egy Red Hat Enterprise Linux virtuális géphez egy Azure AD tartományi szolgáltatások által felügyelt tartományhoz.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 16100caa-f209-4cb0-86d3-9e218aeb51c6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: iainfou
ms.openlocfilehash: d12dd0c79f2e9c1d2b0cc17956a0bb8d8fa35865
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299142"
---
# <a name="join-a-red-hat-enterprise-linux-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>Red Hat Enterprise Linux virtuális gépek csatlakoztatása az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz

Ha azt szeretné, hogy a felhasználók egyetlen hitelesítő adatok használatával jelentkezzenek be az Azure-beli virtuális gépekre, csatlakozhat a virtuális gépekhez egy Azure Active Directory tartományi szolgáltatások (AD DS) felügyelt tartományhoz. Amikor csatlakozik egy virtuális gépegy Azure AD DS felügyelt tartományhoz, felhasználói fiókok és hitelesítő adatok a tartományból lehet használni a bejelentkezéshez és a kiszolgálók kezeléséhez. Az Azure AD DS felügyelt tartományból származó csoporttagságok is alkalmazva vannak, hogy szabályozhatja a virtuális gép fájljaihoz vagy szolgáltatásaihoz való hozzáférést.

Ez a cikk bemutatja, hogyan csatlakozhat egy Red Hat Enterprise Linux (RHEL) virtuális géphez egy Azure AD DS felügyelt tartományhoz.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag végrehajtásához a következő erőforrásokra és jogosultságokra van szükség:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Az előfizetéshez társított Azure Active Directory-bérlő, amely et egy helyszíni könyvtárral vagy egy csak felhőbeli könyvtárral szinkronizált.
    * Szükség esetén [hozzon létre egy Azure Active Directory-bérlőt,][create-azure-ad-tenant] vagy [társítson egy Azure-előfizetést a fiókjához.][associate-azure-ad-tenant]
* Az Azure Active Directory tartományi szolgáltatások felügyelt tartomány a konfigurált és konfigurált az Azure AD-bérlő.
    * Szükség esetén az első oktatóanyag [létrehoz és konfigurál egy Azure Active Directory tartományi szolgáltatások példányát.][create-azure-ad-ds-instance]
* Egy felhasználói fiók, amely az Azure AD DS felügyelt tartomány ának része.

## <a name="create-and-connect-to-a-rhel-linux-vm"></a>RHEL Linux os virtuális gép létrehozása és csatlakozás

Ha rendelkezik egy meglévő RHEL Linux virtuális gép az Azure-ban, csatlakozzon hozzá az SSH használatával, majd folytassa a következő lépéssel [a virtuális gép konfigurálásának megkezdéséhez.](#configure-the-hosts-file)

Ha rhel Linux virtuális gép létrehozása szükséges, vagy egy teszt virtuális gép létrehozásához a cikkhez, az alábbi módszerek egyikét használhatja:

* [Azure-portál](../virtual-machines/linux/quick-create-portal.md)
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
* *Rhel* az RHEL virtuális gép állomásneve, amelyhez csatlakozik a felügyelt tartományhoz.

Frissítse ezeket a neveket a saját értékeivel:

```console
127.0.0.1 rhel rhel.aaddscontoso.com
```

Ha elkészült, mentse *hosts* és lépjen `:wq` ki a hosts fájlból a szerkesztő parancsával.

## <a name="install-required-packages"></a>Szükséges csomagok telepítése

A virtuális gépnek további csomagokra van szüksége, hogy csatlakozzon a virtuális géphez az Azure AD DS felügyelt tartományhoz. A csomagok telepítéséhez és konfigurálásához frissítse és telepítse `yum`a tartománycsatlakozó eszközöket a használatával. Van néhány különbség az RHEL 7.x és az RHEL 6.x között, ezért használja a megfelelő parancsokat a szisztéri változathoz a cikk többi szakaszában.

**RHEL, 7.**

```console
sudo yum install realmd sssd krb5-workstation krb5-libs oddjob oddjob-mkhomedir samba-common-tools
```

**RHEL, 6.**

```console
sudo yum install adcli sssd authconfig krb5-workstation
```

## <a name="join-vm-to-the-managed-domain"></a>Virtuális gép csatlakoztatása a felügyelt tartományhoz

Most, hogy a szükséges csomagok telepítve vannak a virtuális gépre, csatlakozzon a virtuális géphez az Azure AD DS felügyelt tartományhoz. Ismét kövesse az RHEL-sztista verzió megfelelő lépéseit.

### <a name="rhel-7"></a>RHEL, 7.

1. A `realm discover` parancs segítségével felderítheti az Azure AD DS felügyelt tartomány. A következő példa a *AADDSCONTOSO.COM*birodalmat deríti fel. Adja meg saját Azure AD DS felügyelt tartománynevét a NAGYBETŰS RÉSZBEN:

    ```console
    sudo realm discover AADDSCONTOSO.COM
    ```

   Ha `realm discover` a parancs nem találja az Azure AD DS felügyelt tartományát, tekintse át az alábbi hibaelhárítási lépéseket:

    * Győződjön meg arról, hogy a tartomány elérhető a virtuális gépről. Próbálja `ping aaddscontoso.com` meg, hogy ha a pozitív választ adott vissza.
    * Ellenőrizze, hogy a virtuális gép telepítve van-e az azonos, vagy egy társviszony-létesített, virtuális hálózat, amelyben az Azure AD DS felügyelt tartomány érhető el.
    * Ellenőrizze, hogy a virtuális hálózat DNS-kiszolgálójának beállításai frissültek-e, hogy az Azure AD DS felügyelt tartomány tartományvezérlőire mutassanak.

1. Most inicializálja `kinit` a Kerberos-t a paranccsal. Adja meg az Azure AD DS felügyelt tartományának részét használó felhasználót. Szükség esetén [adjon hozzá egy felhasználói fiókot egy csoporthoz az Azure AD-ben.](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md)

    Ismét az Azure AD DS felügyelt tartománynevet kell megadni a NAGYBETŰS. A következő példában a `contosoadmin@aaddscontoso.com` kerberos inicializálására használt fiók használatos. Adja meg saját felhasználói fiókját, amely az Azure AD DS felügyelt tartományának része:

    ```console
    kinit contosoadmin@AADDSCONTOSO.COM
    ```

1. Végül csatlakozzon a géphez az Azure AD `realm join` DS felügyelt tartománya a parancs használatával. Használja ugyanazt a felhasználói fiókot, amely az előző `kinit` parancsban megadott Azure AD DS felügyelt tartomány része, például: `contosoadmin@AADDSCONTOSO.COM`

    ```console
    sudo realm join --verbose AADDSCONTOSO.COM -U 'contosoadmin@AADDSCONTOSO.COM'
    ```

Néhány percet vesz igénybe, hogy csatlakozzon a virtuális gép az Azure AD DS felügyelt tartományhoz. A következő példa kimeneti azt mutatja, hogy a virtuális gép sikeresen csatlakozott az Azure AD DS felügyelt tartományhoz:

```output
Successfully enrolled machine in realm
```

### <a name="rhel-6"></a>RHEL, 6.

1. A `adcli info` parancs segítségével felderítheti az Azure AD DS felügyelt tartomány. A következő példa felderíti a *ADDDSCONTOSO.COM*birodalmat. Adja meg saját Azure AD DS felügyelt tartománynevét a NAGYBETŰS RÉSZBEN:

    ```console
    sudo adcli info aaddscontoso.com
    ```

   Ha `adcli info` a parancs nem találja az Azure AD DS felügyelt tartományát, tekintse át az alábbi hibaelhárítási lépéseket:

    * Győződjön meg arról, hogy a tartomány elérhető a virtuális gépről. Próbálja `ping aaddscontoso.com` meg, hogy ha a pozitív választ adott vissza.
    * Ellenőrizze, hogy a virtuális gép telepítve van-e az azonos, vagy egy társviszony-létesített, virtuális hálózat, amelyben az Azure AD DS felügyelt tartomány érhető el.
    * Ellenőrizze, hogy a virtuális hálózat DNS-kiszolgálójának beállításai frissültek-e, hogy az Azure AD DS felügyelt tartomány tartományvezérlőire mutassanak.

1. Először is, csatlakozzon a tartományhoz a parancs segítségével, ez a `adcli join` parancs is létrehozza a keytab a számítógép hitelesítéséhez. Használjon olyan felhasználói fiókot, amely az Azure AD DS felügyelt tartományának része.

    ```console
    sudo adcli join aaddscontoso.com -U contosoadmin
    ```

1. Most konfigurálja `/ect/krb5.conf` a `/etc/sssd/sssd.conf` és hozza `aaddscontoso.com` létre a fájlokat az Active Directory tartomány használatára.
   Győződjön `AADDSCONTOSO.COM` meg róla, hogy helyébe a saját domain nevet:

    Nyissa `/ect/krb5.conf` meg a fájlt egy szerkesztővel:

    ```console
    sudo vi /etc/krb5.conf
    ```

    Frissítse `krb5.conf` a fájlt a következő mintának megfelelően:

    ```console
    [logging]
     default = FILE:/var/log/krb5libs.log
     kdc = FILE:/var/log/krb5kdc.log
     admin_server = FILE:/var/log/kadmind.log
    
    [libdefaults]
     default_realm = AADDSCONTOSO.COM
     dns_lookup_realm = true
     dns_lookup_kdc = true
     ticket_lifetime = 24h
     renew_lifetime = 7d
     forwardable = true
    
    [realms]
     AADDSCONTOSO.COM = {
     kdc = AADDSCONTOSO.COM
     admin_server = AADDSCONTOSO.COM
     }
    
    [domain_realm]
     .AADDSCONTOSO.COM = AADDSCONTOSO.COM
     AADDSCONTOSO.COM = AADDSCONTOSO.COM
    ```
    
   A `/etc/sssd/sssd.conf` fájl létrehozása:
    
    ```console
    sudo vi /etc/sssd/sssd.conf
    ```

    Frissítse `sssd.conf` a fájlt a következő mintának megfelelően:

    ```console
    [sssd]
     services = nss, pam, ssh, autofs
     config_file_version = 2
     domains = AADDSCONTOSO.COM
    
    [domain/AADDSCONTOSO.COM]
    
     id_provider = ad
    ```

1. Győződjön `/etc/sssd/sssd.conf` meg arról, hogy az engedélyek 600-asak, és a gyökérfelhasználó tulajdonában vannak:

    ```console
    sudo chmod 600 /etc/sssd/sssd.conf
    sudo chown root:root /etc/sssd/sssd.conf
    ```

1. A `authconfig` virtuális gép oktatására használhatja az AD Linux-integrációt:

    ```console
    sudo authconfig --enablesssd --enablesssdauth --update
    ```

1. Indítsa el és engedélyezze az SSSD szolgáltatást:

    ```console
    sudo service sssd start
    sudo chkconfig sssd on
    ```

Ha a virtuális gép nem tudja sikeresen befejezni a tartomány-csatlakozási folyamatot, győződjön meg arról, hogy a virtuális gép hálózati biztonsági csoportja engedélyezi a kimenő Kerberos-forgalmat a TCP + UDP 464-es porton az Azure AD DS felügyelt tartomány virtuális hálózati alhálózatához.

Most ellenőrizze, hogy le kérdezheti-e a felhasználói AD-adatokat a`getent`

```console
sudo getent passwd contosoadmin
```

## <a name="allow-password-authentication-for-ssh"></a>Jelszó-hitelesítés engedélyezése az SSH-hoz

Alapértelmezés szerint a felhasználók csak SSH nyilvános kulcsalapú hitelesítéssel jelentkezhetnek be egy virtuális gépre. A jelszóalapú hitelesítés sikertelen. Amikor csatlakozik a virtuális gép egy Azure AD DS felügyelt tartományhoz, ezek a tartományi fiókok jelszóalapú hitelesítést kell használnia. Frissítse az SSH-konfigurációt a jelszóalapú hitelesítés engedélyezéséhez az alábbiak szerint.

1. Nyissa *meg* a sshd_conf fájlt egy szerkesztővel:

    ```console
    sudo vi /etc/ssh/sshd_config
    ```

1. Frissítse a *PasswordAuthentication* sorát *igenre:*

    ```console
    PasswordAuthentication yes
    ```

    Ha elkészült, mentse *sshd_conf* és lépjen `:wq` ki a sshd_conf fájlból a szerkesztő parancsával.

1. A módosítások alkalmazásához és a felhasználók jelszóval történő bejelentkezésének lehetővé teszi, indítsa újra az RHEL-szórási verzió SSH-szolgáltatását:

   **RHEL, 7.**

    ```console
    sudo systemctl restart sshd
    ```

   **RHEL, 6.**

    ```console
    sudo service sshd restart
    ```

## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Az "AAD DC-rendszergazdák" csoport sudo jogosultságának megadása

Ha az *AAD tartományvezérlő rendszergazdái csoport tagjainak rendszergazdai* jogosultságokat szeretne adni az RHEL virtuális géphez, hozzá kell adnia egy bejegyzést az */etc/sudoers kapcsolóhoz.* A hozzáadott érték után az *AAD DC* `sudo` rendszergazdák csoport tagjai használhatják a parancsot az RHEL virtuális gépen.

1. Nyissa meg a *sudoers* fájlt szerkesztésre:

    ```console
    sudo visudo
    ```

1. Adja hozzá a következő bejegyzést az */etc/sudoers* fájl végéhez. Az *AAD tartományvezérlő rendszergazdák* csoportja szóközt tartalmaz a névben, ezért a fordított perjel escape karaktert is belekell foglalni a csoport nevébe. Adja hozzá saját tartománynevét, például *aaddscontoso.com:*

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators@aaddscontoso.com ALL=(ALL) NOPASSWD:ALL
    ```

    Ha elkészült, mentse és `:wq` lépjen ki a szerkesztőből a szerkesztő parancsával.

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Bejelentkezés a virtuális gépbe tartományi fiókkal

Annak ellenőrzéséhez, hogy a virtuális gép sikeresen csatlakozott-e az Azure AD DS felügyelt tartományához, indítson el egy új SSH-kapcsolatot egy tartományi felhasználói fiók használatával. Ellenőrizze, hogy létrejött-e kezdőkönyvtár, és hogy a tartomány csoporttagsága érvényesüljön-e.

1. Hozzon létre egy új SSH-kapcsolatot a konzolról. Használjon olyan tartományi fiókot, amely `ssh -l` a felügyelt `contosoadmin@aaddscontoso.com` tartományhoz tartozik a paranccsal, például adja meg a virtuális gép címét, például *rhel.aaddscontoso.com.* Ha az Azure Cloud Shell, használja a nyilvános IP-címét a virtuális gép, nem pedig a belső DNS-név.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com rhel.aaddscontoso.com
    ```

1. Ha sikeresen csatlakozott a virtuális géphez, ellenőrizze, hogy a kezdőkönyvtár megfelelően van-e inicializálva:

    ```console
    pwd
    ```

    A */home* könyvtárban kell lennie a saját könyvtárával, amely megfelel a felhasználói fióknak.

1. Most ellenőrizze, hogy a csoporttagságok feloldása megfelelő-e:

    ```console
    id
    ```

    A csoporttagságok az Azure AD DS felügyelt tartományból kell látnia.

1. Ha az *AAD tartományvezérlő-rendszergazdák* csoport tagjaként jelentkezett be a virtuális gépbe, `sudo` ellenőrizze, hogy helyesen tudja-e használni a parancsot:

    ```console
    sudo yum update
    ```

## <a name="next-steps"></a>További lépések

Ha problémái vannak a virtuális gép nek az Azure AD DS által felügyelt tartományhoz való csatlakoztatásával vagy tartományi fiókkal való bejelentkezéssel, olvassa el [a Tartománycsatlakozási problémák elhárítása című témakört.](join-windows-vm.md#troubleshoot-domain-join-issues)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
