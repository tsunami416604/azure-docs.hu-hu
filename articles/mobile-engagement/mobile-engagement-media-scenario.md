---
title: "Az Azure Mobile Engagement megvalósítási Media alkalmazás"
description: "Media app forgatókönyv végrehajtásához az Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 48201cc8-4e04-485c-a8dc-d6406d23f3ed
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: c1591c3e436981e621830916cf0cdc4b7f395d7b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="implement-mobile-engagement-with-media-app"></a>Mobile Engagement az Médiaalkalmazással megvalósítása
## <a name="overview"></a>Áttekintés
John egy mobil projektmenedzsert nagy adathordozó számára. Ezután egy új alkalmazást, amely rendelkezik egy nagyon magas letöltési száma nemrég indul el. Ezután elérte a letöltési a célokat, de továbbra is a térjen vissza a Investment(ROI) felhasználónként nem felel meg a követelményeknek. 

John már észlelt, ezért a ROI értéke túl alacsony. Felhasználók gyakran abbahagyja az alkalmazás csak 2 hét után, és legtöbbjük soha nem térjen vissza. Azt szeretné, hogy az alkalmazás felhasználóinak megtartása növelése érdekében.

Után néhány kezdeti tesztelése, ő rendelkezik megtanulta, amikor ezután kapcsolatba lép a leküldéses értesítések, a felhasználók általában az alkalmazás használatának folytatása. Is, melyeket inaktív felhasználók fog gyakran az alkalmazáshoz való visszatérésre attól függően, hogy elküldi azokat értesítések. John úgy dönt, hogy a bevonási programnak a alkalmazás, amely a leküldéses értesítések használ a speciális célcsoport-kezelési valamilyen beruházásának.

John nemrég elolvasta a [Azure Mobile Engagement – első lépések útmutató ajánlott eljárásoknak megfelelő beállításában](mobile-engagement-getting-started-best-practices.md) és határozott, hogy a útmutató ajánlások megvalósításával.

## <a name="objectives-and-kpis"></a>Célok és a KPI-k
John alkalmazás fő érdekelteknek felel meg. Üzleti ads jönnek létre, a felhasználók a saját media felhasználását. John felhasználónként fogyasztott tartalmak növelésével növeli a saját bevételek. Az összes megállapodjanak egyik fő cél a: növelje a hirdetések értékesítési 25 %-kal. Hoznak létre üzleti a fő teljesítménymutatók (KPI-k) mérték, és a meghajtó ebben a célkitűzésben

* Felhasználónként kattintott reklámkattintások száma
* A cikk lapok meglátogatott (minden felhasználóhoz / munkamenetenként / hetente / havi...)
* Mik azok a kedvenc kategóriáinak

A fő érdekelteknek John értekezlet alapján definiált rendelkezik saját üzleti KPI-k. Ezután a következő 1. rész a [Azure Mobile Engagement – első lépések útmutató ajánlott eljárásoknak megfelelő beállításában](mobile-engagement-getting-started-best-practices.md). 

Ezután létrehoz a következő bevonási KPI-k győződjön meg arról, hogy elérte-e a célok:

* Megőrzési figyeléséhez a következő időszakok között: napi, heti, Kétheti vagy havi.
* Aktív felhasználók száma
* Az alkalmazás minősítése az alkalmazásban tárolja.

Az informatikai csapat javaslatai alapján, a következő műszaki KPI-k lettek hozzáadva a következő kérdések megválaszolásához:

* Mi az a felhasználó elérési út (mely megnyitják, hogy hány amikor a felhasználók egy azt)
* Összeomlások vagy munkamenetenként észlelt hibák száma?
* Milyen operációs rendszer verzióit futtatják a felhasználók?
* Mi az, hogy a felhasználók számára képernyő átlagos mérete?
* Milyen típusú hálózati kapcsolatok használata esetén rendelkeznek a felhasználók?

Az egyes KPI-KET ő osztályozza a szükséges adatokat, és megfelelő helyét, a forgatókönyv a azt rögzíti.

## <a name="engagement-program-and-integration"></a>Bevonási program és az integráció
Most, hogy John befejezte a KPI-k meghatározása, a bevonási stratégia fázis először 4 bevonási programok és a célok meghatározása:![][1]

