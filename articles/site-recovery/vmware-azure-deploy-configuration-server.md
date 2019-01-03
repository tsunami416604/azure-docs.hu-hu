---
title: Az Azure Site Recovery VMware-vészhelyreállításhoz használt konfigurációs kiszolgáló üzembe helyezése |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan helyezhet üzembe egy konfigurációs kiszolgáló VMware-vészhelyreállításhoz az Azure Site Recoveryvel
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 12/11/2018
ms.author: mayg
ms.openlocfilehash: 1efbd6bfb6f3bc3e5deae058b542f665b3153cdb
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/27/2018
ms.locfileid: "53794354"
---
# <a name="deploy-a-configuration-server"></a>Konfigurációs kiszolgáló üzembe helyezése

Egy helyszíni konfigurációs kiszolgálót telepít, használatakor [Azure Site Recovery](site-recovery-overview.md) vész-helyreállítási VMware virtuális gépek és fizikai kiszolgálók Azure-bA. A konfigurációs kiszolgáló koordináták kommunikációhoz között a helyszíni VMware és az Azure. Azt is felügyeli az adatreplikációt. Ez a cikk végigvezeti a konfigurációs kiszolgáló telepítése, ha VMware virtuális gépeket replikál az Azure-bA szükséges lépéseket. [Kövesse az ebben a cikkben](physical-azure-set-up-source.md) Ha kell állítani a konfigurációs kiszolgálót fizikai kiszolgáló replikációjához.

>[!TIP]
Megismerheti a konfigurációs kiszolgáló szerepe az Azure Site Recovery architektúrájáról részeként [Itt](vmware-azure-architecture.md).

## <a name="deployment-of-configuration-server-through-ova-template"></a>Konfigurációs kiszolgáló OVA sablon segítségével üzembe helyezés

Konfigurációs kiszolgáló, az egyes minimális hardver- és méretezési követelményei magas rendelkezésre állású VMware virtuális gép kell állítani. A kényelmes és egyszerű üzembe helyezés a Site Recovery egy letölthető OVA (Open Virtualization alkalmazás) sablont, állítsa be a konfigurációs kiszolgálót, amely megfelel az alábbi felhatalmazott követelményeket biztosít.

## <a name="prerequisites"></a>Előfeltételek

Minimális hardverkövetelmények a konfigurációs kiszolgáló az alábbi táblázat foglalja össze.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="capacity-planning"></a>Kapacitástervezés

A konfigurációs kiszolgáló méretezési követelményei potenciális adatváltozási sebesség függenek. Ez a táblázat használata alapján.

| **CPU** | **Memória** | **Gyorsítótár-lemez mérete** | **A módosult adatok aránya** | **Védett gépek** |
| --- | --- | --- | --- | --- |
| 8 Vcpu (2 sockets * 4 mag \@ 2,5 GHz-es) |16 GB |300 GB |500 GB vagy kevesebb |100-nál kevesebb gépeket replikálni. |
| 12 vcpu-k (2 sockets * 6 magok \@ 2,5 GHz-es) |18 GB |600 GB |500 GB – 1 TB |100 – 150 gépeket replikálni. |
| 16 vcpu-k (2 sockets * 8 magos \@ 2,5 GHz-es) |32 GB |1 TB |1 TB-os 2 TB-ig |150-200 gépeket replikálni. |

Ha egynél több VMware virtuális gép replikál, olvassa el a [kapacitástervezésének szempontjai](site-recovery-plan-capacity-vmware.md). Futtassa a [Deployment planner eszköz](site-recovery-deployment-planner.md) a VMWare replikálásához.

## <a name="download-the-template"></a>A sablon letöltése

1. A tárolóban kattintson **Az infrastruktúra előkészítése** > **Forrás** elemre.
2. A **Forrás előkészítése** ablakban válassza a **+Konfigurációs kiszolgáló** elemet.
3. A **Kiszolgáló hozzáadása** panelen ellenőrizze, hogy a **Kiszolgálótípus** mezőben a **Konfigurációs kiszolgáló VMware-hez** érték jelenik meg.
4. Töltse le a konfigurációs kiszolgáló az Open Virtualization alkalmazás (OVA) sablonját.

  > [!TIP]
