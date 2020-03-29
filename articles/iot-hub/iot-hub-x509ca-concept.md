---
title: Az Azure IoT Hub X.509 biztonságának fogalmai | Microsoft dokumentumok
description: Koncepció – az IoT-eszközgyártásxofok X.509 hitelesítésszolgáltatói tanúsítványainak és a hitelesítésnek a megértése.
author: eustacea
manager: arjmands
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/18/2017
ms.author: eustacea
ms.openlocfilehash: 3c7e1167b3326620863d35cb2d4b07235cbd5517
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "61320230"
---
# <a name="conceptual-understanding-of-x509-ca-certificates-in-the-iot-industry"></a>Az X.509 hitelesítésszolgáltatói tanúsítványok fogalmi ismerete az IoT-iparágban

Ez a cikk az X.509 hitelesítésszolgáltató (CA) tanúsítványok használatának értékét ismerteti az IoT-eszközgyártásban és az IoT Hub hitelesítésében. Információkat tartalmaz az ellátási lánc beállításáról és az előnyök kiemeléséről.

Ez a cikk a következőket ismerteti:

* Mik az X.509 hitelesítésszolgáltatói tanúsítványok, és hogyan szerezhetők be

* Az X.509 hitelesítésszolgáltatói tanúsítvány regisztrálása az IoT Hubba

* Gyártási ellátási lánc beállítása az X.509 hitelesítésszolgáltatói alapú hitelesítéshez

* Az X.509 hitelesítés- és hitelesítéscsatlakoztast aláíró eszközök csatlakozása az IoT Hubhoz

## <a name="overview"></a>Áttekintés

Az X.509 hitelesítésszolgáltató (CA) hitelesítése az eszközök IoT Hubba való hitelesítésének olyan módszerrel történő hitelesítése, amely jelentősen leegyszerűsíti az eszközidentitás létrehozását és az életciklus-kezelést az ellátási láncban.

Az X.509 hitelesítésszolgáltatói hitelesítésszolgáltató imát megkülönböztető tulajdonsága a hitelesítésszolgáltatói tanúsítvány és az alsóbb rétegbeli eszközök közötti egy-a-többhöz kapcsolat. Ez a kapcsolat lehetővé teszi tetszőleges számú eszköz regisztrációját az IoT Hubba egy X.509 hitelesítésszolgáltatói tanúsítvány egyszeri regisztrálásával, ellenkező esetben az eszköz egyedi tanúsítványait minden eszközhez előzetesen regisztrálni kell, mielőtt egy eszköz csatlakozhatna. Ez az egy-a-többhöz kapcsolat leegyszerűsíti az eszköztanúsítványok életciklus-kezelési műveleteit is.

Az X.509 hitelesítésszolgáltatói hitelesítés másik fontos tulajdonsága az ellátási lánc logisztikájának egyszerűsítése. Az eszközök biztonságos hitelesítéséhez minden eszköznek egyedi titkos kulcsot kell biztosítania, például kulcsot a megbízhatóság alapjaként. A tanúsítványalapú hitelesítésben ez a titkos kulcs egy titkos kulcs. Egy tipikus eszköz gyártási folyamat magában foglalja a több lépést, és letétkezelők. Az eszköz személyes kulcsainak biztonságos kezelése több felügyeleti hatóság között, valamint a bizalom fenntartása nehéz és költséges. A hitelesítésszolgáltatók használata úgy oldja meg a problémát, hogy minden egyes letétkezelőt egy kriptográfiai megbízhatósági láncba ír alá, és nem az eszköz személyes kulcsaival bízza meg őket. Minden letétkezelő viszont aláírja az eszközöket a gyártási folyamat megfelelő folyamatlépésénél. A teljes eredmény egy optimális ellátási lánc beépített elszámoltathatóság segítségével a kriptográfiai bizalmi lánc. Érdemes megjegyezni, hogy ez a folyamat a legnagyobb biztonságot adja, amikor az eszközök védik egyedi személyes kulcsaikat. Ebből a célból sürgetjük a hardveres biztonságos modulok (HSM) használatát, amelyek képesek belsőleg olyan személyes kulcsok at generálni, amelyek soha nem fogják látni a napvilágot.

