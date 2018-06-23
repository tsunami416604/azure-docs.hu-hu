---
title: A konfigurációs kiszolgáló VMware vész-helyreállítási az Azure Site Recovery üzembe |} Microsoft Docs
description: Ez a cikk ismerteti, hogyan VMware vész-helyreállítási az Azure Site Recovery konfigurációs kiszolgálók központi telepítéséhez
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 05/06/2018
ms.author: raynew
ms.openlocfilehash: 841176d8c5f215d18edf25b1f191792b37555fa9
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2018
ms.locfileid: "36318119"
---
# <a name="deploy-a-configuration-server"></a>Konfigurációs kiszolgáló üzembe helyezése

Telepít egy a helyszíni konfigurációs kiszolgáló használatakor [Azure Site Recovery](site-recovery-overview.md) vész-helyreállítási VMware virtuális gépek és fizikai kiszolgálók Azure-bA. A konfigurációs kiszolgáló koordináták kommunikáció közötti helyszíni VMware és Azure. Adatreplikálás is kezeli. Ez a cikk végigvezeti a lépéseken, amelyekkel a konfigurációs kiszolgáló központi telepítése, ha VMware virtuális gépeket replikál az Azure-bA. [Kövesse az ebben a cikkben](physical-azure-set-up-source.md) Ha, be kell állítania a fizikai kiszolgáló replikációs konfigurációs kiszolgálót.

>[!TIP]
Megismerheti az Azure Site Recovery architektúrájáról részeként konfigurációs kiszolgáló szerepkör [Itt](vmware-azure-architecture.md).

## <a name="deployment-of-configuration-server-through-ova-template"></a>Központi telepítés konfigurációs kiszolgáló petesejtek sablon segítségével

Konfigurációs kiszolgáló, az egyes minimális hardver- és méretezési követelményei magas rendelkezésre állású VMware virtuális gépként kell beállítani. Kényelmessé és egyszerűvé telepítéshez a Site Recovery biztosít egy letölthető petesejtek (nyitott Virtualization alkalmazás) sablont a konfigurációs kiszolgáló, amely megfelel az alábbi felhatalmazott követelmények beállításához.

## <a name="prerequisites"></a>Előfeltételek

Minimális hardverkövetelmények a konfigurációs kiszolgálón az alábbi táblázat foglalja össze.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="capacity-planning"></a>Kapacitástervezés

A kiszolgáló méretezési követelményei a potenciális adatmódosítási arány függ. Használja ezt a táblázatot útmutatóként.

| **CPU** | **Memória** | **Gyorsítótár-lemez mérete** | **Adatváltozási sebesség** | **Védett gépek** |
| --- | --- | --- | --- | --- |
| 8 Vcpu (2 sockets * @ 2,5 GHz, 4 mag) |16 GB |300 GB |500 GB vagy kevesebb |100-nál kevesebb gépek replikálása. |
| 12 Vcpu (2 sockets * @ 2,5 GHz-es 6 mag) |18 GB |600 GB |1 TB 500 GB |100-150 gépek replikálása. |
| 16 Vcpu (2 sockets * @ 2,5 GHz-es 8 mag) |32 GB |1 TB |1 TB-os és 2 TB |150-200 gépek replikálása. |

Ha egynél több VMware virtuális gép replikál, olvassa el [kapacitástervezésének szempontjai](/site-recovery-plan-capacity-vmware.md). Futtassa a [Deployment planner eszköz](site-recovery-deployment-planner.md) a VMWare-replikáció.

## <a name="download-the-template"></a>A sablon letöltése

1. A tárolóban kattintson **Az infrastruktúra előkészítése** > **Forrás** elemre.
2. A **Forrás előkészítése** ablakban válassza a **+Konfigurációs kiszolgáló** elemet.
3. A **Kiszolgáló hozzáadása** panelen ellenőrizze, hogy a **Kiszolgálótípus** mezőben a **Konfigurációs kiszolgáló VMware-hez** érték jelenik meg.
4. Töltse le a nyitott virtualizálási alkalmazás (petesejtek) sablont a konfigurációs kiszolgáló.

  > [!TIP]