>Emellett közvetlenül, a konfigurációs kiszolgálói sablon legújabb verzióját letöltheti [Microsoft Download Center](https://aka.ms/asrconfigurationserver).

>[!NOTE]
Az OVA sablonnal megadott engedély egy értékelési engedély 180 napig érvényes. POST ennek az időtartamnak kell határidődátumával engedéllyel rendelkező a windows aktiválása.

## <a name="import-the-template-in-vmware"></a>A sablon importálása a VMware-ben

1. Jelentkezzen be a VMware vCenter-kiszolgálóra vagy a vSphere ESXi-gazdagépre a VMWare vSphere Client használatával.
2. A **File** (Fájl) menüben válassza a **Deploy OVF Template** (OVF-sablon telepítése) lehetőséget az OVF-sablon telepítése varázsló elindításához.

     ![OVF-sablon](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. A **forrás kiválasztása**, adja meg a letöltött OVF helyét.
4. A **részletek áttekintése**válassza **tovább**.
5. A **név és mappa kiválasztása** és **válassza konfigurációs**, fogadja el az alapértelmezett beállításokat.
6. A **Select storage** (Tároló kiválasztása) területen a legjobb teljesítmény érdekében válassza a **Thick Provision Eager Zeroed** formátumot a **Select virtual disk format** (Virtuális lemez formátumának kiválasztása) mezőben.
7. A varázsló többi lapján fogadja el az alapértelmezett beállításokat.
8. A **Ready to complete** (Befejezésre kész) területen:

    * A virtuális gép alapértelmezett beállításokkal való telepítéséhez válassza a **Power on after deployment** > **Finish** (Bekapcsolás a telepítést követően > Befejezés) elemet.

    * Adjon hozzá további hálózati adaptereket, törölje a jelet **üzembe helyezés után a Power**, majd válassza ki **Befejezés**. Alapértelmezés szerint a konfigurációs kiszolgáló sablonját a rendszer egyetlen hálózati adapterre telepíti. Az üzembe helyezés után további hálózati adaptereket is hozzáadhat.

## <a name="add-an-additional-adapter"></a>További adapter hozzáadása

Ha szeretne további hálózati Adaptereket adhat hozzá a konfigurációs kiszolgálót, adja hozzá ahhoz regisztrálja a kiszolgálót a tárolóban. A regisztrálást követően további adapterek hozzáadása nem támogatott.

1. A vSphere Client-leltárban kattintson a jobb gombbal a virtuális gépre, és válassza az **Edit Settings** (Beállítások szerkesztése) elemet.
2. A **Hardware** (Hardver) területen válassza az **Add** > **Ethernet Adapter** (Hozzáadás > Ethernet-adapter) elemet. Ezután kattintson a **Tovább** gombra.
3. Válassza ki a hálózati adapter típusát és a hálózatot.
4. Csatlakoztassa a virtuális hálózati Adaptert, amikor a virtuális gép be van kapcsolva, jelölje be **bekapcsolási: Connect**. Válassza ki **tovább** > **Befejezés** > **OK**.

## <a name="register-the-configuration-server-with-azure-site-recovery-services"></a>A konfigurációs kiszolgáló regisztrálása az Azure Site Recovery szolgáltatásokkal

1. A VMWare vSphere Client-konzolon kapcsolja be a virtuális gépet.
2. A virtuális gép a bekapcsolásakor egy Windows Server 2016 telepítési folyamatot indít el. Fogadja el a licencszerződést, és adjon meg egy rendszergazdai jelszót.
3. A telepítés befejezése után jelentkezzen be rendszergazdaként a virtuális gépre.
4. Az első alkalommal bejelentkezik, néhány másodpercen belül az Azure Site Recovery Configuration Tool elindul.
5. Adjon meg egy nevet, amelyen a konfigurációs kiszolgálót regisztrálja a Site Recovery szolgáltatásban. Ezután kattintson a **Tovább** gombra.
6. Az eszköz ellenőrzi, hogy a virtuális gép tud-e csatlakozni az Azure-hoz. A kapcsolat létrejötte után a **Sign in** (Bejelentkezés) gombra kattintva jelentkezzen be az Azure-előfizetésbe. Olyan hitelesítő adatokra van szükség, amelyekkel hozzá lehet férni a tárolóhoz, amelyben regisztrálni kívánja a konfigurációs kiszolgálót.
7. Az eszköz végrehajt néhány konfigurációs feladatot, majd újraindul.
8. Jelentkezzen be újra a gépre. Elindul a konfigurációs kiszolgáló felügyeleti varázslójában **automatikusan** néhány másodperc múlva.

### <a name="configure-settings"></a>Beállítások konfigurálása

1. A konfigurációs kiszolgáló felügyeleti varázslójában válassza a **Setup connectivity** (Kapcsolat beállítása) gombot, majd válassza ki a folyamatkiszolgáló által a virtuális gépekről érkező replikációs forgalom fogadására használt hálózati adaptert. Ezután válassza a **Save** (Mentés) lehetőséget. Ez a beállítás konfigurálását követően nem módosítható. Javasoljuk, hogy ne módosítsa a konfigurációs kiszolgáló IP-címét. Győződjön meg arról, a konfigurációs kiszolgálóhoz rendelt IP-cím statikus és nem a DHCP IP-Címek.
2. A **helyreállítási tár kiválasztása**, jelentkezzen be a Microsoft Azure-ba, válassza ki az Azure-előfizetés és a megfelelő erőforráscsoportot és tárolót.

    > [!NOTE]
    > Miután regisztrált, nincs nincs rugalmasan módosíthatja a recovery services-tároló.

3. A **harmadik féltől származó szoftverek telepítése**,

    |Forgatókönyv   |Végrehajtandó lépések  |
    |---------|---------|
    |Töltse le és telepítse manuálisan a MySQL?     |  Igen. Töltse le a MySQL-alkalmazás és a mappában **C:\Temp\ASRSetup**, majd telepítse manuálisan. Most, ha elfogadja a szerződés > kattintson a **töltse le és telepítse**, a portál szerint *már telepítve van*. A következő lépéssel folytathatja.       |
    |Elkerülheti a MySQL letöltése online?     |   Igen. A MySQL-telepítő alkalmazás a mappába helyezett **C:\Temp\ASRSetup**. Fogadja el a használati > kattintson a **töltse le és telepítse**, a portálon az Ön által hozzáadott telepítő fogja használni, és telepíti az alkalmazást. Folytassa a következő lépés utáni telepítése.    |
    |Szeretném, ha letöltése és telepítése az Azure Site Recovery MySQL     |  Fogadja el a licencszerződést, és kattintson a **töltse le és telepítse**. Majd folytassa a következő lépés utáni telepítése.       |
4. A **Berendezés konfigurációjának ellenőrzése** területen a rendszer ellenőrzi az előfeltételeket, mielőtt továbblépne.
5. A **VCenter-kiszolgáló vagy vSphere ESXi-kiszolgáló hitelesítő adatainak konfigurálása** területen adja meg azon vCenter-kiszolgáló vagy VSphere-gazdagép teljes tartománynevét vagy IP-címét, ahol a replikálni kívánt virtuális gépek találhatók. Adja meg a portot, amelyen a kiszolgáló figyel. Adja meg a VMware-kiszolgáló rövid nevét, amelyet a tárolóban használni fog.
6. Adja meg a konfigurációs kiszolgáló által a VMware-kiszolgálóhoz való csatlakozáshoz használt hitelesítő adatokat. A Site Recovery ezeknek a hitelesítő adatoknak a használatával automatikusan deríti fel a replikáláshoz elérhető VMware virtuális gépeket. Válassza ki **hozzáadása**, majd **továbbra is**. Az itt megadott hitelesítő adatok helyben menti.
7. A **virtuális gép hitelesítő adatainak konfigurálása**, adja meg a felhasználónevet és jelszót a virtuális gépek, automatikusan telepíteni a mobilitási szolgáltatást a replikáció során. A **Windows** gépek, a fióknak rendelkeznie kell a helyi rendszergazdai jogosultságokkal a replikálni kívánt gépeken. A **Linux**, adja meg a rendszergazdai fiók adatait.
8. Válassza a **Konfiguráció véglegesítése** elemet a regisztráció befejezéséhez.
9. Regisztráció befejezését követően nyissa meg az Azure Portalon, ellenőrizze, hogy a konfigurációs kiszolgáló és a VMware-kiszolgáló szerepel-e **Recovery Services-tároló** > **kezelés**  >  **Site Recovery-infrastruktúra** > **konfigurációs kiszolgálók**.

## <a name="upgrade-the-configuration-server"></a>A konfigurációs kiszolgáló frissítése

A konfigurációs kiszolgáló frissítése a legújabb verzióra, kövesse az alábbi [lépéseket](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server).

## <a name="manage-the-configuration-server"></a>A konfigurációs kiszolgáló kezelése

Folyamatban lévő replikáció megszakítása a szükséges elkerülése érdekében győződjön meg arról, hogy a konfigurációs kiszolgáló IP-címe nem módosul a konfigurációs kiszolgáló regisztrálva van egy tárolóba után. További információ a konfigurációs kiszolgáló gyakori felügyeleti feladatok [Itt](vmware-azure-manage-configuration-server.md).

## <a name="faq"></a>GYIK

1. A virtuális gép, amelyen a konfigurációs kiszolgáló telepítve van, különböző célokra használható?

    **Nem**, azt javasoljuk, hogy a virtuális gép konfigurációs kiszolgáló egyetlen célra használható. Mindenképpen kövesse a említett összes specifikációk [Előfeltételek](#prerequisites) vész-helyreállítási hatékony kezelését.
2. Válthatok-e a tárolóban már regisztrálva van egy újonnan létrehozott tárolót a konfigurációs kiszolgálón?

    **Nem**, miután a tároló konfigurációs kiszolgáló regisztrálva van, ezért nem módosítható.
3. Használható fizikai és a virtuális gépek védelmére szolgáló ugyanarra a konfigurációs kiszolgálóra?

    **Igen**, ugyanarra a konfigurációs kiszolgálóra replikálni a fizikai és virtuális gépeken is használható. Azonban a fizikai gép rendszer nem csak a VMware virtuális gép vissza.
4. Mi az a célja a konfigurációs kiszolgáló, és amelyben a rendszer azt használja?

    Tekintse meg [VMware-ből az Azure-bA architektúra](vmware-azure-architecture.md) tudhat meg többet a konfigurációs kiszolgáló és a számítást.
5. Hol található a konfigurációs kiszolgáló legújabb verzióját?

    A portálon keresztül a konfigurációs kiszolgáló frissítése lépéseiért lásd: [a konfigurációs kiszolgáló frissítése](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server). Emellett közvetlenül is letöltheti a [Microsoft Download Center](https://aka.ms/asrconfigurationserver).
6. Honnan tölthetem le a konfigurációs kiszolgáló hozzáférési kódot?

    Tekintse meg [Ez a cikk](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) töltheti le a hozzáférési kódot.
7. Honnan tölthetem le tároló regisztrációs kulcsokat?

    Az a **Recovery Services-tároló**, **kezelése** > **Site Recovery-infrastruktúra** > **konfigurációskiszolgálók**. Válassza ki a kiszolgálók **regisztrációs kulcs letöltése** töltheti le a tároló hitelesítőadat-fájlja.
8. Klónozza a meglévő konfigurációs kiszolgáló és replikálás előkészítését használni?

    **Nem**, klónozott konfigurációs kiszolgáló összetevő nem támogatott.

9. Módosíthatom a konfigurációs kiszolgáló IP-cím?

    **Nem**, erősen ajánlott ne módosítsa a konfigurációs kiszolgáló IP-címét. Győződjön meg, hogy minden IP-címet, és a konfigurációs kiszolgáló hozzárendelt statikus IP-címek és a nem DHCP IP-címek.

## <a name="troubleshoot-deployment-issues"></a>Üzembe helyezési problémák elhárítása

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]



## <a name="next-steps"></a>További lépések

A vészhelyreállítás beállítása [VMware virtuális gépek](vmware-azure-tutorial.md) az Azure-bA.