---
title: Azure Health Analytics Blueprint
description: Útmutatás a HIPAA vagy HITRUST állapotfigyelő Analytics tervezetének telepítéséhez
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 26566e0a-0a54-49f4-a91d-48e20b7cef71
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/27/2018
ms.author: simorjay
ms.openlocfilehash: bdd7dbf4f39529ac76fb496f0d459577e6f929dc
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="azure-security-and-compliance-blueprint---hipaahitrust-health-data-and-ai"></a>Azure biztonsági és megfelelőségi tervezetének - HIPAA/HITRUST állapotadatok és AI

## <a name="overview"></a>Áttekintés

**Az Azure biztonsági és megfelelőségi tervezetének - HIPAA/HITRUST állapotadatok és AI kínál a kulcsrakész megoldás üzembe helyezése az Azure PaaS bemutatni, hogyan biztonságosan betöltési, tárolásához, elemzése, és ugyanakkor nem képes kielégíteni iparági megfelelési egészségügyi adatok kezeléséhez követelmények. Szerkezeti terve segít felgyorsítani felhő elfogadása és kihasználtsági adatok szabályozott rendelkező ügyfelek esetén.**

Az Azure biztonsági és megfelelőségi tervezetének - HIPAA/HITRUST állapotadatok és AI tervezetének biztosít az eszközök és telepítheti a biztonságos útmutatást, egészségügyi biztosítás hordozhatóságáról és elszámolási kötelezettségéről szóló törvény (HIPAA) és egészségügyi adatokat megbízható Alliance (HITRUST) készen áll Platform,--szolgáltatás (PaaS) környezetben választásával dolgozhat fel, a tárolás, a elemzése és a személyes és a nem személyes orvosi rekordok végpont megoldásként telepített biztonságos, többrétegű felhőalapú környezetben való interakció. Bővíthető egy közös referencia-architektúrában, és arra tervezték, hogy egyszerűsítése érdekében a Microsoft Azure elfogadását. A megadott architektúra keresést egy felhőalapú megközelítése az terheket és a központi telepítési költségek csökkentése a szervezetek igényei megoldást mutatja be.

![](images/components.png)

A megoldást arra tervezték, a minta adatkészletet gyors egészségügy együttműködési erőforrások (FHIR), az egészségügyi információcserére elektronikus úton, szabvánnyá fájlrendszerrel formázott fogadni, és biztonságos módon tárolja. Az ügyfelek ezután használhatja Azure Machine Learning hatékony üzleti intelligencia eszközök és az elemzések rajta a mintaadatokra végzett előrejelzések áttekintéséhez előnyeit. Például, hogy milyen típusú Azure Machine Learning megkönnyítheti kísérlet szerkezeti terve mintát adatkészletre, parancsfájlok, és eszközöket tartalmaz a a hossza egy türelmet egy kórháznak létesítményt a személyes előrejelzéséhez. 

Ez tervezetének készült ügyfelek úgy, hogy az egyedi követelményeket, új Azure Machine learning kísérleteket mindkét klinikai és működési használati eset forgatókönyvek megoldására fejlesztése moduláris alapjaként kiszolgálására. Arra tervezték, biztonságos és megfelelő telepítésekor; azonban ügyfelei felelősek az szerepkörök konfigurálása megfelelő, és minden egyéb módosítás megvalósítása. Vegye figyelembe a következőket:

-   Ez tervezetének biztosít a Microsoft Azure használni egy HITRUST ügyfelek alapterv és a HIPAA környezet.

-   Bár szerkezeti terve úgy lett kialakítva, a HIPAA és (a gyakori biztonsági keretrendszeren keresztül--CSF) HITRUST igazítva, akkor nem tekinthető megfelelő, amíg egy külső auditor / HIPAA és HITRUST a hardvertanúsítvány követelményeit által hitelesített.

-   Ügyfelei felelősek a legalapvetőbb architektúra használatával készített megoldások megfelelő biztonsági és megfelelőségi ellenőrzések elvégzéséhez.

## <a name="deploying-the-automation"></a>Az automatizálás telepítése

- A megoldás telepítéséhez kövesse a megjelenő utasításokat az üzembe helyezési útmutatót. 

