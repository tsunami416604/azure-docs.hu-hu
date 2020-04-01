---
title: Felkészülés a Hyper-V virtuális gépek azure-ba való vészhelyreállítására az Azure-ban az Azure Site Recovery segítségével
description: Ismerje meg, hogyan készítheti elő a helyszíni Hyper-V virtuális gépeket az Azure Site Recovery használatával az Azure-ba való vészhelyreállításhoz.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: b36e4688ebd6e929a56869a6bb191d98b2f1f432
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239839"
---
# <a name="prepare-on-premises-hyper-v-servers-for-disaster-recovery-to-azure"></a>Helyszíni Hyper-V-kiszolgálók előkészítése az Azure-ba irányuló vészhelyreállításhoz

Ez a cikk ismerteti, hogyan készítse elő a helyszíni Hyper-V infrastruktúrát, ha be szeretné állítani a hyper-vms vész-helyreállítást az Azure-ba az [Azure Site Recovery](site-recovery-overview.md)használatával.


Ez a második oktatóanyag egy sorozatban, amely bemutatja, hogyan állíthatja be a vész-helyreállítási azure-ban a helyszíni Hyper-V virtuális gépek. Az első oktatóanyagban beállítjuk a Hyper-V vész-helyreállítási szükséges [Azure-összetevőket.](tutorial-prepare-azure.md)

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Tekintse át a Hyper-V és a VMM-követelményeket, ha a Hyper-V gazdagépeket a System Center VMM kezeli.
> * Készítse elő a VMM-et, ha van ilyen.
> * Ellenőrizze az Azure-helyekhez való internet-hozzáférést.
> * Készítse elő a virtuális gépeket, hogy az Azure-ba való feladatátvétel után elérhesse őket.

> [!NOTE]
> Oktatóanyagok megmutatja a legegyszerűbb telepítési útvonalat egy forgatókönyv. Ahol lehet, az alapértelmezett beállításokat használják, és nem mutatják be az összes lehetséges beállítást és útvonalat. Részletes útmutatásért tekintse át a webhely-helyreállítási tartalomjegyzék Útmutató szakaszában található cikket.

## <a name="before-you-start"></a>Előkészületek

Győződjön meg arról, hogy előkészítette az Azure-t a [sorozat első oktatóanyagában leírtak szerint.](tutorial-prepare-azure.md)

## <a name="review-requirements-and-prerequisites"></a>A követelmények és előfeltételek áttekintése

Győződjön meg arról, hogy a Hyper-V gazdagépek és a virtuális gépek megfelelnek a követelményeknek.

