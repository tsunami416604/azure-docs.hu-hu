<properties
    pageTitle="Mobile Engagement – fogalmak | Microsoft Azure"
    description="Azure Mobile Engagement – fogalmak"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

# Azure Mobile Engagement – fogalmak

A Mobile Engagement néhány általános, minden támogatott platform esetén érvényes fogalmat használ. Ez a cikk röviden ismerteti ezen fogalmakat.

A cikk remek kezdőpont, ha a Mobile Engagement új felhasználója. Emellett mindenképpen olvassa el a használt platformra vonatkozó dokumentációt, mert az pontosítja az ebben a cikkben ismertetett fogalmakat további információval, példákkal és lehetséges korlátozásokkal.

## Eszközök és felhasználók
A Mobile Engagement az egyes eszközök számára létrehozott egyedi azonosítóval azonosítja a felhasználókat. Ezen azonosító neve eszközazonosító (vagy `deviceid`). A létrehozása olyan módon történik, hogy az ugyanazon eszközön futó összes alkalmazás ugyanazon eszközazonosítót használja.

Ez lényegében azt jelenti, hogy a Mobile Engagement úgy tekinti, egy eszközhöz pontosan egy felhasználó tartozik, ezért a felhasználók és az eszközök egyenértékű fogalmak.

## Munkamenetek és tevékenységek
A munkamenet a felhasználó által használt alkalmazás egy használati alkalma, amely az alkalmazás használatának a kezdetétől a befejezéséig tart.

A tevékenység az alkalmazás egy alrészének egy felhasználó általi egyszeri használata (ez általában egy képernyő, de az alkalmazás bármely megfelelő része lehet).

Egy felhasználó egyszerre csak egy tevékenységet végezhet.

A tevékenységek névvel azonosíthatók (legfeljebb 64 karakter hosszúságban), és tartalmazhatnak további adatokat (legfeljebb 1024 bájt mennyiségben).

A munkamenetek számítása automatikusan történik a felhasználó által végrehajtott műveletek sorozata alapján. A munkamenet akkor kezdődik, amikor a felhasználó elindítja az első tevékenységet, és az utolsó tevékenység befejezésével ér véget. Ez azt jelenti, hogy a munkamenetet nem kell külön elindítani vagy leállítani. Helyette a tevékenységeket kell külön elindítani és leállítani. Ha nincs jelentett tevékenység, jelentett munkamenet sincs.

## Események
Az események azonnali műveletek (például milyen gombokra kattintott a felhasználó, vagy mely cikkeket olvasta el) jelentésére szolgálnak.

Az esemény kapcsolódhat az aktuális munkamenethez, egy futó feladathoz vagy lehet különálló esemény.

Az események névvel azonosíthatók (legfeljebb 64 karakter hosszúságban), és tartalmazhatnak további adatokat (legfeljebb 1024 bájt mennyiségben).

## Hiba
A hibák az alkalmazás által helyesen észlelt problémák jelentésére használhatók (például hibás felhasználói műveletek vagy sikertelen API-hívások).

A hiba kapcsolódhat az aktuális munkamenethez, egy futó feladathoz vagy lehet különálló hiba.

A hibák névvel azonosíthatók (legfeljebb 64 karakter hosszúságban), és tartalmazhatnak további adatokat (legfeljebb 1024 bájt mennyiségben).

## Feladat
A feladatok időtartammal rendelkező műveletek jelentésére használhatók (például az API-hívások hossza, a hirdetések megjelenítésének időtartama, háttérfeladatok vagy felhasználói műveletek időtartama).

A feladatok nem kapcsolódnak munkamenethez, mert egy feladat elvégezhető a háttérben is, felhasználói interakció nélkül.

A feladatok névvel azonosíthatók (legfeljebb 64 karakter hosszúságban), és tartalmazhatnak további adatokat (legfeljebb 1024 bájt mennyiségben).

