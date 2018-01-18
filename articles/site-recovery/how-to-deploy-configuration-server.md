---
title: " A konfigurációs kiszolgáló VMware vész-helyreállítási az Azure Site Recovery üzembe |} Microsoft Docs"
description: "Ez a cikk ismerteti, hogyan VMware vész-helyreállítási az Azure Site Recovery konfigurációs kiszolgálók központi telepítéséhez"
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 01/15/2018
ms.author: anoopkv
ms.openlocfilehash: e257ede08ac46ad863b4883b10399058e6f59f1f
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2018
---
# <a name="deploy-a-configuration-server"></a>Konfigurációs kiszolgáló központi telepítése

Telepít egy a helyszíni konfigurációs kiszolgáló használatakor az [Azure Site Recovery](site-recovery-overview.md) VMware virtuális gépek és fizikai kiszolgálók Azure-bA vész-helyreállítási szolgáltatás. WThe konfigurációs kiszolgáló közötti kommunikáció koordinálja a helyszíni VMware és az Azure és adatreplikáció kezeli. Ez a cikk végigvezeti a lépéseken, amelyekkel a konfigurációs kiszolgáló központi telepítése.

## <a name="prerequisites"></a>Előfeltételek

Azt javasoljuk, hogy telepít egy magas rendelkezésre állású VMware virtuális gépként a konfigurációs kiszolgáló. A fizikai kiszolgáló replikáció esetén a konfigurációs kiszolgáló beállítható a fizikai számítógépen. Minimális hardverkövetelmények a következő táblázat foglalja össze.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]




## <a name="capacity-planning"></a>Kapacitástervezés

A kiszolgáló méretezési követelményei a potenciális adatmódosítási arány függ. Használja ezt a táblázatot útmutatóként.

| **CPU** | **Memória** | **Gyorsítótár-lemez mérete** | **Adatváltozási sebesség** | **Védett gépek** |
| --- | --- | --- | --- | --- |
| 8 Vcpu (2 sockets * @ 2,5 GHz, 4 mag) |16 GB |300 GB |500 GB vagy kevesebb |100-nál kevesebb gépek replikálása. |
| 12 Vcpu (2 sockets * @ 2,5 GHz-es 6 mag) |18 GB |600 GB |1 TB 500 GB |100-150 gépek közti replikálásához. |
| 16 Vcpu (2 sockets * @ 2,5 GHz-es 8 mag) |32 GB |1 TB |1 TB to 2 TB |150-200 gépek közti replikálásához. |


Ha VMware virtuális gépeket replikál, tudjon meg többet az [kapacitástervezésének szempontjai](/site-recovery-plan-capacity-vmware.md), és futtassa a [Deployment planner eszköz](site-recovery-deployment-planner.md) a VMWare-replikáció.



## <a name="download-the-template"></a>A sablon letöltése

A Site Recovery lehet beállítani a konfigurációs kiszolgálót egy magas rendelkezésre állású VMware virtuális gépként letölthető sablon biztosít. 

