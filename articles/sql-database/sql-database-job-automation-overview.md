---
title: Feladat automatizálása
description: A Feladatautomatizálás segítségével Transact-SQL (T-SQL) parancsfájlokat futtathat egy vagy több Azure SQL-adatbázis készletében
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: overview
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
ms.date: 03/10/2020
ms.openlocfilehash: dcaaf3c2f793e7148e1695cdfaa68c768db5fff6
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240540"
---
# <a name="automate-management-tasks-using-database-jobs"></a>Felügyeleti feladatok automatizálása adatbázis-feladatokkal

Az Azure SQL Database lehetővé teszi olyan feladatok létrehozását és ütemezését, amelyek rendszeres időközönként végrehajthatók egy vagy több adatbázison a T-SQL-lekérdezések futtatásához és karbantartási feladatok végrehajtásához.
Minden feladat naplózza a végrehajtás állapotát, és automatikusan újrapróbálkozik a műveleteket, ha bármilyen hiba történik.
Megadhatja a céladatbázist vagy az Azure SQL-adatbázisok azon csoportjait, ahol a feladat végrehajtásra kerül, és meghatározhatja a feladat futtatásának ütemezését is.
Egy feladat kezeli a céladatbázisba való bejelentkezés feladatát. A Transact-SQL-parancsfájlokat is definiálhatja, karbantarthatja és megőrizheti, amelyeket az Azure SQL-adatbázisok egy csoportján keresztül kell végrehajtani.

## <a name="when-to-use-automated-jobs"></a>Mikor kell automatikus feladatokat használni?

A feladatautomatizálás ta-

- Automatizálja a felügyeleti feladatokat, és ütemezze őket, hogy minden hétköznap, munkaidő után stb.
  - Üzembe helyezhet sémamódosításokat, kezelheti a hitelesítő adatokat, teljesítményadatokat vagy bérlői (ügyfél-) telemetriát gyűjthet.
  - Frissítse a referenciaadatokat (az összes adatbázisban közös információkat), adatokat töltsön be az Azure Blob storage-ból.
  - Az indexek újraépítésével javíthatja a lekérdezési teljesítményt. Úgy konfigurálhatja a feladatokat, hogy a rendszer egy adott adatbázis-gyűjteményen rendszeres időközönként hajtsa őket végre, például csúcsidőn kívül.
  - A lekérdezési adatokat az adatbázis-készletekből folyamatosan egy központi táblába gyűjtheti. A teljesítménylekérdezések folyamatosan végrehajthatók, illetve konfigurálhatók úgy, hogy további végrehajtandó feladatokat indítsanak el.
- Adatokat gyűjthet jelentéskészítéshez
  - Összesítheti az Azure SQL-adatbázisok egy gyűjteményéből származó adatokat egy céltáblában.
  - Olyan hosszabban futó adatfeldolgozási lekérdezéseket hajthat végre nagy adatbáziskészleteken, amilyen például az ügyfél-telemetria gyűjtése. A rendszer az eredményeket egyetlen céltáblában gyűjti össze a további elemzéshez.
- Adatmozgások
  - Olyan feladatokat hozhat létre, amelyek replikálják az adatbázisokban végrehajtott módosításokat más adatbázisokba, vagy begyűjtik a távoli adatbázisokban végrehajtott frissítéseket, és módosításokat alkalmaznak az adatbázisban.
  - Olyan feladatokat hozhat létre, amelyek adatokat töltenek be az adatbázisokból vagy az adatbázisokba az SQL Server Integration Services (SSIS) használatával.

## <a name="overview"></a>Áttekintés

Az Azure SQL Database-ben a következő feladatütemezési technológiák érhetők el:

- **Az SQL Agent-feladatok** klasszikus és harcedzett SQL Server feladatütemezési összetevő, amely a felügyelt példányban érhető el. AZ SQL Agent-feladatok nem érhetők el az Azure SQL egyetlen adatbázisokban.
- **Rugalmas adatbázis-feladatok (előzetes verzió)** olyan feladatütemezési szolgáltatások, amelyek egyéni feladatokat hajtanak végre egy vagy több Azure SQL-adatbázison.

