---
title: "Kódolja vagy egybesimított fájlok az Azure logic apps |} Microsoft Docs"
description: "A fájl fájl kódoló és dekóder használata a logic Apps alkalmazásokat a vállalati integrációs csomag"
services: logic-apps
documentationcenter: .net,nodejs,java
author: divyaswarnkar
manager: anneta
editor: 
ms.assetid: 82152dab-c7ad-43df-b721-596559703be8
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; divswa
ms.openlocfilehash: 410f2e184a92b879d4f75b31216da783e82dc953
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="overview-of-enterprise-integration-with-flat-files"></a>Vállalati integrációs egybesimított fájlok áttekintése

Érdemes lehet XML-tartalom kódolása egy üzleti partner üzleti vállalatközi (B2B) esetén a küldés előtt. A logikai alkalmazás egyszerű fájlkódolás összekötő ehhez használhatja. A logikai alkalmazást az Ön által létrehozott lekérheti az XML tartalom különböző forrásokból, beleértve egy HTTP-kérelem eseményindítóval, egy másik alkalmazás, vagy akár egy a többhöz [összekötők](../connectors/apis-list.md). A logic apps kapcsolatos további információkért tekintse meg a [logic apps dokumentációs](logic-apps-overview.md "további információk a Logic apps").  

## <a name="create-the-flat-file-encoding-connector"></a>Egyszerű fájlkódolás összekötő létrehozása
Kövesse az alábbi lépéseket a logikai alkalmazás-összekötő kódolás egybesimított fájl hozzáadásához.

1. Logikai alkalmazás létrehozása és [hivatkozás integrációs fiókjába](logic-apps-enterprise-integration-accounts.md "integrációs fiók csatolása logikai alkalmazás további"). Ezt a fiókot kódolására az XML-adatok használhatja a séma tartalmazza.  
2. Adja hozzá a **kérelem - amikor egy HTTP-kérelem érkezik** eseményindító a logikai alkalmazáshoz.  
   ![Képernyőkép a eseményindító kiválasztásához](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)    
3. Adja hozzá a strukturálatlan fájlkódolás művelet, az alábbiak szerint:
   
    a. Válassza ki a **plus** bejelentkezési.
   
    b. Válassza ki a **művelet hozzáadása** (jelenik meg, miután kiválasztotta a plusz jelre) hivatkozásra.
   
    c. A keresési mezőbe, írja be a *egyszerű* szűrése, amely a használni kívánt összes műveletet.
   
    d. Válassza ki a **strukturálatlan fájlkódolás** elemet a listában.   
   ![Képernyőfelvétel a strukturálatlan fájlkódolás beállítás](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   
4. Az a **strukturálatlan fájlkódolás** párbeszédpanelen jelölje ki a **tartalom** szövegmezőben.  
   ![Képernyőfelvétel a tartalom szövegmező](media/logic-apps-enterprise-integration-flatfile/flatfile-3.png)  
5. A szervezet címke jelöli ki a kódolni kívánt tartalom. A szervezet címke feltölti a tartalom mezőben.     
   ![Képernyőkép a szervezet címke](media/logic-apps-enterprise-integration-flatfile/flatfile-4.png)  
6. Válassza ki a **sémanév** listán, és válassza ki a sémát, a beviteli tartalom kódolására.    
   ![Képernyőfelvétel a séma neve listamező](media/logic-apps-enterprise-integration-flatfile/flatfile-5.png)  
7. Mentse a munkáját.   
   ![Képernyőfelvétel a mentés ikon](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)  

Ezen a ponton befejezte a fájlszintű kódolási connector beállítása. Egy valós alkalmazás érdemes lehet egy üzleti alkalmazásban, például a Salesforce a kódolt adatok tárolásához. Vagy, hogy a kereskedelmi kódolt adatok partneri küldhet. Egyszerűen hozzáadhatja küldjön a kódolási művelet eredményének Salesforce, illetve a kereskedelmi partner közül legalább egy, a többi összekötőt, a megadott műveletet.

Most tesztelheti az összekötő egy kérést a HTTP-végpont, és többek között az XML-tartalom a kérés törzsében.  

## <a name="create-the-flat-file-decoding-connector"></a>Az összekötő dekódolás egybesimított fájl létrehozása

> [!NOTE]
> A lépések elvégzésével szüksége egy séma-fájl már feltöltött az integráció fiókjába.

1. Adja hozzá a **kérelem - amikor egy HTTP-kérelem érkezik** eseményindító a logikai alkalmazáshoz.  
   ![Képernyőkép a eseményindító kiválasztásához](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)    
2. A művelet, az alábbiak szerint dekódolás egybesimított fájl hozzáadása:
   
    a. Válassza ki a **plus** bejelentkezési.
   
    b. Válassza ki a **művelet hozzáadása** (jelenik meg, miután kiválasztotta a plusz jelre) hivatkozásra.
   
    c. A keresési mezőbe, írja be a *egyszerű* szűrése, amely a használni kívánt összes műveletet.
   
    d. Válassza ki a **Egybesimított fájl dekódolás** elemet a listában.   
   ![Képernyőfelvétel a Egybesimított fájl dekódolás beállítás](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   
3. Válassza ki a **tartalom** vezérlő. Ezzel létrehozza a korábbi lépéseket, melyekkel a tartalmat dekódolás tartalom listáját. Figyelje meg, hogy a *törzs* a bejövő HTTP kérelem érhető el a tartalom dekódolás használható. Is megadhat a tartalmat közvetlenül a dekódolni a **tartalom** vezérlő.     
4. Válassza ki a *törzs* címke. Figyelje meg a szervezet címke van a **tartalom** vezérlő.
5. Válassza ki a sémát, amely a tartalom dekódolás használni kívánt nevét. Az alábbi képernyőfelvételen látható, amely *OrderFile* kijelölt séma neve. A séma neve kellett feltöltve az integráció figyelembe korábban.
   
   ![Képernyőfelvétel a Egybesimított fájl dekódolás párbeszédpanel](media/logic-apps-enterprise-integration-flatfile/flatfile-decode-1.png)    
6. Mentse a munkáját.  
   ![Képernyőfelvétel a mentés ikon](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)    

Ezen a ponton befejezte a egybesimított fájl dekódolás összekötő beállítása. Egy valós alkalmazás érdemes lehet például a Salesforce-üzletági alkalmazásokban a dekódolt adatainak tárolására. Egyszerűen hozzáadhatja a dekódolási művelet kimenetének küldése a Salesforce műveletet.

Az összekötő által a kérést továbbítja a HTTP-végpont az és a kérelem törzsében dekódolni kívánt XML-tartalom, így most tesztelheti.  

## <a name="next-steps"></a>További lépések
* [További tudnivalók a vállalati integrációs csomag](logic-apps-enterprise-integration-overview.md "további információ a vállalati integrációs csomag").  

