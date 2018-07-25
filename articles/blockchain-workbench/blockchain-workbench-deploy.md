---
title: Az Azure Blockchain Workbench üzembe helyezése
description: Az Azure Blockchain Workbench üzembe helyezése
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 7/13/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 57b610b40edff56207617e212d0eb6e591ad50d4
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2018
ms.locfileid: "39224296"
---
# <a name="deploy-azure-blockchain-workbench"></a>Az Azure Blockchain Workbench üzembe helyezése

Az Azure Blockchain Workbench használatával lett telepítve. a megoldássablon az Azure piactéren. A sablon egyszerűbbé teszi a blockchain-alkalmazások létrehozásához szükséges összetevők telepítése. Miután üzembe helyezte a Blockchain Workbenchet ügyfélalkalmazások hozhat létre és kezelheti a felhasználókat és blockchain-alkalmazások hozzáférést biztosít.

A Blockchain Workbenchet az összetevőivel kapcsolatos további információkért lásd: [Azure Blockchain Workbench architektúra](blockchain-workbench-architecture.md).

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

![A központi telepítési példa](media/blockchain-workbench-deploy/example-deployment.png)

A Blockchain Workbenchet költségét az összesítést az alapul szolgáló Azure-szolgáltatások költségeinek. Díjszabási információk az Azure-szolgáltatások használatával kiszámítható az [díjkalkulátor](https://azure.microsoft.com/pricing/calculator/).

Az Azure Blockchain Workbench az üzembe helyezés előtt több előfeltételt is megkövetel. Az Előfeltételek közé tartozik az Azure AD-konfiguráció és alkalmazás registrace.

### <a name="blockchain-workbench-api-app-registration"></a>Blockchain Workbench API-alkalmazás regisztrálása

Blockchain Workbenchet telepítéséhez van szükség a regisztráció az Azure AD-alkalmazást. Szüksége lesz egy Azure Active Directory (Azure AD) bérlő az alkalmazás regisztrálásához. Használjon egy meglévő bérlőt, vagy hozzon létre egy új bérlőt. Ha egy meglévő Azure AD-bérlőt használ,-alkalmazások regisztrálását és az Azure AD-bérlő Graph API-engedélyek megadása a megfelelő engedélyekkel kell. Ha nem rendelkezik megfelelő engedélyekkel a meglévő Azure AD-bérlő hozzon létre egy új bérlőt. 

> [!IMPORTANT]
> Workbench nem kell telepíteni szeretné ugyanabban a bérlőben, azzal, regisztrálni az Azure AD-alkalmazást használ. Workbench-bérlőben, hogy megfelelő engedélyekkel rendelkezik az erőforrások üzembe helyezése kell telepíteni. További információ az Azure AD-bérlőt: [Active Directory-bérlő beszerzése](../active-directory/develop/active-directory-howto-tenant.md) és [alkalmazások integrálása az Azure Active Directory](../active-directory/develop/active-directory-integrating-applications.md).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki a fiók jobb felső sarokban, és váltson a kívánt Azure AD-bérlőben. A bérlő az előfizetés rendszergazdája bérlő az előfizetés, ahol Workbench üzembe van helyezve, és hogy megfelelő engedélyekkel rendelkezik az alkalmazásregisztrációt kell lennie.
3. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatás. Válassza ki **alkalmazásregisztrációk** > **új alkalmazásregisztráció**.

    ![Appok regisztrálása](media/blockchain-workbench-deploy/app-registration.png)

4. Adjon meg egy **neve** és **bejelentkezési URL-** az alkalmazáshoz. Helyőrző értékeket is használhat, mivel az értékek módosulnak az üzembe helyezés során. 

    ![Alkalmazásregisztráció létrehozása](media/blockchain-workbench-deploy/app-registration-create.png)

    |Beállítás  | Érték  |
    |---------|---------|
    |Name (Név) | `Blockchain API` |
    |Alkalmazástípus |Webalkalmazás/API|
    |Bejelentkezési URL | `https://blockchainapi` |

5. Válassza ki **létrehozás** regisztrálni az Azure AD-alkalmazás.

### <a name="modify-application-manifest"></a>Alkalmazásjegyzék módosítása

Ezután meg kell módosítania az alkalmazásjegyzékben, alkalmazás-szerepkörök használatához Azure AD-ben, adja meg a Blockchain Workbench használatával a rendszergazdák.  Alkalmazásjegyzéket kapcsolatos további információkért lásd: [Azure Active Directory alkalmazásjegyzékének](../active-directory/develop/active-directory-application-manifest.md).

1. Az alkalmazás regisztrált, válassza ki a **Manifest** a regisztrált alkalmazás részleteket tartalmazó ablaktáblán.
2. Hozzon létre egy GUID Azonosítót. Létrehozhat egy GUID Azonosítót a PowerShell-paranccsal [guid]:: NewGuid () vagy a New-GUID-parancsmagot. Egy másik lehetőség, hogy egy GUID-generátor webhely használatára.
3. Szeretné frissíteni a **appRoles** a jegyzékfájl rész. Az Edit manifest ablaktáblán jelölje ki **szerkesztése** , és cserélje le `"appRoles": []` a megadott JSON-fájllal. Ügyeljen arra, hogy értékét cserélje le a **azonosító** mezőt a létrehozott GUID. 

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

    ![Jegyzékfájl szerkesztése](media/blockchain-workbench-deploy/edit-manifest.png)

    > [!IMPORTANT]
    > Az érték **rendszergazda** Blockchain Workbench használatával a rendszergazdák azonosításához szükséges.

4.  Kattintson a **mentése** application manifest módosításainak mentéséhez.

### <a name="add-graph-api-required-permissions"></a>Graph API-hoz szükséges engedélyek hozzáadása

Az API-alkalmazást kell engedélykérés a felhasználótól a könyvtár eléréséhez. Állítsa be a következő szükséges engedélyt az API-alkalmazás:

1. Válassza ki a Blockchain API alkalmazásregisztráció **beállítások > szükséges engedélyek > API kiválasztása > a Microsoft Graph**.

    ![API kiválasztása](media/blockchain-workbench-deploy/client-app-select-api.png)

    Kattintson a **Kiválasztás** gombra.

2. A **hozzáférés engedélyezése** alatt **Alkalmazásengedélyek**, válassza a **a felhasználók összes profiladatának olvasása**.

    ![Hozzáférés engedélyezése](media/blockchain-workbench-deploy/client-app-read-perms.png)

    Kattintson a **kiválasztása** kattintson **kész**.

3. A **szükséges engedélyek**válassza **engedélyek megadása** majd **Igen** a hitelesítési paramétert.

   ![Engedélyek megadása](media/blockchain-workbench-deploy/client-app-grant-permissions.png)

   Engedély megadása lehetővé teszi a Blockchain Workbenchet eléréséhez a felhasználók a címtárban. Keressen, és tagokat vehet fel a Blockchain Workbenchet, az olvasási engedély szükséges.

### <a name="add-graph-api-key-to-application"></a>Graph API-kulcs hozzáadása az alkalmazáshoz

Blockchain Workbench az Azure AD, a fő identitáskezelési rendszerének blockchain-alkalmazások kommunikáló felhasználók számára. Ahhoz, hogy a Blockchain Workbenchet el az Azure AD és a felhasználói adatok, például neveket és e-mailek lekérése, hozzá kell egy hozzáférési kulcsot. Blockchain Workbenchet a kulcsot az Azure AD-hitelesítést használ.

1. Az alkalmazás regisztrált, válassza ki a **beállítások** a regisztrált alkalmazás részleteket tartalmazó ablaktáblán.
2. Válassza a **Kulcsok** elemet.
3. Adjon hozzá egy új kulcsot eltérő kulcs megadásával **leírás** választva **lejár** időtartamértéket. 

    ![Kulcs létrehozása](media/blockchain-workbench-deploy/app-key-create.png)

    |Beállítás  | Érték  |
    |---------|---------|
    | Leírás | `Service` |
    | Elévül | Válasszon egy elévülési ideje |

4. Kattintson a **Mentés** gombra. 
5. Másolja a kulcs értékét, és tárolhatja későbbi használatra. Központi telepítés van szükség.

    > [!IMPORTANT]
    >  Ha nem menti a központi telepítés a kulcsot, hozzon létre egy új kulcsot kell. A kulcs értéke nem olvasható be a portálról később.

### <a name="get-application-id"></a>Alkalmazásazonosító beszerzése

Üzembe helyezés az alkalmazás azonosítója és bérlő adatai szükségesek. Gyűjti össze, és az üzembe helyezés során információit tárolja.

1. Az alkalmazás regisztrált, válassza ki a **beállítások** > **tulajdonságok**.
2.  Az a **tulajdonságok** ablaktáblán, a másolás és a tároló üzembe helyezése során a következő értékeket, későbbi használatra.

    ![API-alkalmazás tulajdonságai](media/blockchain-workbench-deploy/app-properties.png)

    | Történő tárolásának  | Használja a központi telepítés |
    |------------------|-------------------|
    | Alkalmazásazonosító | Az Azure Active Directory beállítása > alkalmazás azonosítója |

### <a name="get-tenant-domain-name"></a>Tartománynév-bérlő beszerzése

Gyűjti össze, és tárolja az Active Directory bérlői tartomány neve, ahol az alkalmazás regisztrálva. 

A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatás. Válassza az **Egyéni tartománynevek** elemet. Másolja és mentse a tartomány nevét.

![Tartománynév](media/blockchain-workbench-deploy/domain-name.png)

## <a name="deploy-blockchain-workbench"></a>Blockchain Workbench üzembe helyezése

Az előfeltételként felsorolt lépéseket befejezése után készen áll a Blockchain Workbench üzembe helyezése. A következő szakaszok szerkezeti központi telepítése keretében.

1.  Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2.  Válassza ki a fiókját a jobb felső sarokban sarokban, majd váltson át a kívánt Azure AD bérlői, ahol szeretné az Azure Blockchain Workbench üzembe helyezése.
3.  A bal oldali panelen válassza ki a **erőforrás létrehozása**. Keresse meg `Azure Blockchain Workbench` a a **keresés a piactéren** keresősávba. 

    ![Marketplace-en keresősáv](media/blockchain-workbench-deploy/marketplace-search-bar.png)

4.  Válassza ki **az Azure Blockchain Workbench**.

    ![Marketplace-en keresési eredmények](media/blockchain-workbench-deploy/marketplace-search-results.png)

4.  Kattintson a **Létrehozás** gombra.
5.  Végezze el az alapszintű beállításokat.

    ![Az Azure Blockchain Workbench létrehozása](media/blockchain-workbench-deploy/blockchain-workbench-settings-basic.png)

    | Beállítás | Leírás  |
    |---------|--------------|
    | Erőforrás-előtag | Az üzembe helyezés rövid egyedi azonosítója. Ez az érték erőforrások elnevezési alapjául szolgál. |
    | Virtuális gép felhasználóneve | A felhasználónév rendszergazdaként szolgál az összes virtuális gép (VM). |
    | Hitelesítés típusa | Válassza ki, ha azt szeretné, a jelszót, vagy csatlakozhat a virtuális gépek kulcs. |
    | Jelszó | A jelszó csatlakozik a virtuális gépek szolgál. |
    | SSH | Nyilvános RSA-kulcsot használja az egysoros kiadásától kezdve a **ssh-rsa** vagy a többsoros PEM formátumban. Az SSH-kulcsok használatával is létrehozhat `ssh-keygen` Linux-és OS X, illetve a Windows PuTTYGen használatával. Az SSH-kulcsokat, további információt talál [az SSH használata a Windows Azure-beli kulcsok](../virtual-machines/linux/ssh-from-windows.md). |
    | Adatbázis-jelszó / adatbázis jelszó megerősítése | Adja meg a központi telepítés részeként létrehozta az adatbázis eléréséhez használt jelszó. |
    | Az üzembe helyezés régiójában | Adja meg az üzembe helyezés a Blockchain Workbenchet erőforrásokat. A legjobb rendelkezésre állás érdekében ennek meg kell egyeznie a **hely** beállítás. |
    | Előfizetés | Adja meg a központi telepítéshez használni kívánt Azure-előfizetést. |
    | Erőforráscsoportok | Hozzon létre egy új erőforráscsoportot kiválasztásával **új létrehozása** , és adja meg egy egyedi erőforráscsoport-nevet. |
    | Hely | Adja meg a régiót kívánja telepíteni a keretrendszer. |

6.  Válassza ki **OK** az alapszintű konfigurációs szakasz befejezéséhez.

7.  Végezze el a **Azure Active Directory beállítása**.

    ![Az Azure AD beállítása](media/blockchain-workbench-deploy/blockchain-workbench-settings-aad.png)

    | Beállítás | Leírás  |
    |---------|--------------|
    | Tartománynév | Használja az Azure AD-bérlő gyűjti a [Get-bérlői tartomány neve](#get-tenant-domain-name) Előfeltételek szakasz. |
    | Alkalmazásazonosító | Használja a Blockchain-ügyfél alkalmazásregisztráció Alkalmazásazonosítója gyűjti a [Alkalmazásazonosító beszerzése](#get-application-id) Előfeltételek szakasz. |
    | Alkalmazáskulcs | Használja az alkalmazáskulcsot az a Blockchain ügyfél alkalmazásregisztráció gyűjti a [alkalmazás hozzáadása a Graph API-val kulcs](#add-graph-api-key-to-application) Előfeltételek szakasz. |


8.  Kattintson a **OK** az Azure AD-paraméterek konfigurációs szakasz befejezéséhez.

9.  A **hálózati beállításokat és a teljesítmény**, adja meg, ha egy új blockchain-hálózat létrehozása vagy meglévő koncepció jogosultság blockchain-hálózat használata.

    A **új létrehozása**:

    A *létrehozása új* kapcsoló meg egyetlen tagot az előfizetésen belül Ethereum Proof-szolgáltató (PoA) csomópontok hozza létre. 

    ![Hálózati beállítások és a teljesítmény](media/blockchain-workbench-deploy/blockchain-workbench-settings-network-new.png)

    | Beállítás | Leírás  |
    |---------|--------------|
    | A blockchain-csomópontok száma | Adja meg, Ethereum PoA érvényesítő csomópontok üzembe helyezni a hálózaton. |
    | Tároló-teljesítményre | Válassza ki a virtuális gép előnyben részesített tárolási teljesítmény a blockchain-hálózat. |
    | Virtuális gép mérete | Válassza ki a kívánt Virtuálisgép-méretet a blockchain-hálózat. |

    A **meglévő**:

    A *meglévő* beállítás lehetővé teszi az Ethereum koncepció jogosultság (PoA) blockchain-hálózat megadása. Végpontok az alábbi követelményekkel rendelkezik.

    * A végpont egy Ethereum koncepció jogosultság (PoA) blockchain hálózaton kell lennie.
    * A végpont nyilvánosan elérhetőnek kell lennie a hálózaton keresztül.
    * A blockchain PoA hálózati konfigurálni kell, hogy rendelkezik nulla gáz ár (Megjegyzés: Blockchain Workbenchet fiókok vannak nem támogatott. Ha alapok szükség, a tranzakciók sikertelen).

    ![Hálózati beállítások és a teljesítmény](media/blockchain-workbench-deploy/blockchain-workbench-settings-network-existing.png)

    | Beállítás | Leírás  |
    |---------|--------------|
    | RPC Ethereum-végpont | Adjon meg egy létező PoA blockchain-hálózat RPC-végpont. A végpont http:// előtaggal kezdődik és végződik a port számát. Például: `http://contoso-chain.onmicrosoft.com:8545` |
    | Tároló-teljesítményre | Válassza ki a virtuális gép előnyben részesített tárolási teljesítmény a blockchain-hálózat. |
    | Virtuális gép mérete | Válassza ki a kívánt Virtuálisgép-méretet a blockchain-hálózat. |

10. Válassza ki **OK** befejezéséhez a hálózati beállításokat és a teljesítményt.

11. Végezze el a **Azure Monitor** beállításait.

    ![Azure Monitor](media/blockchain-workbench-deploy/blockchain-workbench-settings-oms.png)

    | Beállítás | Leírás  |
    |---------|--------------|
    | Figyelés | Válassza ki, hogy szeretné-e a blockchain-hálózat figyelése az Azure Monitor engedélyezése |
    | Kapcsolódás a meglévő Log Analytics-példány | Válasszon egy meglévő szeretné-e a Log Analytics-példány, vagy hozzon létre egy újat. Ha egy meglévő példányát használja, adja meg a munkaterület-Azonosítót és elsődleges kulcsot. |

12. Kattintson a **OK** az Azure Monitor szakasz befejezéséhez.

13. Tekintse át az összefoglalást, ellenőrizze a paraméterek pontosak.

    ![Összegzés](media/blockchain-workbench-deploy/blockchain-workbench-summary.png)

14. Válassza ki **létrehozás** fogadja el a feltételeket, és üzembe helyezése az Azure Blockchain Workbench használatával.

A központi telepítés akár 90 percet is igénybe vehet. Az Azure portal segítségével a folyamat állapotának monitorozásához. Válassza ki az újonnan létrehozott erőforráscsoport **központi telepítések > Áttekintés** a telepített összetevők állapotának megjelenítéséhez.

## <a name="blockchain-workbench-web-url"></a>Blockchain Workbenchet webes URL-címe

A Blockchain Workbenchet központi telepítésének befejezése után egy új erőforráscsoport tartalmazza a Blockchain Workbenchet erőforrások. Blockchain Workbenchet szolgáltatások érhetők el egy webes URL-címmel. A következő lépések bemutatják, hogyan kérheti le az üzembe helyezett keretrendszer webes URL-CÍMÉT.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldali navigációs ablaktáblán válassza ki a **erőforráscsoportok**
3. Válassza ki az erőforráscsoport nevének Blockchain Workbench üzembe helyezésekor megadott.
4. Kattintson a **típus** oszlopfejrészre kattintva rendezze betűrendbe típus szerint a listában.
5. Két forrásanyag típusú **App Service-ben**. Válassza ki az erőforrást típusú **App Service-ben** *nélkül* a "-api" utótag.

    ![App service-lista](media/blockchain-workbench-deploy/resource-group-list.png)

6.  Az App Service-ben **Essentials** területén másolja a **URL-cím** érték, amely az üzembe helyezett Blockchain Workbenchet a webes URL-címet jelöli.

    ![App service alapjai](media/blockchain-workbench-deploy/app-service.png)

Egyéni tartománynév társítandó Blockchain Workbenchet, lásd: [webes alkalmazás egy egyéni tartománynév konfigurálása az Azure App Service, Traffic Managert használva a](../app-service/web-sites-traffic-manager-custom-domain-name.md).

## <a name="configuring-the-reply-url"></a>A válasz URL-cím konfigurálása

Az Azure Blockchain Workbench üzembe helyezése után a következő lépés az, hogy az Azure Active Directory (Azure AD) ügyfélalkalmazás regisztrálva van a megfelelő **válasz URL-cím** Blockchain Workbench üzembe helyezett, webes URL-cím.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Ellenőrizze, a bérlőben, ahol regisztrálta az Azure AD-ügyfélalkalmazás.
3. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatás. Válassza az **Alkalmazásregisztrációk** elemet.
4. Válassza ki az Azure AD-ügyfélalkalmazás regisztrálta az Előfeltételek szakaszban.
5. Válassza ki **beállítások > válasz URL-**.
6. Az Azure Blockchain Workbench üzembe helyezés lekért fő webes URL-CÍMÉT adja meg a **lekérése az Azure Blockchain Workbench webes URL-cím** szakaszban. A válasz URL-cím a következő előtaggal kezdődik `https://`. Például: `https://myblockchain2-7v75.azurewebsites.net`

    ![Válasz URL-címek](media/blockchain-workbench-deploy/configure-reply-url.png)

7. Válassza ki **mentése** ügyfél-regisztrációk frissíteni.

## <a name="remove-a-deployment"></a>Távolítsa el a központi telepítés

Ha egy telepítést már nincs rá szükség, eltávolíthatja a egy központi telepítési a Blockchain Workbenchet erőforráscsoport törlésével.

1. Az Azure Portalon lépjen **erőforráscsoport** a bal oldali navigációs panelen, és válassza ki a törölni kívánt erőforráscsoportot. 
2. Válassza az **Erőforráscsoport törlése** elemet. Az erőforráscsoport nevének megadásával erősítse meg a törlést, és válassza ki **törlése**.

    ![Erőforráscsoport törlése](media/blockchain-workbench-deploy/delete-resource-group.png)

## <a name="next-steps"></a>További lépések

A cikkben található útmutató helyezett üzembe az Azure Blockchain Workbench használatával. Ismerje meg, hogyan hozhat létre a blockchain-alkalmazások, folytassa a következő cikkben található útmutató.

> [!div class="nextstepaction"]
> [A blockchain-alkalmazások létrehozása az Azure Blockchain Workbench használatával](blockchain-workbench-create-app.md)
