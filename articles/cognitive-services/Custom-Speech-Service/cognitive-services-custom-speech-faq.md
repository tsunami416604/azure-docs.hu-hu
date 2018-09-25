---
title: Kapcsolatos gyakori kérdések a Custom Speech Service az Azure-ban |} A Microsoft Docs
description: Az alábbiakban a Custom Speech Service legnépszerűbb kérdésekre adott válaszok.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 11/21/2016
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: 5ab4adefbfbfa1a295f980fd31bffaeaa957ce0c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46955282"
---
# <a name="custom-speech-service-frequently-asked-questions"></a>Custom Speech Service – gyakori kérdések

Ha nem találja a kérdésekre adott válaszok a gyakori kérdések, próbálja meg a Custom Speech Service Közösség kéri a [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) és [uservoice-on](https://cognitive.uservoice.com/)

## <a name="general"></a>Általános kérdések

**Kérdés**: Honnan tudom, ha az adatkészlet vagy a modell feldolgozása befejeződött?

**Válasz**: a modell vagy a tábla adatkészlet állapota jelenleg az egyetlen szeretné tudni, hogy.
A feldolgozás befejeződése után a állapota "Kész" lesz.
Továbbfejlesztett állapotának, például az e-mail-értesítés feldolgozása kommunikációs módszerek is dolgozunk.

**Kérdés**: hozhatok létre egynél több modell egyszerre?

**Válasz**: nem korlátozott, hogy hány modellek a gyűjteményben lévő, de minden oldalon egyszerre csak egy lehet létrehozni.
A nyelvi modell-létrehozási folyamata például, ha jelenleg a nyelvi modell a folyamat állapotban nem lehet elindítani.
Az akusztikai modell és a egy nyelvi modell feldolgozása egyszerre azonban is rendelkezik. 

**Kérdés**: vettem hiba. Hogyan megszakítja a saját adatok importálása vagy folyamatban lévő létrehozását modellkezelési? 

**Válasz**: jelenleg nem vonható vissza egy akusztikai és nyelvi betanítás folyamat.
Importált adatok az importálás befejezése után lehet törölni

**Kérdés**: Mi a különbség a Keresés & Diktálás modelleket és a természetes nyelvi modellek között?

**Válasz**: nincsenek két alap akusztikai és nyelvi modelleket a Custom Speech Service közül választhat.
keresési lekérdezések vagy Diktálás. A Microsoft Conversational AM akkor megfelelő, a beszéd beszélt természetes nyelvi stílus felismerve.
Az ilyen típusú speech általában irányul másik személynek, például a telefonos ügyfélszolgálatok vagy értekezletek.

**Kérdés**: is frissíthetők a saját meglévő model (modell rétegezést)?

**Válasz**: nem biztosítunk lehetővé teszi egy meglévő modell új adatokkal frissülnek.
Ha egy új adatkészletet és egy meglévő modell testreszabásához kell újra alakítani az új adatokat és a régi adatkészletet is használt.
A régi és új adatkészletek kell kombinálhatók egyetlen .zip (ha akusztikai adathoz) vagy egy .txt fájl nyelvi adatok egyszer betanítás esetén történik az új frissített modell kell lennie a megszüntetéséhez telepített új végpont beszerzése

**Kérdés**: Mi történik, ha van szükségem az alapértelmezett értéknél magasabb szintű egyidejűség érdekében. 

**Válasz**: a modell 5 egyidejű kérés, amelyet a skálázási egység nevezzük egységnyi növekményekben vertikálisan felskálázhatja. Minden egyes méretezési egység garantálja, hogy a modell képes egyszerre feldolgozni 5 audio-adatfolyam. 100 skálázási egységeket (vagy 500 egyidejű kérelmek) vásárolhatók meg.

Lépjen kapcsolatba velünk, ha magasabb, mint az, hogy van szüksége.

**Kérdés**: I letöltheti futtassam és helyileg futtatja az alkalmazást?

**Válasz**: nem engedélyezzük a modellek kell tölteni és helyben végrehajtott.

**Kérdés**: saját naplózott kérések?

**Válasz**: választhat, hogy a nyomkövetés, onnantól nem hang kikapcsolhatják a központi telepítés létrehozása során, vagy beszédátírás lesz naplózva. Ellenkező esetben kérelmek általában jelentkezett be az Azure-ban biztonságos tárolására. Ha további adatvédelmi megfontolások, amelyek nem engedik a Custom Speech Service segítségével lépjen kapcsolatba velünk.

## <a name="importing-data"></a>Adatok importálása

**Kérdés**: Mi az az adatkészlet méretének korlátját? Hogy miért? 

**Válasz**: az adatkészlet jelenleg legfeljebb 2 GB HTTP feltöltendő fájl mérete a korlátozás miatt nem lehetséges. 

**Kérdés**: is szeretnék zip saját szöveges fájlok annak érdekében, hogy töltsön fel egy nagyobb szöveges fájlt? 

**Válasz**: nem, jelenleg csak a tömörítetlen szöveges fájlok engedélyezettek.

**Kérdés**: az adatok a jelentés szerint a utterances sikertelen volt. Az ezt a problémát?

**Válasz**: Ha csak néhány utterances sikertelen importálása sikeresen megtörtént, de ez nem probléma.
Ha egy akusztikai és nyelvi adatok megcímkézzen túlnyomó többsége (pl. > 95 %-os) sikeresen importálva, az adatkészlet is használható. Ajánlott azonban, hogy próbálja megérteni, hogy megcímkézzen miért volt sikertelen, és hárítsa el a problémákat.
Leggyakoribb problémák, például a formázási hibákat, olyan könnyű megoldást kínál. 

## <a name="creating-am"></a>DE létrehozása

**Kérdés**: mennyi akusztikai adatot van szükségem?

**Válasz**: javasoljuk, hogy az egy órás akusztikai adathoz 30 perc alatt

**Kérdés**: az adatok milyen rendezési kell gyűjteni?

**Válasz**: kell a forgatókönyvet legközelebb adatok gyűjtésére és kis-és nagybetűhasználattal lehető.
Ez azt jelenti, hogy az adatgyűjtés meg kell egyeznie a cél alkalmazás és a felhasználók eszköz vagy eszközök, a környezetek és előadói típusú tekintetében. Általában érdemes összegyűjtötte az adatokat a legszélesebb beszélők minél számos. 

**Kérdés**: hogyan kell gyűjteni azt? 

**Válasz**: adatok gyűjtése önálló alkalmazás létrehozása, vagy használhatja a ki a kereskedelmi rendszerhang rögzítésének szoftvert.
Az alkalmazás, amely az audio adatokat naplózza, és használja, amely egy verziója is létrehozhat. 

**Kérdés**: kell átvezeti betanítás magam? 

**Válasz**: az adatok megjelenített érzéseket kell lennie. Lefényképezze saját maga, vagy szakmai beszédátírási szolgáltatást használ. Közösségi használja ezeket használja szakmai transcribers és mások. Azt is javasoljuk, hogy egy beszédátírási szolgáltatás kérésre.

**Kérdés**: mennyi időt vesz igénybe egy egyéni akusztikai modell létrehozásához?

**Válasz**: arra készül, ugyanaz, mint a akusztikai adatkészlet hossza, a feldolgozási idő egy importálni akusztikai modell létrehozásához.
Így a testreszabott akusztikai modell öt órát adatkészletből egy órát fog igénybe venni nagyjából öt folyamatot. 

## <a name="offline-testing"></a>Offline tesztelés

**Kérdés**: végrehajtható meg, hogy saját egyéni nyelvi modell segítségével importálni akusztikai modell offline tesztelés?

**Válasz**: Igen, csak adja meg a testreszabott nyelvi modell, miután beállította az offline vizsgálat legördülő

**Kérdés**: végrehajtható meg, hogy saját egyéni nyelvi modell, egy egyéni akusztikai modell használatával offline tesztelés?

**Válasz**: Igen, a importálni akusztikai modell csak válassza a legördülő menüben az offline vizsgálat beállításakor.

**Kérdés**: Mi az a Word Hibaarány, és hogyan, számított?

**Válasz**: Word Hibaarány az értékelés metrika beszédfelismerési. A hibák teljes száma, amely tartalmazza a beszúrások, törléseket és helyettesítések továbbá, a hivatkozás beszédátírási szavak száma osztva számítani.

**Kérdés**: I most már ismeri a vizsgálati eredmények, saját egyéni modell, jó, vagy hibás számnak szó?

**Válasz**: az eredmények megjelenítése a modell és a egy testreszabott összehasonlítását.
Meg kell célja, hogy a modell, hogy a testreszabási adnak a munkánknak beat

**Kérdés**: hogyan do I döntse el, a WER alap modell, így látható, hogy történt-e fokozása? 

**Válasz**: az offline vizsgálat eredményeit mutatja a egyéni modell és a javítása alapkonfiguráció pontosságát pontosságát alapkonfiguráció

## <a name="creating-lm"></a>Az LM létrehozása

**Kérdés**: szöveges adatok mennyiségét kell feltölteni?

**Válasz**: Ez egy bonyolult kérdést a pontos választ adni, attól függ, hogy különböző, a szöveg szóhasználati, és az alkalmazásban használt kifejezések a kiindulási nyelvi modellek vannak. Minden új szavak célszerű azokat a szavakat használatát a lehető legtöbb példákat biztosítanak. Az alkalmazásában használt gyakori kifejezések beleértve azokat a nyelvi adatok akkor is hasznos, ahogy azt jelzi, hogy a rendszer figyelni ezeket a feltételeket, valamint a.
Gyakori, hogy legalább száz és általában több száz utterances nyelvi adatkészlet vagy több olyan fontos.
Is ha bizonyos típusú * a lekérdezések várhatóan gyakoribb a többinél, az adatkészlet több másolatot a gyakori lekérdezések beszúrásához.

**Kérdés**: csak feltölthetek olyan szólistát?

**Válasz**: feltöltése szavak listáját beszédstílusok, a szavakat lekérése azonban tartalmazza a rendszer a szavakat általában használata.
Teljes vagy részleges utterances (mondatokból vagy kifejezésekből vagy a felhasználók valószínűleg övezi hálózata) biztosításával a nyelvi modell tudhat meg az új szavakat és a használatuk. Az egyéni nyelvi modell jó, nem csak az első, a rendszer új szavak, hanem az alkalmazás ismert szavak valószínűségét hangolását. Teljes kimondott szöveg így segít a rendszer megtanulja ezeket. 

-----

 * [Áttekintés](cognitive-services-custom-speech-home.md)
 * [Elindítva](cognitive-services-custom-speech-get-started.md)
 * [Szószedet](cognitive-services-custom-speech-glossary.md)
