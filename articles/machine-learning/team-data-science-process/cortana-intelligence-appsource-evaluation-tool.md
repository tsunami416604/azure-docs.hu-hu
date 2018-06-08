---
title: A Cortana Intelligence megoldás kiértékelési eszközével |} Microsoft Docs
description: A Microsoft Partner, lépései a következők minden közzétenni a Cortana Intelligence megoldás AppSource kövesse.
services: machine-learning
documentationcenter: ''
author: AnupamMicrosoft
manager: jhubbard
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2017
ms.author: anupams
ms.openlocfilehash: a94febdb89573930715006501b3690c0aa845b7b
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34836749"
---
# <a name="cortana-intelligence-solution-evaluation-tool"></a>Cortana Intelligence megoldásértékelési eszköz
## <a name="overview"></a>Áttekintés
A Cortana Intelligence megoldás kiértékelési eszköz segítségével felmérheti a speciális elemzési megoldások a Microsoft által ajánlott gyakorlati eljárásoknak megfelelő beállításában. A Microsoft a partnerekkel együttműködve az van érdeklődőbbek (ISV-k / SIs) kiváló minőségű megoldást jelentenek az ügyfelek, viszonteladók és végrehajtása. Ez az útmutató ismerteti a folyamatot, amely a megoldás a megoldás kiértékelési eszköz segítségével, és a keres az adott ajánlott eljárások ismertetik.

