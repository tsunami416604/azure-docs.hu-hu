---
title: Az Azure FarmBeats telepítése
description: Ez a cikk az Azure-FarmBeats Azure-előfizetésben való telepítését ismerteti
author: usha-rathnavel
ms.topic: article
ms.date: 1/17/2020
ms.author: atinb
ms.openlocfilehash: 0702b302af1c964014a6649f5f3e86ce47b4600a
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048372"
---
# <a name="install-azure-farmbeats"></a>Az Azure FarmBeats telepítése

Ez a cikk azt ismerteti, hogyan telepítheti az Azure FarmBeats az Azure-előfizetésében.

Az Azure FarmBeats egy, az Azure Marketplace-en elérhető üzleti célú ajánlat. Lehetővé teszi a mezőgazdasági adatkészletek összevonását a szolgáltatók között, és a gyakorlatban hasznosítható elemzések létrehozását. Az Azure FarmBeats ezt teszi lehetővé azáltal, hogy a mesterséges intelligencia (AI) és a Machine learning (ML) modellek összeolvasztott adatkészletek alapján történő összeállítását végzi. Az Azure FarmBeats két fő összetevője a következők:

- **Datahub**: olyan API-réteg, amely lehetővé teszi különböző mezőgazdasági adatkészletek összesítését, normalizálása és contextualization különböző szolgáltatók között.

- **Accelerator**: webalkalmazás, amely a Datahub-re épül. Ez a lépés elindítja a modell fejlesztését és vizualizációját. A Gyorssegéd az Azure FarmBeats API-kkal mutatja be a betöltött szenzorok adatmegjelenítését diagramokként és a modell kimenetének megjelenítésekor térképként.

## <a name="general-information"></a>Általános információk

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
| Azure Batch fiók    | Datahub   |
| Azure Key Vault |  Datahub & gyorsító        |
| Azure Maps fiók       |     Accelerator    |
| Event hub-névtér    |     Datahub      |
| Logikai alkalmazás      |  Datahub       |
| Tárfiók      |     Datahub & gyorsító      |
| Time Series Insights     |    Datahub    |

### <a name="costs-incurred"></a>Felmerülő költségek

