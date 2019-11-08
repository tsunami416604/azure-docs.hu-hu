---
title: Az Azure FarmBeats üzembe helyezése
description: A FarmBeats üzembe helyezésének ismertetése
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: c609285b727414b4849c9ef6654406a035005bb1
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73797724"
---
# <a name="deploy-farmbeats"></a>FarmBeats üzembe helyezése

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

## <a name="prepare"></a>Előkészítés

Az Azure FarmBeats telepítéséhez a következő engedélyek szükségesek:

- Bérlő: olvasási hozzáférés
- Előfizetés: tulajdonos
- Erőforráscsoport: tulajdonos

## <a name="before-you-begin"></a>Előkészületek

Az üzembe helyezés megkezdése előtt győződjön meg arról, hogy a következőkkel rendelkezik:

- Sentinel-fiók
- Azure Active Directory (alkalmazás-regisztráció)
- Azure-FarmBeats

## <a name="create-a-sentinel-account"></a>Sentinel-fiók létrehozása    

A Sentinel-fiókkal rendelkező fiókok segítségével letöltheti a Sentinel műholdképeket a hivatalos webhelyről az eszközre. Az alábbi lépéseket követve hozzon létre egy ingyenes fiókot:

1. Nyissa meg a következőt: https://scihub.copernicus.eu/dhus/#/self-registration. A regisztráció lapon adja meg az utónév, a vezetéknév, a Felhasználónév, a jelszó és a levelezés nevet.
2. A **tartomány kiválasztása** legördülő menüben válassza a **terület**lehetőséget.
3. A **használat kiválasztása** legördülő menüben válassza az **oktatás**lehetőséget.
4. Az **ország kiválasztása** legördülő menüben válassza ki az országot.
5. A regisztrációs folyamat befejezéséhez válassza a **regisztráció** lehetőséget.

A rendszer visszaigazoló e-mailt küld a regisztrált e-mail-címre. Válassza ki a hivatkozást, és erősítse meg. A regisztrációs folyamat befejeződött.

## <a name="create-azure-ad-app-registration"></a>Azure AD-alkalmazás regisztrációjának létrehozása

Az Azure FarmBeats való hitelesítéshez és engedélyezéshez rendelkeznie kell egy Azure Active Directory-alkalmazás regisztrálásával, amely:

- 1\. eset: a telepítő automatikusan képes létrehozni (amennyiben rendelkezik a szükséges bérlői, előfizetési és erőforráscsoport-hozzáférési engedélyekkel).
- 2\. eset: az Azure-FarmBeats üzembe helyezése előtt hozhat létre és konfigurálhat (manuális lépéseket igényel).

**1. eset**: a telepítő feltételezi, hogy rendelkezik egy Azure Active Directory-alkalmazás regisztrációjának a kívánt előfizetésen belüli létrehozásához szükséges jogokkal. A regisztráláshoz jelentkezzen be a portálra, lépjen az **Azure Active directory** > **alkalmazás-regisztráció** > **új regisztráció**elemre.

Ha már rendelkezik előfizetéssel, közvetlenül a következő eljárással lehet áthelyezni.

