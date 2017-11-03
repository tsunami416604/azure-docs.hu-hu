---
title: "Az egyéni összekötők - Azure Logic Apps tanúsítására |} Microsoft Docs"
description: "Összekötők Azure Logic Apps, a Microsoft Flow és a Microsoft PowerApps minden felhasználója számára elérhetővé"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 9bcb138c181df0c12548cc5dc721542fd9c9ba68
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="submit-your-connectors-for-microsoft-certification"></a>Küldje el a Microsoft hitelesítő az összekötők

Tekintse át, az érvényesítés és a jóváhagyási közzététele a Microsoft összekötő egyéni összekötők nyilvánosan elérhetővé tétele az összes felhasználó számára az Azure Logic Apps, a Microsoft Flow és a Microsoft PowerApps elküldeni. 

## <a name="certification-criteria"></a>Hitelesítésszolgáltató feltételek

| Képesség | Részletek | Szükséges vagy ajánlott |
|------------|---------|-------------------------|
| Alkalmazás,-a-szoftverszolgáltatás (SaaS) | Megfelel egy felhasználói forgatókönyvnek megfelelő is a Logic Apps, a folyamat és a powerapps segítségével. | Szükséges |
| Hitelesítés típusa | Az API-támogatnia kell az OAuth2, az API-kulcs vagy az egyszerű hitelesítés. | Szükséges | 
| Támogatás | Meg kell adnia egy támogatási kapcsolattartó, hogy az ügyfelek tudjanak segítséget kérni. | Szükséges | 
| Rendelkezésre állás és a hasznos üzemidő | Az alkalmazás rendelkezik legalább 99,9 %-os hasznos üzemidő. | Ajánlott | 
|||| 

Is ha Ön nem a Microsoft partnereitől és a független szoftverszállító (ISV), és igazolja, és nyilvánosan kiadási az összekötőt, kell vagy saját a mögöttes szolgáltatás vagy a jelenlegi explicit jogosultsága ahhoz, hogy az API-val.

## <a name="validation-phases"></a>Érvényesítési fázisok

Microsoft érvényesítési fázisokkal használ a connector kiértékelése:

| Érvényesítési fázist | Leírás | 
| ----- | ----------- |
| Funkció | A Microsoft ezeket a hibákat a Logic Apps, a folyamat és a PowerApps összekötő ellenőrzések: <p>-OpenAPI (Swagger) vagy a JSON érvénytelen a hibákat, a definíció szakaszban jelennek meg az egyéni összekötő varázsló <p>-A vizsgálat szakasza jelennek meg az egyéni összekötő varázslóban való futtatókörnyezet és séma érvényesítési hibák | 
| Tartalom | A Microsoft ellenőrzi, hogy az összekötő használja felhasználóbarát neveinek és leírásainak minden egyes entitásnál. Győződjön meg arról, hogy minden művelet, a bemeneti paraméter, és a válasz attribútumot a connector Swagger rendelkezik-e ezeket az elemeket: <p>- [Összefoglalás](../logic-apps/custom-connector-openapi-extensions.md#summary) <br>- [Leírás](../logic-apps/custom-connector-openapi-extensions.md#description) </br>- [Látható információk](../logic-apps/custom-connector-openapi-extensions.md#visibility) | 
||| 

## <a name="nominate-and-submit-your-connector-to-microsoft-for-certification"></a>Jelöljön ki, és küldje el a Microsoft összekötő hitelesítő

Hitelesítésszolgáltató telepítéséhez kövesse az alábbi lépéseket:

1. **Jelöljön ki**

   1. [Küldje el a jelölési](https://go.microsoft.com/fwlink/?linkid=848754).

      1. A lap alján válassza **, lépjen velünk kapcsolatba**.
      2. Töltse ki az űrlapot, és válassza ki **ISV connector program és a közös marketing kérdések**.
      3. Válasszon **nyújt**.

   2. A kölcsönös nem nyilatkozat elfogadása és hibaüzenet Partnerszerződést aláírásához. 

      A Microsoft e aláírt szerződések a folytatás előtt szükséges. 
      Jelenleg akkor ellenőrizze, hogy az összekötő megfelel a certification. 
   
   3. Ha az összekötő jóvá van hagyva, akkor Microsoft értesíti a bevezetési utasításokat tartalmaz.
    
2. **Áttekintés**

   1. Ezek az információk küldése jelölési partnere, tekintse át a:

      * Az API-t a OpenAPI fájlba
      * Az ikonfájl (.png vagy .jpg), amely jelöli az összekötő 
      
        Az ikon belül 230 képpontos négyzet ~ 160 képpontos embléma kell rendelkeznie. 
        Színes háttéren fehér embléma használata ajánlott.
      
      * Az ikon márka hexadecimális formátumban, meg kell felelnie az ikonfájl színes háttérre szín

      * Egy olyan fiókot a ellenőrzésére szolgáló főtanúsítványokat
      * Egy támogatási kapcsolattartó

   2. Ha további információra van szükségünk, kapcsolatba lépünk Önnel.

3. **Közzététel**

    Ellenőrizzük, hogy az összekötő funkció és a tartalom, miután az összekötő központi telepítés összes termékek és régiók tesztelése azt.
    
    Alapértelmezés szerint az összes összekötőt "prémium" összekötők kerülnek közzétételre. 
    Ha az alkalmazás az Azure-ral, az összekötő felsoroló, az Office 365 vállalati tervek minden felhasználó számára elérhető "standard" összekötő alkalmazhatja. 
    További részletekért kérje jelölési.

## <a name="next-steps"></a>Következő lépések

* [Egyéni összekötő – gyakori kérdések](../logic-apps/custom-connector-faq.md)
* [Egyéni összekötő áttekintése](../logic-apps/custom-connector-overview.md)