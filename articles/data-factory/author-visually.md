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
ms.devlang: na
ms.topic: conceptual
ms.date: 06/01/2018
ms.author: shlo
ms.openlocfilehash: 655a6ab2960047cde50bec2953015283ca8577f0
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2018
ms.locfileid: "39214853"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Az Azure Data Factoryban vizuális szerzői
Az Azure Data Factory felhasználói felületen felület (UX) lehetővé teszi, hogy vizuálisan hozhat létre és helyezheti üzembe az erőforrásokat a data Factory kód írása nélkül. Húzza a tevékenységeket a folyamatvásznon, végre, iteratív hibakeresést és üzembe helyezése és a folyamat futásának monitorozása. Kétféleképpen felhasználói a vizuális szerzői műveletek végrehajtásához:

- Szerző közvetlenül a Data Factory szolgáltatással.
- Felügyeleti csomagok készítése a Visual Studio Team Services (VSTS) Git-integráció együttműködés, verziókövetési vagy versioning.

## <a name="author-directly-with-the-data-factory-service"></a>Szerző közvetlenül a Data Factory szolgáltatással
A Data Factory szolgáltatásban történő vizuális tartalomkészítésről eltér a vizuális szerzői a vsts-sel két módon:

- A Data Factory szolgáltatás nem tartalmaz egy helyen a JSON-entitások a módosítások tárolásához.
- A Data Factory szolgáltatás együttműködési vagy a verziókezeléshez nincs optimalizálva.

![A Data Factory szolgáltatás konfigurálása ](media/author-visually/configure-data-factory.png)

A felhasználói felület használata esetén **szerzői vászon** ahhoz, hogy a Data Factory szolgáltatás csak a közvetlenül a **összes közzététele** módban érhető el. A végrehajtott módosításokat közvetlenül a Data Factory szolgáltatás vannak közzétéve.

![Közzétételi mód](media/author-visually/data-factory-publish.png)

