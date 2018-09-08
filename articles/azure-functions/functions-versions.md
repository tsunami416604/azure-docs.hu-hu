---
title: Az Azure Functions runtime verziók áttekintése
description: Az Azure Functions futtatókörnyezet több verzióit támogatja. Ismerje meg azokat, és hogyan választható ki a másik pedig az Önnek megfelelő közötti különbségeket.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 07/29/2018
ms.author: glenga
ms.openlocfilehash: 716f2b537a47c6e721c7393cba340a583c7ed064
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44092138"
---
# <a name="azure-functions-runtime-versions-overview"></a>Az Azure Functions runtime verziók áttekintése

 Az Azure Functions Runtime két fő verziója: 1.x és a 2.x. Éles környezetben való használatra csak 1.x engedélyezték. Ez a cikk ismerteti, what's new in 2.x verziója, amely előzetes verzióban érhető el.

| Futtatókörnyezet | status |
|---------|---------|
|1.x|Általánosan elérhető (GA)|
|2.x|Előzetes verzió<sup>*</sup>|

<sup>*</sup>Verzió fontos frissítések fogadása 2.x, beleértve a használhatatlanná tévő módosítja a bejelentésekről, tekintse meg a [Azure App Service közlemények](https://github.com/Azure/app-service-announcements/issues) tárház.

> [!NOTE] 
> Ez a cikk az Azure Functions felhőalapú szolgáltatásra vonatkozik. A termék, amely lehetővé teszi Azure Functions helyi futtatását kapcsolatos információkért tekintse meg a [Azure Functions Runtime áttekintése](functions-runtime-overview.md).

## <a name="cross-platform-development"></a>Platformfüggetlen fejlesztés

Futtatókörnyezet 1.x támogatja a fejlesztési és üzemeltetési csak a portálon vagy a Windows. Futásidejű 2.x fut a .NET Core, ami azt jelenti, .NET Core, beleértve a macOS és Linux által támogatott összes platformokon futtatható. Ez lehetővé teszi a platformfüggetlen fejlesztési és üzemeltetési helyzeteket, amelyek nem lehetséges a 1.x.

## <a name="languages"></a>Nyelvek

Futásidejű 2.x új nyelvi bővíthetőségi modellt használ. Kezdetben a JavaScript és Java is kihasználhatja az új modell. Az Azure Functions 1.x kísérleti nyelvek még nem lett frissítve az új modellt használ, így a 2.x-es nem támogatottak. Az alábbi táblázat azt jelzi, hogy az egyes verze modulu runtime támogatott programozási nyelvek.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

További információkért lásd: [támogatott nyelvek](supported-languages.md).

## <a name="bindings"></a>Kötések 

Futásidejű 2.x használja egy új [bővíthetőségi modell kötése](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) , amely ezeket az előnyöket kínálja:

* Támogatás a külső kötési bővítményeket.
* Elválasztás futtatókörnyezeti és a kötéseket. Ez lehetővé teszi a rendszerverzióval ellátott és egymástól függetlenül kiadott kötési bővítményeket. Például kérheti egy bővítmény, amely az alapul szolgáló SDK újabb verziója támaszkodik a verzióra frissíteni.
* Egy világosabb végrehajtási környezetet, ahol csak a használatban lévő kötéseket ismertek és a futtatókörnyezet által betöltött.

Az összes beépített Azure Functions-bindings elfogadták ezt a modellt, és már nem szerepelnek alapértelmezetten, kivéve az időzített eseményindítóknak és a HTTP-eseményindítóval. Ezek a bővítmények automatikusan települnek, függvények, például a Visual Studio eszközök vagy a portálon keresztül létrehozásakor.

Az alábbi táblázat azt jelzi, hogy melyik kötések támogatottak a minden egyes modul verzióját.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

## <a name="known-issues-in-2x"></a>A 2.x ismert problémák

További információ a kötéseket és egyéb működési hiányosságok 2.x: [futtatókörnyezet 2.0 ismert problémák](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Azure-Functions-runtime-2.0-known-issues).

## <a name="next-steps"></a>További lépések

További információkért lásd a következőket:

* [Az Azure Functions helyi kódolása és tesztelése](functions-run-local.md)
* [Az Azure Functions runtime verziók bemutatásához](set-runtime-version.md)
* [Kiadási megjegyzések](https://github.com/Azure/azure-functions-host/releases)
