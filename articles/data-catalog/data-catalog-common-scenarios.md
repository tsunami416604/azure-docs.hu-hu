---
title: "Az Azure Data Catalog gyakori forgatókönyvei |} Microsoft Docs"
description: "Az Azure Data Catalog, beleértve a regisztráció és a nagy értékű adatforrásokat, felderítésének engedélyezése az önkiszolgáló üzleti intelligencia, és adatforrások és a folyamatok meglévő ismerete rögzítése gyakori forgatókönyvek áttekintése."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 60930d78-d2d4-4d5d-9651-bdda50b0da0e
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 08/15/2017
ms.author: maroche
ms.openlocfilehash: e01e6fa3b9871541bf9573963ced05848a3726e3
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="azure-data-catalog-common-scenarios"></a>Az Azure Data Catalog gyakori forgatókönyvei
Ez a cikk bemutatja a gyakori forgatókönyvek, ahol a Azure Data Catalog segíthet a szervezet több érték lekérése a meglévő adatforrás.

## <a name="scenario-1-registration-of-central-data-sources"></a>1. forgatókönyv: Központi adatforrások regisztrálása
A szervezetek gyakran sok nagy értékű adatforrásokkal rendelkeznek. Ezeknek az adatforrásoknak üzleti, az online tranzakció-feldolgozási (OLTP) rendszerek, az adatraktárak és üzleti intelligencia/analytics adatbázisokat tartalmazza. A rendszerek, és a kettő között átfedés száma általában érő időbeli üzleti igények fejlődnek, és az üzleti maga fejlődésének keresztül, például a fúziók és felvásárlások.

A szervezet tagok tudja, hol keresse meg az adatokat az adatforrások belül nehézkes lehet. A következők túl gyakori kérdések a következőhöz hasonló:

* A vállalaton belül alkalmazott három HR rendszerek, amelyek érdemes használni az ilyen típusú jelentés létrehozásához?
* Hol kell lépjen a hitelesített értékesítési számok lekérése a pénzügyi év, amely éppen véget ért?
* Ki kell kéri, vagy mi az a folyamat kell használni az adatraktár eléréséhez?
* Nem tudom, ha ezeket a számokat megfelelőek. Ki is kéri betekintés a a hogyan kellene ezeket az adatokat az irányítópult megosztása a csapattal előtt fel kell használni?

Ezekre és további kérdésekre az Azure Data Catalog biztosíthatnak válaszokat. A központi, nagy értékű, informatikai RÉSZLEG által felügyelt adatforrások is használ, a szervezetek általában a logikai kiindulási pontot biztosítanak a katalógus feltöltéséhez. Bár minden olyan felhasználó lehet adatforrást regisztrálni, a legnagyobb valószínűség értéket adhat a felhasználók a lehető legtöbb adatforrásokkal rendelkező kick-started katalógus rendelkező segít elfogadtatásához és használja a rendszer. 

Ha elkezdi az Azure Data Catalog, azonosítsa és regisztrálja az adatfelhasználók a különböző csapatok által használt fő adatforrások lehet sikeres első lépésként.

Ebben a forgatókönyvben is felvet a könnyebb megértéséhez, valamint hozzáférés a nagy értékű adatforrások ellátása megjegyzésekkel lehetőséget. Egyik legfőbb szempontja, hogy ebből a törekvésből azzal az információval a módját a felhasználó kérhet az adatforráshoz való hozzáférés. Az Azure Data Catalog adja meg a felhasználó vagy csoport, amely az adatforrás hozzáférés szabályozását, valamint hivatkozásokat a meglévő eszközök vagy a dokumentációt, vagy szabad leíró szöveg, amelyet a hozzáférés-lekérő folyamat e-mail címet. Ezen információk alapján a tagok, aki regisztrált adatforrások felderítését, de ki még nincs engedélye könnyen kérjen hozzáférést a folyamatok, amelyek a definiált és az adatforrás tulajdonosok által vezérelt használatával adatok eléréséhez.

## <a name="scenario-2-self-service-business-intelligence"></a>2. forgatókönyv: Az önkiszolgáló üzleti intelligencia
Bár a hagyományos vállalati üzletiintelligencia megoldások továbbra is a számos szervezet adatok tájak hasznos információt részét képezik, a módosítását lépést a vállalat által végrehajtott önkiszolgáló üzleti Intelligencia, több fontos. Az önkiszolgáló üzleti Intelligencia használatával információval dolgozó munkatársak és elemzők hozhat létre a saját jelentéseket, munkafüzeteket és irányítópultok anélkül, hogy az egy központi IT-csoport, vagy éppen maximális száma korlátozza, hogy informatikai csapat ütemezés és a rendelkezésre állási.

