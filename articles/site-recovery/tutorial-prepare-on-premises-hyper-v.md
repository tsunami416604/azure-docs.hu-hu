---
title: "Vész-helyreállítási Hyper-V virtuális gépek Azure-bA helyszíni Hyper-V kiszolgáló előkészítése |} Microsoft Docs"
description: "Ismerje meg, hogyan készíti elő a vész-helyreállítási az Azure-bA az Azure Site Recovery szolgáltatásban a System Center VMM által nem kezelt helyszíni Hyper-V virtuális gépek."
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 12/31/2017
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 6e2837341e16f5077cfff18d4a34c097c165ef09
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2018
---
# <a name="prepare-on-premises-hyper-v-servers-for-disaster-recovery-to-azure"></a>Helyszíni Hyper-V kiszolgálók előkészítése az Azure-bA katasztrófa utáni helyreállítás

Az oktatóanyag bemutatja, hogyan készíti elő a helyszíni Hyper-V infrastruktúra, ha a Hyper-V virtuális gépek replikálása Azure-ba, a vész-helyreállítási célokra szeretné. Hyper-V-gazdagépek a System Center Virtual Machine Manager (VMM) kezelhető, de ez nem szükséges.  Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Tekintse át a Hyper-V követelményeinek, és a VMM követelmények, ha van ilyen.
> * Ha van ilyen, készítse elő a VMM
> * Az Azure helyekre internet-hozzáférés ellenőrzése
> * Készítse elő a virtuális gépeket, hogy az Azure-bA a feladatátvételt követően elérheti őket

Ez az a sorozat második oktatóanyaga. Győződjön meg arról, hogy rendelkezik [beállítása az Azure összetevők](tutorial-prepare-azure.md) az előző oktatóanyag leírtak szerint.



## <a name="review-server-requirements"></a>Tekintse át a kiszolgáló követelményei

Győződjön meg arról, hogy a Hyper-V-gazdagépeken az alábbi követelményeknek. Ha az Ön által felügyelt gazdagépek a System Center Virtual Machine Manager (VMM) felhők, ellenőrizze a VMM vonatkozó követelményeket.


**Összetevő** | **A VMM által felügyelt Hyper-V** | **A Hyper-V VMM nélkül**
--- | --- | ---
**Hyper-V gazdagép operációs rendszere** | Windows Server 2016-ot, 2012 R2 | NA
**VMM** | A VMM 2012, A VMM 2012 R2 | NA


## <a name="review-hyper-v-vm-requirements"></a>Hyper-V virtuális gép követelményeinek áttekintése

Győződjön meg arról, hogy a virtuális gép megfelel a táblázat tartalmazza.

