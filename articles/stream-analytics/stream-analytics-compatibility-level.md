---
title: "Ismerje meg az Azure Stream Analytics-feladatok kompatibilitási szintet. | Microsoft Docs"
description: "Útmutató: az Azure Stream Analytics-feladat és a fontosabb változását kompatibilitási szintjének beállítása a legfrissebb kompatibilitási szinten"
keywords: "Kompatibilitási szintet, a streamelési adatok"
documentationcenter: 
services: stream-analytics
author: SnehaGunda
manager: kfile
editor: 
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 01/03/2018
ms.author: sngun
ms.openlocfilehash: 70aa82f5d8a909121c42742bb5261d6c9b8570de
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2018
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Azure Stream Analytics-feladatok kompatibilitási szintje
 
Kompatibilitási szintje a kiadási-specifikus viselkedéseket egy Azure Stream Analytics szolgáltatás hivatkozik. Az Azure Stream Analytics felügyelt szolgáltatást, a rendszeres szolgáltatás-frissítéseket, és a teljesítménnyel kapcsolatos fejlesztések. Általában frissítések automatikusan elérhetővé válnak a végfelhasználók számára. Egyes új funkcióit azonban fontosabb változását bevezeti, ilyen,-módosítás egy meglévő feladat, a folyamat, amely túlzottan ezeket a feladatokat stb származó adatok módosításakor. A kompatibilitási szintet jelent a Stream Analytics-ben bevezetett főbb változást szolgál. Jelentős változások történtek mindig az új kompatibilitási szintet. 

Kompatibilitási szint gondoskodik arról, hogy futnak-e meglévő feladatok meghibásodás nélkül. Amikor létrehoz egy új Stream Analytics-feladat, célszerű a legújabb kompatibilitási szintet, amely elérhető az Ön használatával létrehozott is. 
 
## <a name="set-a-compatibility-level"></a>A kompatibilitási szint beállítása 

Kompatibilitási szintje a stream analytics-feladat működését vezérli. Beállíthatja azt a kompatibilitási szint Stream Analytics-feladat vagy a portál használatával a [feladat REST API-hívás létrehozása](https://docs.microsoft.com/en-us/rest/api/streamanalytics/stream-analytics-job). Az Azure Stream Analytics jelenleg két kompatibilitási szint által "1.0" és "1.1". Alapértelmezés szerint a kompatibilitási szintje "1.0" során az Azure Stream Analytics általánosan rendelkezésre álló operációs rendszerben megjelent. Az alapértelmezett érték frissítéséhez nyissa meg a meglévő Stream Analytics-feladat > Válassza ki a **kompatibilitási szint** beállítást **konfigurálása** szakaszt, és módosítsa az értéket. 

Győződjön meg arról, hogy a kompatibilitási szint frissítése előtt állítsa le a feladatot. A kompatibilitási szintje nem lehet frissíteni, ha a feladat futó állapotban van. 

![A portál kompatibilitási szint](media\stream-analytics-compatibility-level/image1.png)

 
Ha frissíti a kompatibilitási szintet, a T-SQL nyelvű fordító érvényesíti a feladat a használatával, amely megfelel a kiválasztott kompatibilitási szintet. 

## <a name="major-changes-in-the-latest-compatibility-level-11"></a>A legfrissebb kompatibilitási szintet (1.1) lényegesen módosul

A következő jelentős változások történtek az 1.1-es kompatibilitási szintje:

* **Service Bus XML-formátuma**  

  * **korábbi verziók:** Azure Stream Analytics használt DataContractSerializer, ezért az üzenet tartalma tartalmazza XML-címkék. Példa:
    
   @\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{"SensorId": "1", "hőmérséklet": 64\}\u0001 

  * **aktuális verzió:** az üzenet tartalma tartalmaz közvetlenül semmilyen további címkékkel adatfolyam. Példa:
  
   {"SensorId": "1", "hőmérséklet": 64} 
 
* **A mezőneveknek tárolásakor Kisbetű/nagybetű megkülönböztetése**  

  * **korábbi verziók:** mezőnevek módosult kisbetű, amikor az Azure Stream Analytics-végrehajtó dolgozza fel. 

  * **aktuális verzió:** Kisbetű/nagybetű megkülönböztetése a mezőnevek maradnak, ha azokat az Azure Stream Analytics-végrehajtó dolgozza fel. 
 
* **FloatNaNDeserializationDisabled**  

  * **korábbi verziók:** CREATE TABLE parancs nem volt szűrése események NaN (nem szám. Például végtelen, - végtelen) egy LEBEGŐPONTOS oszlop írja be, mert ezeket a számokat az dokumentált tartományon kívül van.

  * **aktuális verzió:** CREATE TABLE lehetővé teszi, hogy adjon meg egy erős sémát. A Stream Analytics-motor ellenőrzi, hogy megfelel-e az adatok ebben a sémában. Ebben a modellben a parancs szűrhetők az események NaN értékeket. 

* **Tiltsa le az automatikus upcast datetime karakterláncok a JSON-ban.**  

  * **korábbi verziók:** a JSON-elemző automatikusan upcast karakterlánc dátum/idő/zóna információk dátum/idő értékek írja be, és átalakítása UTC lenne. Ennek következtében az időzónaadatok elvesztését.

  * **aktuális verzió:** van több automatikusan upcast, dátum/idő/zóna információkkal dátum és idő típusra karakterláncértékek tömbjeként ábrázolva. Ennek eredményeképpen az időzónaadatok maradnak. 

## <a name="next-steps"></a>További lépések
* [Azure Stream Analytics a hibaelhárítási útmutatója](stream-analytics-troubleshooting-guide.md)
* [Stream Analytics állapotfigyelő erőforráspanelen](stream-analytics-resource-health.md)