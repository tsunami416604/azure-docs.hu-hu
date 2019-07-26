---
title: Recovery Services-tároló törlése az Azure-ban
description: Útmutató Recovery Services-tároló törléséhez.
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/11/2019
ms.author: raynew
ms.openlocfilehash: f98b9a02d12cc53ba23857b203ee3eaed9dd7cfa
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466658"
---
# <a name="delete-a-recovery-services-vault"></a>Recovery Services-tároló törlése

Ez a cikk egy [Azure Backup](backup-overview.md) Recovery Services-tároló törlését ismerteti. A függőségek eltávolítására, majd a tároló törlésére vonatkozó utasításokat tartalmaz.


## <a name="before-you-start"></a>Előkészületek

Nem törölhet olyan Recovery Services-tárolót, amely olyan függőségekkel rendelkezik, mint például a védett kiszolgálók vagy a tárolóhoz társított biztonságimásolat-felügyeleti kiszolgálók.<br/>
A biztonsági mentési adattárakat tartalmazó tároló nem törölhető (azaz akkor is, ha leállította a védelmet, de megtartotta a biztonsági mentési adatkészletet).

Ha olyan tárolót töröl, amely függőségeket tartalmaz, a következő hibaüzenet jelenik meg:

![tár törlési hibája](./media/backup-azure-delete-vault/error.png)

A tároló biztonságos törléséhez válassza ki a beállítást, amely megfelel a beállításnak, és kövesse az ajánlott lépéseket:

