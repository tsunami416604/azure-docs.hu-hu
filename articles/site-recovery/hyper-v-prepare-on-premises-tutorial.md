---
title: "Vész-helyreállítási Hyper-V virtuális gépek Azure-bA helyszíni Hyper-V kiszolgáló előkészítése |} Microsoft Docs"
description: "Ismerje meg, hogyan készíti elő a vész-helyreállítási az Azure-bA az Azure Site Recovery szolgáltatásban a System Center VMM által nem kezelt helyszíni Hyper-V virtuális gépek."
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 02/14/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 9524ffde4a588d3ac029bc8a3df91726082e157d
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2018
---
# <a name="prepare-on-premises-hyper-v-servers-for-disaster-recovery-to-azure"></a>Helyszíni Hyper-V kiszolgálók előkészítése az Azure-bA katasztrófa utáni helyreállítás

Az oktatóanyag bemutatja, hogyan készíti elő a helyszíni Hyper-V infrastruktúra, ha a Hyper-V virtuális gépek replikálása Azure-ba, a vész-helyreállítási célokra szeretné. Hyper-V-gazdagépek a System Center Virtual Machine Manager (VMM) kezelhető, de ez nem szükséges.  Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Tekintse át a Hyper-V követelményeinek, és a VMM követelmények, ha van ilyen.
> * Ha van ilyen, készítse elő a VMM
> * Az Azure helyekre internet-hozzáférés ellenőrzése
> * Készítse elő a virtuális gépeket, hogy az Azure-bA a feladatátvételt követően elérheti őket

Ez a sorozat második oktatóanyaga. Győződjön meg arról, hogy [beállította az Azure-összetevőket](tutorial-prepare-azure.md) az előző oktatóanyagban leírtak szerint.



## <a name="review-server-requirements"></a>Tekintse át a kiszolgáló követelményei

Győződjön meg arról, hogy a Hyper-V-gazdagépeken az alábbi követelményeknek. Ha az Ön által felügyelt gazdagépek a System Center Virtual Machine Manager (VMM) felhők, ellenőrizze a VMM vonatkozó követelményeket.


**Összetevő** | **A VMM által felügyelt Hyper-V** | **A Hyper-V VMM nélkül**
--- | --- | ---
**Hyper-V gazdagép operációs rendszere** | Windows Server 2016, 2012 R2 | NA
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

Ha a feladatátvételt követően RDP segítségével szeretne kapcsolódni a Windows virtuális gépekhez, tegye a következőket:

1. Ha internetes hozzáférést kíván használni, engedélyezze az RDP-t a helyszíni virtuális gépen a feladatátvétel előtt. Ellenőrizze, hogy a **Nyilvános** résznél felvette-e a listára a TCP- és UDP-szabályokat, valamint hogy a **Windows tűzfal** > **Engedélyezett alkalmazások** területén az összes profil számára engedélyezve van-e az RDP.
2. Ha helyek közötti VPN-kapcsolatot kíván használni, engedélyezze az RDP-t a helyszíni gépen. Engedélyezze az RDP-t a **Windows tűzfal** -> **Engedélyezett alkalmazások és szolgáltatások** területén a **Tartomány és Privát** hálózatok számára.
   Ellenőrizze, hogy az operációs rendszer tárolóhálózati szabályzata **OnlineAll** értékre van-e állítva. [További információk](https://support.microsoft.com/kb/3031135). A virtuális gépen nem lehetnek függőben lévő Windows-frissítések a feladatátvétel elindításakor. Ha vannak, a frissítés befejeződéséig nem fog tudni bejelentkezni a virtuális gépre.
3. A feladatátvételt követően ellenőrizze a **Rendszerindítási diagnosztika** részt a Windows Azure virtuális gépen a virtuális gép képernyőképének megtekintéséhez. Ha nem sikerül, ellenőrizze, hogy fut-e a virtuális gép, majd tekintse át a [hibaelhárítási tippeket](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Állítsa be az Azure-bA vész-helyreállítási Hyper-V virtuális gépek](tutorial-hyper-v-to-azure.md)
> [beállítása az Azure-bA vész-helyreállítási Hyper-V virtuális gépek VMM-felhőkben](tutorial-hyper-v-vmm-to-azure.md)
