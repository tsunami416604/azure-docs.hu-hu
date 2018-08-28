---
title: Az Azure Data Catalog gyakori forgatókönyvei
description: Az Azure Data Catalog, beleértve a regisztrációs és a nagy értékű adatforrásokat, a felderítés engedélyezése az önkiszolgáló üzleti intelligencia, és rögzíteni a meglévő ismeretek szerezhetők az adatforrásokat és a folyamatok gyakori forgatókönyvek áttekintése.
services: data-catalog
author: steelanddata
ms.author: maroche
ms.assetid: 60930d78-d2d4-4d5d-9651-bdda50b0da0e
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 4c3fe6f85ec1ae04127e4a420fe1f85b18318aef
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43053407"
---
# <a name="azure-data-catalog-common-scenarios"></a>Az Azure Data Catalog gyakori forgatókönyvei
Ez a cikk bemutatja a gyakori forgatókönyvek, ahol az Azure Data Catalog kihasználása a meglévő adatforrásokat a szervezet segítségére lehetnek.

## <a name="scenario-1-registration-of-central-data-sources"></a>1. forgatókönyv: Központi adatforrások regisztrálása
Szervezetek gyakran számos nagy értékű adatforrásokkal rendelkeznek. Ezeknek az adatforrásoknak üzleti –, az online tranzakció-feldolgozási (OLTP) rendszerek, a data warehouse-adattárházak és az üzleti intelligencia és elemzés adatbázisokat tartalmazza. Rendszerek és a közöttük, az átfedés száma általában idővel növekszik, üzleti igényei megváltoznak, és keresztül, például a fúziók és felvásárlások haladásával magához az üzlethez.

Szervezet tagjai számára, hogy hol keresse meg az adatok belül ezeknek az adatforrásoknak nehézkes lehet. Például a következők túl gyakori kérdésekre:

* A három HR-rendszerek a vállalaton belül használt, amely érdemes használni az ilyen típusú jelentést létrehozni?
* Hol kell nyissa meg a minősített eladási számok lekérése a pénzügyi év, amely az imént fejeződött be?
* Ki kell tehetek fel, vagy mi az a folyamat kell használni az adatraktár eléréséhez?
* Nem tudom, ha ezek a számok megfelelőek. Akik tehetek fel a insight meg, hogyan lehet az ezeket az adatokat kell használni, mielőtt a csapatom megoszthatják az irányítópultot?

Ezekre és további kérdések az Azure Data Catalog biztosíthat a válaszokat. A központi, értékes, informatikai RÉSZLEG által kezelt adatforrások szervezetek között használt gyakran történik a katalógus feltöltéséhez logikai kiindulási pont. Bármely felhasználó lehet adatforrást regisztrálni, bár meghajtó elfogadása és a rendszer használatát kellene kick-started azokat az adatforrásokat, valószínűleg a legnagyobb felhasználók száma, adjon meg értéket a katalógus segítségével. 

Ha elkezdi az Azure Data Cataloggal, azonosítsa és regisztrálja az adatfelhasználók különböző csapatok által használt fő adatforrásokat is az első lépés a sikerhez.

Ebben a forgatókönyvben is jár, így egyszerűbb átlátni és elérése a nagy értékű adatforrások ellátása megjegyzésekkel lehetőséget. Ebből a törekvésből egyik legfőbb szempontja, hogy hogyan használói kérhetnek az adatforráshoz való hozzáférés információkat tartalmazzák. Az Azure Data Cataloggal adja meg az e-mail-címét a felhasználó vagy csapat felelős adatforrás-hozzáférés szabályozása, valamint hivatkozásokat a meglévő eszközeivel vagy a dokumentációban vagy szabad szöveg, amely azt ismerteti, hogyan kérést. Ez az információ segít a tagok, aki regisztrált adatforrások felderítését, de még akik nem férhet hozzá az adatokhoz egyszerűen kérjen hozzáférést a folyamatokat, és határozza meg az adatforrások tulajdonosai vezérli az engedélyek.

## <a name="scenario-2-self-service-business-intelligence"></a>2. forgatókönyv: Az önkiszolgáló üzleti intelligencia
Bár a hagyományos vállalati üzletiintelligencia megoldásokat továbbra is, amely számos szervezet adatelrendezések felbecsülhetetlen értékű részét, üzleti lépést módosításának által végrehajtott önkiszolgáló BI egyre több fontos. Önkiszolgáló üzleti Intelligencia használatával az információkkal dolgozó szakemberek és adatelemzők hozhat létre saját jelentések, munkafüzetek és az irányítópultok anélkül, hogy az egy központi informatikai csapat vagy folyamatban van, hogy informatikai csapat ütemezés és a rendelkezésre állási korlátozza.

