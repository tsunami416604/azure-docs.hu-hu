---
title: Oktatóanyag – SQL Server adatbázisok biztonsági mentése az Azure-ba
description: Ez az oktatóanyag a SQL Server Azure-ba történő biztonsági mentését ismerteti.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 06/18/2019
ms.author: dacurwin
ms.openlocfilehash: 729eb0d77cee85356e359dc475f4e439b8236ebb
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2019
ms.locfileid: "68736554"
---
# <a name="back-up-a-sql-server-database-in-an-azure-vm"></a>SQL Server-adatbázis biztonsági mentése Azure-beli virtuális gépen



Ebből az oktatóanyagból megtudhatja, hogyan készíthet biztonsági mentést egy Azure-beli virtuális gépen futó SQL Server-adatbázisról egy Azure Backup Recovery Services-tárolóra. Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Tároló létrehozása és konfigurálása.
> * Adatbázisok felderítése és biztonsági mentések beállítása.
> * Automatikus védelem beállítása adatbázisok számára.
> * Futtasson egy ad-hoc biztonsági mentést.


## <a name="prerequisites"></a>Előfeltételek

A SQL Server-adatbázis biztonsági mentése előtt tekintse meg a következő feltételeket:

1. Azonosítson vagy [hozzon létre](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) egy Recovery Services tárolót ugyanabban a régióban vagy területi beállításban, mint a SQL Server példányt futtató virtuális gép.
2. Tekintse meg az SQL-adatbázisok biztonsági mentéséhez szükséges virtuális gépekre [vonatkozó engedélyeket](backup-azure-sql-database.md#set-vm-permissions) .
3. Ellenőrizze, hogy a virtuális gép rendelkezik-e [hálózati kapcsolattal](backup-sql-server-database-azure-vms.md#establish-network-connectivity).
4. Győződjön meg arról, hogy a SQL Server-adatbázisok neve a Azure Backup [elnevezési irányelveinek](#verify-database-naming-guidelines-for-azure-backup) megfelelően történik.
5. Ellenőrizze, hogy nincs-e engedélyezve más biztonsági mentési megoldás az adatbázishoz. A forgatókönyv beállítása előtt tiltsa le az összes többi SQL Server biztonsági mentést. Az Azure-beli virtuális gépekhez Azure Backup is engedélyezheti a virtuális gépen futó SQL Server-adatbázishoz a Azure Backup ütközés nélkül.


### <a name="establish-network-connectivity"></a>Hálózati kapcsolat létesítése

Az SQL Server VM virtuális gépnek minden művelethez kapcsolódnia kell az Azure nyilvános IP-címeihez. A virtuális gépek műveletei (adatbázis-felderítés, biztonsági másolatok konfigurálása, biztonsági másolatok ütemezése, helyreállítási pontok visszaállítása stb.) meghiúsulnak a nyilvános IP-címekkel való kapcsolat nélkül. Kapcsolat létesítése a következő lehetőségek egyikével:

- **Az Azure-adatközpont IP-tartományának engedélyezése**: Engedélyezi az [IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653) letöltését. A hálózati biztonsági csoport (NSG) eléréséhez használja a **set-AzureNetworkSecurityRule** parancsmagot.
- **Http-proxykiszolgáló üzembe helyezése a forgalom irányításához**: Ha egy Azure-beli virtuális gépen SQL Server adatbázisról készít biztonsági másolatot, a virtuális gépen futó biztonsági mentési bővítmény a HTTPS API-k használatával küldi el a felügyeleti parancsokat a Azure Backup és az Azure Storage-ba. A biztonsági mentési bővítmény a Azure Active Directory (Azure AD) szolgáltatást is használja a hitelesítéshez. Irányítsa a biztonsági mentési bővítmény forgalmát a három szolgáltatáshoz a HTTP-proxyn keresztül. A bővítmények az egyetlen olyan összetevő, amely a nyilvános internethez való hozzáférésre van konfigurálva.

Minden lehetőségnek vannak előnyei és hátrányai

**Beállítás** | **Előnyei** | **Disadvantages**
--- | --- | ---
IP-címtartományok engedélyezése | Nincs további költség. | Összetett a kezeléshez, mert az IP-címtartományok változnak az idő múlásával. <br/><br/> Hozzáférést biztosít az egész Azure-hoz, nem csak az Azure Storage-hoz.
HTTP-proxy használata   | A proxy részletes vezérlője a tárolási URL-címeken keresztül engedélyezett. <br/><br/> A virtuális gépekhez való internetes hozzáférés egyetlen pontja. <br/><br/> Nem vonatkozik az Azure IP-címek változásai. | További költségek a virtuális gép futtatásához a proxy szoftverrel.

### <a name="set-vm-permissions"></a>VM-engedélyek beállítása

Azure Backup számos dolgot tesz elérhetővé egy SQL Server-adatbázis biztonsági mentésének konfigurálásakor:

- Hozzáadja a **AzureBackupWindowsWorkload** bővítményt.
- A virtuális gépen lévő adatbázisok felderítéséhez Azure Backup létrehozza a fiók **NT SERVICE\AzureWLBackupPluginSvc**. Ez a fiók használható a biztonsági mentéshez és a visszaállításhoz, és SQL sysadmin engedélyekre van szükség.
- Azure Backup kihasználja az adatbázis-felderítés/-lekérdezés **NT AUTHORITY\SYSTEM** -fiókját, így ennek a fióknak nyilvános bejelentkezési értéknek kell lennie az SQL-ben.

Ha nem hozta létre a SQL Server VM az Azure piactéren, hibaüzenetet kaphat a **UserErrorSQLNoSysadminMembership**. Ha ez történik, [kövesse ezeket az utasításokat](backup-azure-sql-database.md#set-vm-permissions).

### <a name="verify-database-naming-guidelines-for-azure-backup"></a>A Azure Backup adatbázis-elnevezési irányelveinek ellenőrzése

Kerülje az alábbi adatbázisok nevét:

  * Záró/kezdő szóközök
  * Záró "!"
  * Szögletes zárójel "]"
  * A "F:\" kezdetű adatbázisok nevei

Az Azure Table nem támogatott karakterekhez aliast adunk, de javasoljuk, hogy ne legyenek elkerülve. [További információk](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN).


[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>SQL Server adatbázisok felderítése

A virtuális gépen futó adatbázisok felderítése.

1. A [Azure Portal](https://portal.azure.com)nyissa meg az adatbázis biztonsági mentésére használt Recovery Services-tárolót.

2. A **Recovery Services** -tároló irányítópultján válassza a **biztonsági mentés**lehetőséget.

   ![Válassza a biztonsági mentés lehetőséget a biztonsági mentés célja menü megnyitásához](./media/backup-azure-sql-database/open-backup-menu.png)

3. A **biztonsági mentés célja**beállításnál állítsa be, hogy **hol fut** az **Azure** -beli számítási feladat (ez az alapértelmezett beállítás).

4. A **Miről szeretne biztonsági másolatot készíteni**, válassza **a SQL Server lehetőséget az Azure-beli virtuális gépen**.

    ![SQL Server kiválasztása az Azure-beli virtuális gépen a biztonsági mentéshez](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. A **biztonsági mentési cél** > a**virtuális gépek**területen lévő adatbázisok felderítése területen válassza a **felderítés indítása** lehetőséget, ha nem védett virtuális gépeket szeretne keresni az előfizetésben. Az előfizetéshez tartozó nem védett virtuális gépek számától függően eltarthat egy ideig.

   - A nem védett virtuális gépeknek a felderítés után meg kell jelennie a listában a név és az erőforráscsoport alapján.
   - Ha a virtuális gép nem a várt módon szerepel a listáján, ellenőrizze, hogy már van-e biztonsági másolata egy tárolóban.
   - Több virtuális gépnek is ugyanaz a neve, de különböző erőforráscsoportok tartoznak hozzájuk.

     ![A biztonsági mentés függőben van a virtuális gépeken lévő adatbázisok keresése közben](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. A virtuális gép listában válassza ki a SQL Server adatbázist futtató virtuális gépet > a **felderítési adatbázisok**.

7. Az **értesítések** területén nyomon követheti az adatbázis-felderítést. Eltarthat egy ideig, amíg a feladatok befejeződik, attól függően, hogy hány adatbázis van a virtuális gépen. A kiválasztott adatbázisok észlelésekor megjelenik egy sikert jelző üzenet.

    ![Sikeres üzembe helyezési üzenet](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Azure Backup a virtuális gépen lévő összes SQL Server adatbázist felfedi. A felderítés során a háttérben a következő történik:

    - Azure Backup regisztrálja a virtuális gépet a tárolóban a munkaterhelés biztonsági mentéséhez. A regisztrált virtuális gépen lévő összes adatbázisról csak erre a tárolóra lehet biztonsági másolatot készíteni.
    - Azure Backup telepíti a **AzureBackupWindowsWorkload** -bővítményt a virtuális gépen. Nincs ügynök telepítve az SQL-adatbázison.
    - A Azure Backup létrehozza a szolgáltatási fiók **NT-Service\AzureWLBackupPluginSvc** a virtuális gépen.
      - Az összes biztonsági mentési és visszaállítási művelet a szolgáltatásfiókot használja.
      - Az **NT SERVICE\AZUREWLBACKUPPLUGINSVC** SQL sysadmin engedélyekkel kell rendelkeznie. Az Azure piactéren létrehozott összes SQL Server virtuális gép a **SqlIaaSExtension** van telepítve. A **AzureBackupWindowsWorkload** bővítmény a **SQLIaaSExtension** használatával automatikusan beolvassa a szükséges engedélyeket.
    - Ha nem hozta létre a virtuális gépet a piactéren, akkor a virtuális gépnek nincs telepítve a **SqlIaaSExtension** , és a felderítési művelet meghiúsul, és a **UserErrorSQLNoSysAdminMembership**hibaüzenet jelenik meg. A probléma megoldásához kövesse az [utasításokat](backup-azure-sql-database.md#set-vm-permissions) .

        ![Válassza ki a virtuális gépet és az adatbázist](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Biztonsági mentés konfigurálása  

A biztonsági mentést a következőképpen állíthatja be:

1. A **biztonsági mentés célja** lapon válassza a **biztonsági mentés konfigurálása**lehetőséget.

   ![Válassza a biztonsági mentés konfigurálása lehetőséget.](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. Kattintson a **biztonsági mentés konfigurálása**elemre, majd megjelenik az **elemek kiválasztása a biztonsági mentéshez** panel. Ez felsorolja az összes regisztrált rendelkezésre állási csoportot és az önálló SQL-kiszolgálókat. A sor bal oldalán lévő Chevron kibontásával megjelenítheti az adott példányon vagy always on AG-ban található nem védett adatbázisokat.  

    ![Az összes SQL Server-példány megjelenítése különálló adatbázisokkal](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. Válassza ki az összes védelemmel ellátni kívánt adatbázist > **az OK gombra**.

   ![Az adatbázis védelme](./media/backup-azure-sql-database/select-database-to-protect.png)

   A biztonsági mentési terhelések optimalizálásához Azure Backup egy biztonsági mentési feladatban lévő adatbázisok maximális számát 50-re állítja.

    
     * Azt is megteheti, hogy az automatikus védelmet a teljes példányon vagy always on rendelkezésre állási csoporton engedélyezte. Ehhez válassza a **be** lehetőséget a megfelelő legördülő menüben az automatikus **védelem** oszlopban. Az automatikus védelem funkció nem csupán egyetlen lépésből teszi lehetővé az összes meglévő adatbázis védelmét, de automatikusan védi az adott példányhoz vagy a rendelkezésre állási csoportba felvenni kívánt új adatbázisokat is.  

4. A **biztonsági mentési szabályzat** panel megnyitásához kattintson **az OK** gombra.

    ![Automatikus védelem engedélyezése az Always On rendelkezésre állási csoporton](./media/backup-azure-sql-database/enable-auto-protection.png)

5. A **biztonsági mentési szabályzat kiválasztása**lapon válasszon ki egy házirendet, majd kattintson **az OK**gombra.

   - Válassza ki az alapértelmezett házirendet: HourlyLogBackup.
   - Válassza ki az SQL-hez korábban létrehozott meglévő biztonsági mentési szabályzatot.
   - Adjon meg egy új szabályzatot a RPO és a megőrzési tartomány alapján.

     ![Biztonsági mentési házirend kiválasztása](./media/backup-azure-sql-database/select-backup-policy.png)

6. A **biztonsági mentés** menüben válassza a **biztonsági mentés engedélyezése**lehetőséget.

    ![A kiválasztott biztonsági mentési szabályzat engedélyezése](./media/backup-azure-sql-database/enable-backup-button.png)

7. A konfigurációs folyamat nyomon követése a portál **értesítések** területén.

    ![Értesítési térség](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Biztonsági mentési szabályzat létrehozása

A biztonsági mentési szabályzat meghatározza, hogy mikor készüljön biztonsági mentés, és mennyi ideig őrzi meg a rendszer.

- A rendszer a tároló szintjén hozza létre a szabályzatot.
- Több tároló is használhatja ugyanazt a biztonsági mentési szabályzatot, de a biztonsági mentési szabályzatot minden egyes tárba alkalmaznia kell.
- Amikor létrehoz egy biztonsági mentési szabályzatot, a rendszer az alapértelmezett napi teljes biztonsági mentést.
- Hozzáadhat egy különbözeti biztonsági másolatot, de csak akkor, ha a teljes biztonsági mentés hetente történik.
- [Ismerje meg a](backup-architecture.md#sql-server-backup-types) biztonsági mentési szabályzatok különböző típusait.

Biztonsági mentési szabályzat létrehozásához:

1. A tárolóban kattintson a **biztonsági mentési szabályzatok** > **Hozzáadás**elemre.
2. A **Hozzáadás** menüben kattintson **SQL Server az Azure-beli virtuális gépen**. A szabályzat típusának meghatározása.

   ![Válassza ki az új biztonsági mentési szabályzathoz tartozó szabályzat típusát](./media/backup-azure-sql-database/policy-type-details.png)

3. A **Házirend neve**mezőben adja meg az új szabályzat nevét.
4. A **teljes biztonsági mentési szabályzatban**válassza ki a **biztonsági mentés gyakoriságát**, és válassza a **naponta** vagy **hetente**lehetőséget.

   - **Napi szinten**válassza ki az órát és az időzónát a biztonsági mentési feladatok elkezdése után.
   - Teljes biztonsági mentést kell futtatnia, mert nem lehet kikapcsolni a **teljes biztonsági mentés** lehetőséget.
   - A szabályzat megtekintéséhez kattintson a **teljes biztonsági mentés** elemre.
   - A napi teljes biztonsági mentésekhez nem hozhat létre különbözeti biztonsági másolatokat.
   - **Hetente**válassza ki a hét, óra és időzóna napját a biztonsági mentési feladatok elkezdése után.

     ![Új biztonsági mentési szabályzat mezői](./media/backup-azure-sql-database/full-backup-policy.png)  

5. A **megőrzési**időtartam beállításnál alapértelmezés szerint minden beállítás ki van választva. Törölje a nem kívánt adatmegőrzési tartomány korlátozásait, és állítsa be a használandó intervallumokat.

    - A minimális megőrzési idő bármilyen típusú biztonsági mentés esetén (teljes/különbözeti/napló) 7 nap.
    - A helyreállítási pontok megőrzési időtartam alapján vannak címkézve. Ha például napi teljes biztonsági mentést választ, a naponta csak egy teljes biztonsági mentést indít el.
    - Egy adott nap biztonsági másolata a heti megőrzési időtartam és a heti megőrzési beállítás alapján van megjelölve és megőrizve.
    - A havi és az éves adatmegőrzési tartomány hasonló módon viselkedik.

   ![Megőrzési tartomány intervallumának beállításai](./media/backup-azure-sql-database/retention-range-interval.png)

6. A **teljes biztonsági mentési szabályzat** menüben kattintson az **OK** gombra a beállítások elfogadásához.
7. A különbözeti biztonsági mentési szabályzat hozzáadásához válassza a **különbözeti biztonsági mentés**lehetőséget.

   ![A megőrzési időtartam](./media/backup-azure-sql-database/retention-range-interval.png)
   beállításai![között nyissa meg a különbözeti biztonsági mentési házirend menüt](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. A **különbözeti biztonsági mentési házirendben**válassza az **Engedélyezés** lehetőséget a gyakoriság és a megőrzési vezérlők megnyitásához.

    - A legtöbb esetben naponta egy különbözeti biztonsági mentést indíthat.
    - A különbözeti biztonsági mentések legfeljebb 180 napig tárolhatók. Ha nagyobb adatmegőrzésre van szüksége, teljes biztonsági mentést kell használnia.

9. Kattintson az **OK** gombra a szabályzat mentéséhez és a **biztonsági mentési szabályzat** fő menüjéhez való visszatéréshez.

10. Tranzakciós napló biztonsági mentési szabályzatának hozzáadásához válassza a **napló biztonsági mentése**lehetőséget.
11. A **napló biztonsági mentése**területen válassza az **Engedélyezés**lehetőséget, majd állítsa be a gyakoriságot és a megőrzési vezérlőket. A naplók biztonsági mentései akár 15 percenként is előfordulhatnak, és akár 35 napig is megtartható.
12. Kattintson az **OK** gombra a szabályzat mentéséhez és a **biztonsági mentési szabályzat** fő menüjéhez való visszatéréshez.

    ![A napló biztonsági mentési szabályzatának szerkesztése](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. A **biztonsági mentési házirend** menüben adja meg, hogy engedélyezi-e az SQL-alapú **biztonsági másolatok tömörítését**.
    - A tömörítés alapértelmezés szerint le van tiltva.
    - A háttérben Azure Backup az SQL natív biztonsági mentési tömörítést használja.

14. Miután befejezte a módosításokat a biztonsági mentési szabályzatban, kattintson az **OK gombra**.

## <a name="run-an-ad-hoc-backup"></a>Alkalmi biztonsági mentés futtatása

1. A Recovery Services-tárolóban válassza a biztonsági másolati elemek lehetőséget.
2. Kattintson az "SQL Azure-beli virtuális gépen" elemre.
3. Kattintson a jobb gombbal az adatbázisra, és válassza a "biztonsági mentés most" lehetőséget.
4. Válassza ki a biztonsági mentés típusát (teljes/különbözet/napló/másolás csak teljes) és tömörítés (Engedélyezés/Letiltás)
5. A biztonsági mentés elindításához kattintson az OK gombra.
6. A biztonsági mentési feladat figyeléséhez lépjen a Recovery Services-tárolóra, és válassza a "biztonsági mentési feladatok" lehetőséget.


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a következőket végezte el az Azure Portalon:

> [!div class="checklist"]
> * Tároló létrehozása és konfigurálása.
> * Adatbázisok felderítése és biztonsági mentések beállítása.
> * Automatikus védelem beállítása adatbázisok számára.
> * Futtasson egy ad-hoc biztonsági mentést.

Folytassa a következő oktatóanyaggal, ha tudni szeretné, hogyan lehet lemezről visszaállítani Azure-beli virtuális gépeket.

> [!div class="nextstepaction"]
> [SQL Server-adatbázisok visszaállítása Azure-beli virtuális gépeken](./restore-sql-database-azure-vm.md)
 

