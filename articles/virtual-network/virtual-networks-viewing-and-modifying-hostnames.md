---
title: Megtekintése és módosítása az állomásnevek |} Microsoft Docs
description: Hogyan megtekintése és módosítása az Azure virtuális gépek állomásnevek, webes és feldolgozói szerepkörök névfeloldás
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
ms.date: 05/24/2018
ms.author: genli
ms.openlocfilehash: f4c602368368e8ef36581d3f035ff3943a8f0d8f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34657281"
---
# <a name="viewing-and-modifying-hostnames"></a>Megtekintése és módosítása az állomásnevek
Az állomásnév lehet hivatkozni a szerepkörpéldányok engedélyezéséhez meg kell adni az állomásnév értéke a konfigurációs fájlban az egyes szerepkörökhöz. A kívánt gazdagép nevét hozzáadása ehhez a **vmName** attribútuma a **szerepkör** elemet. Értékét a **vmName** attribútum alapjaként van használatban a névhez, az összes szerepkör-példány. Például ha **vmName** van *webrole típusról* , de az adott szerepkör három alkalmazáspéldányra, az állomásneveket példánya lesz *webrole0*, *webrole1*, és *webrole2*. Meg kell a virtuális gépek gazdagépnevet adjon meg a konfigurációs fájlban, mivel egy virtuális gép állomásnevét a virtuális gép neve alapján van feltöltve. A Microsoft Azure-szolgáltatás konfigurálásával kapcsolatos további információkért lásd: [Azure szolgáltatás konfigurációs séma (.cscfg fájl)](https://msdn.microsoft.com/library/azure/ee758710.aspx)

## <a name="viewing-hostnames"></a>Állomásnevek megtekintése
Az alábbi eszközök egyikével szerepkörpéldányokat és a virtuális gépek állomás nevét a felhőszolgáltatás tekintheti meg.

### <a name="service-configuration-file"></a>Szolgáltatás konfigurációs fájlja
A szolgáltatás konfigurációs fájlja egy telepített szolgáltatáshoz a letöltheti a **konfigurálása** panel az Azure-portálon a szolgáltatást. Majd kereshet a **vmName** az attribútum a **szerepkörnév** elem gazdagép nevének megtekintéséhez. Ne feledje, hogy a állomásnevet alapjaként nem használja az összes szerepkör-példány az állomás neve. Például ha **vmName** van *webrole típusról* , de az adott szerepkör három alkalmazáspéldányra, az állomásneveket példánya lesz *webrole0*, *webrole1*, és *webrole2*.

### <a name="remote-desktop"></a>Távoli asztal
Miután engedélyezte a távoli asztal (Windows), a Windows PowerShell távoli eljáráshívás (Windows) vagy a virtuális gépek vagy a szerepkörpéldányok (Linux és Windows rendszerekhez) SSH-kapcsolatok, megtekintheti az állomásnév aktív távoli asztali kapcsolat különböző módokon:

* Írja be a parancssort vagy a Terminálszolgáltatások SSH állomásnév.
* Írja be az ipconfig/minden parancsot a parancssorba (csak Windows).
* A számítógép nevének megtekintéséhez a rendszer beállításai (csak Windows).

### <a name="azure-service-management-rest-api"></a>Azure szolgáltatásfelügyelet REST API-n
A többi ügyfél kövesse az alábbi utasításokat:

1. Győződjön meg arról, hogy rendelkezik-e csatlakozni az Azure-portálon ügyféltanúsítványt. Szerezzen be ügyféltanúsítványt, kövesse a megjelenő lépéseket [hogyan: letöltése és közzététele beállításokat importálhatja adatait és előfizetési információit](https://msdn.microsoft.com/library/dn385850.aspx). 
2. Állítsa be egy x-ms-version értéke az 2013-11-01 nevű fejlécében bejegyzést.
3. Kérés küldése a következő formátumban: https://management.core.windows.net/\<subscrition-id\>/services/hostedservices/\<service-name\>?embed-detail=true
4. Keresse meg a **állomásnév** minden elem **RoleInstance** elemet.

> [!WARNING]
> Megtekintheti a belső tartomány utótag a felhőalapú szolgáltatás, a REST-hívást válaszban szereplő ellenőrzésével a **InternalDnsSuffix** elem, vagy futtassa az ipconfig/minden a parancssorba a távoli asztali munkamenetben (Windows), vagy futtassa a macskaeledel /etc/resolv.conf SSH terminálról (Linux).
> 
> 

## <a name="modifying-a-hostname"></a>Az állomásnév módosítása
Módosíthatja a virtuális gép vagy szerepkörpéldány állomásneve egy módosított szolgáltatáskonfigurációs fájlt feltölteni, vagy a távoli asztali munkamenetből a számítógép átnevezése.

## <a name="next-steps"></a>További lépések
[Névfeloldás (DNS)](virtual-networks-name-resolution-for-vms-and-role-instances.md)

[Az Azure szolgáltatás konfigurációs séma (.cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710.aspx)

[Azure-beli virtuális hálózat konfigurációs séma](http://go.microsoft.com/fwlink/?LinkId=248093)

[Adja meg a hálózati konfigurációs fájlokat használja a DNS-beállítások](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md)

