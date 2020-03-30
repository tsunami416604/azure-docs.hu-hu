---
title: Sima fájlok kódolása vagy dekódolása
description: Egydakú fájlok kódolása vagy dekódolása az Azure Logic Apps és enterprise integration pack vállalati integrációhoz
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 07/08/2016
ms.openlocfilehash: 2d6182ba01507c2fb361628e01bb52e1ea821f44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152652"
---
# <a name="encode-or-decode-flat-files-with-azure-logic-apps-and-enterprise-integration-pack"></a>Összetett fájlok kódolása vagy dekódolása az Azure Logic Alkalmazásokkal és a Vállalati integrációs csomaggal

Előfordulhat, hogy az XML-tartalmat kódolni szeretné, mielőtt üzleti partnernek küldi el egy vállalaton (B2B) forgatókönyvben. Egy logikai alkalmazásban használhatja a sima fájlkódolási összekötőt ehhez. A létrehozott logikai alkalmazás az XML-tartalmat különböző forrásokból szerezheti be, például egy HTTP-kérelem eseményindítójából, egy másik alkalmazásból vagy akár a számos [összekötő](../connectors/apis-list.md)egyikéből. A logikai alkalmazásokról a [logikai alkalmazások dokumentációjában](logic-apps-overview.md "További információ a logikai alkalmazásokról")olvashat bővebben.  

## <a name="create-the-flat-file-encoding-connector"></a>A síkfájl-kódolási összekötő létrehozása
Az alábbi lépésekkel hozzáadhat egy egylapos fájlkódolási összekötőt a logikai alkalmazáshoz.

1. Hozzon létre egy logikai alkalmazást, és [kapcsolja össze az integrációs fiókkal.](logic-apps-enterprise-integration-accounts.md "Ismerje meg, hogyan lehet integrációs fiókot összekapcsolni egy logikai alkalmazással") Ez a fiók az XML-adatok kódolásához használt sémát tartalmazza.  

1. A Logic App Designer, adja hozzá a **HTTP-kérelem fogadása** esetén eseményindító a logikai alkalmazáshoz.

1. Adja hozzá a sima fájlkódolási műveletet az alábbiak szerint:

   a. Válassza ki a **pluszjelet.**

   b. Jelölje ki a **Művelet hozzáadása** hivatkozást (a pluszjel kiválasztása után jelenik meg).

   c. A keresőmezőbe írja be a *Flat* értéket, ha az összes műveletet a használni kívánt műveletre szeretné szűrni.

   d. Válassza a **lista egylapos kódolási** beállítását.   

      ![Képernyőkép: Egylapos fájlkódolási beállítás](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   

1. A **Síkfájlkódolás** párbeszédpanelen jelölje be a **Tartalom** szövegmezőt.  

   ![Képernyőkép a Tartalom szövegdobozról](media/logic-apps-enterprise-integration-flatfile/flatfile-3.png)  

1. Jelölje ki a törzscímkét a kódolni kívánt tartalomként. A törzscímke feltölti a tartalommezőt.     

   ![Képernyőkép a törzscímkéről](media/logic-apps-enterprise-integration-flatfile/flatfile-4.png)  

1. Jelölje be a **Sémanév** listát, és válassza ki a bemeneti tartalom kódolásához használni kívánt sémát.    

   ![Képernyőkép a Sémanév listáról](media/logic-apps-enterprise-integration-flatfile/flatfile-5.png)  

1. Mentse a munkáját.

   ![Képernyőkép: Mentés ikon](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)  

Ezen a ponton befejezte a sima fájlkódolási összekötő beállítását. Egy valós alkalmazásokban előfordulhat, hogy a kódolt adatokat egy üzletági alkalmazásban, például a Salesforce-ban szeretné tárolni. Vagy elküldheti a kódolt adatokat egy kereskedelmi partnernek. Könnyedén hozzáadhat egy műveletet, amelya kódolási művelet kimenetét elküldheti a Salesforce-nak vagy a kereskedelmi partnerének a rendelkezésre álló bármely más összekötő bármelyikének használatával.

Most már tesztelheti az összekötőt a HTTP-végpontra vonatkozó kéréssel, és az XML-tartalom nak a kérelem törzsébe való belefoglalásával.  

## <a name="create-the-flat-file-decoding-connector"></a>A sima fájldekódolási összekötő létrehozása

> [!NOTE]
> A lépések végrehajtásához már fel kell töltenie egy sémafájlt az integrációs fiókba.

1. A Logic App Designer, adja hozzá a **HTTP-kérelem fogadása** esetén eseményindító a logikai alkalmazáshoz.

1. Adja hozzá a sima fájl dekódolási műveletet az alábbiak szerint:

   a. Válassza ki a **pluszjelet.**

   b. Jelölje ki a **Művelet hozzáadása** hivatkozást (a pluszjel kiválasztása után jelenik meg).

   c. A keresőmezőbe írja be a *Flat* értéket, ha az összes műveletet a használni kívánt műveletre szeretné szűrni.

   d. Válassza az **egyfájlos dekódolást** a listából.   

      ![Képernyőkép: egylapos dekódolási beállítás](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   

1. Válassza a **Tartalom** vezérlőt. Ez létrehoz egy listát a korábbi lépésekből származó tartalomról, amelyet tartalomként használhat a dekódoláshoz. Figyelje meg, hogy a *szervezet* a bejövő HTTP-kérelem érhető el, hogy a tartalom dekódolni. A tartalmat közvetlenül a Tartalom vezérlőbe is **megadhatja.**     

1. Jelölje *Body* ki a Törzscímkét. Figyelje meg, hogy a törzscímke most már a **Tartalom** vezérlőben van.

1. Válassza ki a tartalom dekódolásához használni kívánt séma nevét. A következő képernyőképen látható, hogy az *OrderFile* a kijelölt sémanév. Ezt a sémanevet korábban már feltöltötték az integrációs fiókba.

   ![Képernyőkép: egylapos dekódolás párbeszédpanel](media/logic-apps-enterprise-integration-flatfile/flatfile-decode-1.png) 

1. Mentse a munkáját.  

   ![Képernyőkép: Mentés ikon](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)    

Ezen a ponton befejezte a sima fájldekódolási összekötő beállítását. Egy valós alkalmazásokban előfordulhat, hogy a dekódolt adatokat egy üzletági alkalmazásban, például a Salesforce-ban szeretné tárolni. A dekódolási művelet kimenetének a Salesforce-nak való elküldéséhez egyszerűen hozzáadhat egy műveletet.

Most már tesztelheti az összekötőt a HTTP-végpontra vonatkozó kéréssel, és a kérelem törzsében a dekódolni kívánt XML-tartalommal.  

## <a name="next-steps"></a>További lépések
* [További információ az Enterprise Integration Pack csomagról.](logic-apps-enterprise-integration-overview.md "További információ a vállalati integrációs csomagról")  

