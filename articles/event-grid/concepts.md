---
title: Az Azure Event Grid fogalmai
description: Az Azure Event Gridet és a vele kapcsolatos fogalmakat ismerteti. Az Eseményrács több kulcsfontosságú összetevőjét határozza meg.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: spelluru
ms.openlocfilehash: 348d82f704b89b97e11a09b8f88e92831901b3bf
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393466"
---
# <a name="concepts-in-azure-event-grid"></a>Fogalmak az Azure Event Gridben

Ez a cikk ismerteti a fő fogalmak az Azure Event Gridben.

## <a name="events"></a>Események

Az esemény a legkisebb mennyiségű információ, amely teljes mértékben leírja, hogy mi történt a rendszerben. Minden eseménynek olyan közös információi vannak, mint például: az esemény forrása, az esemény időpontja és az egyedi azonosító. Minden esemény olyan konkrét információkkal is rendelkezik, amelyek csak az adott eseménytípusra vonatkoznak. Például az Azure Storage-ban létrehozott új fájlról szóló esemény a fájlról tartalmaz információkat, például a `lastTimeModified` értéket. Egy Event Hubs esemény pedig a Capture fájl URL-jét tartalmazza. 

A legfeljebb 64 KB méretű eseményekre az általános rendelkezésre állási (GA) szolgáltatásiszint-szerződés (SLA) vonatkozik. Az 1 MB-os méretű események támogatása jelenleg előzetes verzióban érhető el. A 64 KB feletti eseményeket 64 KB-os lépésekben számítjuk fel. 


Az eseményen küldött tulajdonságokról az [Azure Event Grid eseményséma](event-schema.md)című témakörben található.

## <a name="publishers"></a>Kiadók

A közzétevő az a felhasználó vagy szervezet, amely úgy dönt, hogy eseményeket küld az Eseményrácsra. A Microsoft számos Azure-szolgáltatás hoz közzé eseményeket. A saját alkalmazásából is tehet közzé eseményeket. Az Azure-on kívül szolgáltatásokat üzemeltető szervezetek tehetnek közzé eseményeket Event Griden keresztül.

## <a name="event-sources"></a>Eseményforrások

Az eseményforrás az, ahol az esemény történik. Minden eseményforráshoz egy vagy több eseménytípus tartozik. Például a blob által létrehozott események tekintetében az Azure Storage az eseményforrás. Az eszközök által létrehozott események tekintetében az IoT Hub az eseményforrás. A saját megadott egyéni eseményei tekintetében a saját alkalmazása az eseményforrás. Az eseményforrások feladata, hogy eseményeket küldjenek az Event Grid számára.

