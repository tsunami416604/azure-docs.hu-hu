---
title: "Megérteni a felhasználói felület létrehozása az Azure-definíció kezelt alkalmazások |} Microsoft Docs"
description: "Ismerteti, hogyan lehet az Azure által felügyelt alkalmazások felhasználói felületi-meghatározások létrehozása"
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/26/2017
ms.author: tomfitz
ms.openlocfilehash: d8f04d8ed2e56cecb1b7a850bed55a02a9492bb5
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2017
---
# <a name="create-azure-portal-user-interface-for-your-managed-application"></a>Azure portál felhasználói felületet a felügyelt alkalmazás létrehozása
Ez a dokumentum alapvető fogalmait a createUiDefinition.json fájl. Az Azure-portálon használják ezt a fájlt a felhasználói felület létrehozására a kezelt alkalmazás létrehozásához.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json",
   "handler": "Microsoft.Compute.MultiVm",
   "version": "0.1.2-preview",
   "parameters": {
      "basics": [ ],
      "steps": [ ],
      "outputs": { }
   }
}
```

Egy CreateUiDefinition mindig három tulajdonságot tartalmazza: 

* eseménykezelő
* Verzió
* paraméterek

Kezelt alkalmazások, mindig kell kezelő `Microsoft.Compute.MultiVm`, és a legfrissebb támogatott verziót `0.1.2-preview`.

A paraméterek tulajdonság sémájának attól függ, hogy a megadott kezelő és verziót. Kezelt alkalmazások, a támogatott tulajdonságok: `basics`, `steps`, és `outputs`. A alapjait és lépéseket tulajdonságok tartalmaznak a _elemek_ - például a szövegmezőből és a legördülő lista - az Azure portálon megjelenítendő. A kimenetek tulajdonság a megadott elemek kimeneti értékeit hozzárendelése az Azure Resource Manager központi telepítési sablon paramétereinek szolgál.

Beleértve `$schema` javasolt, de nem kötelező. Ha meg van adva, a következő `version` meg kell egyeznie a verzió belül a `$schema` URI.

## <a name="basics"></a>Alapvető beállítások
Az alapok lépés mindig az első lépés a varázsló jönnek létre, ha az Azure-portálon elemzi a fájl. A megadott elemek megjelenítése mellett `basics`, a portálon a felhasználók számára előfizetés, erőforráscsoport és a telepítési hely kiválasztása elemek esetében. Általában lekérdezése a központi telepítés kiterjedő paraméterek, például egy fürt vagy a rendszergazdai hitelesítő adatokat, nevét elemeket kell nyissa meg az ebben a lépésben.

Ha egy elem viselkedés attól függ, a felhasználó előfizetés, erőforráscsoportból vagy helyét, majd, hogy elem nem használható az alapokat. Például **Microsoft.Compute.SizeSelector** attól függ, a felhasználó előfizetésben és helyen meghatározni az elérhető méretek listáját. Ezért **Microsoft.Compute.SizeSelector** csak akkor használható a lépéseket. Általában csak elemeinek a **Microsoft.Common** névtér alapokat is használható. Bár bizonyos elemek más névtérben (például **Microsoft.Compute.Credentials**), amely nem függ a felhasználói környezet, továbbra is engedélyezett.

## <a name="steps"></a>Lépések
A lépések tulajdonság nulla vagy több további lépést is végre megjelenítése után alapjai, egy vagy több elemet tartalmaz, amelyek mindegyike tartalmazhat. Fontolja meg egy szerepkör vagy a réteg az alkalmazás telepítése lépést. Például hozzáadhat egy bemenetek a fő csomóponthoz, és egy lépést az ezen csomópontokhoz tartozó fürtben.

## <a name="outputs"></a>kimenetek
Az Azure-portálon használja a `outputs` tulajdonság elemeket a `basics` és `steps` paramétereinek az Azure Resource Manager központi telepítési sablont. Ehhez a szótárhoz kulcsai a sablon-paraméterek nevei, és a kimeneti objektumokat a hivatkozott elemeket tulajdonságainak értékei.

## <a name="functions"></a>Functions
Az Azure Resource Manager (a szintaxis és a működésének) sablonfüggvényei hasonló, CreateUiDefinition funkciókat nyújt elem bemenetei és kimenetei, valamint szolgáltatások, mint a conditionals használata.

## <a name="next-steps"></a>Következő lépések
Magát a createUiDefinition.json fájlt egy egyszerű sémával rendelkezik. A valós mélysége a támogatott elemek és funkciók származik. Azokat a cikkeket, részletesebben ismertetjük:

- [Elemek](create-uidefinition-elements.md)
- [Functions](create-uidefinition-functions.md)

A jelenlegi JSON-séma createUiDefinition érhető el itt: https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json. 

Ugyanazon a helyen későbbi verzióiban lesz elérhető. Cserélje le a `0.1.2-preview` az URL-cím része, és a `version` a használni kívánt azonosító értéket. A jelenleg támogatott verziójú azonosítók `0.0.1-preview`, `0.1.0-preview`, `0.1.1-preview`, és `0.1.2-preview`.