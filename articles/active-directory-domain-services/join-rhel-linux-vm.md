---
title: RHEL virtuális gép csatlakoztatása Azure AD Domain Serviceshoz | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhat és csatlakoztathat egy Red Hat Enterprise Linux virtuális gépet egy Azure AD Domain Services felügyelt tartományhoz.
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
ms.openlocfilehash: 9472abd7a16c887a796e36b8190e8530c84dafa9
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755707"
---
# <a name="join-a-red-hat-enterprise-linux-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>Red Hat Enterprise Linux virtuális gépek csatlakoztatása az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz

Annak érdekében, hogy a felhasználók egyetlen hitelesítő adat használatával jelentkezzenek be a virtuális gépekre az Azure-ban, csatlakoztathatja a virtuális gépeket egy Azure Active Directory Domain Services (AD DS) felügyelt tartományhoz. Amikor egy virtuális gépet csatlakoztat egy Azure AD DS felügyelt tartományhoz, a tartomány felhasználói fiókjai és hitelesítő adatai használhatók a kiszolgálók bejelentkezéséhez és kezeléséhez. Az Azure AD DS felügyelt tartományból származó csoporttagságok a virtuális gépen található fájlokhoz vagy szolgáltatásokhoz való hozzáférés szabályozására is vonatkoznak.

Ez a cikk bemutatja, hogyan csatlakozhat egy Red Hat Enterprise Linux (RHEL) virtuális géphez egy Azure AD DS felügyelt tartományhoz.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következő erőforrásokra és jogosultságokra van szüksége:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Az előfizetéshez társított Azure Active Directory bérlő, vagy egy helyszíni címtárral vagy egy csak felhőalapú címtárral van szinkronizálva.
    * Ha szükséges, [hozzon létre egy Azure Active Directory bérlőt][create-azure-ad-tenant] , vagy [rendeljen hozzá egy Azure-előfizetést a fiókjához][associate-azure-ad-tenant].
* Egy Azure Active Directory Domain Services felügyelt tartomány engedélyezve és konfigurálva van az Azure AD-bérlőben.
    * Ha szükséges, az első oktatóanyag [egy Azure Active Directory Domain Services példányt hoz létre és konfigurál][create-azure-ad-ds-instance].
* Egy felhasználói fiók, amely tagja az Azure ad *DC-rendszergazdák* csoportnak az Azure ad-bérlőben.

## <a name="create-and-connect-to-a-rhel-linux-vm"></a>RHEL Linux rendszerű virtuális gép létrehozása és kapcsolódás

