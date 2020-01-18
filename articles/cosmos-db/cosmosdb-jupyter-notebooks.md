---
title: A beépített Jupyter notebookok támogatásának bemutatása Azure Cosmos DB (előzetes verzió)
description: Megtudhatja, hogyan használhatja a Azure Cosmos DB beépített Jupyter notebook-támogatását a lekérdezések interaktív futtatásához.
ms.service: cosmos-db
ms.topic: overview
ms.date: 09/22/2019
author: markjbrown
ms.author: mjbrown
ms.openlocfilehash: 5c6d7ae458c30d462c599042e254f4243daf474c
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2020
ms.locfileid: "76260871"
---
# <a name="built-in-jupyter-notebooks-support-in-azure-cosmos-db"></a>A beépített Jupyter notebookok támogatása Azure Cosmos DB

A Jupyter notebook egy nyílt forráskódú webes alkalmazás, amellyel élő kódokat, egyenleteket, vizualizációkat és narratív szövegeket tartalmazó dokumentumokat hozhat létre és oszthat meg. Azure Cosmos DB támogatja a beépített Jupyter jegyzetfüzeteket minden olyan API-hoz, mint például a Cassandra, a MongoDB, az SQL, a Gremlin és a table. Az összes Azure Cosmos DB API-k és adatmodellek beépített notebook-támogatása lehetővé teszi a lekérdezések interaktív futtatását. A Jupyter-jegyzetfüzetek az Azure Cosmos-fiókokon belül futnak, és lehetővé teszik a fejlesztők számára az adatok feltárását, az adatok tisztítását, az adatátalakítások, a numerikus szimulációk, a statisztikai modellezés, az adatvizualizáció és a gépi tanulás végrehajtását.

![Jupyter notebook-vizualizációk Azure Cosmos DB](./media/cosmosdb-jupyter-notebooks/cosmos-notebooks-overview.png)

A Jupyter notebookok olyan mágikus funkciókat támogatnak, amelyek további parancsok támogatásával bővítik a kernel képességeit. A Cosmos Magic egy olyan parancs, amely kibővíti a Python kernel képességeit a Jupyter notebookon, így a Apache Sparkon kívül az Azure Cosmos SQL API-lekérdezéseket is futtathatja. A Python és az SQL API-lekérdezések könnyedén kombinálhatók a renderelési parancsokkal integrált, Rich vizualizációs kódtárak használatával történő lekérdezéséhez és megjelenítéséhez.
Azure Portal natív módon integrálja a Jupyter notebook-élményt az Azure Cosmos-fiókokba az alábbi ábrán látható módon:

![A Jupyter notebookok támogatása Azure Cosmos DB](./media/cosmosdb-jupyter-notebooks/jupyter-notebooks-portal.png)

## <a name="benefits-of-jupyter-notebooks"></a>A Jupyter notebookok előnyei

A Jupyter notebookok eredetileg a Pythonban, az R-ben írt adatelemzési alkalmazásokhoz lettek kifejlesztve. Azonban különböző módokon is használhatók különböző típusú projektekhez, például a következőkhöz:

* ***adatvizualizációk:** a Jupyter-jegyzetfüzetek lehetővé teszik, hogy egy megosztott jegyzetfüzet formájában jelenítse meg az adott adathalmazt, amely egy adatkészletet grafikus formában jelenít meg. A Jupyter notebook segítségével vizualizációkat hozhat létre, megoszthatja őket, és engedélyezheti a megosztott kód és az adathalmaz interaktív módosításait.

* **Kód megosztása:** Az olyan szolgáltatások, mint például a GitHub, a kódok megosztására szolgálnak, de nagyrészt nem interaktívak. A Jupyter notebookon megtekintheti a kódot, végrehajthatja és megjelenítheti az eredményeket közvetlenül a Azure Portal.

* **Élő interakciók kóddal:** A Jupyter notebook kód dinamikus; szerkeszthető, és a növekményes futtatását valós időben végezheti el. A jegyzetfüzetek olyan felhasználói vezérlőket (például csúszkákat vagy szövegbeviteli mezőket) is beágyaznak, amelyek bemeneti forrásként használhatók kód, bemutatók vagy fogalmak igazolására (Pócsként).

* **A kód mintáinak és az adatfeltárás eredményeinek dokumentálása:** Ha egy kódrészlettel rendelkezik, és szeretné elmagyarázni, hogyan működik a Azure Cosmos DBban, a valós idejű kimenettel együtt, akkor beágyazhatja azt egy Jupyter Notebookba. A kód teljes mértékben működőképes marad. A dokumentációval egyidejűleg is hozzáadhat interaktivitást.

* **Cosmos Magic parancsok:** A Jupyter-jegyzetfüzetekben egyéni Magic-parancsokat használhat a Azure Cosmos DB számára, hogy egyszerűbb legyen az interaktív számítástechnika. Például a (z)%% SQL Magic, amely lehetővé teszi, hogy egy Cosmos-tárolót az SQL API használatával közvetlenül egy jegyzetfüzetben Kérdezzen le.

* **Minden egy helyen lévő környezetben:** A Jupyter-jegyzetfüzetek kód, Rich Text, images, videos, animáció, matematikai egyenletek, ábrázolások, térképek, interaktív számok, widgetek és grafikus felhasználói felületek egyetlen dokumentumba való egyesítésére használhatók.

## <a name="components-of-a-jupyter-notebook"></a>Jupyter-jegyzetfüzet összetevői

A Jupyter notebookok többféle típusú összetevőt tartalmazhatnak, amelyek mindegyike különálló blokkokban van csoportosítva:

* **Szöveg és HTML:** A HTML létrehozásához a Markdown szintaxisában szereplő egyszerű szöveg vagy szöveg, amely bármikor beszúrható a dokumentumba. A CSS-stílus a beágyazott vagy a jegyzetfüzet létrehozásához használt sablonba is felvehető.

* **Kód és kimenet:** A Jupyter notebookok támogatják a Python-kódokat. A végrehajtott kód eredményei közvetlenül a kód blokkolása után jelennek meg, és a kódrészletek többször is végrehajthatók tetszőleges sorrendben.

* **Vizualizációk:** Grafikus elemek és diagramok hozhatók létre a kódban, olyan modulok használatával, mint a Matplotlib, a Plot vagy a bokeh. A kimenethez hasonlóan ezek a vizualizációk a generált kód mellett jelennek meg.

* **Multimédia:** Mivel a Jupyter notebook a webes technológiára épül, a weblapokon támogatott összes multimédia-típust megjelenítheti. A jegyzetfüzetben HTML-elemekként is megadhatja őket, vagy programozott módon létrehozhatja őket a `IPython.display` modul használatával.

* **Adatértékek:** Az Azure Cosmos-tárolóból származó adatok és a lekérdezések eredményei programozott módon importálhatók Jupyter notebookba. Például a jegyzetfüzetben szereplő kóddal, amely az Cosmos DB API-k vagy natívan beépített Apache Spark használatával kérdezi le az összes adatát.

## <a name="next-steps"></a>Következő lépések

A Azure Cosmos DB beépített Jupyter-jegyzetfüzetekkel kapcsolatos első lépéseiért tekintse meg a következő cikkeket:

* [Jegyzetfüzetek engedélyezése Azure Cosmos-fiókban](enable-notebooks.md)
* [Jegyzetfüzet-funkciók és-parancsok használata](use-notebook-features-and-commands.md)



