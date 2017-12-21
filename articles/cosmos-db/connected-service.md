---
title: "A Visual Studio csatlakoztatott szolgáltatása az Azure Cosmos DB-hez"
description: "Lehetővé teszi a fejlesztők számára az Azure Cosmos DB-fiókjuk egyszerű csatlakoztatását és az erőforrások kezelését a Visual Studio csatlakoztatott szolgáltatásain keresztül"
services: cosmos-db
documentationcenter: 
author: jejiang
manager: DJ
+tags: cosmos-db
editor: Jenny Jiang
ms.assetid: 
ms.service: cosmos-db
ms.custom: Azure Cosmos DB
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 09/19/2017
ms.author: jejiang
ms.openlocfilehash: f63c20831a2700f1024507bef614a5a7cc87d00e
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2017
---
# <a name="azure-cosmos-db-visual-studio-connected-service-preview"></a>Azure Cosmos DB: a Visual Studio csatlakoztatott szolgáltatása (előzetes verzió)

A Visual Studio csatlakoztatott szolgáltatásai lehetővé teszik a fejlesztők számára az Azure Cosmos DB-fiókjuk egyszerű csatlakoztatását és az erőforrások kezelését.

A csatlakoztatott szolgáltatásban az Adatkezelővel tárolt eljárásokat, felhasználói függvényeket és a kiszolgálóoldali üzleti logikákat végrehajtó eseményindítókat is létrehozhat. Az Adatkezelő hozzáférhetővé teszi az API-k összes beépített, programozható adatelérési funkcióját, miközben az adataihoz is egyszerű hozzáférést biztosít.

## <a name="prerequisites"></a>Előfeltételek

Győződjön meg róla, hogy rendelkezik az alábbi elemekkel:

