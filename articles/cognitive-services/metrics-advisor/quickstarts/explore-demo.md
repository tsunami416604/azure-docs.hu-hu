---
title: A metrikai tanácsadó bemutatójának elindítása
titleSuffix: Azure Cognitive Services
description: A metrikák Advisor webes felületének megismerése az általunk biztosított bemutató használatával
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: quickstart
ms.date: 09/14/2020
ms.author: aahi
ms.openlocfilehash: fbc73e20b8cc2baa1cc5c5a5b2f674fb1b2dde84
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "90947226"
---
# <a name="quickstart-explore-the-metrics-advisor-demo-with-example-data"></a>Gyors útmutató: a metrikai tanácsadó bemutatójának bemutatása példaként megadott adatokkal

> [!Note]
> A metrikai tanácsadó bemutatója csak olvasható, és nem fog tudni adatokat készíteni. A szolgáltatás adatokon való használatához először [hozzon létre egy metrikai Advisor-erőforrást](web-portal.md).

Ebből a cikkből megtudhatja, hogyan kezdheti el a metrikák Advisor legfontosabb funkcióinak megismerését. A bemutató helyet a mintaadatok és az előre beállított konfigurációk biztosítják, így megismerheti a teljes funkcionalitású webes portált.

Kattintson [erre a hivatkozásra](https://aka.ms/MetricsAdvisor/Demo) a bemutató webhelyhez való ugráshoz.

## <a name="view-the-available-sample-data"></a>Az elérhető mintaadatok megtekintése

Miután bejelentkezett a bemutató webhelyre, egy **adatcsatorna** -oldal jelenik meg. az adatcsatorna az idősoros adatok logikai csoportja, amelyet az adatforrásból kell lekérdezni. A metrikai tanácsadóban használt feltételekkel és fogalmakkal kapcsolatos további információkért tekintse meg a [szószedetet](../glossary.md). 

Több adatcsatorna van felsorolva, amelyek különböző típusú adatforrásokból (például Azure SQL Database vagy Azure Table) vannak betöltve. Az egyesek némileg eltérő konfigurációs beállításokat használnak a társított adattárakhoz való kapcsolódáshoz.

:::image type="content" source="../media/sample-datafeeds.png" alt-text="Mintaadatok listája" lightbox="../media/sample-datafeeds.png":::

## <a name="explore-the-data-feed-configurations"></a>Az adatcsatornák konfigurációinak megismerése

Kattintson a *minta – ár/bevétel – város/kategória* adatcsatornára. A hírcsatorna több részletét fogja látni:

* Az adatcsatorna neve és a betöltés állapota.
* Az adatforrásból lekérdezett metrikák listája. Például a *Cost* és a *bevétel*. 
* Riasztási előzmények, amikor az adatcsatorna elérhetetlenné válik. 
* Az adatcsatorna frissítésének naplózása.   
* Adatcsatorna adatai és beállításai.

:::image type="content" source="../media/data-feed-view.png" alt-text="Mintaadatok listája" lightbox="../media/data-feed-view.png":::


## <a name="view-time-series-visualizations-and-configurations"></a>Idősorozat-vizualizációk és-konfigurációk megtekintése

Kattintson a *Cost* mérőszámra a *minta-Cost/Revenue-City/category* adatcsatornán. A társított idősorozatot dimenziók szerint darabolva láthatja, a korábbi metrikai adatoknak megfelelő vizualizációkkal. A metrikai adatok körüli kék sáv a metrikai tanácsadó gépi tanulási modelljeiből származó várt értéket jelöli. A sávon kívüli pontok piros pontokként lesznek megjelölve, amelyek rendellenességeket észleltek. 

:::image type="content" source="../media/series-visualization.png" alt-text="Mintaadatok listája" lightbox="../media/series-visualization.png":::

Az anomáliák észlelése konfigurálható úgy, hogy az **észlelési konfigurációkat** a metrika részletei lap bal oldalán hangolja. Több anomália-észlelési módszer is rendelkezésre áll, és kombinálhatja őket. Emellett különböző érzékenységeket, észlelési irányokat és egyéb konfigurációkat is kipróbálhat. A **konfigurációk észlelésének** alján található **Speciális konfigurációs** hivatkozás lehetővé teszi, hogy összetettebb és testreszabott észlelési beállításokat hozzon létre, amelyek csoportok vagy különálló adatsorozatok esetében használhatók. 

Az észlelési algoritmus visszajelzésének megadásával is beállíthatja a anomáliák észlelését. Kattintson a anomáliára, majd a **visszajelzés hozzáadása** panelen konfigurálja az anomália állapotát, a szezonális és a változási pont állapotát. Ez a visszajelzés a jövőbeli pontok észleléséhez lesz beépítve.  

A **visszajelzés hozzáadása** panel alján található egy hivatkozás az **incidens**-központra, amely az incidensek elemzése oldalra irányítja, és az incidens kiváltó okát elemezni fogja.  

:::image type="content" source="../media/incident-link.png" alt-text="Mintaadatok listája" lightbox="../media/incident-link.png":::

## <a name="explore-anomaly-detection-results-and-perform-root-cause-analysis"></a>Az anomáliák észlelési eredményeinek megismerése és az alapvető okok elemzésének végrehajtása

Ha egy rendellenességből rákattint a **to incidens hub** hivatkozásra, megjelenik egy incidens-elemzési oldal, amely diagnosztikai elemzéseket tartalmaz az incidensről, például a súlyosságról, az érintett rendellenességek számáról, valamint a kezdő/befejező időpontról. A **kiváltó ok** szakasz az incidensek fájának elemzésével mutatja be az automatizált tanácsokat: eltérés, eloszlás és a szülő rendellenességekhez való hozzájárulás, amely az incidens kiváltó oka lehet.

A **diagnosztika** szakasz az incidens fáját mutatja be, valamint az incidens diagnosztizálásának több lapját.

:::image type="content" source="../media/incident-diagnostic.png" alt-text="Mintaadatok listája" lightbox="../media/incident-diagnostic.png":::

Az incidens kiváltó okának kijavításával elvégezheti a probléma megoldását, és csökkentheti a problémát, mielőtt a helyzet még rosszabb lesz. További információkhoz is tájékozódhat, ha a további diagnosztikai funkciókra kattint. 

## <a name="next-steps"></a>További lépések

- [A webes portál használata](web-portal.md)
- [A REST API használata](rest-api.md)
- [Adatcsatornák előkészítése](../how-tos/onboard-your-data.md)
    - [Adatcsatornák kezelése](../how-tos/manage-data-feeds.md)
    - [Különböző adatforrások beállításai](../data-feeds-from-different-sources.md)
