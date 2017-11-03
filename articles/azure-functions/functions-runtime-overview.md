---
title: "Azure Functions futásidejű áttekintése |} Microsoft Docs"
description: "Az Azure Functions futásidejű előzetes áttekintése"
services: functions
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 05/08/2017
ms.author: anwestg
ms.openlocfilehash: cb98d5f2aaa526555820c15ba5a32fb7e78ffc5a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-runtime-overview"></a>Az Azure Functions futásidejű áttekintése

Az Azure Functions Futtatókörnyezettel programozási modell a helyszíni, hogy kihasználják az az egyszerűség és rugalmasság az Azure Functions egy új megoldást kínál. Az azonos nyílt forráskódú gyökerek, az Azure Functions épülő Azure Functions Futtatókörnyezettel telepített helyi a majdnem azonos fejlesztése érdekében a felhőalapú szolgáltatás.

![Az Azure Functions futásidejű a betekintő portálon][1]

Az Azure Functions Futtatókörnyezettel biztosítja, hogy az Azure Functions élmény előtt véglegesítése a felhőbe. Ezzel a módszerrel a kód eszközök készít majd átvihető Önnel a felhő áttelepítésekor.  A futtatókörnyezet új beállítások, például éjszaka futtatni kötegelt eljárások a tartalék számítási teljesítményt, a helyszíni számítógépek segítségével is megnyílik. Is használhatja eszközök a szervezetben feltételesen küldhet adatokat, más rendszerekre, mind a helyszíni és a felhőben.

Az Azure Functions Futtatókörnyezettel két részből áll:
* Az Azure Functions futásidejű kezelés szerepköre
* Az Azure Functions futásidejű feldolgozói szerepkör

## <a name="azure-functions-management-role"></a>Az Azure Functions kezelés szerepköre

Az Azure Functions szerepkör állomás biztosít a funkciók a helyszíni kezelését. Ezt a szerepkört a következő feladatokat hajtja végre:

* Az Azure Functions felügyeleti portálra, amely az üzemeltetési a megegyezik a látni a [Azure-portálon](https://portal.azure.com). Ez lehetővé teszi fejlesztése a funkciók ugyanúgy, mint az Azure-portálon.
* Funkciók osztja több funkciók dolgozó között.
* A közzétételi végpont biztosítása, így közzéteheti a függvényeket közvetlenül a Microsoft Visual Studio eszközből.

## <a name="azure-functions-worker-role"></a>Az Azure Functions feldolgozói szerepkör

Az Azure Functions feldolgozói szerepkörök telepítve van a Windows-tárolókban, és azt, ahol a funkciókódot hajt végre.  Több feldolgozói szerepköröket telepítheti a szervezetben, és ahol tehetik kulcs úgy használjon tartalék számítási teljesítményt.

## <a name="minimum-requirements"></a>Minimumkövetelmények

Ismerkedés az Azure Functions Futtatókörnyezettel a virtuális gép kell **Windows Server 2016 vagy a Windows 10 Creators Update** hozzáférést egy **SQL Server** példány.

## <a name="next-steps"></a>Következő lépések

Telepítse a [Azure Functions Futtatókörnyezettel előzetes verzió](https://aka.ms/azafr)

<!--Image references-->
[1]: ./media/functions-runtime-overview/AzureFunctionsRuntime_Portal.png