## Összeomlás
Az összeomlásokat automatikusan bocsátja ki a Mobile Engagement SDK olyan alkalmazáshibák jelentésére, amelyeket nem észlel az alkalmazás és összeomlik miattuk.

## Alkalmazásadatok
Az alkalmazásadatok a felhasználók címkézéséhez használhatók, vagyis adatoknak egy alkalmazás felhasználóihoz való társítására (a webes cookie-khoz hasonlóan, az alkalmazásadatok tárolása azonban a kiszolgálói oldalon történik az Azure Mobile Engagement platformján).

Az alkalmazásadatok regisztrálhatóak a Mobile Engagement SDK API-jának vagy a Mobile Engagement platform eszköz API-jának használatával.

Az alkalmazásadatok az eszközhöz rendelt kulcs/érték párok. A kulcs az alkalmazásadat neve (legfeljebb 64 ASCII-betűből (a–z, A–Z), számokból (0–9) és aláhúzásjelből (_) állhat). Az érték (legfeljebb 1024 karakter hosszú lehet) bármilyen karakterlánc, egész szám, dátum (éééé-hh-nn) vagy logikai érték (true vagy false) lehet.

Bármennyi alkalmazásadat társítható egy eszközhöz a Mobile Engagement díjszabási feltételei által meghatározott keretek között. Egy adott kulcsra vonatkozóan a Mobile Engagement a kulcsnak csak a legutóbbi értékét követi nyomon (az előzményeket nem). Az alkalmazásadatok értékének beállítása vagy módosítása arra kényszeríti a Mobile Engagement alkalmazást, hogy újraértékelje a célközönséghez kapcsolódó feltételek az alkalmazásadatok alapján (ha vannak), tehát az alkalmazásadatok használhatók valós idejű leküldések eseményindítóiként.

## További adatok
A további adatok olyan tetszőleges adatok, amelyek eseményekhez, hibákhoz, tevékenységekhez és feladatokhoz csatolhatók.

A további adatok szerkezete hasonlít a JSON-objektumokhoz: a kulcs/érték párok fájából állnak. A kulcsok legfeljebb 64 ASCII-betűből [a–z, A–Z], számból [0–9] és aláhúzásjelből (_) állhatnak, a további adatok teljes mérete pedig legfeljebb 1024 karakter lehet (miután a Mobile Engagement SDK JSON-formátumba kódolja azokat).

A kulcs/érték párok teljes fájának tárolása JSON-objektumként történik. A kulcs/érték pároknak azonban csupán az első szintjét bontja fel a rendszer, hogy közvetlenül elérhetők legyenek néhány olyan fejlett funkció számára, amilyen például a Segments (Szegmensek) (egyszerűen definiálhat például egy „Sci-Fi-rajongók” szegmenset, amely az összes olyan felhasználót tartalmazza, akik legalább 10 alkalommal küldték el a „content_viewed” eseményt az elmúlt hónapban a „scifi” értékű „content_type” további kulccsal kiegészítve). Ezért erősen ajánlott csak olyan egyszerű listákból álló további adatokat elküldeni, amelyekben a kulcs/érték párok skaláris értékek (például karakterláncok, dátumok, egész számok és logikai értékek).

## Következő lépések

- [Windows Universal SDK overview for Azure Mobile Engagement (Windows Universal SDK és Azure Mobile Engagement – áttekintés)](mobile-engagement-windows-store-sdk-overview.md)
- [Windows Phone Silverlight SDK overview for Azure Mobile Engagement (Windows Phone Silverlight SDK és Azure Mobile Engagement – áttekintés)](mobile-engagement-windows-phone-sdk-overview.md)
- [iOS SDK for Azure Mobile Engagement (iOS SDK és Azure Mobile Engagement)](mobile-engagement-ios-sdk-overview.md)
- [Android SDK és Azure Mobile Engagement](mobile-engagement-android-sdk-overview.md)



<!--HONumber=sep16_HO1-->