Érdemes megjegyezni néhány különbség az SQL Agent (elérhető a helyszínen és az SQL Database felügyelt példány) és az adatbázis rugalmas feladat ügynök (elérhető az azure-beli SQL-adatbázis és adatbázisok az SQL Data Warehouse) között néhány különbséget.

| |Rugalmas feladatok |SQL-ügynök |
|---------|---------|---------|
|Hatókör | Tetszőleges számú Azure SQL-adatbázis és/vagy Data Warehouse egy Azure-felhőben, amely feladatügynökként működik. A tárolók különböző SQL Database-kiszolgálókon, előfizetésekben és/vagy régiókban lehetnek. <br><br>A célcsoportok állhatnak egyedi adatbázisokból vagy adattárházakból, illetve egy kiszolgáló, készlet vagy szegmenstérkép összes adatbázisából (a feladat futásidejében dinamikusan számba véve). | Bármely egyes adatbázis ugyanabban az SQL Server-példányban, mint az SQL-ügynök. |
|Támogatott API-k és eszközök | Portál, PowerShell, T-SQL, Azure Resource Manager | T-SQL, SQL Server Management Studio (SSMS) |

## <a name="sql-agent-jobs"></a>SQL ügynök feladatok

Az SQL Agent-feladatok az adatbázissal szembeni T-SQL-parancsfájlok meghatározott sorozata. A feladatok segítségével olyan felügyeleti feladatokat definiálhat, amelyek egy vagy több alkalommal futtathatók, és amelyek sikeresek vagy sikertelenek.
Egy feladat egy helyi kiszolgálón vagy több távoli kiszolgálón is futtatható. Az SQL Agent-feladatok egy belső adatbázis-motor-összetevő, amely a felügyelt példány szolgáltatáson belül kerül végrehajtásra.
Az SQL Agent-feladatok számos kulcsfontosságú fogalma létezik:

- **Feladatlépések** egy vagy több lépésből áll, amelyeket a feladaton belül végre kell hajtani. Minden feladat lépés definiálhatja újrapróbálkozási stratégia és a művelet, amely nek meg kell történnie, ha a feladat lépés sikeres vagy sikertelen.
- **Az ütemezések határozzák** meg, hogy mikor kell végrehajtani a feladatot.
- **Az értesítések** lehetővé teszik olyan szabályok meghatározását, amelyek a feladat befejezése után e-mailben értesítik az üzemeltetőket.

### <a name="job-steps"></a>Feladat lépései

Az SQL Agent feladat lépései olyan műveletek sorozatai, amelyeket az SQL Agentnek végre kell hajtania. Minden lépés a következő lépés, amelyet végre kell hajtani, ha a lépés sikeres vagy sikertelen, újrapróbálkozások száma hiba esetén.
Az SQL Agent lehetővé teszi, hogy különböző típusú feladatlépéseket hozzon létre, például a Transact-SQL feladatlépést, amely egyetlen Transact-SQL köteget hajt végre az adatbázison, vagy az operációs rendszer parancs/PowerShell lépéseit, amelyek egyéni operációsrendszer-parancsfájlokat hajthatnak végre, az SSIS-feladat lépései lehetővé teszik az adatok betöltését az SSIS futásidejű használatával, vagy [olyan replikációs](sql-database-managed-instance-transactional-replication.md) lépéseket, amelyek az adatbázis módosításait más adatbázisokban is közzétehetik.

[A tranzakciós replikáció](sql-database-managed-instance-transactional-replication.md) egy adatbázismotor-szolgáltatás, amely lehetővé teszi, hogy egy adatbázis egy vagy több tábláján végrehajtott módosításokat közzétegye, és azokat közzétegye/elossza az előfizetői adatbázisok készletében. A módosítások közzététele a következő SQL-ügynök feladatlépés-típusok használatával valósítva meg:

- Tranzakciónapló-olvasó.
- Pillanatkép.
- Forgalmazó.

A feladatlépések egyéb típusai jelenleg nem támogatottak, például:

- A replikációs feladat egyesítése lépés nem támogatott.
- A várólista-olvasó nem támogatott.
- Az Analysis Services nem támogatott

### <a name="job-schedules"></a>Feladatütemezések

Az ütemezés határozza meg, hogy egy feladat mikor fut. Egynél több feladat futhat ugyanazon az ütemezésen, és ugyanarra a feladatra több ütemezés is vonatkozhat.
Az ütemezés a következő feltételeket határozhatja meg a feladat futásának idejére vonatkozóan:

- A példány újraindításakor (vagy az SQL Server Agent indításakor). A feladat minden feladatátvétel után aktiválódik.
- Egyszer, egy adott napon és időpontban, ami hasznos egy feladat késleltetett végrehajtásához.
- Ismétlődő ütemezés szerint.

> [!Note]
> A felügyelt példány jelenleg nem teszi lehetővé egy feladat indítását, ha a példány "tétlen".

### <a name="job-notifications"></a>Állásértesítések

AZ SQL Agent-feladatok lehetővé teszik, hogy értesítéseket kapjon, ha a feladat sikeresen befejeződik vagy sikertelen. Értesítéseket e-mailben kaphat.

Először be kell állítania az e-mail fiókot, amelyet az e-mail `AzureManagedInstance_dbmail_profile`értesítések küldéséhez fog használni, és hozzá kell rendelnie a fiókot a nevű e-mail profilhoz, amint az a következő mintában látható:

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

Az adatbázis-levelezést is engedélyeznie kell a felügyelt példányon:

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

Értesítheti az operátort, hogy valami történt az SQL Agent-feladatokkal. Az operátor egy vagy több felügyelt példány karbantartásáért felelős személy kapcsolattartási adatait határozza meg. Néha az operátori feladatok egy személyhez vannak rendelve.
A több felügyelt példányt vagy SQL-kiszolgálót tartalmazó rendszerekben sok személy megoszthatja az operátori felelősségeket. Az operátor nem tartalmaz biztonsági információkat, és nem határoz meg rendszerbiztonsági tag.

Operátorokat az SSMS vagy a Transact-SQL parancsfájl használatával hozhat létre a következő példában:

```sql
EXEC msdb.dbo.sp_add_operator
    @name=N'Mihajlo Pupun',
    @enabled=1,
    @email_address=N'mihajlo.pupin@contoso.com'
```

Bármely feladatot módosíthat, és olyan operátorokat rendelhet hozzá, akik e-mailben értesítést kapnak, ha a feladat befejeződik, sikertelen vagy sikeres az SSMS vagy a következő Transact-SQL parancsfájl használata:

```sql
EXEC msdb.dbo.sp_update_job @job_name=N'Load data using SSIS',
    @notify_level_email=3, -- Options are: 1 on succeed, 2 on failure, 3 on complete
    @notify_email_operator_name=N'Mihajlo Pupun'
```

### <a name="sql-agent-job-limitations"></a>SQL ügynökfeladat korlátai

Az SQL Server ben elérhető SQL Agent-szolgáltatások némelyikét a felügyelt példány nem támogatja:

- Az SQL-ügynök beállításai csak olvashatók. Az `sp_set_agent_properties` eljárás nem támogatott a felügyelt példányban.
- Az SQL-ügynök engedélyezése/letiltása jelenleg nem támogatott a felügyelt példányban. Az SQL-ügynök mindig fut.
- Az értesítések részben támogatottak
  - A személyhívó nem támogatott.
  - A NetSend nem támogatott.
  - A riasztások nem támogatottak.
- A proxyk nem támogatottak.
- Az eseménynapló nem támogatott.

