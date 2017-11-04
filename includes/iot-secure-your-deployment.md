# <a name="secure-your-iot-deployment"></a>Az IoT üzemelő példányának védelme
Ez a cikk a következő részletességi biztosít az Azure IoT-alapú az eszközök internetes hálózatát (IoT) infrastruktúra védelmének biztosítása. Hivatkozik szintű részleteket minden összetevőjének telepítése és konfigurálása. Összehasonlítás és a lehetőségek között a különböző versengő módszer is biztosít.

Az Azure IoT-telepítés biztonságossá tétele a következő három biztonsági területeket osztható:

* **Eszközbiztonsági**: biztonságossá tétele az IoT-eszközök, amíg a helyettesítő telepítették.
* **Kapcsolat biztonsági**: bizalmas és hamisíthatatlan biztosítása az IoT-eszközök és az IoT-központ között továbbított összes adat.
* **A felhő biztonsági**: biztonságos adatokat, amikor egy keresztül bonyolítanak, és a felhőben tárolt eszközöket biztosítva.

![Három biztonsági területek][img-overview]

## <a name="secure-device-provisioning-and-authentication"></a>Kiépítés biztonságos eszköz és a hitelesítés
Az Azure IoT Suite az IoT-eszközök az alábbi két módszer biztonságossá tételére:

* Az egyes eszközök ad meg egy egyedi identitáskulcs (biztonsági jogkivonatokat), amelyekkel az eszköz kommunikáljon az IoT-központot.
* A-eszköz segítségével [X.509 tanúsítvány] [ lnk-x509] és titkos kulcsok segítségével hitelesíteni az eszközt az IoT-központ számára. Ez a hitelesítési módszer biztosítja, hogy a titkos kulcs az eszközön nem ismert az eszköz kívül bármikor, így magasabb szintű biztonságot.

A biztonsági token módszer minden hívás által végzett az eszközt az IoT-központ a szimmetrikus kulcsot minden hívás társításával hitelesítést nyújt. X.509-alapú hitelesítés lehetővé teszi, hogy az IoT-eszközök hitelesítési a fizikai rétegben TLS kapcsolat létrehozásának részeként. A biztonsági jogkivonat-alapú módszer az X.509-hitelesítést, amely egy kevésbé biztonságos minta nélkül használható lesz. A két módszerek közötti választást elsősorban határozza meg hogy mennyire vannak biztonságban eszköz hitelesítési kell lennie, és az eszközön (tárolja biztonságos helyen a titkos kulcs) biztonságos tárolási rendelkezésre állását.

## <a name="iot-hub-security-tokens"></a>IoT Hub biztonsági tokenek
Az IoT-központ biztonsági jogkivonatokat használ hitelesítéséhez, eszközöket és szolgáltatásokat lehet, ne küldjön kulcsok a hálózaton. Emellett biztonsági jogkivonatok érvényesség és hatókör korlátozott. Azure IoT SDK-k automatikusan hoz létre jogkivonatokat anélkül, hogy semmiféle speciális beállítást. Bizonyos esetekben azonban a felhasználónak kell hozhat létre és használhat közvetlenül a biztonsági jogkivonatokat. Ezek közé tartozik a MQTT, az AMQP vagy a HTTP-felületek közvetlen használatát, vagy a biztonságijogkivonat-szolgáltatás mintát végrehajtásának.

A biztonsági jogkivonat és a használati szerkezetének további részleteket a következő cikkekben talál:

* [Biztonsági jogkivonat szerkezete][lnk-security-tokens]
* [SAS-tokenje eszközként használatával][lnk-sas-tokens]

Minden egyes IoT-központ rendelkezik egy [identitásjegyzékhez] [ lnk-identity-registry] , amely a a szolgáltatás, például az üzenetsoroktól felhő eszközre üzeneteket tartalmaz várólista eszközönkénti erőforrások létrehozásához és az eszköz felé néző végpontok eléréséhez használható. Az IoT-központ identitásjegyzékhez eszköz identitások és a biztonsági kulcsok megoldás biztonságos tárolására szolgál. Személy vagy eszköz identitások csoportok felveheti egy engedélyezési lista vagy egy tiltólista engedélyezése a teljes felügyeletet gyakorolhat az eszközök elérést. A következő cikkekben további részletekkel szolgálnak az identitásjegyzékhez és a támogatott műveletek struktúra.

