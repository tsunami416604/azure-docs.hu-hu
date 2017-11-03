---
title: "A feladatátvétel és a sikertelen biztonsági Hyper-V virtuális gépek replikálása az Azure Site Recovery szolgáltatással |} Microsoft Docs"
description: "Útmutató az Azure-bA Hyper-V virtuális gépek a feladatátvétel, és visszaadják feladataikat a helyszíni hely Azure Site Recovery szolgáltatással"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 72065d01-ed0f-430e-b117-a5885cecd1db
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/15/2017
ms.author: raynew
ms.openlocfilehash: e1cc21661450a983c25b24fe2a6228e26ceecec6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-azure"></a>A feladatátvétel és a sikertelen biztonsági Hyper-V virtuális gépek Azure-felhőbe replikált

A [Azure Site Recovery](site-recovery-overview.md) szolgáltatás kezeli, és koordinálja a replikációt, a feladatátvételi és a feladat-visszavétel a helyszíni gépeket, és az Azure virtuális gépek (VM).

Ez az oktatóanyag az Azure-bA a Hyper-V virtuális gépek a feladatátvétel ismerteti. Miután keresztül korábban meghiúsult, hogy visszaadják feladataikat a helyszíni hely a rendelkezésre álló. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Feladatok átadása a Hyper-V virtuális gépek helyszíni Azure-bA
> * Az Azure-ból a helyszíni nem sikerül, és indítsa el újra replikálása Azure-bA

## <a name="overview"></a>Áttekintés
A feladatátvételi és a feladat-visszavétel van két fázisból áll:

1. **Feladatok átadása a Azure**: feladatátadást gépek a helyszíni helyről.
2. **Sikertelen az Azure-ból a helyszíni**: tervezett feladatátvétel futtatása az Azure-ból a helyszíni. A tervezett feladatátvétel után a helyszíni történő replikálása Azure újra elindítani a fordított replikáció engedélyezése. 


## <a name="fail-over-to-azure"></a>Feladatok átadása a Azure

### <a name="failover-prerequisites"></a>Feladatátvételi Előfeltételek

Segítségével befejezheti a feladatátvételt:

- Győződjön meg arról, hogy befejezte a [vész-helyreállítási részletezési](tutorial-dr-drill-azure.md) ellenőrizze, hogy minden a várt módon működik.
- Esetleg készítse elő a átadása Azure virtuális gépeken való kapcsolódáshoz.
- Ellenőrizze a virtuális gép tulajdonságait, a feladatátvétel végrehajtása előtt.

#### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Felkészülés az Azure virtuális gépekhez való kapcsolódásra a feladatátvételt követően

Ha szeretne csatlakozni az Azure virtuális gépek a feladatátvételt követően RDP segítségével, tegye a következőket:

##### <a name="azure-vms-running-windows"></a>Windows operációs rendszert futtató Azure virtuális gépek

