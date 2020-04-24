---
title: Szerzői és futásidejű kulcsok használata a LUIS használatával
titleSuffix: Azure Cognitive Services
description: A LUIS két kulcsot használ, a szerzői kulcs segítségével létrehozza a modellt és a futásidejű kulcsot az előrejelzési végpont felhasználói hosszúságú kimondott szöveg való lekérdezéséhez.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 954e7a22ae6b242c6221119c688259e4ce629a2a
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82101059"
---
# <a name="authoring-and-runtime-keys"></a>Tartalomkészítési és futtatókörnyezeti kulcsok

Language Understanding (LUIS) két szolgáltatással és API-készlettel rendelkezik:

* Szerzői műveletek (korábbi nevén _programozott_)
* Előrejelzési futtatókörnyezet

A szolgáltatással együtt szeretne dolgozni, és azt is, hogy hogyan kívánja használni a különböző típusú kulcsokat.

## <a name="non-azure-resources-for-luis"></a>Nem Azure-erőforrások a LUIS számára

### <a name="starter-key"></a>Alapszintű kulcs

A LUIS használatának első megkezdése után létrejön egy **kezdő kulcs** . Ez az erőforrás a következőket biztosítja:

* a LUIS-portálon vagy API-kon (beleértve az SDK-kat) használó szolgáltatásokra vonatkozó kérelmek ingyenes készítése
* Ingyenes 1 000 előrejelzési végponti kérelmek havonta egy böngészőben, API-n vagy SDK-n keresztül

## <a name="azure-resources-for-luis"></a>A LUIS-hez készült Azure-erőforrások

<a name="programmatic-key" ></a>
<a name="endpoint-key"></a>
<a name="authoring-key"></a>

A LUIS három típusú Azure-erőforrást tesz lehetővé:

