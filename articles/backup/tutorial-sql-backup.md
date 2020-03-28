---
title: Oktatóanyag – Sql Server-adatbázisok biztonsági mentése az Azure-ba
description: Ebben az oktatóanyagban megtudhatja, hogyan készíthet biztonsági másolatot egy Azure-beli virtuális gépen futó SQL Server-adatbázisról egy Azure Backup Recovery Services-tárolóba.
ms.topic: tutorial
ms.date: 06/18/2019
ms.openlocfilehash: 3216404b04647e2fb5f15beb09b9a4ccd6648bba
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74171886"
---
# <a name="back-up-a-sql-server-database-in-an-azure-vm"></a>SQL Server-adatbázis biztonsági mentése Azure-beli virtuális gépben

Ez az oktatóanyag bemutatja, hogyan készíthet biztonsági másolatot egy Azure-beli virtuális gépen futó SQL Server-adatbázisról egy Azure Backup Recovery Services-tárolóba. Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
>
> * Hozzon létre és konfiguráljon egy tárolót.
> * Fedezze fel az adatbázisokat, és állítson be biztonsági másolatokat.
> * Állítsa be az adatbázisok automatikus védelmét.
> * Igény szerinti biztonsági mentés futtatása.

## <a name="prerequisites"></a>Előfeltételek

Az SQL Server adatbázis biztonsági mentése előtt ellenőrizze az alábbi feltételeket:

1. Azonosítsa vagy [hozzon létre](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) egy Recovery Services-tárolót ugyanabban a régióban vagy területi beállítással, mint az SQL Server-példányt üzemeltető virtuális gép.
2. Ellenőrizze az SQL-adatbázisok biztonsági mentén szükséges [virtuális gép engedélyeket.](backup-azure-sql-database.md#set-vm-permissions)
3. Ellenőrizze, hogy a virtuális gép rendelkezik-e [hálózati kapcsolattal.](backup-sql-server-database-azure-vms.md#establish-network-connectivity)
4. Ellenőrizze, hogy az SQL Server-adatbázisok elnevezése az Azure Backup [elnevezési irányelveinek](#verify-database-naming-guidelines-for-azure-backup) megfelelően történik-e.
5. Ellenőrizze, hogy nincs-e más biztonsági mentési megoldás engedélyezve az adatbázishoz. Tiltsa le az összes többi SQL Server biztonsági mentést a forgatókönyv beállítása előtt. Engedélyezheti az Azure Backup egy Azure virtuális gép mellett az Azure Backup egy SQL Server-adatbázis fut a virtuális gép ütközés nélkül.

### <a name="establish-network-connectivity"></a>Hálózati kapcsolat létrehozása

Az SQL Server virtuális gép virtuális gépének minden művelethez szüksége van az Azure nyilvános IP-címeivel való kapcsolatra. A virtuális gép műveletei (adatbázis-felderítés, biztonsági mentések konfigurálása, biztonsági mentések ütemezése, helyreállítási pontok visszaállítása és így tovább) sikertelenek a nyilvános IP-címekhez való csatlakozás nélkül. Hozzon létre kapcsolatot az alábbi lehetőségek egyikével:

* **Az Azure-adatközpont IP-tartományai**engedélyezése: Engedélyezze az [IP-tartományokat](https://www.microsoft.com/download/details.aspx?id=41653) a letöltésben. A hálózati biztonsági csoport (NSG) eléréséhez használja a **Set-AzureNetworkSecurityRule** parancsmag.
* **HTTP-proxykiszolgáló üzembe helyezése a forgalom irányításához:** Amikor egy Azure-beli virtuális gépen biztonsági másolatot készít egy SQL Server-adatbázisról, a virtuális gép biztonsági mentési bővítménye a HTTPS API-k használatával küldfelügyeleti parancsokat az Azure Backupnak, és adatokat az Azure Storage-nak. A biztonsági mentési bővítmény az Azure Active Directoryt (Azure AD) is használja a hitelesítéshez. Irányítsa át a biztonsági mentési bővítmény forgalmát a három szolgáltatáshoz a HTTP-proxyn keresztül. A bővítmények az egyetlen olyan összetevő, amely a nyilvános internet eléréséhez van konfigurálva.

Minden lehetőségnek vannak előnyei és hátrányai

**Beállítás** | **Előnyök** | **Hátrányok**
--- | --- | ---
IP-tartományok engedélyezése | Nincsenek további költségek. | Kezelm a kezelni meddő, mert az IP-címtartományok idővel változnak. <br/><br/> Hozzáférést biztosít az Azure egészéhez, nem csak az Azure Storage-hoz.
HTTP-proxy használata   | A tároló URL-címének részletes vezérlése a proxyban engedélyezett. <br/><br/> A virtuális gépek internet-hozzáférésének egypontja. <br/><br/> Nem vonatkozik az Azure IP-cím módosításai. | A virtuális gép proxyszoftverrel való futtatásának további költségei.

### <a name="set-vm-permissions"></a>Virtuálisgép-engedélyek beállítása

Az Azure Backup számos dolgot tesz, amikor egy SQL Server-adatbázis biztonsági mentését konfigurálja:

* Hozzáadja az **AzureBackupWindowsWorkload** bővítményt.
* Adatbázisok felderítéséhez a virtuális gépen, az Azure Backup létrehozza az **NT SERVICE\AzureWLBackupPluginSvc fiókot.** Ez a fiók biztonsági mentésre és visszaállításra szolgál, és SQL sysadmin engedélyeket igényel.
* Az Azure Backup az **NT AUTHORITY\SYSTEM fiókot** használja az adatbázis felderítéséhez/lekérdezéséhez, így ennek a fióknak nyilvános bejelentkezésnek kell lennie az SQL-en.

Ha nem az Azure Marketplace-ről hozták létre az SQL Server virtuális gépét, hibaüzenet jelenhet meg **UserErrorSQLNoSysadminMembership néven.** Ebben az esetben [kövesse az alábbi utasításokat](backup-azure-sql-database.md#set-vm-permissions).

### <a name="verify-database-naming-guidelines-for-azure-backup"></a>Az Azure Backup adatbázis-elnevezési irányelveinek ellenőrzése

Az adatbázisnevek esetében kerülje a következőket:

* Záró/vezető szóközök
* Záró "!"
* Szögletes zárójel bezárása "]"
* 'F:\' kezdetű adatbázisnevek

Az Azure-tábla nem támogatott karakterei aliasok használatával rendelkezünk, de azt javasoljuk, hogy kerülje őket. [További információ](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN).

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>SQL Server-adatbázisok felderítése

A virtuális gépen futó adatbázisok felderítése.

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg a Recovery Services-tárolót, amelyet az adatbázis biztonsági mentéséhez használ.

2. A **Recovery Services-tároló** irányítópultján válassza a **Biztonsági másolat lehetőséget.**

   ![A Biztonsági másolat kiválasztása parancs a Biztonsági másolat menü megnyitásához](./media/backup-azure-sql-database/open-backup-menu.png)

3. A **Biztonsági másolat célja mezőben**állítsa be a Hol fut a számítási feladatok **az** **Azure-ba** (ez az alapértelmezett).

4. A **Mit szeretne biztonsági másolatot készíteni,** válassza az SQL Server az Azure VM.In What you want to backup ( Mit szeretne biztonsági másolatot készíteni ) lehetőséget **az SQL Server az Azure VM-ben**elemre.

    ![Válassza ki az SQL Server t az Azure VM-ben a biztonsági mentéshez](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. A **biztonsági mentési cél** > **felderítése a virtuális gépeken**a **Felderítés indítása** lehetőséget választja a nem védett virtuális gépek kereséséhez az előfizetésben. Eltarthat egy ideig, attól függően, hogy hány nem védett virtuális gépek az előfizetésben.

   * A nem védett virtuális gépeknek a felderítés után meg kell jelenniük a listában, név és erőforráscsoport szerint felsorolva.
   * Ha egy virtuális gép nem szerepel a várt módon, ellenőrizze, hogy már biztonsági mentést egy tárolóban.
   * Több virtuális gép is rendelkezhet ugyanazzal a névvel, de különböző erőforráscsoportokhoz fognak tartozni.

     ![A biztonsági mentés a virtuális gépeken lévő DB-k keresése közben függőben van](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. A virtuális gép listában válassza ki az SQL Server adatbázist futtató virtuális gép > **Fedezze fel a DB-ket.**

7. Kövesse nyomon az adatbázis-felderítést az **Értesítések** területen. Eltarthat egy ideig a feladat befejezéséhez, attól függően, hogy hány adatbázis van a virtuális gépen. A kijelölt adatbázisok felderítésekénegy sikeres üzenet jelenik meg.

    ![A telepítés sikeressé tévő üzenete](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Az Azure Backup felderíti a virtuális gép összes SQL Server-adatbázisát. A felderítés során a következő történik a háttérben:

    * Az Azure Backup regisztrálja a virtuális gépet a tárolóval a számítási feladatok biztonsági mentéséhez. A regisztrált virtuális gép összes adatbázisa csak erre a tárolóba menthető.
    * Az Azure Backup telepíti az **AzureBackupWindowsWorkload** bővítményt a virtuális gépre. Nincs ügynök telepítve az SQL-adatbázisra.
    * Az Azure Backup létrehozza az **NT Service\AzureWLBackupPluginSvc szolgáltatásfiókot** a virtuális gépen.
      * Minden biztonsági mentési és visszaállítási művelet a szolgáltatásfiókot használja.
      * **NT Service\AzureWLBackupPluginSvc** sql sysadmin engedélyeket igényel. Az Azure Piactéren létrehozott összes SQL Server virtuális gép az **SqlIaaSExtension telepítve** van. Az **AzureBackupWindowsWorkload** bővítmény az **SQLIaaSExtension használatával** automatikusan megkapja a szükséges engedélyeket.
    * Ha nem a piactérről hozták létre a virtuális gép, akkor a virtuális gép nem telepítette az **SqlIaaSExtension-t,** és a felderítési művelet sikertelen a **UserErrorSQLNoSysAdminMembership**hibaüzenettel. A probléma megoldásához kövesse az [utasításokat.](backup-azure-sql-database.md#set-vm-permissions)

        ![Válassza ki a virtuális gép és az adatbázis](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Biztonsági mentés konfigurálása  

A biztonsági mentés konfigurálása a következőképpen:

1. A **Biztonsági másolat célja csoportban**válassza a Biztonsági másolat **konfigurálása**lehetőséget.

   ![Válassza a Biztonsági másolat konfigurálása lehetőséget](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. Kattintson a **Biztonsági másolat konfigurálása**gombra, és megjelenik a **Biztonsági másolathoz elemek kiválasztása** panel. Ez felsorolja az összes regisztrált rendelkezésre állási csoportok és önálló SQL-kiszolgálók. Bontsa ki a sortól balra lévő sávsávot az adott példány összes nem védett adatbázisának megtekintéséhez, vagy a Mindig a ag-n.  

    ![Az összes SQL Server-példány megjelenítése önálló adatbázisokkal](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. Jelölje ki az összes védeni kívánt adatbázist > **OK**.

   ![Az adatbázis védelme](./media/backup-azure-sql-database/select-database-to-protect.png)

   A biztonsági mentési terhelésoptimalizáláshoz az Azure Backup egy biztonsági mentési feladatban legfeljebb számú adatbázist állít be 50-re.

     * Másik lehetőségként engedélyezheti az automatikus védelmet a teljes példányon vagy a Mindig rendelkezésre álláskor csoportban, ha az AUTOPROTECT oszlop megfelelő legördülő legördülő menübe a **BE** lehetőséget **választja.** Az automatikus védelem szolgáltatás nem csak lehetővé teszi a védelmet az összes meglévő adatbázisok egy menetben, hanem automatikusan védi az új adatbázisok, amelyek hozzá adódnak az adott példány vagy a rendelkezésre állási csoport a jövőben.  

4. Kattintson az **OK** gombra a **Biztonsági másolat házirend** panel megnyitásához.

    ![Automatikus védelem engedélyezése a Mindig bekapcsolva álláscsoportban](./media/backup-azure-sql-database/enable-auto-protection.png)

5. A **Biztonsági másolat kiválasztása csoportban**jelöljön ki egy házirendet, majd kattintson az **OK**gombra.

   * Válassza ki az alapértelmezett házirendet: HourlyLogBackup.
   * Válasszon egy korábban az SQL-hez létrehozott biztonsági mentési házirendet.
   * Új házirend definiálása az RPO és a megőrzési tartomány alapján.

     ![Válassza a Biztonsági mentési házirend lehetőséget](./media/backup-azure-sql-database/select-backup-policy.png)

6. A **Biztonsági másolat** menüben válassza a Biztonsági másolat engedélyezése **parancsot.**

    ![A kiválasztott biztonsági mentési házirend engedélyezése](./media/backup-azure-sql-database/enable-backup-button.png)

7. A konfigurációs folyamat nyomon követése a portál **Értesítések** területén.

    ![Értesítési terület](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Biztonsági mentési házirend létrehozása

A biztonsági mentési szabályzat határozza meg, hogy mikor és mennyi ideig őrzi meg a biztonsági mentéseket.

* A szabályzat a tároló szintjén jön létre.
* Több tárolóhasználhatja ugyanazt a biztonsági mentési szabályzatot, de minden tárolóra alkalmaznia kell a biztonsági mentési szabályzatot.
* Biztonsági mentési házirend létrehozásakor a napi teljes biztonsági mentés az alapértelmezett.
* Hozzáadhat egy különbözeti biztonsági mentést, de csak akkor, ha a teljes biztonsági mentést hetente konfigurálja.
* További információ a biztonsági mentési házirendek különböző [típusairól.](backup-architecture.md#sql-server-backup-types)

Biztonsági mentési házirend létrehozása:

1. A tárolóban kattintson a **Biztonsági másolat házirendek** > **hozzáadása elemre.**
2. A **Hozzáadás** menüben kattintson **az SQL Server az Azure VM-ben** a házirend típusának meghatározásához.

   ![Az új biztonsági mentési házirend házirendtípusának kiválasztása](./media/backup-azure-sql-database/policy-type-details.png)

3. A **Házirend neve**mezőbe írja be az új házirend nevét.
4. A **Teljes biztonsági mentés házirendben**válassza a Biztonsági mentés **gyakoriságát,** és válassza a **Napi** vagy **heti**lehetőséget.

   * A **Napi**területen válassza ki azt az órát és időzónát, amikor a biztonsági mentési feladat elkezdődik.
   * Teljes biztonsági másolatot kell futtatnia, mivel nem tudja kikapcsolni a **Teljes biztonsági mentés** beállítást.
   * A házirend megtekintéséhez kattintson a **Teljes biztonsági másolat** gombra.
   * A napi teljes biztonsági mentések nem hozhatnak létre különbözeti biztonsági mentéseket.
   * A **Heti**lehetőséget válassza ki a hét, az óra és az időzóna napját, amikor a biztonsági mentési feladat megkezdődik.

     ![Új biztonságimentési házirend-mezők](./media/backup-azure-sql-database/full-backup-policy.png)  

5. Az **Adatmegőrzési tartomány mezőben**alapértelmezés szerint minden beállítás be van jelölve. Törölje a nem kívánt megőrzési tartomány korlátokat, amelyeket nem kíván használni, és állítsa be a használni kívánt időközöket.

    * Minimális megőrzési idő bármely típusú biztonsági mentés (teljes/különbözeti/log) hét nap.
    * A helyreállítási pontok a megőrzési tartományuk alapján vannak címkézve megőrzésre. Ha például kiválaszt egy napi teljes biztonsági mentést, naponta csak egy teljes biztonsági mentés aktiválódik.
    * Egy adott nap biztonsági mentése címkézve van, és a heti megőrzési tartomány és a heti megőrzési beállítás alapján megmarad.
    * A havi és éves megőrzési tartományok hasonlóan viselkednek.

   ![Adatmegőrzési tartomány időköze beállításai](./media/backup-azure-sql-database/retention-range-interval.png)

6. A **Teljes biztonsági mentés házirend** menüben válassza az **OK** gombot a beállítások elfogadásához.
7. Különbözeti biztonsági mentési házirend hozzáadásához válassza a **Különbözeti biztonsági mentés lehetőséget.**

   ![Adatmegőrzési](./media/backup-azure-sql-database/retention-range-interval.png)
   ![tartomány időközebeállításai A különbözeti biztonsági mentési házirend menüjének megnyitása](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. A **Különbözeti biztonsági mentés házirendben**válassza az **Engedélyezés** lehetőséget a gyakorisági és megőrzési vezérlők megnyitásához.

    * Legtöbb, akkor kiváltó egy differenciál biztonsági mentés naponta.
    * A különbözeti biztonsági mentések legfeljebb 180 napig őrizhetők meg. Ha hosszabb megőrzésre van szüksége, teljes biztonsági mentést kell használnia.

9. Válassza az **OK gombot** a házirend mentéséhez és a **Biztonsági másolat főházirend-menüjéhez** való visszatéréshez.

10. Tranzakciós napló biztonsági mentési házirendjének hozzáadásához válassza **a Biztonsági másolat naplózása**lehetőséget.
11. A **Napló biztonsági másolat a**mezőben válassza az **Engedélyezés**lehetőséget, majd állítsa be a gyakorisági és megőrzési vezérlőket. A naplóbiztonsági mentések 15 percenként is előfordulhatnak, és legfeljebb 35 napig őrizhetők meg.
12. Válassza az **OK gombot** a házirend mentéséhez és a **Biztonsági másolat főházirend-menüjéhez** való visszatéréshez.

    ![A naplóbiztonsági házirend szerkesztése](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. A **Biztonsági másolat házirend** menüjében adja meg, hogy engedélyezi-e **az SQL Backup Compression parancsot.**
    * A tömörítés alapértelmezés szerint le van tiltva.
    * A háttérben az Azure Backup SQL natív biztonsági mentési tömörítést használ.

14. Miután befejezte a biztonsági mentési házirend szerkesztését, válassza az **OK gombot.**

## <a name="run-an-on-demand-backup"></a>Igény szerinti biztonsági mentés futtatása

1. A Recovery Services-tárolóban válassza a Biztonsági másolat elemek lehetőséget.
2. Kattintson az "SQL az Azure-beli virtuális gép" elemre.
3. Kattintson a jobb gombbal egy adatbázisra, és válassza a "Biztonsági mentés most" lehetőséget.
4. Válassza ki a biztonsági mentés típusát (teljes/különbözeti/napló/másolás csak teljes) és a tömörítést (engedélyezés/letiltás)
5. A biztonsági mentés megkezdéséhez válassza az OK gombot.
6. Figyelje a biztonsági mentési feladatot a Recovery Services-tárolóba, és válassza a "Biztonsági mentési feladatok" lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a következőket végezte el az Azure Portalon:

> [!div class="checklist"]
>
> * Hozzon létre és konfiguráljon egy tárolót.
> * Fedezze fel az adatbázisokat, és állítson be biztonsági másolatokat.
> * Állítsa be az adatbázisok automatikus védelmét.
> * Igény szerinti biztonsági mentés futtatása.

Folytassa a következő oktatóanyaggal, ha tudni szeretné, hogyan lehet lemezről visszaállítani Azure-beli virtuális gépeket.

> [!div class="nextstepaction"]
> [SQL Server-adatbázisok visszaállítása az Azure virtuális gépein](./restore-sql-database-azure-vm.md)
