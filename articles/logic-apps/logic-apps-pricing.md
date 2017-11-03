---
title: "Árképzési & számlázási - Azure Logic Apps |} Microsoft Docs"
description: "Ismerje meg, az Azure Logic Apps árak és számlázás működése."
author: kevinlam1
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: f8f528f5-51c5-4006-b571-54ef74532f32
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: LADocs; klam
ms.openlocfilehash: 63784c5e3af360b2f3f8cb330a9df8b27a85d859
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="logic-apps-pricing-model"></a>Logic Apps díjszabási modell
Az Azure Logic Apps lehetővé teszi a méretezhető és integrációs munkafolyamatok a felhőben.  Az alábbiakban a számlázással és a Logic Apps díjszabások.
## <a name="consumption-pricing"></a>Felhasználás díjszabása
Az újonnan létrehozott Logic Apps a fogyasztás csomag használata. A Logic Apps-fogyasztás árképzési modellt csak kell fizetnie a valóban használt funkciókért.  A Logic Apps nem szabályozott fogyasztás terv használatakor.
A logic app-példány futását végrehajtott összes műveletek mérése.
### <a name="what-are-action-executions"></a>Mik azok a műveleti végrehajtások?
A logic app-definíciót minden lépése művelet, beleértve eseményindítók, folyamatábrán mint állapotától függően a hatókört, minden egyes hurkok, amíg hurkok, összekötők hívásainak lépések, és meghívja a natív műveletek.
Eseményindítók olyan speciális művelet, úgy tervezték, hogy egy adott esemény bekövetkezésekor hozható létre a logikai alkalmazás egy új példányát.  Nincsenek eseményindítók, amelyek hatással lehetnek a logikai alkalmazást hogyan forgalmi díjas számos különböző beállításokat.
* **Lekérdezési eseményindító** – ehhez az eseményindítóhoz folyamatosan lekérdezi a végpont, amíg nem kap egy üzenetet, amely eleget tesz a logikai alkalmazás példányának létrehozásához.  A lekérdezési időköz az eseményindító a Logic App Designer konfigurálható.  Egyes lekérdezési kérelmek akkor is, ha nem hoz létre egy logikai alkalmazás egy példánya számít egy művelet végrehajtását.
* **Webhook eseményindító** – ehhez az eseményindítóhoz megvárja-e az ügyfél elküldi a kérelmet egy adott végpont számára.  Minden a webhook végpontnak küldött kérelemben egy művelet végrehajtási számít. A kérelem és a HTTP Webhook eseményindító mindkét webhook eseményindítók.
* **Ismétlődés eseményindító** – ehhez az eseményindítóhoz létrehozza a logikai alkalmazást az ismétlődési az eseményindító konfigurált alapján.  Például egy ismétlődési eseményindító beállítható úgy, hogy három naponta vagy akár percenként.

A Logic Apps erőforráspanelen eseményindító előzmények részében eseményindító végrehajtások láthatók.

Minden műveletek végrehajtódtak, egy művelet végrehajtását, hogy sikeres vagy sikertelen volt-e azok mérése.  A művelet végrehajtások nem számít a, amelyek ki lettek hagyva, mert a feltétel nem teljesül vagy műveletekről, amelyek nem hajtható végre, mert a logikai alkalmazást még a befejeződése előtt megszakadt.

Hurkok belül végrehajtható műveletek száma a ciklus ismétléseinek számától bájtjai számítanak.  Például az egyetlen művelettel egy for-each ciklus 10 elemek listája iteráció számítanak elemek a listában (10) a műveletek a hurok (1) a számát szorozva és egyet a kezdeményezés a ciklus, amely ebben a példában lenne (10 * 1) + 1 száma = 11 művelet végrehajtások.
Letiltott Logic Apps nem lehet létrehozni új példányok, és ezért le van tiltva, miközben nem számolnak.  Kell szem előtt tartva, hogy a logikai alkalmazás letiltását követően is eltarthat egy kis idő a példányok fokozatos leválasztása előtt teljesen le van tiltva.
### <a name="integration-account-usage"></a>Integráció fiók használata
Használati fogyasztás alapján szereplő van egy [integrációs fiók](logic-apps-enterprise-integration-create-integration-account.md) feltárása, fejlesztési és tesztelési célra, lehetővé téve használatát a [B2B/EDI](logic-apps-enterprise-integration-b2b.md) és [XML-feldolgozás](logic-apps-enterprise-integration-xml.md) funkciók a Logic Apps minden további költség nélkül. Létrehozhat egy fiókot, valamint régió és legfeljebb 10 szerződések és 25 maps tároló legfeljebb áll. Sémákat, a tanúsítványok és a partnerek is nem határoz meg, és feltöltheti a lehető legtöbb, szükség szerint.

Integrációs fiókok felhasználás befoglalási, mellett szabványos integrációs fiókok ezek a korlátozások nélkül és a szabványos Logic Apps SLA-t is létrehozhat. További információkért lásd: [Azure-beli árakról](https://azure.microsoft.com/pricing/details/logic-apps).

## <a name="app-service-plans"></a>App Service-csomagok
A Logic apps korábban létrehozott egy App Service-csomag hivatkozó továbbra is működik, mint korábban. Attól függően, hogy a kiválasztott terv szabályozott után az előírt napi végrehajtások túllépése, de a művelet végrehajtási mérő vannak számlázva.
Nagyvállalati ügyfelek, amelyeknek az előfizetését, amelyek nem rendelkeznek explicit módon a logikai alkalmazás tartozó, egy App Service-csomag része mennyiségek élvezheti.  Például ha egy Standard App Service-csomag a EA előfizetésben és ugyanabban az előfizetésben logikai alkalmazás majd meg nem szó, a 10 000 művelet végrehajtások napi (lásd a következő táblázat). 

App Service-csomagok és a napi megengedett művelet végrehajtások:
|  | Ingyenes/megosztott/egyszerű | Standard | Prémium |
| --- | --- | --- | --- |
| A művelet végrehajtások száma naponta |200 |10,000 |50,000 |
### <a name="convert-from-app-service-plan-pricing-to-consumption"></a>App Service-csomag árképzési fogyasztás konvertálása
Amely rendelkezik az App Service-csomag fogyasztás modellhez társított logikai alkalmazás módosításához távolítsa el az App Service-csomag mutató hivatkozást a Logic App-definíciót.  Ez a módosítás nem végezhető egy PowerShell-parancsmag hívása:`Set-AzureRmLogicApp -ResourceGroupName ‘rgname’ -Name ‘wfname’ –UseConsumptionModel -Force`
## <a name="pricing"></a>Díjszabás
Díjszabása, lásd: [Logic Apps árképzési](https://azure.microsoft.com/pricing/details/logic-apps).

## <a name="next-steps"></a>Következő lépések
* [A Logic Apps áttekintése][whatis]
* [Az első logikai alkalmazás létrehozása][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: logic-apps-what-are-logic-apps.md
[create]: logic-apps-create-a-logic-app.md

