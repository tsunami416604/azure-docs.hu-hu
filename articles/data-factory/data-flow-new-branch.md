---
title: Azure Data Factory leképezési adatfolyam új ág-átalakítás
description: Azure Data Factory leképezési adatfolyam új ág-átalakítás
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 3f443396627a4bbaba2a3eeb0a3ac05cc1597c85
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029288"
---
# <a name="azure-data-factory-mapping-data-flow-new-branch-transformation"></a>Azure Data Factory leképezési adatfolyam új ág-átalakítás



![Ág beállításai](media/data-flow/menu.png "menü")

Az elágazás a jelenlegi adatfolyamot az adatfolyamba helyezi, és replikálja egy másik streambe. Az új ág használatával több műveletet és átalakítást hajthat végre ugyanahhoz az adatfolyamhoz.

Példa: Az adatfolyamnak van egy forrás-átalakítója, amely egy kijelölt oszlop-és adattípus-konverzióval rendelkezik. Ezután egy származtatott oszlopot helyez el közvetlenül a forrás után. A származtatott oszlopban létrehozott egy új mezőt, amely egyesíti az utónév és a vezetéknév értékét, hogy új "teljes név" mezőt hozzon létre.

Az új streamet az átalakítások készletével és egy sorban lévő fogadóval is kezelheti, és az új ág használatával létrehozhatja a stream másolatát, ahol ugyanezeket az adattranszformációkat is átalakíthatja. Ha átalakítja a másolt adatágakat egy különálló ágban, ezt az adathalmazt később különálló helyre is elkülönítheti.

> [!NOTE]
> Az "új ág" csak akkor jelenik meg műveletként a + transzformációs menüben, ha az aktuális helyet követő átalakítás következik, ahol a ágat próbálja megkeresni. a végén itt nem jelenik meg az "új ág" lehetőség, amíg hozzá nem ad egy másik átalakítást a Select

![Ág](media/data-flow/branch2.png "ág 2")
