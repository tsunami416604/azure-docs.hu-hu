---
title: FTP-kiszolgálóhoz - Azure Logic Apps csatlakoztatása |} A Microsoft Docs
description: Létrehozása, figyelése és kezelése az Azure Logic Apps egy FTP-kiszolgálón található fájlok
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 07/22/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 4355a767d2ecd500662cdf4522e8a7e12de86b80
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/06/2018
ms.locfileid: "37866151"
---
# <a name="get-started-with-the-ftp-connector"></a>Az FTP-összekötő használatának első lépései
Az FTP-összekötő használatával figyelése, kezelése, és hozzon létre egy FTP-kiszolgálón található fájlokat. 

Használandó [összekötőket](apis-list.md), először hozzon létre egy logikai alkalmazást. Úgy kezdheti [egy logikai alkalmazás most már létrehozásának](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-ftp"></a>FTP-csatlakozás
A logikai alkalmazás bármely szolgáltatási férhet hozzá, akkor először kell hoznia egy *kapcsolat* a szolgáltatáshoz. A [kapcsolat](connectors-overview.md) kapcsolatot biztosít a logikai alkalmazás és a egy másik szolgáltatás között.  

### <a name="create-a-connection-to-ftp"></a>FTP-kapcsolat létrehozása
> [!INCLUDE [Steps to create a connection to FTP](../../includes/connectors-create-api-ftp.md)]
> 
> 

## <a name="use-a-ftp-trigger"></a>FTP-triggert
Egy trigger egy eseményt, amely a logikai alkalmazásban definiált munkafolyamat elindításához használható. [További tudnivalók a triggerek](../logic-apps/logic-apps-overview.md#logic-app-concepts).  

> [!IMPORTANT]
> Az FTP-összekötőnek szüksége van egy FTP-kiszolgálóra, amely az interneten, és passzív módban való működésre van konfigurálva. Az FTP-összekötő is **implicit FTPS (SSL feletti FTP) nem kompatibilis**. Az FTP-összekötő csak explicit FTPS (FTP szolgáltatás SSL-en keresztül) támogatja.  
> 
> 

Ebben a példában bemutatom majd, hogyan használható a **FTP - amikor egy fájl hozzáadásakor és módosításakor** eseményindítót, hogy a logikai alkalmazás munkafolyamatának kezdeményezhet, amikor egy fájl felvétele vagy módosítása az FTP-kiszolgálóhoz. A vállalati például használhatja erre az eseményindítóra egy FTP-mappába, az ügyfelektől származó rendelések képviselő új fájlok figyelésére.  Egy FTP-összekötő műveletet aztán használhatja például **fájl tartalmának beolvasása** a rendelés további feldolgozás és a tároló tartalmának beolvasása a a rendelési adatbázisban.

1. Adja meg *ftp* a keresőmezőbe írja be a logic apps Designerben kattintson a **FTP - amikor egy fájl hozzáadásakor és módosításakor** eseményindító   
   ![FTP-eseményindító lemezkép 1](./media/connectors-create-api-ftp/ftp-trigger-1.png)  
   A **amikor felvesznek vagy módosítanak egy fájlt** vezérlő nyit meg  
   ![FTP-eseményindító kép 2](./media/connectors-create-api-ftp/ftp-trigger-2.png)  
2. Válassza ki a **...**  a vezérlő jobb oldalán található. Ekkor megnyílik a mappa Dátumválasztó vezérlőelem  
   ![FTP-eseményindító kép 3](./media/connectors-create-api-ftp/ftp-trigger-3.png)  
3. Válassza ki a **>** (jobbra mutató nyíl), és keresse meg az új vagy módosított fájlok figyelni kívánt mappát. Válassza ki a mappát és a mappa megjelenik a figyelmeztetés a **mappa** vezérlő.  
   ![FTP-eseményindító lemezkép 4](./media/connectors-create-api-ftp/ftp-trigger-4.png)   

Ezen a ponton a logikai alkalmazás egy eseményindítóval, amely más eseményindítók és műveletek a munkafolyamat futtatását akkor kezdődik, amikor egy fájlt módosító vagy az adott FTP-mappába van konfigurálva. 

> [!NOTE]
> A logikai alkalmazás megfelelő működéséhez tartalmaznia kell legalább egy triggert és a egy műveletet. Kövesse a következő szakaszban vegyen fel egy műveletet.  
> 
> 

## <a name="use-a-ftp-action"></a>Egy FTP-művelet használata
Művelet definiálva, a logikai alkalmazás a munkafolyamat által végzett művelet. [További információért azokról a műveletekről](../logic-apps/logic-apps-overview.md#logic-app-concepts).  

Most, hogy hozzáadott egy eseményindítót, hajtsa végre a következő lépésekkel adhatja hozzá egy műveletet, amely megkapja az eseményindító által észlelt az új vagy módosított fájl tartalmát.    

1. Válassza ki **+ új lépés** hozzáadása a műveletet az FTP-kiszolgálón a fájl tartalmának beolvasása  
2. Válassza ki a **művelet hozzáadása** hivatkozásra.  
   ![FTP-művelet lemezkép 1](./media/connectors-create-api-ftp/ftp-action-1.png)  
3. Adja meg *FTP* az FTP-kapcsolódó összes művelet.
4. Válassza ki **FTP - fájl tartalmának beolvasása** , a végrehajtandó műveletet, amikor egy új vagy módosított fájl FTP mappában található.      
   ![FTP-művelet kép 2](./media/connectors-create-api-ftp/ftp-action-2.png)  
   A **fájl tartalmának beolvasása** megnyílik szabályozhatja. **Megjegyzés:**: meg kell adnia az FTP-kiszolgáló fiók elérésére, ha még nem meg korábban a logikai alkalmazás engedélyezése.  
   ![FTP-művelet kép 3](./media/connectors-create-api-ftp/ftp-action-3.png)   
5. Válassza ki a **fájl** vezérlő (a fehér terület alatt található ** fájl x). Itt is használhatja a különböző tulajdonságok valamelyikét, az új vagy módosított fájl FTP-kiszolgálón található.  
6. Válassza ki a **fájl tartalma** lehetőséget.  
   ![FTP-művelet lemezkép 4](./media/connectors-create-api-ftp/ftp-action-4.png)   
7. A vezérlő frissült, amely jelzi, hogy a **FTP - fájl tartalmának beolvasása** művelet fog kapni a *fájl tartalma* az új vagy módosított fájl FTP-kiszolgálón.      
   ![FTP-művelet kép 5](./media/connectors-create-api-ftp/ftp-action-5.png)     
8. Mentse a munkáját, majd adjon hozzá egy fájlt az FTP-mappába, a munkafolyamat tesztelése.    

Ezen a ponton a logikai alkalmazás egy eseményindítóval figyelheti az FTP-kiszolgáló egyik mappájába, és indítja a munkafolyamatot, ha úgy találja, vagy egy új fájlt, vagy egy módosított fájlt FTP-kiszolgálón van konfigurálva. 

A logikai alkalmazás is van konfigurálva, az új vagy módosított fájl tartalmának beolvasása művelettel.

Például egy másik művelet most hozzáadhatja a [SQL Server - sor beszúrása](connectors-create-api-sqlazure.md) művelet beszúrása az új vagy módosított fájl tartalmát egy SQL database-táblába.  

## <a name="connector-specific-details"></a>Összekötő-specifikus részletei

Megtekintheti a valamennyi eseményindítót és műveletet a swaggerben meghatározott, és emellett a korlátozott a [összekötő részletei](/connectors/ftpconnector/). 

## <a name="next-steps"></a>További lépések
[Logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)

