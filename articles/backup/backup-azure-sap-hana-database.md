---
title: SAP HANA-adatbázis biztonsági mentése az Azure-ba Azure Backup használatával | Microsoft Docs
description: Ez az oktatóanyag azt ismerteti, hogyan lehet biztonsági másolatot készíteni egy SAP HANA-adatbázisról az Azure-ba a Azure Backup szolgáltatással.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 08/27/2019
ms.author: dacurwin
ms.openlocfilehash: 8d99ff6f2d8a21a501631a3a062be6b05130c05b
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72931801"
---
# <a name="back-up-an-sap-hana-database-to-azure"></a>SAP HANA-adatbázis biztonsági mentése az Azure-ba

A [Azure Backup](backup-overview.md) támogatja SAP HANA adatbázisok biztonsági mentését az Azure-ba.

> [!NOTE]
> Ez a szolgáltatás jelenleg nyilvános előzetes verzióban érhető el. Jelenleg nem áll készen a gyártásra, és nem rendelkezik garantált SLA-val.

## <a name="scenario-support"></a>Forgatókönyvek támogatása

**Támogatás** | **Részletek**
--- | ---
**Támogatott térségek** | Kelet-Ausztrália, Délkelet-Ausztrália <br> Dél-Brazília <br> Közép-Kanada, Kelet-Kanada <br> Dél-Kelet-Ázsia, Kelet-Ázsia <br> USA keleti régiója, USA 2. keleti régiója, USA nyugati középső régiója, USA nyugati régiója, USA 2. nyugati régiója, USA északi középső régiója, USA középső régiója<br> Közép-India, Dél-India <br> Kelet-Japán, Nyugat-Japán<br> Korea középső régiója, Korea déli régiója <br> Észak-Európa, Nyugat-Európa <br> Egyesült Királyság déli régiója, Egyesült Királyság nyugati régiója
**Támogatott VM-operációs rendszerek** | SLES 12 SP2, SP3 vagy SP4 szervizcsomaggal.
**Támogatott HANA-verziók** | SDC on HANA 1. x, MDC on HANA 2. x < = SPS04 Rev 43

### <a name="current-limitations"></a>Aktuális korlátozások

- Csak Azure-beli virtuális gépeken futó SAP HANA-adatbázisok biztonsági mentését végezheti el.
- Csak egyetlen Azure-beli virtuális gépen futó SAP HANA példány készíthető biztonsági mentésre. Ugyanabban az Azure-beli virtuális gépen jelenleg több HANA-példány nem támogatott.
- Az adatbázisok biztonsági mentését csak Felskálázási módban végezheti el. A kibővíthető, azaz A több Azure-beli virtuális gépen található HANA-példány jelenleg nem támogatott a biztonsági mentéshez.
- Nem lehet biztonsági másolatot készíteni SAP HANA példányról a dinamikus, a kiterjesztett kiszolgálón, azaz a dinamikus rétegek jelennek meg egy másik csomóponton. Ez lényegében kibővíthető, ami nem támogatott.
- Nem lehet biztonsági másolatot készíteni SAP HANA példányról, ha a dinamikus rétegek engedélyezve vannak ugyanazon a kiszolgálón. A dinamikus rétegek jelenleg nem támogatottak.
- A Azure Portalban csak SAP HANA biztonsági mentést lehet konfigurálni. A szolgáltatás nem konfigurálható a PowerShell-lel, a parancssori felülettel.
- Az adatbázis-naplók biztonsági mentését 15 percenként végezheti el. A naplók biztonsági mentései csak az adatbázis sikeres teljes biztonsági mentésének befejeződése után kezdődnek.
- Teljes és különbözeti biztonsági másolatokat készíthet. A növekményes biztonsági mentés jelenleg nem támogatott.
- A biztonsági mentési szabályzatot a SAP HANA biztonsági mentések alkalmazása után nem módosíthatja. Ha más beállításokkal szeretne biztonsági mentést készíteni, hozzon létre egy új szabályzatot, vagy rendeljen hozzá egy másik szabályzatot.
  - Új szabályzat létrehozásához a tárolóban kattintson a **házirendek** > **biztonsági mentési házirendek** >  **+**  > **SAP HANA hozzáadása az Azure virtuális gépen**lehetőségre, és adja meg a házirend-beállításokat.
  - Egy másik szabályzat hozzárendeléséhez az adatbázist futtató virtuális gép tulajdonságainál kattintson a jelenlegi házirend nevére. Ezután a **biztonsági mentési szabályzat** lapon kiválaszthat egy másik, a biztonsági mentéshez használni kívánt szabályzatot.

