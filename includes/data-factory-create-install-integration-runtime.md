---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: a2858ac73838b50c21a76db5860675171a306192
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58505687"
---
## <a name="create-a-self-hosted-integration-runtime"></a>Saját üzemeltetésű Integration Runtime létrehozása

Ebben a szakaszban egy saját üzemeltetésű Integration Runtime átjárót hozhat létre, és társíthatja azt az SQL Server-adatbázist futtató helyszíni géppel. A saját üzemeltetésű integrációs modul az a komponens, amely adatokat másol a gépen futó SQL Serverről az Azure Blob Storage-ba. 

1. Hozzon létre egy változót az integrációs modul nevéhez. Adjon meg egy egyedi nevet, és jegyezze fel. Az oktatóanyag későbbi részében használni fogja. 

    ```powershell
   $integrationRuntimeName = "ADFTutorialIR"
    ```
2. Hozzon létre egy saját üzemeltetésű integrációs modult. 

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -Name $integrationRuntimeName -Type SelfHosted -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName
   ```

   Itt látható a minta kimenete:

   ```json
    Id                : /subscriptions/<subscription ID>/resourceGroups/ADFTutorialResourceGroup/providers/Microsoft.DataFactory/factories/onpremdf0914/integrationruntimes/myonpremirsp0914
    Type              : SelfHosted
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Name              : myonpremirsp0914
    Description       :
    ```
  
3. Futtassa az alábbi parancsot a létrehozott integrációs modul állapotának lekéréséhez. Ellenőrizze, hogy a **State** tulajdonság **NeedRegistration** értékű-e. 

   ```powershell
   Get-AzDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
   ```

   Itt látható a minta kimenete:

   ```json
   Nodes                     : {}
   CreateTime                : 9/14/2017 10:01:21 AM
   InternalChannelEncryption :
   Version                   :
   Capabilities              : {}
   ScheduledUpdateDate       :
   UpdateDelayOffset         :
   LocalTimeZoneOffset       :
   AutoUpdate                :
   ServiceUrls               : {eu.frontend.clouddatahub.net, *.servicebus.windows.net}
   ResourceGroupName         : <ResourceGroup name>
   DataFactoryName           : <DataFactory name>
   Name                      : <Integration Runtime name>
   State                     : NeedRegistration
   ```

4. Futtassa az alábbi parancsot a hitelesítési kulcsok lekéréséhez, hogy a saját üzemeltetésű integrációsmodul-átjárót regisztrálhassa az Azure Data Factory szolgáltatásban a felhőben: 

   ```powershell
   Get-AzDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
   ```

   Itt látható a minta kimenete:

   ```json
   {
       "AuthKey1":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx=",
       "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy="
   }
   ```    

5. Másolja be az egyik kulcsot (idézőjelek nélkül), amelyet a következő lépésekben a gépére telepítendő, saját üzemeltetésű integrációs modul regisztrálásához használt.  

## <a name="install-the-integration-runtime"></a>Az Integration Runtime telepítése
1. Ha az integrációs modul már telepítve van a számítógépen, akkor a **Programok hozzáadása vagy eltávolítása** funkcióval távolítsa el. 

2. [Töltse le](https://www.microsoft.com/download/details.aspx?id=39717) a saját üzemeltetésű integrációs modult egy helyi windowsos gépre. Futtassa a telepítést.

3. Az **Üdvözli a Microsoft Integration Runtime telepítővarázsló** oldalon kattintson a **Tovább** gombra.

4. A **Végfelhasználói licencszerződés** oldalon fogadja el a felhasználási feltételeket és a licencszerződést, majd válassza a **Tovább** gombot.

5. A **Célmappa** oldalon válassza a **Tovább** gombot.

6. **A Microsoft Integration Runtime telepítésre kész** oldalon válassza a **Telepítés** elemet.

7. Ha figyelmeztető üzenet jelenik meg azzal kapcsolatban, hogy a számítógép használaton kívül automatikus alvó módba lépésre vagy hibernálásra lesz konfigurálva, kattintson az **OK** gombra.

8. Ha megjelenik az **Energiagazdálkodási lehetőségek** oldal, zárja be, és váltson a telepítési oldalra.

9. **A Microsoft Integration Runtime telepítővarázslójának befejező lapján** kattintson a **Befejezés** gombra.

10. Az **Integration Runtime (helyi) regisztrálása** lapon illessze be az előző szakaszban mentett kulcsot, és válassza a **Regisztráció** elemet. 

    ![Az integrációs modul regisztrálása](media/data-factory-create-install-integration-runtime/register-integration-runtime.png)

11. Az integrációs modul sikeres regisztrációja esetén a következő üzenet jelenik meg:

    ![Sikeres regisztráció](media/data-factory-create-install-integration-runtime/registered-successfully.png)

12. Az **Új Integration Runtime (helyi) csomópont** lapon válassza a **Tovább** elemet. 

    ![Új integrációs modulcsomópont lap](media/data-factory-create-install-integration-runtime/new-integration-runtime-node-page.png)

13. Az **Intranetes kommunikációs csatorna** lapon válassza a **Kihagyás** elemet. A több csomópontos integrációsmodul-környezetekben a csomóponton belüli kommunikáció biztonságának garantálásához válasszon TLS-/SSL-hitelesítést. 

    ![Intranetes kommunikációs csatorna lap](media/data-factory-create-install-integration-runtime/intranet-communication-channel-page.png)

14. Az **Integration Runtime (helyi) regisztrálása** lapon válassza a **Konfigurációkezelő indítása** elemet.

15. A csomópont a felhőszolgáltatáshoz való csatlakoztatásakor a következő oldal jelenik meg:

    ![Csomópont csatlakoztatva oldal](media/data-factory-create-install-integration-runtime/node-is-connected.png)

16. Teszteltje az SQL Server-adatbázissal létesített kapcsolatot.

    ![Diagnosztika lap](media/data-factory-create-install-integration-runtime/config-manager-diagnostics-tab.png)   

    a. A **Configuration Manager** ablakban váltson a **Diagnosztika** lapra.

    b. Válassza az **SqlServer** értéket az adatforrás típusaként.

    c. Adja meg a kiszolgáló nevét.

    d. Adja meg az adatbázis nevét.

    e. Válassza ki a hitelesítési módot.

    f. Adja meg a felhasználónevet.

    g. Adja meg felhasználónévhez tartozó jelszót.

    h. Válassza a **Teszt** elemet annak ellenőrzéséhez, hogy az integrációs modul kapcsolódni tud-e az SQL Serverhez. Sikeres kapcsolódás esetén egy zöld pipa jelenik meg. Sikertelen kapcsolódás esetén egy hibaüzenet jelenik meg. Javítsa ki a hibákat, és ellenőrizze, hogy az integrációs modul kapcsolódik-e az SQL Serverhez.    

    > [!NOTE]
    > Jegyezze fel a hitelesítési típus, a kiszolgáló, az adatbázis, a felhasználó és a jelszó értékeit. Az oktatóanyag részében használni fogja őket. 
    
