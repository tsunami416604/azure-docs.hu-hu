---
title: Azure Health Analytics Blueprint
description: Útmutató a HIPAA/HITRUST egészségügyi Analytics tervrajz üzembe helyezése
services: security
documentationcenter: na
author: RajeevRangappa
ms.assetid: 26566e0a-0a54-49f4-a91d-48e20b7cef71
ms.service: security
ms.topic: article
ms.date: 07/23/2018
ms.author: rarangap
ms.openlocfilehash: ca844c89b657bc3286f3472af3acbf937ef1e20f
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2018
ms.locfileid: "52891061"
---
# <a name="azure-security-and-compliance-blueprint---hipaahitrust-health-data-and-ai"></a>Azure biztonsági és megfelelőségi terv – a HIPAA/HITRUST-állapotadatok és mesterséges Intelligencia

## <a name="overview"></a>Áttekintés

**Az Azure biztonsági és -megfelelőségi terv – a HIPAA/HITRUST-állapotadatok és mesterséges Intelligencia kínál egy kulcsrakész megoldás üzembe helyezése egy Azure PaaS és IaaS bemutatják, hogyan lehet betöltési, tárolása, elemzése, kezelheti, identitás, és biztonságosan a egészségügyi adatok, miközben a megoldások üzembe helyezése tudnak iparági megfelelőségi előírások teljesítése érdekében. A tervezet segítségével felgyorsíthatja a felhőre való áttérés és kihasználtságát az ügyfelek, amelyek a szabályozott adatokkal.**

Az Azure biztonsági és -megfelelőségi terv – a HIPAA/HITRUST egészségügyi adatok és AI-terv biztosít eszközöket és útmutatás nyújtása a telepítése egy biztonságos, Health Insurance Portability és Accountability Act (HIPAA) és egészségügyi információk megbízható Alliance (HITRUST) készen áll Platform--szolgáltatásként (PaaS) környezetet fürtjét, tárolása, elemzése és személyes, mind a nem személyes orvosi egy biztonságos, többrétegű felhőalapú környezetben, egy teljes körű megoldás telepített implementálására. 

IaaS-megoldások lesz bemutatják, hogyan lehet egy helyszíni SQL-alapú megoldás áttelepítése az Azure-ba, és a egy emelt szintű hozzáférési szintű munkaállomás (PAW) biztonságosan kezelheti a felhő alapú szolgáltatások és megoldások megvalósításához. Az IaaS SQL Server-adatbázis hozzáadása lehetséges Kísérletezési adatok importálása az SQL IaaS virtuális gépre, valamint, hogy a virtuális gép MSI használ hitelesített hozzáféréssel használhatja egy SQL Azure PaaS-szolgáltatás. Mindkét ezen közös referenciaarchitektúra bemutatja, és egyszerűsíti a Microsoft Azure bevezetését. A megadott architektúra a felhőalapú megközelítés a terheket és a központi telepítési költségek csökkentéséhez kérő szervezetek igényeit megoldást mutatja be.

![](images/components.png)

A megoldás célja egy minta adatkészlet gyors Healthcare együttműködési erőforrások (FHIR), az egészségügyi információk cseréje elektronikus úton, globális szabvány használatával formázott felhasználását, és biztonságos módon tárolja. Ügyfelek felhasználhatja az Azure Machine Learning Studio hatékony üzleti intelligencia eszközök és az analitikai tekintse át a mintaadatok rmse előnyeit. Például, hogy milyen típusú kísérlet Azure Machine Learning Studio megkönnyítheti a tervezet minta adatkészlet, a parancsfájlokat és a egy betegnek a kórházi létesítménybe vesznek hossza előrejelzésére szolgáló eszközök is tartalmaz. 

Ez a megoldás alkalmas szolgál az ügyfelek számára állítsa be az adott követelményekhez, új Azure Machine learning-példakísérleteket megoldásában mindkét klinikai és a működési használati esetek fejlesztése moduláris alapjaként. Arra tervezték, hogy biztonságos és megfelelő telepítésekor; azonban ügyfelei felelősek az megfelelően szerepkörök konfigurálása, és minden egyéb módosítás végrehajtása. Vegye figyelembe a következőket:

-   A megoldás lehetővé teszi az ügyfelek számára a Microsoft Azure használata egy HITRUST alapterv és a HIPAA-környezet.