1. A tárolóban, Ugrás **infrastruktúra előkészítése** > **forrás**.
2. A **forrás előkészítése**, kattintson a **+ konfigurációs kiszolgáló**.
3. A **kiszolgáló hozzáadása**, ellenőrizze, hogy **VMware konfigurációs kiszolgáló** megjelenik **kiszolgálótípus**.
4. Töltse le a nyitott virtualizációs formátum (OVF) sablont a konfigurációs kiszolgáló.

  > [!TIP]
  A konfigurációs kiszolgáló sablon legújabb verziója letölthető közvetlenül [Microsoft Download Center](https://aka.ms/asrconfigurationserver)


## <a name="import-the-template-in-vmware"></a>A VMware-sablon importálása


1. Jelentkezzen be a VMware vCenter-kiszolgáló vagy vSphere ESXi-állomáson, a VMWare vSphere Client használatával.
2. Az a **fájl** menüjében válassza **OVF-sablon telepítése**, azt a OVF-sablon központi telepítése varázsló elindításához.  

     ![OVF-sablon](./media/tutorial-vmware-to-azure/vcenter-wizard.png)

3. A **forrás kiválasztása**, adja meg a letöltött OVF helyét.
4. A **tekintse át a részletek**, kattintson a **következő**.
5. A **név és a mappa kiválasztása**, és **válassza konfigurációs**, fogadja el az alapértelmezett beállításokat.
6. A **válassza ki a tárolási**, amely a legjobb teljesítmény érdekében válasszon **vastag rendelkezés különösen nullázni** a **jelölje be a virtuális lemez formátum**.
4. A varázsló utasításait a többi fogadja el az alapértelmezett beállításokat.
5. A **kész befejezni**:
  - A virtuális Gépet, az alapértelmezett beállításokkal, válassza ki **kapcsolja be a telepítést követően** > **Befejezés**.
  - Ha hozzá szeretne adni egy hálózati illesztővel, törölje a jelet **kapcsolja be a telepítést követően**, majd válassza ki **Befejezés**. Alapértelmezés szerint a konfigurációs kiszolgáló sablon az egyetlen hálózati adapter van telepítve, de a telepítés után is hozzáadhat további hálózati adapterek.


## <a name="add-an-additional-adapter"></a>Vegyen fel egy további adaptert

Egy olyan további hálózati adapter hozzáadása a konfigurációs kiszolgáló, tegye, mielőtt regisztrálja a kiszolgálót a tárolóban lévő állapottal. Hozzáadás a felesleges adapterek regisztrálás után nem támogatott.

1. A vSphere Client készletben, kattintson a jobb gombbal a virtuális Gépet, és válassza ki **beállításainak szerkesztése**.
2. A **hardver**, kattintson a **Hozzáadás** > **Ethernet-Adapter**. Ezután kattintson a **Next** (Tovább) gombra.
3. Válassza ki és hálózatiadapter-típust, és a hálózaton. 
4. Csatlakozás a virtuális hálózati Adaptert a virtuális gép bekapcsolása, válassza ki **teljesítménnyel csatlakozását a**. Kattintson a **következő** > **Befejezés**, és kattintson a **OK**.
 

## <a name="register-the-configuration-server"></a>Regisztrálja a konfigurációs kiszolgálót. 

1. A VMWare vSphere ügyfél konzolról bekapcsolása a virtuális Gépet.
2. A virtuális Gépet egy Windows Server 2016 telepítésének tapasztalatokat indul el. Fogadja el a licencszerződést, és adjon meg egy rendszergazdai jelszót.
3. A telepítés befejezése után jelentkezzen be rendszergazdaként, a virtuális Gépet.
4. Az első alkalommal jelentkezik be, az Azure Site Recovery Configuration Tool indít.
5. Adjon meg egy nevet, amellyel a konfigurációs kiszolgáló regisztrálása a Site Recovery szolgáltatással. Ezután kattintson a **Next** (Tovább) gombra.
6. Az eszköz ellenőrzi, hogy a virtuális gép Azure csatlakozhat. A kapcsolat létrejötte után kattintson **bejelentkezés**, hogy jelentkezzen be az Azure-előfizetéshez. A hitelesítő adatokat a tárolóba, ahol a konfigurációs kiszolgáló regisztrálni kívánt hozzáféréssel kell rendelkeznie.
7. Az eszköz bizonyos konfigurációs feladatokat hajtja végre, és majd újraindul.
8. A gép újra bejelentkezni. A konfigurációs kiszolgáló kezelése varázsló automatikusan elindul.

### <a name="configure-settings"></a>Beállítások konfigurálása

1. A konfigurációs kiszolgáló kezelése varázsló > **kapcsolat beállítása**, válassza ki a hálózati Adaptert, amelyek megkapják a replikációs forgalmat. Ezután kattintson a **Save** (Mentés) gombra. Ez a beállítás nem módosítható, miután van konfigurálva.
2. A **válassza ki a Recovery Services-tároló**, válassza ki az Azure-előfizetéshez, és a megfelelő erőforráscsoport és a tároló.
3. A **harmadik féltől származó szoftverek telepítése**fogadja el a licenc agreeemtn, és kattintson a **töltse le és telepítse**, MySQL-kiszolgáló telepítéséhez.
4. Kattintson a **telepíteni a VMware PowerLCI**. Győződjön meg arról, hogy az összes böngészőablakot be van zárva, mielőtt ezt megtehetné. Kattintson a **Folytatás**
5. A **ellenőrzése készülék konfigurációs**, Előfeltételek a rendszer ellenőrzi a folytatás előtt.
6. A **vCenter-kiszolgáló vagy vSphere ESXi kiszolgáló konfigurálása**, adja meg az FQDN vagy IP-címet a vCenter-kiszolgáló vagy vSphere-gazdagép, mely virtuális gépeken a replikálni kívánt találhatók. Adja meg a portot, amelyet a kiszolgáló figyel, és egy rövid nevet használt a VMware-kiszolgáló újraregisztrálásával a felhőben.
7. Adja meg a konfigurációs kiszolgáló által a VMware-kiszolgálóhoz való csatlakozáshoz használandó hitelesítő adatokat. A Site Recovery automatikusan észlelni a VMware virtuális gépek, amelyek érhető(k) el replikálásra ezeket a hitelesítő adatokat használja. Kattintson a **Hozzáadás**, és kattintson a **Folytatás**.
8. A **virtuális gép hitelesítő adatok beállítása**, adja meg a felhasználónevet és jelszót, hogy a mobilitási szolgáltatás automatikusan telepítse a gépeken, ha engedélyezve van a replikáció történik. Windows-alapú gépek a fiók helyi rendszergazdai jogosultságokkal a replikálni kívánt gépeken kell. Linux adja meg a rendszergazdafiók részletei.
9. Kattintson a **véglegesítő konfigurációs** és elvégezheti a regisztrálást. 
10. Az Azure portálon, a regisztráció befejezését követően ellenőrizze, hogy a konfigurációs kiszolgáló és a VMware-kiszolgáló szerepel a **forrás** lap a tárolóban lévő állapottal. Kattintson a **OK** tároló beállításainak konfigurálása.


## <a name="troubleshoot-deployment-issues"></a>Üzembe helyezési problémák elhárítása

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]



## <a name="next-steps"></a>További lépések

Tekintse át a vész-helyreállítási beállításával kapcsolatos oktatóanyagok [VMware virtuális gépek](tutorial-vmware-to-azure.md) és [fizikai kiszolgálók](tutorial-physical-to-azure.md) az Azure-bA.
