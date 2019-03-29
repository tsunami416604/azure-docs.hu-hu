---
title: Gyakran ismételt kérdések az ASC for IoT-előzetes verzió |} A Microsoft Docs
description: Keresse meg a válaszokat a leggyakoribb kapcsolatos gyakori kérdések az ASC IoT-szolgáltatások és a szolgáltatás.
services: ASCforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 97fda6c2-1ecb-491f-b48d-41788bd7e0d3
ms.service: ASCforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 8940d629d2f4827ee724c813a40394378dde5a4c
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2019
ms.locfileid: "58575147"
---
# <a name="asc-for-iot-frequently-asked-questions"></a>Az IoT ASC – gyakori kérdések  

> [!IMPORTANT]
> Az IoT ASC jelenleg nyilvános előzetes verzióban érhető el.
> Ez az előnézeti verzió nélkül egy szolgáltatói szerződést, és nem javasolt éles számítási feladatok esetében. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez a cikk az IoT ASC kapcsolatos gyakori kérdések és válaszok listáját tartalmazza. 

## <a name="does-azure-provide-support-for-iot-security"></a>Nem Azure-támogatást nyújt IoT-biztonság?

Az Azure biztosít egy integrált nézet felügyeléséhez és kezeléséhez az IoT-biztonság az Azure Security Center segítségével a teljes biztonsági megoldás részeként. Ha Ön egy alkalmazásfejlesztő, használhatja az IoT Hub nézetben az IoT-alkalmazások biztonságának kezeléséhez.

## <a name="what-is-azures-unique-value-proposition-for-iot-security"></a>Mi az egyedi értékajánlat az Azure IoT-biztonság?

Az IoT ASC lehetővé teszi a vállalatok számára, hogy a meglévő internetes biztonsági nézetet a teljes IoT-megoldás bővítése. Az Azure üzleti megoldását, lehetővé téve a üzleti műveletek teljes körű képet és döntések alapján a vállalati biztonsági állapotáról, és összegyűjtött adatok. Kombinált használata az Azure IoT, az Azure IoT Edge, az Azure Sphere, Azure központi biztonsági, és az Azure Security Center lehetővé teszi a kívánt megoldás létrehozása az elvárt biztonság mellett.

## <a name="who-is-asc-for-iot-made-for"></a>IoT arról, hogy ki ASC? 

Az IoT ASC integrálva van az Azure IoT Hub biztonsági belül, és a napi üzleti megoldás-kezelést biztosít a biztonsági műveletek. Az IoT ASC is integrálva van az Azure Security Center képességei, és adja meg egy integrált nézet figyelése és kezelése az IoT-biztonság a teljes biztonsági megoldás részeként.

## <a name="how-does-asc-for-iot-compare-to-the-competition"></a>Az IoT ASC összehasonlítása a verseny

Habár más megoldásokkal, amelyek lehetővé teszik az ügyfelek a saját megoldásokat hozhat létre olyan képességeket biztosít, az IoT ASC egyedi teljes körű IoT biztonsági megoldást nyújt, amely nyújt a széles körű az összes kapcsolódó Azure-erőforrásai biztonságát. Az Azure gyors üzembe helyezés és a szolgáltatás teljesen integrálható az IoT Hub ikermodulokkal számára megkönnyíti az integrációt a meglévő eszközeivel eszköz segítségével.

## <a name="do-i-have-to-be-an-azure-security-center-asc-customer"></a>Rendelkeznie kell az Azure Security Center (ASC) ügyfele?

Nem, de ajánlott. ASC nélkül ASC az IoT korlátozott csatlakoztatott erőforrásokkal kapcsolatos adatokat fogad, és a egy korlátozott elemzi a potenciális támadási felületet, a fenyegetések és a potenciális támadások. 

## <a name="do-i-have-to-be-an-azure-iot-customer"></a>Rendelkeznie kell egy Azure IoT-ügyfél?

Igen. Az IoT ASC Azure IoT-kapcsolat és az infrastruktúra támaszkodik.

## <a name="do-i-have-to-install-an-agent"></a>Rendelkezik ügynök telepítéséhez?

Az IoT-eszközök ügynök telepítése nem kötelező, és engedélyezze a Microsoft ASC az IoT. A következő három lehetőség, így különböző szintű biztonsági figyelési és felügyeleti funkciók alapján a kijelölt közül választhat:

