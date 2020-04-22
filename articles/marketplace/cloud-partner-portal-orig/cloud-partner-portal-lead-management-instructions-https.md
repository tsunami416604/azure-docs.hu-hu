---
title: Érdeklődőkezelés konfigurálása HTTPS-végpont használatával | Azure Piactér
description: Ismerje meg, hogyan használhat HTTP-végpontot a Microsoft AppSource és az Azure Marketplace-érdeklődők kezeléséhez.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: dsindona
ms.openlocfilehash: f56cc5aaad7d77ff8dc753115ef1becb08ddde73
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770185"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>Érdeklődőkezelés konfigurálása HTTPS-végpont használatával

Https-végpont használatával kezelheti a Microsoft AppSource és az Azure Marketplace-érdeklődőket. Ezeket az érdeklődőket ügyfélkapcsolat-kezelési (CRM) rendszerbe lehet írni, vagy e-mailben értesítést küldeni. Ez a cikk azt ismerteti, hogy miként konfigurálható a [Microsoft Power Automate](https://powerapps.microsoft.com/automate-processes/) automatizálási szolgáltatás az érdeklődőkezelés konfigurálásához.

## <a name="create-a-flow-using-microsoft-power-automate"></a>Folyamat létrehozása a Microsoft Power Automate segítségével

1. Nyissa meg a [Power Automate](https://flow.microsoft.com/) weblapot. Szabad flow-fiók létrehozásához válassza **a Bejelentkezés** lehetőséget, vagy válassza a **Szabad regisztráció** lehetőséget.

1. Jelentkezzen be, és válassza a **Saját folyamatok** lehetőséget a menüsorban.
    > [!div class="mx-imgBorder"]
    > ![Saját folyamatok](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows.png)

1. A **+ Új**csoportban válassza a + Azonnali lehetőséget az üres **területen.**
    > [!div class="mx-imgBorder"]
    > ![Üres folyamat létrehozása](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-create-fromblank.png)

1. Nevezze el a folyamatot, majd **a Válassza ki, hogyan szeretné elindítani ezt a folyamatot,** válassza **a HTTP-kérelem fogadásának korlehetőséget.**

    > [!div class="mx-imgBorder"]
    > ![A FOGADOTT HTTP-kérelem eseményindítójának kiválasztása](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-pick-request-trigger.png)

1. Kattintson a folyamatlépésre a kibontásához.

    > [!div class="mx-imgBorder"]
    > ![Az áramlási lépés kibontása](./media/cloud-partner-portal-lead-management-instructions-https/expand-flow-step.png)

1. A **Kérelemtörzs JSON-sémájának**konfigurálásához használja az alábbi módszerek egyikét:

   - Másolja a cikk végén található [JSON-sémát](#json-schema) a **Kérelem törzse JSON-séma** mezőbe.
   - Válassza a **Séma létrehozása hasznosadat-minta használatával** lehetőséget. Az **Enter or illessze be a minta JSON hasznos adat** szövegmező, illessze be a [JSON példa](#json-example). A séma létrehozásához válassza a **Kész** lehetőséget.

   >[!Note]
   >A folyamat ezen pontján csatlakozhat egy CRM-rendszerhez, vagy e-mail értesítést állíthat be.

### <a name="to-connect-to-a-crm-system"></a>Csatlakozás CRM-rendszerhez

1. Válassza az **+ Új lépés** gombot.
2. Új rekord létrehozásához válassza ki az Ön által választott CRM-rendszert a művelettel. A következő képernyőfelvételen a **Dynamics 365 – Új rekord létrehozása** példaként látható.

    ![Új rekord létrehozása](./media/cloud-partner-portal-lead-management-instructions-https/https-image009.png)

3. Adja meg a **szervezet nevét,** amely az összekötő kapcsolatbemenete. Válassza az **Érdeklődők** elemet az **Entitásnév** legördülő listából.

    ![Érdeklődők kijelölése](./media/cloud-partner-portal-lead-management-instructions-https/https-image011.png)

4. A Folyamat az érdeklődőadatok megadására szolgáló űrlapot jelenít meg. A beviteli kérelem elemeit dinamikus tartalom hozzáadásával képezheti le. A következő képernyőfelvételen példaként az **OfferTitle** látható.

    ![Dinamikus tartalom hozzáadása](./media/cloud-partner-portal-lead-management-instructions-https/https-image013.png)

5. Képezze le a kívánt mezőket, majd válassza a **Mentés** gombot a folyamat mentéséhez.

6. A kérelemben létrejön egy HTTP-BEJEGYZÉS URL-cím. Másolja ezt az URL-címet, és használja https-végpontként.

    ![HTTP-bejegyzés URL-címe](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

### <a name="to-set-up-email-notification"></a>A levelezési értesítés beállítása

1. Válassza az **+ Új lépés** gombot.
2. A **Művelet kiválasztása**csoportban válassza a **Műveletek**lehetőséget.
3. A **Műveletek** területen válassza az **E-mal küldése** lehetőséget.

    ![E-mail művelet hozzáadása](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-add-email-action.png)

4. Az **E-mail küldése**területen konfigurálja a következő szükséges mezőket:

   - **Címzett** - Adjon meg legalább egy érvényes e-mail címet.
   - **Tárgy** - A Flow lehetőséget ad dinamikus tartalom hozzáadására, például a **LeadSource-hoz** a következő képernyőfelvételen.

     ![E-mail művelet hozzáadása dinamikus tartalommal](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-dynamic-content.png)

   - **Törzs** – A dinamikus tartalomlistában adja hozzá a kívánt információkat az e-mail törzsébe. Például vezetéknév, keresztnév, e-mail és vállalat.

   Amikor befejezte az e-mail értesítés beállítását, a következő képernyőfelvételen a példaként fog kinézni.

   ![E-mail művelet hozzáadása](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-action.png)

5. A folyamat befejezéséhez válassza a **Mentés** lehetőséget.

6. A kérelemben létrejön egy HTTP-BEJEGYZÉS URL-cím. Másolja ezt az URL-címet, és használja https-végpontként.

    ![HTTP-bejegyzés URL-címe](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Az ajánlat konfigurálása a HTTPS-végpontra való érdeklődők küldésére

Amikor konfigurálja az érdeklődőkezelési adatokat az ajánlathoz, válassza a **HTTPS-végpontot** az **érdeklődő célhoz,** és illessze be az előző lépésben másolt HTTP POST URL-címbe.  

![Dinamikus tartalom hozzáadása](./media/cloud-partner-portal-lead-management-instructions-https/https-image017.png)

Érdeklődők létrehozásakor a Microsoft érdeklődőket küld a Power Automate-folyamathoz, amely a beállított CRM-rendszerhez vagy e-mail-címhez irányítja azokat.

## <a name="json-schema-and-example"></a>JSON-séma és példa

A JSON-tesztpélda a következő sémát használja:

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

Másolhatja és szerkesztheti a következő JSON-példát, amelyet tesztként használhat a folyamatban.

### <a name="json-example"></a>JSON példa

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

Ha még nem tette meg, konfigurálja az [ügyfélérdeklődőket](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) a Cloud Partner Portalon.
