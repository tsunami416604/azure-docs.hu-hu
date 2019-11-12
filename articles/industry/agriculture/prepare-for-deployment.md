---
title: Az Azure FarmBeats üzembe helyezése
description: A FarmBeats üzembe helyezésének ismertetése
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 55b59802116eb10d2e7eeb3b13ecb3da2d475c6d
ms.sourcegitcommit: 6dec090a6820fb68ac7648cf5fa4a70f45f87e1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/11/2019
ms.locfileid: "73906976"
---
# <a name="deploy-farmbeats"></a>A FarmBeats üzembe helyezése

Ez a cikk az Azure-FarmBeats beállítását ismerteti.

Az Azure FarmBeats egy iparág-specifikus, bővíthető megoldás az adatvezérelt gazdálkodáshoz, amely zökkenőmentes kiépítési és érzékelős eszközöket biztosít az Azure-felhőhöz, a telemetria adatgyűjtéséhez és az összesítéshez. Az Azure FarmBeats különböző érzékelőkkel rendelkezik, mint például a kamerák, a herék, a talaj-érzékelők és a felhőből származó eszközök kezelése, beleértve az Azure infrastruktúráját és szolgáltatásait a IoT-Ready (eszközök internetes hálózata) eszközök számára egy extendible web és Mobile alkalmazás számára, amely biztosítja a vizualizációk, riasztások és adatfelismerések.

