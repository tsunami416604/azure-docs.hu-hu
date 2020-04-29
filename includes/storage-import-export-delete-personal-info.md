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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "67179332"
---
## <a name="deleting-personal-information"></a>Személyes adatok törlése

[!INCLUDE [gdpr-intro-sentence.md](gdpr-intro-sentence.md)]

A személyes adatok az importálási/exportálási szolgáltatáshoz (a Portálon és az API-n keresztül) kapcsolódnak az importálási és exportálási műveletek során. Az alábbi folyamatokban használt adatműveletek a következők:

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

Importálási/exportálási feladatok létrehozásakor a felhasználók kapcsolattartási adatokat és szállítási címet biztosítanak. A személyes adatokat a rendszer akár két különböző helyen tárolja: a feladatban és opcionálisan a portál beállításaiban. A személyes adatok csak akkor tárolódnak a portál beállításaiban, ha bejelöli ezt a jelölőnégyzetet, a **szállító és a visszaadott cím mentése alapértelmezettként** a *Return Shipping info* szakaszban az exportálási folyamat során.

A személyes kapcsolattartási adatokat a következő módokon lehet törölni:

- A feladatokkal mentett adatok törlődnek a feladatokkal. A felhasználók manuálisan törölhetik a feladatokat, a befejezett feladatokat pedig 90 nap után automatikusan törli a rendszer. A feladatokat manuálisan törölheti a REST API vagy a Azure Portal használatával. Ha törölni szeretné a feladatot a Azure Portalban, lépjen az importálási/exportálási feladatokhoz, majd kattintson a *Törlés* elemre a parancssorból. Az importálási/exportálási feladatok REST API használatával történő törlésével kapcsolatos részletekért tekintse meg az [importálási/exportálási feladatok törlését](../articles/storage/common/storage-import-export-cancelling-and-deleting-jobs.md)ismertető témakört.

- A portál beállításaiban mentett kapcsolattartási adatokat a portál beállításainak törlésével lehet eltávolítani. A portál beállításait a következő lépésekkel törölheti:
  - Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
  - Kattintson a *Beállítások* ikonra ![az Azure beállítások ikon](media/storage-import-export-delete-personal-info/azure-settings-icon.png)
  - Kattintson az *összes beállítás exportálása* (az aktuális beállítások `.json` fájlba mentéséhez) elemre.
  - Kattintson az *összes beállítás és privát irányítópultok törlése* lehetőségre az összes beállítás, beleértve a mentett kapcsolattartási adatok törléséhez.

További információkért tekintse át a Microsoft adatvédelmi szabályzatát a következő helyen: [megbízhatósági központ](https://www.microsoft.com/trustcenter)