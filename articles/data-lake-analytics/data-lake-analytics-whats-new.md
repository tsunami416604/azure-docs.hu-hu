---
title: Legutóbbi módosítások Data Lake Analytics
description: Ez a cikk a Data Lake Analytics legutóbbi változásainak listáját tartalmazza.
author: xujiang1
ms.service: data-lake-analytics
ms.topic: overview
ms.author: xujiang1
ms.date: 07/31/2020
ms.openlocfilehash: 0afd37a9f80fd2778f2b7206f58cec6ff423763e
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2020
ms.locfileid: "88184958"
---
# <a name="whats-new-in-data-lake-analytics"></a>A Data Lake Analytics újdonságai

A Azure Data Lake Analytics bizonyos összetevők aperiodic alapján frissülnek. A legfrissebb frissítéssel naprakészen tarthatja a következő információkat:

- A kulcs összetevő bétaverziójának előzetes verziójának értesítése
- A fontos összetevő-verzióra vonatkozó információk, például: az összetevő elérhető verzióinak listája, a jelenlegi alapértelmezett verzió és így tovább.


## <a name="notification-of-key-component-beta-preview"></a>Értesítés a kulcs összetevő bétaverziójának előzetes verziójáról

Az előzetes verzióhoz nem érhető el a kulcs összetevő bétaverziója. 

## <a name="u-sql-runtime"></a>U-SQL-futtatókörnyezet

A Azure Data Lake u-SQL-futtatókörnyezet, beleértve a fordítót, az optimalizáló és a Feladatkezelőt, a u-SQL-kódot dolgozza fel.

Ha bármely eszközről elküldi a Azure Data Lake Analytics-feladatot, a feladata az aktuálisan elérhető alapértelmezett futtatókörnyezetet fogja használni éles környezetben. 

A futtatókörnyezet verziója frissülni fog a aperiodically. Az előző futtatókörnyezet pedig egy ideig elérhető marad. Ha egy új bétaverzió már készen áll az előzetes verzióra, ott is elérhető lesz.

A jelenleg elérhető futásidejű verziók a következők.

- kiadás – 20200124live_adl_16283022_2 – **a jelenlegi alapértelmezett verzió** >
- release_20200124live_adl_16283022
- release_20200124_adl_14480125
- release_20190904_adl_10236248_1
- release_20190904_adl_10236248
- release_20190904_adl_9225818

Az U-SQL futásidejű hibák elhárításának megismeréséhez tekintse meg az [u-SQL-futtatókörnyezet hibáinak elhárítása](runtime-troubleshoot.md)című témakört.

## <a name="net-framework"></a>.NET-keretrendszer

Azure Data Lake Analytics most a .net- **keretrendszer v 4.7.2**használja. 

Ha a Azure Data Lake Analytics U-SQL-szkript kódja egyéni szerelvényeket használ, és ezek az egyéni szerelvények a .NET-kódtárakat használják, érvényesítse a kódot, és ellenőrizze, hogy van-e megszakítás.

Annak megismeréséhez, hogy miként lehet elhárítani a .NET-frissítést a [.net-frissítéssel kapcsolatos hibák megoldásával](runtime-troubleshoot.md).

## <a name="release-note"></a>Kiadási Megjegyzés

A legutóbbi frissítés részleteiért tekintse meg a [Azure Data Lake Analytics kibocsátási megjegyzését](https://github.com/Azure/AzureDataLake/tree/master/docs/Release_Notes).


## <a name="next-steps"></a>Következő lépések

* Ismerkedés a Data Lake Analytics [Azure Portal](data-lake-analytics-get-started-portal.md)  |  [Azure PowerShell](data-lake-analytics-get-started-powershell.md)  |  [CLI](data-lake-analytics-get-started-cli.md) használatával

