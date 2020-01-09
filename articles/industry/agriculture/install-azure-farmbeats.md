---
title: Az Azure FarmBeats telepítése
description: Ez a cikk az Azure-FarmBeats Azure-előfizetésben való telepítését ismerteti
author: usha-rathnavel
ms.topic: article
ms.date: 12/11/2019
ms.author: usrathna
ms.openlocfilehash: d1a6bdfb38431e18eb305b223ce8ee2467804052
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75482451"
---
# <a name="install-azure-farmbeats"></a>Az Azure FarmBeats telepítése

Ez a cikk azt ismerteti, hogyan telepítheti az Azure FarmBeats az Azure-előfizetésében.

Az Azure FarmBeats egy, az Azure Marketplace-en elérhető üzleti célú ajánlat. Lehetővé teszi a mezőgazdasági adatkészletek összevonását a szolgáltatók között, és a gyakorlatban hasznosítható elemzések létrehozását. Az Azure FarmBeats ezt teszi lehetővé azáltal, hogy a mesterséges intelligencia (AI) és a Machine learning (ML) modellek összeolvasztott adatkészletek alapján történő összeállítását végzi. Az Azure FarmBeats két fő összetevője a következők:

- **Datahub**: olyan API-réteg, amely lehetővé teszi különböző mezőgazdasági adatkészletek összesítését, normalizálása és contextualization különböző szolgáltatók között.

- **Accelerator**: a Datahub-re épülő minta webalkalmazás. Ez a lépés elindítja a modell fejlesztését és vizualizációját. A Gyorssegéd az Azure FarmBeats API-kkal mutatja be a betöltött szenzorok adatmegjelenítését diagramokként és a modell kimenetének megjelenítésekor térképként.

## <a name="before-you-start"></a>Előkészületek
### <a name="components-installed"></a>Telepített összetevők

Az Azure FarmBeats telepítésekor a következő erőforrások vannak kiépítve az Azure-előfizetésében:

| Telepített Azure-erőforrások  | Azure FarmBeats-összetevő  |
|---------|---------|
| Application Insights   |      Datahub & gyorsító      |
| App Service     |     Datahub & gyorsító     |
| App Service-csomag   | Datahub & gyorsító  |
| API-kapcsolatok    |  Datahub       |
| Azure Cache for Redis       | Datahub      |
| Azure Cosmos DB   |  Datahub       |
| Azure Data Factory V2       |     Datahub & gyorsító      |
| Azure Batch-fiók    | Datahub   |
| Azure Key Vault |  Datahub & gyorsító        |
| Azure Maps fiók       |     Accelerator    |
| Event hub-névtér    |     Datahub      |
| Logic App      |  Datahub       |
| Tárfiók      |     Datahub & gyorsító      |
| Time Series Insights     |    Datahub    |

### <a name="costs-incurred"></a>Felmerülő költségek