Az SQL Server Agent szolgáltatásról az [SQL Server Agent című témakörben](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent)talál további információt.

## <a name="elastic-database-jobs-preview"></a>Rugalmas adatbázis-feladatok (előzetes verzió)

**Rugalmas adatbázis-feladatok** segítségével egy vagy több T-SQL-szkriptet párhuzamosan, nagy számú adatbázison, ütemezve vagy igény szerint futtathat.

**Adatbázisok bármilyen kombinációján futtathat feladatokat**: egy vagy több különálló adatbázison, egy kiszolgáló összes adatbázisán, egy rugalmas készletben található összes adatbázison vagy egy szegmenstérképen – ráadásul a további rugalmasság érdekében adott adatbázisokat is bevonhat vagy kizárhat. **A feladatok több kiszolgálón, több készleten, vagy akár különböző előfizetésekhez tartozó adatbázisokon is futtathatók.** A kiszolgálókat és készleteket a rendszer futásidőben dinamikusan veszi számba, ezért a feladatok az összes olyan adatbázison futnak, amelyek a célcsoportban a végrehajtáskor megtalálhatók.

Az alábbi képen egy feladatügynök látható, amely különböző típusú célcsoportokon hajt végre feladatokat:

![Rugalmas feladat ügynökének elméleti modellje](media/elastic-jobs-overview/conceptual-diagram.png)

### <a name="elastic-job-components"></a>Rugalmas feladat összetevői

