---
title: Az Azure Media Services API REST szolgáltatással való eléréséhez használja az Azure AD-hitelesítést | Microsoft dokumentumok
description: Megtudhatja, hogyan érheti el az Azure Media Services API-t az Azure Active Directory-hitelesítéssel a REST használatával.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.reviewer: willzhan; johndeu
ms.openlocfilehash: a693eb374365670da3fe8c4b2bb8ce664a024217
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295443"
---
# <a name="use-azure-ad-authentication-to-access-the-media-services-api-with-rest"></a>A Media Services API és a REST hozzáférése Azure AD-hitelesítéssel

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Nézze meg a legújabb verziót, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Lásd még: [migrálási útmutató a v2-től a v3-ig](../latest/migrate-from-v2-to-v3.md)

Ha Azure AD-hitelesítést használ az Azure Media Services szolgáltatással, kétféleképpen hitelesítheti magát:

- **A felhasználói hitelesítés** hitelesíti azt a személyt, aki az alkalmazást használja az Azure Media Services-erőforrásokkal való interakcióhoz. Az interaktív alkalmazásnak először meg kell kérnie a felhasználónak a hitelesítő adatokat. Egy példa egy felügyeleti konzol alkalmazás, amelyet a jogosult felhasználók a kódolási feladatok vagy az élő közvetítés figyelésére használják. 
- **Az egyszerű szolgáltatás hitelesítése** hitelesíti a szolgáltatást. A hitelesítési módszert gyakran használó alkalmazások olyan alkalmazások, amelyek démonszolgáltatásokat, középső rétegbeli szolgáltatásokat vagy ütemezett feladatokat futtatnak, például webalkalmazásokat, függvényalkalmazásokat, logikai alkalmazásokat, API-kat vagy mikroszolgáltatásokat.

    Ez az oktatóanyag bemutatja, hogyan használhatja az Azure AD **szolgáltatás egyszerű** hitelesítést az AMS API-hoz rest eléréséhez. 

    > [!NOTE]
    > **Az** egyszerű szolgáltatás az ajánlott eljárás az Azure Media Serviceshez csatlakozó legtöbb alkalmazás esetében. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A hitelesítési adatok beszereznie az Azure Portalról
> * A hozzáférési jogkivonat beszereznie postás használatával
> * Az **Assets** API tesztelése a hozzáférési jogkivonat használatával