Ez a cikk az X.509 hitelesítésszolgáltató imázsának végponttól végpontig tartó nézetét kínálja az ellátási lánc beállításától az eszközkapcsolatig, miközben valós példát használ a megértés megszilárdításához.

## <a name="introduction"></a>Bevezetés

Az X.509 hitelesítésszolgáltatói tanúsítvány olyan digitális tanúsítvány, amelynek birtokosa más tanúsítványokat is aláírhat. Ez a digitális tanúsítvány X.509, mert megfelel az IETF RFC 5280 szabványa által előírt tanúsítványformázási szabványnak, és hitelesítésszolgáltató, mivel birtokosa más tanúsítványokat is aláírhat.

Az X.509 Hitelesítésnél való felhasználást egy konkrét példával kapcsolatban lehet a legjobban megérteni. Tekintsük Company-X, a gyártó a Smart-X-Widgets tervezett professzionális telepítés. A Company-X kiszervezi mind a gyártást, mind a telepítést. Ez szerződések gyártó Factory-Y gyártására a Smart-X-Widgets, és a szolgáltató Technikus-Z telepíteni. A Company-X azt szeretné, hogy a Smart-X-Widget közvetlenül szállítson a Factory-Y-ról a Technician-Z-re telepítéscéljából, és hogy közvetlenül csatlakozzon az IoT Hub Vállalat IoT Hub példányához a telepítés után, a Vállalat-X további beavatkozása nélkül. Ahhoz, hogy ez megtörténjen, a Company-X-nek el kell végeznie néhány egyszeri beállítási műveletet az automatikus kapcsolathoz szükséges Smart-X-Widget elsődleges működéséhez. A végpontok utáni forgatókönyvet szem előtt tartva a cikk többi része a következőképpen épül fel:

* Az X.509 hitelesítésszolgáltatói tanúsítvány megszerzése

* X.509 hitelesítésszolgáltatói tanúsítvány regisztrálása az IoT Hubba

* Eszközök aláírása tanúsítványláncba

* Eszközkapcsolat

## <a name="acquire-the-x509-ca-certificate"></a>Az X.509 hitelesítésszolgáltatói tanúsítvány megszerzése

Az X vállalatnak lehetősége van x.509 hitelesítésszolgáltatói tanúsítványt vásárolni egy nyilvános legfelső szintű hitelesítésszolgáltatótól, vagy önaláírt folyamaton keresztül létrehozni egyet. Az egyik lehetőség optimális lenne a másikkal szemben az alkalmazás forgatókönyvétől függően. A beállítástól függetlenül a folyamat két alapvető lépést foglal magában: létrehoz egy nyilvános/titkos kulcspárt, és aláírja a nyilvános kulcsot egy tanúsítványba.

![Folyamat X509CA tanúsítványok létrehozásához](./media/iot-hub-x509ca-concept/csr-flow.png)

A lépések elvégzésének részletei a különböző szolgáltatóktól eltérőek.

### <a name="purchasing-an-x509-ca-certificate"></a>X.509 hitelesítésszolgáltatói tanúsítvány vásárlása

Hitelesítésszolgáltatói tanúsítvány vásárlása azzal az előnnyel jár, hogy egy jól ismert legfelső szintű hitelesítésszolgáltató megbízható harmadik félként szolgál, hogy az eszközök csatlakoztatásakor tanúsítsa az IoT-eszközök legitimitását. Az X vállalat akkor választaná ezt a lehetőséget, ha a Smart-X-Widget az IoT Hubhoz való első kapcsolódásután harmadik fél termékeivel vagy szolgáltatásaival kíván együttműködni.

