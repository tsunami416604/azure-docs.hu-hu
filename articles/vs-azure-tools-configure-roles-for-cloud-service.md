---
title: A szerepkörök az Azure-felhőszolgáltatás konfigurálása a Visual Studio |} Microsoft Docs
description: Megtudhatja, hogyan beállítása és konfigurálása a szerepkörök az Azure felhőszolgáltatások Visual Studio használatával.
services: visual-studio-online
documentationcenter: na
author: ghogen
manager: douge
editor: ''
ms.assetid: d397ef87-64e5-401a-aad5-7f83f1022e16
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 03/21/2017
ms.author: ghogen
ms.openlocfilehash: 8a1eb75e9f38aae29cbb0012e5f15404299d011d
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="configure-azure-cloud-service-roles-with-visual-studio"></a>A Visual Studio Azure cloud service szerepkörök konfigurálása
Azure-felhőszolgáltatás rendelkezhet egy vagy több munkavégző vagy a webes szerepkörök. Az egyes szerepkörökhöz szükség határozza meg, akkor az a szerepkör beállítására és is konfigurálhatja, hogyan fut a szerepkörhöz. A felhőszolgáltatások szerepkörök kapcsolatos további információkért lásd: a videó [Bevezetés az Azure Felhőszolgáltatások](https://channel9.msdn.com/Series/Windows-Azure-Cloud-Services-Tutorials/Introduction-to-Windows-Azure-Cloud-Services). 

A felhőalapú szolgáltatás adatait tárolja a következő fájlokat:

- **ServiceDefinition.csdef** -a szolgáltatásdefiníciós fájlban a milyen szerepkörre szükség, beleértve a felhőalapú szolgáltatás, a végpontok és a virtuális gép méretét a futásidejű beállításokat határoz meg. A tárolt adatokat `ServiceDefinition.csdef` módosítható, ha a szerepkör fut.
- **ServiceConfiguration.cscfg** – a szolgáltatás konfigurációs fájlja konfigurálja, hogy hány példánya egy futnak, és a beállítások értékeit az egy szerepkörhöz definiált. A tárolt adatok `ServiceConfiguration.cscfg` módosíthatja a szerepkör futása közben.

Eltérő értékek tartoznak a szerepkör működésével szabályozó beállítások tárolására, több szolgáltatáskonfiguráció adhat meg. Minden környezet különböző szolgáltatáskonfiguráció használható. Például beállíthatja a tárolási fiók kapcsolati karakterlánc a helyi Azure storage emulatorral helyi szolgáltatás konfigurációban, és hozzon létre egy másik szolgáltatás konfigurációját az Azure storage használata a felhőben.

Azure-felhőszolgáltatás létrehozása a Visual Studióban, ha a két szolgáltatáskonfiguráció a rendszer automatikusan létrehozott és az Azure-projekt hozzáadja:

- `ServiceConfiguration.Cloud.cscfg`
- `ServiceConfiguration.Local.cscfg`

## <a name="configure-an-azure-cloud-service"></a>Azure-felhőszolgáltatás konfigurálása
A Visual Studio megoldáskezelőjében egy Azure felhőszolgáltatást konfigurálhatja, ahogy az az alábbi lépéseket:

1. Hozzon létre, vagy a Visual Studio Azure cloud service projekt megnyitása.

1. A **Megoldáskezelőben**, kattintson jobb gombbal a projektre, és válassza ki a helyi menüből **tulajdonságok**.
   
    ![Solution Explorer projekt helyi menü](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-project-context-menu.png)

1. A projekt Tulajdonságok lapján válassza a **fejlesztési** fülre. 

    ![Projekt párbeszédpanel - fejlesztési lap](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-development-tab.png)

1. Az a **szolgáltatáskonfiguráció** listára, válassza ki a szerkeszteni kívánt szolgáltatáskonfiguráció neve. (Ha a szerepkör szolgáltatáskonfiguráció módosításokat, jelölje be **összes konfiguráció**.)
   
    > [!IMPORTANT]
    > Ha úgy dönt, hogy egy adott szolgáltatás konfigurációs, néhány tulajdonságát le vannak tiltva, mivel azok csak az összes konfiguráció állítható be. Ezek a Tulajdonságok szerkesztése, ki kell választania **összes konfiguráció**.
    > 
    > 
   
    ![Azure-felhőszolgáltatás szolgáltatáskonfiguráció listája](./media/vs-azure-tools-configure-roles-for-cloud-service/cloud-service-service-configuration-property.png)

## <a name="change-the-number-of-role-instances"></a>A szerepkörpéldányok számát
A felhőalapú szolgáltatás teljesítményének javítása érdekében módosíthatja a hány példánya egy szerepkört futtató, a felhasználók vagy a terhelés, az adott szerepkörök várt száma alapján. Egy különálló virtuális gép szerepkör minden egyes példányánál létrejön, amikor a felhőalapú szolgáltatás fut az Azure-ban. Ez hatással van a számlázás a felhőalapú szolgáltatás telepítését. Számlázással kapcsolatos további információkért lásd: [a számlázási megérteni a Microsoft Azure](billing/billing-understand-your-bill.md).

1. Hozzon létre, vagy a Visual Studio Azure cloud service projekt megnyitása.

1. A **Megoldáskezelőben**, bontsa ki a projekt csomópontjára. Az a **szerepkörök** csomópontot, kattintson a jobb gombbal a frissíteni, és a helyi menüből válassza ki a kívánt szerepkör **tulajdonságok**.

    ![Solution Explorer Azure szerepkör helyi menü](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-azure-role-context-menu.png)

1. Válassza ki a **konfigurációs** fülre.

    ![Konfiguráció lap](./media/vs-azure-tools-configure-roles-for-cloud-service/role-configuration-properties-page.png)

1. Az a **szolgáltatáskonfiguráció** listára, válassza ki a frissíteni kívánt szolgáltatás konfigurációját.
   
    ![Konfigurációs szolgáltatás listája](./media/vs-azure-tools-configure-roles-for-cloud-service/role-configuration-properties-page-select-configuration.png)

1. Az a **száma példány** szöveget adja meg, hogy el szeretné indítani a szerepkör-példányok száma. Minden példány külön virtuális gépen fut, az Azure felhőalapú szolgáltatásából közzétételekor.

    ![A példányok száma frissítése](./media/vs-azure-tools-configure-roles-for-cloud-service/role-configuration-properties-page-instance-count.png)

1. A Visual Studio eszköztárában kattintson **mentése**.

## <a name="manage-connection-strings-for-storage-accounts"></a>Kapcsolati karakterláncok storage-fiókok kezelése
Hozzáadhat, távolítsa el, vagy módosítsa a szolgáltatáskonfiguráció-kapcsolati karakterláncok. Például érdemes egy helyi kapcsolati karakterláncot egy helyi szolgáltatás konfigurációjához, amely értéke `UseDevelopmentStorage=true`. Azt is szeretné beállítani egy felhőalapú szolgáltatás konfigurációja, amely egy tárfiókot használja az Azure-ban.

> [!WARNING]
> Az Azure storage kulcs fiókadatai tárolási fiók kapcsolati karakterláncot adja meg, ezeket az információkat tárolja helyileg a szolgáltatás konfigurációs fájljában. Azonban ez az információ nem tárolt titkosított szövegként.
> 
> 

Minden szolgáltatás konfigurációját egy másik értéket használ, nincs különböző kapcsolati karakterláncok használata a felhőalapú szolgáltatás, vagy módosítsa a kódot, a felhőalapú szolgáltatás az Azure-ba való közzétételekor. A kapcsolati karakterlánc ugyanazt a nevet használhatja a kódban, és értéke nem egyezik a felhőalapú szolgáltatás építésekor, vagy amikor közzétesszük a választott szolgáltatás konfigurációja alapján.

1. Hozzon létre, vagy a Visual Studio Azure cloud service projekt megnyitása.

1. A **Megoldáskezelőben**, bontsa ki a projekt csomópontjára. Az a **szerepkörök** csomópontot, kattintson a jobb gombbal a frissíteni, és a helyi menüből válassza ki a kívánt szerepkör **tulajdonságok**.

    ![Solution Explorer Azure szerepkör helyi menü](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-azure-role-context-menu.png)

1. Válassza ki a **beállítások** fülre.

    ![Beállítások lap](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab.png)

1. Az a **szolgáltatáskonfiguráció** listára, válassza ki a frissíteni kívánt szolgáltatás konfigurációját.

    ![Service Configuration](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-select-configuration.png)

1. A kapcsolati karakterlánc hozzáadásához válassza **beállítás hozzáadása**.

    ![Kapcsolati karakterlánc hozzáadása](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-add-setting.png)

1. Miután az új beállítás hozzáadta a listához, frissítse a sort a listán a szükséges információkat.

    ![Új kapcsolati karakterlánc](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-add-setting-new-setting.png)

    - **Név** -adja meg a kapcsolati karakterlánc használni kívánt nevét.
    - **Típus** – Itt adhatja meg **kapcsolati karakterlánc** a legördülő listából.
    - **Érték** -adhatja meg a kapcsolati karakterlánc közvetlenül a **érték** cella, vagy válassza ki a munkát a három ponttal (…) a **tárolási kapcsolati karakterlánc létrehozása** párbeszédpanel.  

1. Az a **tárolási kapcsolati karakterlánc létrehozása** párbeszédpanelen válasszon egy beállítást **protokoll használatával kapcsolódó levelezőprogramokkal**. Ezután kövesse a kiválasztott beállítás:

    - **A Microsoft Azure storage emulator** – Ha ezt a lehetőséget választja, a többi beállítást a párbeszédpanelen le vannak tiltva megfelelően csak az Azure-bA. Kattintson az **OK** gombra.
    - **Az előfizetés** – Ha ezt a beállítást, a legördülő lista segítségével válassza ki, és jelentkezzen be Microsoft-fiókkal, vagy vegye fel a Microsoft-fiókkal. Válassza ki az Azure-előfizetés és a tárolási fiók. Kattintson az **OK** gombra.
    - **Manuálisan kell megadni a hitelesítő adatok** -adja meg a tárfiók nevét és a második vagy elsődleges kulcs. Válassza ki a **kapcsolat** (HTTPS ajánlott a legtöbb esetben.) Válassza ki **OK**.

1. A kapcsolati karakterlánc törléséhez válassza ki a kapcsolati karakterláncot, majd válassza ki **eltávolítása beállítás**.

1. A Visual Studio eszköztárában kattintson **mentése**.

## <a name="programmatically-access-a-connection-string"></a>A kapcsolati karakterlánc programon keresztüli eléréséhez

A következő lépések bemutatják, hogyan egy kapcsolati karakterláncot, amely C# programon keresztüli eléréséhez.

1. Adja hozzá a következő irányelvek segítségével egy C# fájlban hová beállítást használják:

    ```csharp
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.ServiceRuntime;
    ```

1. Az alábbi kód bemutatja, hogyan férhet hozzá a kapcsolati karakterláncot egy példát. Cserélje le a &lt;ConnectionStringName > helyőrzőt a megfelelő értékkel. 

    ```csharp
    // Setup the connection to Azure Storage
    var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("<ConnectionStringName>"));
    ```

## <a name="add-custom-settings-to-use-in-your-azure-cloud-service"></a>Adja hozzá az egyéni beállítások használata az Azure-felhőszolgáltatás
Egyéni beállítások a konfigurációs fájlban adja hozzá egy nevet és értéket egy adott szolgáltatás konfiguráció lehetővé teszik. Dönthet a beállítás segítségével állítsa be a szolgáltatás az a felhőalapú szolgáltatás olvasásakor a beállítás értékét, és ezt az értéket segítségével vezérelheti, a kódban a programot. A szolgáltatás konfigurációs értékeket módosíthatja anélkül, hogy a szolgáltatáscsomag vagy amikor a felhőalapú szolgáltatás fut-e. A kódot is keresése az értesítések, amikor a beállítások módosítása. Lásd: [RoleEnvironment.Changing esemény](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changing.aspx).

Is hozzáadhat, távolítsa el, vagy módosítsa a szolgáltatáskonfiguráció egyéni beállításait. Ezek a különböző szolgáltatáskonfiguráció karakterláncok érdemes eltérő értékek tartoznak.

Minden szolgáltatás konfigurációját egy másik értéket használ, nincs másik karakterláncok használata a felhőalapú szolgáltatás, vagy módosítsa a kódot, a felhőalapú szolgáltatás az Azure-ba való közzétételekor. A karakterlánc ugyanazt a nevet használhatja a kódban, és értéke nem egyezik a felhőalapú szolgáltatás építésekor, vagy amikor közzétesszük a választott szolgáltatás konfigurációja alapján.

1. Hozzon létre, vagy a Visual Studio Azure cloud service projekt megnyitása.

1. A **Megoldáskezelőben**, bontsa ki a projekt csomópontjára. Az a **szerepkörök** csomópontot, kattintson a jobb gombbal a frissíteni, és a helyi menüből válassza ki a kívánt szerepkör **tulajdonságok**.

    ![Solution Explorer Azure szerepkör helyi menü](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-azure-role-context-menu.png)

1. Válassza ki a **beállítások** fülre.

    ![Beállítások lap](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab.png)

1. Az a **szolgáltatáskonfiguráció** listára, válassza ki a frissíteni kívánt szolgáltatás konfigurációját.

    ![Konfigurációs szolgáltatás listája](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-select-configuration.png)

1. Egyéni beállítás hozzáadásához válassza **beállítás hozzáadása**.

    ![Egyéni Alkalmazásbeállítás hozzáadása](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-add-setting.png)

1. Miután az új beállítás hozzáadta a listához, frissítse a sort a listán a szükséges információkat.

    ![Új egyéni beállítása](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-add-setting-new-setting.png)

    - **Név** -adja meg a beállítás nevét.
    - **Típus** – Itt adhatja meg **karakterlánc** a legördülő listából.
    - **Érték** -adja meg a beállítás értékét. Megadhat közvetlenül be értéket a **érték** cella, vagy jelölje ki a három ponttal (…) a értékek a **karakterlánc szerkesztése** párbeszédpanel.  

1. Törölni egy egyéni beállítást, a beállítás, majd válassza ki és **eltávolítása beállítás**.

1. A Visual Studio eszköztárában kattintson **mentése**.

## <a name="programmatically-access-a-custom-settings-value"></a>Programon keresztüli eléréséhez az egyéni beállítás értéke
 
A következő lépések bemutatják, hogyan programon keresztüli eléréséhez használ C# egyéni beállítás.

1. Adja hozzá a következő irányelvek segítségével egy C# fájlban hová beállítást használják:

    ```csharp
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.ServiceRuntime;
    ```

1. Az alábbi kód bemutatja, hogyan férhet hozzá az egyéni beállítás példát. Cserélje le a &lt;SettingName > helyőrzőt a megfelelő értékkel. 
    
    ```csharp
    var settingValue = RoleEnvironment.GetConfigurationSettingValue("<SettingName>");
    ```

## <a name="manage-local-storage-for-each-role-instance"></a>Minden egyes szerepkörpéldányhoz helyi tárterület kezelése
Egy szerepkör minden egyes példányánál helyi fájl rendszerhez szükséges tárhelyet adhat hozzá. A található adatokhoz tároló nem érhető el az adatok tárolását a szerepkör más példányát, vagy más szerepköröket.  

1. Hozzon létre, vagy a Visual Studio Azure cloud service projekt megnyitása.

1. A **Megoldáskezelőben**, bontsa ki a projekt csomópontjára. Az a **szerepkörök** csomópontot, kattintson a jobb gombbal a frissíteni, és a helyi menüből válassza ki a kívánt szerepkör **tulajdonságok**.

    ![Solution Explorer Azure szerepkör helyi menü](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-azure-role-context-menu.png)

1. Válassza ki a **helyi tároló** fülre.

    ![Helyi tárolás lap](./media/vs-azure-tools-configure-roles-for-cloud-service/role-local-storage-tab.png)

1. Az a **szolgáltatáskonfiguráció** listában, ügyeljen arra, hogy **összes konfiguráció** van jelölve, hogy a helyi tároló beállítások érvényesek minden szolgáltatáskonfiguráció. Semmilyen más érték a beviteli mezők le van tiltva az oldalon eredményez. 

    ![Konfigurációs szolgáltatás listája](./media/vs-azure-tools-configure-roles-for-cloud-service/role-local-storage-tab-service-configuration.png)

1. A helyi tárterület-bejegyzés hozzáadásához válassza ki a **hozzáadása helyi tároló**.

    ![Adja hozzá a helyi tároló](./media/vs-azure-tools-configure-roles-for-cloud-service/role-local-storage-tab-add-local-storage.png)

1. Az új helyi tároló bejegyzés hozzáadva a listához, frissíti a listában a sort a szükséges információkat.

    ![Új helyi tárterület-bejegyzés](./media/vs-azure-tools-configure-roles-for-cloud-service/role-local-storage-tab-new-local-storage.png)

    - **Név** -adja meg az új helyi tárolására használni kívánt nevét.
    - **Méret (MB)** -adja meg a mérete (MB), amelyekre szüksége van az új helyi tárolására.
    - **Tiszta a szerepkör újrahasznosítást** – válassza ezt a beállítást, az új helyi tároló eltávolítja az adatokat, amikor a szerepkör a virtuális gép újraindul.

1. Törli a helyi tároló bejegyzést, válassza ki a bejegyzést, és válassza **helyi tárhely eltávolítása**.

1. A Visual Studio eszköztárában kattintson **mentése**.

## <a name="programmatically-accessing-local-storage"></a>Programozott módon a helyi tároló elérése

Ez a szakasz bemutatja, hogyan való szoftveres hozzáféréshez helyi tárolóhely-C# teszt szövegfájlba írásával `MyLocalStorageTest.txt`.  

### <a name="write-a-text-file-to-local-storage"></a>Szövegfájl írni a helyi tároló

A következő kód bemutatja a példa bemutatja, hogyan szövegfájlba írni a helyi tárterület. Cserélje le a &lt;LocalStorageName > helyőrzőt a megfelelő értékkel. 

    ```csharp
    // Retrieve an object that points to the local storage resource
    LocalResource localResource = RoleEnvironment.GetLocalResource("<LocalStorageName>");
    
    //Define the file name and path
    string[] paths = { localResource.RootPath, "MyLocalStorageTest.txt" };
    String filePath = Path.Combine(paths);
    
    using (FileStream writeStream = File.Create(filePath))
    {
        Byte[] textToWrite = new UTF8Encoding(true).GetBytes("Testing Web role storage");
        writeStream.Write(textToWrite, 0, textToWrite.Length);
    }

    ```

### <a name="find-a-file-written-to-local-storage"></a>A fájl helyi tárhelyen található

A kódot az előző szakaszban létrehozott fájl megtekintéséhez kövesse az alábbi lépéseket:
    
1.  A Windows értesítési területen kattintson a jobb gombbal az Azure ikonra, és, a helyi menüből válassza ki a **Show Compute Emulator felhasználói felületén**. 

    ![Az Azure compute emulator megjelenítése](./media/vs-azure-tools-configure-roles-for-cloud-service/show-compute-emulator.png)

1. Válassza ki a webes szerepkör.

    ![Az Azure compute emulator](./media/vs-azure-tools-configure-roles-for-cloud-service/compute-emulator.png)

1. Az a **Microsoft Azure Compute Emulator** menü **eszközök** > **nyissa meg a helyi tárolójába**.

    ![Nyissa meg a helyi tárolójába menüpont](./media/vs-azure-tools-configure-roles-for-cloud-service/compute-emulator-open-local-store-menu.png)

1. Amikor megnyílik a Windows Intéző ablakot, írja be a "MyLocalStorageTest.txt" be a **keresési** szövegmezőbe, majd válassza ki **Enter** a keresés indításához. 

## <a name="next-steps"></a>További lépések
További tudnivalók a Visual Studio Azure projektek olvasásával [konfigurálása az Azure-projekt](vs-azure-tools-configuring-an-azure-project.md). További információ a felhőalapú szolgáltatás séma olvasásával [Sémareferenciája](https://msdn.microsoft.com/library/azure/dd179398).

