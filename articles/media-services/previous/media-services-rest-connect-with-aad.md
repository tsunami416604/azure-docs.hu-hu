---
title: "Azure Media Services API többi eléréséhez használja az Azure AD hitelesítési |} Microsoft Docs"
description: "Útmutató: Azure Active Directory-hitelesítéssel Azure Media Services API hozzáférhet a többi."
services: media-services
documentationcenter: 
author: willzhan
manager: cfowler
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/26/2017
ms.author: willzhan;juliako;johndeu
ms.openlocfilehash: ed78d6c6d4c695b841dbfbf917cd1681adc44ee7
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2018
---
# <a name="use-azure-ad-authentication-to-access-the-azure-media-services-api-with-rest"></a>Az Azure Media Services API REST eléréséhez használja az Azure AD-alapú hitelesítés

Ha az Azure AD-alapú hitelesítés az Azure Media Services használata esetén az alábbi két módszer egyikével hitelesítheti:

- **Felhasználó hitelesítése** hitelesíti a személy, aki használja az alkalmazás interakciót Azure Media Services-erőforrásokkal. Az interaktív alkalmazás először kell kérni a felhasználót a hitelesítő adatokat. Példa: egy kódolási feladatok figyelése, vagy live streaming jogosult felhasználók által használt felügyeleti konzol alkalmazást. 
- **Szolgáltatás egyszerű hitelesítési** szolgáltatás hitelesíti. Alkalmazásokat, amelyek általában arra használják ezt a hitelesítési módszert démon szolgáltatások, a középső rétegbeli szolgáltatások vagy az ütemezett feladatok, például webalkalmazások, függvény alkalmazások, a logic apps, API-k vagy mikroszolgáltatások futó alkalmazások.

    Ez az oktatóanyag bemutatja, hogyan használhatja az Azure Active Directory **egyszerű** hitelesítési többi AMS API eléréséhez. 

    > [!NOTE]
    > **Szolgáltatás egyszerű** az ajánlott eljárás a legtöbb alkalmazás csatlakoztatása az Azure Media Services van. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A hitelesítő adatok beolvasása az Azure-portálon
> * A hozzáférési jogkivonat segítségével Postman beolvasása
> * Teszt a **eszközök** hozzáférési jogkivonat API


