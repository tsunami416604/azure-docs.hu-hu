---
title: Windows Virtual Desktop FSLogix profiltárolófájlok – Azure
description: Ez a cikk a Windows Virtual Desktop és az Azure-fájlokFSLogix-profiltárolóit ismerteti.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 1dc5d54fa24217c91e14a8f37e092888b2bb6474
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127881"
---
# <a name="fslogix-profile-containers-and-azure-files"></a>FSLogix-profiltárolók és Azure Files

A Windows Virtual Desktop szolgáltatás az FSLogix profiltárolókat ajánlja felhasználói profilmegoldásként. Az FSLogix-ot úgy tervezték, hogy profilokat barangoljon távoli számítástechnikai környezetekben, például a Windows Virtual Desktop területén. Egy teljes felhasználói profilt tárol egyetlen tárolóban. Bejelentkezéskor ez a tároló dinamikusan csatlakozik a számítógépes környezethez a natívan támogatott virtuális merevlemez (VHD) és a Hyper-V virtual hard disk (VHDX) használatával. A felhasználói profil azonnal elérhető, és pontosan úgy jelenik meg a rendszerben, mint egy natív felhasználói profil. Ez a cikk azt ismerteti, hogy az Azure Files funkcióval használt FSLogix profiltárolók hogyan használhatók a Windows virtuális asztalon.

>[!NOTE]
>Ha az Azure-beli FSLogix-profiltároló különböző tárolási lehetőségeivel kapcsolatos összehasonlító anyagokat keres, olvassa el [az FSLogix-profiltárolók tárolási beállításai című témakört.](store-fslogix-profile.md)

## <a name="user-profiles"></a>Felhasználói profilok

A felhasználói profilok egy adott személy adatelemeit tartalmazzák, beleértve az olyan konfigurációs adatokat, mint az asztal beállításai, az állandó hálózati kapcsolatok és az alkalmazásbeállítások. Alapértelmezés szerint a Windows olyan helyi felhasználói profilt hoz létre, amely szorosan integrálva van az operációs rendszerrel.

A távoli felhasználói profil partíciót biztosít a felhasználói adatok és az operációs rendszer között. Lehetővé teszi az operációs rendszer cseréjét vagy módosítását a felhasználói adatok befolyásolása nélkül. A Távoli asztali munkamenetgazda (RDSH) és a Virtuális asztali infrastruktúra (VDI) rendszerben az operációs rendszer a következő okok miatt cserélhető le:

- Az operációs rendszer frissítése
- Meglévő virtuális gép (VM) cseréje
- A felhasználó egy készletkészletes (nem állandó) RDSH vagy VDI környezet része

A Microsoft-termékek számos technológiát alkalmaznak a távoli felhasználói profilokhoz, beleértve az alábbi technológiákat is:
- Központi felhasználói profilok (RUP)
- Felhasználói profillemezek (UPD)
- Vállalati állami barangolás (ESR)

Az UPD és az RUP a legszélesebb körben használt technológiák a felhasználói profilokhoz a Távoli asztali munkamenetgazda (RDSH) és a Virtuális merevlemez (VHD) környezetekben.

### <a name="challenges-with-previous-user-profile-technologies"></a>Kihívások a korábbi felhasználói profiltechnológiákkal

A felhasználói profilokhoz meglévő és régebbi Microsoft-megoldások különböző kihívásokkal rendelkeztek. Nincs korábbi megoldás kezelte az összes olyan felhasználói profilt, amely RDSH vagy VDI környezetben érkezik. Az UPD például nem tudja kezelni a nagy méretű OST-fájlokat, és az RUP nem marad meg a modern beállításokban.

#### <a name="functionality"></a>Funkció

Az alábbi táblázat a korábbi felhasználói profiltechnológiák előnyeit és korlátait mutatja be.