Ha rendelkezik egy meglévő RHEL Linux virtuális géppel az Azure-ban, csatlakozzon az SSH-val, majd folytassa a következő lépéssel a [virtuális gép konfigurálásának megkezdéséhez](#configure-the-hosts-file).

Ha létre kell hoznia egy RHEL Linux rendszerű virtuális gépet, vagy létre szeretne hozni egy tesztelési virtuális gépet, amely a jelen cikkben használható, a következő módszerek egyikét használhatja:

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

* a *contoso.com* az Azure AD DS felügyelt tartományának DNS-tartományneve.
* a *RHEL* a felügyelt tartományhoz csatlakozó RHEL-alapú virtuális gép állomásneve.

Frissítse ezeket a neveket a saját értékeivel:

```console
127.0.0.1 rhel rhel.contoso.com
```

Ha elkészült, mentse és zárja be a *hosts* fájlt a szerkesztő `:wq` parancsának használatával.

## <a name="install-required-packages"></a>Szükséges csomagok telepítése

A virtuális gépnek szüksége van néhány további csomagra a virtuális gép Azure AD DS felügyelt tartományhoz való csatlakoztatásához. A csomagok telepítéséhez és konfigurálásához frissítse és telepítse a tartományhoz csatlakozó eszközöket `yum` használatával:

 **7. RHEL** 

```console
sudo yum install realmd sssd krb5-workstation krb5-libs oddjob oddjob-mkhomedir samba-common-tools
```

 **6. RHEL** 

```console
sudo yum install adcli sssd authconfig krb5-workstation
```

## <a name="join-vm-to-the-managed-domain"></a>Virtuális gép csatlakoztatása a felügyelt tartományhoz

Most, hogy a szükséges csomagok telepítve vannak a virtuális gépen, csatlakoztassa a virtuális gépet az Azure AD DS felügyelt tartományhoz.
 
  **7. RHEL**
     
1. Az `realm discover` parancs használatával keresse fel az Azure AD DS felügyelt tartományt. A következő példa felfedi a *contoso.com*tartományát. Adja meg saját Azure AD DS felügyelt tartománynevét az összes nagybetűvel:

    ```console
    sudo realm discover CONTOSO.COM
    ```

   Ha az `realm discover` parancs nem találja az Azure AD DS felügyelt tartományát, tekintse át a következő hibaelhárítási lépéseket:
   
    * Győződjön meg arról, hogy a tartomány elérhető a virtuális gépről. Próbálja meg `ping contoso.com` a pozitív válasz visszaadása.
    * Győződjön meg arról, hogy a virtuális gép üzembe helyezése ugyanarra a virtuális gépre történik, ahol az Azure AD DS felügyelt tartomány elérhető.
    * Győződjön meg arról, hogy a virtuális hálózat DNS-kiszolgálójának beállításai frissítve lettek, hogy az Azure AD DS felügyelt tartományának tartományvezérlőjére mutasson.

1. Most inicializálja a Kerberost a `kinit` parancs használatával. Olyan felhasználót válasszon, amely a *HRE DC-rendszergazdák* csoportjához tartozik. Ha szükséges, [vegyen fel egy felhasználói fiókot egy csoportba az Azure ad-ben](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Ismét az Azure AD DS felügyelt tartománynevet minden nagybetűvel meg kell adni. A következő példában az `contosoadmin@contoso.com` nevű fiók a Kerberos inicializálására szolgál. Adja meg saját felhasználói fiókját, amely a *HRE DC-rendszergazdák* csoport tagja:
    
    ```console
    kinit contosoadmin@CONTOSO.COM
    ``` 

1. Végül csatlakoztassa a gépet az Azure AD DS felügyelt tartományhoz a `realm join` parancs használatával. Ugyanazt a felhasználói fiókot használja, mint amely az előző `kinit` parancsban megadott *HRE DC-rendszergazdák* csoport tagja, például `contosoadmin@CONTOSO.COM`:

    ```console
    sudo realm join --verbose CONTOSO.COM -U 'contosoadmin@CONTOSO.COM'
    ```

Néhány percet vesz igénybe, hogy csatlakozzon a virtuális géphez az Azure AD DS felügyelt tartományhoz. A következő példa kimenete azt mutatja, hogy a virtuális gép sikeresen csatlakozott az Azure AD DS felügyelt tartományhoz:

```output
Successfully enrolled machine in realm
```

  **6. RHEL** 

1. Az `adcli info` parancs használatával keresse fel az Azure AD DS felügyelt tartományt. A következő példa felfedi a *contoso.com*tartományát. Adja meg saját Azure AD DS felügyelt tartománynevét az összes nagybetűvel:

    ```console
    sudo adcli info contoso.com
    ```
    
   Ha az `adcli info` parancs nem találja az Azure AD DS felügyelt tartományát, tekintse át a következő hibaelhárítási lépéseket:
   
    * Győződjön meg arról, hogy a tartomány elérhető a virtuális gépről. Próbálja meg `ping contoso.com` a pozitív válasz visszaadása.
    * Győződjön meg arról, hogy a virtuális gép üzembe helyezése ugyanarra a virtuális gépre történik, ahol az Azure AD DS felügyelt tartomány elérhető.
    * Győződjön meg arról, hogy a virtuális hálózat DNS-kiszolgálójának beállításai frissítve lettek, hogy az Azure AD DS felügyelt tartományának tartományvezérlőjére mutasson.

1. Először csatlakoztassa a tartományt a `adcli join` parancs használatával, ezzel a paranccsal a gép hitelesítéséhez a keytab kiterjesztésű is létrejön. Olyan felhasználói fiókot használjon, amely a *HRE DC-rendszergazdák* csoport tagja. 

    ```console
    sudo adcli join contoso.com -U contosoadmin
    ```

1. Most konfigurálja a `/ect/krb5.conf`, és hozza létre a `/etc/sssd/sssd.conf` fájlokat a `contoso.com` Active Directory tartomány használatához. 
   Győződjön meg arról, hogy a `CONTOSO.COM`t a saját tartományneve váltja fel:

    Nyissa meg a `/ect/krb5.conf` fájlt egy szerkesztővel:

    ```console
    sudo vi /etc/krb5.conf
    ```

    Frissítse a `krb5.conf` fájlt, hogy az megfeleljen a következő mintának:

    ```console
    [logging]
     default = FILE:/var/log/krb5libs.log
     kdc = FILE:/var/log/krb5kdc.log
     admin_server = FILE:/var/log/kadmind.log
    
    [libdefaults]
     default_realm = CONTOSO.COM
     dns_lookup_realm = true
     dns_lookup_kdc = true
     ticket_lifetime = 24h
     renew_lifetime = 7d
     forwardable = true
    
    [realms]
     CONTOSO.COM = {
     kdc = CONTOSO.COM
     admin_server = CONTOSO.COM
     }
    
    [domain_realm]
     .CONTOSO.COM = CONTOSO.COM
     CONTOSO.COM = CONTOSO.COM
    ```
    
   Hozza létre a `/etc/sssd/sssd.conf` fájlt:
    
    ```console
    sudo vi /etc/sssd/sssd.conf
    ```

    Frissítse a `sssd.conf` fájlt, hogy az megfeleljen a következő mintának:

    ```console
    [sssd]
     services = nss, pam, ssh, autofs
     config_file_version = 2
     domains = CONTOSO.COM
    
    [domain/CONTOSO.COM]
    
     id_provider = ad
    ```

1. Győződjön meg arról, hogy `/etc/sssd/sssd.conf` engedélyek 600, és a root felhasználó tulajdonában van:

    ```console
    sudo chmod 600 /etc/sssd/sssd.conf
    sudo chown root:root /etc/sssd/sssd.conf
    ```

1. A `authconfig` használatával utasítsa el a virtuális gépet az AD Linux-integrációval kapcsolatban:

    ```console
    sudo authconfig --enablesssd --enablesssdauth --update
    ```
    
1. A sssd szolgáltatás elindítása és engedélyezése:

    ```console
    sudo service sssd start
    sudo chkconfig sssd on
    ```

Ha a virtuális gép nem tudja sikeresen befejezni a tartományhoz való csatlakozás folyamatát, győződjön meg arról, hogy a virtuális gép hálózati biztonsági csoportja engedélyezi a kimenő Kerberos-forgalmat a 464-as TCP + UDP-porton az Azure AD DS felügyelt tartományának virtuális hálózati alhálózatán.

Most ellenőrizze, hogy tud-e lekérdezni a felhasználói AD-adatokat `getent`

```console
sudo getent passwd contosoadmin
```

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

    Ha elkészült, mentse és zárja be a *sshd_conf* fájlt a szerkesztő `:wq` parancsával.

1. A módosítások alkalmazásához és a felhasználók jelszóval való bejelentkezéséhez indítsa újra az SSH-szolgáltatást:

   **7. RHEL** 
    
    ```console
    sudo systemctl restart sshd
    ```

   **6. RHEL** 
    
    ```console
    sudo service sshd restart
    ```

## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>A "HRE DC Administrators" csoport sudo-jogosultságának megadása

Ahhoz, hogy a *HRE tartományvezérlő rendszergazdák* csoportjának tagjai számára rendszergazdai jogosultságokat lehessen adni a RHEL virtuális gépen, hozzá kell adnia egy bejegyzést a */etc/sudoers*. A Hozzáadás után a *HRE tartományvezérlő rendszergazdák* csoportjának tagjai a `sudo` parancsot HASZNÁLHATJÁK a RHEL virtuális gépen.

1. Nyissa meg a *sudoers* fájlt a szerkesztéshez:

    ```console
    sudo visudo
    ```

1. Adja hozzá a következő bejegyzést a */etc/sudoers* fájl végéhez. A *HRE DC-rendszergazdák* csoport szóközt tartalmaz a névben, így a csoport neve tartalmazza a fordított perjel karaktert. Adja hozzá a saját tartománynevét, például *contoso.com*:

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators@contoso.com ALL=(ALL) NOPASSWD:ALL
    ```

    Ha elkészült, mentse és zárja be a szerkesztőt a szerkesztő `:wq` parancsának használatával.

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Bejelentkezés a virtuális gépre tartományi fiók használatával

Annak ellenőrzéséhez, hogy a virtuális gép sikeresen csatlakozott-e az Azure AD DS felügyelt tartományhoz, indítson el egy új SSH-kapcsolódást egy tartományi felhasználói fiók használatával. Győződjön meg arról, hogy a kezdőkönyvtár létrejött, és a rendszer a tartományból származó csoporttagság alkalmazását alkalmazza.

1. Hozzon létre egy új SSH-kapcsolatokat a konzolon. Használjon olyan tartományi fiókot, amely a felügyelt tartományhoz tartozik a `ssh -l` parancs használatával, például `contosoadmin@contoso.com`, majd adja meg a virtuális gép (például *RHEL.contoso.com*) címeit. Ha a Azure Cloud Shell használja, a belső DNS-név helyett használja a virtuális gép nyilvános IP-címét.

    ```console
    ssh -l contosoadmin@CONTOSO.com rhel.contoso.com
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

1. Ha a *HRE DC-rendszergazdák* csoport tagjaként jelentkezett be a virtuális gépre, ellenőrizze, hogy megfelelően tudja-e használni a `sudo` parancsot:

    ```console
    sudo yum update
    ```

## <a name="next-steps"></a>Következő lépések

Ha problémái adódnak a virtuális gép Azure AD DS felügyelt tartományhoz való csatlakoztatásával vagy egy tartományi fiókkal való bejelentkezéssel kapcsolatban, olvassa el a [tartományhoz való csatlakozással kapcsolatos problémák elhárítása](join-windows-vm.md#troubleshoot-domain-join-issues)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
