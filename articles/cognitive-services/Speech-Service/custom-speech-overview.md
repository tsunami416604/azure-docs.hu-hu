---
title: Custom Speech áttekintése – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: A Custom Speech olyan online eszközöket biztosít, amelyekkel kiértékelheti és javíthatja az alkalmazásaihoz, eszközeihez és termékeihez tartozó beszéd-szöveg pontosságot. Minden, ami az első lépések elvégzéséhez szükséges, néhány teszt hangfájlt használ. Az alábbi hivatkozásokat követve hozzon létre egy egyéni beszéd – szöveg élményt.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: trbye
ms.openlocfilehash: 6dbe97d615753f0a90c8ba80aa7afa6dafa15eb2
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658468"
---
# <a name="what-is-custom-speech"></a>Mi az a Custom Speech?

A [Custom SPEECH](https://aka.ms/customspeech) felhasználóifelület-alapú eszközök, amelyek segítségével kiértékelheti és javíthatja a Microsoft beszéd-szöveg pontosságát alkalmazásai és termékei számára. Minden, ami az első lépések elvégzéséhez szükséges, néhány teszt hangfájlt használ. Az alábbi hivatkozásokat követve hozzon létre egy egyéni beszéd – szöveg élményt.

## <a name="whats-in-custom-speech"></a>Mi a Custom Speech?

Mielőtt bármit elvégezhet a Custom Speech, szüksége lesz egy Azure-fiókra és egy Speech Service-előfizetésre. Ha már rendelkezik fiókkal, előkészítheti az adatait, betaníthatja és tesztelheti a modelleket, ellenőrizheti a minőség felismerését, kiértékelheti a pontosságot, és végső soron üzembe helyezheti és használhatja az egyéni beszéd – szöveg modellt.

Ez a diagram kiemeli a [Custom Speech portált](https://aka.ms/customspeech)alkotó elemeket. Az alábbi hivatkozásokat követve további információkat tudhat meg az egyes lépésekről.

![Kiemeli a Custom Speech portált alkotó különböző összetevőket.](./media/custom-speech/custom-speech-overview.png)

1. [Előfizetés és projekt létrehozása](#set-up-your-azure-account) – hozzon létre egy Azure-fiókot, és fizessen elő a beszédfelismerési szolgáltatásra. Ez az egyesített előfizetés hozzáférést biztosít a beszéd-szöveg, a szöveg és a beszéd fordításához, valamint a [Custom Speech portálhoz](https://speech.microsoft.com/customspeech). Ezután használja a Speech Service-előfizetését, és hozza létre első Custom Speech projektjét.

1. [Töltse fel a tesztelési](how-to-custom-speech-test-data.md) adatok feltöltésének tesztelési adatait (hangfájlok), hogy kiértékelje a Microsoft beszéd-szöveges ajánlatát az alkalmazásaihoz, eszközeihez és termékeihez.

1. A [felismerés minőségének vizsgálata](how-to-custom-speech-inspect-data.md) – a [Custom Speech-portálon](https://speech.microsoft.com/customspeech) visszajátszhatja a feltöltött hangfelvételeket, és megvizsgálhatja a tesztelési adatok beszédfelismerési minőségét. A mennyiségi mérések esetében lásd: [az adatvizsgálat](how-to-custom-speech-inspect-data.md).

1. [Kiértékelheti és javíthatja a pontosságot](how-to-custom-speech-evaluate-data.md) – kiértékelheti és javíthatja a beszéd – szöveg modell pontosságát. A [Custom Speech-portál](https://speech.microsoft.com/customspeech) egy *szó szerinti hibát jelez*, amellyel meghatározható, hogy szükség van-e további képzésre. Ha elégedett a pontossággal, a Speech Service API-jait közvetlenül is használhatja. Ha a pontosságot 5%-20%-os relatív átlagban szeretné növelni, a portálon a **betanítás** lapon további betanítási adatok (például emberi feliratú átiratok és kapcsolódó szövegek) tölthetők fel.

1. [Modell betanítása és üzembe helyezése](how-to-custom-speech-train-model.md) – javítsa a beszéd – szöveg modell pontosságát úgy, hogy írásos átiratokat (10 000 órát) és kapcsolódó szöveget (<200 MB) biztosít, valamint a hangteszt adatait. Ez az érték segít a beszéd – szöveg modell betanításában. Ha betanítást, újratesztelést végez, és ha elégedett az eredménnyel, üzembe helyezheti a modellt egy egyéni végponton.

## <a name="set-up-your-azure-account"></a>Azure-fiók beállítása

Az Azure-fiók és a beszédfelismerési szolgáltatás előfizetése szükséges ahhoz, hogy egyéni modellt hozzon létre a [Custom Speech portálon](https://speech.microsoft.com/customspeech) . Ha nem rendelkezik fiókkal és előfizetéssel, [próbálja ki ingyenesen a Speech szolgáltatást](overview.md#try-the-speech-service-for-free).

> [!NOTE]
> Ügyeljen arra, hogy standard (S0) előfizetéseket hozzon létre, az ingyenes (F0) előfizetések nem támogatottak.

Miután létrehozott egy Azure-fiókot és egy Speech Service-előfizetést, be kell jelentkeznie [Custom Speech portálra](https://speech.microsoft.com/customspeech) , és hozzá kell kötnie az előfizetését.

1. Jelentkezzen be a [Custom Speech portálra](https://aka.ms/custom-speech).
1. Válassza ki azt az előfizetést, amelyre dolgozni szeretne, és hozzon létre egy beszédfelismerési projektet.
1. Ha módosítani szeretné az előfizetését, használja a felső navigációs sávban található **fogaskerék** ikont.

## <a name="how-to-create-a-project"></a>Projekt létrehozása

A tartalmak, például az adattípusok, a modellek, a tesztek és a végpontok a [Custom Speech portálon](https://speech.microsoft.com/customspeech)belüli **projektekben** vannak rendszerezve. Minden projekt egy tartományra és országra/nyelvre vonatkozik. Létrehozhat például egy olyan projektet a hívási központok számára, amelyek az angol nyelvet használják a Egyesült Államok.

Az első projekt létrehozásához válassza a **beszéd-szöveg/egyéni beszéd** lehetőséget, majd kattintson az **új projekt** elemre. A projekt létrehozásához kövesse a varázsló utasításait. Miután létrehozott egy projektet, négy lapot kell látnia: **az adatelemzést**, a **tesztelést**, a **betanítást** és az **üzembe helyezést**. Az egyes lapok használatának megismeréséhez használja a [következő lépések](#next-steps) hivatkozásait.

> [!IMPORTANT]
> A [Custom Speech portált](https://aka.ms/custom-speech) nemrég frissítették! Ha korábban létrehozott, modelleket, teszteket és közzétett végpontokat hozott létre a CRIS.ai-portálon vagy API-kkal, létre kell hoznia egy új projektet az új portálon a régi entitásokhoz való kapcsolódáshoz.

## <a name="model-lifecycle"></a>Modell életciklusa

Az egyéni beszédfelismerés az **alapmodelleket** és az **Egyéni modelleket** is használja. Minden nyelvhez egy vagy több **alapmodell** tartozik. Általánosságban elmondható, hogy amikor új beszédfelismerési modellt bocsátanak ki a normál beszédfelismerési szolgáltatásba, azt a rendszer új **alapmodellként** is importálja a Custom Speech szolgáltatásba. Általában minden 3-6 havonta frissülnek, a régebbi modellek pedig kevésbé hasznosak az idő múlásával, mivel a legújabb modell általában nagyobb pontossággal rendelkezik.

Ezzel szemben az **egyéni modellek** úgy jönnek létre, hogy egy kiválasztott alapmodellt egy adott felhasználói forgatókönyvhöz igazítanak. Előfordulhat, hogy egy adott egyéni modellt is igénybe vehet egy hosszabb ideig, ha az megfelel az igényeinek, de javasoljuk, hogy rendszeres időközönként frissítsen a legújabb alapmodellre, és további adatokkal áttanítsa azokat.

A modell életciklusával kapcsolatos egyéb kulcsfontosságú feltételek a következők:

* **Alkalmazkodás**: alapmodell készítése és testreszabása a tartományhoz/forgatókönyvhöz szöveges adatok és/vagy hangadatok használatával
* **Dekódolás**: modell használata és beszédfelismerés végrehajtása (hang kódolása szövegbe)
* **Endpoint (végpont**): egy alapmodell vagy egy egyéni modell felhasználó-specifikus telepítése, amelyet *csak* egy adott felhasználó elérhet.

### <a name="expiration-timeline"></a>Lejárat idővonala

Ahogy az új modellek és az új funkciók elérhetővé válnak, és a régebbi, kevésbé pontos modellek kimaradnak, tekintse meg a modell és a végpont lejáratának következő ütemterveit:

**Alapmodellek** 

* Adaptáció: 1 évig elérhető. A modell importálása után a rendszer 1 évig elérhetővé teszi az egyéni modellek létrehozását. 1 év után új egyéni modelleket kell létrehozni az alapmodell újabb verziójából.  
* Dekódolás: az importálás után 2 évig elérhető. Ez azt jelenti, hogy létrehozhat egy végpontot, és 2 évig használhatja a Batch-átírást ezzel a modellel. 
* Végpontok: elérhető a dekódolással megegyező idővonalon

**Egyéni modellek**

* Dekódolás: a modell létrehozása után 2 évig elérhető. Ez azt jelenti, hogy az egyéni modellt 2 évre (batch/Realtime/tesztelés) használhatja a létrehozás után. 2 év elteltével **újra be kell tanítania a modellt**, mert az alapmodell leggyakrabban az adaptációhoz lesz elavulttá.  
* Végpontok: elérhető a dekódolással megegyező idővonalon

Ha egy alapmodell vagy egy egyéni modell lejár, akkor mindig vissza fog térni a **legújabb alapmodell-verzióra**. Így a megvalósítás soha nem szakad meg, de az egyéni modellek érvényességének lejárta után kevésbé pontosak lesznek a *konkrét adatai* . A modell lejáratát a következő helyeken tekintheti meg a Custom Speech-portálon:

* Modell képzésének összefoglalása
* Modell betanításának részletei
* A központi telepítés összegzése
* Központi telepítés részletei

A lejárati dátumokat a JSON- [`GetModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetModel) [`GetBaseModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetBaseModel) Válasz tulajdonság alatt található és az egyéni beszédfelismerési API-k segítségével is megtekintheti `deprecationDates` .

Vegye figyelembe, hogy az egyéni beszédfelismerési végponton anélkül frissítheti a modellt, hogy az egyéni beszédfelismerési portál üzembe helyezés szakaszában vagy a Custom Speech API-n keresztül megváltoztatja a végpont által használt modellt.

## <a name="next-steps"></a>Következő lépések

* [Az adatfeldolgozás előkészítése és tesztelése](how-to-custom-speech-test-data.md)
* [Az adatai ellenőrzése](how-to-custom-speech-inspect-data.md)
* [Modell pontosságának kiértékelése és javítása](how-to-custom-speech-evaluate-data.md)
* [Modell betanítása és üzembe helyezése](how-to-custom-speech-train-model.md)
