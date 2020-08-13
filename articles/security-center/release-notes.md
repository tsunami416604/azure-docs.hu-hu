---
title: A Azure Security Center kibocsátási megjegyzései
description: A Azure Security Center újdonságait és változásait ismertető leírás.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2020
ms.author: memildin
ms.openlocfilehash: caad034c2cc3403bc88ee5f50d101702ec692c35
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192256"
---
# <a name="whats-new-in-azure-security-center"></a>A Azure Security Center újdonságai

Az Azure Security aktív fejlesztés alatt áll, és folyamatosan fejleszti a fejlesztéseket. Ha naprakészen szeretne maradni a legújabb fejleményekkel, az oldal a következő információkat tartalmazza:

- Új funkciók
- Hibajavítások
- Elavult funkciók

Ez az oldal rendszeresen frissül, ezért gyakran újra felkeresik. Ha hat hónapnál régebbi elemeket keres, az archívumban találhatja meg a [Azure Security Center újdonságait](release-notes-archive.md).


## <a name="august-2020"></a>Augusztus 2020

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

|Javaslat|Hatókör|
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

|Javaslat|Hatókör|
|----|:----|
|**A biztonsági rések felmérésére szolgáló megoldást engedélyezni kell a virtuális gépeken**<br>Kulcs: ffff0522-1e88-47fc-8382-2a80ba848f5d|Beépített + BYOL|
|**A virtuális gépek biztonsági réseit szervizelni kell**<br>Kulcs: 1195afff-c881-495e-9bc5-1486211ae03f|Beépített + BYOL|
||||

|Szabályzat|Hatókör|
|----|:----|
|[**A biztonsági rések felmérését engedélyezni kell a virtuális gépeken**](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f501541f7-f7e7-4cd6-868c-4190fdad3ac9)<br>Házirend-azonosító: 501541f7-f7e7-4cd6-868c-4190fdad3ac9 |Beépített + BYOL|
||||



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