**2. eset**: Ez a módszer az előnyben részesített lépés, ha nem rendelkezik elegendő jogosultsággal az Azure ad-alkalmazás regisztrációjának létrehozásához és konfigurálásához az előfizetésen belül. Kérje meg a rendszergazdát, hogy használja az [Egyéni szkriptet](https://aka.ms/FarmBeatsAADScript), amely segítséget nyújt a rendszergazdának az Azure ad-alkalmazás regisztrálásának automatikus létrehozásában és konfigurálásában a Azure Portal. Ha ezt az egyéni szkriptet PowerShell-környezet használatával szeretné futtatni, a rendszergazdának meg kell osztania egy Azure Active Directory alkalmazás ügyfél-AZONOSÍTÓját és jelszavát. Jegyezze fel ezeket az értékeket.

Az Azure AD-alkalmazás regisztrációs parancsfájljának futtatásához kövesse az alábbi lépéseket:

1. Szerezze be a regisztrációs [parancsfájlt](https://aka.ms/FarmBeatsAADScript).
2. Jelentkezzen be Azure Portalra, és válassza ki az előfizetését és az AD-bérlőt.
3. Indítsa el a Cloud Shellt az Azure Portal felső navigációs szakaszából.

    ![A Project Farm veri](./media/prepare-for-deployment/navigation-bar-1.png)


4. A rendszer első alkalommal kéri a felhasználókat, hogy válasszon egy előfizetést a Storage-fiók létrehozásához és a fájlmegosztás Microsoft Azureához. Kattintson a **Create storage** (Tároló létrehozása) gombra.
5. A felhasználók első alkalommal való megadását a rendszer az előnyben részesített rendszerhéj-élmény – bash vagy a PowerShell használatával fogja kérni. Válassza a PowerShell lehetőséget.
6. A Cloud Shell írja be az alábbi parancsokat a szkript futtatásához.

    ```powershell
    ./create_aad_script.ps1
    ```
7. Jegyezze fel az Azure AD-alkalmazás AZONOSÍTÓját és az ügyfél titkos kulcsát, hogy megossza az Azure FarmBeats-t üzembe helyező személlyel.

## <a name="create-azure-farmbeats-offer-on-marketplace"></a>Azure FarmBeats-ajánlat létrehozása a piactéren

A következő lépésekkel hozhat létre Azure FarmBeats-ajánlatot a piactéren:

1. Jelentkezzen be a **Azure Portalba** , és válassza ki a fiókját a jobb felső sarokban, és váltson arra az Azure ad-bérlőre, ahol a Microsoft Azure FarmBeats telepíteni kívánja.
2. Válassza a **Keresés/piactér** lehetőséget, vagy **hozzon létre erőforrásokat**. Az ajánlat részleteinek megtekintéséhez írja be a **FarmBeats** . A következő lépések elvégzése előtt töltse le az aka.ms hiperhivatkozások segítségével elérhető útmutatókat ezen az oldalon.
3. Válassza a **Létrehozás** lehetőséget, és adja meg a következő adatokat:

   - Adja meg az előfizetés nevét.
   - Adjon meg egy meglévő erőforráscsoport-nevet (csak üres erőforráscsoport), vagy hozzon létre egy új erőforráscsoportot az Azure FarmBeats telepítéséhez. Jegyezze fel ezt az erőforráscsoportot, hogy az input. JSON fájlban egy későbbi időpontban adja meg.
   - Adja meg azt a régiót, amelyre telepíteni szeretné az Azure FarmBeats-t. A FarmBeats jelenleg a következő régiókban telepíthető: USA középső régiója, Nyugat-Európa, USA 2. keleti régiója, Észak-Európa, USA nyugati régiója, Délkelet-Ázsia, USA keleti régiója, Kelet-Ausztrália, USA 2. nyugati régiója.
4. Válassza az **OK gombot**, amely átirányítja önt a használati feltételek lapra. Tekintse át a standard piactér használati feltételeit, vagy válassza ki a hivatkozást a használati feltételek áttekintéséhez.
5. Válassza a **Bezárás**, majd az "Elfogadom" jelölőnégyzetet, majd kattintson a **Létrehozás**gombra.
6. Ezzel sikeresen aláírta az Azure FarmBeats végfelhasználói licencszerződését (EULA) a piactéren. Az üzembe helyezés folytatásához kövesse az útmutató következő lépéseit.

### <a name="prepare-inputjson-file"></a>Input. JSON fájl előkészítése

Ennek a fájlnak a bemeneti fájlja Azure Cloud Shell és paraméterek, amelyek értékeit a fájlon belül meg kell adni, mielőtt a feltöltés nem fog megjelenni az üzembe helyezés során, így időt takaríthat meg.  

> [!NOTE]
> Ez a fájl a Azure Cloud Shell.  Ha időt szeretne megtakarítani az üzembe helyezés során, a rendszer nem kéri a fájlhoz hozzáadott paraméterek megadását. A rendszer kérni fogja a kihagyott paraméterek megadását.

A fájl előkészítése előtt tekintse át a paramétereket.

|Parancs | Leírás|
|--- | ---|
|SKU  | A lehetőséget nyújt az Azure-FarmBeats-összetevők vagy azok mindkét összetevőjének letöltésére. Meghatározza a letölteni kívánt összetevőket. Csak az adatközpont telepítéséhez használja a "onlydatabhub" kifejezést. Az adatközpont és a Gyorssegéd telepítéséhez használja a "mindkettő" lehetőséget.|
|SubscriptionId  | A FarmBeats telepítésére vonatkozó előfizetést adja meg|
|"datahubResourceGroup"  | Az adatközpont erőforrásainak erőforráscsoport-neve.|
|"datahubLocation" | Az a hely, ahol az adatközpont erőforrásait tárolni szeretné.|
|"acceleratorWebsiteName"  |Egyedi URL-előtag az adatközpont nevének meghívásához
Hencegő webhely. Az alapértelmezett érték az adatközpont erőforráscsoport-neve. Az alapértelmezett érték folytatásához nyomja le az ENTER billentyűt.|
|"acceleratorResourceGroup"  | Az adatközpont erőforrásainak erőforráscsoport-neve. |
|"acceleratorLocation"  | Az adatközpont-erőforrások tárolásának helye
"acceleratorWebsiteName"  | Egyedi URL-előtag a Gyorssegéd-webhely elnevezéséhez. Az alapértelmezett érték a Gyorssegéd. Az alapértelmezett érték folytatásához nyomja le az ENTER billentyűt.|
|''sentinelUsername'' | a bejelentkezéshez használandó Felhasználónév: https://scihub.copernicus.eu/dhus/#/self-registration.|
|"sentinelPassword"  | Bejelentkezéshez használt jelszó: https://scihub.copernicus.eu/dhus/#/self-registration.|
|"farmbeatsAppId"  | A Team Azure FarmBeats által megosztható értékek.  |
|"farmbeatsPassword"  | A Team Azure FarmBeats által megosztható értékek.|
|"notificationEmailAddress"  | E-mail-cím, amely az adatközponton belül konfigurált riasztások értesítéseit fogadja.|
|"frissítés" aadAppClientId ""  |[Nem**kötelező**] A input. JSON fájlban szerepeltetni kívánt paraméter csak akkor, ha az Azure AD-alkalmazás már létezik.  – Igaz/hamis. False (hamis) – az első telepítéshez és az igaz értékre a frissítési forgatókönyv esetén.|
|"aadAppClientId"  | [Nem**kötelező**] A input. JSON fájlban szerepeltetni kívánt paraméter csak akkor, ha az Azure AD-alkalmazás már létezik.  |
|"aadAppClientSecret"   | [Nem**kötelező**] A input. JSON fájlban szerepeltetni kívánt paraméter csak akkor, ha az Azure AD-alkalmazás már létezik.|


Példa JSON-bemenetre:

```json
{  
   "sku":"both", 
   "subscriptionId": "da9xxxec-dxxf-4xxc-xxx21-xxx3ee7xxxxx", 
   "datahubResourceGroup": "dummy-test-dh1", 
   "datahubLocation": "westus2", 
   "datahubWebsiteName": "dummy-test-dh1", 
   "acceleratorResourceGroup": "dummy-test-acc1", 
   "acceleratorLocation": "westus2", 
   "acceleratorWebsiteName": "dummy-test-acc1", 
   "sentinelUsername": "dummy-dev", 
   "farmbeatsAppId": "c3cb3xxx-27xx-4xxb-8xx6-3xxx2xxdxxx5c", 
   "notificationEmailAddress": "dummy@microsoft.com", 
   "updateIfExists": true
}
```
## <a name="deploy-within-cloud-shell-browser-based-command-line"></a>Üzembe helyezés Cloud Shell böngészőalapú parancssoron belül

A Piactéri munkafolyamatok részeként létrehozott egy erőforráscsoportot, és aláírta a végfelhasználói licencszerződést, amely a tényleges üzembe helyezés részeként ismét áttekinthető. A központi telepítés a bash-környezet használatával Azure Cloud Shell (böngészőalapú parancssoron) keresztül történik.  

> [!NOTE]
> Az inaktív Cloud Shell munkamenetek 20 perc elteltével lejárnak. Próbálja meg végrehajtani a központi telepítést.

1. Jelentkezzen be az **Azure** Portalra, és válassza ki a kívánt előfizetést és ad-bérlőt.
2. **Cloud Shell** elindítása az **Azure** Portal felső navigációs felületén.

   ![A Project Farm veri](./media/prepare-for-deployment/navigation-bar-1.png)

3. Válasszon egy előfizetést, és hozzon létre egy Storage-fiókot, és Microsoft Azure a fájlok megosztását.
4. Kattintson a **Create storage** (Tároló létrehozása) gombra.
5. Válassza ki a környezet legördülő listát a rendszerhéj ablakának bal oldalán (bash).

   ![A Project Farm veri](./media/prepare-for-deployment/bash-1-1.png)

### <a name="deployment-scenario-1"></a>1\. üzembe helyezési forgatókönyv

A telepítő létrehozza az Azure AD-t (az AD-bérlő olvasási engedélyei vannak).  

1. Töltse le a [input. JSON](https://aka.ms/PPInputJsonTemplate) sablont. Adja meg az Azure-alkalmazás ügyfél-AZONOSÍTÓját és jelszavát a bemeneti. JSON fájlban, majd mentse azt.
2. Nyissa meg a letöltött fájlt egy Jegyzettömbben, és töltse ki a fájlt az értékek megadásával.

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
    "updateIfExists":true
    }

    ```

    > [!NOTE]
    > Ezt a lépést kihagyhatja, és a bemenetek futás közben is megjelenhetnek.

3. Mentse a fájlt, és jegyezze fel az elérési utat (a helyi számítógépen).  
4. Lépjen a Azure Cloud Shell és a sikeres hitelesítés után válassza ki a feltöltést (lásd az alábbi képen látható Kiemelt ikont), és töltse fel az input. JSON fájlt Cloud Shell tárolóba. Nem kell átadnia az Azure AD-paramétert a JSON-n belül, mivel a telepítő létrehozza és konfigurálja az Azure AD-alkalmazás regisztrációját.

   ![A Project Farm veri](./media/prepare-for-deployment/bash-2-1.png)

5. Írja be vagy illessze be az "üzembehelyezési parancsot" a Cloud Shellba. Ügyeljen rá, hogy módosítsa a bemenet elérési útját. JSON-fájl, majd nyomja le az ENTER billentyűt.  

    ```
    wget -N -O farmbeats-installer.sh https://aka.ms/FB_1.2.0 && bash farmbeats-installer.sh> /home/dummyuser/input.json

    ```
   A parancsfájl automatikusan letölti az összes függőséget, és létrehozza a telepítőt. Ezután meg kell egyeznie az Azure FarmBeats végfelhasználói licencszerződésével (EULA).

   - Ha elfogadja, adja meg az "Y" értéket, és folytassa a következő lépéssel.
   - Ha nem fogadja el a feltételeket, és a központi telepítés leáll, adja meg az "N" értéket.

   Ezután meg kell adnia egy hozzáférési jogkivonatot a központi telepítéshez. Másolja a generált kódot, és jelentkezzen be https://microsoft.com/devicelogin az Azure-beli hitelesítő adataival.

   > [!NOTE]
   > A kód 60 perc után lejár. Ha lejár, a telepítési parancs ismételt beírásával indíthatja újra.

6. A következő üzenetben adja meg a Sentinel-fiók jelszavát.
7. A telepítő most ellenőrzi és elindítja az üzembe helyezést, ami körülbelül 20 percet vesz igénybe.
8. Miután az üzembe helyezés sikeres volt, a következő kimeneti hivatkozásokat fogja kapni:
    - **Adatközpont URL-címe**: hencegő hivatkozás az Azure FarmBeats API-k kipróbálásához.
    - **Gyorsító URL-cím**: felhasználói felület az Azure FarmBeats Smart Farm Accelerator szolgáltatásának megismeréséhez.
    - **Telepítő naplófájlja**– az üzembe helyezés során létrehozott naplófájl. Hibaelhárításhoz is használható.

    - Ha elfogadja, adja meg az "Y" értéket, és folytassa a következő lépéssel.
    - Ha nem fogadja el a feltételeket, és a központi telepítés leáll, adja meg az "N" értéket.

   Ezután meg kell adnia egy hozzáférési jogkivonatot a központi telepítéshez. Másolja a generált kódot, és jelentkezzen be https://microsoft.com/devicelogin Azure-beli hitelesítő adataival.

   > [!NOTE]
    > Jegyezze fel ezeket, és tartsa kéznél a telepítési naplófájl elérési útját a későbbi használat érdekében.


### <a name="deployment-scenario-2"></a>2\. üzembe helyezési forgatókönyv

A rendszer a meglévő Azure Active Directory alkalmazás-regisztrációt használja a telepítéshez.

1. Töltse le a [bemeneti JSON](https://aka.ms/PPInputJsonTemplate) -t az Azure-alkalmazás ügyfél-azonosítója és jelszava a input. JSON fájlban, majd mentse.

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

4. Írja be vagy illessze be a *telepítési parancsot* a Cloud Shellba. Ügyeljen rá, hogy módosítsa a bemenet elérési útját. JSON-fájl, majd nyomja le az ENTER billentyűt.

    ```
    wget -N -O farmbeats-installer.sh https://aka.ms/FB_1.2.0 && bash farmbeats-installer.sh> /home/dummyuser/input.json

    ```
   Kövesse a lépések további lépéseit:

5. A parancsfájl automatikusan letölti az összes függőséget, és létrehozza a telepítőt.
6. A rendszer felszólítja, hogy olvassa el és fogadja el az Azure FarmBeats végfelhasználói licencszerződését (EULA).

   - Ha elfogadja, adja meg az "Y" értéket, és folytassa a következő lépéssel.
   - Ha nem fogadja el a feltételeket, és a központi telepítés leáll, adja meg az "N" értéket.

7. A rendszer kérni fogja, hogy adjon meg egy hozzáférési jogkivonatot a központi telepítéshez. Másolja a generált kódot, és jelentkezzen be https://microsoft.com/devicelogin Azure-beli hitelesítő adataival.
8. A telepítő most ellenőrzi és megkezdi az erőforrások létrehozását, ami körülbelül 20 percet vesz igénybe. A munkamenet aktív állapotban tartása Cloud Shell ebben az időszakban.
9. Az üzembe helyezés sikeres elvégzése után a következő kimeneti hivatkozásokat fogja kapni:
   - **Adatközpont URL-címe**: hencegő hivatkozás a FarmBeats API-k kipróbálásához.
   - **Gyorsító URL-cím**: felhasználói felület a FarmBeats intelligens Farm gyorssegédének megismeréséhez.
   - **Központi telepítési naplófájl**: a rendszer az üzembe helyezés során menti a naplókat, és a hibaelhárításhoz is használható.

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
3. Futtasson egy nem kötelező épelméjű tesztet.

    - Ellenőrizze, hogy tud-e sikeresen bejelentkezni a Gyorssegéd-portálra olyan gyorssegéd-hivatkozás használatával, amelyet kimenetként kapott a sikeres telepítéshez.
    - Válassza a **farm létrehozása**lehetőséget.
    - A "?" ikon alatt nyissa meg a FarmBeats útmutatókat az **első lépések** gomb használatával.

## <a name="upgrade"></a>Frissítés

A verziófrissítés lépései hasonlóak az első telepítéshez. Kövesse az alábbi lépéseket:

1. Jelentkezzen be Azure Portal, és válassza ki a kívánt előfizetést és AD-bérlőt.
2. Indítsa el a Cloud Shellt az Azure Portal felső navigációs szakaszából.

   ![A Project Farm veri](./media/prepare-for-deployment/navigation-bar-1.png)

3. Válasszon egy előfizetést egy Storage-fiók létrehozásához, valamint egy Azure Files fájlmegosztást.
4. Kattintson a **Create storage** (Tároló létrehozása) gombra.
5. Válassza ki a környezetet a shell (bash) bal oldalán található legördülő menüből.
6. Csak szükség esetén módosítsa a bemeneti. JSON fájlt, és töltse fel a Azure Cloud Shell. Frissítheti például az e-mail-címét, hogy milyen értesítéseket szeretne kapni.
7. Jegyezze fel a bemeneti. JSON fájl elérési útját (a helyi számítógépen).
8. Lépjen Azure Cloud Shell. A sikeres hitelesítés után válassza a feltöltés gombot (lásd az alábbi képen látható Kiemelt ikont), és töltse fel az input. JSON fájlt Cloud Shell tárolóba.

   ![A Project Farm veri](./media/prepare-for-deployment/bash-2-1.png)

9. Írja be vagy illessze be a **telepítési parancsot** a Cloud Shellba. Ügyeljen rá, hogy módosítsa a bemenet elérési útját. JSON-fájl, majd nyomja le az ENTER billentyűt.

    ```
    wget -N -O farmbeats-installer.sh https://aka.ms/FB_1.2.0 && bash farmbeats-installer.sh> /home/dummyuser/input.json

    ```
    Kövesse a lépések további lépéseit:

10. A telepítő automatikusan kéri a szükséges bemeneteket a futtatáskor:

    - Adjon meg egy hozzáférési jogkivonatot az üzembe helyezéshez. Másolja a generált kódot, és jelentkezzen be https://microsoft.com/devicelogin Azure-beli hitelesítő adataival.

     > [!NOTE]
     > A kód 60 perc után lejár. Ha lejár, a telepítési parancs ismételt beírásával indíthatja újra.

     - Sentinel-jelszó

11. A telepítő most ellenőrzi és megkezdi az erőforrások létrehozását, ami körülbelül 20 percet vesz igénybe.
12. Miután az üzembe helyezés sikeres volt, a következő kimeneti hivatkozásokat fogja kapni:

    - **Adatközpont URL-címe**: hencegő hivatkozás a FarmBeats API-k kipróbálásához.
    - **Gyorsító URL-cím**: felhasználói felület a FarmBeats intelligens Farm gyorssegédének megismeréséhez.
    - **Központi telepítési naplófájl**: az üzembe helyezés során menti a naplókat. Hibaelhárításhoz is használható.

    > [!NOTE]
    > Jegyezze fel a fenti hivatkozásokat, és tartsa kéznél a telepítési naplófájl elérési útját a későbbi használat érdekében.

## <a name="uninstall"></a>Eltávolítás

Jelenleg nem támogatjuk a FarmBeats automatikus eltávolítását a telepítő használatával. Az adatközpont vagy a Gyorssegéd eltávolításához a Azure Portal törölje azt az erőforráscsoportot, amelyben ezeket az összetevőket telepíti, vagy manuálisan törölje az erőforrásokat.

Ha például két különböző erőforráscsoport esetében telepítette az adatközpontot és a gyorsító eszközt, a következőképpen törölheti ezeket az erőforráscsoportokat:

1. Jelentkezzen be a Azure Portalba.
2. Válassza ki a fiókját a jobb felső sarokban, és váltson arra a kívánt Azure AD-bérlőre, ahol a Microsoft-FarmBeats telepíteni kívánja.

   > [!NOTE]
   > A Gyorssegéd megfelelő működéséhez szükség van az adatközpontra. Nem javasoljuk, hogy a Gyorssegéd eltávolítása nélkül távolítsa el az adatközpontot.

3. Válassza az erőforráscsoportok lehetőséget, majd írja be a törölni kívánt adatközpont vagy gyorssegéd-erőforráscsoport nevét.
4. Válassza ki az erőforráscsoport nevét. Írja be újból a nevet a név mezőbe, majd kattintson a Törlés elemre az erőforráscsoport és az összes mögöttes erőforrás eltávolításához.
5. Azt is megteheti, hogy manuálisan törli az egyes erőforrásokat, ami nem ajánlott.
7. Az adatközpont törléséhez/eltávolításához lépjen az Azure-ba közvetlenül az Azure-on, és onnan törölje az erőforráscsoportot.

## <a name="next-steps"></a>További lépések

Telepítette az Azure FarmBeats-t. Most Ismerkedjen meg a [farmok létrehozásával](manage-farms.md#create-farms).
