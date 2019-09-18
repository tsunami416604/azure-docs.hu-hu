---
title: Csatlakozás CentOS virtuális géphez a Azure AD Domain Serviceshoz | Microsoft Docs "
description: Megtudhatja, hogyan konfigurálhat és csatlakozhat egy CentOS Linux rendszerű virtuális géphez egy Azure AD Domain Services felügyelt tartományhoz.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 16100caa-f209-4cb0-86d3-9e218aeb51c6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/15/2019
ms.author: iainfou
ms.openlocfilehash: 965714fc4b02430653d3670ddec69fcd77cc7920
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71075638"
---
# <a name="join-a-centos-linux-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>CentOS Linux rendszerű virtuális gép csatlakoztatása Azure AD Domain Services felügyelt tartományhoz

Annak érdekében, hogy a felhasználók egyetlen hitelesítő adat használatával jelentkezzenek be a virtuális gépekre az Azure-ban, csatlakoztathatja a virtuális gépeket egy Azure Active Directory Domain Services (AD DS) felügyelt tartományhoz. Amikor egy virtuális gépet csatlakoztat egy Azure AD DS felügyelt tartományhoz, a tartomány felhasználói fiókjai és hitelesítő adatai használhatók a kiszolgálók bejelentkezéséhez és kezeléséhez. Az Azure AD DS felügyelt tartományból származó csoporttagságok a virtuális gépen található fájlokhoz vagy szolgáltatásokhoz való hozzáférés szabályozására is vonatkoznak.

Ez a cikk bemutatja, hogyan csatlakozhat egy CentOS Linux rendszerű virtuális géphez egy Azure AD DS felügyelt tartományhoz.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következő erőforrásokra és jogosultságokra van szüksége:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Az előfizetéshez társított Azure Active Directory bérlő, vagy egy helyszíni címtárral vagy egy csak felhőalapú címtárral van szinkronizálva.
    * Ha szükséges, [hozzon létre egy Azure Active Directory bérlőt][create-azure-ad-tenant] , vagy [rendeljen hozzá egy Azure-előfizetést a fiókjához][associate-azure-ad-tenant].
* Egy Azure Active Directory Domain Services felügyelt tartomány engedélyezve és konfigurálva van az Azure AD-bérlőben.
    * Ha szükséges, az első oktatóanyag [egy Azure Active Directory Domain Services példányt hoz létre és konfigurál][create-azure-ad-ds-instance].
* Egy felhasználói fiók, amely tagja az Azure ad *DC-rendszergazdák* csoportnak az Azure ad-bérlőben.

## <a name="create-and-connect-to-a-centos-linux-vm"></a>CentOS Linux rendszerű virtuális gép létrehozása és kapcsolódás