A támogatott Event Grid-források megvalósításáról az Eseményforrások az [Azure Event Grid ben című témakörben](overview.md#event-sources)talál további információt.

## <a name="topics"></a>Témakörök

Az eseményrács témakör egy végpontot biztosít, ahol a forrás eseményeket küld. A közzétevő létrehozza az eseményrács témakörét, és eldönti, hogy egy eseményforrásnak szüksége van-e egy vagy több témakörre. A témakör kapcsolódó események gyűjteményéhez használatos. Bizonyos típusú eseményekre való reagáláshoz az előfizetők eldöntik, hogy mely témákra iratkozzanak fel.

A rendszer témakörök az Azure-szolgáltatások által biztosított beépített témakörök. A rendszer témakörök nem jelennek meg az Azure-előfizetésben, mert a témakörök tulajdonosa a közzétevő, de fel lehet rájuk iratkozni. Feliratkozáshoz adja meg azzal az erőforrással kapcsolatos információkat, ahonnan eseményeket szeretne kapni. Ha hozzáférhet az erőforráshoz, feliratkozhat az eseményeire.

Az egyéni témakörök az alkalmazások és a külső felek témakörei. Amikor létrehoz vagy hozzáférést kap egy egyéni témakörhöz, akkor az megjelenik a saját előfizetésében.

Az alkalmazás tervezésekor rugalmasan dönti el, hogy hány témakört szeretne létrehozni. Nagy megoldásokhoz hozzon létre egy egyéni témakört a kapcsolódó események minden kategóriájához. Tekintsünk például egy olyan alkalmazást, amelyik a felhasználói fiókok módosításához és a megrendelések feldolgozásához kapcsolódóan küld eseményeket. Nem valószínű, hogy valamely eseménykezelőnek mind a két esemény kategória kellene. Hozzon létre két külön témakört, és az eseménykezelők hadd iratkozzanak fel arra, amelyik érdekli őket. Kis megoldások esetén előfordulhat, hogy az összes eseményt egyetlen témakörbe szeretné küldeni. Az esemény-előfizetők szűrhetik a kívánt eseménytípusokat.

## <a name="event-subscriptions"></a>Esemény-előfizetések

Az előfizetés közli az Event Griddel, hogy mely eseményeket szeretnéd fogadni egy olyan témában, amelyet szeretnél. Az előfizetés létrehozásakor egy végpontot biztosít az esemény kezeléséhez. Szűrheti a végpontra küldött eseményeket. Szűrhet eseménytípus vagy tárgyminta szerint. További információ: [Event Grid subscription séma](subscription-creation-schema.md).

Az előfizetések létrehozásáról az:

* [Azure CLI-minták az Event Gridhez](cli-samples.md)
* [Azure PowerShell-minták az Event Gridhez](powershell-samples.md)
* [Azure Resource Manager-sablonok az Event Gridhez](template-samples.md)

Az aktuális eseményrács-előfizetések beszerzéséről a [Query Event Grid-előfizetések](query-event-subscriptions.md)című témakörben talál további információt.

## <a name="event-subscription-expiration"></a>Esemény-előfizetés lejárata
Az esemény-előfizetés a megadott dátumot követően automatikusan lejár. Állítson be egy lejárati esemény-előfizetéseket, amelyekre csak korlátozott ideig van szükség, és nem szeretne aggódni az előfizetések megtisztítása miatt. Például egy esemény-előfizetés létrehozása egy forgatókönyv teszteléséhez, érdemes lehet beállítani egy lejárati. 

A lejárat beállításának például a [Speciális szűrőkkel feliratkozás](how-to-filter-events.md#subscribe-with-advanced-filters)című témakörben található.

## <a name="event-handlers"></a>Eseménykezelők

Az Event Grid szempontjából az eseménykezelő az a hely, ahol az esemény elküldésre kerül. A kezelő további lépéseket tesz az esemény feldolgozásához. Az Event Grid több kezelőtípust támogat. Használhatja a támogatott Azure-szolgáltatás vagy a saját webhook kezelőként. A kezelő típusától függően az Event Grid különböző mechanizmusokat követ az esemény kézbesítésének garantálása érdekében. A HTTP webhook eseménykezelők esetében az esemény újrapróbálkozik, `200 – OK`amíg a kezelő vissza nem adja a. Az Azure Storage Queue esetében az eseményeket a rendszer addig próbálja meg újra, amíg a várólista-szolgáltatás sikeresen fel nem dolgozza az üzenetleküldéset a várólistába.

A támogatott eseményrács-kezelők megvalósításáról az [Azure Event Grid eseménykezelői](event-handlers.md)című témakörben talál további információt.

## <a name="security"></a>Biztonság

Az Event Grid biztonságot nyújt a témakörökre való feliratkozáshoz és a közzétételi témakörökhöz. Az előfizetéssorán megfelelő engedélyekkel kell rendelkeznie az erőforrás- vagy eseményrács témakörében. A közzétételkor rendelkeznie kell egy SAS-jogkivonatvagy kulcshitelesítés a témakörhöz. További információt az [Event Grid biztonsága és hitelesítése](security-authentication.md)című témakörben talál.

## <a name="event-delivery"></a>Esemény kézbesítése

Ha az Event Grid nem tudja megerősíteni, hogy az előfizető végpontja eseményhez érkezett, akkor újra kézbesíti az eseményt. További információt az [Event Grid üzenetkézbesítése és újrapróbálkozása](delivery-and-retry.md)című témakörben talál.

## <a name="batching"></a>Kötegelés

Egyéni témakör használata esetén az eseményeket mindig egy tömbben kell közzétenni. Ez lehet egy köteg alacsony átviteli sebességű forgatókönyvek, azonban a nagy mennyiségű használati esetekben, ajánlott, hogy a kötegelés több esemény együtt közzétett a nagyobb hatékonyság elérése érdekében. A kötegek legfeljebb 1 MB méretűek lehetnek. Az egyes események továbbra sem lehetnek nagyobbak 64 KB-nál (általános elérhetőség) vagy 1 MB-nál (előzetes verzió).

> [!NOTE]
> A legfeljebb 64 KB méretű eseményekre az általános rendelkezésre állási (GA) szolgáltatásiszint-szerződés (SLA) vonatkozik. Az 1 MB-os méretű események támogatása jelenleg előzetes verzióban érhető el. A 64 KB feletti eseményeket 64 KB-os lépésekben számítjuk fel. 

## <a name="next-steps"></a>További lépések

* Az Event Grid ismertetése: [Az Event Grid bemutatása](overview.md).
* Az Event Grid használatának gyors megkezdéséhez olvassa el az [Egyéni események létrehozása és irányítása az Azure Event Griddel című témakört.](custom-event-quickstart.md)