Az X.509 hitelesítésszolgáltatói tanúsítvány megvásárlásához az X vállalat főtanúsítvány-szolgáltatót választana. A "Root CA" kifejezés internetes keresése jó vezet. A legfelső szintű hitelesítésszolgáltató ismerteti az X vállalatot a nyilvános/titkos kulcspár létrehozásában, valamint a tanúsítvány-aláírási kérelem (CSR) létrehozásához a szolgáltatásaikhoz. A CSR a hitelesítésszolgáltatótól származó tanúsítvány kérelmezésének hivatalos folyamata. A vásárlás eredménye egy tanúsítvány, amelyet hatósági tanúsítványként használhat. Tekintettel az X.509 tanúsítványok elterjedtségére, a tanúsítvány valószínűleg megfelelően lett formázva az IETF RFC 5280 szabványára.

### <a name="creating-a-self-signed-x509-ca-certificate"></a>Önaláírt X.509 hitelesítésszolgáltatói tanúsítvány létrehozása

Az Önaláírt X.509 hitelesítésszolgáltatói tanúsítvány létrehozásának folyamata hasonló a vásárláshoz, kivéve egy harmadik fél aláíróját, például a legfelső szintű hitelesítésszolgáltatót. A példában a Company-X a legfelső szintű hitelesítésszolgáltató helyett a hatósági tanúsítványt írja alá. Az X vállalat ezt a lehetőséget választhatja a teszteléshez, amíg készen nem állnak a hatósági tanúsítvány megvásárlására. A Company-X egy önaláírt X.509 hitelesítésszolgáltatói tanúsítványt is használhat éles környezetben, ha a Smart-X-Widget nem kíván csatlakozni az IoT Hubon kívüli harmadik fél szolgáltatásaihoz.

## <a name="register-the-x509-certificate-to-iot-hub"></a>Az X.509-es tanúsítvány regisztrálása az IoT Hubba

A Vállalat-X-nek regisztrálnia kell az X.509 hitelesítésszolgáltatót az IoT Hubhoz, ahol a Smart-X-Widgetek hitelesítésére szolgál, amikor csatlakoznak. Ez egy egyszeri folyamat, amely lehetővé teszi a képességét, hogy hitelesítse és kezelje tetszőleges számú Smart-X-Widget eszközök. Ez a folyamat egyszeri alkalom a hatósági tanúsítvány és az eszközök közötti egy-a-többhöz kapcsolat miatt, és az X.509 hitelesítésszolgáltató hitelesítési módszerének egyik fő előnye is. A másik lehetőség, hogy feltölt egyéni tanúsítvány ujjlenyomatok minden egyes Smart-X-Widget eszköz ezáltal növelve a működési költségeket.

Az X.509 hitelesítésszolgáltatói tanúsítvány regisztrálása kétlépésből áll, a tanúsítvány feltöltése és a tanúsítvány birtoklásigazolása.

![X509CA tanúsítvány regisztrálása](./media/iot-hub-x509ca-concept/pop-flow.png)

### <a name="x509-ca-certificate-upload"></a>X.509 hitelesítésszolgáltatói tanúsítvány feltöltése

Az X.509 hitelesítésszolgáltatói tanúsítvány feltöltési folyamata csak azt, hogy töltse fel a hitelesítésszolgáltatói tanúsítványt az IoT Hubra. Az IoT Hub a tanúsítványt egy fájlban várja. A Company-X egyszerűen feltölti a tanúsítványfájlt. A tanúsítványfájl semmilyen körülmények között nem tartalmazhat személyes kulcsokat. A nyilvános kulcsú infrastruktúrára (PKI) vonatkozó szabványok bevált gyakorlatai előírják, hogy a Vállalat-X magánjellegű ismeretei ebben az esetben kizárólag az X vállalaton belül találhatók.

### <a name="proof-of-possession-of-the-certificate"></a>A tanúsítvány birtoklásának igazolása

