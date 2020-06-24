---
title: Egyéni parancsok – fogalmak és definíciók – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: Ebben a cikkben megismerheti az egyéni parancsok alkalmazásaival kapcsolatos fogalmakat és definíciókat.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 5a5e3590e0e184633760f080dfd3402ed75bd48e
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2020
ms.locfileid: "85307515"
---
# <a name="custom-commands-concepts-and-definitions"></a>Egyéni parancsok – fogalmak és definíciók

Ez a cikk az egyéni parancsok alkalmazásaira vonatkozó fogalmakkal és definíciókkal kapcsolatos referenciául szolgál.

## <a name="commands-configuration"></a>Parancsok konfigurálása
A parancsok az egyéni parancsok alkalmazás alapszintű építőelemei. A parancsok olyan konfigurációk, amelyek szükségesek a felhasználó által definiált adott feladat végrehajtásához.

### <a name="example-sentences"></a>Példa mondatokra
Példa a hosszúságú kimondott szöveg a felhasználók számára egy adott parancs indítására. Vegye figyelembe, hogy csak hosszúságú kimondott szöveg-mintát kell megadnia, és nem teljes listát. 

### <a name="parameters"></a>Paraméterek
A paraméterek a feladatok végrehajtásához szükséges adatok. Összetett forgatókönyvekben a paramétereket az egyéni műveleteket kiváltó feltételek definiálására is használhatja.

### <a name="completion-rules"></a>Befejezési szabályok
A parancs végrehajtásának befejezését követően végrehajtandó szabályok sorozata, azaz a szabályok összes feltétele teljesül.

### <a name="interaction-rules"></a>Interakciós szabályok
További szabályok a konkrétabb vagy összetett helyzetek kezelésére. Hozzáadhat további érvényesítéseket, vagy konfigurálhat speciális funkciókat, például a megerősítést vagy az egylépéses javítást. Saját, egyéni interakciós szabályokat is létrehozhat.

## <a name="parameters-configuration"></a>Paraméterek konfigurálása

A paraméterek a feladatok végrehajtásához szükséges parancsok. Összetett forgatókönyvekben a paramétereket az egyéni műveleteket kiváltó feltételek definiálására is használhatja.

### <a name="name"></a>Name
A Name tulajdonság azonosítja a paramétereket. Mindig adjon egy leíró nevet a paraméternek. A paraméterek több különböző szakaszban is szerepelhetnek, például a feltételek, a beszédfelismerési válaszok vagy más műveletek létrehozásához.
 
### <a name="isglobal"></a>IsGlobal
Jelölőnégyzet, amely azt jelzi, hogy a paraméter hatóköre meg van-e osztva az alkalmazás összes parancsa között. Ha a paraméter globális, az értéke bármely parancs hatókörében, valamint egy érték hozzárendelése után is megadható. 

### <a name="required"></a>Kötelező
Jelölőnégyzet, amely azt jelzi, hogy a paraméter értéke kötelező-e a parancs teljesítéséhez vagy befejezéséhez. A válaszokat úgy kell konfigurálnia, hogy a felhasználó megadjon értéket, ha egy paraméter kötelezőként van megjelölve.

### <a name="type"></a>Típus
Az egyéni parancsok a következő paramétereket támogatják:


* DateTime
* Földrajzi hely
* Szám
* Sztring

Az összes ilyen típusú paraméter támogatja az alapértelmezett érték konfigurációját, amelyet a portálról konfigurálhat.

### <a name="configuration"></a>Konfiguráció
A Configuration paraméter csak a Type: String típusú tulajdonsághoz van definiálva. Alább láthatók a támogatott értékek
* Nincs
* Teljes bevitel elfogadása: Ha engedélyezi ezt a beállítást, a paraméter fogadja a teljes bevitelt, ez akkor hasznos, ha a felhasználónak a teljes kiírást megadó paraméterre van szüksége. például postai címekre.
* Előre megadott bemeneti értékek elfogadása a külső katalógusból: a paraméter konfigurálására szolgál, amely az értékek széles skáláját feltételezheti. például értékesítési katalógus. Ebben az esetben a katalógus egy külső webes végponton van tárolva, és külön is konfigurálható.
* Előre meghatározott bemeneti értékek elfogadása a belső katalógusból: a paraméter konfigurálására szolgál, amely néhány értéket feltételez. Ebben az esetben az értékeket a Speech Studióban kell konfigurálni.


