---
title: Vezetőkezelés konfigurálása a Marketo-ban | Azure Piactér
description: Konfigurálja a Marketo vezető felügyeletét az Azure piactéri ügyfelek számára.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: 9fa05eae2d297cbd6ae7243d191cae5a7a3f990e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288529"
---
# <a name="configure-lead-management-in-marketo"></a>Érdeklődőkezelés konfigurálása a Marketo-ban

Ez a cikk azt ismerteti, hogyan állítható be a Marketo a Microsoft értékesítési érdeklődőinek kezelésére.

1. Jelentkezzen be a Marketo-ba.
2. Válassza **a Design Studio**lehetőséget.
    ![Marketo Design Stúdió](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo1.png)

3.  Válassza az **Új űrlap lehetőséget.**
    ![Marketo új forma](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo2.png)

4.  Töltse ki a szükséges mezőket az Új űrlapon, majd válassza a **Létrehozás gombot.**
    ![A Marketo új űrlapot hoz létre](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo3.png)

4.  A Mező részletei területen válassza a **Befejezés**lehetőséget.
    ![Marketo befejezési forma](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo4.png)

5.  Jóváhagyás és bezárás.

6.  A MarketplaceLeadBacked lapon válassza a **Kód beágyazása**lehetőséget.
    ![Marketo beágyazási kód opció](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo5.png)

7.  A Marketo embed kódja a következő példához hasonló kódot jelenít meg.

`<script src="//app-ys12.marketo.com/js/forms2/js/forms2.min.js"></script>`

    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>

1. Másolja a beágyazási kódban látható értékeket, hogy konfigurálhassa a **kiszolgálóazonosítót**, a **Munchkin-azonosítót**és az **Űrlapazonosítót** a Cloud Partner Portal Marketo mezőiben.

Használja a következő példát útmutatóként a Marketo embed kód példából szükséges azonosítók megszerzéséhez.

- Kiszolgáló azonosítója = **ys12**
- Munchkin id = **123-PQR-789**
- Űrlapazonosító = **1179**
