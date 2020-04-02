---
title: Az azure Data Explorer betöltéséhez egykattintásos betöltéssel
description: Az adatok Azure Data Explorerbe való betöltésének (betöltésének áttekintése) egyszerűen, egy kattintással történő betöltéssel.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: overview
ms.date: 03/29/2020
ms.openlocfilehash: 5bde63427ce76f14832551864bbf2c3d8e015fd6
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521588"
---
# <a name="what-is-one-click-ingestion"></a>Mi az egykattintásos betöltés? 

Az egykattintásos betöltés lehetővé teszi az adatok gyors betöltését, és automatikusan javaslatokat tesz táblák és leképezési struktúrák javasolására az Azure Data Explorer adatforrása alapján. 

Az Azure Data Explorer webes felhasználói felületén adatokat vihet be a tárolóból (blobfájl), egy helyi fájlból vagy egy tárolóból (legfeljebb 10 000 blobból). A folyamatos betöltéshez egy tárolón is definiálhat eseményrácsot. Az adatok json, CSV és más formátumokban meglévő vagy új táblába is [bevihetők.](#file-formats) Egy kattintással betöltés javasolhat egy struktúrát egy új tábla és tábla leképezéséhez az adatforrás alapján, és intuitív platformot biztosít egy meglévő tábla és tábla leképezés táblaszerkezetének módosításához. Egy kattintással betöltése az adatokat a táblába néhány percen belül.

Az egykattintásos betöltés különösen akkor hasznos, ha először ad be adatokat, vagy ha az adatok sémája ismeretlen az Ön számára.

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* Hozzon létre [egy Azure Data Explorer-fürtöt és -adatbázist.](create-cluster-database-portal.md)
* Jelentkezzen be az [Azure Data Explorer webes felhasználói felületére,](https://dataexplorer.azure.com/) és adjon hozzá egy kapcsolatot a [fürthöz.](/azure/data-explorer/web-query-data#add-clusters)

## <a name="file-formats"></a>Fájlformátumok

Az egykattintásos betöltés a következő formátumok bármelyikében támogatja az új tábla forrásadatokból történő betöltését:
* JSON
* CSV
* Tsv
* SCSV
* SzOHSV
* TSVE
* Psv

## <a name="one-click-ingestion-wizard"></a>Egykattintásos betöltési varázsló

Az egykattintásos betöltési varázsló végigvezeti az egykattintásos betöltési folyamaton. 

> [!Note]
> Ez a szakasz általában a varázslót ismerteti. A kiválasztott beállítások attól függnek, hogy új vagy meglévő táblába szeretne-e belépni. További információkért lásd:
    > * Betöltés [egy új táblába](one-click-ingestion-new-table.md)
    > * Betöltés meglévő [táblába](one-click-ingestion-existing-table.md) 
    
1. A varázsló eléréséhez kattintson a jobb gombbal az Azure Data Explorer webes felhasználói felületének bal oldali menüjében lévő *adatbázisra* vagy táblasorra, és válassza az **új adatok betöltése (előnézet) parancsot.** *table*

    ![Egyetlen kattintásos betöltés kijelölése a webes felhasználói felületen](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   

1. A varázsló végigvezeti a következő lehetőségeken:
       * Betöltés meglévő [táblába](one-click-ingestion-existing-table.md)
       * Betöltés [egy új táblába](one-click-ingestion-new-table.md)
       * Adatok betöltése a következőből: * Blob storage * Helyi fájl * Tároló
       * Adja meg a minta méretét 1 és 10 000 sor között (csak a tárolóból)
       
1. Miután sikeresen kiválasztotta az adatforrást, megjelenik az adatok előnézete. 
    Ha tárolóból távolítja be az adatokat, szűrheti az adatokat, hogy csak adott előtagokkal vagy fájlkiterjesztésekkel rendelkező fájlokat kell betöltése. Előfordulhat például, hogy csak *az Európa*szóval kezdődő fájlnévvel rendelkező fájlokat szeretné beadni, vagy csak *a .json*kiterjesztésű fájlokat. 

1. Kattintson **a Séma szerkesztése gombra.** Ha adatokat ad be egy adott táblába, leképezheti a forrásoszlopokat a céloszlopokhoz, és eldöntheti, hogy szerepel-e az oszlopnevek között.

1. Indítsa el az adatbetöltési folyamatot.

> [!Note]
> Ha az adatforrás egy tároló, vegye figyelembe, hogy az Azure Data Explorer adatbetöltési összesítési (kötegelési) szabályzata a betöltési folyamat optimalizálására szolgál. Alapértelmezés szerint a házirend 5 perc vagy 500 MB adatra van konfigurálva, így késést tapasztalhat. Az [batching policy](/azure/kusto/concepts/batchingpolicy) összesítési beállításokat a kötegelési házirendben láthatja. Más forrásokból származó adatok betöltésekor a betöltés azonnali hatállyal érvénybe lép.

## <a name="next-steps"></a>További lépések

* Döntse el, hogy egy kattintással betöltéssel beszeretné-e adni az adatokat [egy meglévő vagy egy](one-click-ingestion-existing-table.md) új [táblába.](one-click-ingestion-new-table.md)
* [Adatok lekérdezése az Azure Data Explorer webes felhasználói felületén](/azure/data-explorer/web-query-data)
* [Lekérdezések írása az Azure Data Explorer hez a Kusto lekérdezési nyelv használatával](/azure/data-explorer/write-queries)