Az Azure FarmBeats díja a mögöttes Azure-szolgáltatások díjszabásának összessége. Az Azure-szolgáltatásokra vonatkozó díjszabás a [díjszabási számológép](https://azure.microsoft.com/pricing/calculator)használatával számítható ki. A teljes telepítés tényleges költsége a használattól függően változhat. A két összetevő állandó állapotának díja a következő:

* Datahub – kevesebb, mint $10/nap
* Gyorsító – kevesebb, mint $2/nap

### <a name="regions-supported"></a>Támogatott régiók

Az Azure FarmBeats jelenleg a következő régiókban támogatja a nyilvános Felhőbeli környezetekben:
* Ausztrália keleti régiója
* USA középső régiója
* USA keleti régiója
* USA 2. keleti régiója
* USA nyugati régiója
* USA 2. nyugati régiója
* Észak-Európa
* Nyugat-Európa
* Délkelet-Ázsia

### <a name="time-taken"></a>Szükséges idő

Az Azure FarmBeats teljes telepítése, beleértve az előkészítést és a telepítést is, kevesebb mint egy órát vesz igénybe.

## <a name="prerequisites"></a>Előfeltételek    

Az Azure FarmBeats tényleges telepítésének megkezdése előtt végre kell hajtania a következő lépéseket:

### <a name="create-sentinel-account"></a>Sentinel-fiók létrehozása
Az Azure FarmBeats telepítője lehetővé teszi, hogy ingyenes műholdas képeket kapjon az Európai Űrügynökség [Sentinel-2 műhold-](https://scihub.copernicus.eu/) feladatával a farmhoz. A beállítás konfigurálásához Sentinel-fiókra van szükség.

Kövesse a következő lépéseket egy ingyenes fiók létrehozásához a Sentinel használatával:

1. Nyissa meg a hivatalos [regisztrációs](https://scihub.copernicus.eu/dhus/#/self-registration) oldalt.
2. Adja meg a szükséges adatokat (keresztnév, vezetéknév, Felhasználónév, jelszó és e-mail azonosító), és fejezze be az űrlapot.
3. A rendszer egy ellenőrző hivatkozást küld a regisztrált e-mail-AZONOSÍTÓba. Válassza ki az e-mailben megadott hivatkozást, és fejezze be az ellenőrzést.

Az ellenőrzés befejezése után a regisztrációs folyamat befejeződött. Jegyezze fel a Sentinel- **felhasználónevét** és a **Sentinel-jelszavát**.

### <a name="decide-subscription-and-region"></a>Előfizetés és régió kiválasztása

Szüksége lesz az Azure-előfizetés AZONOSÍTÓJÁRA és arra a régióra, ahová az Azure FarmBeats telepíteni kívánja. Válassza ki az egyik régiót, amely a [támogatott régiók](#regions-supported) szakaszban szerepel.

Jegyezze fel az Azure- **előfizetés azonosítóját** és az **Azure-régiót**.

### <a name="verify-permissions"></a>Engedélyek ellenőrzése

Ellenőriznie kell, hogy rendelkezik-e megfelelő jogosultságokkal és engedélyekkel az Azure-FarmBeats telepíteni kívánt Azure-bérlőben.

A Azure Portal a [szerepköralapú hozzáférés-vezérlésre](https://docs.microsoft.com/azure/role-based-access-control/check-access) vonatkozó utasításokat követve ellenőrizheti a hozzáférési engedélyeit.

Az Azure FarmBeats telepítéséhez a következő engedélyek szükségesek:
- Bérlő – olvasási hozzáférés
- Előfizetés – közreműködő vagy tulajdonos
- Erőforráscsoport – tulajdonos.

Az Azure FarmBeats emellett Azure Active Directory alkalmazás-regisztrációt igényel. A szükséges Azure AD-telepítés két módon hajtható végre:

**1. eset**: az Ön által telepített Azure-bérlőn **írási** engedélyekkel rendelkezik. Ez az eset azt jelenti, hogy rendelkezik a szükséges engedélyekkel ahhoz, hogy dinamikusan létrehozza a HRE-alkalmazás regisztrációját a telepítés során.

Folytathatja közvetlenül a [piactér-regisztráció befejezése](#complete-azure-marketplace-sign-up) szakaszt.


**2. eset**: nem rendelkezik **írási** engedéllyel az Azure-bérlőben. Ez az eset gyakori, ha az Azure FarmBeats a vállalat Azure-előfizetésében próbálja telepíteni, és az **írási** hozzáférés csak az Ön tulajdonában lévő erőforráscsoporthoz korlátozódik.
Ebben az esetben kérje meg a rendszergazdát, hogy kövesse az alábbi lépéseket az Azure AD-alkalmazás regisztrálásának automatikus létrehozásához és befejezéséhez a Azure Portalban.

1. Töltse le és csomagolja ki a [HRE app Generator-szkriptet](https://aka.ms/FarmBeatsAADScript) a helyi gépre.
2. Jelentkezzen be a Azure Portalba, és válassza ki az előfizetését és az Azure AD-bérlőt.
3. Cloud Shell elindítása a Azure Portal tetején található eszköztárból.

    ![Projekt FarmBeats](./media/install-azure-farmbeats/navigation-bar-1.png)

4. Válassza a PowerShell lehetőséget az előnyben részesített rendszerhéj-felületként. A rendszer első alkalommal kéri a felhasználókat, hogy válasszon ki egy előfizetést, és hozzon létre egy Storage-fiókot. Végezze el a telepítést a megadott utasítások szerint.
5. Töltse fel a szkriptet (az 1. lépésből) Cloud Shellba, és jegyezze fel a feltöltött fájl helyét.

    > [!NOTE]
    > Alapértelmezés szerint a rendszer feltölti a fájlt a saját könyvtárába.

6. A "CD" parancs használatával nyissa meg a kezdőkönyvtárat, és futtassa a következő parancsfájlt:

      ```azurepowershell-interactive
            ./create_aad_script.ps1
      ```
7. Adja meg a **Datahub webhely** nevét és a **gyorssegéd webhelyének** nevét. Jegyezze fel a szkript kimenetét, és ossza meg az Azure FarmBeats telepítését végző személlyel.

Ha a rendszergazda megadja a szükséges adatokat, jegyezze fel a **HRE ügyfél-azonosítót, a HRE-ügyfél titkát, a Datahub webhely nevét & a Gyorssegéd webhelyének nevét**.

   > [!NOTE]
   > Ha a 2. esethez tartozó útmutatást követi, ne felejtse el felvenni a HRE ügyfél-azonosítót & HRE-ügyfél-titkos kulcsként külön paraméterekként a [Parameters fájlban](#prepare-parameters-file)

Most már rendelkezik a következő szakasz folytatásához szükséges információkkal.

### <a name="complete-azure-marketplace-sign-up"></a>Azure Marketplace-regisztráció befejezése

A felhőalapú telepítési folyamat megkezdése előtt el kell végeznie a regisztrációt az Azure Marketplace-en elérhető Azure FarmBeats-ajánlatba. A regisztráció befejezéséhez kövesse az alábbi lépéseket:

1.  Jelentkezzen be az Azure portálra. Válassza ki a fiókját a jobb felső sarokban, és váltson arra az Azure AD-bérlőre, ahol az Azure FarmBeats-t szeretné telepíteni.

2.  Nyissa meg az Azure Marketplace-t a portálon, és keresse meg az **Azure FarmBeats** a piactéren

3.  Megjelenik egy új ablak, amely áttekintést nyújt az Azure FarmBeats. Válassza a **Létrehozás**lehetőséget.

4.  Ekkor megjelenik egy új ablak. A regisztrációs folyamat befejezéséhez válassza ki a megfelelő előfizetést, erőforráscsoportot és helyet, amelyre telepíteni szeretné az Azure-FarmBeats.

5.  A beírt részletek ellenőrzése után válassza **az OK gombot**. Megjelenik a Használati feltételek lap. Tekintse át a feltételeket, és válassza a **Létrehozás** lehetőséget a regisztrációs folyamat befejezéséhez.

Ez a lépés befejezi a regisztrációs folyamatot az Azure Marketplace-en. Most már készen áll a paraméterek fájl előkészítésének elindítására.

### <a name="prepare-parameters-file"></a>Paraméterek előkészítése fájl
Az előfeltételek fázis utolsó lépéseként létrejön egy olyan JSON-fájl, amely a Cloud Shell telepítése során bemenetként fog szolgálni. A JSON-fájlban lévő paramétereket a megfelelő értékekkel kell helyettesíteni.

Alább látható egy példa JSON-fájl. Töltse le a mintát, és frissítse a szükséges adatokat.

```json
{  
    "sku":"both",
    "subscriptionId":"da9xxxec-dxxf-4xxc-xxx21-xxx3ee7xxxxx",
    "datahubResourceGroup":"dummy-test-dh1",
    "location":"westus2",
    "datahubWebsiteName":"dummy-test-dh1",
    "acceleratorResourceGroup":"dummy-test-acc1",
    "acceleratorWebsiteName":"dummy-test-acc1",
    "sentinelUsername":"dummy-dev",
    "notificationEmailAddress":"dummy@yourorg.com",
    "updateIfExists":true
}
```

A paraméterekkel kapcsolatos további információkért tekintse meg az alábbi paramétereket tartalmazó táblázatot.

| Paraméter | Value (Díj)|
|--- | ---|
|SKU  | Lehetővé teszi a felhasználó számára, hogy a Datahub és a gázpedált is telepítse, vagy csak a Datahub. Csak a Datahub telepítéséhez használja a "Datahub" kifejezést. A Datahub és a Gyorssegéd telepítéséhez használja a "mindkettő" lehetőséget.|
|subscriptionId | Az Azure FarmBeats telepítéséhez szükséges Azure-előfizetés megadása|
|datahubResourceGroup| Meghatározza a Datahub erőforrásainak erőforráscsoport-nevét. Itt adhatja meg az Azure piactéren létrehozott erőforráscsoport nevét|
|location |A hely/Azure-régió, ahová telepíteni szeretné az Azure FarmBeats|
|datahubWebsiteName  | A Datahub webalkalmazás egyedi URL-előtagja |
|acceleratorResourceGroup  | Meghatározza a Gyorssegéd-erőforrások erőforráscsoport-nevét|
|acceleratorWebsiteName |A gyorsító webalkalmazás egyedi URL-előtagja|
|sentinelUsername | A Sentinel Satellite-képek beszerzéséhez használt Felhasználónév|
|notificationEmailAddress  | Az e-mail-cím, amely a Datahub belül konfigurált riasztások értesítéseit fogadja|
|updateIfExists| Választható A Parameters fájlban szerepeltetni kívánt paraméter csak akkor, ha egy meglévő Azure FarmBeats-példányt szeretne frissíteni. A frissítéshez további részleteket, például az erőforráscsoportok nevét és helyét kell megegyeznie.|
|aadAppClientId | Választható A Parameters fájlban szerepeltetni kívánt paraméter csak akkor, ha előre létrehozott HRE alkalmazást használ. További részletekért tekintse meg a 2. esetet az [engedélyek ellenőrzése](#verify-permissions) szakaszban. |
|aadAppClientSecret  | Választható Választható A Parameters fájlban szerepeltetni kívánt paraméter csak akkor, ha előre létrehozott HRE alkalmazást használ. További részletekért tekintse meg a 2. esetet az [engedélyek ellenőrzése](#verify-permissions) szakaszban.|

A fenti táblázat és a JSON-fájl alapján hozza létre a paraméterek JSON-fájlját, és mentse a helyi számítógépére.

## <a name="install"></a>Telepítés

Az Azure FarmBeats-erőforrások tényleges telepítése Cloud Shell böngészőalapú parancssori felületen történik a bash-környezet használatával. Az Azure FarmBeats telepítéséhez kövesse az alábbi utasításokat:

1. Jelentkezzen be az Azure portálra. Válassza ki azt az Azure-előfizetést és bérlőt, amelyre telepíteni szeretné az Azure FarmBeats-t.
2. **Cloud Shell** elindítása a Azure Portal jobb felső sarkában található eszköztárból.
3. Válassza a bash lehetőséget az előnyben részesített rendszerhéj-felületként. Kattintson a **feltöltés** gombra (az alábbi képen kiemelve), és töltse fel az előkészített paraméterek JSON-fájlját.

      ![Projekt FarmBeats](./media/install-azure-farmbeats/bash-2-1.png)

4. **Másolja** az alábbi parancsot, és **cserélje le a \<username >** a megfelelő értékre, hogy a parancs a feltöltött fájl helyes elérési útjára mutasson.

    ```bash
          wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```
5. Futtassa a módosított parancsot a telepítési folyamat elindításához. A rendszer a következőket fogja kérni:
 - Fogadja el az **Azure FarmBeats licencfeltételeket** . Ha elfogadja a Használati feltételek, adja meg az "Y" értéket a következő lépéshez. Ha nem fogadja el a használati feltételeket, adja meg az "N" értéket a telepítés megszakításához.

 - Ezután meg kell adnia egy hozzáférési jogkivonatot a telepítéshez. Másolja a generált kódot, és jelentkezzen be az [eszköz bejelentkezési oldalára](https://microsoft.com/devicelogin) az Azure-beli **hitelesítő adataival**.

 - Miután a bejelentkezés sikeresen befejeződött, a telepítő kérni fogja a Sentinel-fiók jelszavának megadását. Adja meg a **Sentinel-fiók jelszavát**.

6. A rendszer érvényesíti a paramétereket tartalmazó fájlt, és elindítja az Azure-erőforrások telepítését. A telepítés elvégzése körülbelül **25 percet** vesz igénybe.    
> [!NOTE]
> Az inaktív Cloud Shell munkamenetek **20 perc**elteltével lejárnak. Tartsa a Cloud Shell munkamenetet aktív állapotban, amíg a telepítő üzembe helyezi az Azure-erőforrásokat. Ha a munkamenet időtúllépést eredményez, újra kell indítania a telepítési folyamatot.

A telepítés befejezése után a következő kimeneti hivatkozásokat fogja kapni:
* **Datahub URL-cím**: a Datahub API-k elérésére szolgáló hencegő hivatkozás.
* **Gyorsító URL-cím**: az Azure FarmBeats-gyorsító webalkalmazása.
* **Telepítő naplófájlja**: a telepítés részleteit tartalmazó naplófájl. Ez a naplófájl használható a telepítés hibaelhárításához, ha szükséges.

Az Azure FarmBeats-telepítés befejezését a következő ellenőrzésekkel ellenőrizheti:

**Datahub**
1. Jelentkezzen be a megadott gyorssegéd-URL-címre (a **https://\<yourdatahub-web-name >. azurewebsites. net/hencegés**formátumban) az Azure-beli hitelesítő adataival.
2. Láthatja a különböző FarmBeats API-objektumokat, és REST-műveleteket hajthat végre az API-kon.

**Accelerator**
1. Jelentkezzen be a megadott gyorssegéd-URL-címre (a **https://\<youraccelerator-web-name >. azurewebsites. net/hencegés**formátumban) az Azure-beli hitelesítő adataival.
2. Meg kell jelennie a Gyorssegéd felhasználói felületének, amely lehetővé teszi, hogy farmokat hozzon létre a böngészőben.

A fenti műveletek végrehajtásának lehetősége az Azure FarmBeats sikeres telepítését jelzi.

## <a name="upgrade"></a>Frissítés
A nyilvános előzetes verzióban az Azure FarmBeats meglévő telepítésének frissítéséhez újra kell futtatnia a telepítési parancsot Cloud Shellban, és egy további "**updateIfExists**" paramétert kell beállítani a paraméterek fájlban a "**true**" értékre. Tekintse át az alábbi JSON-minta utolsó sorát a frissítési paraméterhez.

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
A parancs frissíti a meglévő Azure FarmBeats-telepítést a legújabb verzióra, és megadja a kimeneti hivatkozásokat.

## <a name="uninstall"></a>Eltávolítás

Az Azure FarmBeats Datahub vagy Accelerator eltávolításához hajtsa végre a következő lépéseket:

1.  Jelentkezzen be a Azure Portalba, és **törölje azokat az erőforráscsoportot** , amelyekben ezek az összetevők telepítve vannak.

2.  Nyissa meg Azure Active Directory & törölje az Azure FarmBeats-telepítéshez kapcsolódó **Azure ad-alkalmazást** . Ezzel törli az Azure FarmBeats telepítését az Azure-előfizetésből.

## <a name="next-steps"></a>Következő lépések
Megtanulta, hogyan telepítheti az Azure FarmBeats az Azure-előfizetésében. Most megtudhatja, hogyan [adhat hozzá felhasználókat](manage-users-in-azure-farmbeats.md#manage-users) az Azure FarmBeats-példányához.
