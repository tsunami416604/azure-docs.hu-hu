---
title: Ajánlott eljárások biztonságossá tétele és a számítási feladatok kezelése az Azure-bA migrálása |} A Microsoft Docs
description: Az Azure-bA az áttelepítés után első működő és biztonságossá tétele az áttelepített alkalmazások és szolgáltatások felügyeletére szolgáló ajánlott eljárásokat.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 12/08/2018
ms.author: raynew
ms.openlocfilehash: b2b5d62c54fbfdef8a5e448a089800eedcb66d07
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54827499"
---
# <a name="best-practices-for-securing-and-managing-workloads-migrated-to-azure"></a>Ajánlott eljárások biztonságossá tétele és a számítási feladatok kezelése az Azure-bA migrálása

A tervezés és szem előtt tartva magát, az áttelepítés mellett az áttelepítés megtervezése, fontolja meg az Azure-ban a biztonsági és felügyeleti modellt migrálás után kell. Ez a cikk ismerteti a tervezési és ajánlott eljárások biztonságossá tétele az Azure-alapú áttelepítése után, valamint, hogy az üzemelő példány optimális teljesítményének fenntartásához futó folyamatban lévő feladatok. 

> [!IMPORTANT]
> Az ajánlott eljárásokról és a jelen cikkben ismertetett vélemények az Azure platformon alapul, és a szolgáltatás írásának időpontjában elérhető szolgáltatások. Funkciók és képességek idővel változni.

## <a name="secure-migrated-workloads"></a>Az áttelepített munkaterhelések védelmére

Az áttelepítés után a kritikus fontosságú feladat az áttelepített alkalmazások és szolgáltatások, külső és belső fenyegetések elleni védelmét. Ajánlott eljárások segítségével végezheti el:

- [Az Azure Security Center használata](#best-practice-follow-azure-security-center-recommendations): Ismerje meg, hogyan dolgozhat a monitorozási, értékelési, és az Azure Security Center által nyújtott ajánlások
- [Az adatok titkosításához](#best-practice-encrypt-data): Ajánlott eljárások beolvasása az Azure-ban az adatok titkosításához.
- [Állítsa be a kártevőirtó](#best-practice-protect-vms-with-antimalware): Virtuális gépek védelme a kártevők és rosszindulatú támadások.
- [Webalkalmazások biztonságossá](#best-practice-secure-web-apps): Bizalmas adatok biztonsága érdekében az áttelepített web apps szolgáltatásban.
- [Tekintse át az előfizetések](#best-practice-review-subscriptions-and-resource-permissions): Ellenőrizze, hogy ki férhet hozzá az Azure-előfizetések és -erőforrások az áttelepítés után.
- [Naplók használata](#best-practice-review-audit-and-security-logs): Tekintse át az Azure naplózási és biztonsági naplókat rendszeres időközönként.
- [Tekintse át a más biztonsági funkciókat](#best-practice-evaluate-other-security-features): Ismerje meg, és kiértékelése az Azure által kínált fejlett biztonsági funkciókat.

## <a name="best-practice-follow-azure-security-center-recommendations"></a>Ajánlott eljárás: Az Azure Security Center javaslatainak követése

A Microsoft keményen együttműködik annak érdekében, hogy az Azure-bérlő rendszergazdák rendelkeznek-e a számítási feladatok támadások ellen védő biztonsági funkciók engedélyezéséhez szükséges információkat.  Az Azure Security Center egységes biztonsági felügyeletet biztosít. A Security centerből biztonsági szabályzatokat alkalmazhat a számítási feladatok, korlátozhatja threat kapta, és észlelheti és elháríthatja a támadásokat. A Security Center elemzi az erőforrások és konfigurációk Azure bérlőn, és lehetővé teszi a biztonsági javaslatok, többek között:

- **Központosított szabályzatkezelés** – Biztosíthatja a vállalati vagy hatósági követelményeknek való megfelelést a hibrid felhőalapú számítási feladatok biztonsági szabályzatainak központi kezelésével.
- **Folyamatos biztonsági értékelés** – Figyelheti a gépek, hálózatok, tárolási és adatszolgáltatások, valamint az alkalmazások biztonságát a potenciális biztonsági problémák észleléséhez.
- **Végrehajtható javaslatok** – Elháríthatja a biztonsági réseket a rangsorolt és végrehajtható biztonsági javaslatokkal, mielőtt a támadók kihasználhatnák azokat.
- **Rangsorolt riasztások és incidensek** – A rangsorolt riasztások és incidensek segítségével először a komoly fenyegetésekre összpontosíthat.

Értékelés és javaslatok kívül a Security Center számos más biztonsági funkciókat, amelyek az adott erőforrásokhoz is engedélyezhető.

- **Csak az idő szerinti (JIT) hozzáférési**: Csökkentse a hálózat támadási felületét a szerinti, szabályozott hozzáféréssel, hogy az Azure virtuális gépek felügyeleti portjaihoz.
    - Virtuális gépek folyamatos kiderítsék tevékenységhez rendelkező virtuális gép RDP-port meg van nyitva az interneten a 3389-es tesz elérhetővé. Az Azure IP-címek jól ismert, és a támadók folyamatosan mintavételi őket 3389 portok megnyitása a támadások. 
    - Csak az idő használt hálózati biztonsági csoportok (NSG-k) és a bejövő szabályok ezt a korlátot az, hogy mennyi ideig egy adott port nyitva.
    - A szerinti engedélyezve van, a Security Center ellenőrzi, hogy a felhasználó rendelkezik-e a szerepköralapú hozzáférés-vezérlés (RBAC) írási hozzáférési engedélyek egy virtuális gép. Emellett adja meg a felhasználók hogyan kapcsolódhatnak a virtuális gépek szabályok. Ha engedélyek rendben, a hozzáférési kérelem jóváhagyása és NSG-ket a kijelölt portok ennyi ideig beérkező forgalom engedélyezésére konfigurálja a következőket adja meg. Az NSG-k akkor lépjen vissza az előző állapotukba, ha az idő lejár.
- **Az adaptív alkalmazásvezérlők**: Szoftver- és kártevők ki a virtuális gépek mely alkalmazások futhatnak őket szabályozásával használhassa a dinamikus alkalmazások engedélyezési listáinak.
    - Az adaptív alkalmazásvezérlők lehetővé teszik az engedélyezési lista alkalmazásokhoz, és megakadályozza, hogy rosszindulatú felhasználók és rendszergazdák számára jóvá nem hagyott vagy átvizsgálási szoftver alkalmazások telepítése a virtuális gépeken.
    - Letilthatja vagy riasztás megkísérli a kártevő alkalmazások futtatására, elkerülheti a nemkívánatos vagy rosszindulatú alkalmazások és a szervezet biztonsági szabályzattal gondoskodhat.
- **Fájlintegritási monitorozás**: A virtuális gépeken futó fájlok integritásának biztosításában.
    - Nem kell telepíteni a szoftvert, hogy a virtuális gép problémákat okozhat.  Egy fájl módosítása, a virtuális gép hibája vagy a teljesítmény teljesítménycsökkenés is okozhat.  A fájl integritás figyelés megvizsgálja a rendszerfájlokat és a beállításjegyzék-beállításai a módosításokat, és értesítést küld, ha valami probléma frissül.
    - A Security Center javasolja, amely fájlok, célszerű figyelemmel kísérni.


**tudj meg többet:**

- [További](https://docs.microsoft.com/azure/security-center/security-center-intro) Azure Security Centerrel kapcsolatos.
- [További](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) kapcsolatos igény szerinti Virtuálisgép-hozzáférést.
- [Ismerje meg](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application) adaptív alkalmazásvezérlők alkalmazása.
- [Első lépések](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring) a fájlintegritási Monitorozás.


## <a name="best-practice-encrypt-data"></a>Ajánlott eljárás: Adatok titkosítása 

Titkosítás az Azure biztonsági eljárások fontos része. Annak biztosítása, hogy a titkosítás engedélyezve van minden szintek megelőzhető, illetéktelen fél férjenek hozzá a bizalmas adatokat, az úton lévő és a tárolt adatokat is beleértve. 

### <a name="encryption-for-iaas"></a>Az IaaS titkosítás

- **Virtuális gépek**: A virtuális gépek az Azure Disk Encryption segítségével a Windows és Linux rendszerű IaaS VM-lemezek titkosítása.
    - Lemeztitkosítás kihasználja a BitLocker for Windows és Linux DM-Crypt biztosít az operációs rendszer és az adatlemezek kötettitkosítását.
    - Használhatja az Azure által létrehozott titkosítási kulcsot, vagy megadhatja, hogy saját titkosítási kulcs, és hogyan gondoskodik a védelmükről az Azure Key Vaultban. 
    - IaaS VM-adatok a Lemeztitkosítást, inaktív (lemezen) védett és a virtuális gép rendszerindítás során. 
    - Az Azure Security Center riasztást küld, ha virtuális gépeket, amelyek nincsenek titkosítva van.
- **Tárolási**: Védelme az Azure storage szolgáltatásban tárolt inaktív adatokat.
    - Az Azure storage-fiókokban tárolt adatok titkosíthatók a Microsoft által létrehozott AES-kulcsokkal, amely a FIPS 140-2 szabványnak megfelelő, vagy saját maga is használhatja.
    - A Storage Service Encryption engedélyezve van az összes meglévő és új storage-fiók, és nem tiltható le.


### <a name="encryption-for-paas"></a>Titkosítás a paas-hez

Ellentétben a saját virtuális gépek és infrastruktúra kezeléséhez, IaaS, paas-megoldás a modell platform és infrastruktúra szolgáltató által felügyelt a, hagyja, hogy arra koncentrálhasson, a core-alkalmazás logikai és a funkciókról. Számos különböző típusú a PaaS-szolgáltatások, az egyes szolgáltatások kiértékelendő külön-külön biztonsági okokból. Példaként nézzük meg, hogyan lehet, hogy engedélyezzük a titkosítás az Azure SQL Database.

- **Always Encrypted**: Az SQL Server Management Studióban az Always Encrypted varázsló segítségével az inaktív adatok védelmét.
    - Mindig titkosított kulcs az egyes oszlopok adatainak titkosításához hoz létre.
    - Mindig titkosított kulcsok tárolt titkosított adatbázis-metaadatok között, vagy a megbízható kulcs áruházakban, például az Azure Key Vaultban tárolt.
    - Alkalmazás módosításai valószínűleg szükség lesz a szolgáltatás használatához.
- **Transzparens adattitkosítás (TDE)**: Az Azure SQL Database valós idejű titkosítási és visszafejtési az adatbázis, azokhoz kapcsolódó biztonsági mentési és tranzakciós naplófájlokra inaktív védelmét.
    - TDE titkosítási tevékenységek, hogy végre lehessen hajtani az alkalmazás rétegben módosítása nélkül teszi lehetővé.
    - TDE titkosítási kulcsokat, a Microsoft által biztosított használhatja, vagy megadhatja a saját kulcsok használata a Bring Your Own Key-támogatás.


**tudj meg többet:**
- [Ismerje meg](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) IaaS virtuális gépekhez az Azure Disk Encryption.
- [Engedélyezése](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-windows) IaaS Windows virtuális gépek titkosítását.
- [Ismerje meg](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) Azure Storage Service Encryption az inaktív adatokat.
- [Olvasási](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) Always Encrypted áttekintése.
- [További információ](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?view=sql-server-2017) TDE az Azure SQL Database-hez.
- [Ismerje meg](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql) TDE Bring Your Own Key.

## <a name="best-practice-protect-vms-with-antimalware"></a>Ajánlott eljárás: Virtuális gépek védelme kártevőirtó

Régebbi Azure áttelepített virtuális gépek, előfordulhat, hogy rendelkezik a megfelelő szintű telepített kártevőirtó.  Az Azure, amely segít megvédeni a virtuális gépek a vírusok, kémprogramok és más kártevők ingyenes végponti megoldást kínál.
- A Microsoft Antimalware az Azure állít elő riasztást, ha ismert kártevő vagy nemkívánatos szoftver megkísérli a telepítést.
- Egyetlen ügynökre megoldás, amely emberi beavatkozás nélkül a háttérben fut, is.
- Az Azure Security Centerben könnyedén azonosíthatja a virtuális gépeket, amelyek nem rendelkeznek a az endpoint protection fut, és a Microsoft Antimalware telepítése, igény szerint.

![Kártevőirtó-beli virtuális gépek](./media/migrate-best-practices-security-management/antimalware.png)
*kártevőirtó-beli virtuális gépek*

**tudj meg többet:**

- [Ismerje meg](https://docs.microsoft.com/azure/security/azure-security-antimalware) Microsoft kártevőirtó szolgáltatásával védi.

## <a name="best-practice-secure-web-apps"></a>Ajánlott eljárás: Webalkalmazások biztonságossá tétele

Az áttelepített webes alkalmazások problémák néhány között:

- A legtöbb örökölt webes alkalmazások általában a bizalmas adatokat rendelkezik a konfigurációs fájlok belül.  Az ilyen adatokat tartalmazó fájlok is jelenthet biztonsági problémákat, amikor alkalmazásokat készül biztonsági másolat, vagy amikor a kód be van jelölve, vagy onnan máshová verziókövetés.
- Emellett ha telepít át egy virtuális gép élő webalkalmazások, valószínűleg helyez át, hogy a gép egy a helyszíni hálózat és tűzfal-védelemmel ellátott környezetben egy környezetben, az internetre irányuló. Győződjön meg arról, hogy beállíthat-e egy olyan megoldás, amely a kód ugyanazt teszi, hogy a helyszíni erőforrások védelme kell.


Az Azure-megoldások néhány kínál:

- **Az Azure Key Vault**: Webes alkalmazások fejlesztőinek ma lépéseket annak érdekében, hogy a bizalmas adatok nem kiszivárgott az érintett fájlok tart. Több módszert az adatok védelméhez, hogy a fájlokat csomagolja, és helyezzük el az Azure Key Vaultban.
    - A Key Vault használata az alkalmazás titkos kulcsok tárolásának központosítása, és azok elosztásának irányítását. Ezzel elkerülheti a biztonsági adatok tárolására az alkalmazás fájljai kell.
    - Alkalmazások biztonsági hozzáférési adatokat a tároló URI-k, használatával anélkül, hogy egyéni kódot is.
    - Az Azure Key Vault lehetővé teszi Azure biztonsági szabályozásait keresztüli hozzáférés zárolását, és a zökkenőmentes megvalósítása a "ugró kulcsok". Microsoft nem tekintheti meg és bontsa ki az adatokat.
- **App Service Environment**: Ha egy alkalmazást telepít át a további védelem van szüksége, fontolja meg az alkalmazás-erőforrások védelméhez egy App Service Environment-környezet és a webalkalmazási tűzfal hozzáadása.
    - Az Azure App Service-környezet egy teljesen elkülönített és dedikált környezetet biztosít, amelyben az App Service-ben futó alkalmazások, például a Windows és Linux-webalkalmazások, Docker tárolók, a mobile apps és functions.
    - Hasznos lehet, hogy a nagyon nagy méretű alkalmazások számára, a szükséges elkülönítés és biztonságos hálózati hozzáférési vagy magas memória kihasználtsága
- **Webalkalmazási tűzfal**: Amely központi védelmet kínál a webalkalmazások Application Gateway egyik szolgáltatása.
    - Háttérrendszer kódmódosítás nélkül megvédi a webalkalmazásokat.
    - Több web apps egy application gateway mögött egy időben védelmet biztosít.
    - Webalkalmazási tűzfal az Azure Monitor használatával figyelhetők és integrálva van az Azure Security Center.



![Webalkalmazások biztonságossá](./media/migrate-best-practices-security-management/web-apps.png)
*Azure Key Vault*

**tudj meg többet:**

- [Áttekintés](https://docs.microsoft.com/azure/key-vault/key-vault-overview) az Azure Key Vaultban.
- [Ismerje meg](https://docs.microsoft.com/azure/application-gateway/waf-overview) webalkalmazási tűzfal.
- [Ismerkedjen meg az](https://docs.microsoft.com/azure/app-service/environment/intro) az App Service Environmentet.
- [Ismerje meg, hogyan](https://docs.microsoft.com/azure/key-vault/tutorial-web-application-keyvault) olvassa el a titkos kulcsok a Key Vaultból egy WebApp konfigurálását.
- [Ismerje meg](https://docs.microsoft.com/azure/application-gateway/waf-overview) webalkalmazási tűzfal

## <a name="best-practice-review-subscriptions-and-resource-permissions"></a>Ajánlott eljárás: Tekintse át az előfizetések és az erőforrás-engedélyek

A számítási feladatok migrálása, és futtathatja őket az Azure-ban, számítási feladatok hozzáféréssel rendelkező személyzeti Navigálás. A biztonsági csapat tekintse át a az Azure-bérlőt és erőforrás csoportok rendszeres időközönként való hozzáférést. Az Azure számos olyan identitáskezelési és hozzáférés-vezérléssel, beleértve a szerepköralapú hozzáférés-vezérlés (RBAC) történő hitelesítéséhez az Azure-erőforrások elérésére jogosult csomagjai.

- Az RBAC rendszerbiztonsági hozzáférési engedélyeket rendel. Rendszerbiztonsági tagok képviselik a felhasználók, csoportok (a felhasználók egy csoportja), egyszerű szolgáltatások (alkalmazások és szolgáltatások által használt identitás), és a felügyelt identitások (az Azure Active Directory identitás az Azure automatikusan kezeli).
- Az RBAC használatával szerepköröket rendelhet biztonsági alapelvek, mint a tulajdonos, közreműködő és olvasó és a szerepkör-definíciók (engedélyek gyűjteménye), amelyek meghatározzák a műveletek végezhetők el a szerepköröket.
- RBAC is beállíthat, amely egy adott szerepkör esetében a határ hatókörök. Hatókör állíthat be egy szintek, beleértve a felügyeleti csoportban, előfizetés, erőforráscsoport vagy erőforrás száma
- Győződjön meg arról, hogy az Azure-hozzáféréssel rendelkező rendszergazdák csak érhessék el az erőforrásokat, amelyek számára engedélyezni kívánja.  Ha az Azure-ban az előre definiált szerepkörök nem elég részletes, létrehozhat egyéni szerepköröket külön és a hozzáférési engedélyek korlátozása.

![Hozzáférés-vezérlés](./media/migrate-best-practices-security-management/subscription.png)
*hozzáférés-vezérlés – IAM*

**tudj meg többet:**

- [Kapcsolatos](https://docs.microsoft.com/azure/role-based-access-control/overview) RBAC.
- [Ismerje meg,](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) hozzáférés rbac-RÓL és az Azure portal használatával történő kezeléséhez.
- [Ismerje meg](https://docs.microsoft.com/azure/role-based-access-control/custom-roles) egyéni szerepkörök.

## <a name="best-practice-review-audit-and-security-logs"></a>Ajánlott eljárás: Naplózási és biztonsági naplók áttekintése

Az Azure Active Directory (AD) biztosít, amely megjelenik az Azure monitorban tevékenységeket tartalmazó naplók. A naplók rögzítése Azure bérlős, amikor azok történt, és ki végzett jelszóműveleteket. 

- Naplók megjelenítése a bérlő feladatok előzményeit. Bejelentkezési tevékenységek naplózza a feladatokat elvégző megjelenítése. 
- Biztonsági jelentések a hozzáférést az Azure AD-licencre függ. Az ingyenes és alapszintű kap kockázatos felhasználók és bejelentkezések listája. Prémium 1 és 2. prémium kiadásban első alapul szolgáló eseményből származó információkat.
- Tevékenységnaplók is átirányítása egy hosszú távú megőrzés és elemzéseket végpontok száma.
- Általános gyakorlat, tekintse át a naplókat, vagy a biztonsági biztonságiadat- és eseménykezelés (SIEM) felügyeleti eszközök a automatikusan tekintse át a rendellenességeket integrálása révén.  Ha Premium 1 vagy 2-es nem használ, szüksége lesz elemzési rengeteg ehhez a saját maga, vagy használja a SIEM-rendszerbe.  Elemzés magában foglalja a kockázatos bejelentkezések és események és egyéb felhasználói támadási mintákat keres.


![Felhasználók és csoportok](./media/migrate-best-practices-security-management/azure-ad.png)
*Azure AD-felhasználók és csoportok*

**tudj meg többet:**

- [Ismerje meg](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor) az Azure AD-Tevékenységnaplók az Azure monitorban.
- [Ismerje meg, hogyan](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs) naplózott tevékenységekre vonatkozó jelentések az Azure AD portálon.

## <a name="best-practice-evaluate-other-security-features"></a>Ajánlott eljárás: Egyéb biztonsági funkciókról kiértékelése

Az Azure számos más biztonsági funkciókat, amelyek fejlett biztonsági lehetőségeket biztosít. Ajánlott eljárások egyes szükség, licencek és a prémium szintű lehetőségekkel.

- **Az Azure AD felügyeleti egységek (AU) megvalósítása**: Rendszergazdai feladatokat a támogató személyzet delegálása nehéz megkülönböztetni, csak az alapszintű Azure hozzáférés-vezérlést.  Összes csoport felügyelete az Azure ad-ben a támogatási személyzet hozzáférést ad a ideális megközelítés a szervezeti biztonság nem feltétlenül.  AU használatával lehetővé teszi, hogy elkülönítse az Azure-erőforrások vannak tárolókba hasonló módon a helyszíni szervezeti egységhez (OU).  Az AU-rendszergazda AU használandó rendelkeznie kell egy prémium szintű Azure AD-licencre. [További információk](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-administrative-units).
- **Többtényezős hitelesítés (MFA) használata**: Ha engedélyezi, és a rendszergazdai fiókok a többtényezős hitelesítés kényszerítése egy prémium szintű Azure AD-licencre. Adathalászat, hogy a fiókok hitelesítő adatai kerülnek veszélybe leggyakoribb módja.  Miután kiderítsék a rendszergazdai hitelesítő adataival rendelkezik, nincs nincs leállítása őket a kiterjedt műveletek, például az erőforráscsoportok törlésével. MFA állíthat be, a különféle módokon, például az e-mailek, az authenticator alkalmazás és a telefon szöveges üzeneteket. Rendszergazdaként a legalább zavaró lehetőséget is választja. MFA threat-analytics integrálható, és a feltételes hozzáférési szabályzatok véletlenszerűen megkövetelése az MFA-hitelesítést válaszol. Tudjon meg többet [biztonsági útmutató](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication-security-best-practices), és [beállítása](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication-security-best-practices) MFA.
- **A feltételes hozzáférés alkalmazása**: A legtöbb kis és közepes méretű szervezetek számára az Azure-rendszergazdák és a támogatási csapat valószínűleg található egyetlen földrajzi. A legtöbb bejelentkezések ebben az esetben az azonos területeken származnak. Ha az IP-címeket a következő helyek viszonylag statikus, logikus, hogy nem látja ezeket a területeket kívül a rendszergazda bejelentkezések. Egy esemény, amelyben egy távoli kiderítsék károsan befolyásolja a rendszergazdai hitelesítő adatait, még akkor is, a biztonsági funkciókkal, például a feltételes hozzáférés, hogy a távoli helyekről vagy véletlenszerű IP-címekről identitáshamisításos helyekről való bejelentkezés MFA kombinálva valósíthat meg. [További](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) információ a feltételes hozzáférésről és [ajánlott eljárásokat](https://docs.microsoft.com/azure/active-directory/conditional-access/best-practices) feltételes hozzáférés az Azure ad-ben.
- **Tekintse át a vállalati alkalmazás engedélyeket**: Az idő múlásával rendszergazdák kattintson a Microsoft és külső hivatkozásokat a szervezet gyakorolt hatásuk ismerete nélkül. Hivatkozások hozzájárulási képernyő engedélyek hozzárendelése az Azure-alkalmazások, és előfordulhat, hogy engedélyezi az Azure AD-adatok olvasási hozzáférést, vagy akár teljes hozzáférés az egész Azure-előfizetése kezeléséhez is jelenthet. Rendszeresen tekintse át az alkalmazások, amelyhez a rendszergazdák és felhasználók rendelkezik Azure-erőforrásokhoz való hozzáférést. Biztosítania kell, hogy ezek az alkalmazások csak a szükséges engedélyekkel rendelkezik-e. Ezenkívül negyedévente vagy évente pontosvesszővel is e-mail alkalmazás oldalak mutató hivatkozást tartalmazó felhasználók, hogy azokat az alkalmazásokat, amelyhez, hogy engedélyezett a szervezeti adatokhoz történő hozzáférés figyelembe. [További](https://docs.microsoft.com/azure/active-directory/manage-apps/application-types) kapcsolatos alkalmazástípusok, és [hogyan vezérelheti](https://docs.microsoft.com/azure/active-directory/manage-apps/remove-user-or-group-access-portal) az Azure AD alkalmazás-hozzárendelések.



## <a name="managed-migrated-workloads"></a>Felügyelt áttelepített alkalmazások és szolgáltatások

Ebben a szakaszban javaslatokat kaphat tőlünk néhány ajánlott eljárást az Azure felügyeleti, többek között:

- [Erőforrások kezelése](#best-practice-name-resource-groups): Ajánlott eljárások Azure-erőforráscsoportot és az erőforrások, például intelligens elnevezési, megakadályozza a véletlen törlés, a megfelelő engedélyekkel, és a hatékony erőforrás-címkézési kezelése.
- [Tervezetek használata](#best-practice-implement-blueprints): Tervezetek használatával felépítésére és kezelésére, az üzembe helyezési környezetekhez rövid áttekintést kaphat.
- [Tekintse át az architektúrák](#best-practice-review-azure-reference-architectures): Felülvizsgálat mintát vesz az Azure-architektúrák segítségével megtudhatja származó, az áttelepítés utáni üzemelő példányok létrehozása.
- [Állítsa be a felügyeleti csoportok](#best-practice-manage-resources-with-management-groups): Ha több előfizetéssel is rendelkezik felügyeleti csoportokba rendezheti őket, és szabályozási beállításokat alkalmazza ezeket a csoportokat.
- [Hozzáférési szabályzatok beállítása](#best-practice-deploy-azure-policy): Megfelelőségi szabályzatok alkalmazása az Azure-erőforrások.
- [A BCDR-stratégia megvalósításához](#best-practice-implement-a-bcdr-strategy): Mindez együttesen egy üzleti folytonossági és vészhelyreállítási (BCDR) stratégiára, értesüljön az adatok biztonságát, a rugalmas környezetet és az erőforrások és a futó leállások esetén.
- [Virtuális gépek kezelése a](#best-practice-use-managed-disks-and-availability-sets): Csoport rendelkezésre állási csoportokba, a rugalmasság és a magas rendelkezésre állású virtuális gépeket. Felügyelt lemezek használata a virtuális gép lemez- és felügyeleti megkönnyítése érdekében.
- [Erőforrás-használat figyelése](#best-practice-monitor-resource-usage-and-performance): Az Azure-erőforrások diagnosztikai naplózás engedélyezése, hozhat létre riasztásokat és a proaktív hibaelhárítási forgatókönyveket, és az Azure-irányítópult használata a központi telepítési állapotának és állapotát egyesített nézetét.
- [Támogatás és a frissítések kezelése](#best-practice-manage-updates): Ismerje meg az Azure-támogatási csomagra és ajánlott eljárások lekérése gondoskodik a naprakész virtuális gépek és a put folyamatok változáskezelés helyen való implementálásáról.


## <a name="best-practice-name-resource-groups"></a>Ajánlott eljárás: Erőforráscsoport neve

Annak biztosítása, hogy az erőforráscsoportok rendelkezik adjon kifejező nevet, hogy a rendszergazdák és a támogatási csapat tagjai egyszerű felismerése és keresse meg jelentősen növeli a hatékonyságot és.
- Azt javasoljuk, hogy az Azure elnevezési konvenciót követve.
- Ha Ön már szinkronizálása a helyszíni Active Directory tartományi szolgáltatások Azure ad-bA az AD Connect, fontolja meg a megfelelő biztonsági csoportokat a helyszíni az erőforráscsoportok az Azure-ban való nevei.

![Elnevezési](./media/migrate-best-practices-security-management/naming.png)
*erőforrás-csoport elnevezése*


**tudj meg többet:**

- [Ismerje meg](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) elnevezési konvenciók

## <a name="best-practice-implement-delete-locks-for-resource-groups"></a>Ajánlott eljárás: Alkalmazzon erőforráscsoportok zárolások törlése

A legutolsó dolog, szüksége van egy erőforráscsoport eltűnnek, mert a véletlenül törölve lett. Azt javasoljuk, hogy törlési zárolás valósítható meg, hogy ez nem fordulhat elő.


![Zárolások törlése](./media/migrate-best-practices-security-management/locks.png)
*zárolások törlése*

**tudj meg többet:**

- [Ismerje meg](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) zárolása a váratlan módosítások megelőzése érdekében erőforrásokat.


## <a name="best-practice-understand-resource-access-permissions"></a>Ajánlott eljárás: Erőforrás-hozzáférési engedélyek ismertetése 

Előfizetés tulajdonosa az előfizetés összes erőforráscsoportra és erőforrások hozzáférése van.
- Személyek hozzáadása takarékosan értékes ezt a hozzárendelést. Az ilyen típusú engedélyeket vonatkozásai annak megértése fontos a környezet biztonságos, stabil játszik.
- Ellenőrizze, hogy a megfelelő erőforráscsoport erőforrásokat helyez:
    - Egy hasonló életciklussal rendelkező erőforrások együtt egyezik. Ideális esetben nem kell áthelyezni egy erőforrást, ha egy teljes erőforráscsoportot törölni kell.
    - Egy függvény vagy a számítási feladatok támogató erőforrások együtt az egyszerűbb kezelés kell elhelyezni.

**tudj meg többet:**

- [Ismerje meg](https://azure.microsoft.com/blog/organizing-subscriptions-and-resource-groups-within-the-enterprise/) rendszerezése előfizetésekhez és erőforráscsoportokhoz.

## <a name="best-practice-tag-resources-effectively"></a>Ajánlott eljárás: Erőforrások hatékony címkézése

Gyakran használata csak erőforráscsoport nevét erőforrásokhoz kapcsolódó nem biztosítanak elegendő metaadatok mechanizmusokkal, mint például a belső számlázás vagy felügyelet egy előfizetésen belüli hatékony megvalósítását.
- Ajánlott eljárásként a címkék az Azure ad hozzá, hogy lekérdezhetők és a jelentett hasznos metaadatokat kell használnia. 
- Címkék olyan módon, hogy logikusan rendszerezhesse az Ön által meghatározott tulajdonságokkal rendelkező erőforrásokat tartalmaznak.  A címkék közvetlenül alkalmazhatók erőforráscsoportokhoz vagy erőforrásokat.
- A címkék is alkalmazható, egy erőforráscsoport vagy egyes erőforrásokat. Csoport az erőforráscímkék nem öröklik a csoportban lévő erőforrásokat.
- Címkézés, a Powershell vagy Azure Automation, vagy a címke az egyes csoportok és erőforrások használatával automatizálható. -címkézési módszer vagy annak egy önkiszolgáló.  Ha egy kérelmet, és módosítsa a felügyeleti rendszer megfelel, majd egyszerűen használhat az adatokat a kérésben a vállalatra jellemző erőforráscímkék feltöltéséhez.


![Címkézés](./media/migrate-best-practices-security-management/tagging.png)
*címkézése*

**tudj meg többet:**

- [Ismerje meg](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) címkézést és a címke korlátozások.
- [Felülvizsgálat](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#powershell) PowerShell és CLI-példák, állítsa be a címkézés és a alkalmazni a címkét egy erőforráscsoport erőforrásainak.
- [Olvasási](http://www.azurefieldnotes.com/2016/07/18/azure-resource-tagging-best-practices/) címkézés ajánlott eljárások Azure.


## <a name="best-practice-implement-blueprints"></a>Ajánlott eljárás: Tervezetek megvalósítása

Ugyanúgy, mint a tervezet lehetővé teszi, hogy a projekt tervezési paraméterek vázlat a mérnökök és a tervezők munkáját, az Azure-tervek engedélyezése felhőben dolgozó tervezők és a központi informatikai csoportok segítségével meghatározhatja egy megismételhető az Azure-erőforrás, amely megvalósítja és a szervezet-szabványoknak megfelelő minták és követelményeit. Tervezetek Azure fejlesztői csapatok használatával gyorsan létrehozhat és hozzon létre új környezeteket, amelyek a szervezeti megfelelőségi követelmények teljesítése érdekében, és, amely rendelkezik egy beépített összetevők, például hálózati, fejlesztésre felgyorsítása érdekében.

- Tervezetek segítségével koordinálhatja az erőforráscsoportok, az Azure Resource Manager-sablonok és a házirend- és szerepkör-hozzárendelések telepítését.
- Az Azure tervezetek egy globálisan elosztott Azure Cosmos DB-ben tárolódnak. A tervobjektumok több Azure-régióba vannak replikálva. Replikációs alacsony késleltetésű, magas rendelkezésre állású és tervezetet, függetlenül a régió, amelyhez a tervezet erőforrásokat üzembe helyezi a konzisztens hozzáférést biztosít.

**tudj meg többet:**

- [Olvasási](https://docs.microsoft.com/azure/governance/blueprints/overview) tervezetek kapcsolatban.
- [Felülvizsgálat](https://azure.microsoft.com/blog/customizing-azure-blueprints-to-accelerate-ai-in-healthcare/) a mesterséges Intelligencia felgyorsítása az egészségügyben használt tervezet példa.

## <a name="best-practice-review-azure-reference-architectures"></a>Ajánlott eljárás: Tekintse át az Azure-referenciaarchitektúrák

Biztonságos, méretezhető és kezelhető Azure-beli másolatain ijesztőnek tűnhet.  A folyamatos változásokat nehézkes lehet tartani az optimális környezet különböző funkcióit. Tanuljon hivatkozást kellene hasznos lehet, kialakításakor és számítási feladatok migrálása az.  Az Azure és az Azure partnerek létrehozott minta referenciaarchitektúrák számos különféle típusú környezeteket. Ezeket a mintákat úgy tervezték, hogy adja meg ötleteit, tanuljon és épülnek. 

A referenciaarchitektúrák forgatókönyv szerint vannak elrendezve. Ajánlott eljárásokat és tanácsokat a felügyeleti, rendelkezésre állást, méretezhetőséget és biztonsági tartalmazzák.
Az Azure App Service-környezet az App Service-ben futtatására, alkalmazások, beleértve a Windows és Linux-webalkalmazások, a Docker tárolók, mobilalkalmazások és funkciók teljesen elkülönített és dedikált környezetet biztosít. App Service-ben az Azure ad a biztonsági, terheléselosztási, automatikus skálázást és az automatikus kezelés a az alkalmazás. Akkor is kihasználhatja a fejlesztési és tesztelési lehetőségeket, például a folyamatos üzembe helyezést az Azure DevOps- és GitHub-csomagkezelés, átmeneti környezetek, egyéni tartományt és SSL-tanúsítványokat. Alkalmazások, amelyeknek az elkülönítés és biztonságos hálózati hozzáférést, és azokat, amelyek nagy mennyiségű memóriát és más szeretné méretezni az erőforrásokat az App Service-ben hasznos.
**tudj meg többet:**

- [Ismerje meg](https://docs.microsoft.com/azure/architecture/reference-architectures/) Azure-referenciaarchitektúrák.
- [Felülvizsgálat](https://docs.microsoft.com/azure/architecture/example-scenario/) Azure példaforgatókönyvek.

## <a name="best-practice-manage-resources-with-management-groups"></a>Ajánlott eljárás: Erőforrások kezelése a felügyeleti csoportok

Ha a szervezet több előfizetéssel rendelkezik, hozzáférési házirendeket és azok megfelelőségi kezeléséhez szüksége. Az Azure Management Groups előfizetések fölötti hatókörszintet biztosít.

- Felügyeleti csoportok nevű tárolókba előfizetéseinek, és irányítási feltételeket alkalmaznak rájuk.
- A felügyeleti csoport összes előfizetés automatikusan örökli a felügyeleti csoport feltételeket.
- Felügyeleti csoportok adja meg a nagyvállalati felügyeleti nagy méretben, függetlenül attól, hogy milyen típusú előfizetések rendelkezik.
- Alkalmazhat például egy felügyeleti csoport szabályzatának, amely korlátozza a régiók, ahol virtuális gépeket lehet létrehozni. Ez a szabályzat a rendszer ezután alkalmazza az összes felügyeleti csoportok, előfizetések és felügyeleti csoporthoz tartozó erőforrások.
- Felügyeleti csoportok és az előfizetések a egyesített házirend- és hozzáférés-kezelés hierarchiává az erőforrások rendszerezéséhez rugalmas szerkezete hozhat létre.

Az alábbi ábrán egy példa látható szabályozási hierarchia létrehozására felügyeleti csoportok használatával.

![Felügyeleti csoportok](./media/migrate-best-practices-security-management/management-groups.png)
*felügyeleti csoportok*

**tudj meg többet:**
- [További](https://docs.microsoft.com/azure/governance/management-groups/index) kapcsolatos erőforrások rendszerezése felügyeleti csoportokba.

## <a name="best-practice-deploy-azure-policy"></a>Ajánlott eljárás: Az Azure Policy üzembe helyezése

Az Azure Policy az Azure egy szolgáltatása, amelynek használatával szabályzatokat hozhat létre, rendelhet hozzá és kezelhet.

- Házirendek különböző szabályokat és kényszerítenek hatások az erőforrásokat, így azok megfeleljenek a vállalati szabványoknak és szolgáltatói szerződéseknek.
- Az Azure Policy kiértékeli az erőforrásokat, azoknak a házirendeknek nem megfelelő vizsgálatát.
- Például, létrehozhat egy szabályzatot, amely lehetővé teszi, hogy csak egy meghatározott SKU-méret a virtuális gépek a környezetben. Az Azure Policy kiértékelik ezt a beállítást, ha erőforrások létrehozása és frissítése, és meglévő erőforrások beolvasásakor. 
- Az Azure biztosít néhány beépített szabályzatot, amelyeket hozzárendelhet, vagy létrehozhat saját.


![Az Azure Policy](./media/migrate-best-practices-security-management/policy.png)
*az Azure Policy*

**tudj meg többet:**
- [Áttekintés](https://docs.microsoft.com/azure/governance/policy/overview) az Azure Policy.
- [Ismerje meg](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage) kényszeríteni a megfelelőségi szabályzatok létrehozása és kezelése.


## <a name="best-practice-implement-a-bcdr-strategy"></a>Ajánlott eljárás: A BCDR-stratégia megvalósításához

Tervezés az üzletmenet-folytonossági és vészhelyreállítási (bcdr) funkciók, van egy kritikus fontosságú a gyakorlatban az Azure-bA áttelepítés tervezése során kell végrehajtani. A jogi feltételeket a szerződést, amely egy nagyobb kényszerített például hurrikánok vagy földrengések kötelezettségek ürügy vis major záradékot tartalmaz. Azonban akkor is lehetővé teszi annak érdekében, hogy szolgáltatások továbbra is futtassa, és szükség esetén helyre, ha katasztrófa arra körül kötelezettségek. Ennek a lehetősége webalkalmazásainkkal a vállalat jövőbeli.

Széles körben a BCDR-stratégia figyelembe kell venni:
- **Az adatok biztonsági mentésének**: Hogyan lehet az adatok biztonságban tartani, hogy helyreállíthatja azt könnyedén Ha leállások esetén.
- **Vész-helyreállítási**: Hogyan kell fenntartani az alkalmazások, rugalmas és érhető el, ha a leállások esetén. 

### <a name="azure-resiliency-features"></a>Az Azure rugalmassági funkciói
Az Azure platform számos rugalmasság szolgáltatást biztosít.

- **Régiót a párosítási**: Az Azure-régiók és az adatok fizikai tárolási helye határain belül regionális védelmet biztosíthat párokkal. Azure régiópárok fizikai elkülönítését biztosítja, rangsorolja a széles körű leállás esetén a pár egyik régió helyreállítása, helyezi üzembe a rendszerfrissítések külön-külön az egyes régiókban, és lehetővé teszi, hogy a szolgáltatások, például az Azure georedundáns tárolási való replikálása a regionális párokról.
- **A rendelkezésre állási zónák**: A rendelkezésre állási zónák fizikai külön zónák egy Azure-régióban való létrehozásával egy teljes Azure-adatközpont meghibásodása elleni védelem. Minden zóna egy megkülönböztető áramforrásról, a hálózati infrastruktúra és a hűtési mechanizmus rendelkezik.
- **A rendelkezésre állási csoportok**: A rendelkezésre állási csoportok az adatközponton belül meghibásodásai elleni védelem érdekében. A rendelkezésre állási csoportokban, így magas rendelkezésre állású virtuális gépek csoportosítja. Egyes rendelkezésre állási csoporton belül az Azure csomagszűrő több tartalék tartomány benne együtt az alapul szolgáló hardver egy közös áramforrásról és a hálózati kapcsoló és a egy csoportba, amely a karbantartás, vagy újra kell indítani, alapul szolgáló hardver frissítési tartományok egy időben. Például ha a munkaterhelés Azure virtuális gépekhez összeset is elhelyezhető, két vagy több virtuális gépet minden egyes alkalmazás-szint egy. Elhelyezhet például előtérbeli virtuális gép egy, és a egy másik virtuális gépek adatszint. Mivel csak egyetlen frissítési tartományt minden újraindítani egyszerre egy készletben, és az Azure biztosítja, hogy egy csoporton belüli virtuális gépek a tartalék tartományok vannak elosztva, akkor győződjön meg arról, hogy a készlet nem minden virtuális gép egyszerre lesz nem.

### <a name="set-up-bcdr"></a>BCDR beállítása

Az Azure-bA migrálásánál fontos megérteni, hogy az Azure platform olyan beépített rugalmassági funkciókat kínál, bár kell terveznie az Azure-alapú kihasználásához az Azure-funkciók és szolgáltatások magas rendelkezésre állást biztosító vészhelyreállítás és a biztonsági mentés.

- A BCDR-megoldás függ, hogy a vállalati célok, és az Azure üzembe helyezési stratégiához befolyásol. Infrastruktúra-szolgáltatás (IaaS) és a Platformszolgáltatás (PaaS) központi telepítések a BCDR különféle kihívásokat jelenthet.
- Egyszer helyen, a BCDR-megoldások kell vizsgálni rendszeresen ellenőrizni, hogy a stratégia működőképes marad.


## <a name="best-practice-back-up-your-data"></a>Ajánlott eljárás: Az adatok biztonsági mentése

A legtöbb esetben egy a helyszíni számítási feladatok kivonják a migrálás után, és a helyszíni adatok biztonsági mentése stratégiát kell kiterjesztett vagy cserélni. Ha telepít át a teljes adatközpontját az Azure-ba, tervezése és implementálása az Azure-technológiák használatával teljes biztonsági mentési megoldást kell, vagy külső integrált megoldásokat. 


### <a name="back-up-an-iaas-deployment"></a>Készítsen biztonsági másolatot egy IaaS-telepítés

Azure IaaS virtuális gépeken futó számítási feladatok fontolja meg ezek a biztonsági mentési megoldások:

- **Az Azure Backup**: Azure Windows és Linux rendszerű virtuális gépek alkalmazáskonzisztens biztonsági mentést biztosít.
- **A pillanatképek tárolási**: A blob Storage pillanatfelvételt.

#### <a name="azure-backup"></a>Azure Backup

Az Azure Backup biztonsági másolatot készít az Azure storage-ban tárolt adatok helyreállítási pontokat hoz létre. Az Azure Backup biztonsági mentése az Azure-beli Virtuálisgép-lemezeket, és az Azure Files (előzetes verzió). Az Azure Files adja meg a felhőben, az SMB-n keresztül elérhető fájlmegosztások.
   
Az Azure Backup segítségével többféle módon a virtuális gépek biztonsági mentése.

- **A virtuális gép beállításainak a közvetlen biztonsági mentést**: Akkor is virtuális gépek biztonsági mentése az Azure Backup szolgáltatással az Azure portal virtuális gép közül. Készítsen biztonsági másolatot a virtuális gép egyszer, és a nap, és a Virtuálisgép-lemez visszaállítása, igény szerint. Az Azure Backup elkészítette az app-kompatibilis data pillanatképek (VSS), nincs ügynök telepítve van a virtuális gépen.
- **Közvetlen biztonsági mentést a Recovery Services-tároló**: Biztonsági másolatot készíthet az IaaS-beli virtuális gépek az Azure biztonsági mentése Recovery Services-tároló üzembe helyezésével. Nyomon követését és a biztonsági másolatok kezelése egyetlen helyen, és részletes biztonsági mentési és helyreállítási lehetőségeket biztosít. Biztonsági mentés: legfeljebb napi háromszori, a fájl vagy mappa szintjén. App-t támogató nem fut, és a Linux nem támogatott. A Microsoft Azure Recovery Services-(MARS) ügynök telepítése minden egyes virtuális Gépet, készítsen biztonsági másolatot szeretne kell.
- **Az Azure Backup Server: A virtuális gép az Azure Backup Server védelmét**: Az Azure Backup Server az Azure Backup szolgáltatásban díjmentesen nyújtja. A virtuális gép biztonsági másolatot a helyi Azure Backup Server storage. Majd készítsen biztonsági másolatot az Azure Backup Server az Azure-tárolóban. Biztonsági mentés: alkalmazás a DNSSEC használatát, a gyakori biztonsági mentés és megőrzés keresztül teljes részletességgel. Az alkalmazás szintjén is biztonsági másolatot készíteni. Például úgy, hogy az SQL Server, a SharePoint biztonsági mentése

A biztonság érdekében az Azure Backup titkosítja az adatokat átvitel közben az AES 256 eljárással, és elküldi azt az HTTPS-kapcsolaton keresztül az Azure-bA. Az adatok biztonsági inaktív az Azure-ban van titkosítva [Storage Service Encryption (SSE)](https://docs.microsoft.com/azure/storage/common/storage-service-encryption?toc=%2fazure%2fstorage%2fqueues%2ftoc.json), és az adatok átvitelét és tárolását.


![Az Azure Backup](./media/migrate-best-practices-security-management/iaas-backup.png)
*az Azure Backup*

**tudj meg többet:**

- [Ismerje meg](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) biztonsági mentések különböző típusairól.
- [A biztonsági mentési infrastruktúrájának tervezése](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction) az Azure virtuális gépek.

#### <a name="storage-snapshots"></a>A pillanatképek tárolási

Az Azure virtuális gépek és az Azure Storage szolgáltatásban tárolódnak. 

- Pillanatképek rögzítése a blob állapotot egy adott időpontban időben.
- Az Azure Virtuálisgép-lemezek alternatív biztonsági mentési módszer pillanatkép készítése a storage-blobok, és másolja őket egy másik tárfiókba. 
- Egy teljes blob másolása vagy használjon egy növekményes pillanatkép másolása, csak a változásokat, és csökkentheti a tárhely.
- Külön óvintézkedés engedélyezheti a blob storage-fiókok a helyreállítható törlés. Ez a funkció engedélyezve van, a törölt blobok törlésre van kijelölve, de nem azonnal törölve. Az ideiglenes időszak alatt vissza tudja állítani a blobot.

**tudj meg többet:**

- [Ismerje meg](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) az Azure blob storage-bA.
- [Ismerje meg, hogyan](https://docs.microsoft.com/azure/storage/blobs/storage-blob-snapshots) blob-pillanatkép létrehozása.
- [Tekintse át a mintaforgatókönyv](https://azure.microsoft.com/blog/microsoft-azure-block-blob-storage-backup) a blob storage biztonsági mentéshez.
- [További információ](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) helyreállítható törlés.
- [Felülvizsgálat](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) Mi a teendő az Azure Storage leállása esetén?


#### <a name="third-party-backup"></a>Külső biztonsági mentése

Külső megoldások segítségével emellett biztonsági mentése Azure virtuális gépek és a storage-tárolók helyi tárba vagy más felhőszolgáltatóknál. [További](https://azuremarketplace.microsoft.com/marketplace/apps?search=backup&page=1) kapcsolatos biztonsági mentési megoldások az Azure Marketplace-en. 


### <a name="back-up-a-paas-deployment"></a>Készítsen biztonsági másolatot egy PaaS üzemelő példány


Ellentétben a saját virtuális gépek és infrastruktúra kezeléséhez, IaaS, paas-megoldás a modell platform és infrastruktúra szolgáltató által felügyelt a, hagyja, hogy arra koncentrálhasson, a core-alkalmazás logikai és a funkciókról. Számos különböző típusú a PaaS-szolgáltatások, az egyes szolgáltatások kiértékelendő külön-külön biztonsági mentés céljából. Két gyakori Azure PaaS-szolgáltatások – Azure SQL Database és az Azure Functions foglalkozunk.

#### <a name="back-up-azure-sql-database"></a>Az Azure SQL-adatbázis biztonsági mentése

Az Azure SQL Database egy teljes körűen felügyelt PaaS-adatbázismotor egy. Számos biztosítja az üzletmenet-folytonossági funkciókat, beleértve a biztonsági mentés automatizálása a.

- Az SQL Database automatikusan végrehajtja a heti teljes adatbázis biztonsági mentését, és a különbözeti biztonsági mentések 12 óránként. Tranzakciós naplók biztonsági mentését öt-tíz percenként megnyílik az adatbázis védelmét adatvesztéssel szemben.
- A biztonsági mentéseket átlátható és további használatáért nem számítunk fel.
- Biztonsági mentések georedundáns tárolás az RA-GRS tároló tárolja, és a párosított földrajzi régióba replikálja.
- A biztonsági másolatok attól függ, hogy a vásárlási modell. DTU-alapú szolgáltatásszintek nyissa meg a hét napig az alapszintű, egyéb csomagokra vonatkozó 35 napon belül.
- Egy adatbázis a megőrzési időtartamon belül visszaállíthatja egy pont kötött. Is helyreállíthatja a törölt adatbázis visszaállítása egy másik földrajzi régióban, vagy hosszú távú adatmegőrzési (LTR) az adatbázis-e a hosszú távú biztonsági másolatból.


![Az Azure SQL-backup](./media/migrate-best-practices-security-management/sql-backup.png)
*Azure SQL biztonsági mentése*

**tudj meg többet:**
- [Automatikus biztonsági mentések](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups) SQL Database-hez.
- [Adatbázis helyreállítása](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups) segítségével automatikus biztonsági mentéseket.

 
#### <a name="back-up-azure-functions"></a>Készítsen biztonsági másolatot az Azure Functions

Mivel az Azure Functions függvények több vagy kevesebb mint kód, biztonsági másolatot kell készíteni őket ugyanazokkal a módszerekkel, amellyel védeni a kód például a verziókövetés a GitHub vagy az Azure DevOps-szolgáltatásokkal

**tudj meg többet:**

- [Az adatvédelem](https://docs.microsoft.com/azure/devops/articles/team-services-security-whitepaper?view=vsts) Azure fejlesztők és üzemeltetők számára.

## <a name="best-practice-set-up-disaster-recovery"></a>Ajánlott eljárás: Vészhelyreállítás beállítása 

Adatok védelme mellett BCDR tervezési érdemes megfontolni, így az alkalmazások és számítási feladatok nem állnak katasztrófa esetén. 

### <a name="set-up-disaster-recovery-for-iaas-apps"></a>Állítsa be az IaaS-alkalmazások vészhelyreállítása

Érdemes lehet ezeket a megoldásokat az Azure IaaS virtuális gépek és az Azure storage futó számítási feladatokat:

- **Az Azure Site Recovery**: Egy elsődleges egy másodlagos régióba az Azure virtuális gépek replikálását koordinálja. Ha valamilyen okból kimaradás lép fel, a rendszer átadja a feladatokat az elsődleges régióból a másodlagos, és a felhasználók továbbra is az alkalmazások eléréséhez. A következők vissza a normál értékre, ha átadja az elsődleges régióba.
- **Az Azure storage**: Az Azure beépített rugalmasság és a különböző típusú tárolóhelyek a magas rendelkezésre állást kínál:

#### <a name="azure-site-recovery"></a>Azure Site Recovery 

Az Azure Site Recovery az elsődleges Azure-szolgáltatás biztosító Azure virtuális gépek online állapotba helyezhetők, és a VM-alkalmazások elérhetővé, ha valamilyen okból kimaradás lép fel a rendszer. A Site Recovery egy elsődleges virtuális gépek másodlagos Azure-régióba replikálja. Vészhelyreállítási feladatokat, amikor feladatátvételt a virtuális gépek az elsődleges régióban, és továbbra is elérhesse őket a szokásos módon a másodlagos régióban. Műveletek visszatér a normális szintre, amikor, visszaadhatja a virtuális gépek biztonsági mentéséhez az elsődleges régióba.


![Az Azure Site Recovery](./media/migrate-best-practices-security-management/site-recovery.png)
*Site Recovery*

**tudj meg többet:**
- [Felülvizsgálat](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-disaster-recovery-guidance) az Azure virtuális gépek vészhelyreállítási forgatókönyveket.
- [Ismerje meg, hogyan](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-replicate-after-migration) vészhelyreállítás beállítása egy Azure virtuális gép áttelepítése után.

#### <a name="azure-storage"></a>Azure Storage tárterület

Az Azure storage beépített rugalmasság és a magas rendelkezésre állású replikálva van.

-   **Georedundáns tárolás (GRS)**: Védelmet, ha egy régióra kiterjedő szolgáltatáskimaradás, az 99,99999999999999 %-ában (16 9-es) tartós objektumok egy adott évben.
    - Storage-adatok, amelyhez az elsődleges régió párosítva van a másodlagos régióba replikálja.
    - Ha az elsődleges régió leáll, és a Microsoft a másodlagos régióba történő feladatátvételét kezdeményezi, kell olvasási hozzáféréssel az adatokat.
- **Írásvédett georedundáns tárolás (RA-GRS)**: Egy régióra kiterjedő szolgáltatáskimaradás nyújt védelmet.
    - Storage-adatok a másodlagos régióba replikálja.
    - A másodlagos régióhoz, függetlenül attól a Microsoft feladatátvételét kezdeményezi a replikált adatok olvasási hozzáféréssel rendelkezik garantált. Ha két vagy több adatközpontok ugyanabban a régióban lehet, hogy probléma lehet, még az adatok földrajzilag különálló régióban továbbra is elérhető.
-   **Zónaredundáns tárolás (ZRS)**:  Adatközpont leállása ellen védi.
    - A ZRS replikálja az adatokat szinkron módon három tárolási fürtöket egy régió között. Fürtök és fizikailag el, és mindegyik a saját rendelkezésre állási zónában található.
    - Ha katasztrófa történik, a tárolás továbbra is elérhetők. A ZRS kell lennie a minimális cél alapvető fontosságú számítási feladatokhoz.



**tudj meg többet:**

- [Ismerje meg](https://docs.microsoft.com/azure/storage/common/storage-redundancy) az Azure storage-replikációt.


### <a name="set-up-disaster-recovery-for-paas-workloads"></a>Állítsa be a PaaS számítási feladatok vészhelyreállítása

Vegyünk például katasztrófa példákban szereplő PaaS számítási feladatok vonatkozó helyreállítási beállításokat.

#### <a name="disaster-recovery-of-azure-sql-server"></a>Az Azure SQL Server vészhelyreállítása

Számos különböző beállítások minden egyes, mely negatív hatással az adatvesztést, a helyreállítás időtartamát és a költségek.

Regionális üzemkimaradások utáni helyreállításon, és a katasztrofális hibák elleni rugalmasság biztosításához használhatja feladatátvételi csoportok és az aktív georeplikáció

- **Aktív georeplikáció**: Aktív georeplikáció gyors vész-helyreállítási üzembe, ha egy adatközpont-kimaradás során, vagy egy elsődleges adatbázis nem hozható létre kapcsolat.
    - Georeplikáció folyamatosan hoz létre az adatbázis olvasható replikát az azonos vagy eltérő régiókban legfeljebb négy másodlagos példány hozható létre.
    - Egy kimaradás átadja a feladatokat egy másodlagos régiót, és az adatbázis online állapotba kerüljön.
- **Automatikus feladatátvételi csoportok**: Automatikus feladatátvételi csoportok kiterjesztése az aktív georeplikáció több adatbázis transzparens feladatátvétellel.
    - Egy automatikus feladatátvételi csoport biztosít az aktív georeplikáció csoport szintű adatbázis-replikáció és automatikus feladatátvételi hatékony absztrakciója.
    - Létrehozhat egy feladatátvételi csoportot, amely tartalmaz egy vagy több elsődleges adatbázis csak olvasható replika az elsődleges adatbázisok, a figyelők, amelyek minden kiszolgálón, és a egy automatikus feladatátvételi szabályzat üzemeltető másodlagos kiszolgálót futtató elsődleges kiszolgáló.
    - A megadott figyelő végpontokat szükségtelenné teszi a feladatátvételt követően az SQL-kapcsolati karakterlánc módosítása.
- **A GEO-visszaállítás**: 
    -   GEO-visszaállítás lehetővé teszi az adatbázis helyreállítása az egy másik régióban. Az összes Azure-adatbázis automatikus biztonsági mentés a háttérben egy másodlagos régióba replikálja. Azt mindig visszaállítja az adatbázis biztonsági mentési fájlokat a másodlagos régió tárolja másolatából.
-   **Zónaredundáns adatbázisok** beépített támogatást nyújt az Azure rendelkezésre állási zónák.
    - Zónaredundáns adatbázisok magas rendelkezésre állás javítása az Azure SQL Server data center hiba esetén.
    - A zóna-redundancia elhelyezhet redundáns adatbázis-replikák belül különböző rendelkezésre állási zónák egy régióban. 



![Georeplikáció](./media/migrate-best-practices-security-management/geo-replication.png)
*Georeplikáció útján*

**tudj meg többet:**
- [Ismerje meg](https://docs.microsoft.com/azure/sql-database/sql-database-high-availability) magas rendelkezésre állás az Azure SQL-kiszolgálót.
- [Olvasási](https://azure.microsoft.com/blog/azure-sql-databases-disaster-recovery-101/) Azure SQL-adatbázisok 101 vész-helyreállítási.
- [Áttekintés](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) aktív georeplikációs és feladatátvételi csoportok.
- [Ismerje meg](https://docs.microsoft.com/azure/sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery) tervezése vészhelyreállításhoz.
- [Ajánlott eljárások beolvasása](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview#best-practices-of-using-failover-groups-for-business-continuity) feladatátvételi csoportok számára.
- [Ajánlott eljárások beolvasása](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-security-config) biztonság geo-visszaállítás vagy a feladatátvétel után.
- [Ismerje meg](https://docs.microsoft.com/azure/sql-database/sql-database-high-availability#zone-redundant-configuration) redundancia. zóna
- [Ismerje meg, hogyan](https://docs.microsoft.com/azure/sql-database/sql-database-disaster-recovery-drills) egy vészhelyreállítási próba végrehajtása SQL database-hez.

### <a name="disaster-recovery-for-azure-functions"></a>Vészhelyreállítás az Azure Functions szolgáltatáshoz

Ha nem sikerül a számítási infrastruktúra Azure-ban, Azure-függvényalkalmazás esetleg elérhetetlenné válik.

- Az ilyen lehetőségét minimalizálása érdekében használja a két, különböző régióban üzembe helyezett függvényalkalmazás.
- Az Azure Traffic Manager beállítható úgy, hogy az elsődleges függvényalkalmazás észlelheti a problémákat, és automatikusan átirányítja a forgalmat a függvényalkalmazáshoz a másodlagos régióban
- A georedundáns tárolás a TRAFFIC Manager lehetővé teszi ugyanannak a függvénynek regionális hiba esetén több régióban


![A TRAFFIC Manager](./media/migrate-best-practices-security-management/traffic-manager.png)
*a Traffic Manager*

**tudj meg többet:**

- [Ismerje meg](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications) vészhelyreállítás Azure-alkalmazások.
- [Ismerje meg](https://docs.microsoft.com/azure/azure-functions/durable/durable-functions-disaster-recovery-geo-distribution) vészhelyreállítás és földrajzi elosztás tartós az Azure Functions.


## <a name="best-practice-use-managed-disks-and-availability-sets"></a>Ajánlott eljárás: A felügyelt lemezek és a rendelkezésre állási csoportok használata

Az Azure rendelkezésre állási csoportokat használ, logikusan csoportosíthatja a virtuális gépek és a egy készlet más erőforrásairól elkülöníteni a virtuális gépek. Virtuális gépet egy rendelkezésre állási csoportban vannak elosztva a különböző alrendszereket, helyi hibák ellen védelmet biztosító több tartalék tartomány, és úgy, hogy a készlet nem minden virtuális gép újraindítása egyszerre is több tartományban futó.

Az Azure Managed Disks az Azure IaaS virtuális gépek, a Lemezkezelés egyszerűbbé a virtuális gépek lemezeihez társított storage-fiókok kezelésével.

- Azt javasoljuk, hogy felügyelt lemezek használata, ahol csak lehetséges. Csak akkor adja meg a kívánt szeretné használni, és a lemez kívánt méretét kell, és az Azure létrehozza és felügyeli a lemezt Ön helyett, a háttérben tárolási típusát.
- Átalakíthatja a meglévő felügyelt lemezeket.
- Virtuális gépek létre kell hoznia a rendelkezésre állási csoportokban nagy rugalmasságot és rendelkezésre állás érdekében. Tervezett vagy nem tervezett leállások esetén, a rendelkezésre állási csoportok biztosítják, hogy a csoportban lévő virtuális gépek legalább egyikének továbbra is elérhető.


![A Managed disks](./media/migrate-best-practices-security-management/managed-disks.png)
*felügyelt lemezek*

**tudj meg többet:**
- [Áttekintés](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) , a felügyelt lemezek.
- [Ismerje meg](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks) konvertálása a felügyelt lemezeket.
- [Ismerje meg, hogyan](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) Windows-beli virtuális gépek rendelkezésre állásának kezelése.


## <a name="best-practice-monitor-resource-usage-and-performance"></a>Ajánlott eljárás: Erőforrás-használat monitorozása és teljesítmény 

Előfordulhat, hogy áthelyezzük a számítási feladatok Azure-bA annak környezetéről skálázási funkciók. Azonban a számítási feladatok áthelyezése nem jelenti azt, hogy az Azure automatikusan fogja végrehajtani a beavatkozás nélküli méretezés. Példa:

- Ha a szervezet, amely a 300 %-kal nagyobb forgalmat új TV hirdetmény leküldéses értesítések, ez hibát okozhat hely rendelkezésre állását. Az újonnan áttelepített alkalmazások és szolgáltatások hozzárendelt korlátok és összeomlási ütközhet.
- Egy másik példa lehet egy elosztott-szolgáltatásmegtagadásos (DDoS-) támadás a áttelepített számítási feladatokra. Ebben az esetben előfordulhat, hogy nem szeretné méretezni, de megakadályozza, hogy a támadások forrásának az erőforrások elérése.

E két kapcsolattípushoz eltérő a megoldásuk is, de mindkét Önnek kell abba, mi történik használatának és teljesítményének figyeléséhez.

- Az Azure Monitor segítségével ezeket a metrikákat tervezőfelületére, és adja meg a választ a figyelmeztetések, automatikus skálázást, az event hubs, a logic apps és több.
- Azure monitoring mellett a külső SIEM-alkalmazás figyelése az Azure naplózási és a teljesítmény események naplóinak integrálható.


![Az Azure Monitor](./media/migrate-best-practices-security-management/monitor.png)
*az Azure Monitor*

**tudj meg többet:**

- [Ismerje meg](https://docs.microsoft.com/azure/azure-monitor/overview) az Azure Monitor.
- [Ajánlott eljárások beolvasása](https://docs.microsoft.com/azure/architecture/best-practices/monitoring) a monitorozási és diagnosztikai.
- [Ismerje meg](https://docs.microsoft.com/azure/architecture/best-practices/auto-scaling) automatikus skálázást.
- [Ismerje meg, hogyan](https://docs.microsoft.com/azure/security-center/security-center-export-data-to-siem) az Azure data átirányítása egy SIEM-eszközével.

## <a name="best-practice-enable-diagnostic-logging"></a>Ajánlott eljárás: Diagnosztikai naplózás engedélyezése

Azure-erőforrások készítése a naplózási metrikákat és telemetriai adatok valós számát.

- Alapértelmezés szerint a legtöbb erőforrástípusok nincsenek engedélyezve diagnosztikai naplózás.
- Diagnosztikai naplózás engedélyezése minden erőforrásban, naplózási adatokat kérdezhet le, és hozhat létre riasztásokat és a forgatókönyvek alapján.
- Diagnosztikai naplózás engedélyezése esetén az egyes erőforrások lesz egy meghatározott készletének kategóriák. Egy vagy több naplózási kategóriát, és a egy helyet a naplóadatok választja. Naplók elküldött egy tárfiókba, az eseményközpontok felé, vagy a Log Analytics szolgáltatásba. 


![Diagnosztikai naplózás](./media/migrate-best-practices-security-management/diagnostics.png)
*diagnosztikai naplózás*

**tudj meg többet:**

- [Ismerje meg](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) gyűjtése és felhasználása naplóadatokat.
- [Ismerje meg, mi támogatott](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-diagnostic-logs-schema) diagnosztikai naplózás.


## <a name="best-practice-set-up-alerts-and-playbooks"></a>Ajánlott eljárás: Állítsa be a riasztások és forgatókönyvek

A diagnosztikai naplózás engedélyezve van az Azure-erőforrásokhoz, elkezdheti létrehozni egyéni riasztások a naplózási adatok használatával.

- Proaktív módon kap értesítést, ha a feltételek a figyelési adatok találhatók. Majd meg lehet oldani problémákat, mielőtt felhasználója láthatja őket. Többek között a metrikaértékek, a naplóbeli keresési lekérdezések, a tevékenységnapló-események, a platform állapota és a webhely rendelkezésre állási is riasztás.
- Ha a figyelmeztetéseket, egy Logic App-forgatókönyv is futtathatja. Egy forgatókönyv segítségével automatizálható és összehangolható egy meghatározott riasztásra adott válasz. Forgatókönyvek az Azure Logic Apps alapulnak. Logic App-sablonok segítségével forgatókönyvek létrehozása, vagy létrehozhatja a sajátját.
- Egyszerű példaként riasztást aktivál, ha a port vizsgálat történik, szemben a hálózati biztonsági csoport is létrehozhat.  Beállíthat egy futtató és a vizsgálati forrás IP-címének zárol forgatókönyv.
- Egy másik példa lehet egy memóriavesztés alkalmazást.  A memóriahasználat beolvasása egy adott időpontra, amikor egy forgatókönyv újraindíthatja az a folyamat.


![Riasztások](./media/migrate-best-practices-security-management/alerts.png)
*riasztások*

**tudj meg többet:**
- [Ismerje meg](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-alerts) riasztásokat.
- [Ismerje meg](https://docs.microsoft.com/azure/security-center/security-center-playbooks) , amely a válaszadás a riasztásokra a Security Center biztonsági forgatókönyvei.

## <a name="best-practice-use-the-azure-dashboard"></a>Ajánlott eljárás: Használja az Azure-irányítópulton

Az Azure Portalon egy webes egységes konzol, amely lehetővé teszi, hogy hozhat létre, kezelhet és monitorozhat egyszerű webes alkalmazásoktól az összetett felhőalapú alkalmazásokig minden rendben. Az irányítópultja testre szabható, és kisegítő lehetőségekkel is rendelkezik.
- Több irányítópultokat hozhat létre, és megoszthatja másokkal, akik hozzáférhetnek az Azure-előfizetést a.
- A megosztott modellel rendelkezik látható-e az Azure környezetbe, így a felhőbeli rendszerek kezelésekor proaktív kell a hozzájuk.


![Azure-irányítópulton](./media/migrate-best-practices-security-management/dashboard.png)
*Azure-irányítópulton*

**tudj meg többet:**

- [Ismerje meg, hogyan](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) hozzon létre egy irányítópultot.
- [Ismerje meg](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards-structure) irányítópult struktúra.


## <a name="best-practice-understand-support-plans"></a>Ajánlott eljárás: Támogatási csomagok ismertetése

Később szüksége lesz működhet együtt a támogatási munkatársak vagy a Microsoft támogatási csapattal. Létfontosságú a házirendek és a támogatási eljárások kellene vész-helyreállítási forgatókönyvek során. Emellett a rendszergazdák és a támogatási munkatársak kell tartani a ezek a házirendek megvalósítására.

- Az, hogy egy Azure-szolgáltatási probléma hatással van a számítási feladatok nem túl valószínű esetben, a rendszergazdák tudnia kell a Microsoft támogatási jegyet is küldhet a leginkább megfelelő és hatékony módon.
- Ismerje meg a különböző támogatási csomagok, az Azure-ban érhető el. Ezek között válaszidők dedikált fejlesztő-példányok, a Premier szintű támogatás és a egy kisebb, mint 15 perces válaszidő.


![Támogatási csomagok](./media/migrate-best-practices-security-management/support.png)
*támogatási csomagok*

**tudj meg többet:**
- [Áttekintés](https://azure.microsoft.com/support/options/) az Azure támogatási csomagok.
- [Ismerje meg](https://azure.microsoft.com/support/legal/sla/) szolgáltatói szerződések (SLA).

## <a name="best-practice-manage-updates"></a>Ajánlott eljárás: Frissítések kezelése

Tartja az Azure virtuális gépek frissítése a legújabb operációs rendszerrel és a szoftverfrissítések egy nagyméretű adathalmazokat. Minden virtuális gép, mely szükséges frissítések ki, és automatikusan leküldéses azonosítani a ezeket a frissítéseket a rendkívül értékes illesztésének lehetősége.

- Az Azure Automation Update Management segítségével üzembe helyezett Windows és Linux rendszerű számítógépek fut, az Azure-ban, a helyszínen, és egyéb felhőszolgáltatók a gép operációs rendszer frissítéseinek kezelése. 
- Használja az Update Management segítségével mérheti fel gyorsan a állapota minden ügynökszámítógépen az elérhető frissítések, és kezelheti a frissítés telepítése.
- Az Update Management a virtuális gépek közvetlenül az Azure Automation-fiók engedélyezése. Egyetlen virtuális gép (VM) lapján az Azure Portalon is frissítheti.
- Emellett Azure virtuális gépeket a System Center Configuration Managerrel is regisztrálható. Sikerült ezután a Configuration Manager számítási feladatok migrálása az Azure-ba, és hajtsa végre a jelentéskészítés és a szoftverfrissítések egy közös webes felületről.


![Virtuális gép frissítéseinek](./media/migrate-best-practices-security-management/updates.png)
*frissítések*

**tudj meg többet:**

- [Ismerje meg](https://docs.microsoft.com/azure/automation/automation-update-management) frissítéskezelés az Azure-ban.
- [Ismerje meg, hogyan](https://docs.microsoft.com/azure/automation/oms-solution-updatemgmt-sccmintegration) az update management Configuration Managerrel integrálva.
- [Gyakori kérdések](https://docs.microsoft.com/sccm/core/understand/configuration-manager-on-azure) Configuration Manager alkalmazásáról az Azure-ban.


## <a name="implement-a-change-management-process"></a>A változáskezelési folyamatot megvalósítása

Csakúgy, mint bármely éles rendszer, így bármilyen módosítása hatással lehet a környezet. A változáskezelési folyamatot ahhoz, hogy hajtsa végre a módosításokat éles rendszereket hamarosan kérelmek igénylő egy értékes hozzáadása a migrált környezetben.

- Ajánlott eljárás keretrendszereket a változáskezeléshez hangsúlyozásával segítse a rendszergazdák és tanácsadók hozhat létre.
- Használhatja az Azure Automation és a konfigurációkezelés és az áttelepített munkafolyamatokhoz a változáskövetés.
- Változáskezelési folyamatot kényszerítése, ha auditnaplók használatával Azure módosítás hivatkozásra naplók elvileg (vagy sem) meglévő változáskéréseket. Így ha egy módosítás nélkül megfelelő változáskérést, vizsgálja meg a folyamat a hiba okát.

Az Azure Change Tracking megoldás az Azure automationben rendelkezik:

- A megoldást Windows és Linux-szoftver és a fájlokhoz, a Windows-beállításkulcsok, a Windows-szolgáltatások és a Linux-démonok nyomon követi a módosításokat.
- Változások a figyelt kiszolgálók kapnak a Log Analytics szolgáltatás a felhőben lévő feldolgozása.
- A fogadott adatokat logikát alkalmaz, és a felhőszolgáltatás-adatait rögzíti.
- A Change Tracking irányítópultján egyszerűen megtekintheti a kiszolgáló-infrastruktúrájában végrehajtott változtatásokat.


![Változáskezelés](./media/migrate-best-practices-security-management/change.png)
*változáskezelés*

**tudj meg többet:**

- [Ismerje meg](https://docs.microsoft.com/azure/automation/automation-change-tracking) a változáskövetés.
- [Ismerje meg](https://docs.microsoft.com/azure/automation/automation-intro) Azure automatizálási képességeivel.




## <a name="next-steps"></a>További lépések 

Tekintse át a többi ajánlott eljárásokat:


- [Ajánlott eljárások](migrate-best-practices-networking.md) hálózati migrálás után.
- [Ajánlott eljárások](migrate-best-practices-costs.md) cost Management a migrálás után.








