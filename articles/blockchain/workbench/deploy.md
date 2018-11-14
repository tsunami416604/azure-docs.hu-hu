---
title: Az Azure Blockchain Workbench üzembe helyezése
description: Az Azure Blockchain Workbench üzembe helyezése
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 11/12/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 33fce88e7108ee45236e20b1f20dde56bb7446b5
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2018
ms.locfileid: "51616384"
---
# <a name="deploy-azure-blockchain-workbench"></a>Az Azure Blockchain Workbench üzembe helyezése

Az Azure Blockchain Workbench használatával lett telepítve. a megoldássablon az Azure piactéren. A sablon egyszerűbbé teszi a blockchain-alkalmazások létrehozásához szükséges összetevők telepítése. Miután üzembe helyezte a Blockchain Workbenchet ügyfélalkalmazások hozhat létre és kezelheti a felhasználókat és blockchain-alkalmazások hozzáférést biztosít.

A Blockchain Workbenchet az összetevőivel kapcsolatos további információkért lásd: [Azure Blockchain Workbench architektúra](architecture.md).

## <a name="prepare-for-deployment"></a>Felkészülés az üzembe helyezésre

Blockchain Workbenchet lehetővé teszi a blockchain Főkönyv megfelelő Azure szolgáltatások leggyakrabban használt blockchain-alapú alkalmazás létrehozása és üzembe helyezése. Blockchain Workbench üzembe helyezése eredménye a következő szolgáltatásokat önköltséggel Azure-előfizetésében erőforráscsoporton belül.

* 1 event Grid-témakör
* 1 Service Bus-Namespace
* 1 az application Insights
* 1 SQL-adatbázis (Standard S0)
* 2 app Services (szokásos)
* 2 azure-Kulcstartók
* 2 azure Storage-fiókok (Standard-LRS)
* 2 virtuális gép méretezési csoportjairól (az érvényesítési és a feldolgozó csomópontok)
* 2 virtual Network (beleértve a terheléselosztót, a hálózati biztonsági csoport és a nyilvános IP-cím az egyes virtuális hálózatok esetében)
* Választható lehetőség: Az Azure Monitor

Az alábbiakban a létrehozott központi telepítésre példát **myblockchain** erőforráscsoportot.

![A központi telepítési példa](media/deploy/example-deployment.png)

