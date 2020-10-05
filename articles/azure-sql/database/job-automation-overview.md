---
title: Feladat automatizálása
description: A feladatütemezés használata Transact-SQL (T-SQL) parancsfájlok futtatásához egy vagy több adatbázison keresztül
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/10/2020
ms.openlocfilehash: 6b4b31ab4bc0cb1fe5bd9140870df86db6841ff3
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91450354"
---
# <a name="automate-management-tasks-using-database-jobs"></a>Felügyeleti feladatok automatizálása adatbázis-feladatok használatával
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

A Transact-SQL (T-SQL) lekérdezések futtatásához és a karbantartási feladatok végrehajtásához olyan feladatokat hozhat létre és ütemezhet, amelyek egy vagy több adatbázisra vonatkozóan rendszeres időközönként végrehajthatók.

Meghatározhat céladatbázist vagy adatbáziscsoportokat, ahol a feladatokat végre szeretné hajtani, és ütemezést is megadhat a feladatok futtatásához.
Egy feladat kezeli a céladatbázisba irányuló bejelentkezéseket. Emellett Transact-SQL-szkripteket is meghatározhat, fenntarthat és megőrizhet, amelyeket adatbázisok egy csoportján futtat.

Minden feladat naplózza a végrehajtás állapotát, és ha bármilyen hiba történik, automatikusan újrapróbálkozik a művelettel.

## <a name="when-to-use-automated-jobs"></a>Mikor kell automatikus feladatokat használni

A feladatok automatizálását több esetben is használhatja:

- Automatizálja a felügyeleti feladatokat, és ütemezze őket a hétköznapok futtatásához, órák után stb.
  - Üzembe helyezhet sémamódosításokat, kezelheti a hitelesítő adatokat, teljesítményadatokat vagy bérlői (ügyfél-) telemetriát gyűjthet.
  - Frissítse a hivatkozási adatokat (az összes adatbázissal közös információ), töltse be az adatokat az Azure Blob Storage-ból.
  - Az indexek újraépítésével javíthatja a lekérdezési teljesítményt. Úgy konfigurálhatja a feladatokat, hogy a rendszer egy adott adatbázis-gyűjteményen rendszeres időközönként hajtsa őket végre, például csúcsidőn kívül.
  - A lekérdezési adatokat az adatbázis-készletekből folyamatosan egy központi táblába gyűjtheti. A teljesítménylekérdezések folyamatosan végrehajthatók, illetve konfigurálhatók úgy, hogy további végrehajtandó feladatokat indítsanak el.
- Adatokat gyűjthet jelentéskészítéshez
  - Adatbázisokból származó összesített adatok egyetlen célként megadott táblába.
  - Olyan hosszabban futó adatfeldolgozási lekérdezéseket hajthat végre nagy adatbáziskészleteken, amilyen például az ügyfél-telemetria gyűjtése. A rendszer az eredményeket egyetlen céltáblában gyűjti össze a további elemzéshez.
- Adatáthelyezés
  - Olyan feladatokat hozhat létre, amelyek replikálják az adatbázisaiban végrehajtott módosításokat más adatbázisokra, vagy a távoli adatbázisokban végrehajtott frissítéseket gyűjtik, és módosításokat alkalmaznak az adatbázisban.
  - SQL Server Integration Services (SSIS) használatával olyan feladatokat hozhat létre, amelyek a vagy az adatbázisaiba töltik be az adatait.

## <a name="overview"></a>Áttekintés

A következő feladatütemezés-technológiák érhetők el:

- Az **SQL Agent-feladatok** a klasszikus és a harci tesztelésen átesett SQL Server feladatütemezés összetevő, amely az Azure SQL felügyelt példányában érhető el. Az SQL-ügynök feladatai nem érhetők el Azure SQL Databaseban.
- **Elastic Database feladatok (előzetes verzió)** olyan feladatütemezés-szolgáltatások, amelyek a Azure SQL Database egy vagy több adatbázisában egyéni feladatokat hajtanak végre.

