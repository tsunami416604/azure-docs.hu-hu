---
title: Az Azure Blockchain munkaterület központi telepítése
description: Azure Blockchain munkaterület központi telepítése
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/17/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: bcd08ac8563edfaf4297e26ad42ed8bc62d86918
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34831635"
---
# <a name="deploy-azure-blockchain-workbench"></a>Az Azure Blockchain munkaterület központi telepítése

Az Azure Blockchain munkaterület használatával lett telepítve. a megoldássablon az Azure piactéren. A sablon az összetevők blockchain alkalmazások létrehozásához szükséges telepítési egyszerűbbé teszi. Amennyiben telepített, akkor Blockchain munkaterület ügyfél alkalmazások létrehozását és kezelését a felhasználók és az alkalmazások blockchain hozzáférést biztosít.

Blockchain munkaterület az összetevőivel kapcsolatos további információkért lásd: [Azure Blockchain munkaterület architektúra](blockchain-workbench-architecture.md).

## <a name="prepare-for-deployment"></a>Felkészülés az üzembe helyezésre

Blockchain munkaterület lehetővé teszi egy blockchain főkönyvi együtt megfelelő Azure szolgáltatások leggyakrabban használt hozható létre olyan blockchain-alapú alkalmazás telepítését. Az Azure-szolgáltatásokat az Azure-előfizetéshez az erőforráscsoporton belül létre eredményezi Blockchain munkaterület központi telepítése.

* 1 esemény rács témakör
* 1 Service Bus Namespace
* 1 application insights szolgáltatással
* 1 SQL-adatbázis (standard szintű, S0)
* 2 alkalmazásszolgáltatások (normál)
* 2 azure kulcstárolójának
* 2 azure Storage-fiókok (Standard-LRS)
* 2 virtuálisgép-méretezési csoportok (az érvényesítési és a munkavégző csomópont)
* (Beleértve a terheléselosztóhoz, a hálózati biztonsági csoport és a nyilvános IP-cím minden virtuális hálózathoz), 2 virtuális hálózatok
* Választható lehetőség: Az Azure-figyelő

Az alábbiakban egy létrehozott központi telepítésre példát **myblockchain** erőforráscsoportot.

![A központi telepítési példa](media/blockchain-workbench-deploy/example-deployment.png)