Bár mostantól több gépen is telepítheti az integrált sebezhetőségi felmérési bővítményt (Qualys), a támogatás csak akkor érhető el, ha a [Qualys beépített sebezhetőségi ellenőrzőeszköz üzembe helyezése](built-in-vulnerability-assessment.md#deploying-the-qualys-built-in-vulnerability-scanner)című részben felsorolt operációs rendszert használja.

További információ a [virtuális gépekhez készült integrált sebezhetőségi képolvasóról (csak standard szintű](built-in-vulnerability-assessment.md)csomag esetén).

További információ a Qualys vagy a Rapid7 saját, saját maga által licencelt sebezhetőségi felmérési megoldásának használatáról a [partneri sebezhetőségi vizsgálat megoldásának üzembe helyezése című](partner-vulnerability-assessment.md)témakörben.


### <a name="threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview"></a>Az Azure Storage veszélyforrások elleni védelme kibővült Azure Files és Azure Data Lake Storage Gen2 (előzetes verzió)

Az Azure Storage veszélyforrások elleni védelme észleli az Azure Storage-fiókok potenciálisan káros tevékenységeit. A Security Center riasztásokat jelenít meg, amikor észleli a Storage-fiókok elérésére vagy kihasználására tett kísérleteket. 

Az Ön adatai védetté tehetik, hogy blob-tárolóként, fájlmegosztásként vagy adattavakként vannak tárolva. 

További információ az [Azure Storage veszélyforrások elleni védelméről](threat-protection.md#threat-protection-for-azure-storage-).




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

Ezek az új javaslatok a komplex **veszélyforrások elleni védelem engedélyezése** biztonsági szabályozáshoz tartoznak.

A javaslatok a gyors javítás lehetőségét is tartalmazzák. 

> [!IMPORTANT]
> A fenti javaslatok bármelyikének szervizelését a kapcsolódó erőforrások védelméért kell fizetnie. Ezek a díjak azonnal megkezdődnek, ha a jelenlegi előfizetésben kapcsolódó erőforrásokkal rendelkezik. Vagy a jövőben, ha később adja hozzá őket.
> 
> Ha például nem rendelkezik Azure Kubernetes Service-fürtökkel az előfizetésben, és engedélyezi a veszélyforrások elleni védelmet, akkor nem számítunk fel díjat. Ha a jövőben egy fürtöt ad hozzá ugyanahhoz az előfizetéshez, az automatikusan védett lesz, és a díjak ekkor megkezdődik.

További információt ezekről a [biztonsági javaslatok hivatkozását ismertető oldalon](recommendations-reference.md)olvashat.

További információ a [veszélyforrások elleni védelemről Azure Security Center](https://docs.microsoft.com/azure/security-center/threat-protection).




### <a name="container-security-improvements---faster-registry-scanning-and-refreshed-documentation"></a>A tároló biztonsági fejlesztése – a beállításjegyzék gyorsabb vizsgálata és a frissített dokumentáció

A tároló biztonsági tartományának folyamatos beruházásainak részeként örömmel vesszük, hogy jelentős teljesítménybeli javulást tapasztalunk a Azure Container Registryban tárolt tárolók rendszerképeinek Security Center dinamikus vizsgálatán. A vizsgálatok mostantól általában nagyjából két percen belül befejeződik. Bizonyos esetekben akár 15 percet is igénybe vehet.

A Azure Security Center tárolójának biztonsági képességeivel kapcsolatos egyértelműség és útmutatás javítása érdekében a Container Security dokumentációs lapjai is frissültek. 

A Security Center tárolójának biztonságáról a következő cikkekben tájékozódhat:

- [A Security Center tárolójának biztonsági funkcióinak áttekintése](https://docs.microsoft.com/azure/security-center/container-security)
- [Az Azure Container Registry-nal való integráció részletei](https://docs.microsoft.com/azure/security-center/azure-container-registry-integration)
- [Az Azure Kubernetes szolgáltatással való integráció részletei](https://docs.microsoft.com/azure/security-center/azure-kubernetes-service-integration)
- [Útmutató a beállításjegyzékek vizsgálatához és a Docker-gazdagépek megerősítéséhez](https://docs.microsoft.com/azure/security-center/monitor-container-security)
- [Biztonsági riasztások az Azure Kubernetes Service-fürtök veszélyforrások elleni védelmi funkcióiról](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster)
- [Biztonsági riasztások az Azure Kubernetes Service-gazdagépek veszélyforrások elleni védelmi funkcióiról](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-containerhost)
- [Biztonsági javaslatok tárolók számára](https://docs.microsoft.com/azure/security-center/recommendations-reference#recs-containers)



### <a name="adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules"></a>Az adaptív alkalmazások vezérlői új javaslattal frissültek, és támogatják a helyettesítő karaktereket az elérésiút-szabályokban

Az adaptív alkalmazás-vezérlők funkció két jelentős frissítést kapott:

* Egy új javaslat azonosítja a korábban nem engedélyezett, potenciálisan legitim viselkedést. Az **adaptív alkalmazás-vezérlési házirend új javaslata, engedélyezési szabályai frissülnek**, és új szabályok hozzáadását kéri a meglévő szabályzathoz, hogy csökkentse a hamis pozitív állapotok számát az adaptív alkalmazás-vezérlők megsértésével kapcsolatos riasztásokban.

* Az elérésiút-szabályok mostantól támogatják a helyettesítő karaktereket. Ebből a frissítésből a helyettesítő karakterek használatával konfigurálhatja az engedélyezett elérésiút-szabályokat. Két támogatott forgatókönyv létezik:

    * Az elérési út végén található helyettesítő karakter használata a mappában és az almappákban található összes végrehajtható fájl engedélyezéséhez

    * Egy elérési út közepén lévő helyettesítő karakter használatával engedélyezheti egy ismert végrehajtható név módosítását a mappanév (például személyes felhasználói mappák ismert végrehajtható fájlokkal, automatikusan létrehozott mappanevek stb.).


[További információ az adaptív alkalmazások vezérlőinek használatáról](security-center-adaptive-application.md).



### <a name="six-policies-for-sql-advanced-data-security-deprecated"></a>Hat házirend a speciális SQL-adatbiztonsághoz – elavult

Az SQL-gépek speciális adatvédelmével kapcsolatos hat szabályzat elavult:

- Az összetett veszélyforrások elleni védelem típusait "all" értékre kell beállítani az SQL felügyelt példány speciális adatbiztonsági beállításainál
- Az összetett veszélyforrások elleni védelem típusait "all" értékre kell állítani az SQL Server speciális adatbiztonsági beállításaiban
- Az SQL által felügyelt példány speciális biztonsági beállításaiban szerepelnie kell egy e-mail-címnek a biztonsági riasztások fogadásához
- Az SQL Server speciális biztonsági beállításaiban szerepelnie kell egy e-mail-címnek a biztonsági riasztások fogadásához
- A rendszergazdáknak és az előfizetések tulajdonosainak szóló e-mailes értesítéseket engedélyezni kell az SQL felügyelt példány speciális biztonsági beállításaiban.
- A rendszergazdák és az előfizetések tulajdonosainak szóló e-mail-értesítéseket engedélyezni kell az SQL Server speciális adatbiztonsági beállításaiban

További információ a [beépített szabályzatokról](security-center-policy-definitions.md).





## <a name="june-2020"></a>2020. június

A júniusi frissítések a következők:
- [Secure score API (előzetes verzió)](#secure-score-api-preview)
- [Fejlett adatbiztonság SQL-gépekhez (Azure, egyéb felhők és helyszíni) (előzetes verzió)](#advanced-data-security-for-sql-machines-azure-other-clouds-and-on-prem-preview)
- [Két új javaslat a Log Analytics-ügynök üzembe helyezéséhez az Azure arc Machines szolgáltatásban (előzetes verzió)](#two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview)
- [Új szabályzatok folyamatos exportálási és munkafolyamat-automatizálási konfigurációk létrehozásához nagy méretekben](#new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale)
- [Új javaslat a NSG használatára a nem internetre irányuló virtuális gépek elleni védelemhez](#new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines)
- [Új szabályzatok a fenyegetések elleni védelem és a speciális adatbiztonság engedélyezéséhez](#new-policies-for-enabling-threat-protection-and-advanced-data-security)



### <a name="secure-score-api-preview"></a>Secure score API (előzetes verzió)

Mostantól elérheti a pontszámát a [Secure score API](https://docs.microsoft.com/rest/api/securitycenter/securescores/) -n keresztül (jelenleg előzetes verzióban érhető el). Az API-módszerek lehetővé teszik az adatlekérdezés rugalmasságát és a biztonságos pontszámok saját jelentési mechanizmusának elkészítését az idő múlásával. Használhatja például a **Secure scores** API-t egy adott előfizetés pontszámának lekéréséhez. Emellett a **Secure score Controls** API használatával is listázhatja az előfizetések biztonsági vezérlőit és aktuális pontszámát.

A biztonságos pontszám API-val lehetséges külső eszközökre vonatkozó példákat a [GitHub-Közösség biztonságos pontszám területén](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)tekintheti meg.

További információ a [Azure Security Center biztonságos pontszámáról és biztonsági vezérlőinek](secure-score-security-controls.md).



### <a name="advanced-data-security-for-sql-machines-azure-other-clouds-and-on-prem-preview"></a>Fejlett adatbiztonság SQL-gépekhez (Azure, egyéb felhők és helyszíni) (előzetes verzió)

Az SQL Machines szolgáltatáshoz készült fejlett adatbiztonság mostantól védi az Azure-ban üzemeltetett SQL-kiszolgálókat, más felhőalapú környezetekben, vagy akár helyszíni gépeken is. Azure Security Center Ez kibővíti az Azure-natív SQL-kiszolgálók védelmét, hogy teljes mértékben támogassa a hibrid környezeteket.

A speciális adatbiztonság biztonsági rések felmérését és komplex veszélyforrások elleni védelmet biztosít az SQL-gépek számára, bárhol is legyenek.

A telepítés két lépést tesz szükségessé:

1. Az Log Analytics-ügynök üzembe helyezése a SQL Server gazdagépén az Azure-fiókkal való kapcsolódás biztosításához.

1. A választható csomag engedélyezése Security Center díjszabási és beállítási lapján.

További információ [az SQL-gépek speciális adatbiztonságáról](security-center-iaas-advanced-data.md).



### <a name="two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview"></a>Két új javaslat a Log Analytics-ügynök üzembe helyezéséhez az Azure arc Machines szolgáltatásban (előzetes verzió)

Két új javaslat lett hozzáadva a [log Analytics-ügynök](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent) üzembe helyezéséhez az Azure arc-gépeken, és gondoskodni kell arról, hogy a Azure Security Center védelemmel rendelkezzenek:

- **Log Analytics ügynöknek telepítve kell lennie a Windows-alapú Azure arc-gépeken (előzetes verzió)**
- **Log Analytics ügynöknek telepítve kell lennie a Linux-alapú Azure arc-gépeken (előzetes verzió)**

Ezek az új javaslatok ugyanabban a négy biztonsági vezérlőben jelennek meg, mint a meglévő (kapcsolódó) javaslat, a **figyelési ügynök telepítése a gépekre**: a biztonsági konfigurációk szervizelése, adaptív alkalmazás-vezérlés alkalmazása, rendszerfrissítések alkalmazása és az Endpoint Protection engedélyezése.

A javaslatok az üzembe helyezési folyamat felgyorsításához szükséges gyors javítási képességgel is rendelkeznek. 

Ebből a két új javaslatból megismerheti a [számítási és alkalmazási javaslatok](recommendations-reference.md#recs-computeapp) táblázatát.

További információ arról, hogy a Azure Security Center hogyan használja az ügynököt a [log Analytics Agent](https://docs.microsoft.com/azure/security-center/faq-data-collection-agents#what-is-the-log-analytics-agent)ügynökben?.

További tudnivalók az [Azure arc-gépek bővítményeiről](https://docs.microsoft.com/azure/azure-arc/servers/manage-vm-extensions#enable-extensions-from-the-portal).



### <a name="new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale"></a>Új szabályzatok folyamatos exportálási és munkafolyamat-automatizálási konfigurációk létrehozásához nagy méretekben

A szervezet figyelési és incidens-reagálási folyamatainak automatizálása nagy mértékben növelheti a biztonsági incidensek kivizsgálásához és enyhítéséhez szükséges időt.

Az Automation-konfigurációk szervezeten belüli üzembe helyezéséhez használja ezeket a beépített "DeployIfdNotExist" Azure-szabályzatokat a [folyamatos exportálási](continuous-export.md) és [munkafolyamat-automatizálási](workflow-automation.md) eljárások létrehozásához és konfigurálásához:

A szabályzatok az Azure Policy szolgáltatásban találhatók:


|Cél  |Szabályzat  |Házirend-azonosító  |
|---------|---------|---------|
|Folyamatos Exportálás az Event hub-ba|[Az Event hub-ba való exportálás üzembe helyezése Azure Security Center riasztások és javaslatok esetén](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
|Folyamatos exportálás Log Analytics munkaterületre|[Azure Security Center riasztások és javaslatok Log Analytics munkaterületre való exportálásának központi telepítése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
|Biztonsági riasztások munkafolyamat-automatizálása|[Munkafolyamat-automatizálás üzembe helyezése Azure Security Center riasztásokhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|Munkafolyamat-automatizálás biztonsági javaslatokhoz|[Munkafolyamat-automatizálás üzembe helyezése Azure Security Center javaslatokhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
||||

Ismerkedés a [munkafolyamat-automatizálási sablonokkal](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation).

További információ a két exportálási házirend használatáról: [Azure Security Center riasztások és javaslatok folyamatos exportálása a házirend](https://techcommunity.microsoft.com/t5/azure-security-center/continuously-export-azure-security-center-alerts-and/ba-p/1440745)segítségével.


### <a name="new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines"></a>Új javaslat a NSG használatára a nem internetre irányuló virtuális gépek elleni védelemhez

Az "ajánlott biztonsági eljárások implementálása" biztonsági vezérlő mostantól az alábbi új javaslatot tartalmazza:

- **A nem internetkapcsolattal rendelkező virtuális gépeket hálózati biztonsági csoportokkal kell védeni**

Egy meglévő, **internetre irányuló virtuális gépet védeni kell a hálózati biztonsági csoportokkal**, nem kell különbséget tenni az internetre irányuló és a nem internetkapcsolattal rendelkező virtuális gépek között. Mindkét esetben magas súlyosságú javaslat jön létre, ha egy virtuális gépet nem hálózati biztonsági csoporthoz rendeltek hozzá. Ez az új javaslat elkülöníti a nem internetkapcsolattal rendelkező gépeket a téves pozitív problémák csökkentése és a szükségtelen magas súlyosságú riasztások elkerülése érdekében.

További információ a [hálózati javaslatok](recommendations-reference.md#recs-network) táblázatban található.




### <a name="new-policies-for-enabling-threat-protection-and-advanced-data-security"></a>Új szabályzatok a fenyegetések elleni védelem és a speciális adatbiztonság engedélyezéséhez

Az alábbi új szabályzatok az ASC alapértelmezett kezdeményezéshez lettek hozzáadva, és úgy vannak kialakítva, hogy segítséget nyújtsanak a veszélyforrások elleni védelem vagy a speciális adatbiztonság biztosításához a megfelelő erőforrástípusok esetében.

A szabályzatok az Azure Policy szolgáltatásban találhatók:


| Szabályzat                                                                                                                                                                                                                                                                | Házirend-azonosító                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------|
| [A speciális adatbiztonságot engedélyezni kell Azure SQL Database-kiszolgálókon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f7fe3b40f-802b-4cdd-8bd4-fd799c948cc2)     | 7fe3b40f-802b-4cdd-8bd4-fd799c948cc2 |
| [A speciális adatbiztonságot engedélyezni kell a gépeken futó SQL-kiszolgálókon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f6581d072-105e-4418-827f-bd446d56421b) | 6581d072-105e-4418-827f-bd446d56421b |
| [Az összetett veszélyforrások elleni védelemnek engedélyezve kell lennie az Azure Storage-fiókokban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f308fbb08-4ab8-4e67-9b29-592e93fb94fa)           | 308fbb08-4ab8-4e67-9b29-592e93fb94fa |
| [A komplex veszélyforrások elleni védelemnek engedélyezve kell lennie Azure Key Vault-tárolón](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f0e6763cc-5078-4e64-889d-ff4d9a839047)           | 0e6763cc-5078-4e64-889d-ff4d9a839047 |
| [A komplex veszélyforrások elleni védelemnek engedélyezve kell lennie Azure App Service-csomagokon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f2913021d-f2fd-4f3d-b958-22354e2bdbcb)                | 2913021d-f2fd-4f3d-b958-22354e2bdbcb |
| [A komplex veszélyforrások elleni védelemnek engedélyezve kell lennie Azure Container Registry-jegyzékeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4)   | c25d9a16-bc35-4e15-a7e5-9db606bf9ed4 |
| [Az összetett veszélyforrások elleni védelemnek engedélyezve kell lennie az Azure Kubernetes Service-fürtökön](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f523b5cd1-3e23-492f-a539-13118b6d1e3a)   | 523b5cd1-3e23-492f-a539-13118b6d1e3a |
| [A komplex veszélyforrások elleni védelemnek engedélyezve kell lennie Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f4da35fc9-c9e7-4960-aec9-797fe7d9051d)           | 4da35fc9-c9e7-4960-aec9-797fe7d9051d |
|                                                                                                                                                                                                                                                                       |                                      |

További információ a [veszélyforrások elleni védelemről Azure Security Center](https://docs.microsoft.com/azure/security-center/threat-protection).





## <a name="may-2020"></a>2020. május

A frissítések a következők lehetnek:
- [Riasztás-elnyomási szabályok (előzetes verzió)](#alert-suppression-rules-preview)
- [A virtuális gépek sebezhetőségi felmérése mostantól általánosan elérhető](#virtual-machine-vulnerability-assessment-is-now-generally-available)
- [Az igény szerinti (JIT) virtuális gépekhez való hozzáférés módosításai](#changes-to-just-in-time-jit-virtual-machine-vm-access)
- [Egyéni javaslatok lettek áthelyezve egy külön biztonsági vezérlőbe](#custom-recommendations-have-been-moved-to-a-separate-security-control)
- [A bekapcsolva beállítással megtekintheti a vezérlőkre vagy a lapos listára vonatkozó javaslatokat](#toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list)
- [Kibővített biztonsági szabályozás – az ajánlott biztonsági eljárások megvalósítása](#expanded-security-control-implement-security-best-practices)
- [Az egyéni metaadatokkal rendelkező egyéni szabályzatok már általánosan elérhetők](#custom-policies-with-custom-metadata-are-now-generally-available)
- [Összeomlási memóriakép-elemzési képességek migrálása a fájlok közötti támadás észlelése érdekében](#crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection)


### <a name="alert-suppression-rules-preview"></a>Riasztás-elnyomási szabályok (előzetes verzió)

Ez az új funkció (jelenleg előzetes verzióban érhető el) segít csökkenteni a riasztások fáradtságát. Szabályok használatával automatikusan elrejtheti azokat a riasztásokat, amelyek ismerten ártalmatlanok vagy a szervezeten belüli szokásos tevékenységekkel kapcsolatosak. Ez lehetővé teszi, hogy a leginkább releváns fenyegetésekre koncentráljon. 

Az engedélyezett letiltási szabályoknak megfelelő riasztások továbbra is létrejönnek, de az állapotukat a rendszer elutasítja. Megtekintheti az állapotot a Azure Portalban, vagy hozzáférhet a Security Center biztonsági riasztásokhoz.

A mellőzési szabályok határozzák meg azokat a feltételeket, amelyeknek a riasztásait automatikusan el kell utasítani. Általában letiltási szabályt kell használnia a következőkhöz:

- hamis pozitívként azonosított riasztások letiltása

- a túl gyakran kiváltott riasztások mellőzése

További információ a [riasztások Azure Security Center fenyegetés elleni védelemből való letiltásáról](alerts-suppression-rules.md).


### <a name="virtual-machine-vulnerability-assessment-is-now-generally-available"></a>A virtuális gépek sebezhetőségi felmérése mostantól általánosan elérhető

A Security Center standard szintű csomagja mostantól egy integrált sebezhetőségi felmérést is tartalmaz a virtuális gépekhez, felár nélkül. Ezt a bővítményt a Qualys működteti, de az eredményeket közvetlenül visszaküldi Security Centerra. Nincs szüksége Qualys-licencre, vagy akár Qualys-fiókra is – minden a Security Centeron belül zökkenőmentesen kezelhető.

Az új megoldás segítségével folyamatosan ellenőrizheti a virtuális gépeket, és megtalálhatja a biztonsági réseket, és bemutathatja az eredményeket a Security Centerban. 

A megoldás üzembe helyezéséhez használja az új biztonsági javaslatot:

"A beépített sebezhetőségi felmérési megoldás engedélyezése a virtuális gépeken (Qualys-alapú)"

További információ a [Security Center a virtuális gépek integrált sebezhetőségi felméréséről](built-in-vulnerability-assessment.md).



### <a name="changes-to-just-in-time-jit-virtual-machine-vm-access"></a>Az igény szerinti (JIT) virtuális gépekhez való hozzáférés módosításai

Security Center tartalmaz egy opcionális szolgáltatást a virtuális gépek felügyeleti portjainak a megóvásához. Ez védelmet nyújt a találgatásos támadás leggyakoribb formája ellen.

Ez a frissítés a következő módosításokat hajtja végre a szolgáltatásban:

- A rendszer azt ajánlja, hogy a JIT engedélyezése egy virtuális gépen legyen átnevezve. Korábban "igény szerinti hálózati hozzáférés-vezérlést kell alkalmazni a virtuális gépeken." most már: "a virtuális gépek felügyeleti portjait az igény szerinti hálózati hozzáférés-vezérléssel kell védeni."

- A javaslat csak akkor aktiválódik, ha nyitott felügyeleti portok vannak.

További információ [az JIT-hozzáférési szolgáltatásról](security-center-just-in-time.md).


### <a name="custom-recommendations-have-been-moved-to-a-separate-security-control"></a>Egyéni javaslatok lettek áthelyezve egy külön biztonsági vezérlőbe

A fokozott biztonságú pontszámmal bevezetett egyik biztonsági vezérlő a következő: "az ajánlott biztonsági eljárások megvalósítása". Az előfizetésekhez létrehozott egyéni javaslatok automatikusan ebbe a vezérlőbe kerülnek. 

Ahhoz, hogy könnyebben megtalálja az egyéni javaslatokat, áthelyezte őket egy dedikált biztonsági vezérlőbe, "egyéni javaslatok". Ez a vezérlő nem befolyásolja a biztonságos pontszámot.

További információ a biztonsági ellenőrzésekről a [Azure Security Center továbbfejlesztett biztonságos pontszám (előzetes verzió)](secure-score-security-controls.md)című témakörben.


### <a name="toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list"></a>A bekapcsolva beállítással megtekintheti a vezérlőkre vagy a lapos listára vonatkozó javaslatokat

A biztonsági vezérlők a kapcsolódó biztonsági javaslatok logikai csoportjai. A sebezhető támadási felületeket tükrözik. A vezérlők a biztonsági javaslatok összessége, amelyek útmutatást nyújtanak a javaslatok megvalósításához.

Ha szeretné azonnal megtekinteni, hogy a szervezet milyen jól védi az egyes támadási felületet, tekintse át az egyes biztonsági vezérlők pontszámait.

Alapértelmezés szerint a javaslatok a biztonsági vezérlőkben jelennek meg. Ebből a frissítésből listát is megjeleníthet. Ha az érintett erőforrások állapotának megfelelően rendezi az egyszerű listát, használja az új "Group By Controls" (csoportosítás a vezérlők szerint) lehetőséget. A váltógomb a portálon megjelenő lista felett van.

A biztonsági vezérlők – és ez a váltás – az új biztonsági pontszám részét képezik. Ne feledje, hogy küldje el nekünk a visszajelzéseit a portálon belülről.

További információ a biztonsági ellenőrzésekről a [Azure Security Center továbbfejlesztett biztonságos pontszám (előzetes verzió)](secure-score-security-controls.md)című témakörben.

!["Group By Controls" – javaslatok](\media\secure-score-security-controls\recommendations-group-by-toggle.gif)

### <a name="expanded-security-control-implement-security-best-practices"></a>Kibővített biztonsági szabályozás – az ajánlott biztonsági eljárások megvalósítása 

A fokozottan biztonságos pontszámsal bevezetett egyik biztonsági vezérlő "az ajánlott biztonsági eljárások megvalósítása". Ha egy javaslat ebben a vezérlőben van, az nem érinti a biztonságos pontszámot. 

Ezzel a frissítéssel három javaslatot helyeztek el azokról a vezérlőkről, amelyekben eredetileg elhelyezték őket, és az ajánlott eljárások szabályozásával. Azért tettük ezt a lépést, mert azt állapítottuk meg, hogy a három javaslat kockázata alacsonyabb, mint eredetileg gondolták.

Emellett két új javaslat is be lett vezetve, és hozzá lett adva a vezérlőhöz.

Az áthelyezett három javaslat a következőket tartalmazza:

- Az **MFA-t engedélyezni kell az előfizetés olvasási engedéllyel rendelkező fiókjain** (eredetileg az "MFA engedélyezése" vezérlőben)
- Az **olvasási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből** (eredetileg a "hozzáférés és engedélyek kezelése" vezérlőben)
- Az **előfizetéshez legfeljebb 3 tulajdonost kell kijelölni** (eredetileg a "hozzáférés és engedélyek kezelése" vezérlőben)

A vezérlőhöz hozzáadott két új javaslat a következő:

- **A vendég konfigurációs bővítményt a Windows rendszerű virtuális gépekre (előzetes verzió) kell telepíteni** – a [Azure Policy vendég konfigurációjának](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration) használatával a virtuális gépeken belül láthatók a kiszolgálók és az alkalmazások beállításai (csak Windows).

- A **Windows Defender Exploit Guard védelmet engedélyezni kell a gépeken (előzetes verzió)** – a Windows Defender Exploit Guard kihasználja a Azure Policy vendég konfigurációs ügynököt. A kiaknázási Gárda négy összetevőből áll, amelyek célja, hogy zárolják az eszközöket a különböző támadási vektorok és a kártevők elleni támadásokban leggyakrabban használt viselkedések blokkolásával, miközben lehetővé teszik a vállalatok számára a biztonsági kockázatok és a termelékenységi követelmények kiegyensúlyozását (csak Windows).

További információ a Windows Defender Exploit Guard-védelemről a biztonsági [rés kiaknázására szolgáló szabályzat létrehozása és üzembe helyezése című](https://docs.microsoft.com/mem/configmgr/protect/deploy-use/create-deploy-exploit-guard-policy)témakörben.

További információ a biztonsági ellenőrzésekről: [bővített biztonságos pontszám (előzetes verzió)](secure-score-security-controls.md).



### <a name="custom-policies-with-custom-metadata-are-now-generally-available"></a>Az egyéni metaadatokkal rendelkező egyéni szabályzatok már általánosan elérhetők

Az egyéni szabályzatok mostantól a Security Center javaslatok, a biztonságos pontszám és a szabályozási megfelelőségi szabványok irányítópultjának részét képezik. Ez a funkció mostantól általánosan elérhető, és lehetővé teszi a szervezet biztonsági értékelésének kiterjesztését Security Center. 

Hozzon létre egy egyéni kezdeményezést az Azure Policy-ben, adja hozzá a szabályzatokat, és helyezze be Azure Security Centerba, és jelenítse meg javaslatként.

Most hozzáadjuk az egyéni ajánlási metaadatok szerkesztésének lehetőségét is. A metaadat-beállítások közé tartozik a súlyosság, a Szervizelési lépések, a fenyegetésekkel kapcsolatos információk és egyebek.  

További információk az [Egyéni javaslatok részletes információkkal történő növeléséről](custom-security-policies.md#enhancing-your-custom-recommendations-with-detailed-information).



### <a name="crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection"></a>Összeomlási memóriakép-elemzési képességek migrálása a fájlok közötti támadás észlelése érdekében 

Integráljuk a Windows Crash dump Analysis (CDA) észlelési funkcióit a [fájlok közötti támadás észlelésére](https://docs.microsoft.com/azure/security-center/threat-protection#windows-fileless). A fájlok közötti támadás észlelésének elemzése a következő biztonsági riasztások továbbfejlesztett verzióit biztosítja a Windows rendszerű gépekhez: kód befecskendezése felderített, álcázott Windows-modul, észlelt Héjkód, és gyanús kódrészletet észlelt.

Az áttérés néhány előnye:

- **Proaktív és kellő időben kártevő szoftverek észlelése** – a CDA megközelítése arra vár, hogy összeomlik, majd futtatja az elemzést a kártékony összetevők kereséséhez. A fájlok közötti támadás észlelésével a memóriában tárolt fenyegetések proaktív módon azonosíthatók a futás közben. 

- Bővített **riasztások** – a fájlokkal nem rendelkező támadások észlelésével kapcsolatos biztonsági riasztások közé tartoznak a CDA-ból nem elérhető bővítések, például az aktív hálózati kapcsolatok adatai. 

- **Riasztások összesítése** – ha a CDA több támadási mintát észlelt egyetlen összeomlási memóriaképen belül, több biztonsági riasztást váltott ki. A fájl nélkül történő támadás észlelése az összes azonosított támadási mintát egyetlen riasztásba ötvözi, így nem kell több riasztást összekapcsolni.

- A **log Analytics munkaterületre vonatkozó kisebb követelmények** – a potenciálisan bizalmas adatokat tartalmazó összeomlási memóriaképek többé nem lesznek feltöltve a log Analytics-munkaterületre.



## <a name="april-2020"></a>2020. április

Az áprilisi frissítések a következők:
- [A dinamikus megfelelőségi csomagok már általánosan elérhetők](#dynamic-compliance-packages-are-now-generally-available)
- [A Azure Security Center ingyenes szinten már szerepelnek a személyazonossággal kapcsolatos javaslatok](#identity-recommendations-now-included-in-azure-security-center-free-tier)


### <a name="dynamic-compliance-packages-are-now-generally-available"></a>A dinamikus megfelelőségi csomagok már általánosan elérhetők

A Azure Security Center szabályozási megfelelőségi irányítópult mostantól tartalmazza a **dinamikus megfelelőségi csomagokat** (mostantól általánosan elérhető) a további iparági és szabályozási szabványok nyomon követéséhez.

A dinamikus megfelelőségi csomagok hozzáadhatók az előfizetéshez vagy a felügyeleti csoportjához a Security Center biztonsági házirend lapról. Ha standard vagy teljesítménytesztet készített elő, a szabvány a szabályzatoknak megfelelő megfelelőségi irányítópulton jelenik meg, amely az értékelésként leképezett összes megfelelőségi adattal együtt szerepel. A rendszer letölthetővé teszi az előkészített szabványok bármelyikének összegző jelentését.

Most hozzáadhat olyan szabványokat, mint például a:

- **NIST SP 800-53 R4**
- **SWIFT CSP CSCF – v2020**
- **Egyesült Királyság hivatalos és egyesült királysági NHS**
- **Canada Federal PBMM**
- **Azure CIS 1.1.0 (új)** (az Azure CIS 1.1.0 teljes körű képviselete)

Emellett a közelmúltban hozzáadta az **Azure biztonsági teljesítménytesztet**, a Microsoft által készített Azure-specifikus iránymutatásokat a biztonsági és megfelelőségi ajánlott eljárásokhoz a közös megfelelőségi keretrendszerek alapján. Az irányítópulton további szabványok is támogatottak lesznek, amint azok elérhetővé válnak.  
 
További információ [a szabványok készletének testreszabásáról a szabályozási megfelelőségi irányítópulton](update-regulatory-compliance-packages.md).


### <a name="identity-recommendations-now-included-in-azure-security-center-free-tier"></a>A Azure Security Center ingyenes szinten már szerepelnek a személyazonossággal kapcsolatos javaslatok

A Azure Security Center ingyenes szinten már általánosan elérhetők az identitásra és a hozzáférésre vonatkozó biztonsági javaslatok. Ez annak a erőfeszítésnek a részét képezi, hogy a Cloud Security testtartás-felügyeleti (CSPM) funkciók ingyenesek legyenek. Eddig ezek a javaslatok csak a standard díjszabási szinten voltak elérhetők.

Az identitásra és a hozzáférésre vonatkozó javaslatok például a következők:

- "A többtényezős hitelesítést engedélyezni kell az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező fiókokon."
- "Az előfizetéshez legfeljebb három tulajdonost kell kijelölni."
- "Az elavult fiókokat el kell távolítani az előfizetésből."

Ha előfizetése van az ingyenes díjszabási szinten, a biztonsági pontszámok hatással lesznek a változásokra, mivel soha nem értékelték a személyazonosságát és a hozzáférés biztonságát.

További információ az [identitással és a hozzáférési javaslatokkal](recommendations-reference.md#recs-identity)kapcsolatban.

További információ az [identitás és a hozzáférés figyeléséről](security-center-identity-access.md).


## <a name="march-2020"></a>2020. március

A márciusi frissítések a következők:
- [A Munkafolyamat-automatizálás mostantól általánosan elérhető](#workflow-automation-is-now-generally-available)
- [Azure Security Center integrációja a Windows felügyeleti központtal](#integration-of-azure-security-center-with-windows-admin-center)
- [Az Azure Kubernetes szolgáltatás védelme](#protection-for-azure-kubernetes-service)
- [Továbbfejlesztett, igény szerinti élmény](#improved-just-in-time-experience)
- [Két biztonsági javaslat elavult webalkalmazásokhoz](#two-security-recommendations-for-web-applications-deprecated)


### <a name="workflow-automation-is-now-generally-available"></a>A Munkafolyamat-automatizálás mostantól általánosan elérhető

A Azure Security Center munkafolyamat-automatizálási funkciója már általánosan elérhető. Ezzel a megoldással automatikusan aktiválhatja Logic Apps biztonsági riasztásokra és javaslatokra. Emellett manuális eseményindítók is elérhetők a riasztásokhoz és az összes olyan javaslathoz, amelynél elérhető a gyors javítás lehetőség.

Minden biztonsági program több munkafolyamatot tartalmaz az incidensek megválaszolásához. Ezek a folyamatok magukban foglalhatják az érintett érintett felek értesítését, a módosítási felügyeleti folyamat indítását és az adott szervizelési lépések alkalmazását. A biztonsági szakértők azt ajánlják, hogy az eljárások több lépésének automatizálására legyen lehetőség. Az Automation csökkenti a terhelést, és javíthatja a biztonságot azáltal, hogy a folyamat lépéseinek gyors, konzisztens és az előre meghatározott követelmények szerint kell történnie.

A munkafolyamatok futtatásának automatikus és manuális Security Center funkcióival kapcsolatos további információkért lásd: [munkafolyamat-automatizálás](workflow-automation.md).

További információ a [Logic apps létrehozásáról](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).


### <a name="integration-of-azure-security-center-with-windows-admin-center"></a>Azure Security Center integrációja a Windows felügyeleti központtal

Mostantól közvetlenül a Azure Security Center helyezheti át a helyszíni Windows-kiszolgálókat a Windows felügyeleti központból. A Security Center ezután az egyetlen üvegtábla lesz a Windows felügyeleti központ összes erőforrásának (beleértve a helyszíni kiszolgálókat, a virtuális gépeket és a további Pásti munkaterheléseket) biztonsági információinak megtekintésére.

Miután áthelyezett egy kiszolgálót a Windows felügyeleti központból a Azure Security Centerba, a következőket teheti:

- Tekintse meg a biztonsági riasztásokat és javaslatokat a Windows felügyeleti központ Security Center bővítményében.
- Tekintse meg a biztonsági állapotot, és kérje le a Windows felügyeleti központ felügyelt kiszolgálóinak további részletes információit a Azure Portalon (vagy egy API-n keresztül) található Security Centerban.

További információ a [Azure Security Center integrálásáról a Windows felügyeleti központba](windows-admin-center-integration.md).


### <a name="protection-for-azure-kubernetes-service"></a>Az Azure Kubernetes szolgáltatás védelme

Azure Security Center kibővíti a tárolók biztonsági funkcióit az Azure Kubernetes szolgáltatás (ak) védelmére.

A népszerű, nyílt forráskódú platform Kubernetes olyan széles körben vezették be, hogy most már iparági szabványnak számít a tárolók összehangolása. Ez a széleskörű megvalósítás ellenére még mindig hiányzik a Kubernetes-környezet biztonságossá tételével kapcsolatos megértés. Egy tároló alkalmazás támadási felületének védelme szaktudást igényel annak biztosításához, hogy az infrastruktúra biztonságosan legyen konfigurálva, és folyamatosan figyelje a potenciális fenyegetéseket.

A Security Center védelmi szolgáltatás a következőket tartalmazza:

- **Felderítés és láthatóság** – a felügyelt AK-példányok folyamatos felderítése a Security Center regisztrált előfizetéseken belül.
- **Biztonsági javaslatok** – gyakorlati ajánlások az AK-ra vonatkozó biztonsági eljárások betartása érdekében. Ezek a javaslatok a biztonságos pontszám részét képezik, így biztosítva, hogy a szervezete biztonsági helyzete része legyen. Egy példa arra, hogy a "szerepköralapú hozzáférés-vezérlést kell használni a Kubernetes Service-fürthöz való hozzáférés korlátozásához".
- **Veszélyforrások elleni védelem** – az AK üzembe helyezésének folyamatos elemzése révén Security Center riasztást küld, amely a gazdagép és az AK-fürt szintjén észlelt fenyegetésekkel és rosszindulatú tevékenységgel kapcsolatos.

További információ az [Azure Kubernetes Services és a Security Center integrálásáról](azure-kubernetes-service-integration.md).

További információ [a Security Center tároló biztonsági funkcióival](container-security.md)kapcsolatban.


### <a name="improved-just-in-time-experience"></a>Továbbfejlesztett, igény szerinti élmény

A felügyeleti portok védelmét biztosító, a Azure Security Center funkcióit, műveleteit és felhasználói felületét a következőképpen fejlesztettük ki: 

- **Indoklási mező** – ha egy virtuális GÉPHEZ (VM) való hozzáférést kér a Azure Portal igény szerinti oldalán, egy új opcionális mező is elérhető, amely a kérelem indoklását adja meg. A mezőben megadott információ nyomon követhető a tevékenység naplójában. 
- A **redundáns igény szerinti (JIT) szabályok automatikus karbantartása** – amikor egy JIT-házirendet frissít, a rendszer automatikusan futtat egy karbantartó eszközt a teljes szabályrendszert érvényességének ellenőrzéséhez. Az eszköz eltéréseket keres a szabályzat szabályai és a NSG lévő szabályok között. Ha a tisztítási eszköz nem megfelelőnek találja, akkor meghatározza az okot, és ha ez biztonságos, eltávolítja a már nem szükséges beépített szabályokat. A tisztább soha nem törli a létrehozott szabályokat. 

További információ [az JIT-hozzáférési szolgáltatásról](security-center-just-in-time.md).


### <a name="two-security-recommendations-for-web-applications-deprecated"></a>Két biztonsági javaslat elavult webalkalmazásokhoz

A webalkalmazásokkal kapcsolatos két biztonsági javaslat elavult: 

- A IaaS-NSG lévő webalkalmazások szabályait meg kell erősíteni.
    (Kapcsolódó szabályzat: a IaaS webalkalmazásaihoz tartozó NSG-szabályokat meg kell erősíteni)

- A App Serviceshoz való hozzáférést korlátozni kell.
    (Kapcsolódó házirend: a App Services elérését korlátozni kell [előzetes verzió])

Ezek a javaslatok többé nem jelennek meg a javaslatok Security Center listájában. A kapcsolódó házirendeket a rendszer nem fogja tartalmazni a "Security Center default" nevű kezdeményezésben.

További információ a [biztonsági javaslatokról](recommendations-reference.md).