Ha már rendelkezik egy meglévő CentOS linuxos virtuális géppel az Azure-ban, csatlakozzon az SSH-val, majd folytassa a következő lépéssel a [virtuális gép konfigurálásának megkezdéséhez](#configure-the-hosts-file).

Ha létre kell hoznia egy CentOS linuxos virtuális gépet, vagy létre szeretne hozni egy tesztelési virtuális gépet, amely a jelen cikkben használható, a következő módszerek egyikét használhatja:

* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
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

* a *contoso.com* az Azure AD DS felügyelt tartományának DNS-tartományneve.
* a *CentOS* a felügyelt tartományhoz csatlakozó CentOS virtuális gép állomásneve.

Frissítse ezeket a neveket a saját értékeivel:

```console
127.0.0.1 centos centos.contoso.com
```

Ha elkészült, mentse és zárja be a *hosts* fájlt `:wq` a szerkesztő parancs használatával.

## <a name="install-required-packages"></a>Szükséges csomagok telepítése

A virtuális gépnek szüksége van néhány további csomagra a virtuális gép Azure AD DS felügyelt tartományhoz való csatlakoztatásához. A csomagok telepítéséhez és konfigurálásához frissítse és telepítse a tartományhoz csatlakozó eszközöket a következő `yum`használatával:

```console
sudo yum install realmd sssd krb5-workstation krb5-libs oddjob oddjob-mkhomedir samba-common-tools
```

## <a name="join-vm-to-the-managed-domain"></a>Virtuális gép csatlakoztatása a felügyelt tartományhoz

Most, hogy a szükséges csomagok telepítve vannak a virtuális gépen, csatlakoztassa a virtuális gépet az Azure AD DS felügyelt tartományhoz.

1. Használja az `realm discover` parancsot az Azure AD DS felügyelt tartomány felderítéséhez. A következő példa felfedi a *contoso.com*tartományát. Adja meg saját Azure AD DS felügyelt tartománynevét az összes nagybetűvel:

    ```console
    sudo realm discover CONTOSO.COM
    ```

   Ha a `realm discover` parancs nem találja az Azure AD DS felügyelt tartományát, tekintse át a következő hibaelhárítási lépéseket:

    * Győződjön meg arról, hogy a tartomány elérhető a virtuális gépről. Próbálja `ping contoso.com` meg megtekinteni, hogy a rendszer pozitív választ ad-e vissza.
    * Győződjön meg arról, hogy a virtuális gép üzembe helyezése ugyanarra a virtuális gépre történik, ahol az Azure AD DS felügyelt tartomány elérhető.
    * Győződjön meg arról, hogy a virtuális hálózat DNS-kiszolgálójának beállításai frissítve lettek, hogy az Azure AD DS felügyelt tartományának tartományvezérlőjére mutasson.

1. Most inicializálja a Kerberost `kinit` a parancs használatával. Olyan felhasználót válasszon, amely a *HRE DC-rendszergazdák* csoportjához tartozik. Ha szükséges, [vegyen fel egy felhasználói fiókot egy csoportba az Azure ad-ben](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Ismét az Azure AD DS felügyelt tartománynevet minden nagybetűvel meg kell adni. A következő példában a nevű `contosoadmin@contoso.com` fiók a Kerberos inicializálására szolgál. Adja meg saját felhasználói fiókját, amely a *HRE DC-rendszergazdák* csoport tagja:

    ```console
    kinit contosoadmin@CONTOSO.COM
    ```

1. Végül csatlakoztassa a gépet az Azure AD DS felügyelt tartományhoz az `realm join` paranccsal. Ugyanazt a felhasználói fiókot használja, amely az előző `kinit` `contosoadmin@CONTOSO.COM`parancsban megadott *HRE DC-rendszergazdák* csoport tagja, például:

    ```console
    sudo realm join --verbose CONTOSO.COM -U 'contosoadmin@CONTOSO.COM'
    ```

Néhány percet vesz igénybe, hogy csatlakozzon a virtuális géphez az Azure AD DS felügyelt tartományhoz. A következő példa kimenete azt mutatja, hogy a virtuális gép sikeresen csatlakozott az Azure AD DS felügyelt tartományhoz:

```output
Successfully enrolled machine in realm
```

Ha a virtuális gép nem tudja sikeresen befejezni a tartományhoz való csatlakozás folyamatát, győződjön meg arról, hogy a virtuális gép hálózati biztonsági csoportja engedélyezi a kimenő Kerberos-forgalmat a 464-as TCP + UDP-porton az Azure AD DS felügyelt tartományának virtuális hálózati alhálózatán.

## <a name="allow-password-authentication-for-ssh"></a>Jelszó-hitelesítés engedélyezése SSH-hoz

Alapértelmezés szerint a felhasználók csak az SSH nyilvános kulcs-alapú hitelesítés használatával jelentkezhetnek be egy virtuális gépre. A jelszó-alapú hitelesítés meghiúsul. Amikor csatlakoztatja a virtuális gépet egy Azure AD DS felügyelt tartományhoz, a tartományi fiókoknak jelszó alapú hitelesítést kell használniuk. Frissítse az SSH-konfigurációt a jelszó alapú hitelesítés engedélyezéséhez az alábbiak szerint.

1. Nyissa meg a *sshd_conf* fájlt szerkesztővel:

    ```console
    sudo vi /etc/ssh/sshd_config
    ```

1. A *PasswordAuthentication* vonalának frissítése *Igen*értékre:

    ```console
    PasswordAuthentication yes
    ```

    Ha elkészült, mentse és zárja be a *sshd_conf* fájlt `:wq` a szerkesztő parancsával.

1. A módosítások alkalmazásához és a felhasználók jelszóval való bejelentkezéséhez indítsa újra az SSH-szolgáltatást:

    ```console
    sudo systemctl restart sshd
    ```

## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>A "HRE DC Administrators" csoport sudo-jogosultságának megadása

Ha a *HRE tartományvezérlő rendszergazdák* csoportjának tagjai számára rendszergazdai jogosultságokat kíván adni a CentOS virtuális gépen, vegyen fel egy bejegyzést a */etc/sudoers*. A Hozzáadás után a *HRE tartományvezérlő rendszergazdák* csoportjának tagjai a `sudo` parancsot a CentOS virtuális gépen is használhatják.

1. Nyissa meg a *sudoers* fájlt a szerkesztéshez:

    ```console
    sudo visudo
    ```

1. Adja hozzá a következő bejegyzést a */etc/sudoers* fájl végéhez. A *HRE DC-rendszergazdák* csoport szóközt tartalmaz a névben, így a csoport neve tartalmazza a fordított perjel karaktert. Adja hozzá a saját tartománynevét, például *contoso.com*:

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators@contoso.com ALL=(ALL) NOPASSWD:ALL
    ```

    Ha elkészült, mentse és zárja be a szerkesztőt `:wq` a szerkesztő parancsának használatával.

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Bejelentkezés a virtuális gépre tartományi fiók használatával

Annak ellenőrzéséhez, hogy a virtuális gép sikeresen csatlakozott-e az Azure AD DS felügyelt tartományhoz, indítson el egy új SSH-kapcsolódást egy tartományi felhasználói fiók használatával. Győződjön meg arról, hogy a kezdőkönyvtár létrejött, és a rendszer a tartományból származó csoporttagság alkalmazását alkalmazza.

1. Hozzon létre egy új SSH-kapcsolatokat a konzolon. Használjon olyan tartományi fiókot, amely a felügyelt tartományhoz tartozik `ssh -l` a parancs használatával, `contosoadmin@contoso.com` például:, majd adja meg a virtuális gép (például *CentOS.contoso.com*) címeit. Ha a Azure Cloud Shell használja, a belső DNS-név helyett használja a virtuális gép nyilvános IP-címét.

    ```console
    ssh -l contosoadmin@CONTOSO.com centos.contoso.com
    ```

1. Ha sikeresen csatlakozott a virtuális géphez, ellenőrizze, hogy helyesen lett-e inicializálva a kezdőkönyvtár:

    ```console
    pwd
    ```

    A */Home* könyvtárban kell lennie a saját címtárával, amely megfelel a felhasználói fióknak.

1. Most győződjön meg arról, hogy a csoporttagságok megfelelően vannak feloldva:

    ```console
    id
    ```

    Az Azure AD DS felügyelt tartományból kell megjelennie a csoporttagságok.

1. Ha a *HRE DC-rendszergazdák* csoport tagjaként jelentkezett be a virtuális gépre, ellenőrizze, hogy helyesen használja-e a `sudo` következő parancsot:

    ```console
    sudo yum update
    ```

## <a name="next-steps"></a>További lépések

Ha problémái adódnak a virtuális gép Azure AD DS felügyelt tartományhoz való csatlakoztatásával vagy egy tartományi fiókkal való bejelentkezéssel kapcsolatban, olvassa el a [tartományhoz való csatlakozással kapcsolatos problémák elhárítása](join-windows-vm.md#troubleshoot-domain-join-issues)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
