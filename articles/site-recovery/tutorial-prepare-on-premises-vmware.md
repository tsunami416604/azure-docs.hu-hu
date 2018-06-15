---
title: Helyszíni VMware-kiszolgálók előkészítése VMware virtuális gépek az Azure-ba való vészhelyreállításához | Microsoft Docs
description: Ismerje meg, hogyan készíthet elő VMware-kiszolgálókat az Azure-ba irányuló vészhelyreállításhoz az Azure Site Recovery szolgáltatással.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 02/07/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 4fecd5f8ddb4a6f432995a7779e29479b5b1a7c0
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2018
ms.locfileid: "29677515"
---
# <a name="prepare-on-premises-vmware-servers-for-disaster-recovery-to-azure"></a>Helyszíni VMware-kiszolgálók előkészítése az Azure-ba irányuló vészhelyreállításához

Az oktatóanyag bemutatja, hogyan készítse elő a helyszíni VMware-infrastruktúrát, ha replikálni szeretné a VMware virtuális gépeket az Azure-ba. Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Fiók előkészítése a vCenter-kiszolgálón vagy vSphere ESXi-gazdagépen a virtuális gépek felderítésének automatizálásához
> * Fiók előkészítése a mobilitási szolgáltatás VMware virtuális gépekre való automatikus telepítéséhez
> * A VMware-kiszolgáló követelményeinek áttekintése
> * A VMware virtuális gépek követelményeinek áttekintése

