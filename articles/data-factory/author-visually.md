---
title: "Vizuálisan létre Azure adat-előállítók |} Microsoft Docs"
description: "Megtudhatja, hogyan vizuálisan létre Azure adat-előállítók"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/9/2018
ms.author: shlo
ms.openlocfilehash: 3e67665facba78c4ca8e2317f0323b4c5c02a49c
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2018
---
# <a name="visually-author-data-factories"></a>Vizuálisan Szerző adat-előállítók
Az Azure Data Factory UX élmény a felhasználók vizuálisan hozhatnak létre, és az adat-előállítóban erőforrások telepítése egy sorba kód írása nélkül. Ez a kódmentes felület lehetővé teszi húzza és dobja el a tevékenységeket a feldolgozási sor vászonra, hajtsa végre a teszt futtatása, debug ismételt, és üzembe helyezés és figyelheti a folyamatot futtat. Ha szeretné az ADF UX eszközzel két módon:

1. Közvetlenül a Data Factory szolgáltatással működik
2. Együttműködés, verziókezelő vagy versioning VSTS Git integráció konfigurálása

## <a name="authoring-with-data-factory"></a>Az adat-előállító készítése
Az első lehetőség közvetlenül a Data Factory mód alkalmazással végzett létrehozási műveletek. Ez a módszer eltér a szerzői VSTS kód tárház keresztül abban, hogy nem tárolja a JSON entitásai számára a módosításokat a tárházba, és nem az optimalizált együttműködés vagy verziókezelést.

![Adat-előállító konfigurálása](media/author-visually/configure-data-factory.png)

Adat-előállító módban csak a "Publish" módban van. Végzett módosításokat közvetlenül a Data Factory szolgáltatásnak kerülnek közzétételre.

![Adat-előállító közzététele](media/author-visually/data-factory-publish.png)

