---
title: Az Azure IoT Hub X.509 security fogalmait |} A Microsoft Docs
description: Koncepció - ismertetése értéke X.509 tanúsítvány az IoT device gyártási és hitelesítési tanúsítványait.
author: eustacea
manager: arjmands
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/18/2017
ms.author: eustacea
ms.openlocfilehash: 3c7e1167b3326620863d35cb2d4b07235cbd5517
ms.sourcegitcommit: e89b9a75e3710559a9d2c705801c306c4e3de16c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2019
ms.locfileid: "59571345"
---
# <a name="conceptual-understanding-of-x509-ca-certificates-in-the-iot-industry"></a>Az IoT-iparág x.509-es Hitelesítésszolgáltatói tanúsítványok fogalmi ismertetése

Ez a cikk ismerteti a hitelesítésszolgáltató (CA) tanúsítvány X.509 az IoT device gyártási és az IoT hub-hitelesítés használatával értékét. Információkkal szolgál az ellátási lánc telepítő, és jelölje ki a előnyeit.

Ez a cikk ismerteti:

* Mik azok a x.509-es Hitelesítésszolgáltatói tanúsítványok és, azok

* Az IoT Hub X.509 Hitelesítésszolgáltatói tanúsítvány regisztrálása

* Hogyan állítható be a gyártási ellátási lánc Hitelesítésszolgáltatói X.509-alapú hitelesítés

* Hogyan aláírt x.509-es Hitelesítésszolgáltatói eszközök csatlakoztatása az IoT hubhoz

## <a name="overview"></a>Áttekintés

X.509-hitelesítésszolgáltató (CA) hitelesítés egy olyan megközelítés egy metódust, amely jelentősen leegyszerűsíti az identitás létrehozását és életciklus-kezelés az ellátási lánc az IoT Hub-eszközök hitelesítése.

Egy x.509-es Hitelesítésszolgáltatói hitelesítése megkülönböztető attribútuma egy Hitelesítésszolgáltatói tanúsítványt az alárendelt eszközökkel rendelkezik egy-a-többhöz kapcsolat. A kapcsolat lehetővé teszi tetszőleges számú eszközök regisztrációja az IoT Hub X.509 Hitelesítésszolgáltatói tanúsítvány egyszer regisztrálja, ellenkező esetben eszköz egyedi tanúsítványt kell lennie minden eszközhöz előre regisztrált eszköz csak akkor csatlakozhatnak. Ez egy-a-többhöz kapcsolat is egyszerűsíti a tanúsítványok életciklusra vonatkozó eszközfelügyeleti műveleteknek.

Az x.509-es Hitelesítésszolgáltatói hitelesítés egy másik fontos attribútuma ellátási lánc logisztikai egyszerűsítését. Az eszközök biztonságos hitelesítése szükséges, hogy rendelkezik-e minden eszközhöz egyedi titkos kulcs hasonlóan a megbízhatósági kapcsolat alapjaként. Tanúsítvány alapú hitelesítést a titkos kód a titkos kulcs. Egy tipikus eszközgyártási folyamat magában foglalja a több lépéseket és jegyektől. Biztonságos eszköz titkos kulcsok kezelése több jegyektől között, és a megbízhatóság fenntartása a bonyolult és költséges. Hitelesítésszolgáltatók használatával erre a kérdésre, egy titkosítási megbízhatósági lánc minden gondviselője aláírási igazgatástechnikai őket az eszköz titkos kulcsok helyett. Minden egyes gondviselője viszont eszközök a gyártási folyamat a megfelelő folyamat lépésnél jelentkezik. Általános eredménye egy optimális ellátási lánc a beépített accountability a titkosítási megbízhatósági lánc használatán keresztül. Fontos megjegyezni, hogy ez a folyamat a lehető legnagyobb biztonságot eredményez, ha az eszközök egyedi titkos kulcsaikat védi. Ennek érdekében nyomatékosan a Hardver biztonságos modul (HSM) képesek belsőleg titkos kulcsok számára soha nem látható a világos nap használatát.

