---
title: Storage FSLogix-profil tároló Windows virtuális asztal – Azure
description: A Windows rendszerű virtuális asztali FSLogix-profil Azure Storage-beli tárolásának lehetőségei.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4bb315991846b115070854b6e81caf16232fef6b
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2020
ms.locfileid: "82607181"
---
# <a name="storage-options-for-fslogix-profile-containers-in-windows-virtual-desktop"></a>Tárolási beállítások a Windows rendszerű virtuális asztali FSLogix-profilok tárolói számára

Az Azure több tárolási megoldást is kínál, amelyek segítségével tárolhatja a FSLogix-profil tárolóját. Ez a cikk összehasonlítja azokat a tárolási megoldásokat, amelyeket az Azure kínál a Windows rendszerű virtuális asztali FSLogix felhasználói profilok tárolói számára.

A Windows rendszerű virtuális asztal az ajánlott felhasználói profil-megoldásként kínál FSLogix-profilokat. A FSLogix távoli számítástechnikai környezetekben, például a Windows Virtual Desktopban barangoló profilokhoz készült. A bejelentkezéskor a tároló dinamikusan csatlakozik a számítástechnikai környezethez egy natív módon támogatott virtuális merevlemez (VHD) és egy Hyper-V virtuális merevlemez (VHDX) használatával. A felhasználói profil azonnal elérhető, és ugyanúgy jelenik meg a rendszeren, mint a natív felhasználói profil.

A következő táblázatok összehasonlítják a Storage Solutions Azure Storage-ajánlatokat a Windows rendszerű virtuális asztali FSLogix-profilok tárolójának felhasználói profiljaihoz.

## <a name="azure-platform-details"></a>Azure-platform – részletek

|Szolgáltatások|Azure Files|Azure NetApp Files|Tárolóhelyek – Közvetlen|
|--------|-----------|------------------|---------------------|
|Használati eset|Általános célú|Ultra teljesítmény vagy Migrálás a helyszíni NetApp-ből|Platformfüggetlen|
|Platform szolgáltatás|Igen, Azure-natív megoldás|Igen, Azure-natív megoldás|Nem, önállóan felügyelt|
|Régiónkénti rendelkezésre állás|Minden régió|[Régiók kiválasztása](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)|Minden régió|
|Redundancia|Helyileg redundáns/Zone-redundáns/geo-redundáns|Helyileg redundáns|Helyileg redundáns/Zone-redundáns/geo-redundáns|
|Rétegek és teljesítmény|Standard<br>Prémium<br>Akár 100 000 000 IOPS, 5 GB/s megosztással, körülbelül 3 MS késéssel|Standard<br>Prémium<br>Ultranagy<br>Akár 320k (16K) IOPS 4,5 GB/s-onként, körülbelül 1 MS késéssel|Standard HDD: legfeljebb 500 IOPS korlát<br>Standard SSD: legfeljebb 4k IOPS korlát<br>Prémium SSD: legfeljebb 20000 IOPS korlát<br>A prémium szintű lemezeket ajánlott Közvetlen tárolóhelyek|
|Kapacitás|100 TiB/megosztás|100 TiB/kötet, akár 12,5 PiB/előfizetés|Maximális 32 TiB/lemez|
|Szükséges infrastruktúra|Minimális megosztás mérete 1 GiB|Minimális kapacitási készlet 4 TiB, min. kötet mérete 100 GiB|Két virtuális gép az Azure IaaS (+ Felhőbeli tanúsító) vagy legalább három virtuális gép nélkül, valamint a lemezek költségei|
|Protokollok|SMB 2.1/3. és REST|NFSv3, NFSv 4.1 (előzetes verzió), SMB 3. x/2. x|NFSv3, NFSv 4.1, SMB 3,1|

## <a name="azure-management-details"></a>Azure-felügyelet részletei

|Szolgáltatások|Azure Files|Azure NetApp Files|Tárolóhelyek – Közvetlen|
|--------|-----------|------------------|---------------------|
|Hozzáférés|Felhő, helyszíni és hibrid (Azure file Sync)|Felhő, helyszíni (ExpressRoute-n keresztül)|Felhő, helyszíni|
|Backup|Azure Backup pillanatkép-integráció|Pillanatképek Azure NetApp Files|Azure Backup pillanatkép-integráció|
|Biztonság és megfelelőség|[Az összes Azure által támogatott tanúsítvány](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|ISO befejezve|[Az összes Azure által támogatott tanúsítvány](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|
|Azure Active Directory-integráció|[Natív Active Directory és Azure Active Directory Domain Services](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-overview)|[Azure Active Directory Domain Services és natív Active Directory](../azure-netapp-files/azure-netapp-files-faqs.md#does-azure-netapp-files-support-azure-active-directory)|Natív Active Directory vagy Azure Active Directory Domain Services csak támogatás|

Ha kiválasztotta a tárolási módszert, tekintse meg a [Windows rendszerű virtuális asztali díjszabást](https://azure.microsoft.com/pricing/details/virtual-desktop/) a díjszabási tervekkel kapcsolatos információkért.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a FSLogix, a felhasználói profil lemezekről és az egyéb felhasználói Profilos technológiákról, tekintse meg a [FSLogix-profilok és az Azure Files](fslogix-containers-azure-files.md)tábla táblázatát.

Ha készen áll a saját FSLogix-profil tárolóinak létrehozására, ismerkedjen meg az alábbi oktatóanyagok egyikével:

- [FSLogix-profilok tárolóinak első lépései a Windows rendszerű virtuális asztali Azure Files](https://techcommunity.microsoft.com/t5/Windows-IT-Pro-Blog/Getting-started-with-FSLogix-profile-containers-on-Azure-Files/ba-p/746477)
- [FSLogix-profil tároló létrehozása az Azure NetApp-fájlokat használó gazdagép-készletekhez](create-fslogix-profile-container.md)
- A [két csomópontos közvetlen tárolóhelyek kibővített fájlkiszolgáló üzembe helyezése az Azure-ban UPD-tárolóban](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/) című témakör útmutatása akkor is érvényes, ha a FSLogix-profil tárolóját használja a felhasználói profil lemeze helyett.

A kezdettől kezdve a Windows rendszerű [virtuális asztali bérlő létrehozása](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)lehetőséggel is elindíthatja a saját Windowsos virtuális asztali megoldását.
