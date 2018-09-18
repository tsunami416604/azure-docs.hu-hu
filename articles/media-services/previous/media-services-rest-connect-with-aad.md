---
title: Az Azure AD hitelesítési el az Azure Media Services API REST-tel |} A Microsoft Docs
description: Ismerje meg az Azure Media Services API elérése az Azure Active Directory-hitelesítést a REST használatával.
services: media-services
documentationcenter: ''
author: willzhan
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/26/2017
ms.author: willzhan;juliako;johndeu
ms.openlocfilehash: b44c5b29f41f08ac5c1d5e6aa3c37f8c15ae7c54
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "45984657"
---
# <a name="use-azure-ad-authentication-to-access-the-azure-media-services-api-with-rest"></a>Az Azure AD-hitelesítés használata az Azure Media Services API REST-tel eléréséhez

Ha az Azure Media Services Azure AD-hitelesítést használ, két módon hitelesítheti:

- **Felhasználói hitelesítés** hitelesíti magát az a személy, aki használja az alkalmazást kezelheti az Azure Media Services-erőforrások. Az interaktív alkalmazás először kéri a felhasználótól a hitelesítő adatokat. Ilyen például, a kódolási feladatok figyelésére, vagy az élő adások online közvetítése jogosult felhasználók által használt felügyeleti konzolalkalmazást. 
- **Egyszerű szolgáltatásnév hitelesítése** szolgáltatás hitelesíti. Alkalmazásokat, amelyek gyakran használják ezt a hitelesítési módszert démonszolgáltatásokat, a középső rétegű services vagy az ütemezett feladatok, például a web apps, a függvényalkalmazások, a logic apps, API-k vagy mikroszolgáltatás-alapú alkalmazások.

    Az oktatóanyag bemutatja, hogyan használható az Azure ad-ben **szolgáltatásnév** elérni az AMS API REST-tel való hitelesítéshez. 

    > [!NOTE]
    > **Egyszerű szolgáltatás** van az ajánlott eljárás a legtöbb alkalmazás csatlakozik az Azure Media Services. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A hitelesítési adatok lekérése az Azure Portalról
> * A Postman segítségével hozzáférési jogkivonat beszerzése
> * Teszt a **eszközök** API hozzáférési token használatával


> [!IMPORTANT]
> Jelenleg a Media Services támogatja az Azure Access Control szolgáltatás hitelesítési modellre. Azonban hozzáférés-vezérlés hitelesítés 2018. június 1. megszűnnek. Javasoljuk, hogy mielőbb térjen át az Azure AD-hitelesítési modellre.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) a virtuális gép létrehozásának megkezdése előtt.
- [Hozzon létre egy Azure Media Services-fiók az Azure portal használatával](media-services-portal-create-account.md).
- Tekintse át a [elérése az Azure Media Services API AAD-hitelesítés – áttekintés](media-services-use-aad-auth-to-access-ams-api.md) cikk.
- Telepítse a [Postman](https://www.getpostman.com/) hajtsa végre a jelen cikkben ismertetett REST API-k REST-ügyfél. 

    Ebben az oktatóanyagban uring tudjuk **Postman** , de bármilyen REST eszköz alkalmas lenne. Egyéb alternatívák: **Visual Studio Code** REST beépülő modullal vagy **Telerik Fiddler**. 

## <a name="get-the-authentication-information-from-the-azure-portal"></a>A hitelesítési adatok lekérése az Azure Portalról

### <a name="overview"></a>Áttekintés

Media Services API eléréséhez kell gyűjtenie a következő adatpontokhoz.

|Beállítás|Példa|Leírás|
|---|-------|-----|
|Azure Active Directory-bérlői tartomány|microsoft.onmicrosoft.com|Az Azure AD-végpontként Secure Token Service (STS) jön létre a következő formátumban: https://login.microsoftonline.com/{your-aad-tenant-name.onmicrosoft.com}/oauth2/token. Az Azure AD kibocsát egy JWT (hozzáférési jogkivonat) erőforrások eléréséhez.|
|REST API-végpont|https://amshelloworld.restv2.westus.media.azure.net/api/|Ez az a végpont elleni melyik összes Media Services REST API hívások az alkalmazásban végrehajtott.|
|Ügyfél-azonosító (Alkalmazásazonosító)|f7fbbb29-a02d-4d91-bbc6-59a2579259d2|Az Azure AD-alkalmazás (ügyfél) azonosítója. A hozzáférési jogkivonat beszerzése az ügyfél-azonosító szükséges. |
|Titkos ügyfélkulcs|+mUERiNzVMoJGggD6aV1etzFGa1n6KeSlLjIq+Dbim0=|Az Azure AD alkalmazás kulcsainak (ügyfél titkos kódot). A hozzáférési jogkivonat lekérése szükséges az ügyfél titkos kulcsát.|

### <a name="get-aad-auth-info-from-the-azure-portal"></a>AAD-hitelesítési információk lekérése az Azure Portalról

Az információk lekéréséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com).
2. Keresse meg az AMS-példányhoz.
3. Válassza ki **API-hozzáférés**.
4. Kattintson a **csatlakozhat az Azure Media Services API-szolgáltatásnév**.

    ![API-hozzáférés](./media/connect-with-rest/connect-with-rest01.png)

