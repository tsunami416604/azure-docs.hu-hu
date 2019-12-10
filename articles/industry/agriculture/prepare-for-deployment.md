---
title: Az Azure FarmBeats üzembe helyezése
description: Ez a cikk az Azure FarmBeats üzembe helyezését ismerteti.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 27bf62cb328273db1f7bdd44117853b00feca9ae
ms.sourcegitcommit: b5ff5abd7a82eaf3a1df883c4247e11cdfe38c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74941576"
---
# <a name="deploy-azure-farmbeats"></a>Az Azure FarmBeats üzembe helyezése

Ez a cikk az Azure-FarmBeats beállítását ismerteti.

Az Azure FarmBeats egy iparág-specifikus, bővíthető megoldás az adatvezérelt gazdálkodáshoz, amely zökkenőmentes kiépítés és érzékelő-eszköz kapcsolat létesítését teszi lehetővé az Azure-felhővel, a telemetria adatgyűjtéssel és az összesítéssel. Az Azure FarmBeats számos érzékelőt tartalmaz, például a kamerákat, a heréket és a talaj-érzékelőket. Az Azure FarmBeats segítségével felügyelheti az eszközöket a felhőből, amely magában foglalja az Azure-ban az infrastruktúra és szolgáltatások kezelését IoT-kompatibilis eszközökhöz, valamint egy bővíthető webes és mobil alkalmazás használatával a vizualizációk, a riasztások és a betekintések biztosításához.

