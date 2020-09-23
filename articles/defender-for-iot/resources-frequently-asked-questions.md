---
title: Defender a IoT-hez – gyakori kérdések
description: Válaszok az Azure Defender IoT funkcióinak és szolgáltatásának leggyakrabban feltett kérdéseire.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 97dd4e13754175e9c81ae308b86faae811e4d554
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939756"
---
# <a name="azure-defender-for-iot-frequently-asked-questions"></a>Azure Defender a IoT-hez – gyakori kérdések

Ez a cikk a Defender for IoT szolgáltatással kapcsolatos gyakori kérdések és válaszok listáját tartalmazza.

## <a name="does-azure-provide-support-for-iot-security"></a>Az Azure támogatást nyújt a IoT biztonsághoz?

Az Azure egy integrált nézetet biztosít a IoT-biztonság monitorozásához és kezeléséhez a teljes biztonsági megoldás részeként Azure Security Centeron keresztül. Ha Ön egy alkalmazás fejlesztője, a IoT-alkalmazás biztonságának kezeléséhez IoT Hub nézetet használhatja.

## <a name="what-is-azures-unique-value-proposition-for-iot-security"></a>Mi az Azure egyedi Value IoT biztonsága?

A Defender for IoT lehetővé teszi a vállalatok számára, hogy a meglévő Cyber-biztonsági nézetet a teljes IoT-megoldásra terjesszek. Az Azure teljes körű áttekintést nyújt az üzleti megoldásról, amely lehetővé teszi, hogy üzleti tevékenységeit és döntéseit a vállalati biztonsági helyzet és az összegyűjtött adatok alapján tegye elérhetővé. Az Azure IoT, a Azure IoT Edge és a Azure Security Center együttes biztonsága lehetővé teszi a kívánt megoldás létrehozását a szükséges biztonsággal.

## <a name="who-is-defender-for-iot-made-for"></a>Kik a IoT Defender?

A IoT Defender az Azure IoT Hub Security szolgáltatásban van integrálva, és felügyeletet biztosít a napi üzleti megoldás biztonsági műveletei számára. A IoT Defender a Azure Security Center képességeibe is integrálva van, és integrált nézetet biztosít a IoT biztonság monitorozásához és kezeléséhez a teljes biztonsági megoldás részeként.

## <a name="how-does-defender-for-iot-compare-to-the-competition"></a>Hogyan hasonlítja össze a Defender a IoT a versenyt?

Míg más megoldások olyan képességeket biztosítanak, amelyek lehetővé teszik, hogy az ügyfelek saját megoldásaikat hozzanak létre, a Defender for IoT egyedi, teljes körű IoT biztonsági megoldást nyújt, amely átfogó képet nyújt az összes kapcsolódó Azure-erőforrás biztonságáról. Az Azure lehetővé teszi a gyors üzembe helyezést és a teljes integrációt IoT Hub Modulos ikrek használatával, így könnyen integrálható a meglévő eszközök felügyeleti eszközeivel.

## <a name="do-i-have-to-be-an-azure-security-center-customer-to-use-this-service"></a>A szolgáltatás használatához Azure Security Center ügyfélnek kell lennie?

Nem, de ajánlott. Azure Security Center nélkül a Defender for IoT korlátozott számú csatlakoztatott erőforrás-adatmennyiséget kap, és korlátozott elemzést nyújt a lehetséges támadási felületről, fenyegetésekről és lehetséges támadásokról.

## <a name="do-i-have-to-be-an-azure-iot-customer"></a>Egy Azure IoT-ügyfélnek kell lennie?

Igen. A IoT Defender az Azure IoT-kapcsolaton és-infrastruktúrán alapul.

## <a name="do-i-have-to-install-an-agent"></a>Kell-e ügynököt telepíteni?

Az ügynök telepítése a IoT-eszközökön nem kötelező, hogy a Defender IoT legyen engedélyezve. A következő három lehetőség közül választhat, és a kiválasztott beállításoknak megfelelően különböző szintű biztonsági monitorozási és felügyeleti képességeket kaphat:

1. Telepítse a Defender for IoT biztonsági ügynököt módosításokkal vagy anélkül. Ez a lehetőség a legmagasabb szintű biztonsági elemzéseket biztosítja az eszközök viselkedésének és elérésének.

