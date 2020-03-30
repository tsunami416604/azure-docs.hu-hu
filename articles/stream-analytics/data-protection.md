---
title: Adatvédelem az Azure Stream Analytics szolgáltatásban
description: Ez a cikk bemutatja, hogyan titkosíthatja az Azure Stream Analytics-feladat által használt személyes adatokat.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 1b3bdad0125b5bddbba20c8d807924fc3ea87e32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299396"
---
# <a name="data-protection-in-azure-stream-analytics"></a>Adatvédelem az Azure Stream Analytics szolgáltatásban 

Az Azure Stream Analytics egy teljes körűen felügyelt platform szolgáltatásként, amely lehetővé teszi, hogy valós idejű elemzési folyamatokat hozzon létre. Az összes nehéz emelés, például a fürt kiépítése, a csomópontok méretezése a használat nak megfelelően, és a belső ellenőrzőpontok kezelése, a színfalak mögött kezeli.

## <a name="encrypt-your-data"></a>Az adatok titkosítása

A Stream Analytics automatikusan a kategóriájában legjobb titkosítási szabványokat alkalmazza az infrastruktúrájában az adatok titkosításához és védelméhez. Egyszerűen megbízhat a Stream Analytics-ben, hogy biztonságosan tárolja az összes adatot, így nem kell aggódnia az infrastruktúra kezelése miatt.

Ha az ügyfelek által felügyelt kulcsokat (CMK) szeretné használni az adatok titkosításához, használhatja a saját tárfiókját (általános célú V1 vagy V2) a Stream Analytics futásideje által megkövetelt személyes adateszközök tárolására. A tárfiók szükség szerint titkosítható. A streamanalytics-infrastruktúra egyik személyes adateszközét sem tárolja tartósan. 

Ezt a beállítást a Stream Analytics-feladatlétrehozásakor kell konfigurálni, és nem módosítható a feladat teljes életciklusa során. A Stream Analytics által használt tárhely módosítása vagy törlése nem ajánlott. Ha törli a tárfiókot, véglegesen törli az összes személyes adatfedezetet, ami a feladat sikertelensítéséhez vezet. 

A streamanalytics-portál használatával nem lehet kulcsokat frissíteni vagy elforgatni a tárfiókba. A kulcsokat a REST API-k használatával frissítheti.


## <a name="configure-storage-account-for-private-data"></a>Tárfiók konfigurálása személyes adatokhoz 

Az alábbi lépésekkel konfigurálhatja a tárfiókot a személyes adateszközökhöz. Ez a konfiguráció a Stream Analytics-feladatból, nem pedig a tárfiókból készült.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra. 

1. Válassza az **Analytics** > **Stream Analytics-feladat lehetőséget** az eredménylistából. 

1. Töltse ki a Stream Analytics feladat oldalt a szükséges adatokkal, például névvel, régióval és méretezéssel. 

1. Jelölje be a *Storage-fiókban a feladathoz szükséges összes személyes adateszköz biztonságossá tétele jelölőnégyzetet.*

1. Válasszon ki egy tárfiókot az előfizetésből. Ne feledje, hogy ez a beállítás nem módosítható a feladat teljes életciklusa során. 

   ![Személyes adattárfiók beállításai](./media/data-protection/storage-account-create.png)

## <a name="private-data-assets-that-are-stored"></a>Tárolt személyes adateszközök

A Stream Analytics által megőrzött személyes adatokat a tárfiók tárolja. Példák a személyes adatokeszközeire: 

* A szerző által készített lekérdezések és a hozzájuk kapcsolódó konfigurációk  

* Felhasználó által meghatározott függvények 

* A Stream Analytics futásidejéhez szükséges ellenőrzőpontok

* Referenciaadatok pillanatképei 

A Stream Analytics-feladat által használt erőforrások kapcsolati adatai is tárolódnak. Titkosítsa a tárfiókot az összes adat védelme érdekében. 

Ha bármilyen szabályozott iparágban vagy környezetben eleget szeretne tenni megfelelőségi kötelezettségeinek, többet is olvashat a [Microsoft megfelelőségi ajánlatairól.](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) 

## <a name="next-steps"></a>További lépések

* [Azure Storage-fiók létrehozása](../storage/common/storage-account-create.md)
* [Az Azure Stream Analytics bemeneteinek megismerése](stream-analytics-add-inputs.md)
* [Ellenőrzőpont- és visszajátszási fogalmak az Azure Stream Analytics-feladatokban](stream-analytics-concepts-checkpoint-replay.md)
* [Referenciaadatok használata a Stream Analytics-ben lévő keresgélő műveletekhez](stream-analytics-use-reference-data.md)
