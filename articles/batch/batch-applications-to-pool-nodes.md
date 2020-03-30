---
title: Alkalmazások és adatok másolása készletcsomópontokba
description: Ismerje meg, hogyan másolhatja az alkalmazásokat és az adatokat a gyűjtőcsomópontokba.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/17/2020
ms.author: labrenne
ms.openlocfilehash: 226a0d69ac387142ecf580537e35f8754ac848a6
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385583"
---
# <a name="copying-applications-and-data-to-pool-nodes"></a>Alkalmazások és adatok másolása készletcsomópontokba

Az Azure Batch számos módot támogat az adatok és alkalmazások számítási csomópontokra való beszerzésére, hogy az adatok és az alkalmazások feladatok által isztebájhoz is elérhetők. Adatokra és alkalmazásokra lehet szükség a teljes feladat futtatásához, ezért minden csomópontra telepíteni kell. Néhány lehet, hogy csak egy adott feladathoz szükséges, vagy telepíteni kell a feladathoz, de nem kell minden csomóponton lennie. A Batch mindegyik forgatókönyvhöz rendelkezik eszközökkel.

- **Készlet indítási tevékenységerőforrás-fájljai**: Olyan alkalmazások vagy adatok esetén, amelyeket a készlet minden csomópontjára telepíteni kell. A telepítési parancs végrehajtásához használja ezt a módszert egy alkalmazáscsomaggal vagy a kezdő feladat erőforrásfájl-gyűjteményével együtt.  

Példák: 
- Alkalmazások áthelyezése és telepítése a kezdő feladat parancssorával

- Adja meg az Azure-tárfiókban lévő konkrét fájlok vagy tárolók listáját. További információ: [Add#resourcefile in REST documentation](https://docs.microsoft.com/rest/api/batchservice/pool/add#resourcefile)

- A készleten futó minden feladat a MyApplication.exe programot futtatja, amelyet először a MyApplication.msi alkalmazással kell telepíteni. Ha ezt a mechanizmust használja, be kell állítania a kezdő feladat **várakozását a sikeres tulajdonság** **true**értékre. További információt a [REST dokumentációjában található add#starttask című témakörben talál.](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask)

- **Alkalmazáscsomag-hivatkozások** a készleten: Olyan alkalmazások vagy adatok esetén, amelyeket a készlet minden csomópontjára telepíteni kell. Alkalmazáscsomaghoz nincs telepítési parancs társítva, de a telepítési parancsok futtatásához használhat indítási feladatot. Ha az alkalmazás nem igényel telepítést, vagy nagy számú fájlból áll, használhatja ezt a módszert. Az alkalmazáscsomagok nagy számú fájl hozadékához alkalmasak, mivel nagyszámú fájlhivatkozást kombinálnak egy kis hasznos adatba. Ha több mint 100 különálló erőforrásfájlt próbál meg egyetlen feladatba foglalni, előfordulhat, hogy a Batch szolgáltatás egyetlen feladat belső rendszerkorlátaival ütközik. Akkor is használjon alkalmazáscsomagokat, ha szigorú verziószámozási követelményekkel rendelkezik, ahol előfordulhat, hogy ugyanazon alkalmazás számos különböző verziójával rendelkezik, és választania kell közülük. További információért olvassa [el az Alkalmazások telepítése a Batch alkalmazáscsomagokat tartalmazó csomópontok számítási](https://docs.microsoft.com/azure/batch/batch-application-packages)adatait.

- **Feladat-előkészítési feladat erőforrásfájljai**: Olyan alkalmazások vagy adatok esetén, amelyeket telepíteni kell a feladat futtatásához, de nem kell a teljes készletre telepíteni. Például: ha a készlet számos különböző típusú feladattal rendelkezik, és csak egy feladattípus futtatásához van szüksége a MyApplication.msi fájlnak, célszerű a telepítési lépést feladat-előkészítési feladatba helyezni. A feladat-előkészítési feladatokról további információt a [Feladat-előkészítési és feladatkiadási feladatok futtatása kötegelt számítási csomópontokon című témakörben talál.](https://azure.microsoft.com/documentation/articles/batch-job-prep-release/)

- **Feladaterőforrás-fájlok**: Ha egy alkalmazás vagy adatok csak egy adott feladatra vonatkoznak. Például: Öt feladat, mindegyik feldolgoz egy másik fájlt, majd írja a kimenetet blob storage.  Ebben az esetben a bemeneti fájlt meg kell adni a **feladatok erőforrásfájl-gyűjteményében,** mert minden feladatnak saját bemeneti fájlja van.

## <a name="determine-the-scope-required-of-a-file"></a>Fájl szükséges hatókörének meghatározása

Meg kell határoznia a fájl hatókörét - ez egy készlethez, feladathoz vagy feladathoz szükséges fájl. A készlethez hatókörrel beálló fájloknak készletalkalmazás-csomagokat vagy indítási feladatot kell használniuk. A feladathoz hatókörrel beálló fájloknak feladat-előkészítési feladatot kell használniuk. A készlet vagy a feladat szintjén hatókörben lévő fájlok rakható fájlokra jó példa az alkalmazások. A feladathoz tartozó fájloknak feladat-erőforrás fájlokat kell használniuk.

### <a name="other-ways-to-get-data-onto-batch-compute-nodes"></a>Az adatok Batch számítási csomópontokra történő bekerülésének egyéb módjai

Más módon is beszerezheti az adatokat a Batch számítási csomópontokra, amelyek hivatalosan nincsenek integrálva a Batch REST API-ba. Mivel ön rendelkezik az Azure Batch-csomópontok felett, és egyéni végrehajtható fájlok futtatásához, tetszőleges számú egyéni forrásból lehívhat adatokat, feltéve, hogy a Batch-csomópont rendelkezik a célkapcsolattal, és rendelkezik az adott forrás hitelesítő adataival az Azure Batch-csomópontra. Néhány gyakori példa:

- Adatok letöltése az SQL-ből
- Adatok letöltése más webszolgáltatásokból/egyéni helyekről
- Hálózati megosztás hozzárendelése

### <a name="azure-storage"></a>Azure Storage-tárterület

A Blob Storage letöltési méretezhetőségi célokkal rendelkezik. Az Azure storage-fájlmegosztásméretezhetőségi céljai megegyeznek az egyetlen blobok méretezhetőségi céljaival. A méret hatással lesz a szükséges csomópontok és készletek számára.

