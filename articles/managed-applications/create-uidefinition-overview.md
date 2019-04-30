---
title: Megismerheti a felhasználói felület létrehozása az Azure-definíciót által felügyelt alkalmazások |} A Microsoft Docs
description: Az Azure Managed Applications Felhasználóifelület-definíciók létrehozása
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
ms.openlocfilehash: ab777b487159b009bf2cac6086bb09cc71714b0d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60587750"
---
# <a name="create-azure-portal-user-interface-for-your-managed-application"></a>Azure portal felhasználói felületet a felügyelt alkalmazás létrehozása
Ez a dokumentum az alapfogalmakat, a createUiDefinition.json fájl mutatja be. Az Azure Portalon ezt a fájlt használja egy felügyelt alkalmazás létrehozásához a felhasználói felület létrehozásához.

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

A CreateUiDefinition mindig három tulajdonságot tartalmazza: 

* Kezelő
* version
* paraméterek

Felügyelt alkalmazások, a kezelő mindig kell `Microsoft.Compute.MultiVm`, és a legújabb támogatott verziót `0.1.2-preview`.

A parameters tulajdonság sémája attól függ, hogy a megadott kezelő és a verzió kombinációja. Felügyelt alkalmazások esetén támogatott a tulajdonságok akkor vannak `basics`, `steps`, és `outputs`. Az alapvető beállítások és lépések tulajdonságok tartalmaznak a _elemek_ – például eszköztippek és legördülő listáit – az Azure Portalon megjelenítendő. A kimeneti értékeket a megadott elemek leképezése az Azure Resource Manager központi telepítési sablon paramétereit a kimenetek tulajdonság használható.

Beleértve a `$schema` javasolt, de nem kötelező. Ha meg van adva, az érték `version` belül a verziószámnak egyeznie kell a `$schema` URI-t.

## <a name="basics"></a>Alapvető beállítások
Az alapvető beállítások lépés, mindig az első lépés a varázsló jönnek létre, ha az Azure Portalon elemzi a fájlban. A megadott elemek megjelenítése mellett `basics`, a portálon a felhasználók számára, válassza ki az előfizetést, erőforráscsoportot és helyet a központi telepítés elemek kódtárba. Ebben a lépésben általában elemeket, amelyeket lekérdezni a központi telepítési kiterjedő paraméterek, például egy fürt vagy a rendszergazdai hitelesítő adatokat, nevét kell végezze.

Ha egy elem viselkedése attól függ, a felhasználói előfizetés, erőforráscsoport vagy helyét, majd az elem nem használható az alapvető beállítások. Ha például **Microsoft.Compute.SizeSelector** attól függ, a felhasználói előfizetést és helyet az elérhető méretek listáját határozza meg. Ezért **Microsoft.Compute.SizeSelector** csak akkor használható a lépéseket. Általában csak elemeit a **Microsoft.Common** névtér alapokat is használható. Bár bizonyos elemek más névtérben (például **Microsoft.Compute.Credentials**), amelyek nem függnek a felhasználói környezetben, továbbra is.

## <a name="steps"></a>Lépések
A lépések tulajdonság nulla vagy több további lépést is végre megjeleníteni után alapjait, amelyek mindegyike tartalmaz egy vagy több elemet is tartalmazhat. Fontolja meg egy szerepkör vagy a telepített alkalmazás szintű lépéseket. A bemenet fő csomópontokért, és egy lépést a munkavégző csomópontok például hozzáadhat egy fürtben.

## <a name="outputs"></a>Kimenetek
Az Azure Portalon használja a `outputs` elemeket a tulajdonság `basics` és `steps` az Azure Resource Manager üzembe helyezési sablon paramétereihez. Tento slovník kulcsainak a sablon-paraméterek nevei, és az értékek a következők a hivatkozott elemeket a kimeneti objektum tulajdonságait.

Állítsa be a felügyelt alkalmazás erőforrásnév, szerepelnie kell egy értéket `applicationResourceName` a kimenetek tulajdonságban. Ha nem ez az érték, az alkalmazás egy GUID Azonosítót a névhez rendeli hozzá. A felhasználói felület, amely kéri a nevet a felhasználó egy szövegmező is felvehet.

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
Az Azure Resource Manager (mind a szintaxist és a funkciók) sablonfüggvények hasonlóan CreateUiDefinition funkciókat nyújt elemek bemenetek és kimenetek, valamint szolgáltatások, például feltételek használata.

## <a name="next-steps"></a>További lépések
Magát a createUiDefinition.json fájlt egy egyszerű sémával rendelkezik. A valódi mélysége támogatott elemeket és funkciókat származik. Ezekhez az elemekhez, részletesebben ismerteti:

- [Elemek](create-uidefinition-elements.md)
- [Functions](create-uidefinition-functions.md)

A jelenlegi JSON-séma createUiDefinition érhető el itt: https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json.

Egy felhasználói felület példafájl, lásd: [createUiDefinition.json](https://github.com/Azure/azure-managedapp-samples/blob/master/samples/201-managed-app-using-existing-vnet/createUiDefinition.json).