Az Azure FarmBeats díja a mögöttes Azure-szolgáltatások díjszabásának összessége. Az Azure-szolgáltatásokra vonatkozó díjszabás a [díjszabási számológép](https://azure.microsoft.com/pricing/calculator)használatával számítható ki. A teljes telepítés tényleges költsége a használattól függően változhat. A két összetevő állandó állapotának díja a következő:

- Datahub – kevesebb, mint $10/nap
- Gyorsító – kevesebb, mint $2/nap

### <a name="regions-supported"></a>Támogatott régiók

Az Azure FarmBeats jelenleg a következő régiókban támogatja a nyilvános Felhőbeli környezetekben:

- Kelet-Ausztrália
- USA középső régiója
- USA keleti régiója
- USA 2. keleti régiója
- USA nyugati régiója
- USA nyugati régiója, 2.
- Észak-Európa
- Nyugat-Európa
- Kelet-Ázsia
- Délkelet-Ázsia

### <a name="time-taken"></a>Szükséges idő

Az Azure FarmBeats teljes telepítése, beleértve az előkészítést és a telepítést is, kevesebb mint egy órát vesz igénybe.

## <a name="prerequisites"></a>Előfeltételek

Az Azure FarmBeats tényleges telepítésének megkezdése előtt végre kell hajtania a következő lépéseket:

### <a name="verify-permissions"></a>Engedélyek ellenőrzése

Az Azure FarmBeats telepíteni kívánt Azure-bérlőn a következő engedélyekre lesz szüksége:

- Bérlő – HRE alkalmazás létrehozója
- Előfizetés – tulajdonos
- Az erőforráscsoport, amelyben a FarmBeats telepítve van – tulajdonos

Az első két engedélyre van szükség a [HRE alkalmazás létrehozásának lépéseinek létrehozásához](#create-an-aad-application) . Ha szükséges, a HRE-alkalmazás létrehozásához a megfelelő engedélyekkel rendelkező személyt is beszerezhet. A FarmBeats telepítő személynek azon erőforráscsoport tulajdonosának kell lennie, amelyben a FarmBeats telepítve van.

A Azure Portal a [szerepköralapú hozzáférés-vezérlésre](https://docs.microsoft.com/azure/role-based-access-control/check-access) vonatkozó utasításokat követve ellenőrizheti a hozzáférési engedélyeit.

### <a name="decide-subscription-and-region"></a>Előfizetés és régió kiválasztása

Szüksége lesz az Azure-előfizetés AZONOSÍTÓJÁRA és arra a régióra, ahová az Azure FarmBeats telepíteni kívánja. Válassza ki az egyik régiót, amely a [támogatott régiók](#regions-supported) szakaszban szerepel.

Jegyezze fel az Azure- **előfizetés azonosítóját** és az **Azure-régiót**.

### <a name="create-an-aad-application"></a>HRE-alkalmazás létrehozása

Az Azure FarmBeats használatához Azure Active Directory alkalmazás létrehozása és regisztrálása szükséges. A HRE-létrehozási parancsfájl sikeres futtatásához a következő engedélyek szükségesek:

- Bérlő – HRE alkalmazás létrehozója
- Előfizetés – tulajdonos

Futtassa az alábbi lépéseket egy Cloud Shell-példányban a PowerShell-környezettel. A rendszer első alkalommal kéri a felhasználókat, hogy válasszon ki egy előfizetést, és hozzon létre egy Storage-fiókot. Végezze el a telepítést a megadott utasítások szerint.

1. A [HRE app Generator parancsfájl](https://aka.ms/FarmBeatsAADScript) letöltése

    ```azurepowershell-interactive
        wget -q https://aka.ms/FarmBeatsAADScript -O ./create_aad_script.ps1
    ```

2. Alapértelmezés szerint a rendszer letölti a fájlt a saját könyvtárába. Navigáljon a címtárhoz.

    ```azurepowershell-interactive
        cd
    ```

3. A HRE parancsfájl futtatása

    ```azurepowershell-interactive
        ./create_aad_script.ps1
    ```

4. A HRE-szkript körülbelül 2 percet vesz igénybe, és megjeleníti az értékeket a képernyőn, valamint egy ugyanabban a könyvtárban található JSON-fájlon. Ha valaki más futtatta a szkriptet, kérje meg őket, hogy ossza meg Önnel ezt a kimenetet.

### <a name="create-sentinel-account"></a>Sentinel-fiók létrehozása

Az Azure FarmBeats telepítője lehetővé teszi, hogy az Európai Űrügynökség [Sentinel-2](https://scihub.copernicus.eu/) Satellite Missziójában műholdas képeket kapjon a farmhoz. A beállítás konfigurálásához Sentinel-fiókra van szükség.

Kövesse a következő lépéseket egy ingyenes fiók létrehozásához a Sentinel használatával:

1. Nyissa meg a hivatalos [regisztrációs](https://aka.ms/SentinelRegistration) oldalt.
2. Adja meg a szükséges adatokat (keresztnév, vezetéknév, Felhasználónév, jelszó és e-mail azonosító), és fejezze be az űrlapot.
3. A rendszer egy ellenőrző hivatkozást küld a regisztrált e-mail-AZONOSÍTÓba. Válassza ki az e-mailben megadott hivatkozást, és fejezze be az ellenőrzést.

Az ellenőrzés befejezése után a regisztrációs folyamat befejeződött. Jegyezze fel a Sentinel- **felhasználónevét** és a **Sentinel-jelszavát**.

## <a name="install"></a>Telepítés

Most már készen áll a FarmBeats telepítésére. A telepítés elindításához kövesse az alábbi lépéseket:

1. Jelentkezzen be az Azure portálra. Válassza ki a fiókját a jobb felső sarokban, és váltson arra az Azure AD-bérlőre, ahol az Azure FarmBeats-t szeretné telepíteni.

2. Nyissa meg az Azure Marketplace-t a portálon, és keresse meg az **Azure FarmBeats** a piactéren.

3. Megjelenik egy új ablak, amely áttekintést nyújt az Azure FarmBeats. Kattintson a **Létrehozás** gombra.

4. Megjelenik egy új ablak. A regisztrációs folyamat befejezéséhez válassza ki a megfelelő előfizetést, erőforráscsoportot és helyet, amelyre telepíteni szeretné az Azure-FarmBeats.

5. Adja meg azt az e-mail-címet, amelynek az Azure FarmBeats kapcsolatos riasztásokat kell kapnia a **FarmBeats szolgáltatás riasztások** szakaszában. A lap alján található tovább gombra kattintva lépjen a **függőségek** lapra. ![alapjai lap](./media/install-azure-farmbeats/create-azure-farmbeats-basics.png)

6. Másolja az egyes bejegyzéseket a [HRE szkript](#create-an-aad-application) kimenetéről a HRE-alkalmazás bemenetei szakaszba.

7. Adja meg a [Sentinel-fiók](#create-sentinel-account) felhasználónevét és jelszavát a Sentinel-fiók szakaszban. A Tovább gombra kattintva lépjen a **felülvizsgálat + létrehozás** lapra ![függőségek lapra](./media/install-azure-farmbeats/create-azure-farmbeats-dependencies.png)

8. A beírt részletek ellenőrzése után válassza **az OK gombot**. Megjelenik a Használati feltételek lap. Tekintse át a feltételeket, és válassza a **Létrehozás** lehetőséget a telepítés elindításához. A rendszer automatikusan átirányítja egy oldalra, ahol követheti a telepítés folyamatát.

A telepítés befejezése után ellenőrizheti a telepítést, és megkezdheti a FarmBeats-portál használatát, ha a telepítés során megadott webhely nevére navigál: https://\<FarmBeats-web-Name >. azurewebsites. net. A farmok létrehozásához a FarmBeats felhasználói felületét kell megtekinteni.

A **Datahub** a következő címen érhető el: https://\<FarmBeats-website-Name >-API. azurewebsites. net/henceg. Itt látni fogja a különböző FarmBeats API-objektumokat, és REST-műveleteket hajt végre az API-kon.

## <a name="upgrade"></a>Frissítés

A FarmBeats legújabb verzióra való frissítéséhez futtassa a következő lépéseket egy Cloud Shell példányban a PowerShell-környezet használatával. A felhasználónak azon előfizetés tulajdonosának kell lennie, amelyben a FarmBeats telepítve van.
A rendszer első alkalommal kéri a felhasználókat, hogy válasszon ki egy előfizetést, és hozzon létre egy Storage-fiókot. Végezze el a telepítést a megadott utasítások szerint.

1. A [Frissítési parancsfájl](https://aka.ms/FarmBeatsUpgradeScript) letöltése

    ```azurepowershell-interactive
        wget –q https://aka.ms/FarmBeatsUpgradeScript -O ./update-farmbeats.ps1
    ```

2. Alapértelmezés szerint a rendszer letölti a fájlt a saját könyvtárába. Navigáljon a címtárhoz.

    ```azurepowershell-interactive
        cd
    ```

3. A Frissítési parancsfájl futtatása

    ```azurepowershell-interactive
        ./upgrade-farmbeats.ps1 -InputFilePath [Path to input.json file]
    ```

A input. JSON fájl elérési útja nem kötelező. Ha nincs megadva, a parancsfájl kérni fogja az összes szükséges bemenetet. A frissítés körülbelül 30 percen belül befejeződik.

## <a name="uninstall"></a>Eltávolítás

Az Azure FarmBeats Datahub vagy Accelerator eltávolításához hajtsa végre a következő lépéseket:

1. Jelentkezzen be a Azure Portalba, és **törölje azokat az erőforráscsoportot** , amelyekben ezek az összetevők telepítve vannak.

2. Nyissa meg Azure Active Directory & törölje az Azure FarmBeats-telepítéshez kapcsolódó **Azure ad-alkalmazást** .

## <a name="next-steps"></a>Következő lépések

Megtanulta, hogyan telepítheti az Azure FarmBeats az Azure-előfizetésében. Most megtudhatja, hogyan [adhat hozzá felhasználókat](manage-users-in-azure-farmbeats.md#manage-users) az Azure FarmBeats-példányához.
