---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-policy
author: craigshoemaker
ms.service: azure-policy
ms.topic: include
ms.date: 05/18/2018
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: e6a0ded137162328fd446b65ddb4a15fa6f1db88
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36313880"
---
## <a name="deleting-personal-information"></a>Személyes adatok törlése

[!INCLUDE [gdpr-intro-sentence.md](gdpr-intro-sentence.md)]

Személyes adatok esetében az importálási/exportálási releváns importálása során (keresztül a portál és API) szolgáltatást, és kiviteli művelet. Ezek az eljárások során használt adatok a következők:

- Kapcsolattartó neve
- Telefonszám
- E-mail
- Utca, házszám
- Város
- Irányítószám
- Állapot
- Tartomány/ország
- Meghajtó azonosítója
- Portvívő számát
- Szállítási követési száma

Importálási/exportálási feladatok létrehozásakor felhasználók kapcsolattartási adatokat és a szállítási címet adjon meg. Személyes adatokat legfeljebb két különböző helyen tárolja: a feladat, és opcionálisan a portál beállításai. Személyes adatokat csak tárolódik a portálbeállítások között, ha bejelöli a jelölőnégyzetet, **szolgáltatója és visszaállítja a cím mentése alapértelmezettként** során a *szállítási információ vissza* az exportálási folyamat szakasza.

Kapcsolattartási adatait a következőképpen törölhető:

- A feladat a mentett adatok törlődik a feladathoz. Felhasználók manuális feladatok törlése és a befejezett feladatokhoz 90 nap múlva automatikusan törli. Manuálisan törölheti a feladatokat, a REST API-t vagy az Azure-portálon keresztül. Törli a feladatot az Azure portálon, nyissa meg az importálási/exportálási feladat, és kattintson a *törlése* a parancssávon. Importálási/exportálási feladatok REST API-n keresztül törlésével kapcsolatos részletekért tekintse meg [importálási/exportálási feladatok törlése](../articles/storage/common/storage-import-export-cancelling-and-deleting-jobs.md).

- A portálbeállítások törlésével eltávolíthatja a kapcsolattartási adatait a portál beállításai mentve. Portálbeállítások törölheti a következő lépések végrehajtásával:
  - Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
  - Kattintson a *beállítások* ikon ![Azure beállítások ikon](media/storage-import-export-delete-personal-info/azure-settings-icon.png)
  - Kattintson a *összes beállítások exportálása* (az aktuális beállítások mentéséhez egy `.json` fájlt).
  - Kattintson a *törli az összes beállítás és személyes irányítópultok* minden olyan beállításokat, beleértve a mentett kapcsolattartási adatait is törli.

További információkért tekintse át a Microsoft Privacy házirendje [biztonsági és adatkezelési központ](https://www.microsoft.com/trustcenter)