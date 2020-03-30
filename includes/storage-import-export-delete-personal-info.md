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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67179332"
---
## <a name="deleting-personal-information"></a>Személyes adatok törlése

[!INCLUDE [gdpr-intro-sentence.md](gdpr-intro-sentence.md)]

A személyes adatok az importálási/exportálási szolgáltatás szempontjából relevánsak (a portálon és api-n keresztül) az importálási és exportálási műveletek során. Az ilyen folyamatok során felhasznált adatok a következők:

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

Az importálási/exportálási feladat létrehozásakor a felhasználók megadják a kapcsolattartási adatokat és a szállítási címet. A személyes adatok at legfeljebb két különböző helyen tároljuk: a feladatban és opcionálisan a portál beállításaiban. A személyes adatok csak akkor tárolódnak a portál beállításaiban, ha az exportálási folyamat *Szállítási adatok visszaküldése* szakaszában alapértelmezés szerint bejelöli a szállító és a **visszaküldési cím jelölőnégyzetet.**

A személyes kapcsolattartási adatok a következőképpen törölhetők:

- A feladattal együtt mentett adatok törlődnek a feladattal együtt. A felhasználók manuálisan törölhetik a feladatokat, és a befejezett feladatok 90 nap elteltével automatikusan törlődnek. Manuálisan törölheti a feladatokat a REST API-n vagy az Azure Portalon keresztül. Ha törölni szeretné a feladatot az Azure Portalon, nyissa meg az importálási/exportálási feladatot, és kattintson a *Törlés gombra* a parancssávról. Az importálási/exportálási feladat REST API-n keresztüli törléséről az [Importálási/exportálási feladat törlése](../articles/storage/common/storage-import-export-cancelling-and-deleting-jobs.md)című rész ből tájékot talál.

- A portál beállításaiban mentett kapcsolattartási adatok a portál beállításainak törlésével távolíthatók el. A portál beállításait az alábbi lépésekkel törölheti:
  - Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
  - Kattintson *Settings* a ![Beállítások ikonra Az Azure Beállítások ikonjára](media/storage-import-export-delete-personal-info/azure-settings-icon.png)
  - Kattintson *az Összes beállítás exportálása gombra* (az aktuális beállítások `.json` fájlba mentéséhez).
  - Kattintson *az Összes beállítás és privát irányítópult törlésére* az összes beállítás törléséhez, beleértve a mentett kapcsolattartási adatokat is.

További információt a Microsoft adatvédelmi irányelvei az [Adatvédelmi központban talál.](https://www.microsoft.com/trustcenter)