>Emellett letöltheti a legújabb közvetlenül a konfigurációs kiszolgáló sablon [Microsoft Download Center](https://aka.ms/asrconfigurationserver).

>[!NOTE]
Az engedély petesejtek sablonhoz megadott egy értékelési engedély 180 napig érvényes. POST ezen időszak alatt ügyféligények határidődátumával engedéllyel rendelkező a windows aktiválása.

## <a name="import-the-template-in-vmware"></a>A sablon importálása a VMware-ben

1. Jelentkezzen be a VMware vCenter-kiszolgálóra vagy a vSphere ESXi-gazdagépre a VMWare vSphere Client használatával.
2. A **File** (Fájl) menüben válassza a **Deploy OVF Template** (OVF-sablon telepítése) lehetőséget az OVF-sablon telepítése varázsló elindításához.

     ![OVF-sablon](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. A **forrás kiválasztása**, adja meg a letöltött OVF helyét.
4. A **tekintse át a részletek**, jelölje be **következő**.
5. A **név és a mappa kiválasztása** és **válassza konfigurációs**, fogadja el az alapértelmezett beállításokat.
6. A **Select storage** (Tároló kiválasztása) területen a legjobb teljesítmény érdekében válassza a **Thick Provision Eager Zeroed** formátumot a **Select virtual disk format** (Virtuális lemez formátumának kiválasztása) mezőben.
7. A varázsló többi lapján fogadja el az alapértelmezett beállításokat.
8. A **Ready to complete** (Befejezésre kész) területen:

    * A virtuális gép alapértelmezett beállításokkal való telepítéséhez válassza a **Power on after deployment** > **Finish** (Bekapcsolás a telepítést követően > Befejezés) elemet.

    * Adja hozzá a hálózati illesztővel, törölje a jelet **kapcsolja be a telepítést követően**, majd válassza ki **Befejezés**. Alapértelmezés szerint a konfigurációs kiszolgáló sablonját a rendszer egyetlen hálózati adapterre telepíti. Az üzembe helyezés után további hálózati adaptereket is hozzáadhat.

## <a name="add-an-additional-adapter"></a>További adapter hozzáadása

Ha meg szeretne adni egy további hálózati Adapterre a konfigurációs kiszolgáló, vegye fel előtt regisztrálja a kiszolgálót a tárolóban lévő állapottal. A regisztrálást követően további adapterek hozzáadása nem támogatott.

1. A vSphere Client-leltárban kattintson a jobb gombbal a virtuális gépre, és válassza az **Edit Settings** (Beállítások szerkesztése) elemet.
2. A **Hardware** (Hardver) területen válassza az **Add** > **Ethernet Adapter** (Hozzáadás > Ethernet-adapter) elemet. Ezután kattintson a **Tovább** gombra.
3. Válassza ki a hálózati adapter típusát és a hálózatot. 
4. Csatlakozás a virtuális hálózati Adaptert a virtuális gép bekapcsolása, válassza ki **bekapcsolási: Csatlakozás**. Válassza ki **következő** > **Befejezés** > **OK**.

## <a name="register-the-configuration-server-with-azure-site-recovery-services"></a>A konfigurációs kiszolgáló regisztrálása az Azure Site Recovery services

1. A VMWare vSphere Client-konzolon kapcsolja be a virtuális gépet.
2. A virtuális gép a bekapcsolásakor egy Windows Server 2016 telepítési folyamatot indít el. Fogadja el a licencszerződést, és adjon meg egy rendszergazdai jelszót.
3. A telepítés befejezése után jelentkezzen be rendszergazdaként a virtuális gépre.
4. Az első bejelentkezéskor, néhány másodpercen belül az Azure Site Recovery Configuration Tool kezdődik.
5. Adjon meg egy nevet, amelyen a konfigurációs kiszolgálót regisztrálja a Site Recovery szolgáltatásban. Ezután kattintson a **Tovább** gombra.
6. Az eszköz ellenőrzi, hogy a virtuális gép tud-e csatlakozni az Azure-hoz. A kapcsolat létrejötte után a **Sign in** (Bejelentkezés) gombra kattintva jelentkezzen be az Azure-előfizetésbe. Olyan hitelesítő adatokra van szükség, amelyekkel hozzá lehet férni a tárolóhoz, amelyben regisztrálni kívánja a konfigurációs kiszolgálót.
7. Az eszköz végrehajt néhány konfigurációs feladatot, majd újraindul.
8. Jelentkezzen be újra a gépre. Elindul a konfigurációs kiszolgáló kezelése varázsló **automatikusan** néhány másodperc múlva.

### <a name="configure-settings"></a>Beállítások konfigurálása

1. A konfigurációs kiszolgáló felügyeleti varázslójában válassza a **Setup connectivity** (Kapcsolat beállítása) gombot, majd válassza ki a folyamatkiszolgáló által a virtuális gépekről érkező replikációs forgalom fogadására használt hálózati adaptert. Ezután válassza a **Save** (Mentés) lehetőséget. Ez a beállítás nem módosítható, miután van konfigurálva.
2. A **válassza ki a Recovery Services-tároló**, jelentkezzen be a Microsoft Azure, válassza ki az Azure-előfizetés és a megfelelő erőforráscsoport és a tárolóban.
    >[!NOTE]
    > Regisztrálás után nincs nincs rugalmasan módosíthatja a recovery services-tároló.
3. A **harmadik féltől származó szoftverek telepítése**,

    |Forgatókönyv   |A követendő lépések  |
    |---------|---------|
    |Töltse le és telepítse manuálisan a MySQL?     |  Igen. MySQL-alkalmazás letöltése és a mappában **C:\Temp\ASRSetup**, majd manuálisan telepíti. Mostantól, ha elfogadja a feltételeket > kattintson a **töltse le és telepítse**, a portál szerint *már telepített*. Folytassa a következő lépéssel.       |
    |Elkerülheti a MySQL letöltésének online?     |   Igen. A MySQL installer-alkalmazást a mappába helyezett **C:\Temp\ASRSetup**. A feltételek elfogadásának > kattintson a **töltse le és telepítse**, a portálon az Ön által hozzáadott telepítő fogja használni, és telepíti az alkalmazást. A következő lépés utáni telepítése lépne.    |
    |Töltse le és telepítse az Azure Site Recovery segítségével MySQL szeretnék     |  Fogadja el a licencszerződést, és kattintson a **töltse le és telepítse**. Majd folytassa a következő lépés feladás egy vagy több telepítésre.       |
4. A **Berendezés konfigurációjának ellenőrzése** területen a rendszer ellenőrzi az előfeltételeket, mielőtt továbblépne.
5. A **VCenter-kiszolgáló vagy vSphere ESXi-kiszolgáló hitelesítő adatainak konfigurálása** területen adja meg azon vCenter-kiszolgáló vagy VSphere-gazdagép teljes tartománynevét vagy IP-címét, ahol a replikálni kívánt virtuális gépek találhatók. Adja meg a portot, amelyen a kiszolgáló figyel. Adja meg a VMware-kiszolgáló rövid nevét, amelyet a tárolóban használni fog.
6. Adja meg a konfigurációs kiszolgáló által a VMware-kiszolgálóhoz való csatlakozáshoz használt hitelesítő adatokat. A Site Recovery ezeknek a hitelesítő adatoknak a használatával automatikusan deríti fel a replikáláshoz elérhető VMware virtuális gépeket. Válassza ki **hozzáadása**, majd **továbbra is**. Az itt megadott hitelesítő adatok helyben menti.
7. A **virtuális gép hitelesítő adatok beállítása**, adja meg a felhasználónevet és jelszót a virtuális gépek automatikusan telepíteni a mobilitási szolgáltatás replikáció során. A **Windows** gépek, a fiók a replikálni kívánt gépeken helyi rendszergazdai jogosultságokkal kell rendelkeznie. A **Linux**, rendszergazdafiók részletesen.
8. Válassza a **Konfiguráció véglegesítése** elemet a regisztráció befejezéséhez.
9. Regisztráció befejezése után nyissa meg az Azure portált, ellenőrizze, hogy a konfigurációs kiszolgáló és a VMware-kiszolgáló szerepel **Recovery Services-tároló** > **kezelése**  >  **Site Recovery-infrastruktúra** > **konfigurációs kiszolgálók**.

## <a name="faq"></a>GYIK

1. A virtuális gép, amelyen telepítve van a konfigurációs kiszolgáló többféle célra használható? **Nem**, a konfigurációs kiszolgáló egycélú kiszolgálónak kell lennie, és megosztott kiszolgálóként való használata nem támogatott.
2. Váltson a tárolóban már regisztrálva van a konfigurációs kiszolgálón egy újonnan létrehozott tárolóban? **Nem**, miután egy tárolót a konfigurációs kiszolgáló regisztrálva van, ezért nem módosítható.
3. Használhatok ugyanazon a konfigurációs kiszolgálón a fizikai és a virtuális gépek védelméhez? **Igen**, ugyanarra a konfigurációs kiszolgálóra fizikai és virtuális gépek replikálása is használható. A fizikai gép feladat-visszavétel azonban nem támogatott.
4. Ha konfigurációs kiszolgáló használható? Tekintse meg az Azure Site Recovery architektúrájáról [Itt](vmware-azure-architecture.md) tudhat meg többet a konfigurációs kiszolgáló és a funkciók.
5. Hol található a konfigurációs kiszolgáló legújabb verzióját? Közvetlenül letöltheti a [Microsoft Download Center](https://aka.ms/asrconfigurationserver). A cikk a frissítési kiszolgáló lépéseket [Itt](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server).

## <a name="upgrade-the-configuration-server"></a>A konfigurációs kiszolgáló verziófrissítése

A kiszolgáló frissítése a legújabb verzióra, olvassa el a lépéseket [Itt](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)

## <a name="troubleshoot-deployment-issues"></a>Üzembe helyezési problémák elhárítása

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]



## <a name="next-steps"></a>További lépések

Állítsa be a vész-helyreállítási [VMware virtuális gépek](vmware-azure-tutorial.md) az Azure-bA.