1. Az ASC IoT biztonsági ügynök telepítése, vagy a módosítások nélkül. Ez a beállítás nagyobb biztonságot betekintést az eszköz működésének és elérésének a legmagasabb szintű biztosít.

2. Hozzon létre saját ügynököt, és valósítson meg Microsoft ASC IoT biztonsági üzenet séma. Ez a beállítás engedélyezi az eszköz biztonsági ügynök IoT elemzési eszközöket a Microsoft ASC használatát.

3. Nincsenek biztonsági ügynök telepítése az IoT-eszközökről. Ez a beállítás lehetővé teszi az IoT Hub kommunikációs megfigyelés, az alacsonyabb szintű biztonsági figyelési és felügyeleti képességeket. 

## <a name="what-does-the-asc-for-iot-agent-do"></a>Mit jelent az ASC IoT ügynök?

IoT-ügynök ASC eszköz szintű fenyegetést lefedettséget biztosít a eszközkonfiguráció, a viselkedését, és a hozzáférést (a konfiguráció vizsgálatával), a folyamat és a kapcsolat. Az IoT biztonsági ügynök ASC nem ellenőrzi a üzleti adatok vagy a tevékenységekhez.

## <a name="where-i-can-get-the-asc-for-iot-security-agent"></a>Ahol az IoT biztonsági ügynök ASC szoftverei?

Az IoT biztonsági ügynök ASC is nyílt forráskódú és elérhető a Githubon a 32 bites és 64 bites Windows és Linux-verziók: https://github.com/Azure/Azure-Security-IoT-Preview

## <a name="where-does-the-asc-for-iot-agent-get-installed"></a>Ahol települjenek nem az ASC IoT ügynök? 

Részletes telepítési és az ügynök telepítési információk a Githubon található: https://github.com/Azure/Azure-Security-IoT-Preview

## <a name="what-are-the-dependencies-and-prerequisites-of-the-agent"></a>Mik azok a függőségeket és előfeltételek az ügynök?

ASC IOT számos különböző platformokat támogatja. Lásd: [támogatott eszközplatformok](how-to-deploy-agent.md) ellenőrizheti az adott eszközök támogatása. 

## <a name="which-data-is-collected-by-the-agent"></a>Az ügynök által összegyűjtött adatok?

Az ügynök által gyűjtött kapcsolat, a hozzáférés, a tűzfal-konfiguráció, a folyamat listán és a operációs rendszer.

## <a name="how-much-data-will-the-agent-generate"></a>Mekkora mennyiségű adatot fog létrehozni az ügynök?

Az ügynök adat-előállítás határozzák meg az eszközt, az alkalmazás, a kapcsolat típusa és a felhasználói ügynök konfigurálása. Eszközök és az IoT-megoldások között, a nagy változatossággal miatt javasoljuk, hogy első az ügynököt a labor- vagy tesztkörnyezetből figyelje meg, ismerje meg, és állítsa be, amely a legjobban az igényeinek, során létrehozott adatok mennyiségét a konfigurációs beállítást. A szolgáltatás elindítása után az ASC IoT-ügynök operatív javaslatokat tesz a ügynök átviteli sebesség optimalizálásával segítséget a konfigurációs és testreszabási folyamat.

## <a name="how-can-i-control-my-billing"></a>Hogyan felügyelhetem történik a számlázás?

ASC IOT konfigurálható az ügynök biztosít, vizsgálatokat, adatok buffers és lehetővé teszi, hogy növelje vagy csökkentse az ügynök által létrehozott adatok mennyiségét egyéni riasztásokat is létrehozhat.

## <a name="do-agent-messages-use-up-quota-from-iot-hub"></a>Az ügynök üzenetek használnak fel az IoT hubról kvóta?

Igen. Az ügynök továbbított adatok az IoT Hub kvóta külön tranzakciónak minősül. 

## <a name="what-next-ive-installed-an-agent-and-dont-see-any-activities-or-logs"></a>Következő lépések E ügynök már telepítve van, és nem jelenik meg semmilyen tevékenységet vagy naplók

1. Ellenőrizze a [ügynöktípus megfelel az eszköz a kijelölt operációsrendszer-platform](how-to-deploy-agent.md)

