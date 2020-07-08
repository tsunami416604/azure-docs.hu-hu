---
title: Biztonságos kód – ajánlott eljárások
titleSuffix: Azure Machine Learning
description: Ismerje meg a Azure Machine Learning fejlesztésekor esetlegesen fennálló lehetséges biztonsági fenyegetéseket. Ismerje meg az Azure ML által biztosított enyhítéseket és az ajánlott eljárásokat annak biztosítására, hogy a fejlesztési környezetek biztonságban maradjanak.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: larryfr
ms.date: 11/12/2019
ms.openlocfilehash: 8932640a69ad68e0fd30b8577ba95768dbf8b9b9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85660810"
---
# <a name="secure-code-best-practices-with-azure-machine-learning"></a>Ajánlott eljárások a szabályzatok biztonságossá tételéhez Azure Machine Learning

Azure Machine Learning a fájlokat és tartalmakat bármilyen forrásból feltöltheti. A Jupyter-jegyzetfüzeteken vagy-szkripteken belüli tartalmak beolvashatják a munkamenetek adatait, hozzáférhetnek a szervezeten belüli adatokhoz az Azure-ban, vagy kártékony folyamatokat futtathatnak az Ön nevében.

> [!IMPORTANT]
> Csak megbízható forrásból származó jegyzetfüzeteket vagy parancsfájlokat futtasson. Például, ha Ön vagy a biztonsági csapata áttekintette a jegyzetfüzetet vagy a parancsfájlt.

## <a name="potential-threats"></a>Lehetséges fenyegetések

A Azure Machine Learning-fejlesztés gyakran webes fejlesztési környezeteket (notebookok & Azure ML Studio) is magában foglal. Webalapú fejlesztői környezetek használatakor a lehetséges fenyegetések a következők:

* [Helyek közötti parancsfájlok (XSS)](https://owasp.org/www-community/attacks/xss/)

    * __Dom-injektálás__: Ez a típusú támadás módosíthatja a böngészőben megjelenő felhasználói felületet. Például úgy, hogy a Futtatás gomb egy Jupyter Notebookban való működésének módosítását végzi.
    * __Hozzáférési jogkivonat/cookie-__ k: az XSS-támadások a helyi tárolók és a böngésző cookie-jai is hozzáférhetnek. A Azure Active Directory (HRE) hitelesítési token a helyi tárolóban van tárolva. Egy XSS-támadás ezt a tokent használva API-hívásokat hajthat végre az Ön nevében, majd elküldheti az adatait egy külső rendszernek vagy API-nak.

* [Helyek közötti kérelem hamisítása (CSRF)](https://owasp.org/www-community/attacks/csrf): Ez a támadás lecserélheti egy rendszerkép URL-címét, vagy egy rosszindulatú parancsfájl vagy API URL-címével. Ha a rendszerkép betöltődik, vagy hivatkozásra kattint, a rendszer meghívja az URL-címet.

## <a name="azure-ml-studio-notebooks"></a>Azure ML Studio-jegyzetfüzetek

A Azure Machine Learning Studio üzemeltetett notebook-élményt biztosít a böngészőben. A jegyzetfüzetben található cellák a kártékony kódokat tartalmazó HTML-dokumentumokat vagy-töredékeket is tartalmazhatnak.  A kimenet megjelenítésekor a kód végrehajtható.

__Lehetséges fenyegetések__:
* Helyek közötti parancsfájlok (XSS)
* Helyek közötti kérelmek hamisítása (CSRF)

__A Azure Machine learning által biztosított enyhítések__:
* A __kód cellájának kimenete__ iframe-ben van lefoglalva. Az IFRAME megakadályozza, hogy a parancsfájl hozzáférjen a szülő DOM, a cookie-khoz vagy a munkamenet-tárolóhoz.
* A __Markdown__ a dompurify könyvtár használatával tisztítja. Ez blokkolja a rosszindulatú parancsfájlok futtatását a Markdown-cellákkal.
* A rendszer a __KÉPurl-címeket__ és a __Markdown-hivatkozásokat__ egy Microsoft tulajdonú végpontnak küldi el, amely rosszindulatú értékeket keres. Ha a rendszer kártékony értéket észlel, a végpont elutasítja a kérelmet.

__Ajánlott műveletek__:
* Győződjön meg arról, hogy a studióba való feltöltés előtt megbízik a fájlok tartalmában. A feltöltéskor meg kell erősítenie, hogy megbízható fájlokat tölt fel.
* Egy külső alkalmazás megnyitására szolgáló hivatkozás kiválasztásakor a rendszer felszólítja, hogy Bízzon meg az alkalmazásban.

## <a name="azure-ml-compute-instance"></a>Azure ML számítási példány

Azure Machine Learning számítási példány __Jupyter__ és __Jupyter labort__üzemeltet. Ha a vagy a használatával, a jegyzetfüzetben vagy a kódban található cellákban a HTML-dokumentumok vagy a kártékony kódokat tartalmazó töredékek is megadhatók. A kimenet megjelenítésekor a kód végrehajtható. Ugyanezek a fenyegetések is érvényesek, ha számítási példányon üzemeltetett __RStudio__ használ.

__Lehetséges fenyegetések__:
* Helyek közötti parancsfájlok (XSS)
* Helyek közötti kérelmek hamisítása (CSRF)

__A Azure Machine learning által biztosított enyhítések__:
* Nincsenek. A Jupyter és a Jupyter Lab a Azure Machine Learning számítási példányon üzemeltetett nyílt forráskódú alkalmazások.

__Ajánlott műveletek__:
* Győződjön meg arról, hogy a studióba való feltöltés előtt megbízik a fájlok tartalmában. A feltöltéskor meg kell erősítenie, hogy megbízható fájlokat tölt fel.

## <a name="report-security-issues-or-concerns"></a>Biztonsági problémák és problémák jelentése 

Azure Machine Learning jogosult a Microsoft Azure Bounty program keretében. További információért látogasson el a következő oldalra:  [https://www.microsoft.com/msrc/bounty-microsoft-azure](https://www.microsoft.com/msrc/bounty-microsoft-azure) .

## <a name="next-steps"></a>További lépések

* [Vállalati biztonsági Azure Machine Learning](concept-enterprise-security.md)