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
ms.date: 02/28/2020
ms.author: diberry
ms.openlocfilehash: 679073715588a4a81e69e3e7ba2d18341b1bab4b
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82096622"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Migrálás Azure Resource authoring-kulcsba

Language Understanding (LUIS) a létrehozási hitelesítés egy e-mail-fiókból egy Azure-erőforrásra módosult. Noha jelenleg nem szükséges, az Azure-erőforrásra való áttérés a jövőben is érvénybe lép.

## <a name="why-migrate"></a>Miért érdemes az áttelepítés?

Az Azure-erőforrások szerzői műveletekkel való használata lehetővé teszi, hogy az erőforrás tulajdonosaként vezérelje a szerzői műveletek elérését. Létrehozhat és elnevezheti a szerzők különböző csoportjainak kezeléséhez szükséges erőforrásokat.

Tegyük fel, hogy Ön a 2 LUIS-alkalmazás tulajdonosa, és különböző tagjai vannak, akik az egyes alkalmazásokban közreműködők. Létrehozhat két különböző authoring-erőforrást, és hozzárendelheti az egyes alkalmazásokat az egyes erőforrásokhoz. Ezután rendeljen hozzá minden tagot közreműködőként a megfelelő authoring-erőforráshoz attól függően, hogy melyik alkalmazással működnek. Az Azure authoring Resource szabályozza az engedélyt.

> [!Note]
> A Migrálás előtt a közös szerzők a LUIS-alkalmazás szintjén _közreműködőként_ ismertek. Az áttelepítés után a _közreműködő_ Azure-szerepköre ugyanazokkal a funkciókkal, de az Azure-erőforrás szintjén is használatos.

## <a name="what-is-migrating"></a>Mi az áttelepítés?

A Migrálás a következőket tartalmazza:

* A LUIS, a tulajdonosok és a közreműködők minden felhasználója.
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