5. Válasszon egy meglévő **Azure AD-alkalmazás** , vagy hozzon létre egy újat (lásd alább).

    > [!NOTE]
    > Az Azure Media REST kérelem sikeres, a hívó felhasználónak rendelkeznie kell egy **közreműködői** vagy **tulajdonosa** szerepkör a Media Services-fiók elérésére tett kísérlet. Ha a kivételt, amely szerint "a távoli kiszolgáló hibát adott vissza: (401) nem engedélyezett," Lásd [hozzáférés-vezérlés](media-services-use-aad-auth-to-access-ams-api.md#access-control).

    Ha szeretne létrehozni egy új AD-alkalmazást, kövesse az alábbi lépéseket:
    
    1. Nyomja meg **létrehozása új**.
    2. Adjon meg egy nevet.
    3. Nyomja meg **hozzon létre új** újra.
    4. Kattintson a **Mentés** gombra.

    ![API-hozzáférés](./media/connect-with-rest/new-app.png)

    Az új alkalmazás megjelenik az oldalon.

6. Első a **ügyfél-azonosító** (Alkalmazásazonosító).
    
    1. Válassza ki az alkalmazást.
    2. Első a **ügyfél-azonosító** a jobb oldali ablakban. 

    ![API-hozzáférés](./media/connect-with-rest/existing-client-id.png)

7.  Az alkalmazás **kulcs** (ügyfél titkos kódot). 

    1. Kattintson a **alkalmazás kezeléséhez** gomb (figyelje meg, hogy az ügyfél-azonosító adatai területen **Alkalmazásazonosító**). 
    2. Nyomja meg **kulcsok**.
    
        ![API-hozzáférés](./media/connect-with-rest/manage-app.png)
    3. Hozza létre a az alkalmazás kulcsot (ügyfél titkos kódot) kitöltésével **leírás** és **LEJÁRAT** billentyű megnyomásával **mentése**.
    
        Miután a **mentése** bekapcsolva, megjelenik a kulcs értékét. Másolja a kulcs értékét a panel elhagyása előtt.

    ![API-hozzáférés](./media/connect-with-rest/connect-with-rest03.png)

Hozzáadhat AD kapcsolódási paraméterek értékei a web.config vagy az app.config fájlban, hogy a kód későbbi felhasználásra.

> [!IMPORTANT]
> A **ügyfélkulcsot** van egy fontos titkos kulcsot, és legyen megfelelően a key vault biztonságos vagy titkosított éles környezetben.

## <a name="get-the-access-token-using-postman"></a>A Postman segítségével hozzáférési jogkivonat beszerzése

Ez a szakasz bemutatja, hogyan **Postman** egy REST API-t, amely visszaadja a JWT tulajdonosi jogkivonat (hozzáférési tokenre) végrehajtásához. Minden olyan Media Services REST API hívásához, kell az "Engedélyezés" fejléc hozzáadása a hívásokat, és adja hozzá az értékét "tulajdonosi *your_access_token*" az egyes műveletmeghívásokhoz (ahogyan az ebben az oktatóanyagban a következő szakaszban látható). 

1. Nyissa meg **Postman**.
2. Válassza a **POST** lehetőséget.
3. Adja meg az URL-cím, amely tartalmazza a bérlő neve a következő formátumban: a bérlő nevét kell végződnie **. onmicrosoft.com** és az URL-címet kell végződnie **oauth2/token**: 

    https://login.microsoftonline.com/{your-aad-tenant-name.onmicrosoft.com}/oauth2/token

4. Válassza ki a **fejlécek** fülre.
5. Adja meg a **fejlécek** a "Kulcs/érték" adatrács használatával. 

    ![Adatrácsa](./media/connect-with-rest/headers-data-grid.png)

    Másik lehetőségként kattintson **tömeges szerkesztése** a Postman-ablak jobb oldalán lévő hivatkozásra, és illessze be az alábbi kódot.

        Content-Type:application/x-www-form-urlencoded
        Keep-Alive:true

6. Nyomja le az **törzs** fülre.
7. Adja meg a szervezet a "Kulcs/érték" adatrács (cserélje le az ügyfél-azonosító és titkos értékkel) használatával. 

    ![Adatrácsa](./media/connect-with-rest/data-grid.png)

    Másik lehetőségként kattintson **tömeges szerkesztése** jobb oldalán a Postman-ablakot, és illessze be a következő törzsében (cserélje le az ügyfél-azonosító és titkos értékkel):

        grant_type:client_credentials
        client_id:{Your Client ID that you got from your AAD Application}
        client_secret:{Your client secret that you got from your AAD Application's Keys}
        resource:https://rest.media.azure.net

8. Kattintson a **Küldés** gombra.

    ![Szerezze be a tokent](./media/connect-with-rest/connect-with-rest04.png)

A visszaadott válasz tartalmazza a **hozzáférési jogkivonat** , hogy szeretné-e használni minden olyan AMS API-k elérésére.

## <a name="test-the-assets-api-using-the-access-token"></a>Teszt a **eszközök** API hozzáférési token használatával

Ez a szakasz bemutatja, hogyan lehet hozzáférni a **eszközök** API használatával **Postman**.

1. Nyissa meg **Postman**.
2. Válassza a **GET** lehetőséget.
3. Illessze be (például a REST API-végpont https://amshelloworld.restv2.westus.media.azure.net/api/Assets)
4. Válassza ki a **engedélyezési** fülre. 
5. Válassza ki **tulajdonosi jogkivonat**.
6. Illessze be a jogkivonatot, az előző szakaszban létrehozott.

    ![Szerezze be a tokent](./media/connect-with-rest/connect-with-rest05.png)

    > [!NOTE]
    > A Postman UX különböző PC és Mac között lehet. Ha a Mac-verzió nem rendelkezik a "Tulajdonosi jogkivonat" lehetőséget a **hitelesítési** szakasz legördülő menüben adja hozzá a **engedélyezési** manuálisan a Mac-ügyfél a fejléc.

   ![Hitelesítési fejléc](./media/connect-with-rest/auth-header.png)

7. Válassza ki **fejlécek**.
5. Kattintson a **tömeges szerkesztése** a jobb oldali hivatkozásra a Postman-ablakot.
6. Illessze be a következő fejléceket:

        x-ms-version:2.15
        Accept:application/json
        Content-Type:application/json
        DataServiceVersion:3.0
        MaxDataServiceVersion:3.0

7. Kattintson a **Küldés** gombra.

A visszaadott válasz tartalmazza az eszközöket, amelyek a fiókjában.

## <a name="next-steps"></a>További lépések

* A mintakód a próbálja [az Azure Media Services használatához Azure AD-hitelesítés: mindkét REST API-n keresztül](https://github.com/willzhan/WAMSRESTSoln)
* [.NET-tel fájlok feltöltése](media-services-dotnet-upload-files.md)
