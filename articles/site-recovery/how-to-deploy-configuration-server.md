---
title: A konfigurációs kiszolgáló VMware vész-helyreállítási az Azure Site Recovery üzembe |} Microsoft Docs
description: Ez a cikk ismerteti, hogyan VMware vész-helyreállítási az Azure Site Recovery konfigurációs kiszolgálók központi telepítéséhez
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 02/25/2018
ms.author: anoopkv
ms.openlocfilehash: f7ec02cb4723d8a4ea0847810b1a1822cf20e957
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2018
ms.locfileid: "29768570"
---
# <a name="deploy-a-configuration-server"></a>Konfigurációs kiszolgáló üzembe helyezése

Telepít egy a helyszíni konfigurációs kiszolgáló használatakor [Azure Site Recovery](site-recovery-overview.md) vész-helyreállítási VMware virtuális gépek és fizikai kiszolgálók Azure-bA. A konfigurációs kiszolgáló koordináták kommunikáció közötti helyszíni VMware és Azure. Adatreplikálás is kezeli. Ez a cikk végigvezeti a lépéseken, amelyekkel a konfigurációs kiszolgáló központi telepítése, ha VMware virtuális gépeket replikál az Azure-bA. [Kövesse az ebben a cikkben](site-recovery-set-up-physical-to-azure.md) Ha, be kell állítania a fizikai kiszolgáló replikációs konfigurációs kiszolgálót.

## <a name="prerequisites"></a>Előfeltételek

Azt javasoljuk, hogy telepít egy magas rendelkezésre állású VMware virtuális gépként a konfigurációs kiszolgáló. Minimális hardverkövetelmények a következő táblázat foglalja össze.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]




## <a name="capacity-planning"></a>Kapacitástervezés

A kiszolgáló méretezési követelményei a potenciális adatmódosítási arány függ. Használja ezt a táblázatot útmutatóként.

| **CPU** | **Memória** | **Gyorsítótár-lemez mérete** | **Adatváltozási sebesség** | **Védett gépek** |
| --- | --- | --- | --- | --- |
| 8 Vcpu (2 sockets * @ 2,5 GHz, 4 mag) |16 GB |300 GB |500 GB vagy kevesebb |100-nál kevesebb gépek replikálása. |
| 12 Vcpu (2 sockets * @ 2,5 GHz-es 6 mag) |18 GB |600 GB |1 TB 500 GB |100-150 gépek replikálása. |
| 16 Vcpu (2 sockets * @ 2,5 GHz-es 8 mag) |32 GB |1 TB |1 TB to 2 TB |150-200 gépek replikálása. |


Ha VMware virtuális gépeket replikál, tudjon meg többet az [kapacitástervezésének szempontjai](/site-recovery-plan-capacity-vmware.md). Futtassa a [Deployment planner eszköz](site-recovery-deployment-planner.md) a VMWare-replikáció.



## <a name="download-the-template"></a>A sablon letöltése

A Site Recovery lehet beállítani a konfigurációs kiszolgálót egy magas rendelkezésre állású VMware virtuális gépként letölthető sablon biztosít. 

