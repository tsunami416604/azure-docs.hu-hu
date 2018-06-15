---
title: Vész-helyreállítási Hyper-V virtuális gépek Azure-bA helyszíni Hyper-V kiszolgáló előkészítése |} Microsoft Docs
description: Ismerje meg, hogyan készíti elő a vész-helyreállítási az Azure-bA az Azure Site Recovery szolgáltatásban a System Center VMM által nem kezelt helyszíni Hyper-V virtuális gépek.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 03/15/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 7e0219a662483ef123bdc2889a43dd3d93d23ac2
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31413228"
---
# <a name="prepare-on-premises-hyper-v-servers-for-disaster-recovery-to-azure"></a>Helyszíni Hyper-V kiszolgálók előkészítése az Azure-bA katasztrófa utáni helyreállítás

Az oktatóanyag bemutatja, hogyan készíti elő a helyszíni Hyper-V infrastruktúra, ha a Hyper-V virtuális gépek replikálása Azure-ba, a vész-helyreállítási célokra szeretné. Hyper-V-gazdagépek a System Center Virtual Machine Manager (VMM) kezelhető, de ez nem szükséges.  Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Tekintse át a Hyper-V követelményeinek, és a VMM követelmények, ha van ilyen.
> * Ha van ilyen, készítse elő a VMM
> * Az Azure helyekre internet-hozzáférés ellenőrzése
> * Készítse elő a virtuális gépeket, hogy az Azure-bA a feladatátvételt követően elérheti őket

Ez a sorozat második oktatóanyaga. Győződjön meg arról, hogy [beállította az Azure-összetevőket](tutorial-prepare-azure.md) az előző oktatóanyagban leírtak szerint.



## <a name="review-requirements-and-prerequisites"></a>Felülvizsgálati követelmények és Előfeltételek

Győződjön meg arról, hogy a Hyper-V-gazdagépek és virtuális gépek megfelelnek a követelményeinek.

1. [Győződjön meg arról](hyper-v-azure-support-matrix.md#on-premises-servers) helyszíni kiszolgáló követelményei.
2. [A követelmények ellenőrzéséhez](hyper-v-azure-support-matrix.md#replicated-vms) Hyper-V virtuális gépek replikálása Azure-bA szeretne.
3. Ellenőrizze a Hyper-V gazdagép [hálózati](hyper-v-azure-support-matrix.md#hyper-v-network-configuration); és a gazdagép és vendég [tárolási](hyper-v-azure-support-matrix.md#hyper-v-host-storage) támogatja a helyszíni Hyper-V gazdagépek.
4. Ellenőrizze az Azure támogatott [hálózati](hyper-v-azure-support-matrix.md#azure-vm-network-configuration-after-failover), [tárolási](hyper-v-azure-support-matrix.md#azure-storage) és [számítási](hyper-v-azure-support-matrix.md#azure-compute-features) lehetőségeit a feladatátvételt követően.
5. Az Azure-ba replikált helyszíni virtuális gépeknek meg kell felelniük az [Azure virtuális gépekre vonatkozó feltételeinek](hyper-v-azure-support-matrix.md#azure-vm-requirements).


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

1. Az oktatóanyag céljából a legegyszerűbb konfiguráció esetén a Hyper-V-gazdagépek és a VMM-kiszolgáló és az internet közvetlen hozzáféréssel rendelkezzenek a proxy használata nélkül. 
2. Győződjön meg arról, hogy a Hyper-V-gazdagépek és a VMM-kiszolgálót, ha szükséges, férhetnek hozzá az URL-címek: 

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
3. Ha IP-cím szerint szabályozására van, győződjön meg arról, hogy:
    - IP-címeken alapuló tűzfalszabályok szabályok csatlakozni tud-e [Azure Datacenter IP-címtartományok](https://www.microsoft.com/download/confirmation.aspx?id=41653), és a HTTPS (443) port.
    - Az IP-címtartományok az előfizetés az Azure-régió, valamint a (hozzáférés-vezérlés és identitás kezelésre szolgáló) USA nyugati régiója engedélyezése.


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Felkészülés az Azure virtuális gépekhez való kapcsolódásra a feladatátvételt követően

Egy feladatátvevő forgatókönyv során érdemes lehet a replikált a helyszíni hálózathoz való kapcsolódáshoz.

Windows virtuális gépek a feladatátvételt követően RDP segítségével csatlakozhat, elérésének engedélyezése az alábbiak szerint:

1. Ha internetes hozzáférést kíván használni, engedélyezze az RDP-t a helyszíni virtuális gépen a feladatátvétel előtt. Ellenőrizze, hogy a **Nyilvános** résznél felvette-e a listára a TCP- és UDP-szabályokat, valamint hogy a **Windows tűzfal** > **Engedélyezett alkalmazások** területén az összes profil számára engedélyezve van-e az RDP.
2. Ha helyek közötti VPN-kapcsolatot kíván használni, engedélyezze az RDP-t a helyszíni gépen. Engedélyezze az RDP-t a **Windows tűzfal** -> **Engedélyezett alkalmazások és szolgáltatások** területén a **Tartomány és Privát** hálózatok számára.
   Ellenőrizze, hogy az operációs rendszer tárolóhálózati szabályzata **OnlineAll** értékre van-e állítva. [További információk](https://support.microsoft.com/kb/3031135). A virtuális gépen nem lehetnek függőben lévő Windows-frissítések a feladatátvétel elindításakor. Ha vannak, a frissítés befejeződéséig nem fog tudni bejelentkezni a virtuális gépre.
3. A feladatátvételt követően ellenőrizze a **Rendszerindítási diagnosztika** részt a Windows Azure virtuális gépen a virtuális gép képernyőképének megtekintéséhez. Ha nem sikerül, ellenőrizze, hogy fut-e a virtuális gép, majd tekintse át a [hibaelhárítási tippeket](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

A feladatátvétel után a azonos IP-címet használ a replikált helyszíni virtuális gép vagy egy másik IP-cím Azure virtuális gépek végezheti el. [További](concepts-on-premises-to-azure-networking.md) beállítása a feladatátvételi IP-címzést.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Állítsa be az Azure-bA vész-helyreállítási Hyper-V virtuális gépek](tutorial-hyper-v-to-azure.md)
> [beállítása az Azure-bA vész-helyreállítási Hyper-V virtuális gépek VMM-felhőkben](tutorial-hyper-v-vmm-to-azure.md)