A Blockchain Workbenchet költségét az összesítést az alapul szolgáló Azure-szolgáltatások költségeinek. Díjszabási információk az Azure-szolgáltatások használatával kiszámítható az [díjkalkulátor](https://azure.microsoft.com/pricing/calculator/).

## <a name="prerequisites"></a>Előfeltételek

Az Azure Blockchain Workbench az Azure AD-konfiguráció és alkalmazás regisztrációk van szükség. Ha szeretné, hajtsa végre az Azure AD [konfigurációk manuálisan](#azure-ad-configuration) üzembe helyezési vagy egy parancsfájl futtatása előtt a telepítés után. Ha a Blockchain Workbenchet újbóli üzembe helyezés, tekintse meg [Azure AD-konfigurációjának](#azure-ad-configuration) az Azure AD-konfiguráció ellenőrzése.

> [!IMPORTANT]
> Workbench nem kell telepíteni szeretné ugyanabban a bérlőben, azzal, regisztrálni az Azure AD-alkalmazást használ. Workbench-bérlőben, hogy megfelelő engedélyekkel rendelkezik az erőforrások üzembe helyezése kell telepíteni. További információ az Azure AD-bérlőt: [Active Directory-bérlő beszerzése](../../active-directory/develop/quickstart-create-new-tenant.md) és [alkalmazások integrálása az Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md).



## <a name="deploy-blockchain-workbench"></a>Blockchain Workbench üzembe helyezése

Az előfeltételként felsorolt lépéseket befejezése után készen áll a Blockchain Workbench üzembe helyezése. A következő szakaszok szerkezeti központi telepítése keretében.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki a fiókját a jobb felső sarokban sarokban, majd váltson át a kívánt Azure AD bérlői, ahol szeretné az Azure Blockchain Workbench üzembe helyezése.
3. A bal oldali panelen válassza ki a **erőforrás létrehozása**. Keresse meg `Azure Blockchain Workbench` a a **keresés a piactéren** keresősávba. 

    ![Marketplace-en keresősáv](media/deploy/marketplace-search-bar.png)

4. Válassza ki **az Azure Blockchain Workbench**.

    ![Marketplace-en keresési eredmények](media/deploy/marketplace-search-results.png)

5. Kattintson a **Létrehozás** gombra.
6. Végezze el az alapszintű beállításokat.

    ![Az Azure Blockchain Workbench létrehozása](media/deploy/blockchain-workbench-settings-basic.png)

    | Beállítás | Leírás  |
    |---------|--------------|
    | Erőforrás-előtag | Az üzembe helyezés rövid egyedi azonosítója. Ez az érték erőforrások elnevezési alapjául szolgál. |
    | Virtuális gép felhasználóneve | A felhasználónév rendszergazdaként szolgál az összes virtuális gép (VM). |
    | Hitelesítés típusa | Válassza ki, ha azt szeretné, a jelszót, vagy csatlakozhat a virtuális gépek kulcs. |
    | Jelszó | A jelszó csatlakozik a virtuális gépek szolgál. |
    | SSH | Nyilvános RSA-kulcsot használja az egysoros kiadásától kezdve a **ssh-rsa** vagy a többsoros PEM formátumban. Az SSH-kulcsok használatával is létrehozhat `ssh-keygen` Linux-és OS X, illetve a Windows PuTTYGen használatával. Az SSH-kulcsokat, további információt talál [az SSH használata a Windows Azure-beli kulcsok](../../virtual-machines/linux/ssh-from-windows.md). |
    | Adatbázis-jelszó / adatbázis jelszó megerősítése | Adja meg a központi telepítés részeként létrehozta az adatbázis eléréséhez használt jelszó. |
    | Az üzembe helyezés régiójában | Adja meg az üzembe helyezés a Blockchain Workbenchet erőforrásokat. A legjobb rendelkezésre állás érdekében ennek meg kell egyeznie a **hely** beállítás. |
    | Előfizetés | Adja meg a központi telepítéshez használni kívánt Azure-előfizetést. |
    | Erőforráscsoportok | Hozzon létre egy új erőforráscsoportot kiválasztásával **új létrehozása** , és adja meg egy egyedi erőforráscsoport-nevet. |
    | Hely | Adja meg a régiót kívánja telepíteni a keretrendszer. |

7. Válassza ki **OK** az alapszintű konfigurációs szakasz befejezéséhez.

8. A **speciális beállítások**, adja meg, ha egy új blockchain-hálózat létrehozása vagy meglévő koncepció jogosultság blockchain-hálózat használata.

    A **új létrehozása**:

    A *létrehozása új* kapcsoló meg egyetlen tagot az előfizetésen belül Ethereum Proof-szolgáltató (PoA) csomópontok hozza létre. 

    ![Új blockchain-hálózat speciális beállításai](media/deploy/advanced-blockchain-settings-new.png)

    | Beállítás | Leírás  |
    |---------|--------------|
    | Figyelés | Válassza ki, hogy szeretné-e a blockchain-hálózat figyelése az Azure Monitor engedélyezése |
    | Az Azure Active Directory-beállítások | Válasszon **hozzáadását a későbbiekben**.</br>Megjegyzés: Ha úgy döntött, hogy [előre konfigurálhatja az Azure ad-ben](#azure-ad-configuration) vagy újbóli üzembe helyezés, válassza ki a *felvétele most*. |
    | Virtuális gép kiválasztása | Válassza ki a kívánt Virtuálisgép-méretet a blockchain-hálózat. |

    A **meglévő**:

    A *meglévő* beállítás lehetővé teszi az Ethereum koncepció jogosultság (PoA) blockchain-hálózat megadása. Végpontok az alábbi követelményekkel rendelkezik.

    * A végpont egy Ethereum koncepció jogosultság (PoA) blockchain hálózaton kell lennie.
    * A végpont nyilvánosan elérhetőnek kell lennie a hálózaton keresztül.
    * A blockchain PoA hálózati nastavena nA hodnotu nula gáz ár rendelkeznie kell konfigurálni.

    > [!NOTE]
    > Blockchain Workbenchet fiókok vannak nem támogatott. Ha alapok szükség, a tranzakciók sikertelenek.

    ![Meglévő blockchain-hálózat speciális beállításai](media/deploy/advanced-blockchain-settings-existing.png)

    | Beállítás | Leírás  |
    |---------|--------------|
    | RPC Ethereum-végpont | Adjon meg egy létező PoA blockchain-hálózat RPC-végpont. A végpont https:// vagy a http:// előtaggal kezdődik, és a egy portszámot végződik. Például: `https://network.westus.cloudapp.com:8540` |
    | Az Azure Active Directory-beállítások | Válasszon **hozzáadását a későbbiekben**.</br>Megjegyzés: Ha úgy döntött, hogy [előre konfigurálhatja az Azure ad-ben](#azure-ad-configuration) vagy újbóli üzembe helyezés, válassza ki a *felvétele most*. |
    | Virtuális gép kiválasztása | Válassza ki a kívánt Virtuálisgép-méretet a blockchain-hálózat. |

9. Válassza ki **OK** speciális beállítások befejezéséhez.

10. Tekintse át az összefoglalást, ellenőrizze a paraméterek pontosak.

    ![Összegzés](media/deploy/blockchain-workbench-summary.png)

11. Válassza ki **létrehozás** fogadja el a feltételeket, és üzembe helyezése az Azure Blockchain Workbench használatával.

A központi telepítés akár 90 percet is igénybe vehet. Az Azure portal segítségével a folyamat állapotának monitorozásához. Válassza ki az újonnan létrehozott erőforráscsoport **központi telepítések > Áttekintés** a telepített összetevők állapotának megjelenítéséhez.

> [!IMPORTANT]
> Közzététel a központi telepítést kell végezze el az Active Directory-beállításokat. Ha úgy döntött **hozzáadása később**, futtatnia kell a [az Azure AD-konfigurációs parancsprogram](#azure-ad-configuration-script).  Ha úgy döntött **felvétele most**, kell [a válasz URL-cím konfigurálása](#configuring-the-reply-url).

## <a name="blockchain-workbench-web-url"></a>Blockchain Workbenchet webes URL-címe

A Blockchain Workbenchet központi telepítésének befejezése után egy új erőforráscsoport tartalmazza a Blockchain Workbenchet erőforrások. Blockchain Workbenchet szolgáltatások érhetők el egy webes URL-címmel. A következő lépések bemutatják, hogyan kérheti le az üzembe helyezett keretrendszer webes URL-CÍMÉT.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldali navigációs ablaktáblán válassza ki a **erőforráscsoportok**
3. Válassza ki az erőforráscsoport nevének Blockchain Workbench üzembe helyezésekor megadott.
4. Válassza ki a **típus** oszlopfejrészre kattintva rendezze betűrendbe típus szerint a listában.
5. Két forrásanyag típusú **App Service-ben**. Válassza ki az erőforrást típusú **App Service-ben** *nélkül* a "-api" utótag.

    ![App service-lista](media/deploy/resource-group-list.png)

6. Az App Service-ben **Essentials** területén másolja a **URL-cím** érték, amely az üzembe helyezett Blockchain Workbenchet a webes URL-címet jelöli.

    ![App service alapjai](media/deploy/app-service.png)

Egyéni tartománynév társítandó Blockchain Workbenchet, lásd: [webes alkalmazás egy egyéni tartománynév konfigurálása az Azure App Service, Traffic Managert használva a](../../app-service/web-sites-traffic-manager-custom-domain-name.md).

## <a name="azure-ad-configuration-script"></a>Az Azure AD-konfigurációs parancsprogram

Az Azure AD meg kell adni a Blockchain Workbenchet központi telepítés befejezéséhez. Egy PowerShell-parancsprogram használatával fog a konfiguráció beállítása.

1. Egy böngészőben navigáljon a [Blockchain Workbench webes URL-cím](#blockchain-workbench-web-url).
2. Láthatja, hogy állíthatja be az Azure AD-bA a Cloud Shell utasításokat. Másolja be a parancsot, és a Cloud Shell indítása.

    ![Indítsa el az AAD-parancsfájl](media/deploy/launch-aad-script.png)

3. Válassza ki az Azure AD-bérlőt, amelybe telepítette a Blockchain Workbenchet.
4. A Cloud Shellben illessze be, és futtassa a parancsot.
5. Amikor a rendszer kéri, adja meg a Blockchain Workbench használni kívánt Azure AD-bérlővel. Ez lesz a Blockchain Workbench felhasználókat tartalmazó bérlő.

    > [!IMPORTANT]
    > A hitelesített felhasználó Azure ad-ben alkalmazásregisztrációkat hozhat létre, és a delegált alkalmazásengedélyeket a bérlő engedélyekre van szüksége. Kérje meg a rendszergazdát a bérlő az Azure AD-konfigurációs parancsprogram futtatása, vagy hozzon létre egy új bérlőt szeretne.

    ![Adja meg az Azure AD-bérlő](media/deploy/choose-tenant.png)

6. A rendszer felszólítja egy böngészőben az Azure AD-bérlőhöz hitelesítéséhez. Egy böngészőben nyissa meg a webes URL-címe, írja be a kódot és a hitelesítéshez.

    ![Hitelesítés a kódot](media/deploy/authenticate.png)

7. A parancsfájl több állapotüzeneteket jelenít meg. Kap egy **sikeres** állapotüzenetet, ha a bérlő kiosztása sikeresen megtörtént.
8. Keresse meg a Blockchain Workbenchet URL-CÍMÉT. A rendszer felkéri beleegyezik abba a könyvtárba olvasási engedélyeket ad. Ez lehetővé teszi a Blockchain Workbenchet webalkalmazásokhoz való hozzáférés a felhasználók számára a bérlőben. Ha a bérlői rendszergazda, válassza ki a teljes szervezetre vonatkozó beleegyezés. Ez a beállítás a bérlő összes felhasználója esetében a jóváhagyás fogad el. Ellenkező esetben minden felhasználónak beleegyezését kell adnia a Blockchain Workbenchet webalkalmazás első használatkor.
9. Válassza ki **elfogadás** jóváhagyást.

     ![Hozzájárulás megadása a felhasználói profil olvasása](media/deploy/graph-permission-consent.png)

10. Jóváhagyás, miután a Blockchain Workbenchet webalkalmazáshoz is használható.

## <a name="azure-ad-configuration"></a>Az Azure AD konfigurálása

Ha manuálisan konfigurálja vagy ellenőrizze az üzembe helyezés előtt az Azure AD-beállításokat, ebben a szakaszban az összes lépéseket. Ha szeretné automatikusan konfigurálni az Azure AD-beállításokat, [az Azure AD-konfigurációs parancsprogram](#azure-ad-configuration-script) Blockchain Workbench üzembe helyezése után.

### <a name="blockchain-workbench-api-app-registration"></a>Blockchain Workbench API – alkalmazásregisztrálás

Blockchain Workbenchet telepítéséhez van szükség a regisztráció az Azure AD-alkalmazást. Szüksége lesz egy Azure Active Directory (Azure AD) bérlő az alkalmazás regisztrálásához. Használjon egy meglévő bérlőt, vagy hozzon létre egy új bérlőt. Meglévő Azure AD-bérlővel használja, ha-alkalmazások regisztrálását, a Graph API-engedélyek megadása és Azure AD-bérlő belül a vendégfiók engedélyezése megfelelő engedélyek szükségesek. Ha nem rendelkezik megfelelő engedélyekkel a meglévő Azure AD-bérlő hozzon létre egy új bérlőt.


1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki a fiók jobb felső sarokban, és váltson a kívánt Azure AD-bérlőben. A bérlő az előfizetés rendszergazdája bérlő az előfizetés, ahol Workbench üzembe van helyezve, és hogy megfelelő engedélyekkel rendelkezik az alkalmazásregisztrációt kell lennie.
3. Válassza ki az **Azure Active Directory** szolgáltatást a bal oldali navigációs panelen. Válassza ki **alkalmazásregisztrációk** > **új alkalmazásregisztráció**.

    ![Appok regisztrálása](media/deploy/app-registration.png)

4. Adjon meg egy **neve** és **bejelentkezési URL-** az alkalmazáshoz. Helyőrző értékeket is használhat, mivel az értékek módosulnak az üzembe helyezés során. 

    ![Alkalmazásregisztráció létrehozása](media/deploy/app-registration-create.png)

    |Beállítás  | Érték  |
    |---------|---------|
    |Name (Név) | `Blockchain API` |
    |Alkalmazástípus |Webalkalmazás/API|
    |Bejelentkezési URL-cím | `https://blockchainapi` |

5. Válassza ki **létrehozás** regisztrálni az Azure AD-alkalmazás.

### <a name="modify-manifest"></a>Jegyzékfájl módosítása

Ezután meg kell módosítania a jegyzékfájl az alkalmazás-szerepkörök használatához Azure AD-ben, adja meg a Blockchain Workbench használatával a rendszergazdák.  Alkalmazásjegyzéket kapcsolatos további információkért lásd: [Azure Active Directory alkalmazásjegyzékének](../../active-directory/develop/reference-app-manifest.md).

1. Az alkalmazás regisztrált, válassza ki a **Manifest** a regisztrált alkalmazás részleteket tartalmazó ablaktáblán.
2. Hozzon létre egy GUID Azonosítót. Létrehozhat egy GUID Azonosítót a PowerShell-paranccsal [guid]:: NewGuid () vagy a New-GUID-parancsmagot. Egy másik lehetőség, hogy egy GUID-generátor webhely használatára.
3. Szeretné frissíteni a **appRoles** a jegyzékfájl rész. Az Edit manifest ablaktáblán jelölje ki **szerkesztése** , és cserélje le `"appRoles": []` a megadott JSON-fájllal. Ügyeljen arra, hogy értékét cserélje le a **azonosító** mezőt a létrehozott GUID. 

    ![Jegyzékfájl szerkesztése](media/deploy/edit-manifest.png)

    ``` json
    "appRoles": [
         {
           "allowedMemberTypes": [
             "User",
             "Application"
           ],
           "displayName": "Administrator",
           "id": "<A unique GUID>",
           "isEnabled": true,
           "description": "Blockchain Workbench administrator role allows creation of applications, user to role assignments, etc.",
           "value": "Administrator"
         }
       ],
    ```

    > [!IMPORTANT]
    > Az érték **rendszergazda** Blockchain Workbench használatával a rendszergazdák azonosításához szükséges.

4. A jegyzékfájlban is módosíthatja a **Oauth2AllowImplictFlow** értéket a következőre **igaz**.

    ``` json
    "oauth2AllowImplicitFlow": true,
    ```

5. Válassza ki **mentése** jegyzékfájl módosításainak mentéséhez.

### <a name="add-graph-api-required-permissions"></a>Graph API-hoz szükséges engedélyek hozzáadása

Az API-alkalmazást kell engedélykérés a felhasználótól a könyvtár eléréséhez. Állítsa be a következő szükséges engedélyt az API-alkalmazás:

1. Válassza ki a Blockchain API alkalmazásregisztráció **beállítások > szükséges engedélyek > API kiválasztása > a Microsoft Graph**.

    ![API kiválasztása](media/deploy/client-app-select-api.png)

    Kattintson a **Kiválasztás** gombra.

2. A **hozzáférés engedélyezése** alatt **delegált engedélyek**, válassza a **minden felhasználók főbb profiladatainak olvasása**.

    ![Hozzáférés engedélyezése](media/deploy/client-app-read-perms.png)

    Válassza ki **mentése** majd **kész**.

3. A **szükséges engedélyek**válassza **engedélyek megadása** majd **Igen** a hitelesítési paramétert.

   ![Engedélyek megadása](media/deploy/client-app-grant-permissions.png)

   Engedély megadása lehetővé teszi a Blockchain Workbenchet eléréséhez a felhasználók a címtárban. Keressen, és tagokat vehet fel a Blockchain Workbenchet, az olvasási engedély szükséges.

### <a name="get-application-id"></a>Alkalmazásazonosító beszerzése

Üzembe helyezés az alkalmazás azonosítója és bérlő adatai szükségesek. Gyűjti össze, és az üzembe helyezés során információit tárolja.

1. Az alkalmazás regisztrált, válassza ki a **beállítások** > **tulajdonságok**.
2. Az a **tulajdonságok** ablaktáblán, a másolás és a tároló üzembe helyezése során a következő értékeket, későbbi használatra.

    ![API-alkalmazás tulajdonságai](media/deploy/app-properties.png)

    | Történő tárolásának  | Használja a központi telepítés |
    |------------------|-------------------|
    | Alkalmazásazonosító | Az Azure Active Directory beállítása > alkalmazás azonosítója |

### <a name="get-tenant-domain-name"></a>Tartománynév-bérlő beszerzése

Gyűjti össze, és tárolja az Active Directory bérlői tartomány neve, ahol az alkalmazás regisztrálva. 

Válassza ki az **Azure Active Directory** szolgáltatást a bal oldali navigációs panelen. Válassza az **Egyéni tartománynevek** elemet. Másolja és mentse a tartomány nevét.

![Tartománynév](media/deploy/domain-name.png)

### <a name="guest-user-settings"></a>Vendég felhasználói beállítások

Ha vendég felhasználók az Azure AD-bérlővel rendelkezik, hajtsa végre a felhasználó-hozzárendelés Blockchain Workbenchet biztosításához a további lépéseket, és felügyeleti megfelelően működik.

1. Váltson, az Azure AD-bérlőhöz, és válassza ki **Azure Active Directory > felhasználói beállítások > külső együttműködési beállítások kezelése**.
2. Állítsa be **vendég felhasználók engedélyei korlátozottak** való **nem**.
    ![Külső együttműködési beállítások](media/deploy/user-collaboration-settings.png)

## <a name="configuring-the-reply-url"></a>A válasz URL-cím konfigurálása

Az Azure Blockchain Workbench üzembe helyezése után kell konfigurálni az Azure Active Directory (Azure AD) ügyfélalkalmazás **válasz URL-cím** Blockchain Workbench üzembe helyezett, webes URL-cím.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Ellenőrizze, a bérlőben, ahol regisztrálta az Azure AD-ügyfélalkalmazás.
3. Válassza ki az **Azure Active Directory** szolgáltatást a bal oldali navigációs panelen. Válassza az **Alkalmazásregisztrációk** elemet.
4. Válassza ki az Azure AD-ügyfélalkalmazás regisztrálta az Előfeltételek szakaszban.
5. Válassza ki **beállítások > válasz URL-**.
6. Az Azure Blockchain Workbench üzembe helyezés lekért fő webes URL-CÍMÉT adja meg a **lekérése az Azure Blockchain Workbench webes URL-cím** szakaszban. A válasz URL-cím a következő előtaggal kezdődik `https://`. Például: `https://myblockchain2-7v75.azurewebsites.net`

    ![Válasz URL-címek](media/deploy/configure-reply-url.png)

7. Válassza ki **mentése** ügyfél-regisztrációk frissíteni.

## <a name="remove-a-deployment"></a>Távolítsa el a központi telepítés

Ha egy telepítést már nincs rá szükség, eltávolíthatja a egy központi telepítési a Blockchain Workbenchet erőforráscsoport törlésével.

1. Az Azure Portalon lépjen **erőforráscsoport** a bal oldali navigációs panelen, és válassza ki a törölni kívánt erőforráscsoportot. 
2. Válassza az **Erőforráscsoport törlése** elemet. Az erőforráscsoport nevének megadásával erősítse meg a törlést, és válassza ki **törlése**.

    ![Erőforráscsoport törlése](media/deploy/delete-resource-group.png)

## <a name="next-steps"></a>További lépések

A cikkben található útmutató helyezett üzembe az Azure Blockchain Workbench használatával. Ismerje meg, hogyan hozhat létre a blockchain-alkalmazások, folytassa a következő cikkben található útmutató.

> [!div class="nextstepaction"]
> [A blockchain-alkalmazások létrehozása az Azure Blockchain Workbench használatával](create-app.md)