1. A tárolóban kattintson **Az infrastruktúra előkészítése** > **Forrás** elemre.
2. A **Forrás előkészítése** ablakban válassza a **+Konfigurációs kiszolgáló** elemet.
3. A **Kiszolgáló hozzáadása** panelen ellenőrizze, hogy a **Kiszolgálótípus** mezőben a **Konfigurációs kiszolgáló VMware-hez** érték jelenik meg.
4. Töltse le a konfigurációs kiszolgáló OVF- (Open Virtualization Format – nyílt virtualizációs formátum) sablonját.

  > [!TIP]
  A konfigurációs kiszolgáló sablon közvetlenül a legújabb verzióját letöltheti [Microsoft Download Center](https://aka.ms/asrconfigurationserver).


## <a name="import-the-template-in-vmware"></a>A sablon importálása a VMware-ben


1. Jelentkezzen be a VMware vCenter-kiszolgálóra vagy a vSphere ESXi-gazdagépre a VMWare vSphere Client használatával.
2. A **File** (Fájl) menüben válassza a **Deploy OVF Template** (OVF-sablon telepítése) lehetőséget az OVF-sablon telepítése varázsló elindításához.

     ![OVF-sablon](./media/tutorial-vmware-to-azure/vcenter-wizard.png)

3. A **forrás kiválasztása**, adja meg a letöltött OVF helyét.
4. A **tekintse át a részletek**, jelölje be **következő**.
5. A **név és a mappa kiválasztása** és **válassza konfigurációs**, fogadja el az alapértelmezett beállításokat.
6. A **Select storage** (Tároló kiválasztása) területen a legjobb teljesítmény érdekében válassza a **Thick Provision Eager Zeroed** formátumot a **Select virtual disk format** (Virtuális lemez formátumának kiválasztása) mezőben.
4. A varázsló többi lapján fogadja el az alapértelmezett beállításokat.
5. A **Ready to complete** (Befejezésre kész) területen:

    * A virtuális gép alapértelmezett beállításokkal való telepítéséhez válassza a **Power on after deployment** > **Finish** (Bekapcsolás a telepítést követően > Befejezés) elemet.

    * Adja hozzá a hálózati illesztővel, törölje a jelet **kapcsolja be a telepítést követően**, majd válassza ki **Befejezés**. Alapértelmezés szerint a konfigurációs kiszolgáló sablonját a rendszer egyetlen hálózati adapterre telepíti. Az üzembe helyezés után további hálózati adaptereket is hozzáadhat.


## <a name="add-an-additional-adapter"></a>További adapter hozzáadása

Ha meg szeretne adni egy további hálózati Adapterre a konfigurációs kiszolgáló, vegye fel előtt regisztrálja a kiszolgálót a tárolóban lévő állapottal. A regisztrálást követően további adapterek hozzáadása nem támogatott.

1. A vSphere Client-leltárban kattintson a jobb gombbal a virtuális gépre, és válassza az **Edit Settings** (Beállítások szerkesztése) elemet.
2. A **Hardware** (Hardver) területen válassza az **Add** > **Ethernet Adapter** (Hozzáadás > Ethernet-adapter) elemet. Ezután kattintson a **Tovább** gombra.
3. Válassza ki a hálózati adapter típusát és a hálózatot. 
4. A virtuális hálózati adapter a virtuális gép bekapcsolásakor való csatlakoztatásához válassza a **Connect at power on** (Csatlakoztatás a bekapcsoláskor) elemet. Válassza ki **következő** > **Befejezés** > **OK**.
 

## <a name="register-the-configuration-server"></a>A konfigurációs kiszolgáló regisztrálása 

1. A VMWare vSphere Client-konzolon kapcsolja be a virtuális gépet.
2. A virtuális gép a bekapcsolásakor egy Windows Server 2016 telepítési folyamatot indít el. Fogadja el a licencszerződést, és adjon meg egy rendszergazdai jelszót.
3. A telepítés befejezése után jelentkezzen be rendszergazdaként a virtuális gépre.
4. Az első bejelentkezés alkalmával elindul az Azure Site Recovery Configuration Tool.
5. Adjon meg egy nevet, amelyen a konfigurációs kiszolgálót regisztrálja a Site Recovery szolgáltatásban. Ezután kattintson a **Tovább** gombra.
6. Az eszköz ellenőrzi, hogy a virtuális gép tud-e csatlakozni az Azure-hoz. A kapcsolat létrejötte után a **Sign in** (Bejelentkezés) gombra kattintva jelentkezzen be az Azure-előfizetésbe. Olyan hitelesítő adatokra van szükség, amelyekkel hozzá lehet férni a tárolóhoz, amelyben regisztrálni kívánja a konfigurációs kiszolgálót.
7. Az eszköz végrehajt néhány konfigurációs feladatot, majd újraindul.
8. Jelentkezzen be újra a gépre. A konfigurációs kiszolgáló felügyeleti varázslója automatikusan elindul.

### <a name="configure-settings"></a>Beállítások konfigurálása

1. A konfigurációs kiszolgáló kezelése varázslóban válassza **kapcsolat beállítása**. Válassza ki a replikációs forgalom fogadására, és jelölje ki a hálózati adapter **mentése**. Ez a beállítás a konfigurációt követően nem módosítható.
2. A **Helyreállítási tár kiválasztása** területen válassza ki az Azure-előfizetést, valamint a megfelelő erőforráscsoportot és tárolót.
3. A **Független gyártótól származó szoftver telepítése** területen fogadja el a licencszerződést. Kattintson a **Letöltés és telepítés** gombra a MySQL-kiszolgáló telepítéséhez.
4. Válassza ki **telepíteni a VMware PowerLCI**. Győződjön meg arról, hogy az összes böngészőablakot be van zárva, ez a lépés végrehajtása előtt. Ezután válassza a **Folytatás** elemet.
5. A **Berendezés konfigurációjának ellenőrzése** területen a rendszer ellenőrzi az előfeltételeket, mielőtt továbblépne.
6. A **VCenter-kiszolgáló vagy vSphere ESXi-kiszolgáló hitelesítő adatainak konfigurálása** területen adja meg azon vCenter-kiszolgáló vagy VSphere-gazdagép teljes tartománynevét vagy IP-címét, ahol a replikálni kívánt virtuális gépek találhatók. A portot adja meg a kiszolgáló figyel és egy rövid nevet a VMware-kiszolgáló újraregisztrálásával a felhőben.
7. Adja meg a konfigurációs kiszolgáló által a VMware-kiszolgálóhoz való csatlakozáshoz használt hitelesítő adatokat. A Site Recovery ezeknek a hitelesítő adatoknak a használatával automatikusan deríti fel a replikáláshoz elérhető VMware virtuális gépeket. Válassza a **Hozzáadás**, majd a **Folytatás** elemet.
8. A **virtuális gép hitelesítő adatok beállítása**, írja be a felhasználónevet és az Azure Site Recovery mobilitási szolgáltatás automatikusan telepítse az gépekre, ha engedélyezve van a replikáció használandó jelszót. A Windows-alapú gépek esetében a fióknak helyi rendszergazdai jogosultságokkal kell rendelkeznie a replikálni kívánt gépeken. Linux-alapú gépeken a rendszergazdai fiók adatait adja meg.
9. Válassza ki **véglegesítő konfigurációs** regisztráció befejezéséhez. 
10. A regisztráció befejezését követően ellenőrizze az Azure Portalon, hogy a konfigurációs kiszolgáló és a VMware-kiszolgáló szerepelnek-e a tároló **Forrás** lapján. Ezután válassza az **OK** gombot a célbeállítások konfigurálásához.


## <a name="troubleshoot-deployment-issues"></a>Üzembe helyezési problémák elhárítása

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]



## <a name="next-steps"></a>További lépések

Állítsa be a vész-helyreállítási [VMware virtuális gépek](tutorial-vmware-to-azure.md) az Azure-bA.
