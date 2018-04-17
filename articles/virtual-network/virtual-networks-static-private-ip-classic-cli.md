---
title: Magánhálózati IP-címek konfigurálása virtuális gépek (klasszikus) - Azure CLI 1.0 |} Microsoft Docs
description: Útmutató az Azure parancssori felület (CLI) 1.0 használó virtuális gépek (klasszikus) magánhálózati IP-címek konfigurálásához.
services: virtual-network
documentationcenter: na
author: genli
manager: cshepard
editor: tysonn
tags: azure-service-management
ms.assetid: 17386acf-c708-4103-9b22-ff9bf04b778d
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c23419fdee7330d091d4699714f4ec08e72be2df
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-cli-10"></a>A virtuális gép (klasszikus) használata az Azure CLI 1.0 magánhálózati IP-címek konfigurálása

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. Emellett [kezelése a Resource Manager üzembe helyezési modellel statikus magánhálózati IP-cím](virtual-networks-static-private-ip-arm-cli.md).

Az alábbi minta Azure parancssori felület parancsait már létrehozott egy egyszerű környezetben várható. Ha szeretné a parancsokat a jelen dokumentum megjelenített, először leírt tesztkörnyezet felépítéséhez [hozhat létre egy vnetet](virtual-networks-create-vnet-classic-cli.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>A statikus magánhálózati IP-cím megadása a virtuális gép létrehozásakor
Nevű új virtuális gép létrehozása *DNS01* új felhőszolgáltatásban nevű *TestService* a fenti forgatókönyv alapján, kövesse az alábbi lépéseket:

1. Ha még sosem használta az Azure CLI-t, akkor tekintse meg [Install and Configure the Azure CLI](../cli-install-nodejs.md) (Az Azure CLI telepítése és konfigurálása) részt, és kövesse az utasításokat addig a pontig, ahol ki kell választania az Azure-fiókot és -előfizetést.
2. Futtassa a **azure-szolgáltatás létrehozása** parancs használatával a felhőalapú szolgáltatás létrehozása.
   
        azure service create TestService --location uscentral
   
    Várt kimenet:
   
        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name TestService
        info:    service create command OK
3. Futtassa a **azure virtuális gép létrehozása** parancsot a virtuális gép létrehozásához. Figyelje meg, hogy egy statikus magánhálózati IP-cím értékét. A kimenet után látható lista ismerteti a használt paramétereket.
   
        azure vm create -l centralus -n DNS01 -w TestVNet -S "192.168.1.101" TestService bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2 adminuser AdminP@ssw0rd
   
    Várt kimenet:
   
        info:    Executing command vm create
        warn:    --vm-size has not been specified. Defaulting to "Small".
        info:    Looking up image bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2
        info:    Looking up virtual network
        info:    Looking up cloud service
        warn:    --location option will be ignored
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Retrieving storage accounts
        info:    Creating VM
        info:    OK
        info:    vm create command OK
   
   * **-l (vagy --location)**. Azure-régió, ahol a virtuális gép létrejön. A mi esetünkben *centralus*.
   * **-n (vagy--virtuálisgép-név)**. A létrehozandó virtuális gép neve.
   * **-l (vagy--virtuális hálózat neve)**. A VNet neve, ahol a virtuális gép létrejön. 
   * **-S (vagy--statikus ip-)**. Statikus magánhálózati IP-címet a virtuális gép számára.
   * **TestService**. A felhőalapú szolgáltatás, ahol létrejön a virtuális gép neve.
   * **bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2**. A virtuális gép létrehozásához használt lemezkép.
   * **adminuser**. Helyi rendszergazda a Windows virtuális gép számára.
   * **AdminP@ssw0rd**. Helyi rendszergazda jelszavát a Windows virtuális gép számára.

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Hogyan lehet lekérni a statikus magánhálózati IP-címadatok a virtuális gépek
A statikus magánhálózati IP-címadatok a fenti parancsfájl létrehozza a virtuális gép megtekintéséhez futtassa a következő Azure CLI parancsot, és tekintse meg az értékét *hálózati StaticIP*:

    azure vm static-ip show DNS01

Várt kimenet:

    info:    Executing command vm static-ip show
    info:    Getting virtual machines
    data:    Network StaticIP "192.168.1.101"
    info:    vm static-ip show command OK

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>A statikus magánhálózati IP-cím eltávolítása a virtuális gépek
A statikus magánhálózati IP-cím eltávolítása hozzáadva a virtuális Gépet, a fenti, a parancsfájl a következő Azure CLI-parancsot:

    azure vm static-ip remove DNS01

Várt kimenet:

    info:    Executing command vm static-ip remove
    info:    Getting virtual machines
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip remove command OK

## <a name="how-to-add-a-static-private-ip-to-an-existing-vm"></a>Egy statikus magánhálózati IP-cím hozzáadása egy meglévő virtuális Gépen
Adja hozzá a statikus magánhálózati IP-címe cím a fent runt parancsfájl használatával létrehozott virtuális gép a következő parancsot:

    azure vm static-ip set DNS01 192.168.1.101

Várt kimenet:

    info:    Executing command vm static-ip set
    info:    Getting virtual machines
    info:    Looking up virtual network
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip set command OK

## <a name="set-ip-addresses-within-the-operating-system"></a>Állítsa be az operációs rendszer belüli IP-címek

Javasoljuk, hogy nem statikusan rendelje a magánhálózati IP-címe az operációs rendszerben a virtuális gépek az Azure virtuális géphez rendelt kivéve, ha szükséges. Ha manuálisan állítsa be a magánhálózati IP-cím, az operációs rendszerből, győződjön meg arról, hogy-e a magánhálózati IP-cím, az Azure virtuális Géphez rendelt megegyező címre, vagy is megszakad a kapcsolat a virtuális géphez. Manuálisan soha ne rendelje a nyilvános IP-cím, egy Azure virtuális gépen belül a virtuális gép operációs rendszerének rendelt.

## <a name="next-steps"></a>További lépések
* További tudnivalók [foglalt nyilvános IP-cím](virtual-networks-reserved-public-ip.md) címek.
* További tudnivalók [példányszintű nyilvános IP (ILPIP)](virtual-networks-instance-level-public-ip.md) címek.
* Tekintse át a [fenntartott IP-REST API-k](https://msdn.microsoft.com/library/azure/dn722420.aspx).

