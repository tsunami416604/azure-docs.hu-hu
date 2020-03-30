---
title: Az Azure FarmBeats telepítése
description: Ez a cikk bemutatja, hogyan telepítheti az Azure FarmBeats-t az Azure-előfizetésében
author: usha-rathnavel
ms.topic: article
ms.date: 1/17/2020
ms.author: atinb
ms.openlocfilehash: 0761db6b73c6fcfeb1ef6fda729a68c9644bbc72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479559"
---
# <a name="install-azure-farmbeats"></a>Az Azure FarmBeats telepítése

Ez a cikk ismerteti, hogyan telepítheti az Azure FarmBeats az Azure-előfizetésben.

Az Azure FarmBeats egy olyan vállalkozáson belüli ajánlat, amely elérhető az Azure Piactéren. Lehetővé teszi a mezőgazdasági adatkészletek összesítését a szolgáltatók között, és a perelhető elemzések létrehozását. Az Azure FarmBeats ezt úgy éri el, hogy lehetővé teszi mesterséges intelligencia (AI) vagy gépi tanulási (ML) modellek készítését az olvasztott adatkészletek alapján. Az Azure FarmBeats két fő összetevője a következő:

- **Datahub:** Olyan API-réteg, amely lehetővé teszi a különböző mezőgazdasági adatkészletek összesítését, normalizálását és környezetnyelvi vétését a különböző szolgáltatók között.

- **Gyorsító**: A Datahubra épülő webalkalmazás. Ez ugrás-elindítja a modell fejlesztése és vizualizációja. A gyorsító az Azure FarmBeats API-kat használja a bevitt érzékelőadatok diagramként való vizualizációjának és a modellkimenet térképként való megjelenítésének bemutatására.

## <a name="general-information"></a>Általános információk

### <a name="components-installed"></a>Telepített összetevők

Az Azure FarmBeats telepítésekor a következő erőforrások vannak kiépítve az Azure-előfizetésben:

| Telepített Azure-erőforrások  | Azure FarmBeats összetevő  |
|---------|---------|
| Application Insights   |      Datahub & gyorsító      |
| App Service     |     Datahub & gyorsító     |
| App Service-csomag   | Datahub & gyorsító  |
| API-kapcsolat    |  Datahub (Adatközpont)       |
| Azure Cache for Redis       | Datahub (Adatközpont)      |
| Azure Cosmos DB   |  Datahub (Adatközpont)       |
| Azure Data Factory V2       |     Datahub & gyorsító      |
| Azure Batch-fiók    | Datahub (Adatközpont)   |
| Azure Key Vault |  Datahub & gyorsító        |
| Azure Maps-fiók       |     Gyorsító    |
| Eseményközpont-névtér    |     Datahub (Adatközpont)      |
| Logikai alkalmazás      |  Datahub (Adatközpont)       |
| Tárfiók      |     Datahub & gyorsító      |
| Time Series Insights     |    Datahub (Adatközpont)    |

### <a name="costs-incurred"></a>Felmerült költségek

