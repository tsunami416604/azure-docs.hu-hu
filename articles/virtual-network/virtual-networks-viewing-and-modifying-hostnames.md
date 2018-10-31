---
title: Megtekintése és módosítása a gazdanév |} A Microsoft Docs
description: Hogyan megtekintése és módosítása állomásnevek Azure-beli virtuális gépek, webes és feldolgozói szerepkörök a névfeloldáshoz
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
ms.assetid: c668cd8e-4e43-4d05-acc3-db64fa78d828
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 4a836e0f5e47d6ef91e11fd1a0076859b54ed8e6
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50243027"
---
# <a name="viewing-and-modifying-hostnames"></a>Megtekintése és módosítása az állomásnevek
Ahhoz, hogy a szerepkörpéldány állomásnév szerint lehet hivatkozni, állítsa az értékét az állomásnév a szolgáltatáskonfigurációs fájlban az egyes szerepkörökhöz. Ezt teheti, hogy adja hozzá a kívánt állomás nevét a **vmName** attribútuma a **szerepkör** elemet. Értékét a **vmName** attribútuma minden egyes szerepkör-példány állomásneve szolgál alapul. Például ha **vmName** van *webrole* , de az adott szerepkör három példányban, a gazdagép-nevek a példányok lesznek *webrole0*, *webrole1*, és *webrole2*. Nem kell megadnia egy virtuálisgép-nevet a konfigurációs fájlban, mert a virtuális gép az állomásnév a virtuális gép neve alapján van feltöltve. Microsoft Azure-szolgáltatás konfigurálásával kapcsolatos további információkért lásd: [Azure szolgáltatás konfigurációs sémáját (.cscfg fájl)](https://msdn.microsoft.com/library/azure/ee758710.aspx)

## <a name="viewing-hostnames"></a>Gazdagépnevek megtekintése
Megtekintheti a gazdagépek nevének a virtuális gépek és szerepkörpéldányok egy cloud service-ben az alábbi eszközök használatával.

### <a name="service-configuration-file"></a>Szolgáltatáskonfigurációs fájlban
A szolgáltatás konfigurációs fájlja egy telepített szolgáltatáshoz a letöltheti a **konfigurálása** panelen a szolgáltatás az Azure Portalon. Ezután megkeresheti a **vmName** az attribútum a **szerepkörnév** elem megtekintéséhez a gazdagép nevét. Ne feledje, hogy a gazdagép neve alapként szolgál minden egyes szerepkör-példány állomásneve. Például ha **vmName** van *webrole* , de az adott szerepkör három példányban, a gazdagép-nevek a példányok lesznek *webrole0*, *webrole1*, és *webrole2*.

### <a name="remote-desktop"></a>Távoli asztal
Miután engedélyezte a távoli asztal (Windows), Windows PowerShell távoli eljáráshívás (Windows) vagy a virtuális gépek vagy szerepkörpéldányok (Linux és Windows) SSH-kapcsolatokat, az állomásnév egy aktív távoli asztali kapcsolatáról tekintheti meg különböző módokon:

* Írja be a gazdagépnév, a parancssorba vagy SSH-terminálba.
* Írja be az IP-konfiguráció/az összes parancsot a parancssorba (csak Windows).
* A számítógép nevét a rendszer beállításai (csak Windows) megtekintése.

### <a name="azure-service-management-rest-api"></a>Az Azure Service Management REST API
A REST-ügyféllel kövesse az alábbi utasításokat:

1. Győződjön meg arról, hogy rendelkezik-e csatlakozni az Azure Portalon egy ügyféltanúsítványt. Szerezzen be ügyféltanúsítványt, kövesse a megjelenő lépéseket [hogyan: letöltési és a közzétételi beállítások importálása és az előfizetési adatok](https://msdn.microsoft.com/library/dn385850.aspx). 
2. Állítsa be egy x-ms-version értéke az 2013-11-01 nevű fejléc-bejegyzést.
3. Kérés küldése a következő formátumban: https://management.core.windows.net/\<subscrition-id\>/services/hostedservices/\<service-name\>?embed-detail=true
4. Keresse meg a **állomásnév** minden elem **RoleInstance** elemet.

> [!WARNING]
> Is megtekintheti a belső tartomány utótag a felhőszolgáltatás a REST-hívást válaszból ellenőrzésével a **InternalDnsSuffix** elem, vagy futtatja az ipconfig/minden parancsot a parancssorba a távoli asztali munkamenet (Windows), vagy pedig a ssh-t a Terminálszolgáltatások (Linux) cat /etc/resolv.conf fut.
> 
> 

## <a name="modifying-a-hostname"></a>A hostname módosítása
Módosíthatja a virtuális gépek vagy szerepkörpéldányok bármelyikéhez az állomásnév, fel kell töltenie a módosított szolgáltatáskonfigurációs fájlt, vagy egy távoli asztali munkamenetből a számítógép átnevezése.

## <a name="next-steps"></a>További lépések
[Névfeloldás (DNS)](virtual-networks-name-resolution-for-vms-and-role-instances.md)

[Az Azure szolgáltatás konfigurációs sémáját (.cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710.aspx)

[Az Azure Virtual Network konfigurációs séma](http://go.microsoft.com/fwlink/?LinkId=248093)

[Hálózati konfigurációs fájlok használatával DNS-beállítások megadása](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md)

