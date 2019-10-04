---
title: HTTPS-végpont | Azure piactér
description: Az érdeklődők felügyeletének beállítása egy HTTPS-végponthoz.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: evansma
ms.openlocfilehash: 6a34bdcab5a13af682515bbae96e9a1800ccc37f
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69902194"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>Az érdeklődők felügyeletének konfigurálása HTTPS-végpont használatával

Ha az Ügyfélkapcsolat-kezelési (CRM) rendszer nincs kifejezetten támogatva a partner Centerben az Azure Marketplace és a AppSource-érdeklődők fogadásához, akkor az MS flow-ban HTTPS-végpontot használhat ezen érdeklődők kezelésére. A HTTPS-végpontok esetében ezek az érdeklődők e-mail-értesítésként küldhetők el, vagy írhatók az MS flow által támogatott ügyfélkapcsolat-kezelési (CRM) rendszerbe. Az ebben a cikkben található utasítások végigvezetik az új folyamat létrehozásán Microsoft Flow használatával, amely a HTTP POST URL-címet fogja létrehozni, amelyet a közzétételi portálon fog megadni az ólom-felügyeleti > **https-végpont URL-címe** mezőben. Emellett a befoglalt utasítások arról, hogyan tesztelheti a folyamatot egy Poster nevű eszköz használatával [](https://www.getpostman.com/downloads/) , amely online állapotú.

## <a name="create-a-flow-using-microsoft-flow"></a>Folyamat létrehozása Microsoft Flow használatával

1. Nyissa meg a [folyamat](https://flow.microsoft.com/) weboldalát. Válassza a **Bejelentkezés**lehetőséget, vagy ha még nem rendelkezik fiókkal, válassza a **regisztráció ingyenes** lehetőséget egy ingyenes flow-fiók létrehozásához.

2. Jelentkezzen be, majd a menüsávon válassza a **saját folyamatok** lehetőséget.

3. Válassza **az + automatizált – üres**lehetőséget.

    ![Saját folyamatok + automatizált – üresről](./media/commercial-marketplace-lead-management-instructions-https/my-flows-automated.png)

4. Az *automatikus folyamat létrehozása* ablakban válassza a **kihagyás**lehetőséget. 

    ![Automatikus folyamat létrehozása – kihagyás](./media/commercial-marketplace-lead-management-instructions-https/build-automated-flow.png)

5. Az **Összekötők és eseményindítók keresése** mezőben írja be a "kérelem" kifejezést a kérelem-összekötő megkereséséhez.
6. Az *Eseményindítók*területen válassza ki **a HTTP-kérelem fogadásának**időpontját. 

    ![kérelem-összekötő – eseményindítók](./media/commercial-marketplace-lead-management-instructions-https/request-connector.png)

7. A *http-kérés fogadásakor* ablakban másolja az alábbi JSON-sémát a **kérelem törzse JSON-séma** szövegmezőbe. Ezt a sémát a Microsoft használja az érdeklődői adatai tárolására.

    ![kérelem-összekötő – eseményindítók](./media/commercial-marketplace-lead-management-instructions-https/https-request-received.png)

    **JSON-séma**

    ```JSON
    {
      "$schema": "https://json-schema.org/draft-04/schema#",
      "definitions": {},
      "id": "http://example.com/example.json",
      "properties": {
        "ActionCode": {
          "id": "/properties/ActionCode",
          "type": "string"
        },
        "OfferTitle": {
          "id": "/properties/OfferTitle",
          "type": "string"
        },
        "LeadSource": {
          "id": "/properties/LeadSource",
          "type": "string"
        },
        "UserDetails": {
          "id": "/properties/UserDetails",
          "properties": {
            "Company": {
              "id": "/properties/UserDetails/properties/Company",
              "type": "string"
            },
            "Country": {
              "id": "/properties/UserDetails/properties/Country",
              "type": "string"
            },
            "Email": {
              "id": "/properties/UserDetails/properties/Email",
              "type": "string"
            },
            "FirstName": {
              "id": "/properties/UserDetails/properties/FirstName",
              "type": "string"
            },
            "LastName": {
              "id": "/properties/UserDetails/properties/LastName",
              "type": "string"
            },
            "Phone": {
              "id": "/properties/UserDetails/properties/Phone",
              "type": "string"
            },
            "Title": {
              "id": "/properties/UserDetails/properties/Title",
              "type": "string"
            }
          },
          "type": "object"
        }
      },
      "type": "object"
    }
    ```

>[!Note]
>A konfiguráció ezen pontján kiválaszthatja, hogy csatlakozik-e egy CRM rendszerhez, vagy konfiguráljon egy e-mailes értesítést. Kövesse az Ön által választott további utasításokat.

### <a name="to-connect-to-a-crm-system"></a>Kapcsolódás CRM rendszerhez

1. Válassza az **+ új lépés**lehetőséget.
2. Válassza ki az Ön által választott CRM-rendszert úgy, hogy megkeresi az *összekötőket és a műveleteket*, majd a *műveletek* szakaszban válassza ki az új rekord létrehozásához szükséges műveletet. Az alábbi képernyőfelvételen a **Dynamics 365 –** új rekord létrehozása példaként.

    ![Új rekord létrehozása](./media/commercial-marketplace-lead-management-instructions-https/create-new-record.png)

3. Adja meg a CRM rendszerhez társított **szervezet nevét** . Az **entitás neve** legördülő listából válassza az érdeklődők lehetőséget.

    ![Érdeklődők kiválasztása](./media/commercial-marketplace-lead-management-instructions-https/select-leads.png)

4. A flow egy űrlapot jelenít meg az érdeklődői adatok biztosításához. A bemeneti kérelem elemeit leképezheti a dinamikus tartalom hozzáadására szolgáló lehetőség választásával. Az alábbi képernyőfelvételen a **OfferTitle** látható példa.

    ![Dinamikus tartalom hozzáadása](./media/commercial-marketplace-lead-management-instructions-https/add-dynamic-content.png)

5. Képezze le a kívánt mezőket, majd válassza a **Mentés** lehetőséget a folyamat mentéséhez. A rendszer létrehoz egy HTTP POST URL-címet, és elérhetővé válik a *http-kérelem fogadása* ablakban. Másolja ezt az URL-t a HTTP POST URL-cím jobb oldalán található másolási vezérlő használatával. Ez azért fontos, hogy ne hagyjon el véletlenül a teljes URL-cím egy részét. Mentse ezt az URL-címet, mert szüksége lesz rá, amikor a közzétételi portálon konfigurálja az érdeklődők felügyeletét.

    ![HTTP-kérelem fogadásakor.](./media/commercial-marketplace-lead-management-instructions-https/when-http-request-received.png)

### <a name="to-set-up-email-notification"></a>E-mail értesítések beállítása

1. Most, hogy végrehajtotta a JSON-sémát, válassza az **+ új lépés**lehetőséget.
2. A **válasszon műveletet**területen válassza a **műveletek**lehetőséget.
3. A **műveletek**területen válassza **az E-mail küldése (Office 365 Outlook)** lehetőséget.

    >[!Note]
    >Ha más e-mail-szolgáltatót szeretne használni, válassza az *e-mail értesítés küldése (e-mail)* lehetőséget a művelet helyett.

    ![E-mail művelet hozzáadása](./media/commercial-marketplace-lead-management-instructions-https/https-request-received-send-email.png)

4. Az **E-mail küldése** ablakban adja meg a következő kötelező mezőket:

   - Adjon meg legalább egy érvényes e-mail-címet, ahol a rendszer elküldi az érdeklődőket.
   - A tulajdonosi folyamat lehetővé teszi, hogy dinamikus tartalmat (például **LeadSource** ) adjon hozzá a következő képernyőfelvételen. Először írja be a mező nevét, majd a felugró ablakban kattintson a dinamikus tartalom kiválasztása listára. 

        >[!Note] 
        > A mezőnevek hozzáadásakor követheti a ":" értéket, majd megadhatja az új sor létrehozásához. Miután hozzáadta a mezőneveket, hozzáadhatja az egyes társított paramétereket a dinamikus kivételezések listájából.

        ![E-mail művelet hozzáadása dinamikus tartalom használatával](./media/commercial-marketplace-lead-management-instructions-https/add-email-using-dynamic-content.png)

   - **Törzs** – a dinamikus tartalom kiválasztása listából adja hozzá a kívánt adatokat az e-mail törzsébe. Például: LastName, FirstName, email és Company. <br> <br> Ha végzett az e-mail-értesítések beállításával, az a következő képernyőfelvételen látható példához hasonlóan fog kinézni.


       ![E-mail művelet hozzáadása](./media/commercial-marketplace-lead-management-instructions-https/send-an-email.png)

5. A folyamat befejezéséhez kattintson a **Mentés** gombra. A rendszer létrehoz egy HTTP POST URL-címet, és elérhetővé válik a *http-kérelem fogadása* ablakban. Másolja ezt az URL-t a HTTP POST URL-cím jobb oldalán található másolási vezérlő használatával. Ez azért fontos, hogy ne hagyjon el véletlenül a teljes URL-cím egy részét. Mentse ezt az URL-címet, mert szüksége lesz rá, amikor a közzétételi portálon konfigurálja az érdeklődők felügyeletét.

   ![HTTP POST URL-címe ](./media/commercial-marketplace-lead-management-instructions-https/http-post-url.png)

### <a name="testing"></a>Tesztelés

A következő lépések végrehajtásával ellenőrizheti, hogy minden a várt módon működik-e a [Poster](https://app.getpostman.com/app/download/win64)nevű eszköz használatával, amely online letölthető. Ez a Windowshoz érhető el. 

1. Indítsa el a Poster alkalmazást, és válassza az **új** > **kérelem** lehetőséget a tesztelési eszköz beállításához. 

   ![A tesztelési eszköz beállításához szükséges kérelem](./media/commercial-marketplace-lead-management-instructions-https/postman-request.png)

2. Töltse ki a *mentési kérelem* űrlapot, majd **mentse** a létrehozott mappába.

   ![Kérelem mentése](./media/commercial-marketplace-lead-management-instructions-https/postman-save-to-test.png)

3. Válassza a **Közzététel** lehetőséget a legördülő listából. 

   ![A folyamat tesztelése](./media/commercial-marketplace-lead-management-instructions-https/test-my-flow.png)

4. Illessze be a HTTP-POST URL-címét az MS flow-ban létrehozott folyamatból, ahol az *adja meg a kérelem URL-címét*.

   ![A HTTP POST URL-cím beillesztése](./media/commercial-marketplace-lead-management-instructions-https/paste-http-post-url.png)

5. Lépjen vissza a [flow](https://flow.microsoft.com/) -ba, és keresse meg az érdeklődők küldéséhez létrehozott folyamatot a folyamat menüsávján.  Válassza a folyamat neve melletti 3 pontot, és válassza a **Szerkesztés**lehetőséget.

   ![Saját folyamatok – szerkesztés](./media/commercial-marketplace-lead-management-instructions-https/my-flows-edit.png)

6. Válassza a **tesztelés** lehetőséget a jobb felső sarokban, majd válassza az "indítom el a trigger" műveletet, majd válassza a **teszt**lehetőséget. Megjelenik egy jelzés a képernyő tetején, amely azt jelzi, hogy a teszt elindult

   ![Folyamat tesztelése – trigger](./media/commercial-marketplace-lead-management-instructions-https/test-flow-trigger-action.png)

7. Lépjen vissza a Poster-alkalmazáshoz, és válassza a **Küldés** lehetőséget a HTTPS URL-cím beillesztette elem mellett.

   ![Saját folyamat tesztelése – küldés](./media/commercial-marketplace-lead-management-instructions-https/postman-send.png)

8. Térjen vissza a folyamathoz, és tekintse át az eredményt. Ha minden a várt módon működik, megjelenik egy üzenet, amely jelzi, hogy a művelet sikeres volt.

   ![Folyamat – ellenőrzési eredmények](./media/commercial-marketplace-lead-management-instructions-https/my-flow-check-results.png)

9. E-mailt is kell kapnia. Keresse meg az e-mailek mappáját. 

    >[!Note] 
    >Ha nem lát e-mailt a tesztből, ellenőrizze a levélszemét és a levélszemét mappáját. Alább láthatja, hogy csak az e-mail-értesítések konfigurálásakor hozzáadott mezők jelennek meg. Ha ez az ajánlat által generált tényleges érdeklődő volt, akkor a törzsben és a Tárgy sorban lévő érdeklődői kapcsolattartótól származó tényleges adatokat is látni fogja.

   ![E-mail érkezett](./media/commercial-marketplace-lead-management-instructions-https/email-received.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Az ajánlat beállítása a HTTPS-végpontnak küldött érdeklődők küldésére

Ha készen áll az ajánlathoz tartozó érdeklődői felügyeleti információk konfigurálására a közzétételi portálon, kövesse az alábbi lépéseket:

1. Navigáljon az ajánlat **telepítési** lapjára.
2. Válassza a **kapcsolat** lehetőséget az érdeklődő felügyelete szakaszban.
3. A kapcsolat részletei előugró ablakban válassza a https- **végpont** lehetőséget a **vezető célhelyhez** , és ILLESSZE be a http post URL-címet a létrehozott folyamatból a **https-végpont URL-címe** mezőbe a korábbi lépések követésével.
4. Kattintson a **Mentés** gombra. 

>[!Note] 
>Be kell fejeznie az ajánlat többi részének konfigurálását, és közzé kell tennie az ajánlathoz tartozó érdeklődők fogadása előtt.

Az érdeklődők létrehozásakor a Microsoft elküldi az érdeklődőket a folyamatnak, amely a beállított CRM rendszerre vagy e-mail-címre irányítja át.

![Vezető felügyelet – kapcsolat](./media/commercial-marketplace-lead-management-instructions-https/lead-management-connect.png)

![Kapcsolat részletei](./media/commercial-marketplace-lead-management-instructions-https/connection-details.png)

![Kapcsolat részletei](./media/commercial-marketplace-lead-management-instructions-https/connection-details-1.png)