> [!NOTE]
> Az Azure FarmBeats csak nyilvános Felhőbeli környezetekben támogatott. A felhőalapú környezettel kapcsolatos további információkért lásd: [Azure](https://azure.microsoft.com/overview/what-is-a-public-cloud/).

Az Azure FarmBeats a következő két összetevővel rendelkezik:

- **Adatközpont – az adatközpont az** Azure FarmBeats platform rétege, amely lehetővé teszi az adatok összeállítását, tárolását, feldolgozását és elemzését meglévő vagy új adatfolyamatokból. Ez a platform-réteg hasznos a mezőgazdasági adatfolyamatok és modellek futtatásához és felépítéséhez.

- A **gyorsító** -gyorsító az Azure FarmBeats megoldási rétege, amely egy beépített alkalmazással mutatja be az Azure FarmBeats képességeit az előre létrehozott mezőgazdasági modellek használatával. Ez a megoldás lehetővé teszi, hogy a farm határain belül hozza létre a gazdaságok határait, és elemzéseket készítsen a mezőgazdasági adatokból.

Az Azure-FarmBeats gyors üzembe helyezése kevesebb mint egy órát vehet igénybe. Az adatközpont és a Gyorssegéd költségei a használat alapján változnak.

## <a name="deployed-resources"></a>Üzembe helyezett erőforrások

Az Azure FarmBeats üzemelő példánya az alábbi felsorolt erőforrásokat hozza létre az előfizetésen belül:

|Sorszám  |Erőforrás neve  |Azure FarmBeats-összetevő  |
|---------|---------|---------|
|1  |       Azure Cosmos DB   |  Adatközpont       |
|2  |    Application Insights      |     Adatközpont/gyorssegéd     |
|3  |Azure Cache for Redis   |Adatközpont   |
|4  |       Azure kulcstartó    |  Adatközpont/gyorssegéd        |
|5  |    Time Series Insights       |     Adatközpont      |
|6 |      EventHub-névtér    |  Adatközpont       |
|7  |    Azure Data Factory V2       |     Adatközpont/gyorssegéd      |
|8  |Batch-fiók    |Adatközpont   |
|9  |       Tárfiók     |  Adatközpont/gyorssegéd        |
|10  |    Logikai alkalmazás        |     Adatközpont      |
|11  |    API-kapcsolatok        |     Adatközpont      |
|12|      App Service      |  Adatközpont/gyorssegéd       |
|13 |    App Service-csomag        |     Adatközpont/gyorssegéd      |
|14 |Azure Maps fiók     |Accelerator    |
|15 |       Time Series Insights      |  Adatközpont     |

Az Azure FarmBeats letölthető az Azure Marketplace-ről. Közvetlenül a Azure Portal érhető el.  

## <a name="create-azure-farmbeats-offer-on-marketplace"></a>Azure FarmBeats-ajánlat létrehozása a piactéren

A következő lépésekkel hozhat létre Azure FarmBeats-ajánlatot a piactéren:

1. Jelentkezzen be a Azure Portalba, és válassza ki a fiókját a jobb felső sarokban, és váltson arra az Azure AD-bérlőre, ahol a Microsoft Azure FarmBeats telepíteni kívánja.
2. Az Azure FarmBeats az Azure piactéren érhető el. A piactér lapon válassza a "Letöltés most" lehetőséget.
3. Válassza a létrehozás lehetőséget, és adja meg a következő adatokat:
  - előfizetés neve.
  - egy meglévő erőforráscsoport-név (csak üres erőforráscsoport), vagy hozzon létre egy új erőforráscsoportot az Azure FarmBeats telepítéséhez. Jegyezze fel ezt az erőforráscsoportot a következő részekben.
4. Az a régió, amelyre telepíteni szeretné az Azure FarmBeats-t. Jelenleg a következő régiókban FarmBeats: USA középső régiója, Nyugat-Európa, USA 2. keleti régiója, Észak-Európa, USA nyugati régiója, Délkelet-Ázsia, USA keleti régiója, Kelet-Ausztrália, USA nyugati régiója 2
5. Kattintson az **OK** gombra.
Megjelenik a Használati feltételek lap. Tekintse át a standard piactér használati feltételeit, vagy válassza ki a hivatkozást a használati feltételek áttekintéséhez.
6. Válassza a **Bezárás**, majd az "Elfogadom" jelölőnégyzetet, majd kattintson a **Létrehozás**gombra.
7. Ezzel sikeresen aláírta az Azure FarmBeats végfelhasználói licencszerződését (EULA) a piactéren.  
7. Az üzembe helyezés folytatásához kövesse az útmutató következő lépéseit.

## <a name="prepare"></a>Előkészítés

Az Azure FarmBeats telepítéséhez a következő engedélyek szükségesek:

- Bérlő: olvasási hozzáférés
- Előfizetés: közreműködő vagy tulajdonos
- Erőforráscsoport: tulajdonos

## <a name="before-you-begin"></a>Előkészületek

Az üzembe helyezés megkezdése előtt győződjön meg arról, hogy a következőkkel rendelkezik:

- Sentinel-fiók
- Azure Active Directory (AD) alkalmazás regisztrálása

## <a name="create-a-sentinel-account"></a>Sentinel-fiók létrehozása    

A Sentinel-fiókkal rendelkező fiókok segítségével letöltheti a Sentinel műholdképeket a hivatalos webhelyről az eszközre. Az alábbi lépéseket követve hozzon létre egy ingyenes fiókot:

Nyissa meg a következőt: https://scihub.copernicus.eu/dhus/#/self-registration. A regisztráció lapon adja meg az utónév, a vezetéknév, a Felhasználónév, a jelszó és a levelezés nevet.
A rendszer visszaigazoló e-mailt küld a regisztrált e-mail-címre. Válassza ki a hivatkozást, és erősítse meg. A regisztrációs folyamat befejeződött.

## <a name="create-azure-ad-app-registration"></a>Azure AD-alkalmazás regisztrációjának létrehozása

Az Azure FarmBeats való hitelesítéshez és engedélyezéshez rendelkeznie kell egy Azure Active Directory-alkalmazás regisztrálásával, amely:

- 1\. eset: a telepítő automatikusan képes létrehozni (amennyiben rendelkezik a szükséges bérlői, előfizetési és erőforráscsoport-hozzáférési engedélyekkel).
- 2\. eset: az Azure-FarmBeats üzembe helyezése előtt hozhat létre és konfigurálhat (manuális lépéseket igényel).

**1. eset**:: Ha hozzáfér egy HRE-alkalmazás regisztrálásához, kihagyhatja ezt a lépést, és engedélyezheti, hogy a telepítő létrehozza az alkalmazás regisztrációját. Folytassa a következő szakasszal: [input. JSON fájl előkészítése](#prepare-input-json-file)

Ha már rendelkezik előfizetéssel, közvetlenül a következő eljárással lehet áthelyezni.

**2. eset**: Ez a módszer az előnyben részesített lépés, ha nem rendelkezik elegendő jogosultsággal az Azure ad-alkalmazás regisztrációjának létrehozásához és konfigurálásához az előfizetésen belül. Kérje meg a rendszergazdát, hogy használja az [Egyéni szkriptet](https://aka.ms/FarmBeatsAADScript), amely segítséget nyújt a rendszergazdának az Azure ad-alkalmazás regisztrálásának automatikus létrehozásában és konfigurálásában a Azure Portal. Ha ezt az egyéni szkriptet PowerShell-környezet használatával szeretné futtatni, a rendszergazdának meg kell osztania egy Azure Active Directory alkalmazás ügyfél-AZONOSÍTÓját és jelszavát. Jegyezze fel ezeket az értékeket.

Az Azure AD-alkalmazás regisztrációs parancsfájljának futtatásához kövesse az alábbi lépéseket:

1. [Parancsfájl](https://aka.ms/FarmBeatsAADScript)letöltése.
2. Jelentkezzen be Azure Portalra, és válassza ki az előfizetését és az AD-bérlőt.
3. Indítsa el a Cloud Shellt az Azure Portal felső navigációs szakaszából.

    ![A Project Farm veri](./media/prepare-for-deployment/navigation-bar-1.png)


4. A rendszer első alkalommal kéri a felhasználókat, hogy válasszon egy előfizetést a Storage-fiók létrehozásához és a fájlmegosztás Microsoft Azureához. Kattintson a **Create storage** (Tároló létrehozása) gombra.
5. A felhasználók első alkalommal való megadását a rendszer az előnyben részesített rendszerhéj-élmény – bash vagy a PowerShell használatával fogja kérni. Válassza a PowerShell lehetőséget.
6. Töltse fel a szkriptet (az 1. lépésből) a Cloud Shellba, és jegyezze fel a feltöltött fájl helyét.

    > [!NOTE]
    > Alapértelmezés szerint a rendszer feltölti a saját könyvtárába.

    Használja a következő parancsfájlt:

    ```azurepowershell-interactive
    ./create_aad_script.ps1
    ```
7. Jegyezze fel az Azure AD-alkalmazás AZONOSÍTÓját és az ügyfél titkos kulcsát, hogy megossza az Azure FarmBeats-t üzembe helyező személlyel.

### <a name="prepare-input-json-file"></a>Bemeneti JSON-fájl előkészítése

A telepítés részeként hozzon létre egy input. JSON fájlt a következőképpen:

    ```
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

Ez a fájl a bemeneti fájlja Azure Cloud Shell és azokat a paramétereket, amelyek értékeit a rendszer a telepítés során használja. A JSON-ban lévő összes paramétert megfelelő értékekkel kell helyettesíteni, vagy el kell távolítani azokat. Ha eltávolítja, a telepítő a telepítés során kérni fogja


A fájl előkészítése előtt tekintse át a paramétereket.

|Parancs | Leírás|
|--- | ---|
|SKU  | A lehetőséget nyújt az Azure-FarmBeats-összetevők vagy azok mindkét összetevőjének letöltésére. Meghatározza a letölteni kívánt összetevőket. Csak az adatközpont telepítéséhez használja a "onlydatabhub" kifejezést. Az adatközpont és a gyorsító telepítéséhez használja a "mindkettő" lehetőséget.|
|subscriptionId | A FarmBeats telepítésére vonatkozó előfizetést adja meg|
|datahubResourceGroup| Az adatközpont erőforrásainak erőforráscsoport-neve|
|location |A hely, ahol létre szeretné hozni az erőforrásokat|
|acceleratorWebsiteName |Egyedi URL-előtag az adatközpont nevének meghívásához|
|acceleratorResourceGroup  | Egyedi URL-előtag a Gyorssegéd-webhely elnevezéséhez.|
|datahubWebsiteName  | UUnique URL-előtag az adatközpont webhelyének elnevezéséhez. |
|sentinelUsername | a bejelentkezéshez használandó Felhasználónév: https://scihub.copernicus.eu/dhus/#/self-registration.|
|notificationEmailAddress  | E-mail-cím, amely az adatközponton belül konfigurált riasztások értesítéseit fogadja.|
|updateIfExists|Választható A input. JSON fájlban szerepeltetni kívánt paraméter csak akkor lehetséges, ha egy meglévő FarmBeats-példányt szeretne frissíteni. A frissítéshez további részleteket például a következő címen talál:. Az erőforráscsoportok neveinek, helyeinek stb. azonosnak kell lenniük.|
|aadAppClientId | [Nem**kötelező**] A input. JSON fájlban szerepeltetni kívánt paraméter csak akkor, ha az Azure AD-alkalmazás már létezik.  |
|aadAppClientSecret  | [Nem**kötelező**] A input. JSON fájlban szerepeltetni kívánt paraméter csak akkor, ha az Azure AD-alkalmazás már létezik.|

## <a name="deploy-within-cloud-shell-browser-based-command-line"></a>Üzembe helyezés Cloud Shell böngészőalapú parancssoron belül

A fenti piactér-munkafolyamat részeként létre kell hoznia egy erőforráscsoportot, és alá kell írnia a végfelhasználói licencszerződést, amely a tényleges telepítés részeként újra áttekinthető. A központi telepítés a bash-környezet használatával Azure Cloud Shell (böngészőalapú parancssoron) keresztül végezhető el. Folytassa a következő szakasszal a Cloud Shell használatával történő üzembe helyezéshez.

> [!NOTE]
> Az inaktív Cloud Shell munkamenetek 20 perc elteltével lejárnak. Próbálja meg végrehajtani a központi telepítést.

1. Jelentkezzen be Azure Portalba, és válassza ki a kívánt előfizetést és AD-bérlőt.
2. Indítsa el a Cloud Shellt az Azure Portal felső navigációs szakaszából.
3. Ha első alkalommal használja a Cloud Shell, a rendszer kérni fogja, hogy válasszon egy előfizetést a Storage-fiók létrehozásához és a fájlmegosztás Microsoft Azure.
4. Válassza a **tároló létrehozása**lehetőséget.  

Válassza ki a környezetet Bashként (és ne PowerShell-ként).

## <a name="deployment-scenario-1"></a>1\. üzembe helyezési forgatókönyv

A telepítő létrehozza a Azure AD alkalmazás regisztrációt (1. eset)

1. Másolja a következő sablont, és nevezze el a input. JSON névre.  
Példa JSON-bemenetre:

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

2. Mentse a fájlt, és jegyezze fel az elérési utat (a helyi számítógépen).
3. Lépjen a Azure Cloud Shell és a sikeres hitelesítés után válassza ki a feltöltést (lásd az alábbi képen látható Kiemelt ikont), és töltse fel az input. JSON fájlt Cloud Shell tárolóba.  

    ![A Project Farm veri](./media/prepare-for-deployment/bash-2-1.png)

4. Nyissa meg a saját könyvtárat a Cloud shellben. Alapértelmezés szerint ez a/Home/<username>
5. Írja be vagy illessze be a következő parancsot a Cloud Shellba. Ügyeljen rá, hogy módosítsa a bemenet elérési útját. JSON-fájl, majd nyomja le az ENTER billentyűt.

      ```azurepowershell-interactive
      wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
     ```
     A telepítő automatikusan letölti az összes függőséget, és létrehozza az üzembe helyezést. A rendszer kérni fogja, hogy fogadja el az Azure FarmBeats végfelhasználói licencszerződését (EULA).

     - Ha elfogadja, adja meg az "Y" értéket, és folytassa a következő lépéssel.
     - Ha nem fogadja el a feltételeket, és a központi telepítés leáll, adja meg az "N" értéket.

6. Ezután meg kell adnia egy hozzáférési jogkivonatot a központi telepítéshez. Másolja a generált kódot, és jelentkezzen be https://microsoft.com/devicelogin az Azure-beli hitelesítő adataival.

    > [!NOTE]
    > A jogkivonat 60 perc után lejár. Ha lejár, a telepítési parancs ismételt beírásával indíthatja újra.

7. Ha a rendszer kéri, adja meg a Sentinel-fiók jelszavát.
8. A telepítő most ellenőrzi és elindítja az üzembe helyezést, ami körülbelül 20 percet vesz igénybe.
9. Miután az üzembe helyezés sikeres volt, a következő kimeneti hivatkozásokat fogja kapni:

 - **Adatközpont URL-címe**: hencegő hivatkozás az Azure FarmBeats API-k kipróbálásához.
 - **Gyorsító URL-cím**: felhasználói felület az Azure FarmBeats Smart Farm Accelerator szolgáltatásának megismeréséhez.
 - **Telepítő naplófájlja**– az üzembe helyezés során létrehozott naplófájl. Szükség esetén hibaelhárításra is használható.

## <a name="deployment-scenario-2"></a>2\. üzembe helyezési forgatókönyv

A rendszer a meglévő Azure Active Directory alkalmazás-regisztrációt használja a telepítéshez (2. eset)

1. Másolja az alábbi JSON-fájlt, amely tartalmazza az Azure-alkalmazás ügyfél-AZONOSÍTÓját és jelszavát a input. JSON fájlban, majd mentse.

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

Kövesse a lépések további lépéseit:

2. Jegyezze fel a bemeneti. JSON fájl elérési útját (a helyi számítógépen).
3. Lépjen a Azure Cloud Shell ismét, és sikeresen megtörtént a hitelesítés, válassza a feltöltés gombot (lásd az alábbi képen látható Kiemelt ikont), és töltse fel az input. JSON fájlt Cloud Shell tárterületre.

    ![A Project Farm veri](./media/prepare-for-deployment/bash-2-1.png)

4. Nyissa meg a saját könyvtárat a Cloud shellben. Alapértelmezés szerint ez a/Home/<username>
5. Írja be vagy illessze be a következő parancsot a Cloud Shellba. Ügyeljen rá, hogy módosítsa a bemenet elérési útját. JSON-fájl, majd nyomja le az ENTER billentyűt.

    ```azurepowershell-interactive
    wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```

Kövesse a képernyőn megjelenő utasításokat.

6. A parancsfájl automatikusan letölti az összes függőséget, és létrehozza a telepítőt.
7. A rendszer kérni fogja, hogy olvassa el és fogadja el az Azure FarmBeats végfelhasználói licencszerződését (EULA).

    - Ha elfogadja a t, adja meg az "Y" értéket, és folytassa a következő lépéssel.
    - Ha nem fogadja el a feltételeket, és a központi telepítés leáll, adja meg az "N" értéket.

8. A rendszer kérni fogja, hogy adjon meg egy hozzáférési jogkivonatot a központi telepítéshez. Másolja a generált kódot, és jelentkezzen be https://microsoft.com/devicelogin Azure-beli hitelesítő adataival.
9. A telepítő most ellenőrzi és megkezdi az erőforrások létrehozását, ami körülbelül 20 percet vesz igénybe. A munkamenet aktív állapotban tartása Cloud Shell ebben az időszakban.
10. Az üzembe helyezés sikeres elvégzése után a következő kimeneti hivatkozásokat fogja kapni:

 - **Adatközpont URL-címe**: hencegő hivatkozás a FarmBeats API-k kipróbálásához.
 - **Gyorsító URL-cím**: felhasználói felület a FarmBeats intelligens Farm gyorssegédének megismeréséhez.
 - **Központi telepítési naplófájl**: az üzembe helyezés során létrehozott naplófájl. Szükség esetén hibaelhárításra is használható.

Ha bármilyen problémába ütközik, tekintse át a [hibaelhárítást](troubleshoot-project-farmbeats.md).


## <a name="validate-deployment"></a>Központi telepítés ellenőrzése

### <a name="data-hub"></a>Adatközpont

Az adatközpont telepítésének befejezése után megkapja az URL-címet az Azure FarmBeats API-k eléréséhez a hencegő felületen keresztül a következő formátumban: https://\<yourdatahub-web-Name >. azurewebsites. net

1. A hencegés használatával történő bejelentkezéshez másolja ki és illessze be az URL-címet a böngészőben.
2. Jelentkezzen be Azure Portal hitelesítő adataival.
3. Józan ész teszt (nem kötelező)

     - Képes sikeresen bejelentkezni a hencegő portálra az adatközpont-hivatkozás használatával, amelyet sikeres központi telepítésként kapott kimenetként.
     - Kiterjesztett típusok – API – válassza ki a "kipróbálható/Execute" lehetőséget.
     - Meg kell kapnia a 200-es kiszolgáló-reagálási kódot, és nem kivételt, például 403 "jogosulatlan felhasználó".

### <a name="accelerator"></a>Accelerator

Miután befejeződött a Gyorssegéd telepítése, megkapja az URL-címet, hogy hozzáférhessen a FarmBeats felhasználói felületéhez a következő formátumban: https://\<Accelerator-websites-Name >. azurewebsites. net

1. A Gyorssegédből való bejelentkezéshez másolja ki és illessze be az URL-címet a böngészőben.
2. Jelentkezzen be Azure Portal hitelesítő adataival.

## <a name="upgrade"></a>Frissítés

A verziófrissítés lépései hasonlóak az első telepítéshez. Kövesse az alábbi lépéseket:

1. Jelentkezzen be Azure Portal, és válassza ki a kívánt előfizetést és AD-bérlőt.
2. Indítsa el a Cloud Shellt az Azure Portal felső navigációs szakaszából.

   ![A Project Farm veri](./media/prepare-for-deployment/navigation-bar-1.png)

3. A rendszerhéj bal oldalán található legördülő listából válassza ki a környezetet "bash"-ként.
4. Csak szükség esetén módosítsa a bemeneti. JSON fájlt, és töltse fel a Azure Cloud Shell. Frissítheti például az e-mail-címét, hogy milyen értesítéseket szeretne kapni.
5. Töltse fel az input. JSON fájlt Azure Cloud Shellba.
6. Írja be vagy illessze be az alábbi két parancsot a Cloud Shellba. Ügyeljen arra, hogy módosítsa a bemeneti. JSON-fájl elérési útját, majd nyomja le az ENTER billentyűt.

    ```azurepowershell-interactive
    wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```
Kövesse a képernyőn megjelenő utasításokat:

7. A telepítő futtatáskor automatikusan kéri a szükséges bemeneti adatokat:
8. Adjon meg egy hozzáférési jogkivonatot az üzembe helyezéshez. Másolja a generált kódot, és jelentkezzen be https://microsoft.com/devicelogin Azure-beli hitelesítő adataival.
9. Sentinel-jelszó
10. A telepítő most ellenőrzi és megkezdi az erőforrások létrehozását, ami körülbelül 20 percet vesz igénybe.
11. Miután az üzembe helyezés sikeres volt, a következő kimeneti hivatkozásokat fogja kapni:
 - **Adatközpont URL-címe**: hencegő hivatkozás a FarmBeats API-k kipróbálásához.
 - **Gyorsító URL-cím**: felhasználói felület a FarmBeats intelligens Farm gyorssegédének megismeréséhez.
 - **Központi telepítési naplófájl**: az üzembe helyezés során menti a naplókat. Hibaelhárításhoz is használható.

> [!NOTE]
> Jegyezze fel a fenti értékeket a jövőbeli használatra.


## <a name="uninstall"></a>Eltávolítás

Jelenleg nem támogatjuk a FarmBeats automatikus eltávolítását a telepítő használatával. Az adatközpont vagy a Gyorssegéd eltávolításához a Azure Portal törölje azt az erőforráscsoportot, amelyben ezeket az összetevőket telepíti, vagy manuálisan törölje az erőforrásokat.

Ha például két különböző erőforráscsoport esetében telepítette az adatközpontot és a gyorsító eszközt, a következőképpen törölheti ezeket az erőforráscsoportokat:

1. Jelentkezzen be a Azure Portalba.
2. Válassza ki a fiókját a jobb felső sarokban, és váltson arra a kívánt Azure AD-bérlőre, ahol a Microsoft-FarmBeats telepíteni kívánja.

   > [!NOTE]
   > A Gyorssegéd megfelelő működéséhez szükség van az adatközpontra. Nem javasoljuk, hogy a Gyorssegéd eltávolítása nélkül távolítsa el az adatközpontot.

3. Válassza az erőforráscsoportok lehetőséget, majd írja be a törölni kívánt adatközpont vagy gyorssegéd-erőforráscsoport nevét.
4. Válassza ki az erőforráscsoport nevét. Írja be újból a nevet a név mezőbe, majd a Törlés elemre kattintva távolítsa el az erőforráscsoportot és annak összes mögöttes erőforrását.
5. Azt is megteheti, hogy manuálisan törli az egyes erőforrásokat, ami nem ajánlott.
7. Az adatközpont törléséhez/eltávolításához lépjen az Azure-ba közvetlenül az Azure-on, és onnan törölje az erőforráscsoportot.

## <a name="next-steps"></a>Következő lépések

Telepítette az Azure FarmBeats-t. Most Ismerkedjen meg a [farmok létrehozásával](manage-farms.md#create-farms).
