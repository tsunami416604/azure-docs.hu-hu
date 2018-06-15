---
title: Megérteni a felhasználói felület létrehozása az Azure-definíció kezelt alkalmazások |} Microsoft Docs
description: Ismerteti, hogyan lehet az Azure által felügyelt alkalmazások felhasználói felületi-meghatározások létrehozása
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2017
ms.author: tomfitz
ms.openlocfilehash: 59003e71324f5342cb2b724f670603fd6b67afe4
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
ms.locfileid: "34305225"
---
# <a name="create-azure-portal-user-interface-for-your-managed-application"></a>Azure portál felhasználói felületet a felügyelt alkalmazás létrehozása
Ez a dokumentum alapvető fogalmait a createUiDefinition.json fájl. Az Azure-portálon használják ezt a fájlt a felhasználói felület létrehozására a kezelt alkalmazás létrehozásához.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
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
* verzió:
* paraméterek

Kezelt alkalmazások, mindig kell kezelő `Microsoft.Compute.MultiVm`, és a legfrissebb támogatott verziót `0.1.2-preview`.

A paraméterek tulajdonság sémájának attól függ, hogy a megadott kezelő és verziót. Kezelt alkalmazások, a támogatott tulajdonságok: `basics`, `steps`, és `outputs`. A alapjait és lépéseket tulajdonságok tartalmaznak a _elemek_ - például a szövegmezőből és a legördülő lista - az Azure portálon megjelenítendő. A kimenetek tulajdonság a megadott elemek kimeneti értékeit hozzárendelése az Azure Resource Manager központi telepítési sablon paramétereinek szolgál.

Beleértve `$schema` javasolt, de nem kötelező. Ha meg van adva, a következő `version` meg kell egyeznie a verzió belül a `$schema` URI.

## <a name="basics"></a>Alapvető beállítások
Az alapok lépés mindig az első lépés a varázsló jönnek létre, ha az Azure-portálon elemzi a fájl. A megadott elemek megjelenítése mellett `basics`, a portálon a felhasználók számára előfizetés, erőforráscsoport és a telepítési hely kiválasztása elemek esetében. Általában lekérdezése a központi telepítés kiterjedő paraméterek, például egy fürt vagy a rendszergazdai hitelesítő adatokat, nevét elemeket kell nyissa meg az ebben a lépésben.

Ha egy elem viselkedés attól függ, a felhasználó előfizetés, erőforráscsoportból vagy helyét, majd, hogy elem nem használható az alapokat. Például **Microsoft.Compute.SizeSelector** attól függ, a felhasználó előfizetésben és helyen meghatározni az elérhető méretek listáját. Ezért **Microsoft.Compute.SizeSelector** csak akkor használható a lépéseket. Általában csak elemeinek a **Microsoft.Common** névtér alapokat is használható. Bár bizonyos elemek más névtérben (például **Microsoft.Compute.Credentials**), amely nem függ a felhasználói környezet, továbbra is engedélyezett.

## <a name="steps"></a>Lépések
A lépések tulajdonság nulla vagy több további lépést is végre megjelenítése után alapjai, egy vagy több elemet tartalmaz, amelyek mindegyike tartalmazhat. Fontolja meg egy szerepkör vagy a réteg az alkalmazás telepítése lépést. Például hozzáadhat egy bemenetek a fő csomóponthoz, és egy lépést az ezen csomópontokhoz tartozó fürtben.

## <a name="outputs"></a>Kimenetek
Az Azure-portálon használja a `outputs` tulajdonság elemeket a `basics` és `steps` paramétereinek az Azure Resource Manager központi telepítési sablont. Ehhez a szótárhoz kulcsai a sablon-paraméterek nevei, és a kimeneti objektumokat a hivatkozott elemeket tulajdonságainak értékei.

A kezelt alkalmazás-erőfforás neve beállításához meg kell adni egy értéket `applicationResourceName` kimenetek tulajdonságában. Ha ez az érték nincs megadva, az alkalmazás nevét egy GUID Azonosítót rendeli hozzá. A szövegmezőben megadhat egy nevét kéri le a felhasználó felhasználói felületén.

```json
"outputs": {
    "vmName": "[steps('appSettings').vmName]",
    "trialOrProduction": "[steps('appSettings').trialOrProd]",
    "userName": "[steps('vmCredentials').adminUsername]",
    "pwd": "[steps('vmCredentials').vmPwd.password]",
    "applicationResourceName": "[steps('appSettings').vmName]"
}
```

## <a name="functions"></a>Functions
Az Azure Resource Manager (a szintaxis és a működésének) sablonfüggvényei hasonló, CreateUiDefinition funkciókat nyújt elem bemenetei és kimenetei, valamint szolgáltatások, mint a conditionals használata.

## <a name="next-steps"></a>További lépések
Magát a createUiDefinition.json fájlt egy egyszerű sémával rendelkezik. A valós mélysége a támogatott elemek és funkciók származik. Azokat a cikkeket, részletesebben ismertetjük:

- [Elemek](create-uidefinition-elements.md)
- [Functions](create-uidefinition-functions.md)

A jelenlegi JSON-séma createUiDefinition érhető el itt: https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json.

Egy példa felhasználói felület fájl, lásd: [createUiDefinition.json](https://github.com/Azure/azure-managedapp-samples/blob/master/samples/201-managed-app-using-existing-vnet/createUiDefinition.json).
