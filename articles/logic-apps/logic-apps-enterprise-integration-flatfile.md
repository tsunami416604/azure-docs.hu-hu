---
title: Lapos fájlok kódolása vagy dekódolása
description: A nagyvállalati integráció Azure Logic Apps és Enterprise Integration Packsal
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 07/08/2016
ms.openlocfilehash: 2d6182ba01507c2fb361628e01bb52e1ea821f44
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77152652"
---
# <a name="encode-or-decode-flat-files-with-azure-logic-apps-and-enterprise-integration-pack"></a>Lapos fájlok kódolása vagy dekódolása Azure Logic Apps és Enterprise Integration Pack

Előfordulhat, hogy az XML-tartalom kódolása szükséges ahhoz, hogy üzleti partnernek küldje a vállalatközi (B2B) forgatókönyvben. A logikai alkalmazásokban ezt elvégezheti a sima file Encoding-összekötővel. A létrehozott logikai alkalmazás különböző forrásokból, például egy HTTP-kérelem eseményindítóból, egy másik alkalmazásból, vagy akár a sok [összekötőből](../connectors/apis-list.md)származó XML-tartalmakat is elérheti. A Logic apps szolgáltatással kapcsolatos további információkért tekintse meg a [Logic apps dokumentációját](logic-apps-overview.md "További információ a Logic apps szolgáltatásról").  

## <a name="create-the-flat-file-encoding-connector"></a>Az egyszerű fájl kódolási összekötő létrehozása
A következő lépésekkel adhat hozzá egy sima file Encoding-összekötőt a logikai alkalmazáshoz.

1. Hozzon létre egy logikai alkalmazást, és [kapcsolja össze az integrációs fiókjával](logic-apps-enterprise-integration-accounts.md "Integrációs fiók összekapcsolása logikai alkalmazással"). Ez a fiók tartalmazza azt a sémát, amelyet az XML-adatfájlok kódolásához fog használni.  

1. A Logic app Designerben adja hozzá a **http-kérelem elfogadásának időpontját** a logikai alkalmazáshoz.

1. Adja hozzá a sima fájl kódolási műveletét az alábbiak szerint:

   a. Válassza ki a **plusz** jelet.

   b. Válassza a **művelet hozzáadása** hivatkozást (ekkor megjelenik a plusz jel kiválasztása után).

   c. A keresőmezőbe írja be a *Flat* értéket a használni kívánt összes művelet szűréséhez.

   d. Válassza ki a **lapos fájl kódolása** lehetőséget a listából.   

      ![Képernyőfelvétel a lapos file Encoding lehetőségről](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   

1. A **sima fájl kódolása** párbeszédpanelen jelölje ki a **tartalom** szövegmezőt.  

   ![Képernyőfelvétel a tartalom szövegmezőről](media/logic-apps-enterprise-integration-flatfile/flatfile-3.png)  

1. Válassza ki a törzs címkét a kódolni kívánt tartalomhoz. A törzs címkéje feltölti a tartalom mezőt.     

   ![Képernyőfelvétel a Body címkéről](media/logic-apps-enterprise-integration-flatfile/flatfile-4.png)  

1. Válassza ki a **séma neve** listát, és válassza ki azt a sémát, amelyet a bemeneti tartalom kódolásához használni kíván.    

   ![A séma neve lista képernyőképe](media/logic-apps-enterprise-integration-flatfile/flatfile-5.png)  

1. Mentse a munkáját.

   ![Képernyőkép a Mentés ikonról](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)  

Ezen a ponton befejezte a sima file Encoding-összekötő beállítását. Egy valós alkalmazásban érdemes lehet a kódolt adatmennyiséget egy üzletági alkalmazásban (például Salesforce) tárolni. Vagy elküldheti a kódolt adategységeket egy kereskedelmi partnernek. Egyszerűen hozzáadhat egy műveletet, amely a kódolási művelet kimenetét Salesforce vagy a kereskedelmi partnernek küldi el a többi elérhető összekötő használatával.

Most tesztelheti az összekötőt úgy, hogy egy kérést küld a HTTP-végpontnak, és tartalmazza a kérelem törzsében lévő XML-tartalmat is.  

## <a name="create-the-flat-file-decoding-connector"></a>Az egyszerű fájl dekódolása összekötő létrehozása

> [!NOTE]
> A lépések végrehajtásához rendelkeznie kell egy, az integrációs fiókba már feltöltött sémafájl-fájllal.

1. A Logic app Designerben adja hozzá a **http-kérelem elfogadásának időpontját** a logikai alkalmazáshoz.

1. Adja hozzá a lapos fájl dekódolása műveletet a következőképpen:

   a. Válassza ki a **plusz** jelet.

   b. Válassza a **művelet hozzáadása** hivatkozást (ekkor megjelenik a plusz jel kiválasztása után).

   c. A keresőmezőbe írja be a *Flat* értéket a használni kívánt összes művelet szűréséhez.

   d. Válassza ki a **lapos fájl dekódolása** lehetőséget a listából.   

      ![Képernyőfelvétel a lapos fájl dekódolási lehetőségéről](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   

1. Válassza ki a **tartalom** vezérlőelemet. Ez megjeleníti a korábbi lépésekből származó tartalom listáját, amelyet a dekódolható tartalomként használhat. Figyelje meg, hogy a bejövő HTTP-kérés *törzse* elérhető a dekódolni kívánt tartalomként való használathoz. Azt is megadhatja, hogy a tartalom közvetlenül a **Content** Control vezérlőelembe legyen dekódolva.     

1. Válassza ki a *törzs* címkét. Figyelje meg, hogy a Body címke most már szerepel a **Content** vezérlőelemben.

1. Válassza ki annak a sémának a nevét, amelyet a tartalom dekódolásához használni kíván. Az alábbi képernyőfelvételen látható, hogy a *OrderFile* a kiválasztott séma neve. A séma neve korábban már fel lett töltve az integrációs fiókba.

   ![A lapos fájl dekódolása párbeszédpanel képernyőképe](media/logic-apps-enterprise-integration-flatfile/flatfile-decode-1.png) 

1. Mentse a munkáját.  

   ![Képernyőkép a Mentés ikonról](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)    

Ekkor elkészült a sima fájl dekódolási összekötő beállításával. Egy valós alkalmazásban érdemes lehet a dekódolású adatmennyiséget egy üzletági alkalmazásban (például Salesforce) tárolni. Egyszerűen hozzáadhat egy műveletet a dekódolási művelet kimenetének Salesforce való elküldéséhez.

Most tesztelheti az összekötőt úgy, hogy egy kérést küld a HTTP-végpontnak, és a kérelem törzsében dekódolni kívánt XML-tartalmat is tartalmazza.  

## <a name="next-steps"></a>További lépések
* [További információ a Enterprise Integration packról](logic-apps-enterprise-integration-overview.md "Tudnivalók a Enterprise Integration Pack").  

