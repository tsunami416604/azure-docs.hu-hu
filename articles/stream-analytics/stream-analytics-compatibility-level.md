---
title: Kompatibilitási szintje az Azure Stream Analytics-feladatok ismertetése
description: Ismerje meg, hogyan állíthatja be az Azure Stream Analytics-feladat és a fontosabb változását foglalja össze a kompatibilitási szintje a legújabb kompatibilitási szinten
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
manager: kfile
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/15/2018
ms.openlocfilehash: 661aa85a200d4eafa1c521b06f6e12378d61e93e
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2018
ms.locfileid: "49343585"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Az Azure Stream Analytics-feladatok kompatibilitási szintje
 
Kompatibilitási szint egy Azure Stream Analytics szolgáltatás kiadás-specifikus viselkedéseit hivatkozik. Az Azure Stream Analytics egy felügyelt szolgáltatás, amelynek rendszeres szolgáltatásfrissítések, és a teljesítménnyel kapcsolatos fejlesztések. Általában frissítések automatikusan elérhetővé a végfelhasználók számára. Olyan új funkciókat azonban vezeti be a fontosabb változását foglalja össze az ilyen,-módosítási található egy meglévő feladat, módosítsa ezeket a feladatokat stb származó adatokat használ, a folyamatokban. A kompatibilitási szint jelent meg a Stream Analytics főbb változást megjelenítésére szolgál. Nagyobb módosítások mindig egy új kompatibilitási szintű jelennek meg. 

Kompatibilitási szint gondoskodik arról, hogy a meglévő feladatok futtatása minden hiba nélkül. Amikor létrehoz egy új Stream Analytics-feladat, célszerű a legújabb kompatibilitási szintje, amely az Ön számára elérhető használatával létrehozott. 
 
## <a name="set-a-compatibility-level"></a>Kompatibilitási szint beállítása 

Kompatibilitási szint szabályozza a stream analytics-feladat működését. A Stream Analytics-feladat kompatibilitási szintje is megadhatja a portálon vagy az a [feladat REST API-hívás létrehozásához](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job). Az Azure Stream Analytics jelenleg támogatja a két kompatibilitási szintek-"1.0" és "1.1". Alapértelmezés szerint a kompatibilitási szintje "1.0", az Azure Stream Analytics általános rendelkezésre állás időszakában megjelent. Az alapértelmezett érték frissítéséhez keresse meg a meglévő Stream Analytics-feladat > Válassza ki a **kompatibilitási szint** beállítást **konfigurálása** szakaszt, és módosítsa az értéket. 

Győződjön meg arról, hogy a kompatibilitási szint frissítése előtt állítsa le a feladatot. A kompatibilitási szint nem frissíthető, ha a feladat nem futó állapotban. 

![A portál kompatibilitási szint](media\stream-analytics-compatibility-level/image1.png)

 
Amikor frissíti a kompatibilitási szintet, a T-SQL nyelvű fordítón belülre érvényesíti a feladat a szintaxissal, amely megfelel a kiválasztott kompatibilitási szintet. 

## <a name="major-changes-in-the-latest-compatibility-level-11"></a>Fontosabb változását foglalja össze a legújabb kompatibilitási szinten (1.1-es)

1.1-kompatibilitási szinten a következő jelentős változások történtek:

* **Service Bus XML-formátuma**  

  * **korábbi verziók:** Azure Stream Analytics használt DataContractSerializer, ezért az üzenet tartalmának XML-címkéket tartalmazza. Példa:
    
   @\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{ "SensorId": "1", "hőmérséklet": 64\}\u0001 

  * **jelenlegi verzió:** az üzenet tartalmának tartalmaz közvetlenül semmilyen további címkékkel a stream. Példa:
  
   {"SensorId": "1", "hőmérséklet": 64} 
 
* **A mezőnevek megőrzése Kisbetű/nagybetű megkülönböztetése**  

  * **korábbi verziók:** mezőnevek alacsonyabb olyan esetekben, amikor az Azure Stream Analytics-motor általi feldolgozásának módosult. 

  * **jelenlegi verzió:** Kisbetű/nagybetű megkülönböztetése mezők nevét a rendszer megőrzi, az Azure Stream Analytics-motor feldolgozásakor. 

  > [!NOTE] 
  > Persisting Kisbetű/nagybetű megkülönböztetése még nem érhető el az Edge-környezetben üzemeltetett Stream elemzési feladatok. Ennek eredményeképpen minden mezőnevek vannak kisbetűssé konvertált, ha a feladat az Edge-ben üzemel. 

* **FloatNaNDeserializationDisabled**  

  * **korábbi verziók:** CREATE TABLE parancs nem fejeződött az NaN (nem szám az események szűrése. Például végtelenig, - Infinity) egy LEBEGŐPONTOS oszlop írja be, mert ezek a számok a dokumentált tartományon vannak.

  * **jelenlegi verzió:** CREATE TABLE lehetővé teszi, hogy adjon meg egy erős sémát. A Stream Analytics-motor ellenőrzi, hogy az adatok megfelel-e a séma. Ebben a modellben a parancs szűrheti NaN értéket tartalmazó eseményeket. 

* **Tiltsa le az automatikus upcast dátum/idő karakterlánc JSON-fájlban.**  

  * **korábbi verziók:** a JSON-elemző, akkor automatikusan upcast karakterlánc dátum és idő dátum/időzóna információkat értékeket írja be, és átalakítása (UTC). Ennek következtében elvesztése az időzóna adatait.

  * **jelenlegi verzió:** van több automatikusan upcast, a karakterlánc-értékeket dátum/idő típus dátum/időzóna információkat. Ennek eredményeképpen az időzónaadatok tartani. 

## <a name="next-steps"></a>További lépések
* [Az Azure Stream Analytics bemenetek hibaelhárítása](stream-analytics-troubleshoot-input.md)
* [Stream Analytics erőforrás-állapot panel](stream-analytics-resource-health.md)