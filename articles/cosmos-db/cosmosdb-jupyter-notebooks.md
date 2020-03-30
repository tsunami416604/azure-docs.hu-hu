---
title: Bevezetés a beépített Jupyter notebookok támogatásaz Azure Cosmos DB (előzetes verzió)
description: Megtudhatja, hogyan használhatja a beépített Jupyter notebookok támogatása az Azure Cosmos DB interaktív lekérdezések interaktív futtatásához.
ms.service: cosmos-db
ms.topic: overview
ms.date: 09/22/2019
author: markjbrown
ms.author: mjbrown
ms.openlocfilehash: 5b320485001d6cbc457d39ef193ed8c57f7161df
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "76760283"
---
# <a name="built-in-jupyter-notebooks-support-in-azure-cosmos-db-preview"></a>Beépített Jupyter-jegyzetfüzetek támogatása az Azure Cosmos DB-ben (előzetes verzió)

A Jupyter notebook egy nyílt forráskódú webes alkalmazás, amely lehetővé teszi élő kódot, egyenleteket, vizualizációkat és narratív szöveget tartalmazó dokumentumok létrehozását és megosztását. Az Azure Cosmos DB támogatja a beépített Jupyter-jegyzetfüzeteket az összes API-hoz, például a Cassandra, a MongoDB, az SQL, a Gremlin és a Table. A beépített notebook támogatás az összes Azure Cosmos DB API-k és adatmodellek lehetővé teszi, hogy interaktívan lekérdezések futtatásához. A Jupyter-jegyzetfüzetek az Azure Cosmos-fiókokon belül futnak, és lehetővé teszik a fejlesztők számára az adatok feltárását, az adatok tisztítását, az adatátalakításokat, a numerikus szimulációkat, a statisztikai modellezést, az adatmegjelenítést és a gépi tanulást.

![Jupyter-jegyzetfüzetek vizualizációi az Azure Cosmos DB-ben](./media/cosmosdb-jupyter-notebooks/cosmos-notebooks-overview.png)

A Jupyter notebookok támogatja a mágikus funkciókat, amelyek további parancsok támogatásával bővítik a kernel képességeit. A Cosmos magic egy olyan parancs, amely kiterjeszti a Python kernel képességeit a Jupyter-jegyzetfüzetben, így az Apache Spark mellett azure Cosmos SQL API-lekérdezéseket is futtathat. A Python- és SQL API-lekérdezéseket egyszerűen kombinálhatja az adatok lekérdezéséhez és megjelenítéséhez a renderelési parancsokkal integrált gazdag vizualizációs kódtárak használatával.
Az Azure Portal natív módon integrálja a Jupyter-jegyzetfüzet-élményt az Azure Cosmos-fiókokba az alábbi képen látható módon:

![A Jupyter-jegyzetfüzetek támogatása az Azure Cosmos DB-ben](./media/cosmosdb-jupyter-notebooks/jupyter-notebooks-portal.png)

## <a name="benefits-of-jupyter-notebooks"></a>A Jupyter notebookok előnyei

A Jupyter notebookokat eredetileg Python, R nyelven írt adattudományi alkalmazásokhoz fejlesztették ki. Ezek azonban különböző módokon használhatók különböző típusú projektekhez, mint például:

* ***Adatvizualizációk:** A Jupyter-jegyzetfüzetek lehetővé teszik az adatok megjelenítését megosztott jegyzetfüzet formájában, amely bizonyos adatkészleteket grafikusként jelenít meg. A Jupyter-jegyzetfüzet lehetővé teszi vizualizációk szerkesztését, valamint a megosztott kód és adatkészlet interaktív módosításának engedélyezését.

* **Kódmegosztás:** Az olyan szolgáltatások, mint a GitHub, lekínálják a kódmegosztás módját, de nagyrészt nem interaktívak. A Jupyter-jegyzetfüzetek segítségével megtekintheti a kódot, végrehajthatja azt, és az eredményeket közvetlenül az Azure Portalon jelenítheti meg.

* **Élő interakciók kóddal:** A Jupyter notebook kódja dinamikus; szerkeszthető, és valós időben újra futtatható. A jegyzetfüzetek olyan felhasználói vezérlőket (pl. csúszkákat vagy szövegbeviteli mezőket) is beágyazhatnak, amelyek kód, bemutatók vagy a koncepciók igazolása (POC-k) bemeneti forrásaként használhatók.

* **A kódminták dokumentációja és az adatfeltárás eredményei:** Ha rendelkezik egy kóddarabbal, és el szeretné magyarázni, hogyan működik az Azure Cosmos DB-ben, és az út során valós idejű kimenettel együtt beágyazza azt egy Jupyter-jegyzetfüzetbe. A kód továbbra is teljes mértékben működőképes marad. A dokumentációval együtt egyidejűleg is hozzáadhat interaktivitást.

* **Cosmos mágikus parancsok:** A Jupyter-jegyzetfüzetekben az Azure Cosmos DB egyéni mágikus parancsaival megkönnyítheti az interaktív számítástechnikát. Például a %%sql magic, amely lehetővé teszi, hogy lekérdezegy Cosmos-tároló t az SQL API használatával közvetlenül a jegyzetfüzetben.

* **Minden egy helyen környezetben:** A Jupyter notebookok egyetlen dokumentumba egyesítik a kódot, a rich text-et, a képeket, a videókat, az animációkat, a matematikai egyenleteket, a telkeket, a térképeket, az interaktív ábrákat, a widgeteket és a grafikus felhasználói felületeket.

## <a name="components-of-a-jupyter-notebook"></a>Jupyter notebook összetevői

A Jupyter notebookok többféle összetevőt tartalmazhatnak, amelyek mindegyike különálló blokkokba rendezve:

* **Szöveg és HTML:** Az egyszerű szöveg vagy a html létrehozásához a markdown szintaxisban jegyzetekbe jegyzetelt szöveg bármikor beilleszthető a dokumentumba. CsS stílus is szerepela vonalon, vagy hozzá a sablon létrehozásához használt notebook.

* **Kód és kimenet:** A Jupyter notebookok támogatják a Python-kódot. A végrehajtott kód eredményei közvetlenül a kódblokkok után jelennek meg, és a kódblokkok többször is végrehajthatók tetszőleges sorrendben.

* **Vizualizációk:** Grafikák és diagramok generálhatók a kódból olyan modulok használatával, mint a Matplotlib, a Plotly vagy a Bokeh. A kimenethez hasonlóan ezek a vizualizációk az azokat létrehozó kód mellett szövegközien jelennek meg.

* **Multimédia:** Mivel a Jupyter notebook a webes technológiára épül, képes megjeleníteni a weboldalon támogatott összes multimédiás típust. A jegyzetfüzetbe HTML-elemként is felveheti őket, vagy a `IPython.display` modul használatával programozott módon is létrehozhatja őket.

* **Adatok:** Az Azure Cosmos-tárolókból származó adatok és a lekérdezések eredményei programozott módon importálhatók egy Jupyter-jegyzetfüzetbe. Például a kód a jegyzetfüzetben az adatok lekérdezéséhez a Cosmos DB API-k vagy natívan beépített Apache Spark használatával.

## <a name="next-steps"></a>További lépések

Az Azure Cosmos DB beépített Jupyter-jegyzetfüzeteinek első lépései az alábbi cikkekben:

* [Jegyzetfüzetek engedélyezése Azure Cosmos-fiókban](enable-notebooks.md)
* [Jegyzetfüzet-szolgáltatások és -parancsok használata](use-notebook-features-and-commands.md)



