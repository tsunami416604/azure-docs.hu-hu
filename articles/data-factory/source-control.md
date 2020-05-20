---
title: Verziókövetés
description: Ismerje meg, hogyan konfigurálhatja a verziókövetés Azure Data Factory
services: data-factory
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.reviewer: ''
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/30/2020
ms.openlocfilehash: 2d6f667b6a49520dfe210fd797a828328899b634
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83674591"
---
# <a name="source-control-in-azure-data-factory"></a>Verziókövetés a Azure Data Factory
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Alapértelmezés szerint a Azure Data Factory felhasználói felületi élmény (UX) szerzője közvetlenül a The Refactory szolgáltatással szemben. Ez a felhasználói élmény a következő korlátozásokkal jár:

- A Data Factory szolgáltatás nem tartalmaz olyan tárházat, amely a változtatások JSON-entitásait tárolja. A módosítások mentésének egyetlen módja az **összes közzététele** gomb, és az összes módosítás közvetlenül a Refactory szolgáltatásban lesz közzétéve.
- A Data Factory szolgáltatás nem az együttműködéshez és a verziókövetés-vezérléshez van optimalizálva.

A jobb authoring Experience Azure Data Factory lehetővé teszi egy git-tárház konfigurálását az Azure Repos vagy a GitHub használatával. A git egy verziókövető rendszer, amely megkönnyíti a változások nyomon követését és együttműködését. Ebből az oktatóanyagból megtudhatja, hogyan konfigurálhat és dolgozhat a git-tárházban, valamint kiemelheti az ajánlott eljárásokat és a hibaelhárítási útmutatót.

> [!NOTE]
> Az Azure-beli adatgyári git-integráció nem érhető el a Azure Government felhőben.

## <a name="advantages-of-git-integration"></a>A Git-integráció előnyei

Az alábbi lista a git-integráció egyes előnyeit mutatja be a szerzői műveletekhez:

-   **Verziókövetés:** Mivel az adat-előállító számítási feladatai elengedhetetlenek, a gyárat a git segítségével integrálva számos forrás-ellenőrzési előnyt használhat, például az alábbiakat:
    -   A módosítások nyomon követésének és naplózásának lehetősége.
    -   A hibákat bevezetett változások visszaállíthatók.
-   **Részleges mentés:** A adatfeldolgozó szolgáltatással végzett szerzői műveletek során a módosítások nem menthetők piszkozatként, és az összes közzétételnek át kell adnia az adatfeldolgozó-ellenőrzést. Függetlenül attól, hogy a folyamat nem fejeződött be, vagy egyszerűen nem kívánja elveszíteni a változásokat a számítógép összeomlása esetén, a git-integráció lehetővé teszi a adat-előállító erőforrásainak növekményes módosítását, függetlenül attól, hogy milyen állapotban vannak. A git-tárház konfigurálása lehetővé teszi a módosítások mentését, így csak akkor tehet közzé módosításokat, ha az Ön elégedettségét tesztelte.
-   **Együttműködés és felügyelet:** Ha több csapattag is járul hozzá ugyanahhoz a gyárhoz, érdemes lehet a csapattársait egy kód-felülvizsgálati folyamattal együttműködni egymással. A gyárat úgy is beállíthatja, hogy ne minden közreműködő legyen egyenlő jogosultsággal. Egyes csapattagok csak a git-n keresztül módosíthatják a módosításokat, és csak a csapat bizonyos tagjai jogosultak a módosítások közzétételére a gyárban.
-   **Jobb CI/CD:**  Ha [folyamatos kézbesítési folyamattal](continuous-integration-deployment.md)több környezetbe helyez üzembe, a git-integráció megkönnyíti az egyes műveleteket. Példák ilyen műveletekre:
    -   Konfigurálja úgy a kiadási folyamatot, hogy automatikusan elindítson, amint a "dev" gyárban történt módosítások történnek.
    -   Szabja testre a gyári tulajdonságokat a Resource Manager-sablonban paraméterként elérhetővé. Hasznos lehet, ha csak a szükséges tulajdonságokat szeretné megtartani paraméterekként, és minden más nehezen kódolva van.