## <a name="authoring-with-vsts-git-integration"></a>Az VSTS Git integráció készítése
Szerkesztése a VSTS Git integráció lehetővé teszi a verziókövetési és együttműködés a data factory folyamatok készítése során. Felhasználók lehetősége nyílik egy adat-előállító társítani a VSTS Git fiók tárház verziókezelő, együttműködés és versioning stb. Egy olyan VSTS GIT-fiók rendelkezhet több tárházak találhatók. Azonban a VSTS Git-tárház csak társítható egy egyetlen adat-előállítóban. Ha egy VSTS-fiókot és a tárház már nem rendelkezik, hozzon létre egyet [Itt](https://docs.microsoft.com/en-us/vsts/accounts/create-account-msa-or-work-student).

### <a name="configure-vsts-git-repo-with-azure-data-factory"></a>VSTS Git-tárház beállítása az Azure Data Factoryvel
Felhasználók is konfigurálhatja a VSTS GIT-tárház egy adat-előállító két módszerrel.

#### <a name="method-1-lets-get-started-page"></a>1. módszer: "lássunk" lap

A "Lássunk" lapra, majd kattintson a "Kód tárház konfigurálása"

![Kód adattár konfigurálása](media/author-visually/configure-repo.png)

Ott egy ügyféloldali panel jelenik meg a tárház beállításainak konfigurálása.

![Tárház beállításainak konfigurálása](media/author-visually/repo-settings.png)
* **Tárház típus**: a Visual Studio Team Services Git (jelenleg Github nem támogatott.)
* **A Visual Studio Team Services-fiók**: A fiók neve található https://{account neve}. visualstudio.com webhelyre. Jelentkezzen be a VSTS-fiókba [Itt](https://www.visualstudio.com/team-services/git/) és érhető el a Visual Studio profil adattárak és projektek
* **Projektnév:** a projekt nevét található https://{account name}.visualstudio.com/{project neve}
* **RepositoryName:** a tárház nevét. VSTS-projektek Git tárhelyek kezelése a forráskódot, a projekt növekedésével tartalmaz. Hozzon létre egy új tárházat, vagy használjon egy meglévő-tárház már a projekthez.
* **Importálja a meglévő adat-előállító erőforrások tárházba**: szerint a jelölőnégyzet bejelölésével, az aktuális adatokat feldolgozó erőforrások a UX vásznon, a társított VSTS GIT-tárházba JSON formátumban hozhatóak létre importálhatja. Ez a művelet exportálja az egyes erőforrások külön-külön (Ez azt jelenti, hogy társított szolgáltatások és adatkészletek exportálása külön JSONs be).    Ha törli ezt a jelölőnégyzetet, a rendszer nem importálta a meglévő erőforrásokat a Git-tárházba.

#### <a name="method-2-from-authoring-canvas"></a>2. módszer: A vászon készítése

A "szerzői műveletek vászonra" kattintson a "Data Factory" legördülő menü a data factory neve alatt. Kattintson a "Konfigurálása kód tárház." Hasonló **1. módszer**, egy ügyféloldali panel jelenik meg a tárház beállításainak konfigurálása. További információ a beállításokról az előző szakaszban talál.

![Kód tárház 2 konfigurálása](media/author-visually/configure-repo-2.png)

### <a name="version-control"></a>Verziókövetés
Verziókövetés, más néven a verziókövetés, mint rendszerek a fejlesztők olyan kódot, és nyomon követése módosításait a kód alap másokkal közösen szerkeszthet. A verziókövetési rendszerrel több fejlesztői projektek alapvető eszközét.

Minden adat-előállító egyszer társított VSTS Git-tárház egy főághoz rendelkezik. Ezekből a minden felhasználó, aki hozzáféréssel rendelkezik a VSTS Git-tárházba két pontot tartalmaz, ha módosítja: szinkronizálása és közzététele.

![Szinkronizálási közzététele](media/author-visually/sync-publish.png)

#### <a name="sync"></a>Sync

Miután "sync" gombra kattint, a főágba értéke helyett a helyi fiókiroda lekéréses, vagy küldje le a módosításokat az Ön helyi ágából a főágba.

![Módosítások szinkronizálása](media/author-visually/sync-change.png)

#### <a name="publish"></a>Közzététel
 A Data Factory szolgáltatásnak főághoz változtatásokat.

> [!NOTE]
> A **főághoz nincs képviselő mi történik a Data Factory szolgáltatásban.** A főágba *kell* manuálisan közzé kell tenni a Data Factory szolgáltatásnak.




## <a name="expression-language"></a>Kifejezés nyelve

Felhasználók megadhatja a kifejezések a definiáló tulajdonságértékek az Azure Data Factory által támogatott kifejezés nyelv használatával. Lásd: [kifejezések és az Azure Data Factory funkciók](control-flow-expression-language-functions.md) a több arról, hogy mely kifejezések használata támogatott.

Adja meg a UX tulajdonságértékeket kifejezések így.

![Kifejezés nyelve](media/author-visually/expression-language.png)

## <a name="parameters"></a>Paraméterek
Felhasználók paraméter is megadható adatcsatornákat és adathalmazokat, a "Parameters" lapon. Ezenkívül kihasználhassák tulajdonságai paraméterek könnyen billentyűkombináció lenyomásával "Hozzáadása a dinamikus tartalmat."

![Dinamikus tartalom](media/author-visually/dynamic-content.png)

Ott meglévő paraméter használatára, vagy adjon meg egy új paraméter a tulajdonság értéke.

![Paraméterek](media/author-visually/parameters.png)

## <a name="feedback"></a>Visszajelzés
Kattintson a visszajelzését (Microsoft) számos funkció vagy probléma merül fel, akkor előfordulhat, hogy elérhető a "Visszajelzés" ikonra.

![Visszajelzés](media/monitor-visually/feedback.png)

## <a name="next-steps"></a>További lépések

Figyelés és folyamatok kezelése, lásd: [figyelő programozott folyamatok kezelését és](monitor-programmatically.md) cikk
