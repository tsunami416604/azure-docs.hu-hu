---
title: Kapcsolódás a Google Drive-hoz
description: A Google Drive-hoz tartozó fájlok létrehozásával és kezelésével kapcsolatos munkafolyamatok automatizálása Azure Logic Apps használatával
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: 155a579438747ca7de67eba6449b8a0191e86e2c
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666839"
---
# <a name="create-and-manage-files-for-google-drive-by-using-azure-logic-apps"></a>Fájlok létrehozása és kezelése a Google Drive-hoz a Azure Logic Apps használatával

Kapcsolódjon a Google Drive-hoz fájlok létrehozásához, sorok beszerzéséhez és egyebekhez. A Google Drive használatával a következőket teheti: 

* Hozza létre üzleti folyamatát a kereséstől kapott adatok alapján. 
* Használjon műveleteket a képek kereséséhez, a hírek kereséséhez és egyebekhez. Ezek a műveletek választ kapnak, majd elérhetővé teszik a kimenetet más műveletekhez. Kereshet például egy videót, majd a Twitter használatával közzéteheti a videót egy Twitter-hírcsatornában.

A logikai alkalmazások létrehozásának első lépéseiről a [logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)című témakörben olvashat.

## <a name="create-the-connection-to-google-drive"></a>A Google Drive-hoz való kapcsolódás létrehozása

Ha hozzáadja ezt az összekötőt a logikai alkalmazásokhoz, engedélyezni kell a Logic Apps szolgáltatást a Google Drive-hoz való csatlakozáshoz.

> [!INCLUDE [Steps to create a connection to googledrive](../../includes/connectors-create-api-googledrive.md)]

A kapcsolatok létrehozása után adja meg a Google Drive tulajdonságait, például a mappa elérési útját vagy fájlnevét. 

## <a name="connector-specific-details"></a>Összekötő-specifikus részletek

Az eseményindítók, műveletek és korlátok részletes technikai részleteiért lásd az összekötők feladatainak leírását, és tekintse át az [összekötő hivatkozási oldalát](/connectors/googledrive/).

## <a name="next-steps"></a>Következő lépések

* További Logic Apps- [Összekötők](apis-list.md) megismerése