Blockchain munkaterület költsége az alapul szolgáló Azure-szolgáltatás költségének aggregátum. Díjszabási információkért az Azure-szolgáltatások használatával kell kiszámítani a [árképzési Számológép](https://azure.microsoft.com/pricing/calculator/).

Azure Blockchain munkaterület szükséges a telepítés előtt több előfeltételnek. Az Előfeltételek például a következők az Azure AD-konfiguráció és az alkalmazás regisztráció.

### <a name="blockchain-workbench-api-app-registration"></a>Blockchain munkaterület API app regisztrációs

Blockchain munkaterület telepítéséhez szükséges egy Azure AD-alkalmazás regisztrációja. Meg kell regisztrálnia az alkalmazást az Azure Active Directory (Azure AD) bérlő. Használjon egy meglévő bérlői, vagy hozzon létre egy új bérlőt. Meglévő Azure AD-bérlő használ, ha regisztrálja az alkalmazást az Azure AD-bérlő elegendő jogosultság szükséges. Az alkalmazás regisztrációk kell lennie az előfizetéshez rendszergazdai bérlő az előfizetés munkaterület telepítési helyét. Az Azure AD-bérlő további információkért lásd: [az Active Directory-bérlő beszerzése](../active-directory/develop/active-directory-howto-tenant.md) és [alkalmazások integrálása az Azure Active Directory](../active-directory/develop/active-directory-integrating-applications.md).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki a fiókot a jobb felső sarokban, és váltson a kívánt Azure ad bérlői. A bérlő az előfizetés rendszergazdai bérlő az előfizetés, ahol munkaterület van telepítve, és rendelkezik-e engedélyekkel alkalmazások regisztrálni kell.
3. A bal oldali navigációs ablaktáblán válassza ki a **Azure Active Directory** szolgáltatás. Válassza ki **App regisztrációk** > **új alkalmazás regisztrációja**.

    ![Appok regisztrálása](media/blockchain-workbench-deploy/app-registration.png)

4. Adjon meg egy **neve** és **bejelentkezési URL-cím** az alkalmazáshoz. Helyőrző értékeket is használhat, mivel a telepítés során az értékek megváltoztak. 

    ![Alkalmazás regisztrálása létrehozása](media/blockchain-workbench-deploy/app-registration-create.png)

    |Beállítás  | Érték  |
    |---------|---------|
    |Name (Név) | `Blockchain API` |
    |Alkalmazástípus |Webalkalmazás/API|
    |Bejelentkezési URL | `https://blockchainapi` |

5. Válassza ki **létrehozása** az Azure AD-alkalmazás regisztrálásához.

### <a name="modify-application-manifest"></a>Az alkalmazásjegyzék módosítása

A következő módosítania kell az alkalmazás jegyzékében belül az Azure AD alkalmazás-szerepkörök használni Blockchain munkaterület a rendszergazdák.  Az alkalmazásjegyzékeknek kapcsolatos további információkért lásd: [Azure Active Directory alkalmazásjegyzékének](../active-directory/develop/active-directory-application-manifest.md).

1. A regisztrált alkalmazáshoz, jelölje ki a **Manifest** a regisztrált alkalmazáshoz részleteket tartalmazó ablaktáblán.
2. Hozzon létre egy GUID Azonosítót. Létrehozhat egy GUID, [guid] PowerShell-parancs használatával:: NewGuid () vagy a New-GUID-parancsmagot. Egy másik lehetőség, hogy egy GUID-generátor webhelyet használja.
3. Frissíteni kívánja a **appRoles** a jegyzékfájl rész. A Szerkesztés jegyzék ablaktábla, válassza a **szerkesztése** , és cserélje le `"appRoles": []` , a megadott JSON-NÁ. Ügyeljen arra, hogy cserélje le a értéke a **azonosító** mezőt a létrehozott GUID. 

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
    > Az érték **rendszergazda** Blockchain munkaterület a rendszergazdák azonosításához van szükség.

4.  Kattintson a **mentése** az alkalmazás jegyzékének módosítások mentéséhez.

### <a name="add-graph-api-required-permissions"></a>A Graph API szükséges engedélyek hozzáadása

Az API-alkalmazást kell engedélykérés a felhasználótól a könyvtár eléréséhez. Állítsa be a következő szükséges engedélyekkel, az API-alkalmazáshoz:

1. Válassza ki a Blockchain API app regisztrációs **beállítások > szükséges engedélyek > API kiválasztása > Microsoft Graph**.

    ![API kiválasztása](media/blockchain-workbench-deploy/client-app-select-api.png)

    Kattintson a **Kiválasztás** gombra.

2. A **hozzáférés engedélyezése** alatt **Alkalmazásengedélyek**, válassza a **összes profiladatának olvasása**.

    ![Hozzáférés engedélyezése](media/blockchain-workbench-deploy/client-app-read-perms.png)

    Kattintson a **válasszon** kattintson **végzett**.

3. A **szükséges engedélyek**, jelölje be **engedélyt adjon** válassza **Igen** meghatározó ellenőrző megkötéseket.

   ![Engedélyek megadása](media/blockchain-workbench-deploy/client-app-grant-permissions.png)

   Engedély megadása lehetővé teszi, hogy a címtárban szereplő felhasználó eléréséhez Blockchain munkaterület. Az olvasási hozzáférést keresheti ki és tagokat vehet fel Blockchain munkaterület szükséges.

### <a name="add-graph-api-key-to-application"></a>Graph API-kulcs hozzáadása az alkalmazáshoz

Blockchain munkaterület használja az Azure AD a fő felügyeleti identitásrendszere felhasználók blockchain alkalmazások való interakció. Ahhoz, hogy az Azure AD eléréséhez, és a felhasználói adatok, például neveket és e-mailek lekérése Blockchain munkaterület kell hozzáadnia az elérési kulcsot. Blockchain munkaterület kulcsot használja az Azure AD szolgáltatással való hitelesítésre.

1. A regisztrált alkalmazáshoz, jelölje ki a **beállítások** a regisztrált alkalmazáshoz részleteket tartalmazó ablaktáblán.
2. Válassza a **Kulcsok** elemet.
3. Adja hozzá egy új kulcsot a kulcs megadásával **leírás** kiválasztásával **lejár** az időtartamot. 

    ![Kulcs létrehozása](media/blockchain-workbench-deploy/app-key-create.png)

    |Beállítás  | Érték  |
    |---------|---------|
    | Leírás | `Service` |
    | Elévül | Válassza ki a lejárati idő |

4. Kattintson a **Mentés** gombra. 
5. Másolja a kulcs értékét, és tárolja a későbbi használatra. Központi telepítéshez van szükség.

    > [!IMPORTANT]
    >  Ha a kulcs az üzemelő példány nem menti, akkor hozzon létre egy új kulcsot. A kulcs értéke később nem tudja beolvasni a portálról.

### <a name="get-application-id"></a>Alkalmazásazonosító beszerzése

Az alkalmazás Azonosítóját és a bérlői adatai szükségesek telepítéséhez. Összegyűjtése és központi telepítése során való használatra az adatok tárolásához.

1. A regisztrált alkalmazáshoz, jelölje ki a **beállítások** > **tulajdonságok**.
2.  Az a **tulajdonságok** ablaktáblán, a másolási és a tároló üzembe helyezése során a következő értékeket, későbbi használatra.

    ![API-alkalmazás tulajdonságainak](media/blockchain-workbench-deploy/app-properties.png)

    | Történő tárolásának  | Használja a központi telepítés |
    |------------------|-------------------|
    | Alkalmazásazonosító | Az Azure Active Directory beállítása > alkalmazás azonosítója |

### <a name="get-tenant-domain-name"></a>Tartománynév-bérlő beszerzése

Gyűjthet, és tárolja az Active Directory bérlői tartomány neve, ahol az alkalmazás regisztrálva. 

A bal oldali navigációs ablaktáblán válassza ki a **Azure Active Directory** szolgáltatás. Válassza ki **egyéni tartománynevek**. Másolja, és tárolja a tartomány nevét.

![Tartománynév](media/blockchain-workbench-deploy/domain-name.png)

## <a name="deploy-blockchain-workbench"></a>Blockchain munkaterület központi telepítése

Az előfeltételként szükséges lépések elvégzése után, készen áll a Blockchain munkaterület központi telepítéséhez. Az alábbi szakaszok felsorolják központi telepítése keretében.

1.  Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2.  Jelölje ki a fiókot a felső sarokban, és váltson a kívánt Azure AD bérlői, ahol az Azure Blockchain munkaterület telepíteni szeretné.
3.  A bal oldali panelen válassza ki a **hozzon létre egy erőforrást**. Keresse meg `Azure Blockchain Workbench` a a **keresése a piactéren** keresősáv. 

    ![Piactér keresősáv](media/blockchain-workbench-deploy/marketplace-search-bar.png)

4.  Válassza ki **Azure Blockchain munkaterület**.

    ![Piactér keresési eredmények](media/blockchain-workbench-deploy/marketplace-search-results.png)

4.  Kattintson a **Létrehozás** gombra.
5.  Végezze el az egyszerű beállítások.

    ![Az Azure Blockchain munkaterület létrehozása](media/blockchain-workbench-deploy/blockchain-workbench-settings-basic.png)

    | Beállítás | Leírás  |
    |---------|--------------|
    | Erőforrás-előtag | A központi telepítés rövid egyedi azonosítója. Ezt az értéket erőforrások elnevezési alapjaként használja. |
    | Virtuális gép felhasználónév | A felhasználónév az összes virtuális gép (VM) használatos rendszergazdaként. |
    | Hitelesítés típusa | Válassza ki, ha azt szeretné, használhat olyan jelszót, vagy a kulcsot a következőhöz csatlakozni a virtuális gépek. |
    | Jelszó | A jelszó szolgál a virtuális gépeken való kapcsolódáshoz. |
    | SSH | Nyilvános RSA-kulcs használata az egysoros formátum kezdődő **ssh-rsa** , vagy használja a többsoros PEM-formátumba. SSH-kulcsok használatával hozhat létre `ssh-keygen` Linux- és OS X, vagy a PuTTYGen Windows rendszeren. SSH-kulcsokat, a további tudnivalókat lásd a [SSH használata a Windows Azure-kulcsok](../virtual-machines/linux/ssh-from-windows.md). |
    | Adatbázis-jelszó / adatbázis jelszó megerősítése | Adja meg a központi telepítés részeként létrehozott adatbázis eléréséhez használandó jelszó. |
    | Központi telepítés régió | Adja meg a telepítendő Blockchain munkaterület erőforrások helyét. A legjobb rendelkezésre állás érdekében ez meg kell felelnie a **hely** beállítást. |
    | Előfizetés | Adja meg a telepítéshez használni kívánt Azure-előfizetést. |
    | Erőforráscsoportok | Hozzon létre egy új erőforráscsoportot kiválasztásával **hozzon létre új** , és adja meg egy egyedi erőforráscsoport neve. |
    | Hely | Adja meg a régió kívánja telepíteni a keretrendszer. |

6.  Válassza ki **OK** az alapszintű beállítás konfigurációs szakasz befejezéséhez.

7.  Fejezze be a **Azure Active Directory telepítő**.

    ![Az Azure AD beállítása](media/blockchain-workbench-deploy/blockchain-workbench-settings-aad.png)

    | Beállítás | Leírás  |
    |---------|--------------|
    | Tartománynév | Használja az Azure AD bérlő gyűjti a [Get-bérlő tartománynevét](#get-tenant-domain-name) című cikk Előfeltételek szakaszát. |
    | Alkalmazásazonosító | Használja az Blockchain app regisztráció az Alkalmazásazonosító gyűjti a [Alkalmazásazonosító beszerzése](#get-application-id) című cikk Előfeltételek szakaszát. |
    | Alkalmazáskulcs | Az alkalmazás-kulcsot az összegyűjtött Blockchain app regisztráció használja a [alkalmazás hozzáadása Graph API-kulcs](#add-graph-api-key-to-application) című cikk Előfeltételek szakaszát. |


8.  Kattintson a **OK** az Azure AD-paraméterek konfigurációs szakasz befejezéséhez.

9.  Fejezze be a **hálózati mérete és a teljesítmény** beállításait.

    ![Érvényben lévő hálózati és teljesítmény](media/blockchain-workbench-deploy/blockchain-workbench-settings-network.png)

    | Beállítás | Leírás  |
    |---------|--------------|
    | Blockchain csomópontok száma | Adja meg, Ethereum PoA érvényesítő csomópontok a hálózat üzembe helyezhető. |
    | Tárolási teljesítmény | Válassza ki az elsődleges virtuális gép tárolási teljesítményének megjelenítése a blockchain hálózati. |
    | Virtuális gép mérete | Válassza ki a blockchain hálózati az elsődleges Virtuálisgép-méretet. |

10. Válassza ki **OK** a hálózat mérete és teljesítménye szakasz befejezéséhez.

11. Fejezze be a **Azure figyelő** beállításait.

    ![Azure Monitor](media/blockchain-workbench-deploy/blockchain-workbench-settings-oms.png)

    | Beállítás | Leírás  |
    |---------|--------------|
    | Figyelés | Válassza ki, hogy később engedélyezni kívánja a blockchain hálózat figyelése Azure figyelője |
    | Csatlakozás meglévő Naplóelemzési példányhoz | Válassza ki, hogy egy meglévő használni kívánt Naplóelemzési példányt, vagy hozzon létre egy újat. Ha egy meglévő példányát használja, adja meg a munkaterület azonosítója és az elsődleges kulcs. |

12. Kattintson a **OK** az Azure-figyelő szakasz befejezéséhez.

13. Tekintse át az összefoglalást, hogy ellenőrizze a paramétereket pontosak.

    ![Összegzés](media/blockchain-workbench-deploy/blockchain-workbench-summary.png)

14. Válassza ki **létrehozása** elfogadja a feltételeket, és az Azure Blockchain munkaterület telepítéséhez.

A központi telepítés akár 90 percig is tarthat. Az Azure portál segítségével figyelemmel az előrehaladást. Válassza ki az újonnan létrehozott erőforráscsoport **központi telepítések > Áttekintés** a telepített összetevők állapotát tekintheti meg.

## <a name="blockchain-workbench-web-url"></a>Blockchain munkaterület webes URL-címe

A Blockchain munkaterület központi telepítésének befejezése után egy új erőforráscsoport a Blockchain munkaterület erőforrásai. Blockchain munkaterület szolgáltatások egy webes URL-címe keresztül érhetők el. A következő lépések bemutatják a lekérni a webalkalmazás URL-címet a telepített keretrendszer.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldali navigációs ablaktáblán válassza ki a **erőforráscsoportok**
3. Válassza ki az erőforráscsoport neve megadott Blockchain munkaterület központi telepítésekor.
4. Kattintson a **típus** oszlopfejrészre kattintva rendezheti a listát típus szerint.
5. A típus két erőforrás **App Service**. Válassza ki az erőforrás típusa **App Service** *nélkül* a "-api" utótag.

    ![App service listája](media/blockchain-workbench-deploy/resource-group-list.png)

6.  Az az App Service-ben **Essentials** szakaszban, másolja a **URL-cím** értéket, amely a telepített Blockchain munkaterület a webalkalmazás URL-címet jelöli.

    ![App service alapjai](media/blockchain-workbench-deploy/app-service.png)

Egy egyéni tartománynevet társítandó Blockchain munkaterület, lásd: [egy webalkalmazást az egyéni tartománynév konfigurálása az Azure App Service szolgáltatásban a Traffic Managerrel](../app-service/web-sites-traffic-manager-custom-domain-name.md).

## <a name="configuring-the-reply-url"></a>A válasz URL-cím konfigurálása

Miután az Azure-Blockchain munkaterület van telepítve, a következő lépéssel hoz győződjön meg arról, hogy az Azure Active Directory (Azure AD) ügyfélalkalmazás regisztrálva van a megfelelő **válasz URL-CÍMEN** a telepített Blockchain munkaterület webes URL-címhez.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Ellenőrizze, ahol az Azure AD-ügyfélalkalmazás regisztrálta a bérlő.
3. A bal oldali navigációs ablaktáblán válassza ki a **Azure Active Directory** szolgáltatás. Válassza az **Alkalmazásregisztrációk** elemet.
4. Válassza ki az Előfeltételek szakaszában regisztrált az Azure AD-ügyfélalkalmazás.
5. Válassza ki **beállítások > válasz URL-címek**.
6. Az elsődleges webes URL-cím az Azure Blockchain munkaterület központi telepítés olvassa be a **Azure Blockchain munkaterület webes URL-cím beszerzése** szakasz. A válasz URL-CÍMEN nem előzi meg a `https://`. Például: `https://myblockchain2-7v75.azurewebsites.net`

    ![Válasz URL-címek](media/blockchain-workbench-deploy/configure-reply-url.png)

7. Válassza ki **mentése** ügyfél-regisztrációk frissítése.

## <a name="remove-a-deployment"></a>Távolítsa el a telepítést

Ha már nincs szükség a központi telepítés, eltávolíthatja a központi telepítés törölni kell a Blockchain munkaterület-csoport.

1. Az Azure-portálon lépjen a **erőforráscsoport** a bal oldali navigációs panelen, és válassza ki a törölni kívánt erőforráscsoportot. 
2. Válassza az **Erőforráscsoport törlése** elemet. Törlés ellenőrzéséhez írja be az erőforráscsoport neve, és válassza ki **törlése**.

    ![Erőforráscsoport törlése](media/blockchain-workbench-deploy/delete-resource-group.png)

## <a name="next-steps"></a>További lépések

Útmutató cikkben telepített Azure Blockchain munkaterületet. Megtudhatja, hogyan hozhat létre egy blockchain alkalmazást, folytassa a következő cikkben található útmutató.

> [!div class="nextstepaction"]
> [Azure Blockchain munkaterület blockchain-alkalmazás létrehozása](blockchain-workbench-create-app.md)
