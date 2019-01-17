---
title: Első lépések – Microsoft Threat Modeling Tool – Azure |} A Microsoft Docs
description: Ez a működés a Threat Modeling Tool kiemelése részletesebb áttekintése.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: jegeib
ms.openlocfilehash: 6315e6d39a3b68854beb6563d075e3c79ca93a69
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359476"
---
# <a name="getting-started-with-the-threat-modeling-tool"></a>A Threat Modeling Tool – első lépések

A Microsoft Threat Modeling eszköz 2018 jelent meg, a végleges verzió 2018 szeptember egy szabad  **[kattintson-letöltési](https://aka.ms/threatmodelingtool)**. A kézbesítési mechanizmus a változás lehetővé teszi számunkra az küldje le a legújabb javítások és hibajavítások az ügyfelek számára minden alkalommal, amikor megnyitják az eszközt, így könnyebben karbantartásáról és használatáról.
Ez a cikk végigvezeti az első lépései a Microsoft SDL fenyegetés megközelítés modellezés, és bemutatja, hogyan fejleszthet nagyszerű modelljei a biztonsági folyamat egy vázaként eszközének használata.

Ez a cikk a meglévő ismeretek a modellezés megközelítés SDL fenyegetés épül. Tekintse át, a **[Threat Modeling webalkalmazások](https://msdn.microsoft.com/library/ms978516.aspx)** és a egy archivált verzióját **[tárhat fel biztonsági hibára derült megközelítéssel a STRIDE](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy)** Az MSDN-cikkben 2006 közzé.

Gyorsan összegzéséhez megközelítés magában foglalja a diagram létrehozása, fenyegetések azonosítása, csökkentése őket, és egyes kockázatcsökkentéseket ellenőrzése. Íme egy diagram, amely kiemeli a folyamat:

![SDL-folyamat](./media/azure-security-threat-modeling-tool-feature-overview/sdlapproach.png)

## <a name="starting-the-threat-modeling-process"></a>A fenyegetés a modellezési folyamat indítása

Ha elindítja a Threat Modeling Tool, néhány dolgot, láthatja, ahogyan az a képen látható:

![Üres Start lap](./media/azure-security-threat-modeling-tool-feature-overview/tmtstart.png)

### <a name="threat-model-section"></a>Veszélyforrások elleni modell szakasz

| Összetevő                                   | Részletek                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Visszajelzések, a javaslatokat és a problémák gomb** | Megnyílik a **[MSDN-fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=sdlprocess)** összes dolgot SDL keretében. Ez lehetővé teszi, olvassa el, mivel foglalkoznak az egyéb felhasználói, megkerülő megoldások és javaslatok együtt. Ha még mindig nem találja amit keres, e-mailt tmtextsupport@microsoft.com segítséget nyújtanak az ügyfélszolgálati csapatunkhoz a                                                                                                                            |
| **Modell létrehozása**                          | Ekkor megnyílik egy üres vászonból, hogy a diagram megrajzolásához. Győződjön meg arról, hogy melyik sablont szeretné használni a modell                                                                                                                                                                                                                                                                                                                                                                       |
| **A sablon új modell**                 | Ki kell választania, melyik sablont szeretnék használni a modell létrehozása előtt. Fő sablont az Azure veszélyforrások elleni modell-sablon, Azure-ra vonatkozó rajzsablonokhoz, fenyegetések és megoldást tartalmaz. Általános modellek esetében válassza ki a legördülő menüből az SDL TM Tudásbázis. Saját sablon létrehozása, vagy küldje el az összes felhasználó számára egy új? Tekintse meg a **[sablontár](https://github.com/Microsoft/threat-modeling-templates)** további GitHub-oldalon                              |
| **Nyissa meg a modell**                            | <p>Megnyílik a modelljei korábban mentett. A legutóbb megnyitott modellek használata nagyszerű, ha meg kell nyitnia a legutóbbi fájlok. Ha az egérmutatót a kijelölést, nyissa meg a modellek 2 módjai találja:</p><p><ul><li>Nyissa meg a számítógépről – klasszikus módját a helyi tároló használata a fájl megnyitása</li><li>Nyissa meg a OneDrive – csapatok mappákat a onedrive vállalati verzióban használható, mentése és megosztása az összes saját modelljei növelheti a teljesítményt és együttműködés segítségével egy helyen</li></ul></p> |
| **Első lépések útmutató**                   | Megnyílik a **[Microsoft Threat Modeling Tool](./azure-security-threat-modeling-tool.md)** főoldala                                                                                                                                                                                                                                                                                                                                                                                            |

### <a name="template-section"></a>Sablon szakasz

| Összetevő               | Részletek                                                                                                                                                          |
| ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Új sablon létrehozása** | Megnyílik egy üres sablonnal, hogy épülnek. Kivéve, ha az alapoktól sablonok készítésekor alapos ismeretek, azt javasoljuk, hogy hozhat létre meglévő állók közül. |
| **Sablon megnyitása**       | Megnyílik a meglévő hajthat végre a sablonok                                                                                                              |

A Threat Modeling Tool csapata folyamatosan javíthatja az eszköz funkcióinak és tapasztalatok dolgozik. Néhány kisebb módosításokat az év folyamán lehet, hogy kerül sor, de minden fontosabb változását foglalja össze az útmutató a újraírások igényelnek. Tekintse meg, milyen gyakran annak érdekében hogy a legújabb bejelentésekről.

## <a name="building-a-model"></a>Modell létrehozása

Ebben a szakaszban azt kövesse:

- Cristina (fejlesztői)
- Ricardo (egyik programmenedzsere) és a
- Szerzője Ashish (tesztelés)

Amelyben a folyamatot az első fenyegetések modellezése fejlesztéséhez.

> Ricardo: Üdv mindenkinek Cristina, e, amelyeken dolgozott a fenyegetés modellek diagramja, és ezzel jobb fájt a részleteket. Segíthet nekem, tekintse át?
> Cristina: Abszolút. Tekintsük át.
> Ricardo megnyitja az eszközt, és megosztja a képernyő Cristina.

![Alapszintű fenyegetések modellezése](./media/azure-security-threat-modeling-tool-feature-overview/basictmt.png)

> Cristina: Rendben, megkeresi a havidíjba vannak foglalva, de is vezeti végig me azt?
> Ricardo: Róla! A következő táblázat összefoglalja:
> - Az emberi felhasználói megjelenítése egy külső entitásként – négyzet
> - Ezek használt parancsok küldését ezen a webalkalmazás-kiszolgáló – a kör
> - A webalkalmazás-kiszolgáló (két párhuzamos vonalak) adatbázis van tanácsadás

Milyen Ricardo mutatott Cristina egy rövid a DFD  **[adatfolyam-Diagram](https://en.wikipedia.org/wiki/Data_flow_diagram)**. A Threat Modeling Tool lehetővé teszi, hogy a felhasználók számára a bizalmi kapcsolat határain mutatja, hogy hol különböző entitások vezérlőelem a piros pontozott vonal jelzi. Például a rendszergazdák igényelnek az Active Directory rendszer hitelesítés céljára, ezért az Active Directory a hatókörükön kívüli.

> Cristina: Úgy tűnik megfelelő a számomra. Mi a helyzet a fenyegetések?
> Ricardo: Szeretnék, megjelenítése.

## <a name="analyzing-threats"></a>Fenyegetések elemzésével

Miután a nézet az ikon menü kijelölésből (fájl nagyítóüveg), az alapértelmezett sablon alapján létrehozott fenyegetéseket a Threat Modeling Tool található listájára vették, amely használja az SDL néven ismert megközelítés kattint  **[ STRIDE (hamisítást, illetéktelen módosítás, adatok kiadásáról, Letagadhatóság, szolgáltatásmegtagadás és jogok kiterjesztése)](https://en.wikipedia.org/wiki/STRIDE_(security))**. A cél pedig az, hogy a szoftver a fenyegetéseket, amely 6 kategóriákban is található egy előre jelezhető készletét származik.

Ez a módszer van, a ház biztosítása úgy, hogy minden egyes ajtó- és zárolási mechanizmussal rendelkezik helyben riasztórendszerként hozzáadása vagy rekord feloldása után a tolvaj előtt.

![Alapszintű fenyegetések](./media/azure-security-threat-modeling-tool-feature-overview/basicthreats.png)

Ricardo megkezdi a lista első elemének kiválasztásával. Ez történik:

Először a a két rajzsablonokhoz közötti interakció továbbfejlesztett

![Interakció](./media/azure-security-threat-modeling-tool-feature-overview/interaction.png)

A fenyegetés második, további információkat a fenyegetés tulajdonságai ablakban jelenik meg

![Kapcsolati adatai](./media/azure-security-threat-modeling-tool-feature-overview/interactioninfo.png)

A létrehozott fenyegetés segít neki a lehetséges tervezési hibái ismertetése. A STRIDE kategorizálási ad neki egy a potenciális támadási vektorok, bár a további leírás jelzi neki pontosan mi okozza, lehetséges módjai a veszélyt együtt. Szerkeszthető mezők írhat jegyzeteket az indoklás adatokat, vagy módosítsa cége bug sáv függően minősítés használhat.

Az Azure-sablonok leírásában, nem csak mi okozza, hanem hogyan háríthatja el a leírások, példák és a hivatkozásokat hozzá Azure-ra vonatkozó dokumentáció segítségével további részleteket rendelkezik.

A leírás miatt a fontosnak tartja meg, hogy a felhasználók hamisíthatók, hitelesítési módszer hozzáadása az első fenyegetés dolgozni rajta illesztőprogramban. Cristina, megbeszélést be néhány percet, végrehajtási hozzáférés-vezérléshez és a szerepkörök fontosságáról értelmezni. Ricardo kitöltött néhány gyorsjegyzetek, hogy ezek lett megvalósítva.

Ricardo került, a fenyegetések Információfelfedés alatt, mivel ő is felismerte, hogy a hozzáférés-vezérlési tervet a naplózás és jelentéskészítés céljából egyes csak olvasható-fiókok szükséges. Phil testreszabásakor nem tudta, hogy ez legyen az új fenyegetéseket, de kockázatcsökkentő is azonos, így ő külön jelezve a fenyegetés.
Ő is egy kicsit részletesebben kapcsolatos információk felfedése úgy Gondoltuk, és a adatmegfelelőség, a biztonsági mentési szalagot a titkosítás, az üzemeltetési csapat egy feladatot kell is mellett.

Nem alkalmazható a kialakítás miatt a meglévő megoldásokkal kapcsolatban, vagy a biztonsági fenyegetések garantálja, hogy módosítani lehet a "Nem alkalmazható" állapot legördülő listából. Nincsenek három egyéb lehetőségek: Nem indult el – alapértelmezett kijelölést, kell vizsgálat – segítségével nyomon követheti, és csak Mitigated – Miután teljes létrejön a.

## <a name="reports--sharing"></a>Jelentések és megosztása

Ricardo halad végig a listát Cristina, és a fontos megjegyzések, megoldások és a indoklások, prioritás és állapot módosul, he kijelölt jelentések -> -> Mentés jelentést, amely kiírja a neki haladhat végig a munkatársaival a már jól néz kis jelentést teljes jelentés létrehozása annak biztosítása érdekében a megfelelő biztonsági munkahelyi van megvalósítva.

![Kapcsolati adatai](./media/azure-security-threat-modeling-tool-feature-overview/report.png)

Ricardo szeretné a fájlt ossza meg helyette, ha ő egyszerűen ehhez a cége OneDrive-fiók mentése. Után úgy használható, ő a dokumentum hivatkozás másolása, és ossza meg a munkatársaival. 

## <a name="threat-modeling-meetings"></a>Veszélyforrások elleni modellezési értekezletek

Ricardo a fenyegetések modellezése munkatársával, onedrive vállalati verzió, Ashish, a tester használatával küldi el, amikor underwhelmed volt. Ricardo és Cristina kihagyott több fontos sarokban esetekről, melyek könnyen feltörhető, tűnt. Saját skepticism modelljei egészíti ki.

Ebben a forgatókönyvben után Ashish átvette a fenyegetések modellezése, ő nevű két threat modellezési értekezletek esetén: egy értekezlet, a folyamat szinkronizálását, és haladjon végig a diagramok és egy második értekezletet hoz threat tekintse át és a sign-off.

Az első értekezlet Ashish töltött walking mindenki keresztül a modellezési folyamat SDL fenyegetés 10 perc. Phil threat modell diagram lekért és lépései részletesen elmagyarázza. Öt percen belül egy fontos hiányzik összetevő azonosították.

Pár perc alatt később Ashish és Ricardo került egy kiterjesztett tárgyalja, hogyan lett létrehozva a webkiszolgáló. Nem volt a folytatáshoz értekezlet ideális konfigurálási módjának, de mindenki végül elfogadott, hogy idő takaríthat meg őket a későbbiekben folyamatban korai felderítése eltérést volt.

A második konferencián, a csapat a fenyegetések telefont, és azok leküzdési néhány módszer tárgyalt és jóváhagyta a fenyegetések modellezése. Ezeket be van jelölve a dokumentum forrásvezérlőben és folyamatos fejlesztési.

## <a name="thinking-about-assets"></a>Szem előtt tartva eszközök

Olyan olvasók, akik rendelkeznek a fenyegetések modellezése Észreveheti, hogy még nem beszéltünk eszközök egyáltalán. Tudjuk, hogy sok szoftverfejlesztő jobban megérthessék, a szoftverfrissítési objektumok fogalma értik, és milyen eszközöket egy támadó érdekelheti talált.

Ha a fenyegetések modellezése a ház, előfordulhat, hogy lépésként szem előtt tartva a család, pótolhatatlan fényképek és műalkotások értékes. Talán, előfordulhat, hogy első lépésként ki meghiúsulhat és a jelenlegi biztonsági rendszer terhelése. Vagy előfordulhat, hogy indítsa el a fizikai funkciókat, például a készlet vagy az elülső porch figyelembe véve. Ezek a hasonló eszközök, a támadók vagy a szoftver kialakítása előtt tartva. Ezek a módszerek bármelyikét működik.

A fenyegetés modellezés, hogy már itt bemutatott megközelítés lényegesen egyszerűbb, mint amit a Microsoft végzett az elmúlt. Úgy találtuk, hogy a szoftver tervezési megközelítés számos csapatok számára is működik-e. Reméljük, hogy az Öné, amelyek tartalmaznak.

## <a name="next-steps"></a>További lépések

Küldés a kérdéseit, megjegyzéseit, és problémákat tmtextsupport@microsoft.com. **[Töltse le](https://aka.ms/threatmodelingtool)**  a Threat Modeling Tool a kezdéshez.
