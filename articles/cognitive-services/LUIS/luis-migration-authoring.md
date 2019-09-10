---
title: Migrálás az Azure-erőforrásba szerzői műveletekhez
titleSuffix: Azure Cognitive Services
description: Migrálás egy Azure authoring Resource kulcsba.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/08/2019
ms.author: diberry
ms.openlocfilehash: ee185ca5d2e2cdda89945f0fc1e7aae32030c1be
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/08/2019
ms.locfileid: "70803896"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Migrálás Azure Resource authoring-kulcsba

Language Understanding (LUIS) a létrehozási hitelesítés egy e-mail-fiókból egy Azure-erőforrásra módosult. 

## <a name="why-migrate"></a>Miért érdemes az áttelepítés?

Az Azure-erőforrások szerzői műveletekkel való használata lehetővé teszi, hogy az erőforrás tulajdonosaként vezérelje a szerzői műveletek elérését. Létrehozhat és elnevezheti a szerzők különböző csoportjainak kezeléséhez szükséges erőforrásokat. 

Ha például 2 típusú LUIS-alkalmazással rendelkezik, különböző tagokkal, létrehozhat két különböző authoring-erőforrást, és hozzárendelheti a közreműködőket is. Az Azure authoring Resource szabályozza az engedélyt. 

> [!Note]
> Az áttelepítés előtt a közös szerzők neve _közreműködő_. Az áttelepítés után a _közreműködő_ Azure-szerepköre ugyanarra a funkcióra van használatban.

## <a name="what-is-migrating"></a>Mi az áttelepítés?

A Migrálás a következőket tartalmazza:

* **A tulajdonos** alkalmazásai.
* Egy **egyirányú** áttelepítés.

A tulajdonos nem választhat az áttelepítendő alkalmazások egy részhalmazát, és a folyamat nem vonható vissza. 

Az áttelepítés nem: 

* Egy folyamat, amely összegyűjti a közreműködőket, és automatikusan áthelyezi vagy hozzáadja az Azure authoring-erőforráshoz. Az alkalmazás tulajdonosaként el kell végeznie ezt a lépést. Ehhez a lépéshez szükséges a megfelelő erőforráshoz való hozzáférés.
* Az előrejelzési futtatókörnyezet erőforrásának létrehozásának és hozzárendelésének folyamata. Ha előrejelzési futtatókörnyezeti erőforrásra van szüksége, akkor ez [egy külön folyamat](luis-how-to-azure-subscription.md##create-resources-in-the-azure-portal) , és változatlan marad. 

## <a name="how-are-the-apps-migrating"></a>Hogyan történik az alkalmazások áttelepítése?

A [Luis-portál](https://www.luis.ai) biztosítja az áttelepítési folyamatot. 

A rendszer a következőt fogja kérni a Migrálás esetén:

* Az e-mail-hitelesítési rendszeren alkalmazásokat készít a szerzői műveletekhez.
* Ön pedig Ön az alkalmazás tulajdonosa. 

Az áttelepítési folyamat késleltethető az ablak megszakításával. A rendszer rendszeres időközönként áttelepíti az áttelepítést, vagy átadja az áttelepítési határidőt. Az áttelepítési folyamat elindításához a felső navigációs sáv zárolási ikonja látható.

## <a name="migration-for-the-app-owner"></a>Migrálás az alkalmazás tulajdonosának

### <a name="before-you-migrate"></a>Migrálás előtt

* A Luis portál alkalmazások listájáról is készíthet biztonsági másolatot az alkalmazásokról az egyes alkalmazások exportálásával vagy az exportálási [API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)használatával.
* **Szükség**esetén mentse az egyes alkalmazások collaborator's listáját. Az e-mail-lista az áttelepítési folyamat részeként van megadva.
* **Szükséges**, rendelkeznie kell egy Azure- [előfizetéssel](https://azure.microsoft.com/free/). Az előfizetési folyamat egy része számlázási adatokat igényel. Ha azonban a LUIS-t használja, használhatja az ingyenes (F0) díjszabási szintet is. Előfordulhat, hogy végül egy fizetős szintet kell használnia, mivel a használat növekszik. 

Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon](https://azure.microsoft.com/free/). 

### <a name="migration-steps"></a>A migrálás lépései

Kövesse [az alábbi áttelepítési lépéseket](luis-migration-authoring-steps.md).

### <a name="after-you-migrate"></a>Teendők migrálás után 

Az áttelepítési folyamat után minden LUIS-alkalmazás hozzá van rendelve egyetlen LUIS authoring-erőforráshoz.

A _Luis portálon_létrehozhat több szerzői erőforrást, és hozzárendelheti a **Manage-> Azure-erőforrások** oldaláról. 

Az adott erőforrás **Access Control (iam)** lapján hozzáadhat közreműködőket a szerzői erőforráshoz _Azure Portal_. További információért lásd: [közreműködői hozzáférés hozzáadása](luis-migration-authoring-steps.md#after-the-migration-process-add-contributors-to-your-authoring-resource) .

|Portál|Cél|
|--|--|
|[Azure](https://azure.microsoft.com/free/)|* Hozzon létre előrejelzési és létrehozási erőforrásokat.<br>* Közreműködők kiosztása.|
|[LUIS](https://www.luis.ai)|* Migrálás az új szerzői erőforrásokra.<br>* A felügyeleti **> Azure-erőforrások** oldaláról kioszthatja vagy kioszthatja az alkalmazásokat az alkalmazások számára.| 

## <a name="migration-for-the-app-contributor"></a>Migrálás az alkalmazás közreműködője számára

### <a name="before-the-app-is-migrated"></a>Az alkalmazás migrálása előtt

Dönthet úgy, hogy olyan alkalmazást exportál, amelyhez Ön is közreműködő, majd importálja újra az alkalmazást a LUIS-ba. Az importálási folyamat létrehoz egy új alkalmazást egy új alkalmazás-AZONOSÍTÓval, amelynek Ön a tulajdonosa.

### <a name="after-the-app-is-migrated"></a>Az alkalmazás migrálása után

Ha az áttelepítési folyamat az eredeti alkalmazáshoz való hozzáférésre van szüksége, az alkalmazás tulajdonosaként hozzá kell adnia az Azure authoring erőforráshoz.  

## <a name="next-steps"></a>További lépések

* [Az alkalmazás migrálása egy szerzői erőforrásba](luis-migration-authoring-steps.md)