Ez a cikk az x.509-es Hitelesítésszolgáltatói hitelesítéssel, az eszközkapcsolat, az ellátási lánc telepítés teljes körű képet nyújt előállítása során használja a való világból bármit például megszilárdulni ismertetése.

## <a name="introduction"></a>Bevezetés

Az x.509-es Hitelesítésszolgáltatói tanúsítvány egy digitális tanúsítványt, amelynek tulajdonosa is alá más tanúsítványokat. A digitális tanúsítvány X.509 azért, mert az IETF RFC 5280 standard által előírt standard formázás tanúsítvány megfelel, és a egy hitelesítésszolgáltató (CA) azért, mert a tulajdonos is alá más tanúsítványokat.

X.509-es Hitelesítésszolgáltatói használatát legjobban értendő egy konkrét példa viszonyítva. Fontolja meg a vállalati-X, egy készítő intelligenskártya-X-widgetek szakmai telepítési tervezve. Az X vállalathoz outsources gyártási és a telepítés. Az intelligenskártya-X-Widgetek, és a technikus a-Z szolgáltató telepítéséhez az előállító-Y gyártó szerződések azt. Az X vállalathoz kívánja, hogy intelligenskártya-X-Widget közvetlenül mobilplatform az előállító-Y technikus a-Z, a telepítéshez és, hogy kapcsolódik közvetlenül a vállalati-x-EK az IoT Hub-példány X vállalat további beavatkozása nélkül a telepítés után. Ahhoz, hogy ez akkor fordulhat elő, az X vállalathoz kell előkészíteni az intelligenskártya-X-Widget automatikus kapcsolat néhány egyszeri beállítás műveletek végrehajtásához. A végpontok közötti forgatókönyv szem előtt ez a cikk további részének szerkezete a következő:

* Az x.509-es Hitelesítésszolgáltatói tanúsítvány beszerzése

* Az IoT Hub X.509 Hitelesítésszolgáltatói tanúsítvány regisztrálása

* A tanúsítvány megbízhatósági láncában bejelentkezési eszközöket

* Eszköz kapcsolat

## <a name="acquire-the-x509-ca-certificate"></a>Az x.509-es Hitelesítésszolgáltatói tanúsítvány beszerzése

Az X vállalathoz X.509 Hitelesítésszolgáltatói tanúsítvány nyilvános legfelső szintű hitelesítésszolgáltatótól vagy hozzon létre egyet egy önaláírt folyamat során lehetősége van. Az egyik lehetőség az alkalmazás forgatókönyvtől függően történő optimális lenne. A beállítást, függetlenül a folyamat vonja maga után két alapvető lépéseit, egy nyilvános/titkos kulcspár létrehozása és a nyilvános kulcs jelentkezik be egy tanúsítványt.

![A folyamat egy X509CA tanúsítványok létrehozásához](./media/iot-hub-x509ca-concept/csr-flow.png)

Hogyan végezheti el ezeket a lépéseket a részletek eltérőek a különböző szolgáltatókkal.

### <a name="purchasing-an-x509-ca-certificate"></a>X.509 Hitelesítésszolgáltatói tanúsítvány vásárlása

A Hitelesítésszolgáltatói tanúsítvány vásárlása rendelkezik előnye, hogy egy jól ismert legfelső szintű hitelesítésszolgáltató act az IoT-eszközök valódiságának s ők felelnek az eszközök megbízható harmadik félként. X vállalat akkor válassza ezt a lehetőséget, ha, amelyeket az intelligenskártya-X-Widget külső gyártótól származó termékek vagy szolgáltatások kommunikáljon az IoT hubhoz a kezdeti kapcsolódást követően.