> [!NOTE]
> Az Azure FarmBeats csak nyilvános Felhőbeli környezetekben támogatott. További információ: [Mi az a nyilvános felhő?](https://azure.microsoft.com/overview/what-is-a-public-cloud/).

Az Azure FarmBeats a következő két összetevővel rendelkezik:

- **Datahub**: az Azure FarmBeats platform rétege, amely lehetővé teszi az adatok kiépítését, tárolását, feldolgozását és elemzését meglévő vagy új adatfolyamatokból. Ez a platform-réteg hasznos a mezőgazdasági adatfolyamatok és modellek futtatásához és felépítéséhez.

- **Gyorsító**: az Azure FarmBeats megoldási rétege, amely egy beépített alkalmazással szemlélteti az Azure FarmBeats képességeit az előre létrehozott mezőgazdasági modellek használatával. Ez a megoldás lehetővé teszi, hogy a farm határain belül hozza létre a gazdaságok határait, és elemzéseket készítsen a mezőgazdasági adatokból.

Az Azure-FarmBeats gyors üzembe helyezése kevesebb mint egy órát vehet igénybe. A Datahub és a gyorsított költségek a használattól függően eltérőek.

## <a name="deployed-resources"></a>Üzembe helyezett erőforrások

Az Azure FarmBeats üzembe helyezése a következő erőforrásokat hozza létre az előfizetésén belül:

| Sorozatszám. sz.  | Erőforrás neve  | FarmBeats-összetevő  |
|---------|---------|---------|
|1  |       Azure Cosmos DB   |  Datahub       |
|2  |    Application Insights      |     Datahub/gyorssegéd     |
|3  |Azure Cache for Redis   |Datahub   |
|4  |       Azure Key Vault    |  Datahub/gyorssegéd        |
|5  |    Azure Time Series Insights       |     Datahub      |
|6 |      Azure Event hub-névtér    |  Datahub       |
|7  |    Azure Data Factory V2       |     Datahub/gyorssegéd      |
|8  |Batch-fiók    |Datahub   |
|9  |       Tárfiók     |  Datahub/gyorssegéd        |
|10  |    Logikai alkalmazás        |     Datahub      |
|11  |    API-kapcsolatok        |     Datahub      |
|12|      Azure App Service      |  Datahub/gyorssegéd       |
|13 |    App Service-csomag        |     Datahub/gyorssegéd      |
|14 |Azure Maps fiók     |Accelerator    |

Az Azure FarmBeats letölthető az Azure Marketplace-en, amelyet közvetlenül a Azure Portal érhet el.  

## <a name="create-an-azure-farmbeats-offer-in-azure-marketplace"></a>Azure FarmBeats-ajánlat létrehozása az Azure Marketplace-en

Azure FarmBeats-ajánlat Azure Marketplace-en való létrehozásához tegye a következőket:

1. Jelentkezzen be a Azure Portalba, válassza ki a fiókját a jobb felső sarokban, majd váltson arra a Azure Active Directory (Azure AD) bérlőre, ahol az Azure FarmBeats telepíteni kívánja.
1. Az Azure FarmBeats az Azure piactéren érhető el. Az **Azure Marketplace** oldalon válassza a **Letöltés most**lehetőséget.
1. Kattintson a **Létrehozás** gombra.

> [!NOTE]
> Az ajánlat az Azure Marketplace-en való befejezése csak a telepítés része. Az Azure-FarmBeats Azure-előfizetésben való üzembe helyezésének befejezéséhez kövesse a következő szakaszokban ismertetett utasításokat.

## <a name="prepare"></a>Előkészületek

Az Azure FarmBeats üzembe helyezése előtt a következő engedélyek szükségesek:

- **Bérlő**: olvasási hozzáférés
- **Előfizetés**: közreműködő vagy tulajdonos
- **Erőforráscsoport**: tulajdonos

## <a name="before-you-begin"></a>Előzetes teendők

Az üzembe helyezés megkezdése előtt győződjön meg arról, hogy a következő előfeltételek vannak érvényben:

- Sentinel-fiók
- Azure AD-alkalmazás regisztrálása

## <a name="create-a-sentinel-account"></a>Sentinel-fiók létrehozása    

A Sentinel-fiókkal rendelkező fiókok segítségével letöltheti a Sentinel műholdképeket a hivatalos webhelyről az eszközre. Ingyenes fiók létrehozásához tegye a következőket:

1. Nyissa meg a [Sentinel-fiók regisztrálása lapot](https://scihub.copernicus.eu/dhus/#/self-registration).
1. A regisztrációs űrlapon adja meg az utónév, a vezetéknév, a Felhasználónév, a jelszó és az e-mail-cím nevét.

A rendszer visszaigazoló e-mailt küld a regisztrált e-mail-címre. Válassza ki a hivatkozást az e-mail-cím megerősítéséhez. A regisztrációs folyamat befejeződött.

## <a name="create-an-azure-ad-app-registration"></a>Azure AD-alkalmazás regisztrálásának létrehozása

Az Azure FarmBeats való hitelesítéshez és engedélyezéshez Azure AD-alkalmazás regisztrálása szükséges. Két módon is létrehozhatja:

* **1. lehetőség**: a telepítő automatikusan létrehozhatja a regisztrációt, ha rendelkezik a bérlő, az előfizetés és az erőforráscsoport hozzáférési engedélyeivel. Ha ezek az engedélyek érvényben vannak, folytassa a ["input. JSON fájl előkészítése"](#prepare-the-inputjson-file) című szakaszban.

* **2. lehetőség**: a regisztrációt manuálisan is létrehozhatja és konfigurálhatja az Azure FarmBeats üzembe helyezése előtt. Ezt a módszert akkor javasoljuk, ha nem rendelkezik a szükséges engedélyekkel az Azure AD-alkalmazás regisztrációjának létrehozásához és konfigurálásához az előfizetésen belül. Kérje meg a rendszergazdát, hogy használja az [Egyéni szkriptet](https://aka.ms/FarmBeatsAADScript), amely segítséget nyújt a rendszergazda számára az Azure ad-alkalmazás regisztrálásának automatikus létrehozásához és konfigurálásához a Azure Portal. Az egyéni parancsfájl PowerShell-környezet használatával történő futtatásának kimenete a rendszergazdának meg kell osztania az Azure AD-alkalmazás ügyfél-AZONOSÍTÓját és a jelszó titkos kódját. Jegyezze fel ezeket az értékeket.

    Az Azure AD-alkalmazás regisztrációs parancsfájljának futtatásához tegye a következőket:

    1. Töltse le a [szkriptet](https://aka.ms/FarmBeatsAADScript).
    1. Jelentkezzen be a Azure Portalba, és válassza ki az előfizetését és az Azure AD-bérlőt.
    1. Cloud Shell elindítása a Azure Portal tetején található eszköztárból.

        ![Projekt FarmBeats](./media/prepare-for-deployment/navigation-bar-1.png)

    1. Ha Ön a felhasználó, akkor a rendszer kéri, hogy válasszon egy előfizetést a Storage-fiók létrehozásához és a fájlmegosztás Microsoft Azureához. Kattintson a **Create storage** (Tároló létrehozása) gombra.
    1. Az első alkalommal a felhasználók is választhatnak a Shell-élmények, a bash vagy a PowerShell használatával. Válassza a PowerShell lehetőséget.
    1. Töltse fel a szkriptet (az 1. lépésből) Cloud Shellba, és jegyezze fel a feltöltött fájl helyét.

        > [!NOTE]
        > Alapértelmezés szerint a rendszer feltölti a fájlt a saját könyvtárába.

        Használja a következő parancsfájlt:

        ```azurepowershell-interactive
        ./create_aad_script.ps1
        ```
    1. Jegyezze fel az Azure AD-alkalmazás AZONOSÍTÓját és az ügyfél titkos kulcsát, hogy megossza az Azure FarmBeats üzembe helyezését végző személlyel.

### <a name="prepare-the-inputjson-file"></a>A bemeneti. JSON fájl előkészítése

A telepítés részeként hozzon létre egy *input. JSON* fájlt, ahogy az itt látható:

```json
{  
    "sku":"both",
    "subscriptionId":"da9xxxec-dxxf-4xxc-xxx21-xxx3ee7xxxxx",
    "datahubResourceGroup":"dummy-test-dh1",
    "location":"westus2",
    "datahubWebsiteName":"dummy-test-dh1",
    "acceleratorResourceGroup":" dummy-test-acc1",
    "acceleratorWebsiteName":" dummy-test-acc1",
    "sentinelUsername":"dummy-dev",
    "notificationEmailAddress":"dummy@yourorg.com",
    "updateIfExists":true
}
```

Ez a fájl a Azure Cloud Shell a bemeneti fájl. Tartalmazza azokat a paramétereket, amelyek értékeit a rendszer a telepítés során használja. A JSON-fájlban lévő összes paramétert megfelelő értékekkel kell helyettesíteni, vagy el kell távolítani. Az eltávolításuk után a telepítő kéri a telepítés során.

A fájl előkészítése előtt tekintse át a következő táblázatban található paramétereket:

|Parancs | Leírás|
|--- | ---|
|SKU  | Lehetőséget biztosít az Azure FarmBeats egyik vagy mindkét összetevőjének letöltésére. Meghatározza a letölteni kívánt összetevőket. Csak a Datahub telepítéséhez használja a "onlydatabhub" kifejezést. A Datahub és a Gyorssegéd telepítéséhez használja a "mindkettő" lehetőséget.|
|subscriptionId | Az Azure FarmBeats telepítésére vonatkozó előfizetést adja meg.|
|datahubResourceGroup| A Datahub erőforrások erőforráscsoport-neve.|
|location |A hely, ahol létre kívánja hozni az erőforrásokat.|
|acceleratorWebsiteName |Az egyedi URL-előtag, amely a Datahub-példány nevét adja meg.|
|acceleratorResourceGroup  | Az egyedi URL-előtag, amely a gyorsított webhely nevét adja meg.|
|datahubWebsiteName  | Az egyedi URL-előtag, amely a Datahub webhely nevét adja meg. |
|sentinelUsername | A bejelentkezéshez használt Felhasználónév (például `https://scihub.copernicus.eu/dhus/#/self-registration`).|
|notificationEmailAddress  | Az e-mail-cím, amely a Datahub-példányon belül konfigurált riasztások értesítéseit fogadja.|
|updateIfExists| Választható Egy paraméter, amely csak akkor tartalmazza a *input. JSON* fájlt, ha egy meglévő Azure FarmBeats-példányt szeretne frissíteni. A frissítéshez a további részleteket, például az erőforráscsoportok nevét és helyét kell megegyeznie.|
|aadAppClientId | Választható Az *input. JSON* fájlban szerepeltetni kívánt paraméter, ha az Azure ad-alkalmazás már létezik.  |
|aadAppClientSecret  | Választható Az *input. JSON* fájlban szerepeltetni kívánt paraméter, ha az Azure ad-alkalmazás már létezik.|

## <a name="deploy-the-app-within-cloud-shell"></a>Az alkalmazás üzembe helyezése Cloud Shellon belül

A korábban tárgyalt Piactéri munkafolyamat részeként létre kell hoznia egy erőforráscsoportot, és alá kell írnia a "licencfeltételek" szerződést, amelyet a tényleges telepítés részeként is át lehet tekinteni. Az alkalmazást a Cloud Shell böngészőalapú parancssori felületen keresztül helyezheti üzembe a bash-környezet használatával. A Cloud Shell használatával történő üzembe helyezéshez folytassa a következő szakasszal.

> [!NOTE]
> Az inaktív Cloud Shell munkamenetek 20 perc elteltével lejárnak. Próbálja meg végrehajtani a központi telepítést.

1. Jelentkezzen be a Azure Portalba, és válassza ki a használni kívánt előfizetést és Azure-bérlőt.
1. Cloud Shell elindítása a Azure Portal tetején található eszköztárból.
1. Ha első alkalommal használja a Cloud Shell, a rendszer felkéri egy előfizetés kiválasztására egy Storage-fiók és egy Azure Files-megosztás létrehozásához.
1. Válassza a **tároló létrehozása**lehetőséget.  
1. A környezethez válassza a **bash** (nem PowerShell) lehetőséget.

## <a name="deployment-scenario-1"></a>1\. üzembe helyezési forgatókönyv

Ebben a forgatókönyvben, amely az 1. lehetőségnél korábban szerepel, a telepítő automatikusan létrehozza az Azure AD-alkalmazás regisztrációját. A FarmBeats a következő módon telepítheti:

1. Másolja az alábbi JSON-sablont, és mentse *input. JSON*néven. Ügyeljen arra, hogy jegyezze fel a helyi számítógép fájljának elérési útját.

    ```json
    {  
       "sku":"both", 
       "subscriptionId":"da9xxxec-dxxf-4xxc-xxx21-xxx3ee7xxxxx", 
       "datahubResourceGroup":"dummy-test-dh1", 
       "location":"eastus2", 
       "datahubWebsiteName":"dummy-test-dh1", 
       "acceleratorResourceGroup":" dummy-test-acc1",   
       "acceleratorWebsiteName":" dummy-test-acc1", 
       "sentinelUsername":"dummy-dev", 
       "notificationEmailAddress":" dummy@microsoft.com", 
       "updateIfExists":true 
    }
    ```

1. Nyissa meg Azure Cloud Shell. A sikeres hitelesítés után válassza a **feltöltés** gombot (az alábbi képen látható), majd töltse fel az *input. JSON* fájlt Cloud Shell tárolóba.  

    ![Projekt FarmBeats](./media/prepare-for-deployment/bash-2-1.png)

1. Nyissa meg Cloud Shell a saját címtárát. Alapértelmezés szerint a könyvtár neve */home/\<username >* .
1. A Cloud Shell írja be a következő parancsot. Ügyeljen arra, hogy módosítsa a *bemeneti. JSON* fájl elérési útját, majd válassza az ENTER billentyűt.

   ```bash
      wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```
     A telepítő automatikusan letölti az összes függőséget, és létrehozza az üzembe helyezést. A rendszer felszólítja, hogy fogadja el az Azure FarmBeats licencfeltételeket.

     - Ha elfogadja, adja meg az **Y** értéket a következő lépéshez való továbblépéshez.
     - Ha nem fogadja el, adja meg az **N**értéket, és a rendszer megszakítja a telepítést.

1. A parancssorban adjon meg egy hozzáférési jogkivonatot a központi telepítéshez. Másolja a generált kódot, és jelentkezzen be az [eszköz bejelentkezési lapjára](https://microsoft.com/devicelogin) az Azure-beli hitelesítő adataival.

    > [!NOTE]
    > A jogkivonat 60 perc után lejár. Ha lejár, újraindítással újragépelheti a telepítési parancsot.

1. A parancssorba írja be a Sentinel-fiók jelszavát.

   A telepítő ellenőrzi és elindítja az üzemelő példányt, ami körülbelül 20 percet vesz igénybe.

   Az üzembe helyezés sikeres befejeződése után a következő kimeneti hivatkozásokat fogja kapni:

    - **Datahub URL-cím**: az Azure FarmBeats API-k kipróbálására szolgáló hencegő hivatkozás.
    - **Gyorsító URL-cím**: a felhasználói felület az Azure FarmBeats intelligens Farm-gyorsító felderítéséhez.
    - **Központi telepítési naplófájl**: az üzembe helyezés során létrehozott naplófájl. Szükség esetén a hibaelhárításhoz is használható.

## <a name="deployment-scenario-2"></a>2\. üzembe helyezési forgatókönyv

Ebben a forgatókönyvben, amelyet korábban a "2. lehetőség" című cikkben ismertetünk, a meglévő Azure Active Directory alkalmazás-regisztráció használatával telepítheti a FarmBeats a következő módon:

1. Másolja a következő JSON-fájlt, amely tartalmazza az Azure-alkalmazás ügyfél-AZONOSÍTÓját és jelszavát a *bemeneti. JSON* fájlban, majd mentse. Ügyeljen arra, hogy jegyezze fel a helyi számítógép fájljának elérési útját.

    ```json
   {

   "sku":"both",
   "subscriptionId":"daxx9xxx-d18f-4xxc-9c21-5xx3exxxxx45",
   "datahubResourceGroup":"dummy-test-dh1",   
   "location":"westus2",   
   "datahubWebsiteName":"dummy-test-dh1",   
   "acceleratorResourceGroup":"dummy-test-acc1",   
   "acceleratorWebsiteName":"dummy-test-acc1",   
   "sentinelUsername":"dummy-dev",
   "notificationEmailAddress":"dummyuser@org1.com",
   "updateIfExists": true,
   "aadAppClientId": "lmtlemlemylmylkmerkywmkm823",
   "aadAppClientSecret": "Kxxxqxxxxu*kxcxxx3Yxxu5xx/db[xxx"

   }
   ```

1. Lépjen Azure Cloud Shell újra, és sikeresen megtörtént a hitelesítés.
1. Válassza ki a **feltöltés** gombot (a következő képen kiemelve), majd töltse fel a *bemeneti. JSON* fájlt Cloud Shell tárolóba.

    ![Projekt FarmBeats](./media/prepare-for-deployment/bash-2-1.png)

1. Nyissa meg Cloud Shell a saját címtárát. Alapértelmezés szerint a könyvtár neve */home/\<username >* .
1. A Cloud Shell írja be a következő parancsot. Ügyeljen arra, hogy módosítsa a *bemeneti. JSON* fájl elérési útját, majd válassza az ENTER billentyűt.

    ```bash
    wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```

     A telepítő automatikusan letölti az összes függőséget, és létrehozza az üzembe helyezést. A rendszer felszólítja, hogy fogadja el az Azure FarmBeats licencfeltételeket.

     - Ha elfogadja, adja meg az **Y** értéket a következő lépéshez való továbblépéshez.
     - Ha nem fogadja el, adja meg az **N**értéket, és a rendszer megszakítja a telepítést.

1. A parancssorban adjon meg egy hozzáférési jogkivonatot a központi telepítéshez. Másolja a generált kódot, és jelentkezzen be az [eszköz bejelentkezési lapjára](https://microsoft.com/devicelogin) az Azure-beli hitelesítő adataival.

   A telepítő ellenőrzi és elindítja az üzemelő példányt, ami körülbelül 20 percet vesz igénybe.

   Az üzembe helyezés sikeres befejeződése után a következő kimeneti hivatkozásokat fogja kapni:

    - **Datahub URL-cím**: az Azure FarmBeats API-k kipróbálására szolgáló hencegő hivatkozás.
    - **Gyorsító URL-cím**: a felhasználói felület az Azure FarmBeats intelligens Farm-gyorsító felderítéséhez.
    - **Központi telepítési naplófájl**: az üzembe helyezés során létrehozott naplófájl. Szükség esetén a hibaelhárításhoz is használható.

Ha bármilyen problémába ütközik, tekintse át a [hibaelhárítást](troubleshoot-project-farmbeats.md).


## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

### <a name="datahub"></a>Datahub

A Datahub telepítésének befejezését követően megkapja az URL-címet, amely az Azure FarmBeats API-k elérését teszi elérhetővé a következő formátumban: https://\<yourdatahub-web Name >. azurewebsites. net/henceg.

1. A hencegés használatával történő bejelentkezéshez másolja ki és illessze be az URL-címet a böngészőben.
2. Jelentkezzen be Azure Portal hitelesítő adataival.
3. Megtekintheti a hencegés és az összes REST-művelet elvégzését az Azure FarmBeats API-kon. Ez az Azure-FarmBeats sikeres üzembe helyezését jelzi.

### <a name="accelerator"></a>Accelerator

A gyorsított telepítés befejezését követően megkapja az URL-címet, amely az Azure FarmBeats felhasználói felületének elérésére szolgál a következő formátumban: https://\<Accelerator-websites-Name >. azurewebsites. net.

1. A Gyorssegédből való bejelentkezéshez másolja ki és illessze be az URL-címet a böngészőben.
1. Jelentkezzen be Azure Portal hitelesítő adataival.

## <a name="upgrade"></a>Frissítés

A frissítésre vonatkozó utasítások hasonlóak az első telepítéshez. Tegye a következőket:

1. Jelentkezzen be a Azure Portalba, majd válassza ki a kívánt előfizetést és az Azure-bérlőt.
1. Nyissa meg Cloud Shell a Azure Portal legfelső navigációs sávján.

   ![Projekt FarmBeats](./media/prepare-for-deployment/navigation-bar-1.png)

1. A bal oldali **környezet** legördülő listában válassza a **bash**elemet.
1. Ha szükséges, módosítsa a *input. JSON* fájlt, majd töltse fel Cloud Shellba. Frissítheti például az e-mail-címét, hogy milyen értesítéseket szeretne kapni.
1. Írja be vagy illessze be az alábbi két parancsot Cloud Shellba. Ügyeljen rá, hogy módosítsa a *bemeneti. JSON* fájl elérési útját, majd válassza az ENTER billentyűt.

    ```bash
    wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```

   A telepítő automatikusan kéri a szükséges bemenetek futtatását futásidőben.

1. Adjon meg egy hozzáférési jogkivonatot az üzembe helyezéshez. Másolja a generált kódot, és jelentkezzen be az [eszköz bejelentkezési lapjára](https://microsoft.com/devicelogin) az Azure-beli hitelesítő adataival.
1. Adja meg a Sentinel-jelszavát.

   A telepítő most ellenőrzi és megkezdi az erőforrások létrehozását, ami körülbelül 20 percet vesz igénybe.

   Az üzembe helyezés sikeres befejeződése után a következő kimeneti hivatkozásokat fogja kapni:
    - **Datahub URL-cím**: az Azure FarmBeats API-k kipróbálására szolgáló hencegő hivatkozás.
    - **Gyorsító URL-cím**: a felhasználói felület az Azure FarmBeats intelligens Farm-gyorsító felderítéséhez.
    - **Központi telepítési naplófájl**: az üzembe helyezés során létrehozott naplófájl. Szükség esetén a hibaelhárításhoz is használható.

> [!NOTE]
> Jegyezze fel a korábbi értékeket későbbi használatra.


## <a name="uninstall"></a>Eltávolítás

Jelenleg nem támogatjuk az Azure-FarmBeats automatikus eltávolítását a telepítő használatával. A Datahub vagy a Gyorssegéd eltávolításához a Azure Portal törölje azt az erőforráscsoportot, amelyben ezeket az összetevőket telepíti, vagy manuálisan törölje az erőforrásokat.

Ha például két különböző erőforráscsoport esetében telepítette a Datahub és a gyorssegédet, akkor az alábbi módon törölheti ezeket az erőforráscsoportokat:

1. Jelentkezzen be az Azure portálra.
1. Válassza ki a fiókját a jobb felső sarokban, és váltson arra az Azure AD-bérlőre, ahol az Azure FarmBeats üzembe kívánja helyezni.

   > [!NOTE]
   > A Datahub megfelelő működéséhez szükség van a gyorssegédre. A Datahub eltávolítása nem ajánlott a Gyorssegéd eltávolítása nélkül.

1. Válassza az **erőforráscsoportok**lehetőséget, majd adja meg a törölni kívánt Datahub vagy gyorssegéd-erőforráscsoport nevét.
1. Válassza ki az erőforráscsoport nevét. Írja be újra a nevet a megerősítéshez, majd válassza a **Törlés** lehetőséget az erőforráscsoport és az összes mögöttes erőforrás eltávolításához.
   Azt is megteheti, hogy manuálisan törli az egyes erőforrásokat, de nem ajánlott módszert.
1. A Datahub törléséhez vagy eltávolításához nyissa meg az erőforráscsoportot közvetlenül az Azure-ban, majd onnan törölje az erőforráscsoportot.

## <a name="next-steps"></a>Következő lépések

Megtanulta, hogyan helyezheti üzembe az Azure FarmBeats-t. Következő lépésként megtudhatja, hogyan [hozhat létre FarmBeats-farmokat](manage-farms.md#create-farms).