Az X.509 hitelesítésszolgáltatói tanúsítvány, csakúgy, mint bármely digitális tanúsítvány, nyilvános információ, amely alkalmas a lehallgatásra. Mint ilyen, egy hallgatózás elkaphat egy tanúsítványt, és megpróbálhatja feltölteni, mint a saját. A példában az IoT Hub győződjön meg arról, hogy a CA tanúsítvány Company-X feltöltése valóban tartozik a Vállalat-X. Ezt úgy éri el, hogy kihívást jelent a Company-X-nek annak bizonyítására, hogy valójában rendelkeznek a bizonyítvánnyal a [birtoklás igazolása (PoP) áramlásán](https://tools.ietf.org/html/rfc5280#section-3.1)keresztül. A proof-of-possession flow magában foglalja az IoT Hub generál egy véletlen számot kell aláírni a Company-X a személyes kulcs használatával. Ha a Vállalat-X követte a PKI bevált módszereit, és megvédte a személyes kulcsát, akkor csak ők lennének abban a helyzetben, hogy helyesen reagáljanak a birtoklásigazolási kihívásra. Az IoT Hub folytatja az X.509 hitelesítésszolgáltatói tanúsítvány regisztrálását a birtoklásigazolási kihívás sikeres válasza után.

Az IoT Hub birtoklásigazolási kihívására adott sikeres válasz befejezi az X.509 hitelesítés- és hitelesítés- és hitelesítésregisztrációs műveletet.

## <a name="sign-devices-into-a-certificate-chain-of-trust"></a>Eszközök aláírása megbízhatósági tanúsítványláncba

Az IoT-nek minden eszköznek egyedi identitással kell rendelkeznie. Ezek az identitások a tanúsítványalapú hitelesítési sémák tanúsítványainak formájában találhatók. A példánkban ez azt jelenti, hogy minden Smart-X-Widgetnek egyedi eszköztanúsítvánnyal kell rendelkeznie. Hogyan áll be ehhez a Company-X az ellátási láncában?

Az egyik módja ennek az, hogy előre létrehoz tanúsítványok Smart-X-Widgets és bízza ismerete megfelelő egyedi eszköz személyes kulcsok ellátási lánc partnerekkel. A Company-X esetében ez azt jelenti, hogy a Factory-Y-t és a Technician-Z-t bízza meg. Bár ez egy érvényes módszer, jön a kihívásokat, hogy le kell küzdeni, hogy biztosítsák a bizalmat az alábbiak szerint:

1. Az eszközszemélyes kulcsok megosztása az ellátásilánc-partnerekkel, amellett, hogy figyelmen kívül hagyja a PKI bevált módszereit a személyes kulcsok megosztásának sohanem történő megosztására, költségessé teszi az ellátási láncmegbízhatóság kiépítését. Ez azt jelenti, hogy olyan tőkerendszereket kell telepíteni, mint például a biztonságos szobák az eszköz személyes kulcsainak elhelyezésére, és olyan folyamatokat kell telepíteni, mint az időszakos biztonsági auditok. Mindkettő növeli az ellátási lánc költségeit.

2. Az ellátási láncban lévő eszközök biztonságos számlázása és későbbi kezelése az üzembe helyezéssorán egy-az-egyhez feladatlesz minden kulcs-eszköz pár számára az eszköz egyedi tanúsítványának (így a személyes kulcs) létrehozásától az eszköz-kivonásig. Ez kizárja az eszközök csoportkezelését, kivéve, ha a csoportok fogalma valamilyen módon kifejezetten be van építve a folyamatba. A biztonságos könyvelés és az eszköz életciklus-kezelése ezért súlyos működési teherré válik. A mi példánkban az X vállalat viselné ezt a terhet.

Az X.509 hitelesítésszolgáltatói tanúsítvány hitelesítése elegáns megoldásokat kínál a fent felsorolt kihívásokra a tanúsítványláncok használatával. A tanúsítványlánc egy köztes hitelesítésszolgáltatót aláíró hitelesítésszolgáltatóból származik, amely viszont aláír egy másik köztes hitelesítésszolgáltatót, és így addig folytatódik, amíg egy végső köztes hitelesítésszolgáltató alá nem írja az eszközt. A mi példánkban a Company-X aláírja a Factory-Y-t, ami viszont aláírja a Technikus-Z-t, amely végül aláírja a Smart-X-Widget-t.

![Tanúsítványlánc-hierarchia](./media/iot-hub-x509ca-concept/cert-chain-hierarchy.png)

A láncban lévő tanúsítványok kaszkádja a hatóság logikus átadását mutatja be. Számos ellátási lánc követi ezt a logikus átadást, amelynek során minden köztes hitelesítésszolgáltató belesz jelentkezve a láncba, miközben megkapja az összes upstream hitelesítésszolgáltatói tanúsítványt, és az utolsó köztes hitelesítésszolgáltató végül aláírja az egyes eszközöket, és beadja az összes hatósági tanúsítványt a láncból a készülékbe. Ez gyakori, ha a szerződéses gyártó cég a hierarchia gyárak jutalékok egy adott gyár, hogy nem a gyártás. Bár a hierarchia több szint mély lehet (például földrajzi/terméktípus/gyártási vonal szerint), csak a végén lévő gyár lép kölcsönhatásba az eszközzel, de a lánc a hierarchia tetejéről marad fenn.

Az alternatív láncok különböző köztes hitelesítésszolgáltatóval rendelkezhetnek, amelyek akkor lépnek kapcsolatba az eszközzel, amely esetben az eszközzel interakcióba lépő hitelesítésszolgáltató ezen a ponton tanúsítványlánc-tartalmat injektál. Hibrid modellek akkor is lehetségesek, ha a hitelesítéstechnikai együttműködésnek csak egy része rendelkezik fizikai interakcióval az eszközzel.

A mi példánkban, mind a Factory-Y és technikus-Z kölcsönhatásba lépnek a Smart-X-Widget. Míg a Company-X tulajdonosa Smart-X-Widget, ez valójában nem fizikailag kölcsönhatásba lépnek vele a teljes ellátási láncban. A tanúsítvány lánc bizalom Smart-X-Widget ezért magában Company-X aláírása Factory-Y ami viszont jelek Technikus-Z, hogy majd a végső aláírást Smart-X-Widget. A Gyártás és bevezetés -ból Smart-X-Widget áll Factory-Y és Technikus-Z használ -uk megfelelő közbülső CA igazolások -hoz jel mindegyik és mind Szúró-X-Fütyülőréce. A teljes folyamat végeredménye a Smart-X-Widgets, amely egyedi eszköztanúsítványokkal és megbízhatósági lánccal rendelkezik, és felmegy a Company-X hitelesítésszolgáltatói tanúsítványra.

![Az egyik vállalat tanúsítványaitól egy másik vállalat tanúsítványaiig tartó bizalmi lánc](./media/iot-hub-x509ca-concept/cert-mfr-chain.png)

Ez egy jó pont, hogy vizsgálja felül az értéke az X.509 hitelesítésnél módszer. Ahelyett, hogy előre generálna és átadná a tanúsítványokat minden Smart-X-Widget-hez az ellátási láncba, a Company-X-nek csak egyszer kellett aláírnia a Factory-Y-t. Ahelyett, hogy minden eszközt nyomon kellene követnie az eszköz életciklusa során, a Company-X most már nyomon követheti és kezelheti az eszközöket olyan csoportokon keresztül, amelyek természetesen kiemelkednek az ellátási lánc folyamatából, például a Technikus-Z által néhány év júliusa után telepített eszközök.

Végül, de nem utolsósorban, a hitelesítésszolgáltató hitelesítési módszere biztonságos elszámoltathatóságot biztosít az eszközgyártási ellátási láncban. A tanúsítványlánc-folyamat miatt a lánc minden tagjának tevékenysége kriptográfiailag rögzítésre kerül és ellenőrizhető.

Ez a folyamat bizonyos feltételezéseken alapul, amelyeket a teljesség hez fel kell bukkanni. Az eszköz egyedi nyilvános/titkos kulcspárjának független létrehozását igényli, és a személyes kulcsot az eszközön belül védeni kell. Szerencsére, biztonságos szilícium chipek formájában Hardver Secure Modules (HSM) képes belsőleg generáló kulcsok és védelme titkos kulcsok léteznek. A Company-X-nek csak egy ilyen chipet kell hozzáadnia a Smart-X-Widget komponensanyag-számlájához.

## <a name="device-connection"></a>Eszközkapcsolat

A fenti korábbi szakaszok az eszközkapcsolatra épülnek. Ha egyszer egyszerűen regisztrál egy X.509-es hitelesítésszolgáltatói tanúsítványt az IoT Hubra, hogyan csatlakozhat potenciálisan több millió eszköz, és hogyan lehet hitelesíteni őket az első alkalommal?  Egyszerű; ugyanazon a tanúsítványfeltöltési és -igazolási folyamaton keresztül, amellyel korábban találkoztunk az X.509 hitelesítésszolgáltatói tanúsítvány regisztrálásával.

Az X.509 hitelesítésszolgáltatói hitelesítéshez gyártott eszközök egyedi tanúsítvánnyal és a megfelelő gyártási ellátási láncból származó tanúsítványlánccal vannak felszerelve. Eszköz kapcsolat, még a legelső alkalommal, történik egy kétlépéses folyamat: tanúsítvány lánc feltöltése és proof-of-birtoklás.

A tanúsítványlánc feltöltése során az eszköz feltölti az eszköz egyedi tanúsítványát a tanúsítványláncban az IoT Hubra telepített tanúsítványlánccal együtt. Az előre regisztrált X.509 hitelesítésszolgáltatói tanúsítvány használatával az IoT Hub kriptográfiailag ellenőrizhet néhány dolgot, hogy a feltöltött tanúsítványlánc belsőleg konzisztens-e, és hogy a láncot az X.509 hitelesítésszolgáltatói tanúsítvány érvényes tulajdonosa hozta-e meg. Csak volt az X.509 hitelesítésszolgáltató regisztrációs folyamat, IoT Hub kezdeményezne egy proof-of-birtokában kihívás-válasz folyamat annak megállapítására, hogy a lánc és így az eszköz tanúsítvány ténylegesen tartozik az eszköz feltöltése. Ezt úgy éri el, hogy létrehoz egy véletlenszerű kihívást, amelyet az eszköz a személyes kulcsával ír alá az IoT Hub általi érvényesítéshez. A sikeres válasz elindítja az IoT Hub ot, hogy fogadja el az eszközt hitelesként, és adja meg a kapcsolatot.

A példánkban minden Smart-X-Widget feltölti az eszköz egyedi tanúsítványát a Factory-Y és a Technician-Z X.509 hitelesítésszolgáltatói tanúsítványokkal együtt, majd válaszol az IoT Hub által a birtoklás igazolása kihívásra.

![Flow egyik cert a másikra, pop kihívás hub](./media/iot-hub-x509ca-concept/device-pop-flow.png)

Figyelje meg, hogy a megbízhatóság alapja a személyes kulcsok, köztük az eszköz személyes kulcsainak védelme. Ezért nem tudjuk eléggé hangsúlyozni a hardveres biztonságos modulok (HSM) formájában található biztonságos szilíciumchipek fontosságát az eszköz személyes kulcsainak védelmében, és azt az általános legjobb gyakorlatot, hogy soha ne osszunk meg személyes kulcsokat, mint például az egyik gyár, amely egy másikat bíz meg a személyes kulcsot.