|Összetevő | Leírás (további részletek a táblázat alatt találhatók) |
|---------|---------|
|[**Rugalmas feladat ügynöke**](#elastic-job-agent) | Az az Azure-erőforrás, amelyet a feladatok futtatására és kezelésére hoz létre. |
|[**Feladat-adatbázis**](#job-database) | A feladatügynök által a feladatadatok, a feladatdefiníciók és egyebek tárolására használt Azure SQL-adatbázis. |
|[**Célcsoport**](#target-group) | Azon kiszolgálók, készletek, adatbázisok és szegmenstérképek, amelyeken egy feladatot kíván futtatni. |
|[**Foglalkozása**](#job) | A feladat egy vagy több [munkalépésből](#job-step)álló munkaegység. A feladatlépések meghatározzák a futtatandó T-SQL-szkriptet, valamint a szkript végrehajtásához szükséges egyéb részleteket. |

#### <a name="elastic-job-agent"></a>Rugalmas feladat ügynöke

A rugalmasfeladat-ügynök a feladatok létrehozásához, futtatásához és kezeléséhez használt Azure-erőforrás. A rugalmasfeladat-ügynök egy Azure-erőforrás, amelyet a portálon hozhat létre (a [PowerShell](elastic-jobs-powershell.md) és a REST is támogatott).

**Rugalmasfeladat-ügynök** létrehozásához szükség van egy meglévő SQL-adatbázisra. Az ügynök [*feladat-adatbázisként*](#job-database) konfigurálja a meglévő adatbázist.

A rugalmasfeladat-ügynök használata ingyenes. A feladat-adatbázis számlázása ugyanolyan ütemben történik, mint bármely SQL-adatbázis.

#### <a name="job-database"></a>Feladat-adatbázis

A *feladat-adatbázis* feladatok meghatározására, valamint a feladat-végrehajtások állapotának és előzményeinek nyomon követésére szolgál. A *feladat-adatbázis* az ügynök metaadatainak, naplóinak, eredményeinek, feladatdefinícióinak tárolására is szolgál, valamint számos hasznos tárolt eljárást és más adatbázis-objektumot is tartalmaz a feladatok T-SQL használatával történő létrehozásához, futtatásához és kezeléséhez.

A jelenlegi előzetes verzióban egy meglévő (S0 vagy magasabb szintű) Azure SQL-adatbázis szükséges a rugalmasfeladat-ügynök létrehozásához.

A *feladat adatbázis* nem kell a szó szoros értelmében új, de legyen egy tiszta, üres, S0 vagy magasabb szolgáltatási cél. A *feladat-adatbázis* ajánlott szolgáltatási célja S1 vagy magasabb, de az optimális választás a feladat(ok) teljesítményigényeitől függ: a feladatlépések számától, a feladatcélok számától és a feladatok futtatásának gyakori számától. Például egy S0-adatbázis elegendő lehet egy feladatügynök, amely óránként néhány feladatot futtat, és kevesebb, mint tíz adatbázist céloz meg, de egy feladat percenként futtatása nem lehet elég gyors egy S0-adatbázis, és egy magasabb szolgáltatási szint jobb lehet.

Ha a feladat-adatbázison keresztüli műveletek a vártnál lassabbak, [figyelje](sql-database-monitor-tune-overview.md#sql-database-resource-monitoring) az adatbázis teljesítményét és az erőforrás-kihasználtságot a feladat-adatbázisban az Azure Portal vagy a [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) DMV lassúsági időszakokban. Ha egy erőforrás, például a CPU, az adatok IO vagy a log írási megközelítések 100%-os, és korrelál a lassúsági időszakok, fontolja meg az adatbázis növekményes skálázása magasabb szolgáltatási célok (akár a [DTU modell,](sql-database-service-tiers-dtu.md) vagy a [virtuálismag-modell)](sql-database-service-tiers-vcore.md)amíg a feladat-adatbázis teljesítménye megfelelően javul.

##### <a name="job-database-permissions"></a>Feladat-adatbázis engedélyei

Feladatügynök létrehozásakor a séma, a táblák és a *jobs_reader* nevű szerepkör a *feladat-adatbázisban* jön létre. A szerepkör a következő engedéllyel jön létre, és célja, hogy részletesebb hozzáférés-vezérlést biztosítson a rendszergazdák számára a feladatok monitorozásához:

|Szerepkörnév |'jobs' sémaengedélyek |'jobs_internal' sémaengedélyek |
|---------|---------|---------|
|**jobs_reader** | SELECT | None |

> [!IMPORTANT]
> Mielőtt adatbázis-rendszergazdaként hozzáférést biztosítana a *feladat-adatbázishoz*, fontolja meg a lehetséges biztonsági következményeket. A feladatok létrehozásához és szerkesztéséhez szükséges engedélyekkel rendelkező rosszindulatú felhasználó létrehozhat vagy szerkeszthet egy olyan feladatot, amely tárolt hitelesítő adattal csatlakozik a rosszindulatú felhasználó által vezérelt adatbázishoz, ezáltal a felhasználó meghatározhatja a hitelesítő adathoz tartozó jelszót.

#### <a name="target-group"></a>Célcsoport

A *célcsoport* határozza meg az azokat az adatbázisokat, amelyeket az adott feladatlépés végre lesz hajtva. A célcsoport tetszőleges számban és kombinációban tartalmazhatja a következőket:

- **SQL Database server** – ha meg van adva kiszolgáló, a feladat végrehajtásakor a kiszolgálón lévő összes adatbázis a csoport része. A master adatbázis hitelesítő adatait meg kell adni ahhoz, hogy a csoportot a rendszer a feladat végrehajtása előtt számba vegye és frissítse.
- **Rugalmas készlet** – Ha meg van adva egy rugalmas készlet, a feladat végrehajtásának időpontjában a rugalmas készletben található összes adatbázis a csoport tagja lesz. Kiszolgáló esetén a master adatbázis hitelesítő adatait meg kell adni ahhoz, hogy a csoport a feladat futtatása előtt frissíthető legyen.
- **Önálló adatbázis** – adjon meg egy vagy több egyedi adatbázist, amelyet a csoport részévé kíván tenni.
- **Szegmenstérkép** – egy szegmenstérkép adatbázisai.

> [!TIP]
> A feladat végrehajtásakor a *dinamikus számbavételezés* újra kiértékeli a célcsoportokban található adatbázisokat, amelyek kiszolgálókat és készletet is tartalmaznak. A dinamikus számbavételezéssel biztosítható, hogy **a feladatok a feladat végrehajtásakor a kiszolgálón vagy a készletben létező összes adatbázison fussanak**. Az adatbázisok listájának futásidőben történő ismételt kiértékelése különösen hasznos olyan esetekben, amikor a készlet- vagy a kiszolgálótagság gyakran változik.

A készletek és az önálló adatbázisok megadhatók úgy, hogy a csoport részét képezzék vagy ki legyenek zárva a csoportból. Ez lehetővé teszi, hogy az adatbázisok tetszőleges kombinációjából hozzon létre céladatbázist. Hozzáadhat például egy kiszolgálót egy céladatbázishoz, miközben kizárja egy rugalmas készlet egyes adatbázisait (vagy egy egész rugalmas készletet).

A célcsoportok több előfizetésből és több régióból származó adatbázisokat is tartalmazhatnak. Vegye figyelembe, hogy a régiók közötti végrehajtás magasabb késéssel jár, mint a régión belüli.

Az alábbi példákban az látható, hogyan történik a különböző célcsoport-meghatározások dinamikus számba vétele a feladat végrehajtásának pillanatában, a feladat által futtatandó adatbázisok megadásához:

![Példák célcsoportokra](media/elastic-jobs-overview/targetgroup-examples1.png)

Az **1. példában** olyan célcsoport szerepel, amely egyedi adatbázisok listáját tartalmazza. Ha egy feladatlépést e célcsoport használatával hajt végre, akkor a szóban forgó műveletet a rendszer az összes adatbázison elvégzi.<br>
A **2. példában** olyan célcsoport szerepel, amely célhelyként egy Azure SQL Server-kiszolgálót tartalmaz. Ha egy feladatlépést e célcsoport használatával hajt végre, akkor a kiszolgáló dinamikus számba vételével a rendszer képes a kiszolgálón aktuálisan megtalálható adatbázisok listájának összeállítására. A szóban forgó műveletet a rendszer az összes adatbázison elvégzi.<br>
A **3. példában** a *2. példában* lévőhöz hasonló célcsoport szerepel, amelyből viszont egy adott adatbázis ki van zárva. A szóban forgó műveletet a rendszer *nem* végzi el a kizárt adatbázison.<br>
A **4. példában** olyan célcsoport szerepel, amely célhelyként egy rugalmas készletet tartalmaz. A *2. példához* hasonlóan a feladat futtatása során a készlet dinamikus számba vételével a rendszer képes a készletben lévő adatbázisok listájának összeállítására.
<br><br>

![Példák célcsoportokra](media/elastic-jobs-overview/targetgroup-examples2.png)

**5. példa** és **a 6-os példa** speciális forgatókönyveket jelenít meg, ahol az Azure SQL-kiszolgálók, rugalmas készletek és adatbázisok kombinálhatók szabályok belefoglalásával és kizárásával.<br>
A **7. példa** azt mutatja be, hogy a feladat futtatása során a szilánkleképezésben szereplő szilánkok is kiértékelhetők.

> [!NOTE]
> Maga a feladat-adatbázis lehet egy feladat célja. Ebben az esetben a feladat adatbázis ugyanúgy kezeli, mint bármely más céladatbázis. A feladat felhasználókell létrehozni, és megfelelő engedélyeket a feladat adatbázisban, és az adatbázis hatóköre hitelesítő a feladat felhasználó is léteznie kell a Feladat adatbázisban, mint bármely más cél adatbázis.
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

- [Mi az SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent)
- [Rugalmas feladatok létrehozása és kezelése](elastic-jobs-overview.md)
- [Rugalmas feladatok létrehozása és kezelése a PowerShell használatával](elastic-jobs-powershell.md)
- [Rugalmas feladatok létrehozása és kezelése a Transact-SQL (T-SQL) használatával](elastic-jobs-tsql.md)
