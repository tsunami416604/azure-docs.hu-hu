---
title: Kódolás, vagy a dekódolandó egybesimított fájlok – Azure Logic Apps |} A Microsoft Docs
description: Kódolás, vagy enterprise integration és Azure Logic Apps és az Enterprise Integration Pack egybesimított fájl dekódolása
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 82152dab-c7ad-43df-b721-596559703be8
ms.date: 07/08/2016
ms.openlocfilehash: d0ef61b94d7bd604b6c0062341224510f3048c57
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43123928"
---
# <a name="encode-or-decode-flat-files-with-azure-logic-apps-and-enterprise-integration-pack"></a>Kódolás, vagy az Azure Logic Apps és az Enterprise Integration Pack egybesimított fájl dekódolása

Kódolandó XML-tartalom egy üzleti partnertől-vállalatközi (B2B) forgatókönyvekben való elküldés előtt érdemes. Ehhez a logikai alkalmazás, használhatja az egybesimított fájl kódolása összekötő. A logikai alkalmazás által létrehozott tudnak tartalmat beolvasni a hozzá tartozó XML számos különféle forrásból, például egy HTTP-kérés eseményindító, más alkalmazásból származó, vagy akár egy a többhöz [összekötők](../connectors/apis-list.md). Logic apps szolgáltatással kapcsolatos további információkért lásd: a [logic apps-dokumentáció](logic-apps-overview.md "további tudnivalók a Logic apps").  

## <a name="create-the-flat-file-encoding-connector"></a>Az egybesimított fájl kódolása összekötő létrehozása
Kövesse az alábbi lépéseket egy egybesimított fájl kódolása a logikai alkalmazás-összekötő hozzáadásához.

1. Hozzon létre egy logikai alkalmazást, és [hivatkozás az integrációs fiókba](logic-apps-enterprise-integration-accounts.md "Ismerkedjen meg az integrációs fiók összekapcsolása egy logikai alkalmazást"). Ennek a fióknak a használatával az XML-adatok kódolása sémát tartalmaz.  
1. Adjon hozzá egy **kérelem – Ha egy HTTP-kérelem érkezett** eseményindítót a logikai alkalmazáshoz.  
   ![Képernyőkép – trigger kiválasztása](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)    
1. Adja hozzá a következő kódolási művelet, egybesimított fájl:
   
    a. Válassza ki a **plusz** bejelentkezést.
   
    b. Válassza ki a **művelet hozzáadása** (jelenik meg, miután kiválasztotta a plusz jelre) hivatkozásra.
   
    c. A Keresés mezőbe írja be a *Egybesimított* szűrése, amely a használni kívánt összes műveletet.
   
    d. Válassza ki a **Egybesimított fájl kódolása** lehetőséget a listából.   
   ![Képernyőkép az Egybesimított fájl kódolása lehetőség](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   
1. Az a **Egybesimított fájl kódolása** párbeszédpanelen válassza ki a **tartalom** szövegmezőben.  
   ![Képernyőfelvétel: a tartalom szövegmező](media/logic-apps-enterprise-integration-flatfile/flatfile-3.png)  
1. Válassza ki a kódolni kívánt tartalom a törzscímkét. A törzscímkét fel a tartalom mező.     
   ![Képernyőkép a törzscímkét](media/logic-apps-enterprise-integration-flatfile/flatfile-4.png)  
1. Válassza ki a **sémanév** listán, és válassza ki a sémát, a bemeneti tartalom kódolása a használni kívánt.    
   ![A sémanév képernyőkép listamező](media/logic-apps-enterprise-integration-flatfile/flatfile-5.png)  
1. Mentse a munkáját.   
   ![Képernyőkép a mentés ikon](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)  

Ezen a ponton végzett az egybesimított fájl kódolási connector beállítása. Egy valós alkalmazás esetében érdemes a kódolt adatok tárolása egy sor üzleti alkalmazás, például a Salesforce-hoz. Vagy, hogy a kódolt adatok egy kereskedelmi partneri küldhet. Egyszerűen hozzáadhat egy olyan műveletet, a kódolási művelet kimenetét, vagy a Salesforce-hoz a kereskedelmi partner által biztosított az egyéb összekötőkhöz valamelyikét használja.

Az összekötő most tesztelheti a kérés végpontjához, és többek között az XML-tartalom a kérelem törzsében.  

## <a name="create-the-flat-file-decoding-connector"></a>Az egybesimított fájl dekódolása összekötő létrehozása

> [!NOTE]
> A lépések elvégzéséhez szüksége már fel van töltve az integrációs fiók sémájának fájlt.

1. Adjon hozzá egy **kérelem – Ha egy HTTP-kérelem érkezett** eseményindítót a logikai alkalmazáshoz.  
   ![Képernyőkép – trigger kiválasztása](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)    
1. Adja hozzá a következő dekódolás művelet, egybesimított fájl:
   
    a. Válassza ki a **plusz** bejelentkezést.
   
    b. Válassza ki a **művelet hozzáadása** (jelenik meg, miután kiválasztotta a plusz jelre) hivatkozásra.
   
    c. A Keresés mezőbe írja be a *Egybesimított* szűrése, amely a használni kívánt összes műveletet.
   
    d. Válassza ki a **Egybesimított fájl dekódolása** lehetőséget a listából.   
   ![Képernyőkép az Egybesimított fájl dekódolása lehetőség](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   
1. Válassza ki a **tartalom** vezérlő. Ez a korábbi lépésekben való dekódolandó a tartalom lehet használni a tartalom listáját hoz létre. Figyelje meg, hogy a *törzs* a bejövő HTTP kérés számára érhető el a tartalom dekódolására használhatók. A tartalom való dekódolandó közvetlenül is megadhat a **tartalom** vezérlő.     
1. Válassza ki a *törzs* címke. A törzscímkét már az értesítés a **tartalom** vezérlő.
1. Válassza ki azt a sémát, a tartalom dekódolandó használni kívánt nevét. Az alábbi képernyőképen az látható, hogy *OrderFile* kijelölt séma neve. A sémanév volna fel lett töltve az integrációs fiókba korábban.
   
   ![Képernyőkép az Egybesimított fájl dekódolása párbeszédpanel](media/logic-apps-enterprise-integration-flatfile/flatfile-decode-1.png)    
1. Mentse a munkáját.  
   ![Képernyőkép a mentés ikon](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)    

Ezen a ponton végzett az egybesimított fájl dekódolása connector beállítása. Egy valós alkalmazás esetében érdemes a dekódolt adatok tárolása egy üzleti alkalmazást például a Salesforce-ban. Egyszerűen hozzáadhat egy olyan műveletet, a Salesforce-hoz a dekódolási művelet kimenetét.

Most tesztelheti az összekötő egy kérés a HTTP-végpontot, és többek között a kérelem törzsében dekódolni kívánt XML-tartalom.  

## <a name="next-steps"></a>További lépések
* [További információ az Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "megismerheti a vállalati integrációs csomag").  