**Virtuális gép követelmény** | **Részletek**
--- | ---
**Vendég operációs rendszer** | A vendég operációs rendszer [használható az Azure-](https://technet.microsoft.com/library/cc794868.aspx).
**Azure-követelmények** | A helyszíni Hyper-V virtuális gépek Azure virtuális gép requirements(site-recovery-support-matrix-to-azure.md) kell megfelelnie.

## <a name="prepare-vmm-optional"></a>Készítse elő a VMM (nem kötelező)

Ha Hyper-V-gazdagépek a VMM kezeli, akkor készítse elő a helyszíni VMM-kiszolgálón. 

- Győződjön meg arról, hogy a VMM-kiszolgáló legalább egy felhőben, egy vagy több gazdagépcsoportot van. A Hyper-V gazdagépet, amelyen a virtuális gépek futnak a felhőben található kell lennie.
- A hálózatleképezés előkészítése a VMM-kiszolgáló.

### <a name="prepare-vmm-for-network-mapping"></a>A VMM hálózatleképezés előkészítése

Használata esetén a VMM-ben [hálózatleképezés](site-recovery-network-mapping.md) helyszíni VMM-Virtuálisgép-hálózatok és az Azure virtuális hálózatok közötti leképezéseket. Leképezési biztosítja az Azure virtuális gépek csatlakoznak a megfelelő hálózati, ha a feladatátvételt követően jönnek létre.

Készítse elő a VMM a hálózatleképezés az alábbiak szerint:

1. Győződjön meg arról, hogy egy [VMM logikai hálózata](https://docs.microsoft.com/system-center/vmm/network-logical) , amelyhez társítva a felhőben, amelyben a Hyper-V-gazdagépek találhatók.
2. Ellenőrizze, hogy egy [Virtuálisgép-hálózat](https://docs.microsoft.com/system-center/vmm/network-virtual) a logikai hálózathoz csatolva.
3. A Virtuálisgép-hálózatot a virtuális gépek csatlakozni a VMM-ben.

## <a name="verify-internet-access"></a>Internet-hozzáférés ellenőrzése

1. Az oktatóanyag céljából a legegyszerűbb konfiguráció esetén a Hyper-V-gazdagépek és a VMM-kiszolgálón érvényes, az internet közvetlen hozzáféréssel rendelkezzenek a proxy használata nélkül. 
2. Győződjön meg arról, hogy a Hyper-V-gazdagépek és a VMM-kiszolgálót, ha szükséges, férhetnek hozzá az URL-címek: 

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
3. Győződjön meg arról, hogy:
    - Bármely IP-címeken alapuló tűzfalszabályok szabályokat engedélyezni kell az Azure-kommunikációt.
    - Engedélyezze az [Azure-adatközpont IP-tartományait](https://www.microsoft.com/download/confirmation.aspx?id=41653) és a HTTPS-portot (443).
    - Az IP-címtartományok az előfizetés az Azure-régió, valamint a (hozzáférés-vezérlés és identitás kezelésre szolgáló) USA nyugati régiója engedélyezése.


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Felkészülés az Azure virtuális gépekhez való kapcsolódásra a feladatátvételt követően

Egy feladatátvevő forgatókönyv során érdemes lehet a replikált a helyszíni hálózathoz való kapcsolódáshoz.

Windows virtuális gépek a feladatátvételt követően RDP segítségével csatlakozhat, tegye a következőket:

1. Szeretne használni az interneten keresztül, engedélyezze az RDP a feladatátvétel előtt a helyszíni virtuális Gépre. Győződjön meg arról, hogy a TCP és UDP szabályok hozzáadása történik meg a a **nyilvános** profilt, és, hogy engedélyezi-e az RDP a **Windows tűzfal** > **engedélyezett alkalmazások** minden profil esetében.
2. Pont-pont VPN-kapcsolaton keresztüli elérésére engedélyezze az RDP a helyi számítógépen. Engedélyezni kell az RDP a **Windows tűzfal** -> **engedélyezett alkalmazások és szolgáltatások** a **tartomány és a saját** hálózatok.
   Ellenőrizze, hogy az operációs rendszer TÁROLÓHÁLÓZATI szabályzatát állítsa a **OnlineAll**. [További információk](https://support.microsoft.com/kb/3031135). Lehetnek nincs függőben lévő Windows-frissítéseket a virtuális Gépre amikor feladatátvételt indít el. Ha van, nem fogja tudni bejelentkezni a virtuális gép csak a frissítés befejeződése után.
3. Ellenőrizze a Windows Azure virtuális Gépen a feladatátvételt követően, **rendszerindítási diagnosztika** egy Képernyőkép a virtuális Gépet a megtekintéséhez. Ha nem sikerül, ellenőrizze, hogy fut-e a virtuális Gépet, és tekintse át a [hibaelhárítási tippek](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Állítsa be az Azure-bA vész-helyreállítási Hyper-V virtuális gépek](tutorial-hyper-v-to-azure.md)
> [beállítása az Azure-bA vész-helyreállítási Hyper-V virtuális gépek VMM-felhőkben](tutorial-hyper-v-vmm-to-azure.md)
