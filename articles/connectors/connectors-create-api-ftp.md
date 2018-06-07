---
title: FTP-kiszolgáló - Azure Logic Apps csatlakozni |} Microsoft Docs
description: Hozzon létre, figyeléséhez és az FTP-kiszolgálón az Azure Logic Apps fájlok kezelése
author: ecfan
manager: cfowler
ms.author: estfan
ms.date: 07/22/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 3bb8c750846ee14331c749aeece4315d5c16d411
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34610565"
---
# <a name="get-started-with-the-ftp-connector"></a>Az FTP-összekötő az első lépései
Az FTP-összekötő segítségével figyeléséhez, kezeléséhez és az FTP-kiszolgálón-fájlok létrehozása. 

Használandó [a csatlakozókat](apis-list.md), először hozzon létre egy logikai alkalmazást. Elkezdheti által [logikai alkalmazás létrehozása most](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-ftp"></a>FTP-csatlakozás
A Logic Apps alkalmazást bármely szolgáltatás hozzáférni, először hozzon létre egy *kapcsolat* a szolgáltatáshoz. A [kapcsolat](connectors-overview.md) biztosít a logikai alkalmazás és egy másik szolgáltatás közötti kapcsolat.  

### <a name="create-a-connection-to-ftp"></a>FTP-kapcsolat létrehozása
> [!INCLUDE [Steps to create a connection to FTP](../../includes/connectors-create-api-ftp.md)]
> 
> 

## <a name="use-a-ftp-trigger"></a>FTP-eseményindítók
Egy eseményindító nem egy eseményt, a logikai alkalmazás definiált munkafolyamat indításához használható. [További tudnivalók az eseményindítók](../logic-apps/logic-apps-overview.md#logic-app-concepts).  

> [!IMPORTANT]
> Az FTP-összekötőnek szüksége van egy FTP-kiszolgálót, amely elérhető az internetről és passzív módban való működésre van konfigurálva. Az FTP-összekötő is **implicit ftps-t (FTP szolgáltatás SSL-en keresztül) nem kompatibilis**. Az FTP-összekötő csak explicit ftps-t (FTP szolgáltatás SSL-en keresztül) támogatja.  
> 
> 

Ebben a példában I bemutatja, hogyan használható a **FTP - amikor egy fájl hozzáadása vagy módosítása** eseményindítót, hogy kezdeményezhet a logic app munkafolyamat, amikor egy fájl hozzá vagy módosítja az FTP-kiszolgálóhoz. A vállalati például ehhez az eseményindítóhoz segítségével egy FTP-mappa az új fájlok, amelyek megfelelnek a rendeléseket ügyfelek figyelése.  Az FTP-összekötő művelet aztán használhatja például a **fájl tartalmának lekérdezése** az rendelések adatbázisban a ahhoz, hogy további feldolgozás és a tároló tartalmának eléréséhez.

1. Adja meg *ftp* be a keresőmezőbe a logic apps designer válassza ki a **FTP - amikor egy fájl hozzáadása vagy módosítása** eseményindító   
   ![FTP eseményindító kép 1](./media/connectors-create-api-ftp/ftp-trigger-1.png)  
   A **amikor egy fájl hozzáadása vagy módosítása esetén** vezérlő megnyílik.  
   ![FTP eseményindító kép 2](./media/connectors-create-api-ftp/ftp-trigger-2.png)  
2. Válassza ki a **...**  a vezérlő jobb oldalán található. Ekkor megnyílik a mappa példányválasztó vezérlő  
   ![FTP eseményindító kép 3](./media/connectors-create-api-ftp/ftp-trigger-3.png)  
3. Válassza ki a **>** (jobbra), és keresse meg a mappát, amely új vagy módosított fájlokat szeretné figyelni. Válassza ki a mappát és a mappa megjelenik a figyelmeztetés a **mappa** vezérlő.  
   ![FTP eseményindító kép 4](./media/connectors-create-api-ftp/ftp-trigger-4.png)   

A Logic Apps alkalmazást ezen a ponton úgy van konfigurálva, az eseményindítók és műveletek a munkafolyamat futtató akkor kezdődik, amikor egy fájl módosított, vagy az adott FTP mappában létrehozott eseményindítót. 

> [!NOTE]
> A logikai alkalmazás működéséhez legalább egy eseményindító és egy műveletet kell tartalmaznia. Kövesse a következő szakaszban művelet hozzáadása.  
> 
> 

## <a name="use-a-ftp-action"></a>Egy FTP művelettel
Egy művelet során a logikai alkalmazás definiált munkafolyamat által végzett. [További információ a műveletek](../logic-apps/logic-apps-overview.md#logic-app-concepts).  

Most, hogy hozzáadta egy eseményindítót, kövesse az alábbi lépéseket, amely megkapja a tartalmát az új vagy módosított fájl található az eseményindító által művelet hozzáadása.    

1. Válassza ki **+ új lépés** hozzáadni a a műveletet az FTP-kiszolgálón a fájl tartalmának beolvasása  
2. Válassza ki a **művelet hozzáadása** hivatkozásra.  
   ![FTP-művelet kép 1](./media/connectors-create-api-ftp/ftp-action-1.png)  
3. Adja meg *FTP* FTP kapcsolatos összes műveletet kereséséhez.
4. Válassza ki **FTP - fájl tartalmának lekérdezése** , a végrehajtandó műveletet, amikor egy új vagy módosított fájl FTP mappában található.      
   ![FTP-művelet kép 2](./media/connectors-create-api-ftp/ftp-action-2.png)  
   A **fájl tartalmának lekérdezése** megnyílik szabályozzák. **Megjegyzés:**: kérni fogja a Logic Apps alkalmazást az FTP-kiszolgáló fiók eléréséhez, ha még nem meg korábban engedélyezésére.  
   ![FTP-művelet kép 3](./media/connectors-create-api-ftp/ftp-action-3.png)   
5. Válassza ki a **fájl** vezérlő (az üres helyet alatt található ** fájl x). Itt használhatja a különböző tulajdonságok valamelyikét a következő új vagy módosított fájl megtalálható-e az FTP-kiszolgáló.  
6. Válassza ki a **tartalom fájl** lehetőséget.  
   ![FTP-művelet kép 4](./media/connectors-create-api-ftp/ftp-action-4.png)   
7. A vezérlő frissül, amely jelzi, hogy a **FTP - fájl tartalmának lekérdezése** művelet kap a *tartalom fájl* az új vagy módosított fájl az FTP-kiszolgálón.      
   ![FTP-művelet kép 5](./media/connectors-create-api-ftp/ftp-action-5.png)     
8. Mentse a munkáját, majd adjon hozzá egy fájlt a munkafolyamat tesztelése az FTP-mappa.    

A logikai alkalmazást ezen a ponton úgy van konfigurálva, az FTP-kiszolgálón mappa megfigyelése és indítja a munkafolyamatot, ha egy új fájl vagy az FTP-kiszolgálón a módosított fájlt talál eseményindító. 

A logikai alkalmazás is van konfigurálva a művelet az új vagy módosított fájl tartalmának eléréséhez.

Mostantól hozzáadhatja azok egy másik művelet, mint a [SQL Server - sor beszúrása](connectors-create-api-sqlazure.md) lehet beszúrni az új vagy módosított fájl tartalmának az SQL-adatbázistáblában szereplő művelet.  

## <a name="connector-specific-details"></a>Összekötő-specifikus részletei

Bármely eseményindítók és a swagger definiált műveletek megtekintése, és semmilyen határnak a Lásd még: a [connector részleteket](/connectors/ftpconnector/). 

## <a name="next-steps"></a>További lépések
[Logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)