## <a name="author-with-vsts-git-integration"></a>Felügyeleti csomagok készítése a VSTS Git-integráció
VSTS Git-integrációval történő vizuális tartalomkészítésről támogatja a forráskezelés és az együttműködést az adat-előállító folyamatok munka. Adat-előállító társíthatja egy VSTS Git-fiók tárház verziókövetés, együttműködési, verziószámozása és így tovább. VSTS Git egyetlen fiók több tárházak rendelkezhet, de egy VSTS Git-tárház társítható csak egy adat-előállító. Ha nem rendelkezik a VSTS-fiók vagy a tárház, hajtsa végre a [ezek az utasítások](https://docs.microsoft.com/vsts/accounts/create-account-msa-or-work-student) az erőforrások létrehozásához.

> [!NOTE]
> Egy VSTS GIT-tárház feladatparancsfájl és adatfájlok tárolhatja. Azonban hogy a fájlok manuális feltöltése az Azure Storage. Data Factory-folyamatok nem automatikusan tölti fel parancsfájl vagy adatfájlok VSTS GIT-tárházat az Azure Storage tárolja.

### <a name="configure-a-vsts-git-repository-with-azure-data-factory"></a>VSTS Git-adattár konfigurálása az Azure Data Factoryvel
Az adat-előállító egy VSTS GIT-tárház két módszer segítségével konfigurálhatja.

#### <a name="method1"></a> Konfiguráció 1. módszer: első lépések lap

Az Azure Data Factoryben, nyissa meg a **első lépések** lapot. Válassza ki **kódtár konfigurálása**:

![Egy VSTS-kódtárral konfigurálása](media/author-visually/configure-repo.png)

A **adattár beállításai** konfigurációs panelen jelenik meg:

![A kód tárház beállításainak konfigurálása](media/author-visually/repo-settings.png)

A panelen látható a következő VSTS kód adattár beállításai:

| Beállítás | Leírás | Érték |
|:--- |:--- |:--- |
| **Adattár típusa** | A VSTS-kódtárral típusa.<br/>**Megjegyzés:**: GitHub jelenleg nem támogatott. | Visual Studio Team Services Git |
| **Azure Active Directory** | Az Azure AD-bérlő neve. | <your tenant name> |
| **Visual Studio Team Services Account** | A VSTS-fiók nevét. Megkeresheti a VSTS-fiók nevét, `https://{account name}.visualstudio.com`. Is [jelentkezzen be a VSTS-fiók](https://www.visualstudio.com/team-services/git/) érhető el a Visual Studio-profil, és az adattárak és a projekteket. | <your account name> |
| **ProjectName** | A VSTS-projekt neve. Megkeresheti a VSTS-projekt neve, `https://{account name}.visualstudio.com/{project name}`. | <your VSTS project name> |
| **RepositoryName** | A VSTS kód adattár nevét. VSTS-projekteket a Git-tárházakat a forráskód kezelheti a projekt növekedésével tartalmaz. Hozzon létre egy új adattár, vagy használjon egy meglévő adattárhoz, amely a projektben már van. | <your VSTS code repository name> |
| **Együttműködés ág** | A VSTS együttműködési ág közzétételéhez használt. Alapértelmezés szerint a `master`. Módosítsa az erőforrások egy másik ágban való közzétételéhez szeretne. | <your collaboration branch name> |
| **Gyökérmappa** | A VSTS-együttműködés ág a gyökérmappában. | <your root folder name> |
| **Meglévő Data Factory-erőforrások importálása az adattárba** | Megadja, hogy a meglévő data factory-erőforrások importálása a UX **szerzői vászon** egy VSTS Git-tárházba. Jelölje be a data factory-erőforrások importálása a társított Git-tárház JSON formátumban. Ez a művelet exportálja az egyes erőforrások külön-külön (azaz a társított szolgáltatásokat és adatkészleteket exportálása külön JSONs be). Ha a jelölőnégyzet nincs bejelölve, a meglévő erőforrások nem importálja. | Kiválasztott (alapértelmezett) |

#### <a name="configuration-method-2-ux-authoring-canvas"></a>Konfigurációs 2. módszer: UX szerzői vászon
Az Azure Data Factory felhasználói **szerzői vászon**, keresse meg az adat-előállítóhoz. Válassza ki a **adat-előállító** legördülő menüből, és válassza ki **kódtár konfigurálása**.

A konfiguráció panelen jelenik meg. A konfigurációs beállításaival kapcsolatos információkért lásd: található leírások <a href="#method1">konfigurációs módszer 1</a>.

![A UX szerzői kód tárház beállításainak konfigurálása](media/author-visually/configure-repo-2.png)

#### <a name="switch-to-a-different-git-repo"></a>Váltson egy másik Git-adattár

Váltson át egy másik Git-adattár, keresse meg a ikonra a Data Factory – Áttekintés lap jobb felső sarokban az alábbi képernyőképen látható módon. Ha nem látja a ikonra, törölje a helyi böngésző gyorsítótárát. Válassza ki a ikonra kattintva távolítsa el az aktuális tárház a hozzárendelését.

Miután eltávolítja a társítást az aktuális tárház, konfigurálhatja a Git-beállításait, hogy használjon egy másik adattárral. Ezután importálhatja meglévő Data Factory-erőforrásokkal az új adattárhoz.

![Távolítsa el az aktuális Git-adattárat a hozzárendelését.](media/author-visually/remove-repo.png)

### <a name="use-version-control"></a>Használja a verziókövetés
Verziókövető rendszereket (más néven _verziókövetés_) lehetővé teszik a fejlesztőknek, kód és nyomon követésére helyadatbázisokban végrehajtott módosításokat a kód alap másokkal közös használatához. Verziókövetés fontos több fejlesztési projektek eszközzel.

Minden egyes VSTS Git-tárház az adat-előállító társított együttműködési ág rendelkezik. (`master` alapértelmezett együttműködési ág). Felhasználók is létrehozhat a szolgáltatás ágak kattintva **+ új ág** és végezze el a szolgáltatás ágak fejlesztés.

![A kód módosítása szinkronizálása vagy közzététele](media/author-visually/sync-publish.png)

Amikor készen áll a funkció fejlesztéséhez a szolgáltatás ágban, kattintson **Create pull-kérelem**. Ekkor megjelenik a VSTS GIT, ahol lekéréses is növelheti a kérések, code felülvizsgálatok, és módosítások az együttműködés ágban. (`master` az alapértelmezett beállítás). A Data Factory szolgáltatásban közzétenni az együttműködés ágból csak engedélyezett. 

![Új lekéréses kérelem létrehozása](media/author-visually/create-pull-request.png)

#### <a name="publish-code-changes"></a>Kód módosítások közzététele
Rendelkezik sikeresen összevonta a együttműködési ág módosításai után (`master` az alapértelmezett beállítás), jelölje be **közzététel** , manuálisan tegye közzé a kódmódosításokat a főágban, a Data Factory szolgáltatásban.

![Módosítások mentése a Data Factory szolgáltatásban](media/author-visually/publish-changes.png)

> [!IMPORTANT]
> Nem áll a Data Factory szolgáltatásban üzembe helyezett képviselő a master ággal. A főág *kell* manuálisan tehetők közzé a Data Factory szolgáltatásban.

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
