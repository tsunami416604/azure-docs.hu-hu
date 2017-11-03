---
title: "A logic Apps alkalmazásait az SFTP-összekötő használata |} Microsoft Docs"
description: "Az Azure App service logic Apps alkalmazások létrehozása Csatlakozás küldeni és fogadni fájlok SFTP API. Például hozzon létre, update, get vagy fájltörléssel különböző műveleteket hajthat végre."
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 697eb8b0-4a66-40c7-be7b-6aa6b131c7ad
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/20/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 31253d8daee1581167a96a20ba8ad529a04b3e92
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-sftp-connector"></a>Az SFTP-összekötő az első lépései
Használja az SFTP összekötő küldeni és fogadni fájlok SFTP fiók eléréséhez. Például hozzon létre, update, get vagy fájltörléssel különböző műveleteket hajthat végre.  

Használandó [a csatlakozókat](apis-list.md), először hozzon létre egy logikai alkalmazást. Elkezdheti által [logikai alkalmazás létrehozása most](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-sftp"></a>SFTP kapcsolódni
A Logic Apps alkalmazást bármely szolgáltatás hozzáférni, először hozzon létre egy *kapcsolat* a szolgáltatáshoz. A [kapcsolat](connectors-overview.md) biztosít a logikai alkalmazás és egy másik szolgáltatás közötti kapcsolat.  

### <a name="create-a-connection-to-sftp"></a>SFTP kapcsolat létrehozása
> [!INCLUDE [Steps to create a connection to SFTP](../../includes/connectors-create-api-sftp.md)]
> 
> 

## <a name="use-an-sftp-trigger"></a>Használja az SFTP eseményindító
Egy eseményindító nem egy eseményt, a logikai alkalmazás definiált munkafolyamat indításához használható. [További tudnivalók az eseményindítók](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).  

Ebben a példában a **SFTP - amikor egy fájl hozzáadása vagy módosítása** eseményindító kezdeményezhető a logic app munkafolyamat, amikor egy fájlt ad hozzá, vagy módosult, az SFTP-kiszolgáló. Is hozzáadhat olyan feltétel, amely ellenőrzi az új vagy módosított fájl tartalmát, és lehetővé teszi egy elsődleges bontsa ki a fájlt, ha a tartalma jelzi, hogy azt ki kell nyerni a tartalmak használata előtt. Végül fájl tartalmának kibontása művelet hozzáadása, és helyezze el a kibontott tartalma az SFTP kiszolgáló egyik mappájába. 

A vállalati például ehhez az eseményindítóhoz segítségével egy SFTP mappát, amelyben megrendelések egy új fájlok figyelése.  SFTP-összekötő intézkedésre aztán használhatja például a **fájl tartalmának lekérdezése**, a ahhoz, hogy további feldolgozás és a tároló tartalmának beolvasása a rendelések adatbázist.

> [!INCLUDE [Steps to create an SFTP trigger](../../includes/connectors-create-api-sftp-trigger.md)]
> 
> 

## <a name="add-a-condition"></a>Feltétel hozzáadása
> [!INCLUDE [Steps to add a condition](../../includes/connectors-create-api-sftp-condition.md)]
> 
> 

## <a name="use-an-sftp-action"></a>SFTP-művelet használata
Egy művelet során a logikai alkalmazás definiált munkafolyamat által végzett. [További információ a műveletek](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).  

> [!INCLUDE [Steps to create an SFTP action](../../includes/connectors-create-api-sftp-action.md)]
> 
> 

## <a name="connector-specific-details"></a>Összekötő-specifikus részletei

Bármely eseményindítók és a swagger definiált műveletek megtekintése, és semmilyen határnak a Lásd még: a [connector részleteket](/connectors/sftpconnector/).

## <a name="more-connectors"></a>További összekötők
Lépjen vissza a [API-k lista](apis-list.md).