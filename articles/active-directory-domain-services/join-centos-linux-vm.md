---
title: Csatlakozás CentOS virtuális géphez az Azure AD tartományi szolgáltatásokhoz | Microsoft dokumentumok
description: Ismerje meg, hogyan konfigurálhat és csatlakozhat egy CentOS Linux-virtuális géphez egy Azure AD tartományi szolgáltatások által felügyelt tartományhoz.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 16100caa-f209-4cb0-86d3-9e218aeb51c6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/23/2020
ms.author: iainfou
ms.openlocfilehash: d08552dcae51c897f2419d94e5e61e857247f09a
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655240"
---
# <a name="join-a-centos-linux-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>Csatlakozás CentOS Linux-virtuális géphez egy Azure AD tartományi szolgáltatások által kezelt tartományhoz

Ha azt szeretné, hogy a felhasználók egyetlen hitelesítő adatok használatával jelentkezzenek be az Azure-beli virtuális gépekre, csatlakozhat a virtuális gépekhez egy Azure Active Directory tartományi szolgáltatások (AD DS) felügyelt tartományhoz. Amikor csatlakozik egy virtuális gépegy Azure AD DS felügyelt tartományhoz, felhasználói fiókok és hitelesítő adatok a tartományból lehet használni a bejelentkezéshez és a kiszolgálók kezeléséhez. Az Azure AD DS felügyelt tartományból származó csoporttagságok is alkalmazva vannak, hogy szabályozhatja a virtuális gép fájljaihoz vagy szolgáltatásaihoz való hozzáférést.

Ez a cikk bemutatja, hogyan csatlakozhat egy CentOS Linux virtuális géphez egy Azure AD DS felügyelt tartományhoz.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag végrehajtásához a következő erőforrásokra és jogosultságokra van szükség:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Az előfizetéshez társított Azure Active Directory-bérlő, amely et egy helyszíni könyvtárral vagy egy csak felhőbeli könyvtárral szinkronizált.
    * Szükség esetén [hozzon létre egy Azure Active Directory-bérlőt,][create-azure-ad-tenant] vagy [társítson egy Azure-előfizetést a fiókjához.][associate-azure-ad-tenant]
* Az Azure Active Directory tartományi szolgáltatások felügyelt tartomány a konfigurált és konfigurált az Azure AD-bérlő.
    * Szükség esetén az első oktatóanyag [létrehoz és konfigurál egy Azure Active Directory tartományi szolgáltatások példányát.][create-azure-ad-ds-instance]
* Az Azure AD DS felügyelt tartomány ának részét tartalmazó felhasználói fiók.

## <a name="create-and-connect-to-a-centos-linux-vm"></a>CentOS Linux virtuális gép létrehozása és csatlakozás

Ha már rendelkezik Egy meglévő CentOS Linux virtuális gép az Azure-ban, csatlakozzon hozzá az SSH használatával, majd folytassa a következő lépéssel [a virtuális gép konfigurálásának megkezdéséhez.](#configure-the-hosts-file)

Ha létre kell hoznia egy CentOS Linux virtuális gép, vagy szeretne létrehozni egy teszt virtuális gép használható ezzel a cikkel, használhatja az alábbi módszerek:

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
* *A centos* a felügyelt tartományhoz csatlakozó CentOS virtuális gép állomásneve.

Frissítse ezeket a neveket a saját értékeivel:

```console
127.0.0.1 centos.aaddscontoso.com centos
```

Ha elkészült, mentse *hosts* és lépjen `:wq` ki a hosts fájlból a szerkesztő parancsával.

## <a name="install-required-packages"></a>Szükséges csomagok telepítése

A virtuális gépnek további csomagokra van szüksége, hogy csatlakozzon a virtuális géphez az Azure AD DS felügyelt tartományhoz. A csomagok telepítéséhez és konfigurálásához frissítse és telepítse `yum`a tartománycsatlakozási eszközöket a következő használatával:

```console
sudo yum install realmd sssd krb5-workstation krb5-libs oddjob oddjob-mkhomedir samba-common-tools
```

## <a name="join-vm-to-the-managed-domain"></a>Virtuális gép csatlakoztatása a felügyelt tartományhoz

Most, hogy a szükséges csomagok telepítve vannak a virtuális gépre, csatlakozzon a virtuális géphez az Azure AD DS felügyelt tartományhoz.

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

Ha a virtuális gép nem tudja sikeresen befejezni a tartomány-csatlakozási folyamatot, győződjön meg arról, hogy a virtuális gép hálózati biztonsági csoportja engedélyezi a kimenő Kerberos-forgalmat a TCP + UDP 464-es porton az Azure AD DS felügyelt tartomány virtuális hálózati alhálózatához.

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

1. A módosítások alkalmazásához és a felhasználók jelszóval történő bejelentkezésének lehetővé teszi, indítsa újra az SSH szolgáltatást:

    ```console
    sudo systemctl restart sshd
    ```

## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Az "AAD DC-rendszergazdák" csoport sudo jogosultságának megadása

Ha az *AAD DC rendszergazdák* csoport tagjainak rendszergazdai jogosultságokat szeretne biztosítani a CentOS virtuális gépen, hozzá kell adnia egy bejegyzést az */etc/sudoers kapcsolóhoz.* A hozzátatott érték után az *AAD DC rendszergazdák* csoport tagjai használhatják a `sudo` parancsot a CentOS virtuális gépen.

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

1. Hozzon létre egy új SSH-kapcsolatot a konzolról. Használjon olyan tartományi fiókot, amely `ssh -l` a felügyelt `contosoadmin@aaddscontoso.com` tartományhoz tartozik a paranccsal, például adja meg a virtuális gép címét, például *centos.aaddscontoso.com.* Ha az Azure Cloud Shell, használja a nyilvános IP-címét a virtuális gép, nem pedig a belső DNS-név.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com centos.aaddscontoso.com
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
