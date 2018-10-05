---
title: HTTPS-végpont |} A Microsoft Docs
description: Lead felügyelet HTTPS konfigurálása.
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
ms.date: 09/17/2018
ms.author: pbutlerm
ms.openlocfilehash: fd13a7281c7e8702fd199364261ebcd458db0555
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48809696"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>HTTPS-végpont használatával lead felügyelet konfigurálása

HTTPS-végpont használatával kezeli az Azure Marketplace és appsource-ban az érdeklődők, hogy egy CRM-rendszerrel írhatók. Ez a cikk ismerteti a Microsoft Flow automatizálási szolgáltatás használatával lead felügyelet konfigurálása.


## <a name="create-a-flow-using-microsoft-flow"></a>Hozzon létre egy folyamatot, Microsoft Flow használatával

1.  Nyissa meg a [Flow](https://flow.microsoft.com/) weblapon. Válassza ki **jelentkezzen be a** , vagy válasszon **ingyenes regisztráció** ingyenes Flow-fiók létrehozásához.

2.  Jelentkezzen be, és válassza ki **saját folyamatok** a menüsávon.

    ![Saját folyamatok](./media/cloud-partner-portal-lead-management-instructions-https/image001.png)

3.  Válassza ki **üres folyamat létrehozása**.

    ![Üres folyamat létrehozása](./media/cloud-partner-portal-lead-management-instructions-https/image003.png)


4.  Válassza ki a **kérés/válasz** összekötő, és keressen a kérés eseményindító. 

    ![Üres folyamat létrehozása](./media/cloud-partner-portal-lead-management-instructions-https/image005.png)

5. Válassza ki a **kérelem** eseményindító.
    ![Kérelem típusú trigger](./media/cloud-partner-portal-lead-management-instructions-https/image007.png)


6.  Másolás a **példa JSON** , ez a cikk végén található a **kérelem Kéréstörzs JSON-sémája**.

7.  Új lépés hozzáadása, és válassza ki a CRM-rendszerrel, a választott a művelettel egy új rekord létrehozásához. A következő képernyőn látható rögzítése **Dynamics 365 – új rekord létrehozása** példaként.

    ![Új rekord létrehozása](./media/cloud-partner-portal-lead-management-instructions-https/image009.png)

8.  Adja meg a kapcsolat bemenetei között meg az összekötő, válassza ki a **érdeklődők** entitás.

    ![Válassza ki az érdeklődők](./media/cloud-partner-portal-lead-management-instructions-https/image011.png)

9.  Folyamatok, amelyek biztosítják az érdeklődők adatait egy űrlap látható. Válassza ki a dinamikus tartalom hozzáadása a bemeneti kérelem elemek leképezheti.

    ![Dinamikus tartalom hozzáadása](./media/cloud-partner-portal-lead-management-instructions-https/image013.png)

10.  Szeretné, és válassza ki a mezők leképezése **mentése** annak mentéséhez.

11. Egy HTTP POST URL-címe a kérelemben szereplő jön létre. Másolja az URL-címet, és szeretné használni a HTTPS-végpont.

    ![HTTP Post URL-címe](./media/cloud-partner-portal-lead-management-instructions-https/image015.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Az ajánlat az érdeklődők küldeni a HTTPS-végpont konfigurálása

Az érdeklődők felügyeleti adatait az ajánlatban konfigurálásakor válassza ki a **HTTPS-végpont** a cél vezethet és illessze be a HTTP POST URL-címet az előző lépésben kimásolt.  

![Dinamikus tartalom hozzáadása](./media/cloud-partner-portal-lead-management-instructions-https/image017.png)

Érdeklődők jönnek létre, amikor a Microsoft a folyamatot, amely a CRM-rendszerrel konfigurált van irányítva az érdeklődők küld.


## <a name="json-example"></a>Példa JSON

``` json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
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
