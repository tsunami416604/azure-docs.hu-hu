---
title: Az érdeklődők felügyeletének konfigurálása a Marketo-ben | Azure piactér
description: Az Azure Marketplace-ügyfelek Marketo vezető felügyeletének konfigurálása.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pabutler
ms.openlocfilehash: 7949507c8c7ef57cded25cde8579c1945aa93a81
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825201"
---
# <a name="configure-lead-management-in-marketo"></a>Az érdeklődők felügyeletének konfigurálása a Marketo-ben

Ez a cikk azt ismerteti, hogyan állíthatja be a Marketo a Microsoft értékesítési érdeklődők kezelésére.

1. Jelentkezzen be a Marketo.
2. Válassza a **Design Studio**elemet.
    ![Marketo Design Studio](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo1.png)

3.  Válassza az **új űrlap**lehetőséget.
    ![Marketo új űrlap](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo2.png)

4.  Töltse ki a kötelező mezőket az új űrlapon, majd válassza a **Létrehozás**lehetőséget.
    ![Marketo új űrlap létrehozása](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo3.png)

4.  A mező részletei lapon válassza a **Befejezés**lehetőséget.
    ![Marketo befejezési formája](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo4.png)

5.  Jóváhagyás és bezárás.

6.  A MarketplaceLeadBacked lapon válassza a **beágyazási kód**lehetőséget.
    ![Marketo beágyazási kód beállítás](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo5.png)

7.  A Marketo beágyazási kód az alábbi példához hasonló kódot jelenít meg.

`<script src="//app-ys12.marketo.com/js/forms2/js/forms2.min.js"></script>`

    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>

1. Másolja a beágyazási kódban megjelenő értékeket úgy, hogy a **kiszolgáló azonosítóját**, a **Munchkin azonosítóját**és az **űrlap azonosítóját** a Cloud Partner Portal Marketo mezőiben konfigurálja.

Használja a következő példát útmutatóként a szükséges azonosítók beolvasásához a Marketo beágyazási kódjának példája alapján.

- Kiszolgáló azonosítója = **ys12**
- Munchkin-azonosító = **123-PQR-789**
- Űrlap azonosítója = **1179**\
