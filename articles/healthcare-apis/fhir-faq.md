---
title: Az Azure-beli FHIR-szolgáltatásokkal kapcsolatos gyakori kérdések – Azure API for FHIR
description: Válaszok a FHIR készült Azure API-val kapcsolatos gyakori kérdésekre, például a FHIR API-k mögötti adattárolási helyhez és a verziók támogatásához.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: af891935fd474e6f1f83ff1c2ce56ef71cd065c6
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86536725"
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


## <a name="iot-connector-preview"></a>IoT-összekötő (előzetes verzió)

### <a name="what-is-iomt"></a>Mi az a IoMT?
A IoMT az orvosi eszközök internetes hálózata, és az olyan IoT-eszközök kategóriája, amelyek az állapot-és wellness-szolgáltatásokat más egészségügyi informatikai rendszerekkel együtt, hálózaton keresztül rögzítik és cserélik. Néhány példa a IoMT-eszközökre: fitnesz és klinikai wearables, monitorozási érzékelők, tevékenység-követési funkciók, vagy akár egy intelligens pirula.

### <a name="how-many-iot-connectors-do-i-need"></a>Hány IoT-összekötőre van szükségem?
Egyetlen IoT-összekötő használatával nagy mennyiségű különböző típusú eszközről lehet adatot befogadni. A következő okokból is dönthet úgy, hogy különböző összekötőket használ:
- **Skála**: a nyilvános előzetes verzió esetében a IoT-összekötő erőforrás-kapacitása rögzített, és a várt érték körülbelül 200 üzenet másodpercenkénti átviteli sebessége. Ha nagyobb átviteli sebességre van szükség, további IoT-összekötőket vehet fel.
- **Eszköz típusa**: beállíthatja, hogy külön IoT-összekötőt állítson be minden egyes IoMT-eszközhöz.

### <a name="is-there-a-limit-on-number-of-iot-connectors-during-public-preview"></a>A nyilvános előzetes verzióban korlátozott a IoT-összekötők száma?
Igen, csak két IoT-összekötőt hozhat létre előfizetésben, miközben a funkció nyilvános előzetes verzióban érhető el. Ez a korlát azért van, hogy megakadályozza a váratlan költségeket, mivel a funkció ingyenesen elérhető az előzetes verzióban. Kérelem esetén ez a korlát legfeljebb öt IoT-összekötőre növelhető.

### <a name="what-azure-regions-iot-connector-feature-is-available-during-public-preview"></a>Milyen Azure Regions IoT-összekötő szolgáltatás érhető el a nyilvános előzetes verzióban?
Az IoT-összekötő minden olyan Azure-régióban elérhető, ahol elérhető az Azure API a FHIR.

### <a name="can-i-configure-scaling-capacity-for-iot-connector"></a>Konfigurálható a IoT-összekötő méretezési kapacitása?
Mivel a IoT-összekötő díjmentesen használható a nyilvános előzetes verzióban, a méretezési kapacitása rögzített és korlátozott. A nyilvános előzetes verzióban elérhető IoT-összekötő-konfiguráció várhatóan körülbelül 200 üzenet másodpercenkénti átviteli sebességét biztosítja. Az erőforrás-kapacitás konfigurációjának valamilyen formája elérhetővé válik az általános elérhetőségen (GA).

### <a name="what-fhir-version-does-iot-connector-support"></a>Milyen FHIR-verziót támogat a IoT Connector?
Az IoT-összekötő jelenleg csak az R4-es FHIR-verziót támogatja. Ezért ez a funkció csak az Azure API FHIR-hoz készült R4-példányain látható, a Microsoft azonban jelenleg nem tervezi a STU3 verziójának támogatását.

### <a name="why-cant-i-install-iot-connector-when-private-link-is-enabled-on-azure-api-for-fhir"></a>Miért nem tudom telepíteni a IoT-összekötőt, ha a FHIR-hez készült Azure API-ban engedélyezve van a privát hivatkozás?
A IoT-összekötő jelenleg nem támogatja a privát kapcsolati funkciót. Ezért ha a FHIR-hez készült Azure API-ban engedélyezve van a privát hivatkozás, nem telepítheti a IoT-összekötőt, és fordítva. Ez a korlátozás várhatóan megszűnik, ha az IoT-összekötő általánosan elérhető (GA) verzióban érhető el.

### <a name="whats-the-difference-between-the-open-source-iomt-fhir-connector-for-azure-and-iot-connector-feature-of-azure-api-for-fhir-service"></a>Mi a különbség a nyílt forráskódú IoMT FHIR-összekötő és az Azure API IoT-összekötője között a FHIR szolgáltatáshoz?
Az IoT-összekötő az Azure-hoz készült nyílt forráskódú IoMT FHIR-összekötő üzemeltetett és felügyelt verziója. A felügyelt szolgáltatásban a Microsoft biztosítja az összes karbantartást és frissítést.

Ha a IoMT FHIR-összekötőt az Azure-hoz futtatja, közvetlen hozzáférést biztosít a mögöttes erőforrásokhoz. Azonban Ön is felelős a kiszolgáló karbantartásához és frissítéséhez, valamint az összes szükséges megfelelőségi munkához, ha a PHI-adatokat tárolja.

Fejlesztési szempontból minden funkció üzembe kerül az Azure nyílt forráskódú IoMT FHIR-összekötője számára. A nyílt forráskódú hitelesítés után a rendszer az Azure API FHIR Service-hez készült IoT-összekötő szolgáltatását fogja felszabadítani. A nyílt forráskódú és a Pásti kiadás közötti idő a szolgáltatás összetettségét és az egyéb közúti Térkép prioritásait határozza meg.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben elolvasta a FHIR készült Azure API-val kapcsolatos gyakori kérdéseket. További információ a FHIR Server for Azure támogatott szolgáltatásairól:
 
>[!div class="nextstepaction"]
>[Támogatott FHIR funkciók](fhir-features-supported.md)