Az Azure FarmBeats költsége az alapul szolgáló Azure-szolgáltatások költségének összesítése. Az Azure-szolgáltatások díjszabási információi a [Díjkalkulátor](https://azure.microsoft.com/pricing/calculator)segítségével számítható ki. A teljes telepítés tényleges költsége a használattól függően változik. A két összetevő állandósult állapotának költsége:

- Datahub - kevesebb, mint $10 naponta
- Gyorsító - kevesebb, mint $ 2 naponta

### <a name="regions-supported"></a>Támogatott régiók

Jelenleg az Azure FarmBeats a következő régiókban támogatott nyilvános felhőalapú környezetekben:

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

### <a name="time-taken"></a>Elvett idő

Az Azure FarmBeats teljes beállítása, beleértve az előkészítést és a telepítést, kevesebb mint egy órát vesz igénybe.

## <a name="prerequisites"></a>Előfeltételek

Az Azure FarmBeats tényleges telepítésének megkezdése előtt el kell végeznie a következő lépéseket:

### <a name="verify-permissions"></a>Engedélyek ellenőrzése

Az Azure FarmBeats telepítéséhez a következő engedélyekre lesz szüksége az Azure-bérlőben:

- Bérlő – AAD-alkalmazás készítője
- Előfizetés - Tulajdonos
- Erőforráscsoport, amelyben a FarmBeats telepítve van - Tulajdonos

Az első két engedély szükséges [az AAD-alkalmazáslépés létrehozásához.](#create-an-aad-application) Szükség esetén beszerezhet valakit, aki rendelkezik az AAD-alkalmazás létrehozásához szükséges engedélyekkel.

A FarmBeats-telepítést a piactérről futtató személynek annak az erőforráscsoportnak a tulajdonosának kell lennie, amelyben a FarmBeats telepítve van. Az előfizetés-tulajdonosok esetében ez automatikusan megtörténik az Erőforráscsoport létrehozásakor. Mások számára kérjük, hogy hozza létre előre az erőforráscsoportot, és kérje meg az Előfizetés tulajdonosát, hogy az Erőforráscsoport tulajdonosát tegye.

A hozzáférési engedélyeket az Azure Portalon a [szerepköralapú hozzáférés-vezérlésre](https://docs.microsoft.com/azure/role-based-access-control/check-access)vonatkozó utasításokat követve ellenőrizheti.

### <a name="decide-subscription-and-region"></a>Előfizetés és régió tájékánsának döntése

Szüksége lesz az Azure-előfizetés-azonosítóra és arra a régióra, ahol telepíteni szeretné az Azure FarmBeats-t. Válasszon egyet a Régiók [támogatott szakaszban](#regions-supported) felsorolt régiók közül.

Jegyezze fel az **Azure-előfizetés-azonosítót** és az **Azure-régiót.**

### <a name="create-an-aad-application"></a>AAD-alkalmazás létrehozása

Az Azure FarmBeats megköveteli az Azure Active Directory-alkalmazások létrehozását és regisztrációját. Az AAD létrehozási parancsfájl sikeres futtatásához a következő engedélyekszükségesek:

- Bérlő – AAD-alkalmazás készítője
- Előfizetés - Tulajdonos

Futtassa a következő lépéseket egy Cloud Shell-példányban a PowerShell-környezetben. Az első alkalommal a felhasználók nak ki kell választaniuk egy előfizetést, és létre kell hozniuk egy tárfiókot. Végezze el a telepítést az utasításoknak megfelelően.

1. Az [AAD alkalmazásgenerátor-parancsfájl](https://aka.ms/FarmBeatsAADScript) letöltése

    ```azurepowershell-interactive
        wget -q https://aka.ms/FarmBeatsAADScript -O ./create_aad_script.ps1
    ```

2. Alapértelmezés szerint a program letölti a fájlt a kezdőkönyvtárba. Keresse meg a könyvtárat.

    ```azurepowershell-interactive
        cd
    ```

3. Az AAD-parancsfájl futtatása

    ```azurepowershell-interactive
        ./create_aad_script.ps1
    ```

4. A parancsfájl a következő három bemenetet kéri:

    - **FarmBeats webhely neve**: Ez az egyedi URL-előtag a FarmBeats webalkalmazáshoz. Abban az esetben, ha az előtag már megtörtént, a parancsfájl hiba ki. A telepítés után a FarmBeats központi\<telepítése elérhető lesz https:// FarmBeats-website-name>.azurewebsites.net\<és a swagger API-k lesz nek https:// FarmBeats-website-name>-api.azurewebsites.net

    - **Azure bejelentkezési azonosító:** Adja meg az Azure bejelentkezési azonosítóját a farmbeats-rendszergazdaként hozzáadni kívánt felhasználó számára. Ez a felhasználó ezután hozzáférést biztosíthat a FarmBeats webalkalmazás eléréséhez más felhasználók számára. A bejelentkezési azonosító általában a john.doe@domain.comforma . Az Azure UPN is támogatott.

    - **Előfizetés-azonosító:** Ez annak az előfizetésnek az azonosítója, amelyben telepíteni szeretné az Azure FarmBeats-t

5. Az AAD parancsfájl futtatása körülbelül 2 percet vesz igénybe, és az értékeket a képernyőn, valamint egy json fájlt ugyanabban a könyvtárban adja ki. Ha valaki más futtatta a parancsfájlt, kérje meg, hogy ossza meg önnel ezt a kimenetet.

### <a name="create-sentinel-account"></a>Sentinel-fiók létrehozása

Az Azure FarmBeats beállítása lehetővé teszi, hogy műholdas képeket kapjon az Európai Űrügynökség [Sentinel-2](https://scihub.copernicus.eu/) műholdas küldetéséről a farmjára. A telepítés konfigurálásához Sentinel-fiókra van szükség.

Kövesse a lépéseket, hogy hozzon létre egy ingyenes fiókot Sentinel:

1. Lépjen a hivatalos [regisztrációs](https://aka.ms/SentinelRegistration) oldalra.
2. Adja meg a szükséges adatokat (utónév, vezetéknév, felhasználónév, jelszó és e-mail azonosító), és töltse ki az űrlapot.
3. A regisztrált e-mail-azonosítóhoz ellenőrző hivatkozást küldünk. Válassza ki az e-mailben megadott hivatkozást, és végezze el az ellenőrzést.

A regisztrációs folyamat befejeződött. Jegyezd fel **Sentinel felhasználónevedet** és **Sentinel jelszavadat,** amint az ellenőrzés is befejeződött.

## <a name="install"></a>Telepítés

Most már készen áll a FarmBeats telepítésére. A telepítés megkezdéséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be az Azure portálra. Válassza ki a fiókját a jobb felső sarokban, és váltson az Azure AD-bérlőre, ahol telepíteni szeretné az Azure FarmBeats-t.

2. Nyissa meg az Azure Marketplace-t a portálon, és keresse meg az **Azure FarmBeats-t** a Marketplace-en.

3. Megjelenik egy új ablak az Azure FarmBeats áttekintésével. Kattintson a **Létrehozás** gombra.

4. Ekkor megjelenik egy új ablak. Fejezze be a regisztrációs folyamatot a megfelelő előfizetés, erőforráscsoport és hely kiválasztásával, amelyre telepíteni szeretné az Azure FarmBeats-t.

5. Adja meg az e-mail címet, amely megkapja az Azure FarmBeats-hez kapcsolódó szolgáltatási riasztásokat a **FarmBeats szolgáltatásriasztások** szakaszban. A lap alján válassza a **Tovább** gombot a **Függőségek** lapra lépéshez.

    ![Alapok lap](./media/install-azure-farmbeats/create-azure-farmbeats-basics.png)

6. Másolja az egyes bejegyzéseket az [AAD-parancsfájl](#create-an-aad-application) kimenetéből az AAD alkalmazásszakasz bemeneteibe.

7. Add meg a [Sentinel fiók](#create-sentinel-account) felhasználónevét és jelszavát a Sentinel-fiók részben. Válassza a **Tovább** gombot a **Véleményezés + Létrehozás** lapra lépéshez.

    ![Függőségek lap](./media/install-azure-farmbeats/create-azure-farmbeats-dependencies.png)

8. A megadott adatok érvényesítése után válassza az **OK gombot.** Megjelenik a Használati feltételek oldal. Tekintse át a feltételeket, és a telepítés elindításához válassza a **Létrehozás** gombot. A program átirányítja arra az oldalra, ahol követheti a telepítés előrehaladását.

Miután a telepítés befejeződött, ellenőrizheti a telepítést, és elkezdheti használni a\<FarmBeats portált, ha a telepítés során megadott webhelynévre navigál: https:// FarmBeats-website-name>.azurewebsites.net. Meg kell jelennie FarmBeats felhasználói felület egy lehetőséget, hogy hozzon létre Farms.

**A Datahub** a\<https:// FarmBeats-website-name>-api.azurewebsites.net/swagger címen található. Itt láthatja a különböző FarmBeats API-objektumokat, és REST-műveleteket hajthat végre az API-kon.

## <a name="upgrade"></a>Frissítés

A FarmBeats frissítése a legújabb verzióra, futtassa a következő lépéseket a Cloud Shell-példány ban a PowerShell-környezetben. A felhasználónak annak az előfizetésnek a tulajdonosának kell lennie, amelyben a FarmBeats telepítve van.

Az első alkalommal a felhasználók nak ki kell választaniuk egy előfizetést, és létre kell hozniuk egy tárfiókot. Végezze el a telepítést az utasításoknak megfelelően.

1. A [frissítési parancsfájl](https://aka.ms/FarmBeatsUpgradeScript) letöltése

    ```azurepowershell-interactive
        wget –q https://aka.ms/FarmBeatsUpgradeScript -O ./upgrade-farmbeats.ps1
    ```

2. Alapértelmezés szerint a program letölti a fájlt a kezdőkönyvtárba. Keresse meg a könyvtárat.

    ```azurepowershell-interactive
        cd
    ```

3. A frissítési parancsfájl futtatása

    ```azurepowershell-interactive
        ./upgrade-farmbeats.ps1 -InputFilePath [Path to input.json file]
    ```

Az input.json fájl elérési útja nem kötelező. Ha nincs megadva, a parancsfájl kérni fogja az összes szükséges bemenetet. A frissítés körülbelül 30 perc alatt befejeződik.

## <a name="uninstall"></a>Eltávolítás

Az Azure FarmBeats Datahub vagy gyorssegéd eltávolításához hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be az Azure Portalra, és **törölje azokat az erőforráscsoportokat,** amelyekben ezek az összetevők telepítve vannak.

2. Nyissa meg az Azure Active Directoryt, & törölje az Azure FarmBeats-telepítéshez kapcsolódó **Azure AD-alkalmazást.**

## <a name="next-steps"></a>További lépések

Megtanulta, hogyan telepítheti az Azure FarmBeats-t az Azure-előfizetésében. Most megtudhatja, hogyan [adhat hozzá felhasználókat](manage-users-in-azure-farmbeats.md#manage-users) az Azure FarmBeats-példányhoz.
