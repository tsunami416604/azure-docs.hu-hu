---
title: Áttelepítés az Azure-erőforrásra a szerzői jog hozásához
titleSuffix: Azure Cognitive Services
description: Áttelepíthető egy Azure-szerzői erőforráskulcsra.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: diberry
ms.openlocfilehash: ec6f9592a4c149be382fab66cca27d929644d988
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78194509"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Áttelepítés egy Azure-erőforrás szerzői kulcsára

Language Understanding (LUIS) szerzői hitelesítés e-mail fiókról Azure-erőforrásra változott. Bár jelenleg nem szükséges, az Azure-erőforrásra való váltás a jövőben érvénybe lép.

## <a name="why-migrate"></a>Miért érdemes áttérni?

Egy Azure-erőforrás szerzői lehetővé teszi, hogy az erőforrás tulajdonosaként szabályozhatja a szerzői hozzáférést. A szerzők különböző csoportjainak kezeléséhez szerzői erőforrásokat hozhat létre és nevezhet el.

Például Ön a tulajdonosa 2 LUIS-alkalmazások, és különböző tagok, akik együttműködők az egyes alkalmazásokban. Két különböző szerzői erőforrást hozhat létre, és minden alkalmazást hozzárendelhet minden egyes erőforráshoz. Ezután rendelje hozzá az egyes tagokat a megfelelő szerzői erőforrás hoz, attól függően, hogy melyik alkalmazáson működnek együtt. Az Azure szerzői erőforrás szabályozza az engedélyezést.

> [!Note]
> Az áttelepítés előtt a társszerzők a LUIS alkalmazás szintjén _közreműködőkként_ ismertek. Migrálás után a _közreműködő_ Azure-szerepkörét használja az azonos funkciók, de az Azure erőforrás szintjén.

## <a name="what-is-migrating"></a>Mi az áttelepítés?

Az áttelepítés a következőket tartalmazza:

* A LUIS, a tulajdonosok és a közreműködők összes felhasználója.
* **Minden** alkalmazás.
* **Egyirányú** migráció.

A tulajdonos nem választhatja ki az áttelepítendő alkalmazások egy részét, és a folyamat nem visszafordítható.

Az áttelepítés nem:

* Olyan folyamat, amely összegyűjti a közreműködőket, és automatikusan áthelyezi vagy hozzáadja az Azure szerzői erőforrást. Önnek, mint az alkalmazás tulajdonosának, el kell végeznie ezt a lépést. Ez a lépés a megfelelő erőforrás engedélyeit igényli.
* Előrejelzési futásidejű erőforrás létrehozásához és hozzárendeléséhez. Ha előrejelzési futásidejű erőforrásra van szüksége, az [egy külön folyamat,](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) és változatlan.

## <a name="how-are-the-apps-migrating"></a>Hogyan vándorolnak át az alkalmazások?