|Kulcs|Cél|Kognitív szolgáltatás`kind`|Kognitív szolgáltatás`type`|
|--|--|--|--|
|[Szerzői kulcs](#programmatic-key)|A szerzői műveletek, a képzés, a közzététel és a tesztelés segítségével hozzáférhetnek az alkalmazáshoz, és kezelhetik azokat. Hozzon létre egy LUIS authoring-kulcsot, ha a LUIS-alkalmazásokat programozott módon szeretné létrehozni.<br><br>A `LUIS.Authoring` kulcs célja, hogy lehetővé tegye a következőket:<br>* programozott módon felügyelheti Language Understanding alkalmazásokat és modelleket, beleértve a képzést és a közzétételt<br> * a szerzői erőforrásra vonatkozó engedélyek vezérlése [a közreműködő szerepkörhöz](#contributions-from-other-authors)rendelt személyek hozzárendelésével.|`LUIS.Authoring`|`Cognitive Services`|
|[Előrejelzési kulcs](#prediction-endpoint-runtime-key)| Lekérdezés-előrejelzési végponti kérelmek. Hozzon létre egy LUIS-előrejelzési kulcsot, mielőtt az ügyfélalkalmazás a kezdő erőforrás által biztosított 1 000-kérelmeknél újabb előrejelzéseket kér. |`LUIS`|`Cognitive Services`|
|[Kognitív szolgáltatás – több szolgáltatásból álló erőforrás kulcsa](../cognitive-services-apis-create-account-cli.md?tabs=windows#create-a-cognitive-services-resource)|A lekérdezés-előrejelzési végpontok megosztva a LUIS és más támogatott Cognitive Servicesokkal.|`CognitiveServices`|`Cognitive Services`|

Ha az erőforrás-létrehozási folyamat elkészült, [rendelje hozzá a kulcsot](luis-how-to-azure-subscription.md) az alkalmazáshoz a Luis portálon.

Fontos, hogy LUIS-alkalmazásokat hozzon létre azokon a [régiókban](luis-reference-regions.md#publishing-regions) , amelyeken közzé és lekérdezéseket szeretne közzétenni.

> [!CAUTION]
> A legtöbb példa az [alapszintű kulcsot](#starter-key) használja, mivel néhány ingyenes előrejelzési végpontot biztosít a [kvótájában](luis-limits.md#key-limits).


### <a name="query-prediction-resources"></a>Lekérdezés-előrejelzési erőforrások

* A futásidejű kulcs az összes LUIS-alkalmazáshoz, illetve az adott LUIS-alkalmazásokhoz is használható.
* Ne használja a futásidejű kulcsot a LUIS-alkalmazások létrehozásához.

A LUIS Runtime végpont két stílust fogad el, mindkettő az előrejelzési végpont futtatókörnyezetének kulcsát használja, de különböző helyeken.

A futtatókörnyezet eléréséhez használt végpont olyan altartományt használ, amely egyedi az erőforrás régiójában, és `{region}` az alábbi táblázatban látható.

## <a name="assignment-of-the-key"></a>A kulcs hozzárendelése

A futásidejű kulcsot a [Luis-portálon](https://www.luis.ai) vagy a megfelelő API-kon keresztül [rendelheti hozzá](luis-how-to-azure-subscription.md) .

## <a name="key-limits"></a>Kulcs korlátai

Régiónként legfeljebb 10 szerzői kulcsot hozhat létre egy előfizetéshez.

Lásd a [legfontosabb korlátokat](luis-limits.md#key-limits) és az [Azure-régiókat](luis-reference-regions.md).

A közzétételi régiók eltérnek a szerzői régióktól. Győződjön meg arról, hogy létrehoz egy alkalmazást a szerzői régióban, amely ahhoz a közzétételi régióhoz tartozik, amelyet az ügyfélalkalmazás el szeretne helyezni.

## <a name="key-limit-errors"></a>Kulcs korlátozásával kapcsolatos hibák
Ha túllépi a tranzakció-másodpercenkénti (TPS-) kvótát, HTTP 429-es hibaüzenetet kap. Ha túllépi a havi tranzakciós (TPS) kvótát, HTTP 403-es hibaüzenetet kap.

## <a name="contributions-from-other-authors"></a>Más szerzők hozzájárulásai

**Az [áttelepített erőforrás-alkalmazások létrehozásához](luis-migration-authoring.md) **: a _közreműködők_ kezelése a Azure Portal a szerzői erőforráshoz a **hozzáférés-vezérlés (iam)** lapon. Megtudhatja, [hogyan adhat hozzá felhasználót](luis-how-to-collaborate.md)a közreműködő e-mail-címével és a _közreműködő_ szerepkörrel.

**Még nem telepített alkalmazások esetén**: az összes _közreműködőt_ a Luis portálon kezelheti a **Manage-> közreműködők** lapról.

## <a name="move-transfer-or-change-ownership"></a>Tulajdonos áthelyezése, átvitele vagy módosítása

Az alkalmazást az Azure-erőforrásai határozzák meg, amelyet a tulajdonos előfizetése határoz meg.

Elvégezheti a LUIS-alkalmazás áthelyezését. Használja az alábbi dokumentációs erőforrásokat a Azure Portal vagy az Azure CLI-ben:

* [Alkalmazás áthelyezése a LUIS authoring-erőforrások között](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [Erőforrás áthelyezése új erőforráscsoporthoz vagy előfizetésbe](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Erőforrás áthelyezése ugyanazon az előfizetésen belül vagy előfizetések között](../../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)

Az előfizetés [tulajdonjogának](../../cost-management-billing/manage/billing-subscription-transfer.md) átadása:

**Azon felhasználók esetében, akik áttelepítették az [erőforrás-áttelepített](luis-migration-authoring.md) alkalmazásokat**: az erőforrás tulajdonosaként hozzáadhat egy. `contributor`

**A még nem migrált felhasználók esetében**: az alkalmazás exportálása JSON-fájlként. Egy másik LUIS-felhasználó is importálhatja az alkalmazást, így az alkalmazás tulajdonosa válik. Az új alkalmazás egy másik alkalmazás-AZONOSÍTÓval fog rendelkezni.

## <a name="access-for-private-and-public-apps"></a>Hozzáférés magán-és nyilvános alkalmazásokhoz

**Privát** alkalmazások esetén a futtatókörnyezet-hozzáférés a tulajdonosokhoz és a közreműködők számára érhető el. **Nyilvános** alkalmazások esetén a futtatókörnyezet-hozzáférés mindenki számára elérhető, aki saját Azure [kognitív szolgáltatással](../cognitive-services-apis-create-account.md) vagy [Luis](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) futásidejű erőforrással rendelkezik, és rendelkezik a nyilvános alkalmazás azonosítójával.

Jelenleg nem létezik katalógus a nyilvános alkalmazásokról.

### <a name="authoring-access"></a>Szerzői hozzáférés
Az alkalmazáshoz való hozzáférést a [Luis](luis-reference-regions.md#luis-website) portálról vagy a [szerzői API-kat](https://go.microsoft.com/fwlink/?linkid=2092087) az Azure authoring Resource vezérli.

A tulajdonos és az összes közreműködő hozzáfér az alkalmazás létrehozásához.

|A szerzői hozzáférés magában foglalja a|Megjegyzések|
|--|--|
|Végponti kulcsok hozzáadása vagy eltávolítása||
|Verzió exportálása||
|Végponti naplók exportálása||
|Verzió importálása||
|Alkalmazás nyilvánosvé tétele|Ha egy alkalmazás nyilvános, a szerzői vagy végponti kulccsal bárki lekérdezheti az alkalmazást.|
|Modell módosítása|
|Közzététel|
|Az [aktív tanulási](luis-how-to-review-endpoint-utterances.md) végpont hosszúságú kimondott szöveg áttekintése|
|Betanítás|

<a name="prediction-endpoint-runtime-key"></a>

### <a name="prediction-endpoint-runtime-access"></a>A végpont futásidejű hozzáférésének előrejelzése

Az előrejelzési végpont lekérdezéséhez a **kezelés** szakasz **alkalmazás adatai** lapján található beállítás vezérli.

|[Privát végpont](#runtime-security-for-private-apps)|[Nyilvános végpont](#runtime-security-for-public-apps)|
|:--|:--|
|Elérhető a tulajdonos és a közreműködők számára|Elérhető a tulajdonos, a közreműködők és bárki másnak, aki ismeri az alkalmazás AZONOSÍTÓját|

Megadhatja, hogy ki látja el a LUIS Runtime-kulcsot úgy, hogy a kiszolgálót kiszolgáló-kiszolgáló környezetben hívja meg. Ha a LUIS-t egy robotból használja, a robot és a LUIS közötti kapcsolat már biztonságos. Ha közvetlenül hívja meg a LUIS-végpontot, hozzon létre egy kiszolgálóoldali API-t (például egy Azure- [függvényt](https://azure.microsoft.com/services/functions/)) vezérelt hozzáféréssel (például [HRE](https://azure.microsoft.com/services/active-directory/)). A kiszolgálóoldali API meghívása és hitelesítése, valamint az engedélyezés ellenőrzése után adja át a hívást a LUIS-nek. Habár ez a stratégia nem gátolja meg az emberen belüli támadásokat, az eltorzítja a felhasználók kulcs-és végpont-URL-címét, lehetővé teszi a hozzáférés nyomon követését, és lehetővé teszi a végponti válaszok naplózásának hozzáadását (például [Application Insights](https://azure.microsoft.com/services/application-insights/)).

#### <a name="runtime-security-for-private-apps"></a>Futásidejű biztonság privát alkalmazásokhoz

A privát alkalmazások futtatókörnyezete csak a következőkhöz érhető el:

|Kulcs és felhasználó|Magyarázat|
|--|--|
|Tulajdonos szerzői kulcsa| Akár 1000 végpontot elér|
|Közreműködő/közreműködő szerzői kulcsok| Akár 1000 végpontot elér|
|Egy szerző vagy közreműködő vagy közreműködő által a LUIShoz rendelt kulcs|Kulcshasználat szintje alapján|

#### <a name="runtime-security-for-public-apps"></a>Futtatókörnyezeti biztonság nyilvános alkalmazásokhoz

Az alkalmazás nyilvánosként való konfigurálása után _bármely_ érvényes Luis authoring Key vagy Luis Endpoint kulcs lekérdezheti az alkalmazást, feltéve, hogy a kulcs nem használta a teljes végponti kvótát.

Olyan felhasználó, aki nem tulajdonos vagy közreműködő, csak akkor férhet hozzá egy nyilvános alkalmazás futtatókörnyezetéhez, ha az alkalmazás azonosítója van megadva. A LUIS nem rendelkezik nyilvános _piaccal_ vagy más módszerrel a nyilvános alkalmazások kereséséhez.

A nyilvános alkalmazások minden régióban közzé vannak téve, hogy a régió-alapú LUIS-erőforrás kulcsával rendelkező felhasználók hozzáférhessenek az alkalmazáshoz, amely bármelyik régióban hozzá van rendelve az erőforrás-kulcshoz.

## <a name="transfer-of-ownership"></a>Tulajdonjog átruházása

LUIS nem rendelkezik az erőforrások tulajdonjogának átadására szolgáló koncepcióval.

## <a name="securing-the-endpoint"></a>A végpont biztonságossá tétele

Megadhatja, hogy ki láthatja a LUIS előrejelzési futtatókörnyezet végpontjának kulcsát úgy, hogy a kiszolgálót kiszolgáló-kiszolgáló környezetben hívja meg. Ha a LUIS-t egy robotból használja, a robot és a LUIS közötti kapcsolat már biztonságos. Ha közvetlenül hívja meg a LUIS-végpontot, hozzon létre egy kiszolgálóoldali API-t (például egy Azure- [függvényt](https://azure.microsoft.com/services/functions/)) vezérelt hozzáféréssel (például [HRE](https://azure.microsoft.com/services/active-directory/)). A kiszolgálóoldali API meghívásakor, valamint a hitelesítés és az engedélyezés ellenőrzése után adja át a hívást a LUIS-nek. Habár ez a stratégia nem gátolja meg az emberen belüli támadásokat, az eltorzítja a végpontot a felhasználóktól, lehetővé teszi a hozzáférés nyomon követését, és lehetővé teszi a végponti válaszok naplózásának (például [Application Insights](https://azure.microsoft.com/services/application-insights/)) hozzáadását.

## <a name="next-steps"></a>További lépések

* A [verziószámozási](luis-concept-version.md) fogalmak ismertetése.
* Útmutató [a kulcsok létrehozásához](luis-how-to-azure-subscription.md).