> [!IMPORTANT]
> A Media Services jelenleg az Azure hozzáférés-vezérlés hitelesítési modell. Azonban hozzáférés-vezérlés hitelesítési elavulttá válik 2018. június 1. Javasoljuk, hogy mielőbb térjen át az Azure AD-hitelesítési modellre.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) a virtuális gép létrehozásának megkezdése előtt.
- [Az Azure portál használatával Azure Media Services-fiók létrehozása](media-services-portal-create-account.md).
- Tekintse át a [eléréséhez Azure Media Services API-t az AAD-hitelesítés áttekintése](media-services-use-aad-auth-to-access-ams-api.md) cikk.
- Telepítse a [Postman](https://www.getpostman.com/) többi ügyfél a REST API-k, ez a cikk látható. 

    Ebben az oktatóanyagban uring vagyunk **Postman** , de a többi eszközt lenne megfelelő. Más alternatív megoldások: **Visual Studio Code** , a többi beépülő modul vagy **Telerik Fiddler**. 

## <a name="get-the-authentication-information-from-the-azure-portal"></a>A hitelesítő adatok beolvasása az Azure-portálon

### <a name="overview"></a>Áttekintés

Media Services API eléréséhez kell összegyűjtenie a következő adatpontokhoz.

|Beállítás|Példa|Leírás|
|---|-------|-----|
|Azure Active Directory-bérlői tartomány|microsoft.onmicrosoft.com|Azure AD szolgáltatásba a Secure Token Service (STS) végpont jön létre a következő formátumban: https://login.microsoftonline.com/{your-aad-tenant-name.onmicrosoft.com}/oauth2/token. Az Azure AD kibocsát jwt-t (olyan hozzáférési jogkivonatot) erőforrások eléréséhez.|
|REST API-végpont|https://amshelloworld.restv2.westus.media.azure.net/api/|Ez az a végpont, mely összes Media Services REST API elleni hívások az alkalmazásban végrehajtott.|
|Ügyfél-azonosító (Alkalmazásazonosító)|f7fbbb29-a02d-4d91-bbc6-59a2579259d2|Az Azure AD (ügyfél) azonosítót. A hozzáférési jogkivonat beszerzése az ügyfél-azonosító szükséges. |
|Titkos ügyfélkulcs|+mUERiNzVMoJGggD6aV1etzFGa1n6KeSlLjIq+Dbim0=|Az Azure AD alkalmazás kulcsok (titkos). A titkos ügyfélkulcs szükség van a hozzáférési jogkivonat.|

### <a name="get-aad-auth-info-from-the-azure-portal"></a>Beolvasni az aad-ben hitelesítési adatait az Azure-portálon

Ahhoz, hogy az adatokat, kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure portálra](http://portal.azure.com).
2. Nyissa meg az AMS-példányt.
3. Válassza ki **API-hozzáférés**.
4. Kattintson a **csatlakozás az Azure Media Services API-t a szolgáltatás egyszerű**.

    ![API-hozzáférés](./media/connect-with-rest/connect-with-rest01.png)

5. Válasszon ki egy létező **az Azure AD-alkalmazást** , vagy hozzon létre egy új (lásd alább).

    > [!NOTE]
    > Az Azure Media REST kérelem sikeres, a hívó felhasználónak rendelkeznie kell egy **közreműködő** vagy **tulajdonos** szerepkört a Media Services-fiókhoz elérésére tett kísérlet. Ha egy kivételt, amely szerint "a távoli kiszolgáló hibát adott vissza: (401) nem engedélyezett," Lásd [hozzáférés-vezérlés](media-services-use-aad-auth-to-access-ams-api.md#access-control).

    Ha szeretne hozzon létre egy új AD-alkalmazást, kövesse az alábbi lépéseket:
    
    1. Nyomja le az **új**.
    2. Adjon meg egy nevet.
    3. Nyomja le az **hozzon létre új** újra.
    4. Nyomja le az **mentése**.

    ![API-hozzáférés](./media/connect-with-rest/new-app.png)

    Az új alkalmazás megjelenik a lapon.

6. Beolvasása a **ügyfél-azonosító** (Alkalmazásazonosító).
    
    1. Válassza ki az alkalmazást.
    2. Beolvasása a **ügyfél-azonosító** a jobb oldali ablakból. 

    ![API-hozzáférés](./media/connect-with-rest/existing-client-id.png).

7.  Az alkalmazás **kulcs** (titkos). 

    1. Kattintson a **alkalmazás kezeléséhez** gomb (figyelje meg, hogy az ügyfél-azonosító adatai alatt áll **Alkalmazásazonosító**). 
    2. Nyomja le az **kulcsok**.
    
        ![API-hozzáférés](./media/connect-with-rest/manage-app.png)
    3. Hozzon létre az alkalmazás kulcsot (titkos) kitöltésével **leírása** és **EXPIRES** gomb lenyomásával **mentése**.
    
        Egyszer a **mentése** bekapcsolva, a kulcs értékét jeleníti meg. Másolja a kulcs értéke mielőtt elhagynák a panelt.

    ![API-hozzáférés](./media/connect-with-rest/connect-with-rest03.png)

A web.config vagy az app.config fájlt, a kód későbbi felhasználásra AD kapcsolat paraméter értékét is hozzáadhat.

> [!IMPORTANT]
> A **ügyfélkulcsot** van egy fontos titkos kulcs és legyen megfelelően kulcstároló a védett vagy titkosított éles környezetben.

## <a name="get-the-access-token-using-postman"></a>A hozzáférési jogkivonat segítségével Postman beolvasása

Ez a szakasz bemutatja, hogyan használható **Postman** végrehajtani egy REST API-t, amely visszaadja a JWT tulajdonosi Token (jogkivonat). Media Services REST API-k meghívásához kell az "Engedélyezés" fejléc hozzáadását a hívásokat, majd adja meg az értékét "tulajdonosi *your_access_token*" minden hívásnak (ahogy ez az oktatóanyag a következő szakaszban). 

1. Nyissa meg **Postman**.
2. Válassza a **POST** lehetőséget.
3. Az URL-címét, amely tartalmazza a bérlő neve a következő formátumban: a bérlő nevét kell végződnie **. onmicrosoft.com** és az URL-címet kell végződnie **oauth2/token**: 

    https://login.microsoftonline.com/{your-aad-tenant-name.onmicrosoft.com}/oauth2/token

4. Válassza ki a **fejlécek** fülre.
5. Adja meg a **fejlécek** információit a "Kulcs/érték" adatrácson. 

    ![Adatrács](./media/connect-with-rest/headers-data-grid.png)

    Másik lehetőségként kattintson **tömeges módosítása** a az ablak jobb oldalán Postman hivatkozásra, és illessze be az alábbi kódot.

        Content-Type:application/x-www-form-urlencoded
        Keep-Alive:true

6. Nyomja meg a **törzs** fülre.
7. Adja meg a szervezet a "Kulcs/érték" adatrácson (csere az ügyfél-Azonosítót és titkos értékek) használatával. 

    ![Adatrács](./media/connect-with-rest/data-grid.png)

    Másik lehetőségként kattintson **tömeges módosítása** jobb oldalán a Postman ablakot, és illessze be a következő törzsében (csere az ügyfél-Azonosítót és titkos értékek):

        grant_type:client_credentials
        client_id:{Your Client ID that you got from your AAD Application}
        client_secret:{Your client secret that you got from your AAD Application's Keys}
        resource:https://rest.media.azure.net

8. Kattintson a **Küldés** gombra.

    ![Szerezze be a tokent](./media/connect-with-rest/connect-with-rest04.png)

A visszaadott válaszban a **hozzáférési jogkivonat** , hogy szeretné-e használni minden AMS API-k elérésére.

## <a name="test-the-assets-api-using-the-access-token"></a>Teszt a **eszközök** hozzáférési jogkivonat API

Ez a szakasz bemutatja, hogyan férhet hozzá a **eszközök** API használatával **Postman**.

1. Nyissa meg **Postman**.
2. Válassza a **GET** lehetőséget.
3. Illessze be (például a REST API-végpont https://amshelloworld.restv2.westus.media.azure.net/api/Assets)
4. Válassza ki a **engedélyezési** fülre. 
5. Válassza ki **tulajdonosi jogkivonattal**.
6. Illessze be a jogkivonat az előző szakaszban létrehozott.

    ![Szerezze be a tokent](./media/connect-with-rest/connect-with-rest05.png)

    > [!NOTE]
    > Lehet, hogy a Postman UX különböző, a Mac és a számítógépek között. Ha a Mac-verzió nem rendelkezik a "Tulajdonosi jogkivonat" lehetőséget a **hitelesítési** szakasz legördülő menüben adja hozzá a **engedélyezési** manuálisan a Mac-ügyfél a fejlécet.

   ![Hitelesítési fejléc](./media/connect-with-rest/auth-header.png)

7. Válassza ki **fejlécek**.
5. Kattintson a **tömeges módosítása** a Postman ablak jobb hivatkozásra.
6. Illessze be a következő fejléc:

        x-ms-version:2.15
        Accept:application/json
        Content-Type:application/json
        DataServiceVersion:3.0
        MaxDataServiceVersion:3.0

7. Kattintson a **Küldés** gombra.

A visszaadott válaszban az eszközöket, amelyek a fiókban.

## <a name="next-steps"></a>További lépések

* A mintakód a próbálja [az Azure AD-alapú hitelesítés az Azure Media Services Access: mindkét REST API-n keresztül](https://github.com/willzhan/WAMSRESTSoln)
* [A .NET fájlok feltöltése](media-services-dotnet-upload-files.md)