-   Bár a tervezet úgy lett kialakítva, hogy illeszkedjenek a HIPAA és a HITRUST (keresztül a közös biztonsági keretrendszert--CSF), azt kell nem megfelelőnek tekinthető mindaddig, amíg egy külső auditor szerint a HIPAA és a HITRUST-minősítési követelmények által hitelesített.

-   Ügyfelek megoldással, ez a alapvető architektúra használatával létrehozott megfelelő biztonsági és megfelelőségi felülvizsgálatai elvégzéséért felelős.

## <a name="deploying-the-automation"></a>Az automatizálás telepítése

- A megoldás üzembe helyezéséhez kövesse az ismertetett a [üzembe helyezési útmutatót](https://github.com/Azure/Health-Data-and-AI-Blueprint/blob/master/deployment.md). 

- Röviden, ez a megoldás működéséről, tekintse meg ezt [videó](https://aka.ms/healthblueprintvideo) pedig elmagyarázza és annak üzembe helyezési bemutatására.

- Gyakran feltett kérdést találhat a [– gyakori kérdések](https://aka.ms/healthblueprintfaq) útmutatást.

-   **Architekturális diagramja.** Az ábrán látható a referenciaarchitektúrát a tervezet használt, és a példát használja használatieset-forgatókönyvek.

-   [IaaS-bővítményt](https://github.com/Azure/Health-Data-and-AI-Blueprint/blob/master/README%20IaaS.md) ebben a megoldásban bemutatjuk, hogyan egy helyszíni SQL-alapú megoldás áttelepítése az Azure-ba, és a egy Privieged hozzáférési szintű munkaállomásokat biztonságosan kezelheti a felhő-bsed szolgáltatások és megoldások megvalósításához. 

## <a name="solution-components"></a>Megoldás-összetevők


Alapvető architektúrája a következő összetevőkből áll:

-   **[Fenyegetések modellezése](https://aka.ms/healththreatmodel)**  egy átfogó fenyegetések modellezése tm7 formátumban van megadva a segítségével a [Microsoft Threat Modeling Tool](https://www.microsoft.com/en-us/download/details.aspx?id=49168), a megoldás összetevői látható, a közti adatfolyamok, és a megbízhatósági kapcsolat határok. A modell segítségével az ügyfelek megismerhetik a pontokat a rendszer infrastruktúra esetleges kockázat, Machine Learning Studio-összetevők vagy egyéb módosítások fejlesztése során.

-   **[Ügyfél megvalósítása mátrix](https://aka.ms/healthcrmblueprint)**  A Microsoft Excel-munkafüzet HITRUST vonatkozó követelményeit, és azt ismerteti, hogyan a Microsoft és az ügyfél a mindegyiknél teljesítéséért felelős.

-   **[Állapotának áttekintése.](https://aka.ms/healthreviewpaper)** A megoldás felülvizsgálták Coalfire Systems, Inc. Az egészségügyi megfelelőségi (a HIPAA és a HITRUST) felülvizsgálati és megvalósítási útmutatást nyújt egy auditor\'s át kell tekinteni a megoldás és a egy éles használatra kész telepítéshez a tervezet átalakítása szempontjai.

# <a name="architectural-diagram"></a>Architekturális diagramja


![](images/ra2.png)

## <a name="roles"></a>Szerepkörök


A tervezet határozza meg a két szerepkört azon rendszergazda felhasználók (operátorok), és a felhasználók számára három szerepkört a kórházvezetés és a betegellátásban. A hatodik szerepkör van definiálva egy auditor kiértékelni a HIPAA és más szabályozások előírásainak betartását. Az Azure szerepköralapú hozzáférés-vezérlés (RBAC) lehetővé teszi, hogy a megoldás beépített és egyéni szerepkör segítségével minden felhasználó pontosan koncentrálhassanak a kezelését. Lásd: [szerepköralapú hozzáférés-vezérlés az Azure Portalon – első lépések](https://docs.microsoft.com/azure/role-based-access-control/overview) és [Azure szerepköralapú hozzáférés-vezérlés beépített szerepkörei](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) RBAC szerepkörök és engedélyek kapcsolatos részletes információkat.

### <a name="site-administrator"></a>Hely rendszergazdája


A hely rendszergazdájának felelős az ügyfél Azure-előfizetést. Általános üzembe helyezését, de nem férhetnek hozzá a betegek kartonjai.

-   Alapértelmezett szerepkör-hozzárendeléseit: [tulajdonosa](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner)

-   Egyéni szerepkör-hozzárendeléseit: N/A

-   Hatókör: előfizetés

### <a name="database-analyst"></a>Adatbázis-elemző

Az adatbázis-elemző felügyeli, az SQL Server-példány és az adatbázist.
A betegek kartonjai nincs hozzáféréssel rendelkeznek.

-   Beépített szerepkör-hozzárendeléseit: [SQL DB Contributor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-db-contributor), [SQL Server Közreműködője](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-server-contributor)

-   Egyéni szerepkör-hozzárendeléseit: N/A

-   Hatókör: erőforráscsoport

 ### <a name="data-scientist"></a>Adatelemzési szakértő


Az adatszakértő működik, az Azure Machine Learning Studio. Ezek is importálása, exportálása, adatok kezelése és jelentések futtatása. Az adatszakértő férhet hozzá a betegek adatai, de nem rendelkezik rendszergazdai jogosultságokkal.

-   Beépített szerepkör-hozzárendeléseit: [Tárfiók-közreműködő](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-contributor)

-   Egyéni szerepkör-hozzárendeléseit: N/A

-   Hatókör: erőforráscsoport

### <a name="chief-medical-information-officer-cmio"></a>Informatikai Medical igazgató (Fontos)


Az egészségügyi Informatikai igazgató a informatikusait/technológiai szakértőit az egészségügyi egészségügyi szervezet közötti szakadékot feladata. A feladataik rendszerint elemzés segítségével határozza meg, ha erőforrások kerülnek lefoglalásra megfelelően a szervezeten belül.

-   Beépített szerepkör-hozzárendeléseit: nincs

### <a name="care-line-manager"></a>Betegápolási vezető


A betegápolási vezető van közvetlenül is részt vesz a betegek ellátásában.
Az e munkakörben dolgozók feladata, hogy nyomon kövessék az egyes betegek állapotát, valamint garantálják, hogy a dolgozók megfeleljenek a hozzájuk rendelt betegek ellátási igényeinek. A betegápolási vezető felelős hozzáadása és a betegek kartonjai frissítése.

-   Beépített szerepkör-hozzárendeléseit: nincs

-   Egyéni szerepkör-hozzárendeléseit: jogosultsággal ehhez mindkét beteg már a Betegfelvétel, HealthcareDemo.ps1 futtatásához és lezárására.

-   Hatókör: erőforráscsoport

### <a name="auditor"></a>Auditor


Az auditor kiértékeli megfelelőség szempontjából a megoldás. A hálózat nincs közvetlen hozzáféréssel rendelkeznek.

-   Beépített szerepkör-hozzárendeléseit: [olvasó](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader)

-   Egyéni szerepkör-hozzárendeléseit: N/A

-   Hatókör: előfizetés

## <a name="example-use-case"></a>Példa használati esetekhez


Ez a megoldás részét képező példa használati eset azt mutatja be, hogyan a tervezet használható engedélyezése a machine learning és a felhőalapú egészségügyi adatok elemzése. Contosoclinic egy kis kórházi, az Egyesült Államokban található. A kórházi hálózati rendszergazdáknak is érdemes az Azure Machine Learning Studio használatának jobb egy páciens ápolás felvételéről hatékonyságának növeléséhez és ellátás azt is lehetővé teszi a minőség fokozása érdekében idején előre jelezni.

### <a name="predicting-length-of-stay"></a>Tartózkodás időtartamának előrejelzése


A példa használati esetekhez Azure Machine Learning Studio használatával előre jelezni egy újonnan bevezetett beteg kórházi ápolás összehasonlításával összesített előzményadatok előző betegek származnak, betegek bevitel orvosi részleteit.
A tervezet anonimizált orvosi bemutatásához a megoldás betanítást, prediktív képességeit nagy készletét tartalmazza. Az éles környezet esetében ügyfelek lenne használja saját rekordokat a megoldást a pontos előrejelzések tükröző a környezetben, a létesítmények és a betegek egyedi részleteit.

### <a name="users-and-roles"></a>Felhasználók és szerepkörök


**Hely rendszergazdája--Alex**

*E-mail cím: Alex\_SiteAdmin*

Alex a feladat, amely egy helyszíni hálózat kezelésének terhe csökkentése és a felügyeleti költségek csökkentése technológiák kiértékelheti, hogy. Alex rendelkezik lett szeretném összehasonlítani az Azure egy kis ideig, de rendelkezik struggled konfigurálása a szolgáltatásokat, amelyek kell beteg adatokat tárolni a felhőben, a HiTrust megfelelőségi követelmények teljesítése érdekében. Alex ki van választva az Azure állapota AI telepíthet megfelelőségi használatra kész állapot megoldást, a HiTrust az ügyfelek igényeinek megfelelően a követelményeknek már képes kezelni.

**Statisztikusnak Perényi**

*E-mail cím: Perényi\_DataScientist*

Perényi használatával, és elemzéséhez, orvosi betegellátásban betekintést biztosít a modellek létrehozására van. Perényi SQL és statisztikai R programozási nyelv használatával saját modelleket hozhat létre.

**Adatbázis-elemző--Danny**

*E-mail cím: Danny\_DBAnalyst*

Danny bármit fő forduljon a Microsoft SQL Server, amely a betegek adatokat tárolja a Contosoclinic kapcsolatban. Danny egy tapasztalt SQL Server-rendszergazdához, aki rendelkezik nemrég megismerkedhet az Azure SQL Database.

**Informatikai Medical igazgató – Caroline**

Chris a betegápolási vezető, és az Adatszakértő Perényi meghatározni, hogy milyen tényezők befolyásolják a betegek kórházi ápolás Caroline dolgoznak.
Caroline a hossz – a tartózkodási (LOS) megoldás által létrehozott javaslatok alapján határozza meg, ha erőforrások kerülnek lefoglalásra megfelelően a kórházi hálózatban. Ha például az irányítópulttal ebben a megoldásban foglalt.

**Chris gondoskodik a betegápolási vezető--**

*E-mail cím: Chris\_CareLineManager*

Az egyéni közvetlenül betegek már a betegfelvétel és Contosoclinic, a kibocsátás kezeléséért Chris használ a LOS megoldás által generált előrejelzések győződjön meg arról, hogy elérhetők-e megfelelő gondossággal nyújt betegeket, míg azok tartózkodó a létesítmény.

**Auditor--Han**

*E-mail cím: Han\_Auditor*

Han egy hitelesített auditor rendelkező, az ISO, SOC és a HiTrust naplózásának élmény. Han volt felvett Contosoclinc a hálózati áttekintéséhez. Han tekintheti át az ügyfél felelőssége mátrix a megoldáshoz mellékelt győződjön meg arról, hogy a tervezet és LOS megoldás tárol, dolgoz fel, és jelenítheti meg a személyes adatok használhatók.


# <a name="design-configuration"></a>Tervezési konfiguráció


Ez a szakasz részletesen a meghatározott alapértelmezett konfigurációk és a tervezet vázolt a beépített biztonsági intézkedéseket:

- **BETÖLTÉS** FHIR adatforrás beleértve nyers adatforrások
- **TÁROLÓ** bizalmas adatokat
- **Az elemzés** és az eredmények előrejelzése
- **JELENTÉSEK** az eredmények és az előrejelzések
- **IDENTITÁS** felügyeleti megoldás
- **BIZTONSÁGI** funkciók engedélyezve


## <a name="identity"></a>IDENTITÁS 

### <a name="azure-active-directory-and-role-based-access-control-rbac"></a>Az Azure Active Directory és a szerepköralapú hozzáférés-vezérlés (RBAC)


**Hitelesítés:**

-   [Az Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) van a Microsoft\'s több-bérlős felhőalapú címtár- és identitáskezelési szolgáltatás. A megoldás összes felhasználó jöttek létre az Azure Active Directoryban, beleértve a felhasználók az SQL-adatbázis elérésére.



-   Az alkalmazás-hitelesítést az Azure AD használatával történik. További információkért lásd: [alkalmazások integrálása az Azure Active Directory](/azure/active-directory/develop/active-directory-integrating-applications).

-   [Az Azure Active Directory Identity Protection](/azure/active-directory/active-directory-identityprotection) észlel, a szervezet identitásait érintő lehetséges biztonsági résekről, konfigurálja az automatikus reakciók, a szervezet identitásait, kapcsolódó észlelt gyanús tevékenységeket és kivizsgálja a gyanús eseményekre, és a problémák megoldásához szükséges lépéseket tart.

-   [Az Azure szerepköralapú hozzáférés-vezérlés (RBAC)](/azure/role-based-access-control/role-assignments-portal) lehetővé teszi, hogy pontosan fókuszban lévő hozzáférés-kezelés az Azure-hoz. Előfizetéshez való hozzáférés korlátozva, az előfizetés rendszergazdája, és az Azure Key Vault hozzáférés korlátozódik a webhely rendszergazdájához. Az erős jelszavak (felső és alsó legalább egy betűt, számot és speciális karaktert a minimális 12 karakter) szükség.

-   A multi-factor authentication használata támogatott, ha a - enableMFA kapcsoló üzembe helyezése során engedélyezve van.

-   Jelszavak Ha engedélyezve van a - enableADDomainPasswordPolicy kapcsoló üzembe helyezése során 60 nap után lejár.

**Szerepkörök:**

-   A megoldás használnak az [beépített szerepkörök](/azure/role-based-access-control/built-in-roles) erőforrásokhoz való hozzáférés kezelésére.

-   Minden felhasználó rendelt konkrét beépített szerepkörök alapértelmezés szerint.

### <a name="azure-key-vault"></a>Azure Key Vault

-   A Key Vaultban tárolt adatokat tartalmazza:

    -   Application insight-kulcs
    -   Betegek adatokat tároló hozzáférési kulcs
    -   Betegek kapcsolati karakterlánc
    -   A betegek adatai tábla neve
    -   Az Azure ML Web Service-végpont
    -   Az Azure Machine Learning szolgáltatás API-kulcs

-   Speciális hozzáférési szabályzatok vannak konfigurálva kell alapon
-   A Key Vault hozzáférési házirendeket a kulcsok és titkos kulcsok minimálisan szükséges engedélyeket
-   Összes kulcsot és titkos kulcsokat a Key Vault lejárati dátummal rendelkezik
-   HSM által védett összes kulcsok a Key Vaultban \[kulcstípus HSM által védett 2048-bites RSA-kulcs =\]
-   Összes felhasználók és identitások szerepkör alapú hozzáférés-vezérlés (RBAC) használatával minimálisan szükséges engedélyeket kapjanak.
-   Alkalmazások nem osztoznak egy Key Vaultot, ha azok megbíznak egymásban és futásidőben az azonos titkos kódokhoz való hozzáférés szükséges
-   Diagnosztikai naplók a Key vault legalább 365 napos megőrzési idővel rendelkező engedélyezve vannak.
-   Engedélyezett titkosítási műveletek kulcsok korlátozva a szükséges kapcsolatok

## <a name="ingest"></a>BETÖLTÉSI 

### <a name="azure-functions"></a>Azure Functions
A megoldás úgy lett kialakítva, használandó [Azure Functions](/azure/azure-functions/) tartózkodási data analytics bemutató használt minta hossza feldolgozásához. Három képességek az a funkciók lettek létrehozva.

**1. Vásárlói adatok phi adatokat tömeges importálása**

Ha a bemutató-parancsfájl használatával. . \\A HealthcareDemo.ps1 a **BulkPatientAdmission** leírt módon váltson **üzembe helyezése és futtatása a bemutató** a következő feldolgozási folyamat végrehajtása:
1. **Az Azure Blob Storage** -tárolóba feltöltött betegek adatokat .csv fájlba minta
2. **Event Grid** -eseményt tesz közzé adatokat az Azure-függvény (tömeges importálás – blob esemény)
3. **Azure-függvény** - elvégzi a feldolgozást, és tárolja az adatokat a biztonságos függvény használatával az SQL-tároló - esemény (írja be a; blob URL-címe)
4. **Az SQL DB** - besorolási címkék a betegek adatait az adatbázis-tároló, és a gépi Tanulási folyamat a tanítási kísérlet ehhez megkezdődik.

![](images/dataflow.png)

Az azure-függvény ezen kívül úgy lett kialakítva, és a minta adatkészletben, a következő címkék használatával kijelölt bizalmas adatok védelme érdekében:
- dataProfile = > "ePHI"
- tulajdonos = > \<hely felügyeleti egyszerű felhasználónév\>
- környezet = > "Próbaüzem"
- részleg = > "Globális ökoszisztéma", a címkézés lett alkalmazva a mintaadatkészlettel, ahol beteg "nevek" egyszerű szövegként azonosított.

**2. Új betegek beléptetéséhez**

Ha a bemutató-parancsfájl használatával. . \\A HealthcareDemo.ps1 a **BulkPatientadmission** leírt módon váltson **üzembe helyezése és futtatása a bemutató** a következő feldolgozási folyamat végrehajtása: ![](images/securetransact.png) 
 **1. Azure-függvény** aktiválódik, és a függvény vonatkozó kérések egy [tulajdonosi jogkivonat](/rest/api/) az Azure Active Directoryból.

**2. A Key Vault** kért a titkos kulcs, amely a kért jogkivonatot társítva van.

**3. Azure-szerepkörök a kérelem érvényesítéséhez, és engedélyezze a Key Vault hozzáférési kérelmet.

**4. A Key Vault** karakterláncot ad vissza, a titkos kulcsot, ebben az esetben az SQL DB-kapcsolat.

**5. Azure-függvény** , biztonságosan csatlakozhat az SQL-adatbázis kapcsolati karakterláncát használja, és továbbra is fennáll, további feldolgozás ePHI adatok tárolására.

A storage, az adatok elérése, egy közös API-séma implementálása a következő gyors Healthcare együttműködési erőforrások (FHIR, fire erősebb). A függvény lett megadva, a következő FHIR exchange elemeket:

-   [Betegek séma](https://www.hl7.org/fhir/patient.html) tartalmaz, a "ki" becsléseknek információt.

-   [Megfigyelési séma](https://www.hl7.org/fhir/observation.html) magában foglalja a központi eleme az egészségügyben, támogatja a diagnosztikát, előrehaladásának figyeléséhez, alapkonfigurációk és minták segítségével és demográfiai jellemzőit is rögzítheti. 

-   [Ütközik a sémában](https://www.hl7.org/fhir/encounter.html) tartalmazza a típusú ütközik ambulatory, sürgős, például otthoni állapot, a beteg és a virtuális ütközik.

-   [A feltétel séma](https://www.hl7.org/fhir/condition.html) feltétel, a probléma, elemzés céljából, vagy más esemény, helyzet, a probléma vagy klinikai demonstráció létrehozásában, amely potenciálisan veszélyes szintre emelkedett kapcsolatos részletes információkat tartalmaz.  



### <a name="event-grid"></a>Event Grid


A megoldás az Azure Event Grid, egy egyetlen szolgáltatás kezelése, az összes esemény bármilyen forrásból bármilyen cél felé való útválasztását biztosító támogatja:

-   [Biztonság és hitelesítés](/azure/event-grid/security-authentication)

-   [Szerepköralapú hozzáférés-vezérlés](/azure/event-grid/security-authentication#management-access-control) különféle felügyeleti műveletek, például ajánlati esemény-előfizetések, újakat hozna létre és a kulcs létrehozásakor

-   Naplózás

## <a name="store"></a>TÁROLÓ 

### <a name="sql-database-and-server"></a>Az SQL Database és a kiszolgáló 


-   [Transzparens adattitkosítás (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) valós időben titkosítja és az Azure SQL Database, az Azure Key Vaultban tárolt kulcs használatával tárolt visszafejtés biztosít.

-   [SQL-sebezhetőségi felmérés](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) egy egyszerű, Fedezze fel, nyomon követheti, és javíthatja az adatbázis biztonsági réseinek eszköz konfigurálása.

-   [Az SQL Database Threat Detection](/azure/sql-database/sql-database-threat-detection) engedélyezve van.

-   [Az SQL Database naplózási szolgáltatásával](/azure/sql-database/sql-database-auditing) engedélyezve van.

-   [SQL-adatbázis-metrikák és diagnosztikai naplózás](/azure/sql-database/sql-database-metrics-diag-logging) engedélyezve van.

-   [Kiszolgáló - és adatbázisszintű tűzfalszabályok](/azure/sql-database/sql-database-firewall-configure) egyszerűsítették.

-   [Always Encrypted az oszlopok](/azure/sql-database/sql-database-always-encrypted-azure-key-vault) betegek első, a középső és az utolsó nevek védelmére használhatók.
    Ezenkívül az adatbázis oszloptitkosítás is használ az Azure Active Directory (AD) hitelesítheti az alkalmazást az Azure SQL Database.

-   SQL Database és SQL Server elérését az elvét megfelelően van konfigurálva.

-   Csak a szükséges IP-címek engedélyezettek a hozzáférés az SQL-tűzfalon keresztül.

### <a name="storage-accounts"></a>Tárfiókok


-   [Mozgásban lévő adatok továbbítása a TLS/SSL használatával csak](/azure/storage/common/storage-require-secure-transfer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json).

-   Névtelen hozzáférés nem engedélyezett a for containers szolgáltatásban.

-   Riasztási szabályok nyomon követési tevékenység névtelen vannak konfigurálva.

-   HTTPS-kapcsolat szükséges a tárfiók erőforrásainak eléréséhez.

-   Hitelesítési kérelem adatai naplózza, és figyelemmel kísérni.

-   A Blob storage szolgáltatásban tárolt adatok titkosítása.

## <a name="analyze"></a>ELEMZÉSE

### <a name="machine-learning"></a>Machine Learning


- [Naplózás engedélyezve van](/azure/machine-learning/studio/web-services-logging) Machine Learning Studio a web services.
- Használatával [Machine Learning Studio](/azure/machine-learning/studio/what-is-ml-studio) kísérletek, amely lehetővé teszi, hogy a megoldás számára egy előre jelezni fejlődéséhez igényel.

## <a name="security"></a>BIZTONSÁG

### <a name="azure-security-center"></a>Azure Security Center
- [Az Azure Security Center](https://azure.microsoft.com/services/security-center/) központi az Azure-erőforrások biztonsági állapotát jeleníti meg. Egy pillanat alatt ellenőrizheti, hogy a megfelelő biztonsági ellenőrzések, és megfelelően konfigurálva, és gyorsan azonosíthatja a figyelmet igénylő erőforrásokat. 

- [Az Azure Advisor](/azure/advisor/advisor-overview) egy személyre szabott felhőalapú tanácsadó, amely segítséget nyújt az Azure-környezetek optimalizálására vonatkozó ajánlott eljárásokat követve. Az Advisor elemzi az erőforrások konfiguráció- és használattelemetriáját, és megoldási javaslatokat tesz, amelyek segítségével javítható az Azure-erőforrások költséghatékonysága, teljesítménye, rendelkezésre állása és biztonsága.

### <a name="application-insights"></a>Application Insights
- [Az Application Insights](/azure/application-insights/app-insights-overview) egy bővíthető alkalmazásteljesítmény-felügyeleti (APM) szolgáltatás webfejlesztőknek, több platformon. Az élő webalkalmazásának figyelésére használhatja. Teljesítménnyel kapcsolatos anomáliák észlel. Hatékony elemzőeszközöket tartalmaz, amelyek segítenek a problémák felismerésében, és annak a megértésében, hogy a felhasználók mire használják ténylegesen az alkalmazást. Úgy tervezték, hogy használatával folyamatosan javíthassa a teljesítményt és a használhatóságot.

### <a name="azure-alerts"></a>Azure-riasztások
- [Riasztások kínálnak az Azure-szolgáltatások figyelésére szolgáló módszer, és lehetővé teszi, hogy feltételeket konfiguráljon az adatok. Riasztások értesítések is biztosítanak, amikor egy riasztási feltétel megfelel a monitorozási adatok.

### <a name="log-analytics"></a>Log Analytics
[Log Analytics](/azure/operations-management-suite/operations-management-suite-overview) felügyeleti szolgáltatások gyűjteménye.

-   Munkaterület engedélyezve van a Security Centerben

-   Munkaterület engedélyezve van a munkaterhelés-figyelés

-   Számítási feladatok figyelés engedélyezve van:

    -   Identitás és hozzáférés

    -   Biztonság és naplózás

    -   Azure SQL DB Analytics

    -   [Azure WebApp Analytics](/azure/log-analytics/log-analytics-azure-web-apps-analytics) megoldás

    -   Key Vault-elemzés

    -   Változások követése

-   [Application Insights-összekötő (előzetes verzió)](/azure/log-analytics/log-analytics-app-insights-connector) engedélyezve van

-   [Tevékenység a log analytics](/azure/log-analytics/log-analytics-activity) engedélyezve van
