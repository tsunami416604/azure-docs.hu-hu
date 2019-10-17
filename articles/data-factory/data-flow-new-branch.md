---
title: Azure Data Factory leképezési adatfolyam új ág-átalakítás
description: Azure Data Factory leképezési adatfolyam új ág-átalakítás
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: de8cb74d788e3ca7599f226e4204c4b09112e70c
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387216"
---
# <a name="azure-data-factory-mapping-data-flow-new-branch-transformation"></a>Azure Data Factory leképezési adatfolyam új ág-átalakítás



![Ág beállításai](media/data-flow/menu.png "menüben")

Az elágazás a jelenlegi adatfolyamot az adatfolyamba helyezi, és replikálja egy másik streambe. Az új ág használatával több műveletet és átalakítást hajthat végre ugyanahhoz az adatfolyamhoz.

Példa: az adatfolyamnak van egy forrás-átalakítója, amely egy kijelölt oszlop-és adattípus-konverziót tartalmaz. Ezután egy származtatott oszlopot helyez el közvetlenül a forrás után. A származtatott oszlopban létrehozott egy új mezőt, amely egyesíti az utónév és a vezetéknév értékét, hogy új "teljes név" mezőt hozzon létre.

Az új streamet az átalakítások készletével és egy sorban lévő fogadóval is kezelheti, és az új ág használatával létrehozhatja a stream másolatát, ahol ugyanezeket az adattranszformációkat is átalakíthatja. Ha átalakítja a másolt adatágakat egy különálló ágban, ezt az adathalmazt később különálló helyre is elkülönítheti.

> [!NOTE]
> Az "új ág" csak akkor jelenik meg műveletként a + transzformációs menüben, ha az aktuális helyet követő átalakítás következik, ahol a ágat próbálja megkeresni. a végén itt nem jelenik meg az "új ág" lehetőség, amíg hozzá nem ad egy másik átalakítást a Select

![Ág](media/data-flow/branch2.png "2\. ág")
