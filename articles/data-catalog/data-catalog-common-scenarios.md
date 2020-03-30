---
title: Az Azure Data Catalog gyakori forgatókönyvei
description: Az Azure Data Catalog gyakori forgatókönyveinek áttekintése, beleértve a nagy értékű adatforrások regisztrálását és felderítését, az önkiszolgáló üzleti intelligencia engedélyezését, valamint az adatforrásokkal és folyamatokkal kapcsolatos meglévő ismeretek rögzítését.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: aeae505b510f563a6640726c384ea358983eb24f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68736466"
---
# <a name="azure-data-catalog-common-scenarios"></a>Az Azure Data Catalog gyakori forgatókönyvei
Ez a cikk bemutatja a gyakori forgatókönyveket, ahol az Azure Data Catalog segítségével a szervezet több értéket a meglévő adatforrások.

## <a name="scenario-1-registration-of-central-data-sources"></a>1. forgatókönyv: Központi adatforrások regisztrálása
A szervezetek gyakran sok nagy értékű adatforrással rendelkeznek. Ezek az adatforrások közé tartoznak az üzletági, az online tranzakció-feldolgozó (OLTP) rendszerek, az adatraktárak és az üzleti intelligencia/elemzési adatbázisok. A rendszerek száma és a köztük lévő átfedések általában idővel nőnek, ahogy az üzleti igények fejlődnek, és maga az üzlet is fejlődik, például az egyesülések és felvásárlások révén.

A szervezet tagjai nehezen tudják, hogy hol találják meg az adatokat ezekben az adatforrásokban. Az alábbihoz hasonló kérdések túl gyakoriak:

* A vállalaton belül használt három HR-rendszer közül melyiket használjam az ilyen típusú jelentések létrehozásához?
* Hova menjek, hogy megkapjam a most véget ért pénzügyi év hitelesített értékesítési számait?
* Kit kérdezzek meg, vagy milyen folyamatot kell használnom az adattárházhoz való hozzáféréshez?
* Nem tudom, hogy ezek a számok helyesek-e. Kit kérhetek betekintést arról, hogyan kell ezeket az adatokat felhasználni, mielőtt megosztom ezt az irányítópultot a csapatommal?

Ezekre és más kérdésekre az Azure Data Catalog adhat választ. A központi, nagy értékű, informatikai felügyelt adatforrások, amelyek a szervezetek között használt gyakran a logikai kiindulási pont a katalógus feltöltése. Bár bármely felhasználó regisztrálhat egy adatforrást, a katalógus indítása azokkal az adatforrásokkal, amelyek a legnagyobb valószínűséggel a dedikálódnak a legtöbb felhasználó számára, segít a rendszer bevezetésében és használatában. 

Ha első lépésekben használja az Azure Data Catalog szolgáltatást, az adatfogyasztók számos különböző csapata által használt kulcsfontosságú adatforrások azonosítása és regisztrálása az első lépés lehet a sikerhez.

Ez a forgatókönyv lehetőséget nyújt a nagy értékű adatforrások jegyzetelésére is, hogy azok könnyebben érthetőek és hozzáférhetők legyenek. Ennek az erőfeszítésnek az egyik fő szempontja, hogy információkat tartalmazzon arról, hogy a felhasználók hogyan kérhetnek hozzáférést az adatforráshoz. Az Azure Data Catalog segítségével megadhatja az adatforrás-hozzáférés szabályozásáért felelős felhasználó vagy csoport e-mail címét, a meglévő eszközökre vagy dokumentációra mutató hivatkozásokat, vagy a hozzáférési kérelem folyamatát leíró szabad szöveget. Ez az információ segít azoknak a tagoknak, akik észlelik a regisztrált adatforrásokat, de akik még nem rendelkeznek az adatokhoz való hozzáféréshez szükséges engedélyekkel, hogy az adatforrás-tulajdonosok által meghatározott és ellenőrzött folyamatok használatával könnyen hozzáférést kérjenek.

## <a name="scenario-2-self-service-business-intelligence"></a>2. forgatókönyv: Önkiszolgáló üzleti intelligencia
Bár a hagyományos vállalati üzleti intelligencia megoldások továbbra is felbecsülhetetlen részét képezik számos szervezet adatkörnyezetének, az üzleti tevékenység változó üteme egyre fontosabbá tette az önkiszolgáló BI-t. Az önkiszolgáló üzletiintelligencia-szolgáltatás használatával az informatikai dolgozók és elemzők létrehozhatják saját jelentéseiket, munkafüzeteiket és irányítópultjaikat anélkül, hogy központi informatikai csapatra támaszkodnának, vagy az informatikai csapat ütemezése és elérhetősége korlátoznák őket.