> [!IMPORTANT]
> A Media Services jelenleg támogatja az Azure Access Control services hitelesítési modelljét. 2018. június 1-jén azonban a hozzáférés-vezérléshitelesítés elavult. Javasoljuk, hogy mielőbb térjen át az Azure AD-hitelesítési modellre.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) mielőtt elkezdené.
- [Hozzon létre egy Azure Media Services-fiókot az Azure Portalon.](media-services-portal-create-account.md)
- Tekintse át az [Azure Media Services API-elérése az Azure AD-hitelesítés áttekintése cikk.](media-services-use-aad-auth-to-access-ams-api.md)
- Telepítse a [Postman](https://www.getpostman.com/) REST-ügyfelet a cikkben látható REST API-k végrehajtásához. 

    Ebben a bemutatóban **postásot** használunk, de bármilyen REST eszköz megfelelő lenne. Egyéb alternatívák: **Visual Studio Kód** a REST plugin vagy **Telerik Fiddler**. 

## <a name="get-the-authentication-information-from-the-azure-portal"></a>A hitelesítési adatok beszereznie az Azure Portalról

### <a name="overview"></a>Áttekintés

A Media Services API eléréséhez a következő adatpontokat kell gyűjtenie.

|Beállítás|Példa|Leírás|
|---|-------|-----|
|Azure Active Directory-bérlői tartomány|microsoft.onmicrosoft.com|Az Azure AD mint Secure Token Service (STS) végpont <https://login.microsoftonline.com/{your-ad-tenant-name.onmicrosoft.com}/oauth2/token>a következő formátumban jön létre: . Az Azure AD kiad egy JWT-t az erőforrások (egy hozzáférési jogkivonat) elérése érdekében.|
|REST API-végpont|<https://amshelloworld.restv2.westus.media.azure.net/api/>|Ez az a végpont, amelyen az alkalmazásösszes Media Services REST API-hívása történik.|
|Ügyfélazonosító (alkalmazásazonosító)|f7fbbb29-a02d-4d91-bbc6-59a2579259d2|Azure AD-alkalmazás (ügyfél) azonosító. Az ügyfélazonosító szükséges a hozzáférési jogkivonat lekéréséhez. |
|Titkos ügyfélkulcs|+mUERiNzVMoJGggD6aV1etzFGa1n6KeSlLjIq+Dbim0=|Azure AD alkalmazáskulcsok (ügyféltitkos kulcs). Az ügyféltitkos kulcs a hozzáférési jogkivonat lekéréséhez szükséges.|

### <a name="get-aad-auth-info-from-the-azure-portal"></a>AAD-hitelesítési adatok bekéselése az Azure Portalról

Az információk beszerezéséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
2. Nyissa meg az AMS-példányt.
3. Válassza az **API-hozzáférés lehetőséget.**
4. Kattintson a **Csatlakozás az Azure Media Services API-hoz egyszerű szolgáltatással**elemre.

    ![API-hozzáférés](./media/connect-with-rest/connect-with-rest01.png)

5. Válasszon ki egy meglévő **Azure AD-alkalmazást,** vagy hozzon létre egy újat (lásd alább).

    > [!NOTE]
    > Az Azure Media REST-kérelem sikerességéhez a hívó felhasználónak rendelkeznie kell egy **közreműködői** vagy **tulajdonosi** szerepkörrel a Media Services-fiókhoz, amelyhez hozzá próbál férni. Ha olyan kivételt kap, amely szerint a távoli kiszolgáló a következő hibaüzenetet adta vissza: (401) Jogosulatlan, olvassa el [a Hozzáférés-vezérlés című témakört.](media-services-use-aad-auth-to-access-ams-api.md#access-control)

    Ha új AD-alkalmazást kell létrehoznia, kövesse az alábbi lépéseket:
    
   1. Nyomja **le az Új létrehozása gombot.**
   2. Írjon be egy nevet.
   3. Nyomja meg ismét **az Új létrehozása** gombot.
   4. Kattintson a **Mentés** gombra.

      ![API-hozzáférés](./media/connect-with-rest/new-app.png)

      Az új alkalmazás megjelenik az oldalon.

6. Az **ügyfélazonosító** (alkalmazásazonosító) beszerezése.
    
   1. Válassza ki az alkalmazást.
   2. Az **ügyfélazonosító** lekérte a jobb oldali ablakból. 

      ![API-hozzáférés](./media/connect-with-rest/existing-client-id.png)

7. Az alkalmazás **kulcsának** beszereznie (ügyféltitkos kulcs). 

   1. Kattintson az **alkalmazás kezelése** gombra (figyelje meg, hogy az ügyfélazonosító adatai az **Alkalmazásazonosító**alatt vannak). 
   2. Nyomja **le a billentyűket**.
    
       ![API-hozzáférés](./media/connect-with-rest/manage-app.png)
   3. Az alkalmazáskulcs (ügyféltitok) létrehozása **a LEÍRÁS** és a LEJÁR kitöltésével, valamint a Mentés billentyű lenyomásával. **EXPIRES** **Save**
    
       A Mentés gomb **megnyomása** után megjelenik a kulcs értéke. A kés elhagyása előtt másolja a kulcs értékét.

   ![API-hozzáférés](./media/connect-with-rest/connect-with-rest03.png)

Az AD-kapcsolat paramétereinek értékeit hozzáadhatja a web.config vagy app.config fájlhoz, amelyet később használni szeretne a kódban.

> [!IMPORTANT]
> Az **ügyfélkulcs** fontos titkos kulcs, és megfelelően kell biztosítani a key vault ban, vagy éles környezetben titkosítva kell lennie.

## <a name="get-the-access-token-using-postman"></a>A hozzáférési jogkivonat beszereznie postás használatával

Ez a szakasz bemutatja, hogyan használhatja **a Postman** egy REST API-t, amely egy JWT-tulajdonosi jogkivonatot (hozzáférési jogkivonatot) ad vissza. Bármely Media Services REST API-jának hívásához hozzá kell adnia az "Engedélyezés" fejlécet a hívásokhoz, és hozzá kell adnia a "Tulajdonos *your_access_token"* értékét minden híváshoz (ahogy az az oktatóanyag következő szakaszában látható). 

1. Nyílt **postás**.
2. Válassza a **POST** lehetőséget.
3. Adja meg a bérlő nevét tartalmazó URL-címet a következő formátumban: a bérlő nevének .onmicrosoft.com végződésűnek kell **lennie,** az URL-címnek **pedig oauth2/token-re:** 

    `https://login.microsoftonline.com/{your-aad-tenant-name.onmicrosoft.com}/oauth2/token`

4. Válassza a **Fejlécek** lapot.
5. Adja meg a **Fejlécek** adatait a "Kulcs/érték" adatrács használatával. 

    ![Adatrács](./media/connect-with-rest/headers-data-grid.png)

    Másik lehetőségként kattintson a Postman ablak jobb oldalán található **Tömeges szerkesztés** hivatkozásra, és illessze be a következő kódot.

        Content-Type:application/x-www-form-urlencoded
        Keep-Alive:true

6. Nyomja le a **Body** fület.
7. Adja meg a törzs adatait a "Kulcs/érték" adatrács használatával (cserélje le az ügyfélazonosítót és a titkos értékeket). 

    ![Adatrács](./media/connect-with-rest/data-grid.png)

    Másik lehetőségként kattintson a Postman ablak jobb oldalán található **Tömeges szerkesztés** elemre, és illessze be a következő törzset (cserélje le az ügyfélazonosítót és a titkos értékeket):

        grant_type:client_credentials
        client_id:{Your Client ID that you got from your Azure AD Application}
        client_secret:{Your client secret that you got from your Azure AD Application's Keys}
        resource:https://rest.media.azure.net

8. Kattintson a **Küldés** gombra.

    ![token bekéselése](./media/connect-with-rest/connect-with-rest04.png)

A visszaadott válasz tartalmazza a **hozzáférési jogkivonatot,** amelyet bármely AMS API-k eléréséhez kell használnia.

## <a name="test-the-assets-api-using-the-access-token"></a>Az **Assets** API tesztelése a hozzáférési jogkivonat használatával

Ez a szakasz bemutatja, hogyan érheti el az **Eszközök** API-t a **Postman**használatával.

1. Nyílt **postás**.
2. Válassza a **GET** lehetőséget.
3. Illessze be a REST API-végpontot (példáulhttps://amshelloworld.restv2.westus.media.azure.net/api/Assets)
4. Válassza az **Engedélyezés** lapot. 
5. Válassza **a Tulajdonostoken lehetőséget.**
6. Illessze be az előző szakaszban létrehozott jogkivonatot.

    ![token bekéselése](./media/connect-with-rest/connect-with-rest05.png)

    > [!NOTE]
    > A Postman UX lehet különböző a Mac és a PC. Ha a Mac-verzió nem rendelkezik a "Tulajdonosi token" beállítással a **Hitelesítés** szakasz legördülő menüben, akkor az **engedélyezési fejlécet** manuálisan kell hozzáadnia a Mac-ügyfélhez.

   ![Hitelesítési fejléc](./media/connect-with-rest/auth-header.png)

7. Válassza **a Fejlécek**lehetőséget.
5. Kattintson a Postman ablak jobb oldalán található **Tömeges szerkesztés** hivatkozásra.
6. Illessze be a következő fejléceket:

        x-ms-version:2.19
        Accept:application/json
        Content-Type:application/json
        DataServiceVersion:3.0
        MaxDataServiceVersion:3.0

7. Kattintson a **Küldés** gombra.

A visszaküldött válasz a fiókjában lévő eszközöket tartalmazza.

## <a name="next-steps"></a>További lépések

* Próbálja ki ezt a mintakódot az [Azure AD-hitelesítés az Azure Media Services Access szolgáltatásban: Mindkettő REST API-n keresztül](https://github.com/willzhan/WAMSRESTSoln)
* [Fájlok feltöltése a .NET fájllal](media-services-dotnet-upload-files.md)