[Az IoT-központ például MQTT AMQP vagy HTTP protokollt támogat][lnk-protocols]. Biztonsági jogkivonatokat az IoT-eszközről az IoT hubhoz másképp egyes ezeket a protokollokat használja:

* AMQP: SASL egyszerű és AMQP jogcímalapú biztonsági ({házirendnév}@sas.root. { iothubName} esetén az IoT hub-szintű jogkivonatok; {deviceId} eszköz hatókörű jogkivonatok esetén).
* MQTT: Csatlakozás csomagot használ {deviceId} a {ClientId}, {IoThubhostname} / {deviceId} a a **felhasználónév** mező, és egy SAS-token a a **jelszó** mező.
* HTTP: Érvénytelen lexikális elem a hitelesítési kérelem fejlécében.

Az IoT-központ identitásjegyzékhez segítségével eszközönkénti biztonsági hitelesítő adatok konfigurálása és hozzáférés-vezérlést. Azonban ha egy IoT-megoldás már jelentős befektetési egy [egyéni eszköz identitása beállításjegyzék és/vagy hitelesítési séma][lnk-custom-auth], ezért integrálható az IoT hubbal meglévő infrastruktúra hozzon létre egy jogkivonat-szolgáltatás.

### <a name="x509-certificate-based-device-authentication"></a>X.509 tanúsítvány alapú eszközhitelesítés
Használatát egy [eszközalapú X.509 tanúsítvány] [ lnk-use-x509] és a társított titkos és nyilvános kulcsból álló kulcspárt lehetővé teszi, hogy a további hitelesítési a fizikai rétegben. A titkos kulcs lesz biztonságosan tárolva az eszközt, és nincs felderíthető kívül az eszközt. Az X.509 tanúsítvány információkat az eszköz, például az eszköz-Azonosítóját, és az egyéb szervezeti adatait tartalmazza. A tanúsítvány aláírása hozza létre a titkos kulccsal.

Magas szintű eszköz üzembe helyezési folyamata:

* Társítson egy fizikai eszközön – eszközidentitást és/vagy eszköz gyártási vagy üzembe helyezés során az eszközhöz társított X.509-tanúsítvány azonosítót.
* Hozzon létre egy megfelelő identitás IoT Hub – eszközidentitást és az IoT-központ identitásjegyzékhez társított eszköz információival.
* Biztonságosan tárolja az IoT-központ identitásjegyzékhez X.509 tanúsítvány ujjlenyomata.

### <a name="root-certificate-on-device"></a>Legfelső szintű tanúsítványt az eszközre
Az IoT hubbal biztonságos TLS kapcsolódás közben, az IoT-eszközök hitelesíti az IoT-központ az eszköz SDK részét képező legfelső szintű tanúsítványt használ. A mappában található a tanúsítvány a C ügyfél SDK "\\c\\Tanúsítványos" alatt a tárház gyökérkönyvtárában. Bár a legfelső szintű tanúsítványok hosszú élettartamú, továbbra is lehetséges, hogy lejáratukig vagy vonható vissza. Ha nem tudja frissíteni a tanúsítvány az eszközön, az eszköz nem lehet csatlakozni később az IoT-központ (vagy bármely más felhőalapú szolgáltatás). A azt jelenti, a legfelső szintű tanúsítvány frissítése után az IoT-eszközök telepítve van a kockázat hatékonyan csökkentése.

## <a name="securing-the-connection"></a>A kapcsolat biztonságossá tétele
Az IoT-eszközök és az IoT-központ között internetkapcsolat használatával lett biztonságossá téve a Transport Layer Security (TLS) szabvány. Az Azure IoT támogatja [TLS 1.2][lnk-tls12], TLS 1.1 és TLS 1.0, az itt megadott sorrendben. A TLS 1.0 támogatását a csak a visszamenőleges kompatibilitás érdekében. Javasoljuk, hogy a TLS 1.2 használni, mivel a lehető legnagyobb biztonságot nyújt.

Az Azure IoT Suite támogatja a következő titkosító csomag használatát, az itt megadott sorrendben.

