---
title: HTTPS-végpont |} A Microsoft Docs
description: HTTPS-végpont érdeklődő felügyeletének konfigurálásához.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/24/2018
ms.author: pbutlerm
ms.openlocfilehash: cfcd154b2f44c9e8acf12a9666abc9ce95fb3c26
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58648350"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>HTTPS-végpont használatával lead felügyelet konfigurálása

HTTPS-végpont használatával kezeli az Azure Marketplace-en, és az appsource-ban vezet. Érdeklődők válaszaránya szolgáltatásba lehet írni, amely írt egy ügyfél kapcsolat felügyeleti (CRM) rendszerhez, vagy e-mailben értesítést küldeni. Ez a cikk azt ismerteti, érdeklődő felügyeleti történő konfigurálása a [Microsoft Flow](https://powerapps.microsoft.com/automate-processes/) automation szolgáltatást.

## <a name="create-a-flow-using-microsoft-flow"></a>Hozzon létre egy folyamatot, Microsoft Flow használatával

1. Nyissa meg a [Flow](https://flow.microsoft.com/) weblapon. Válassza ki **jelentkezzen be a** , vagy válasszon **ingyenes regisztráció** ingyenes Flow-fiók létrehozásához.

2. Jelentkezzen be, és válassza ki **saját folyamatok** a menüsávon.

    ![Saját folyamatok](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows.png)

3. Válassza ki **+ üres folyamat létrehozása**.

    ![Üres folyamat létrehozása](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-create-fromblank.png)

4. Válassza ki **üres folyamat létrehozása**.

    ![Üres folyamat létrehozása](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-create-fromblank2.png)

5. Az a **összekötő és trigger között keresés** mezőbe írja be a "request" a kérelem-összekötő található.
6. A **eseményindítók**válassza **amikor egy HTTP-kérés érkezik**. 

    ![A HTTP-kérelem érkezett a trigger kiválasztása](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-pick-request-trigger.png)

7. Konfigurálásához használja az alábbi lépések egyikét a **kérelem Kéréstörzs JSON-sémája**:

   - Másolás a [JSON-sémája](#json-schema) , ez a cikk végén található a **kérelem Kéréstörzs JSON-sémája** szövegmezőben.
   - Válassza a **Séma létrehozása hasznosadat-minta használatával** lehetőséget. Az a **írja vagy illessze be a JSON hasznosadat-minta** szövegbeviteli mezőben illessze be a [példa JSON](#json-example). Válassza ki **kész** , létre kell hozni a sémát.

   >[!Note]
   >Ezen a ponton a folyamat kapcsolódni egy CRM-rendszerrel, vagy egy e-mail-értesítések konfigurálása.

### <a name="to-connect-to-a-crm-system"></a>Egy CRM-rendszerekhez való kapcsolódáshoz

1. Válassza ki **+ új lépés**.
2. Válassza ki a CRM-rendszerrel, a választott a művelettel egy új rekord létrehozásához. Az alábbi képernyőn látható rögzítése **Dynamics 365 – új rekord létrehozása** példaként.

    ![Új rekord létrehozása](./media/cloud-partner-portal-lead-management-instructions-https/https-image009.png)

3. Adja meg a **szervezetnevet** Ez az összekötő a kapcsolat bemenetei között. Válassza ki **érdeklődők** származó a **entitás neve** legördülő listából.

    ![Válassza ki az érdeklődők](./media/cloud-partner-portal-lead-management-instructions-https/https-image011.png)

4. A folyamat egy képernyő, amelyek biztosítják az érdeklődők adatait jeleníti meg. Válassza ki a dinamikus tartalom hozzáadása a bemeneti kérelem elemek leképezheti. Az alábbi képernyőn látható rögzítése **OfferTitle** példaként.

    ![Dinamikus tartalom hozzáadása](./media/cloud-partner-portal-lead-management-instructions-https/https-image013.png)

5. Szeretné, és válassza ki a mezők leképezése **mentése** annak mentéséhez.

6. Egy HTTP POST URL-címe a kérelemben szereplő jön létre. Másolja az URL-címet, és szeretné használni a HTTPS-végpont.

    ![HTTP Post URL-címe](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

### <a name="to-set-up-email-notification"></a>E-mail-értesítések beállítása

1. Válassza ki **+ új lépés**.
2. A **válasszon ki egy műveletet**válassza **műveletek**.
3. A **Műveletek** területen válassza az **E-mal küldése** lehetőséget.

    ![E-mail-művelet hozzáadása](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-add-email-action.png)

4. A **e-mail küldése**, konfigurálja a következő kötelező mezőket:

   - **A** – legalább egy érvényes e-mail címet adjon meg.
   - **Tulajdonos** -folyamat teszi lehetővé, például a dinamikus tartalom hozzáadása **LeadSource** az alábbi képernyőfelvételen.

     ![E-mail-művelet használatával dinamikus tartalom hozzáadása](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-dynamic-content.png)

   - **Törzs** – a dinamikus tartalmú listából az e-mail törzsében található adatokat, adja hozzá. Ha például utónév, Vezetéknév, E-mail, és a céges.

   Ha elkészült, az e-mail-értesítés beállítása, az alábbi képernyőfelvételen példához hasonlóan fog kinézni.

   ![E-mail-művelet hozzáadása](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-action.png)

5. Válassza ki **mentése** a folyamat befejezéséhez.
6. Egy HTTP POST URL-címe a kérelemben szereplő jön létre. Másolja az URL-címet, és szeretné használni a HTTPS-végpont.

    ![HTTP Post URL-címe](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Az ajánlat az érdeklődők küldeni a HTTPS-végpont konfigurálása

Az érdeklődők felügyeleti adatait az ajánlatban konfigurálásakor válassza ki a **HTTPS-végpont** a a **vezethet cél** , és illessze be az előző lépésben kimásolt HTTP POST URL-címben.  

![Dinamikus tartalom hozzáadása](./media/cloud-partner-portal-lead-management-instructions-https/https-image017.png)

Érdeklődők jönnek létre, a Microsoft a folyamatot, amely a CRM rendszert vagy az e-mail címet, amelyet a van irányítva az érdeklődők küld.

## <a name="json-schema-and-example"></a>Példa és JSON-sémája

A JSON teszt példában a következő mintát követik:

### <a name="json-schema"></a>JSON-sémája

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

Másolja, és szerkessze a következő JSON-példa egy teszt, az MS Flow-ban használandó.

### <a name="json-example"></a>Példa JSON

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

Ha ezt még nem tette meg, konfigurálja az ügyfél [érdeklődők](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) az a Cloud Partner portálra.
