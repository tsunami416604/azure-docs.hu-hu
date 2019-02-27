---
title: Mi az Azure-alkalmazások konfigurálása |} A Microsoft Docs
description: Az Azure App konfigurációs szolgáltatás áttekintése.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 11dd91039bb352e86800982d0a294f82622a56fe
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56884831"
---
# <a name="what-is-azure-app-configuration"></a>Mi az Azure-alkalmazások konfigurálása

Az Azure App konfigurálása Alkalmazásbeállítások kezelése szolgáltatást biztosít központilag. A modern, különösen egy felhőben futó programok általában számos összetevőből jellegű elosztott rendelkezik. Konfigurációs beállítások terjedhetnek ezen összetevők között vezethet rögzített hibaelhárítása hibák az alkalmazások központi telepítése során. Alkalmazások konfigurálása lehetővé teszi az alkalmazás összes beállításainak tárolására, és biztonságos azok hozzáfér egy helyen.

## <a name="why-use-app-configuration"></a>Miért érdemes használni az alkalmazások konfigurálása

Felhőalapú alkalmazások gyakran több virtuális gépek és tárolók több régióban is futtathatja, és több külső szolgáltatások használata. Elosztott alkalmazások létrehozása, amely hatékony és méretezhető valós kihívást jelent. Különböző programozási módszerek segítségével a fejlesztők foglalkoznak, ezek az alkalmazások készítése a növekvő összetettsége merültek fel. Például a 12 tényezős alkalmazásfejlesztési részletesen számos tesztelt architektúramintákat és ajánlott eljárások a felhőbeli alkalmazásokhoz való használatra. Ez az útmutató egy kulcs ajánljuk, hogy külön konfigurációs code-ból. Ez azt jelenti, hogy egy alkalmazás konfigurációs beállítások például külső tartani a végrehajtható fájlt kell-e, és a futtatókörnyezet vagy külső forrásból olvas be.

Bármely alkalmazás győződjön meg arról is, miközben jó példa a különböző típusú alkalmazás, amely kell használni az Alkalmazáskonfigurációt igénybe, a következőket:

* Mikroszolgáltatás alapú AKS, Service Fabric vagy más tárolóalapú alkalmazások üzembe helyezett egy vagy több földrajzi területeken.
* Kiszolgáló nélküli alkalmazások, beleértve az Azure Functionsből vagy más állapot nélküli számítási eseményvezérelt alkalmazásokat.
* Folyamatos üzembe helyezési folyamatot.

Alkalmazások konfigurálása az alábbi előnyöket biztosítja:

* Egy teljes körűen felügyelt szolgáltatás, amely percek alatt beállíthatja.
* Rugalmas kulcs nyilatkozatok és hozzárendeléseket.
* Címkézés, a címkék.
* Pont-in-time ismétlés beállításai.
* A dimenziók egyénileg definiált konfigurációk két készletnyi összehasonlítása.
* Fokozott biztonság az Azure által felügyelt identitások használatával.
* Végezze el az adatok titkosítások használatára, inaktív és átvitel közben.
* Natív integráció a népszerű keretrendszereket.

## <a name="how-to-use-app-configuration"></a>Hogyan használhatja az Alkalmazáskonfigurációt

A legegyszerűbben úgy, hogy adjon hozzá egy ügyfélkódtáron egy alkalmazás a konfigurációs adattárolónál az alkalmazásnak, hogy a Microsoft biztosít. Programozási nyelv és keretrendszer, a következőket az elérhető legjobb módszerek megjelenítése Önnek:

| Programozási nyelv és keretrendszer | Csatlakozás |
|---|---|
| **A .NET core** és **ASP.NET Core** | Alkalmazás konfigurációja konfigurációszolgáltatót a .NET Core. |
| **.NET** és **ASP.NET** | Alkalmazás konfigurációja konfigurációs builder a .NET-hez. |
| **Java Spring** | Alkalmazás konfigurációja konfigurációs ügyfél Spring Cloud. |
| Egyéb | Alkalmazáskonfiguráció – REST API-t. |

## <a name="next-steps"></a>További lépések

* [Rövid útmutató: ASP.NET-webalkalmazás létrehozása](quickstart-aspnet-core-app.md)  