Érdemes megjegyezni, hogy az SQL-ügynök (a helyszínen és az SQL felügyelt példányának részeként elérhető) és az adatbázis rugalmas feladatainak (az Azure szinapszis Analytics-ben Azure SQL Database és adatbázisaiban található önálló adatbázisokhoz elérhető) közötti különbségeket is figyelembe kell venni.

| |Rugalmas feladatok |SQL-ügynök |
|---------|---------|---------|
|**Hatókör** | A Azure SQL Database és/vagy adattárházban lévő adatbázisok száma ugyanabban az Azure-felhőben, mint a feladatsor. A célok lehetnek különböző kiszolgálókon, előfizetéseken és/vagy régiókban. <br><br>A célcsoportok állhatnak egyedi adatbázisokból vagy adattárházakból, illetve egy kiszolgáló, készlet vagy szegmenstérkép összes adatbázisából (a feladat futásidejében dinamikusan számba véve). | Az SQL-ügynökkel megegyező példányban lévő egyes adatbázisok. |
|**Támogatott API-k és eszközök** | Portál, PowerShell, T-SQL, Azure Resource Manager | T-SQL, SQL Server Management Studio (SSMS) |

## <a name="sql-agent-jobs"></a>SQL Agent-feladatok

Az SQL-ügynök feladatai a T-SQL-parancsfájlok megadott sorozata az adatbázison. A feladatok használatával olyan felügyeleti feladatokat határozhat meg, amelyek egy vagy több alkalommal futtathatók, és a sikerhez vagy a meghibásodáshoz is megfigyelhetők.
A feladatok futhatnak egy helyi kiszolgálón vagy több távoli kiszolgálón is. Az SQL Agent-feladatok egy belső adatbázismotor-összetevő, amely a felügyelt példányok szolgáltatáson belül fut.
Az SQL Agent feladatainak számos alapvető fogalma van:

- A feladatok **végrehajtásához** szükséges lépések egy vagy több lépésből állnak. Minden feladat lépésnél megadhatja az újrapróbálkozási stratégiát, valamint azt a műveletet, amely a feladat lépéseinek sikeres vagy sikertelen végrehajtása esetén fordulhat elő.
- Az **ütemezett** feladatok határozzák meg, hogy mikor kell végrehajtani a feladatot.
- Az **értesítések** segítségével meghatározhatja azokat a szabályokat, amelyek segítségével a rendszer e-mailben értesíti a kezelőket a feladatok befejezését követően.

### <a name="job-steps"></a>A feladatok lépései

Az SQL Agent-feladatok lépései az SQL Agent által végrehajtandó műveletek sorozatából állnak. Minden lépésnél a következő lépésnek kell megjelennie, ha a lépés sikeres vagy sikertelen, hiba esetén az újrapróbálkozások száma.

Az SQL-ügynök lehetővé teszi, hogy különböző típusú feladatokat hozzon létre, például a Transact-SQL-feladatok lépéseit, amelyek egyetlen Transact-SQL-köteget hajtanak végre az adatbázison, vagy az operációs rendszert futtató, az egyéni operációsrendszer-parancsfájlt végrehajtó operációsrendszer-parancsot, illetve PowerShell-lépéseket, amelyek lehetővé teszik az adatok SSIS-futtatókörnyezettel való betöltését [, illetve az](../managed-instance/replication-transactional-overview.md) adatbázisból

A [tranzakciós replikáció](../managed-instance/replication-transactional-overview.md) egy adatbázismotor-szolgáltatás, amely lehetővé teszi egy adatbázis egy vagy több tábláján végzett módosítások közzétételét, valamint az előfizetői adatbázisokba való közzétételét és terjesztését. A módosítások közzététele a következő SQL-ügynök-feladattípus-típusok használatával valósítható meg:

- Tranzakciónapló-olvasó.
- Pillanatkép.
- Terjesztő.

Az egyéb típusú feladatok lépései jelenleg nem támogatottak, beleértve a következőket:

- A replikációs feladatok egyesítése lépés nem támogatott.
- A várólista-olvasó nem támogatott.
- A Analysis Services nem támogatottak

### <a name="job-schedules"></a>Feladatütemezések

Az ütemterv meghatározza, hogy mikor fusson a feladatok. Ugyanazon az ütemterven több feladatot is futtathat, és több ütemező is vonatkozhat ugyanarra a feladatokra.
Az ütemtervek a következő feltételeket határozzák meg a feladatok futásának idejére:

- Amikor a rendszer újraindítja a példányt (vagy SQL Server Agent indításakor). A feladatot minden feladatátvétel után aktiválja a rendszer.
- Egy alkalommal, egy adott dátummal és időponttal, amely bizonyos feladatok késleltetett végrehajtásához hasznos.
- Ismétlődő ütemterv szerint.

> [!Note]
> Az SQL felügyelt példány jelenleg nem teszi lehetővé a feladatok elindítását, ha a példány "tétlen".

### <a name="job-notifications"></a>Feladatok értesítései

Az SQL Agent-feladatok lehetővé teszik, hogy értesítéseket kapjon a feladat sikeres befejeződése vagy meghibásodása esetén. Értesítéseket e-mailben kaphat.

Először be kell állítania az e-mail értesítések küldéséhez használt e-mail-fiókot, majd hozzá kell rendelnie a fiókot az nevű e-mail-profilhoz `AzureManagedInstance_dbmail_profile` , ahogy az az alábbi példában is látható:

```sql
-- Create a Database Mail account
EXECUTE msdb.dbo.sysmail_add_account_sp
    @account_name = 'SQL Agent Account',
    @description = 'Mail account for Azure SQL Managed Instance SQL Agent system.',
    @email_address = '$(loginEmail)',
    @display_name = 'SQL Agent Account',
    @mailserver_name = '$(mailserver)' ,
    @username = '$(loginEmail)' ,
    @password = '$(password)'

-- Create a Database Mail profile
EXECUTE msdb.dbo.sysmail_add_profile_sp
    @profile_name = 'AzureManagedInstance_dbmail_profile',
    @description = 'E-mail profile used for messages sent by Managed Instance SQL Agent.' ;

-- Add the account to the profile
EXECUTE msdb.dbo.sysmail_add_profileaccount_sp
    @profile_name = 'AzureManagedInstance_dbmail_profile',
    @account_name = 'SQL Agent Account',
    @sequence_number = 1;
```

A felügyelt példányon Database Mail is engedélyeznie kell:

```sql
GO
EXEC sp_configure 'show advanced options', 1;
GO
RECONFIGURE;
GO
EXEC sp_configure 'Database Mail XPs', 1;
GO
RECONFIGURE
```

Értesítheti a kezelőt arról, hogy valami történt az SQL Agent-feladatokkal. Az operátor az SQL által felügyelt példányok egy vagy több példányának fenntartásáért felelős személy kapcsolattartási adatait határozza meg. Egyes esetekben az operátori feladatok egy személyhez vannak rendelve.
Az SQL felügyelt példányában vagy SQL Server több példányban lévő rendszerekben számos személy megoszthatja az operátori feladatokat. Az operátor nem tartalmaz biztonsági információt, és nem határoz meg rendszerbiztonsági tag.

A SSMS vagy a Transact-SQL parancsfájl használatával a következő példában látható módon hozhat létre kezelőket:

```sql
EXEC msdb.dbo.sp_add_operator
    @name=N'Mihajlo Pupun',
    @enabled=1,
    @email_address=N'mihajlo.pupin@contoso.com'
```

Bármilyen feladatot módosíthat, és a SSMS vagy a következő Transact-SQL-parancsfájl használatával értesítést kaphat e-mailben, ha a feladatok befejeződik, sikertelenek vagy sikeresek lesznek:

```sql
EXEC msdb.dbo.sp_update_job @job_name=N'Load data using SSIS',
    @notify_level_email=3, -- Options are: 1 on succeed, 2 on failure, 3 on complete
    @notify_email_operator_name=N'Mihajlo Pupun'
```

### <a name="sql-agent-job-limitations"></a>SQL Agent-feladatok korlátozásai

A felügyelt példányok nem támogatják a SQL Serverban elérhető SQL Agent-funkciók némelyikét:

- Az SQL-ügynök beállításai csak olvashatók. `sp_set_agent_properties`A felügyelt példány nem támogatja az eljárást.
- Az SQL Agent engedélyezése/letiltása jelenleg nem támogatott a felügyelt példányokban. Az SQL Agent folyamatosan fut.
- Az értesítések részben támogatottak
  - A személyhívó nem támogatott.
  - A NetSend nem támogatott.
  - A riasztások nem támogatottak.
- A proxyk nem támogatottak.
- Az Eseménynapló nem támogatott.

További információ a SQL Server Agentről: [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent).

## <a name="elastic-database-jobs-preview"></a>Elastic Database feladatok (előzetes verzió)

**Rugalmas adatbázis-feladatok** segítségével egy vagy több T-SQL-szkriptet párhuzamosan, nagy számú adatbázison, ütemezve vagy igény szerint futtathat.

**Adatbázisok bármilyen kombinációján futtathat feladatokat**: egy vagy több különálló adatbázison, egy kiszolgáló összes adatbázisán, egy rugalmas készletben található összes adatbázison vagy egy szegmenstérképen – ráadásul a további rugalmasság érdekében adott adatbázisokat is bevonhat vagy kizárhat. **A feladatok több kiszolgálón, több készleten, vagy akár különböző előfizetésekhez tartozó adatbázisokon is futtathatók.** A kiszolgálókat és készleteket a rendszer futásidőben dinamikusan veszi számba, ezért a feladatok az összes olyan adatbázison futnak, amelyek a célcsoportban a végrehajtáskor megtalálhatók.

Az alábbi képen egy feladatügynök látható, amely különböző típusú célcsoportokon hajt végre feladatokat:

![Rugalmas feladat ügynökének elméleti modellje](./media/job-automation-overview/conceptual-diagram.png)

### <a name="elastic-job-components"></a>Rugalmas feladat összetevői

