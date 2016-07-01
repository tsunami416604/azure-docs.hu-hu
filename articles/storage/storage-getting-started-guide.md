<properties 
    pageTitle="Az Azure Storage első lépéseinek rövid összefoglalása | Microsoft Azure" 
    description="Gyorsan megismerkedhet a Microsoft Azure-blobokkal, -táblákkal és -üzenetsorokkal az Azure Storage gyors üzembe helyezés, a Visual Studio és az Azure Storage Emulator használatával. Öt percen belül lefuttathatja az első Azure Storage-alkalmazását." 
    services="storage" 
    documentationCenter=".net" 
    authors="tamram" 
    manager="carmonm" 
    editor="tysonn"/>

<tags 
    ms.service="storage" 
    ms.workload="storage" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="get-started-article" 
    ms.date="05/23/2016"
    ms.author="tamram"/>

# Az Azure Storage első lépéseinek rövid összefoglalása 

## Áttekintés

Az Azure Storage segítségével könnyen elkezdheti a fejlesztést. Az oktatóanyagból megtudhatja, hogyan készíthet gyorsan egy Azure Storage-alkalmazást. A folyamat során az Azure SDK for .NET-hez mellékelt gyors üzembe helyezési sablonokat fogja használni. A gyors üzembe helyezés futtatásra kész kódokat tartalmaz, amelyek bemutatnak néhány alapszintű programozási forgatókönyvet az Azure Storage-ban. 

