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
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 44baac5eb4e8887594ba05498901ba664380005f
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280777"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Migrálás Azure Resource authoring-kulcsba

Language Understanding (LUIS) a létrehozási hitelesítés egy e-mail-fiókból egy Azure-erőforrásra módosult. Noha jelenleg nem szükséges, az Azure-erőforrásra való áttérés a jövőben is érvénybe lép.

## <a name="why-migrate"></a>Miért érdemes az áttelepítés?

Az Azure-erőforrások szerzői műveletekkel való használata lehetővé teszi, hogy az erőforrás tulajdonosaként vezérelje a szerzői műveletek elérését. Létrehozhat és elnevezheti a szerzők különböző csoportjainak kezeléséhez szükséges erőforrásokat. 

Ha például 2 típusú LUIS-alkalmazással rendelkezik, különböző tagokkal, létrehozhat két különböző authoring-erőforrást, és hozzárendelheti a közreműködőket is. Az Azure authoring Resource szabályozza az engedélyt. 

> [!Note]
> Az áttelepítés előtt a közös szerzők neve _közreműködő_. Az áttelepítés után a _közreműködő_ Azure-szerepköre ugyanarra a funkcióra van használatban.

## <a name="what-is-migrating"></a>Mi az áttelepítés?

A Migrálás a következőket tartalmazza:

* A LUIS, a tulajdonosok és a közreműködők összes felhasználója.
* **Minden** alkalmazás.
* Egy **egyirányú** áttelepítés.

A tulajdonos nem választhat az áttelepítendő alkalmazások egy részhalmazát, és a folyamat nem vonható vissza. 

Az áttelepítés nem: 

* Egy folyamat, amely összegyűjti a közreműködőket, és automatikusan áthelyezi vagy hozzáadja az Azure authoring-erőforráshoz. Az alkalmazás tulajdonosaként el kell végeznie ezt a lépést. Ehhez a lépéshez szükséges a megfelelő erőforráshoz való hozzáférés.
* Az előrejelzési futtatókörnyezet erőforrásának létrehozásának és hozzárendelésének folyamata. Ha előrejelzési futtatókörnyezeti erőforrásra van szüksége, akkor ez [egy külön folyamat](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) , és változatlan marad. 

## <a name="how-are-the-apps-migrating"></a>Hogyan történik az alkalmazások áttelepítése?

A [Luis-portál](https://www.luis.ai) biztosítja az áttelepítési folyamatot. 

A rendszer a következőt fogja kérni a Migrálás esetén:

* Az e-mail-hitelesítési rendszeren alkalmazásokat készít a szerzői műveletekhez.
* Ön pedig Ön az alkalmazás tulajdonosa. 

Az áttelepítési folyamatot késleltetve törölheti az ablakot. A rendszer rendszeres időközönként áttelepíti az áttelepítést, vagy átadja az áttelepítési határidőt. Az áttelepítési folyamat elindításához a felső navigációs sáv zárolási ikonja látható.

## <a name="migration-for-the-app-owner"></a>Migrálás az alkalmazás tulajdonosának

### <a name="before-you-migrate"></a>Migrálás előtt

* A Luis portál alkalmazások listájáról is készíthet biztonsági másolatot az alkalmazásokról az egyes alkalmazások exportálásával vagy az exportálási [API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)használatával.
* **Szükség**esetén mentse az egyes alkalmazások collaborator's listáját. Az e-mail-lista az áttelepítési folyamat részeként van megadva.
* **Szükséges**, rendelkeznie kell egy Azure- [előfizetéssel](https://azure.microsoft.com/free/). Az előfizetési folyamat egy része számlázási adatokat igényel. A LUIS használata esetén azonban használhatja az ingyenes (`F0`) díjszabási szintet. 

A **Luis-alkalmazás készítése ingyenes**, amelyet a `F0`i szinten jelez. [További információ a díjszabási szintekről](luis-boundaries.md#key-limits).

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

A LUIS minden felhasználóját át kell telepíteni, beleértve a közreműködőket és a közreműködőket is. 

### <a name="before-the-app-is-migrated"></a>Az alkalmazás migrálása előtt

Dönthet úgy, hogy olyan alkalmazást exportál, amelyhez Ön is közreműködő, majd importálja újra az alkalmazást a LUIS-ba. Az importálási folyamat létrehoz egy új alkalmazást egy új alkalmazás-AZONOSÍTÓval, amelynek Ön a tulajdonosa.

### <a name="after-the-app-is-migrated"></a>Az alkalmazás migrálása után

Az alkalmazás tulajdonosának [hozzá kell adnia az e-maileket az Azure authoring-erőforráshoz közreműködőként](luis-how-to-collaborate.md#add-contributor-to-azure-authoring-resource). 

Az áttelepítési folyamat után az Ön tulajdonában lévő alkalmazások a LUIS portál **saját alkalmazások** lapján érhetők el.  

## <a name="troubleshooting"></a>Hibaelhárítás

A LUIS authoring Keys csak az áttelepítési folyamat befejezése után látható a LUIS-portálon. Ha létrehoz egy szerzői kulcsot, például a LUIS CLI-vel, a felhasználónak továbbra is végre kell hajtania az áttelepítési folyamatot. 

## <a name="next-steps"></a>Következő lépések

* [Az alkalmazás migrálása egy szerzői erőforrásba](luis-migration-authoring-steps.md)