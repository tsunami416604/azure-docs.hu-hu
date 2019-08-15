---
title: Recovery Services-tároló törlése az Azure-ban
description: Útmutató Recovery Services-tároló törléséhez.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: dacurwin
ms.openlocfilehash: 9c63170b60a871182042acab8a35e505c603f260
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2019
ms.locfileid: "69018890"
---
# <a name="delete-a-recovery-services-vault"></a>Recovery Services-tároló törlése

Ez a cikk egy [Azure Backup](backup-overview.md) Recovery Services-tároló törlését ismerteti. A függőségek eltávolítására, majd a tároló törlésére vonatkozó utasításokat tartalmaz.


## <a name="before-you-start"></a>Előkészületek

Nem törölhet olyan Recovery Services-tárolót, amely olyan függőségekkel rendelkezik, mint például a védett kiszolgálók vagy a tárolóhoz társított biztonságimásolat-felügyeleti kiszolgálók.

- A biztonsági mentési adattárakat tartalmazó tároló nem törölhető (azaz akkor is, ha leállította a védelmet, de megtartotta a biztonsági mentési adatkészletet).

- Ha olyan tárolót töröl, amely függőségeket tartalmaz, a következő hibaüzenet jelenik meg:

  ![Tár törlési hibája](./media/backup-azure-delete-vault/error.png)

- Ha töröl egy helyszíni védett elemet (MARS, MABS vagy DPM az Azure-ba) a függőségeket tartalmazó portálról, figyelmeztető üzenet jelenik meg:

  ![Védett kiszolgálóhiba törlésének hibája](./media/backup-azure-delete-vault/error-message.jpg)

  
A tároló biztonságos törléséhez válassza ki a beállítást, amely megfelel a beállításnak, és kövesse az ajánlott lépéseket:

Forgatókönyv | A tár törléséhez szükséges függőségek eltávolításának lépései |
-- | --
A Azure Backup Agent (MARS) használatával védett helyszíni fájlok és mappák az Azure-ba történő biztonsági mentéssel | A biztonsági mentési adatok és a biztonsági másolati elemek törlése a [Mars](#delete-backup-items-from-mars-management-console) -ügynökhöz című témakörben ismertetett lépések végrehajtása
MABS (Microsoft Azure Backup Server) vagy az Azure-ba (System Center Data Protection Manager) védett helyszíni gépeket használok | Hajtsa végre a biztonsági mentési adatok és a biztonsági másolati elemek törlésének lépéseit – [MABS-ügynök esetén](#delete-backup-items-from-mabs-management-console)
Védett elemek vannak a felhőben (pl. laaS VM, Azure fájlmegosztás stb.)  | A biztonsági mentési adatok és a biztonsági mentési elemek törlése – a felhőben található [védett elemek esetében](#delete-protected-items-in-cloud) hajtsa végre a következő lépéseket:
A helyszínen és a felhőben egyaránt védett elemek vannak | Hajtsa végre a biztonsági mentési adatok és a biztonsági másolati elemek törlésének lépéseit az alábbi sorozatban: <br> - [Védett elemek a felhőben](#delete-protected-items-in-cloud)<br> - [MARS-ügynök esetén](#delete-backup-items-from-mars-management-console) <br> - [MABS-ügynök esetén](#delete-backup-items-from-mabs-management-console)
Nem rendelkezem helyszíni vagy Felhőbeli védett elemekkel; a tár törlési hibája azonban továbbra is fennáll. | Hajtsa végre az Recovery Services-tároló törlésének lépései [Azure Resource Manager ügyfél használatával](#delete-the-recovery-services-vault-using-azure-resource-manager-client)


## <a name="delete-protected-items-in-cloud"></a>Védett elemek törlése a felhőben

Mielőtt továbblépne, olvassa el **[ezt](#before-you-start)** a szakaszt a függőségek és a tár törlési folyamatának megismeréséhez.

A védelem leállításához és a biztonsági mentési adatok törléséhez hajtsa végre az alábbi műveleteket:

1. A portálon > **Recovery Services** > tároló**biztonsági másolati elemei**közül válassza a felhőben található védett elemeket (például AzureVirtual Machine, Azure Storage (Azure Files), SQL az Azure virtuális gépen stb.).

    ![Válassza ki a biztonsági mentés típusát](./media/backup-azure-delete-vault/azure-storage-selected.png)

2. Kattintson a jobb gombbal a biztonsági másolat elemre. Attól függően, hogy a biztonsági másolati elem védett-e, vagy sem, a menü megjeleníti a biztonsági mentés **leállítása** vagy a **biztonsági mentési információk törlése**beállítást.

    - A **biztonsági mentés leállítása**beállításnál válassza a **biztonsági másolat adatainak törlése** lehetőséget a legördülő menüből. Adja meg a biztonsági másolati elem **nevét** (kis-és nagybetűk megkülönböztetése), válasszon ki egy **okot**, írjon be **megjegyzéseket**, majd kattintson a **biztonsági mentés leállítása**elemre.

        ![Válassza ki a biztonsági mentés típusát](./media/backup-azure-delete-vault/stop-backup-item.png)

    - A **biztonsági mentési adatok törléséhez**adja meg a biztonsági másolati elem nevét (kis-és nagybetűk megkülönböztetése), válasszon ki egy **okot**, írja be a **megjegyzéseket**, majd kattintson a **Törlés**gombra. 

         ![biztonsági másolati adatbázis törlése](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. Győződjön meg **arról** ![, hogy az](./media/backup-azure-delete-vault/messages.png)értesítés törli a biztonsági másolatokat. A befejezést követően a szolgáltatás a következő üzenetet jeleníti meg: Biztonsági mentés **leállítása és a biztonsági másolati*elem*** biztonsági másolatának törlése. **A művelet sikeresen befejeződött**.
6. A biztonsági másolati **elemek** menüjében kattintson a **frissítés** gombra, és ellenőrizze, hogy a biztonsági másolati elem el lett-e távolítva.

      ![biztonsági másolati adatbázis törlése](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="delete-protected-items-on-premises"></a>Védett elemek törlése a helyszínen

Mielőtt továbblépne, olvassa el **[ezt](#before-you-start)** a szakaszt a függőségek és a tár törlési folyamatának megismeréséhez.

1. A tároló irányítópultjának menüjében kattintson a **biztonsági mentési infrastruktúra**elemre.
2. A helyszíni forgatókönyvtől függően válassza az alábbi lehetőséget:

      - **Azure Backup ügynök**esetében válassza a **védett kiszolgálók** > **Azure Backup ügynök** lehetőséget, és válassza ki a törölni kívánt kiszolgálót. 

        ![Válassza ki a tárolót az irányítópult megnyitásához](./media/backup-azure-delete-vault/identify-protected-servers.png)

      - **Azure Backup Server**/**DPM**válassza a **biztonsági mentési felügyeleti kiszolgálók**lehetőséget. Válassza ki a törölni kívánt kiszolgálót. 


          ![Válassza ki a tárolót az irányítópult megnyitásához](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. A **Törlés** panel a figyelmeztető üzenettel jelenik meg.

     ![biztonsági másolati adatbázis törlése](./media/backup-azure-delete-vault/delete-protected-server.png)

     Tekintse át a figyelmeztető üzenetet és a megjelenő utasításokat.
    
    > [!NOTE]
    >- Ha a védett kiszolgáló szinkronizálva van az Azure szolgáltatással, és a biztonsági másolati elemek léteznek, akkor a beleegyező jelölőnégyzet megjeleníti a függő biztonsági mentési elemek számát, valamint a biztonsági mentési elemek megtekintésére szolgáló hivatkozást.
    >- Ha a védett kiszolgáló nincs szinkronban az Azure-szolgáltatással és a biztonsági másolati elemekkel, akkor a beleegyező jelölőnégyzet a biztonsági mentési elemek számát jeleníti meg.
    >- Ha a biztonsági másolati elemek nem léteznek, a beleegyező jelölőnégyzet a törlést kéri.

4. Jelölje be a beleegyezett jelölőnégyzetet, majd kattintson a **Törlés**gombra.




5. Győződjön meg **arról** ![, hogy az](./media/backup-azure-delete-vault/messages.png)értesítés törli a biztonsági másolatokat. A befejezést követően a szolgáltatás a következő üzenetet jeleníti meg: Biztonsági mentés **leállítása és a biztonsági másolati*elem*** biztonsági másolatának törlése. **A művelet sikeresen befejeződött**.
6. A biztonsági másolati **elemek** menüjében kattintson a **frissítés** gombra, és ellenőrizze, hogy a biztonsági másolati elem el lett-e távolítva.

Most már folytathatja a biztonsági mentési elemek törlését a felügyeleti konzolról:
    
   - [A MARS használatával védett elemek](#delete-backup-items-from-mars-management-console)
    - [MABS használatával védett elemek](#delete-backup-items-from-mabs-management-console)


### <a name="delete-backup-items-from-mars-management-console"></a>Biztonsági másolati elemek törlése a MARS felügyeleti konzolról

Biztonsági másolati elemek törlése a MARS felügyeleti konzolról

- Indítsa el a MARS felügyeleti konzolját, lépjen a **műveletek** ablaktáblára, és válassza az **ütemezett biztonsági mentés**lehetőséget.
- Az **ütemezett biztonsági mentési varázsló módosítása vagy leállítása** lehetőségnél válassza a **Leállítás a biztonsági mentési ütemezés használatával lehetőséget, és törölje az összes tárolt biztonsági** mentést, és kattintson a **tovább**gombra.

    ![Ütemezett biztonsági mentés módosítása vagy leállítása](./media/backup-azure-delete-vault/modify-schedule-backup.png)

- Az **ütemezett biztonsági mentési** varázsló leállításához kattintson a **Befejezés**gombra.

    ![Ütemezett biztonsági mentés leállítása](./media/backup-azure-delete-vault/stop-schedule-backup.png)
- A rendszer a biztonsági PIN-kód megadását kéri. A PIN-kód létrehozásához hajtsa végre az alábbi lépéseket:
  - Jelentkezzen be az Azure portálra.
  - Tallózással keresse meg **Recovery Services** > tár**Beállítások** > **tulajdonságait**.
  - A **biztonsági PIN**-kódalatt kattintson a előállítás elemre. Másolja ezt a PIN-kódot. (Ez a PIN-kód csak öt percig érvényes.)
- A felügyeleti konzolon (ügyfélalkalmazás) illessze be a PIN-kódot, és kattintson az **OK**gombra.

  ![Biztonsági PIN-kód](./media/backup-azure-delete-vault/security-pin.png)

- A **biztonsági mentési folyamat módosítása** varázslóban a *törölt biztonsági mentési adat 14 napig meg fog maradni. Ez idő után véglegesen törlődik a biztonsági mentési adatvesztés.*  

    ![Biztonsági mentési infrastruktúra törlése](./media/backup-azure-delete-vault/deleted-backup-data.png)

Most, hogy törölte a helyszíni biztonsági mentési elemeket, hajtsa végre a következő lépéseket a portálon.

### <a name="delete-backup-items-from-mabs-management-console"></a>Biztonsági másolati elemek törlése a MABS felügyeleti konzolról

Biztonsági másolati elemek törlése a MABS felügyeleti konzolról

**1. módszer** A védelem leállításához és a biztonsági mentési adatok törléséhez hajtsa végre az alábbi lépéseket:

1.  DPM Felügyeleti konzol a navigációs sávon kattintson a **védelem** elemre.
2.  A Megjelenítés ablaktáblán válassza ki a védelmi csoport eltávolítani kívánt tagját. Kattintson a jobb gombbal a **csoporttagok védelmének kikapcsolása** lehetőség kiválasztásához.
3.  A **védelem leállítása** párbeszédpanelen jelölje be a **védett adatok** > törlése**tároló online állapotban** jelölőnégyzetet, majd kattintson a **védelem kikapcsolása**elemre.

    ![Tárterület online törlése](./media/backup-azure-delete-vault/delete-storage-online.png)

A védett tag állapota mostantól az **elérhető inaktív replikára**módosul.

4. Kattintson a jobb gombbal az inaktív védelmi csoportra, és válassza az **inaktív védelem eltávolítása**lehetőséget.

    ![Inaktív védelem eltávolítása](./media/backup-azure-delete-vault/remove-inactive-protection.png)

5. Az **inaktív védelem törlése** ablakban válassza az **online tárterület törlése** lehetőséget, majd kattintson **az OK**gombra.

    ![Replikák eltávolítása a lemezen és az interneten](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

**2. módszer** Indítsa el a **MABS felügyeleti** konzolt. Az **adatvédelmi módszer kiválasztása** szakaszban törölje a jelölést az **online védelem lehetőség**kiválasztásához.

  ![adatvédelmi módszer kiválasztása](./media/backup-azure-delete-vault/data-protection-method.png)

Most, hogy törölte a helyszíni biztonsági mentési elemeket, hajtsa végre a következő lépéseket a portálon.


## <a name="delete-the-recovery-services-vault"></a>A helyreállítási tár törlése

1. Ha az összes függőség el lett távolítva, görgessen a tároló menü **Essentials (alapvető** erőforrások) paneljére.
2. Ellenőrizze, hogy nincsenek-e **biztonsági másolati elemek**, **biztonságimásolat-kezelési kiszolgálók**vagy **replikált elemek** felsorolva. Ha az elemek továbbra is megjelennek a tárolóban, olvassa [el](#before-you-start) az előkészületek című szakaszt.

3. Ha nincs több elem a tárolóban, a tároló Irányítópultján kattintson a **Törlés**elemre.

    ![biztonsági másolati adatbázis törlése](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. Annak ellenőrzéséhez, hogy törölni kívánja-e a tárolót, kattintson az **Igen**gombra. A tár törlődik, és a portál visszatér az **új** szolgáltatás menüjébe.

## <a name="delete-the-recovery-services-vault-using-azure-resource-manager-client"></a>A Recovery Services-tároló törlése Azure Resource Manager ügyfél használatával

A Recovery Services-tároló törlésének lehetősége csak akkor ajánlott, ha az összes függőség el lett távolítva, és atár törlési hibája továbbra is fennáll.

- A tár menü **Essentials** paneljén ellenőrizze, hogy nincsenek-e **biztonsági másolati elemek**, **biztonságimásolat-kezelési kiszolgálók**vagy **replikált elemek** felsorolva. Ha vannak biztonsági másolati elemek, tekintse át az előkészületek című szakaszt. [](#before-you-start)
- Próbálja meg ismét [törölni a](#delete-the-recovery-services-vault)tárolót a portálról.
- Ha az összes függőség el lett távolítva, és továbbra is a tár *törlési hibát észlel*, akkor a ARMClient eszköz használatával hajtsa végre az alábbi lépéseket.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Telepítse a csokoládét [](https://chocolatey.org/) innen, és a ARMClient telepítéséhez futtassa az alábbi parancsot:

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Jelentkezzen be az Azure-fiókjába, és futtassa a következő parancsot:

    `ARMClient.exe login [environment name]`

3. A Azure Portal Gyűjtse össze a törölni kívánt tároló előfizetés-AZONOSÍTÓját és erőforráscsoport-nevét.

A ARMClient paranccsal kapcsolatos további információkért tekintse meg ezt a [dokumentumot](https://github.com/projectkudu/ARMClient/blob/master/README.md).

### <a name="use-azure-resource-manager-client-to-delete-recovery-services-vault"></a>Azure Resource Manager-ügyfél használata Recovery Services-tároló törléséhez

1. Futtassa az alábbi parancsot az előfizetés-azonosító, az erőforráscsoport neve és a tár neve használatával. A parancs futtatásakor törli a tárolót, ha nem rendelkezik függőségekkel.

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
2. Ha a tár nem üres, a "tár nem törölhető, mert a tárolóban már vannak erőforrások." hibaüzenet jelenik meg. Védett elemek/tárolók egy tárolón belüli eltávolításához tegye a következőket:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. A Azure Portal ellenőrizze, hogy a tároló törölve lett-e.


## <a name="next-steps"></a>További lépések

[További](backup-azure-recovery-services-vault-overview.md) információ Recovery Services-tárolók.<br/>
[Tudnivalók a](backup-azure-manage-windows-server.md) Recovery Services-tárolók figyeléséről és kezeléséről.