Ez az oktatóanyag-sorozat egyetlen virtuális gép az Azure Site Recovery használatával való biztonsági mentését mutatja be. Ha több VMware virtuális gép védelmét tervezi biztosítani, töltse le a [Deployment Planner eszközt](https://aka.ms/asr-deployment-planner) a VMware replikálásához. Az eszközzel információt gyűjthet a virtuális gép kompatibilitásáról, a lemezek virtuális gépenkénti számáról és a lemezenkénti adatváltozásról. Emellett az eszköz teljesíti a hálózati sávszélességre vonatkozó követelményeket, és lefedi a sikeres replikációhoz és feladatátvételi teszthez szükséges Azure-infrastruktúrát. [Itt részletesebben olvashat](site-recovery-deployment-planner.md) az eszköz futtatásáról.

Ez a sorozat második oktatóanyaga. Győződjön meg arról, hogy [beállította az Azure-összetevőket](tutorial-prepare-azure.md) az előző oktatóanyagban leírtak szerint.

## <a name="prepare-an-account-for-automatic-discovery"></a>Fiók előkészítése automatikus felderítéshez

A Site Recoverynek hozzáféréssel kell rendelkeznie a VMware-kiszolgálókhoz az alábbiak érdekében:

- A virtuális gépek automatikus felderítése. Szükség van legalább egy csak olvasási jogokat biztosító fiókra.
- Replikáció, feladatátvétel és feladat-visszavétel vezénylése. Szükség van egy fiókra, amely képes műveleteket futtatni, mint például lemezek létrehozása és eltávolítása, valamint virtuális gépek működtetése.

Hozza létre a fiókot az alábbiak szerint:

1. Dedikált fiók használatához hozzon létre egy szerepkört a vCenter-szinten. Adjon meg egy nevet, például: **Azure_Site_Recovery**.
2. Rendelje hozzá a szerepkörhöz az engedélyeket az alábbi táblázatban összefoglaltaknak megfelelően.
3. Hozzon létre egy felhasználót a vCenter-kiszolgálón vagy vSphere-gazdagépen. Rendelje hozzá a szerepkört a felhasználóhoz.

### <a name="vmware-account-permissions"></a>A VMware-fiók engedélyei

**Tevékenység** | **Szerepkör/Engedélyek** | **Részletek**
--- | --- | ---
**Virtuális gépek felderítése** | Legalább egy csak olvasási jogosultsággal rendelkező felhasználó<br/><br/> Adatközpont-objektum –> Gyermekobjektumba propagálás, szerepkör = csak olvasható | A felhasználó az adatközpontszinten hozzárendelve, és hozzáféréssel rendelkezik az adatközpontban lévő összes objektumhoz.<br/><br/> A hozzáférés korlátozásához rendelje a **Gyermekobjektumba propagálás** objektummal rendelkező **Nincs hozzáférés** szerepkört a gyermekobjektumokhoz (vSphere-gazdagépek, adattárolók, virtuális gépek és hálózatok).
**Teljes replikáció, feladatátvétel, feladat-visszavétel** |  Hozzon létre egy szerepkört (Azure_Site_Recovery) a szükséges engedélyekkel, majd rendelje hozzá a szerepkört egy VMware-felhasználóhoz vagy -csoporthoz<br/><br/> Adatközpont-objektum –> Gyermekobjektumba propagálás, szerepkör = Azure_Site_Recovery<br/><br/> Adattároló -> Terület lefoglalása, adattároló böngészése, alacsony szintű fájlműveletek, fájl eltávolítása, virtuális gépek fájljainak frissítése<br/><br/> Hálózat -> Hálózat hozzárendelése<br/><br/> Erőforrás -> Virtuális gép hozzárendelése az erőforráskészlethez, kikapcsolt virtuális gép migrálása, bekapcsolt virtuális gép migrálása<br/><br/> Feladatok -> Feladat létrehozása, feladat frissítése<br/><br/> Virtuális gép -> Konfiguráció<br/><br/> Virtuális gép -> Használat -> Kérdés megválaszolása, eszközkapcsolat, CD-adathordozó konfigurálása, hajlékonylemezes adathordozó, kikapcsolás, bekapcsolás, VMware-eszközök telepítése<br/><br/> Virtuális gép -> Leltár -> Létrehozás, regisztrálás, regisztráció törlése<br/><br/> Virtuális gép -> Üzembe helyezés -> Virtuális gép letöltésének engedélyezése, virtuálisgépfájlok feltöltésének engedélyezése<br/><br/> Virtuális gép -> Pillanatképek -> Pillanatképek eltávolítása | A felhasználó az adatközpontszinten hozzárendelve, és hozzáféréssel rendelkezik az adatközpontban lévő összes objektumhoz.<br/><br/> A hozzáférés korlátozásához rendelje a **Gyermekobjektumba propagálás** objektummal rendelkező **Nincs hozzáférés** szerepkört a gyermekobjektumokhoz (vSphere-gazdagépek, adattárolók, virtuális gépek és hálózatok).

## <a name="prepare-an-account-for-mobility-service-installation"></a>Fiók előkészítése a mobilitási szolgáltatás telepítéséhez

A mobilitási szolgáltatásnak telepítve kell lennie a replikálni kívánt virtuális gépen. A Site Recovery automatikusan telepíti ezt a szolgáltatást, ha engedélyezi a virtuális gép replikálását. Az automatikus telepítéshez elő kell készíteni egy fiókot, amelynek használatával a Site Recovery eléri a virtuális gépet. Ezt a fiókot kell megadnia, amikor beállítja a vészhelyreállítást az Azure-konzolon.

1. Készítsen elő egy tartományi vagy helyi fiókot, amely rendelkezik a virtuális gépre való telepítéshez szükséges engedélyekkel.
2. Ha Windows virtuális gépekre kíván telepíteni, de nem használ tartományi fiókot, tiltsa le a távoli felhasználói hozzáférés-vezérlést a helyi számítógépen.
   - Adja hozzá a **LocalAccountTokenFilterPolicy** DWORD bejegyzést a beállításjegyzékben a **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** területen, és állítsa az értékét 1-re.
3. Ha Linux virtuális gépekre kíván telepíteni, készítsen elő egy rendszergazdai fiókot a Linux-forráskiszolgálón.


## <a name="check-vmware-server-requirements"></a>A VMware-kiszolgáló követelményeinek ellenőrzése

Győződjön meg róla, hogy a VMware-kiszolgálók megfelelnek az alábbi követelményeknek.

**Összetevő** | **Követelmény**
--- | ---
**vCenter server** | vCenter 6.5, 6.0 vagy 5.5
**vSphere-gazdagép** | vSphere 6.5, 6.0, 5.5

## <a name="check-vmware-vm-requirements"></a>A VMware virtuális gépek követelményeinek ellenőrzése

Győződjön meg arról, hogy a virtuális gép megfelel az alábbi táblázatban összefoglalt Azure-követelményeknek.

**Virtuális gép követelményei** | **Részletek**
--- | ---
**Operációsrendszer-lemez mérete** | Legfeljebb 2048 GB-ig.
**Operációsrendszer-lemezek száma** | 1
**Adatlemezek száma** | 64 vagy kevesebb
**Adatlemez virtuális merevlemezének mérete** | Legfeljebb 4095 GB-ig
**Hálózati adapterek** | Több adapter támogatott
**Megosztott VHD** | Nem támogatott
**FC-lemez** | Nem támogatott
**Merevlemez formátuma** | VHD vagy VHDX.<br/><br/> Bár a VHDX jelenleg nem támogatott az Azure-ban, a Site Recovery automatikusan átalakítja a VHDX-et VHD-re, amikor feladatátvételt hajt végre az Azure-ba. Amikor feladatátvételt hajt végre a helyszíni virtuális gépekre, használja továbbra is a VHDX formátumot.
**Bitlocker** | Nem támogatott. Tiltsa le, mielőtt engedélyezi a virtuális gépek replikálását.
**Virtuális gép neve** | 1 és 63 karakter között.<br/><br/> Csak betűket, számokat és kötőjelet tartalmazhat. A virtuális gép nevének betűvel vagy számmal kell kezdődnie és végződnie.
**Virtuális gép típusa** | 1. generációs – Linux vagy Windows<br/><br/>2. generációs – csak Windows

A virtuális gépen valamelyik támogatott operációs rendszernek is futnia kell. A támogatott verziók teljes listájához tekintse meg a következőt: [Site Recovery támogatási mátrix](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions).

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Felkészülés az Azure virtuális gépekhez való kapcsolódásra a feladatátvételt követően

Feladatátviteli forgatókönyv során az Azure-beli replikált virtuális gépekhez a helyszíni hálózatból érdemes csatlakozni.

Ha a feladatátvételt követően RDP segítségével szeretne kapcsolódni a Windows virtuális gépekhez, tegye a következőket:

1. Ha internetes hozzáférést kíván használni, engedélyezze az RDP-t a helyszíni virtuális gépen a feladatátvétel előtt. Ellenőrizze, hogy a **Nyilvános** résznél felvette-e a listára a TCP- és UDP-szabályokat, valamint hogy a **Windows tűzfal** > **Engedélyezett alkalmazások** területén az összes profil számára engedélyezve van-e az RDP.
2. Ha helyek közötti VPN-kapcsolatot kíván használni, engedélyezze az RDP-t a helyszíni gépen. Engedélyezze az RDP-t a **Windows tűzfal** -> **Engedélyezett alkalmazások és szolgáltatások** területén a **Tartomány és Privát** hálózatok számára.
   Ellenőrizze, hogy az operációs rendszer tárolóhálózati szabályzata **OnlineAll** értékre van-e állítva. [További információk](https://support.microsoft.com/kb/3031135). A virtuális gépen nem lehetnek függőben lévő Windows-frissítések a feladatátvétel elindításakor. Ha vannak, a frissítés befejeződéséig nem fog tudni bejelentkezni a virtuális gépre.
3. A feladatátvételt követően ellenőrizze a **Rendszerindítási diagnosztika** részt a Windows Azure virtuális gépen a virtuális gép képernyőképének megtekintéséhez. Ha nem sikerül, ellenőrizze, hogy fut-e a virtuális gép, majd tekintse át a [hibaelhárítási tippeket](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Ha a feladatátvételt követően SSH segítségével szeretne kapcsolódni a Linux virtuális gépekhez, tegye a következőket:

1. A helyszíni gépen, a feladatátvétel előtt ellenőrizze, hogy beállította-e, hogy a Secure Shell szolgáltatás rendszerindításkor automatikusan elinduljon. Ellenőrizze, hogy a tűzfalszabályok engedélyezik-e az SSH-kapcsolatot.

2. A feladatátvételt követően engedélyezze az SSH-porthoz beérkező kapcsolatokat az Azure-beli virtuális gépen a feladatátviteli virtuális gépen lévő hálózati biztonsági csoport szabályaihoz és az Azure-alhálózathoz, amelyhez csatlakozik.
   [Nyilvános IP-cím hozzáadása](site-recovery-monitoring-and-troubleshooting.md) a virtuális gép számára. Ellenőrizheti a **Rendszerindítási diagnosztika** részt a virtuális gép képernyőképének megtekintéséhez.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az Azure-ba irányuló vészhelyreállítás beállítása VMware virtuális gépekhez](tutorial-vmware-to-azure.md)
