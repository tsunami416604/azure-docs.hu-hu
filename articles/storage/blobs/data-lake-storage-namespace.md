---
title: Azure Data Lake Storage Gen2 hierarchikus névtér
description: Az Azure Data Lake Storage Gen2 hierarchikus névterének fogalma
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 6ce94590a1d0de6941c27d972bdd1c4194080e95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77153077"
---
# <a name="azure-data-lake-storage-gen2-hierarchical-namespace"></a>Azure Data Lake Storage Gen2 hierarchikus névtér

Egy kulcsfontosságú mechanizmus, amely lehetővé teszi az Azure Data Lake Storage Gen2 számára, hogy objektumtárolási méretekben és árakon biztosítsa a fájlrendszer teljesítményét, **hierarchikus névtér**hozzáadása. Ez lehetővé teszi, hogy a fiókon belüli objektumok/fájlok gyűjteménye a könyvtárak és beágyazott alkönyvtárak hierarchiájába ugyanúgy legyen rendszerezve, mint a számítógépen lévő fájlrendszer. Ha a hierarchikus névtér engedélyezve van, a tárfiók képeslesz az objektumtárolás méretezhetőségét és költséghatékonyságát biztosítani, az elemzési motorok és keretrendszerek számára jól ismert fájlrendszer-szemantika ként.

## <a name="the-benefits-of-a-hierarchical-namespace"></a>A hierarchikus névtér előnyei

A következő előnyök kapcsolódnak a fájlrendszerekhez, amelyek hierarchikus névteret valósítanak meg a blobadatokon keresztül:

- **Atomi könyvtár manipulálása:** Az objektum a címtárhierarchiát közelítő tárolja a perjelek (/) objektumnévbe ágyazásának konvencióját, amely a görbeszegmenseket jelöli. Bár ez az egyezmény objektumok szervezésére szolgál, az egyezmény nem nyújt segítséget olyan műveletekhez, mint a könyvtárak áthelyezése, átnevezése vagy törlése. Valódi könyvtárak nélkül az alkalmazásoknak potenciálisan több millió egyedi blobot kell feldolgozniuk a címtárszintű feladatok eléréséhez. Ezzel szemben egy hierarchikus névtér egyetlen bejegyzés (a szülőkönyvtár) frissítésével dolgozza fel ezeket a feladatokat.

    Ez a drámai optimalizálás különösen fontos számos big data-elemzési keretrendszer esetében. Az olyan eszközök, mint a Hive, a Spark stb. Hierarchikus névtér nélkül ez az átnevezés gyakran hosszabb időt vesz igénybe, mint maga az elemzési folyamat. Az alacsonyabb feladatkésés egyenlő az elemzési számítási feladatok teljes tulajdonlási költségével (TCO).

- **Ismerős felületstílus:** A fájlrendszereket a fejlesztők és a felhasználók egyaránt jól értik. Nincs szükség arra, hogy új tárolási paradigmát tanuljon, amikor a felhőbe költözik, mivel a Data Lake Storage Gen2 által elérhetővé tett fájlrendszer-felület ugyanaz a paradigma, amelyet a számítógépek használnak, nagy és kicsi.

Az egyik oka annak, hogy az objektumtárolók korábban nem támogatták a hierarchikus névteret, az, hogy a hierarchikus névtér korlátozza a skálázást. A Data Lake Storage Gen2 hierarchikus névtér azonban lineárisan skálázódik, és nem rontja sem az adatkapacitást, sem a teljesítményt.

## <a name="deciding-whether-to-enable-a-hierarchical-namespace"></a>Annak eldöntése, hogy engedélyezi-e a hierarchikus névteret

Miután engedélyezte a hierarchikus névteret a fiókjában, nem állíthatja vissza egy sima névtérbe. Ezért fontolja meg, hogy van-e értelme egy hierarchikus névtér engedélyezése az objektumtárolási számítási feladatok jellege alapján.

Előfordulhat, hogy egyes számítási feladatok nem nyernek semmilyen előnyt egy hierarchikus névtér engedélyezésével. Ilyenek például a biztonsági mentések, a lemezképek tárolása és más alkalmazások, ahol az objektumszervezést maguktól az objektumoktól elkülönítve tárolják (például egy külön adatbázisban). 

Emellett a Blob storage-funkciók és az Azure-szolgáltatási ökoszisztéma támogatása továbbra is növekszik, még mindig vannak olyan funkciók és Azure-szolgáltatások, amelyek még nem támogatottak a hierarchikus névtérrel rendelkező fiókokban. Lásd: [Ismert problémák](data-lake-storage-known-issues.md). 

Általában azt javasoljuk, hogy kapcsolja be a hierarchikus névtér a tárolási számítási feladatok, amelyek célja a könyvtárakat kezelő fájlrendszerekhez. Ez magában foglalja az összes számítási feladatok, amelyek elsősorban az elemzési feldolgozás. A magas szintű szervezést igénylő adatkészletek számára a hierarchikus névtér engedélyezése is előnyös.

A hierarchikus névtér engedélyezésének okait egy TCO-elemzés határozza meg. Általánosságban elmondható, hogy a tárolási gyorsítás miatti számítási késés javulása kevesebb ideig igényel számítási erőforrásokat. Számos számítási feladat késése továbbfejleszthető a hierarchikus névtér által engedélyezett atomi címtár-kezelés miatt. Sok számítási feladatok, a számítási erőforrás > 85%-át teszi ki a teljes költség, és így még egy szerény csökkentése munkaterhelés késés megegyezik a jelentős mennyiségű TCO megtakarítás. Még azokban az esetekben is, amikor a hierarchikus névtér engedélyezése növeli a tárolási költségeket, a tco még mindig csökken a csökkentett számítási költségek miatt.

Az adattárolási árak, a tranzakciós árak és a tárolási kapacitásfoglalásdíj-szabások különbségeinek elemzéséhez az egysíkú hierarchikus névtérrel és hierarchikus névtérrel rendelkező fiókok között az [Azure Data Lake Storage Gen2 díjszabása](https://azure.microsoft.com/pricing/details/storage/data-lake/)című témakörben található.

## <a name="next-steps"></a>További lépések

- [Tárfiók létrehozása](./data-lake-storage-quickstart-create-account.md)
