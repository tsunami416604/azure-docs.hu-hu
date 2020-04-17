---
title: Állomásnevek megtekintése és módosítása | Microsoft dokumentumok
description: Az Azure virtuális gépek, webes és feldolgozói szerepkörök állomásnevének megtekintése és módosítása névfeloldáshoz
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
ms.assetid: c668cd8e-4e43-4d05-acc3-db64fa78d828
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 09c911838d34a083de8450145ce849fc966eb0e8
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459151"
---
# <a name="viewing-and-modifying-hostnames"></a>Állomásnevek megtekintése és módosítása
Ahhoz, hogy a szerepkörpéldányokra állomásnév alapján lehessen hivatkozni, minden szerepkörhöz be kell állítania az állomásnév értékét a szolgáltatáskonfigurációs fájlban. Ehhez adja hozzá a kívánt állomásnevet a **szerepkörelem** **vmName** attribútumához. A **vmName** attribútum értéke az egyes szerepkörpéldányok állomásnevének alapjaként szolgál. Ha például **a vmName** *webrole,* és a szerepkörnek három példánya van, a példányok állomásnevei *a webrole0*, *webrole1*és *webrole2*lesznek. A konfigurációs fájlban nem kell állomásnevet megadnia a virtuális gépekhez, mert a virtuális gép állomásneve a virtuális gép neve alapján van feltöltve. A Microsoft Azure-szolgáltatások konfigurálásáról az [Azure service konfigurációs sémájában (.cscfg fájl)](https://msdn.microsoft.com/library/azure/ee758710.aspx) talál további információt.

## <a name="viewing-hostnames"></a>Állomásnevek megtekintése
A virtuális gépek és a szerepkörpéldányok állomásnevét egy felhőszolgáltatásban az alábbi eszközök bármelyikével tekintheti meg.

### <a name="service-configuration-file"></a>Szolgáltatás konfigurációs fájlja
Letöltheti a szolgáltatás konfigurációs fájl egy telepített szolgáltatás a **szolgáltatás konfigurálása** panel az Azure Portalon. Ezután megkeresheti a **vmName** attribútumot a **szerepkörnév** eleméhez az állomásnév megtekintéséhez. Ne feledje, hogy ez az állomásnév az egyes szerepkörpéldányok állomásnevének alapjaként szolgál. Ha például **a vmName** *webrole,* és a szerepkörnek három példánya van, a példányok állomásnevei *a webrole0*, *webrole1*és *webrole2*lesznek.

### <a name="remote-desktop"></a>Távoli asztal
Miután engedélyezte a Távoli asztal (Windows), a Windows PowerShell(Windows) vagy az SSH (Linux és Windows) kapcsolatokat a virtuális gépekkel vagy a szerepkörpéldányokkal, az állomásnevet egy aktív Távoli asztali kapcsolatról különböző módokon tekintheti meg:

* Írja be az állomásnév parancsot a parancssorba vagy az SSH-terminálba.
* Írja be az ipconfig /all parancsot a parancssorba (csak Windows rendszerben).
* A számítógép nevének megtekintése a rendszerbeállításokban (csak Windows rendszerben).

### <a name="azure-service-management-rest-api"></a>Az Azure Service Management REST API-ja
REST-ügyféltől kövesse az alábbi utasításokat:

1. Győződjön meg arról, hogy rendelkezik egy ügyfél-tanúsítvánnyal az Azure Portalhoz való csatlakozáshoz. Ügyféltanúsítvány beszerzéséhez kövesse a [Közzétételi beállítások és az Előfizetési adatok letöltése és importálása](https://msdn.microsoft.com/library/dn385850.aspx)című részben leírt lépéseket. 
2. X-ms-version nevű fejlécbejegyzés beállítása 2013-11-01 értékkel.
3. Kérelem küldése a következő formátumban:\/\<https: /management.core.windows.net/\>subscrition-id\</services/hostedservices/ service-name\>?embed-detail=true
4. Keresse meg az egyes **RoleInstance-elemek** **HostName** elemét.

> [!WARNING]
> A felhőszolgáltatás belső tartományutótagot a REST hívásválaszból is megtekintheti az **InternalDnsSuffix** elem ellenőrzésével, vagy az ipconfig /all futtatásával egy távoli asztali munkamenetben (Windows), vagy a cat /etc/resolv.conf ssh terminálról (Linux) történő futtatásával.
> 
> 

## <a name="modifying-a-hostname"></a>Állomásnév módosítása
Bármely virtuális gép vagy szerepkörpéldány állomásnevét módosíthatja egy módosított szolgáltatáskonfigurációs fájl feltöltésével vagy a számítógép távoli asztali munkamenetből történő átnevezésével.

## <a name="next-steps"></a>További lépések
[Névfeloldás (DNS)](virtual-networks-name-resolution-for-vms-and-role-instances.md)

[Azure-szolgáltatáskonfigurációs séma (.cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710.aspx)

[Az Azure virtuális hálózati konfigurációs sémája](https://go.microsoft.com/fwlink/?LinkId=248093)

[DNS-beállítások megadása hálózati konfigurációs fájlokkal](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md)