Egy x.509-es Hitelesítésszolgáltatói tanúsítvány megvásárlása az X vállalathoz válassza a legfelső szintű tanúsítványok services-szolgáltató. Jó érdeklődők előállításához egy internetes keresés a a "Legfelső szintű hitelesítésszolgáltató" kifejezés helyett szerepel. A legfelső szintű hitelesítésszolgáltató X vállalat végigvezeti a nyilvános/titkos kulcspár létrehozása és a egy tanúsítvány-aláírási kérelem (CSR) szolgáltatásaik létrehozása. CSR formális zajlik le egy tanúsítvány egy hitelesítésszolgáltatótól alkalmazása. Ez a vásárlás eredménye egy tanúsítvány használható tanúsítvány. Adja meg a áthatóságának X.509 tanúsítványok, a tanúsítvány valószínű, hogy az IETF RFC 5280 standard a megfelelő formátumú.

### <a name="creating-a-self-signed-x509-ca-certificate"></a>Önaláírt X.509 Hitelesítésszolgáltatói tanúsítvány létrehozása

Önaláírt X.509 Hitelesítésszolgáltatói tanúsítvány létrehozása a folyamat hasonlít vásárlására is érintő egy harmadik féltől származó aláíró legfelső szintű hitelesítésszolgáltató például kivételével. Ebben a példában az X vállalathoz alá fogja írni a helyett a legfelső szintű hitelesítésszolgáltató tanúsítványának. Az X vállalathoz előfordulhat, hogy ezt a lehetőséget, amíg el nem készen áll egy tanúsítvány megvásárlása a teszteléshez. Az X vállalathoz is előfordulhat, hogy használható önaláírt x.509-es Hitelesítésszolgáltatói tanúsítványok éles környezetben, ha intelligens-X-Widget nem célja, hogy csatlakozik az IoT Hub-en kívül bármely harmadik féltől származó szolgáltatások.

## <a name="register-the-x509-certificate-to-iot-hub"></a>Az IoT hub X.509-tanúsítvány regisztrálása

X vállalat kell regisztrálni az x.509-es Hitelesítésszolgáltatói az IoT hubhoz, ahol fog szolgálni intelligenskártya-X-Widgetek hitelesítés, mert csatlakoznak. Ez a egy egyszeri folyamat, amely lehetővé teszi a hitelesítést, és tetszőleges számú intelligenskártya-X-Widget eszközök kezelése. Ez a folyamat egyszeri tanúsítvány és az eszközök közötti egy-a-többhöz kapcsolat miatt, amely egyik fő előnye az x.509-es Hitelesítésszolgáltatói hitelesítési módszerrel is képezi. A tulajdonos alternatív, hogy ezáltal a működési költségek ad hozzá minden intelligenskártya-X-Widget eszközhöz egyedi tanúsítvány ujjlenyomatok feltöltése.

Az x.509-es Hitelesítésszolgáltatói tanúsítvány regisztrálása egy kétlépéses folyamat, a tanúsítvány feltöltése és a tanúsítvány koncepció-az-ekre.

![Egy X509CA tanúsítvány regisztrálása](./media/iot-hub-x509ca-concept/pop-flow.png)

### <a name="x509-ca-certificate-upload"></a>X.509 Hitelesítésszolgáltatói tanúsítvány feltöltése

Az x.509-es Hitelesítésszolgáltatói tanúsítvány feltöltése folyamatban van, amely csak, és töltse fel a hitelesítésszolgáltató tanúsítványát az IoT hubnak. Az IoT Hub vár a tanúsítvány-fájlban. X vállalathoz egyszerűen a tanúsítvány fájlt tölt fel. A tanúsítvány fájl nem kell semmilyen körülmények tartalmaz bármely titkos kulcshoz. Ajánlott eljárások a szabványokat fogalmaz meg a nyilvános kulcsok infrastruktúrája (PKI) előírásoknak, a vállalati Tudásbázis-x-EK privát ebben az esetben kizárólag az X vállalathoz belül található.

### <a name="proof-of-possession-of-the-certificate"></a>A koncepció-az-birtokában a tanúsítvány

