---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/16/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 0cb3de7d893ccfe638468110b1b6f5fb61b2bc7c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
ms.locfileid: "29402495"
---
Erőforrás zárolások akadályozza meg a szervezet véletlen törlés vagy módosítása a fontos erőforrásokhoz. Szerepköralapú hozzáférés-vezérlés, eltérően erőforrás zárolások alkalmazhatók a korlátozás összes felhasználók és szerepkörök. 

Beállíthatja a zárolási szint beállítása azokhoz a **CanNotDelete** vagy **ReadOnly**. A portálon, a zárolás szintek jelennek meg, **törlése** és **írásvédett** kulcsattribútumokkal.

* **CanNotDelete** azt jelenti, hogy a jogosult felhasználók továbbra is olvasni és módosítani az erőforrást, de azokat nem lehet törölni az erőforrás. 
* **Csak olvasható** azt jelenti, hogy a jogosult felhasználók olvashatják egy erőforrást, de nem lehet törölni vagy az erőforrás frissítése. A zárolás alkalmazása hasonlít összes jogosult felhasználó által megadott engedélyek korlátozása a **olvasó** szerepkör. 

> [!TIP]
> Legyen óvatos, amikor alkalmazza a **ReadOnly** zárolása. Bizonyos műveleteket, amelyek az adatok, például olvasási műveletek ténylegesen szükséges további műveleteket. Például egy **ReadOnly** zárolását egy tárfiókot minden felhasználót megakadályoz a kulcsainak listázása. A listában kulcsok művelet segítségével egy POST kérést kezel, mert a visszaadott kulcsok érhetők el az írási műveletek. A **ReadOnly** egy App Service erőforrás zárolását megakadályozza, hogy a Visual Studio Server Explorer megjelenítése az erőforrás fájlok, mert adott interakció írási hozzáféréssel kell rendelkeznie.

A szülő hatókörben zárolást alkalmazásakor hatókörön belüli összes erőforrás öröklik az azonos zárolása. A későbbiekben még akkor is, erőforrások örökölt a zárolást. Az öröklés a legszigorúbb zár lép érvénybe.

Csak a fordul elő a felügyeleti vezérlősík, amely küldött műveletek műveletek érvényes erőforrás-kezelő zárolások `https://management.azure.com`. A zárolás nem korlátozza, hogyan erőforrások feldolgozni a saját funkciók. Erőforrás módosítások korlátozott, de erőforrás műveletek nem korlátozott. Például egy SQL-adatbázis csak olvasható zárolást megakadályozza az törölhessék vagy módosíthassák az adatbázisban. Nem gátolja meg létrehozása, frissítése vagy törlése az adatbázisban. Adatok tranzakciók nem engedélyezettek, mert a rendszer nem küldi el a műveletek `https://management.azure.com`.

### <a name="who-can-create-or-delete-locks-in-your-organization"></a>Aki létrehozhat vagy törölhet zárolásokat a szervezetében
Hozzon létre, vagy törölje a felügyeleti zárolás, hozzáféréssel kell rendelkeznie `Microsoft.Authorization/locks/*` műveletek. A beépített szerepkörök, csak **tulajdonos** és **felhasználói hozzáférés adminisztrátora** kapnak a csatolási műveleteket.
