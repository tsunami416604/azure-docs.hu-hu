---
title: A Azure Security Center kibocsátási megjegyzései
description: A Azure Security Center újdonságait és változásait ismertető leírás.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2020
ms.author: memildin
ms.openlocfilehash: 484a8c7c230863f230719ddaf4e98a6248512bcc
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/16/2020
ms.locfileid: "97560253"
---
# <a name="whats-new-in-azure-security-center"></a>A Azure Security Center újdonságai

Security Center aktívan működik, és folyamatosan fejleszti a fejlesztéseket. A legújabb fejleményekkel naprakészen tarthatja az oldalt, amely az új funkciókkal, hibajavításokkal és elavult funkciókkal kapcsolatos információkat tartalmaz.

Ez az oldal gyakran frissül, ezért gyakran érdemes újra felkeresni. 

Ha szeretne többet megtudni a Security Center hamarosan elérhető *tervezett* változásokról, tekintse meg [a Azure Security Center fontos jövőbeli változásait](upcoming-changes.md). 

> [!TIP]
> Ha hat hónapnál régebbi elemeket keres, az archívumban találhatja meg a [Azure Security Center újdonságait](release-notes-archive.md).


## <a name="december-2020"></a>2020. december

A decemberi frissítések a következők:

- [Általánosan elérhető az Azure Defender az SQL-kiszolgálókhoz a gépeken](#azure-defender-for-sql-servers-on-machines-is-generally-available)
- [Általánosan elérhető az Azure Defender for SQL-támogatás az Azure szinapszis Analytics dedikált SQL-készletéhez](#azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available)
- [A globális rendszergazdák mostantól bérlői szintű engedélyeket is biztosíthatnak](#global-administrators-can-now-grant-themselves-tenant-level-permissions)
- [Két új Azure Defender-csomag: az Azure Defender for DNS és az Azure Defender for Resource Manager (előzetes verzió)](#two-new-azure-defender-plans-azure-defender-for-dns-and-azure-defender-for-resource-manager-in-preview)
- [Új biztonsági riasztások oldal a Azure Portal (előzetes verzió)](#new-security-alerts-page-in-the-azure-portal-preview)

### <a name="azure-defender-for-sql-servers-on-machines-is-generally-available"></a>Általánosan elérhető az Azure Defender az SQL-kiszolgálókhoz a gépeken

Azure Security Center két Azure Defender-csomagot kínál az SQL Serverhez:

- Azure **Defender Azure SQL Database-kiszolgálókhoz** – védi az Azure-beli natív SQL-kiszolgálókat 
- **Azure Defender a gépeken futó SQL Server-kiszolgálókon** – a hibrid, többfelhős és helyszíni környezetekben az SQL-kiszolgálókon ugyanazok a védelem kiterjeszthető.

Ebben a közleményben az **Azure Defender for SQL** mostantól védi az adatbázisait és az adataikat, bárhol is legyenek.

Az SQL-hez készült Azure Defender biztonsági rések felmérésére szolgáló képességeket tartalmaz. A sebezhetőség-felmérési eszköz a következő speciális funkciókat tartalmazza:

- **Alapkonfiguráció** (új!) – a biztonsági rések vizsgálatának eredményét intelligensen finomíthatja azokon, amelyek valós biztonsági problémákat jelenthetnek. Miután létrehozta az alapkonfiguráció biztonsági állapotát, a sebezhetőség-felmérési eszköz csak az eredeti állapottól származó eltéréseket jelenti. Az alapkonfigurációnak megfelelő eredmények a következő vizsgálatoknak minősülnek. Ez lehetővé teszi, hogy Ön és az elemzők a fontos dolgokra összpontosítsanak.
- **Részletes teljesítményteszt-információk** , amelyek segítenek *megérteni* a felderített megállapításokat, és hogy miért kapcsolódnak az erőforrásokhoz.
- **Szervizelési parancsfájlok** , amelyek segítenek az azonosított kockázatok enyhítésében.

További információ [Az Azure Defender for SQL szolgáltatásról](defender-for-sql-introduction.md).


### <a name="azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available"></a>Általánosan elérhető az Azure Defender for SQL-támogatás az Azure szinapszis Analytics dedikált SQL-készletéhez

Az Azure szinapszis Analytics (korábbi nevén SQL DW) egy olyan elemzési szolgáltatás, amely egyesíti a vállalati adattárházat és a big data elemzéseket. A dedikált SQL-készletek az Azure szinapszis vállalati adattárház-szolgáltatásai. További információ: [Mi az az Azure szinapszis Analytics (korábban SQL DW)?](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md).

Az SQL-hez készült Azure Defender az alábbiakkal védi a dedikált SQL-készleteket:

- Komplex **veszélyforrások elleni védelem** a fenyegetések és a támadások észlelése érdekében 
- **Sebezhetőségi felmérési képességek** a biztonsági konfigurációs beállítások azonosításához és szervizeléséhez

Az Azure Defender for SQL az Azure szinapszis Analytics SQL-készletekhez való támogatását automatikusan hozzáadja az Azure SQL Databases csomaghoz Azure Security Center. A Azure Portal a szinapszis munkaterület lapján egy új "Azure Defender for SQL" lapot talál.

További információ [Az Azure Defender for SQL szolgáltatásról](defender-for-sql-introduction.md).


### <a name="global-administrators-can-now-grant-themselves-tenant-level-permissions"></a>A globális rendszergazdák mostantól bérlői szintű engedélyeket is biztosíthatnak

A **globális rendszergazda** Azure Active Directory szerepkörrel rendelkező felhasználó bérlői szintű felelősséggel rendelkezhet, de nem rendelkezik az Azure-engedélyekkel a szervezetre vonatkozó információk megtekintésére a Azure Security Centerban. 

A bérlői szintű engedélyek kiosztásához kövesse a [bérlői szintű engedélyek megadása saját magának](security-center-management-groups.md#grant-tenant-wide-permissions-to-yourself)című témakör utasításait.


### <a name="two-new-azure-defender-plans-azure-defender-for-dns-and-azure-defender-for-resource-manager-in-preview"></a>Két új Azure Defender-csomag: az Azure Defender for DNS és az Azure Defender for Resource Manager (előzetes verzió)

Az Azure-környezethez két új, felhőben natív, széles körű veszélyforrások elleni védelmi képességgel bővült.

Ezek az új védelem nagy mértékben fokozza rugalmasságát a veszélyforrások elleni támadásokkal szemben, és jelentősen növeli az Azure Defender által védett Azure-erőforrások számát.

- **Azure Defender for Resource Manager** – a szervezeten belül végrehajtott összes erőforrás-kezelési művelet automatikus figyelése. További információ:
    - [A Resource Managerhez készült Azure Defender bemutatása](defender-for-resource-manager-introduction.md)
    - [Válaszadás a Resource Managerhez készült Azure Defender-riasztásokra](defender-for-resource-manager-usage.md)
    - [Az Azure Defender for Resource Manager által biztosított riasztások listája](alerts-reference.md#alerts-resourcemanager)

- **Azure Defender DNS-ben** – folyamatosan figyeli az Azure-erőforrások összes DNS-lekérdezését. További információ:
    - [A DNS-hez készült Azure Defender bemutatása](defender-for-dns-introduction.md)
    - [Válaszadás a DNS-hez készült Azure Defender-riasztásokra](defender-for-dns-usage.md)
    - [Az Azure Defender által a DNS-hez biztosított riasztások listája](alerts-reference.md#alerts-dns)


### <a name="new-security-alerts-page-in-the-azure-portal-preview"></a>Új biztonsági riasztások oldal a Azure Portal (előzetes verzió)

A Azure Security Center biztonsági riasztások lapja az alábbiak biztosítására lett újratervezve:

- **Jobb osztályozási élmény a riasztásokhoz** – segít csökkenteni a riasztások fáradtságát, és könnyebben koncentrálni a legfontosabb fenyegetésekre, a lista tartalmaz testreszabható szűrőket és csoportosítási lehetőségeket.
- **További információk a riasztások listájában** – például a MITRE ATT&ACK-taktika
- A riasztások **létrehozásának gombja** – az Azure Defender képességeinek kiértékeléséhez és a riasztások konfigurációjának teszteléséhez (Siem-integráció, e-mail-értesítések és munkafolyamat-automatizálások esetén) létrehozhat minta-riasztásokat az összes Azure Defender-csomagból.
- Az **Azure Sentinel incidensekkel való összehangolása** – a két terméket használó ügyfelek esetében a váltás mostantól egyszerűbb, és könnyen megtanulható egy másik
- **Nagyobb teljesítmény** a nagyméretű riasztások listája esetén
- A riasztások listáján a **billentyűzetes Navigálás**
- **Riasztások az Azure Resource Graph-ból** – a riasztásokat lekérdezheti az Azure Resource Graph-ban, a Kusto API-t az összes erőforráshoz. Ez akkor is hasznos, ha saját riasztási irányítópultokat épít ki. [További információ az Azure Resource Graph-ról](../governance/resource-graph/index.yml).

Az új felület eléréséhez használja a "kipróbálás most" hivatkozást a biztonsági riasztások oldal tetején található szalagcímből.

:::image type="content" source="media/security-center-managing-and-responding-alerts/preview-alerts-experience-banner.png" alt-text="Az új előzetes riasztási élményre mutató hivatkozást tartalmazó szalagcím":::

A riasztások új felhasználói élményből való létrehozásával kapcsolatban lásd: [minta Azure Defender-riasztások](security-center-alert-validation.md#generate-sample-azure-defender-alerts)létrehozása.

## <a name="november-2020"></a>2020. november

A novemberi frissítések a következők:

- [29 előzetes javaslat hozzáadva az Azure biztonsági teljesítményteszt lefedettségének növeléséhez](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [NIST SP 800 171 R2 hozzáadva a Security Center szabályozási megfelelőségi irányítópulthoz](#nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard)
- [A javaslatok listája mostantól szűrőket is tartalmaz](#recommendations-list-now-includes-filters)
- [Továbbfejlesztett és bővített automatikus üzembe helyezési élmény](#auto-provisioning-experience-improved-and-expanded)
- [A biztonságos pontszám már elérhető a folyamatos exportálásban (előzetes verzió)](#secure-score-is-now-available-in-continuous-export-preview)
- ["A rendszerfrissítéseket telepíteni kell a gépekre" javaslat mostantól aljavaslatokat is tartalmaz](#system-updates-should-be-installed-on-your-machines-recommendation-now-includes-sub-recommendations)
- [A Azure Portal házirend-kezelés lapja megjeleníti az alapértelmezett házirend-hozzárendelések állapotát.](#policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments)

### <a name="29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>29 előzetes javaslat hozzáadva az Azure biztonsági teljesítményteszt lefedettségének növeléséhez

Az Azure biztonsági teljesítményteszt a Microsoft által létrehozott, Azure-specifikus irányelvek a biztonsági és megfelelőségi szabályzatok közös megfelelőségi keretrendszereken alapuló bevált eljárásaihoz. [További tudnivalók az Azure-biztonsági teljesítménytesztről](../security/benchmarks/introduction.md).

A jelen teljesítményteszt lefedettségének növeléséhez a következő 29 előzetes javaslat lett hozzáadva a Security Centerhoz.

Az előzetes verzióra vonatkozó javaslatok nem jelenítik meg az erőforrás állapotát, és nem tartoznak bele a biztonságos pontszám számításaiba. Ha lehetséges, javítsa őket, hogy ha az előzetes verzió időtartama lejár, a pontszáma is hozzájárul. További információ a javaslatokról a [Azure Security Centerban található javaslatok szervizelése](security-center-remediate-recommendations.md)című témakörben található.

| Biztonsági ellenőrzés                     | Új javaslatok                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Adatforgalom titkosítása              | -Az SSL-kapcsolat érvényesítése engedélyezve kell legyen a PostgreSQL-adatbázis-kiszolgálókhoz<br>– Engedélyezni kell az SSL-kapcsolat betartatását a MySQL adatbázis-kiszolgálókon<br>-A TLS-t frissíteni kell az API-alkalmazás legújabb verziójára<br>-A TLS-t frissíteni kell a Function alkalmazás legújabb verziójára<br>– A TLS-t a webalkalmazás legújabb verziójára kell frissíteni<br>-FTPS szükséges az API-alkalmazásban<br>– A FTPS kötelező megadni a Function alkalmazásban<br>– A FTPS kötelező megadni a webalkalmazásban                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Hozzáférés és engedélyek kezelése        | – A webalkalmazásoknak SSL-tanúsítványt kell igényelnie minden bejövő kérelemhez<br>-Felügyelt identitást kell használni az API-alkalmazásban<br>-Felügyelt identitást kell használni a Function alkalmazásban<br>-Felügyelt identitást kell használni a webalkalmazásban                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Jogosulatlan hálózati hozzáférés korlátozása | -A privát végpontot engedélyezni kell a PostgreSQL-kiszolgálókhoz<br>-A privát végpontot engedélyezni kell a MariaDB-kiszolgálókon<br>-A privát végpontot engedélyezni kell a MySQL-kiszolgálókon                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| Naplózás és naplózás engedélyezése          | – A App Services diagnosztikai naplóit engedélyezni kell                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| Ajánlott biztonsági eljárások megvalósítása    | -Azure Backup engedélyezni kell a virtuális gépeket<br>-A Geo-redundáns biztonsági mentést engedélyezni kell a Azure Database for MariaDB<br>-A Geo-redundáns biztonsági mentést engedélyezni kell a Azure Database for MySQL<br>-A Geo-redundáns biztonsági mentést engedélyezni kell a Azure Database for PostgreSQL<br>-A PHP-t frissíteni kell az API-alkalmazás legújabb verziójára<br>– A PHP-t a webalkalmazás legújabb verziójára kell frissíteni<br>– A Java-t az API-alkalmazás legújabb verziójára kell frissíteni<br>– A Java-t a Function app legújabb verziójára kell frissíteni<br>– A Java-t a webalkalmazás legújabb verziójára kell frissíteni<br>-A Pythont az API-alkalmazás legújabb verziójára kell frissíteni<br>-A Pythont a Function alkalmazás legújabb verziójára kell frissíteni<br>-A Pythont a webalkalmazás legújabb verziójára kell frissíteni<br>– Az SQL-kiszolgálók naplózási megőrzését legalább 90 napra kell beállítani |
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

Kapcsolódó hivatkozások:

- [További információ az Azure biztonsági teljesítménytesztről](../security/benchmarks/introduction.md)
- [További információ az Azure API apps szolgáltatásról](../app-service/app-service-web-tutorial-rest-api.md)
- [További információ az Azure Function apps szolgáltatásról](../azure-functions/functions-overview.md)
- [További információ az Azure Web Apps szolgáltatásról](../app-service/overview.md)
- [További információ a Azure Database for MariaDB](../mariadb/overview.md)
- [További információ a Azure Database for MySQL](../mysql/overview.md)
- [További információ a Azure Database for PostgreSQL](../postgresql/overview.md)


### <a name="nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard"></a>NIST SP 800 171 R2 hozzáadva a Security Center szabályozási megfelelőségi irányítópulthoz

A NIST SP 800-171 R2 standard mostantól beépített kezdeményezésként használható Azure Security Center szabályozási megfelelőségi irányítópultján való használatra. A vezérlők leképezéseit a [NIST SP 800-171 R2 szabályozási megfelelőség beépített kezdeményezésének részletes ismertetése](../governance/policy/samples/nist-sp-800-171-r2.md)ismerteti. 

A standard előfizetésekre való alkalmazásához és a megfelelőségi állapot folyamatos figyeléséhez használja a [szabványoknak a szabályozási megfelelőségi irányítópulton való testreszabásával](update-regulatory-compliance-packages.md)kapcsolatos utasításokat.

:::image type="content" source="media/release-notes/nist-sp-800-171-r2-standard.png" alt-text="A NIST SP 800 171 R2 Standard Security Center szabályozási megfelelőségi irányítópultján":::

További információ erről a megfelelőségi szabványról: [NIST SP 800-171 R2](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final).


### <a name="recommendations-list-now-includes-filters"></a>A javaslatok listája mostantól szűrőket is tartalmaz

Mostantól szűrheti a biztonsági javaslatok listáját a feltételek körének megfelelően. A következő példában a javaslatok listáját a rendszer szűrte a következő javaslatok megjelenítéséhez:

- **általánosan elérhetők** (azaz nem előzetes verzió)
- a **Storage-fiókok** esetében
- **gyors javítási** szervizelés támogatása

:::image type="content" source="media/release-notes/recommendations-filters.png" alt-text="A javaslatok listához tartozó szűrők":::


### <a name="auto-provisioning-experience-improved-and-expanded"></a>Továbbfejlesztett és bővített automatikus üzembe helyezési élmény

Az automatikus kiépítési funkció segít csökkenteni a felügyeleti terhelést azáltal, hogy telepíti a szükséges bővítményeket az új és a meglévő Azure-beli virtuális gépekre, így azok a Security Center védelmére is kihasználhatják őket. 

Ahogy Azure Security Center nő, több bővítmény lett kifejlesztve, és Security Center képes figyelni az erőforrástípusok nagyobb listáját. Az automatikus kiépítési eszközök mostantól kibővültek a további kiterjesztések és erőforrástípusok támogatásához a Azure Policy képességeinek kihasználásával.

Mostantól konfigurálhatja az automatikus kiépítés lehetőségeit:

- Log Analytics-ügynök
- Új A Kubernetes Azure Policy bővítménye
- Új Microsoft függőségi ügynök

További információ: [Azure Security Centerból származó automatikus kiépítési ügynökök és bővítmények](security-center-enable-data-collection.md).


### <a name="secure-score-is-now-available-in-continuous-export-preview"></a>A biztonságos pontszám már elérhető a folyamatos exportálásban (előzetes verzió)

A biztonságos pontszám folyamatos exportálásával valós időben továbbíthatja a pontszám változásait az Azure Event Hubs vagy egy Log Analytics-munkaterületre. Ezt a képességet a következő célra használhatja:

- a biztonságos pontszám időbeli követése dinamikus jelentésekkel
- biztonságos pontszám-adatértékek exportálása az Azure Sentinelbe (vagy bármely más SIEM)
- integrálja ezeket az összes olyan folyamattal, amelyet esetleg már használ a biztonságos pontszám figyeléséhez a szervezetében

További információ a [Security Center adatainak folyamatos exportálásáról](continuous-export.md).


### <a name="system-updates-should-be-installed-on-your-machines-recommendation-now-includes-sub-recommendations"></a>"A rendszerfrissítéseket telepíteni kell a gépekre" javaslat mostantól aljavaslatokat is tartalmaz

A **rendszerfrissítéseket telepíteni kell a Machines** javaslatra. Az új verzió az egyes hiányzó frissítésekre vonatkozó aljavaslatokat tartalmaz, és a következő újításokat tartalmazza:

- Egy újratervezett élmény a Azure Portal Azure Security Center lapjain. A **rendszerfrissítésekre** vonatkozó ajánlás részletei lapon telepíteni kell a számítógépeken az alább látható megállapításokat tartalmazó listát. Egyetlen keresés kiválasztásakor megnyílik a részletek ablaktábla a Szervizelési információkra mutató hivatkozással és az érintett erőforrások listájával.

    :::image type="content" source="./media/upcoming-changes/system-updates-should-be-installed-subassessment.png" alt-text="A frissített javaslathoz tartozó alárendelt javaslatok egyikének megnyitása a portálon":::

- Dúsított adatok az Azure Resource Graph (ARG) javaslatához. Az ARG egy olyan Azure-szolgáltatás, amely hatékony erőforrás-feltárást tesz lehetővé. Az ARG használatával nagy léptékű lekérdezéseket végezhet az adott előfizetések között, így hatékonyan szabályozhatja a környezetét. 

    Azure Security Center esetében az ARG és a [Kusto lekérdezési nyelv (KQL)](/azure/data-explorer/kusto/query/) használatával kérdezheti le a biztonsági testhelyzetek széles körét.

    Korábban, ha a jelen ajánlást az ARG-ben kérdezte le, az egyetlen elérhető információ az volt, hogy a javaslatot szervizelni kell egy gépen. A továbbfejlesztett verzió következő lekérdezése visszaküldi a hiányzó rendszerfrissítéseket a számítógép szerint csoportosítva.

    ```kusto
    securityresources
    | where type =~ "microsoft.security/assessments/subassessments"
    | where extract(@"(?i)providers/Microsoft.Security/assessments/([^/]*)", 1, id) == "4ab6e3c5-74dd-8b35-9ab9-f61b30875b27"
    | where properties.status.code == "Unhealthy"
    ```

### <a name="policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments"></a>A Azure Portal házirend-kezelés lapja megjeleníti az alapértelmezett házirend-hozzárendelések állapotát.

Most már megtekintheti, hogy az előfizetések rendelkeznek-e az alapértelmezett Security Center házirend hozzárendelésével a Azure Portal Security Center **biztonsági házirend** lapján.

:::image type="content" source="media/release-notes/policy-assignment-info-per-subscription.png" alt-text="Az alapértelmezett szabályzat-hozzárendeléseket megjelenítő Azure Security Center házirend-kezelési lapja":::

## <a name="october-2020"></a>2020. október

Az októberi frissítések a következők:
- [Biztonsági rések felmérése helyszíni és többfelhős gépekhez (előzetes verzió)](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview)
- [Azure Firewall javaslat hozzáadva (előzetes verzió)](#azure-firewall-recommendation-added-preview)
- [Az illetékes IP-tartományokat a gyors javítással frissített Kubernetes Services-javaslatban kell meghatározni.](#authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix)
- [A szabályozási megfelelőségi irányítópult mostantól tartalmazza a szabványok eltávolításának lehetőségét](#regulatory-compliance-dashboard-now-includes-option-to-remove-standards)
- [Microsoft. Security/securityStatuses tábla eltávolítva az Azure Resource Graphből (ARG)](#microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview"></a>Biztonsági rések felmérése helyszíni és többfelhős gépekhez (előzetes verzió)

[Az Azure Defender for Servers](defender-for-servers-introduction.md)beépített sebezhetőség-felmérési ellenőrzőeszköze (Qualys-alapú) mostantól megvizsgálja az Azure arc-kompatibilis kiszolgálókat.

Ha engedélyezte az Azure arc használatát a nem Azure-beli gépeken, Security Center az integrált biztonsági rések képolvasó üzembe helyezését teszi lehetővé manuálisan és méretezéssel.

Ezzel a frissítéssel kihasználhatja az **Azure Defender-kiszolgálók** erejét, hogy megszilárdítsa a sebezhetőségi kezelési programot az összes Azure-beli és nem Azure-beli eszközön.

Főbb képességek:

- A VA (sebezhetőségi felmérés) képolvasó üzembe helyezési állapotának figyelése az Azure arc-gépeken
- Az integrált VA-ügynök kiépítés a nem védett Windows-és Linux Azure-alapú arc-gépekre (manuálisan és nagy méretekben)
- Észlelt biztonsági rések fogadása és elemzése az üzembe helyezett ügynököktől (manuálisan és nagy méretben)
- Az Azure-beli virtuális gépek és az Azure arc-számítógépek egységes felhasználói felülete

[További információ az integrált sebezhetőségi képolvasó hibrid gépekre való üzembe helyezéséről](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines).

[További információ az Azure arc használatára képes kiszolgálókról](../azure-arc/servers/index.yml).


### <a name="azure-firewall-recommendation-added-preview"></a>Azure Firewall javaslat hozzáadva (előzetes verzió)

Új javaslat lett hozzáadva az összes virtuális hálózat Azure Firewall-mel való ellátásához.

Javasoljuk, hogy a **virtuális hálózatokat a Azure Firewall védelemmel** lássa el, hogy korlátozza a hozzáférést a virtuális hálózatokhoz, és megakadályozza a potenciális fenyegetéseket Azure Firewall használatával.

További információ a [Azure Firewallról](https://azure.microsoft.com/services/azure-firewall/).


### <a name="authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix"></a>Az illetékes IP-tartományokat a gyors javítással frissített Kubernetes Services-javaslatban kell meghatározni.

Az ajánlott **IP-címtartományok meghatározása a Kubernetes Services szolgáltatásban** most már rendelkezik egy gyors javítási lehetőséggel.

További információ erről a javaslatról és az összes többi Security Center javaslatról: [biztonsági javaslatok – útmutató](recommendations-reference.md).

:::image type="content" source="./media/release-notes/authorized-ip-ranges-recommendation.png" alt-text="A hitelesítő IP-tartományokat a Kubernetes Services javaslatban kell meghatározni a gyors javítás lehetőséggel":::


### <a name="regulatory-compliance-dashboard-now-includes-option-to-remove-standards"></a>A szabályozási megfelelőségi irányítópult mostantól tartalmazza a szabványok eltávolításának lehetőségét

A Security Center szabályozási megfelelőségi irányítópultja betekintést nyújt a megfelelőségi állapotba, és megfelel a megfelelőségi ellenőrzéseknek és követelményeknek.

Az irányítópult a szabályozási szabványok alapértelmezett készletét tartalmazza. Ha a megadott szabványok bármelyike nem felel meg a szervezete számára, most egy egyszerű folyamat, amellyel egyszerűen eltávolíthatja őket a felhasználói felületről egy előfizetéshez. A szabványokat csak az *előfizetés* szintjén lehet eltávolítani; a felügyeleti csoport hatóköre nem.

További információ: [standard eltávolítása az irányítópultról](update-regulatory-compliance-packages.md#removing-a-standard-from-your-dashboard).


### <a name="microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg"></a>Microsoft. Security/securityStatuses tábla eltávolítva az Azure Resource Graphből (ARG)

Az Azure Resource Graph egy Azure-szolgáltatás, amelynek célja, hogy hatékony erőforrás-feltárást biztosítson, amely lehetővé teszi az adott előfizetések skálájának lekérdezését, így hatékonyan szabályozhatja a környezetét. 

Azure Security Center esetében az ARG és a [Kusto lekérdezési nyelv (KQL)](/azure/data-explorer/kusto/query/) használatával kérdezheti le a biztonsági testhelyzetek széles körét. Például:

- Eszközök leltározása (ARG)
- Dokumentáltak egy példa ARG-lekérdezést a [fiókok azonosításához a többtényezős hitelesítés (MFA) engedélyezése nélkül](security-center-identity-access.md#identify-accounts-without-multi-factor-authentication-mfa-enabled)

Az ARG-n belül a lekérdezésekben használható adattáblák találhatók.

:::image type="content" source="./media/release-notes/azure-resource-graph-tables.png" alt-text="Az Azure Resource Graph Explorer és a rendelkezésre álló táblák":::

> [!TIP]
> Az ARG dokumentációja felsorolja az összes elérhető táblázatot az [Azure Resource Graph-táblában és az erőforrástípus-referenciában](../governance/resource-graph/reference/supported-tables-resources.md).

Ebből a frissítésből a **Microsoft. Security/securityStatuses** tábla el lett távolítva. A securityStatuses API továbbra is elérhető.

Az adatcsere a Microsoft. Security/Assessments tábla használatával végezhető el.

A Microsoft. Security/securityStatuses és a Microsoft. Security/Assessments között a legfontosabb különbség az, hogy míg az első az értékelések összesítését mutatja, a másodpercek mindegyike egyetlen rekorddal rendelkezik.

Például a Microsoft. Security/securityStatuses egy olyan eredményt ad vissza, amely két policyAssessments tömbjét eredményezi:

```
{
id: "/subscriptions/449bcidd-3470-4804-ab56-2752595 felab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/securityStatuses/mico-rg-vnet",
name: "mico-rg-vnet",
type: "Microsoft.Security/securityStatuses",
properties:  {
    policyAssessments: [
        {assessmentKey: "e3deicce-f4dd-3b34-e496-8b5381bazd7e", category: "Networking", policyName: "Azure DDOS Protection Standard should be enabled",...},
        {assessmentKey: "sefac66a-1ec5-b063-a824-eb28671dc527", category: "Compute", policyName: "",...}
    ],
    securitystateByCategory: [{category: "Networking", securityState: "None" }, {category: "Compute",...],
    name: "GenericResourceHealthProperties",
    type: "VirtualNetwork",
    securitystate: "High"
}
```
Míg a Microsoft. Security/Assessments egy rekordot tart fenn az egyes házirend-értékelésekhez a következőképpen:

```
{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft. Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/e3delcce-f4dd-3b34-e496-8b5381ba2d70",
name: "e3deicce-f4dd-3b34-e496-8b5381ba2d70",
properties:  {
    resourceDetails: {Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet"...},
    displayName: "Azure DDOS Protection Standard should be enabled",
    status: (code: "NotApplicable", cause: "VnetHasNOAppGateways", description: "There are no Application Gateway resources attached to this Virtual Network"...}
}

{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/80fac66a-1ec5-be63-a824-eb28671dc527",
name: "8efac66a-1ec5-be63-a824-eb28671dc527",
properties: {
    resourceDetails: (Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet"...),
    displayName: "Audit diagnostic setting",
    status:  {code: "Unhealthy"}
}
```

**Példa egy meglévő ARG-lekérdezés securityStatuses használatával történő átalakítására az értékelés táblázatának használatával:**

SecurityStatuses hivatkozó lekérdezés:

```kusto
SecurityResources 
| where type == 'microsoft.security/securitystatuses' and properties.type == 'virtualMachine'
| where name in ({vmnames}) 
| project name, resourceGroup, policyAssesments = properties.policyAssessments, resourceRegion = location, id, resourceDetails = properties.resourceDetails
```

Az értékelések táblázatának helyettesítő lekérdezése:

```kusto
securityresources
| where type == "microsoft.security/assessments" and id contains "virtualMachine"
| extend resourceName = extract(@"(?i)/([^/]*)/providers/Microsoft.Security/assessments", 1, id)
| extend source = tostring(properties.resourceDetails.Source)
| extend resourceId = trim(" ", tolower(tostring(case(source =~ "azure", properties.resourceDetails.Id,
source =~ "aws", properties.additionalData.AzureResourceId,
source =~ "gcp", properties.additionalData.AzureResourceId,
extract("^(.+)/providers/Microsoft.Security/assessments/.+$",1,id)))))
| extend resourceGroup = tolower(tostring(split(resourceId, "/")[4]))
| where resourceName in ({vmnames}) 
| project resourceName, resourceGroup, resourceRegion = location, id, resourceDetails = properties.additionalData
```

További információt a következő hivatkozásokon talál:
- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md)
- [Kusto lekérdezési nyelv (KQL)](/azure/data-explorer/kusto/query/)


## <a name="september-2020"></a>2020. szeptember

A szeptemberben elérhető frissítések a következők:
- [Security Center új arculatot kap!](#security-center-gets-a-new-look)
- [Az Azure Defender megjelent](#azure-defender-released)
- [Általánosan elérhető az Azure Defender for Key Vault](#azure-defender-for-key-vault-is-generally-available)
- [Általánosan elérhető az Azure Defender a fájlok és ADLS Gen2 tárolásának védelméhez](#azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available)
- [Az Asset Inventory Tools mostantól általánosan elérhető](#asset-inventory-tools-are-now-generally-available)
- [A tároló-beállításjegyzék és a virtuális gépek vizsgálatára szolgáló adott sebezhetőség letiltása](#disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines)
- [Erőforrás kivonása egy javaslatból](#exempt-a-resource-from-a-recommendation)
- [A Security Center AWS-és GCP-összekötők több felhős élményt nyújtanak](#aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience)
- [Kubernetes munkaterhelés-védelmi javaslat csomag](#kubernetes-workload-protection-recommendation-bundle)
- [A sebezhetőségi felmérés eredményei mostantól folyamatos exportálással érhetők el](#vulnerability-assessment-findings-are-now-available-in-continuous-export)
- [Biztonsági konfigurációs problémák megakadályozása az új erőforrások létrehozásakor feltett javaslatok végrehajtásakor](#prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources)
- [A hálózati biztonsági csoport javaslatai javultak](#network-security-group-recommendations-improved)
- [Elavult előnézeti AK-javaslat: "Pod biztonsági szabályzatokat kell meghatározni a Kubernetes-szolgáltatásokban"](#deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services)
- [Azure Security Center továbbfejlesztett e-mail-értesítések](#email-notifications-from-azure-security-center-improved)
- [A biztonságos pontszám nem tartalmazza az előzetes verzióra vonatkozó javaslatokat](#secure-score-doesnt-include-preview-recommendations)
- [A javaslatok mostantól a súlyossági mutatót és a frissességi időközt is tartalmazzák](#recommendations-now-include-a-severity-indicator-and-the-freshness-interval)


### <a name="security-center-gets-a-new-look"></a>Security Center új arculatot kap!

Megjelent egy frissített felhasználói felület a Security Center portál oldalain. Az új lapok közé tartozik egy új áttekintő oldal, valamint a biztonságos pontszámok, az eszközök leltározása és az Azure Defender irányítópultok.

Az átalakított áttekintő oldal mostantól tartalmaz egy csempét a biztonságos pontszám, a tárgyieszköz-leltár és az Azure Defender-irányítópultok eléréséhez. Emellett egy csempe is kapcsolódik a szabályozási megfelelőségi irányítópulthoz.

További információ az [áttekintő oldalról](overview-page.md).


### <a name="azure-defender-released"></a>Az Azure Defender megjelent

Az **Azure Defender** a felhőalapú munkaterhelés-védelmi platform (CWPP), amely a fejlett, intelligens, Azure-és hibrid számítási feladatok védelméhez Security Centeron belül integrált. Lecseréli Security Center Standard díjszabási csomagját. 

Amikor engedélyezi az Azure Defender számára a Azure Security Center **díjszabási és beállítási** területét, a következő Defender-csomagok egyidejűleg engedélyezve vannak, és átfogó védelmet biztosítanak a környezet számítási, adatés szolgáltatási rétegei számára:

- [Azure Defender kiszolgálókhoz](defender-for-servers-introduction.md)
- [Azure Defender App Service-hez](defender-for-app-service-introduction.md)
- [Azure Defender tároláshoz](defender-for-storage-introduction.md)
- [Azure Defender SQL-hez](defender-for-sql-introduction.md)
- [Azure Defender Key Vaulthoz](defender-for-key-vault-introduction.md)
- [Azure Defender Kuberneteshez](defender-for-kubernetes-introduction.md)
- [Azure Defender tárolóregisztrációs adatbázisokhoz](defender-for-container-registries-introduction.md)

Ezeket a csomagokat a Security Center dokumentációjában külön ismertetjük.

A dedikált irányítópulttal az Azure Defender biztonsági riasztásokat és komplex veszélyforrások elleni védelmet biztosít a Virtual Machines, az SQL Database, a containers, a web Applications, a Network és más rendszerekhez.

[További információ az Azure Defenderről](azure-defender.md)

### <a name="azure-defender-for-key-vault-is-generally-available"></a>Általánosan elérhető az Azure Defender for Key Vault

A Azure Key Vault egy felhőalapú szolgáltatás, amely védelmet biztosít a titkosítási kulcsok és a titkok, például a tanúsítványok, a kapcsolatok karakterláncai és a jelszavak számára. 

Az **Azure Defender for Key Vault** biztosítja az Azure-natív, komplex veszélyforrások elleni védelmet a Azure Key Vault számára, amely további biztonsági intelligenciát biztosít. A bővítmények által Key Vault Azure Defender a Key Vault-fiókoktól függő számos erőforrás védelmét is védi.

A választható csomag már a GA. Ez a szolgáltatás előzetes verzióként érhető el, mivel "komplex veszélyforrások elleni védelem Azure Key Vault".

Emellett a Azure Portal Key Vault lapjain szerepel egy dedikált **biztonsági** oldal is **Security Center** javaslatok és riasztások számára.

További információ: [Key Vault Azure Defender](defender-for-key-vault-introduction.md).


### <a name="azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available"></a>Általánosan elérhető az Azure Defender a fájlok és ADLS Gen2 tárolásának védelméhez 

Az **Azure Defender for Storage** észleli az Azure Storage-fiókokban potenciálisan ártalmas tevékenységeket. Az Ön adatai védetté tehetik, hogy blob-tárolóként, fájlmegosztásként vagy adattavakként vannak tárolva.

A [Azure Files](../storage/files/storage-files-introduction.md) és [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) támogatása már általánosan elérhető.

Október 1-től 2020-én megkezdjük a szolgáltatások erőforrásainak védelmét.

További információ az [Azure Defender for Storage szolgáltatásban](defender-for-storage-introduction.md).


### <a name="asset-inventory-tools-are-now-generally-available"></a>Az Asset Inventory Tools mostantól általánosan elérhető

Azure Security Center eszközök leltározási lapja egyetlen oldalt biztosít a Security Centerhoz csatlakoztatott erőforrások biztonsági állapotának megtekintéséhez.

Security Center rendszeresen elemzi az Azure-erőforrások biztonsági állapotát az esetleges biztonsági rések azonosítása érdekében. Ezután javaslatokat tesz a biztonsági rések megoldására.

Ha bármilyen erőforráshoz függőben lévő javaslatok vannak, akkor azok megjelennek a leltárban.

További információ: [erőforrások felderítése és kezelése az eszközök leltározásával és felügyeleti eszközeivel](asset-inventory.md).



### <a name="disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines"></a>A tároló-beállításjegyzék és a virtuális gépek vizsgálatára szolgáló adott sebezhetőség letiltása

Az Azure Defender biztonsági réseket tartalmazó képolvasók a Azure Container Registry és a virtuális gépek rendszerképeinek vizsgálatára is lehetőséget biztosítanak.

Ha a szervezetnek figyelmen kívül kell hagynia egy megállapítást, és nem javítja azt, akkor letilthatja. A letiltott eredmények nem befolyásolják a biztonságos pontszámot, vagy nem eredményeznek nemkívánatos zajt.

Ha a keresés megfelel a letiltási szabályokban definiált feltételeknek, az nem jelenik meg az eredmények listájában.

Ez a lehetőség a javaslatok részleteinek oldalain érhető el:

- **A Azure Container Registry lemezképek biztonsági réseit szervizelni kell**
- **A virtuális gépek biztonsági réseit szervizelni kell**

További információ a [tároló-lemezképek konkrét megállapításainak letiltásával](defender-for-container-registries-usage.md#disable-specific-findings-preview) és [a virtuális gépek konkrét megállapításainak letiltásával](remediate-vulnerability-findings-vm.md#disable-specific-findings-preview)kapcsolatban.


### <a name="exempt-a-resource-from-a-recommendation"></a>Erőforrás kivonása egy javaslatból

Időnként előfordulhat, hogy egy erőforrás nem megfelelőként jelenik meg egy konkrét javaslatra vonatkozóan (és így csökkenti a biztonságos pontszámát), még akkor is, ha úgy érzi, hogy nem lenne. Lehetséges, hogy a Security Center által nem követett folyamat szervizelte. Vagy lehet, hogy a szervezete úgy döntött, hogy elfogadja az adott erőforrás kockázatát. 

Ilyen esetekben létrehozhat egy kivételi szabályt, és gondoskodhat arról, hogy az erőforrás ne szerepeljen a nem megfelelő állapotú erőforrások között a jövőben. Ezek a szabályok az alább leírtak szerint dokumentált indoklásokat is tartalmazhatnak.

További információ: az erőforrások kizárása a [javaslatokból és a biztonságos pontszámból](exempt-resource.md).


### <a name="aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience"></a>A Security Center AWS-és GCP-összekötők több felhős élményt nyújtanak

A Felhőbeli számítási feladatok gyakran több felhőalapú platformra is kiterjednek, a Cloud Security servicesnek ugyanezt kell tennie.

A Azure Security Center mostantól védi a munkaterheléseket az Azure-ban, az Amazon Web Services (AWS) és a Google Cloud Platformban (GCP).

Az AWS-és GCP-fiókok Security Centerba való bevezetésével integrálható az AWS biztonsági központ, a GCP biztonsági parancs és a Azure Security Center. 

További információt az [AWS-fiókok Összekapcsolásával Azure Security Center](quickstart-onboard-aws.md) és [a GCP-fiókok Azure Security Centerhoz való összekapcsolásával](quickstart-onboard-gcp.md)kapcsolatban itt olvashat.


### <a name="kubernetes-workload-protection-recommendation-bundle"></a>Kubernetes munkaterhelés-védelmi javaslat csomag

Annak biztosítása érdekében, hogy a Kubernetes-munkaterhelések alapértelmezés szerint biztonságosak legyenek, Security Center Kubernetes-szint megerősítő javaslatokat ad hozzá, beleértve a Kubernetes-belépésvezérlés használatával történő kényszerítési lehetőségeket is.

Ha telepítette a Kubernetes Azure Policy bővítményét az AK-fürtön, a Kubernetes API-kiszolgálónak küldött összes kérést a rendszer a fürtön megőrzött ajánlott eljárások alapján figyeli. Ezután konfigurálhatja az ajánlott eljárások betartatását és a jövőbeli munkaterhelések megadását.

Megadhatja például, hogy az emelt szintű tárolók ne legyenek létrehozva, és minden jövőbeli kérelem le lesz tiltva.

További információ a [munkaterhelések elleni védelemben – ajánlott eljárások a Kubernetes belépésvezérlés használatával](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control).


### <a name="vulnerability-assessment-findings-are-now-available-in-continuous-export"></a>A sebezhetőségi felmérés eredményei mostantól folyamatos exportálással érhetők el

A folyamatos exportálással valós időben továbbíthatja a riasztásokat és a javaslatokat az Azure Event Hubsba, Log Analytics munkaterületekre vagy Azure Monitorokra. Innen az adatok integrálása SIEM-ekkel (például Azure Sentinel, Power BI, Azure Adatkezelő stb.) végezhető el.

Security Center integrált sebezhetőségi felmérési eszközei a "szülő" javaslaton belül, például "a virtuális gépek biztonsági rései" javításával kapcsolatos ajánlott ajánlásokat adnak vissza az erőforrásokra vonatkozóan. 

A biztonsági megállapítások mostantól a folyamatos exportálással is elérhetők, amikor kiválasztja az ajánlások lehetőséget, és engedélyezi a **biztonsági megállapítások belefoglalása** lehetőséget.

:::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="Biztonsági megállapítások bekapcsolása a folyamatos exportálási konfigurációban" :::

Kapcsolódó lapok:

- [Security Center az Azure Virtual Machines szolgáltatáshoz készült integrált sebezhetőségi felmérési megoldás](deploy-vulnerability-assessment-vm.md)
- [Security Center integrált sebezhetőségi felmérési megoldás Azure Container Registry rendszerképekhez](defender-for-container-registries-usage.md)
- [Folyamatos exportálás](continuous-export.md)

### <a name="prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources"></a>Biztonsági konfigurációs problémák megakadályozása az új erőforrások létrehozásakor feltett javaslatok végrehajtásakor

A biztonsági incidensek jelentős oka a biztonság. Security Center mostantól lehetővé teszi az új erőforrások helytelen konfigurációjának *megelőzését* az adott javaslatok tekintetében. 

Ez a funkció segít megőrizni a számítási feladatokat, és stabilizálni tudja a biztonságos pontszámot.

A biztonságos konfiguráció egy adott javaslat alapján történő érvényesítése két üzemmódban érhető el:

- A Azure Policy **megtagadásának** hatására leállíthatja a nem megfelelő állapotú erőforrásokat

- A **kikényszerítés** lehetőség használatával kihasználhatja az Azure-szabályzat **DeployIfNotExist** hatását, és a létrehozáskor automatikusan elháríthatja a nem megfelelő erőforrásokat.
 
Ez a kiválasztott biztonsági javaslatok esetében elérhető, és az erőforrás részletei lap tetején található.

További információ a [helytelen konfigurációkkal kapcsolatos kényszerítő/megtagadási javaslatok megelőzéséről](prevent-misconfigurations.md).

###  <a name="network-security-group-recommendations-improved"></a>A hálózati biztonsági csoport javaslatai javultak

A hálózati biztonsági csoportokkal kapcsolatos következő biztonsági javaslatok javultak a téves pozitív események egyes példányainak csökkentése érdekében.

- Az összes hálózati portot korlátozni kell a virtuális géphez társított NSG
- A felügyeleti portokat be kell zárni a virtuális gépeken
- Az internetre irányuló virtuális gépeket hálózati biztonsági csoportokkal kell védeni
- Az alhálózatokat hálózati biztonsági csoporttal kell társítani


### <a name="deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services"></a>Elavult előnézeti AK-javaslat: "Pod biztonsági szabályzatokat kell meghatározni a Kubernetes-szolgáltatásokban"

A "Pod biztonsági házirendek meghatározása a Kubernetes-szolgáltatásokban" előzetes javaslat az [Azure Kubernetes szolgáltatás](../aks/use-pod-security-policies.md) dokumentációjában leírtaknak megfelelően elavult.

A pod biztonsági házirend (előzetes verzió) funkció az elavult, és a továbbiakban nem lesz elérhető 2020. október 15-én, Azure Policy az AK-hoz.

Miután a pod biztonsági házirend (előzetes verzió) elavult, le kell tiltania a szolgáltatást minden meglévő fürtön az elavult funkcióval a későbbi fürtök frissítéséhez és az Azure-támogatáson belüli tartózkodáshoz.


### <a name="email-notifications-from-azure-security-center-improved"></a>Azure Security Center továbbfejlesztett e-mail-értesítések

A biztonsági riasztásokkal kapcsolatos e-mailek következő területei javultak: 

- Lehetőség van a riasztásokról értesítő e-mailek küldésére az összes súlyossági szinthez.
- Lehetőség van arra, hogy az előfizetésen keresztül különböző Azure-szerepkörökkel tájékoztassa a felhasználókat
- Alapértelmezés szerint az előfizetések tulajdonosait proaktívan értesítjük a nagy súlyosságú riasztásokról (amelyek nagy valószínűséggel valódi behatolást okoznak)
- Eltávolította a telefonszám mezőt az e-mail értesítések konfigurációs oldaláról.

További információt a [biztonsági riasztások e-mail értesítéseinek beállítása](security-center-provide-security-contact-details.md)című témakörben olvashat.


### <a name="secure-score-doesnt-include-preview-recommendations"></a>A biztonságos pontszám nem tartalmazza az előzetes verzióra vonatkozó javaslatokat 

Security Center folyamatosan felméri az erőforrásokat, az előfizetéseket és a szervezetet a biztonsági problémákra. Ezután összesíti az összes megállapítást egyetlen pontszámba, így eldöntheti, hogy az aktuális biztonsági helyzet: minél magasabb a pontszám, annál alacsonyabb az azonosított kockázati szint.

Mivel új fenyegetések észlelhetők, új biztonsági tanácsokat Security Center új javaslatok keretében elérhetővé tesznek. Ha el szeretné kerülni, hogy a meglepetések ne legyenek a biztonságos pontszámban, és adjon meg egy türelmi időszakot, amelyben az új ajánlásokat még a pontszámok befolyásolása előtt megtekintheti, az **előzetes** verzióként megjelölt javaslatok már nem szerepelnek a biztonságos pontszám kiszámítása során. Ha lehetséges, még mindig szervizelni kell őket, hogy ha az előzetes verzió időtartama lejár, a pontszáma is hozzájárul.

Emellett az **előzetes** verzióra vonatkozó javaslatok nem teszik lehetővé a "nem megfelelő" erőforrás megjelenítését.

Példa az előzetes verziójú javaslatra:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Javaslat az előnézet jelölővel":::

[További információ a biztonságos pontszámról](secure-score-security-controls.md).


### <a name="recommendations-now-include-a-severity-indicator-and-the-freshness-interval"></a>A javaslatok mostantól a súlyossági mutatót és a frissességi időközt is tartalmazzák

A javaslatok részleteit tartalmazó oldal mostantól tartalmazza a frissességi intervallum jelzőjét (ha van ilyen), valamint a javaslat súlyosságának egyértelmű megjelenítését.

:::image type="content" source="./media/release-notes/recommendations-severity-freshness-indicators.png" alt-text="A frissességet és súlyosságot mutató javaslat oldal":::



## <a name="august-2020"></a>2020. augusztus

Az augusztusi frissítések a következők:

- [Tárgyieszköz-leltár – az eszközök biztonsági helyzetének hatékony új nézete](#asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets)
- [A Azure Active Directory biztonsági Alapértelmezések támogatása (a többtényezős hitelesítéshez)](#added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication)
- [Egyszerű szolgáltatásnév – javaslat hozzáadva](#service-principals-recommendation-added)
- [Sebezhetőségi felmérés a virtuális gépeken – összevont javaslatok és szabályzatok](#vulnerability-assessment-on-vms---recommendations-and-policies-consolidated)
- [A ASC_default kezdeményezéshez hozzáadott új AK biztonsági szabályzatok – csak privát előzetes verzióban elérhető ügyfelek általi használatra](#new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only)


### <a name="asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets"></a>Tárgyieszköz-leltár – az eszközök biztonsági helyzetének hatékony új nézete

Security Center adatkészlete (jelenleg előzetes verzióban érhető el) segítségével megtekintheti a Security Centerhoz csatlakoztatott erőforrások biztonsági állapotát.

Security Center rendszeresen elemzi az Azure-erőforrások biztonsági állapotát az esetleges biztonsági rések azonosítása érdekében. Ezután javaslatokat tesz a biztonsági rések megoldására. Ha bármilyen erőforráshoz függőben lévő javaslatok vannak, akkor azok megjelennek a leltárban.

A nézet és a hozzá tartozó szűrők segítségével megvizsgálhatja a biztonsági helyzet adatait, és további műveleteket hajthat végre az eredmények alapján.

További információ az [eszközök leltározásáról](asset-inventory.md).


### <a name="added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication"></a>A Azure Active Directory biztonsági Alapértelmezések támogatása (a többtényezős hitelesítéshez)

Security Center teljes körű támogatást kapott a [biztonsági alapértékekhez](../active-directory/fundamentals/concept-fundamentals-security-defaults.md), a Microsoft ingyenes Identity Security-védelmi szolgáltatásait.

A biztonsági alapértékek előre konfigurált identitás-biztonsági beállításokat biztosítanak a szervezet számára az identitással kapcsolatos közös támadások elleni védelemhez. A biztonsági alapértékek már a több mint 5 000 000 bérlőt védik; az 50 000-bérlőket Security Center is védi.

Security Center mostantól biztonsági javaslatot nyújt, ha az Azure-előfizetést az alapértelmezett biztonsági beállítások nélkül azonosítja. Eddig Security Center javasolt a többtényezős hitelesítés engedélyezése a feltételes hozzáférés használatával, amely a Azure Active Directory (AD) prémium szintű licenc részét képezi. Az ingyenes Azure AD-t használó ügyfeleink számára a biztonsági alapértékek engedélyezését javasoljuk. 

Célunk, hogy minél több ügyfelet ösztönözzön a Felhőbeli környezetek védelmére az MFA-val, és hogy enyhítse az egyik legnagyobb kockázatot is, amely a legjelentősebb a [biztonságos pontszám](secure-score-security-controls.md)szempontjából.

További információ a [biztonsági alapbeállításokról](../active-directory/fundamentals/concept-fundamentals-security-defaults.md).


### <a name="service-principals-recommendation-added"></a>Egyszerű szolgáltatásnév – javaslat hozzáadva

Új javaslat lett hozzáadva, amely azt ajánlja, hogy Security Center a felügyeleti tanúsítványokat használó ügyfeleket az előfizetések kezeléséhez az egyszerű szolgáltatásokhoz.

Az **előfizetések felügyeleti tanúsítványok helyett a szolgáltatással való ellátására szolgáló egyszerű szolgáltatásnév** használatával biztosíthatja, hogy az előfizetések biztonságos kezeléséhez használjon egyszerű szolgáltatásokat vagy Azure Resource Manager. 

További információ az [alkalmazások és szolgáltatások egyszerű objektumairól Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object).


### <a name="vulnerability-assessment-on-vms---recommendations-and-policies-consolidated"></a>Sebezhetőségi felmérés a virtuális gépeken – összevont javaslatok és szabályzatok

Security Center megvizsgálja a virtuális gépeket annak észlelésére, hogy a biztonsági rések felmérésére szolgáló megoldás fut-e. Ha nem található sebezhetőségi felmérési megoldás, Security Center javaslatot tesz az üzembe helyezés egyszerűsítésére.

A biztonsági rések megtalálása esetén a Security Center javaslatot tesz a vizsgálat eredményeinek összefoglalására, és szükség esetén orvosolja azokat.

Ha az összes felhasználó számára egységes felhasználói élményt szeretne biztosítani, függetlenül attól, hogy milyen típusú lapolvasót használ, a következő két javaslatot egyesítettük:

|Egyesített javaslat|Módosítások ismertetése|
|----|:----|
|**A biztonsági rések felmérésére szolgáló megoldást engedélyezni kell a virtuális gépeken**|A következő két javaslat helyébe lép:<br> **•** A beépített sebezhetőségi felmérési megoldás engedélyezése a virtuális gépeken (a Qualys (mostantól elavult) a standard szintű csomag részeként)<br> **•** A sebezhetőség-felmérési megoldást telepíteni kell a virtuális gépekre (mostantól elavult) (standard és ingyenes szint)|
|**A virtuális gépek biztonsági réseit szervizelni kell**|A következő két javaslat helyébe lép:<br>**•** A virtuális gépeken észlelt biztonsági rések szervizelése (Qualys-alapú) (már elavult)<br>**•** A biztonsági réseket a sebezhetőség-felmérési megoldásnak kell kijavítania (mostantól elavult)|
|||

Most ugyanazt a javaslatot fogja használni Security Center sebezhetőségi felmérési bővítményének vagy egy magántulajdonban lévő licenccel rendelkező megoldásnak ("BYOL") a partnertől, például a Qualys vagy a Rapid7.

Emellett a biztonsági rések észlelése és a Security Centerra való jelentése esetén egyetlen javaslat figyelmezteti Önt az eredményekre, függetlenül az azokat azonosító sebezhetőségi felmérési megoldástól.

#### <a name="updating-dependencies"></a>Függőségek frissítése

Ha olyan parancsfájlokkal, lekérdezésekkel vagy automatizálással rendelkezik, amelyek az előző javaslatokra vagy a szabályzat kulcsaira/nevére hivatkoznak, használja az alábbi táblázatokat a hivatkozások frissítéséhez:

##### <a name="before-august-2020"></a>Augusztus 2020 előtt

|Ajánlás|Hatókör|
|----|:----|
|**A beépített sebezhetőségi felmérési megoldás engedélyezése a virtuális gépeken (Qualys-alapú)**<br>Kulcs: 550e890b-e652-4d22-8274-60b3bdb24c63|Beépített|
|**A virtuális gépeken található biztonsági rések szervizelése (Qualys-alapú)**<br>Kulcs: 1195afff-c881-495e-9bc5-1486211ae03f|Beépített|
|**A sebezhetőség-felmérési megoldást telepíteni kell a virtuális gépekre**<br>Kulcs: 01b1ed4c-b733-4fee-b145-f23236e70cf3|BYOL|
|**A biztonsági réseket a sebezhetőség-felmérési megoldásnak kell szervizelni**<br>Kulcs: 71992a2a-d168-42e0-b10e-6b45fa2ecddb|BYOL|
||||


|Szabályzat|Hatókör|
|----|:----|
|**A biztonsági rések felmérését engedélyezni kell a virtuális gépeken**<br>Házirend-azonosító: 501541f7-f7e7-4cd6-868c-4190fdad3ac9|Beépített|
|**A biztonsági réseket a sebezhetőség-felmérési megoldásnak kell szervizelni**<br>Házirend-azonosító: 760a85ff-6162-42b3-8d70-698e268f648c|BYOL|
||||


##### <a name="from-august-2020"></a>Augusztus 1-től 2020

|Ajánlás|Hatókör|
|----|:----|
|**A biztonsági rések felmérésére szolgáló megoldást engedélyezni kell a virtuális gépeken**<br>Kulcs: ffff0522-1e88-47fc-8382-2a80ba848f5d|Beépített + BYOL|
|**A virtuális gépek biztonsági réseit szervizelni kell**<br>Kulcs: 1195afff-c881-495e-9bc5-1486211ae03f|Beépített + BYOL|
||||

|Szabályzat|Hatókör|
|----|:----|
|[**A biztonsági rések felmérését engedélyezni kell a virtuális gépeken**](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f501541f7-f7e7-4cd6-868c-4190fdad3ac9)<br>Házirend-azonosító: 501541f7-f7e7-4cd6-868c-4190fdad3ac9 |Beépített + BYOL|
||||


### <a name="new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only"></a>A ASC_default kezdeményezéshez hozzáadott új AK biztonsági szabályzatok – csak privát előzetes verzióban elérhető ügyfelek általi használatra

Annak biztosítása érdekében, hogy a Kubernetes-munkaterhelések alapértelmezés szerint biztonságosak legyenek, Security Center Kubernetes szintű szabályzatokat és megerősítő javaslatokat ad hozzá, beleértve a Kubernetes belépésvezérlés használatával történő kényszerítési lehetőségeket is.

A projekt korai szakasza tartalmaz egy privát előzetes verziót, és az új (alapértelmezés szerint letiltott) szabályzatok hozzáadását a ASC_default kezdeményezéshez.

Nyugodtan figyelmen kívül hagyhatja ezeket a szabályzatokat, és nem lesz hatással a környezetre. Ha engedélyezni szeretné őket, regisztráljon az előzetes verzióra, https://aka.ms/SecurityPrP és válasszon a következő lehetőségek közül:

1. **Single Preview** – csak a privát előzetes verzióhoz csatlakozhat. Explicit módon megemlíti a "ASC folyamatos vizsgálat" lehetőséget, mint a csatlakozni kívánt előnézetet.
1. **Folyamatban lévő program** – ehhez és a jövőbeli privát előzetesekhez való hozzáadáshoz. Egy profilt és egy adatvédelmi szerződést kell elvégeznie.


## <a name="july-2020"></a>2020. július

A júliusban elérhető frissítések a következők:
- [A virtuális gépek sebezhetőségi felmérése mostantól nem Piactéri rendszerképekhez érhető el](#vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images)
- [Az Azure Storage veszélyforrások elleni védelme kibővült Azure Files és Azure Data Lake Storage Gen2 (előzetes verzió)](#threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview)
- [Nyolc új javaslat a veszélyforrások elleni védelem funkcióinak engedélyezéséhez](#eight-new-recommendations-to-enable-threat-protection-features)
- [A tároló biztonsági fejlesztése – a beállításjegyzék gyorsabb vizsgálata és a frissített dokumentáció](#container-security-improvements---faster-registry-scanning-and-refreshed-documentation)
- [Az adaptív alkalmazások vezérlői új javaslattal frissültek, és támogatják a helyettesítő karaktereket az elérésiút-szabályokban](#adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules)
- [Hat házirend a speciális SQL-adatbiztonsághoz – elavult](#six-policies-for-sql-advanced-data-security-deprecated)




### <a name="vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images"></a>A virtuális gépek sebezhetőségi felmérése mostantól nem Piactéri rendszerképekhez érhető el

Sebezhetőség-felmérési megoldás telepítésekor Security Center korábban ellenőrzési ellenőrzést hajtottak végre a telepítése előtt. Az ellenőrzéshez a cél virtuális gép Piactéri SKU-jának megerősítése szükséges. 

Ebből a frissítésből az ellenőrzés el lett távolítva, és most már telepítheti a sebezhetőség-felmérési eszközöket az "egyéni" Windows és Linux rendszerű gépekre. Az egyéni lemezképek olyanok, amelyeket a piactér alapértelmezett értékeivel módosított.

Bár mostantól több gépen is üzembe helyezheti az integrált sebezhetőségi felmérési bővítményt (Qualys), a támogatás csak akkor érhető el, ha az [integrált sebezhetőségi képolvasó telepítése standard szintű virtuális](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines) gépekre című részben felsorolt operációs rendszert használja.

További információ a [virtuális gépekhez készült integrált sebezhetőségi képolvasóról (Azure Defender szükséges)](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner).

További információ a Qualys vagy a Rapid7 által a saját, magánkézben lévő licenccel rendelkező sebezhetőségi felmérési megoldás használatáról a [partneri sebezhetőségi vizsgálat megoldásának üzembe helyezése című](deploy-vulnerability-assessment-vm.md)témakörben.


### <a name="threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview"></a>Az Azure Storage veszélyforrások elleni védelme kibővült Azure Files és Azure Data Lake Storage Gen2 (előzetes verzió)

Az Azure Storage veszélyforrások elleni védelme észleli az Azure Storage-fiókok potenciálisan káros tevékenységeit. A Security Center riasztásokat jelenít meg, amikor észleli a Storage-fiókok elérésére vagy kihasználására tett kísérleteket. 

Az Ön adatai védetté tehetik, hogy blob-tárolóként, fájlmegosztásként vagy adattavakként vannak tárolva.




### <a name="eight-new-recommendations-to-enable-threat-protection-features"></a>Nyolc új javaslat a veszélyforrások elleni védelem funkcióinak engedélyezéséhez

Nyolc új javaslat lett hozzáadva, amely lehetővé teszi Azure Security Center veszélyforrások elleni védelmi funkcióinak használatát a következő erőforrástípusok esetében: Virtual Machines, App Service Plans, Azure SQL Database Servers, SQL Servers on Machines, Azure Storage-fiókok, Azure Kubernetes Service-fürtök, Azure Container Registry-jegyzékek és Azure Key Vault-tárolók.

Az új javaslatok a következők:

- **A speciális adatbiztonságot engedélyezni kell Azure SQL Database-kiszolgálókon**
- **A speciális adatbiztonságot engedélyezni kell a gépeken futó SQL-kiszolgálókon**
- **A komplex veszélyforrások elleni védelemnek engedélyezve kell lennie Azure App Service-csomagokon**
- **A komplex veszélyforrások elleni védelemnek engedélyezve kell lennie Azure Container Registry-jegyzékeken**
- **A komplex veszélyforrások elleni védelemnek engedélyezve kell lennie Azure Key Vault-tárolón**
- **Az összetett veszélyforrások elleni védelemnek engedélyezve kell lennie az Azure Kubernetes Service-fürtökön**
- **Az összetett veszélyforrások elleni védelemnek engedélyezve kell lennie az Azure Storage-fiókokban**
- **A komplex veszélyforrások elleni védelemnek engedélyezve kell lennie a virtuális gépeken**

Ezek az új javaslatok az Azure Defender biztonsági ellenőrzésének **engedélyezéséhez** tartoznak.

A javaslatok a gyors javítás lehetőségét is tartalmazzák. 

> [!IMPORTANT]
> A fenti javaslatok bármelyikének szervizelését a kapcsolódó erőforrások védelméért kell fizetnie. Ezek a díjak azonnal megkezdődnek, ha a jelenlegi előfizetésben kapcsolódó erőforrásokkal rendelkezik. Vagy a jövőben, ha később adja hozzá őket.
> 
> Ha például nem rendelkezik Azure Kubernetes Service-fürtökkel az előfizetésben, és engedélyezi a veszélyforrások elleni védelmet, akkor nem számítunk fel díjat. Ha a jövőben egy fürtöt ad hozzá ugyanahhoz az előfizetéshez, az automatikusan védett lesz, és a díjak ekkor megkezdődik.

További információt ezekről a [biztonsági javaslatok hivatkozását ismertető oldalon](recommendations-reference.md)olvashat.

További információ a [veszélyforrások elleni védelemről Azure Security Center](azure-defender.md).




### <a name="container-security-improvements---faster-registry-scanning-and-refreshed-documentation"></a>A tároló biztonsági fejlesztése – a beállításjegyzék gyorsabb vizsgálata és a frissített dokumentáció

A tároló biztonsági tartományának folyamatos beruházásainak részeként örömmel vesszük, hogy jelentős teljesítménybeli javulást tapasztalunk a Azure Container Registryban tárolt tárolók rendszerképeinek Security Center dinamikus vizsgálatán. A vizsgálatok mostantól általában nagyjából két percen belül befejeződik. Bizonyos esetekben akár 15 percet is igénybe vehet.

A Azure Security Center tárolójának biztonsági képességeivel kapcsolatos egyértelműség és útmutatás javítása érdekében a Container Security dokumentációs lapjai is frissültek. 

A Security Center tárolójának biztonságáról a következő cikkekben tájékozódhat:

- [A Security Center tárolójának biztonsági funkcióinak áttekintése](container-security.md)
- [Az Azure Container Registry-nal való integráció részletei](defender-for-container-registries-introduction.md)
- [Az Azure Kubernetes szolgáltatással való integráció részletei](defender-for-kubernetes-introduction.md)
- [Útmutató a beállításjegyzékek vizsgálatához és a Docker-gazdagépek megerősítéséhez](container-security.md)
- [Biztonsági riasztások az Azure Kubernetes Service-fürtök veszélyforrások elleni védelmi funkcióiról](alerts-reference.md#alerts-akscluster)
- [Biztonsági riasztások az Azure Kubernetes Service-gazdagépek veszélyforrások elleni védelmi funkcióiról](alerts-reference.md#alerts-containerhost)
- [Biztonsági javaslatok tárolók számára](recommendations-reference.md#recs-containers)



### <a name="adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules"></a>Az adaptív alkalmazások vezérlői új javaslattal frissültek, és támogatják a helyettesítő karaktereket az elérésiút-szabályokban

Az adaptív alkalmazás-vezérlők funkció két jelentős frissítést kapott:

* Egy új javaslat azonosítja a korábban nem engedélyezett, potenciálisan legitim viselkedést. Az **adaptív alkalmazás-vezérlési házirend új javaslata, engedélyezési szabályai frissülnek**, és új szabályok hozzáadását kéri a meglévő szabályzathoz, hogy csökkentse a hamis pozitív állapotok számát az adaptív alkalmazás-vezérlők megsértésével kapcsolatos riasztásokban.

* Az elérésiút-szabályok mostantól támogatják a helyettesítő karaktereket. Ebből a frissítésből a helyettesítő karakterek használatával konfigurálhatja az engedélyezett elérésiút-szabályokat. Két támogatott forgatókönyv létezik:

    * Az elérési út végén található helyettesítő karakter használata a mappában és az almappákban található összes végrehajtható fájl engedélyezéséhez

    * Egy elérési út közepén található helyettesítő karakter használatával engedélyezhető egy ismert végrehajtható név (például személyes felhasználói mappák ismert végrehajtható fájlokkal, automatikusan létrehozott mappák nevei stb.).


[További információ az adaptív alkalmazások vezérlőinek használatáról](security-center-adaptive-application.md).



### <a name="six-policies-for-sql-advanced-data-security-deprecated"></a>Hat házirend a speciális SQL-adatbiztonsághoz – elavult

Az SQL-gépek speciális adatvédelmével kapcsolatos hat szabályzat elavult:

- Az összetett veszélyforrások elleni védelem típusait "all" értékre kell beállítani az SQL felügyelt példány speciális adatbiztonsági beállításainál
- Az összetett veszélyforrások elleni védelem típusait "all" értékre kell állítani az SQL Server speciális adatbiztonsági beállításaiban
- Az SQL által felügyelt példány speciális biztonsági beállításaiban szerepelnie kell egy e-mail-címnek a biztonsági riasztások fogadásához
- Az SQL Server speciális biztonsági beállításaiban szerepelnie kell egy e-mail-címnek a biztonsági riasztások fogadásához
- A rendszergazdáknak és az előfizetések tulajdonosainak szóló e-mailes értesítéseket engedélyezni kell az SQL felügyelt példány speciális biztonsági beállításaiban.
- A rendszergazdák és az előfizetések tulajdonosainak szóló e-mail-értesítéseket engedélyezni kell az SQL Server speciális adatbiztonsági beállításaiban

További információ a [beépített szabályzatokról](./policy-reference.md).
