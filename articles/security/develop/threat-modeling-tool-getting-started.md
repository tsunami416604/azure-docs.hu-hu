---
title: Első lépések - Microsoft Threat Modeling Tool - Azure | Microsoft dokumentumok
description: Ez egy mélyebb áttekintést kiemelve a fenyegetésmodellezési eszköz működés közben.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: jegeib
ms.openlocfilehash: 1454826095bcced9b20935405c0befd5a1ed1ddd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68728308"
---
# <a name="getting-started-with-the-threat-modeling-tool"></a>A fenyegetésmodellező eszköz első lépései

A Microsoft Threat Modeling Tool 2018 2018 szeptemberében jelent meg GA-ként, ingyenes **[kattintással letölthető .](https://aka.ms/threatmodelingtool)** A szállítási mechanizmus változása lehetővé teszi számunkra, hogy a legújabb fejlesztéseket és hibajavításokat az ügyfelekre tolják minden alkalommal, amikor megnyitják az eszközt, megkönnyítve a karbantartást és a használatot.
Ez a cikk végigvezeti a Microsoft SDL fenyegetésmodellezési megközelítés első lépésekén, és bemutatja, hogyan használhatja az eszközt nagy fenyegetési modellek kifejlesztésére a biztonsági folyamat gerinceként.

Ez a cikk az SDL-fenyegetésmodellezési megközelítés meglévő ismereteire épül. A gyors áttekintést a **[Fenyegetésmodellezési webalkalmazások](https://msdn.microsoft.com/library/ms978516.aspx)** és **[a Biztonsági hibák felfedése című,](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy)** 2006-ban közzétett MSDN-cikk archivált verziójában találhatja meg.

Gyorsan összefoglalva, a megközelítés magában foglalja egy diagram létrehozása, fenyegetések azonosítása, azok enyhítésére és érvényesítése minden további. Az alábbiakban egy diagramot, amely kiemeli ezt a folyamatot:

![SDL-folyamat](./media/threat-modeling-tool-getting-started/sdlapproach.png)

## <a name="starting-the-threat-modeling-process"></a>A fenyegetésmodellezési folyamat indítása

Amikor elindítja a Fenyegetésmodellező eszközt, észre fog venni néhány dolgot, ahogy az a képen látható:

![Üres kezdőlap](./media/threat-modeling-tool-feature-overview/tmtstart.png)

### <a name="threat-model-section"></a>Fenyegetésmodell szakasz

| Összetevő                                   | Részletek                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Visszajelzés, javaslatok és problémák gomb** | Elviszi az **[MSDN Fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=sdlprocess)** mindent SDL. Ez lehetőséget ad arra, hogy elolvassa, mit csinálnak más felhasználók, valamint a megoldások és javaslatok. Ha továbbra sem találja, amit keres, küldjön e-mailt tmtextsupport@microsoft.com támogatási csapatunknak, hogy segítsen                                                                                                                            |
| **Modell létrehozása**                          | Megnyit egy üres vásznat a diagram rajzolásához. Győződjön meg arról, hogy melyik sablont szeretné használni a modellhez                                                                                                                                                                                                                                                                                                                                                                       |
| **Sablon új modellekhez**                 | A modell létrehozása előtt ki kell választania, hogy melyik sablont szeretné használni. Fő sablonunk az Azure Threat Model Template, amely Azure-specifikus rajzsablonokat, fenyegetéseket és kockázatcsökkentéseket tartalmaz. Általános modellek esetén válassza ki az SDL TM Tudásbázist a legördülő menüből. Saját sablont szeretne létrehozni, vagy újat szeretne küldeni minden felhasználó számára? További információért tekintse meg **[a Sablontár](https://github.com/Microsoft/threat-modeling-templates)** GitHub-oldalát                              |
| **Modell megnyitása**                            | <p>Korábban mentett fenyegetésmodellek megnyitása. A Legutóbb megnyitott modellek funkció nagyszerű, ha meg kell nyitnia a legújabb fájlokat. Ha az egérmutatót a kijelölés fölé viszi, két módszert talál a modellek megnyitására:</p><p><ul><li>Megnyitás erről a számítógépről – a fájl helyi tárolóval történő megnyitásának klasszikus módja</li><li>Megnyitás a OneDrive-ról – a csapatok a OneDrive-on lévő mappák segítségével egyetlen helyen menthetik és oszthatják meg fenyegetési modelljeiket a termelékenység és az együttműködés növelése érdekében</li></ul></p> |
| **Útmutató az első lépésekhez**                   | A **[Microsoft Threat Modeling Tool főlapjának](threat-modeling-tool.md)** megnyitása                                                                                                                                                                                                                                                                                                                                                                                            |

### <a name="template-section"></a>Sablon szakasz

| Összetevő               | Részletek                                                                                                                                                          |
| ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Új sablon létrehozása** | Megnyit egy üres sablont, amelyre építhet. Hacsak nem rendelkezik széles körű ismeretekkel a sablonok építésében a semmiből, javasoljuk, hogy építsen a meglévőkből |
| **Sablon megnyitása**       | Meglévő sablonok megnyitása a módosításokhoz                                                                                                              |

A Fenyegetésmodellező Eszköz csapata folyamatosan dolgozik az eszköz funkcionalitásának és élményének javításán. Néhány kisebb változtatás történhet az év során, de minden jelentős ebbel át kell írni az útmutatóban. Gyakran olvassa el, hogy biztosan megkapja a legújabb bejelentéseket.

## <a name="building-a-model"></a>Modell létrehozása

Ebben a részben követjük:

- Cristina (fejlesztő)
- Ricardo (programmenedzser) és
- Ashish (tesztelő)

Az első fenyegetési modell jük kifejlesztésének folyamatán mennek keresztül.

> Ricardo: Szia Cristina, dolgoztam a fenyegetés modell diagram, és azt akarta, hogy megbizonyosodjon arról, megvan a részleteket jobb. Segítenél átnézni?
> Cristina: Abszolút. Vizsgáljuk ezt meg.
> Ricardo megnyitja az eszközt, és megosztja a képernyőjét Cristinával.

![Alapvető fenyegetési modell](./media/threat-modeling-tool-feature-overview/basictmt.png)

> Cristina: Ok, úgy néz ki, egyszerű, de nem tud elvezeti nekem rajta?
> Ricardo: Persze! Itt van a bontás:
> - Az emberi felhasználónk külső entitásként rajzolódik ki – egy négyzet
> - Parancsokat küldenek a webszerverünkre – a körre
> - A webkiszolgáló egy adatbázisban (két párhuzamos vonal) konzultál

Amit Ricardo most mutatott Cristina egy DFD, rövid **[adatáramlás diagram](https://en.wikipedia.org/wiki/Data_flow_diagram)**. A fenyegetésmodellezési eszköz lehetővé teszi a felhasználók számára, hogy a piros pontozott vonalak által jelzett megbízhatósági határokat adjanak meg, hogy megmutassák, hol vannak a különböző entitások. A rendszergazdák nak például Active Directory rendszert kell kérniük hitelesítési célokra, így az Active Directory kívül esik a hatáskörükön.

> Cristina: Úgy néz ki, jobb nekem. Mi van a fenyegetésekkel?
> Hadd mutassam meg.

## <a name="analyzing-threats"></a>Fenyegetések elemzése

Amint rákattint az elemző nézetre az ikon menükiválasztásából (fájl nagyítóval), akkor az alapértelmezett sablon alapján talált fenyegetésmodellező eszköz által az alapértelmezett sablon alapján talált fenyegetések listájára kerül, amely a **[STRIDE (Spoofing, Tampering, Info Disclosure, Repudiation, Denial of Service and Elevation of Privilege)](https://en.wikipedia.org/wiki/STRIDE_(security))** nevű SDL megközelítést használja. Az elképzelés az, hogy a szoftver jön egy kiszámítható sor fenyegetések, amely megtalálható ezekkel a 6 kategóriában.

Ez a megközelítés olyan, mint biztosítva a ház biztosításával minden ajtó és ablak egy zár mechanizmus a helyén hozzáadása előtt riasztórendszer vagy üldözi a tolvajt.

![Alapvető fenyegetések](./media/threat-modeling-tool-getting-started/basicthreats.png)

Ricardo a lista első elemének kiválasztásával kezdődik. A következők történnek:

Először is, a két rajzsablon közötti kölcsönhatás

![Interakció](./media/threat-modeling-tool-getting-started/interaction.png)

Másodszor, a fenyegetéssel kapcsolatos további információk a Fenyegetés tulajdonságai ablakban jelennek meg

![Interakciós adatok](./media/threat-modeling-tool-getting-started/interactioninfo.png)

A generált fenyegetés segít neki megérteni a lehetséges tervezési hibákat. A STRIDE kategorizálás ad neki egy ötletet a potenciális támadási vektorok, míg a kiegészítő leírás megmondja neki, hogy pontosan mi a baj, valamint a lehetséges módon enyhíteni azt. A szerkeszthető mezők segítségével jegyzeteket írhat az indoklás részleteibe, vagy módosíthatja a prioritási minősítéseket a szervezet hibasávjának függően.

Az Azure-sablonok további részleteket tartalmaznak, amelyek segítségével a felhasználók nem csak azt érthetik meg, hogy mi a baj, hanem azt is, hogyan javíthatja ki leírások, példák és hivatkozások hozzáadásával az Azure-specifikus dokumentációt.

A leírás ráébresztette egy hitelesítési mechanizmus hozzáadásának fontosságára, hogy megakadályozzák a felhasználók meghamisítását, felfedve az első fenyegetést, amelyen dolgozni kell. Néhány perccel a Cristinával folytatott beszélgetés után megértették a hozzáférés-vezérlés és a szerepek megvalósításának fontosságát. Ricardo kitöltött néhány gyors jegyzetet, hogy megbizonyosodjon arról, hogy ezeket végrehajtották.

Ahogy Ricardo bement a fenyegetések alatt Information Disclosure, rájött, hogy a hozzáférés-vezérlési terv szükséges néhány csak olvasható számlák audit és a jelentés létrehozását. Azon tűnődött, vajon ez új fenyegetést jelent-e, de a mérséklések azonosak voltak, ezért ennek megfelelően megjegyezte a fenyegetést.
Azt is gondolta, információ nyilvánosságra hozatala egy kicsit, és rájött, hogy a biztonsági szalagok lesz szükség titkosítás, a munkát a műveleti csapat.

A meglévő megoldások vagy biztonsági garanciák miatt a tervre nem alkalmazható fenyegetések az Állapot legördülő menüből "Nem alkalmazható"-ra módosíthatók. Három további lehetőség közül választhat: Nem kezdődött el – alapértelmezett kiválasztás, Needs Investigation – az elemek nyomon követésére és a csökkentelésre szolgál – ha már teljesen megdolgozott.

## <a name="reports--sharing"></a>Jelentések megosztás &

Miután Ricardo végigmegy a listán Cristina és hozzáteszi, fontos jegyzetek, mérséklések / indoklások, prioritás és állapot változások, kiválasztja jelentések -> Create Full Report -> Save Report, amely kinyomtatja egy szép jelentést neki, hogy menjen át a kollégákkal, hogy biztosítsák a megfelelő biztonsági munka végrehajtása.

![Interakciós adatok](./media/threat-modeling-tool-feature-overview/report.png)

Ha Ricardo inkább meg szeretné osztani a fájlt, ezt egyszerűen megteheti, ha a szervezet OneDrive-fiókjába menti. Miután ezt megteszi, lemásolhatja a dokumentum linkjét, és megoszthatja a kollégáival. 

## <a name="threat-modeling-meetings"></a>Fenyegetésmodellezési értekezletek

Amikor Ricardo elküldte a fenyegetési modelljét a onedrive-os kollégájának, Ashish, a tesztelő, alulmaradt. Úgy tűnt, Ricardo és Cristina kihagyott néhány fontos ügyet, ami könnyen veszélybe kerülhet. A szkepticizmusa kiegészíti a fenyegetési modelleket.

Ebben a forgatókönyvben, miután Ashish átvette a fenyegetés modell, két fenyegetés modellezési értekezleteket kért: egy értekezletet a folyamat szinkronizálásához és a diagramok átjárásához, majd egy második értekezletet a fenyegetés felülvizsgálatához és a bejelentkezéshez.

Az első találkozón Ashish 10 percet töltött azzal, hogy mindenkit végigsétáltaz SDL fenyegetésmodellezési folyamatán. Ezután elővette a fenyegetésmodell diagramját, és elkezdte részletesen elmagyarázni. Öt percen belül azonosítottak egy fontos hiányzó alkatrészt.

Néhány perccel később Ashish és Ricardo egy hosszabb beszélgetésbe keveredett a rról, hogyan épült a webkiszolgáló. Nem ez volt az ideális módja annak, hogy egy találkozó folytatódjon, de végül mindenki egyetértett abban, hogy az eltérés korai felfedezése időt takarít meg nekik a jövőben.

A második találkozón a csapat végigment a fenyegetéseken, megvitatta a megoldásuk néhány módját, és aláírta a fenyegetésmodellt. Ellenőrizték a dokumentumot a forrásellenőrzésbe, és folytatták a fejlesztést.

## <a name="thinking-about-assets"></a>Gondolkodás eszközök

Egyes olvasók, akik fenyegetés mintájára is észre, hogy mi nem beszéltünk eszközök egyáltalán. Felfedeztük, hogy sok szoftvermérnök jobban érti a szoftverét, mint az eszközök fogalmát, és hogy a támadó milyen eszközök érdeklik őket.

Ha meg akarsz fenyegető modell egy házat, akkor lehet kezdeni gondol a család, pótolhatatlan fotók vagy értékes műalkotások. Talán kezdhetné azzal, hogy azon gondolkodik, ki törhet be, és ki léphet be a jelenlegi biztonsági rendszerbe. Vagy lehet kezdeni figyelembe véve a fizikai jellemzők, mint a medence vagy a tornácon. Ezek hasonlóak az eszközökre, a támadókra vagy a szoftvertervezésre való gondolkodáshoz. A három megközelítés bármelyike működik.

Az itt bemutatott fenyegetésmodellezés megközelítése lényegesen egyszerűbb, mint amit a Microsoft a múltban tett. Azt találtuk, hogy a szoftver tervezési megközelítés jól működik sok csapat. Reméljük, hogy a tiéd is.

## <a name="next-steps"></a>Következő lépések

Küldje el kérdéseit, észrevételeit tmtextsupport@microsoft.comés aggályait a alkalmazásnak. **[A](https://aka.ms/threatmodelingtool)** kezdéshez töltse le a Fenyegetésmodellezési eszközt.
