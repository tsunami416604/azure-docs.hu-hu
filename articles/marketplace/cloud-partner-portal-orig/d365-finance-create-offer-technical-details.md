---
title: A technikai információ űrlap kitöltésére |} A Microsoft Docs
description: Adja meg a ismerteti a számára egy új Dynamics 365 Business Central-alkalmazást a műszaki adatok űrlapon.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 5aa118815146287d00a3bb8ee7d5fce57a6ad9ca
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58120295"
---
<a name="how-to-fill-out-the-technical-info-form"></a>Hogyan kell kitölteni a műszaki adatok képernyő
===========================================

1.  Az a **alkalmazástípus kiválasztása** részen töltse fel a bővítmény alkalmazáscsomag-fájl (.app), és minden olyan bővítmény csomagfájlok a bővítmény maga.

    ![Alkalmazáscsomag-információk](./media/d365-financials/image015.png)

-   **Bővítmények csomagfájl** --szükséges – a bővítmény alkalmazáscsomag-fájl (.app).

-   **Függőségi csomagfájl** --szükséges, ha az alkalmazás maga az appsource-ban közzétett egy másik alkalmazás. Ez a .app fájl már közzétett kiterjesztésének az appsource-ban, amely az aktuális alkalmazás függ. 

-   **Szalagtár csomagfájl** -szükséges, ha az alkalmazás maga, amely egy másik alkalmazás *nem* közzé az appsource-ban. Ez .app fájl egy meglévő alkalmazást, de egy nem lett, és nem teszik közzé az appsource-ban.

-   **Alkalmazás tesztelése Automation** --szükséges – a VS kódolt teszt csomagot, amelyet létre kell hoznia, automatizált tesztelés-bővítmények.

1. Az a **további információt a bővítmény** szakaszt, és töltse fel a kiterjesztés vonatkozó további információkat. Ezt az információt használja érvényesítése során.

   ![További információk az app-bővítmény űrlap](./media/d365-financials/image016.png)


-   **URL-címe a termék dokumentációja** --szükséges – URL-címet a bővítmény dokumentációját.

-   **Kulcs használati forgatókönyvek** --szükséges – egy dokumentumot, amely felsorolja a bővítmény részletes telepítés és használat részleteit. Egy példa a cikkben található [felhasználói forgatókönyv dokumentáció](https://docs.microsoft.com/dynamics-nav/compliance/apptest-userscenario/).

-   **Érintett kiadás** --szükséges – válassza ki a kiadás, amelyre az alkalmazás telepítése. Válassza ki **aktuális** az aktuális piaci verzió telepítéséhez. Válassza ki **ezután kisebb** ki kell adni a következő alverzió üzembe helyezhető. Válassza ki **következő jelentős** ki kell adni a következő főverzió üzembe helyezhető.

-   **Prémium szintű Termékváltozat szükséges** – nem kötelező – válassza ki a prémium szintű gomb, amennyiben az alkalmazást a prémium szintű Termékváltozat szükséges. Service Management és a gyártási kizárólag a prémium szintű érhetők el. Részletes információ a prémium szintű tekintheti meg a cikk alapvető és [módosítás, amely funkciók jelennek meg](https://docs.microsoft.com/dynamics365/financials/ui-experiences).

-   **A kód elemzési hibákat magyarázata** – nem kötelező – a dokumentum sorolja fel, és indokolja, hogy nem felel meg a követelményeknek.

-   **Magyarázat az érintett legfontosabb funkcióit** – nem kötelező – a dokumentum sorolja fel, és bármely, amely korlátozza a bővítmény alapvető funkcióit mutatja be.

-   **Tesztfiókok** – nem kötelező – felhasználói fiókok távoli szolgáltatások, webhelyek, stb. a teljes körű használat teszt elvégzéséhez szükséges.

-   **UX követelmények kivételek** – nem kötelező – a dokumentum sorolja fel, és indokolja, hogy nem érheti el, ha a bővítmény felhasználói élmény követelményeit.

A következő lépés, hogy az ajánlatban kirakatban részletek megadása.
