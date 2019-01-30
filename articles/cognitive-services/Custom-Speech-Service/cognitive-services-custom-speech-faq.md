---
title: Kapcsolatos gyakori kérdések a Custom Speech Service az Azure-ban |} A Microsoft Docs
description: Az alábbiakban a Custom Speech Service legnépszerűbb kérdésekre adott válaszok.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.subservice: custom-speech
ms.topic: article
ms.date: 11/21/2016
ms.author: panosper
ms.openlocfilehash: 88486ec9d1ca11d25ca31ca0abb4a34509d19a27
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55228032"
---
# <a name="custom-speech-service-frequently-asked-questions"></a>Custom Speech Service – gyakori kérdések

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-custom-speech-deprecation-note.md)] 

Ha nem találja a kérdésekre adott válaszok a gyakori kérdések, próbálja meg a Custom Speech Service Közösség kéri a [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) és [uservoice-on](https://cognitive.uservoice.com/)

## <a name="general"></a>Általános kérdések

**Kérdés**: Honnan tudom, ha az adatkészlet vagy a modell feldolgozása befejeződött?

**válasz**: A modell vagy a tábla adatkészlet állapotát jelenleg az egyetlen szeretne tudni erről.
A feldolgozás befejeződése után a állapota "Kész" lesz.
Továbbfejlesztett állapotának, például az e-mail-értesítés feldolgozása kommunikációs módszerek is dolgozunk.

**Kérdés**: Létrehozható egyszerre egynél több modell?

**válasz**: Nem korlátozott, hogy hány modellek a gyűjteményben lévő, de minden oldalon egyszerre csak egy lehet létrehozni.
A nyelvi modell-létrehozási folyamata például, ha jelenleg a nyelvi modell a folyamat állapotban nem lehet elindítani.
Az akusztikai modell és a egy nyelvi modell feldolgozása egyszerre azonban is rendelkezik. 

**Kérdés**: Vettem hiba. Hogyan megszakítja a saját adatok importálása vagy folyamatban lévő létrehozását modellkezelési? 

**válasz**: Jelenleg nem vonható vissza egy akusztikai és nyelvi betanítás folyamat.
Importált adatok az importálás befejezése után lehet törölni

**Kérdés**: Mi a különbség a Keresés & Diktálás modelleket és a természetes nyelvi modellek között?

**válasz**: Nincsenek két alap akusztikai és nyelvi modelleket a Custom Speech Service közül választhat.
keresési lekérdezések vagy Diktálás. A Microsoft Conversational AM akkor megfelelő, a beszéd beszélt természetes nyelvi stílus felismerve.
Az ilyen típusú speech általában irányul másik személynek, például a telefonos ügyfélszolgálatok vagy értekezletek.

**Kérdés**: Frissítheti a saját meglévő model (modell rétegezést)?

**válasz**: Nem biztosítunk lehetővé teszi egy meglévő modell új adatokkal frissülnek.
Ha egy új adatkészletet és egy meglévő modell testreszabásához kell újra alakítani az új adatokat és a régi adatkészletet is használt.
A régi és új adatkészletek kell kombinálhatók egyetlen .zip (ha akusztikai adathoz) vagy egy .txt fájl nyelvi adatok egyszer betanítás esetén történik az új frissített modell kell lennie a megszüntetéséhez telepített új végpont beszerzése

**Kérdés**: Mi történik, ha szükséges, mint az alapértelmezett érték a magasabb szintű egyidejűség érdekében. 

**válasz**: A modell 5 egyidejű kérés, amelyet a skálázási egység nevezzük egységnyi növekményekben vertikálisan felskálázhatja. Minden egyes méretezési egység garantálja, hogy a modell képes egyszerre feldolgozni 5 audio-adatfolyam. 100 skálázási egységeket (vagy 500 egyidejű kérelmek) vásárolhatók meg.

Lépjen kapcsolatba velünk, ha magasabb, mint az, hogy van szüksége.

**Kérdés**: A modell letöltheti és helyileg futtatja az alkalmazást?

**válasz**: Hogy ne engedélyezze a modellek kell tölteni és helyben végrehajtott.

**Kérdés**: Bejelentkezett saját kérések?

**válasz**: Választhat, hogy nyomon követését, onnantól nem audio- és beszédátírás lesz naplózva kikapcsolhatják a központi telepítés létrehozása során. Ellenkező esetben kérelmek általában jelentkezett be az Azure-ban biztonságos tárolására. Ha további adatvédelmi megfontolások, amelyek nem engedik a Custom Speech Service segítségével lépjen kapcsolatba velünk.

## <a name="importing-data"></a>Adatok importálása

**Kérdés**: Mi az az adatkészlet méretének korlátját? Hogy miért? 

**válasz**: Az adatkészlet jelenleg legfeljebb 2 GB-os, töltse fel a HTTP-hez egy fájlt a mérete korlátozás miatt. 

**Kérdés**: Képes a szöveges fájlokat is zip annak érdekében, hogy töltsön fel egy nagyobb szöveges fájlt? 

**válasz**: Nem, jelenleg csak tömörítetlen szöveges fájlok engedélyezettek.

**Kérdés**: Az adatok a jelentés szerint utterances sikertelen volt. Az ezt a problémát?

**válasz**: Ha csak néhány utterances importálása sikerült sikertelen, mert ez nem probléma.
Ha egy akusztikai és nyelvi adatok megcímkézzen túlnyomó többsége (pl. > 95 %-os) sikeresen importálva, az adatkészlet is használható. Ajánlott azonban, hogy próbálja megérteni, hogy megcímkézzen miért volt sikertelen, és hárítsa el a problémákat.
Leggyakoribb problémák, például a formázási hibákat, olyan könnyű megoldást kínál. 

## <a name="creating-am"></a>DE létrehozása

**Kérdés**: Mennyi akusztikai adatot van szükségem?

**válasz**: Javasoljuk, hogy az egy órás akusztikai adathoz 30 perc alatt

**Kérdés**: Milyen típusú adatokat kell gyűjteni?

**válasz**: Érdemes legközelebb a forgatókönyvet adatok gyűjtésére és a kis-és nagybetűhasználattal lehető.
Ez azt jelenti, hogy az adatgyűjtés meg kell egyeznie a cél alkalmazás és a felhasználók eszköz vagy eszközök, a környezetek és előadói típusú tekintetében. Általában érdemes összegyűjtötte az adatokat a legszélesebb beszélők minél számos. 

**Kérdés**: Hogyan kell azok? 

**válasz**: Adatok gyűjtése önálló alkalmazás létrehozása, vagy használhatja a ki a kereskedelmi rendszerhang rögzítésének szoftvert.
Az alkalmazás, amely az audio adatokat naplózza, és használja, amely egy verziója is létrehozhat. 

**Kérdés**: Kell átvezeti betanítás magam? 

**válasz**: Az adatok a megjelenített érzéseket kell lennie. Lefényképezze saját maga, vagy szakmai beszédátírási szolgáltatást használ. Közösségi használja ezeket használja szakmai transcribers és mások. Azt is javasoljuk, hogy egy beszédátírási szolgáltatás kérésre.

**Kérdés**: Mennyi ideig tart egy egyéni akusztikai modell létrehozásához?

**válasz**: A feldolgozási idő egy importálni akusztikai modell létrehozásához tárgya ugyanaz, mint a akusztikai adatkészlet hossza.
Így a testreszabott akusztikai modell öt órát adatkészletből egy órát fog igénybe venni nagyjából öt folyamatot. 

## <a name="offline-testing"></a>Offline tesztelés

**Kérdés**: Végezhetek el saját egyéni nyelvi modell segítségével importálni akusztikai modell offline tesztelés?

**válasz**: Igen, csak adja meg a testreszabott nyelvi modell az offline vizsgálat beállításakor legördülő

**Kérdés**: Végezhetek el saját egyéni nyelvi modell, egy egyéni akusztikai modell használatával offline tesztelés?

**válasz**: Igen, csak adja meg az importálni akusztikai modell a legördülő menüben az offline vizsgálat beállításakor.

**Kérdés**: Mi a Word-hibák aránya, és hogyan, számított?

**válasz**: Word Hibaarány az értékelés metrika beszédfelismerési. A hibák teljes száma, amely tartalmazza a beszúrások, törléseket és helyettesítések továbbá, a hivatkozás beszédátírási szavak száma osztva számítani.

**Kérdés**: E most már ismeri a vizsgálati eredmények, saját egyéni modell, ez helyes, vagy hibás számos?

**válasz**: Az eredmények megjelenítése a modell és a egy testreszabott összehasonlítását.
Meg kell célja, hogy a modell, hogy a testreszabási adnak a munkánknak beat

**Kérdés**: Hogyan do I döntse el, a WER alap modell, így látható, hogy történt-e fokozása? 

**válasz**: Az offline vizsgálat eredményeit mutatja a egyéni modell és a javítása alapkonfiguráció pontosságát pontosságát alapkonfiguráció

## <a name="creating-lm"></a>Az LM létrehozása

**Kérdés**: Szöveges adatok mennyiségét van szükségem a feltöltendő?

**válasz**: Ez nehéz kérdés, a pontos választ adni, a kezdési nyelvi modelljeit, attól függ, a különböző szóhasználatának és az alkalmazásban használt kifejezések származnak. Minden új szavak célszerű azokat a szavakat használatát a lehető legtöbb példákat biztosítanak. Az alkalmazásában használt gyakori kifejezések beleértve azokat a nyelvi adatok akkor is hasznos, ahogy azt jelzi, hogy a rendszer figyelni ezeket a feltételeket, valamint a.
Gyakori, hogy legalább száz és általában több száz utterances nyelvi adatkészlet vagy több olyan fontos.
Is ha bizonyos típusú * a lekérdezések várhatóan gyakoribb a többinél, az adatkészlet több másolatot a gyakori lekérdezések beszúrásához.

**Kérdés**: Csak feltölthetek olyan szólistát?

**válasz**: Olyan szólistát feltöltése beszédstílusok, a szavakat beolvasása, de tartalmazza a rendszer a szavakat általában használata.
Teljes vagy részleges utterances (mondatokból vagy kifejezésekből vagy a felhasználók valószínűleg övezi hálózata) biztosításával a nyelvi modell tudhat meg az új szavakat és a használatuk. Az egyéni nyelvi modell jó, nem csak az első, a rendszer új szavak, hanem az alkalmazás ismert szavak valószínűségét hangolását. Teljes kimondott szöveg így segít a rendszer megtanulja ezeket. 

-----

 * [Áttekintés](cognitive-services-custom-speech-home.md)
 * [Elindítva](cognitive-services-custom-speech-get-started.md)
 * [Szószedet](cognitive-services-custom-speech-glossary.md)
