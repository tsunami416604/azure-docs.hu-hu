---
title: A VMware vész-helyreállítási konfigurációs kiszolgáló üzembe helyezése Azure Site Recoverysal | Microsoft Docs
description: Ez a cikk bemutatja, hogyan helyezhet üzembe egy konfigurációs kiszolgálót a VMware vész-helyreállításhoz Azure Site Recovery
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/15/2019
ms.author: ramamill
ms.openlocfilehash: 5812cc73fb1da58c591d0593e079851e05bd0940
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331958"
---
# <a name="deploy-a-configuration-server"></a>Konfigurációs kiszolgáló üzembe helyezése

Ha [Azure site Recoveryt](site-recovery-overview.md) használ a VMWare virtuális gépek és a fizikai kiszolgálók Azure-ba történő helyreállításához, a helyszíni konfigurációs kiszolgálót is üzembe kell helyeznie. A konfigurációs kiszolgáló koordinálja a helyszíni VMware és az Azure közötti kommunikációt. Emellett az adatreplikálást is kezeli. Ez a cikk végigvezeti a konfigurációs kiszolgáló üzembe helyezéséhez szükséges lépéseken, amikor a VMware virtuális gépeket az Azure-ba replikálja. [Kövesse ezt a cikket](physical-azure-set-up-source.md) , ha konfigurációs kiszolgálót kell beállítania a fizikai kiszolgálók replikálásához.

> [!TIP]
> A konfigurációs kiszolgáló szerepkörének megismeréséhez Azure Site Recovery architektúra részeként [itt](vmware-azure-architecture.md)olvashat.

## <a name="deployment-of-configuration-server-through-ova-template"></a>A konfigurációs kiszolgáló üzembe helyezése a petesejtek sablonnal

A konfigurációs kiszolgálót egy olyan, magasan elérhető VMware virtuális gépnek kell beállítani, amely bizonyos minimális hardver-és méretezési követelményekkel rendelkezik. A kényelmes és egyszerű üzembe helyezés érdekében a Site Recovery letölthető PETESEJTeket (nyílt virtualizációs alkalmazás) tartalmazó sablont biztosít a konfigurációs kiszolgáló beállításához, amely megfelel az alább felsorolt összes előírt követelménynek.

## <a name="prerequisites"></a>Előfeltételek

A konfigurációs kiszolgáló minimális hardverkövetelmények a következő táblázatban vannak összegezve.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="azure-active-directory-permission-requirements"></a>Azure Active Directory engedélyekre vonatkozó követelmények

A konfigurációs kiszolgáló Azure Site Recovery-szolgáltatásokkal való regisztrálásához a **következő engedélyek egyikével** kell RENDELKEZNIE: hre (Azure Active Directory).