| Titkosítási csomagok | Hossza |
| --- | --- |
| A TLS\_ECDHE\_RSA\_WITH\_AES\_256\_CBC\_SHA384 (0xc028) ECDH secp384r1 (eq. FS 7680 bits RSA) |256 |
| A TLS\_ECDHE\_RSA\_WITH\_AES\_128\_CBC\_SHA-256 (0xc027) ECDH secp256r1 (eq. FS 3072 bits RSA) |128 |
| A TLS\_ECDHE\_RSA\_WITH\_AES\_256\_CBC\_SHA (0xc014) ECDH secp384r1 (eq. FS 7680 bits RSA) |256 |
| A TLS\_ECDHE\_RSA\_WITH\_AES\_128\_CBC\_SHA (0xc013) ECDH secp256r1 (eq. FS 3072 bits RSA) |128 |
| A TLS\_RSA\_WITH\_AES\_256\_GCM\_SHA384 (0x9d) |256 |
| A TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA-256 (0x9c) |128 |
| A TLS\_RSA\_WITH\_AES\_256\_CBC\_SHA-256 (0x3d) |256 |
| A TLS\_RSA\_WITH\_AES\_128\_CBC\_SHA-256 (0x3c) |128 |
| A TLS\_RSA\_WITH\_AES\_256\_CBC\_SHA (0x35 hiba) |256 |
| A TLS\_RSA\_WITH\_AES\_128\_CBC\_SHA (0x2f) |128 |
| A TLS\_RSA\_WITH\_3DES\_EDE\_CBC\_SHA (0xa) |112 |

## <a name="securing-the-cloud"></a>A felhő biztonságossá tétele
Az Azure IoT-központ lehetővé teszi, hogy a definíciója [hozzáférés-vezérlési házirendeket] [ lnk-protocols] minden egyes biztonsági kulcshoz. Hozzáférést biztosít egyes IoT-központok végpontjai használja a következő engedélyekkel vannak beállítva. Az engedélyek korlátozhatják az IoT-központ funkciókon alapulnak.

* **RegistryRead**. Olvasási hozzáférést biztosít az identitásjegyzékhez. További információkért lásd: [identitásjegyzékhez][lnk-identity-registry].
* **RegistryReadWrite**. Biztosít az olvasási és írási hozzáférést biztosít az identitásjegyzékhez. További információkért lásd: [identitásjegyzékhez][lnk-identity-registry].
* **ServiceConnect**. Cloud service irányuló kommunikációra és figyelési végpontok biztosít hozzáférést. Például az engedélyt háttér-felhőszolgáltatások eszközről a felhőbe üzeneteket fogadni, felhőalapú-eszközre küldött üzenetek küldése és a megfelelő kézbesítési visszaigazolások beolvasása.
* **DeviceConnect**. Engedélyezi a hozzáférést a eszköz felé néző végpontok. Például az engedélyt felhő eszközre üzeneteket eszköz a felhőbe küldött üzeneteket küldjön és fogadjon. Ez az engedély eszközöket használják.

Két módon beszerzése **DeviceConnect** IoT hubot engedélyeket [biztonsági jogkivonatokat][lnk-sas-tokens]: egy eszköz identitáskulcs, vagy egy megosztott elérési kulcsot. Fontos továbbá, vegye figyelembe, hogy eszközökhöz elérhető összes funkciót tesz elérhetővé előtaggal rendelkező végpontokon tervezési `/devices/{deviceId}`.

[Szolgáltatás-összetevők csak hozhat létre a biztonsági jogkivonatokat] [ lnk-service-tokens] használatával megosztott hozzáférési házirendeket a megfelelő jogosultságokat.

Azure IoT Hub és egyéb szolgáltatásokat, amelyek a megoldás részét képezhetik engedélyezése az Azure Active Directory használatával felhasználókat kezelését.

Azure IoT-központ által okozhatnak adatokat képes használni a számos olyan szolgáltatásokat, például Azure Stream Analytics és az Azure blob Storage tárolóban. Ezek a szolgáltatások felügyeleti hozzá lehessen férni. További tájékoztatást talál a szolgáltatások és a rendelkezésre álló lehetőségeket az alábbi:

* [Az Azure Cosmos DB][lnk-cosmosdb]: egy méretezhető, teljes mértékben indexelt dokumentumadatbázis-szolgáltatás, amely felügyeli az eszközök metaadatok félig strukturált adatok ellátásához, például az attribútumokat, a konfiguráció és a biztonsági tulajdonságait. Azure Cosmos DB nagy teljesítményű és nagy átviteli feldolgozás, a séma-független indexelő adatokat, és egy részletes SQL-lekérdezési felületet kínál.
* [Az Azure Stream Analytics][lnk-asa]: valós idejű streamfeldolgozó, amely lehetővé teszi, hogy a gyors fejlesztésére és alacsony költségű analytics megoldást nyújt a valós idejű betekintést az eszközök, érzékelőket, infrastruktúra és alkalmazások telepítése a felhőben. A teljes körűen felügyelt szolgáltatás adatainak megőrzése mellett nagy átviteli sebességet, alacsony késéssel és rugalmasság méretezhető, azonosíthatja a kötettel.
* [Az Azure App Services][lnk-appservices]: egy felhőalapú platform hozhat létre hatékony webes és mobilalkalmazások bárhol adatokat; a felhőben, vagy a helyszínen. Vonzó alkalmazások készítése iOS, Android és Windows rendszerre. A szoftver egy szolgáltatott szoftverként (SaaS) és a felhő alapú szolgáltatások több tucatnyi out-of-az-box kapcsolattal rendelkező vállalati alkalmazások és a vállalati alkalmazások integrálása. A kedvenc nyelvi és IDE (.NET, Node.js, PHP, Python vagy Java) hozhat létre webes alkalmazásokat és API-k minden eddiginél gyorsabban kódot.
* [A Logic Apps][lnk-logicapps]: A Logic Apps szolgáltatás az Azure App Service segítségével integrálható a meglévő üzleti rendszereken az IoT-megoldásból és munkafolyamat-folyamatok automatizálása. A Logic Apps segítségével a fejlesztők olyan munkafolyamatokat alakíthatnak ki, amely egy és majd végrehajtanak bizonyos lépéseket – szabályok és műveleteit, amelyekkel az üzleti folyamatok integrálása hatékony összekötők. A Logic Apps kínál a túlnyomó ökoszisztémájának alkalmazásával az SaaS, a felhő alapú out-of-az-box kapcsolat és a helyszíni alkalmazások.
* [Az Azure blob storage][lnk-blob]: az eszközök elküldik a felhőbe adatok megbízható, gazdaságos felhőalapú tárhelyre.

## <a name="conclusion"></a>Összegzés
Ez a cikk áttekintést nyújt az megvalósítási tervezése és telepítése az IoT-infrastruktúrát használó Azure IoT szintű részleteit. Az általános IoT-infrastruktúra védelmének biztosítása a kulcs beállítása az egyes összetevők biztonságos. A tervezési döntések ütköznek azokkal elérhető Azure IoT biztosít bizonyos fokú rugalmasságot és a választott; minden kiválasztott azonban biztonsági hatásai lehetnek. Javasoljuk, hogy ezek mindegyikének lehet értékelni a kockázat/költsége értékelése.

[img-overview]: media/iot-secure-your-deployment/overview.png

[lnk-security-tokens]: ../articles/iot-hub/iot-hub-devguide-security.md#security-token-structure
[lnk-sas-tokens]: ../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app
[lnk-identity-registry]: ../articles/iot-hub/iot-hub-devguide-identity-registry.md
[lnk-protocols]: ../articles/iot-hub/iot-hub-devguide-security.md
[lnk-custom-auth]: ../articles/iot-hub/iot-hub-devguide-security.md#custom-device-authentication
[lnk-x509]: http://www.itu.int/rec/T-REC-X.509-201210-I/en
[lnk-use-x509]: ../articles/iot-hub/iot-hub-devguide-security.md
[lnk-tls12]: https://tools.ietf.org/html/rfc5246
[lnk-service-tokens]: ../articles/iot-hub/iot-hub-devguide-security.md#use-security-tokens-from-service-components
[lnk-cosmosdb]: https://azure.microsoft.com/services/cosmos-db/
[lnk-asa]: https://azure.microsoft.com/services/stream-analytics/
[lnk-appservices]: https://azure.microsoft.com/services/app-service/
[lnk-logicapps]: https://azure.microsoft.com/services/app-service/logic/
[lnk-blob]: https://azure.microsoft.com/services/storage/