| Technológia | Modern beállítások | Win32 beállítások | Operációs rendszer beállításai | Felhasználói adatok | Kiszolgálótermék-változat támogatott | Háttéralapú tárolás az Azure-ban | Háttér-tároló a helyszínen | Verziótámogatás | Későbbi bejelentkezési idő |Megjegyzések|
| ---------- | :-------------: | :------------: | :---------: | --------: | :---------------------: | :-----------------------: | :--------------------------: | :-------------: | :---------------------: |-----|
| **Felhasználói profil lemezei (UPD)** | Igen | Igen | Igen | Igen | Igen | Nem | Igen | Nyerj 7+ | Igen | |
| **Központi felhasználói profil (RUP), karbantartási mód** | Nem | Igen | Igen | Igen | Igen| Nem | Igen | Nyerj 7+ | Nem | |
| **Vállalati állami barangolás (ESR)** | Igen | Nem | Igen | Nem | Lásd a jegyzeteket | Igen | Nem | Győzelem 10 | Nem | Funkciók a kiszolgálótermékváltozaton, de nincs támogató felhasználói felület |
| **Felhasználói élmény virtualizálása (UE-V)** | Igen | Igen | Igen | Nem | Igen | Nem | Igen | Nyerj 7+ | Nem |  |
| **OneDrive felhőfájlok** | Nem | Nem | Nem | Igen | Lásd a jegyzeteket | Lásd a jegyzeteket  | Lásd: Megjegyzések | Nyerj 10 RS3 | Nem | Nincs tesztelve a kiszolgáló termékváltozatán. Háttér-tároló az Azure-ban a szinkronizálási ügyfélfügg. Háttér-tároló on-prem szüksége van egy szinkronizálási ügyfél. |

#### <a name="performance"></a>Teljesítmény

Az UPD-nek a közvetlen [tárolóhelyekre (S2D)](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/) van szükség a teljesítménykövetelmények teljesítéséhez. Az UPD a Server Message Block (SMB) protokollt használja. Másolja a profilt a virtuális gépre, amelyben a felhasználó naplózása alatt van. Upd s2d a megoldás javasoljuk a Windows Virtual Desktop.  

#### <a name="cost"></a>Költségek

Bár az S2D-fürtök elérik a szükséges teljesítményt, a költség költséges a vállalati ügyfelek számára, de különösen a kis- és középvállalkozások (SMB) ügyfelei számára. Ehhez a megoldásért a vállalkozások fizetnek a tárolólemezekért, valamint a lemezeket egy megosztáshoz használó virtuális gépek költségeiért.

#### <a name="administrative-overhead"></a>Nagyobb adminisztrációs terhelés

Az S2D-fürtökhez olyan operációs rendszerre van szükség, amelyet biztonságos állapotban javítanak, frissítenek és karbantartanak. Ezek a folyamatok és az S2D vész-helyreállítási rendszer létrehozásának összetettsége miatt az S2D csak a dedikált informatikai személyzettel rendelkező vállalatok számára valósítható meg.

## <a name="fslogix-profile-containers"></a>FSLogix profiltárolók

