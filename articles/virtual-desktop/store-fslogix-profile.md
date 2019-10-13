---
title: Tárolási beállítások a Windows rendszerű virtuális asztali FSLogix-profilok tárolói számára – Azure
description: A Windows rendszerű virtuális asztali FSLogix-profil Azure Storage-beli tárolásának lehetőségei.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: helohr
ms.openlocfilehash: 8bee226c85e6433500fcbef9b084cc547ef2b58f
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301671"
---
# <a name="storage-options-for-fslogix-profile-containers-in-windows-virtual-desktop"></a>Tárolási beállítások a Windows rendszerű virtuális asztali FSLogix-profilok tárolói számára

Az Azure több tárolási megoldást is kínál, amelyek segítségével tárolhatja a FSLogix-profil tárolóját. Ez a cikk összehasonlítja a tárolási megoldásokat az Azure Storage-ajánlatokkal a Windows rendszerű virtuális asztali FSLogix-profilok felhasználói profiljaihoz.

A Windows rendszerű virtuális asztal az ajánlott felhasználói profil-megoldásként kínál FSLogix-profilokat. A FSLogix távoli számítástechnikai környezetekben, például a Windows Virtual Desktopban barangoló profilokhoz készült. A bejelentkezéskor a tároló dinamikusan csatlakozik a számítástechnikai környezethez egy natív módon támogatott virtuális merevlemez (VHD) és egy Hyper-V virtuális merevlemez (VHDX) használatával. A felhasználói profil azonnal elérhető, és ugyanúgy jelenik meg a rendszeren, mint a natív felhasználói profil.

A következő táblázatok összehasonlítják a Storage Solutions Azure Storage-ajánlatokat a Windows rendszerű virtuális asztali FSLogix-profilok tárolójának felhasználói profiljaihoz.

## <a name="azure-platform-details"></a>Azure-platform – részletek

|Jellemzők|Azure Files|Azure NetApp Files|Közvetlen tárolóhelyek|
|--------|-----------|------------------|---------------------|
|Platform szolgáltatás|Igen, Azure-natív megoldás|Igen, Azure-natív megoldás|Nem, önállóan felügyelt|
|Regionális elérhetőség|0\. gyűrű, széles körű rendelkezésre állás|1\. gyűrű, jelenleg [legalább kilenc régióban](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all) elérhető|Az Azure-beli számítások által támogatott DC-régiók, a prémium szintű lemezek széles körben elérhetők és ajánlottak a Közvetlen tárolóhelyek|
|Redundancia|Helyileg redundáns/Zone-redundáns/geo-redundáns|Locally redundant (Helyileg redundáns)|Helyileg redundáns/Zone-redundáns/geo-redundáns|
|Rétegek és teljesítmény|Standard<br>Prémium<br>Akár 100 000 000 IOPS, 5 GB/s megosztással, körülbelül 3 MS késéssel|Standard<br>Prémium<br>Ultranagy<br>Akár 320k (16K) IOPS 4,5 GB/s-onként, körülbelül 1 MS késéssel|Standard HDD: legfeljebb 500 IOPS korlát<br>Standard SSD: legfeljebb 4k IOPS korlát<br>Prémium SSD: legfeljebb 20000 IOPS korlát|
|Kapacitás|100 TiB/megosztás|100 TiB/kötet, akár 12,5 PiB/előfizetés|Maximális 32 TiB/lemez|
|Szükséges infrastruktúra|Minimális megosztás mérete 1 GiB|Minimális kapacitási készlet 4 TiB, min. kötet mérete 100 GiB|Két virtuális gép az Azure IaaS (+ Felhőbeli tanúsító) vagy legalább három virtuális gép nélkül, valamint a lemezek költségei|
|Protokollok|SMB 2.1/3. és REST|NFSv3, NFSv 4.1 (előzetes verzió), SMB 3. x/2. x|NFSv3, NFSv 4.1, SMB 3,1|

## <a name="azure-management-details"></a>Azure-felügyelet részletei

|Jellemzők|Azure Files|Azure NetApp Files|Közvetlen tárolóhelyek|
|--------|-----------|------------------|---------------------|
|Hozzáférés|Felhő, helyszíni és hibrid (Azure file Sync)|Felhő, helyszíni (ExpressRoute-n keresztül)|Felhő, helyszíni|
|Azure Active Directory-integráció|Azure Active Directory és Azure Active Directory Domain Services|Azure Active Directory Domain Services és natív Active Directory|Natív Active Directory vagy Azure Active Directory Domain Services csak támogatás|
|Backup|Azure Backup pillanatkép-integráció|Pillanatképek Azure NetApp Files|Azure Backup pillanatkép-integráció|
|Biztonság és megfelelőség|[Az összes Azure által támogatott tanúsítvány](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|ISO befejezve|[Az összes Azure által támogatott tanúsítvány](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|
|Azure Active Directory-integráció|Azure Active Directory és Azure Active Directory Domain Services|[Azure Active Directory Domain Services és natív Active Directory](../azure-netapp-files/azure-netapp-files-faqs.md#does-azure-netapp-files-support-azure-active-directory)|Natív Active Directory vagy Azure Active Directory Domain Services csak támogatás|

Ha kiválasztotta a tárolási módszert, tekintse meg a [Windows rendszerű virtuális asztali díjszabást](https://azure.microsoft.com/pricing/details/virtual-desktop/) a díjszabási tervekkel kapcsolatos információkért.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a FSLogix, a felhasználói profil lemezekről és az egyéb felhasználói Profilos technológiákról, tekintse meg a [FSLogix-profilok és az Azure Files](fslogix-containers-azure-files.md)tábla táblázatát.

Ha készen áll a saját FSLogix-profil tárolóinak létrehozására, ismerkedjen meg az alábbi oktatóanyagok egyikével:

- [FSLogix-profilok tárolóinak első lépései a Windows rendszerű virtuális asztali Azure Files](https://techcommunity.microsoft.com/t5/Windows-IT-Pro-Blog/Getting-started-with-FSLogix-profile-containers-on-Azure-Files/ba-p/746477)
- [FSLogix-profil tároló létrehozása az Azure NetApp-fájlokat használó gazdagép-készletekhez](create-fslogix-profile-container.md)
- A [két csomópontos közvetlen tárolóhelyek kibővített fájlkiszolgáló üzembe helyezése az Azure-ban UPD-tárolóban](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment) című témakör útmutatása akkor is érvényes, ha a FSLogix-profil tárolóját használja a felhasználói profil lemeze helyett.

A kezdettől kezdve a Windows rendszerű [virtuális asztali bérlő létrehozása](tenant-setup-azure-active-directory.md)lehetőséggel is elindíthatja a saját Windowsos virtuális asztali megoldását.