1. Hozzon létre saját ügynököt, és implementálja a Defender for IoT biztonsági üzenet sémáját. Ez a beállítás lehetővé teszi a Defender használatát a IoT Analysis Tools számára az eszköz biztonsági ügynökén felül.

1. Nincs biztonsági ügynök telepítése a IoT-eszközökön. Ez a beállítás lehetővé teszi IoT Hub kommunikáció figyelését, csökkentve a biztonsági monitorozási és felügyeleti képességeket.

## <a name="what-does-the-defender-for-iot-agent-do"></a>Mi a IoT-ügynök Defenderje?

A Defender for IoT-ügynök az eszközök konfigurálására, viselkedésére és hozzáférésére (a konfiguráció vizsgálatával) biztosítja az eszköz szintű veszélyforrások lefedettségét, feldolgozza & a kapcsolatot. A IoT biztonsági ügynök Defender nem vizsgálja meg az üzleti tevékenységgel kapcsolatos adatait vagy tevékenységeit.

## <a name="where-can-i-get-the-defender-for-iot-security-agent"></a>Honnan szerezhetem be a Defender for IoT biztonsági ügynököt?

A Defender for IoT biztonsági ügynök nyílt forráskódú, és elérhető a GitHubon a 32 bites és a 64-bites Windows-és Linux-verziókon: https://github.com/Azure/Azure-IoT-Security .

## <a name="where-does-the-defender-for-iot-agent-get-installed"></a>Hová kell telepíteni a Defender for IoT-ügynököt?

Részletes telepítési és ügynök-telepítési információk találhatók a GitHubban: https://github.com/Azure/Azure-IoT-Security .

## <a name="what-are-the-dependencies-and-prerequisites-of-the-agent"></a>Mik az ügynök függőségei és előfeltételei?

A IoT Defender számos platformot támogat. Az adott eszközök támogatásának ellenőrzéséhez tekintse meg a [támogatott eszközök platformot](how-to-deploy-agent.md) .

## <a name="which-data-is-collected-by-the-agent"></a>Milyen adatokat gyűjt az ügynök?

A kapcsolat, a hozzáférés, a tűzfal konfigurációja, a folyamatok listája & az operációs rendszer alapkonfigurációját az ügynök gyűjti.

## <a name="how-much-data-will-the-agent-generate"></a>Mennyi adattal fog létrejönni az ügynök?

Az ügynök adatgenerálását az eszköz, az alkalmazás, a kapcsolat típusa és az ügyfél-ügynök konfigurációja vezérli. Az eszközök és a IoT megoldások közötti nagyfokú változékonyság miatt javasoljuk, hogy először üzembe helyezze az ügynököt egy tesztkörnyezetben vagy tesztelési beállításban, hogy megfigyelje, megismerje és beállítsa az igényeinek megfelelő egyedi konfigurációt, miközben megméri a generált adatmennyiséget. A szolgáltatás elindítása után a IoT Agent Defender az ügynök átviteli sebességének optimalizálásához nyújt operatív javaslatokat a konfiguráció és a Testreszabás folyamatának elősegítése érdekében.

## <a name="how-can-i-control-my-billing"></a>Hogyan szabályozható a számlázás?

A IoT Defender konfigurálható ügynökök vizsgálatait, adatpuffereket és egyéni riasztások létrehozását teszi lehetővé, amelyek növelhetik vagy csökkenthetik az ügynök által generált adatmennyiséget.

## <a name="do-agent-messages-use-up-quota-from-iot-hub"></a>Az ügynök üzenetei kvótát használnak a IoT Hubtól?

Igen. Az ügynök által továbbított adatokat a rendszer a IoT Hub kvótába számítja.

## <a name="what-next-ive-installed-an-agent-and-dont-see-any-activities-or-logs"></a>Mi a következő lépés? Telepítettem az ügynököt, és nem látok semmilyen tevékenységet vagy naplót...

1. Győződjön meg arról, hogy az [ügynök típusa megfelel az eszköz kijelölt operációsrendszer-platformjának](how-to-deploy-agent.md)

1. Ellenőrizze, hogy az [ügynök fut-e az eszközön](how-to-agent-configuration.md).

