---
title: "Helyszíni VMware-kiszolgálók előkészítése a vész-helyreállítási VMware virtuális gépek Azure-bA |} Microsoft Docs"
description: "Útmutató a helyszíni VMware-kiszolgálók előkészítése a vész-helyreállítási az Azure-bA az Azure Site Recovery szolgáltatással."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 90a4582c-6436-4a54-a8f8-1fee806b8af7
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: af09c5602c53be4377ba19e68ff3486bcfefe0ea
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2018
---
# <a name="prepare-on-premises-vmware-servers-for-disaster-recovery-to-azure"></a>Helyszíni VMware-kiszolgálók előkészítése az Azure-bA katasztrófa utáni helyreállítás

Az oktatóanyag bemutatja, hogyan készíti elő a helyszíni VMware-infrastruktúra, ha VMware virtuális gépek replikálása az Azure-bA. Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Készítse elő a vCenter-kiszolgáló vagy vSphere ESXi-állomáson, automatizálhatja a virtuális gép felderítési fiókkal
> * Egy fiók előkészítése a mobilitási szolgáltatást a VMware virtuális gépek automatikus telepítése
> * VMware-kiszolgáló követelményeinek áttekintése
> * VMware virtuális gép követelményeinek áttekintése

A oktatóanyag sorozat megmutatjuk, hogyan biztonsági mentése egy egyetlen virtuális gép Azure Site Recovery segítségével. Ha azt tervezi, hogy több VMware virtuális gépek védelméhez, le kell töltenie a [telepítési Planner eszköz](https://aka.ms/asr-deployment-planner) a VMware-replikáció. Ez az eszköz gyűjtse össze a virtuális gép kompatibilitási, virtuális gépenként lemezeket, és lemezenként adatforgalommal kapcsolatos információk. Az eszköz is magában foglalja az sávszélességre van szükség, és az Azure-infrastruktúra szükséges sikeres replikáció és a teszt feladatátvételhez. [További](site-recovery-deployment-planner.md) az eszköz futtatásával kapcsolatos.

Ez az a sorozat második oktatóanyaga. Győződjön meg arról, hogy rendelkezik [beállítása az Azure összetevők](tutorial-prepare-azure.md) az előző oktatóanyag leírtak szerint.

## <a name="prepare-an-account-for-automatic-discovery"></a>Az automatikus felderítési fiók előkészítése

A Site Recovery VMware-kiszolgálókhoz való hozzáférésre van szüksége:

- Automatikusan észleli a virtuális gépek. Legalább egy csak olvasható fiókot kell megadni.
- Levezényelni a replikálása, feladatátvétele és feladat-visszavételre. Egy fiók futtatható virtuális gépek műveletek, például a létrehozása és lemezek eltávolítása és működtetéséhez szükséges.

A fiók létrehozásához az alábbiak szerint:

1. Egy külön fiókot használatához hozzon létre egy szerepkört a vCenter szinten. Nevezze el a szerepkör például **Azure_Site_Recovery**.
2. A szerepkör hozzárendelése az engedélyeket, az alábbi táblázat foglalja össze.
3. Felhasználó létrehozása a vCenter-kiszolgáló vagy vSphere-gazdagép. A szerepkör hozzárendeléséhez a felhasználóhoz.

### <a name="vmware-account-permissions"></a>VMware fiókjához hozzárendelt jogosultságoktól

**Tevékenység** | **Szerepkör-engedélyek** | **Részletek**
--- | --- | ---
**Virtuális gép felderítése** | Legalább egy írásvédett felhasználó<br/><br/> Adatközpont objektum –> propagálása gyermekobjektum, a szerepkör = csak olvasható | Felhasználói datacenter szinten hozzárendelt, és rendelkezik hozzáféréssel az objektumokhoz az adatközpontban.<br/><br/> A hozzáférés korlátozása érdekében rendelje hozzá a **nem lehet hozzáférni** szerepkört a **gyermekre propagálása** objektum, a gyermekobjektumokra (vSphere-gazdagép, datastores, virtuális gépek és hálózatok).
**A teljes replikáció, a feladatátvétel, a feladat-visszavétel** |  (Azure_Site_Recovery) szerepkör létrehozása a szükséges engedélyekkel, és hozzárendelheti a szerepkör egy VMware-felhasználó vagy csoport<br/><br/> Adatközpont objektum –> propagálása gyermekobjektum, a szerepkör = Azure_Site_Recovery<br/><br/> Datastore foglaljon le terület ->, keresse meg az adattároló, alacsony szintű fájlműveletek, távolítsa el a fájlt, frissítse a virtuális géphez tartozó fájlokat<br/><br/> Hálózat -> hálózat hozzárendelése<br/><br/> Erőforrás -> rendelje hozzá a virtuális gép erőforráskészlethez, energiaforrással rendelkező ki a virtuális gép áttelepítése, energiaforrással rendelkező, a virtuális gép áttelepítése<br/><br/> Feladatok -> hozzon létre feladat, a frissítési feladat<br/><br/> Virtuális gép konfigurációja -><br/><br/> Virtuálisgép -> kommunikáció -> válasz kérdést, az eszköz kapcsolat, a CD media konfigurálásához, a konfigurálásához a hajlékonylemezes adathordozót, kapcsolja ki, a bekapcsolás, VMware-eszközök telepítése<br/><br/> Virtuálisgép -> leltár -> létrehozási, regisztrálása, unregister<br/><br/> Virtuálisgép -> kiépítés engedélyezése virtuális gép letöltési ->, hogy a virtuálisgép-fájlok feltöltése<br/><br/> Virtuális gép pillanatkép ->-Remove pillanatképek > | Felhasználói datacenter szinten hozzárendelt, és rendelkezik hozzáféréssel az objektumokhoz az adatközpontban.<br/><br/> A hozzáférés korlátozása érdekében rendelje hozzá a **nem lehet hozzáférni** szerepkört a **gyermekre propagálása** objektum, a gyermekobjektumokra (vSphere-gazdagép, datastores, virtuális gépek és hálózatok).

## <a name="prepare-an-account-for-mobility-service-installation"></a>Készítse elő a fiók a mobilitási szolgáltatás telepítése

A mobilitási szolgáltatást telepítenie kell a replikálni kívánt virtuális Gépre. A Site Recovery automatikusan telepíti ezt a szolgáltatást, ha engedélyezi a virtuális gép replikálása. Automatikus telepítés elő kell készíteni egy Site Recovery eléréséhez a virtuális gép által használt fiók. Ezt a fiókot kell megadnia, az Azure-konzolon a vész-helyreállítási beállításakor.

1. Készítsen elő egy tartományi vagy helyi fiók telepítése a virtuális gépen.
2. Telepítse a Windows virtuális gépeken, ha nem használ egy olyan tartományi fiók, tiltsa le a távoli felhasználói hozzáférés-vezérlés a helyi számítógépen.
   - A beállításkulcs alatt **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, adja hozzá a DWORD bejegyzést **LocalAccountTokenFilterPolicy**, 1 értékű.
3. Linux virtuális gépek telepítéséhez előkészítése a forráskiszolgálón Linux a rendszergazdafiók.


## <a name="check-vmware-server-requirements"></a>Ellenőrizze a VMware-kiszolgáló követelményei

Győződjön meg arról, hogy a VMware Server az alábbi követelményeknek.

**Összetevő** | **Követelmény**
--- | ---
**vCenter server** | vCenter 6.5, 6.0 vagy 5.5
**vSphere-gazdagép** | vSphere 6.5, 6.0, 5.5

## <a name="check-vmware-vm-requirements"></a>VMware virtuális gép követelményeinek ellenőrzéséhez

Győződjön meg arról, hogy a virtuális gép megfelel-e az Azure-követelmények az alábbi táblázat foglalja össze.

**Virtuális gép követelmény** | **Részletek**
--- | ---
**Operációs rendszert tároló lemez mérete** | Legfeljebb 2048 GB.
**Operációs rendszer lemez száma** | 1
**Adatlemez** | 64 vagy kevesebb
**Adattároló lemez virtuális merevlemez mérete** | Legfeljebb 4095 GB
**Hálózati adapterek** | Több adapter támogatottak.
**Megosztott virtuális merevlemez** | Nem támogatott
**FC-lemez** | Nem támogatott
**Merevlemez formátuma** | VHD- vagy VHDX.<br/><br/> Bár VHDX jelenleg nem támogatott az Azure-ban, a Site Recovery automatikusan átalakítja VHDX virtuális merevlemezre történő feladatátadást követően Azure-bA. Ha visszaadják feladataikat a helyszíni virtuális gépek továbbra is használhatja a VHDX formátumú.
**Bitlocker** | Nem támogatott. Tiltsa le a virtuális gépek replikációjának engedélyezése előtt.
**Virtuális gép neve** | 1 és 63 karakter közötti.<br/><br/> Kizárólag betűket, számokat és kötőjeleket tartalmazhat. A virtuális gép nevét kell kezdődnie, és betűvel vagy számmal végződhet.
**Virtuálisgép-típussá** | Az 1 - Linux vagy a Windows<br/><br/>Csak Windows - a 2. generációs

A virtuális gép is futnia kell egy támogatott operációs rendszert. Tekintse meg a [Site Recovery támogatási mátrix](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) a támogatott verziók teljes listáját.

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Felkészülés az Azure virtuális gépekhez való kapcsolódásra a feladatátvételt követően

Egy feladatátvevő forgatókönyv során érdemes lehet az Azure-ban a replikált virtuális gépek csatlakozni a helyi hálózatról.

Windows virtuális gépek a feladatátvételt követően RDP segítségével csatlakozhat, tegye a következőket:

1. Szeretne használni az interneten keresztül, engedélyezze az RDP a feladatátvétel előtt a helyszíni virtuális Gépre. Győződjön meg arról, hogy a TCP és UDP szabályok hozzáadása történik meg a a **nyilvános** profilt, és, hogy engedélyezi-e az RDP a **Windows tűzfal** > **engedélyezett alkalmazások** minden profil esetében.
2. Pont-pont VPN-kapcsolaton keresztüli elérésére engedélyezze az RDP a helyi számítógépen. Engedélyezni kell az RDP a **Windows tűzfal** -> **engedélyezett alkalmazások és szolgáltatások** a **tartomány és a saját** hálózatok.
   Ellenőrizze, hogy az operációs rendszer TÁROLÓHÁLÓZATI szabályzatát állítsa a **OnlineAll**. [További információk](https://support.microsoft.com/kb/3031135). Lehetnek nincs függőben lévő Windows-frissítéseket a virtuális Gépre amikor feladatátvételt indít el. Ha van, nem fogja tudni bejelentkezni a virtuális gép csak a frissítés befejeződése után.
3. Ellenőrizze a Windows Azure virtuális Gépen a feladatátvételt követően, **rendszerindítási diagnosztika** egy Képernyőkép a virtuális Gépet a megtekintéséhez. Ha nem sikerül, ellenőrizze, hogy fut-e a virtuális Gépet, és tekintse át a [hibaelhárítási tippek](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Linux virtuális gépek a feladatátvételt követően SSH használatával csatlakozhat, tegye a következőket:

1. A feladatátvétel előtt a helyi számítógépen ellenőrizze, hogy a Secure Shell szolgáltatás rendszerindításkor automatikus indításra van-e beállítva. Ellenőrizze, hogy a tűzfalszabályok engedélyezik-e az SSH-kapcsolat.

2. A Azure virtuális Gépen a feladatátvételt követően az SSH-port a feladatait átadó virtuális gép hálózati biztonsági csoportszabályok, valamint az Azure-alhálózaton, amelyhez csatlakozik a bejövő kapcsolatok tiltása.
   [A nyilvános IP-cím hozzáadása](site-recovery-monitoring-and-troubleshooting.md) a virtuális gép számára. Ellenőrizheti a **rendszerindítási diagnosztika** egy Képernyőkép a virtuális Gépet a megtekintéséhez.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [VMware virtuális gépek Azure-bA vész-helyreállítási beállítása](tutorial-vmware-to-azure.md)
