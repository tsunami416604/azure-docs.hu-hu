---
title: Hitelesítő adatok az Azure-ban tartalom moderátor |} Microsoft Docs
description: Az API-k használata a tartalom moderátor hitelesítő adatok kezelése.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/25/2017
ms.author: sajagtap
ms.openlocfilehash: 4531fa4c8bbb7bb9c1daeaaac6f9e7078dae250a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346963"
---
# <a name="manage-credentials"></a>Hitelesítő adatok kezelése

A tartalom moderátor hitelesítő adatait a következő helyeken jönnek létre:

- [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator)
- [A tartalom moderátor felülvizsgálati eszköz](http://contentmoderator.cognitive.microsoft.com/)

Ez a cikk ismerteti, hogy azok hol találhatók, és hogyan kapcsolódnak egymáshoz.

## <a name="the-azure-portal"></a>Az Azure-portálon

Az Azure portál irányítópultján válassza ki a tartalom moderátor fiókját. A **erőforrás-kezelés**, jelölje be **kulcsok**. A kulcs másolásához jelölje ki a jobb oldalán a kulcs ikonra.

![Az Azure portálon tartalomkulcs moderátor](images/credentials-azure-portal-keys.PNG)

### <a name="how-to-use-your-azure-account-with-the-review-tool"></a>Az Azure-fiók használata a felülvizsgálati eszközzel
Az Azure-kulcs használata a felülvizsgálati API-k, másolja az erőforrás-azonosító szerepel a **tulajdonságok** az alábbi képernyőfelvételen a képernyőn, és adja meg a hitelesítő adatok képernyőn a felülvizsgálati eszköz a **szerepel az engedélyezési listán erőforrás-azonosítót** látható módon a következő mezők **erőforrás-azonosító** szakasz. 

Elérhető a tartalom moderátor munkafolyamatok az Azure-kulcsot használ, adja meg azt a **Ocp-Apim-előfizetés-kulcs** mező mellett a **munkafolyamat-beállításokat** szakaszban, ahogy az az alábbiak  **Munkafolyamatok** szakasz.

> [!NOTE]
> Cserélje a felülvizsgálati eszköz két helyen a kulcs és az erőforrás-azonosítója az Azure-előfizetése, ha a **próbaverzió Ocp-Apim-előfizetés-kulcs** jelenik meg a hitelesítő adatok képernyő már nincs használatban, de mindig elérhető.
> A Próbakulcs (RPS) másodpercenként 1 kérésére havonta legfeljebb 5000 tranzakciók korlátozza.

![Tartalom moderátor erőforrás-azonosító az Azure-portálon](images/credentials-azure-portal-resourceid.PNG)


## <a name="the-review-tool"></a>A felülvizsgálati eszköz

A tekintse át az eszköz az irányítópult a **beállítások** lapon jelölje be **hitelesítő adatok**.

![A felülvizsgálati eszköz tartalom moderátor hitelesítő adatait](images/credentials-trial-resource-workflow.PNG)

A következő szakasz megvizsgálja a fenti kép részletesebben:


### <a name="api"></a>API

Az első része listák a **tekintse át az API-végpont**, **vonja össze az Azonosítóját**, és a **Ocp-Apim-előfizetés-kulcs (tartalom moderátor Próbakulcs)** a felülvizsgálati csoport részeként létrehozott létrehozását. Használja őket az összes tartalom moderátor API-k, beleértve a felülvizsgálati API hívásához.

Azt is vegye figyelembe az API-végpont a régió azonosítója. Például **westus** a régió, a "https://westus.api.cognitive.microsoft.com/contentmoderator/review/v1.0"

![A felülvizsgálati eszközben tartalomkulcsot moderátor](images/credentials-trialkey.PNG)


### <a name="resource-id"></a>Erőforrás-azonosító

A második rész kezdete kimenő üresként nem erőforrás-azonosítóval. **Az Azure-előfizetés billentyűvel felülvizsgálati API-val, keresse meg az erőforrás-azonosító képernyőre, amint azt korábban, és másolja azt a mező látható**. Elérte a **"+"** menteni az erőforrás-azonosító.

> [!NOTE]
> A tartalom moderátor előfizetés régió meg kell felelnie a felülvizsgálati team régió ahhoz, hogy a csapat ismeri fel és érheti el a csoport adatait. Például az ezen a lapon a képek a **USA nyugati régiója** régió **(4)** a tartalom moderátor Azure-előfizetés és a felülvizsgálati csoport tartalmazza.

![Tartalom moderátor erőforrás-azonosító a felülvizsgálati eszköz](images/credentials-resourceids.PNG)


### <a name="workflows"></a>Munkafolyamatok

A harmadik rész a munkafolyamatok futtatásához használt információkat tartalmazza. Akkor indul, az automatikusan létrehozott Próbakulcs megjelenítő alapértelmezés szerint. 

**Frissíteni kell azt az Azure key, amikor Azure-előfizetés igénylése**. A két mező lehetővé kifejezés és a lemezkép a képernyő szöveg- és képfájlok kiértékelése műveletek kulcsattribútumokkal.

![Tartalom moderátor munkafolyamat-felhasználó hitelesítő adatait a felülvizsgálati eszköz](images/credentials-workflow.PNG)


## <a name="next-steps"></a>További lépések

* A tartalom moderátor hitelesítő adatok használata a [munkafolyamatok](workflows.md).