### <a name="validation"></a>Ellenőrzés
Az érvényesítések bizonyos paraméterekre érvényesek, amelyek segítségével megkötéseket konfigurálhat a paraméter értékeként. Az egyéni parancsok jelenleg a következő paraméterek típusai esetében támogatják az érvényesítést:
* DateTime
* Szám

## <a name="rules-configuration"></a>Szabályok konfigurálása
Az egyéni parancsok szabályait egy meghatározott **feltételek**határozzák meg, amelyek a teljesülés után végrehajtják a **műveletek**egy csoportját. A szabály azt is lehetővé teszi, hogy a **végrehajtás utáni állapotot** és a következő bekapcsolási **elvárásokat** is konfigurálja.

### <a name="types"></a>Típusok
Az egyéni parancsok a következő szabályok kategóriáit támogatják:
* Befejezési szabályok

    A parancs teljesítése után végrehajtandó szabályok listája. Az ebben a szakaszban konfigurált összes szabályt, amely esetében a feltételek teljesülnek, végrehajtja a rendszer.
    
* Interakciós szabályok

    Az interakciós szabályok további egyéni érvényesítések, megerősítések, Egylépéses javítás vagy más egyéni párbeszédpanel-logika megvalósítására használhatók. Ezeket a szabályokat a rendszer minden egyes folyamat során kiértékeli, és a befejezési szabályok elindítására használható.

A szabály részeként konfigurált különböző műveletek végrehajtása a szerzői portálon megjelenő sorrendben történik.
    
### <a name="conditions"></a>Feltételek
Olyan feltételek összessége, amelyeknek teljesülnie kell egy szabály végrehajtásához. A szabályok feltétele a következő típusú lehet:
* A paraméter értéke egyenlő: a konfigurált paraméter értéke egy adott értéknek felel meg.
* Nincs paraméter értéke: a konfigurált paraméterek nem rendelkezhetnek értékkel.
* Szükséges paraméterek: a konfigurált paraméter értékkel rendelkezik.
* Az összes kötelező paraméter: a kötelezőként megjelölt összes paraméternek értékkel kell rendelkeznie.
* Frissített paraméterek: a rendszer egy vagy több paraméter értékét frissítették az aktuális bevitel (teljes/tevékenység) feldolgozásának eredményeképpen.
* A megerősítés sikeres volt: a bemeneti művelet/tevékenység sikeres megerősítést kapott (igen).
* A megerősítés megtagadva: a bemenő adat/tevékenység sikeres megerősítést kapott (nem).
* A korábbi parancsot frissíteni kell: ezt a feltételt akkor használja a rendszer, ha egy adott frissítéssel együtt el szeretné érni a megtagadást. A színfalak mögött ez akkor van konfigurálva, amikor a párbeszédpanel-motor negatív megerősítést észlel, ha a cél ugyanaz, mint az előző kanyar, és a felhasználó egy frissítéssel válaszolt.

### <a name="actions"></a>Műveletek
* Beszédfelismerési válasz küldése: beszédfelismerési válasz küldése az ügyfélnek.
* Frissítési paraméter értéke: egy parancs-paraméter értékének frissítése egy megadott értékre.
* A paraméter értékének törlése törli a parancssori paraméter értékét.
* Webes végpont hívása: hívás egy webes végpontra.
* Tevékenység küldése az ügyfélnek: egyéni tevékenység küldése az ügyfélnek.

### <a name="expectations"></a>Elvárások
Az elvárások a következő felhasználói bevitel feldolgozására vonatkozó tippeket konfigurálják. A következő típusok támogatottak:
* A felhasználó jóváhagyását várta: Megadja, hogy az alkalmazás megerősítő (igen/nem) értéket vár a következő felhasználói bevitelhez.
* A paraméter (ek) felhasználótól való bevitelének várt értéke: adjon meg egy vagy több olyan paramétert, amelyet az alkalmazás a felhasználói beviteltől várt.

### <a name="post-execution-state"></a>Végrehajtás utáni állapot
Párbeszédpanel állapota az aktuális bevitel (teljes/tevékenység) feldolgozását követően. A következő típusokból áll:
* A parancs befejeződött: fejezze be a parancsot, és a rendszer nem dolgozza fel a parancs további szabályait.
* Végrehajtás befejezési szabályai: hajtsa végre az összes érvényes befejezési szabályt.
* Várja meg a felhasználó bemenetét: várjon a következő felhasználói bevitelre.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Minták megtekintése a GitHubon](https://aka.ms/speech/cc-samples)