John megnyitja mélyebb és részletesen leírja a leküldéses értesítések minden program által. Leküldéses értesítési öt elem határozzák meg:

1. Cél: Mi az a cél az értesítés
2. Hogyan a cél érhető el
3. Cél: ki fogja megkapni az értesítés?
4. Tartalom: Mi az az a szöveg és az értesítés (a App/Out a következő alkalmazás) formátumát
5. Mikor: Mi az a legjobb néhány percet a leküldéses értesítés küldése
   
    ![][2]

További információ a [Playbooks](https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks).

Megfelelően a rész a tanulmány John 2. cél szakasz segítségével határozza meg, milyen adatokat kell gyűjteni, és írja a címkézési tervben közösen az informatikai csapat a megoldás megvalósításához. A megvalósítás és a felhasználói tesztelés 1 hét után John végül indíthatja el a programok.

## <a name="program-results"></a>Program eredmények
4 hónappal később János áttekinti a programok teljesítményét. Üdvözli a Program és a heti Program teljesítésének a célokat. A felhasználó csak egy munkamenet-száma csökken, az alkalmazás több szolgáltatást használják, és hetente kapcsolatok száma rendelkezik kettőzni.

A **inaktív Program** elősegíti az John felhasználói tendenciák feltárásához ismertetése. Úgy tűnik, hogy az inaktív felhasználók 15 %-át térjen vissza az alkalmazáshoz. Azonban ezek többsége nem maradnak aktívak, több mint 1 hónap. John úgy rendelkezik, a potenciális optimalizálása további értesítések és a tartalom lehetőségek bővítése esik.

A **felderítése Program** nem működik jól. Növeli a több értékesítő azonban nem elegendő a célok eléréséhez. John azonosítja, hogy ő nem rendelkezik elegendő adatokba, így a megfelelő célcsoport-meghatározási és javasolja a megfelelő tartalom. Ezután a program leáll, és "Szerkesztői leküldéses értesítések küldése" az Azure Mobile Engagement-ra összpontosít. A újságírók már van egy CMS megoldás leküldéses értesítések küldéséhez, és azokat nem szükséges módosítani.

John úgy dönt, hogy használja-a Reach API, amely egy HTTP REST API-t, amely lehetővé teszi a Reach-kampányokat kezelése AZME webes felület használata nélkül. Ezt a módszert János ezután van szüksége, és annak írók használhatja a CMS megoldás az adatok képes összegyűjteni.

Győződjön meg arról, hogy a szolgáltatás megfelelően működik-e, hogy John kéri informatikai csoportját, hogy éberen meg a következő szempontokat:

1. **A művelet rendszerek** : minden rendelkeznek a saját szabályok felügyelheti a leküldéses értesítések, ezért John úgy dönt, hogy minden esetben listában, és ellenőrzi az API-k kezelni azt.
   Például: Android leküldéses rendszer lehetővé teszi, hogy nagy vonalakban tekinti, amely esetben az iOS.
2. **Időkereten belül**: John szeretne egy API-t, amely az időkeret és egy záró kampányokra. Szeretné megőrizni a felhasználók bármilyen zavaró értesítési bombing.
3. **Kategóriák**: Marketing csapat sablon előkészíti az egyes riasztások. John IT-csoport beállítása az API-t belül kategóriák kéri.

Néhány teszt után John meggyőződött. Ez az API környezetnek köszönhetően újságírók továbbra is küldhet a CMS és Azure Mobile Engagement leküldéses értesítéseket az összes viselkedési adatokat gyűjt a számukra

Ezek 4 után először hónap, eredmények tükröznie egy jó általános teljesítmény- és ad abban, hogy John és annak üzenőfalon, ROI felhasználói növekszik a tartomány 15 %-a / és mobil értékesítési képviselő 17.5 % összes értékesítés 7.5 %-os növekedést csak négy hónapon belül.

<!--Image references-->
[1]: ./media/mobile-engagement-media-scenario/engagement-strategy.png
[2]: ./media/mobile-engagement-media-scenario/push-scenarios.png

<!--Link references-->
[Media Playbook link]: https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks
