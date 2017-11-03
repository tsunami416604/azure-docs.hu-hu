---
title: "Az Azure IoT Hub X.509 biztonsági fogalmak |} Microsoft Docs"
description: "Koncepció - ismertetése a érték X.509 tanúsítvány IoT eszköz gyártó és hitelesítési tanúsítványait."
services: iot-hub
documentationcenter: .net
author: eustacea
manager: arjmands
editor: 
ms.assetid: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2017
ms.author: eustacea
ms.openlocfilehash: 34d4be431b76d5ba8258d932cb21ed6f4818863e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="conceptual-understanding-of-x509-ca-certificates-in-the-iot-industry"></a>Az IoT-iparág X.509 Hitelesítésszolgáltatói tanúsítványok fogalmi ismertetése

Ez a cikk ismerteti a értéke (X.509 tanúsítvány CA) tanúsítványait az IoT-eszközök gyártási és az IoT Hub-hitelesítés használatával.  Információkkal szolgál az ellátási lánc telepítő, és jelölje ki a előnyeit.

Ez a cikk ismerteti:

* Mik azok a X.509 Hitelesítésszolgáltatói tanúsítványok és azokat hogyan
* Az IoT-központ X.509 Hitelesítésszolgáltatói tanúsítvány regisztrálása
* A gyártási beállításával ellátási lánc hitelesítésszolgáltató X.509-alapú hitelesítés
* Hogyan X.509 hitelesítésszolgáltató aláírt eszközök csatlakoznak-e az IoT hubhoz

## <a name="overview"></a>Áttekintés

X.509 tanúsítvány hitelesítésszolgáltatói (CA) hitelesítés egy módszert az IoT-központot egy metódust, amely jelentősen leegyszerűsíti az identitás létrehozását és életciklus-kezelés a ellátási lánc eszközök hitelesítéséhez.

Az X.509 hitelesítésszolgáltató hitelesítési megkülönböztető attribútum a CA-tanúsítványokat az alárendelt eszközökkel rendelkezik egy-a-többhöz kapcsolat.  Ez a kapcsolat lehetővé teszi a lévő eszközök regisztrálása az IoT-központ történő regisztráció után egy X.509 Hitelesítésszolgáltatói tanúsítvány, egyébként egyedi eszköztanúsítványok előtt el kell előre regisztrált minden eszköz csatlakozni tud-eszköz. Ez egy-a-többhöz kapcsolat emellett leegyszerűsíti az eszköz tanúsítványok életciklus-kezelési műveleteket.

Egy másik fontos a X.509 CA hitelesítési attribútum ellátási lánc logisztikai egyszerűsítése.  Az eszközök biztonságos hitelesítése szükséges, hogy rendelkezik-e minden eszköz egy egyedi titkos kulcs hasonlóan a megbízhatósági kapcsolat alapjaként. A tanúsítvány alapú hitelesítést a titkos kulcs egy titkos kulccsal. Egy tipikus eszköz gyártási folyamata magában foglalja a lépéseket és a jegyektől.  Biztonságosan eszköz titkos kulcsok több őrzői belüli kezelésére és a bizalmi kapcsolat fenntartása nem bonyolult és költséges.  Minden konfigurációelem gondnoka képes legyen egy megbízhatósági kriptográfiai láncba aláíró igazgatástechnikai őket az eszköz a titkos kulcsok helyett hitelesítésszolgáltatók használatával megoldja ezt a problémát.  Minden egyes konfigurációelem gondnoka képes legyen pedig aláírja a megfelelő folyamat lépése a gyártási flow eszközökön.  Általános eredménye egy optimális ellátási lánc a beépített felelős a kriptográfiai megbízhatósági lánc használata.  Érdemes megjegyezni, hogy ez a folyamat a lehető legnagyobb biztonságot eredményez, ha az eszközök védelme az egyedi titkos kulcsok.  Ennek érdekében a Hardver biztonságos modulok (HSM) képesek belső titkos kulcsokhoz, soha nem jelenik meg a világos nap használatát javasoljuk.

Ez a cikk nyújt egy végpont nézet az X.509 hitelesítésszolgáltató hitelesítés ellátási lánc Telepítőtől eszköz kapcsolat használatával elvégzése során használja a valós életben példában megszilárdítani ismertetése.

## <a name="introduction"></a>Bevezetés

A hitelesítésszolgáltató X.509 tanúsítványa nem egy digitális tanúsítványt, amelynek tulajdonosa is alá más tanúsítványokat.  A digitális tanúsítvány X.509 oka az IETF által RFC 5280 standard által előírt standard formázás tanúsítvány megfelel, és nem a hitelesítésszolgáltatótól (CA), mert a tulajdonosa is alá más tanúsítványokat.

X.509 hitelesítésszolgáltató használatának legjobb értendő egy konkrét példában viszonyítva.  Fontolja meg az-X vállalathoz, egy készítő az intelligenskártya-X-Widgeteket szakmai telepítési készült. X vállalathoz outsources gyártási és a telepítés.  Az szerződések gyártó gyári-Y előállításához a intelligenskártya-X-Widgeteket, és technikus-Z szolgáltató telepítéséhez. X vállalathoz kívánja, hogy intelligenskártya-X-Widget közvetlenül érhető el az gyári-Y technikus-z-ig telepítéshez és, hogy az csatlakozik közvetlenül vállalati-x-EK az IoT-központ példányát után X vállalathoz további beavatkozása nélkül. Ahhoz, hogy ez akkor fordulhat elő, az X vállalathoz való automatikus csatlakozás az intelligenskártya-X-Widget rendszerlemez néhány egyszeri telepítő műveletek végrehajtását kell.  A végpont forgatókönyv szem előtt a többi cikkben szerkezete az alábbiak szerint:

* A hitelesítésszolgáltató X.509 tanúsítvány beszerzésére

* Az IoT-központ X.509 Hitelesítésszolgáltatói tanúsítvány regisztrálása

* A megbízható tanúsítványlánc bejelentkezési eszközök

* Kapcsolat eszköz

## <a name="acquire-the-x509-ca-certificate"></a>A hitelesítésszolgáltató X.509 tanúsítvány beszerzésére

X vállalathoz megvásárlásakor egy X.509 Hitelesítésszolgáltatói tanúsítvány egy nyilvános, legfelső szintű hitelesítésszolgáltatótól származó, vagy hozzon létre egyet egy önaláírt folyamatot, lehetősége van.  Egy beállítás attól függően, hogy az alkalmazási forgatókönyv történő optimális lenne.  A beállítás, függetlenül a folyamat terjed ki a két alapvető lépéseit, egy nyilvános/titkos kulcspár, és a nyilvános kulcs jelentkezik be egy tanúsítványt.

![img csr-folyamat](./media/csr-flow.png)

Részletes információt a lépések elvégzéséhez különböző szolgáltatókkal térnek el egymástól.

### <a name="purchasing-an-x509-ca-certificate"></a>Egy X.509 Hitelesítésszolgáltatói tanúsítvány beszerzése

CA-tanúsítvány beszerzése az előnye, hogy egy megbízható harmadik fél az IoT-eszközök a érvényességét s ők felelnek, amikor az eszközök csatlakoznak, amelynek a jól ismert legfelső szintű hitelesítésszolgáltató act rendelkezik. X vállalathoz akkor válassza ezt a beállítást, ha intelligens-X-Widget IoT-központ való kezdeti csatlakozáshoz után a külső gyártótól származó termékek vagy szolgáltatások interaktív kívánják.

Egy X.509 Hitelesítésszolgáltatói tanúsítvány megvásárlásához X vállalathoz válassza a legfelső szintű tanúsítványok services-szolgáltató. Jó érdeklődők eredményez, egy internet keressen rá a "Legfelső szintű hitelesítésszolgáltató" kifejezés helyett szerepel.  A legfelső szintű hitelesítésszolgáltató X vállalathoz létrehozása a nyilvános és titkos kulcsból álló kulcspárt és egy tanúsítvány-aláírási kérelem (CSR) a szolgáltatások létrehozásának módját ismerteti.  A CSR hitelesítésszolgáltatótól származó tanúsítvány kérelmezése formális során a rendszer.  Ez a vásárlás eredménye egy tanúsítvány használható tanúsítvány.  Az x.509 szabványú tanúsítványokban kiterjedő jelenlétét megadott, a tanúsítvány valószínű, hogy tartozó IETF RFC 5280 szabványos a megfelelő formátumú.

### <a name="creating-a-self-signed-x509-ca-certificate"></a>Egy X.509 hitelesítésszolgáltató önaláírt tanúsítvány létrehozása

X.509 hitelesítésszolgáltató önaláírt tanúsítvány létrehozása a folyamat hasonlít kivételével használata esetén például a legfelső szintű hitelesítésszolgáltató egy harmadik féltől származó aláíró megvásárlását. A jelen példában X vállalathoz alá fogja írni a helyett a legfelső szintű hitelesítésszolgáltató tanúsítványát.  X vállalathoz előfordulhat, hogy válassza ezt a beállítást, amíg azok készen áll egy hitelesítésszolgáltatói tanúsítvány vásárlása tesztelési. X vállalathoz is előfordulhat, hogy éles, önaláírt X.509 hitelesítésszolgáltató tanúsítvány használata, ha intelligens-X-Widget nem célja, hogy az IoT Hub kívül bármely harmadik féltől származó szolgáltatásokhoz.

## <a name="register-the-x509-certificate-to-iot-hub"></a>Az IoT hubhoz X.509-tanúsítvány regisztrálása

X vállalathoz IoT-központot, ahol fog szolgálni intelligenskártya-X-Widgeteket hitelesítéséhez, hogy azok valóban kapcsolódnak a X.509 hitelesítésszolgáltató regisztrálni kell.  Ez az egy egyszeri folyamat, amely lehetővé teszi a hitelesítéshez, és tetszőleges számú intelligenskártya-X-Widget eszközök kezelése.  Ez a folyamat egy-a-többhöz tanúsítvány és az eszközök közötti miatt egyszeri, és egyben egyik fő előnye X.509 hitelesítésszolgáltató a hitelesítési módszerrel.  A alternatíva a következő: töltse fel az egyes azon tanúsítvány-ujjlenyomatok minden intelligenskártya-X-Widget eszköz ezáltal hozzáadása a működési költségek.

A X.509 CA-tanúsítvány regisztrálása az egy kétlépéses folyamat, a tanúsítvány feltöltése és a tanúsítvány igazolása-az-birtokában.

![img pop-folyamat](./media/pop-flow.png)

### <a name="x509-ca-certificate-upload"></a>X.509 CA-tanúsítvány feltöltése

A X.509 CA-tanúsítvány feltöltése a folyamatot, hogy csak, a CA-tanúsítvány feltöltése az IoT-központ.  Az IoT-központ vár a tanúsítvány egy fájlba. X vállalathoz egyszerűen feltölti tanúsítványfájlt. A tanúsítvány-fájlja nem kell semmilyen körülmények tartalmaz bármely titkos kulcshoz.  Gyakorlati tanácsok a nyilvánoskulcs-infrastruktúrát (PKI) előírásait arra, hogy a vállalati Tudásbázis-x-EK saját ebben az esetben kizárólag X vállalathoz belül helyezkedik el.

### <a name="proof-of-possession-of-the-certificate"></a>Igazolása-az-birtokában a tanúsítvány

