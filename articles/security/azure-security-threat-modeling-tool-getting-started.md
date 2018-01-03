---
title: "Ismerkedés az Azure - modellezési eszköz Microsoft fenyegetés - |} Microsoft Docs"
description: "Ez a működés közben a fenyegetés modellezési eszköz Kiemelés a mélyebb áttekintését."
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 73d6b42e7a97d6041f6213a1f7d060806734d763
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="getting-started-with-the-threat-modeling-tool"></a>Első lépések a fenyegetések modellezése eszközzel

A felhő- és eszközök a vállalati biztonsági csoport, amely a fenyegetések modellezése eszköz Preview a korábban az év egy szabadként  **[kattintson-letöltési](https://aka.ms/tmtpreview)**. Az mechanizmus módosítása lehetővé teszi küldje le a legújabb javítások és hibajavításokat tartalmaz az ügyfelek minden alkalommal, amikor megnyitják az eszközt, így könnyebben karbantartásáról és használatáról.
Ez a cikk végigvezeti az első lépések a Microsoft SDL fenyegetés megközelítés modellezési, és bemutatja, hogyan számára a nagy fenyegetés egy gerincét a biztonsági folyamatot, az eszköz használatához.

Ez a cikk az SDL fenyegetés megközelítés modellezési meglévő ismerete épül. A gyors áttekintését lásd  **[fenyegetések modellezése webalkalmazások](https://msdn.microsoft.com/library/ms978516.aspx)**  és archivált verziója  **[felderíthesse biztonsági hiányosságokat STRIDE módszert használva](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy)**  MSDN-cikk 2006 közzétéve.

Gyorsan összefoglalva, a megközelítés diagram létrehozása, fenyegetések azonosítására, őket kiküszöböléséhez és ellenőrzése minden megoldás. Ez a diagram, amely kiemeli ezt a folyamatot:

![SDL-folyamat](./media/azure-security-threat-modeling-tool-feature-overview/sdlapproach.png)

## <a name="starting-the-threat-modeling-process"></a>A fenyegetés modellezési folyamatának indítása

Ha a fenyegetések modellezése eszköz elindításához néhány dolog, láthatja, a képen látható módon:

![Üres Start lap](./media/azure-security-threat-modeling-tool-feature-overview/tmtstart.png)

### <a name="threat-model-section"></a>Fenyegetés modell szakasz

| Összetevő                                   | Részletek                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Visszajelzés, a javaslatok és a problémák gomb** | Megjelenik a  **[MSDN fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=sdlprocess)**  összes dolgot SDL. Ez lehetővé teszi elolvasandó más felhasználók tevékenységeit, és a lehetséges megoldások és javaslatokat. Ha még mindig nem talál meg, amit keres, e-mailben tmtextsupport@microsoft.com segítséget a támogatási csapat számára                                                                                                                            |
| **A modell létrehozása**                          | Ahhoz, hogy a diagram rajzolása üres vászonból megnyitása. Ügyeljen arra, hogy melyik sablont szeretné használni a modell kiválasztása                                                                                                                                                                                                                                                                                                                                                                       |
| **Új modell sablon**                 | Ki kell választania egy modell létrehozása előtt használandó sablont. A fő sablont az Azure fenyegetés folyamatmodell-sablont, amely tartalmazza az Azure-rajzsablonok, fenyegetések és azok mérséklési. Általános esetében válassza ki a legördülő menüből a SDL TM Tudásbázis. Létrehozhat saját sablont, vagy küldje el az összes felhasználó számára egy új? Tekintse meg a  **[sablon tárház](https://github.com/Microsoft/threat-modeling-templates)**  további GitHub-oldalon                              |
| **Nyissa meg a modell**                            | <p>Megnyílik a fenyegetés modellek korábban mentett. A modellek nemrég megnyitott nagyszerű, ha meg kell nyitnia a legutóbbi fájlok. Ha a kijelölt mutat, nyissa meg a modellek 2 módjai találhat:</p><p><ul><li>Nyissa meg a számítógépről – klasszikus mód a helyi tárhelyet használ a fájlok megnyitása</li><li>Nyissa meg a onedrive-ról – csoportok segítségével a onedrive-on mappák mentése és megosztása növelheti a hatékonyságot és együttműködés egyetlen helyen a fenyegetés modellek</li></ul></p> |
| **Első lépések útmutató**                   | Megnyitja a  **[Microsoft fenyegetések modellezése eszköz](./azure-security-threat-modeling-tool.md)**  főoldala                                                                                                                                                                                                                                                                                                                                                                                            |

### <a name="template-section"></a>Sablon szakasz

| Összetevő               | Részletek                                                                                                                                                          |
| ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Új sablon létrehozása** | Megnyílik az Ön létrehozásához, egy üres sablont. Ha nincs teljesen új sablonok létrehozásakor a széles körű Tudásbázis, azt javasoljuk, hogy a meglévőket felépíteni |
| **Nyissa meg sablon**       | Megnyitja a meglévő sablonok meg a szükséges módosításokat                                                                                                              |

A fenyegetések modellezése eszköz team javítása eszköz funkcióit, és megtapasztalhatja folyamatosan működik. Néhány másodlagos változtatások előfordulhat, hogy elvégezze az év folyamán, de minden fontosabb változását igénylő újraírások az útmutatóban. Hivatkozik rá gyakran biztosításához a legújabb közlemények kap.

## <a name="building-a-model"></a>A modell létrehozása

Ez a szakasz azt kövesse:

- Cristina (egy fejlesztő)
- Richárd (Programvezető) és
- Szerzője Ashish (tester)

A folyamatot, az első fenyegetések modellezése fejlődő a rendszer hamarosan.

> Richárd: Nagy Cristina, I munkaidő fenyegetés modell diagram, és győződjön meg arról, hogy azt a kapott a következő részleteit jobb. Is segítséget kérek, tekintse át?
> Cristina: elengedhetetlen. Ismerkedjen meg.
> Richárd az eszköz megnyitása, és a képernyő Cristina megosztja.

![Alapszintű fenyegetések modellezése](./media/azure-security-threat-modeling-tool-feature-overview/basictmt.png)

> Cristina: Ok, egyszerű, keres, de az is, bízná me azt?
> Richárd: meg arról, hogy! A részletes információkat a következő:
> - Az emberi felhasználói megrajzolása egy külső egységként – négyzet
> - Azok parancsok küldjük a webkiszolgálóhoz – a kör
> - A webalkalmazás-kiszolgáló van tanácsadás egy adatbázist (két párhuzamos sorok)

Mi Richárd csak bemutatta Cristina DFD, rövid a  **[adatfolyam-Diagram](https://en.wikipedia.org/wiki/Data_flow_diagram)**. A fenyegetések modellezése eszköz lehetővé teszi, hogy a felhasználók meghatározhatják a bizalmi kapcsolat határain mutatja, hogy hol különféle entitásokat az irányítást a piros pontozott vonalak jelölik. Például a rendszergazdák megkövetelése az Active Directory rendszer hitelesítési célokra, az Active Directory hozzáférésének szabályozására kívül esik.

> Cristina: Nekem jobb keres. Mi a helyzet a fenyegetések?
> Richárd: Én is láthat.

## <a name="analyzing-threats"></a>Fenyegetések elemzése

Amennyiben az elemre kattint a ikon a menüpont kiválasztásának (fájl nagyítóüveg), az alapértelmezett sablon alapján létrehozott fenyegetések a fenyegetések modellezése eszköz található listájára vették, az úgynevezett SDL módszerrel használó a nézet  **[ STRIDE (IP-hamisítás, illetéktelen módosítás, információ felfedése, szolgáltatásmegtagadás és jogok kiterjesztése)](https://en.wikipedia.org/wiki/STRIDE_(security))**. A lényege, hogy a szoftver a fenyegetéseket, amely 6 kategóriákkal is található egy előre jelezhető készletét származik.

Erre akkor van például biztonságossá tétele a ház minden ajtók és biztosításával rendelkezik zárolási mechanizmus az riasztó rendszerek hozzáadása vagy a tolvaj után követésnek előtt.

![Alapszintű fenyegetések](./media/azure-security-threat-modeling-tool-feature-overview/basicthreats.png)

Richárd megkezdi a listán az első elem kiválasztásával. Ez történik:

A két rajzsablonok közötti interakció továbbfejlesztett először

![Interakció](./media/azure-security-threat-modeling-tool-feature-overview/interaction.png)

A fenyegetés második, további információkat a fenyegetés tulajdonságai ablakban jelenik meg

![Interakció adatai](./media/azure-security-threat-modeling-tool-feature-overview/interactioninfo.png)

A generált fenyegetés segít neki a lehetséges tervezési hibái ismertetése. A STRIDE kategorizálási ad neki egy a potenciális támadási, amíg a további leírás amelyből megtudja, pontosan milyen nem megfelelő, a lehetséges módjai a csökkentése, valamint. Szerkeszthető mezők megjegyzéseket fűzni indoklás részleteit, vagy attól függően, hogy a szervezet hiba sáv minősítés módosításához használhat.

Azure-sablonok további részleteket a felhasználók nem csak mi, hanem miként lehet elhárítani leírásokat, példák és hivatkozások hozzá Azure-specifikus dokumentáció megérthetik rendelkezik.

A leírás miatt a fontosnak tartja megakadályozhatja, hogy a felhasználók hamisíthatók, a hitelesítési módszer hozzáadása az első fenyegetést lehessen felfedése. Néhány percig, amíg a Cristina, ugyanis az azok megértettem a végrehajtási hozzáférés-vezérléshez és a szerepkörök fontosságát. Richárd néhány gyors megjegyzések az győződjön meg arról, hogy ezek bevezetett kitöltve.

Richárd került, a fenyegetések az információk felfedése, mivel azt is tudjuk a hozzáférés-vezérlési tervek egyes írásvédett fiókok szükséges naplózási és jelentéskészítés céljából. Ezután már azon, hogy e új fenyegetést legyen, de a megoldást azonos volt, hogy külön jelezve a fenyegetés.
Ezután is-re vonatkozó információk felfedése további műveletek, és a is tudjuk, hogy a biztonsági mentési szalagot kell a titkosítást, egy feladatot az a műveleti csapata volt fog.

Nem alkalmazható a tervezési miatt a meglévő megoldást vagy a biztonsági fenyegetések biztosítja, hogy módosítani lehet a "Nem alkalmazható" az az állapot legördülő. Három lehetősége van: kell vizsgálat – alapértelmezett használt nem indult el – Miután teljesen folyamatban lévő cikkek és Mitigated – nyomon követéséhez.

## <a name="reports--sharing"></a>Jelentések és megosztása

Miután Richárd végighalad Cristina a listát, és hozzáadja a fontos megjegyzések, azok mérséklési/indokok, prioritás és állapotmódosítások, he választja ki Jelentések -> teljes jelentés mentése jelentést, amely számára, hogy halad át munkatársaival töltött jelentést kiírja -> létrehozása Ahhoz, hogy a megfelelő biztonsági munkahelyi van megvalósítva.

![Interakció adatai](./media/azure-security-threat-modeling-tool-feature-overview/report.png)

Ha Richárd kíván inkább megosztani a fájlt, hogy egyszerűen megteheti mentése a OneDrive-fiókja a szervezet által. Amennyiben az általa Igen, ezután másolja a dokumentum hivatkozását, és ossza meg a munkatársaival. 

## <a name="threat-modeling-meetings"></a>Fenyegetés modellezési értekezletek

Ha Richárd a fenyegetések modellezése a onedrive-on, Ashish, a tesztelés használatával munkatársát, underwhelmed volt. Úgy tűnik, például Richárd és Cristina számos fontos esetekben, amelyek könnyen sérülhet kimaradt. A skepticism fenyegetés modellek egészíti ki.

Ebben a forgatókönyvben után Ashish átvette a fenyegetések modellezése őt hívni a két fenyegetés modellezési értekezletek esetén: egy értekezlet folyamat szinkronizálását, és végezze el a diagramok és egy második értekezlet esetében fenyegetések tekintse át és kijelentkezési.

Az első értekezlet Ashish töltött mindenki keresztül az SDL fenyegetés modellezési folyamatban érdekében 10 perc. Ezután fenyegetés modell diagram lekért és részletesen elmagyarázza az elindult. Öt percen belül egy fontos hiányzik egy összetevő azonosították.

Néhány perc múlva újabb Ashish és Richárd került egy bővített leírását a módját a webalkalmazás-kiszolgáló lett létrehozva. Nem volt folytatható értekezlet ideális, de mindenki végül elfogadott, hogy az eltérés korai felderítéséhez lett fog menti ideje a jövőben.

A második értekezlet a team telefonon keresztül a fenyegetések, bizonyos értelemben hozzájuk tárgyalt és bejelentkezett a fenyegetések modellezése. Ezeket a dokumentum beadja a verziókövetési rendszerrel, és folytatni a fejlesztési.

## <a name="thinking-about-assets"></a>Eszközök számbavétele

Bizonyos fenyegetések modellezése rendelkező olvasók tapasztalhatja, hogy még nem megtartásról vonatkozó minden. Megismerte, azt, hogy sok szoftverfejlesztő ismerniük eszközök fogalmát, milyen eszközöket egy támadó el iránt érdeklődik jobb megértése-e a szoftvereket.

Ha fog fenyegetések modellezése egy házat, előfordulhat, hogy elindíthatja a családja, pótolhatatlan fényképeket vagy értékes alkotást számbavétele. Például előfordulhat, hogy megkezdéséhez ki lehet, hogy feltörhessék és az aktuális biztonsági rendszer továbbléphetnek. Vagy előfordulhat, hogy indítsa el a fizikai funkciók, például a készlet vagy az első porch figyelembe véve. Ezek a hasonló eszközök, a támadók vagy a szoftver tervezési számbavétele. Ezek a módszerek bármelyikét működik.

A fenyegetés azt korábban itt bemutatott modellezési megközelítése jóval egyszerűbb, mint mi a Microsoft által elvégzett a múltban. Észleltünk, hogy a szoftver tervezett módszert alkalmaz sok csoportok esetén működik-e. Reméljük, amelyek tartalmazzák a saját.

## <a name="next-steps"></a>További lépések

A kérdéseit, megjegyzéseit és kapcsolatos kérdéseket küldése tmtextsupport@microsoft.com. **[Töltse le](https://aka.ms/tmtpreview)**  a fenyegetés modellezési eszköz a kezdéshez.