---
title: A Marketo |} A Microsoft Docs
description: A Marketo érdeklődő felügyeletének konfigurálásához.
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
ms.date: 09/14/2018
ms.author: pbutlerm
ms.openlocfilehash: abb0abb94d3b3e7abc4dce58cdb11fa0c2cedd34
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48809704"
---
# <a name="configure-lead-management-in-marketo"></a>A marketo rendszerében lead felügyelet konfigurálása

Ez a cikk ismerteti, hogyan állítható be a Marketo Microsoft értékesítési érdeklődők kezeléséhez.

1. Jelentkezzen be a Marketo.
2. Válassza ki **Studio tervezési**.
    ![Marketo-Tervező Studio](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo1.png)

3.  Válassza ki **új űrlap**.
    ![A Marketo új űrlap](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo2.png)

4.  Töltse ki a kötelező mezőket az új képernyőn, majd **létrehozás**.
    ![A Marketo új űrlap létrehozása](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo3.png)

4.  A mező részleteit, jelölje be **Befejezés**.
    ![A Marketo Befejezés űrlap](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo4.png)

5.  Hagyja jóvá, és zárja be.

6.  A MarketplaceLeadBacked lapon válassza ki a **beágyazási kód**.
    ![A Marketo beágyazási kód beállítása](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo5.png)

7.  A Marketo beágyazási kódot az alábbi példához hasonló kódot jelenít meg.

`<script src="//app-ys12.marketo.com/js/forms2/js/forms2.min.js"></script>`

    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>

8.  Másolja a beágyazási kódban látható, hogy konfigurálhassa a **kiszolgálóazonosító**, **Munchkin azonosító**, és **űrlap-azonosító** a Marketo mezőket a Cloud Partner portálra.

Az azonosítók kell lekérése a Marketo beágyazási kód példa használni alapján a következő példában.

- Kiszolgáló azonosítója = **ys12**
- Munchkin azonosítója = **123-PQR-789**
- Űrlap-Id = **1179**\