|Összetevő | Leírás (további részletek a táblázat alatt találhatók) |
|---------|---------|
|[**Rugalmas feladat ügynöke**](#elastic-job-agent) | Az az Azure-erőforrás, amelyet a feladatok futtatására és kezelésére hoz létre. |
|[**Feladat-adatbázis**](#job-database) | Egy adatbázis a Azure SQL Databaseban, amelyet a Feladatkezelő a feladatok kapcsolódó adatainak, a feladatdefiníciók stb. tárolására használ. |
|[**Célcsoport**](#target-group) | Azon kiszolgálók, készletek, adatbázisok és szegmenstérképek, amelyeken egy feladatot kíván futtatni. |
|[**Feladat**](#job) | A feladatok olyan Munkaegységek, amelyek egy vagy több tevékenységi [lépésből](#job-step)állnak. A feladatlépések meghatározzák a futtatandó T-SQL-szkriptet, valamint a szkript végrehajtásához szükséges egyéb részleteket. |

#### <a name="elastic-job-agent"></a>Rugalmas feladat ügynöke

A rugalmasfeladat-ügynök a feladatok létrehozásához, futtatásához és kezeléséhez használt Azure-erőforrás. A rugalmasfeladat-ügynök egy Azure-erőforrás, amelyet a portálon hozhat létre (a [PowerShell](elastic-jobs-powershell-create.md) és a REST is támogatott).

A **rugalmas feladatok ügynökének** létrehozásához a Azure SQL Database meglévő adatbázisra van szükség. Az ügynök [*feladat-adatbázisként*](#job-database) konfigurálja a meglévő adatbázist.

A rugalmasfeladat-ügynök használata ingyenes. A feladatok adatbázisa a Azure SQL Databaseban található összes adatbázissal megegyező sebességgel lett kiszámlázva.

#### <a name="job-database"></a>Feladat-adatbázis

A *feladat-adatbázis* feladatok meghatározására, valamint a feladat-végrehajtások állapotának és előzményeinek nyomon követésére szolgál. A *feladat-adatbázis* az ügynök metaadatait, naplóit, eredményeit, feladatait, valamint számos hasznos tárolt eljárást és egyéb, a T-SQL-T használó feladatok létrehozására, futtatására és kezelésére szolgáló adatbázis-objektumot is tartalmaz.

Az aktuális előzetes verzióhoz a rugalmas feladatok ügynökének létrehozásához Azure SQL Database (S0 vagy újabb) meglévő adatbázis szükséges.

A *feladat adatbázisának* a szó szerint nem kell újnak lennie, de tiszta, üres, S0 vagy magasabb szolgáltatási célnak kell lennie. A *feladat-adatbázis* javasolt szolgáltatási célja az S1 vagy a magasabb, de az optimális választás a feladat (ok) teljesítményének, a feladatok számának, a feladathoz tartozó célok számának és a gyakran futtatott feladatok számától függ. Előfordulhat például, hogy egy S0-adatbázis elegendő ahhoz, hogy egy olyan feladathoz tartozó ügynök, amely tíz adatbázisnál kevesebb feladatot futtat, de a feladat futtatása percenként nem elég gyors egy S0-adatbázishoz, és a magasabb szolgáltatási szintet is érdemes lehet.

Ha a feladatra vonatkozó művelet a vártnál lassabban működik, [Figyelje](monitor-tune-overview.md#azure-sql-database-and-azure-sql-managed-instance-resource-monitoring) az adatbázis teljesítményét és az erőforrás-kihasználtságot a feladatban a Azure Portal vagy a [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) DMV használatával. Ha egy Erőforrás kihasználtsága, például a CPU, az adat IO vagy a log Write a 100%-ot közelíti meg, és a lassulási időszakokkal összefügg, érdemes lehet az adatbázis növekményes méretezését magasabb szolgáltatási célkitűzésekre (akár a [DTU-modellbe](service-tiers-dtu.md) , akár a [virtuális mag-modellbe](service-tiers-vcore.md)), amíg a feladatok adatbázisának teljesítménye nem megfelelő.

##### <a name="job-database-permissions"></a>Feladat-adatbázis engedélyei

Feladatügynök létrehozásakor a séma, a táblák és a *jobs_reader* nevű szerepkör a *feladat-adatbázisban* jön létre. A szerepkör a következő engedéllyel jön létre, és célja, hogy részletesebb hozzáférés-vezérlést biztosítson a rendszergazdák számára a feladatok monitorozásához:

|Szerepkörnév |'jobs' sémaengedélyek |'jobs_internal' sémaengedélyek |
|---------|---------|---------|
|**jobs_reader** | SELECT | Nincs |

> [!IMPORTANT]
> Mielőtt adatbázis-rendszergazdaként hozzáférést biztosítana a *feladat-adatbázishoz*, fontolja meg a lehetséges biztonsági következményeket. A feladatok létrehozásához és szerkesztéséhez szükséges engedélyekkel rendelkező rosszindulatú felhasználó létrehozhat vagy szerkeszthet egy olyan feladatot, amely tárolt hitelesítő adattal csatlakozik a rosszindulatú felhasználó által vezérelt adatbázishoz, ezáltal a felhasználó meghatározhatja a hitelesítő adathoz tartozó jelszót.

#### <a name="target-group"></a>Célcsoport

A *célcsoport* határozza meg az azokat az adatbázisokat, amelyeket az adott feladatlépés végre lesz hajtva. A célcsoport tetszőleges számban és kombinációban tartalmazhatja a következőket:

- **Logikai SQL Server** – ha meg van adva egy kiszolgáló, a rendszer a feladatok végrehajtásának időpontjában létező összes adatbázist a csoport részévé teheti. A master adatbázis hitelesítő adatait meg kell adni ahhoz, hogy a csoportot a rendszer a feladat végrehajtása előtt számba vegye és frissítse.
- **Rugalmas készlet** – Ha meg van adva egy rugalmas készlet, a feladat végrehajtásának időpontjában a rugalmas készletben található összes adatbázis a csoport tagja lesz. Kiszolgáló esetén a master adatbázis hitelesítő adatait meg kell adni ahhoz, hogy a csoport a feladat futtatása előtt frissíthető legyen.
- **Önálló adatbázis** – adjon meg egy vagy több egyedi adatbázist, amelyet a csoport részévé kíván tenni.
- **Szegmenstérkép** – egy szegmenstérkép adatbázisai.

> [!TIP]
> A feladat végrehajtásakor a *dinamikus számbavételezés* újra kiértékeli a célcsoportokban található adatbázisokat, amelyek kiszolgálókat és készletet is tartalmaznak. A dinamikus számbavételezéssel biztosítható, hogy **a feladatok a feladat végrehajtásakor a kiszolgálón vagy a készletben létező összes adatbázison fussanak**. Az adatbázisok listájának futásidőben történő ismételt kiértékelése különösen hasznos olyan esetekben, amikor a készlet- vagy a kiszolgálótagság gyakran változik.

A készletek és az önálló adatbázisok megadhatók úgy, hogy a csoport részét képezzék vagy ki legyenek zárva a csoportból. Ez lehetővé teszi, hogy az adatbázisok tetszőleges kombinációjából hozzon létre céladatbázist. Hozzáadhat például egy kiszolgálót egy céladatbázishoz, miközben kizárja egy rugalmas készlet egyes adatbázisait (vagy egy egész rugalmas készletet).

A célcsoportok több előfizetésből és több régióból származó adatbázisokat is tartalmazhatnak. Vegye figyelembe, hogy a régiók közötti végrehajtás magasabb késéssel jár, mint a régión belüli.

Az alábbi példákban az látható, hogyan történik a különböző célcsoport-meghatározások dinamikus számba vétele a feladat végrehajtásának pillanatában, a feladat által futtatandó adatbázisok megadásához:

![Példák célcsoportokra](./media/job-automation-overview/targetgroup-examples1.png)

Az **1. példában** olyan célcsoport szerepel, amely egyedi adatbázisok listáját tartalmazza. Ha egy feladatlépést e célcsoport használatával hajt végre, akkor a szóban forgó műveletet a rendszer az összes adatbázison elvégzi.<br>
A **2. példa** egy olyan célcsoportot mutat be, amely célként megadott kiszolgálót tartalmaz. Ha egy feladatlépést e célcsoport használatával hajt végre, akkor a kiszolgáló dinamikus számba vételével a rendszer képes a kiszolgálón aktuálisan megtalálható adatbázisok listájának összeállítására. A szóban forgó műveletet a rendszer az összes adatbázison elvégzi.<br>
A **3. példában** a *2. példában* lévőhöz hasonló célcsoport szerepel, amelyből viszont egy adott adatbázis ki van zárva. A szóban forgó műveletet a rendszer *nem* végzi el a kizárt adatbázison.<br>
A **4. példában** olyan célcsoport szerepel, amely célhelyként egy rugalmas készletet tartalmaz. A *2. példához* hasonlóan a feladat futtatása során a készlet dinamikus számba vételével a rendszer képes a készletben lévő adatbázisok listájának összeállítására.
<br><br>

![További példák a célcsoportokra](./media/job-automation-overview/targetgroup-examples2.png)

**5. példa** és a **6. példa** olyan speciális forgatókönyveket mutat be, amelyekben a kiszolgálók, a rugalmas készletek és az adatbázisok kombinálhatók a belefoglalási és kizárási szabályokkal.<br>
A **7. példa** azt mutatja be, hogy a feladat futtatása során a szilánkleképezésben szereplő szilánkok is kiértékelhetők.

> [!NOTE]
> A feladatok adatbázisa lehet egy adott feladatokhoz tartozó cél. Ebben az esetben a feldolgozói adatbázist ugyanúgy kezeli a rendszer, mint bármely más céladatbázis. A feladathoz tartozó felhasználónak létre kell hoznia és megfelelő engedélyeket kell biztosítania a feladathoz tartozó adatbázishoz, és az adatbázis hatókörön belüli hitelesítő adatainak is léteznie kell a feladathoz tartozó adatbázisban, ugyanúgy, mint bármely más céladatbázis esetében.
>

#### <a name="job"></a>Feladat

A *feladatok* ütemezetten vagy egyszeri feladatként végrehajtható munkaegységek. Egy feladat egy vagy több *feladatlépésből* áll.

##### <a name="job-step"></a>Feladatlépés

Minden feladatlépés meghatároz egy végrehajtandó T-SQL-szkriptet, egy vagy több célcsoportot, amelyen a T-SQL-szkript futtatható és a feladatügynök számára a céladatbázishoz történő csatlakozáshoz szükséges hitelesítő adatokat. Minden feladatlépés testreszabható időtúllépési és újrapróbálkozási szabályzattal és választhatóan megadható kimeneti paraméterekkel rendelkezik.

#### <a name="job-output"></a>Feladat kimenete

A feladatok lépéseinek eredménye részletesen rögzítve lesz az egyes adatbázisokon, a szkript kimenete pedig a megadott táblában. Megadhat egy adatbázist a feladat által visszaadott adatok tárolásához.

#### <a name="job-history"></a>Feladatelőzmények

A feladatok futtatásának előzményeit a rendszer a *feladat-adatbázisban* tárolja. A rendszertisztítási feladat törli a 45 napnál régebbi végrehajtási előzményeket. A 45 napnál nem régebbi előzmények törléséhez hívja meg az **sp_purge_history** tárolt eljárást a *feladat-adatbázisban*.

### <a name="agent-performance-capacity-and-limitations"></a>Az ügynök teljesítménye, kapacitása és korlátai

A rugalmas feladatok minimális számítási erőforrást használnak, amíg a hosszan futó feladatok befejezésére várakoznak.

Az adatbázisok célcsoportjának méretétől és az adott feladat kívánt végrehajtási idejétől (az egyidejű feldolgozók számától) függően az ügynök különböző mértékű számítási kapacitást és teljesítményt igényel a *feladat-adatbázis* részéről (több cél és több feladat esetén nagyobb számítási kapacitás szükséges).

Jelenleg az előzetes verzió 100 feladat egyidejű futtatására képes.

#### <a name="prevent-jobs-from-reducing-target-database-performance"></a>A feladatok a céladatbázis teljesítményét csökkentő hatásának megakadályozása

Ha szeretné elkerülni, hogy az erőforrások túlterheltek legyenek egy rugalmas SQL-készlet adatbázisain történő feladatvégrehajtás közben, akkor a feladatok esetén konfigurálhatja, hogy azok egyszerre legfeljebb hány adatbázison futhatnak.

## <a name="next-steps"></a>További lépések

- [Mi az SQL Server Agent?](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent)
- [Rugalmas feladatok létrehozása és kezelése](elastic-jobs-overview.md)
- [Rugalmas feladatok létrehozása és kezelése a PowerShell-lel](elastic-jobs-powershell-create.md)
- [Rugalmas feladatok létrehozása és kezelése a Transact-SQL (T-SQL) használatával](elastic-jobs-tsql-create-manage.md)
