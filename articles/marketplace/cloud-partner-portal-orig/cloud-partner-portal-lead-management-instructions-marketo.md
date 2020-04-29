---
title: Az érdeklődők felügyeletének konfigurálása a Marketo-ben | Azure piactér
description: Az Azure Marketplace-ügyfelek Marketo vezető felügyeletének konfigurálása.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: 9fa05eae2d297cbd6ae7243d191cae5a7a3f990e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80288529"
---
# <a name="configure-lead-management-in-marketo"></a>Az érdeklődők felügyeletének konfigurálása a Marketo-ben

Ez a cikk azt ismerteti, hogyan állíthatja be a Marketo a Microsoft értékesítési érdeklődők kezelésére.

1. Jelentkezzen be a Marketo.
2. Válassza a **Design Studio**elemet.
    ![Marketo Design Studio](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo1.png)

3.  Válassza az **új űrlap**lehetőséget.
    ![Új Marketo űrlap](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo2.png)

4.  Töltse ki a kötelező mezőket az új űrlapon, majd válassza a **Létrehozás**lehetőséget.
    ![Új űrlap létrehozása a Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo3.png)

4.  A mező részletei lapon válassza a **Befejezés**lehetőséget.
    ![Marketo befejezési űrlapja](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo4.png)

5.  Jóváhagyás és bezárás.

6.  A MarketplaceLeadBacked lapon válassza a **beágyazási kód**lehetőséget.
    ![Marketo beágyazási kód beállítás](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo5.png)

7.  A Marketo beágyazási kód az alábbi példához hasonló kódot jelenít meg.

`<script src="//app-ys12.marketo.com/js/forms2/js/forms2.min.js"></script>`

    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>

1. Másolja a beágyazási kódban megjelenő értékeket úgy, hogy a **kiszolgáló azonosítóját**, a **Munchkin azonosítóját**és az **űrlap azonosítóját** a Cloud Partner Portal Marketo mezőiben konfigurálja.

Használja a következő példát útmutatóként a szükséges azonosítók beolvasásához a Marketo beágyazási kódjának példája alapján.

- Kiszolgáló azonosítója = **ys12**
- Munchkin-azonosító = **123-PQR-789**
- Űrlap azonosítója = **1179**