* Aktív Azure-fiók. Ha még nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/). 
* Azure Cosmos DB-fiók. Ha még nem rendelkezik ilyennel, kövesse az [Azure Cosmos DB-fiók létrehozása](create-sql-api-dotnet.md) című témakör lépéseit, és hozzon létre egyet az Azure Portalon, vagy tekintse meg az [Azure Cosmos DB-fiók csatlakoztatott szolgáltatás eszközben való létrehozását ismertető cikket](#Create-an-Azure-Cosmo-DB-account-in-Connected-Service-tool). 
* Ha helyi környezetet szeretne használni fejlesztési célokra, használhatja az [Azure Cosmos DB Emulatort](local-emulator.md). A környezet emulálja az Azure Cosmos DB szolgáltatást.
* [Visual Studio](http://www.visualstudio.com/).
* Az Azure Cosmos DB csatlakoztatott szolgáltatás legújabb elemei. Az Azure Cosmos DB csatlakoztatott szolgáltatást az alábbi képernyőfelvételen láthatók szerint töltheti le a Visual Studio piacteréről. Nyissa meg a **Visual Studiót** a számítógépén. A **Tools** (Eszközök) menüben válassza az **Extensions and update...** (Bővítmények és frissítés) lehetőséget, majd válassza az **Online** / **Visual Studio Marketplace** (Visual Studio-piactér) elemet. Írja be a **cosmosdb** szöveget az eszköz megkereséséhez.

    Az Azure Cosmos DB csatlakoztatott szolgáltatást a [Visual Studio piacteréről](https://go.microsoft.com/fwlink/?linkid=858709) is telepítheti.

    ![A csatlakoztatott szolgáltatások letöltésének képernyőképe.png](./media/connected-service/connected-service-downloadbits.png) 

## <a id="SetupVS"></a>A Visual Studio-megoldás beállítása
1. Nyissa meg a **Visual Studiót** a számítógépén.
2. A **Fájl** menüben válassza az **Új**, majd a **Projekt** elemet.
3. A **New Project** (Új projekt) párbeszédpanelen válassza a **Visual C#** / **Console App (.NET Framework)** (Konzolalkalmazás (.NET-keretrendszer)) vagy a **WPF App (.NET Framework)** (WPF alkalmazás (.NET-keretrendszer)) elemet, nevezze el a projektet, majd kattintson az **OK** gombra.

    ![Képernyőfelvétel az Új projekt ablakról](./media/connected-service/connected-service-new-project.png)
    
## <a name="add-connected-service-and-add-account"></a>Csatlakoztatott szolgáltatás hozzáadása és fiók hozzáadása
1. A Solution Explorer (Megoldáskezelő) felületén kattintson a jobb gombbal a projektcsomópontra, majd válassza a **Add** (Hozzáadás) / **Connected Service** (Csatlakoztatott szolgáltatás) lehetőséget. Vagy kattintson a **Project** (Projekt) menüre, majd válassza az **Add Connected Service** (Csatlakoztatott szolgáltatás hozzáadása) lehetőséget.

    ![Az Add Connected Service (Csatlakoztatott szolgáltatás hozzáadása) ablak képernyőképe](./media/connected-service/connected-service-add-connectedservice-rightclick.png)
2. A csatlakoztatott szolgáltatás oldalán kattintson a **Connected Services** (Csatlakoztatott szolgáltatások) / **Azure Cosmos DB** elemre az **Azure Cosmos DB** oldal megnyitásához.

    ![Képernyőkép az Azure Cosmos DB ablakról](./media/connected-service/connected-service-choose-azure-cosmosdb.png)
3. Kattintson a lefelé mutató nyílra az első alkalommal történő bejelentkezéshez, vagy adjon hozzá egy fiókot. A bejelentkezés után az összes Azure Cosmos DB-fiók megjelenik az üres területen. Válasszon ki egy Azure Cosmos DB-fiókot, amelyet hozzá kíván adni a projekthez.

    ![Képernyőkép a bejelentkezési és a DB-fiókokat felsoroló ablakról](./media/connected-service/connected-service-add-db-account.png)
4. Miután hozzáadott egy Azure Cosmos DB-fiókot, a rendszer hozzáadja a projekthez az Azure Cosmos DB-fiókhoz csatlakoztatott szolgáltatás mappáját. Az 1–3. lépések megismétlésével további Azure Cosmos DB-fiókokat adhat hozzá.

    ![A csatlakoztatott szolgáltatás mappáját megjelenítő ablak képernyőképe](./media/connected-service/connected-service-add-connectedservice-folder.png)

5. Miután hozzáadta az Azure Cosmos DB csatlakoztatott szolgáltatást, a következő módszerek valamelyikével módosítsa a projektet, hogy engedélyezze a hozzáférést az Azure Cosmos DB-hez:

    * Az Azure Cosmos DB-ügyfél számára szükséges NuGet-csomagok telepítése. Ezeket a csomagok konfigurációs fájljában tekintheti meg. 

        ![Az új Azure Cosmos DB packages.config fájl](./media/connected-service/connected-service-packages-config.png)   
    
    * Az Azure Cosmos DB kapcsolati URI és kulcs hozzáadása a projekt konfigurációs fájljához, ebben az esetben az App.config fájlhoz. 

        ![Az új Azure Cosmos DB app.config fájl](./media/connected-service/connected-service-app-config.png) 

## <a name="open-azure-cosmos-db-explorer"></a>Az Azure Cosmos DB-kezelő megnyitása
1. Kattintson a jobb gombbal a projektcsomópontra, majd válassza az **Open Cosmos DB Explorer...** (A Cosmos DB-kezelő megnyitása...) lehetőséget.

    ![Képernyőfelvétel az Adatkezelő ablakának megnyitásáról](./media/connected-service/connected-service-right-click-open-data-exporer.png)
2. A **Choose a Cosmos DB Account** (Cosmos DB-fiók kiválasztása) oldalon kattintson a legördülő listára egy Azure Cosmos DB-fiók kiválasztásához.

    ![Az Added Account Connected Service (Hozzáadott fiókhoz csatlakoztatott szolgáltatás) ablak képernyőképe](./media/connected-service/connected-service-open-explorer.png)
3. Kattintson az **Open** (Megnyitás) elemre; ekkor megjelenik az adatkezelő ablaka.

## <a id="Create-an-Azure-Cosmo-DB-account-in-Connected-Service-tool"></a>Azure Cosmos DB-fiók létrehozása a csatlakoztatott szolgáltatás eszközben
1. A csatlakoztatott szolgáltatás oldalon az ablaktábla bal alsó részén kattintson a **Create a New Cosmos DB Account** (Új Cosmos DB-fiók létrehozása) elemre a **Create Cosmos DB Account** (Cosmos DB-fiók létrehozása) oldal megnyitásához.

    ![Képernyőkép a megnyitott Azure Cosmos DB-fiók létrehozása ablak beviteli pontjáról](./media/connected-service/connected-service-click-new-db-account.png)
2. A **Create Cosmos DB Account** (Cosmos DB-fiók létrehozása) oldalon adja meg az Azure Cosmos DB-fiók kívánt beállításait.

    Töltse ki a **Create Cosmos DB Account** (Cosmos DB-fiók létrehozása) oldalon található mezőket a következőképen látható információk alapján. 
 
    ![Az új Azure Cosmos DB oldal](./media/connected-service/connected-service-create-new-account.png)        
3. A fiók létrehozásához kattintson a **Létrehozás** gombra.

## <a name="use-data-explorer"></a>Az Adatkezelő használata

Az Adatkezelő megnyitása után a következőket teheti:
* Adatbázisok létrehozása és törlése;
* Gyűjtemények létrehozása és törlése;
* Dokumentumok létrehozása, törlése és szűrése;
* Tárolt eljárások létrehozása és törlése;
* A kiszolgálóoldali üzleti logika végrehajtására szolgáló eseményindítók és felhasználó által definiált függvények létrehozása és törlése. 

![Az új Azure Cosmos DB oldal](./media/connected-service/connected-service-dataexplorerui.png)

## <a name="demo"></a>Bemutató

Tekintse meg a következő videót az Azure Cosmos DB csatlakoztatott szolgáltatás Visual Studióban való használatáról: [Use Azure Cosmos DB Connected Service in Visual Studio](https://go.microsoft.com/fwlink/?linkid=858711) (Az Azure Cosmos DB csatlakoztatott szolgáltatás használata a Visual Studióban)

## <a name="next-steps"></a>Következő lépések
Ebben a dokumentumban a következőket mutattuk be:

> [!div class="checklist"]
> * Azure Cosmos DB-fiók létrehozása
> * Csatlakoztatott szolgáltatás hozzáadása és fiók hozzáadása
> * Az Azure Cosmos DB-kezelő megnyitása
> * Az Adatkezelő használata

Most, hogy a Csatlakoztatott szolgáltatás működik az Azure Cosmos DB-fiókjával, folytassa a következő oktatóanyagok egyikével, és kezdje el fejleszteni saját megoldását:

* [Fejlesztés az SQL API-val .NET-keretrendszerben](tutorial-develop-sql-api-dotnet.md).
* [Azure Cosmos DB: Az SQL API kezdeti lépéseit ismertető oktatóanyag](sql-api-get-started.md).
* Méret- és teljesítménytesztelést szeretne végezni az Azure Cosmos DB használatával? Tekintse meg a következőt: [Teljesítmény- és mérettesztelés az Azure Cosmos DB használatával](performance-testing.md).
* Ismerje meg, hogyan [monitorozhat egy Azure Cosmos DB-fiókot](monitor-accounts.md).