Az X.509 Hitelesítésszolgáltatói tanúsítvány, csakúgy, mint bármely digitális tanúsítványt nyilvános információkat, amelyek ki vannak téve a lehallgatás. Mint ilyen lehallgató előfordulhat, hogy intercept egy tanúsítványt, és megpróbálja feltölteni, a saját. Ebben a példában az IoT Hub, győződjön meg arról, hogy a Hitelesítésszolgáltatói tanúsítvány X vállalat van feltöltésével valóban az X vállalathoz, ha. Így kihívást jelentő munkahelyi-x igazolják, hogy valójában rendelkeznek a tanúsítvány keresztül végez el egy [koncepció birtokában (PoP) folyamat](https://tools.ietf.org/html/rfc5280#section-3.1). A koncepció-az-birtokában folyamat maga után vonja az IoT Hub létrehozása egy véletlenszerű szám csak az aláírt vállalati x a titkos kulccsal. Ha X vállalat nyilvános kulcsokra épülő infrastruktúra ajánlott eljárásokat követni, és a titkos kulccsal védett majd csak lennének helyzetben, hogy a koncepció-az-birtokában kihívás megfelelően válaszolnak. Az IoT Hub sikeres válasz, a koncepció-az-birtokában kérdés esetén az X.509 Hitelesítésszolgáltatói tanúsítvány regisztrálása folytatódik.

A koncepció-az-birtokában kihívás, az IoT Hub sikeres válasz befejezi az x.509-es Hitelesítésszolgáltatói regisztrációját.

## <a name="sign-devices-into-a-certificate-chain-of-trust"></a>A tanúsítvány megbízhatósági láncában bejelentkezési eszközöket

IoT minden eszközhöz egyedi azonosítóval rendelkezik a szükséges. Ezeket az identitásokat a tanúsítványalapú hitelesítési sémákat űrlap tanúsítványok találhatók. Ebben a példában ez azt jelenti, hogy minden intelligenskártya-X-Widget rendelkeznie kell egy eszköz egyedi tanúsítványt. X vállalat hogyan nem beállítása ehhez az ellátási lánc?

Egy go erről módja előre a megfelelő eszköz egyedi titkos kulcsok ellátási lánc partnerekkel intelligenskártya-X-Widgetek és igazgatástechnikai ismerete tanúsítványainak előállításához. Az X vállalathoz Ez azt jelenti, gyári-I és a technikus a-Z igazgatástechnikai. Bár ez platnou metodu, azt, amely kell meg lehet oldani annak biztosítása érdekében a következő adatvédelmi kihívások tartalma:

1. Nem kell az eszköz titkos kulcsok megoszthatja az ellátási lánc partnerek, figyelmen kívül hagyja a nyilvános kulcsokra épülő infrastruktúra mellett ajánlott eljárásokat megosztási soha nem a titkos kulcsok, megkönnyíti a költséges ellátási lánc bizalmi kapcsolat létrehozásához. Azt jelenti, hogy az adott eszköz titkos kulcsok biztonságos termek befektetési rendszerek és folyamatok, mint a rendszeres biztonsági naplózás kell telepíteni. Az ellátási lánc költség is hozzá.

2. Biztonságosan elszámolása az ellátási lánc eszközöket, és később kezelnie azokat a központi telepítésben lévő válik minden kulcs az eszközre irányuló eszköz egyedi tanúsítványt (így a titkos kulcsot) generációs pontról eszközkivonás párhoz egy-az-egyhez feladatot. Ez kizárja csoport az eszközök kezelését, kivéve, ha a csoportok fogalmát explicit módon be van építve a folyamat valamilyen módon. Biztonságos számlázás és az eszköz életciklus-kezelés, ezért nehéz műveletek terhet válik. Ebben a példában az X vállalathoz ezt a terhet kellene szerepelnie.

X.509-es Hitelesítésszolgáltatói tanúsítvány alapú hitelesítést, elegáns megoldásokat kínál, bekövetkeznie attól felsorolt ügyféladatoknak tanúsítványláncok kihívásokat. Egy hitelesítésszolgáltató, viszont a közbenső hitelesítésszolgáltató egy másik aláíró, és így kerül, amíg a végső közbenső hitelesítésszolgáltató regisztrál egy eszközt a közbenső Hitelesítésszolgáltatókat aláíró tanúsítványlánc eredménye. Ebben a példában az X vállalathoz viszont aláírja a végül aláírja az intelligenskártya-X-Widget technikus Z gyári-Y aláírja.

![Lánc tanúsítványhierarchiához](./media/iot-hub-x509ca-concept/cert-chain-hierarchy.png)

Lépcsőzetes elrendezés tanúsítványok a lánc fent megadja a logikai aktuális indító hatóság. Számos ellátási láncokat. hajtsa végre a logikai aktuális indító amellyel minden közbenső hitelesítésszolgáltató lekérdezi be van jelentkezve a lánc minden felsőbb szintű CA-tanúsítványok fogadása közben, és az utolsó közbenső hitelesítésszolgáltató végül aláírja az egyes eszközök és a láncból hatóság tanúsítványok beszúrása az eszközbe. Ez a gyakori, ha a szerződés gyártócég előállítók hierarchia megrendeli a gyártási tennie egy adott előállító. Bár lehet, hogy a hierarchia több szinten mély (például földrajzi hely/termék típusa/gyártási sor szerint), csak az előállító végén lekérdezi az eszköz kommunikáljon, de a láncban, kezelik a hierarchia tetején.

Előfordulhat, hogy az alternatív láncok használja, amelyben a hitelesítésszolgáltató használata az eszköz eset kódtárba tanúsítvány láncot tartalom ezen a ponton az eszközt különböző közbenső hitelesítésszolgáltató. A hibrid modellek is is ahol csak néhány, a hitelesítésszolgáltató fizikai interakció az eszközzel rendelkezik.

Ebben a példában gyári-I és a technikus a-Z dolgozhat az intelligenskártya-X-Widget. X vállalat tulajdonában lévő intelligenskártya-X-Widget, amíg azt ténylegesen nem fizikailag működik együtt, a teljes ellátási lánc. A tanúsítványlánc intelligenskártya-X-widget megbízhatósági ezért foglalja X vállalat aláírási gyári-Y, amelyek viszont a végső aláírás majd nyújtson az intelligenskártya-X-Widget technikus Z jelentkezik. A gyártás és intelligenskártya-X-Widget telepítésének gyári-Y és azok megfelelő köztes Hitelesítésszolgáltatói tanúsítványok használatával történő bejelentkeztetési a intelligenskártya-X-Widgetek minden technikus Z áll. A végeredmény a teljes folyamat során az eszköz egyedi tanúsítványokat és eredményezi az X vállalathoz Hitelesítésszolgáltatói tanúsítvány megbízhatósági láncában tanúsítvány intelligenskártya-X-Widgetek.

![Egy vállalati tanúsítványok, a tanúsítványokat egy másik vállalat megbízhatósági lánc](./media/iot-hub-x509ca-concept/cert-mfr-chain.png)

Ez a jó pont, tekintse át az x.509-es Hitelesítésszolgáltatói metódus az értékét. X vállalat helyett előre elkészítése és beadása tanúsítványok minden intelligenskártya-X-widget az ellátási lánc be, csak rendelkezett gyári-Y egyszer jelentkezzen. Nem minden eszköz alatt az eszköz életciklus-nyomon követésére, az X vállalathoz előfordulhat, hogy nyomon követheti és kezelheti a csoportokat, amelyek a természetes módon vet fel az ellátási lánc folyamatába, például telepítette a technikus a-Z júliusban egy év után eszközök eszközét.

Utolsó, de nem a hitelesítésszolgáltató a hitelesítési módszer infuses biztonságos accountability az eszközgyártási ellátási lánc be. A lánc folyamat, mert a műveleteket a lánc minden tag a titkosítási szempontból rögzített, és ellenőrizhető.

Ez a folyamat támaszkodik bizonyos Előfeltételek, amely a teljesség kell jelennek meg. Független eszköz egyedi nyilvános/titkos kulcspár létrehozása és a titkos kulccsal védeni az eszközön belül van szükség. Szerencsére létezik biztonságos szilícium lapkák formájában a Hardver biztonságos modulok (HSM) képes a belső generálása kulcsok és titkos kulcsok védelme. X vállalat csak hozzá kell adnia egy ilyen lapkák intelligenskártya-X-Widget a összetevő anyagjegyzék be.

## <a name="device-connection"></a>Eszköz kapcsolata

A fenti korábbi szakaszokban rendelkezik lett halmoz fel a eszköz kapcsolat. Egyszerűen regisztrálja az IoT Hub-x.509-es Hitelesítésszolgáltatói tanúsítványok egy időben, hogyan potenciálisan több millió eszköz csatlakoztatása és első hitelesítést hajtottak végre az első alkalommal?  Egyszerű; az azonos tanúsítvány feltöltése és a koncepció-az-birtokában folyamat azt korábban észlelt az x.509-es Hitelesítésszolgáltatói tanúsítvány regisztrálása.

Az x.509-es Hitelesítésszolgáltatói hitelesítési egérporttal rendelkeznek, az eszköz egyedi tanúsítványt és egy tanúsítványláncra, valamint saját eszközöket ellátási lánc gyártási. Eszköz kapcsolat, még ha első alkalommal történik, egy kétlépéses folyamat: tanúsítvány láncot feltöltése és a koncepció-az-birtokában.

A tanúsítvány láncot feltöltésekor az eszköz feltölti az eszköz egyedi tanúsítványt a tanúsítványlánc telepítve vannak, az IoT hub együtt. Az előre regisztrált x.509-es Hitelesítésszolgáltatói tanúsítványt használ, az IoT Hub titkosítási szempontból ellenőrizheti néhány dolgot, a feltöltött tanúsítványlánc belsőleg összhangban, és hogy a lánc érkezett az x.509-es Hitelesítésszolgáltatói tanúsítvány érvényes tulajdonosa. Csak az x.509-es Hitelesítésszolgáltatói regisztrációs folyamat segítségével történt, az IoT Hub lenne elindította a koncepció-az-birtokában kérdés-válasz megállapítani, hogy a lánc, és ezért eszköztanúsítványokat ténylegesen tartozik, az eszköz feltölteni. Így létrehoz egy véletlenszerű vonatkozó kérdést állít be az eszközt az IoT Hub által érvényesítési annak titkos kulcsát használatával írja alá hajtja végre. A sikeres válasz IoT hubot, hogy fogadja el az eszköz hiteles, és adja meg azt kapcsolati aktivál.

Ebben a példában minden intelligenskártya-X-Widget gyári-I és a technikus a-Z x.509-es Hitelesítésszolgáltatói tanúsítványok és az eszköz egyedi tanúsítvány feltöltése és megválaszolására a koncepció-az-birtokában kihívás az IoT hubról.

![Áramlását, egy tanúsítvány egy másik, a pop challenge-központból](./media/iot-hub-x509ca-concept/device-pop-flow.png)

Figyelje meg, hogy alapját a megbízhatósági kapcsolat alapja, többek között az eszköz titkos kulcsok, titkos kulcsok védelmében. Hogy ezért nem kifejezetten egy gyári igazgatástechnikai egy másik, elegendő az eszköz titkos kulcsok és a teljes ajánlott soha nem megosztása minden titkos kulcsok védelme érdekében a hardveres biztonsági modulok (HSM) formájában chipeken biztonságos szilícium fontosságát, például a titkos kulcs.