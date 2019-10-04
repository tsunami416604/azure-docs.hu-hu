---
title: 'Importálás webes URL-címről HTTP-n keresztül: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan használhatja az importálás webes URL-címről HTTP-modulon keresztül a Azure Machine Learning szolgáltatásban az adatok egy nyilvános weboldalról való beolvasásához a gépi tanulási kísérletekben való használatra.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 59b8e2e73b9904a503c16d8891e5a5bd771fc87f
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128756"
---
# <a name="import-from-web-url-via-http-module"></a>Importálás webes URL-címről HTTP-modul használatával

Ez a cikk a Azure Machine Learning szolgáltatás vizuális felületének (előzetes verzió) modulját ismerteti.

Ezzel a modullal olvashatók be az adatok egy nyilvános weboldalról a Machine learning-kísérletekben való használatra.

A következő korlátozások vonatkoznak a weblapokon közzétett adatszolgáltatásokra:

- Az adatmennyiségnek a támogatott formátumok egyikében kell lennie: CSV, TSV, ARFF vagy SvmLight. Az egyéb adathibák hibát okoznak.
- Nincs szükség hitelesítésre vagy nem támogatott. Az adatszolgáltatásnak nyilvánosan elérhetőnek kell lennie. 

Kétféleképpen lehet beolvasni az adatforrást: használja a varázslót az adatforrás beállításához, vagy konfigurálja manuálisan.

## <a name="use-the-data-import-wizard"></a>Az adatimportálás varázsló használata

1. Adja hozzá az Adatimportálási modult a kísérlethez. A modult a felületen, az **adatok bemenete és a kimenet** kategóriában találja.

2. Kattintson **az adatimportálás varázsló indítása** lehetőségre, és válassza a webes URL-cím http-n keresztül lehetőséget.

3. Illessze be az URL-címet, és válassza ki az adatformátumot.

4. Ha a konfigurálás befejeződött.

Meglévő adatkapcsolatok szerkesztéséhez indítsa újra a varázslót. A varázsló betölti az összes korábbi konfigurációs részletet, így nem kell újból elindítania

## <a name="manually-set-properties-in-the-import-data-module"></a>Tulajdonságok manuális beállítása az Adatimportálási modulban

A következő lépések azt írják le, hogyan konfigurálhatja manuálisan az importálási forrást.

1. Adja hozzá [](import-data.md) az Adatimportálási modult a kísérlethez. A modult a felületen, az **adatok bemenete és a kimenet** kategóriában találja.

2. Az **adatforrás**területen válassza a **webes URL-cím http-n keresztül**lehetőséget.

3. Az **URL-cím**mezőben írja be vagy illessze be a betölteni kívánt adatmennyiséget tartalmazó oldal teljes URL-címét.

    Az URL-címnek tartalmaznia kell a webhely URL-címét és a fájl nevét és kiterjesztését tartalmazó teljes elérési utat a betölteni kívánt adatnak a laphoz.

    A következő oldal például a Kaliforniai Egyetem (University of California) Machine learning adattárában található írisz-adathalmazt tartalmazza:

    `https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data`

4. **Adatformátum**esetén válassza ki a listából a támogatott adatformátumok egyikét.

    Azt javasoljuk, hogy a formátum meghatározásához mindig ellenőrizze az előre megadott adategységeket. Az UC Irvine-oldal CSV-formátumot használ. Más támogatott adatformátumok a TSV, a ARFF és az SvmLight.

5. Ha az adatai CSV-vagy TSV-formátumúak, akkor a **fájl fejléce** beállítással jelezheti, hogy a forrásadatok tartalmaz-e fejlécsort. A fejlécsor az oszlopnevek hozzárendelésére szolgál.

6. Jelölje be a **gyorsítótárazott eredmények használata** lehetőséget, ha nem várható, hogy az adatok sokat változnak, vagy ha el szeretné kerülni az adatok ismételt betöltését minden egyes alkalommal, amikor futtatja a kísérletet.

    Ha ez a beállítás be van jelölve, a kísérlet a modul első futtatásakor betölti az adatokat, és ezt követően az adatkészlet gyorsítótárazott verzióját használja.

    Ha újra szeretné tölteni az adatkészletet a kísérlet adatkészletének minden egyes iterációjában, törölje a **gyorsítótárbeli eredmények használata** lehetőséget. Az eredmények akkor is újratölthetők, ha az [importálási adatok](import-data.md)paraméterei módosulnak.

7. Futtassa a kísérletet.

## <a name="results"></a>Results (Eredmények)

Ha elkészült, kattintson a kimeneti adatkészletre, és válassza a **Megjelenítés** lehetőséget, hogy megtekintse az adatokat sikeresen importálta.


## <a name="next-steps"></a>További lépések

Tekintse [meg Azure Machine learning szolgáltatás számára elérhető modulok készletét](module-reference.md) . 