1. Erősítse meg a [ügynök fut-e az eszközön](how-to-agent-configuration.md).

2. Ellenőrizze a [szolgáltatás engedélyezése sikeres volt](quickstart-onboard-iot-hub.md) való **biztonsági** az IoT hubban. 

3. Ellenőrizze, hogy az eszköz [konfigurálva az IoT Hub az IoT-modul az ASC](quickstart-create-security-twin.md).  

Ha még nem érhető el a tevékenységeket, vagy a naplók, IoT-partnerrel, további segítségért forduljon az ASC.

## <a name="what-happens-when-the-internet-connection-stops-working"></a>Mi történik, ha az internetes kapcsolat nem működik?

Az ügynök tovább fut, és mindaddig, amíg az eszköz fut-e az adatok tárolásához. Méretre vonatkozó konfigurációja szerint a biztonsági üzenet gyorsítótárban tárolt adatokat. Ha az eszköz ismét érvénybe lép, kapcsolat, a biztonsági üzenetek küldése folytatódik. 

## <a name="if-the-device-is-restarted-will-the-security-agent-self-recover"></a>Ha az eszköz újraindul, a biztonsági ügynök önálló állítja helyre?

A biztonsági ügynök automatikusan újra futtatni, minden egyes eszköz újraindítással tervezték.

## <a name="can-the-agent-affect-the-performance-of-the-device-or-other-installed-software"></a>Az ügynök hatással lehet az eszköz vagy egyéb telepített szoftverek teljesítményét?

Az ügynök egy másik alkalmazás vagy folyamat, a gép erőforrásokat használ fel, és nem okozhat hibát normál eszköz tevékenység. Erőforrás-használat az eszközön, az ügynök fut a beállítási és konfigurációs van így. Azt javasoljuk, hogy az ügynök konfigurációjának tesztelése tartalmazott-környezetben, és az egyéb IoT-alkalmazások és -funkcióihoz, együttműködés próbál egy éles környezetbe telepítése előtt.

## <a name="im-making-some-maintenance-on-the-device-can-i-turn-off-the-agent"></a>Bizonyos karbantartási teszem az eszközön. Ki lehet kapcsolni az ügynököt?

Az ügynök nem kapcsolható ki.

## <a name="is-there-a-way-to-test-if-the-agent-is-working-correctly"></a>Van mód a tesztelése, ha az ügynök megfelelően működik-e? 

Ha az ügynök leállítja a kommunikáció során, vagy nem biztonsági üzeneteket küld egy **eszköz csendes** riasztás akkor jön létre.

## <a name="can-i-create-my-own-alerts"></a>Saját riasztásokat hozhatnak létre?

Igen. A testre szabott riasztás például IP-cím és portok megnyitása viselkedés előre meghatározott készletét megadhatja azt. Lásd: [egyéni riasztásokat is létrehozhat](quickstart-create-custom-alerts.md) további információ az egyéni riasztások, és hogyan javíthatja azokat. 

## <a name="where-can-i-see-logs-can-i-customize-logs"></a>Hol tekinthetem meg naplók? Testreszabhatja a naplókat?

- Riasztások megtekintése és használata a csatlakoztatott Log Analytics-munkaterület javaslatok. Konfigurálja a tároló mérete és időtartama a munkaterületen.

- Nyers adatok a biztonsági ügynök is tárolhatók a Log Analytics-fiókban. Ez a beállítás konfigurációjának módosítása előtt fontolja meg a mérete, az időtartam, a tárhellyel kapcsolatos követelmények és a kapcsolódó költségek. 

## <a name="why-should-i-add-asc-for-iot-to-the-module-identity-what-is-it-used-for"></a>Miért kell hozzáadni az ASC az IoT a modul identitás? Mire való?

Az IoT-modul ASC ügynök konfigurálására és felügyeletére szolgál.


## <a name="next-steps"></a>További lépések

Többet szeretne megtudni az IoT használatába ASC, tekintse meg a következő cikkeket:


- Olvassa el az ASC IOT [áttekintése](overview.md)
- Ellenőrizze a [szolgáltatás előfeltételei](service-prerequisites.md)
- Ismerje meg, hogyan [első lépései](getting-started.md)
- Megismerheti [ASC IoT biztonsági riasztások](concept-security-alerts.md)

