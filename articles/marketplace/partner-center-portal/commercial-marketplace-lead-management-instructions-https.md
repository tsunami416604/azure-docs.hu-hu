---
title: HTTPS végpont | Azure Piactér
description: Https-végpont okainak konfigurálása.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 33359883df86091120295b93618a13476e428d2f
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262613"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>Érdeklődőkezelés konfigurálása HTTPS-végpont használatával

>[!Note]
>Az útmutatóban használt Power Automate csatlakozóhoz fizetős előfizetésszükséges a Power Automate szolgáltatáshoz. Kérjük, vegye figyelembe ezt, mielőtt követné a jelen dokumentumban található utasításokat.

Ha az ügyfélkapcsolat-kezelési (CRM) rendszer nem kifejezetten támogatja a Partner Center az Azure Marketplace-en és AppSource-érdeklődők fogadására, használhatja a Power Automate HTTPS-végpontját az érdeklődők kezeléséhez. HTTPS-végpont esetén ezek az érdeklődők elküldhetők e-mail értesítésként, vagy a Power Automate által támogatott ÜGYFÉLkapcsolat-kezelési (CRM) rendszerbe is. Ebben a cikkben található utasítások végigvezetik az alapvető folyamaton, hogy új folyamatot hozzon létre a Power Automate használatával, amely létrehozza a HTTP POST URL-címét, amelyet az Érdeklődőkezelés > **A HTTPS-végpont URL-mezőjében** fog megadni. Is, az utasításokat, hogyan lehet tesztelni a flow segítségével egy eszköz nevű [Postman,](https://www.getpostman.com/downloads/) amely megtalálható az interneten.

## <a name="create-a-flow-using-power-automate"></a>Folyamat létrehozása a Power Automate segítségével

1. Nyissa meg a [Flow](https://flow.microsoft.com/) weblapot. Válassza **a Bejelentkezés**lehetőséget, vagy ha még nem rendelkezik fiókkal, válassza a Szabad **regisztráció** lehetőséget szabad folyamatfiók létrehozásához.

2. Jelentkezzen be, és válassza a **Saját folyamatok** lehetőséget a menüsorban.

3. Válassza a **+Automated - elemet az üres ből.**

    ![Saját folyamatok + Automatizált - üres](./media/commercial-marketplace-lead-management-instructions-https/my-flows-automated.png)

4. Az *Automatikus folyamat létrehozása* ablakban válassza a **Kihagyás**lehetőséget. 

    ![Automatikus folyamat kiépítése - Ugrás](./media/commercial-marketplace-lead-management-instructions-https/build-automated-flow.png)

5. Az **összekötők és eseményindítók keresése** mezőbe írja be a "request" szót a Kérelem összekötő megkereséséhez.
6. Az *Eseményindítók*csoportban jelölje be **a HTTP-kérelem fogadásának**kori jelölését. 

    ![kérelem összekötő - Eseményindítók](./media/commercial-marketplace-lead-management-instructions-https/request-connector.png)

7. A *HTTP-kérelem fogadásakor* ablakmásolja át és illessze be az alábbi JSON-sémát a **Kérelem törzse JSON-séma** szövegmezőbe. Ezt a sémát a Microsoft az érdeklődőadatok tárolására használja.

    ![kérelem összekötő - Eseményindítók](./media/commercial-marketplace-lead-management-instructions-https/https-request-received.png)

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
>A konfiguráció ezen pontján kiválaszthatja, hogy crm-rendszerhez csatlakozzon-e, vagy e-mail értesítést állítson be. Kövesse a többi utasítást a választása alapján.

### <a name="to-connect-to-a-crm-system"></a>Csatlakozás CRM-rendszerhez

1. Válassza az **+ Új lépés** gombot.
2. Válassza ki a választott CRM-rendszert úgy, hogy megkeresi azt, ahol a *Keresés összekötők és műveletek*című rész reked, és jelölje ki a *Műveletek* szakaszban, és a művelettel új rekordot hozzon létre. A következő képernyőfelvételen a **Dynamics 365 – Új** rekord létrehozása példaként látható.

    ![Új rekord létrehozása](./media/commercial-marketplace-lead-management-instructions-https/create-new-record.png)

3. Adja meg a CRM-rendszerhez társított **szervezetnevet.** Válassza az **Érdeklődők** elemet az **Entitásnév** legördülő listából.

    ![Érdeklődők kijelölése](./media/commercial-marketplace-lead-management-instructions-https/select-leads.png)

4. A Folyamat az érdeklődőadatok megadására szolgáló űrlapot jelenít meg. A beviteli kérelem elemeit dinamikus tartalom hozzáadásával képezheti le. A következő képernyőfelvételen példaként az **OfferTitle** látható.

    ![Dinamikus tartalom hozzáadása](./media/commercial-marketplace-lead-management-instructions-https/add-dynamic-content.png)

5. Képezze le a kívánt mezőket, majd válassza a **Mentés** gombot a folyamat mentéséhez. Létrejön egy HTTP-BEJEGYZÉS URL-cím, amely a *HTTP-kérelem fogadásakor* ablakban érhető el. Másolja ezt az URL-t a HTTP POST URL-címtől jobbra található másolásvezérlő vel - ez fontos, hogy ne hagyjon ki tévesen a teljes URL-nek egyetlen részét sem. Mentse ezt az URL-címet, ahogy szüksége lesz rá, amikor konfigurálja az érdeklődőkezelést a közzétételi portálon.

    ![Http-kérelem érkezésekor.](./media/commercial-marketplace-lead-management-instructions-https/when-http-request-received.png)

### <a name="to-set-up-email-notification"></a>A levelezési értesítés beállítása

1. Most, hogy befejezte a JSON-sémát, válassza a **+ Új lépés lehetőséget.**
2. A **Művelet kiválasztása**csoportban válassza a **Műveletek**lehetőséget.
3. A **Műveletek csoportban**válassza **az E-mail küldése (Office 365 Outlook)** lehetőséget.

    >[!Note]
    >Ha egy másik e-mail szolgáltatót szeretne használni, keressen rá, és helyette az *E-mail értesítés küldése (E-mail)* lehetőséget válassza.

    ![E-mail művelet hozzáadása](./media/commercial-marketplace-lead-management-instructions-https/https-request-received-send-email.png)

4. Az **E-mail küldése ablakban** konfigurálja a következő szükséges mezőket:

   - **Címzett** – Adjon meg legalább egy érvényes e-mail címet, ahová az érdeklődőket küldi a rendszer.
   - **Tárgy** - A Flow lehetőséget ad dinamikus tartalom hozzáadására, például a **LeadSource-hoz** a következő képernyőfelvételen. Először írja be a mező nevét, majd kattintson a dinamikus tartalom kivételezési listájára az előugró ablakban. 

        >[!Note] 
        > Mezőnevek hozzáadásakor a következő szöveggel követheti a ":" karaktert, majd az Enter billentyűt beírással új sor létrehozásához. Miután hozzáadta a mezőneveket, hozzáadhatja az egyes társított paramétereket a dinamikus kivételezési listából.

        ![E-mail művelet hozzáadása dinamikus tartalommal](./media/commercial-marketplace-lead-management-instructions-https/add-email-using-dynamic-content.png)

   - **Törzs** – A Dinamikus tartalom kivételezési listából adja hozzá a kívánt információkat az e-mail törzsébe. Például vezetéknév, keresztnév, e-mail és vállalat. <br> <br> Amikor befejezte az e-mail értesítés beállítását, a következő képernyőfelvételen a példaként fog kinézni.


       ![E-mail művelet hozzáadása](./media/commercial-marketplace-lead-management-instructions-https/send-an-email.png)

5. A folyamat befejezéséhez válassza a **Mentés** lehetőséget. Létrejön egy HTTP-BEJEGYZÉS URL-cím, amely a *HTTP-kérelem fogadásakor* ablakban érhető el. Másolja ezt az URL-t a HTTP POST URL-címtől jobbra található másolásvezérlő vel - ez fontos, hogy ne hagyjon ki tévesen a teljes URL-nek egyetlen részét sem. Mentse ezt az URL-címet, ahogy szüksége lesz rá, amikor konfigurálja az érdeklődőkezelést a közzétételi portálon.

   ![HTTP BEJEGYZÉS URL-CÍME ](./media/commercial-marketplace-lead-management-instructions-https/http-post-url.png)

### <a name="testing"></a>Tesztelés

A [Postman](https://app.getpostman.com/app/download/win64)nevű eszközzel tesztelheti, hogy minden a várt módon működik a következő lépésekkel, amely online letölthető. Ez a Windows rendszerhez érhető el. 

1. Indítsa el a Postman t, és válassza az **Új** > **kérelem** lehetőséget a teszteszköz beállításához. 

   ![A teszteszköz beállításának kérése](./media/commercial-marketplace-lead-management-instructions-https/postman-request.png)

2. Töltse ki a *Mentési kérelem* űrlapot, majd **mentse** a létrehozott mappába.

   ![Kérés mentése](./media/commercial-marketplace-lead-management-instructions-https/postman-save-to-test.png)

3. Válassza a **POST** elemet a legördülő listából. 

   ![A folyamat tesztelése](./media/commercial-marketplace-lead-management-instructions-https/test-my-flow.png)

4. Illessze be a HTTP POST URL-címét a Power Automate rendszerben létrehozott folyamatból, ahol az *Enter kérés URL-címe*jelenik meg.

   ![A HTTP POST URL-CÍM beillesztése](./media/commercial-marketplace-lead-management-instructions-https/paste-http-post-url.png)

5. Lépjen vissza a [Folyamathoz,](https://flow.microsoft.com/) és keresse meg az érdeklődők küldéséhez létrehozott folyamatot, ha a Folyamat menüsorból a **Saját folyamatok** menüsorból lép vissza.  Jelölje ki a folyamat neve melletti 3 pontos t, és válassza a **Szerkesztés**lehetőséget.

   ![Saját folyamatok - Szerkesztés](./media/commercial-marketplace-lead-management-instructions-https/my-flows-edit.png)

6. Válassza a **Teszt** lehetőséget a jobb felső sarokban, válassza az "Végrehajtom az eseményindító műveletet" lehetőséget, majd a **Teszt**lehetőséget. A képernyő tetején megjelenik egy jelzés, amely azt jelzi, hogy a teszt megkezdődött

   ![Vizsgálati folyamat - trigger](./media/commercial-marketplace-lead-management-instructions-https/test-flow-trigger-action.png)

7. Lépjen vissza a Postman alkalmazásba, és válassza a **Küldés** lehetőséget a HTTPS-URL beillesztési helye mellett.

   ![Folyamat tesztelése - Küldés](./media/commercial-marketplace-lead-management-instructions-https/postman-send.png)

8. Menjen vissza a folyamathoz, és ellenőrizze az eredményt. Ha minden a várt módon működik, megjelenik egy üzenet, amely jelzi, hogy sikeres volt.

   ![Flow - Eredmények ellenőrzése](./media/commercial-marketplace-lead-management-instructions-https/my-flow-check-results.png)

9. Önnek is kapott egy e-mailt. Ellenőrizze az e-mail postafiókját. 

    >[!Note] 
    >Ha nem lát egy e-mailt a tesztből, akkor ellenőrizze a levélszemét és a levélszemét mappáit. Az alábbiakban csak az e-mail értesítés konfigurálásakor hozzáadott mezőcímkéket fogja észrevenni. Ha ez az ajánlatból származó tényleges érdeklődő lenne, akkor az érdeklődő kapcsolattartójából származó tényleges információkat is láthatná a törzsben és a Tárgy sorban.

   ![E-mail érkezett](./media/commercial-marketplace-lead-management-instructions-https/email-received.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Az ajánlat konfigurálása a HTTPS-végpontra való érdeklődők küldésére

Ha készen áll az ajánlat érdeklődőkezelési adatainak konfigurálására a közzétételi portálon, kövesse az alábbi lépéseket:

1. Nyissa meg az **ajánlat beállítási** oldalát.
2. Válassza a **Csatlakozás** lehetőséget az Érdeklődőkezelés szakaszban.
3. A Kapcsolat részletei előugró ablakban válassza a **HTTPS-végpont** lehetőséget az **érdeklődő célpontjához,** és illessze be a HTTP POST URL-címbe a létrehozott folyamatból a **HTTPS-végpont URL-mezőjébe** lépésekkel létrehozott folyamatból.
4. **Kapcsolatfelvételi e-mail** – E-maileket adhat meg a vállalatnál lévő személyeknek, akiknek e-mailes értesítéseket kell kapniuk, ha új érdeklődő érkezik. Több e-mailt is megadhat, ha pontosvesszővel választja el őket.
5. Válassza **az OK gombot.**

Ha meg szeretne győződni arról, hogy sikeresen csatlakozott egy érdeklődő célállomásához, kattintson az érvényesítés gombra. Ha sikeres, akkor lesz egy teszt vezető az érdeklődő cél.

>[!Note] 
>Be kell fejeznie az ajánlat többi részének konfigurálását, és közzé kell tennie, mielőtt érdeklődőket kapna az ajánlathoz.

Érdeklődők létrehozásakor a Microsoft érdeklődőket küld a Folyamatnak, amely a beállított CRM-rendszerhez vagy e-mail-címhez irányítja azokat.

![Érdeklődőkezelés - csatlakozás](./media/commercial-marketplace-lead-management-instructions-https/lead-management-connect.png)

![Kapcsolat adatai](./media/commercial-marketplace-lead-management-instructions-https/connection-details.png)

![Kapcsolat adatai](./media/commercial-marketplace-lead-management-instructions-https/https-connection-details.png)

