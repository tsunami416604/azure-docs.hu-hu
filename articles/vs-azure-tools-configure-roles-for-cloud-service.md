---
title: A szerepkörök az Azure cloud Services számára konfigurálása a Visual Studióval |} A Microsoft Docs
description: Megtudhatja, hogyan állíthatja be, és a szerepkörök a Visual Studio használatával Azure cloud services konfigurálása.
services: visual-studio-online
author: ghogen
manager: douge
assetId: d397ef87-64e5-401a-aad5-7f83f1022e16
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/21/2017
ms.author: ghogen
ms.openlocfilehash: 010a345d0bc756855b9a85660afcd647d111db75
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2018
ms.locfileid: "42056884"
---
# <a name="configure-azure-cloud-service-roles-with-visual-studio"></a>Az Azure cloud service szerepkörök konfigurálása a Visual Studióval
Egy Azure-felhőszolgáltatás egy vagy több feldolgozói vagy a webes szerepkörök rendelkezhet. Az egyes szerepkörökhöz kell határozza meg, hogy hogyan van beállítva, hogy a szerepkör és a is konfigurálhatja, hogyan fut a szerepkörhöz. A cloud services-szerepkörök kapcsolatos további információkért tekintse meg a videó [Azure Cloud Services bemutatása](https://channel9.msdn.com/Series/Windows-Azure-Cloud-Services-Tutorials/Introduction-to-Windows-Azure-Cloud-Services). 

A felhőszolgáltatás adatait tárolja a következő fájlokat:

- **ServiceDefinition.csdef** – a szolgáltatásdefiníciós fájlt a futásidő beállításait, a felhőalapú szolgáltatás, többek között, hogy milyen szerepkörök szükségesek, végpontok és virtuális gép mérete határozza meg. A tárolt adatokat `ServiceDefinition.csdef` módosítható, ha a szerepkör fut.
- **ServiceConfiguration.cscfg** – a szolgáltatás konfigurációs fájlja konfigurálja, hogy hány példánya egy futnak, és a beállítások értékeit meghatározott szerepkör. A tárolt adatok `ServiceConfiguration.cscfg` munkaköre futása közben is módosítható.

A beállítás szabályozza, hogyan fut a egy szerepkör különböző értékeket szeretne tárolni, több szolgáltatáskonfiguráció adhatja meg. Minden üzembehelyezési környezetet is használhat egy másik szolgáltatás konfigurációját. Például beállíthatja a tárfiók kapcsolati sztringje a helyi Azure storage emulatorral helyi szolgáltatás konfigurációban, és hozzon létre egy másik szolgáltatás konfigurációját az Azure storage használata a felhőben.

Egy Azure-felhőszolgáltatás a Visual Studióban történő létrehozásakor két szolgáltatáskonfiguráció automatikusan létrehozott és hozzáadott Azure-projektjéhez:

- `ServiceConfiguration.Cloud.cscfg`
- `ServiceConfiguration.Local.cscfg`

## <a name="configure-an-azure-cloud-service"></a>Az Azure felhőszolgáltatás konfigurálása
Konfigurálhatja egy Azure-felhőszolgáltatásban a Megoldáskezelőből a Visual Studióban, ahogyan az az alábbi lépéseket:

1. Hozzon létre vagy nyisson meg egy Azure-felhőszolgáltatás-projekt a Visual Studióban.

1. A **Megoldáskezelőben**, kattintson a jobb gombbal a projektre, és a helyi menüből válassza ki a **tulajdonságok**.
   
    ![Megoldás Explorer projekt helyi menüjében](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-project-context-menu.png)

1. A projekt tulajdonságai lapon válassza ki a **fejlesztési** fülre. 

    ![Projekt tulajdonságai lap – fejlesztési lap](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-development-tab.png)

1. Az a **szolgáltatáskonfiguráció** listájához, válassza ki a szerkeszteni kívánt szolgáltatáskonfiguráció nevét. (Ha azt szeretné, ezt a szerepkört az összes szolgáltatás konfiguráció módosításához válassza **az összes konfiguráció**.)
   
    > [!IMPORTANT]
    > Ha úgy dönt, hogy egy adott szolgáltatás konfigurációja, bizonyos tulajdonságok le vannak tiltva, mert azok a konfigurációk csak állítható. Ezek a tulajdonságok szerkesztéséhez választania kell **az összes konfiguráció**.
    > 
    > 
   
    ![Az Azure cloud Services számára Service Configuration list](./media/vs-azure-tools-configure-roles-for-cloud-service/cloud-service-service-configuration-property.png)

## <a name="change-the-number-of-role-instances"></a>A szerepkörpéldányok számát
A felhőszolgáltatás a teljesítmény javítása érdekében módosíthatja a példányok egy szerepkör, amely futtatja, a felhasználók vagy a terhelés egy adott szerepkör esetében várt száma alapján. Egy különálló virtuális gép szerepkör minden egyes példányánál jön létre az Azure-ban a felhőszolgáltatás futtatásakor. Ez hatással van a számlázás a felhőalapú szolgáltatás központi telepítésére vonatkozóan. Számlázással kapcsolatos további információkért lásd: [Microsoft Azure-hoz kapcsolódó számlák magyarázata](billing/billing-understand-your-bill.md).

1. Hozzon létre vagy nyisson meg egy Azure-felhőszolgáltatás-projekt a Visual Studióban.

1. A **Megoldáskezelőben**, bontsa ki a projekt csomópontjára. Alatt a **szerepkörök** csomópontot, kattintson a jobb gombbal a frissíteni, és a helyi menüből válassza ki a kívánt szerepkör **tulajdonságok**.

    ![Megoldás Explorer Azure szerepkör helyi menü](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-azure-role-context-menu.png)

1. Válassza ki a **konfigurációs** fülre.

    ![Konfiguráció lap](./media/vs-azure-tools-configure-roles-for-cloud-service/role-configuration-properties-page.png)

1. Az a **szolgáltatáskonfiguráció** listájához, válassza ki a frissíteni kívánt szolgáltatás konfigurációját.
   
    ![Service Configuration list](./media/vs-azure-tools-configure-roles-for-cloud-service/role-configuration-properties-page-select-configuration.png)

1. Az a **példányszám** szöveget adja meg, hogy el szeretné indítani a szerepkör-példányok száma. Minden példány egy külön virtuális gépet futtat, a felhőalapú szolgáltatás az Azure-ba való közzétételekor.

    ![A példányok számának frissítése](./media/vs-azure-tools-configure-roles-for-cloud-service/role-configuration-properties-page-instance-count.png)

1. A Visual Studio eszköztárában kattintson **mentése**.

## <a name="manage-connection-strings-for-storage-accounts"></a>Kapcsolati karakterláncok storage-fiókok kezelése
Hozzáadása, eltávolítása vagy módosítása a szolgáltatáskonfiguráció kapcsolati karakterláncokat. Például érdemes egy helyi kapcsolati karakterláncot, amelynek értéke helyi szolgáltatás konfiguráció `UseDevelopmentStorage=true`. Érdemes konfigurálni egy felhőalapú szolgáltatás konfigurációja, amely egy storage-fiókot használja az Azure-ban is.

> [!WARNING]
> Írja be az Azure storage fiók kulcs adatait egy tárfiók kapcsolati sztringje, ezeket az információkat tárolja helyileg a konfigurációs fájlban. Azonban ezeket az adatokat jelenleg nem tárolja a titkosított szövegként.
> 
> 

Egy másik értéket használatával minden egyes szolgáltatás konfigurációja, nem kell különböző kapcsolati karakterláncok használata a cloud service-ben, vagy módosítsa a kódot, a cloud Services az Azure-ba való közzétételekor. Használhatja ugyanazt a nevet a kapcsolati karakterláncot a kódban, és az érték nem egyezik, a szolgáltatás konfigurációja, amely a felhőszolgáltatás készítése során, vagy már a közzététel idején alapján.

1. Hozzon létre vagy nyisson meg egy Azure-felhőszolgáltatás-projekt a Visual Studióban.

1. A **Megoldáskezelőben**, bontsa ki a projekt csomópontjára. Alatt a **szerepkörök** csomópontot, kattintson a jobb gombbal a frissíteni, és a helyi menüből válassza ki a kívánt szerepkör **tulajdonságok**.

    ![Megoldás Explorer Azure szerepkör helyi menü](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-azure-role-context-menu.png)

1. Válassza ki a **beállítások** fülre.

    ![Beállítások lap](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab.png)

1. Az a **szolgáltatáskonfiguráció** listájához, válassza ki a frissíteni kívánt szolgáltatás konfigurációját.

    ![Szolgáltatás konfigurációja](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-select-configuration.png)

1. Kapcsolati karakterlánc hozzáadásához válassza **beállítás hozzáadása**.

    ![Kapcsolati sztring hozzáadása](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-add-setting.png)

1. Miután a lista az új beállítással bővült, frissítse a sorra a listában a szükséges információkat.

    ![Új kapcsolati sztring](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-add-setting-new-setting.png)

    - **Név** – adja meg a kapcsolati karakterláncot használni kívánt nevét.
    - **Típus** – ki **kapcsolati karakterlánc** a legördülő listából.
    - **Érték** -adhatja meg a kapcsolati karakterláncot, közvetlenül a **érték** cella, vagy válassza ki a munkát a három pontra (...) a **tárolási kapcsolati karakterlánc létrehozása** párbeszédpanel.  

1. Az a **tárolási kapcsolati karakterlánc létrehozása** párbeszédpanelen jelöljön ki egy lehetőséget **érdemesebb**. Kövesse az utasításokat a lehetőséget választja:

    - **A Microsoft Azure storage emulator** – Ha ezt a lehetőséget választja, a többi beállítás párbeszédpanelen le vannak tiltva mivel azok csak az Azure-bA. Kattintson az **OK** gombra.
    - **Az előfizetés** – Ha ezt a beállítást, használja a legördülő listából válassza ki, és jelentkezzen be Microsoft-fiókot, vagy a Microsoft-fiók hozzáadása. Válassza ki az Azure előfizetés- és storage-fiók. Kattintson az **OK** gombra.
    - **Manuálisan kell megadni a hitelesítő adatok** – adja meg a tárfiók nevét és az elsődleges vagy a második kulcsot. Válassza ki a **kapcsolat** (a legtöbb forgatókönyvhöz HTTPS ajánlott.) Válassza ki **OK**.

1. Kapcsolati karakterlánc törléséhez válassza ki a kapcsolati karakterláncot, és válassza **távolítsa el a beállítás**.

1. A Visual Studio eszköztárában kattintson **mentése**.

## <a name="programmatically-access-a-connection-string"></a>Egy kapcsolati karakterláncot programozott módon érheti el

A következő lépések bemutatják, hogyan C# használatával kapcsolati karakterláncot programozott módon érheti el.

1. Adja hozzá az alábbi irányelvek használatával hol kívánja használni a beállítás C# fájlban:

    ```csharp
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.ServiceRuntime;
    ```

1. Az alábbi kód bemutatja, hogyan érhető el egy kapcsolati karakterláncot egy példát. Cserélje le a &lt;ConnectionStringName > helyőrzőt a megfelelő értékre. 

    ```csharp
    // Setup the connection to Azure Storage
    var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("<ConnectionStringName>"));
    ```

## <a name="add-custom-settings-to-use-in-your-azure-cloud-service"></a>Az Azure cloud service-ben használható egyéni beállítások hozzáadása
Egyéni beállítások a konfigurációs fájlban segítségével adjon hozzá egy nevet és a egy adott szolgáltatás konfigurációs karakterlánc értékét. Előfordulhat, hogy használni kívánja ezt a beállítást, a szolgáltatás konfigurálása a cloud service-ben a beállítás értékét olvasásakor, és ezt az értéket használja a kód a logika. Módosíthatja a szolgáltatás konfigurációs értékek nélkül építse újra a szolgáltatáscsomag vagy a felhőszolgáltatás futtatásakor. A kód az értesítések ellenőrizheti, amikor a beállítások módosításai. Lásd: [RoleEnvironment.Changing esemény](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changing.aspx).

Hozzáadhat, távolítsa el, vagy egyéni beállítások a szolgáltatáskonfiguráció módosítása. Ezek a karakterláncok eltérő konfigurációk esetében érdemes lehet különböző értékeket.

Egy másik értéket használatával minden egyes szolgáltatás konfigurációja, nem kell különböző karakterláncokat használni a cloud service-ben, vagy módosítsa a kódot, a cloud Services az Azure-ba való közzétételekor. Használhatja ugyanazt a nevet a karakterláncot a kódban, és az érték nem egyezik, a szolgáltatás konfigurációja, amely a felhőszolgáltatás készítése során, vagy már a közzététel idején alapján.

1. Hozzon létre vagy nyisson meg egy Azure-felhőszolgáltatás-projekt a Visual Studióban.

1. A **Megoldáskezelőben**, bontsa ki a projekt csomópontjára. Alatt a **szerepkörök** csomópontot, kattintson a jobb gombbal a frissíteni, és a helyi menüből válassza ki a kívánt szerepkör **tulajdonságok**.

    ![Megoldás Explorer Azure szerepkör helyi menü](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-azure-role-context-menu.png)

1. Válassza ki a **beállítások** fülre.

    ![Beállítások lap](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab.png)

1. Az a **szolgáltatáskonfiguráció** listájához, válassza ki a frissíteni kívánt szolgáltatás konfigurációját.

    ![Service Configuration list](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-select-configuration.png)

1. Egy egyéni beállítás hozzáadásához válassza **beállítás hozzáadása**.

    ![Egyéni beállítás hozzáadása](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-add-setting.png)

1. Miután a lista az új beállítással bővült, frissítse a sorra a listában a szükséges információkat.

    ![Új egyéni beállítás](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-add-setting-new-setting.png)

    - **Név** – adja meg a beállítás nevét.
    - **Típus** – ki **karakterlánc** a legördülő listából.
    - **Érték** – adja meg a beállítás értékét. Vagy megadhatja közvetlenül az érték a **érték** cella, vagy jelölje ki a három pontra (...), írja be az értéket a **karakterlánc szerkesztése** párbeszédpanel.  

1. Egy egyéni beállítás törléséhez jelölje ki a beállítást, és válassza **távolítsa el a beállítás**.

1. A Visual Studio eszköztárában kattintson **mentése**.

## <a name="programmatically-access-a-custom-settings-value"></a>Programozott módon érheti el az egyéni beállítás értéke
 
A következő lépések bemutatják, hogyan programozott módon érheti el az egyéni beállítás C# használatával.

1. Adja hozzá az alábbi irányelvek használatával hol kívánja használni a beállítás C# fájlban:

    ```csharp
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.ServiceRuntime;
    ```

1. A következő kódot egy egyéni beállítás elérése példáját mutatja be. Cserélje le a &lt;SettingName > helyőrzőt a megfelelő értékre. 
    
    ```csharp
    var settingValue = RoleEnvironment.GetConfigurationSettingValue("<SettingName>");
    ```

## <a name="manage-local-storage-for-each-role-instance"></a>Minden egyes szerepkör-példány helyi tárterület kezelése
Hozzáadhat a helyi fájlrendszer-tárhely a szerepkör minden egyes példányánál. Az adatok tárolt abban, hogy a tároló nem érhető el, további példányok szerepköréhez tartozó adatokat tárolja, vagy más szerepköröket.  

1. Hozzon létre vagy nyisson meg egy Azure-felhőszolgáltatás-projekt a Visual Studióban.

1. A **Megoldáskezelőben**, bontsa ki a projekt csomópontjára. Alatt a **szerepkörök** csomópontot, kattintson a jobb gombbal a frissíteni, és a helyi menüből válassza ki a kívánt szerepkör **tulajdonságok**.

    ![Megoldás Explorer Azure szerepkör helyi menü](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-azure-role-context-menu.png)

1. Válassza ki a **helyi tároló** fülre.

    ![Helyi tároló lap](./media/vs-azure-tools-configure-roles-for-cloud-service/role-local-storage-tab.png)

1. Az a **szolgáltatáskonfiguráció** listában, ellenőrizze, hogy **az összes konfiguráció** van jelölve, hogy a helyi tároló beállítások vonatkoznak az összes szolgáltatás konfigurációja. Bármely más érték eredményez az adatbeviteli mezők le van tiltva, az oldalon. 

    ![Service Configuration list](./media/vs-azure-tools-configure-roles-for-cloud-service/role-local-storage-tab-service-configuration.png)

1. Egy helyi tárterület-bejegyzés hozzáadásához válassza **helyi tárhely hozzáadása**.

    ![Helyi tároló hozzáadása](./media/vs-azure-tools-configure-roles-for-cloud-service/role-local-storage-tab-add-local-storage.png)

1. Ha az új helyi tárterület-bejegyzést a listához hozzáadott, frissítse a sorra a listában a szükséges információkat.

    ![Új helyi tárterület-bejegyzés](./media/vs-azure-tools-configure-roles-for-cloud-service/role-local-storage-tab-new-local-storage.png)

    - **Név** – adja meg az új helyi tárolására használni kívánt nevét.
    - **Méret (MB)** – adja meg a mérete (MB), amely az új helyi tárhelyre van szüksége.
    - **Törölje a szerepkör újraindítási** – ezzel a lehetőséggel távolítsa el az adatokat az új helyi tároló, amikor a szerepkör a virtuális gép újraindul.

1. A helyi tároló bejegyzés törléséhez válassza ki a bejegyzést, és válassza **helyi tároló eltávolítása**.

1. A Visual Studio eszköztárában kattintson **mentése**.

## <a name="programmatically-accessing-local-storage"></a>Programozott módon a helyi tárolók elérése során

Ez a szakasz bemutatja, hogyan programozott módon érheti el a helyi tároló használata a C#, ha a teszt szövegfájl `MyLocalStorageTest.txt`.  

### <a name="write-a-text-file-to-local-storage"></a>Szövegfájl írni a helyi tároló

A következő kódot egy szövegfájlba írásával helyi tárba egy példát mutat be. Cserélje le a &lt;LocalStorageName > helyőrzőt a megfelelő értékre. 

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

### <a name="find-a-file-written-to-local-storage"></a>Helyi tároló írt fájl keresése

A fájl által a kód az előző szakaszban létrehozott megtekintéséhez kövesse az alábbi lépéseket:
    
1.  A Windows értesítési területen kattintson a jobb gombbal az Azure ikonra, és, a helyi menüből válassza ki a **Show Compute Emulator felhasználói felületén**. 

    ![Az Azure compute emulator megjelenítése](./media/vs-azure-tools-configure-roles-for-cloud-service/show-compute-emulator.png)

1. Válassza ki a webes szerepkör.

    ![Az Azure compute emulator](./media/vs-azure-tools-configure-roles-for-cloud-service/compute-emulator.png)

1. Az a **a Microsoft Azure Compute Emulator** menüjében válassza **eszközök** > **nyissa meg a helyi tároló**.

    ![Nyissa meg a helyi tároló menüpont](./media/vs-azure-tools-configure-roles-for-cloud-service/compute-emulator-open-local-store-menu.png)

1. Amikor megnyílik a Windows Intéző ablakot, írja be a "MyLocalStorageTest.txt:" % be a **keresési** szövegmezőbe, majd válassza ki **Enter** kereséséhez. 

## <a name="next-steps"></a>További lépések
Olvassa el a Visual Studio Azure-projektek többet [egy Azure-projekt konfigurálása](vs-azure-tools-configuring-an-azure-project.md). További információ a cloud service-séma olvasásával [Sémaleírás](https://msdn.microsoft.com/library/azure/dd179398).