Az X.509 Hitelesítésszolgáltatói tanúsítvány, csakúgy, mint bármely digitális tanúsítvány nyilvános információkat, ki vannak téve a lehallgatás.  Lehallgató, egy tanúsítvány intercept és megpróbálja feltölteni, a saját.  A fenti példában az IoT-központ szeretné győződjön meg arról, hogy a CA-tanúsítvány X vállalathoz tölti fel valóban az X vállalathoz. Így kihívást jelentő munkahelyi-x igazolják, hogy azokat ténylegesen kell rendelkeznie a tanúsítványt tesz egy [igazolása birtokában (PoP) folyamat](https://tools.ietf.org/html/rfc5280#section-3.1). Az igazolás a birtokában a folyamat terjed ki az IoT Hub létrehozása véletlenszerűen vállalati X a titkos kulccsal kell aláírni.  Ha X vállalat nyilvános kulcsokra épülő infrastruktúra ajánlott eljárásokat követte, és a titkos kulccsal védett majd csak akkor lesz megfelelően az igazolás a birtokában a kérdés megválaszolásához helyzetben.  Az IoT-központ továbblép, a sikeres válasz az igazolás a birtokában a kihívás az X.509 CA-tanúsítvány regisztrálása.

A sikeres válasz a igazolása a birtokában a kérdésre az IoT-központ X.509 hitelesítésszolgáltató regisztrációja befejeződött.

## <a name="sign-devices-into-a-certificate-chain-of-trust"></a>A megbízható tanúsítványlánc bejelentkezési eszközök

Az IoT használatához minden eszközt kell rendelkeznie egy egyedi azonosító.  Ezeket az identitásokat az űrlap tanúsítványok tanúsítványalapú hitelesítési sémák szerepelnek.  Ebben a példában ez azt jelenti, hogy minden intelligenskártya-X-Widget rendelkeznie kell az eszköz egyedi tanúsítványt.  X vállalat hogyan nem beállítani a saját ellátási lánc?

Egy nyissa meg a módja előre tanúsítványainak létrehozásához szükséges megfelelő az eszköz egyedi titkos kulcsok ellátási lánc partnerekkel intelligenskártya-X-Widgeteket és igazgatástechnikai ismerete.  Az X vállalathoz Ez azt jelenti, igazgatástechnikai gyári-Y és technikus-z-ig.  Ez nem egy érvényes metódusnevet, azt kell lehet oldani annak biztosítása érdekében az alábbiak szerint megbízhatósági igénylő kihívást tartalmaz:

1. Ajánlott eljárásokat, hogy az eszköz a titkos kulcsok megosztása ellátási lánc partnerek, figyelmen kívül hagyja a nyilvános kulcsokra épülő infrastruktúra mellett kellene soha nem megosztása a titkos kulcsok, elérhetővé válnak a költséges ellátási lánc megbízhatósági kapcsolat létrehozása.  Azt jelenti, hogy a ház eszköz titkos kulcshoz biztonságos helyiségekben például nagy rendszerek és folyamatok például rendszeres biztonsági naplózás telepítve kell lennie.  Az ellátási lánc költség hozzáadása is.

2. Biztonságosan nyilvántartás az ellátási lánc-eszközökhöz, és később a központi telepítés kezelése őket lesz minden eszközre kulcs pár eszközhöz egyedi tanúsítvány (így a titkos kulcs) generálása pontot eszközkivonás egy az egyhez típusú feladat. Ez kizárja a eszközök felügyeleti csoport, kivéve, ha a csoportok fogalmát explicit módon be van építve a folyamat valamilyen módon. Biztonságos számlázási és eszköz életciklus-kezelés, ezért nehéz műveletek terhet válik.  A jelen példában X vállalathoz ezt a terhet kellene szerepelnie.

X.509 CA-tanúsítvány hitelesítése a elegáns megoldásokat kínál, bekövetkeznie attól felsorolt kihívást tanúsítványláncok révén.  Egy hitelesítésszolgáltató, amely pedig a másik közbenső hitelesítésszolgáltató jelentkezik, és így kerül mindaddig, amíg a végső közbenső hitelesítésszolgáltató egy eszköz aláírja a közbenső szintű Hitelesítésszolgáltatókat aláíró tanúsítványlánc eredménye.  A jelen példában X vállalathoz pedig aláírja a finally bejelentkezésekor intelligenskártya-X-Widget technikus Z gyári-Y jelentkezik.

![img-tanúsítvány-lánc-hierarchia](./media/cert-chain-hierarchy.png)

A tanúsítványok a lánc cascade fent megadja a logikai aktuális indító hatóság.  Sok ellátási láncok hajtsa végre a logikai aktuális indító során minden közbenső hitelesítésszolgáltató lekérdezi be van jelentkezve a lánc minden felsőbb szintű CA-tanúsítványok fogadása során, és az utolsó közbenső hitelesítésszolgáltató végül jelentkezik minden eszköz és a lánc minden a hitelesítésszolgáltatói tanúsítványokat beszúrása az eszközbe. Ez akkor közös, ha a szerződés gyártó céget az előállítók hierarchiáját jutalékok egy adott gyári a gyártási elvégzéséhez.  Amikor a hierarchiában lehet, hogy több szintet mély (például földrajzi hely/product típusú/gyártási sor által), csak a gyári végén lekérdezi az eszköz kommunikál, de a lánc megmarad a hierarchia tetején.

Alternatív láncok előfordulhat, hogy az eszközt, ahol az eszköz hitelesítésszolgáltató interakció eset esetében tanúsítványok lánc tartalmát ezen a ponton interakcióba más közbenső hitelesítésszolgáltató.  A hibrid modellek is is előfordulhatnak, ha csak az eszköz fizikai kapcsolata van néhány, a hitelesítésszolgáltató.

A jelen példában gyári-I és a technikus-Z kommunikálni az intelligenskártya-X-Widget.  Amíg X vállalathoz intelligenskártya-X-Widget rendelkezik, valójában nem fizikailag működik együtt, a teljes ellátási lánc.  A tanúsítványlánc megbízható intelligenskártya-X-Widgethez tartozó ezért tartalmazza X vállalathoz gyári-Y, ami viszont jelentkezik az intelligenskártya-X-Widget majd nyújt végső aláírás technikus Z aláírása. A gyártás és intelligenskártya-X-Widget telepítése gyári-Y és a megfelelő köztes Hitelesítésszolgáltatói tanúsítványok intelligenskártya-X-Widgeteket minden aláírásához használt technikus Z áll. A záró a teljes folyamat eredménye intelligenskártya-X-Widgeteket egyedi eszköztanúsítványok és az X vállalathoz Hitelesítésszolgáltatói tanúsítvány Ugrás megbízhatósági láncot.

![img-tanúsítvány-maximális pénzügyi igény-lánc](./media/cert-mfr-chain.png)

Ez a jó pont áttekintheti az X.509 hitelesítésszolgáltató metódus értékét.  X vállalathoz előre generálása és beadása tanúsítványok minden intelligenskártya-X-Widgethez tartozó azokat az ellátási lánc, helyett csak kézi gyári-Y egyszer aláírásához.  Nyomon követheti az összes eszközt, az eszközök életciklusa során, helyett a X vállalathoz is nyomon követése és nem természetes feloldja az ellátási lánc folyamat során, például után néhány év július technikus-Z által telepített eszközök csoportok eszközök felügyeletére.

Utolsó, de nem a hitelesítésszolgáltató a hitelesítési módszer infuses biztonságos elszámolási kötelezettségéről szóló ellátási lánc gyártási eszközbe. A lánc folyamat, mert a lánc minden tag műveletek, dokumentumtitkosítási rögzített és ellenőrizhető.

Ez a folyamat bizonyos feltételek, amelyek a teljesség kell illesztendő támaszkodik.  Az eszköz egyedi nyilvános/titkos kulcspár független létrehozása és, hogy a titkos kulcs az eszközön belüli védendő van szükség.  Szerencsére a képernyőn a Hardver biztonságos modulok (HSM) belső generálása kulcsok és titkos kulcsok védelme képes biztonságos szilícium modulok létezik.  X vállalathoz csak vegyen fel egy ilyen modulok intelligenskártya-X-Widgethez tartozó összetevő anyagjegyzék kell.

## <a name="device-connection"></a>Kapcsolat eszköz

A fenti korábbi szakaszokban rendelkezik eszköz kapcsolathoz lett felépítését.  Ha egyszerűen regisztrálja az IoT-központot egy X.509 Hitelesítésszolgáltatói tanúsítvány egy arra, hogyan potenciálisan eszközök millióira csatlakozás és lekérése az első alkalommal a hitelesített?  Egyszerű; az azonos tanúsítvány feltöltése és igazolása a birtokában a folyamat korábbi merültek fel a X.509 CA-tanúsítvány regisztrálása a.

Az X.509 hitelesítésszolgáltató hitelesítési felszerelt egyedi eszköztanúsítványok és a tanúsítványlánc a megfelelő eszközökre gyártási ellátási lánc.  Eszköz kapcsolat, még a nagyon először történik, egy kétlépéses folyamat: tanúsítvány lánc feltöltés és igazolása a birtokában.

A tanúsítvány lánc feltöltés közben az eszköz feltölti az eszköz egyedi tanúsítvány telepítve vannak az IoT hubhoz tanúsítványlánc együtt.  Az előre regisztrált X.509 CA-tanúsítványt használ, az IoT-központ kriptográfiai ellenőrzéséhez különböző dolgok állíthatók, a feltöltött tanúsítványlánc belsőleg következetes, és a lánc érkezett az X.509 Hitelesítésszolgáltatói tanúsítvány érvényes tulajdonosa.  Az X.509 hitelesítésszolgáltató regisztrációs folyamat során csak volt, az IoT-központ volna elindította egy igazolása a birtokában a kérdés-válasz megállapítani, hogy a lánc, és ezért eszköztanúsítványokat ténylegesen tartozik az eszköz ismét feltölteni a.  Igen, az eszközt a titkos kulcsot használja az IoT hubból érvényesítéshez kell aláírni véletlenszerű kihívást létrehozásával.   A sikeres válasz az IoT-központ fogadja el az eszköz hiteles, és adja meg azt kapcsolati váltja ki.

A fenti példában minden intelligenskártya-X-Widget volna az eszköz gyári-Y és technikus-Z X.509 Hitelesítésszolgáltatói tanúsítványok és egyedi tanúsítvány feltöltése és az IoT-központ az igazolás a birtokában a kérdés majd válaszol.

![img eszköz-pop-folyamat](./media/device-pop-flow.png)

Figyelje meg, hogy bizalmi foundation alapja a titkos kulcsok, beleértve az eszköz a titkos kulcsok védelmében.  Hogy ezért nem kifejezetten elég biztonságos szilícium fontosságát levő az űrlap a hardveres biztonsági modulok (HSM) eszköz titkos kulcsok és a teljes ajánlott eljárás szerint az soha nem megosztó bármely titkos kulcshoz védelmére, például egy gyári igazgatástechnikai egy másik, a titkos kulcs.

