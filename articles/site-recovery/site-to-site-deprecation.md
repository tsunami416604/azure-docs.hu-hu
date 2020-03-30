---
title: Vészhelyreállítás e-visszaállása az ügyfél által felügyelt helyek között (vmm-mel) az Azure Site Recovery használatával | Microsoft dokumentumok
description: Részletek a DR közelgő ejgéséről az ügyfél tulajdonában lévő webhelyek között a Hyper-V használatával, valamint az SCVMM által kezelt és az Azure-ba kezelt webhelyek között, valamint alternatív lehetőségek
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/25/2020
ms.author: rajanaki
ms.openlocfilehash: 208177d10e9002fafe2495710da229541a11a43e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77661670"
---
# <a name="deprecation-of-disaster-recovery-between-customer-managed-sites-with-vmm-using-azure-site-recovery"></a>Vészhelyreállítás e-visszaállásának eprecációja az ügyfél által felügyelt helyek között (vmm-mel) az Azure Site Recovery használatával

Ez a cikk ismerteti a közelgő eprecation terv, a megfelelő következményeket, és az alternatív lehetőségek állnak rendelkezésre az ügyfelek számára a következő forgatókönyv:

Dr a System Center Virtual Machine Manager (SCVMM) által a Site Recovery használatával kezelt ügyféltulajdonú helyek között

> [!IMPORTANT]
> Javasoljuk, hogy az ügyfelek a lehető leghamarabb tegyenek a javítási lépésekhez, hogy elkerüljék a környezetük megzavarását. 

## <a name="what-changes-should-you-expect"></a>Milyen változásokra számíthat?

- 2020 márciusátantól az Azure Portalon értesítést kap & e-mailes kommunikációról a Hyper-V virtuális gépek helyek közötti replikációjának közelgő e-mailes e-mailben történő e-mailben történő leplezésével. Az aknamentesítést 2023 márciusára tervezik.

- Ha meglévő konfigurációval rendelkezik, annak nincs hatása a beállításra.

- Miután a forgatókönyvek elavult, kivéve, ha az ügyfél követi az alternatív megközelítések, a meglévő replikációk előfordulhat, hogy megszakad. Az ügyfelek nem tekinthetik meg, kezelhetik és nem hajthatják végre a VÉSZ-kezeléssel kapcsolatos műveleteket az Azure Site Recovery szolgáltatáson keresztül az Azure Portalon.
 
## <a name="alternatives"></a>Alternatív megoldások 

Az alábbiakban azokat az alternatívákat, amelyek közül az ügyfél választhat annak érdekében, hogy a VÉSZ-stratégia nem érinti, ha a forgatókönyv elavult. 

- 1. lehetőség (Ajánlott): Válassza ki, hogy [az Azure-t szeretné-e dr-célként használni.](hyper-v-vmm-azure-tutorial.md)


- 2. lehetőség: Válassza ki, hogy folytatja a helyek közötti replikáció t az alapul szolgáló [Hyper-V replika megoldás](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/set-up-hyper-v-replica)használatával, de nem fogja tudni kezelni a DR-konfigurációk at Azure Site Recovery az Azure Portalon. 


## <a name="remediation-steps"></a>Javítási lépések

Ha az 1.

1. [Tiltsa le a virtuális gépekkel társított összes virtuális gép védelmét.](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario) Használja a **Replikáció letiltása és eltávolítása** lehetőséget, vagy futtassa az említett parancsfájlokat a replikációs beállítások helyszíni karbantartásához. 

2. [Törölje az összes VMM-kiszolgáló regisztrációját](site-recovery-manage-registration-and-protection.md#unregister-a-vmm-server) a helyek közötti replikációs konfigurációból.

3. [Készítse elő az Azure-erőforrásokat](tutorial-prepare-azure-for-hyperv.md) a virtuális gépek replikációjának engedélyezéséhez.
4. [Helyszíni Hyper-V-kiszolgálók előkészítése](hyper-v-prepare-on-premises-tutorial.md)
5. [A virtuális gépek replikációjának beállítása a VMM-felhőben](hyper-v-vmm-azure-tutorial.md)
6. Nem kötelező, de ajánlott: [DR-fúró futtatása](tutorial-dr-drill-azure.md)

Ha a Hyper-V replika 2.

1. A **Védett elemek** > **replikált elemei ben**kattintson a jobb gombbal a számítógépre > A replikáció **letiltása parancsra.**
2. A **Replikáció letiltása**csoportban válassza **az Eltávolítás**lehetőséget.

    Ezzel eltávolítja a replikált elemet az Azure Site Recovery szolgáltatásból (a számlázás levan állítva). A helyszíni virtuális gépen a replikációs konfiguráció **nem lesz** törölve. 

## <a name="next-steps"></a>További lépések
Tervezze meg az eprecációt, és válasszon egy másik lehetőséget, amely a legjobban megfelel az infrastruktúrának és az üzleti életnek. Abban az esetben, ha bármilyen kérdése van ezzel kapcsolatban, forduljon a Microsoft támogatási

