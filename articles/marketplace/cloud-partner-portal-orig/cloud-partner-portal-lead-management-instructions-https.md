---
title: Az érdeklődők felügyeletének konfigurálása HTTPS-végpont használatával | Azure piactér
description: Megtudhatja, hogyan használhatja a HTTP-végpontokat Microsoft AppSource és az Azure Marketplace-érdeklődők kezelésére.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: dsindona
ms.openlocfilehash: f56cc5aaad7d77ff8dc753115ef1becb08ddde73
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81770185"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>Az érdeklődők felügyeletének konfigurálása HTTPS-végpont használatával

HTTPS-végpontok segítségével kezelheti Microsoft AppSource és az Azure Marketplace-érdeklődőket. Ezek az érdeklődők az Ügyfélkapcsolat-kezelési (CRM) rendszerbe írhatók, vagy e-mail-értesítésként lesznek elküldve. Ez a cikk azt ismerteti, hogyan használható a [Microsoft energiagazdálkodási](https://powerapps.microsoft.com/automate-processes/) automatizálási szolgáltatás az érdeklődők felügyeletének konfigurálásához.

## <a name="create-a-flow-using-microsoft-power-automate"></a>Folyamat létrehozása a Microsoft Power automatizáló használatával

1. Nyissa meg a [Power automatizáló](https://flow.microsoft.com/) weblapot. Válassza a **Bejelentkezés** lehetőséget, vagy válassza a **regisztráció ingyenes** lehetőséget egy ingyenes flow-fiók létrehozásához.

1. Jelentkezzen be, majd a menüsávon válassza a **saját folyamatok** lehetőséget.
    > [!div class="mx-imgBorder"]
    > ![Saját folyamatok](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows.png)

1. Az **+ új**alatt válassza **a + azonnali**lehetőséget.
    > [!div class="mx-imgBorder"]
    > ![Üres folyamat létrehozása](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-create-fromblank.png)

1. Adja meg a folyamat nevét, majd a folyamat kiváltási **módjának kiválasztása**területen válassza ki **a HTTP-kérés fogadásakor**lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![Válassza ki a kapott HTTP-kérelem triggerét](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-pick-request-trigger.png)

1. Kattintson a flow lépésre a kibontásához.

    > [!div class="mx-imgBorder"]
    > ![A folyamat lépésének kibontása](./media/cloud-partner-portal-lead-management-instructions-https/expand-flow-step.png)

1. A **kérelem törzse JSON-séma**konfigurálásához használja a következő módszerek egyikét:

   - Másolja a cikk végén található [JSON-sémát](#json-schema) a **kérelem törzse JSON-séma** szövegmezőbe.
   - Válassza a **Séma létrehozása hasznosadat-minta használatával** lehetőséget. Az **írja be vagy illessze be a minta JSON-adattartalom** szövegmezőbe illessze be a [JSON-példát](#json-example). Válassza a **kész** lehetőséget a séma létrehozásához.

   >[!Note]
   >A folyamat ezen pontján csatlakozhat egy CRM rendszerhez, vagy konfigurálhat egy e-mailes értesítést.

### <a name="to-connect-to-a-crm-system"></a>Kapcsolódás CRM rendszerhez

1. Válassza az **+ Új lépés** gombot.
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

1. Válassza az **+ Új lépés** gombot.
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

Az érdeklődők létrehozásakor a Microsoft elküldi az érdeklődőket a Power automatizáló folyamatnak, amely a konfigurált CRM rendszerre vagy e-mail-címre irányítja át.

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
    "Description": {
      "id": "/properties/Description",
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

Az alábbi JSON-példa másolásával és szerkesztésével tesztként használható a folyamatában.

### <a name="json-example"></a>JSON-példa

```json
{
  "UserDetails": {
    "FirstName": "Some",
    "LastName": "One",
    "Email": "someone@contoso.com",
    "Phone": "16175555555",
    "Country": "USA",
    "Company": "Contoso",
    "Title": "Esquire"
 },
  "LeadSource": "AzureMarketplace",
  "ActionCode": "INS",
  "OfferTitle": "Test Microsoft",
  "Description": "Test run through Power Automate"
}
```

## <a name="next-steps"></a>További lépések

Ha még nem tette meg, konfigurálja az ügyfél- [érdeklődőket](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) a Cloud Partner Portalban.
