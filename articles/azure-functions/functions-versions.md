---
title: "Az Azure Functions futásidejű verziók áttekintése"
description: "Az Azure Functions a futtatókörnyezet több verzióit támogatja. Ismerje meg őket, és hogyan válassza azt, amelyik az Ön számára legmegfelelőbb közötti különbségeket."
services: functions
documentationcenter: 
author: ggailey777
manager: cfowler
editor: 
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.author: glenga
ms.openlocfilehash: 9f916aaa8032ff519709d73a1c1f51195f811686
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="azure-functions-runtime-versions-overview"></a>Az Azure Functions futásidejű verziók áttekintése

 Két fő verziója van az Azure Functions futtatókörnyezethez: 1.x és a 2.x. Ez a cikk azt ismerteti, hogyan kiválaszthatja, melyik fő verziót kell használni.

> [!IMPORTANT] 
> Futásidejű 1.x az üzemi használatra jóváhagyott csak verziója.

| Futtatókörnyezet | status |
|---------|---------|
|1.x|Általában elérhető (GA)|
|2.x|Előzetes verzió|

Egy függvény alkalmazást vagy egy adott verziójához a fejlesztési környezet konfigurálásával kapcsolatos további információkért lásd: [bemutatásához az Azure Functions futásidejű verziók](set-runtime-version.md) és [kód és a vizsgálati helyileg Azure Functions](functions-run-local.md).

## <a name="cross-platform-development"></a>Platformfüggetlen fejlesztésekhez

Futásidejű 1.x támogatja a fejlesztési és üzemeltetési csak a portál vagy a Windows. Futásidejű 2.x futtat a .NET Core, ami azt jelenti, .NET Core, beleértve a macOS és a Linux által támogatott összes platformokon futtatható. Ez lehetővé teszi a platformok közötti fejlesztési és üzemeltetési helyzeteket, amelyek nem 1.x tesz lehetővé.

## <a name="languages"></a>Nyelvek

Futásidejű 2.x új nyelvi bővítési modellt használ. Kezdetben JavaScript és Java tart az új modell előnyeit. Az Azure Functions 1.x kísérleti nyelveket az új modell használatára, így azok nem támogatottak a 2.x még nem frissítették. A következő táblázat minden egyes futásidejű verziójában támogatott programozási nyelveket.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

További információkért lásd: [támogatott nyelv](supported-languages.md).

## <a name="bindings"></a>Kötések 

Futásidejű 2.x használ egy új [kötés bővíthetőségi modell](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) , amely ezeket az előnyöket nyújtja:

* Támogatása a külső kötés.
* Futásidejű és kötések leválasztásával. Ez lehetővé teszi, hogy a kötés bővítmények rendszerverzióval ellátott és egymástól függetlenül kiadott kell lennie. Dönthet úgy is, például az olyan bővítményekre, amely egy alapul szolgáló SDK újabb verziójával támaszkodik egy verziójára történő frissítéshez.
* Egy világosabb végrehajtási környezetben, ahol csak a használatban lévő kötések ismert és a futtatókörnyezet által betöltött.

Minden beépített Azure Functions kötés kezdtek el alkalmazni ezt a modellt, és már nem szerepelnek az alapértelmezés szerint a időzítő indítófeltételt és a HTTP-eseményindítóval kivételével. Ezek a bővítmények automatikusan települnek, Funkciók, például a Visual Studio eszközök vagy a portálon keresztül létrehozásakor.

A következő táblázat azt jelzi, hogy mely kötések támogatottak a minden egyes futásidejű verzióját.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

## <a name="known-issues-in-2x"></a>2.x kapcsolatos ismert problémák

Kötések támogatási és egyéb működési hiányosságait 2.x kapcsolatban további információkért lásd: [ismert problémák futtatókörnyezet 2.0](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Azure-Functions-runtime-2.0-known-issues).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A helyi fejlesztési környezetben 2.0 futásidejű cél](functions-run-local.md)

> [!div class="nextstepaction"]
> [Tekintse meg a kibocsátási megjegyzések a futtatókörnyezet-verzió](https://github.com/Azure/azure-webjobs-sdk-script/releases)