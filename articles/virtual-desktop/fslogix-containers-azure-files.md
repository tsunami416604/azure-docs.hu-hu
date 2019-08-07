---
title: FSLogix-profilok és Azure Files a Windows rendszerű virtuális asztali környezetekben – Azure
description: Ez a cikk a Windows rendszerű virtuális asztali és az Azure Files FSLogix-profilok tárolóit ismerteti.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: helohr
ms.openlocfilehash: c01e138c8afcdd59fcb0c87f189d98bec10e16d7
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68828142"
---
# <a name="fslogix-profile-containers-and-azure-files"></a>FSLogix-profiltárolók és Azure Files

A Windows rendszerű virtuális asztali előnézet szolgáltatás felhasználói profil-megoldásként javasolja a FSLogix-profilok tárolóit. A FSLogix távoli számítástechnikai környezetekben, például a Windows Virtual Desktopban barangoló profilokhoz készült. Egy teljes felhasználói profilt tárol egyetlen tárolóban. Bejelentkezéskor a tároló dinamikusan csatlakozik a számítástechnikai környezethez a natív módon támogatott virtuális merevlemez (VHD) és a Hyper-V virtuális merevlemez (VHDX) használatával. A felhasználói profil azonnal elérhető, és ugyanúgy jelenik meg a rendszeren, mint a natív felhasználói profil.

