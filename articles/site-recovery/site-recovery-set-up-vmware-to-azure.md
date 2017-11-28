---
title: "Állítsa be a forrás-környezetet (VMware az Azure-bA) |} Microsoft Docs"
description: "A cikkből megtudhatja, hogyan állíthat be a helyszíni környezetben elindítani a VMware virtuális gépek replikálása Azure-bA."
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/23/2017
ms.author: anoopkv
ms.openlocfilehash: 32a3f7498d3c8891178818436e33221f91ae2f8f
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2017
---
# <a name="set-up-the-source-environment-vmware-to-azure"></a>Állítsa be a forrás környezetet (az Azure-bA VMware)
> [!div class="op_single_selector"]
> * [VMware – Azure](./site-recovery-set-up-vmware-to-azure.md)
> * [Fizikai az Azure-bA](./site-recovery-set-up-physical-to-azure.md)

A cikkből megtudhatja, hogyan állíthat be a helyszíni környezet az Azure-bA a VMware rendszerben futó virtuális gépek replikálást indítani.

## <a name="prerequisites"></a>Előfeltételek

A cikk feltételezi, hogy már létrehozta:
- A Recovery Services-tároló a a [Azure-portálon](http://portal.azure.com "Azure-portálon").
- A VMware vcenter programban, amely nem használható egy külön fiókot [automatikus felderítés](./site-recovery-vmware-to-azure.md).
- A virtuális gép telepítéséhez a konfigurációs kiszolgáló.

## <a name="configuration-server-minimum-requirements"></a>Konfigurációs kiszolgáló minimális követelményeknek
A következő táblázat felsorolja a minimális hardver-, szoftver, és hálózati követelményei a konfigurációs kiszolgáló.

> [!IMPORTANT]
> VMware virtuális gépek védelme a konfigurációs kiszolgáló telepítésekor ajánlott telepíteni azt egy **magas rendelkezésre ÁLLÁS** virtuális gépet.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

> [!NOTE]
> A konfigurációs kiszolgáló által a HTTPS-alapú proxykiszolgálókat nem támogatottak.

## <a name="choose-your-protection-goals"></a>Védelmi célok megválasztása

1. Az Azure-portálon lépjen a **Recovery Services** tároló panelt, és válassza ki a tároló.
2. A tároló erőforrás menüben Ugrás **bevezetés** > **Site Recovery** > **1. lépés: infrastruktúra előkészítése**  >  **Védelmi cél**.

    ![Célok megválasztása](./media/site-recovery-set-up-vmware-to-azure/choose-goals.png)
3. A **védelmi cél**, jelölje be **az Azure-bA**, és válassza a **Igen, amelyen a VMware vSphere Hipervizorra**. Ezután kattintson az **OK** gombra.

    ![Célok megválasztása](./media/site-recovery-set-up-vmware-to-azure/choose-goals2.png)

## <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása
A forrás-környezet létrehozása két fő tevékenységet foglal magában:

- Telepítse, és a konfigurációs kiszolgáló regisztrálása a Site Recovery szolgáltatással.
- A helyszíni virtuális gépek észlelése csatlakozva a Site Recovery a helyszíni VMware vCenter vagy vSphere EXSi gazdagépek.

### <a name="step-1-install-and-register-a-configuration-server"></a>1. lépés: Telepítse és regisztrálja a konfigurációs kiszolgálón

1. Kattintson a **1. lépés: infrastruktúra előkészítése** > **forrás**. A **forrás előkészítése**, ha nem rendelkezik a konfigurációs kiszolgáló, kattintson a **+ konfigurációs kiszolgáló** kattintva felvehet egyet.

    ![A forrás beállítása](./media/site-recovery-set-up-vmware-to-azure/set-source1.png)
2. Az a **kiszolgáló hozzáadása** panelen ellenőrizze, hogy **konfigurációs kiszolgáló** megjelenik **kiszolgálótípus**.
4. Töltse le a Site Recovery az egységes telepítő telepítőfájlját.
5. Töltse le a tárolóregisztrációs kulcsot. Az egységes telepítő futtatásakor a regisztrációs kulcsot kell. A kulcs a generálásától számított öt napig érvényes.

    ![A forrás beállítása](./media/site-recovery-set-up-vmware-to-azure/set-source2.png)
6. A számítógépen, mint a konfigurációs kiszolgálót használ, futtassa **Azure Site Recovery az egységes telepítő** a konfigurációs kiszolgáló, a folyamatkiszolgáló és a fő célkiszolgáló telepítése.

#### <a name="run-azure-site-recovery-unified-setup"></a>Futtassa az Azure Site Recovery egyesített telepítő

> [!TIP]
> Konfigurációs kiszolgáló regisztrálása sikertelen lesz, ha az a számítógép rendszerórája eltér a helyi idő több mint öt perc. A rendszer szinkronizálást, és egy [kiszolgálót](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service) a telepítés megkezdése előtt.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> A konfigurációs kiszolgáló telepíthető a parancssorból. További információkért lásd: [a konfigurációs kiszolgáló, a parancssori eszközök telepítése](http://aka.ms/installconfigsrv).

#### <a name="add-the-vmware-account-for-automatic-discovery"></a>Adja hozzá a VMware-fiókot a automatikus felderítése

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

### <a name="step-2-add-a-vcenter"></a>2. lépés: A vCenter hozzáadása
Ahhoz, hogy az Azure Site Recovery számára a helyszíni környezetben futó virtuális gépek felderítése, meg kell kapcsolni a VMware vCenter-kiszolgáló vagy vSphere ESXi-gazdagépek a Site Recovery.

Válassza ki **+ vCenter** elindítani a VMware vCenter-kiszolgáló vagy egy VMware vSphere ESXi-állomáson.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]


## <a name="common-issues"></a>Gyakori problémák
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Következő lépések
[A célkörnyezet beállítása](./site-recovery-prepare-target-vmware-to-azure.md) az Azure-ban.