[![](./images/deploy.png)](https://aka.ms/healthblueprintdeploy)

Nézze meg ezt a gyors áttekintést a megoldás működéséről, [videó](https://aka.ms/healthblueprintvideo) foglalja össze, és a központi telepítés bemutatására.

- A gyakran ismételt kérdések megtalálhatók a [gyakran ismételt kérdések](https://aka.ms/healthblueprintfaq) útmutatást.

-   **Architekturális diagramja.** Az ábrán látható a referencia-architektúrában szerkezeti terve használt, és a példa eset használja.

-   **A központi telepítési sablonok**. Ebben a felállásban [Azure Resource Manager-sablonok](/azure/azure-resource-manager/resource-group-overview#template-deployment) automatikusan központi telepítésével a architektúra összetevői a Microsoft Azure konfigurációs paraméterek megadásával a telepítés során.

-   **[Automatikus központi telepítési parancsfájlok](https://aka.ms/healthblueprintdeploy)**. Ezek a parancsfájlok segítségével, a megoldás üzembe helyezéséhez. A parancsfájlok foglalják magukban:


-   A házirendmodul-telepítésének és [globális rendszergazda](/azure/active-directory/active-directory-assign-admin-roles-azure-portal) telepítési parancsfájl használatával telepítse, és győződjön meg arról, hogy a szükséges PowerShell-modulok és a globális rendszergazdai szerepkörök helyesen vannak konfigurálva. 
-   PowerShell parancsfájl telepítés üzembe helyezéséhez, egy előre elkészített bemutató funkciók tartalmazó .zip fájl keresztül megadott szolgál.

## <a name="solution-components"></a>Megoldás-összetevők


A legalapvetőbb architektúra a következő összetevőkből áll:

-   **[Fenyegetések modellezése](https://aka.ms/healththreatmodel)**  egy átfogó fenyegetések modellezése való használatra tm7 formátumban van megadva a [Microsoft fenyegetések modellezése eszköz](https://www.microsoft.com/en-us/download/details.aspx?id=49168), a megoldás összetevői megjelenítő, az adatok között zajló kommunikációról, és a megbízhatóság határok. A modell segít megérteni a pontokat a rendszer infrastruktúra kockázatokkal machine learning-összetevők vagy egyéb módosítások fejlesztése során az ügyfelek.

-   **[Ügyfél megvalósítása mátrix](https://aka.ms/healthcrmblueprint)**  A Microsoft Excel-munkafüzet HITRUST vonatkozó követelményeit sorolja fel, és elmagyarázza, hogyan Microsoft és az ügyfél felelősek az értekezlet minden egyes.

-   **[Állapotának áttekintése.](https://aka.ms/healthreviewpaper)** A megoldás Coalfire Systems, Inc. lett felül Az állapotfigyelő megfelelőségi (HIPAA és HITRUST) áttekintése és útmutatást biztosít a megvalósítás biztosít egy auditor\'s át kell tekinteni a megoldás, és egy éles használatra kész telepítéshez szerkezeti terve átalakítása szempontjai.

# <a name="architectural-diagram"></a>Architekturális diagramja


![](images/refarch.png)

## <a name="roles"></a>Szerepkörök


Szerkezeti terve kórháznak felügyeleti és betegápolás határozza meg két szerepkört azon rendszergazda felhasználók (operátorok), és három szerepkörök a felhasználók számára. Hatodik szerepkör a HIPAA és egyéb szabályozásoknak való megfelelőség kiértékeléséhez egy auditor van meghatározva. Azure szerepköralapú hozzáférés-vezérlés (RBAC) lehetővé teszi, hogy pontosan célzott kezelési minden felhasználóhoz a megoldás a beépített és egyéni szerepkörök keresztül. Lásd: [szerepköralapú hozzáférés-vezérlés az Azure-portálon az első lépései](https://docs.microsoft.com/azure/role-based-access-control/overview) és [Azure szerepköralapú hozzáférés-vezérlés beépített szerepkörök](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) RBAC, szerepkörök és engedélyekkel kapcsolatos részletes információkat.

### <a name="site-administrator"></a>Hely rendszergazdája


A hely rendszergazdájának felelős az ügyfél az Azure-előfizetéséhez. Általános központi telepítésére, de nincs hozzáférési jogosultsága beteg rögzíti.

-   Alapértelmezett szerepkör-hozzárendelések: [tulajdonosa](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner)

-   Egyéni szerepkör-hozzárendelések: nincs

-   Hatókör: előfizetés

### <a name="database-analyst"></a>Adatbázis-elemző

Az adatbázis elemző felügyeli az SQL Server-példány és az adatbázis.
Nem lehet hozzáférni beteg rekordok rendelkeznek.

-   Beépített szerepkör-hozzárendelések: [SQL DB Contributor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-db-contributor), [SQL Server közreműködő](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-server-contributor)

-   Egyéni szerepkör-hozzárendelések: nincs

-   Hatókör: erőforráscsoport

 ### <a name="data-scientist"></a>Adatok tudósok


Az adatok tudósok az Azure Machine Learning szolgáltatás működik. Ezek importálni, exportálni, és adatok kezelésére, és jelentéseket is futtathatnak. Az adatok tudósok betegek adatai hozzáféréssel rendelkezik, de nem rendelkezik rendszergazdai jogosultságokkal.

-   Beépített szerepkör-hozzárendelések: [tárolási fiók közreműködői](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-contributor)

-   Egyéni szerepkör-hozzárendelések: nincs

-   Hatókör: erőforráscsoport

### <a name="chief-medical-information-officer-cmio"></a>Fő orvosi információk tisztviselő (CMIO)


A CMIO straddles az oszd informatikai/technológiák és egészségügyi szervezetben egészségügyi szakemberek között. Feladataik rendszerint analytics segítségével határozza meg, ha éppen erőforrásokat megfelelően a szervezeten belül.

-   Beépített szerepkör-hozzárendelések: nincs

### <a name="care-line-manager"></a>Ügyeljen arra, Vonalkezelő


A figyelmet vonalkezelő van közvetlenül érintett betegek körültekintően.
Az e munkakörben dolgozók feladata, hogy nyomon kövessék az egyes betegek állapotát, valamint garantálják, hogy a dolgozók megfeleljenek a hozzájuk rendelt betegek ellátási igényeinek. A figyelmet vonalkezelő hozzáadása és beteg rekordok frissítése felelős.

-   Beépített szerepkör-hozzárendelések: nincs

-   Egyéni szerepkör-hozzárendelések: rendelkezik-e ehhez mindkét türelmet beléptetésre HealthcareDemo.ps1 futtatásához és ellátásához.

-   Hatókör: erőforráscsoport

### <a name="auditor"></a>Auditor


Az auditor kiértékeli megfelelőség szempontjából a megoldás. Nincs a hálózathoz közvetlen hozzáférést rendelkeznek.

-   Beépített szerepkör-hozzárendelések: [olvasó](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader)

-   Egyéni szerepkör-hozzárendelések: nincs

-   Hatókör: előfizetés

## <a name="example-use-case"></a>Példa használati eset


A példa használati eset a tervezetének mellékelt mutatja be, hogyan szerkezeti terve segítségével engedélyezze a gépi tanulási és elemzés egészségügyi adatok a felhőben. Contosoclinic egy kis kórháznak az Amerikai Egyesült Államokban található. A kórháznak hálózati rendszergazdáknak is érdemes Azure Machine Learning segítségével jobban előre jelezni az egy türelmet hossza időpontjában felvételéről működési munkaterhelés hatékonyság növelése, és nyújthat gondot minőségének javítása érdekében.

### <a name="predicting-length-of-stay"></a>Előrejelzésére hossza


A példa felhasználási forgatókönyve Azure Machine Learning segítségével egy újonnan bevezetett türelmet hossza előrejelzése összehasonlítva beteg bevitel venni az összesített előzményadatok az előző betegek orvosi részleteit.
Szerkezeti terve anonimizált egészségügyi adatok bemutatják a képzés és prediktív képességeit a megoldás nagy készletét tartalmazza. Éles környezetben az ügyfelek saját használna a megoldást a környezet, az eszközök és a betegek egyedi részleteit tükröző több pontos előrejelzéseket betanításához.

### <a name="users-and-roles"></a>Felhasználók és szerepkörök


**Webhely-rendszergazda--Alex**

*E-mailek: Alex\_SiteAdmin*

Alex meg folyamat kiértékelése technológiákkal, csökkentse egy helyi hálózati felügyelete okozta terheket és a felügyeleti költségek csökkentése. Alex rendelkezik lett kiértékelése Azure egy kis ideig, de a szolgáltatásait, amely a HiTrust megfelelőségi követelményeknek a felhőben tárolni türelmet kell struggled rendelkezik. Alex az Azure állapotfigyelő AI telepíthet megfelelőségi használatra kész állapotfigyelő megoldást, amely a felhasználói igényeknek HiTrust követelmények küldött van kijelölve.

**Adatok tudósok--Perényi**

*E-mailek: Perényi\_DataScientist*

Perényi van feladata használ, és betekintést betegápolás egészségügyi adatok elemzése modellek létrehozása. Perényi saját modellek létrehozásához használja az SQL és az R statisztikai programozási nyelv.

**Adatbázis-elemző--Danny**

*E-mailek: Danny\_DBAnalyst*

Danny a fő kapcsolódási semmit a Microsoft SQL Server minden beteg adatokat tároló Contosoclinic a kapcsolatban. Danny egy tapasztalt SQL Server rendszergazdaként közelmúltban vált ismeri az Azure SQL Database.

**Fő orvosi információk tisztviselő--Caroline**

Caroline Chris a fontos Vonalkezelő, és az adatok tudósok Perényi meghatározza ható tényezők hatással beteg hossza dolgoznak.
Caroline az előrejelzés a hosszát a tartózkodási (másik) megoldásban való alapján határozza meg, ha éppen erőforrásokat megfelelően a kórháznak hálózatban. Például az irányítópulttal megadott ebben a megoldásban.

**Fontos sor Manager--Chris**

*E-mailek: Chris\_CareLineManager*

Az egyes, közvetlenül kezeléséért beteg beléptetése és a kibocsátás Contosoclinic, a Chris használja az előrejelzés a másik megoldás által létrehozott arról, hogy megfelelő személyzettel meghozandó gondot kínálnak, amíg azok tartózkodó a lehetőség.

**Auditor--Han**

*E-mailek: Han\_Auditor*

Han élmény naplózásának ISO, SOC és HiTrust rendelkező tanúsított naplózójának. Tekintse át a Contosoclinc tartozó hálózati han lett alkalmaz. Han tekintheti meg a felhasználói felelősségi mátrix a megoldás megadott győződjön meg arról, hogy a tervezetének és a másik megoldás vannak tárolva, a folyamat, és bizalmas személyes adatok megjelenítéséhez használható.


# <a name="design-configuration"></a>Tervezési konfiguráció


Ez a szakasz részletesen felsorolja a meghatározott alapértelmezett konfigurációk és a leírt szerkezeti terve beépített biztonsági intézkedéseket:

- **BEMENETI** nyers adatforrás FHIR adatforrás
- **TÁROLÓ** bizalmas információk
- **ELEMZÉS** és eredmények előrejelzése
- **KOMMUNIKÁCIÓ** az eredmények és előrejelzés
- **IDENTITÁS** felügyeleti megoldás
- **BIZTONSÁGI** engedélyezve van a szolgáltatások


## <a name="identity"></a>IDENTITÁS 

### <a name="azure-active-directory-and-role-based-access-control-rbac"></a>Az Azure Active Directory és a szerepköralapú hozzáférés-vezérlést (RBAC)


**Hitelesítés:**

-   [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) van a Microsoft\'s több-bérlős felhőalapú címtár- és identitáskezelési felügyeleti szolgáltatást. Minden felhasználó a megoldáshoz az Azure Active Directoryban, beleértve az SQL-adatbázist elérő felhasználók jöttek létre.



-   Az alkalmazás-hitelesítés az Azure AD használatával történik. További információkért lásd: [alkalmazások integrálása az Azure Active Directory](/azure/active-directory/develop/active-directory-integrating-applications).

-   [Az Azure Active Directory Identity Protection](/azure/active-directory/active-directory-identityprotection) észlel, a szervezet identitásait érintő lehetséges biztonsági rések, konfigurálja az automatikus válaszokat ad a szervezet identitásait kapcsolódó észlelt gyanús tevékenységek és gyanús incidensek megvizsgálja, és végrehajtja a megfelelő művelettel hárítsa el őket.

-   [Azure szerepköralapú hozzáférés-vezérlés (RBAC)](/azure/role-based-access-control/role-assignments-portal) lehetővé teszi, hogy pontosan célzott hozzáférés-kezelés az Azure-bA. Előfizetés hozzáférés korlátozódik előfizetés-rendszergazdaként, és az Azure Key Vault hozzáférés korlátozódik a webhely rendszergazdájához. Az erős jelszavak (legalább egy felső/alsó betűvel, számot és speciális karakterek minimális 12 karakter) szükség.

-   A multi-factor authentication akkor támogatott, ha a - enableMFA kapcsoló engedélyezve van a telepítés során.

-   A jelszavak lejárnak, ha engedélyezve van a - enableADDomainPasswordPolicy kapcsoló üzembe helyezése során 60 nap után.

**Szerepkörök:**

-   A megoldás teszi [beépített szerepkörök](/azure/role-based-access-control/built-in-roles) erőforrásokhoz való hozzáférés kezeléséhez.

-   Minden felhasználó adott beépített szerepkörök alapértelmezett rendeli hozzá.

### <a name="azure-key-vault"></a>Azure Key Vault

-   A Key Vault adataihoz tartalmazza:

    -   Alkalmazás insight kulcs
    -   Beteg adatokat tároló elérési kulcsot
    -   Beteg kapcsolati karakterlánc
    -   A betegek adatai tábla neve
    -   Az Azure gépi tanulás webszolgáltatás végpontja
    -   Az Azure ML Service API-kulcs

-   Speciális hozzáférési házirendek kell alapon
-   Kulcstároló hozzáférési házirendeket a kulcsok és titkos minimálisan szükséges engedélyeket
-   Minden kulcsok és titkos kulcs tárolóban lévő állapottal rendelkezik lejárati dátuma
-   A Key Vault minden kulcs HSM által védett \[kulcs HSM-védelemmel 2048 bites RSA-kulcs =\]
-   Minden felhasználó/identitások kapnak a minimálisan szükséges engedélyeket szerepköralapú hozzáférés vezérlés (RBAC) használata
-   Alkalmazások nem ugyanazt a kulcstároló, kivéve, ha azok megbízhatósági kapcsolat áll fenn, és ugyanazt a titkos kulcsok, futási időben való hozzáférésre van szükségük
-   Diagnosztikai naplókat a Key Vault legalább 365 nap megőrzési idővel rendelkező engedélyezve van.
-   A kulcsok engedélyezett titkosítási műveleteket a meglévők közül szükséges korlátozva

## <a name="ingest"></a>BETÖLTÉSI 

### <a name="azure-functions"></a>Azure Functions
A megoldás úgy lett kialakítva, használandó [Azure Functions](/azure/azure-functions/) feldolgozni a tartózkodási adatait az analytics bemutató mintát hosszát. A funkciók három képességei lettek létrehozva.

**1. Tömeges importálással adatok Fí ügyféladatok**

Ha a bemutató-parancsfájl használatával. . \\Rendelkező HealthcareDemo.ps1 a **BulkPatientAdmission** a Váltás **központi telepítése és futtatása a bemutató** végrehajtása a következő folyamat:
1. **Az Azure Blob Storage** -tárhelyre feltöltött beteg adatokat .csv fájl minta
2. **Esemény rács** -esemény tesz közzé adatokat az Azure-függvény (tömeges importálással - blob esemény)
3. **Az Azure-függvény** - hajtja végre a feldolgozását, és tárolja az adatok a biztonságos funkcióval SQL-tároló - esemény (típusú; blob URL-címe)
4. **SQL-adatbázis a** – türelmet adatok az adatbázis-tároló besorolási címkék és a gépi tanulás folyamat van kezdődött el ehhez a tanítási kísérletet.

![](images/dataflow.png)

Az azure függvény emellett úgy lett kialakítva, olvassa el és a következő minta adatkészletben, a következő címkék használatával kijelölt bizalmas adatok védelmét:
- dataProfile = > "ePHI"
- tulajdonos = > \<hely Admin egyszerű felhasználónév\>
- környezet = > "Próbaüzem"
- szervezeti egység = > "Globális ökoszisztéma", a címkézés lett alkalmazva: a minta adatkészlet, ahol türelmet "neve" egyszerű szövegként azonosított.

**2. Új betegek beléptetéséhez**

Ha a bemutató-parancsfájl használatával. . \\Rendelkező HealthcareDemo.ps1 a **BulkPatientadmission** a Váltás **központi telepítése és futtatása a bemutató** végrehajtása a következő folyamat: ![](images/securetransact.png) 
 **1. Azure függvény** elindul, és a vonatkozó kérések a függvény egy [tulajdonosi jogkivonattal](/rest/api/) az Azure Active Directoryból.

**2. Key Vault** kért a titkos kulcs, amely a kért token rendelve.

**3. Az Azure szerepkörök érvényesíteni a kérést, és engedélyezik a kulcstároló hozzáférési kérelmet.

**4. Key Vault** karakterláncot ad vissza, a titkos kulcsot, ebben az esetben az SQL adatbázis-kapcsolatot.

**5. Az Azure-függvény** a kapcsolati karakterlánc segítségével biztonságosan csatlakozzon az SQL Database, és továbbra is fennáll, további feldolgozás ePHI adatok tárolására.

Eléréséhez, hogy az adatokat, egy közös API-séma megtörtént, a következő gyors egészségügy együttműködési erőforrások (FHIR, tűz hangsúlyozottan). A függvény lett megadva a következő FHIR exchange elemeket:

-   [Beteg séma](https://www.hl7.org/fhir/patient.html) tartalmaz, a "ki" egy türelmet információt.

-   [Megfigyelési séma](https://www.hl7.org/fhir/observation.html) egészségügyi központi elemet tartalmazza, támogatja az elemzés céljából, figyelemmel az előrehaladást, alapkonfigurációk és minták segítségével és demográfiai jellemzőit is rögzítheti. 

-   [Séma előforduló](https://www.hl7.org/fhir/encounter.html) magában foglalja az a típusú ütközik ambulatory, sürgős, például otthoni állapot, a beteg és a virtuális ütközik.

-   [Az állapot séma](https://www.hl7.org/fhir/condition.html) egy feltétel, a probléma, elemzés céljából, vagy más esemény, helyzet, probléma vagy klinikai fogalom, amely potenciálisan veszélyes szintre van során részletes információkat tartalmazza.  



### <a name="event-grid"></a>Event Grid


A megoldás Azure esemény rács, az útválasztás kezelése összes események forrásból bármilyen célra, amely egyetlen szolgáltatásként támogatja:

-   [Biztonsági és hitelesítési](/azure/event-grid/security-authentication)

-   [Szerepköralapú hozzáférés-vezérlés](/azure/event-grid/security-authentication#management-access-control) különféle felügyeleti műveletek, például esemény-előfizetések listázása, új címkék létrehozása és a kulcsok létrehozása

-   Naplózás

## <a name="store"></a>TÁROLÓ 

### <a name="sql-database-and-server"></a>SQL-adatbázis és a kiszolgáló 


-   [Transzparens Data Encryption (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) biztosít a valós idejű titkosítási és visszafejtési az Azure SQL Database az Azure Key Vault-ben tárolt kulcs használatával tárolt adatok.

-   [SQL biztonsági réseinek értékelése](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) egy könnyen beállítható eszköz, amely is felderítése, nyomon követheti és szervizelheti azokat a potenciális adatbázis biztonsági réseket.

-   [SQL-adatbázis Fenyegetésészlelés](/azure/sql-database/sql-database-threat-detection) engedélyezve van.

-   [SQL Database Auditing](/azure/sql-database/sql-database-auditing) engedélyezve van.

-   [SQL-adatbázis metrikák és diagnosztikai naplózás](/azure/sql-database/sql-database-metrics-diag-logging) engedélyezve van.

-   [Adatbázis - és kiszolgálószintű tűzfal-szabályok](/azure/sql-database/sql-database-firewall-configure) egyszerűsítették.

-   [Mindig titkosítja az oszlopok](/azure/sql-database/sql-database-always-encrypted-azure-key-vault) beteg első, a középső és az utolsó nevek védelmére használhatók.
    Emellett az adatbázis oszlopok titkosítását is az Azure Active Directory (AD) segítségével hitelesíti az alkalmazás az Azure SQL Database.

-   SQL-adatbázis és az SQL Server elérésére a legalacsonyabb jogosultsági szint elvét megfelelően van konfigurálva.

-   Csak a szükséges IP-címek hozzáférhetnek az SQL-tűzfalon keresztül.

### <a name="storage-accounts"></a>Tárfiókok


-   [Mozgásérzékelő – az adatátvitel csak TLS/SSL használatával](/azure/storage/common/storage-require-secure-transfer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json).

-   Névtelen hozzáférés nem engedélyezett az tárolókat.

-   A riasztási szabályok névtelen tevékenység követési vannak konfigurálva.

-   HTTPS-kapcsolat szükséges a tárfiók erőforrásainak eléréséhez.

-   Hitelesítési kérelem adatokat naplózza, és figyeli.

-   A Blob Storage tárolóban adatok titkosítása.

## <a name="analyze"></a>ELEMZÉSE

### <a name="machine-learning"></a>Machine Learning


-   [Naplózás engedélyezve van](/azure/machine-learning/studio/web-services-logging) Machine Learning webszolgáltatások.
- használatával [Machine Learning](/azure/machine-learning/desktop-workbench/experimentation-service-configuration) munkaterület megköveteli a kísérletek, amely lehetővé teszi a előre jelezni, hogy egy megoldást. [A munkaterület integrálása](/azure/machine-learning/desktop-workbench/using-git-ml-project) kísérletek felügyeleti csoportosíthatók.

## <a name="security"></a>BIZTONSÁG

### <a name="azure-security-center"></a>Azure Security Center
- [Az Azure Security Center](https://azure.microsoft.com/services/security-center/) központosított összes Azure-erőforrások biztonsági állapotát jeleníti meg. Egy pillanat alatt ellenőrizze, hogy a megfelelő biztonsági vezérlőket teljesül, és megfelelően konfigurálva, és gyorsan azonosíthatja a figyelmet igénylő erőforrásokat. 

- [Az Azure Advisor](/azure/advisor/advisor-overview) , amelynek segítségével személyre szabott felhő tanácsadó kövesse a bevált gyakorlatokat az Azure-környezetekhez optimalizálása érdekében. Az Advisor elemzi az erőforrások konfiguráció- és használattelemetriáját, és megoldási javaslatokat tesz, amelyek segítségével javítható az Azure-erőforrások költséghatékonysága, teljesítménye, rendelkezésre állása és biztonsága.

### <a name="application-insights"></a>Application Insights
- [Az Application Insights](/azure/application-insights/app-insights-overview) egy bővíthető alkalmazásteljesítmény-felügyeleti (APM) szolgáltatás webfejlesztőknek, több platformon. Az élő webalkalmazásának figyelésére használhatja. Teljesítményanomáliákat észlel. Hatékony elemzőeszközöket tartalmaz, amelyek segítenek a problémák felismerésében, és annak a megértésében, hogy a felhasználók mire használják ténylegesen az alkalmazást. Úgy tervezték, hogy használatával folyamatosan javíthassa a teljesítményt és a használhatóságot.

### <a name="azure-alerts"></a>Az Azure riasztások
- [Riasztások kínálnak az Azure-szolgáltatások figyelésére szolgáló módszer, és lehetővé teszik adatok konfigurálhatja a platformesemények létrehozási feltételeit. Riasztások is adja meg az értesítéseket, egy riasztási feltétel megegyezik a figyelési adatokat.

### <a name="operations-management-suite-oms"></a>Az Operations Management Suite (OMS)
[Az Operations Management Suite (OMS)](/azure/operations-management-suite/operations-management-suite-overview) szolgáltatások gyűjteménye.

-   A Security Center engedélyezve van a munkaterület

-   Munkaterület engedélyezve van a munkaterhelés-figyelés

-   Munkaterhelés-figyelés engedélyezve van:

    -   Identitás és hozzáférés

    -   Biztonsági és naplózása

    -   Azure SQL DB Analytics

    -   [Az Azure webalkalmazás Analytics](/azure/log-analytics/log-analytics-azure-web-apps-analytics) megoldás

    -   Key Vault-elemzés

    -   Változások követése

-   [Application Insights-összekötő (előzetes verzió)](/azure/log-analytics/log-analytics-app-insights-connector) engedélyezve van

-   [Tevékenység naplóelemzési](/azure/log-analytics/log-analytics-activity) engedélyezve van
