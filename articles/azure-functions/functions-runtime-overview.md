---
title: Azure Functions futásidejű áttekintése |} Microsoft Docs
description: Az Azure Functions futásidejű előzetes áttekintése
services: functions
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: 557f071e2cd8d4f639c881274e6e74a8fb745859
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2017
ms.locfileid: "26290225"
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

* Az Azure Functions felügyeleti portálra, amely megegyezik a látni tárolása a [Azure-portálon](https://portal.azure.com). A portál, amely lehetővé teszi fejlesztése a funkciók ugyanúgy, mint az Azure-portálon következetes élményt nyújt.
* Funkciók osztja több funkciók dolgozó között.
* Közzétételi végpont biztosítása, így közzéteheti a függvényeket közvetlenül a Microsoft Visual Studio által letöltéséhez és a közzétételi profil importálásához.

## <a name="azure-functions-worker-role"></a>Az Azure Functions feldolgozói szerepkör

Az Azure Functions feldolgozói szerepkörök vannak telepítve, a Windows-tárolók és ahol a funkciókódot hajt végre.  Több feldolgozói szerepköröket telepítheti a szervezetben, és ez a beállítás, amelyben tehetik kulcs úgy használata tartalék számítási teljesítményt.  Egy tartalék számítási esetén számos szervezet példája gépeket folyamatosan bekapcsolt, de nagy időszakokra, nem használható.

## <a name="minimum-requirements"></a>Minimumkövetelmények

Való ismerkedés az Azure Functions futtatókörnyezete, virtuális gép Windows Server 2016 vagy a Windows 10 Creators frissítés egy SQL Server-példányhoz való hozzáféréssel kell rendelkeznie.

## <a name="next-steps"></a>Következő lépések

Telepítse a [Azure Functions Futtatókörnyezettel előzetes verzió](https://aka.ms/azafrdoc)

<!--Image references-->
[1]: ./media/functions-runtime-overview/AzureFunctionsRuntime_Portal.png