1. Az alkalmazás létrehozásához a felhasználónak "Application Developer" szerepkörrel kell rendelkeznie.
   1. Az ellenőrzéshez jelentkezzen be Azure Portal</br>
   1. Navigáljon Azure Active Directory > szerepkörökhöz és rendszergazdákhoz</br>
   1. Ellenőrizze, hogy az "Application Developer" szerepkör hozzá van-e rendelve a felhasználóhoz. Ha nem, használja ezt az engedélyt használó felhasználót, vagy érje el [a rendszergazdát, hogy engedélyezze az engedélyt](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal#assign-roles).
    
1. Ha az "alkalmazás-fejlesztő" szerepkör nem rendelhető hozzá, győződjön meg arról, hogy a "felhasználó regisztrálhatja az alkalmazást" jelző értéke TRUE (igaz), ha a felhasználó identitást hoz létre. A fenti engedélyek engedélyezéséhez
   1. Bejelentkezés az Azure Portalra
   1. Navigáljon Azure Active Directory > felhasználói beállítások
   1. A * * Alkalmazásregisztrációk "alatt a" felhasználók regisztrálhatnak alkalmazásokat "beállítás" yes "(igen) értékűnek kell lennie.

      ![AAD_application_permission](media/vmware-azure-deploy-configuration-server/AAD_application_permission.png)

> [!NOTE]
> A Active Directory összevonási szolgáltatások (AD FS) (ADFS) **nem támogatott**. Használjon [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)használatával felügyelt fiókot.

## <a name="capacity-planning"></a>Kapacitástervezés

A konfigurációs kiszolgáló méretezési követelményei a lehetséges adatváltozási aránytól függenek. Ezt a táblázatot útmutatóként használhatja.

| **CPU** | **Memória** | **Gyorsítótárazott lemez mérete** | **Adatváltozási arány** | **Védett gépek** |
| --- | --- | --- | --- | --- |
| 8 vCPU (2 szoftvercsatorna * 4 mag \@ 2,5 GHz) |16 GB |300 GB |500 GB vagy kevesebb |A replikálás kevesebb, mint 100 gép. |
| 12 vCPU (2 szoftvercsatorna * 6 mag \@ 2,5 GHz) |18 GB |600 GB |500 GB – 1 TB |100-150 gép replikálása. |
| 16 vCPU (2 szoftvercsatorna * 8 mag \@ 2,5 GHz) |32 GB |1 TB |1 TB – 2 TB |150-200 gép replikálása. |

Ha több VMware virtuális gépet replikál, olvassa el a [kapacitás megtervezésével kapcsolatos szempontokat](site-recovery-plan-capacity-vmware.md). Futtassa a [Deployment Planner eszközt](site-recovery-deployment-planner.md) a VMware-replikációhoz.

## <a name="download-the-template"></a>A sablon letöltése

1. A tárolóban kattintson **Az infrastruktúra előkészítése** > **Forrás** elemre.
2. A **Forrás előkészítése** ablakban válassza a **+Konfigurációs kiszolgáló** elemet.
3. A **Kiszolgáló hozzáadása** panelen ellenőrizze, hogy a **Kiszolgálótípus** mezőben a **Konfigurációs kiszolgáló VMware-hez** érték jelenik meg.
4. Töltse le az Open Virtualization Application (petesejtek) sablont a konfigurációs kiszolgálóhoz.

   > [!TIP]
   >A konfigurációs kiszolgáló sablonjának legújabb verzióját közvetlenül a [Microsoft letöltőközpontból](https://aka.ms/asrconfigurationserver)is letöltheti.

> [!NOTE]
> A petesejtek sablonnal megadott licenc 180 napig érvényes próbaverzió. Ezen időszak közzététele után az ügyfélnek egy beszerzett licenccel kell aktiválnia a Windowst.

## <a name="import-the-template-in-vmware"></a>A sablon importálása a VMware-ben

1. Jelentkezzen be a VMware vCenter-kiszolgálóra vagy a vSphere ESXi-gazdagépre a VMWare vSphere Client használatával.
2. A **File** (Fájl) menüben válassza a **Deploy OVF Template** (OVF-sablon telepítése) lehetőséget az OVF-sablon telepítése varázsló elindításához.

     ![OVF-sablon](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. A **forrás kiválasztása**területen adja meg a letöltött OVF helyét.
4. A **részletek áttekintése**lapon válassza a **tovább**lehetőséget.
5. A **név és mappa kiválasztása** és a **konfiguráció kiválasztása lapon**fogadja el az alapértelmezett beállításokat.
6. A **Select storage** (Tároló kiválasztása) területen a legjobb teljesítmény érdekében válassza a **Thick Provision Eager Zeroed** formátumot a **Select virtual disk format** (Virtuális lemez formátumának kiválasztása) mezőben. A dinamikus kiépítési lehetőség használata hatással lehet a konfigurációs kiszolgáló teljesítményére.
7. A varázsló többi lapján fogadja el az alapértelmezett beállításokat.
8. A **Ready to complete** (Befejezésre kész) területen:

    * A virtuális gép alapértelmezett beállításokkal való telepítéséhez válassza a **Power on after deployment** > **Finish** (Bekapcsolás a telepítést követően > Befejezés) elemet.

    * További hálózati adapter hozzáadásához törölje **a bekapcsolást a telepítés után**, majd kattintson a **Befejezés gombra**. Alapértelmezés szerint a konfigurációs kiszolgáló sablonját a rendszer egyetlen hálózati adapterre telepíti. Az üzembe helyezés után további hálózati adaptereket is hozzáadhat.

> [!IMPORTANT]
> A telepítés után ne módosítsa az erőforrás-konfigurációkat (memória/magok/CPU-korlátozás), módosítsa/törölje a telepített szolgáltatásokat vagy fájlokat a konfigurációs kiszolgálón. Ez hatással lesz a konfigurációs kiszolgáló Azure-szolgáltatásokkal való regisztrálására és a konfigurációs kiszolgáló teljesítményére.

## <a name="add-an-additional-adapter"></a>További adapter hozzáadása

> [!NOTE]
> Két hálózati adapterre van szükség, ha azt tervezi, hogy a feladatátvétel során megtartja a forrásoldali gépek IP-címeit, és később szeretné feladat-visszavételt végezni a helyszínen. Az egyik hálózati adapter csatlakoztatva lesz a forrásoldali gépekhez, és a másik hálózati adaptert fogja használni az Azure-kapcsolathoz.

Ha további hálózati adaptert szeretne hozzáadni a konfigurációs kiszolgálóhoz, adja hozzá azt a tárolóban lévő kiszolgáló regisztrálása előtt. A regisztrálást követően további adapterek hozzáadása nem támogatott.

1. A vSphere Client-leltárban kattintson a jobb gombbal a virtuális gépre, és válassza az **Edit Settings** (Beállítások szerkesztése) elemet.
2. A **Hardware** (Hardver) területen válassza az **Add** > **Ethernet Adapter** (Hozzáadás > Ethernet-adapter) elemet. Ezután kattintson a **Tovább** gombra.
3. Válassza ki a hálózati adapter típusát és a hálózatot.
4. Ha a virtuális hálózati adaptert a virtuális gép bekapcsolása után szeretné összekapcsolni, válassza a **Csatlakozás a Power-on**lehetőséget. Ezután válassza a **Next** > **Befejezés** > **OK**elemet.

## <a name="register-the-configuration-server-with-azure-site-recovery-services"></a>A konfigurációs kiszolgáló regisztrálása Azure Site Recovery szolgáltatásokkal

1. A VMWare vSphere Client-konzolon kapcsolja be a virtuális gépet.
2. A virtuális gép a bekapcsolásakor egy Windows Server 2016 telepítési folyamatot indít el. Fogadja el a licencszerződést, és adjon meg egy rendszergazdai jelszót.
3. A telepítés befejezése után jelentkezzen be rendszergazdaként a virtuális gépre.
4. Amikor először jelentkezik be, néhány másodpercen belül elindul az Azure Site Recovery konfigurációs eszköz.
5. Adjon meg egy nevet, amelyen a konfigurációs kiszolgálót regisztrálja a Site Recovery szolgáltatásban. Ezután kattintson a **Tovább** gombra.
6. Az eszköz ellenőrzi, hogy a virtuális gép tud-e csatlakozni az Azure-hoz. A kapcsolat létrejötte után a **Sign in** (Bejelentkezés) gombra kattintva jelentkezzen be az Azure-előfizetésbe.</br>
    a. Olyan hitelesítő adatokra van szükség, amelyekkel hozzá lehet férni a tárolóhoz, amelyben regisztrálni kívánja a konfigurációs kiszolgálót.</br>
    b. Győződjön meg arról, hogy a kiválasztott felhasználói fiók rendelkezik az alkalmazás Azure-beli létrehozásához szükséges engedélyekkel. A szükséges engedélyek engedélyezéséhez kövesse az [itt](#azure-active-directory-permission-requirements)megadott irányelveket.
7. Az eszköz végrehajt néhány konfigurációs feladatot, majd újraindul.
8. Jelentkezzen be újra a gépre. A konfigurációs kiszolgáló felügyeleti varázslója néhány másodpercen belül **automatikusan** elindul.

### <a name="configure-settings"></a>Beállítások konfigurálása

1. A konfigurációs kiszolgáló kezelése varázslóban válassza a **kapcsolat beállítása**lehetőséget. A legördülő listából válassza ki azt a hálózati adaptert, amelyet a beépített Process Server használ a mobilitási szolgáltatás felderítéséhez és leküldéses telepítéséhez a forrásoldali gépeken, majd válassza ki azt a hálózati adaptert, amelyet a konfigurációs kiszolgáló az Azure-hoz való kapcsolódáshoz használ. Ezután válassza a **Save** (Mentés) lehetőséget. Ez a beállítás a konfigurálás után nem módosítható. Erősen ajánlott a konfigurációs kiszolgáló IP-címének módosítása. Győződjön meg arról, hogy a konfigurációs kiszolgálóhoz rendelt IP-cím statikus IP-cím, nem DHCP-IP-cím.
2. A **Recovery Services-tároló kiválasztása lapon**jelentkezzen be Microsoft Azure a "[konfigurációs kiszolgáló regisztrálása a Azure site Recovery Services szolgáltatásban](#register-the-configuration-server-with-azure-site-recovery-services)" című **témakör 6. lépésében** használt hitelesítő adatokkal.
3. Bejelentkezés után válassza ki az Azure-előfizetését, valamint a megfelelő erőforráscsoportot és tárolót.

    > [!NOTE]
    > A regisztrálást követően nincs rugalmasság a Recovery Services-tároló módosítására.
    > A Recovery Services-tároló módosításához meg kell szüntetni a konfigurációs kiszolgáló aktuális tárból való társítását, és a konfigurációs kiszolgáló alatt lévő összes védett virtuális gép replikálása leállt. További [információ](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault).

4. A **külső gyártótól származó szoftverek telepítése**,

    |Alkalmazási helyzet   |Követendő lépések  |
    |---------|---------|
    |Tudom letölteni & a MySQL telepítését manuálisan?     |  Igen. Töltse le a MySQL-alkalmazást & helyezze a mappába a **C:\Temp\ASRSetup**, majd telepítse manuálisan. Most, ha elfogadja a feltételeket > kattintson a **letöltés és telepítés**gombra, a portálon *már telepítve van*. Folytassa a következő lépéssel.       |
    |El tudom kerülni a MySQL online letöltését?     |   Igen. Helyezze a MySQL Installer-alkalmazást a **C:\Temp\ASRSetup**mappába. Fogadja el a feltételeket > kattintson a **letöltés és telepítés**lehetőségre, a portálon az Ön által hozzáadott telepítőt fogja használni, és telepíti az alkalmazást. A telepítés után folytassa a következő lépéssel.    |
    |Szeretném letölteni & telepíteni a MySQL-t Azure Site Recovery     |  Fogadja el a licencszerződést & kattintson a **letöltés és telepítés**gombra. Ezután folytassa a telepítés utáni következő lépéssel.       |

5. A **Berendezés konfigurációjának ellenőrzése** területen a rendszer ellenőrzi az előfeltételeket, mielőtt továbblépne.
6. A **VCenter-kiszolgáló vagy vSphere ESXi-kiszolgáló hitelesítő adatainak konfigurálása** területen adja meg azon vCenter-kiszolgáló vagy VSphere-gazdagép teljes tartománynevét vagy IP-címét, ahol a replikálni kívánt virtuális gépek találhatók. Adja meg a portot, amelyen a kiszolgáló figyel. Adja meg a VMware-kiszolgáló rövid nevét, amelyet a tárolóban használni fog.
7. Adja meg a konfigurációs kiszolgáló által a VMware-kiszolgálóhoz való csatlakozáshoz használt hitelesítő adatokat. A Site Recovery ezeknek a hitelesítő adatoknak a használatával automatikusan deríti fel a replikáláshoz elérhető VMware virtuális gépeket. Válassza a **Hozzáadás**, majd a **Folytatás**lehetőséget. Az itt megadott hitelesítő adatok helyileg mentve.
8. A **virtuális gép hitelesítő adatainak konfigurálása**területen adja meg a virtuális gépek felhasználónevét és jelszavát, hogy a replikáció során automatikusan telepítse a mobilitási szolgáltatást. **Windows rendszerű** gépek esetén a fióknak helyi rendszergazdai jogosultságokkal kell rendelkeznie a replikálni kívánt gépeken. **Linux**esetén adja meg a legfelső szintű fiók részleteit.
9. Válassza a **Konfiguráció véglegesítése** elemet a regisztráció befejezéséhez.
10. A regisztráció befejeződése után nyissa meg a Azure Portalt, és ellenőrizze, hogy a konfigurációs kiszolgáló és a VMware-kiszolgáló szerepel-e a **Recovery Services**-tárolóban  > **kezelése** > **site Recovery infrastruktúra** > **konfiguráció Kiszolgálók**.

## <a name="upgrade-the-configuration-server"></a>A konfigurációs kiszolgáló frissítése

A konfigurációs kiszolgáló legújabb verzióra való frissítéséhez kövesse az alábbi [lépéseket](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server). Az összes Site Recovery-összetevő frissítésével kapcsolatos részletes információkért kattintson [ide](service-updates-how-to.md).

## <a name="manage-the-configuration-server"></a>A konfigurációs kiszolgáló kezelése

A folyamatban lévő replikáció megszakításának elkerülése érdekében győződjön meg arról, hogy a konfigurációs kiszolgáló IP-címe nem változik, miután a konfigurációs kiszolgáló regisztrálva van egy tárolóban. További információt [itt](vmware-azure-manage-configuration-server.md)talál a gyakori konfigurációs kiszolgáló felügyeleti feladatairól.

## <a name="faq"></a>Gyakori kérdések

1. Mennyi ideig érvényes a OVF-en keresztül üzembe helyezett konfigurációs kiszolgálón megadott licenc? Mi történik, ha nem aktiválom újra a licencet?

    A petesejtek sablonnal megadott licenc 180 napig érvényes próbaverzió. A lejárat előtt aktiválnia kell a licencet. Ez azt eredményezheti, hogy a konfigurációs kiszolgáló gyakori leállítása, így a replikálási tevékenységek akadályozása.

2. Használhatom a virtuális gépet, ahol a konfigurációs kiszolgáló telepítve van, különböző célokra?

    **Nem**, javasoljuk, hogy a virtuális gépet a konfigurációs kiszolgáló kizárólagos céljára használja. Győződjön meg arról, hogy a vész-helyreállítás hatékony kezelésének [előfeltételei](#prerequisites) között szereplő összes specifikációt követi.
3. Átválthatom a tárolót a konfigurációs kiszolgálón már regisztrált új tárolóval?

    **Nem**, ha a tároló regisztrálva van a konfigurációs kiszolgálón, az nem módosítható.
4. Használhatom ugyanazt a konfigurációs kiszolgálót mind a fizikai, mind a virtuális gépek védelmére?

    **Igen**, ugyanazt a konfigurációs kiszolgálót lehet használni a fizikai és a virtuális gépek replikálásához. A fizikai gép azonban csak a VMware virtuális gépeken végezhető el.
5. Mi a konfigurációs kiszolgáló célja, és hol van használatban?

    A konfigurációs kiszolgálóval és annak funkcionalitásával kapcsolatos további tudnivalókért tekintse meg a [VMware – Azure replikálási architektúrát](vmware-azure-architecture.md) .
6. Hol találhatom meg a konfigurációs kiszolgáló legújabb verzióját?

    A konfigurációs kiszolgáló portálon keresztüli frissítésének lépéseiért lásd: [a konfigurációs kiszolgáló frissítése](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server). Az összes Site Recovery-összetevő frissítésének részletes ismertetését [itt](https://aka.ms/asr_how_to_upgrade)tekintheti meg.
7. Honnan tölthetők le a konfigurációs kiszolgáló jelszava?

    A jelszó letöltéséhez tekintse meg [ezt a cikket](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) .
8. Módosítható a jelszó?

    **Nem**, **nyomatékosan javasoljuk, hogy ne változtassa meg a** konfigurációs kiszolgáló jelszavát. A jelszó módosítása megszakítja a védett gépek replikálását, és kritikus állapotba vezet.
9. Honnan tölthetők le a tároló regisztrációs kulcsai?

    A **Recovery Services**-tárolóban **kezelje** > **site Recovery infrastruktúra** > **konfigurációs kiszolgálókat**. A kiszolgálók területen válassza a **regisztrációs kulcs letöltése** lehetőséget a tár hitelesítő adatainak fájljának letöltéséhez.
10. Megtehetem a meglévő konfigurációs kiszolgáló klónozását és a replikálási folyamathoz való használatát?

    **Nem**, a klónozott konfigurációs kiszolgáló összetevő használata nem támogatott. A kibővíthető folyamat kiszolgálójának klónozása szintén nem támogatott forgatókönyv. A klónozás Site Recovery összetevők befolyásolják a folyamatban lévő replikációkat.

11. Módosíthatom a konfigurációs kiszolgáló IP-címét?

    **Nem**, erősen ajánlott a konfigurációs kiszolgáló IP-címének módosítása. Győződjön meg arról, hogy a konfigurációs kiszolgálóhoz rendelt összes IP-cím statikus IP-címek és nem DHCP-IP-címek.
12. Beállítható a konfigurációs kiszolgáló az Azure-ban?

    Azt javasoljuk, hogy a konfigurációs kiszolgálót helyszíni környezetben, közvetlen, a v-centerrel és az adatátvitel késleltetésének minimalizálásával állítsa be. A konfigurációs kiszolgáló ütemezett biztonsági mentését feladat- [visszavételi célokra](vmware-azure-manage-configuration-server.md#failback-requirements)is elvégezheti.

A konfigurációs kiszolgálóval kapcsolatos további gyakori kérdésekért tekintse meg a [konfigurációs kiszolgáló dokumentációját](vmware-azure-common-questions.md#configuration-server) .

## <a name="troubleshoot-deployment-issues"></a>Üzembe helyezési problémák elhárítása

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]



## <a name="next-steps"></a>Következő lépések

Állítsa be a [VMWare virtuális gépek](vmware-azure-tutorial.md) vész-helyreállítását az Azure-ba.
