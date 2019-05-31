---
title: FSLogix profil tárolók és az Azure Files a Windows virtuális asztal – Azure
description: Ez a cikk ismerteti a Windows virtuális asztalok és az Azure files FSLogix profil tárolókra.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: v-chjenk
ms.openlocfilehash: c3f31e8d260ea5e462e8782fadd9f61f34d03add
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66307272"
---
# <a name="fslogix-profile-containers-and-azure-files"></a>FSLogix-profiltárolók és Azure Files

A Windows Virtual Desktop előzetes verziójú szolgáltatások FSLogix profil tárolók felhasználói profil megoldás használatát javasolja. FSLogix barangolhassanak a távoli számítási környezetekben, például Windows virtuális asztali profilok tervezték. Az egyetlen tároló tárolja a teljes felhasználói profil. Bejelentkezéskor a tároló dinamikusan csatlakoztatva van a számítási környezet natív, a Vendég virtuális merevlemez (VHD) és a Hyper-V virtuális merevlemezek lemez (VHDX) a Microsoft services használatával. A felhasználói profil azonnal elérhető, és a rendszer natív felhasználói profil hasonlóan jelenik meg.

Ebben a cikkben az Azure Files használt FSLogix profil tárolókat ismertetjük. Windows virtuális asztal, amely összefüggésben van az információk [3/21 meghirdetett](https://www.microsoft.com/microsoft-365/blog/2019/03/21/windows-virtual-desktop-public-preview/).

## <a name="user-profiles"></a>Felhasználói profilok

A felhasználói profil adatait elemeket kapcsolatos egyéni, beleértve a konfigurációs adatok, például az asztal beállításait, a hálózati kapcsolatokat és az alkalmazás beállításait tartalmazza. Alapértelmezés szerint a Windows létrehoz egy helyi felhasználói profilt, amely szorosan integrálva van az operációs rendszer.

A távoli felhasználói profilra biztosít egy partíció felhasználói adatok és az operációs rendszer között. Lehetővé teszi az operációs rendszer vagy a felhasználói adatok műveletekért nélkül módosítható. A távoli asztali munkamenetgazda (RDSH) és a virtuális asztali infrastruktúra (VDI) az operációs rendszer lecserélhető a következő okok miatt:

- Az operációs rendszer frissítése
- A helyettesítő egy meglévő virtuális gép (VM)
- Egy felhasználó egy készletezett (nem állandó) RDSH vagy VDI-környezetek részeként folyamatban van

Microsoft-termékek működik a távoli felhasználói profilokhoz, többek között ezek a technológiák számos technológiát:
- Barangoló felhasználói profilok (központi felhasználói profil)
- Lemezeket (UPD)
- Vállalati állapothordozás (ESR)

UPD és központi felhasználói Profilhoz a felhasználói profilok, a távoli asztali munkamenetgazda (RDSH) és a virtuális merevlemez (VHD) környezetekben a leggyakrabban használt technológiákat.

### <a name="challenges-with-previous-user-profile-technologies"></a>A korábbi felhasználói profil technológiákkal kihívásai

A meglévő és az örökölt Microsoft megoldások felhasználói profilok különféle kihívások dokumentációját. Nincs előző megoldás kezeli az összes felhasználói profil igényeinek, amely egy távoli asztali Munkamenetgazda vagy VDI környezetet kapható. Például UPD OST nagy méretű fájlok nem tudja kezelni, és a központi felhasználói Profilhoz modern beállítások nem maradnak.

#### <a name="functionality"></a>Funkció

Az alábbi táblázat előnyök és korlátozások az előző felhasználói profilok technológiák.

| Technológia | A modern beállításai | A Win32-beállítások | Operációs rendszer beállításai | Felhasználói adatok | Támogatott SKU-kiszolgálón | Háttér-tároló az Azure-ban | Helyszíni háttér-tároló | Támogatott verziók | További bejelentkezési idő |Megjegyzések|
| ---------- | :-------------: | :------------: | :---------: | --------: | :---------------------: | :-----------------------: | :--------------------------: | :-------------: | :---------------------: |-----|
| **Lemezeket (UPD)** | Igen | Igen | Igen | Igen | Igen | Nem | Igen | Win 7 + | Igen | |
| **Barangoló felhasználói profil (központi felhasználói Profilhoz), karbantartási mód** | Nem | Igen | Igen | Igen | Igen| Nem | Igen | Win 7 + | Nem | |
| **Vállalati Állapothordozás (ESR)** | Igen | Nem | Igen | Nem | Lásd: a megjegyzések | Igen | Nem | Win 10 | Nem | A kiszolgáló Termékváltozat, de nem támogató felhasználói felület funkciók |
| **Felhasználói élmény virtualizációs (UE-V)** | Igen | Igen | Igen | Nem | Igen | Nem | Igen | Win 7 + | Nem |  |
| **Felhőbeli OneDrive-fájlok** | Nem | Nem | Nem | Igen | Lásd: a megjegyzések | Lásd: a megjegyzések  | Lásd: a megjegyzések | Win 10 RS3 | Nem | Nem tesztelt SKU-kiszolgálón. Háttér-tároló az Azure-ban a Szinkronizáló ügyfél függ. Helyszíni háttér-tároló a Szinkronizáló ügyfél van szüksége. |

#### <a name="performance"></a>Teljesítmény

UPD igényel [közvetlen tárolóhelyek (S2D)](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment) cím teljesítmény követelményeit. UPD Server Message Block (SMB) protokollt használja. Másolja a profil a virtuális gép, ahol a felhasználó már bejelentkezett. Az S2D UPD volt a megoldás a távoli asztali szolgáltatások csapat ajánlott a Windows virtuális asztal a szolgáltatás az előzetes verzióban.  

#### <a name="cost"></a>Költségek

Amíg a szükséges teljesítmény eléréséhez az S2D-fürt költsége, a vállalati ügyfelek a költséges, de a különösen költséges, kis és közepes üzleti (SMB) ügyfelek számára. Ebben a megoldásban vállalkozások prémium szintű tárolólemezeket, valamint a megosztás a lemezeket használó virtuális gépek díját kell fizetnie.

#### <a name="administrative-overhead"></a>Adminisztrációs terhelés

Az S2D-fürt szükséges az operációs rendszer, amely javítani, frissítve, és egy biztonságos állapotban tartani. Ezeket a folyamatokat és az S2D-t vész-helyreállítási beállítása összetettségét győződjön meg arról, S2D-t csak azokra a vállalatokra egy dedikált informatikai munkatársak megvalósítható.

## <a name="fslogix-profile-containers"></a>FSLogix profil tárolók

2018. November 19 [Microsoft megszerzett FSLogix](https://blogs.microsoft.com/blog/2018/11/19/microsoft-acquires-fslogix-to-enhance-the-office-365-virtualization-experience/). FSLogix címek számos profil tároló kihívásokat, kulcsfontosságú, többek között a következők:

- **Teljesítmény:** A [FSLogix profil tárolók](https://fslogix.com/products/profile-containers) nagy teljesítményű és megoldása teljesítményproblémát, amelyekkel korábban letiltotta a gyorsítótáras exchange üzemmódot.
- **OneDrive:** Nélkül FSLogix profil tárolók a OneDrive vállalati verzióba nem állandó RDSH vagy VDI-környezetekben nem támogatott. [OneDrive vállalati verzió és minden FSLogix ajánlott eljárások](https://fslogix.com/products/technical-faqs/284-onedrive-for-business-and-fslogix-best-practices) ismerteti, hogyan használják. További információkért lásd: [használja a Szinkronizáló ügyfél virtuális asztali](https://docs.microsoft.com/deployoffice/rds-onedrive-business-vdi).
- **További mappák:** FSLogix lehetővé teszi a felhasználói profilok meghatározásához, hogy további mappákat kiterjesztését.

A beszerzési óta a Microsoft lépések FSLogix profil tárolók meglévő felhasználói profil megoldások, például az UDP, és cserélje le.

## <a name="azure-files-integration-with-azure-active-directory"></a>Az Azure Files integrációja az Azure Active Directory

FSLogix teljesítményadatainak tárolók és szolgáltatások a felhő előnyeit. 2018. szeptember 24., a Microsoft Azure Files meghirdetett nyilvános előzetes verziója [támogatása az Azure Active Directory-hitelesítés az Azure Files](https://azure.microsoft.com/blog/azure-active-directory-integration-for-smb-access-now-in-public-preview/). Kielégítésével költségeket és az adminisztratív terhelést, az Azure Active Directory-hitelesítés az Azure Files felhasználói profilok az új virtuális asztali Windows-szolgáltatás prémium szintű megoldás is.

## <a name="best-practices-for-windows-virtual-desktop"></a>Ajánlott eljárások a Windows virtuális asztal

Windows virtuális asztal méretű, típusú és ügyfelek által használt virtuális gépek száma feletti teljes körű vezérlést biztosít. További információkért lásd: [mi Windows virtuális asztal előzetes verziója?](https://docs.microsoft.com/azure/virtual-desktop/overview).

Annak biztosítása érdekében a Windows virtuális asztali környezetben következő ajánlott eljárásokat:

- Az Azure Files storage-fiókot a munkamenetgazda virtuális gépeket ugyanabban a régióban kell lennie.
- Azure Files-engedélyek meg kell egyeznie a fent leírt engedélyeket [követelmények - profil tárolók](https://docs.fslogix.com/display/20170529/Requirements+-+Profile+Containers).
- Minden gazdagép címkészlet kell elkészíteni, ugyanilyen típusú és méretű virtuális gép ugyanazon a fő rendszerkép alapján.
- Mindegyik gazdagép gyűjtő virtuális gép, ezzel elősegítve a felügyeleti, méretezése és frissítését ugyanabban az erőforráscsoportban kell lennie.
- Az optimális teljesítmény érdekében a tárolási megoldásnak és a profil tárolóban kell lennie, ugyanazokat az adatokat a FSLogix adatközpont elhelyezkedése határozza meg.
- A tárfiók, amely tartalmazza a fő rendszerkép kell azonos régióban és előfizetéshez, ahol a virtuális gépek lettek létrehozva.

## <a name="next-steps"></a>További lépések

Egy Windows virtuális asztali környezet beállításához kövesse az alábbi utasításokat.

- Az asztali virtualizálási megoldás kiépítésének megkezdéséhez lásd [bérlő létrehozása a Windows virtuális asztal](https://docs.microsoft.com/azure/virtual-desktop/tenant-setup-azure-active-directory).
- A Windows virtuális asztal bérlőn belül gazdagép készlet létrehozásához lásd: [gazdagép-készlet létrehozása az Azure Marketplace](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-azure-marketplace).
- Beállításához teljes körűen felügyelt fájlmegosztások felhőalapú, lásd: [beállítása az Azure-fájlmegosztási](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-enable).
- FSLogix profil tárolók beállítása: [állítsa be a felhasználói állomás készlet megosztásának](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-user-profile).
- Felhasználók hozzárendelése egy gazdagép-készletet, lásd: [alkalmazáscsoportok kezelése a Windows virtuális asztal](https://docs.microsoft.com/azure/virtual-desktop/manage-app-groups).
- A virtuális asztali Windows-erőforrások eléréséhez egy webböngészőben, lásd: [csatlakozhat Windows virtuális asztal](https://docs.microsoft.com/azure/virtual-desktop/connect-web).
