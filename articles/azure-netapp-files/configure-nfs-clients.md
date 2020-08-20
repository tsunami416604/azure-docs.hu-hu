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
ms.date: 08/19/2020
ms.author: b-juche
ms.openlocfilehash: 4a54858f4a09cd62bb555a6df2c12efa2290dd69
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88653171"
---
# <a name="configure-an-nfs-client-for-azure-netapp-files"></a>NFS-ügyfél konfigurálása az Azure NetApp Fileshoz

A Linux-disztribúciók széles választéka használható a Azure NetApp Fileshoz. Ez a cikk két leggyakrabban használt környezet konfigurációit ismerteti: RHEL 8 és Ubuntu 18,04. Az ebben a cikkben ismertetett NFS-ügyfél konfigurációja a [nfsv 4.1 Kerberos-titkosítás konfigurálásakor](configure-kerberos-encryption.md) vagy [egy kettős protokollú kötet létrehozásakor](create-volumes-dual-protocol.md)a telepítés része.  

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

1. Csomagok telepítése:  
    `sudo yum -y install realmd packagekit sssd adcli samba-common krb5-workstation chrony`

2. Konfigurálja az NTP-ügyfelet.  
    Az Ubuntu 18,04 `chrony` alapértelmezés szerint használja.  A konfigurációs irányelvek az [Ubuntu Bionic-ben: a Chrony használata az NTP konfigurálásához](https://ubuntu.com/blog/ubuntu-bionic-using-chrony-to-configure-ntp).

3. Csatlakozzon a Active Directory-tartományhoz:  
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou= OU=$YOUROU,DC=$DOMAIN,DC=TLD`

## <a name="next-steps"></a>További lépések  

* [NFS-kötet létrehozása az Azure NetApp Files számára](azure-netapp-files-create-volumes.md)
* [Kettős protokollú kötet létrehozása a Azure NetApp Fileshoz](create-volumes-dual-protocol.md)

