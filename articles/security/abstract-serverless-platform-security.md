---
title: Absztrakt Azure-funkciók és biztonsági kiszolgáló nélküli Platform
description: Az Azure Functions és a kiszolgáló nélküli Platform biztonsági tanulmányt absztrakt.
author: TomShinder
ms.author: TomSh
ms.date: 06/21/2018
ms.topic: article
ms.service: security
ms.openlocfilehash: 8f5f600b0680b85d760e72b8e177ae247e41ac6b
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37132942"
---
# <a name="azure-functions-and-serverless-platform-security"></a>Az Azure Functions és a kiszolgáló nélküli platform biztonsági
## <a name="abstract"></a>Absztrakt
A legtöbb vállalat kell jelentős mennyiségű erőforrással és idővel kezelését, amely további költség. A vállalatok kevesebb erőforrást segítségével kezelheti a kiszolgálók, ha azok összpontosíthat kiváló alkalmazások.  

Kiszolgáló nélküli számítástechnikai segít éppen ez, mert futtatásához és az alkalmazások méretezéséhez szükséges infrastruktúra van kezelve. A kiszolgáló nélküli számítástechnika a kiszolgálók, az infrastruktúra és az operációs rendszerek absztrakciója. Kiszolgáló nélküli események és eseményindítók szembeni célja a számítástechnikai, amelyeket az összes zajló közel valós idejű – a felhőben. 

Teljes körűen felügyelt szolgáltatásként-kezelő és a kapacitástervezés nem láthatók a fejlesztőnek. A kiszolgáló nélküli keretrendszer segítséget nyújt a fejlesztés és a kiszolgáló nélküli alkalmazások telepítését az Azure Functions. A parancssori felület (CLI) struktúra és az automatizálás segítségükkel kifinomult, eseményvezérelt, kiszolgáló nélküli architektúrák funkciók és események által is. Egy Azure függvény egy független telepítési egység, amely, például egy mikroszolgáltatási. Csupán kódot, a felhőben, egyetlen feladat elvégzésére leggyakrabban írt telepítve.

Annak ellenére, hogy az előnyöket a kiszolgáló nélküli biztonsági rendelkezik saját kockázati tényezők kezelésére. A kiszolgáló nélküli megközelítés nem vezetnek be az új biztonsági problémáit, de ha egy meglévő biztonsági problémáit módjáról igényel. Ez a dokumentum elsősorban a biztonsági kérdések: 
* Egy kiszolgáló nélküli platform előnyei
* A kiszolgáló nélküli számítógépes biztonsági problémái
* Fontos biztonsági problémákra és azok mérséklési környezetében az Azure
* Biztonságossá tétele a Microsoft kiszolgáló nélküli platform

[Töltse le a tanulmány](https://gallery.technet.microsoft.com/Azure-Functions-and-c6449f8d/file/202175/1/Microsoft%20Serverless%20Platform.pdf)

