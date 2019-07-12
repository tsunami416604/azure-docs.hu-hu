---
title: Az Azure-bA az Azure Backup egy SAP HANA-adatbázis biztonsági mentése |} A Microsoft Docs
description: Ez az oktatóanyag azt ismerteti, hogyan egy SAP HANA-adatbázis biztonsági mentése az Azure-bA az Azure Backup szolgáltatással.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: raynew
ms.openlocfilehash: 1c5c38e9af58aa9468fb0e22491327d3a35f0dda
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2019
ms.locfileid: "67656663"
---
# <a name="back-up-an-sap-hana-database"></a>Az SAP HANA-adatbázis biztonsági mentése

[Az Azure Backup](backup-overview.md) támogatja a biztonsági mentés SAP HANA-adatbázisok az Azure-bA.

> [!NOTE]
> Ez a funkció jelenleg nyilvános előzetes verzióban érhető el. Jelenleg nem éles üzemre, és nem rendelkezik a garantált szolgáltatási szerződést. 

## <a name="scenario-support"></a>A forgatókönyv támogatása

**Támogatás** | **Részletek**
--- | ---
**Támogatott helyeken** | Délkelet-Ausztrália, Kelet-Ausztrália <br> Dél-Brazília <br> Kanada közép-India, kelet-Kanada <br> Délkelet-Ázsia, Kelet-Ázsia <br> USA keleti RÉGIÓJA, USA keleti RÉGIÓJA 2, USA nyugati középső RÉGIÓJA, USA nyugati RÉGIÓJA, USA nyugati RÉGIÓJA 2, USA északi középső RÉGIÓJA, USA középső RÉGIÓJA, USA déli középső RÉGIÓJA<br> India középső régiója, Dél-India <br> Kelet-Japán, Nyugat-Japán<br> Korea középső régiója, Korea déli régiója <br> Észak-Európa, Nyugat-Európa <br> Egyesült Királyság déli régiója, Egyesült Királyság nyugati régiója
**Virtuális gépek támogatott operációs rendszerek** | SLES 12 SP2 és SP3 verziót kell használnia.
**HANA támogatott verziói** | A HANA SDC 1.x, on HANA MDC 2.x < = SPS03

### <a name="current-limitations"></a>Aktuális korlátozások

- Azure virtuális gépeken futó SAP HANA-adatbázisok csak készíteni.
- SAP HANA biztonsági mentés csak az Azure Portalon lehet beállítani. A szolgáltatás nem állítható be a PowerShell, CLI vagy a REST API-t.
- Vertikális Felskálázás módban lévő adatbázisok csak készíteni.
- Biztonsági másolatot készíthet az adatbázisnaplók 15 percenként. Naplóalapú biztonsági mentések csak a flow, az adatbázis sikeres teljes biztonsági mentés befejezése után kezdődik.
- Teljes és különbségi biztonsági másolatok is igénybe vehet. A növekményes biztonsági mentés jelenleg nem támogatott.
- A biztonsági mentési szabályzat nem módosíthatók, az SAP HANA biztonsági mentésekhez való alkalmazása után. Ha szeretne biztonsági másolatot készíteni, eltérő beállításokkal, hozzon létre egy új szabályzatot, vagy egy másik szabályzat hozzárendelése.
  - Hozzon létre egy új szabályzatot, a tárolóban kattintson **házirendek** > **biztonsági mentési házirendek** >  **+ Hozzáadás** > **az SAP HANA Az Azure virtuális gép**, és adja meg a házirend-beállításokat.
  - A jelenlegi házirend nevére kattintva szabályzatot rendel egy másik, az adatbázis futó virtuális gép tulajdonságait. Ezután a a **biztonsági mentési szabályzat** lapon kiválaszthatja, hogy egy másik szabályzatot a biztonsági mentés használandó.

## <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, hogy a biztonsági mentések konfigurálása előtt hajtsa végre a következőket:

1. A virtuális gépen futó SAP HANA-adatbázis, telepítse a hivatalos Microsoft [.NET Core Runtime 2.1](https://dotnet.microsoft.com/download/linux-package-manager/sles/runtime-current) csomagot. Vegye figyelembe:
    - Csak akkor kell a **dotnet-futtatókörnyezet – 2.1** csomagot. Nem kell **aspnetcore-futtatókörnyezet – 2.1**.
    - Ha a virtuális gép nem rendelkezik az oldal az internet eléréséhez, tükrözik, vagy adjon meg egy offline gyorsítótár dotnet-futtatókörnyezet – 2.1-es (és minden függő rpm) a Microsoft csomagból hírcsatorna megadott.
    - Csomag telepítése során előfordulhat, hogy megkérdezi, hogy egy beállítás megadásához. Ha igen, adja meg a **megoldás 2**.

        ![Csomag telepítési lehetőség](./media/backup-azure-sap-hana-database/hana-package.png)

2. A virtuális gép telepítése és engedélyezése ODBC illesztőprogram-csomagot a hivatalos SLES csomag/adathordozó használatával a zypper használatával, az alábbiak szerint:

    ```unix
    sudo zypper update
    sudo zypper install unixODBC
    ```

3. Engedélyezi a csatlakozást a virtuális gépről az internethez, hogy elérheti az Azure, az eljárásban leírt módon [alább](#set-up-network-connectivity).

4. Az előzetes regisztráció szkriptet futtathatja a virtuális gép HANA helyéül legfelső szintű felhasználóként. A parancsprogram [a portálon](#discover-the-databases) a flow-ban, és állítsa be a szükséges a [jobb gombbal az engedélyek](backup-azure-sap-hana-database-troubleshoot.md#setting-up-permissions).

### <a name="set-up-network-connectivity"></a>Állítsa be a hálózati kapcsolat

Minden műveletre a SAP HANA virtuális gép kapcsolatot kell létesítenie az Azure nyilvános IP-címeket. Virtuális gép operations (adatbázis felderítése, biztonsági mentések konfigurálása, a biztonsági mentések ütemezéséhez, helyreállítási pontok visszaállítása és így tovább) kapcsolat nélkül nem működnek. Kapcsolat létrehozása az Azure-adatközpont IP-címtartományok hozzáférést: 

- Letöltheti a [IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653) az Azure-adatközpontokhoz, és engedje, hogy ezen IP-címek elérését.
- Ha a hálózati biztonsági csoportok (NSG-k) használata esetén használhatja az AzureCloud [szolgáltatáscímke](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) , hogy minden Azure nyilvános IP-címek. Használhatja a [Set-AzureNetworkSecurityRule parancsmag](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurenetworksecurityrule?view=azuresmps-4.0.0) NSG-szabályok módosítása.

## <a name="onboard-to-the-public-preview"></a>A nyilvános előzetes előkészítése

Megoldás előkészítése a nyilvános előzetes verzióban az alábbiak szerint:

- A portálon regisztrálja meg előfizetési Azonosítóját a Recovery Services-szolgáltató által [Ez a cikk a következő](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal). 
- PowerShell esetén futtassa ezt a parancsmagot. Mint "Regisztrálva" teljesítéséhez.

    ```powershell
    PS C:>  Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
    ```



[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>Az adatbázisok felderítése

1. A tárolóban lévő **első lépések**, kattintson a **biztonsági mentés**. A **a számítási feladat futtató?** válassza **SAP HANA Azure-beli virtuális gépen**.
2. Kattintson a **felderítés indítása**. Ez elindítja a a tárolórégióban nem védett Linuxos virtuális gépek felderítése.

   - Nevére és erőforráscsoportjára nem védett virtuális gépek megjelennek-e a portálon felsorolt felderítési, után.
   - Ha egy virtuális gép nem találja a várt módon, ellenőrizze-e már biztonsági mentését egy tárolóban.
   - Több virtuális gép is rendelkezhet ugyanazzal a névvel, de különböző erőforráscsoportokban tartoznak.

3. A **válassza ki a virtuális gépek**, a hivatkozásra kattintva töltse le a parancsfájlt, amely engedélyeket biztosít az SAP HANA virtuális gépei az adatbázis eléréséhez az Azure Backup szolgáltatással
4. Futtassa a szkriptet az egyes virtuális Gépeken, amelyet szeretne biztonsági másolatot készíteni az SAP HANA-adatbázisok üzemeltetéséhez.
5. A virtuális gépeken, az a szkript futtatása után **válassza ki a virtuális gépek**, válassza ki a virtuális gépeket. Kattintson a **adatbázisok felderítése**.
6. Az Azure Backup a virtuális gép összes SAP HANA-adatbázis deríti fel. Felderítés alatt az Azure Backup a virtuális Gépet regisztrálja a tárolóban, és a virtuális gép egy kiterjesztést telepít. Nincs ügynök telepítve van az adatbázisban.

    ![SAP HANA-adatbázisok felderítése](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>Biztonsági mentés konfigurálása  

Most már engedélyezheti a biztonsági mentés.

1. 2\. lépésben kattintson **biztonsági mentés konfigurálása**.
2. A **készíteni kívánt elemek kiválasztása**, válassza ki a védeni kívánt összes adatbázis > **OK**.
3. A **biztonsági mentési szabályzat** > **biztonsági mentési házirend kiválasztása**, hozzon létre egy új biztonsági mentési szabályzatot, az adatbázisok, az alábbi utasítások szerint.
4. A szabályzat létrehozását követően a **biztonsági mentési** menüben kattintson a **biztonsági mentés engedélyezése**.
5. A biztonsági mentési konfiguráció állapotának nyomon követéséhez a **értesítések** területén a portálon.

### <a name="create-a-backup-policy"></a>A biztonsági mentési szabályzat létrehozása

Biztonsági mentési szabályzat határozza meg, amikor a biztonsági másolatokat készít, és mennyi ideig tartott használ.

- Egy szabályzat jön létre a tároló szintjén.
- Több tárolóhoz is használhat az azonos mentési házirenddel, de a biztonsági mentési házirendet kell alkalmaznia az egyes tárolókban.

Adja meg a szabályzat beállításai a következőképpen:

1. A **házirendnév**, adja meg az új házirend nevét.
2. A **teljes biztonsági mentés házirend**, jelölje be a **biztonsági mentés gyakorisága**, válassza a **napi** vagy **heti**.
   - **Napi**: Válassza ki az óra és az időzóna, amelyben a biztonsági mentési feladat kezdődik.
   
       - Egy teljes biztonsági mentést kell futtatni. Nem kapcsolhatja ki ezt a beállítást.
       - Kattintson a **teljes biztonsági mentés** a szabályzat megtekintéséhez.
       - Különbségi biztonsági mentés napi teljes biztonsági mentések esetén nem hozható létre.
       
   - **Heti**: Válassza ki a nap, hét, óra, és az időzóna, amelyben a biztonsági mentési feladat futtatása.
3. A **megőrzési**, a teljes biztonsági mentés megőrzési beállításainak konfigurálása.
    - Alapértelmezés szerint minden lehetőség ki van jelölve. Törölje a megőrzési tartomány korlátait nem kívánja használni, és állítsa be azokat, meg kell keresnie.
    - Bármilyen típusú biztonsági mentés (teljes és különbségi/log) a minimális megőrzési ideje hét nap.
    - Helyreállítási pontok megőrzésének a megőrzési tartomány alapján címkével vannak ellátva. Ha például napi teljes biztonsági mentést választja, csak egy teljes biztonsági mentés naponta aktiválódik.
    - A biztonsági mentés egy adott nap címkézett és a megőrzött és alapján a hetente megőrzési beállítás.
    - A havi és éves megőrzési időtartamok viselkedése hasonló módon.

4. Az a **teljes biztonsági mentés házirend** menüben kattintson a **OK** , fogadja el a beállításokat.
5. Válassza ki **különbségi biztonsági másolat** különbözeti házirend hozzáadása.
6. A **különbözeti biztonsági mentési szabályzat**válassza **engedélyezése** a gyakoriság és megőrzési vezérlőelemek megnyitásához.
    - Legfeljebb napi egy különbségi biztonsági mentés is indíthat.
    - Különbségi biztonsági másolatok megőrzésének legfeljebb 180 napra. Ha hosszabb adatmegőrzés megadásához, teljes biztonsági mentést kell használnia.

    > [!NOTE]
    > A növekményes biztonsági mentések jelenleg nem támogatottak. 

7. Kattintson a **OK** mentse a szabályzatot, és térjen vissza a fő **biztonsági mentési szabályzat** menü.
8. Válassza ki **napló biztonsági mentési** tranzakciós napló biztonsági mentési házirend hozzáadása
    - A **Naplóalapú biztonsági mentés**válassza **engedélyezése**.
    - Állítsa be a gyakoriság és megőrzési szabályozza.

    > [!NOTE]
    > Naplóalapú biztonsági mentések csak a folyamat sikeres teljes biztonsági mentés befejezése után kezdődik.

9. Kattintson a **OK** mentse a szabályzatot, és térjen vissza a fő **biztonsági mentési szabályzat** menü.
10. Miután elvégezte a biztonsági mentési házirend meghatározása, kattintson a **OK**.


## <a name="run-an-on-demand-backup"></a>Egy igény szerinti biztonsági mentés futtatása

Biztonsági mentések futtatása a házirend-ütemezés szerint. A biztonsági mentési igény szerinti módon futtathatja:


1. A tároló menüjében kattintson **biztonsági mentési elemek**.
2. A **biztonsági másolati elemek**, válassza ki a virtuális gép futó SAP HANA-adatbázis, és kattintson **biztonsági mentés**.
3. A **biztonsági mentés**, használja a naptárvezérlőt annak, hogy megőrződjön-e a helyreállítási pont kiválasztására. Ezután kattintson az **OK** gombra.
4. A portál feladatértesítések figyelésére. Figyelheti a feladat előrehaladását a tároló irányítópultján > **biztonsági mentési feladatok** > folyamatban van. Az adatbázis méretétől függően a kezdeti biztonsági mentés létrehozása eltarthat egy ideig.

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>Az SAP HANA Studio biztonsági mentés futtatása az adatbázisok az Azure Backup engedélyezve

Ha azt szeretné, hogy helyi (HANA Studio használatával) biztonsági másolatot készít egy adatbázis, amely másolat készül az Azure Backup, tegye a következőket:

1. Várja meg minden olyan teljes vagy a Naplók biztonsági mentését az adatbázis befejezéséhez. Az SAP HANA Studio állapotának ellenőrzéséhez.
2. Naplóalapú biztonsági mentések tiltása, és állítsa be a biztonságimásolat-katalógus a fájlrendszerre vonatkozó adatbázis.
3. Ehhez kattintson duplán a **systemdb** > **konfigurációs** > **adatbázis választása** > **szűrőt (naplózás)** .
4. Állítsa be **enable_auto_log_backup** való **nem**.
5. Állítsa be **log_backup_using_backint** való **hamis**.
6. Egy ad-hoc teljes biztonsági mentés, az adatbázis.
7. Várjon, amíg a teljes biztonsági mentés és a katalógus biztonsági mentés befejeződik.
8. Visszaállítás az előző beállítások azokra az Azure-hoz:
    - Állítsa be **enable_auto_log_backup** való **Igen**.
    - Állítsa be **log_backup_using_backint** való **igaz**.



## <a name="next-steps"></a>További lépések

[Ismerje meg](backup-azure-sap-hana-database-troubleshoot.md) közös kapcsolatos hibák elhárítása Azure virtuális gépeken futó SAP HANA biztonsági mentés használata során.
[Ismerje meg](backup-azure-arm-vms-prepare.md) Azure virtuális gépek biztonsági mentésének.
