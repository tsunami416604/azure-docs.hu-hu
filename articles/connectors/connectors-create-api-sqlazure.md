---
title: "Adja hozzá az Azure SQL Database-összekötőt a Logic Apps |} Microsoft Docs"
description: "REST API-paraméterek az Azure SQL Database-összekötő áttekintése"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: d8a319d0-e4df-40cf-88f0-29a6158c898c
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: def2b65f009c377233c45356f8fa661b86d73f51
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="get-started-with-the-azure-sql-database-connector"></a>Ismerkedés az Azure SQL Database-összekötő
Az Azure SQL Database-összekötővel, munkafolyamatokat a szervezet számára, hogy a tábla adatainak kezelése. 

Az SQL Database meg:

* A munkafolyamat egy új ügyfél ügyfelek adatbázishoz, vagy frissítésétől egészen a rendelések adatbázisban egy rendelés hozhat létre.
* Műveletek segítségével az adatok egy sor lekéréséhez, új sor beszúrására, és akkor is törli. Például egy rekord létrehozásakor a Dynamics CRM Online (trigger), majd sor beszúrása egy Azure SQL Database (a műveletet). 

Ez a témakör bemutatja, hogyan használható az SQL-adatbázis összekötő logikai alkalmazás, és is felsorolja azokat a műveleteket.

A Logic Apps kapcsolatos további információkért lásd: [Mik azok a logic apps](../logic-apps/logic-apps-overview.md) és [logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-azure-sql-database"></a>Csatlakozás az Azure SQL Database
A Logic Apps alkalmazást bármely szolgáltatás hozzáférni, először létre kell hoznia egy *kapcsolat* a szolgáltatáshoz. Egy kapcsolat egy logikai alkalmazást és egy másik szolgáltatás közötti kapcsolatot biztosít. SQL-adatbázishoz való kapcsolódáshoz, először hozzon létre például egy SQL-adatbázis *kapcsolat*. VPN-kapcsolat létrehozásához adja meg a hitelesítő adatokkal kell általában hozzáférhetnek a szolgáltatáshoz való kapcsolódás esetén. Igen az SQL-adatbázis, adja meg az SQL-adatbázis hitelesítő adatokat a VPN-kapcsolat létrehozásához. 

#### <a name="create-the-connection"></a>A kapcsolat létrehozása
> [!INCLUDE [Create the connection to SQL Azure](../../includes/connectors-create-api-sqlazure.md)]
> 
> 

## <a name="use-a-trigger"></a>Eseményindítók
Ez az összekötő nem rendelkezik a eseményindítókat. Más eseményindítók segítségével indítsa el a logikai alkalmazás, például az ismétlődési eseményindítót, egy HTTP Webhook eseményindító, eseményindítók érhető el a többi összekötőt, és több. [Logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md) példaként szolgál.

## <a name="use-an-action"></a>Egy művelettel
Egy művelet során a logikai alkalmazás definiált munkafolyamat által végzett. [További információ a műveletek](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Kattintson a plusz ikonra. Több beállítások megtekintéséhez: **művelet hozzáadása**, **feltétel hozzáadása**, vagy az egyik a **további** beállítások.
   
    ![](./media/connectors-create-api-sqlazure/add-action.png)
2. Válasszon **művelet hozzáadása**.
3. A szövegmezőben írja be a rendelkezésre álló műveletek listájának "sql".
   
    ![](./media/connectors-create-api-sqlazure/sql-1.png) 
4. Válassza ki a fenti példában **SQL Server - Get sor**. Ha már létezik egy kapcsolat, majd válassza ki a **táblanév** a legördülő listában, és adja meg a **Sorazonosító** szeretne visszaállítani.
   
    ![](./media/connectors-create-api-sqlazure/sample-table.png)
   
    Ha a kapcsolati adatokat kéri, adja meg a részleteket a VPN-kapcsolat létrehozásához. [A kapcsolat létrehozása](connectors-create-api-sqlazure.md#create-the-connection) a témakörben ismertetett ezeket a tulajdonságokat. 
   
   > [!NOTE]
   > Ebben a példában azt egy táblából egy sort ad vissza. A sor adatai, vegye fel egy újabb műveletet, amely létrehoz egy fájlt, a mezők a táblából. Adja hozzá például a Keresztnév és Vezetéknév mező használatával hozzon létre egy új fájlt a felhőalapú társzolgáltatás fiókja a onedrive vállalati verzió végrehajtandó. 
   > 
   > 
5. **Mentés** a módosításokat (bal felső sarkában az eszköztár). A Logic Apps alkalmazást menti, és lehet, hogy automatikusan engedélyezve.

## <a name="connector-specific-details"></a>Összekötő-specifikus részletei

Bármely eseményindítók és a swagger definiált műveletek megtekintése, és semmilyen határnak a Lásd még: a [connector részleteket](/connectors/sql/). 

## <a name="next-steps"></a>További lépések
[Logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md). Az egyéb rendelkezésre álló összekötők Logic Apps, megismerkedhet a [API-k lista](apis-list.md).