Önkiszolgáló üzletiintelligencia-esetekben a felhasználók általában több forrásból származó adatokat kombinálnak, amelyek közül sok korábban nem használt üzletiintelligencia-hez és elemzéshez. Bár ezen adatforrások némelyike már ismert lehet, kihívást jelenthet annak felderítése, hogy mi a teendő egy adott feladat lehetséges adatforrásainak megkereséséhez és kiértékeléséhez.

Hagyományosan ez a felderítési folyamat manuális: az elemzők a társhálózati kapcsolataik segítségével azonosítják azokat, akik a keresett adatokkal dolgoznak. Az adatforrás okának feltalálása és használatba való tévő része után a folyamat ismét megismétli önmagát minden további önkiszolgáló üzletiintelligencia-erőfeszítésnél, és több felhasználó hajt végre egy redundáns manuális felderítési folyamatot.

Az Azure Data Catalog segítségével a szervezet megszakíthatja ezt a ciklust. Miután hagyományos eszközökkel felfedezte az adatforrást, az elemző regisztrálhatja azt, hogy a jövőben más felhasználók könnyebben felfedezhetők legyenek. Bár az elemző a regisztrált adateszközök jegyzetelésével több értéket adhat hozzá, ennek a jegyzetnek nem kell a regisztrációval egy időben lezajlania. A felhasználók idővel hozzájárulhatnak, ahogy az ütemezésük lehetővé teszi, fokozatosan növelve a katalógusban regisztrált adatforrások értékét.

A katalógustartalom organikus növekedése természetes kiegészítője a központi adatforrások előzetes regisztrációjának. A katalógus olyan adatokkal való előzetes feltöltése, amelyeksok felhasználónak szükségük lesz, motiváló erő lehet a kezdeti használatra és felderítésre. Ha lehetővé teszi a felhasználók számára a további források regisztrálását és megjegyzéssel való fogadását, így megtarthatja őket és a szervezet többi tagját.

Érdemes megjegyezni, hogy bár ez a forgatókönyv kifejezetten az önkiszolgáló üzleti intelligencia, ugyanazok a minták és kihívások vonatkoznak a nagy vállalati BI projektek is. A Data Catalog használatával a szervezet javíthatja az adatforrások felderítésének manuális folyamatát.

## <a name="scenario-3-capturing-tribal-knowledge"></a>3. forgatókönyv: Törzsi tudás rögzítése
Honnan tudja, hogy milyen adatokra van szüksége a munkájához, és hol találja ezeket az adatokat?

Ha már egy ideje a munkádban vagy, valószínűleg csak tudod. Egy fokozatos tanulási folyamaton ment keresztül, és idővel megismerkedett a mindennapi munka kulcsaként fontos adatforrásokkal.

Amikor egy új alkalmazott csatlakozik a csapathoz, honnan tudja az adott személy, hogy milyen adatokra van szükség a feladathoz, és hol találja meg?

Esélyes, hogy az új személy ezekkel a kérdésekkel jön hozzád.

A törzsi tudás folyamatos átadása része az adatforrás-felderítési folyamatnak a kis és nagy szervezetekben. Az évek során több idősebb és tapasztalt csapattag szerzett tudást, és az újabb csapattagok megtanulták feltenni őket, amikor kérdéseik vannak. A legfontosabb információ gyakran csak néhány kulcsember fejében létezik, és amikor ezek az emberek nyaralnak, vagy elhagyják a csapatot, a szervezet szenved.

Az adatszakértők általában arra törekszenek, hogy dokumentálják tudásukat, és megosszák azokat e-mailben vagy Word-dokumentumokban egy csoport SharePoint-webhelyen. Bár ez a megközelítés értékes lehet, egy új felfedezési problémát vezet be: honnan tudják az emberek, hogy milyen dokumentáció létezik, és hol találják meg?

Az Azure Data Catalog segítségével a szervezet egyetlen, központi helyet rendelkezik a törzsi ismeretek tárolásához és megosztásához, valamint a könnyen felderíthetővé tételéhez. A Data Catalogban az adatszakértők közvetlenül jegyzetelhetnek az adatelemekről, és hivatkozásokat adhatnak a meglévő dokumentációhoz. Amikor a szervezet tagjai a katalógussegítségével észlelnek egy adatforrást, nem csak magát a forrást találják meg, hanem azt a tudást is, amely korábban csak a szervezet szakértőinek fejében létezett.
