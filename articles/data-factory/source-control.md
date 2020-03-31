---
title: Verziókövetés
description: A forrásvezérlés konfigurálása az Azure Data Factoryban
services: data-factory
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.reviewer: ''
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/09/2019
ms.openlocfilehash: 6645c2672e15c562216b4347f779ef3634a2f124
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80130871"
---
# <a name="source-control-in-azure-data-factory"></a>Forrásvezérlés az Azure Data Factoryban

Az Azure Data Factory felhasználói felülete (UX) két vizuális szerzői élménytel rendelkezik:

- Szerző közvetlenül a Data Factory szolgáltatás
- Szerző készítése az Azure Repos Git- vagy GitHub-integrációval

> [!NOTE]
> Az Azure Government Cloud csak közvetlenül a Data Factory szolgáltatással való szerkesztés támogatott.

## <a name="author-directly-with-the-data-factory-service"></a>Szerző közvetlenül a Data Factory szolgáltatás

Míg a szerzői közvetlenül a Data Factory szolgáltatás, az egyetlen módja annak, hogy mentse a módosításokat keresztül **Az Összes közzététele** gombra. Kattintás után az összes végrehajtott módosítás közvetlenül a Data Factory szolgáltatásban lesz közzétéve. 

![Közzétételi mód](media/author-visually/data-factory-publish.png)

A közvetlenül a Data Factory szolgáltatással történő szerkesztés a következő korlátozásokkal rendelkezik:

- A Data Factory szolgáltatás nem tartalmaz egy tárházat a JSON-entitások tárolására a módosításokhoz.
- A Data Factory szolgáltatás nincs együttműködésre vagy verziókövetésre optimalizálva.

> [!NOTE]
> Közvetlenül a Data Factory szolgáltatás sal történő szerkesztése le van tiltva az Azure Data Factory UX-ben, ha egy Git-tárház konfigurálva van. A módosítások közvetlenül a szolgáltatás powershellen vagy egy SDK-n keresztül hajthatók végre.

## <a name="author-with-azure-repos-git-integration"></a>Létrehozás Azure Repos Git-integrációval

Vizuális szerzői Azure Repos Git integráció támogatja a forrás-szabályozás és az együttműködés az adat-előállító folyamatok. Az adat-előállítók társíthatók egy Azure Repos Git szervezeti tárház a forrás-ellenőrzés, együttműködés, verziószámozás és így tovább. Egyetlen Azure Repos Git-szervezet több tárházzal is rendelkezhet, de egy Azure Repos Git-tárház csak egy adat-előállítóhoz társítható. Ha nem rendelkezik egy Azure Repos szervezet vagy tárház, kövesse [az alábbi utasításokat](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student) az erőforrások létrehozásához.

> [!NOTE]
> Parancsfájlok és adatfájlok tárolhatók egy Azure Repos Git-tárházban. Azonban manuálisan kell feltöltenie a fájlokat az Azure Storage-ba. A Data Factory folyamat nem tölti fel automatikusan az Azure Repos Git-tárházban tárolt parancsfájlokat vagy adatfájlokat az Azure Storage-ba.

### <a name="configure-an-azure-repos-git-repository-with-azure-data-factory"></a>Azure Repos Git-tárház konfigurálása az Azure Data Factory segítségével

Konfigurálhatja az Azure Repos Git-tárház egy adat-előállító két módszerrel konfigurálható.

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>1. konfigurációs módszer: Az Azure Data Factory kezdőlapja

Az Azure Data Factory kezdőlapján válassza a **Kódtár beállítása**lehetőséget.

![Azure repos kódtárház konfigurálása](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>2. konfigurációs módszer: Felhasználói felület szerzői vászna
Az Azure Data Factory UX szerzői vásznán válassza a **Data Factory** legördülő menüt, majd a **Kódtár beállítása**lehetőséget.

![A felhasználói felület szerzői kódtár-beállításainak konfigurálása](media/author-visually/configure-repo-2.png)

Mindkét módszer megnyitja a tárház beállítások konfigurációs ablaktábláját.

![A kódtár beállításainak konfigurálása](media/author-visually/repo-settings.png)

A konfigurációs ablaktábla a következő Azure Repos kódtárház beállításait jeleníti meg:

| Beállítás | Leírás | Érték |
|:--- |:--- |:--- |
| **Tárház típusa** | Az Azure Repos kódtárház típusa.<br/> | Azure DevOps Git vagy GitHub |
| **Azure Active Directory** | Az Azure AD-bérlő neve. | `<your tenant name>` |
| **Azure Repos szervezet** | Az Azure Repos szervezet neve. Az Azure Repos szervezet nevét `https://{organization name}.visualstudio.com`a.-on találja meg. [Bejelentkezhet az Azure Repos szervezetbe](https://www.visualstudio.com/team-services/git/) a Visual Studio-profiljának eléréséhez és az adattárak és a projektek megtekintéséhez. | `<your organization name>` |
| **Projektneve** | Az Azure Repos projekt neve. Az Azure Repos projekt nevét `https://{organization name}.visualstudio.com/{project name}`a. | `<your Azure Repos project name>` |
| **Tárházneve** | Az Azure Repos kódtár neve. Az Azure Repos-projektek Git-adattárakat tartalmaznak a forráskód kezeléséhez a projekt növekedésével. Létrehozhat egy új tárházat, vagy használhat egy már a projektben lévő tárházat. | `<your Azure Repos code repository name>` |
| **Együttműködési ág** | Az Azure Repos együttműködési ág, amely a közzétételhez használatos. Alapértelmezés szerint ez `master`. Módosítsa ezt a beállítást abban az esetben, ha egy másik ágból szeretne erőforrásokat közzétenni. | `<your collaboration branch name>` |
| **Gyökérmappa** | A gyökérmappa az Azure Repos együttműködési ágban. | `<your root folder name>` |
| **Meglévő adatfeldolgozó erőforrások importálása a tárházba** | Itt adható meg, hogy importáljon-e meglévő adat-előállító erőforrásokat a felhasználói felület **szerzői vászonról** egy Azure Repos Git-tárházba. Jelölje be az adat-előállító erőforrások importálásához a kapcsolódó Git-tárházba JSON formátumban. Ez a művelet minden erőforrást egyenként exportál (azaz a csatolt szolgáltatásokat és adatkészleteket külön JSON-okba exportálja). Ha ez a mező nincs bejelölve, a meglévő erőforrások importálása nem történik meg. | Kiválasztva (alapértelmezett) |
| **Elágazás az erőforrás importálásához** | Itt adható meg, hogy az adat-előállító erőforrások (folyamatok, adatkészletek, csatolt szolgáltatások stb.) melyik ágba importálók. Az erőforrásokat a következő ágak egyikébe importálhatja: a. Együttműködés b. Új c létrehozása. Meglévő használata |  |

> [!NOTE]
> Ha Microsoft Edge-et használ, és nem lát értékeket az Azure DevOps-fiók legördülő listájában, adja hozzá a https://*.visualstudio.com webhelyet a megbízható helyek listájához.

### <a name="use-a-different-azure-active-directory-tenant"></a>Másik Azure Active Directory-bérlő használata

Létrehozhat egy Azure Repos Git-adattárat egy másik Azure Active Directory-bérlőn. Másik Azure Active Directory-bérlő megadásához rendszergazdai engedélyekkel kell rendelkeznie az Ön által használt Azure-előfizetésben.

### <a name="use-your-personal-microsoft-account"></a>Személyes Microsoft-fiók használata

Személyes Microsoft-fiók használatával git-integráció, összekapcsolhatja a személyes Azure-tárhivatal a szervezet Active Directory.

1. Személyes Microsoft-fiókját vendégként adja hozzá a szervezet Active Directoryjához. További információ: [Add Azure Active Directory B2B együttműködési felhasználók az Azure Portalon.](../active-directory/b2b/add-users-administrator.md)

2. Jelentkezzen be az Azure Portalra személyes Microsoft-fiókjával. Ezután váltson a szervezet Active Directorycímére.

3. Nyissa meg az Azure DevOps szakaszt, ahol most láthatja a személyes tárta. Jelölje ki a tártárat, és csatlakozzon az Active Directoryhoz.

A konfigurációs lépések után a személyes tárház elérhetővé válik, amikor beállítja a Git-integrációt a Data Factory felhasználói felületén.

Az Azure Repos-ok szervezet Active Directoryhoz való csatlakoztatásáról az [Azure DevOps-szervezet csatlakoztatása az Azure Active Directoryhoz (Connect your Azure DevOps) (Az Azure DevOps-szervezet csatlakoztatása)](/azure/devops/organizations/accounts/connect-organization-to-azure-ad)témakörben talál további információt.

## <a name="author-with-github-integration"></a>Létrehozás GitHub-integrációval

A GitHub-integrációval végzett vizuális szerzői műveletek támogatják a forrásvezérlést és az együttműködést az adatfeldolgozó folyamatokon végzett munkához. Társíthat egy adat-előállító egy GitHub-fióktár a forrás-vezérlés, együttműködés, verziószámozás. Egyetlen GitHub-fiók több tárházzal is rendelkezhet, de egy GitHub-tárház csak egy adat-előállítóhoz társítható. Ha nem rendelkezik GitHub-fiókkal vagy tárházzal, kövesse [az alábbi utasításokat](https://github.com/join) az erőforrások létrehozásához.

A GitHub-integráció a Data Factory támogatja a nyilvános [https://github.com](https://github.com)GitHub (azaz) és a GitHub Enterprise. Használhatja a nyilvános és a privát GitHub-adattárak a Data Factory, amíg olvasási és írási engedéllyel rendelkezik a github-tárház.

A GitHub-tártár konfigurálásához rendszergazdai engedélyekkel kell rendelkeznie a használt Azure-előfizetéshez.

A funkció kilencperces bemutatásához és bemutatásához tekintse meg az alábbi videót:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Azure-Data-Factory-visual-tools-now-integrated-with-GitHub/player]

### <a name="configure-a-github-repository-with-azure-data-factory"></a>GitHub-tárház konfigurálása az Azure Data Factory segítségével

A GitHub-tárház két módszerrel konfigurálható adatelényekkel.

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>1. konfigurációs módszer: Az Azure Data Factory kezdőlapja

Az Azure Data Factory kezdőlapján válassza a **Kódtár beállítása**lehetőséget.

![Azure repos kódtárház konfigurálása](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>2. konfigurációs módszer: Felhasználói felület szerzői vászna

Az Azure Data Factory UX szerzői vásznán válassza a **Data Factory** legördülő menüt, majd a **Kódtár beállítása**lehetőséget.

![A felhasználói felület szerzői kódtár-beállításainak konfigurálása](media/author-visually/configure-repo-2.png)

Mindkét módszer megnyitja a tárház beállítások konfigurációs ablaktábláját.

![GitHub-tárház beállításai](media/author-visually/github-integration-image2.png)

A konfigurációs ablaktábla a következő GitHub-tárház-beállításokat jeleníti meg:

| **Beállítás** | **Leírás**  | **Érték**  |
|:--- |:--- |:--- |
| **Tárház típusa** | Az Azure Repos kódtárház típusa. | GitHub |
| **A GitHub Enterprise használata** | Jelölőnégyzet a GitHub Enterprise kiválasztásához | nincs bejelölve (alapértelmezett) |
| **GitHub vállalati URL-címe** | A GitHub Enterprise gyökér URL-címe (a helyi GitHub Enterprise kiszolgálóhttps-nek kell lennie). Például: https://github.mydomain.com. Csak akkor kötelező, ha **a GitHub Enterprise használata** van kiválasztva | `<your GitHub enterprise url>` |                                                           
| **GitHub-fiók** | A GitHub-fiók neve. Ez a név a\/https: /github.com/{account name}/{repository name} címen található. Az erre a lapra való navigálás során meg kell adnia a GitHub OAuth hitelesítő adatait a GitHub-fiókjához. | `<your GitHub account name>` |
| **Tárház neve**  | A GitHub-kódtár neve. A GitHub-fiókok Git-adattárakat tartalmaznak a forráskód kezeléséhez. Létrehozhat egy új tárházat, vagy használhat egy már meglévő tárházat, amely már a fiókjában van. | `<your repository name>` |
| **Együttműködési ág** | A GitHub együttműködési ág, amely a közzétételhez használt. Alapértelmezés szerint a mester. Módosítsa ezt a beállítást abban az esetben, ha egy másik ágból szeretne erőforrásokat közzétenni. | `<your collaboration branch>` |
| **Gyökérmappa** | A gyökérmappa a GitHub együttműködési ágban. |`<your root folder name>` |
| **Meglévő adatfeldolgozó erőforrások importálása a tárházba** | Itt adható meg, hogy importáljon-e meglévő adat-előállító erőforrásokat a felhasználói felület szerzői vászonról egy GitHub-tárházba. Jelölje be az adat-előállító erőforrások importálásához a kapcsolódó Git-tárházba JSON formátumban. Ez a művelet minden erőforrást egyenként exportál (azaz a csatolt szolgáltatásokat és adatkészleteket külön JSON-okba exportálja). Ha ez a mező nincs bejelölve, a meglévő erőforrások importálása nem történik meg. | Kiválasztva (alapértelmezett) |
| **Elágazás az erőforrás importálásához** | Itt adható meg, hogy az adat-előállító erőforrások (folyamatok, adatkészletek, csatolt szolgáltatások stb.) melyik ágba importálók. Az erőforrásokat a következő ágak egyikébe importálhatja: a. Együttműködés b. Új c létrehozása. Meglévő használata |  |

### <a name="known-github-limitations"></a>Ismert GitHub-korlátozások

- Parancsfájlok és adatfájlok tárolhatók egy GitHub-tárházban. Azonban manuálisan kell feltöltenie a fájlokat az Azure Storage-ba. A Data Factory folyamat nem tölti fel automatikusan a GitHub-tárházban tárolt parancsfájlokat vagy adatfájlokat az Azure Storage-ba.

- A 2.14.0-nál régebbi verzióval rendelkező GitHub Enterprise nem működik a Microsoft Edge böngészőben.

- A Data Factory vizuális szerzői eszközeivel való GitHub-integráció csak a Data Factory általánosan elérhető verziójában működik.

- Erőforrástípusonként legfeljebb 1000 entitás (például folyamatok és adatkészletek) hívhatók le egyetlen GitHub-ágból. Ha eléri ezt a korlátot, javasoljuk, hogy ossza fel az erőforrásokat külön gyárakra. Az Azure DevOps Git nem rendelkezik ezzel a korlátozással.

## <a name="switch-to-a-different-git-repo"></a>Váltás másik Git-tárfélre

Ha másik Git-tárházra szeretne váltani, kattintson a **Git-tárház beállításai** ikonra a Data Factory áttekintő oldalának jobb felső sarkában. Ha nem látja az ikont, törölje a helyi böngészőgyorsítótárat. Az ikonra választva távolítsa el az aktuális társóval való társítást.

![A Git ikonja](media/author-visually/remove-repo.png)

Miután megjelenik a Tárház beállításai ablaktábla, válassza **a Git eltávolítása**lehetőséget. Adja meg az adatgyár nevét, és kattintson a **megerősítés** gombra az adat-előállítóhoz társított Git-tárház eltávolításához.

![Az aktuális Git-társótársítás eltávolítása](media/author-visually/remove-repo2.png)

Miután eltávolította a társítást az aktuális tártárral, beállíthatja a Git-beállításokat egy másik tártár használatára, majd importálhatja a meglévő Data Factory-erőforrásokat az új tárműtérbe. 

## <a name="version-control"></a>Verziókövetés

A verzióvezérlő rendszerek (más néven _forrásvezérlés)_ lehetővé teszik a fejlesztők számára, hogy együttműködjenek a kódon, és nyomon kövessék a kódbázison végrehajtott módosításokat. A forrásellenőrzés a többfejlesztős projektek alapvető eszköze.

### <a name="creating-feature-branches"></a>Jellemzőágak létrehozása

Minden egyes Azure Repos Git-tárház, amely egy adat-előállítóhoz társított együttműködési ággal rendelkezik. (`master` az alapértelmezett együttműködési ág). A felhasználók az ág legördülő menü **+ Új ág** elemre kattintva is létrehozhatnak funkcióágakat. Az új ágablak megjelenítése után adja meg a jellemzőág nevét.

![Új ág létrehozása](media/author-visually/new-branch.png)

Ha készen áll a módosítások egyesítésére a szolgáltatáságból az együttműködési ágba, kattintson az elágazás legördülő **menülegördülő menü Lekéréses kérelem létrehozása lehetőségre.** Ez a művelet az Azure Repos Git-hez vezet, ahol lekéréses kérelmeket hívhat meg, kódellenőrzéseket hajthat végre, és egyesítheti az együttműködési ág módosításait. (`master` az alapértelmezett). Csak az együttműködési ágból teheti közzé a Data Factory szolgáltatást. 

![Új lekéréses kérelem létrehozása](media/author-visually/create-pull-request.png)

### <a name="configure-publishing-settings"></a>Közzétételi beállítások konfigurálása

A közzétételi ág – azaz az Erőforrás-kezelő sablonjainak `publish_config.json` mentéséhez való ág – konfigurálásához adjon hozzá egy fájlt az együttműködési ág gyökérmappájához. A Data Factory beolvassa ezt `publishBranch`a fájlt, megkeresi a mezőt, és létrehoz egy új ágat (ha még nem létezik) a megadott értékkel. Ezután az összes Erőforrás-kezelő sablont a megadott helyre menti. Példa:

```json
{
    "publishBranch": "factory/adf_publish"
}
```

Új közzétételi ág megadásakor a Data Factory nem törli az előző közzétételi ágat. Ha el szeretné távolítani az előző közzétételi ágat, törölje azt manuálisan.

> [!NOTE]
> A Data Factory `publish_config.json` csak akkor olvassa be a fájlt, amikor betölti a gyárat. Ha már bevan töltve a gyár a portálon, frissítse a böngészőt, hogy a módosítások érvénybe lépjenek.

### <a name="publish-code-changes"></a>Kódmódosítások közzététele

Miután egyesítette az együttműködési ág`master` módosításait ( ez az alapértelmezett), kattintson a **Közzététel** gombra, ha manuálisan szeretné közzétenni a kódmódosításokat a főágban a Data Factory szolgáltatásban.

![Módosítások közzététele a Data Factory szolgáltatásban](media/author-visually/publish-changes.png)

Megnyílik egy oldalsó ablaktábla, ahol meggyőződik arról, hogy a közzétételi ág és a függőben lévő módosítások helyesek. Miután ellenőrizte a módosításokat, kattintson az **OK** gombra a közzététel megerősítéséhez.

![A megfelelő közzétételi ág megerősítése](media/author-visually/configure-publish-branch.png)

> [!IMPORTANT]
> A főág nem reprezentatív a Data Factory szolgáltatásban üzembe helyezett adatokra nézve. A főágat manuálisan *kell* közzétenni a Data Factory szolgáltatásban.

## <a name="advantages-of-git-integration"></a>A Git-integráció előnyei

-   **Forrásvezérlő .** Mivel az adat-gyári munkaterhelések fontossá válnak, érdemes integrálni a gyárat a Git-tel, hogy kihasználja a következő forrásellenőrzési előnyöket:
    -   A változások nyomon követésének/naplózásának képessége.
    -   Képesség, hogy visszaállítja a hibákat bevezető változásokat.
-   **Részleges mentések**. Ahogy sok változtatást hajt végre a gyárában, rá fog jönni, hogy a normál LIVE módban nem tudja menteni a módosításokat vázlatként, mert nem áll készen, vagy nem szeretné elveszíteni a módosításokat, ha a számítógép összeomlik. A Git-integrációval folytathatja a módosítások növekményes mentését, és csak akkor teheti közzé a gyárban, ha készen áll. A Git a munkája átmeneti helyeként működik, amíg a módosításokat az Ön megelégedésére nem tesztelte.
-   **Együttműködés és vezérlés**. Ha több csapattag is részt vesz ugyanabban a gyárban, érdemes lehet hagyni, hogy a csapattársaid egy kód-ellenőrzési folyamaton keresztül együttműködjenek egymással. Azt is beállíthatja a gyárat, hogy nem minden hozzájáruló a gyár ban van engedélye telepíteni a gyárba. Előfordulhat, hogy a csapattagok csak a Git-en keresztül végezhetnek módosításokat, de csak bizonyos személyek a csapatban "közzétehetik" a módosításokat a gyárban.
-   **A diffs megjelenítése**. Git módban, kapsz, hogy egy szép diff a hasznos teher, hogy hamarosan kap közzé a gyárban. Ez a diff megmutatja az összes erőforrást/entitást, amely a gyárban való legutóbbi közzététel óta módosult/lett hozzáadva/törölve. A különbség alapján folytathatja a közzétételt, vagy visszatérhet a módosítások ellenőrzéséhez, majd később visszatérhet.
-   **Jobb CI / CD**. Ha Git módot használ, beállíthatja a kiadási folyamat automatikusan aktiválódik, amint a fejlesztési gyárban végrehajtott módosítások történtek. Testre szabhatja a gyárban lévő azon tulajdonságokat is, amelyek paraméterként érhetők el az Erőforrás-kezelő sablonban. Hasznos lehet, ha csak a szükséges tulajdonságkészletet tartja meg paraméterekként, és minden mást kemény kódol.
-   **Jobb teljesítmény**. Egy átlagos gyár imitátor tízszer gyorsabbGit módban, mint normál LIVE módban, mert az erőforrások a Git-en keresztül töltődnek le.

## <a name="best-practices-for-git-integration"></a>Gyakorlati tanácsok a Git-integrációhoz

### <a name="permissions"></a>Engedélyek

Általában nem szeretné, hogy minden csapattag nak legyen engedélye a gyár frissítésére. A következő engedélybeállítások ajánlottak:

*   Minden csapattagnak olvasási engedéllyel kell rendelkeznie az adat-előállítóba.
*   Csak egy kiválasztott csoport az emberek számára lehetővé kell tenni, hogy tegye közzé a gyárban. Ehhez rendelkezniük kell a **Data Factory közreműködői** szerepkört a gyárban. Az engedélyekről az [Azure Data Factory szerepkörei és engedélyei](concepts-roles-permissions.md)című témakörben talál további információt.
   
Javasoljuk, hogy ne engedélyezze a közvetlen bejelentkezéseket az együttműködési ágba. Ez a korlátozás segíthet megelőzni a hibákat, mivel minden bejelentkezés a [Szolgáltatáságak létrehozása című](source-control.md#creating-feature-branches)témakörben leírt lekéréses kérelem-ellenőrzési folyamaton megy keresztül.

### <a name="using-passwords-from-azure-key-vault"></a>Az Azure Key Vault ból származó jelszavak használata

Javasoljuk, hogy az Azure Key Vault használatával tárolja a Data Factory csatolt szolgáltatások kapcsolati karakterláncait vagy jelszavait. Biztonsági okokból nem tárolunk ilyen titkos adatokat a Gitben, így a csatolt szolgáltatások módosításait azonnal közzétesszük az Azure Data Factory szolgáltatásban.

A Key Vault használata is megkönnyíti a folyamatos integrációt és üzembe helyezést, mivel nem kell megadnia ezeket a titkos kulcsokat az Erőforrás-kezelő sablon üzembe helyezése során.

## <a name="troubleshooting-git-integration"></a>Git-integráció – hibaelhárítás

### <a name="stale-publish-branch"></a>Elavult közzétételi ág

Ha a közzétételi ág nincs szinkronban a főággal, és a legutóbbi közzététel ellenére elavult erőforrásokat tartalmaz, próbálkozzon az alábbi lépésekkel:

1. Az aktuális Git-tárház eltávolítása
1. A Git újrakonfigurálása ugyanazzal a beállítással, de győződjön meg arról, **hogy a meglévő Data Factory-erőforrások importálása a tárházba** ki van jelölve, és válassza az Új **ág** lehetőséget
1. Lekéréses kérelem létrehozása az együttműködési ág módosítási funkcióinak egyesítéséhez 

Az alábbiakban néhány példa látható olyan helyzetekre, amelyek elavult közzétételi ágat okozhatnak:
- Egy felhasználónak több ága van. Az egyik szolgáltatáságban töröltek egy összekapcsolt szolgáltatást, amely nem AKV-hoz van társítva (a nem AKV-hoz csatolt szolgáltatások azonnal közzé lesznek téve, függetlenül attól, hogy Git-ben vannak-e vagy sem), és soha nem egyesítették a szolgáltatáságat az együttműködési brnach-ba.
- Egy felhasználó módosította az adat-előállítót az SDK vagy a PowerShell használatával
- A felhasználó az összes erőforrást áthelyezte egy új ágba, és első alkalommal próbálta meg közzétenni. Az erőforrások importálásakor a csatolt szolgáltatásokat manuálisan kell létrehozni.
- A felhasználó manuálisan tölt fel egy nem AKV-kapcsolattal rendelkező szolgáltatást vagy egy integrációs futásidejű JSON-t. Hivatkoznak arra az erőforrásra egy másik erőforrásból, például egy adatkészletből, egy csatolt szolgáltatásból vagy csővezetékből. A felhasználói felületen keresztül létrehozott nem AKV-alapú szolgáltatás azonnal közzétételre kerül, mert a hitelesítő adatokat titkosítani kell. Ha a csatolt szolgáltatásra hivatkozó adatkészletet tölt fel, és megpróbálja közzétenni, a felhasználói felület engedélyezi, mert létezik a git környezetben. A közzétételkor a rendszer elutasítja, mivel nem létezik az adatgyári szolgáltatásban.

## <a name="provide-feedback"></a>Visszajelzés küldése
Válassza **a Visszajelzés** lehetőséget a szolgáltatásokkal kapcsolatos megjegyzések hez vagy az eszközrel kapcsolatos problémákról való értesítéshez:

![Visszajelzés](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>További lépések

* A folyamatok figyeléséről és kezeléséről a [Folyamatok figyelése és kezelése programozott módon.](monitor-programmatically.md)
* A folyamatos integráció és üzembe helyezés megvalósításához olvassa el [a Folyamatos integráció és kézbesítés (CI/CD) című témakört az Azure Data Factoryban.](continuous-integration-deployment.md)