## <a name="getting-started"></a>Első lépések
Adjon [letöltése](https://aka.ms/aa-evaluation-tool-download) és a Cortana Intelligence megoldás kiértékelési eszközével telepítse.

Előfeltételek:
- Windows 10: [hivatalos webhely a Windows 10-hez](https://www.microsoft.com/en-us/windows)
- Az Azure Powershell: [telepítse és konfigurálja az Azure Powershellt](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.0.0).

## <a name="identifying-your-app"></a>Az alkalmazás azonosítása
Telepítés befejezése után nyissa meg az eszközt, és az első próbaverziójának megkezdéséhez.

![Nyissa meg kiértékelési eszközével](./media/cortana-intelligence-appsource-evaluation-tool/1-open-evaluation-tool.png)

Adja meg a megoldás azonosító információkat.

![Csatlakozás Azure-előfizetés](./media/cortana-intelligence-appsource-evaluation-tool/2-connect-azure-subscription.png)

Az Azure-előfizetéssel kapcsolódni, és adja meg az alkalmazást tartalmazó erőforráscsoportot.

![Erőforrások kiválasztása](./media/cortana-intelligence-appsource-evaluation-tool/3-select-resources.png)

Ha be van töltve az erőforráscsoportot, válassza ki az erőforrásokat, amelyek szerepelnek a megoldás, és azonosíthatja a kisegítő adatok erőforrásokat, vagyis a:
- Adatfeldolgozást
- Használat
- Belső

Ezen információk használatával jobb megértése, hogyan a megoldás használatával van a különböző összetevők és biztosításához felhasználók számára is elérhető összetevői megegyeznek az ajánlott eljárásoknak megfelelő beállításában.

### <a name="ingestion"></a>Adatfeldolgozást
Adatfeldolgozást ebben az esetben azt jelenti, hogy a egyetlen adatforrást sem való kívül a megoldás az adatok lekérésére használt, vagy kívül a megoldás olyan szolgáltatásokat használó történő azt.

### <a name="consumption"></a>Használat
Felhasználás ebben az esetben azt jelenti, hogy a bármely adatokat küldeni a végfelhasználók számára, közvetlenül vagy közvetve szolgáló adatkészleteket. Példa:
- A közvetlen lekérdezés a Power bi adatkészletekből.
- Az adathalmaz egy webalkalmazás kérdezhetők le.

>[!NOTE]
Válasszon ki egy adott erőforrás használata adatfeldolgozást és felhasználás esetén **fogyasztás**.

### <a name="internal"></a>Belső
Csak a belső alkalmazás feldolgozása használt adatok erőforrásokat belső használatos.

A következő kérni fogja az előző lépésben megadott adatbázisoknak érvényes hitelesítő adatok biztosítására:

![Set teszt Előfeltételek](./media/cortana-intelligence-appsource-evaluation-tool/4-set-test-prerequisites.png)

## <a name="solution-test-cases"></a>Megoldás vizsgálati esetek
A megoldás eszköz automatikus tesztek gyűjteménye a megoldás hajt végre.

![Teszt végrehajtásának](./media/cortana-intelligence-appsource-evaluation-tool/5-set-test-execution.png)

A tesztek befejeződése után a rendszer kéri, hogy adjon meg egy magyarázat vagy indoklását miért a megoldás nem felel meg a vonatkozó követelményeknek.

![Adja meg az üzleti indoklásának](./media/cortana-intelligence-appsource-evaluation-tool/6-provide-business-justification.png)

Például a megoldás az Azure SQL DW tesz közzé, ha az értékelés szükséges is közzéteheti a Azure Analysis Services. 

A megoldás IaaS virtuális gépeket futtató Sql Server Analysis Services Azure Analysis Services helyett használhat. Hiba a vizsgálat egy elfogadható okának lenne.
## <a name="packaging-your-evaluation-results"></a>Az értékelési eredmények-csomagban
A vizsgálati esetek befejezése után egy zip-fájlba exportálja az értékelés csomagot, és kérni fogja a kiértékelési eszközével meg. 

A vizsgálati eredmények zip-fájl megosztása a Microsofttal a megoldás AppSource hozzáadandó jóváhagyás kérése előtt értékelni szeretné

![Osztályú kiértékelési eszközével](./media/cortana-intelligence-appsource-evaluation-tool/7-grade-evaluation-tool.png)

Ez a szakasz fenti cikk ismerteti az eszköz különböző funkcióit, most ossza meg velünk tekintse át az eszköz kiértékelése bevált gyakorlatokat típusú.

## <a name="security-evaluation-considerations"></a>Biztonsági értékelési szempontok
### <a name="databases-should-use-azure-active-directory-authentication"></a>Adatbázisok Azure Active Directory hitelesítést kell használnia.
A sloution az Azure SQL- vagy Azure SQL DW erőforrásokat az Azure Active Directory (AAD) hitelesítéssel engedélyezni kell. Az összes identitások és szerepkörök kezelése egyetlen AAD itt.

| További információ | Ebben a cikkben találhat |
| --- | --- |
| SQL-adatbázis és az SQL Data Warehouse az aad-ben | [Az SQL Database vagy az SQL Data Warehouse hitelesítéshez használandó Azure Active Directory-hitelesítés](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) |
| Konfigurálhatja és kezelheti az aad-ben | [Konfigurálhatja és kezelheti az Azure Active Directory-hitelesítés az SQL Database vagy az SQL Data Warehouse](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure) |
| Az Azure webalkalmazás-hitelesítés | [Hitelesítési és engedélyezési az Azure App Service-ben](https://docs.microsoft.com/azure/app-service/app-service-authentication-overview) |
| Webalkalmazás konfigurálása az aad-ben | [Az App Service alkalmazás használhatja az Azure Active Directory bejelentkezési konfigurálása](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-active-directory-authentication)|

### <a name="datasets-accessible-to-end-users-should-support-role-based-access-control"></a>Végfelhasználók számára elérhető adatkészletek támogatnia kell a szerepköralapú hozzáférés-vezérlés
A kiértékelési eszközével végrehajtásakor fogja kérni adja meg a jelentésekben vagy erőforrások közzétételéhez. Feltételezzük, hogy ezeket az erőforrásokat szánt hozzáférés végfelhasználók, nem a fejlesztők által. Ezeket az erőforrásokat kell biztosítania kell szerepköralapú hozzáférés-vezérlést (RBAC) ahhoz, hogy, hogy, hogy a végfelhasználók csak hitelesített adatok eléréséhez.

Pontosabban a következő Azure-erőforrások bármelyike RBAC konfigurálható, és elfogadható számítanak:
- HDInsight biztonságos című [tartományhoz HDInsight-fürtökkel a Hadoop biztonsági bemutatása](https://docs.microsoft.com/azure/hdinsight/hdinsight-domain-joined-introduction)
- Az Azure SQL, lásd: [AAD-hitelesítés és az Azure SQL]( https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)
- Az Azure Analysis Services, lásd: [adatbázis-szerepkörök és a felhasználók kezelése az Azure Analysis Services](https://docs.microsoft.com/azure/analysis-services/analysis-services-database-users)
- Az SQL Data Warehouse (vegye figyelembe, hogy SQL DW nem támogatja a Szerepalapú, nem ajánlott a közvetlen végfelhasználói hozzáférése.)

Ha egy másik erőforrástípust, amely támogatja az RBAC használata esetén adja meg, amely a Teszteset indoklás.

### <a name="azure-data-lake-store-should-use-at-rest-encryption"></a>Azure Data Lake Store kell használnia, inaktív adatok titkosítása
Azure Data Lake Store-(ADLS-), inaktív adatok titkosítása ADLS-felügyelet alatt álló titkosítási kulcsok használatával alapértelmezés szerint támogatja. Az Azure Key Vault használatával titkosítási is beállítható.

ADLS-titkosítási beállításait, információ [Azure Data Lake Store-fiók létrehozása](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal#create-an-azure-data-lake-store-account).

### <a name="azure-sql-and-azure-sql-data-warehouse-should-use-encryption"></a>Az Azure SQL és az Azure SQL Data Warehouse használandó titkosítási
Az Azure SQL és Azure SQL DW is támogatja a transzparens adatok titkosítás (TDE), amely biztosítja az adatok és a naplófájlok valós idejű titkosításához és visszafejtéséhez.

| További információ | Ebben a cikkben találhat |
| --- | --- |
| Az átlátható adattitkosítás (TDE) | [Átlátható adattitkosítás](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) |
| Az Azure SQL Data Warehouse TDE | [SQL Data Warehouse Encrption TDE TSQL](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-encryption-tde-tsql) |
| Az Azure SQL TDE konfigurálása | [Az Azure SQL Database átlátható adattitkosítás](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database) |
| Mindig titkosítja az Azure SQL konfigurálása | [SQL-adatbázis mindig titkosítja az Azure Key Vault](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)|

Mellett TDE Azure SQL is támogat mindig titkosítja, egy új titkosítási technológiát, amely biztosítja az adatok titkosítása nem csak nyugalmi és adatátviteli ügyfél és kiszolgáló közötti, hanem adatainak közben során használja a kiszolgálón parancsok végrehajtása során.

### <a name="any-virtual-machines-must-be-deployed-from-the-azure-marketplace"></a>A virtuális gépek telepítenie kell az Azure piactérről
Ahhoz, hogy a konzisztens biztonsági szintű AppSource biztosít, kérjük bármely a Cortana Intelligence megoldás részeként telepített virtuális gépek hitelesített és közzé az Azure piactéren.

Az Azure piactéren elérhető rendszerkép aktuális listáját, látogasson el a [Microsoft Azure piactérről](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute).

A virtuálisgép-lemezkép közzététele az Azure piactér információkért lásd: [útmutató a virtuálisgép-lemezkép létrehozása az Azure piactéren](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation).

## <a name="scalability-evaluation-considerations"></a>Méretezhetőség értékelési kapcsolatos szempontok
### <a name="cortana-intelligence-solutions-should-include-a-scalable-big-data-platform"></a>A Cortana Intelligence megoldások tartalmaznia kell egy méretezhető nagy adatplatform
A Cortana Intelligence megoldásokat kell méretezhető nagyon nagy méretű. Az Azure Ez azt jelenti, tartalmazniuk kell a két Petabájtnyi méretű adatok platform egyikét:
- Azure Data Lake Store
- Azure SQL Data Warehouse

Ha a megoldás nem igényel támogatást ezen adatok mérete, vagy egy alternatív adatplatform használ, kérem, ez az a Teszteset indoklás.
### <a name="cortana-intelligence-solutions-should-include-dedicated-ingestion-data-environments"></a>A Cortana Intelligence megoldások dedikált adatfeldolgozást adatok környezetekben kell tartalmaznia.
A Cortana Intelligence megoldások általában közvetlenül az adatok beszúrása relációs adatforrások kerülendő. Ehelyett nyers adatokat kell tárolni egy strukturálatlan környezetet az idempotent Beszúrások frissítések bármely Azure Data Factory használatával relációs tárolja azokat.

További információ az Azure Data Factory, az adatok másolásának [oktatóanyag: hozzon létre egy folyamatot Visual Studio használatával, a másolási tevékenység](https://docs.microsoft.com/azure/data-factory/v1/data-factory-copy-activity-tutorial-using-visual-studio).

### <a name="azure-sql-data-warehouse-should-use-polybase-for-data-ingestion"></a>Az SQL Data Warehouse PolyBase adatfeldolgozást kell használnia
Az Azure SQL DW PolyBase jól skálázható, párhuzamos adatfeldolgozást támogatja. A PolyBase lehetővé teszi Azure SQL DW használatát vagy az Azure Blob Storage tárolóban, vagy az Azure Data Lake Store tárolt külső adatkészletek probléma lekérdezésekre. Ez a tömeges frissítése alternatív módszerek kiváló teljesítményt biztosít.

Ismerkedés a PolyBase és az Azure SQL DW, lásd: [adatok betöltése a PolyBase az SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-load-with-polybase).

A PolyBase és az Azure SQL DW ajánlott eljárásokra vonatkozó további információkért lásd: [útmutató az SQL Data Warehouse PolyBase használatával](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-polybase-guide).

## <a name="availability-evaluation-considerations"></a>Rendelkezésre állási értékelési kapcsolatos szempontok

### <a name="datasets-accessible-to-end-users-should-support-a-large-volume-of-concurrent-users"></a>A végfelhasználók számára elérhető adatkészletek támogatnia kell a nagy egyidejű felhasználók
A kiértékelési eszközével végrehajtásakor fogja kérni adja meg a jelentésekben vagy erőforrások közzétételéhez. Feltételezzük, hogy ezeket az erőforrásokat szánt hozzáférés végfelhasználók, nem a fejlesztők által. Ezeket az erőforrásokat támogatnia kell a közepes és nagy mennyiségű egyidejű felhasználók.

Azure SQL Data Warehouse kifejezetten, nem lehet az egyetlen adatforrás rendelkezésre a végfelhasználók számára. Ha az Azure SQL DW kiemelt felhasználók előírt erőforrásként, Azure Analysis Services kell elérhetővé válik a jellemző felhasználók számára.

Azure SQL DW feldolgozási korlátok kapcsolatos további információkért lásd: [egyidejűségi és munkaterhelés-kezelés az SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-concurrency).

Azure Analysis Services kapcsolatos további információkért lásd: [Analysis Services áttekintése](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview).

### <a name="azure-sql-resources-should-have-a-read-only-replica-for-failover"></a>Azure SQL-erőforrások feladatátvételhez csak olvasható replika kell rendelkeznie.
Az Azure SQL-adatbázisok másodlagos példányra georeplikáció támogatja. Ez a példány majd segítségével egy feladatátvétel-példányként adja meg a magas rendelkezésre állású alkalmazások.

Az Azure SQL-adatbázisok georeplikáció kapcsolatos további információkért lásd: [SQL adatbázis földrajzi régiók közötti replikáció áttekintése](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview).

Az Azure SQL georeplikáció konfigurálásával kapcsolatos útmutatásért lásd: [aktív georeplikáció konfigurálása az Azure SQL Database Transact-SQL](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-transact-sql).

### <a name="azure-sql-data-warehouse-should-have-geo-redundant-backups-enabled"></a>Az SQL Data Warehouse georedundáns biztonsági mentések engedélyezve kell rendelkeznie.
Az Azure SQL DW támogatja a georedundáns tárolás napi biztonsági mentések. A georeplikáció biztosítja, hogy visszaállíthassa az adatraktárban még akkor is, ha nem fér hozzá az elsődleges régióban tárolt pillanatképek helyzetekben. Ez a funkció alapértelmezés szerint be van kapcsolva, és nem lehet letiltani a Cortana Intelligence megoldások.

További információ az Azure SQL DW biztonsági mentés és visszaállítás talál [SQL Data Warehouse biztonsági mentések](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-backups).

### <a name="virtual-machines-should-be-configured-with-availability-sets"></a>Virtuális gépek rendelkezésre állási készletek kell konfigurálni
Az Azure virtuális gépek ahhoz, hogy a tervezett és nem tervezett karbantartási események gyakorolt hatásának minimalizálása érdekében a rendelkezésre állási készletek lehet beállítani.

Azure virtuális gép rendelkezésre állási kapcsolatos további információkért lásd: [a Windows Azure virtuális gépek rendelkezésre állásának kezelése](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability).

## <a name="other-evaluation-considerations"></a>Egyéb értékelési szempontok
### <a name="cortana-intelligence-apps-should-use-a-centralized-tool-for-data-orchestration"></a>A Cortana Intelligence alkalmazásokat kell használnia egy központosított eszköz adatok előkészítése
Egyetlen eszközzel kezelésére és adatmozgás és átalakítás ütemezése kritikus fontosságú adatok körül konzisztencia biztosítja. Biztosít egy tiszta logika körül újrapróbálkozási logika, a függőségi felügyeleti, a riasztás/naplózási, stb. Azt javasoljuk, hogy használatát [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/v1/data-factory-introduction) az adatok előkészítése az Azure-ban.

Ha egy eszköz nem Azure Data Factory adatok előkészítése az használ, írja le mely eszköz vagy a használt eszközök.
### <a name="azure-machine-learning-models-should-be-retrained-using-azure-data-factory"></a>Az Azure Machine Learning modellek kell retrained Azure Data Factory használatával
Az Azure Machine Learning (AzureML) biztosít a könnyen használható eszközök létrehozásának és telepítésének prediktív modellezési és gépi tanulási folyamatok. Azonban fontos, hogy az AzureML modellek üzemi környezetek nem egyetlen rögzített adatkészlet alapján, de ehelyett valós jelenségek shifting Dynamics alkalmazkodik.

Megőrzési webszolgáltatások AzureML létrehozásáról további információk: [Machine Learning-modellek szoftveres](https://docs.microsoft.com/azure/machine-learning/machine-learning-retrain-models-programmatically).

Azure Data Factory használatával modell betanítási folyamatának automatizálásával kapcsolatos további információkért lásd: [frissítése Azure Machine Learning modellek használata az Update-Erőforrástevékenység](https://docs.microsoft.com/azure//data-factory/v1/data-factory-azure-ml-update-resource-activity).

## <a name="existing-documentation"></a>Meglévő dokumentáció
[A Microsoft Azure hitelesített nő, a felhő üzleti](https://azure.microsoft.com/marketplace/programs/certified/)

[A Microsoft Azure Cortana Intellignece tanúsítva](https://azure.microsoft.com/marketplace/programs/certified/cortana/)

