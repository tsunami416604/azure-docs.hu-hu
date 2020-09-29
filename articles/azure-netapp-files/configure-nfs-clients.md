---
title: NFS-ügyfél konfigurálása a Azure NetApp Fileshoz | Microsoft Docs
description: Ismerteti, hogyan lehet konfigurálni az NFS-ügyfeleket a Azure NetApp Filessal való használatra.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/28/2020
ms.author: b-juche
ms.openlocfilehash: b2e597ff8fc761b66de6228063c471933a364144
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91449654"
---
# <a name="configure-an-nfs-client-for-azure-netapp-files"></a>NFS-ügyfél konfigurálása az Azure NetApp Fileshoz

Az ebben a cikkben ismertetett NFS-ügyfél konfigurációja a [nfsv 4.1 Kerberos-titkosítás konfigurálásakor](configure-kerberos-encryption.md) vagy [egy kettős protokollú kötet létrehozásakor](create-volumes-dual-protocol.md)a telepítés része. A Linux-disztribúciók széles választéka használható a Azure NetApp Fileshoz. Ez a cikk két leggyakrabban használt környezet konfigurációit ismerteti: RHEL 8 és Ubuntu 18,04. 

A használt Linux-íztől függetlenül a következő konfigurációk szükségesek:
* Konfiguráljon egy NTP-ügyfelet, hogy elkerülje a problémák időbeli eldöntését.
* Konfigurálja a Linux-ügyfél DNS-bejegyzéseit névfeloldásra.  
    Ez a konfiguráció tartalmazza az "A" (továbbítás) rekordot és a PTR (fordított) rekordot. 
* A tartományhoz való csatlakozáshoz hozzon létre egy számítógépfiókot a cél Active Directoryban (amely a tartományhoz való csatlakozás parancsban jön létre). 
    > [!NOTE] 
    > Az `$SERVICEACCOUNT` alábbi parancsokban használt változónak olyan felhasználói fióknak kell lennie, amely rendelkezik engedélyekkel vagy delegálással számítógépfiók létrehozásához a célként megadott szervezeti egységben.
* Engedélyezze az ügyfélnek az NFS-kötetek és más kapcsolódó figyelési eszközök csatlakoztatását.

## <a name="rhel-8-configuration"></a>RHEL 8 konfiguráció 

1. Csomagok telepítése:   
    `sudo yum -y install realmd sssd adcli samba-common krb5-workstation chrony`

2. Az NTP-ügyfél konfigurálása:  
    Alapértelmezés szerint a RHEL 8 használja `chrony` .  A konfigurációs irányelveket a [Chrony Suite használatával konfigurálhatja az NTP konfigurálásához](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/using-chrony-to-configure-ntp).

3. Csatlakozzon a Active Directory tartományhoz:  
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou= OU=$YOUROU,DC=$DOMAIN,DC=TLD`

## <a name="ubuntu-configuration"></a>Ubuntu-konfiguráció 
Ez a szakasz az NFS-ügyfelek Ubuntu-konfigurációját ismerteti.  

### <a name="if-you-are-using-nfsv41-kerberos-encryption"></a>Ha a NFSv 4.1-es Kerberos-titkosítást használja 

1. Csomagok telepítése:  
    `sudo yum -y install realmd packagekit sssd adcli samba-common krb5-workstation chrony`

2. Konfigurálja az NTP-ügyfelet.  
    Az Ubuntu 18,04 `chrony` alapértelmezés szerint használja.  A konfigurációs irányelvek az [Ubuntu Bionic-ben: a Chrony használata az NTP konfigurálásához](https://ubuntu.com/blog/ubuntu-bionic-using-chrony-to-configure-ntp).

3. Csatlakozzon a Active Directory-tartományhoz:  
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou= OU=$YOUROU,DC=$DOMAIN,DC=TLD`

### <a name="if-you-are-using-dual-protocol"></a>Ha kettős protokollt használ  

1. Futtassa a következő parancsot a telepített csomagok frissítéséhez:  
    `sudo apt update && sudo apt install libnss-ldap libpam-ldap ldap-utils nscd`

    Példa:   

    `base dc=hariscus,dc=com` `uri ldap://10.20.0.4:389/`
    `ldap_version 3`
    `rootbinddn cn=admin,cn=Users,dc=hariscus,dc=com`
    `pam_password ad`
 
2. Futtassa a következő parancsot a szolgáltatás újraindításához és engedélyezéséhez:   
    `sudo systemctl restart nscd && sudo systemctl enable nscd`

Az alábbi példa az AD LDAP-kiszolgálót az Ubuntu LDAP-ügyfélről kérdezi le egy LDAP-felhasználó számára `ldapu1` :   

`root@cbs-k8s-varun4-04:/home/cbs# getent passwd hari1`   
`hari1:*:1237:1237:hari1:/home/hari1:/bin/bash`   

## <a name="next-steps"></a>Következő lépések  

* [NFS-kötet létrehozása az Azure NetApp Files számára](azure-netapp-files-create-volumes.md)
* [Kettős protokollú kötet létrehozása a Azure NetApp Fileshoz](create-volumes-dual-protocol.md)

