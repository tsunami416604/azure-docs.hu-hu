---
title: "Az Azure ML munkaterület kibocsátási megjegyzések a sprint 2018. január 3."
description: "Ez a dokumentum részletesen a frissítéseket az Azure ml sprint 3 kiadásban"
services: machine-learning
author: raymondlaghaeian
ms.author: raymondl
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 01/22/2018
ms.openlocfilehash: f75fcec3b722563949b6553f17c4f3db3e223675
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2018
---
# <a name="sprint-3---january-2018"></a>Sprint 3 – 2018. január 

#### <a name="version-number-01171218263"></a>Verziószám: 0.1.1712.18263

>Ez hogyan lehetséges [verziószámának](https://docs.microsoft.com/en-us/azure/machine-learning/preview/known-issues-and-troubleshooting-guide).

Üdvözli az Azure Machine Learning-munkaterület negyedik frissítésére. Az alábbi táblázat a frissítéseket és a sprint javítását. Ezek a frissítések számos felhasználói visszajelzés közvetlen következményeként történik. 

## <a name="notable-new-features-and-changes"></a>Fontos új funkciók és módosítások
- A hitelesítési verem frissítések arra kényszeríti az indításkor a bejelentkezési és a fiók kiválasztása

## <a name="detailed-updates"></a>Részletes frissítések
Az alábbiakban az Azure Machine Learning a sprint a minden összetevő területén részletes frissítések listáját.

### <a name="workbench"></a>Workbench
- A alkalmazást a Programok telepítése/törlése telepítési/eltávolítási képessége
- A hitelesítési verem frissítések arra kényszeríti az indításkor a bejelentkezési és a fiók kiválasztása
- Továbbfejlesztett egyszeri bejelentkezés (SSO) élmény Windows rendszeren
- Több bérlő más hitelesítő adatokkal tartozó felhasználók jelentkezhetnek be munkaterület lesz

#### <a name="ui"></a>UI
- Általános fejlesztések és hibajavítások

### <a name="notebooks"></a>Notebookok
- Általános fejlesztések és hibajavítások

### <a name="data-preparation"></a>Adatok előkészítése 
- Továbbfejlesztett automatikus javaslatok minta alapján átalakítások végrehajtása közben
- A minta gyakoriság inspector továbbfejlesztett algoritmus
- Minta alapján átalakítások végrehajtása közben a mintaadatok és visszajelzés küldése ![küldési visszajelzési hivatkozásra kattintva képe származnia oszlop átalakítás](media/release-notes-sprint-3/SendFeedbackFromDeriveColumn.png)
- Spark futásidejű fejlesztései
- Scala Pyspark váltja fel
- Zárja be az adatok nem alkalmazható az az idő adatsorozat Inspector rögzített nem tudja 
- Rögzített adatok Prep végrehajtása HDI lefagy ideje

### <a name="model-management-cli-updates"></a>Minta felügyeleti CLI frissítések 
  - Az előfizetés tulajdonjogának átruházása már nem szükséges erőforrások történő üzembe helyezéséhez. Az erőforráscsoport közreműködői elérésére elegendő a környezet beállításához lesz.
  - Engedélyezett a helyi környezet beállítása az ingyenes előfizetések 
