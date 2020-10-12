---
title: Az Azure-beli FHIR-szolgáltatásokkal kapcsolatos gyakori kérdések – Azure API for FHIR
description: Válaszok a FHIR készült Azure API-val kapcsolatos gyakori kérdésekre, például a FHIR API-k mögötti adattárolási helyhez és a verziók támogatásához.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 08/03/2020
ms.author: matjazl
ms.openlocfilehash: 35b59fb0583911b5b9faee96276d1bb09a8d6679
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91269709"
---
# <a name="frequently-asked-questions-about-the-azure-api-for-fhir"></a>A FHIR készült Azure API-val kapcsolatos gyakori kérdések

## <a name="azure-api-for-fhir"></a>Azure API for FHIR

### <a name="what-is-fhir"></a>Mi az a FHIR?
A gyors egészségügyi együttműködési erőforrások (FHIR – ejtsd: "Fire") egy együttműködési szabvány, amely lehetővé teszi az egészségügyi információk különböző egészségügyi rendszerek közötti cseréjét. Ezt a standardot a HL7-szervezet fejlesztette ki, és az egészségügyi szervezetek az egész világon elfogadják. A FHIR legújabb verziója az R4 (4. kiadás). A FHIR készült Azure API támogatja az R4-et, és az előző verzió STU3 is támogatja (a standard próbaverziós használatra 3). A FHIR kapcsolatos további információkért látogasson el a [HL7.org](http://hl7.org/fhir/summary.html)webhelyre.

### <a name="is-the-data-behind-the-fhir-apis-stored-in-azure"></a>Az Azure-ban tárolt FHIR API-k mögött vannak az adathalmazok?

Igen, az Azure-ban felügyelt adatbázisok tárolják az adattárolást. A FHIR készült Azure API nem biztosít közvetlen hozzáférést a mögöttes adattárhoz.

### <a name="what-identity-provider-do-you-support"></a>Milyen identitás-szolgáltatót támogat?

Jelenleg az identitás-szolgáltatóként támogatja a Microsoft Azure Active Directory.

### <a name="what-fhir-version-do-you-support"></a>Milyen FHIR-verziót támogat?

A 4.0.0 és az 3.0.1-es verziókat a FHIR (FHIR-kiszolgáló) Azure API-ra és az Azure-ra (nyílt forráskód) is támogatja.

Részletekért lásd: [támogatott szolgáltatások](fhir-features-supported.md). Olvassa el, hogy mi változott a verziók között a [HL7 FHIR verziójának korábbi](https://hl7.org/fhir/R4/history.html)verzióiban.

### <a name="whats-the-difference-between-the-open-source-microsoft-fhir-server-for-azure-and-the-azure-api-for-fhir"></a>Mi a különbség a nyílt forráskódú Microsoft FHIR Server for Azure és az Azure API for FHIR között?

A FHIR készült Azure API az Azure-beli nyílt forráskódú Microsoft FHIR-kiszolgáló üzemeltetett és felügyelt verziója. A felügyelt szolgáltatásban a Microsoft biztosítja az összes karbantartást és frissítést. 

Ha a FHIR-kiszolgálót az Azure-hoz futtatja, közvetlen hozzáférést biztosít a mögöttes szolgáltatásokhoz. Azonban Ön is felelős a kiszolgáló karbantartásához és frissítéséhez, valamint az összes szükséges megfelelőségi munkához, ha a PHI-adatokat tárolja.

Fejlesztési szempontból minden funkció üzembe kerül az Azure nyílt forráskódú Microsoft FHIR-kiszolgálója számára. A nyílt forráskódú hitelesítés után a rendszer felveszi a FHIR-megoldáshoz tartozó, a Pásti Azure API-ra. A nyílt forráskódú és a Pásti kiadás közötti idő a szolgáltatás összetettsége és az ütemterv egyéb prioritásaitól függ. 

### <a name="what-is-smart-on-fhir"></a>Mi a FHIR intelligens?

Az intelligens (behelyettesíthető orvosi alkalmazások és újrafelhasználható technológia) a FHIR-ben olyan Nyílt specifikációk összessége, amelyek a FHIR-kiszolgálókkal és más egészségügyi informatikai rendszerekkel, például az elektronikus egészségügyi adatokkal és az állapotadatok cseréjével integrálják a partneri alkalmazásokat. Egy intelligens on FHIR alkalmazás létrehozásával gondoskodhat arról, hogy az alkalmazás elérhető legyen, és számos különböző rendszerből is kihasználható legyen.
Hitelesítés és Azure API a FHIR-hez. Ha többet szeretne megtudni az intelligens szolgáltatásról, látogasson el az [intelligens állapotba](https://smarthealthit.org/).

### <a name="can-i-create-a-custom-fhir-resource"></a>Létrehozhatok egyéni FHIR-erőforrásokat?

Nem engedélyezzük az egyéni FHIR-erőforrások használatát. Ha egyéni FHIR-erőforrásra van szüksége, hozzon létre egy egyéni erőforrást az [alapszintű erőforráshoz](http://www.hl7.org/fhir/basic.html) a bővítmények használatával. 

### <a name="are-extensions-supported-on-azure-api-for-fhir"></a>Támogatottak-e a [bővítmények](https://www.hl7.org/fhir/extensibility.html) a FHIR készült Azure API-ban?

Lehetővé tesszük, hogy bármilyen érvényes FHIR JSON-adatkészletet töltsön be a-kiszolgálóra. Ha a bővítményt definiáló struktúrát szeretné tárolni, akkor azt struktúra-definíciós erőforrásként mentheti. Jelenleg nem kereshet bővítményeket.

### <a name="what-is-the-limit-on-_count"></a>Mi a korlát a _count?

A jelenlegi korlát értéke 100.

### <a name="are-there-any-limitations-on-the-group-export-functionality"></a>Vannak korlátozások a csoportos exportálási funkciókhoz?

A csoportos exportálás esetében csak a csoporton belül található hivatkozásokat exportáljuk, nem a [csoport erőforrásainak](https://www.hl7.org/fhir/group.html)minden jellemzőjét.

### <a name="can-i-post-a-bundle-to-the-azure-api-for-fhir"></a>Küldhetek egy köteget az Azure API FHIR?

Jelenleg támogatjuk a [Batch-csomagok](https://www.hl7.org/fhir/valueset-bundle-type.html) közzétételét, de nem támogatják a tranzakciós kötegek feladását a FHIR készült Azure API-ban. Az SQL által támogatott nyílt forráskódú FHIR-kiszolgálót tranzakciós kötegek küldésére használhatja.

### <a name="how-can-i-get-all-resources-for-a-single-patient-in-the-azure-api-for-fhir"></a>Hogyan szerezhetem be az összes erőforrást egyetlen beteg számára a FHIR készült Azure API-ban?

Az FHIR-hez készült Azure API-ban támogatjuk az [adatrekeszek keresését](https://www.hl7.org/fhir/compartmentdefinition.html) . Ez lehetővé teszi, hogy egy adott beteghez kapcsolódó összes erőforrást lekérje. Vegye figyelembe, hogy a jobb oldali rekesz magában foglalja az összes olyan erőforrást, amely a beteghez kapcsolódik, de maga nem maga a beteg, így a beteg számára is keresnie kell, ha az eredmények között a beteg erőforrásra van szüksége.

Néhány példa erre:

* Beteg/<id>/*
* Beteg//Observation beolvasása <id>
* Beolvassa a beteg/ <id> /Observation? Code = 8302-2

### <a name="where-can-i-see-some-examples-of-using-the-azure-api-for-fhir-within-a-workflow"></a>Hol láthatok néhány példát a FHIR készült Azure API használatára a munkafolyamaton belül?

Az állapotfigyelő architektúrák gyűjteménye az [Health Architecture GitHub oldalán](https://github.com/microsoft/health-architectures)érhető el.

## <a name="azure-iot-connector-for-fhir-preview"></a>Azure IoT-összekötő a FHIR (előzetes verzió)

### <a name="what-is-iomt"></a>Mi az a IoMT?
A IoMT az orvosi eszközök internetes hálózata, és az olyan IoT-eszközök kategóriája, amelyek az állapot-és wellness-szolgáltatásokat más egészségügyi informatikai rendszerekkel együtt, hálózaton keresztül rögzítik és cserélik. Néhány példa a IoMT-eszközökre: fitnesz és klinikai wearables, monitorozási érzékelők, tevékenység-követési funkciók, vagy akár egy intelligens pirula.

### <a name="how-many-azure-iot-connector-for-fhir-preview-do-i-need"></a>Hány Azure IoT-összekötőre van szükség a FHIR (előzetes verzió) számára?
A FHIR * rendszerhez készült egyetlen Azure IoT-összekötő használatával nagy mennyiségű különböző típusú eszközről lehet adatot befogadni. A következő okokból is dönthet úgy, hogy különböző összekötőket használ:
- **Skála**: a nyilvános előzetes verzióhoz az Azure IOT Connector FHIR erőforrás-kapacitásának javítása rögzített, és a várt érték körülbelül 200 üzenet másodpercenkénti átviteli sebessége. Ha nagyobb átviteli sebességre van szükség, hozzáadhat további Azure IoT-összekötőt a FHIR-hez.
- **Eszköz típusa**: beállíthatja, hogy külön Azure IoT-összekötőt állítson be a FHIR-hez minden IoMT-eszközhöz.

### <a name="is-there-a-limit-on-number-of-azure-iot-connector-for-fhir-preview-during-public-preview"></a>Van-e korlátozva a FHIR (előzetes verzió) Azure IoT-összekötője a nyilvános előzetes verzióban?
Igen, csak két Azure IoT-összekötőt hozhat létre a FHIR előfizetésekhez, miközben a funkció nyilvános előzetes verzióban érhető el. Ez a korlát azért van, hogy megakadályozza a váratlan költségeket, mivel a funkció ingyenesen elérhető az előzetes verzióban. Kérelem esetén ez a korlát legfeljebb öt Azure IoT-összekötőt eredményezhet a FHIR számára.

### <a name="what-azure-regions-azure-iot-connector-for-fhir-preview-feature-is-available-during-public-preview"></a>Milyen Azure-régiók érhetők el az Azure IoT Connector for FHIR (előzetes verzió) szolgáltatáshoz a nyilvános előzetes verzióban?
A FHIR készült Azure IoT-összekötő minden olyan Azure-régióban elérhető, ahol elérhető az Azure API a FHIR számára.

### <a name="can-i-configure-scaling-capacity-for-azure-iot-connector-for-fhir-preview"></a>Konfigurálható a méretezési kapacitás az Azure IoT Connectorhoz a FHIR (előzetes verzió) esetében?
Mivel a FHIR készült Azure IoT-összekötő díjmentesen használható a nyilvános előzetes verzióban, a skálázási kapacitása rögzített és korlátozott. A nyilvános előzetes verzióban elérhető FHIR-konfiguráció Azure IoT-összekötője várhatóan körülbelül 200 üzenet másodpercenkénti átviteli sebességét biztosítja. Az erőforrás-kapacitás konfigurációjának valamilyen formája elérhetővé válik az általános elérhetőségen (GA).

### <a name="what-fhir-version-does-azure-iot-connector-for-fhir-preview-support"></a>Milyen FHIR-verziót támogat az Azure IoT Connector a FHIR (előzetes verzió) szolgáltatáshoz?
Az Azure IoT Connector for FHIR jelenleg csak az R4-es FHIR-verziót támogatja. Ezért ez a funkció csak az Azure API FHIR-hoz készült R4-példányain látható, a Microsoft azonban jelenleg nem tervezi a STU3 verziójának támogatását.

### <a name="why-cant-i-install-azure-iot-connector-for-fhir-preview-when-private-link-is-enabled-on-azure-api-for-fhir"></a>Miért nem tudom telepíteni az Azure IoT Connectort a FHIR (előzetes verzió) szolgáltatásra, ha a privát hivatkozás engedélyezve van a FHIR készült Azure API-ban?
A FHIR készült Azure IoT-összekötő jelenleg nem támogatja a privát kapcsolati funkciót. Ezért ha a FHIR-hez készült Azure API-ban engedélyezve van a privát hivatkozás, nem telepítheti az Azure IoT Connectort a FHIR-hez, és fordítva. Ez a korlátozás várhatóan megszűnik, ha a FHIR készült Azure IoT Connector általánosan elérhető (GA).

### <a name="whats-the-difference-between-the-open-source-iomt-fhir-connector-for-azure-and-azure-iot-connector-for-fhir-preview-feature-of-azure-api-for-fhir-service"></a>Mi a különbség az Azure-hoz készült nyílt forráskódú IoMT FHIR-összekötő és az Azure IoT Connector for FHIR (előzetes verzió) szolgáltatásban az Azure API for FHIR szolgáltatás esetében?
A FHIR készült Azure IoT Connector az Azure-hoz készült nyílt forráskódú IoMT FHIR-összekötő üzemeltetett és felügyelt verziója. A felügyelt szolgáltatásban a Microsoft biztosítja az összes karbantartást és frissítést.

Ha a IoMT FHIR-összekötőt az Azure-hoz futtatja, közvetlen hozzáférést biztosít a mögöttes erőforrásokhoz. Azonban Ön is felelős a kiszolgáló karbantartásához és frissítéséhez, valamint az összes szükséges megfelelőségi munkához, ha a PHI-adatokat tárolja.

Fejlesztési szempontból minden funkció üzembe kerül az Azure nyílt forráskódú IoMT FHIR-összekötője számára. A nyílt forráskódú hitelesítés után a rendszer az Azure API FHIR Service-hez készült FHIR funkciójának a Pásti Azure IoT-összekötője számára jelenik meg. A nyílt forráskódú és a Pásti kiadás közötti idő a szolgáltatás összetettségét és az egyéb közúti Térkép prioritásait határozza meg.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben elolvasta a FHIR készült Azure API-val kapcsolatos gyakori kérdéseket. További információ a FHIR Server for Azure támogatott szolgáltatásairól:
 
>[!div class="nextstepaction"]
>[Támogatott FHIR funkciók](fhir-features-supported.md)

* A Azure Portal a FHIR készült Azure IoT-összekötő a IoT-összekötő (előzetes verzió) néven ismert.

Az FHIR a HL7 bejegyzett védjegye, amelynek felhasználását a HL7 engedélyezte.