1. Azure virtuális gépek eléréséhez az interneten keresztül, engedélyezze az RDP a feladatátvétel előtt a helyszíni virtuális Gépre. Győződjön meg arról, hogy a TCP és UDP szabályok hozzáadása történik meg a a **nyilvános** profilt, és, hogy engedélyezi-e az RDP a **Windows tűzfal** > **engedélyezett alkalmazások** minden profil esetében.
2. Pont-pont VPN-kapcsolaton keresztüli elérésére engedélyezze az RDP a helyi számítógépen. Engedélyezni kell az RDP a **Windows tűzfal** -> **engedélyezett alkalmazások és szolgáltatások** a **tartomány és a saját** hálózatok. Ellenőrizze, hogy az operációs rendszer TÁROLÓHÁLÓZATI szabályzatát állítsa a **OnlineAll**. [További információk](https://support.microsoft.com/kb/3031135). Lehetnek nincs függőben lévő Windows-frissítéseket a virtuális Gépre amikor feladatátvételt indít el. Ha van, nem fogja tudni bejelentkezni a virtuális gép csak a frissítés befejeződése után. 
3. Ellenőrizze a Windows Azure virtuális Gépen a feladatátvételt követően, **rendszerindítási diagnosztika** egy Képernyőkép a virtuális Gépet a megtekintéséhez. Ha nem sikerül, ellenőrizze, hogy fut-e a virtuális Gépet, és tekintse át a [hibaelhárítási tippek](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


##### <a name="azure-vms-running-linux"></a>Linuxos Azure virtuális gépek

1. A feladatátvétel előtt a helyi számítógépen ellenőrizze, hogy a Secure Shell szolgáltatás rendszerindításkor automatikus indításra van-e beállítva. Ellenőrizze, hogy a tűzfalszabályok engedélyezik-e az SSH-kapcsolat.
2. A Azure virtuális Gépen a feladatátvételt követően az SSH-port a feladatait átadó virtuális gép hálózati biztonsági csoportszabályok, valamint az Azure-alhálózaton, amelyhez csatlakozik a bejövő kapcsolatok tiltása.  [A nyilvános IP-cím hozzáadása](site-recovery-monitoring-and-troubleshooting.md#adding-a-public-ip-on-a-resource-manager-virtual-machine) a virtuális gép számára. Ellenőrizheti a **rendszerindítási diagnosztika** egy Képernyőkép a virtuális Gépet a megtekintéséhez.


#### <a name="verify-vm-properties"></a>Ellenőrizze a virtuális gép tulajdonságai

Ellenőrizze a virtuális gép tulajdonságait, és győződjön meg arról, hogy a virtuális gép megfelel-e az [Azure-követelményeknek](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

1. A **védett elemek**, kattintson a **replikált elemek** > virtuális gép.
2. Az a **replikált elemek** ablaktáblában nincs Virtuálisgép-adatok, állapotát, összegzését, és a legújabb elérhető helyreállítási pontok. Kattintson a **tulajdonságok** megtekintheti annak további részleteit.
3. A **számítás és hálózat**, módosíthatja a Azure nevét, az erőforráscsoport, a célméretet, [rendelkezésre állási csoport](../virtual-machines/windows/tutorial-availability-sets.md), és [lemezbeállításokat felügyelt](#managed-disk-considerations)
4. Megtekintheti és módosíthatja a hálózati beállításokat, beleértve a hálózatot/alhálózatot, amelyben az Azure virtuális Gépen található után feladatátvétel és a hozzá rendelt IP-cím.
5. A **lemezek**, az operációs rendszer és az adatlemezek információkat tekintheti meg a virtuális Gépen.


### <a name="run-a-failover-from-on-premises-to-azure"></a>Az Azure-bA helyszíni feladatátvételt

Hyper-V virtuális gépek is futtathatja egy rendszeres vagy tervezett feladatátvétel

- Váratlan leállás rendszeres feladatátvevő használja. Ennél a feladatátvételnél futtatásakor a Site Recovery hoz létre egy Azure virtuális gép, és amely rendszert működtet azt. A feladatátvételi futtatni egy adott helyreállítási pontot. Adatvesztés fordulhat elő, attól függően, hogy a helyreállítási pont használja.
- Egy tervezett feladatátvételt a karbantartás vagy várt leállás során használható. Ez a beállítás nulla adatveszteséget biztosít. Egy tervezett feladatátvételt akkor váltódik ki, ha a forrás virtuális gépek állnak le. Nem szinkronizált adatok szinkronizálása, és akkor váltódik ki, a feladatátvételt.

Ez az eljárás rendszeres feladatátvételt ismerteti.


1. A **beállítások** > **replikált elemek** kattintson a virtuális gép > **feladatátvételi**.
2. A **feladatátvételi** válassza ki a **helyreállítási pont** feladatokat. Az alábbi lehetőségek egyikét használhatja:
    - **Legújabb** (alapértelmezett): Ez a beállítás először dolgozza fel a Site Recovery számára küldött minden adathoz. Mert az Azure virtuális gép létrehozása után feladatátvételi rendelkezik minden az adatok replikálása a Site Recovery szolgáltatásban a feladatátvétel kiváltásakor a legalacsonyabb helyreállítási Időkorlát (Helyreállításipont-célkitűzés) biztosít.
    - **Legújabb feldolgozott**: Ez a beállítás átadja a feladatokat a virtuális gép a legutóbbi helyreállítási pontot, a Site Recovery által feldolgozott. Ezt a lehetőséget biztosít egy alacsony RTO (helyreállítási idő célkitűzése), mert nincs van feldolgozásával töltött idő feldolgozatlan adatokat.
    - **Legutóbbi alkalmazáskonzisztens**: Ez a beállítás átadja a feladatokat a virtuális Gépet, a Site Recovery által feldolgozott legújabb alkalmazáskonzisztens helyreállítási pontnak. 
    - **Egyéni**: Adjon meg egy helyreállítási pontot.
3. Ha a System Center VMM-felhőkben, az Azure-ba, a Hyper-V virtuális gépek keresztül visszavétele és engedélyezett az adattitkosítás a felhő a **titkosítási kulcs**, válassza ki a tanúsítványt, ha engedélyezte az adattitkosítást a szolgáltató telepítése során a korábban kiadott a VMM-kiszolgálón...
4. Válassza ki **gép leállítása a feladatátvétel megkezdése előtt** Ha azt szeretné, hogy a hely helyreállításával lehet engedélyezi ezt a forrás virtuális gépek leállítása a feladatátvétel elindítása előtt. A Site Recovery megpróbál is, hogy a felhasználó még nem küldte el az Azure-ba, ahhoz, hogy kiváltsa a feladatátvétel a helyszíni adatok szinkronizálása. Vegye figyelembe, hogy a feladatátvétel továbbra is fennáll, akkor is, ha a leállítása sikertelen. A feladatátvételi folyamat előrehaladásának követheti a **feladatok** lap.
5. Ha az Azure virtuális Géphez való kapcsolódáshoz készített, csatlakozás érvényesíti a feladatátvétel után.
6. Miután ellenőrizte, **véglegesítése** a feladatátvételt. Ez törli az összes rendelkezésre álló helyreállítási pontok.

> [!WARNING]
> **Ne szakítsa meg a folyamatban lévő feladatátvételi**: elindítani a feladatátvételt, mielőtt a virtuális gép replikációs le van állítva. Ha megszakítja a folyamatban lévő feladatátvételi leáll, a feladatátvétel, de a virtuális gép nem fog újra replikálni.  


## <a name="fail-back-from-azure-to-on-premises"></a>Az Azure-ból a helyszíni sikertelen

### <a name="prerequisites-for-failback"></a>Feladat-visszavételhez szükséges előfeltételek

Feladat-visszavétel befejezése, győződjön meg róla, hogy az elsődleges hely VMM-kiszolgáló vagy Hyper-V kiszolgáló csatlakoztatva van-e a Site Recovery szolgáltatásban.


### <a name="run-a-failback-and-reprotect-on-premises-vms"></a>A feladat-visszavétel futtassa, és állítsa a helyszíni virtuális gépek

Feladatok átadása az Azure-ból a helyszíni hely, és indítsa el a virtuális gépek replikálása Azure a helyszíni helyről.

1. A **beállítások** > **replikált elemek** kattintson a virtuális gép > **tervezett feladatátvétel**.
2. A **tervezett feladatátvétel megerősítése**, ellenőrizze a feladatátvételi irányát (az Azure-ból), és válassza ki a forrás és cél helyeket. 
3. A **adatszinkronizálás**, adja meg, hogyan szeretné szinkronizálni:
    - **Szinkronizálja az adatokat a feladatátvétel előtt (csak a változási különbözeteket szinkronizálása)**– ezt a beállítást VM állásidő minimálisra csökkenti, mert a virtuális gép leállítása nélkül szinkronizálják. Íme működése:
        1. Az Azure virtuális gép pillanatképet készít, és másolja azt a helyszíni Hyper-V gazdagép. A virtuális gép folyamatosan működik az Azure-ban.
        2. Az Azure virtuális Gépen, leáll, így nem új módosítások hiba fordul elő. A változási különbözeteket végső készletének átkerülnek a helyi kiszolgálón, és a helyszíni virtuális gép elindul.
    - **Szinkronizálja az adatokat csak a feladatátvétel során (teljes letöltésére)**– használja ezt a beállítást, ha Ön már van Azure-beli hosszú ideig. Ez a beállítás nem gyorsabb, mert azt várhatóan több lemez változik, és ellenőrzőösszeg-számítások az időt nem. Ez a beállítás egy lemez letöltés hajt végre. Ez akkor hasznos, ha a helyszíni virtuális gép törölve lett.
4. Ha a System Center VMM-felhőkben, az Azure-ba, a Hyper-V virtuális gépek keresztül visszavétele és engedélyezett az adattitkosítás a felhő a **titkosítási kulcs**, válassza ki a tanúsítványt, ha engedélyezte az adattitkosítást a szolgáltató telepítése során a korábban kiadott a VMM-kiszolgálót.
5. Kezdeményezze a feladatátvételt. A feladatátvételi folyamat előrehaladásának követheti a **feladatok** fülre.
6. Ha a kijelölt szinkronizálja az adatokat a feladatátvétel előtt, a kezdeti adatszinkronizálás történik, és készen áll az Azure virtuális gépek kattintson leállítása után **feladatok** > tervezett feladatátvétel feladat neve > **befejezhetiafeladatátvételt**. Ez az Azure virtuális gép leállása, átviszi a legutóbbi változtatásokat a helyszíni és a helyszíni virtuális gép elindul.
7. Jelentkezzen be a helyszíni virtuális gép ellenőrizni a várt módon érhető el.
8. A helyszíni virtuális gép most egy függőben lévő véglegesítési állapotban van. Kattintson a **véglegesítési**, a feladatátvétel véglegesítésének. Ez törli az Azure virtuális gépek és a lemezek, és előkészíti a helyszíni virtuális gép fordított replikációs.
9. Indítsa el a helyszíni virtuális gép replikálása Azure-ba, engedélyezze a **visszirányú replikálása**.


> [!NOTE]
> A fordított replikáció csak replikál óta az Azure virtuális Géphez ki lett kapcsolva, és csak a változási különbözeteket kerülnek.

