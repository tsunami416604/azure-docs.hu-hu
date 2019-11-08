---
title: HTTPS-végpont | Azure piactér
description: Az érdeklődők felügyeletének beállítása egy HTTPS-végponthoz.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/24/2018
ms.author: pabutler
ms.openlocfilehash: 817e431f5386b10345d414190e8bda0954ef2aca
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825231"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>Az érdeklődők felügyeletének konfigurálása HTTPS-végpont használatával

Az Azure Marketplace és a AppSource-érdeklődők kezelésére HTTPS-végpontot használhat. Ezek az érdeklődők megírhatók, hogy megírhatók legyenek az Ügyfélkapcsolat-kezelési (CRM) rendszerbe, vagy e-mail-értesítésként legyenek elküldve. Ez a cikk bemutatja, hogyan konfigurálhatja az ólom-kezelést a [Microsoft flow](https://powerapps.microsoft.com/automate-processes/) Automation szolgáltatással.

## <a name="create-a-flow-using-microsoft-flow"></a>Folyamat létrehozása Microsoft Flow használatával

1. Nyissa meg a [folyamat](https://flow.microsoft.com/) weboldalát. Válassza a **Bejelentkezés** lehetőséget, vagy válassza a **regisztráció ingyenes** lehetőséget egy ingyenes flow-fiók létrehozásához.

2. Jelentkezzen be, majd a menüsávon válassza a **saját folyamatok** lehetőséget.

    ![Saját folyamatok](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows.png)

3. Válassza **a + létrehozás üresből**lehetőséget.

    ![létrehozás üresből](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-create-fromblank.png)

4. Válassza **a létrehozás üresből**lehetőséget.

    ![létrehozás üresből](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-create-fromblank2.png)

5. Az **Összekötők és eseményindítók keresése** mezőben írja be a "kérelem" kifejezést a kérelem-összekötő megkereséséhez.
6. Az **Eseményindítók**területen válassza ki **a HTTP-kérelem fogadásának**időpontját. 

    ![Válassza ki a kapott HTTP-kérelem triggerét](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-pick-request-trigger.png)

7. A **kérelem törzse JSON-séma**konfigurálásához kövesse az alábbi lépések egyikét:

   - Másolja a cikk végén található [JSON-sémát](#json-schema) a **kérelem törzse JSON-séma** szövegmezőbe.
   - Válassza a **Séma létrehozása hasznosadat-minta használatával** lehetőséget. Az **írja be vagy illessze be a minta JSON-adattartalom** szövegmezőbe illessze be a [JSON-példát](#json-example). Válassza a **kész** lehetőséget a séma létrehozásához.

   >[!Note]
   >A folyamat ezen pontján csatlakozhat egy CRM rendszerhez, vagy konfigurálhat egy e-mailes értesítést.

### <a name="to-connect-to-a-crm-system"></a>Kapcsolódás CRM rendszerhez

1. Válassza az **+ új lépés**lehetőséget.
2. Válassza ki az Ön által választott CRM-rendszert, és hozzon létre egy új rekordot a művelettel. Az alábbi képernyőfelvételen a **Dynamics 365 – új rekord létrehozása** példaként.

    ![Új rekord létrehozása](./media/cloud-partner-portal-lead-management-instructions-https/https-image009.png)

3. Adja meg a **szervezet nevét** , amely az összekötőhöz tartozó csatlakozási bemenet. Az **entitás neve** legördülő listából válassza az **érdeklődők** lehetőséget.

    ![Érdeklődők kiválasztása](./media/cloud-partner-portal-lead-management-instructions-https/https-image011.png)

4. A flow egy űrlapot jelenít meg az érdeklődői adatok biztosításához. A bemeneti kérelem elemeit leképezheti a dinamikus tartalom hozzáadására szolgáló lehetőség választásával. Az alábbi képernyőfelvételen a **OfferTitle** látható példa.

    ![Dinamikus tartalom hozzáadása](./media/cloud-partner-portal-lead-management-instructions-https/https-image013.png)

5. Képezze le a kívánt mezőket, majd válassza a **Mentés** lehetőséget a folyamat mentéséhez.

6. A kérelemben létrejön egy HTTP POST URL-cím. Másolja ezt az URL-címet, és használja HTTPS-végpontként.

    ![HTTP Post URL-cím](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

### <a name="to-set-up-email-notification"></a>E-mail értesítések beállítása

1. Válassza az **+ új lépés**lehetőséget.
2. A **válasszon műveletet**területen válassza a **műveletek**lehetőséget.
3. A **Műveletek** területen válassza az **E-mal küldése** lehetőséget.

    ![E-mail művelet hozzáadása](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-add-email-action.png)

4. Az **E-mail küldése**területen adja meg a következő kötelező mezőket:

   - Adjon meg legalább egy érvényes **e-mail-címet** .
   - A **tulajdonosi** folyamat lehetővé teszi, hogy dinamikus tartalmat (például **LeadSource** ) adjon hozzá a következő képernyőfelvételen.

     ![E-mail művelet hozzáadása dinamikus tartalom használatával](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-dynamic-content.png)

   - **Törzs** – a dinamikus tartalmak listájában adja meg a kívánt adatokat az e-mail törzsében. Például: LastName, FirstName, email és Company.

   Ha végzett az e-mail-értesítések beállításával, az a következő képernyőfelvételen látható példához hasonlóan fog kinézni.

   ![E-mail művelet hozzáadása](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-action.png)

5. A folyamat befejezéséhez kattintson a **Mentés** gombra.
6. A kérelemben létrejön egy HTTP POST URL-cím. Másolja ezt az URL-címet, és használja HTTPS-végpontként.

    ![HTTP Post URL-cím](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Az ajánlat beállítása a HTTPS-végpontnak küldött érdeklődők küldésére

Ha az ajánlat érdeklődő-felügyeleti információit konfigurálja, válassza a **https-végpont** lehetőséget az **érdeklődő céljához** , és illessze be az előző lépésben másolt http post URL-címet.  

![Dinamikus tartalom hozzáadása](./media/cloud-partner-portal-lead-management-instructions-https/https-image017.png)

Az érdeklődők létrehozásakor a Microsoft elküldi az érdeklődőket a folyamatnak, amely a beállított CRM rendszerre vagy e-mail-címre irányítja át.

## <a name="json-schema-and-example"></a>JSON-séma és példa

A JSON-teszt példája a következő sémát használja:

### <a name="json-schema"></a>JSON-séma

``` json
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

A következő JSON-példát másolhatja és szerkesztheti, ha tesztként kívánja használni az MS flow-ban.

### <a name="json-example"></a>JSON-példa

```json
{
"OfferTitle": "Test Microsoft",
"LeadSource": "Test run through MS Flow",
"UserDetails": {
"Company": "Contoso",
"Country": "USA",
"Email": "someone@contoso.com",
"FirstName": "Some",
"LastName": "One",
"Phone": "16175555555",
"Title": "Esquire"
}
}
```

## <a name="next-steps"></a>További lépések

Ha még nem tette meg, konfigurálja az ügyfél- [érdeklődőket](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) a Cloud Partner Portalban.