1. [Ellenőrizze](hyper-v-azure-support-matrix.md#on-premises-servers) a helyszíni kiszolgáló követelményeit.
2. Ellenőrizze az Azure-ba replikálni kívánt Hyper-V virtuális gépek [követelményeit.](hyper-v-azure-support-matrix.md#replicated-vms)
3. Ellenőrizze a Hyper-V állomás [hálózatát;](hyper-v-azure-support-matrix.md#hyper-v-network-configuration) és a helyszíni Hyper-V-állomások gazda- és [vendégtárolási](hyper-v-azure-support-matrix.md#hyper-v-host-storage) támogatása.
4. Ellenőrizze az Azure támogatott [hálózati](hyper-v-azure-support-matrix.md#azure-vm-network-configuration-after-failover), [tárolási](hyper-v-azure-support-matrix.md#azure-storage) és [számítási](hyper-v-azure-support-matrix.md#azure-compute-features) lehetőségeit a feladatátvételt követően.
5. Az Azure-ba replikált helyszíni virtuális gépeknek meg kell felelniük az [Azure virtuális gépekre vonatkozó feltételeinek](hyper-v-azure-support-matrix.md#azure-vm-requirements).


## <a name="prepare-vmm-optional"></a>VMM előkészítése (nem kötelező)

Ha a Hyper-V gazdagépeket a VMM kezeli, elő kell készítenie a helyszíni VMM-kiszolgálót. 

- Győződjön meg arról, hogy a VMM-kiszolgáló legalább egy felhővel rendelkezik, egy vagy több állomáscsoporttal. A Hyper-V gazdagép, amelyen a virtuális gépek futnak kell elhelyezni a felhőben.
- Készítse elő a VMM-kiszolgálót a hálózat leképezésére.

### <a name="prepare-vmm-for-network-mapping"></a>A VMM előkészítése a hálózat leképezésére

VMM használata esetén [a hálózati leképezések](site-recovery-network-mapping.md) a helyszíni VMM VM-hálózatok és az Azure virtuális hálózatok között. A leképezés biztosítja, hogy az Azure virtuális gépek a megfelelő hálózathoz csatlakozzanak, amikor feladatátvétel után jönnek létre.

Készítse elő a VMM-et a hálózat leképezéséhez az alábbiak szerint:

1. Győződjön meg arról, hogy rendelkezik egy [VMM logikai hálózat,](https://docs.microsoft.com/system-center/vmm/network-logical) amely társítva van a felhő, amelyben a Hyper-V gazdagépek találhatók.
2. Győződjön meg arról, hogy a logikai hálózathoz virtuális [gép hálózata](https://docs.microsoft.com/system-center/vmm/network-virtual) kapcsolódik.
3. A VMM-ben csatlakoztassa a virtuális gépeket a virtuális gép hálózatához.

## <a name="verify-internet-access"></a>Internet-hozzáférés ellenőrzése

1. Az oktatóanyag alkalmazásában a legegyszerűbb konfiguráció az, hogy a Hyper-V gazdagépek és a VMM-kiszolgáló proxy használata nélkül közvetlen hozzáféréssel rendelkeznek az internethez. 
2. Győződjön meg arról, hogy a Hyper-V állomások és a VMM-kiszolgáló, ha szükséges, hozzáférhetnek a szükséges URL-eket alább.   
3. Ha az IP-cím szerint szabályozza a hozzáférést, győződjön meg arról, hogy:
    - AZ IP-címalapú tűzfalszabályok csatlakozhatnak [az Azure Datacenter IP-tartományaihoz](https://www.microsoft.com/download/confirmation.aspx?id=41653)és a HTTPS (443) porthoz.
    - Ip-címtartományok engedélyezése az előfizetés Azure-régiójához.
    
### <a name="required-urls"></a>Kötelező URL-címek


[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Felkészülés az Azure virtuális gépekhez való kapcsolódásra a feladatátvételt követően

Feladatátvételi forgatókönyv esetén érdemes lehet csatlakozni a replikált helyszíni hálózathoz.

Ha a feladatátvétel után RDP-vel szeretne csatlakozni a Windows virtuális gépekhez, engedélyezze a hozzáférést az alábbiak szerint:

1. Ha internetes hozzáférést kíván használni, engedélyezze az RDP-t a helyszíni virtuális gépen a feladatátvétel előtt. Győződjön meg arról, hogy tcp- és UDP-szabályok at adtak hozzá a **nyilvános** profilhoz, és hogy az RDP engedélyezett a **Windows tűzfal** > **engedélyezett alkalmazásai** ban az összes profilhoz.
2. Ha helyek közötti VPN-kapcsolatot kíván használni, engedélyezze az RDP-t a helyszíni gépen. Az RDP-t engedélyezni kell a **Windows tűzfal** -> **engedélyezett alkalmazásaiban és szolgáltatásaiban** **a tartományi és magánhálózatokszámára.**
   Ellenőrizze, hogy az operációs rendszer tárolóhálózati szabályzata **OnlineAll** értékre van-e állítva. [További információ](https://support.microsoft.com/kb/3031135). A virtuális gépen nem lehetnek függőben lévő Windows-frissítések a feladatátvétel elindításakor. Ha vannak, a frissítés befejezéséig nem tud bejelentkezni a virtuális gépre.
3. A feladatátvételt követően ellenőrizze a **Rendszerindítási diagnosztika** részt a Windows Azure virtuális gépen a virtuális gép képernyőképének megtekintéséhez. Ha nem sikerül, ellenőrizze, hogy fut-e a virtuális gép, majd tekintse át a [hibaelhárítási tippeket](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Feladatátvétel után elérheti az Azure virtuális gépek ugyanazt az IP-címet, mint a replikált helyszíni virtuális gép, vagy egy másik IP-címet. [További információ](concepts-on-premises-to-azure-networking.md) a feladatátvétel IP-címzésének beállításáról.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Vészhelyreállítás beállítása az Azure hyper-v virtuális gépekhez:](tutorial-hyper-v-to-azure.md)
> [Vészhelyreállítás beállítása az Azure-ba a Hyper-V virtuális gépekhez VMM-felhőkben](tutorial-hyper-v-vmm-to-azure.md)
