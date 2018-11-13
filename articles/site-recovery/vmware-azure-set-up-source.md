---
title: A forrás környezet és a konfiguráció alkalmazásai vészhelyreállítás beállítása VMware virtuális gépek az Azure-bA az Azure Site Recoveryvel |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan állítható be a helyszíni környezet és a konfigurációs kiszolgáló VMware virtuális gépek vészhelyreállítása az Azure-bA az Azure Site Recoveryvel.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/29/2018
ms.author: ramamill
ms.openlocfilehash: e09e93ef85449c51e35b8da7ad93ee7088a0e7b4
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51566312"
---
# <a name="set-up-the-source-environment-and-configuration-server"></a>A forrás-környezet és a konfigurációs kiszolgáló beállítása

Ez a cikk azt ismerteti, hogyan állítható be a forráskörnyezetet a helyszíni VMware virtuális gépek vészhelyreállítása az Azure-bA a [Azure Site Recovery](site-recovery-overview.md). A Site Recovery konfigurációs kiszolgálóként a helyszíni gépre beállításának lépéseit tartalmazza, és automatikusan felderítése a helyszíni virtuális gépeket. 

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, a következőket hajtotta végre

- A vész helyreállítási központi telepítés használatával tervezett a [Azure Site Recovery Deployment Planner](site-recovery-deployment-planner.md). A Planner segít biztosítani, elegendő erőforrással rendelkezik, és a vész-helyreállítási sávszélességet és a helyreállítási pont célkitűzés (RPO) a szervezetben.
- [Állítsa be az Azure-erőforrások](tutorial-prepare-azure.md) a a [az Azure portal](http://portal.azure.com).
- [Állítsa be a helyszíni VMware](vmware-azure-tutorial-prepare-on-premises.md) erőforrások, például egy dedikált fiókot a helyszíni VMware virtuális gépek automatikus felderítését.
- Is [tekintse át a gyakori kérdéseket](vmware-azure-common-questions.md#configuration-server) kapcsolatos konfigurációs kiszolgáló üzembe helyezését és felügyeletét.


## <a name="choose-protection-goals"></a>Védelmi célok megválasztása

1. A **Helyreállítási tárak** listából válassza ki a tár nevét. 
2. Az **Első lépések** területen válassza ki a Site Recovery elemet. Ezután válassza **Az infrastruktúra előkészítése** lehetőséget.
3. A **Védelmi cél** > **Hol találhatók a gépek?** területen válassza a **Helyszíni** lehetőséget.
4. A **Hová szeretné replikálni a gépeket?** területen válassza **Az Azure-ba** lehetőséget.
5. A **Virtualizáltak a gépek?** területen válassza az **Igen, a VMware vSphere hipervizorral** lehetőséget. Ezután kattintson az **OK** gombra.

## <a name="about-the-configuration-server"></a>Tudnivalók a konfigurációs kiszolgáló

VMware virtuális gépek vészhelyreállítása az Azure-bA beállítása során telepít egy helyszíni konfigurációs kiszolgálót.

- A konfigurációs kiszolgáló koordináták kommunikációhoz között a helyszíni VMware és az Azure. Azt is felügyeli az adatreplikációt.
- A konfigurációs kiszolgáló VMware virtuális gépként üzembe helyezése.
- A Site recoveryben egy OVA-sablon letöltése az Azure Portalról, és importálja a vCenter-kiszolgálóhoz a konfigurációs kiszolgáló virtuális gép beállításához.
- [További](vmware-azure-architecture.md) a konfigurációs kiszolgáló összetevőit és folyamatokról.


>[!NOTE]
Ne használja ezeket az utasításokat, ha a használt konfigurációs kiszolgáló, valamint a helyszíni fizikai gépeket az Azure-bA telepíti. Ebben az esetben kövesse [Ez a cikk](physical-azure-set-up-source.md).


## <a name="before-you-deploy-the-configuration-server"></a>A konfigurációs kiszolgáló telepítése előtt

Ha a konfigurációs kiszolgáló az OVA-sablon használatával VMware virtuális gépként telepíti, a virtuális gép az összes előfeltétel telepíthető. Ha azt szeretné, hogy tekintse át az előfeltételeket, használja a következő cikkekben talál.

- [Ismerje meg](vmware-azure-configuration-server-requirements.md) hardver-, szoftver, és a konfigurációs kiszolgáló kapacitásának követelmény.
- Ha több VMware virtuális gépeket replikál, tájékozódjon a [kapacitástervezésének szempontjai](site-recovery-plan-capacity-vmware.md), és futtassa a [Azure Site Recovery Deployment Planner](site-recovery-deployment-planner.md) eszköz a VMWare replikálásához.
- Az OVA-sablon a Windows-licenc egy próbalicencre, 180 napig érvényes. Megadott idő után érvényes licenccel rendelkező Windows aktiválni kell. 
- Az OVA sablon állíthatja be a konfigurációs kiszolgáló VMware virtuális gépként egyszerű módszert kínál. Ha valamilyen okból, be kell állítania a VMware virtuális gép a sablon nélkül, tekintse át az előfeltételeket, és manuális kövesse a [Ez a cikk](physical-azure-set-up-source.md).
- Az Azure-fiókjával engedélyre van szüksége az Azure AD-alkalmazások létrehozásához.


>[!IMPORTANT]
A konfigurációs kiszolgálót ebben a cikkben ismertetett módon kell telepíteni. Másolja, vagy egy meglévő konfigurációs kiszolgálót a Klónozás nem támogatott.

### <a name="set-up-azure-account-permissions"></a>Az Azure-fiók engedélyeinek beállítása

Egy bérlő globális rendszergazdai vagy a fiók az Azure AD-alkalmazások létrehozásához szükséges engedélyek hozzárendelése, vagy rendelje hozzá a alkalmazásfejlesztő szerepkört (az engedélyekkel rendelkező) fiók.


A bérlő globális rendszergazdája az engedélyeket a fiókhoz a következő:

1. Az Azure AD-ben a bérlő globális rendszergazdája kell lépjen **Azure Active Directory** > **felhasználók** > **felhasználói beállítások**.
2. A rendszergazdának kell beállítania **alkalmazásregisztrációk** való **Igen**.

 > [!NOTE]
 > Ez a nem bizalmas alapértelmezett beállítás. [További információk](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

Másik lehetőségként a bérlő globális rendszergazdája számára rendelje hozzá a szerepkört a fiókhoz. [További információk](https://docs.microsoft.comazure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

 


## <a name="download-the-ova-template"></a>Az OVA-sablon letöltése

1. A tárolóban kattintson **Az infrastruktúra előkészítése** > **Forrás** elemre.
2. A **Forrás előkészítése** ablakban válassza a **+Konfigurációs kiszolgáló** elemet.
3. A **Kiszolgáló hozzáadása** panelen ellenőrizze, hogy a **Kiszolgálótípus** mezőben a **Konfigurációs kiszolgáló VMware-hez** érték jelenik meg.
4. Töltse le a konfigurációs kiszolgáló az Open Virtualization alkalmazás (OVA) sablonját.

  > [!TIP]
>A konfigurációs kiszolgáló sablonját a legújabb verzióját is letöltheti a [Microsoft Download Center](https://aka.ms/asrconfigurationserver).


## <a name="import-the-ova-template-in-vmware"></a>Az OVA VMware-sablon importálása

1. Jelentkezzen be a VMware vCenter-kiszolgálóra vagy a vSphere ESXi-gazdagépre a VMware vSphere Client használatával.
2. A **File** (Fájl) menüben válassza a **Deploy OVF Template** (OVF-sablon telepítése) lehetőséget az OVF-sablon telepítése varázsló elindításához.

     ![OVF-sablon](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. A **forrás kiválasztása**, adja meg a letöltött OVF helyét.
4. A **részletek áttekintése**válassza **tovább**.
5. A **név és mappa kiválasztása** és **válassza konfigurációs**, fogadja el az alapértelmezett beállításokat.
6. A **Select storage** (Tároló kiválasztása) területen a legjobb teljesítmény érdekében válassza a **Thick Provision Eager Zeroed** formátumot a **Select virtual disk format** (Virtuális lemez formátumának kiválasztása) mezőben.
7. A varázsló többi lapján fogadja el az alapértelmezett beállításokat.
8. A **való**, az alapértelmezett beállításokkal, válassza ki a virtuális gép beállításához **üzembe helyezés után a Power** > **Befejezés**.
9. Alapértelmezés szerint a virtuális gép üzembe helyezéséhez egy egyetlen hálózati adapterrel. Ha szeretne további hálózati adapter hozzáadása, törölje a jelet **üzembe helyezés után a Power**, és kattintson a **Befejezés**. Ezután kövesse a következő eljárással.

## <a name="add-an-adapter-to-the-configuration-server"></a>Adjon hozzá egy adaptert a konfigurációs kiszolgálóhoz

Ha szeretne további hálózati Adaptereket adhat hozzá a konfigurációs kiszolgálót, adja hozzá ahhoz regisztrálja a kiszolgálót a tárolóban. A regisztrálást követően további adapterek hozzáadása nem támogatott.

1. A vSphere Client-leltárban kattintson a jobb gombbal a virtuális gépre, és válassza az **Edit Settings** (Beállítások szerkesztése) elemet.
2. A **Hardware** (Hardver) területen válassza az **Add** > **Ethernet Adapter** (Hozzáadás > Ethernet-adapter) elemet. Ezután kattintson a **Tovább** gombra.
3. Válassza ki a hálózati adapter típusát és a hálózatot.
4. Csatlakoztassa a virtuális hálózati Adaptert, amikor a virtuális gép be van kapcsolva, jelölje be **bekapcsolási: Connect**. Válassza ki **tovább** > **Befejezés** > **OK**.

## <a name="register-the-configuration-server"></a>A konfigurációs kiszolgáló regisztrálása 
Kapcsolja be a virtuális Gépet, és a konfigurációs kiszolgálót regisztrálja a Site Recovery-tárolóban.

1. A VMWare vSphere Client-konzolon kapcsolja be a virtuális gépet.
2. A virtuális gép betölti a Windows Server 2016 telepítési folyamatot. Fogadja el a licencszerződést, és adjon meg egy rendszergazdai jelszót.
3. A telepítés befejezése után jelentkezzen be rendszergazdaként a virtuális gépre.



## <a name="set-up-the-configuration-server"></a>A konfigurációs kiszolgáló beállítása

Az üzembe helyezés részeként kell a MySQL telepítése a virtuális gép konfigurációs kiszolgálón. Ezt több módon végrehajthatja:

- Töltse le és telepítse a MySQL a Site Recovery segítségével. Ha szeretné ezt a beállítást használja, nem kell semmit, a konfigurációs kiszolgáló telepítőjének indítása előtt.
- Töltse le és telepítse manuálisan a MySQL: a konfigurációs kiszolgáló beállítása előtt töltse le a MySQL, és helyezze a **C:\Temp\ASRSetup** mappát. A MySQL telepítése most. 
- Töltse le a manuális, és telepíteni a Site Recovery segítségével. A konfigurációs kiszolgáló beállítása előtt töltse le a MySQL-telepítőt, és tegye a **C:\Temp\ASRSetup** mappát.


1. Az első alkalommal jelentkezik be a virtuális gép az Azure Site Recovery Configuration Tool elindul.
2. Adja meg a konfigurációs kiszolgálót regisztrálja a Site Recovery-tárolóban használt név. Ezután kattintson a **Tovább** gombra.
3. Az eszköz ellenőrzi, hogy a virtuális gép tud-e csatlakozni az Azure-hoz. A kapcsolat létrejötte után a **Sign in** (Bejelentkezés) gombra kattintva jelentkezzen be az Azure-előfizetésbe. Vegye figyelembe, hogy a fiók, amelyben a konfigurációs kiszolgálót regisztrálja szeretne a tárolóhoz hozzáféréssel kell rendelkeznie.
4. Az eszköz végrehajt néhány konfigurációs feladatot, majd újraindul.
5. Jelentkezzen be újra a gépre. A konfigurációs kiszolgáló felügyeleti varázslója automatikusan néhány másodpercen belül elindul.
6. A varázslóban válassza a **kapcsolat beállítása**.
7. Válassza ki a hálózati Adaptert a folyamatkiszolgáló (futtató alapértelmezés szerint a konfigurációs kiszolgálón) használó virtuális gépek replikációs forgalom fogadására.
8. Ezután válassza a **Save** (Mentés) lehetőséget. Vegye figyelembe, hogy a konfigurációs kiszolgáló regisztrálása után nem módosítható tároló beállításait. 
9. A **helyreállítási tár kiválasztása**, jelentkezzen be a Microsoft Azure-ba, válassza ki az Azure-előfizetés és a megfelelő erőforráscsoportot és tárolót. 
10. A **harmadik féltől származó szoftverek telepítése**, a MySQL telepítése:
     - Ha a Site Recovery kezeli a MySQL letöltése és telepítése, kattintson a **letöltése és telepítése**. Várjon, amíg a telepítés befejeződését, majd folytassa a varázslót.
     - Ha letöltötte a MySQL és a Site Recovery telepíti azt, kattintson a **töltse le és telepítse**. Várjon, amíg a telepítés befejeződik, és folytathatja a munkát.
     - Ha manuálisan letöltött és telepített MySQL, kattintson a **töltse le és telepítse**. Az alkalmazás állapota **már telepítve van**. A varázsló folytatásához.
11. A **berendezés konfigurációjának ellenőrzése**, a telepítő ellenőrzi az előfeltételeket a folytatás előtt. 
12. A **vCenter-kiszolgáló vagy vSphere ESXi kiszolgáló konfigurálása**, írja be a teljes Tartománynevét vagy IP-címét a vCenter-kiszolgáló vagy vSphere-gazdagép, virtuális gépek mely a replikálni kívánt találhatók. Adja meg a portot, amelyet a kiszolgáló figyel, és adjon meg egy rövid nevet a VMware-kiszolgáló a tárolóban.
13. A VMware-kiszolgálóhoz csatlakozhat, és automatikusan felderíteni a replikáláshoz elérhető VMware virtuális gépeket a konfigurációs kiszolgáló által használandó hitelesítő adatok megadása. Válassza ki **hozzáadása** >  **továbbra is**. A hitelesítő adatokat a helyileg menti.
14. A **virtuális gép hitelesítő adatainak konfigurálása**, adja meg a hitelesítő adatait, amelynek használatával a Site Recovery automatikusan telepíti a mobilitási szolgáltatást, amikor engedélyezi egy virtuális gép replikációját.
    - A Windows-alapú gépek esetében a fióknak helyi rendszergazdai jogosultságokkal kell rendelkeznie a replikálni kívánt gépeken.
    - Linux-alapú gépeken a rendszergazdai fiók adatait adja meg.
15. Válassza a **Konfiguráció véglegesítése** elemet a regisztráció befejezéséhez.
16. Regisztráció befejezését követően nyissa meg az Azure Portalt, és ellenőrizze, hogy a konfigurációs kiszolgáló és a VMware-kiszolgáló megjelennek **Recovery Services-tároló** > **kezelés**  >   **Site Recovery-infrastruktúra** > **konfigurációs kiszolgálók**.


## <a name="exclude-antivirus-on-the-configuration-server"></a>A konfigurációs kiszolgálón a víruskereső kizárása

Ha víruskereső fut a virtuális gép konfigurációs kiszolgálón, győződjön meg arról, hogy a következő mappák víruskereső műveletek nem tartoznak. Ez biztosítja, hogy replikáció és a kapcsolat a várt módon működik: 

- C:\Program Files\Microsoft Azure Recovery Services-ügynök
- C:\Program Files\Microsoft Azure Site Recovery Provider
- C:\Program Files\Microsoft Azure Site Recovery Konfigurációkezelő
- C:\Program Files\Microsoft Azure Site Recovery hiba gyűjtemény eszköz
- C:\thirdparty
- C:\Temp
- C:\strawberry
- C:\ProgramData\MySQL
- C:\Program Files (x86) \MySQL
- C:\ProgramData\ASR
- C:\ProgramData\Microsoft Azure Site Recovery
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\Inetpub
- Site Recovery telepítési könyvtárában. Például: E:\Program fájlok (x86) \Microsoft Azure Site Recovery


## <a name="next-steps"></a>További lépések
- Ha nehézségek tapasztal, tekintse át a [gyakori kérdésekre](vmware-azure-common-questions.md#configuration server) és [hibaelhárítási](vmware-azure-troubleshoot-configuration-server.md) a konfigurációs kiszolgáló.
- Most [a célkörnyezet beállítása](./vmware-azure-set-up-target.md) az Azure-ban.