Forgatókönyv | A tár törléséhez szükséges függőségek eltávolításának lépései |
-- | --
A Azure Backup Agent (MARS) használatával védett helyszíni fájlok és mappák az Azure-ba történő biztonsági mentéssel | A biztonsági mentési adatok és a biztonsági másolati elemek törlése a [Mars](#for-mars-agent) -ügynökhöz című témakörben ismertetett lépések végrehajtása
MABS (Microsoft Azure Backup Server) vagy az Azure-ba (System Center Data Protection Manager) védett helyszíni gépeket használok | Hajtsa végre a biztonsági mentési adatok és a biztonsági másolati elemek törlésének lépéseit – [MABS-ügynök esetén](#for-mabs-agent)
Védett elemek vannak a felhőben (pl. laaS VM, Azure fájlmegosztás stb.)  | A biztonsági mentési adatok és a biztonsági mentési elemek törlése – a felhőben található [védett elemek esetében](#for-protected-items-in-cloud) hajtsa végre a következő lépéseket:
A helyszínen és a felhőben egyaránt védett elemek vannak | Hajtsa végre a biztonsági mentési adatok és a biztonsági másolati elemek törlésének lépéseit az alábbi sorozatban: <br> - [Védett elemek a felhőben](#for-protected-items-in-cloud)<br> - [MABS-ügynök esetén](#for-mars-agent) <br> - [MABS-ügynök esetén](#for-mabs-agent)
Nem rendelkezem helyszíni vagy Felhőbeli védett elemekkel; a tár törlési hibája azonban továbbra is fennáll. | Hajtsa végre az Recovery Services-tároló törlésének lépései [Azure Resource Manager ügyfél használatával](#delete-the-recovery-services-vault-using-azure-resource-manager-client)
Nem tudom, hogy az eredeti helyszíni kiszolgáló már (elveszett/leszerelt), és szeretném törölni az Recovery Services-tárolót | Forduljon a Microsoft ügyfélszolgálatához.

## <a name="delete-backup-data-and-backup-items"></a>Biztonsági mentési adatok és biztonsági másolati elemek törlése

Mielőtt továbblépne, olvassa el **[ezt](#before-you-start)** a szakaszt a függőségek és a tár törlési folyamatának megismeréséhez.

### <a name="for-protected-items-in-cloud"></a>Védett elemek a felhőben

A védelem leállításához és a biztonsági mentési adatok törléséhez hajtsa végre az alábbi műveleteket:

1. A portálon > Recovery Services-tárolóban > biztonsági másolati elemek válassza a felhőben található védett elemeket.

    ![Válassza ki a biztonsági mentés típusát](./media/backup-azure-delete-vault/azure-storage-selected.jpg)

2. Minden elemnél kattintson a jobb gombbal, és válassza a **biztonsági mentés leállítása**lehetőséget.

    ![Válassza ki a biztonsági mentés típusát](./media/backup-azure-delete-vault/stop-backup-item.png)

3. A **biztonsági mentés** > leállítása lehetőségnél válassza a **biztonsági másolati adatbázis törlése** **lehetőséget**.
4. Írja be az elem nevét, majd kattintson a **biztonsági mentés leállítása**elemre.
   - Ez ellenőrzi, hogy törölni kívánja-e az elemeket.
   - A **biztonsági mentés leállítása** gomb az ellenőrzése után aktiválódik.
   - Ha megtartja és nem törli az adatvesztést, nem fogja tudni törölni a tárolót.

     ![biztonsági másolati adatbázis törlése](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. Győződjön meg **arról** ![, hogy az](./media/backup-azure-delete-vault/messages.png)értesítés törli a biztonsági másolatokat. A befejezést követően a szolgáltatás a következő üzenetet jeleníti meg: Biztonsági mentés **leállítása és a biztonsági másolati*elem*** biztonsági másolatának törlése. **A művelet sikeresen befejeződött**.
6. A biztonsági másolati **elemek** menüjében kattintson a **frissítés** gombra, és ellenőrizze, hogy a biztonsági másolati elem el lett-e távolítva.

      ![biztonsági másolati adatbázis törlése](./media/backup-azure-delete-vault/empty-items-list.png)

### <a name="for-mars-agent"></a>MARS-ügynök esetén

A védelem leállításához és a biztonsági mentési adatok törléséhez hajtsa végre az alábbi sorrendben szereplő lépéseket:

- [1. lépés: Biztonsági másolati elemek törlése a MARS felügyeleti konzolról](#step-1-delete-backup-items-from-mars-management-console)
- [2. lépés: A portálról Azure Backup ügynök eltávolítása](#step-1-delete-backup-items-from-mars-management-console)


#### <a name="step-1-delete-backup-items-from-mars-management-console"></a>1\. lépés: Biztonsági másolati elemek törlése a MARS felügyeleti konzolról

Ha nem tudja végrehajtani ezt a lépést, mert a kiszolgáló nem érhető el, forduljon a Microsoft ügyfélszolgálatához.

- Indítsa el a MARS felügyeleti konzolját, lépjen a **műveletek** ablaktáblára, és válassza az **ütemezett biztonsági mentés**lehetőséget.
- Az **ütemezett biztonsági mentési varázsló módosítása vagy leállítása** lehetőségnél válassza a **Leállítás a biztonsági mentési ütemezés használatával lehetőséget, és törölje az összes tárolt biztonsági** mentést, és kattintson a **tovább**gombra.

    ![Ütemezett biztonsági mentés módosítása vagy leállítása](./media/backup-azure-delete-vault/modify-schedule-backup.png)

- Az **ütemezett biztonsági mentési** varázsló leállításához kattintson a **Befejezés**gombra.

    ![Ütemezett biztonsági mentés leállítása](./media/backup-azure-delete-vault/stop-schedule-backup.png)
- A rendszer a biztonsági PIN-kód megadását kéri. A PIN-kód létrehozásához hajtsa végre az alábbi lépéseket:
  - Jelentkezzen be az Azure portálra.
  - Tallózással keresse meg **Recovery Services** > tár**Beállítások** > **tulajdonságait**.
  - A **biztonsági PIN**-kód alatt kattintson a előállítás elemre. Másolja ezt a PIN-kódot. (Ez a PIN-kód csak öt percig érvényes)
- A felügyeleti konzolon (ügyfélalkalmazás) illessze be a PIN-kódot, és kattintson az **OK**gombra.

  ![Biztonsági PIN-kód](./media/backup-azure-delete-vault/security-pin.png)

- A **biztonsági mentési folyamat módosítása** varázslóban látható *, hogy a törölt biztonsági mentési adat 14 napig meg fog maradni. Ez idő után véglegesen törlődik a biztonsági mentési adatvesztés.*  

    ![Biztonsági mentési infrastruktúra törlése](./media/backup-azure-delete-vault/deleted-backup-data.png)

Most, hogy törölte a helyszíni biztonsági mentési elemeket, hajtsa végre a következő lépéseket a portálon.

#### <a name="step-2-from-portal-remove-azure-backup-agent"></a>2\. lépés: A portálról Azure Backup ügynök eltávolítása

A folytatás előtt győződjön meg arról, hogy az [1. lépés](#step-1-delete-backup-items-from-mars-management-console) befejeződött:

1. A tároló irányítópultjának menüjében kattintson a **biztonsági mentési infrastruktúra**elemre.
2. A **védett kiszolgálók** elemre kattintva megtekintheti az infrastruktúra-kiszolgálókat.

    ![Válassza ki a tárolót az irányítópult megnyitásához](./media/backup-azure-delete-vault/identify-protected-servers.png)

3. A **védett kiszolgálók** listában kattintson Azure Backup ügynök elemre.

    ![Válassza ki a biztonsági mentés típusát](./media/backup-azure-delete-vault/list-of-protected-server-types.png)

4. Kattintson a kiszolgálóra Azure Backup ügynökkel védett kiszolgálók listájában.

    ![az adott védett kiszolgáló kiválasztása](./media/backup-azure-delete-vault/azure-backup-agent-protected-servers.png)

5. A kijelölt kiszolgáló irányítópulton kattintson a **Törlés**elemre.

    ![a kijelölt kiszolgáló törlése](./media/backup-azure-delete-vault/selected-protected-server-click-delete.png)

6. A **Törlés** menüben írja be a kiszolgáló nevét, majd kattintson a **Törlés**gombra.

     ![biztonsági másolati adatbázis törlése](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

> [!NOTE]
> Ha az alábbi hiba jelenik meg, először hajtsa végre a [biztonsági másolati elemek törlése a felügyeleti konzolról](#step-1-delete-backup-items-from-mars-management-console)című témakörben felsorolt lépéseket.
>
>![a törlés nem sikerült](./media/backup-azure-delete-vault/deletion-failed.png)
>
>Ha nem tudja végrehajtani a biztonsági mentések törlésének lépéseit a felügyeleti konzolról, például a kiszolgáló felügyeleti konzollal való elérhetőségének hiánya miatt, forduljon a Microsoft ügyfélszolgálatához.

7. Győződjön meg **arról** ![, hogy az](./media/backup-azure-delete-vault/messages.png)értesítés törli a biztonsági másolatokat. A befejezést követően a szolgáltatás a következő üzenetet jeleníti meg: Biztonsági mentés **leállítása és a biztonsági másolati*elem*** biztonsági másolatának törlése. **A művelet sikeresen befejeződött**.
8. A biztonsági másolati **elemek** menüjében kattintson a **frissítés** gombra, és ellenőrizze, hogy a biztonsági másolati elem el lett-e távolítva.


### <a name="for-mabs-agent"></a>MABS-ügynök esetén

A védelem leállításához és a biztonsági mentési adatok törléséhez hajtsa végre az alábbi sorrendben szereplő lépéseket:

- [1. lépés: Biztonsági másolati elemek törlése a MABS felügyeleti konzolról](#step-1-delete-backup-items-from-mabs-management-console)
- [2. lépés: A portálról távolítsa el Azure Backup felügyeleti kiszolgálókat](#step-2-from-portal-remove-azure-backup-agent)

#### <a name="step-1-delete-backup-items-from-mabs-management-console"></a>1\. lépés: Biztonsági másolati elemek törlése a MABS felügyeleti konzolról

Ha nem tudja végrehajtani ezt a lépést, mert a kiszolgáló nem érhető el, forduljon a Microsoft ügyfélszolgálatához.

**1. módszer** A védelem leállításához és a biztonsági mentési adatok törléséhez hajtsa végre az alábbi lépéseket:

1.  DPM Felügyeleti konzol a navigációs sávon kattintson a **védelem** elemre.
2.  A Megjelenítés ablaktáblán válassza ki a védelmi csoport eltávolítani kívánt tagját. Kattintson a jobb gombbal a **csoporttagok védelmének kikapcsolása** lehetőség kiválasztásához.
3.  A **védelem leállítása** párbeszédpanelen válassza a **védett adatok** > törlése**tároló online** jelölőnégyzetet, majd kattintson a **védelem leállítása**elemre.

    ![Tárterület online törlése](./media/backup-azure-delete-vault/delete-storage-online.png)

A védett tag állapota mostantól az **elérhető inaktív replikára**módosul.

5. Kattintson a jobb gombbal az inaktív védelmi csoportra, és válassza az **inaktív védelem eltávolítása**lehetőséget.

    ![Inaktív védelem eltávolítása](./media/backup-azure-delete-vault/remove-inactive-protection.png)

6. Az **inaktív védelem törlése** ablakban válassza az **online tárterület törlése** lehetőséget, majd kattintson **az OK**gombra.

    ![Replikák eltávolítása a lemezen és az interneten](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

**2. módszer** Indítsa el a **MABS felügyeleti** konzolt. Az **adatvédelmi módszer kiválasztása** szakaszban szüntesse **meg a kívánt online védelmet**.

  ![adatvédelmi módszer kiválasztása](./media/backup-azure-delete-vault/data-protection-method.png)

Most, hogy törölte a helyszíni biztonsági mentési elemeket, hajtsa végre a következő lépéseket a portálon.

#### <a name="step-2-from-portal-remove-azure-backup-management-servers"></a>2\. lépés: A portálról távolítsa el Azure Backup felügyeleti kiszolgálókat

A folytatás előtt győződjön meg arról, hogy az [1. lépés](#step-1-delete-backup-items-from-mabs-management-console) befejeződött:

1. A tároló irányítópultjának menüjében kattintson a **biztonsági mentési infrastruktúra**elemre.
2. A kiszolgálók megtekintéséhez kattintson a **biztonságimásolat-felügyeleti kiszolgálók** elemre.

    ![Válassza ki a tárolót az irányítópult megnyitásához](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. Kattintson a jobb gombbal az elemre > **Törlés**lehetőségre.
4. A **Törlés** menüben írja be a kiszolgáló nevét, majd kattintson a **Törlés**gombra.

     ![biztonsági másolati adatbázis törlése](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

> [!NOTE]
> Ha az alábbi hiba jelenik meg, először hajtsa végre a [biztonsági másolati elemek törlése a felügyeleti konzolról](#step-2-from-portal-remove-azure-backup-management-servers)című témakörben felsorolt lépéseket.
>
>![a törlés nem sikerült](./media/backup-azure-delete-vault/deletion-failed.png)
>
> Ha nem tudja végrehajtani a biztonsági mentések törlésének lépéseit a felügyeleti konzolról, például a kiszolgáló felügyeleti konzollal való elérhetőségének hiánya miatt, forduljon a Microsoft ügyfélszolgálatához.

5. Győződjön meg **arról** ![, hogy az](./media/backup-azure-delete-vault/messages.png)értesítés törli a biztonsági másolatokat. A befejezést követően a szolgáltatás a következő üzenetet jeleníti meg: Biztonsági mentés **leállítása és a biztonsági másolati*elem*** biztonsági másolatának törlése. **A művelet sikeresen befejeződött**.
6. A biztonsági másolati **elemek** menüjében kattintson a **frissítés** gombra, és ellenőrizze, hogy a biztonsági másolati elem el lett-e távolítva.


## <a name="delete-the-recovery-services-vault"></a>A helyreállítási tár törlése

1. Ha az összes függőség el lett távolítva, görgessen a tároló menü **Essentials (alapvető** erőforrások) paneljére.
2. Ellenőrizze, hogy nincsenek-e **biztonsági másolati elemek**, **biztonságimásolat-kezelési kiszolgálók**vagy **replikált elemek** felsorolva. Ha az elemek továbbra is megjelennek a tárolóban, [távolítsa el őket](#delete-backup-data-and-backup-items).

3. Ha nincs több elem a tárolóban, a tároló Irányítópultján kattintson a **Törlés**elemre.

    ![biztonsági másolati adatbázis törlése](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. Annak ellenőrzéséhez, hogy törölni kívánja-e a tárolót, kattintson az **Igen**gombra. A tár törlődik, és a portál visszatér az **új** szolgáltatás menüjébe.

## <a name="delete-the-recovery-services-vault-using-azure-resource-manager-client"></a>A Recovery Services-tároló törlése Azure Resource Manager ügyfél használatával

A Recovery Services-tároló törlésének lehetősége csak akkor ajánlott, ha az összes függőség el lett távolítva, és a tár törlési hibája továbbra is fennáll.



- A tár menü **Essentials** paneljén ellenőrizze, hogy nincsenek-e **biztonsági másolati elemek**, **biztonságimásolat-kezelési kiszolgálók**vagy **replikált elemek** felsorolva. Ha vannak biztonsági másolati elemek, hajtsa végre a [biztonsági mentési adatok és a biztonsági másolati elemek törlése](#delete-backup-data-and-backup-items)című témakör lépéseit.
- Próbálja meg ismét [törölni a](#delete-the-recovery-services-vault)tárolót a portálról.
- Ha az összes függőség el lett távolítva, és továbbra  is a tár törlési hibáját kapja, akkor a ARMClient eszközzel hajtsa végre az alábbi lépéseket.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Telepítse a csokoládét innen [, és a](https://chocolatey.org/) ARMClient telepítéséhez futtassa az alábbi parancsot:

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Jelentkezzen be az Azure-fiókjába, és futtassa a következő parancsot:

    `ARMClient.exe login [environment name]`

3. A Azure Portal Gyűjtse össze a törölni kívánt tároló előfizetés-AZONOSÍTÓját és erőforráscsoport-nevét.

A ARMClient paranccsal kapcsolatos további információkért tekintse meg ezt a [dokumentumot](https://github.com/projectkudu/ARMClient/blob/master/README.md).

### <a name="use-azure-resource-manager-client-to-delete-recovery-services-vault"></a>Azure Resource Manager-ügyfél használata Recovery Services-tároló törléséhez

1. Futtassa az alábbi parancsot az előfizetés-azonosító, az erőforráscsoport neve és a tár neve használatával. A parancs futtatásakor törli a tárolót, ha nem rendelkezik függőségekkel.

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
2. Ha a tár nem üres, a "tár nem törölhető, mert a tárolóban már vannak erőforrások." hibaüzenet jelenik meg. Védett elemek/tárolók egy tárolón belüli eltávolításához tegye a következőket:

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. A Azure Portal ellenőrizze, hogy a tároló törölve lett-e.


## <a name="next-steps"></a>További lépések

[További](backup-azure-recovery-services-vault-overview.md) információ Recovery Services-tárolók.<br/>
[Tudnivalók a](backup-azure-manage-windows-server.md) Recovery Services-tárolók figyeléséről és kezeléséről.