Ha többet szeretne megtudni az Azure Storage-ról a kód áttekintése előtt, olvassa el a [Következő lépések](#next-steps) szakaszt.

## Előfeltételek

A kezdéshez a következő előfeltételeknek kell teljesülniük:

1. Az alkalmazás fordításához és felépítéséhez a számítógépen telepítve kell lennie a [Visual Studio](https://www.visualstudio.com/) valamelyik verziójának. 

2. Telepítse az [Azure SDK for .NET](https://azure.microsoft.com/downloads/) legfrissebb verzióját. Az SDK tartalmazza az Azure gyors üzembe helyezés mintaprojektjeit, az Azure Storage Emulatort, valamint a [.NET-hez készült Azure Storage ügyféloldali kódtárat](https://msdn.microsoft.com/library/azure/dn261237.aspx).

3. Győződjön meg arról, hogy számítógépén telepítve van a [.NET-keretrendszer 4.5](http://www.microsoft.com/download/details.aspx?id=30653), mivel az oktatóanyagban használt Azure gyors üzembe helyezési mintaprojektekhez szükség lesz rá. 

    Ha nem biztos benne, hogy a .NET-keretrendszer mely verziója van telepítve a számítógépén, lásd: [How to: Determine Which .NET Framework Versions Are Installed](https://msdn.microsoft.com/vstudio/hh925568.aspx) (Útmutató: A .NET-keretrendszer telepített verzióinak megállapítása). A **Start** gomb vagy a Windows billentyű lenyomása után írja be a következőt: **Vezérlőpult**. Ezután kattintson a **Programok** > **Programok és szolgáltatások** elemre, és ellenőrizze, hogy a .NET-keretrendszer 4.5-ös verziója szerepel-e a telepített programok között.

4. Szüksége lesz egy Azure-előfizetésre és egy Azure-tárfiókra.

    - Az Azure-előfizetés beszerzéséhez keresse fel az [Ingyenes próbaverzió](https://azure.microsoft.com/pricing/free-trial/), a [Vásárlási lehetőségek](https://azure.microsoft.com/pricing/purchase-options/) és az MSDN, a Microsoft Partner Network, a BizSpark és egyéb Microsoft programok tagjai számára elérhető [Ajánlatok tagoknak](https://azure.microsoft.com/pricing/member-offers/) webhelyeket.
    - Az Azure Storage-fiók létrehozásáról lásd: [How to create a storage account](storage-create-storage-account.md#create-a-storage-account) (Tárfiók létrehozása).

## Az első Azure Storage-alkalmazás futtatása a felhőalapú Azure Storage-ban

Ha már van fiókja, létrehozhat egy egyszerű Azure Storage-alkalmazást az egyik Azure gyors üzembe helyezési mintaprojekttel a Visual Studióban. Ez az oktatóanyag az Azure Storage-hoz készült mintaprojektekre koncentrál: **Azure Storage: Blobs** (Azure Storage: Blobok), **Azure Storage: Files** (Azure Storage: Fájlok), **Azure Storage: Queues** (Azure Storage: Üzenetsorok) és **Azure Storage: Tables** (Azure Storage: Táblák):

1. Indítsa el a Visual Studiót.
2. Kattintson a **Fájl** az **Új projekt** elemre.
3. Az **Új projekt** párbeszédpanelen kattintson a **Telepített** > **sablonok** > **Visual C#** > **Felhő** > **QuickStarts** (Gyors üzembe helyezések) > **Data Services** (Adatszolgáltatások) elemre.
    a. Válasszon egyet a következő sablonok közül: **Azure Storage: Blobs** (Azure Storage: Blobok), **Azure Storage: Files** (Azure Storage: Fájlok), **Azure Storage: Queues** (Azure Storage: Üzenetsorok) és **Azure Storage: Tables** (Azure Storage: Táblák).
    b. Győződjön meg arról, hogy a **.NET-keretrendszer 4.5** van kiválasztva célkeretrendszerként.
    - 3.c. Adjon nevet a projektnek, majd az alábbiak szerint hozza létre az új Visual Studio-megoldást:
    
    ![Azure gyors üzembe helyezések][Image1]

Az alkalmazás futtatása előtt ajánlott felülvizsgálni a forráskódot. A kód felülvizsgálatához válassza a **Nézet** menü **Megoldáskezelő** elemét a Visual Studióban. Ezután kattintson duplán a Program.cs fájlra. 

A következő lépésben futtassa a mintaalkalmazást:

1.  Válassza a **Nézet** menü **Megoldáskezelő** elemét a Visual Studióban. Nyissa meg az App.config fájlt, és tegye megjegyzésbe az Azure Storage Emulator kapcsolati karakterláncát:

    `<!--<add key="StorageConnectionString" value = "UseDevelopmentStorage=true;"/>-->`

2.  Állítsa vissza az Azure Storage szolgáltatás kapcsolati karakterláncát, és adja meg az App.config fájlban a tárfiók nevét és hívóbetűjét: `<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]"`

    A tárfiók elérési kulcsának lekéréséhez lásd: [Manage your storage access keys](storage-create-storage-account.md#manage-your-storage-access-keys) (A tárfiókok hívóbetűinek kezelése).

3.  Miután az App.config fájlban megadta a tárfiók nevét és hívóbetűjét, a **Fájl** menüben kattintson **Az összes mentése** elemre az összes projektfájl mentéséhez.
4.  A **Build** menüben kattintson a **Megoldás fordítása** elemre.
5.  A **Hibakeresés** menüben a megoldás lépésről lépésre való futtatásához nyomja le az **F11**, vagy a teljes megoldás futtatásához az **F5** billentyűt.


## Az első Azure Storage-alkalmazás helyi futtatása az Azure Storage Emulatorban

Az [Azure Storage Emulator](storage-use-emulator.md) helyi környezetet biztosít, amely az Azure Blob, Queue és Table szolgáltatásait emulálja a fejlesztéshez. A Storage Emulatorral helyben tesztelheti Storage-alkalmazását Azure-előfizetés vagy tárfiók létrehozása, illetve bármilyen felmerülő költség nélkül.

A kipróbáláshoz hozzon létre egy egyszerű Azure Storage-alkalmazást az egyik Azure gyors üzembe helyezési mintaprojekttel a Visual Studióban. Ez az oktatóanyag az **Azure Blob Storage**, **Azure Table Storage** és az **Azure Queue Storage** szolgáltatásokra koncentrál:

1. Indítsa el a Visual Studiót.
2. Kattintson a **Fájl** az **Új projekt** elemre.
3. Az **Új projekt** párbeszédpanelen kattintson a **Telepített** > **sablonok** > **Visual C#** > **Felhő** > **QuickStarts** (Gyors üzembe helyezések) > **Data Services** (Adatszolgáltatások) elemre.
   a. Válasszon egyet a következő sablonok közül: **Azure Storage: Blobs** (Azure Storage: Blobok), **Azure Storage: Files** (Azure Storage: Fájlok), **Azure Storage: Queues** (Azure Storage: Üzenetsorok) és **Azure Storage: Tables** (Azure Storage: Táblák).
   b. Győződjön meg arról, hogy a **.NET-keretrendszer 4.5** van kiválasztva célkeretrendszerként.   
    c. Adjon nevet a projektnek, majd az alábbiak szerint hozza létre az új Visual Studio-megoldást:
    
    ![Azure gyors üzembe helyezések][Image1]

4.  Válassza a **Nézet** menü **Megoldáskezelő** elemét a Visual Studióban. Nyissa meg az App.config fájlt, és ha már felvett egy Azure-tárfiókot, akkor tegye megjegyzésbe a kapcsolati karakterláncát. Ezután állítsa vissza az Azure Storage Emulator kapcsolati karakterláncát:

    `<add key="StorageConnectionString" value = "UseDevelopmentStorage=true;"/>`

Az alkalmazás futtatása előtt ajánlott felülvizsgálni a forráskódot. A kód felülvizsgálatához válassza a **Nézet** menü **Megoldáskezelő** elemét a Visual Studióban. Ezután kattintson duplán a Program.cs fájlra. 

Következő lépésben futtassa a mintaalkalmazást az Azure Storage Emulatorban:

1.  A **Start** gomb vagy a Windows billentyű lenyomása után keressen rá a *Microsoft Azure Storage emulator* kifejezésre, majd indítsa el az alkalmazást. Az emulátor elindulása után a Windows Feladatnézet területén megjelenik egy ikon és egy értesítés.
2.  A Visual Studio **Build** menüjében kattintson a **Megoldás fordítása** elemre. 
3.  A **Hibakeresés** menüben a megoldás lépésről lépésre való futtatásához nyomja le az **F11**, vagy a teljes megoldás futtatásához az **F5** billentyűt.

## További lépések

Az alábbi forrásokból többet is megtudhat az Azure Storage-ról:

* [A Microsoft Azure Storage bemutatása](storage-introduction.md)
* [Get started with Azure Blob Storage using .NET (Az Azure Blob Storage használatának első lépései a .NET-keretrendszerrel)](storage-dotnet-how-to-use-blobs.md)
* [Az Azure Table Storage használatának első lépései a .NET-keretrendszerrel](storage-dotnet-how-to-use-tables.md)
* [Az Azure Queue Storage használatának első lépései a .NET-keretrendszerrel](storage-dotnet-how-to-use-queues.md)
* [Get started with Azure File storage on Windows (Ismerkedés a Windowshoz készült Azure File Storage szolgáltatással)](storage-dotnet-how-to-use-files.md)
* [Transfer data with the AzCopy Command-Line Utility (Adatátvitel az AzCopy parancssori segédprogrammal)](storage-use-azcopy.md)
* [Azure Storage-dokumentáció](https://azure.microsoft.com/documentation/services/storage/)
* [A Microsoft Azure Storage ügyféloldali kódtára a .NET-hez](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [Az Azure Storage szolgáltatások REST API-ja](https://msdn.microsoft.com/library/azure/dd179355.aspx)

[1. kép]: ./media/storage-getting-started-guide/QuickStart.png
 



<!--HONumber=Jun16_HO2--->