2018. november [19-én a Microsoft felvásárolta az FSLogix-ot.](https://blogs.microsoft.com/blog/2018/11/19/microsoft-acquires-fslogix-to-enhance-the-office-365-virtualization-experience/) Az FSLogix számos profiltároló-kihívással foglalkozik. Ezek közül a legfontosabbak a következők:

- **Teljesítmény:** Az [FSLogix profiltárolók](/fslogix/configure-profile-container-tutorial/) nagy teljesítményűek, és olyan teljesítményproblémákat oldanak meg, amelyek korábban blokkolták a gyorsítótárazott cseremódot.
- **OneDrive:** FSLogix profiltárolók nélkül a OneDrive Vállalati verzió nem támogatott nem állandó RDSH- vagy VDI-környezetekben. [A OneDrive Vállalati verzió és az FSLogix gyakorlati tanácsok ismertetik](/fslogix/overview/) az interakciót. További információt [a Szinkronizálási ügyfélalkalmazás használata virtuális asztalokon című témakörben talál.](/deployoffice/rds-onedrive-business-vdi/)
- **További mappák:** Az FSLogix lehetővé teszi a felhasználói profilok kiterjesztését további mappákra.

Az akvizíció óta a Microsoft elkezdte helyettesíteni a meglévő felhasználói profilmegoldásokat, például az UPD-t FSLogix profiltárolókkal.

## <a name="azure-files-integration-with-azure-active-directory-domain-service"></a>Az Azure-fájlok integrációja az Azure Active Directory tartományi szolgáltatással

Az FSLogix profiltárolók teljesítménye és szolgáltatásai kihasználják a felhő előnyeit. 2019. augusztus 7-én a Microsoft Azure Files bejelentette az Azure Files-hitelesítés általános elérhetőségét az [Azure Active Directory tartományi szolgáltatással (AD DS).](../storage/files/storage-files-active-directory-overview.md) A költségek és a felügyeleti költségek kezelése, az Azure Files az Azure AD DS-hitelesítés egy prémium megoldás a felhasználói profilok a Windows virtuális asztali szolgáltatás.

## <a name="best-practices-for-windows-virtual-desktop"></a>Gyakorlati tanácsok a Windows virtuális asztalhoz

A Windows virtuális asztal teljes mértékben szabályozhatja az ügyfelek által használt virtuális gépek méretét, típusát és számát. További információ: [Mi a Windows virtuális asztal?](overview.md)

Annak biztosítása érdekében, hogy a Windows virtuális asztal környezete kövesse az ajánlott eljárásokat:

- Az Azure Files tárfióknak ugyanabban a régióban kell lennie, mint a munkamenetgazda virtuális gépei.
- Az Azure Files-engedélyeknek meg kell egyezniük a [Követelmények – Profiltárolók](/fslogix/fslogix-storage-config-ht)című részben leírt engedélyekkel.
- Minden gazdakészlet kell építeni az azonos típusú és méretű virtuális gép alapján ugyanazt a fő lemezképet.
- Minden gazdagépkészlet virtuális gépének ugyanabban az erőforráscsoportban kell lennie a kezelés, a méretezés és a frissítés támogatásához.
- Az optimális teljesítmény érdekében a tárolási megoldásnak és az FSLogix profiltárolónak ugyanazon az adatközponti helyen kell lennie.
- A fő lemezképet tartalmazó tárfióknak ugyanabban a régióban és előfizetésben kell lennie, ahol a virtuális gépek kivannak kiépítve.

## <a name="next-steps"></a>További lépések

Az alábbi útmutatók segítségével windowsos virtuális asztal környezetet állíthat be.

- Az asztali virtualizálási megoldás kiépítésének megkezdéséről a Bérlő létrehozása a Windows virtuális asztalon című [témakörben található.](tenant-setup-azure-active-directory.md)
- Ha gazdagépkészletet szeretne létrehozni a Windows virtuális asztal bérlőjében, olvassa el a Host pool létrehozása az [Azure Marketplace-en című témakört.](create-host-pools-azure-marketplace.md)
- Ha teljes körűen felügyelt fájlmegosztásokat szeretne beállítani a felhőben, olvassa [el az Azure-fájlok megosztásának beállítása](/azure/storage/files/storage-files-active-directory-enable/)című témakört.
- Az FSLogix profiltárolók konfigurálásához olvassa el a [Profiltároló létrehozása egy gazdagépkészlethez fájlmegosztás használatával című témakört.](create-host-pools-user-profile.md)
- Ha felhasználókat szeretne hozzárendelni egy gazdakészlethez, olvassa el [a Windows Virtuális asztal alkalmazáscsoportjainak kezelése](manage-app-groups.md).
- A Windows virtuális asztal erőforrásainak webböngészőből történő eléréséről a [Csatlakozás a Windows virtuális asztalhoz (Csatlakozás a Windows virtuális asztalhoz)](connect-web.md)témakörben talál.
