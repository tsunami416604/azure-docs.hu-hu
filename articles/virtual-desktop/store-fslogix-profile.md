---
title: FSLogix profiltároló tárolása Windows Virtual Desktop – Azure
description: A Windows Virtual Desktop FSLogix-profil Azure Storage-ban való tárolásának lehetőségei.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 71ba24784dee7771acbe19bf0261c7dc02478b24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127516"
---
# <a name="storage-options-for-fslogix-profile-containers-in-windows-virtual-desktop"></a>Az FSLogix profiltárolók tárolási lehetőségei a Windows Virtual Desktop rendszerben

Az Azure több tárolási megoldást kínál, amelyek segítségével tárolhatja az FSLogix profiltárolóját. Ez a cikk összehasonlítja azokat a tárolási megoldásokat, amelyeket az Azure a Windows Virtual Desktop FSLogix felhasználói profiltárolóihoz kínál.

A Windows Virtual Desktop az FSLogix profiltárolókat kínálja az ajánlott felhasználói profilmegoldásként. Az FSLogix-ot úgy tervezték, hogy profilokat barangoljon távoli számítástechnikai környezetekben, például a Windows Virtual Desktop területén. Bejelentkezéskor ez a tároló dinamikusan csatlakozik a számítógépes környezethez egy natívan támogatott virtuális merevlemez (VHD) és egy Hyper-V virtuális merevlemez (VHDX) használatával. A felhasználói profil azonnal elérhető, és pontosan úgy jelenik meg a rendszerben, mint egy natív felhasználói profil.

Az alábbi táblázatok összehasonlítják az Azure Storage által a Windows Virtual Desktop FSLogix profiltároló felhasználói profilokhoz kínálott tárolási megoldásokat.

## <a name="azure-platform-details"></a>Az Azure platform részletei

|Szolgáltatások|Azure Files|Azure NetApp Files|Tárolóhelyek – Közvetlen|
|--------|-----------|------------------|---------------------|
|Használati eset|Általános célú|Ultra teljesítmény vagy áttelepítés a NetApp-ról a helyszínen|Platformfüggetlen|
|Platform szolgáltatás|Igen, az Azure natív megoldása|Igen, az Azure natív megoldása|Nem, saját irányítású|
|Régiónkénti rendelkezésre állás|Minden régió|[Régiók kiválasztása](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)|Minden régió|
|Redundancia|Helyileg redundáns/zónaredundáns/georedundáns|Helyileg redundáns|Helyileg redundáns/zónaredundáns/georedundáns|
|Szintek és teljesítmény|Standard<br>Prémium<br>Akár max 100k IOPS egy részvényre jutó 5 GBps egy részvényre jutó körülbelül 3 ms késleltetés|Standard<br>Prémium<br>Ultra<br>Akár 320 k (16K) IOPS 4,5 GBp/s térfogattal, körülbelül 1 ms késleltetéssel|Szabványos HDD: lemezenként akár 500 IOPS-korlát<br>Szabványos SSD: lemezenként 4k IOPS-ig<br>Prémium Szintű SSD: lemezenként akár 20 k IOPS-korlátozás<br>Prémium lemezek közvetlen tárolóhelyekhez ajánlott|
|Kapacitás|100 TiB részvényenként|100 TiB mennyiségenként, előfizetésenként akár 12,5 PiB|Legfeljebb 32 TiB lemezenként|
|Szükséges infrastruktúra|Minimális megosztási méret 1 GiB|Minimális kapacitású medence 4 TiB, min térfogatméret 100 GiB|Két virtuális gép az Azure IaaS-on (+ Cloud Witness) vagy legalább három virtuális gép lemezek nélkül és lemezek költségei nélkül|
|Protokollok|SMB 2.1/3. és REST|NFSv3, NFSv4.1 (előzetes verzió), SMB 3.x/2.x|NFSv3, NFSv4.1, SMB 3.1|

## <a name="azure-management-details"></a>Az Azure-kezelés részletei

|Szolgáltatások|Azure Files|Azure NetApp Files|Tárolóhelyek – Közvetlen|
|--------|-----------|------------------|---------------------|
|Hozzáférés|Felhő, helyszíni és hibrid (Azure-fájlszinkronizálás)|Felhő, helyszíni (ExpressRoute-on keresztül)|Felhő, helyszíni|
|Backup|Azure biztonsági mentési pillanatkép-integrációja|Az Azure NetApp-fájlok pillanatképei|Azure biztonsági mentési pillanatkép-integrációja|
|Biztonság és megfelelőség|[Minden Azure által támogatott tanúsítvány](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|ISO befejeződött|[Minden Azure által támogatott tanúsítvány](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|
|Azure Active Directory-integráció|[Natív Active Directory és Az Azure Active Directory tartományi szolgáltatások](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-overview)|[Azure Active Directory tartományi szolgáltatások és natív Active Directory](../azure-netapp-files/azure-netapp-files-faqs.md#does-azure-netapp-files-support-azure-active-directory)|A natív Active Directory vagy az Azure Active Directory tartományi szolgáltatások támogatása|

Miután kiválasztotta a tárolási módot, tekintse meg a [Windows virtuális asztal díjszabását](https://azure.microsoft.com/pricing/details/virtual-desktop/) az árképzési csomagjainkkal kapcsolatos információkért.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az FSLogix profiltárolókról, a felhasználói profillemezekről és más felhasználói profiltechnológiákról, tekintse meg az [FSLogix profiltárolók és az Azure-fájlok táblázatát.](fslogix-containers-azure-files.md)

Ha készen áll saját FSLogix profiltárolók létrehozására, első lépések az alábbi oktatóanyagok egyikével:

- [Az FSLogix profiltárolók első lépései az Azure Files rendszerben a Windows Virtual Desktop rendszerben](https://techcommunity.microsoft.com/t5/Windows-IT-Pro-Blog/Getting-started-with-FSLogix-profile-containers-on-Azure-Files/ba-p/746477)
- [FSLogix-profiltároló létrehozása gazdagépkészlethez Az Azure NetApp-fájljainak használatával](create-fslogix-profile-container.md)
- A [kétcsomópontos tárolóhelyek központi telepítése közvetlen kibővített fájlkiszolgáló azure-beli telepítése az Azure-ban](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/) című útmutatóban is érvényes, ha felhasználói profillemez helyett FSLogix-profiltárolót használ

A kezdetektől fogva beállítható saját Windows virtuális asztal megoldása a Bérlő létrehozása a Windows virtuális asztalon című [verzióban.](tenant-setup-azure-active-directory.md)