Az önkiszolgáló üzleti Intelligencia funkciók a felhasználók gyakran több forrásból, amelyek közül előfordulhat, hogy rendelkezik korábban nem használt üzleti Intelligencia és elemzés származó adatokat kombinálni. Bár ezek az adatforrások némelyike már ismertek, ez kihívást jelenthet Mi a teendő, keresse meg és kiértékelheti egy adott feladat lehetséges adatforrások felderítését.

Hagyományosan, ez a felderítési folyamat, adott manuális: elemzők a társ hálózati kapcsolataikat segítségével azonosíthatók a másokkal, akik együttműködnek a keresett adatokat. Miután egy adatforrás található, és használja, a folyamat megismétlődik újra minden ezt követő önkiszolgáló BI erőfeszítés, a több felhasználó által végzett felderítés redundáns manuális folyamat.

Az Azure Data Catalogban a szervezet tönkretehetik a ciklus erőfeszítéssel. Miután értesült a hagyományos eszközökkel adatforrást, az elemzőknek regisztrálhatja azt, hogy legyen könnyebben felfedezhetővé más felhasználók által a jövőben. Az elemző nagyobb értéket adhat hozzá a regisztrált adategységek dekorálását bemutató cikkben, bár a jegyzet nem kell regisztrációs egy időben történik. A felhasználók közre is idővel az ütemezések lehetővé fokozatosan értéket ad hozzá az a katalógusban regisztrált adatforrásokat.

A katalógus tartalmának a szerves növekedési egészíti ki természetes előzetes regisztrációja központi adatforrások. Előre az adatokat, sok felhasználó szüksége lesz a katalógus feltöltése egy kezdeti és a felderítési motivator is lehet. Felhasználók regisztrálása és jegyzettel láthatja el további források oly módon, így azokat, és más lesz a szervezet tagjai is lehetnek.

Fontos megjegyezni, hogy bár ebben a forgatókönyvben kifejezetten önkiszolgáló BI összpontosít, az azonos minták és kihívásokat alkalmazni a nagy méretű vállalati BI projektek is. Data Catalog segítségével a szervezet minden erőfeszítés, amely magában foglalja az adatforrás-felderítés manuális folyamat javítja.

## <a name="scenario-3-capturing-tribal-knowledge"></a>3. forgatókönyv: A kollektív tudás összegyűjthető rögzítése
Hogyan, hogy milyen adatokat kell tennie a feladat, és hol találja az adatokat?

Ha, hogy a feladat egy ideig, akkor valószínűleg tudnia kell. A tanulási folyamat fokozatos mintajelentéshez, és idővel ismerkedtünk azokat az adatforrásokat, ahhoz, hogy a napi munkára.

Amikor új alkalmazott csapata csatlakozik, hogyan nem ez a személy, hogy milyen adatokra szükség, a feladat, és hol találja azt?

Zajok, az új személy tartalmaz, akkor ezeket a kérdéseket.

Ez a közösségi folyamatos átvitelét az adatforrás-felderítési folyamat nagy és kis szervezetek részét képezi. Több vezető és tapasztalt csapat tagjai az évek során létrehozott Tudásbázis fel, és az újabb csapat tagjai kérje meg, ha kérdése van, hogy megismerte. A legtöbb alapvető fontosságú információk gyakran csak néhány kulcsfontosságú személy vezetői létezik, és ezen személyek szabadságon, vagy hagyja meg a csapat, amikor romlik a a szervezetben.

Újságíróként parancsmagokéval győződjön meg arról, annak érdekében, hogy a dokumentum tudásukat, megosztja e-mailen keresztül, vagy a Word-dokumentumok csapata SharePoint-webhelyen. Bár lehet, hogy ez a megközelítés értékes, azt mutatja be egy új felderítési hiba: hogyan személyek, hogy milyen dokumentációt létezik, és hol találja azt?

Az Azure Data Catalogban a szervezet rendelkezik egy egyetlen központi helyen tárolja, és a kollektív tudás megosztása, és hogy azok könnyen áttekinthető. A Data Catalog a data-szakértők közvetlenül adategységek ellátása dekorációkkal és létező dokumentumok mutató hivatkozásokat tartalmaznak. Ha a szervezet tagjai a katalógus segítségével adatforrásra, azok találhat nem csak a forrás magát, hanem a meglévő ismeretek csak a szervezet szakértők kapcsolatos.