-   **Jobb teljesítmény:** A git-integrációval rendelkező átlagos gyár tízszer gyorsabban töltődik be, mint egy szerzői művelet az adatfeldolgozó szolgáltatásban. Ez a teljesítmény-fejlesztés az, hogy az erőforrások a git használatával töltődnek le.

> [!NOTE]
> A Data Factory szolgáltatással közvetlenül történő szerzői műveletek a git-tárház konfigurálásakor le vannak tiltva a Azure Data Factory UX-ben. A módosításokat közvetlenül a PowerShell vagy egy SDK használatával végezheti el a szolgáltatásban.

## <a name="author-with-azure-repos-git-integration"></a>Létrehozás Azure Repos Git-integrációval

A Visual authoring with Azure Repos git-integrációja támogatja a forrás-és együttműködési munkát az adatfeldolgozó-folyamatokban. Az Azure Repos git szervezeti adattárral társíthat egy adatgyárat a verziókövetés, az együttműködés, a verziószámozás és így tovább. Egyetlen Azure Repos git-szervezet több tárházral is rendelkezhet, de egy Azure-beli Repos git-tárház csak egy adatgyárhoz társítható. Ha nem rendelkezik Azure Repos-szervezettel vagy-adattárral, kövesse az [alábbi utasításokat](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student) az erőforrások létrehozásához.

> [!NOTE]
> A parancsfájlokat és az adatfájlokat egy Azure Repos git-tárházban tárolhatja. A fájlokat azonban manuálisan kell feltöltenie az Azure Storage-ba. Egy Data Factory folyamat nem tölt fel automatikusan egy Azure Repos git-tárházban tárolt parancsfájl-vagy adatfájlt az Azure Storage-ba.

### <a name="configure-an-azure-repos-git-repository-with-azure-data-factory"></a>Azure Repos git-tárház konfigurálása Azure Data Factory

Az Azure Repos git-tárházat két módszer használatával konfigurálhatja egy adatgyárral.

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>1. konfigurációs módszer: Azure Data Factory Kezdőlap

A Azure Data Factory kezdőlapján válassza a **kódlap beállítása**lehetőséget.