* **Szükséges**, rendelkeznie kell egy Azure- [előfizetéssel](https://azure.microsoft.com/free/). Az előfizetési folyamat egy része számlázási adatokat igényel. A LUIS használata esetén azonban használhatja az ingyenes`F0`() díjszabási szintet.
* **Optionally**A Luis portál alkalmazások listájáról is készíthet biztonsági másolatot az alkalmazásokról az egyes alkalmazások exportálásával vagy az exportálási [API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)használatával.
* **Szükség**esetén mentse az egyes alkalmazások collaborator's listáját. Az e-mail-lista az áttelepítési folyamat részeként van megadva.


A **Luis-alkalmazás készítése ingyenes**, a `F0` szintet is jelzi. [További információ a díjszabási szintekről](luis-limits.md#key-limits).

Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon](https://azure.microsoft.com/free/).

### <a name="migration-steps"></a>A migrálás lépései

Kövesse [az alábbi áttelepítési lépéseket](luis-migration-authoring-steps.md).

### <a name="after-you-migrate"></a>Az áttelepítés után

Az áttelepítési folyamat után minden LUIS-alkalmazás hozzá van rendelve egyetlen LUIS authoring-erőforráshoz.

A _Luis portálon_létrehozhat több szerzői erőforrást, és hozzárendelheti a **Manage-> Azure-erőforrások** oldaláról.

Az adott erőforrás **Access Control (iam)** lapján hozzáadhat közreműködőket a szerzői erőforráshoz _Azure Portal_. További információ: [közreműködői hozzáférés hozzáadása](luis-migration-authoring-steps.md#after-the-migration-process-add-contributors-to-your-authoring-resource).

|Portál|Cél|
|--|--|
|[Azure](https://azure.microsoft.com/free/)|* Hozzon létre előrejelzési és létrehozási erőforrásokat.<br>* Közreműködők kiosztása.|
|[LUIS](https://www.luis.ai)|* Migrálás az új szerzői erőforrásokra.<br>* A felügyeleti **> Azure-erőforrások** oldaláról kioszthatja vagy kioszthatja az alkalmazásokat az alkalmazások számára.|

## <a name="migration-for-the-app-contributor"></a>Migrálás az alkalmazás közreműködője számára

A LUIS minden felhasználóját át kell telepíteni, beleértve a közreműködőket és a közreműködőket is. Egy közreműködőnek át kell térnie az alkalmazáshoz való hozzáférésre.

> [!Note]
> Ha a LUIS-alkalmazás tulajdonosa át lett telepítve, és hozzáadta a közreműködőt az Azure-erőforráshoz, akkor a közreműködő továbbra sem férhet hozzá az alkalmazáshoz, kivéve, ha azok migrálása is megtörténik.

### <a name="before-the-app-is-migrated"></a>Az alkalmazás migrálása előtt

Dönthet úgy, hogy olyan alkalmazást exportál, amelyhez Ön is közreműködő, majd importálja újra az alkalmazást a LUIS-ba. Az importálási folyamat létrehoz egy új alkalmazást egy új alkalmazás-AZONOSÍTÓval, amelynek Ön a tulajdonosa.

### <a name="after-the-app-is-migrated"></a>Az alkalmazás migrálása után

Az alkalmazás tulajdonosának [hozzá kell adnia az e-maileket az Azure authoring-erőforráshoz közreműködőként](luis-how-to-collaborate.md#add-contributor-to-azure-authoring-resource).

Az áttelepítési folyamat után az Ön tulajdonában lévő alkalmazások a LUIS portál **saját alkalmazások** lapján érhetők el.

## <a name="troubleshooting-the-migration-process-for-luis-authoring"></a>A LUIS-készítés áttelepítési folyamatának hibaelhárítása

* A LUIS authoring Keys csak az áttelepítési folyamat befejezése után látható a LUIS-portálon. Ha létrehoz egy szerzői kulcsot, például a LUIS CLI-vel, a felhasználónak továbbra is végre kell hajtania az áttelepítési folyamatot a LUIS portálon.
* Ha egy áttelepített felhasználó egy nem áttelepített felhasználót ad hozzá közreműködőként az Azure-erőforráshoz, a nem áttelepített felhasználónak nincs hozzáférése az alkalmazásokhoz, kivéve, ha áttelepíti őket.
* Ha egy nem áttelepített felhasználó nem tulajdonosa egyetlen alkalmazásnak sem, de a mások tulajdonában lévő más alkalmazások munkatársai, és a tulajdonosok az áttelepítési folyamaton estek át, a felhasználónak át kell térnie az alkalmazásokhoz való hozzáférésre.
* Ha egy nem áttelepített felhasználó hozzáadta egy másik áttelepített felhasználót az alkalmazásához, akkor hiba lép fel, mivel nem tud hozzáadni egy áttelepített felhasználót egy alkalmazáshoz. A nem áttelepített felhasználónak el kell végeznie az áttelepítési folyamatot, és létre kell hoznia egy Azure-erőforrást, és hozzá kell adnia az áttelepített felhasználót közreműködőként az erőforráshoz.

Az áttelepítési folyamat során hibaüzenetet kap, ha:
* Az előfizetés nem engedélyezi, hogy Cognitive Services erőforrásokat hozzon létre
* Az áttelepítés negatívan befolyásolja az alkalmazások futtatókörnyezetét. Az áttelepítés során minden közreműködő el lesz távolítva az alkalmazásokból, és a többi alkalmazásból is együttműködik. Ez a folyamat azt jelenti, hogy a hozzárendelt kulcsok is el lettek távolítva. Az áttelepítés le lesz tiltva, ha más alkalmazásokban vannak hozzárendelve kulcsok. A Migrálás előtt távolítsa el a biztonságosan hozzárendelt kulcsot. Ha tudja, hogy a hozzárendelt kulcs nem használatos a futtatókörnyezetben, akkor el kell távolítania az áttelepítéshez, hogy el tudja végezni a folyamatot.

A következő URL-formátummal férhet hozzá az alkalmazás Azure-erőforrás-listájához:

`https://www.luis.ai/applications/REPLACE-WITH-YOUR-APP-ID/versions/REPLACE-WITH-YOUR-VERSION-ID/manage/resources`

## <a name="next-steps"></a>További lépések

* [Az alkalmazás migrálása egy szerzői erőforrásba](luis-migration-authoring-steps.md)