A [LUIS-portál](https://www.luis.ai) biztosítja az áttelepítési folyamatot.

A rendszer kérni fogja az áttelepítést, ha:

* Vannak alkalmazások az e-mail hitelesítési rendszer authoring.
* És te vagy az alkalmazás tulajdonosa.

Az áttelepítési folyamat ot az ablakon kívüli megszakítással késleltetheti. A rendszer rendszeresen kéri az áttelepítést az áttelepítés vagy az áttelepítési határidő lejártáig. Az áttelepítési folyamatot a felső navigációs sáv lakatikonjáról indíthatja el.

## <a name="migration-for-the-app-owner"></a>Az alkalmazás tulajdonosának áttelepítése

### <a name="before-you-migrate"></a>Az áttelepítés előtt

* **Kötelező**, [Azure-előfizetéssel kell rendelkeznie.](https://azure.microsoft.com/free/) Az előfizetési folyamat egy része számlázási adatokat igényel. Azonban használhatja az ingyenes`F0`( ) tarifacsomag luis használata esetén.
* **Szükség esetén**készítsen biztonsági másolatot az alkalmazásokról a LUIS-portál alkalmazáslistájáról az egyes alkalmazások exportálásával vagy az exportálási [API használatával.](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)
* **Szükség esetén**mentse az egyes alkalmazások munkatársainak listáját. Ez az e-mail lista az áttelepítési folyamat részeként érhető el.


**A LUIS-alkalmazás szerzői**joga ingyenes, amelyet a `F0` szint jelez. További információ [a tarifacsomagokról.](luis-boundaries.md#key-limits)

Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon.](https://azure.microsoft.com/free/)

### <a name="migration-steps"></a>A migrálás lépései

Kövesse [az áttelepítési lépéseket.](luis-migration-authoring-steps.md)

### <a name="after-you-migrate"></a>Áttelepítés után

Az áttelepítési folyamat után az összes LUIS-alkalmazás egyetlen LUIS-szerzői erőforráshoz van rendelve.

Több szerzői erőforrást hozhat létre, és a _LUIS portál_ **Manage -> Azure-erőforrások** lapján rendelhet hozzá.

A szerzői erőforrás hoz az Azure _Portalon,_ a **hozzáférés-vezérlés (IAM)** lapon az adott erőforrás közreműködők. További információt a [Közreműködői hozzáférés hozzáadása című témakörben talál.](luis-migration-authoring-steps.md#after-the-migration-process-add-contributors-to-your-authoring-resource)

|Portál|Cél|
|--|--|
|[Azure](https://azure.microsoft.com/free/)|* Hozzon létre előrejelzési és szerzői erőforrásokat.<br>* Hozzászólók hozzárendelése.|
|[LUIS](https://www.luis.ai)|* Vándorol -hoz új szerzői anyagi javak.<br>* Előrejelzési és szerzői erőforrások hozzárendelése és hozzárendelésének visszavonása az alkalmazásokhoz a **Manage -> Azure resources** oldalon.|

## <a name="migration-for-the-app-contributor"></a>Az alkalmazás közreműködőjének áttelepítése

A LUIS minden felhasználójának át kell telepítenie, beleértve a közreműködők/közreműködők is. Egy közreműködőnek át kell telepítenie az alkalmazást.

> [!Note]
> Ha a LUIS-alkalmazás tulajdonosa áttelepített, és hozzáadta a közreműködőt az Azure-erőforrás közreműködőjeként, a közreműködő továbbra is nem férhet hozzá az alkalmazáshoz, kivéve, ha ők is áttelepülnek.

### <a name="before-the-app-is-migrated"></a>Az alkalmazás áttelepítése előtt

Dönthet úgy, hogy exportál egy alkalmazást, amelyen ön egy közreműködő, majd importálja az alkalmazást a LUIS-ba. Az importálási folyamat létrehoz egy új alkalmazást egy új alkalmazásazonosítóval, amelynek Ön a tulajdonosa.

### <a name="after-the-app-is-migrated"></a>Az alkalmazás áttelepítése után

Az alkalmazás tulajdonosának hozzá kell [adnia az e-mailt az Azure szerzői erőforrásához munkatársként.](luis-how-to-collaborate.md#add-contributor-to-azure-authoring-resource)

Az áttelepítési folyamat után a saját alkalmazások elérhetők a LUIS-portál **Saját alkalmazások** lapján.

## <a name="troubleshooting-the-migration-process-for-luis-authoring"></a>Luis-szerzői áttelepítési folyamatkal kapcsolatos hibaelhárítás

* A LUIS szerzői kulcsai csak az áttelepítési folyamat befejezése után láthatók a LUIS-portálon. Ha létrehozza a szerzői kulcsokat, például a LUIS CLI-vel, a felhasználónak továbbra is be kell fejeznie az áttelepítési folyamatot a LUIS-portálon.
* Ha egy áttelepített felhasználó hozzáad egy nem áttelepített felhasználót az azure-erőforrás közreműködőjeként, a nem áttelepített felhasználó csak akkor férhet hozzá az alkalmazásokhoz, ha áttelepülnek.
* Ha egy nem áttelepített felhasználó nem tulajdonosa egyetlen alkalmazásnak sem, hanem más, mások tulajdonában lévő alkalmazások munkatársa, és a tulajdonosok átestek az áttelepítési folyamaton, a felhasználónak át kell telepítenie az alkalmazásokhoz való hozzáféréshez.
* Ha egy nem áttelepített felhasználó egy másik áttelepített felhasználót adott hozzá az alkalmazáshoz, hiba lép fel, mivel nem tud egy áttelepített felhasználót együttműködési munkatársként hozzáadni egy alkalmazáshoz. A nem áttelepített felhasználó nak ezután át kell mennie az áttelepítési folyamaton, és létre kell hoznia egy azure-erőforrást, és hozzá kell adnia az áttelepített felhasználót az erőforrás közreműködőjeként.

Az áttelepítési folyamat során hibaüzenet jelenik meg, ha:
* Az előfizetés nem jogosítja fel a Cognitive Services-erőforrások létrehozására
* Az áttelepítés negatívan befolyásolja az alkalmazások futásidejű. Az áttelepítéssorán a rendszer eltávolítja a közreműködőket az alkalmazásokból, és önt is eltávolítja, mint más alkalmazásokból. Ez a folyamat azt jelenti, hogy a hozzárendelt kulcsokat is eltávolítjuk. Az áttelepítés letiltásra kerül, ha más alkalmazásokban rendelt kulcsokat. Az áttelepítés előtt távolítsa el a biztonságosan hozzárendelt kulcsot. Ha tudja, hogy a hozzárendelt kulcs nem használatos a futásidőben, majd el kell távolítania, hogy képes legyen az áttelepítés előrehaladásához.

Az alkalmazás Azure-erőforráslistájának elérése a következő URL-formátumban:

`https://www.luis.ai/applications/REPLACE-WITH-YOUR-APP-ID/versions/REPLACE-WITH-YOUR-VERSION-ID/manage/resources`

## <a name="next-steps"></a>További lépések

* [Az alkalmazás áttelepítése szerzői erőforrásba](luis-migration-authoring-steps.md)