![Azure Repos-programkódok tárházának konfigurálása](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>2. konfigurációs módszer: UX authoring Canvas
A Azure Data Factory UX authoring vásznon válassza a **Data Factory** legördülő menüt, majd válassza a **kódlap beállítása**lehetőséget.

![A Code adattár beállításainak konfigurálása az UX-létrehozáshoz](media/author-visually/configure-repo-2.png)

Mindkét módszer megnyithatja a tárház beállításainak konfigurációs paneljét.

![A kódlap beállításainak konfigurálása](media/author-visually/repo-settings.png)

A konfiguráció ablaktáblán a következő Azure Repos-adattár beállításai láthatók:

| Beállítás | Description | Érték |
|:--- |:--- |:--- |
| **Tárház típusa** | Az Azure Repos Code adattár típusa.<br/> | Azure DevOps git vagy GitHub |
| **Azure Active Directory** | Az Azure AD-bérlő neve. | `<your tenant name>` |
| **Azure Repos-szervezet** | Az Azure Repos-szervezet neve. Az Azure Repos-szervezet nevét itt találja: `https://{organization name}.visualstudio.com` . Bejelentkezhet az [Azure Repos-szervezetbe](https://www.visualstudio.com/team-services/git/) a Visual Studio-profil eléréséhez, és megtekintheti a tárházait és projektjeit. | `<your organization name>` |
| **Projektnév** | Azure Repos-projekt neve. Az Azure Repos-projekt nevét itt találja: `https://{organization name}.visualstudio.com/{project name}` . | `<your Azure Repos project name>` |
| **RepositoryName** | Az Azure Repos-kód tárházának neve. Az Azure Repos-projektek git-adattárakat tartalmaznak, amelyekkel a projekt növekedésével kezelhetők a forráskód. Létrehozhat egy új tárházat, vagy használhat olyan meglévő tárházat, amely már szerepel a projektben. | `<your Azure Repos code repository name>` |
| **Együttműködési ág** | A közzétételhez használt Azure Repos együttműködési ág. Alapértelmezés szerint a `master` . Módosítsa ezt a beállítást abban az esetben, ha egy másik ág erőforrásait közzé szeretné tenni. | `<your collaboration branch name>` |
| **Gyökérmappa** | Az Azure Repos együttműködési ág gyökérkönyvtára. | `<your root folder name>` |
| **Meglévő Data Factory-erőforrások importálása adattárba** | Itt adhatja meg, hogy a rendszer importálja-e a meglévő adatok gyári erőforrásait az UX **authoring vászonból** egy Azure Repos git-tárházba. Jelölje be a jelölőnégyzetet az adatfeldolgozó-erőforrások a társított git-tárházba való importálásához JSON formátumban. Ez a művelet egyenként exportálja az egyes erőforrásokat (azaz a társított szolgáltatásokat és adatkészleteket külön JSON-ba exportálja). Ha ez a mező nincs bejelölve, a meglévő erőforrások nem kerülnek importálásra. | Kijelölt (alapértelmezett) |
| **Erőforrás importálása a következőbe** | Meghatározza, hogy az adat-előállító erőforrásait (folyamatokat, adatkészleteket, társított szolgáltatásokat stb.) milyen ágakba importálja a rendszer. Az erőforrásokat a következő ágak egyikére importálhatja: a. Együttműködés: b. Hozzon létre új c-t. Meglévő használata |  |

> [!NOTE]
> Ha a Microsoft Edge-t használja, és nem lát értékeket az Azure DevOps-fiók legördülő listájában, adja hozzá a https://*. VisualStudio. com kiterjesztést a megbízható helyek listájához.

### <a name="use-a-different-azure-active-directory-tenant"></a>Másik Azure Active Directory bérlő használata

Az Azure Repos git-tárháza lehet egy másik Azure Active Directory bérlő. Másik Azure Active Directory-bérlő megadásához rendszergazdai engedélyekkel kell rendelkeznie az Ön által használt Azure-előfizetésben.

### <a name="use-your-personal-microsoft-account"></a>Személyes Microsoft-fiók használata

Ha személyes Microsoft-fiók szeretne használni a git-integrációhoz, összekapcsolhatja a személyes Azure-tárházat a szervezete Active Directory.

1. Adja hozzá személyes Microsoft-fiókét a szervezet Active Directory vendégként. További információ: [Azure Active Directory B2B együttműködéssel rendelkező felhasználók hozzáadása a Azure Portal](../active-directory/b2b/add-users-administrator.md).

2. Jelentkezzen be a Azure Portalba személyes Microsoft-fiókával. Ezután váltson a szervezet Active Directoryra.

3. Nyissa meg az Azure DevOps szakaszt, ahol most megjelenik a személyes tárháza. Válassza ki a tárházat, és kapcsolódjon Active Directory.

Ezeket a konfigurációs lépéseket követően a saját tárháza akkor érhető el, ha a Data Factory felhasználói felületen beállítja a git-integrációt.

Az Azure Repos szervezete Active Directoryhoz való csatlakoztatásával kapcsolatos további információkért lásd: [Az Azure DevOps-szervezet csatlakoztatása Azure Active Directoryhoz](/azure/devops/organizations/accounts/connect-organization-to-azure-ad).

## <a name="author-with-github-integration"></a>Létrehozás GitHub-integrációval

A GitHub-integrációval rendelkező vizuális szerzői műveletek támogatják a forrás-és együttműködési munkát a saját adatfeldolgozó-folyamataiban. Egy GitHub-fiók adattárral társíthat egy adatgyárat a verziókövetés, az együttműködés, a verziószámozás érdekében. Egyetlen GitHub-fióknak több tárháza is lehet, de egy GitHub-tárház csak egy adatgyárhoz társítható. Ha nem rendelkezik GitHub-fiókkal vagy-adattárral, kövesse az [alábbi utasításokat](https://github.com/join)   az erőforrások létrehozásához.

A GitHub-integráció Data Factory a nyilvános GitHub (azaz [https://github.com](https://github.com) ) és a GitHub Enterprise használatát is támogatja. A nyilvános és a privát GitHub-adattárakat Data Factory is használhatja, ha már rendelkezik olvasási és írási engedéllyel a GitHubon lévő tárházhoz.

GitHub-tárház konfigurálásához rendszergazdai jogosultságokkal kell rendelkeznie a használt Azure-előfizetéshez.

A szolgáltatás kilenc perces bemutatása és bemutatása esetén tekintse meg a következő videót:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Azure-Data-Factory-visual-tools-now-integrated-with-GitHub/player]

### <a name="configure-a-github-repository-with-azure-data-factory"></a>GitHub-adattár konfigurálása Azure Data Factory

A GitHub-tárházat két módszerrel is konfigurálhatja egy adatelőállítóval.

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>1. konfigurációs módszer: Azure Data Factory Kezdőlap

A Azure Data Factory kezdőlapján válassza a **kódlap beállítása**lehetőséget.

![Azure Repos-programkódok tárházának konfigurálása](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>2. konfigurációs módszer: UX authoring Canvas

A Azure Data Factory UX authoring vásznon válassza a **Data Factory** legördülő menüt, majd válassza a **kódlap beállítása**lehetőséget.

![A Code adattár beállításainak konfigurálása az UX-létrehozáshoz](media/author-visually/configure-repo-2.png)

Mindkét módszer megnyithatja a tárház beállításainak konfigurációs paneljét.

![GitHub-adattár beállításai](media/author-visually/github-integration-image2.png)

A konfigurációs ablaktábla a GitHub-tárház következő beállításait jeleníti meg:

| **Beállítás** | **Leírás**  | **Érték**  |
|:--- |:--- |:--- |
| **Tárház típusa** | Az Azure Repos Code adattár típusa. | GitHub |
| **A GitHub Enterprise használata** | Jelölőnégyzet a GitHub Enterprise kiválasztásához | nincs kiválasztva (alapértelmezett) |
| **GitHub Enterprise URL-cím** | A GitHub vállalati gyökér URL-címe (a helyi GitHub Enterprise Server esetében HTTPS-nek kell lennie). Példa: `https://github.mydomain.com`. Csak akkor szükséges, ha a **GitHub Enterprise használata** van kiválasztva | `<your GitHub enterprise url>` |                                                           
| **GitHub-fiók** | A GitHub-fiók neve. Ez a név a https: \/ /GitHub.com/{Account Name}/{repository neve} helyről érhető el. Ezen az oldalon navigálva megadhatja a GitHub-OAuth hitelesítő adatait a GitHub-fiókjában. | `<your GitHub account name>` |
| **Adattár neve**  | A GitHub-kód tárházának neve. A GitHub-fiókok git-tárházat tartalmaznak a forráskód kezeléséhez. Létrehozhat egy új tárházat, vagy használhat olyan meglévő tárházat, amely már szerepel a fiókjában. | `<your repository name>` |
| **Együttműködési ág** | A GitHub-együttműködési ág, amely a közzétételhez használatos. Alapértelmezés szerint a főkiszolgálója. Módosítsa ezt a beállítást abban az esetben, ha egy másik ág erőforrásait közzé szeretné tenni. | `<your collaboration branch>` |
| **Gyökérmappa** | A legfelső szintű mappa a GitHub-együttműködési ágban. |`<your root folder name>` |
| **Meglévő Data Factory-erőforrások importálása adattárba** | Meghatározza, hogy a meglévő adatok gyári erőforrásai a UX authoring vászonról egy GitHub-tárházba legyenek importálva. Jelölje be a jelölőnégyzetet az adatfeldolgozó-erőforrások a társított git-tárházba való importálásához JSON formátumban. Ez a művelet egyenként exportálja az egyes erőforrásokat (azaz a társított szolgáltatásokat és adatkészleteket külön JSON-ba exportálja). Ha ez a mező nincs bejelölve, a meglévő erőforrások nem kerülnek importálásra. | Kijelölt (alapértelmezett) |
| **Erőforrás importálása a következőbe** | Meghatározza, hogy az adat-előállító erőforrásait (folyamatokat, adatkészleteket, társított szolgáltatásokat stb.) milyen ágakba importálja a rendszer. Az erőforrásokat a következő ágak egyikére importálhatja: a. Együttműködés: b. Hozzon létre új c-t. Meglévő használata |  |

### <a name="known-github-limitations"></a>Ismert GitHub-korlátozások

- A parancsfájlokat és az adatfájlokat egy GitHub-tárházban tárolhatja. A fájlokat azonban manuálisan kell feltöltenie az Azure Storage-ba. Egy Data Factory folyamat nem tölt fel automatikusan egy GitHub-tárházban tárolt parancsfájl-vagy adatfájlt az Azure Storage-ba.

- A 2.14.0-nál régebbi verzióval rendelkező GitHub Enterprise nem működik a Microsoft Edge böngészőben.

- A GitHub-integráció a Data Factory Visual authoring Tools eszközzel csak a Data Factory általánosan elérhető verziójában működik.

- Egy GitHub-ág legfeljebb 1 000 entitást hívhat le (például folyamatokat és adatkészleteket). Ha eléri ezt a korlátot, a rendszer azt javasolja, hogy az erőforrásokat külön gyárakra ossza fel. Az Azure DevOps git nem rendelkezik ezzel a korlátozással.

## <a name="version-control"></a>Verziókövetés

A verziókövetés rendszerei (más néven a _verziókövetés_) lehetővé teszik a fejlesztők számára a kód és a kód alapján végzett módosítások nyomon követését. A verziókövetés elengedhetetlen eszköz a több fejlesztő projektjeihez.

### <a name="creating-feature-branches"></a>Szolgáltatási ágak létrehozása

Minden olyan Azure Repos git-tárház, amely egy adatgyárhoz van társítva, együttműködési ág tartozik. ( `master` az alapértelmezett együttműködési ág). A felhasználók létrehozhatnak szolgáltatási ágakat is, ha a ág legördülő menüben az **+ új ág** lehetőségre kattintanak. Miután megjelenik az új ág ablaktábla, adja meg a szolgáltatási ág nevét.

![Új ág létrehozása](media/author-visually/new-branch.png)

Ha készen áll a szolgáltatással kapcsolatos változások egyesítésére az együttműködési ágra, kattintson az ág legördülő listára, és válassza a **pull-kérelem létrehozása**lehetőséget. Ezzel a művelettel elvégezheti az Azure Repos git-t, ahol lekéréses kérelmeket tehet fel, kód-felülvizsgálatokat végezhet, és egyesítheti az együttműködési ág módosításait. ( `master` az alapértelmezett érték). Az együttműködési ágban csak a Data Factory szolgáltatásban lehet közzétenni. 

![Új lekéréses kérelem létrehozása](media/author-visually/create-pull-request.png)

### <a name="configure-publishing-settings"></a>Közzétételi beállítások konfigurálása

Alapértelmezés szerint a Factory a közzétett gyár Resource Manager-sablonjait hozza létre, és egy nevű ágra menti őket `adf_publish` . Egyéni közzétételi ág konfigurálásához vegyen fel egy `publish_config.json` fájlt az együttműködési ág gyökérmappa mappájába. Közzétételkor az ADF beolvassa ezt a fájlt, megkeresi a mezőt `publishBranch` , és menti az összes Resource Manager-sablont a megadott helyre. Ha a ág nem létezik, a rendszer automatikusan létrehozza az adatelőállítót. És példa arra, hogy a fájl milyen módon néz ki:

```json
{
    "publishBranch": "factory/adf_publish"
}
```

Azure Data Factory egyszerre csak egy közzétételi ág lehet. Új közzétételi ág megadásakor Data Factory nem törli az előző közzétételi ágat. Ha el szeretné távolítani az előző közzétételi ágat, törölje manuálisan.

> [!NOTE]
> Data Factory csak akkor olvassa be a `publish_config.json` fájlt, amikor betölti a gyárat. Ha már betöltötte a gyárat a portálon, frissítse a böngészőt a módosítások érvénybe léptetéséhez.

### <a name="publish-code-changes"></a>Kód módosításának közzététele

Miután összevonta a módosításokat az együttműködési ágra (az `master` alapértelmezett beállítás), kattintson a **Közzététel** gombra a kód módosításának manuális közzétételéhez a főág és a Data Factory szolgáltatás között.

![Módosítások közzététele a Data Factory szolgáltatásban](media/author-visually/publish-changes.png)

Ekkor megnyílik egy oldalsó ablaktábla, ahol megerősítheti, hogy a közzétételi ág és a függőben lévő módosítások helyesek. A módosítások ellenőrzése után kattintson az **OK** gombra a közzététel megerősítéséhez.

![A megfelelő közzétételi ág megerősítése](media/author-visually/configure-publish-branch.png)

> [!IMPORTANT]
> A fő ág nem reprezentatív a Data Factory szolgáltatásban üzembe helyezett szolgáltatások esetében. A *fő ágat manuálisan közzé kell tenni* a Data Factory szolgáltatásban.

## <a name="best-practices-for-git-integration"></a>Ajánlott eljárások a git-integrációhoz

### <a name="permissions"></a>Engedélyek

Általában nem szeretné, hogy minden csapattag rendelkezzen a gyár frissítéséhez szükséges engedélyekkel. A következő engedélyek használata ajánlott:

*   Az összes csapattagnak olvasási engedéllyel kell rendelkeznie az adatelőállítóhoz.
*   A gyárban csak a kiválasztott személyek adhatók közzé. Ehhez rendelkeznie kell a **Data Factory közreműködő** szerepkörrel azon az erőforráson, amelyen a gyár található. Az engedélyekkel kapcsolatos további információkért lásd: [szerepkörök és engedélyek a Azure Data Factoryhoz](concepts-roles-permissions.md).
   
Javasoljuk, hogy ne engedélyezze a közvetlen bejelentkezéseket az együttműködési ágban. Ez a korlátozás segít megakadályozni a hibákat, mivel minden bejelentkezés a [szolgáltatási ágak létrehozása](source-control.md#creating-feature-branches)című témakörben leírt lekéréses kérelem-felülvizsgálati folyamaton halad át.

### <a name="using-passwords-from-azure-key-vault"></a>Jelszavak használata Azure Key Vault

A Data Factory társított szolgáltatások esetében ajánlott a Azure Key Vault használata a kapcsolati karakterláncok, jelszavak vagy a felügyelt identitások hitelesítésének tárolására. Biztonsági okokból a Factory nem tárolja a titkokat a git szolgáltatásban. A titkos kulcsokat (például a jelszavakat) tartalmazó társított szolgáltatások módosításai azonnal közzé lesznek téve a Azure Data Factory szolgáltatásnak.

A Key Vault vagy az MSI-hitelesítés használata is megkönnyíti a folyamatos integrációt és üzembe helyezést, mivel nem kell megadnia ezeket a titkokat a Resource Manager-sablonok telepítése során.

## <a name="troubleshooting-git-integration"></a>A git-integráció hibaelhárítása

### <a name="stale-publish-branch"></a>Elavult közzétételi ág

Ha a közzétételi ág nem szinkronizált a főágra, és a legutóbbi közzététel ellenére elavult erőforrásokat tartalmaz, próbálkozzon a következő lépésekkel:

1. Az aktuális git-tárház eltávolítása
1. Konfigurálja újra a git-t ugyanazokkal a beállításokkal, de győződjön meg arról, hogy a **meglévő Data Factory erőforrások importálása az adattárba** lehetőség van **kiválasztva**
1. Lekéréses kérelem létrehozása az együttműködési ág változásainak egyesítéséhez 

Az alábbi példák olyan helyzetekre mutatnak, amelyek elavult közzétételi ágat okozhatnak:
- Egy felhasználónak több ága van. Az egyik szolgáltatási ág törölte azokat a társított szolgáltatásokat, amelyek nincsenek társítva AKV (a nem AKV társított szolgáltatások azonnal közzé lesznek téve, függetlenül attól, hogy git vagy nem), és soha nem vonta össze a szolgáltatási ágat az együttműködési ágra.
- Egy felhasználó módosította az adatelőállítót az SDK vagy a PowerShell használatával
- A felhasználó minden erőforrást egy új ágra helyezett át, és első alkalommal próbált közzétenni. A társított szolgáltatásokat manuálisan kell létrehozni az erőforrások importálásakor.
- Egy felhasználó manuálisan feltölt egy nem AKV társított szolgáltatást vagy egy Integration Runtime JSON-t. Erre az erőforrásra hivatkoznak egy másik erőforrásból, például egy adatkészletből, egy társított szolgáltatásból vagy egy folyamatból. Az UX használatával létrehozott nem AKV társított szolgáltatás azonnal közzé van téve, mert a hitelesítő adatokat titkosítani kell. Ha olyan adatkészletet tölt fel, amely hivatkozik erre a társított szolgáltatásra, és megpróbál közzétenni, az UX lehetővé teszi, hogy a git-környezetben is megtalálható legyen. A közzétételi időpontban elutasításra kerül, mivel nem létezik a (z) adatfeldolgozó szolgáltatásban.

## <a name="switch-to-a-different-git-repository"></a>Váltás másik git-tárházra

Ha másik git-tárházra szeretne váltani, kattintson a git-adattár **beállításai** ikonra a Data Factory áttekintés oldal jobb felső sarkában. Ha nem látja az ikont, törölje a helyi böngésző gyorsítótárát. Válassza ki az ikont az aktuális tárház társításának eltávolításához.

![Git ikon](media/author-visually/remove-repo.png)

Ha megjelenik az adattár beállításai ablaktábla, válassza a **git eltávolítása**lehetőséget. Adja meg az adatelőállító nevét, és kattintson a **Confirm (megerősítés** ) gombra az adatgyárhoz társított git-tárház eltávolításához.

![Az aktuális git-tárház társításának eltávolítása](media/author-visually/remove-repo2.png)

Miután eltávolította az aktuális tárházhoz való társítást, beállíthatja, hogy a git-beállítások egy másik tárházat használjanak, majd meglévő Data Factory erőforrásokat importáljon az új tárházba.

> [!IMPORTANT]
> A git-konfiguráció adat-előállítóból való eltávolítása nem törli az adattárból származó semmit. A gyár minden közzétett erőforrást tartalmaz majd. A gyárat továbbra is szerkesztheti közvetlenül a szolgáltatással.

## <a name="next-steps"></a>További lépések

* A folyamatok figyelésével és kezelésével kapcsolatos további információkért lásd a [folyamatok programozott figyelését és](monitor-programmatically.md)felügyeletét ismertető témakört.
* A folyamatos integráció és üzembe helyezés megvalósításához tekintse [meg az Azure Data Factory folyamatos integrációját és továbbítását (CI/CD)](continuous-integration-deployment.md).