1. Győződjön meg arról, hogy a [szolgáltatás sikeresen engedélyezve lett](quickstart-onboard-iot-hub.md) a IoT hub **biztonsága** érdekében.

1. Győződjön meg arról, hogy az eszköz [konfigurálva van IoT hub a Defender for IoT modullal](quickstart-create-security-twin.md).

Ha a tevékenységek vagy naplók még nem érhetők el, további segítségért forduljon a Defender for IoT partnerhez.

## <a name="what-happens-when-the-internet-connection-stops-working"></a>Mi történik, ha az internetkapcsolat leáll?

Az ügynök továbbra is futtatja és tárolja az adattárolást, amíg az eszköz fut. A rendszer a biztonsági üzenet gyorsítótárában tárolja az adatmennyiséget a méret konfigurációjának megfelelően. Amikor az eszköz visszanyeri a kapcsolatot, a biztonsági üzenetek folytatják a küldést.

## <a name="if-the-device-is-restarted-will-the-security-agent-self-recover"></a>Ha az eszköz újraindul, a biztonsági ügynök önállóan helyreállítható?

A biztonsági ügynök úgy lett kialakítva, hogy automatikusan újrafusson az egyes eszközök újraindításakor.

## <a name="can-the-agent-affect-the-performance-of-the-device-or-other-installed-software"></a>Hatással lehet az ügynök az eszköz vagy más telepített szoftver teljesítményére?

Az ügynök bármilyen más alkalmazásként vagy folyamatként felhasználja a gépi erőforrásokat, és nem zavarja meg a normál eszköz tevékenységeit. Az erőforrás-használat azon az eszközön, amelyen az ügynök fut, a beállítással és a konfigurációval párosul. Azt javasoljuk, hogy az ügynök konfigurációját egy foglalt környezetben tesztelje, valamint a többi IoT-alkalmazással és-funkcióval való együttműködéssel, mielőtt éles környezetben telepítené az üzembe helyezést.

## <a name="im-making-some-maintenance-on-the-device-can-i-turn-off-the-agent"></a>Karbantartást végezek az eszközön. Ki tudom kapcsolni az ügynököt?

Az ügynök nem kapcsolható ki.

## <a name="is-there-a-way-to-test-if-the-agent-is-working-correctly"></a>Van mód annak tesztelésére, hogy az ügynök megfelelően működik-e?

Ha az ügynök leállítja a kommunikációt, vagy nem tudja elküldeni a biztonsági üzeneteket, az **eszköz csendes** riasztást generál.

## <a name="can-i-create-my-own-alerts"></a>Létrehozhatom a saját riasztásokat?

Igen. Az előre meghatározott viselkedések, például az IP-címek és a nyitott portok esetében beállíthatja a testreszabott riasztásokat. Az egyéni riasztásokkal és azok létrehozásával kapcsolatos további tudnivalókért tekintse meg az [egyéni riasztások létrehozása](quickstart-create-custom-alerts.md) című témakört.

## <a name="where-can-i-see-logs-can-i-customize-logs"></a>Hol láthatom a naplókat? Testreszabhatók a naplók?

- Riasztások és javaslatok megtekintése a csatlakoztatott Log Analytics munkaterületen. Konfigurálja a tárterület méretét és időtartamát a munkaterületen.

- A biztonsági ügynökből származó nyers adatok a Log Analytics-fiókban is tárolhatók. A beállítás konfigurációjának módosítása előtt vegye figyelembe a méretet, az időtartamot, a tárolási követelményeket és a kapcsolódó költségeket.

## <a name="why-should-i-add-defender-for-iot-to-the-module-identity-what-is-it-used-for"></a>Miért érdemes a IoT-hez hozzáadni a Defendert a modul identitásához? Mire használható?

Az ügynökök konfigurálásához és felügyeletéhez a Defender for IoT modult használjuk.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni arról, hogyan kezdheti meg a Defendert a IoT-hez, tekintse meg a következő cikkeket:

- Olvassa el a Defender for IoT [áttekintését](overview.md)
- A [szolgáltatás előfeltételeinek](service-prerequisites.md) ellenőrzése
- További információ az [első lépésekről](getting-started.md)
- [A Defender IoT biztonsági riasztások](concept-security-alerts.md) megismerése
