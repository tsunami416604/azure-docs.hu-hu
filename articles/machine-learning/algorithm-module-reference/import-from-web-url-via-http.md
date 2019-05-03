---
title: 'Importálás a HTTP-n keresztül, a webes URL-címe: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Útmutató az webes URL-címről az importálási keresztül az Azure Machine Learning szolgáltatás HTTP-modulja nyilvános weblap használható a machine learning-kísérletből adatokat olvasni.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 2f0847e9dd90267d985b75be3c3a07ce8fae98a9
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029670"
---
# <a name="import-from-web-url-via-http-module"></a>Z Webové adresy URL via HTTP-modul importálása

Ez a cikk ismerteti a vizuális felületen (előzetes verzió) az Azure Machine Learning szolgáltatás egy moduljához.

Ez a modul használatával olvassák az adatokat a machine learning-kísérletből használatra nyilvános weblapon.

Az alábbi korlátozások vonatkoznak a webhelyen közzétett adatok:

- Adatok a támogatott formátumok valamelyikében kell lennie: CSV, TSV, ARFF vagy SvmLight. Más adatok hibát okoznak.
- Nincs hitelesítés szükséges, vagy a támogatott. Adatok nyilvánosan elérhetőnek kell lennie. 

Az adatok a két módja van: a varázsló segítségével állítsa be az adatforrás, vagy manuálisan konfigurálja azt.

## <a name="use-the-data-import-wizard"></a>Az adatok importálása varázslójával

1. Adja hozzá a **adatok importálása** modult a kísérletvászonra. Annak a modul a felületen, a a **adatok bemeneti és kimeneti** kategória.

2. Kattintson a **importálása varázsló elindításához** , és válassza ki a webes URL-cím a HTTP Protokollon keresztül.

3. Illessze be az URL-címet, és válassza ki a adatok formátumát.

4. Ha konfigurációja befejeződött.

Az adatok meglévő kapcsolatok szerkesztésére, indítsa el újra a varázslót. A varázsló minden korábbi konfigurációs adatait betölti, így nem kell újra kezdenie a folyamatot

## <a name="manually-set-properties-in-the-import-data-module"></a>Manuálisan állítsa be a tulajdonságokat a az adatok importálása modullal

A következő lépések bemutatják, hogyan manuális konfigurálásához az importálás forrás.

1. Adja hozzá a [adatok importálása](import-data.md) modult a kísérletvászonra. Annak a modul a felületen, a a **adatok bemeneti és kimeneti** kategória.

2. A **adatforrás**válassza **webes URL-cím a HTTP Protokollon keresztül**.

3. A **URL-cím**írja be vagy illessze be a teljes URL-címét a betölteni kívánt adatokat tartalmazó oldalon.

    Az URL-címet tartalmaznia kell a webhely URL-címe és a fájl nevét és kiterjesztését, az oldal, amely tartalmazza az adatok betöltése a teljes elérési útja.

    Például a következő lapot tartalmaz az Írisz adatkészletet, a machine learning-tárházat a University of California, Irvine:

    `http://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data`

4. A **adatformátum**, válassza ki a támogatott adatok egyik formázza a listából.

    Azt javasoljuk, hogy mindig ellenőrizze az adatokat előzetesen formátumának meghatározásához. A UC Irvine lap a CSV formátumot használja. Egyéb támogatott formátumok a következők: TSV ARFF és SvmLight.

5. Ha az adatok CSV- vagy TSV formátumban, használja a **fájl rendelkezik fejlécsor** lehetőséget kiválasztva jelezze-e a forrásadatok tartalmaz egy fejléc sorra. A fejléc sorában segítségével rendelje hozzá az oszlopok neveit.

6. Válassza ki a **használja a gyorsítótárazott eredményeket** beállításai, ha várhatóan nem sokkal módosíthatja az adatokat, vagy ha el szeretné kerülni az újjáépítés az adatokat, minden egyes alkalommal, amikor futtathatja a kísérletet.

    Ezt a beállítást, ha a kísérlet betölti az adatokat az első idő a modul fut, és ezt követően a az adatkészlet egy gyorsítótárazott verzióját használja.

    Ha azt szeretné, az adatkészlet minden egyes megismételt kísérletet adatkészlet újbóli betöltéséhez, kapcsolja ki a **használja a gyorsítótárazott eredményeket** lehetőséget. Eredmények is újbóli, ha módosításokat paramétereihez [adatok importálása](import-data.md).

7. Futtassa a kísérletet.

## <a name="results"></a>Results (Eredmények)

Amikor végzett, kattintson a kimeneti adatkészletet, és válassza ki **Visualize** megtekintheti, ha az adatok importálása sikerült.


## <a name="next-steps"></a>További lépések

Tekintse meg a [modullistából készletét](module-reference.md) Azure Machine Learning szolgáltatáshoz. 