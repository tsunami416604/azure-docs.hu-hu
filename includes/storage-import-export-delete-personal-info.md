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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66114977"
---
## <a name="deleting-personal-information"></a>Személyes adatok törlése

[!INCLUDE [gdpr-intro-sentence.md](gdpr-intro-sentence.md)]

Személyes adatokat, az importálási/exportálási fontos az importálás során (keresztül a portál és API-t) szolgáltatást, és exportálási műveletet. Ezek a folyamatok során használt adatok a következők:

- Kapcsolattartó neve
- Telefonszám
- E-mail
- Utca, házszám
- Város
- Irányítószám
- Állapot
- Ország/tartomány/régió
- Meghajtóazonosító
- Szállítmányozó fiókszáma
- Szállítmány nyomkövetési száma

Importálási/exportálási feladat létrehozásakor a felhasználók adja meg a kapcsolattartási adatokat és a szállítási cím. Akár két különböző helyeken tárolt személyes adatokat: a feladat és igény szerint a portál beállításaiban. Személyes adatokat csak tárolódik a portál beállításaiban, ha bejelöli a jelölőnégyzetet, **az alapértelmezett szolgáltatója, és lépjen vissza cím mentése** során a *szállítási adatok visszaadása* az exportálási folyamat szakaszában.

Személyes adatait a következőképpen törölheti:

- A feladat a mentett adatok törlődik a feldolgozás. A felhasználók törölhetik feladatok manuálisan, és a befejezett feladatok automatikusan 90 nap után törlődnek. Manuálisan törölheti a feladatokat a REST API-t vagy az Azure Portalon keresztül. Az Azure Portalon a feladat törléséhez nyissa meg az importálási/exportálási feladat, és kattintson a *törlése* a parancssávon. REST API-n keresztül az importálási/exportálási feladat törlése. a részletekért tekintse meg a [az importálási/exportálási feladat törlése](../articles/storage/common/storage-import-export-cancelling-and-deleting-jobs.md).

- Kapcsolattartási adatait a portál beállításaiban mentett el kell távolítani a portálbeállítások törlésével. Portálbeállítások törölheti az alábbi lépéseket:
  - Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
  - Kattintson a *beállítások* ikon ![Azure beállítások ikon](media/storage-import-export-delete-personal-info/azure-settings-icon.png)
  - Kattintson a *az összes beállítás exportálása* (a jelenlegi beállítások mentésére szolgáló egy `.json` fájlt).
  - Kattintson a *törli az összes beállítás és saját irányítópult* törli az összes beállítást, beleértve a mentett kapcsolattartási adatokat.

További információkért tekintse át a Microsoft Privacy szabályzatokban [biztonsági és adatkezelési központ](https://www.microsoft.com/trustcenter)