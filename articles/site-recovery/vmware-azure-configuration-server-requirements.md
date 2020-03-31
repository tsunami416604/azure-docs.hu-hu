---
title: A VMware vészhelyreállítási konfigurációs kiszolgálóinak követelményei az Azure Site Recovery szolgáltatásban
description: Ez a cikk a VMware vész-helyreállítási konfigurációs kiszolgálójának azure-beli Azure-beli Azure-beli azure-beli üzembe helyezésekor nyújtott támogatást és követelményeket ismerteti.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 0b0942b517c8dc83c048bd1203a58d9861515dfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257406"
---
# <a name="configuration-server-requirements-for-vmware-disaster-recovery-to-azure"></a>Konfigurációs kiszolgáló követelményei a VMware vész-helyreállítási azure-ba

A helyszíni konfigurációs kiszolgálót akkor telepíti, amikor [az Azure Site Recovery](site-recovery-overview.md) szolgáltatást használja a VMware virtuális gépek és a fizikai kiszolgálók azure-beli vészhelyreállítása érdekében.

- A konfigurációs kiszolgáló koordinálja a helyszíni VMware és az Azure közötti kommunikációt. Az adatreplikációt is kezeli.
- [További információ](vmware-azure-architecture.md) a konfigurációs kiszolgáló összetevőiről és folyamatairól.

## <a name="configuration-server-deployment"></a>Konfigurációs kiszolgáló telepítése

A VMware virtuális gépek Azure-ba történő vészhelyreállítása esetén a konfigurációs kiszolgálót VMware virtuális gépként telepíti.

- A Site Recovery egy OVA-sablont biztosít, amelyet az Azure Portalról tölt le, és importál a vCenter-kiszolgálóra a konfigurációs kiszolgáló virtuális gépének beállításához.
- Ha a konfigurációs kiszolgálót a OVA sablon használatával telepíti, a virtuális gép automatikusan megfelel a cikkben felsorolt követelményeknek.
- Javasoljuk, hogy állítsa be a konfigurációs kiszolgálót a OVA sablon használatával. Ha azonban vészhelyreállítást állít be a VMware virtuális gépekhez, és nem tudja használni a OVA sablont, az alábbi utasítások alapján telepítheti a konfigurációs [kiszolgálót.](physical-azure-set-up-source.md)
- Ha a konfigurációs kiszolgálót a helyszíni fizikai gépek vész-helyreállításához telepíti az Azure-ba, kövesse a [jelen cikkben](physical-azure-set-up-source.md)található utasításokat. 

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="next-steps"></a>További lépések
Állítsa be a [VMware virtuális gépek](vmware-azure-tutorial.md) vészhelyreállítását az Azure-ba.
