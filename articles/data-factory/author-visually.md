---
title: Vizuális szerzői az Azure Data Factoryban |} A Microsoft Docs
description: Ismerje meg, hogyan használhatja a vizuális szerzői az Azure Data Factoryban
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: shlo
ms.openlocfilehash: e934c8bdc851f8c550e8b87a923215df3f95e389
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56728256"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Az Azure Data Factoryban vizuális szerzői
Az Azure Data Factory felhasználói felületen felület (UX) lehetővé teszi, hogy vizuálisan hozhat létre és helyezheti üzembe az erőforrásokat a data Factory kód írása nélkül. Húzza a tevékenységeket a folyamatvásznon, végre, iteratív hibakeresést és üzembe helyezése és a folyamat futásának monitorozása. Kétféleképpen felhasználói a vizuális szerzői műveletek végrehajtásához:

- Szerző közvetlenül a Data Factory szolgáltatással.
- Az Azure Git-Adattárakkal integráció az együttműködéshez, verziókövetési és verziókezelés Szerző.

## <a name="author-directly-with-the-data-factory-service"></a>Szerző közvetlenül a Data Factory szolgáltatással
A Data Factory szolgáltatásban történő vizuális tartalomkészítésről eltér a kétféle módon Git-integrációval történő vizuális tartalomkészítésről:

- A Data Factory szolgáltatás nem tartalmaz egy helyen a JSON-entitások a módosítások tárolásához.
- A Data Factory szolgáltatás együttműködési vagy a verziókezeléshez nincs optimalizálva.

![A Data Factory szolgáltatás konfigurálása ](media/author-visually/configure-data-factory.png)

A felhasználói felület használata esetén **szerzői vászon** ahhoz, hogy a Data Factory szolgáltatás csak a közvetlenül a **összes közzététele** módban érhető el. A végrehajtott módosításokat közvetlenül a Data Factory szolgáltatás vannak közzétéve.

![Közzétételi mód](media/author-visually/data-factory-publish.png)

## <a name="author-with-azure-repos-git-integration"></a>Az Azure Git-Adattárakkal integráció Szerző
Azure-Adattárakkal Git-integrációval történő vizuális tartalomkészítésről támogatja a forráskezelés és az együttműködés az adat-előállító folyamatok végzett munka. Adat-előállító társíthatja az Azure Git-Adattárakkal szervezeti adattárba a verziókövetés, együttműködési, verziószámozása és így tovább. Egyetlen Azure Git-Adattárakkal szervezet több tárházak rendelkezhet, de egy Azure-Adattárakkal Git-tárház társítható csak egy adat-előállító. Ha nem rendelkezik egy Azure-Adattárakkal szervezet vagy a tárház, hajtsa végre a [ezek az utasítások](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student) az erőforrások létrehozásához.

> [!NOTE]
> Egy Azure-Adattárakkal Git-tárház feladatparancsfájl és adatfájlok tárolhatja. Azonban hogy a fájlok manuális feltöltése az Azure Storage. Data Factory-folyamatok nem automatikusan tölti fel az Azure Storage Azure Git-Adattárakkal tárházban tárolt parancsfájl- vagy adatfájlok.

### <a name="configure-an-azure-repos-git-repository-with-azure-data-factory"></a>Egy Azure-Adattárakkal Git-tárház beállítása az Azure Data Factoryvel
Az adat-előállító két módszer segítségével konfigurálhatja egy Azure-Adattárakkal Git-tárházba.

#### <a name="method1"></a> Konfigurálása (Azure-Adattárakkal Git-tárház) 1. módszer: Első lépések lap

Az Azure Data Factoryben, nyissa meg a **első lépések** lapot. Válassza ki **kódtár konfigurálása**:

![Egy Azure-kódtárak a kódtár konfigurálása](media/author-visually/configure-repo.png)

A **adattár beállításai** konfigurációs panelen jelenik meg:

![A kód tárház beállításainak konfigurálása](media/author-visually/repo-settings.png)

A panelen látható a következő Azure-Adattárakkal kód adattár beállításai:

| Beállítás | Leírás | Érték |
|:--- |:--- |:--- |
| **Adattár típusa** | Az Azure-Adattárakkal kódtár típusa.<br/>**Megjegyzés**: GitHub jelenleg nem támogatott. | Az Azure-Adattárakkal Git |
| **Azure Active Directory** | Az Azure AD-bérlő neve. | <your tenant name> |
| **Az Azure-Adattárakkal szervezet** | Az Azure-Adattárakkal szervezet neve. : Az Azure-Adattárakkal szervezet nevének megkereséséhez `https://{organization name}.visualstudio.com`. Is [jelentkezzen be a szervezet Azure-Adattárakkal](https://www.visualstudio.com/team-services/git/) érhető el a Visual Studio-profil, és az adattárak és a projekteket. | <your organization name> |
| **ProjectName** | Az Azure-Adattárakkal projekt nevét. Megkeresheti az Azure-Adattárakkal projekt nevét `https://{organization name}.visualstudio.com/{project name}`. | <your Azure Repos project name> |
| **RepositoryName** | Az Azure-Adattárakkal kód adattár nevét. Az Azure Adattárakkal-projektek kezeléséhez a forráskód növekedésével a projekt Git-tárházak tartalmaznak. Hozzon létre egy új adattár, vagy használjon egy meglévő adattárhoz, amely a projektben már van. | <your Azure Repos code repository name> |
| **Együttműködés ág** | Az Azure-Adattárakkal együttműködés ágban közzétételéhez használt. Alapértelmezés szerint a `master`. Ezen beállítás módosításához, abban az esetben, ha szeretne közzétenni egy másik ágban származó erőforrásokat. | <your collaboration branch name> |
| **Gyökérmappa** | A legfelső szintű mappát a saját Azure-Adattárakkal együttműködés ágban. | <your root folder name> |
| **Meglévő Data Factory-erőforrások importálása az adattárba** | Megadja, hogy a meglévő data factory-erőforrások importálása a UX **szerzői vászon** egy Azure-Adattárakkal Git-tárházba. Jelölje be a data factory-erőforrások importálása a társított Git-tárház JSON formátumban. Ez a művelet exportálja az egyes erőforrások külön-külön (azaz a társított szolgáltatásokat és adatkészleteket exportálása külön JSONs be). Ha a jelölőnégyzet nincs bejelölve, a meglévő erőforrások nem importálja. | Kiválasztott (alapértelmezett) |

#### <a name="configuration-method-2-azure-repos-git-repo-ux-authoring-canvas"></a>Konfigurálása (Azure-Adattárakkal Git-tárház) 2. módszer: Szerzői vászon UX
Az Azure Data Factory felhasználói **szerzői vászon**, keresse meg az adat-előállítóhoz. Válassza ki a **adat-előállító** legördülő menüből, és válassza ki **kódtár konfigurálása**.

A konfiguráció panelen jelenik meg. A konfigurációs beállításaival kapcsolatos információkért lásd: található leírások <a href="#method1">konfigurációs módszer 1</a>.

![A UX szerzői kód tárház beállításainak konfigurálása](media/author-visually/configure-repo-2.png)

### <a name="use-a-different-azure-active-directory-tenant"></a>Egy másik Azure Active Directory-bérlő használata

Létrehozhat egy Azure-Adattárakkal Git-tárház egy másik Azure Active Directory-bérlőben található. Egy másik Azure AD-bérlővel, rendelkeznie kell Azure-előfizetést használ, rendszergazdai jogosultságokkal.

### <a name="use-your-personal-microsoft-account"></a>Személyes Microsoft-fiókjával

Személyes Microsoft-fiókkal használandó Git-integrációval, kapcsolat a személyes Azure tárházban a szervezet Active Directory.

1. A személyes Microsoft-fiók hozzáadása a szervezet Active Directory vendégként. További információ: [hozzáadása az Azure Active Directory B2B együttműködési felhasználókat az Azure Portalon](../active-directory/b2b/add-users-administrator.md).

2. Jelentkezzen be az Azure Portalon, a személyes Microsoft-fiókjával. Ezután váltson a szervezet Active Directory.

3. Nyissa meg az Azure DevOps szakaszhoz, ahol most már láthatók a személyes tárházban. Válassza ki a tárházat, és az Active Directory connect.

Ezeket a konfigurációs lépéseket a személyes tárházban érhető el a Data Factory felhasználói felületén Git-integráció beállítása során.

További információ a szervezet Active Directory Azure-Adattárakkal való csatlakozással kapcsolatban lásd: [Azure DevOps-szervezet csatlakozhat az Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad).

### <a name="switch-to-a-different-git-repo"></a>Váltson egy másik Git-adattár

Váltson át egy másik Git-adattár, keresse meg a ikonra a Data Factory – Áttekintés lap jobb felső sarokban az alábbi képernyőképen látható módon. Ha nem látja a ikonra, törölje a helyi böngésző gyorsítótárát. Válassza ki a ikonra kattintva távolítsa el az aktuális tárház a hozzárendelését.

Miután eltávolítja a társítást az aktuális tárház, konfigurálhatja a Git-beállításait, hogy használjon egy másik adattárral. Ezután importálhatja meglévő Data Factory-erőforrásokkal az új adattárhoz.

![Távolítsa el a hozzárendelését az aktuális Git-adattár](media/author-visually/remove-repo.png)

### <a name="use-version-control"></a>Használja a verziókövetés
Verziókövető rendszereket (más néven _verziókövetés_) lehetővé teszik a fejlesztőknek, kód és nyomon követésére helyadatbázisokban végrehajtott módosításokat a kód alap másokkal közös használatához. Verziókövetés fontos több fejlesztési projektek eszközzel.

Minden egyes Azure-Adattárakkal Git-tárházba az adat-előállító társított együttműködési ág rendelkezik. (`master` alapértelmezett együttműködési ág). Felhasználók is létrehozhat a szolgáltatás ágak kattintva **+ új ág** és végezze el a szolgáltatás ágak fejlesztés.

![A kód módosítása szinkronizálása vagy közzététele](media/author-visually/sync-publish.png)

Amikor készen áll a funkció fejlesztéséhez a szolgáltatás ágban, kattintson **Create pull-kérelem**. Ez a művelet vesz igénybe, hogy ahol lekéréses kérelmeket, is növelheti Azure-Adattárakkal Git felülvizsgálatok code, és egyesíti az egyes módosításokat az együttműködés ágban. (`master` az alapértelmezett beállítás). A Data Factory szolgáltatásban közzétenni az együttműködés ágból csak engedélyezett. 

![Új lekéréses kérelem létrehozása](media/author-visually/create-pull-request.png)

### <a name="configure-publishing-settings"></a>Közzétételi beállítások konfigurálása

A publish - ág konfigurálása, a Resource Manager-sablonok tároló - ág hozzáadása egy `publish_config.json` fájlt az együttműködés ág gyökérmappájába. A Data Factory beolvassa ezt a fájlt, a mező keres `publishBranch`, és (Ha még nem létezik) létrehoz egy új ágat a megadott érték. Az összes Resource Manager-sablonok majd a megadott helyre írja. Példa:

```json
{
    "publishBranch": "factory/adf_publish"
}
```

Ha közzéteszi a Git módban, erősítse meg, hogy a Data Factory használ a közzététel ág meg a várt a következő képernyőképen látható módon:

![Erősítse meg a helyes közzétételi ág](media/author-visually/configure-publish-branch.png)

Közzététel felkínálásával új ág megadása esetén a Data Factory nem törli az előző közzététel ág. Ha az előző közzététel ágat szeretne távoli, törölje azt manuálisan.

A Data Factory csak olvassa be a `publish_config.json` fájlt, amikor betölti az előállító. Ha már rendelkezik az előállító, a portál betöltése, frissítse a böngészőben, hogy a módosítások érvénybe léptetéséhez.

### <a name="publish-code-changes"></a>Kód módosítások közzététele
Rendelkezik sikeresen összevonta a együttműködési ág módosításai után (`master` az alapértelmezett beállítás), jelölje be **közzététel** , manuálisan tegye közzé a kódmódosításokat a főágban, a Data Factory szolgáltatásban.

![Módosítások mentése a Data Factory szolgáltatásban](media/author-visually/publish-changes.png)

> [!IMPORTANT]
> Nem áll a Data Factory szolgáltatásban üzembe helyezett képviselő a master ággal. A főág *kell* manuálisan tehetők közzé a Data Factory szolgáltatásban.

### <a name="advantages-of-git-integration"></a>Git-integráció előnyei

-   **Verziókövetés**. A data factory számítási feladatok egyre rendkívül fontos, szeretné integrálni szeretné a gyári git használatával kihasználhatja az olyan forrás vezérlő előnyöket a következőhöz hasonló:
    -   Változások követése/naplózási képesség.
    -   Lehetővé teszi a hibák bevezetett módosítások visszaállítása.
-   **Részleges menti**. Változtatással egy időben sok módosítást az előállító, akkor lesz vegye figyelembe, hogy a normál élő módban nem lehet a módosítások mentése vázlatként, mert nem áll készen, vagy Ön nem szeretné elveszteni a módosításokat, abban az esetben, ha a számítógép összeomlik. A Git-integrációval továbbra is a módosítások mentése növekményes, és csak akkor, amikor készen áll az előállító közzététele. Git a munkáját, egy átmeneti helyen funkcionál, amíg nem ellenőrizte a módosításokat az megfelelően.
-   **Együttműködés és a vezérlő**. Ha több csoport tagjai azonos factoryhoz résztvevő, szüksége lehet ahhoz, hogy a csapattársai együtt tudjanak működni egymással a kódvizsgálati folyamattal keresztül. Akkor is állíthatja az előállító úgy, hogy nem minden közreműködő factoryhoz van engedélye az előállító üzembe helyezése. Csapat tagjai csak a módosításokat a Git használatával engedélyezni, de csak bizonyos személyeknek a csapat "Közzététel" számára engedélyezett a módosítások az előállító.
-   **Eltérések megjelenítő**. A Git módban egy nagyszerű diff a tartalom, amely arra készül, hogy kap, a gyári közzé. A diff látható összes erőforrások vagy entitás, amely rendelkezik a módosított/hozzáadni vagy törölni az előállító közzétett legutóbbi indítása óta. A diff alapján, vagy továbbra is a további közzétételi, vagy lépjen vissza és a módosítások ellenőrzése, majd térjen vissza később.
-   **CI/CD jobb**. Ha Git módot használ, konfigurálhatja a kibocsátási folyamat nem annyi fejlesztési előállító végzett módosítások automatikusan indítható. Érhet el az előállító, a Resource Manager-sablonban szereplő paraméterekkel rendelkezésre álló tulajdonságok személyre szabásához. Csak a szükséges tulajdonságkészlettel paraméterekként tartani, és minden más esetben nehéz kódolt hasznos lehet.
-   **Jobb teljesítmény**. Egy átlagos gyári betölti a 10 x alkalommal gyorsabb Git módban, mint a rendszeres élő módban, mert az erőforrásokat a Git használatával letöltődnek.

### <a name="best-practices-for-git-integration"></a>Ajánlott eljárások a Git-integráció

-   **Engedélyek**. Általában nem szeretné frissíteni az előállító engedélyeket is biztosítani csapattagok.
    -   Az összes csoport tagjai kell Olvasás engedéllyel rendelkezik az adat-előállítóban.
    -   Személyek csak egy kiválasztott csoportja factoryhoz és az, hogy a "Data Factory-közreműködő" szerepkört az adat-előállítóval részeként kell közzé kell tenni.
    -   A bevált gyakorlatok a forrás-vezérlő egyik is teszi lehetővé az együttműködést ággal való közvetlen-ellenőrzéshez. Ez a követelmény megakadályozza, hogy a hibák, minden egyes halad végig egy Pull-kérési folyamat.
-   **Váltás az üzemmódok**.
    -    Miután a Git módban van, nem ajánlott oda-vissza élő üzemmódba válthat elsősorban, mert az nem látható az élő módban végzett módosításokat mikor váltson vissza a Git. Próbálja meg magát a Git módban hajtsa végre a módosításokat, és tegye közzé őket a felhasználói felületen keresztül.
    -   Hasonlóképpen ne használjon Data factory powershell-parancsmagok vagy, ugyanezt a hatást által közvetlenül a megadott módosítások alkalmazása az élő előállítóval érnek.
-   **Használja az Azure Key vault jelszavak**.
    -   Javasoljuk, hogy bármely kapcsolati karakterláncok vagy a DataFactory társított szolgáltatások jelszavak tárolása azurekeyvault értékre van használatával.
    -   Nem tároljuk bármely ilyen titkos információk git (biztonsági okokból), így minden társított szolgáltatások módosításai azonnal közzétett élő factoryhoz. Ez azonnali közzététele néha nem megfelelő, mivel előfordulhat, hogy a módosítások nem közben teszteltük, amely defeats Git célját.
    -   Ennek eredményeképpen az összes ilyen titkos adatokat kell beolvasni társított szolgáltatásokat, amelyek használják az Azure Key Vault-alapú.
    -   A Key vault, a többi előnyöket nyújtja az, hogy ez megkönnyíti a CI/CD, azáltal, hogy nem, adja meg titkos adatokat során a Resource Manager sablon telepítése.

## <a name="author-with-github-integration"></a>Felügyeleti csomagok készítése a GitHub-integráció

GitHub-integráció történő vizuális tartalomkészítésről támogatja a forráskezelés és az együttműködést az adat-előállító folyamatok munka. Adat-előállító egy GitHub-fiók tárházat a verziókövetés, együttműködési, versioning is társíthat. Egy GitHub-fiók több tárházak rendelkezhet, de egy GitHub-adattár társítható csak egy adat-előállító. Ha nem rendelkezik egy GitHub-fiók vagy egy tárház, hajtsa végre a [ezek az utasítások](https://github.com/join) az erőforrások létrehozásához.

A GitHub-integráció, a Data Factory támogatja mind a nyilvános GitHub (azaz [ https://github.com ](https://github.com)) és a GitHub Enterprise-zal. A Data Factory nyilvános és privát GitHub-adattárak használhatja, mennyi ideig rendelkezik-e olvasási és írási engedélye a GitHub-tárház.

Egy GitHub-adattár konfigurálásához rendelkeznie kell Azure-előfizetést használ, rendszergazdai jogosultságokkal.

A 9 perces bevezető és a funkció bemutatójáért tekintse meg a következő videót:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Azure-Data-Factory-visual-tools-now-integrated-with-GitHub/player]

### <a name="limitations"></a>Korlátozások

- Egy GitHub-adattár feladatparancsfájl és adatfájlok tárolhatja. Azonban hogy a fájlok manuális feltöltése az Azure Storage. Data Factory-folyamatok nem automatikusan tölt fel egy GitHub-tárházat az Azure Storage tárolja, a parancsfájl vagy adatfájlok.

- GitHub Enterprise-zal 2.14.0 régebbi verzióját a Microsoft Edge böngészővel nem működik.

- Az adatok tényező vizuális szerzői eszközökkel GitHub-integráció csak a Data Factory általánosan elérhető verziójában működik.

### <a name="configure-a-public-github-repository-with-azure-data-factory"></a>Egy nyilvános GitHub-adattár konfigurálása az Azure Data Factoryvel

Az adat-előállító két módszer segítségével konfigurálhatja egy GitHub-adattárból.

**Konfiguráció (nyilvános tárházban) 1. módszer: Első lépések lap**

Az Azure Data Factoryben, nyissa meg a **első lépések** lapot. Válassza ki **kódtár konfigurálása**:

![Adat-előállító első lépések lap](media/author-visually/github-integration-image1.png)

A **adattár beállításai** konfigurációs panelen jelenik meg:

![GitHub-adattár beállításai](media/author-visually/github-integration-image2.png)

A panelen látható a következő Azure-Adattárakkal kód adattár beállításai:

| **Beállítás**                                              | **Leírás**                                                                                                                                                                                                                                                                                                                                                                                                                   | **Érték**          |
|----------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------|
| **Adattár típusa**                                      | Az Azure-Adattárakkal kódtár típusa.                                                                                                                                                                                                                                                                                                                                                                                             | GitHub             |
| **GitHub-fiók**                                       | A GitHub-fiók neve. Ez a név megtalálja https://github.com/{account neve} / {adattár neve}. Ellenőrizheti, hogy ezen a lapon kéri, hogy adja meg a GitHub OAuth hitelesítő adatokat ahhoz a GitHub-fiókhoz.                                                                                                                                                                                                                                               |                    |
| **RepositoryName**                                       | A GitHub code-adattár neve. GitHub-fiókjukban, kezelheti a forráskód Git-tárházak tartalmaznak. Hozzon létre egy új adattár, vagy használjon egy meglévő adattárhoz, amely már a fiókjához.                                                                                                                                                                                                                              |                    |
| **Együttműködés ág**                                 | A GitHub együttműködési ág közzététel szolgálja ki. Alapértelmezés szerint a főadatbázis. Ezen beállítás módosításához, abban az esetben, ha szeretne közzétenni egy másik ágban származó erőforrásokat.                                                                                                                                                                                                                                                               |                    |
| **Gyökérmappa**                                          | A legfelső szintű mappát a GitHub együttműködési ágban.                                                                                                                                                                                                                                                                                                                                                                             |                    |
| **Meglévő Data Factory-erőforrások importálása az adattárba** | Megadja, hogy a meglévő data factory-erőforrások importálása a UX **szerzői vászon** egy GitHub-tárházba. Jelölje be a data factory-erőforrások importálása a társított Git-tárház JSON formátumban. Ez a művelet exportálja az egyes erőforrások külön-külön (azaz a társított szolgáltatásokat és adatkészleteket exportálása külön JSONs be). Ha a jelölőnégyzet nincs bejelölve, a meglévő erőforrások nem importálja. | Kiválasztott (alapértelmezett) |
| **Az erőforrás importálásához ág**                       | Itt adhatja meg, mely a főágban (a folyamatok, adatkészletek, a társított szolgáltatások stb.) a data factory-erőforrások importálása. Erőforrások importálhatja a következő fiókok közül: egy. Együttműködés b. Hozzon létre új c. Meglévő használata                                                                                                                                                                                                     |                    |

#### <a name="configuration-method-2-public-repo-ux-authoring-canvas"></a>Konfiguráció (nyilvános tárházban) 2. módszer: Szerzői vászon UX

Az Azure Data Factory felhasználói **szerzői vászon**, keresse meg az adat-előállítóhoz. Válassza ki a **adat-előállító** legördülő menüből, és válassza ki **kódtár konfigurálása**.

A konfiguráció panelen jelenik meg. A konfigurációs beállításaival kapcsolatos információkért lásd: található leírások *konfigurációs módszer 1* felett.

### <a name="configure-a-github-enterprise-repository-with-azure-data-factory"></a>A GitHub Enterprise-zal adattár konfigurálása az Azure Data Factoryvel

Az adat-előállító két módszer segítségével konfigurálhatja a GitHub Enterprise-zal tárház.

#### <a name="configuration-method-1-enterprise-repo-lets-get-started-page"></a>Konfiguráció (Enterprise tárházban) 1. módszer: Első lépések lap

Az Azure Data Factoryben, nyissa meg a **első lépések** lapot. Válassza ki **kódtár konfigurálása**:

![Adat-előállító első lépések lap](media/author-visually/github-integration-image1.png)

A **adattár beállításai** konfigurációs panelen jelenik meg:

![GitHub-adattár beállításai](media/author-visually/github-integration-image3.png)

A panelen látható a következő Azure-Adattárakkal kód adattár beállításai:

| **Beállítás**                                              | **Leírás**                                                                                                                                                                                                                                                                                                                                                                                                                   | **Érték**          |
|----------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------|
| **Adattár típusa**                                      | Az Azure-Adattárakkal kódtár típusa.                                                                                                                                                                                                                                                                                                                                                                                             | GitHub             |
| **Használja a GitHub Enterprise-zal**                                | Jelölőnégyzetet, válassza ki a GitHub Enterprise-zal                                                                                                                                                                                                                                                                                                                                                                                              |                    |
| **GitHub Enterprise-zal URL-címe**                                | A GitHub Enterprise-zal gyökér URL-címe. Például:https://github.mydomain.com                                                                                                                                                                                                                                                                                                                                                          |                    |
| **GitHub-fiók**                                       | A GitHub-fiók neve. Ez a név megtalálja https://github.com/{account neve} / {adattár neve}. Ellenőrizheti, hogy ezen a lapon kéri, hogy adja meg a GitHub OAuth hitelesítő adatokat ahhoz a GitHub-fiókhoz.                                                                                                                                                                                                                                               |                    |
| **RepositoryName**                                       | A GitHub code-adattár neve. GitHub-fiókjukban, kezelheti a forráskód Git-tárházak tartalmaznak. Hozzon létre egy új adattár, vagy használjon egy meglévő adattárhoz, amely már a fiókjához.                                                                                                                                                                                                                              |                    |
| **Együttműködés ág**                                 | A GitHub együttműködési ág közzététel szolgálja ki. Alapértelmezés szerint a főadatbázis. Ezen beállítás módosításához, abban az esetben, ha szeretne közzétenni egy másik ágban származó erőforrásokat.                                                                                                                                                                                                                                                               |                    |
| **Gyökérmappa**                                          | A legfelső szintű mappát a GitHub együttműködési ágban.                                                                                                                                                                                                                                                                                                                                                                             |                    |
| **Meglévő Data Factory-erőforrások importálása az adattárba** | Megadja, hogy a meglévő data factory-erőforrások importálása a UX **szerzői vászon** egy GitHub-tárházba. Jelölje be a data factory-erőforrások importálása a társított Git-tárház JSON formátumban. Ez a művelet exportálja az egyes erőforrások külön-külön (azaz a társított szolgáltatásokat és adatkészleteket exportálása külön JSONs be). Ha a jelölőnégyzet nincs bejelölve, a meglévő erőforrások nem importálja. | Kiválasztott (alapértelmezett) |
| **Az erőforrás importálásához ág**                       | Itt adhatja meg, mely a főágban (a folyamatok, adatkészletek, a társított szolgáltatások stb.) a data factory-erőforrások importálása. Erőforrások importálhatja a következő fiókok közül: egy. Együttműködés b. Hozzon létre új c. Meglévő használata                                                                                                                                                                                                     |                    |

#### <a name="configuration-method-2-enterprise-repo-ux-authoring-canvas"></a>Konfiguráció (Enterprise tárházban) 2. módszer: Szerzői vászon UX

Az Azure Data Factory felhasználói **szerzői vászon**, keresse meg az adat-előállítóhoz. Válassza ki a **adat-előállító** legördülő menüből, és válassza ki **kódtár konfigurálása**.

A konfiguráció panelen jelenik meg. A konfigurációs beállításaival kapcsolatos információkért lásd: található leírások *konfigurációs módszer 1* felett.

## <a name="use-the-expression-language"></a>Kifejezés nyelve
Az Azure Data Factory által támogatott kifejezés nyelv használatával megadhatja a tulajdonságértékek a kifejezéseket.

Adja meg a kifejezéseket tulajdonságértékek kiválasztásával **dinamikus tartalom hozzáadása**:

![Kifejezés nyelve](media/author-visually/dynamic-content-1.png)

## <a name="use-functions-and-parameters"></a>Paraméterek és függvények használata

Használhatja a functions vagy adja meg a folyamatokról és adatkészletekről paraméterek adat-előállító **Kifejezésszerkesztő**:

Támogatott kifejezésekkel kapcsolatos további információkért lásd: [kifejezések és függvények az Azure Data Factoryban](control-flow-expression-language-functions.md).

![Dinamikus tartalom hozzáadása](media/author-visually/dynamic-content-2.png)

## <a name="provide-feedback"></a>Visszajelzés küldése
Válassza ki **visszajelzés** funkciókkal kapcsolatos megjegyzés, vagy értesítést küldeni a Microsoft az eszközzel kapcsolatos problémák:

![Visszajelzés](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>További lépések
További információk figyelése és kezelése a folyamatok kapcsolatban lásd: [figyelő programozással felügyelheti a folyamatokat és](monitor-programmatically.md).