## <a name="prerequisites"></a>Előfeltételek

A biztonsági mentések konfigurálása előtt győződjön meg arról, hogy a következőket végzi el:

1. A SAP HANA adatbázist futtató virtuális gépen telepítse és engedélyezze az ODBC-illesztőprogram csomagjait a hivatalos SLES-csomagból/adathordozóról a Zypper használatával, az alábbiak szerint:

    ```unix
    sudo zypper update
    sudo zypper install unixODBC
    ```

    > [!NOTE]
    > Ha nem frissíti a adattárakat, győződjön meg arról, hogy a unixODBC verziója min 2.3.4. A uniXODBC verziójának megismeréséhez futtassa a ```odbcinst -j``` root-ként

2. Az [alábbi](#set-up-network-connectivity)eljárásban leírtak alapján engedélyezze a kapcsolatot a virtuális gépről az internetre, hogy az elérhető legyen az Azure-ban.

3. Futtassa az előzetes regisztrációs parancsfájlt a virtuális gépen, ahol a HANA a root felhasználóként van telepítve. A parancsfájl a folyamat [portálján](#discover-the-databases) található, és a [megfelelő engedélyek](backup-azure-sap-hana-database-troubleshoot.md#setting-up-permissions)beállításához szükséges.

### <a name="set-up-network-connectivity"></a>Hálózati kapcsolat beállítása

Az SAP HANA virtuális gépnek minden művelethez kapcsolódnia kell az Azure nyilvános IP-címeihez. A virtuális gépek műveletei (adatbázis-felderítés, biztonsági másolatok konfigurálása, biztonsági másolatok ütemezése, helyreállítási pontok visszaállítása stb.) nem működhetnek kapcsolat nélkül. Kapcsolat létesítése az Azure-adatközpont IP-tartományokhoz való hozzáférés engedélyezésével: 

- Letöltheti az Azure-adatközpontok [IP-](https://www.microsoft.com/download/details.aspx?id=41653) címtartományt, majd engedélyezheti a hozzáférést ezekhez az IP-címekhez.
- Hálózati biztonsági csoportok (NSG) használata esetén a AzureCloud [szolgáltatás címkével](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) engedélyezheti az összes Azure-beli nyilvános IP-címet. A NSG szabályok módosításához a [set-AzureNetworkSecurityRule parancsmag](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurenetworksecurityrule?view=azuresmps-4.0.0) használható.
- az 443-as portot az átvitel HTTPS-kapcsolaton keresztül kell engedélyezni.

## <a name="onboard-to-the-public-preview"></a>Bevezetés a nyilvános előzetes verzióba

A nyilvános előzetes verzióra az alábbiak szerint kell bejelentkezni:

- A portálon regisztrálja előfizetési AZONOSÍTÓját a Recovery Services szolgáltatónak a [jelen cikkben](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal)leírtak szerint. 
- A PowerShell esetében futtassa ezt a parancsmagot. A műveletnek "regisztrálva" kell lennie.

    ```powershell
    PS C:>  Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
    ```

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>Az adatbázisok felderítése

1. A tárolóban **első lépések**kattintson a **biztonsági mentés**elemre. A **hol fut a**számítási feladat? területen válassza **a SAP HANA lehetőséget az Azure-beli virtuális gépen**.
2. Kattintson a **felderítés indítása**gombra. Ez elindítja a nem védett Linux rendszerű virtuális gépek felderítését a tároló régiójában.

   - A felderítést követően a nem védett virtuális gépek a portálon jelennek meg a név és az erőforráscsoport alapján.
   - Ha a virtuális gép nem a várt módon van felsorolva, ellenőrizze, hogy már van-e biztonsági másolat egy tárolóban.
   - Több virtuális gépnek is ugyanaz a neve, de különböző erőforráscsoportok tartoznak hozzájuk.

3. A **Virtual Machines kiválasztása lapon**kattintson a hivatkozásra azon parancsfájl letöltéséhez, amely a Azure Backup szolgáltatás számára engedélyeket biztosít a SAP HANA virtuális gépek adatbázis-felderítéshez való hozzáféréséhez.
4. Futtassa a szkriptet minden olyan virtuális gépen, amely SAP HANA-adatbázisokat üzemeltet, amelyekről biztonsági másolatot szeretne készíteni.
5. Miután futtatta a szkriptet a virtuális gépeken, a **Virtual Machines kiválasztása**területen válassza ki a virtuális gépeket. Ezután kattintson az **adatbázisok felderítése**elemre.
6. Azure Backup a virtuális gépen lévő összes SAP HANA adatbázist felfedi. A felderítés során Azure Backup regisztrálja a virtuális gépet a tárolóban, és telepít egy bővítményt a virtuális gépre. Nincs ügynök telepítve az adatbázison.

    ![SAP HANA adatbázisok felderítése](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>Biztonsági mentés konfigurálása  

Most engedélyezze a biztonsági mentést.

1. A 2. lépésben kattintson a **biztonsági mentés konfigurálása**elemre.
2. Az **elemek kijelölése biztonsági mentéshez**területen jelölje ki az összes védelemmel ellátni kívánt adatbázist > **az OK gombra**.
3. A **biztonsági mentési szabályzat** > **válassza a biztonsági mentési szabályzat lehetőséget**, hozzon létre egy új biztonsági mentési szabályzatot az adatbázisokhoz az alábbi utasítások szerint.
4. A szabályzat létrehozása után a **biztonsági mentés** menüben kattintson a **biztonsági mentés engedélyezése**elemre.
5. A biztonsági mentési konfiguráció előrehaladásának nyomon követése a portál **értesítések** területén.

### <a name="create-a-backup-policy"></a>Biztonsági mentési szabályzat létrehozása

A biztonsági mentési szabályzat meghatározza a biztonsági másolatok készítésének idejét, valamint azt, hogy mennyi ideig őrzi meg a rendszer.

- A rendszer a tároló szintjén hozza létre a szabályzatot.
- Több tároló is használhatja ugyanazt a biztonsági mentési szabályzatot, de a biztonsági mentési szabályzatot minden egyes tárba alkalmaznia kell.

A házirend-beállításokat a következőképpen adhatja meg:

1. A **Házirend neve**mezőben adja meg az új szabályzat nevét.
2. A **teljes biztonsági mentési szabályzatban**válassza ki a **biztonsági mentés gyakoriságát**, és válassza a **naponta** vagy **hetente**lehetőséget.
   - **Napi**: válassza ki azt az órát és időzónát, amelyben a biztonsági mentési feladatok megkezdődnek.
   
       - Teljes biztonsági mentést kell futtatnia. Ezt a beállítást nem lehet kikapcsolni.
       - A szabályzat megtekintéséhez kattintson a **teljes biztonsági mentés** elemre.
       - A napi teljes biztonsági mentésekhez nem hozhat létre különbözeti biztonsági másolatokat.
       
   - **Hetente**: válassza ki a hét azon napját, óráját és időzónáját, amelyben a biztonsági mentési feladatot futtatja.
3. A **megőrzési tartomány**területen konfigurálja a teljes biztonsági mentés megőrzési beállításait.
    - Alapértelmezés szerint minden beállítás ki van választva. Törölje az összes olyan megőrzési időtartamra vonatkozó korlátozást, amelyet nem kíván használni, és állítsa be azokat.
    - A minimális megőrzési idő bármilyen típusú biztonsági mentés esetén (teljes/különbözeti/napló) hét nap.
    - A helyreállítási pontok megőrzési időtartam alapján vannak címkézve. Ha például napi teljes biztonsági mentést választ, a naponta csak egy teljes biztonsági mentést indít el.
    - Egy adott nap biztonsági másolata a heti megőrzési időtartam és a beállítás alapján van megcímkézve és megtartva.
    - A havi és az éves adatmegőrzési tartomány hasonló módon viselkedik.

4. A **teljes biztonsági mentési szabályzat** menüben kattintson az **OK** gombra a beállítások elfogadásához.
5. Válasszon különbözeti **biztonsági másolatot** a különbözeti szabályzat hozzáadásához.
6. A **különbözeti biztonsági mentési házirendben**válassza az **Engedélyezés** lehetőséget a gyakoriság és a megőrzési vezérlők megnyitásához.
    - A legtöbb esetben naponta egy különbözeti biztonsági mentést indíthat.
    - A különbözeti biztonsági mentések legfeljebb 180 napig tárolhatók. Ha nagyobb adatmegőrzésre van szüksége, teljes biztonsági mentést kell használnia.

    > [!NOTE]
    > A növekményes biztonsági mentések jelenleg nem támogatottak. 

7. A házirend mentéséhez kattintson az **OK** gombra, és térjen vissza a **biztonsági mentési házirend** fő menüjére.
8. A tranzakciós napló biztonsági mentési szabályzatának hozzáadásához válassza a **napló biztonsági mentése** lehetőséget,
    - A **napló biztonsági mentése**területen válassza az **Engedélyezés**lehetőséget.
    - Állítsa be a gyakoriságot és a megőrzési vezérlőket.

    > [!NOTE]
    > A biztonsági másolatok naplózása csak a sikeres teljes biztonsági mentés befejezése után kezdi a folyamatot.

9. A házirend mentéséhez kattintson az **OK** gombra, és térjen vissza a **biztonsági mentési házirend** fő menüjére.
10. Miután befejezte a biztonsági mentési szabályzat definiálását, kattintson **az OK**gombra.


## <a name="run-an-on-demand-backup"></a>Igény szerinti biztonsági mentés futtatása

A biztonsági mentések a szabályzat ütemezésével összhangban futnak. Az igény szerinti biztonsági mentést az alábbi módon futtathatja:


1. A tároló menüjében kattintson a **biztonsági másolati elemek elemre**.
2. A **biztonsági másolati elemek**területen válassza ki a SAP HANA adatbázist futtató virtuális gépet, majd kattintson a **biztonsági mentés**elemre.
3. A **biztonsági mentés most**a Calendar (naptár) vezérlőelem használatával válassza ki azt az utolsó napot, ameddig a helyreállítási pontot meg kell őrizni. Ezután kattintson az **OK** gombra.
4. A portál értesítéseinek figyelése. A feladat előrehaladását a tároló irányítópultján követheti nyomon > a **biztonsági mentési feladatok** > folyamatban van. Az adatbázis méretétől függően a kezdeti biztonsági mentés hosszabb időt is igénybe vehet.

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>SAP HANA Studio Backup futtatása Azure Backup engedélyezve lévő adatbázison

Ha egy olyan adatbázis helyi biztonsági másolatát kívánja használni, amelyről biztonsági mentés készül Azure Backupsal, tegye a következőket:

1. Várjon, amíg befejeződik az adatbázis teljes vagy naplózott biztonsági mentése. Győződjön meg arról, hogy az állapota SAP HANA Studióban.
2. Tiltsa le a naplók biztonsági mentését, és állítsa a biztonsági mentési katalógust a fájlrendszerre a megfelelő adatbázishoz.
3. Ehhez kattintson duplán a **systemdb** > **konfiguráció** >  Válassza az**adatbázis** > **Filter (napló)** elemet.
4. A **enable_auto_log_backup** beállítása **nem**értékre.
5. Állítsa a log_backup_using_backint **hamis**értékre.
6. Készítsen ad hoc teljes biztonsági mentést az adatbázisról.
7. Várjon, amíg befejeződik a teljes biztonsági mentés és a katalógus biztonsági mentése.
8. A korábbi beállítások visszaállítása az Azure-ba:
    - A **enable_auto_log_backup** beállítása **Igen**.
    - Állítsa a log_backup_using_backint **igaz**értékre.


## <a name="upgrading-protected-10-dbs-to-20"></a>Védett 1,0-adatbázisok frissítése 2,0-re

Ha SAP HANA 1,0-es adatbázisok védelmét kívánja biztosítani, és a 2,0-re kíván frissíteni, hajtsa végre az alábbi lépéseket.

- A védelem leállítása a régi SDC-adatbázis megőrzése érdekében.
- Futtassa újra az előzetes regisztrációs parancsfájlt a (SID és MDC) helyes részleteivel. 
- A bővítmény újbóli regisztrálása (Backup-> nézet részletei – > Válassza ki a megfelelő Azure-beli virtuális gépet – > újra regisztrálja). 
- Kattintson az azonos virtuális géphez tartozó adatbázisok újbóli felderítése elemre. Ennek megfelelően a 2. lépésben szereplő új adatbázisok helyes részletekkel (SYSTEMDB és bérlői ADATBÁZISsal, nem SDC) jelennek meg. 
- Az új adatbázisok védelméhez.

## <a name="next-steps"></a>Következő lépések

A SAP HANA biztonsági mentés Azure-beli virtuális gépeken való használata során [megtudhatja](backup-azure-sap-hana-database-troubleshoot.md) , hogyan lehet gyakori hibákat elhárítani.
[Ismerje meg az](backup-azure-arm-vms-prepare.md) Azure-beli virtuális gépek biztonsági mentését.