Önkiszolgáló üzleti Intelligencia forgatókönyvekben a felhasználók általában sok előfordulhat, hogy nem rendelkezik korábban használt BI és elemzési több forrásból származó adatok össze. Bár az adatforrások egy részénél már ismertek, azt komoly kihívást jelenthet, mi a teendő, keresse meg és értékelje ki egy adott tevékenység lehetséges adatforrások felderítésére.

Hagyományosan, ez a felderítési folyamat, adott kézi: elemzők a társ hálózati kapcsolatok segítségével azonosíthatók a más felhasználókkal a keresett adatok. Miután egy adatforrás található, és használja, a folyamat megismétlődik újra minden ezt követő önkiszolgáló BI annak érdekében, a felderítés redundáns kézi folyamat végrehajtása több felhasználóval rendelkező.

A szervezet az Azure Data Catalog, ez a ciklus erőfeszítéssel érvénytelenné. Miután értesült a hagyományos módon adatforrás, valamelyik elemzőnek regisztrálhatja azt, hogy legyen könnyebben felderíthetők más felhasználók által a jövőben. Bár az elemző által a regisztrált adategységeket ellátása megjegyzésekkel adhat hozzá további értéket, a jegyzet nem kell a regisztrációs azonos időpontban kerül sor. Felhasználók fokozatosan érték hozzáadása a katalógusban regisztrált adatforrások adott idő alatt, az ütemezések engedély járulhat.

Ez a katalógus tartalmának szerves növekedési természetes egészíti ki a kezdeti regisztráció központi adatforrások. A katalógus-adatokkal, amelyeket számos felhasználó kell keresési lehet egy kezdeti és felderítési motivator. Így a felhasználók regisztrálásával és megjegyzésekkel további források lehet őket, és más részt vevő szervezet tagjaival.

Érdemes megjegyezni, hogy bár a forgatókönyvnek a témája a kifejezetten az önkiszolgáló üzleti Intelligencia, azonos mintákat és kihívást érvényesek nagyméretű vállalati Üzletiintelligencia-projektek is. A Data Catalog használatával a szervezet javíthatja bármely erőfeszítés, amely magában foglalja az adatforrás felderítés manuális folyamat.

## <a name="scenario-3-capturing-tribal-knowledge"></a>3. forgatókönyv: Kollektív ismereteit rögzítése
Hogyan tudja, milyen adatokat kell tennie a feladatot, és hol található adatok?

Ha egy ideje már a feladat, akkor valószínűleg csak tudja. De folyamat tanulási fokozatos keresztül, és idővel megtanulhatta az adatforrásokról, amelyek a mindennapos munka alapvető.

Ha új alkalmazott csatlakozik a csapat, hogyan nem adott személyt, hogy mely adatokra szükség a feladathoz, és hol találhatók az?

Valószínűleg, az új személy rendelkezik Önnek ezeket a kérdéseket.

A folyamatban lévő átruházása kollektív ismereteit az adatforrás felderítési folyamat a szervezet kis és nagy részét képezi. Több vezető és tapasztalt csoport tagjai létrehozott Tudásbázis az évek során, és újabb csapattagok megtanulhatta, kérje meg őket, ha kérdése. A legtöbb fontos adatokat gyakran csak néhány kulcsfontosságú személy vezetői létezik, és személyek szabadságon van, vagy hagyja meg a csoport, amikor a szervezet romlik.

Adatok szakértők szokásos, hogy a Tudásbázis dokumentum megosztja e-mailben, vagy a Word-dokumentumok csapat SharePoint-webhelyen. Bár lehet, hogy ez a megközelítés értékes, egy új felderítési problémát okozna: hogyan személyek, hogy milyen dokumentáció létezik, és hol találhatók az?

Az Azure Data Catalog a szervezet rendelkezik egy egyetlen központi helyen tárolja, és a kollektív ismereteit megoszthatók, és hogy azok könnyen felfedezhetővé. A Data Catalog az adatok szakértők adhat megjegyzéseket az adategységekhez közvetlenül, és adja meg a meglévő dokumentációjára mutató hivatkozásokat. Ha a szervezet tagjai a katalógus használatáról az adatforrás felderítéséhez, azok találhat nem csak a forrás magát, hanem a Tudásbázis korábban már létező csak a kapcsolatos a szervezet szakértői.