Ebben a cikkben a Azure Fileshoz használt FSLogix-profilok tárolóit ismertetjük. Az információ a Windows rendszerű virtuális asztal kontextusában jelent meg, amelyet [a 3/21](https://www.microsoft.com/microsoft-365/blog/2019/03/21/windows-virtual-desktop-public-preview/)-es verzióban jelentettek be.

## <a name="user-profiles"></a>Felhasználói profilok

A felhasználói profil egy egyedi adatelemet tartalmaz, beleértve a konfigurációs adatokat, például az asztali beállításokat, az állandó hálózati kapcsolatokat és az alkalmazás beállításait. Alapértelmezés szerint a Windows egy helyi felhasználói profilt hoz létre, amely szorosan integrálódik az operációs rendszerbe.

A távoli felhasználói profil egy partíciót biztosít a felhasználói és az operációs rendszer között. Lehetővé teszi az operációs rendszer lecserélését vagy módosítását anélkül, hogy ez hatással lenne a felhasználói értékekre. Távoli asztal munkamenet-gazdagép (RDSH) és a virtuális asztali infrastruktúrák (VDI) esetében az operációs rendszer a következő okok miatt váltható fel:

- Az operációs rendszer frissítése
- Meglévő virtuális gép (VM) cseréje
- Egy felhasználó egy készletezett (nem állandó) RDSH vagy VDI-környezet részét képezi

A Microsoft-termékek számos technológiával működnek a távoli felhasználói profilokhoz, beleértve a következő technológiákat:
- Barangoló felhasználói profilok (központi felhasználói PROFILHOZ)
- Felhasználói profil lemezei (UPD)
- Vállalati állapot barangolása (ESR)

A UPD és a központi felhasználói PROFILHOZ a legszélesebb körben használt technológiák a Távoli asztal munkamenet-gazdagépen (RDSH) és a virtuális merevlemezen (VHD) lévő felhasználói profilokhoz.

### <a name="challenges-with-previous-user-profile-technologies"></a>A korábbi felhasználói profil technológiáinak kihívásai

A meglévő és a régi Microsoft-megoldások a felhasználói profilokhoz különböző kihívásokkal jártak. Egyetlen korábbi megoldás sem kezelte az összes olyan felhasználói profilt, amely RDSH vagy VDI-környezettel rendelkezik. Például a UPD nem tudja kezelni a nagyméretű OST-fájlokat, és a központi felhasználói PROFILHOZ nem őrzi meg a modern beállításokat.

#### <a name="functionality"></a>Funkció

Az alábbi táblázat a korábbi felhasználói profilok technológiáinak előnyeit és korlátozásait mutatja be.

| Technológia | Modern beállítások | Win32-beállítások | Operációs rendszer beállításai | Felhasználói adatok | Kiszolgáló SKU-ban támogatott | Háttérbeli tároló az Azure-ban | Helyszíni háttérbeli tároló | Verziók támogatása | Későbbi bejelentkezési idő |Megjegyzések|
| ---------- | :-------------: | :------------: | :---------: | --------: | :---------------------: | :-----------------------: | :--------------------------: | :-------------: | :---------------------: |-----|
| **Felhasználói profil lemezei (UPD)** | Igen | Igen | Igen | Igen | Igen | Nem | Igen | 7\. győzelem + | Igen | |
| **Barangoló felhasználói profil (központi felhasználói PROFILHOZ), karbantartási mód** | Nem | Igen | Igen | Igen | Igen| Nem | Igen | 7\. győzelem + | Nem | |
| **Enterprise State Roaming (ESR)** | Igen | Nem | Igen | Nem | Lásd: megjegyzések | Igen | Nem | 10. győzelem | Nem | A Server SKU-ban lévő függvények, de nem támogatott felhasználói felület |
| **Felhasználói élmény virtualizálása (UE-V)** | Igen | Igen | Igen | Nem | Igen | Nem | Igen | 7\. győzelem + | Nem |  |
| **OneDrive Cloud Files** | Nem | Nem | Nem | Igen | Lásd: megjegyzések | Lásd: megjegyzések  | Lásd: megjegyzések | 10 RS3 | Nem | Nem tesztelt a kiszolgálói SKU-ban. Az Azure-beli háttérbeli tárterület a szinkronizálási ügyféltől függ. Az on-back-end tárolónak szinkronizálási ügyfélre van szüksége. |

#### <a name="performance"></a>Teljesítmény

A UPD a teljesítményre vonatkozó követelmények kielégítéséhez [közvetlen tárolóhelyek (S2D)](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment) szükséges. A UPD Server Message Block (SMB) protokollt használ. Átmásolja a profilt arra a virtuális gépre, amelyben a felhasználó naplózva van. A UPD with S2D volt a megoldás, amelyet az RDS csapat ajánlott a Windows virtuális asztal számára a szolgáltatás előzetes verziójában.  

#### <a name="cost"></a>Költség

Bár a S2D-fürtök a szükséges teljesítményt érik el, a nagyvállalati ügyfelek számára költséges költség, különösen a kis-és közepes méretű vállalkozások (SMB) ügyfelei számára. Ebben a megoldásban a vállalatok a Storage-lemezekért fizetnek, valamint a megosztások lemezeit használó virtuális gépek költségeit.

#### <a name="administrative-overhead"></a>Adminisztratív terhelés

A S2D-fürtök olyan operációs rendszert igényelnek, amely egy javított, frissített és karbantartott, biztonságos állapotban van. Ezek a folyamatok és a S2D vész-helyreállítás beállításának összetettsége csak a dedikált informatikai személyzettel rendelkező vállalatok számára teszi elérhetővé a S2D.

## <a name="fslogix-profile-containers"></a>FSLogix-profilok tárolói

2018. november 19-én a Microsoft megszerezte a [FSLogix](https://blogs.microsoft.com/blog/2018/11/19/microsoft-acquires-fslogix-to-enhance-the-office-365-virtualization-experience/). A FSLogix számos profillal kapcsolatos kihívást jelent. A kulcsok közül a következők:

- **Teljesítmény** A [FSLogix-profil tárolói](https://fslogix.com/products/profile-containers) nagy teljesítményű és olyan teljesítménnyel kapcsolatos problémák megoldására szolgálnak, amelyek történelmileg letiltották a gyorsítótáras Exchange üzemmódot.
- **OneDrive** FSLogix-profilok nélkül a OneDrive for Business nem állandó RDSH-vagy VDI-környezetekben nem támogatott. [A OneDrive for Business és az FSLogix ajánlott eljárásai](https://fslogix.com/products/technical-faqs/284-onedrive-for-business-and-fslogix-best-practices) ismertetik, hogyan hatnak egymásra. További információ: [a szinkronizálási ügyfél használata a virtuális asztalokon](https://docs.microsoft.com/deployoffice/rds-onedrive-business-vdi).
- **További mappák:** A FSLogix lehetővé teszi a felhasználói profilok kiterjesztését további mappák befoglalására.

A beszerzés óta a Microsoft megkezdte a meglévő felhasználói profil-megoldások, például a UPD FSLogix-profilok tárolására való lecserélését.

## <a name="azure-files-integration-with-azure-active-directory"></a>Azure Files integráció a Azure Active Directory

A FSLogix-profil tárolóinak teljesítménye és funkciói kihasználják a felhő előnyeit. A 2018-es szeptember 24-én Microsoft Azure fájlok nyilvános előzetes kiadást jelentettek [Azure Files támogató Azure Active Directory hitelesítéshez](https://azure.microsoft.com/blog/azure-active-directory-integration-for-smb-access-now-in-public-preview/). A költségek és az adminisztrációs terhelések kezelésével a Azure Files a Azure Active Directory-hitelesítéssel az új Windows virtuális asztali szolgáltatásban található felhasználói profilokhoz tartozó prémium megoldás.

## <a name="best-practices-for-windows-virtual-desktop"></a>Ajánlott eljárások a Windows rendszerű virtuális asztali gépekhez

A Windows rendszerű virtuális asztali szolgáltatás teljes körű vezérlést biztosít az ügyfelek által igénybe vett virtuális gépek méretének, típusának és számának. További információ: [Mi az a Windows rendszerű virtuális asztali előnézet?](https://docs.microsoft.com/azure/virtual-desktop/overview).

Annak biztosítása érdekében, hogy a Windows rendszerű virtuális asztali környezet az ajánlott eljárásokat követi:

- Azure Files Storage-fióknak ugyanabban a régióban kell lennie, mint a munkamenet-gazdagép virtuális gépei.
- Azure Files engedélyeknek meg kell egyezniük a [követelmények – profil tárolók](https://docs.fslogix.com/display/20170529/Requirements+-+Profile+Containers)című cikkben ismertetett engedélyekkel.
- Mindegyik gazdagépnek azonos típusú és méretű virtuális gépről kell kiépíteni, ugyanazon fő rendszerkép alapján.
- Minden egyes gazdagép-készlet virtuális gépnek ugyanabban az erőforráscsoporthoz kell tartoznia, hogy segítse a felügyeletet, a skálázást és a frissítést.
- Az optimális teljesítmény érdekében a tárolási megoldásnak és a FSLogix-profil tárolójának ugyanabban az adatközpont-helyen kell lennie.
- A fő lemezképet tartalmazó Storage-fióknak ugyanabban a régióban és előfizetésben kell lennie, ahol a virtuális gépek üzembe lettek kiépítve.

## <a name="next-steps"></a>További lépések

A Windows rendszerű virtuális asztali környezet beállításához kövesse az alábbi utasításokat.

- Az asztali virtualizálási megoldás létrehozásának megkezdéséhez lásd: [bérlő létrehozása a Windows rendszerű virtuális asztalon](https://docs.microsoft.com/azure/virtual-desktop/tenant-setup-azure-active-directory).
- A Windows rendszerű virtuális asztali bérlőn belüli gazdagépek létrehozásával kapcsolatban lásd: [állomáslista létrehozása az Azure Marketplace-szel](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-azure-marketplace).
- A teljes körűen felügyelt fájlmegosztás felhőben való beállításával kapcsolatban lásd: [Azure Files megosztás beállítása](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-enable).
- A FSLogix-profilok tárolóinak konfigurálásával kapcsolatban lásd: [felhasználói profil megosztásának beállítása egy gazdagéphez](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-user-profile).
- A felhasználók egy gazdagéphez való hozzárendelésével kapcsolatban lásd: [Manage app groups for Windows Virtual Desktop](https://docs.microsoft.com/azure/virtual-desktop/manage-app-groups).
- A Windows rendszerű virtuális asztali erőforrások webböngészőből való eléréséhez tekintse meg a [Kapcsolódás a Windows rendszerű virtuális asztalhoz](https://docs.microsoft.com/azure/virtual-desktop/connect-web)című témakört.
