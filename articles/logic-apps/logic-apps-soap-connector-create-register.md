---
title: "Létrehozni és regisztrálni az SOAP összekötők - Azure Logic Apps |} Microsoft Docs"
description: "SOAP-összekötők beállítása az Azure Logic Appsben való használatra"
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
ms.date: 10/24/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 031762e5639fc52e0b0a6a5bf8d12db25da25e12
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2018
---
# <a name="create-and-register-soap-connectors-in-azure-logic-apps"></a>Létrehozni és regisztrálni az Azure Logic Apps a SOAP-összekötők

A SOAP-szolgáltatások a logikai alkalmazások munkafolyamataiba való integrálásához létrehozhat és regisztrálhat egy egyéni SOAP-összekötőt a SOAP-szolgáltatás a webszolgáltatások leíró nyelvén (WSDL) való leírásával. A SOAP-összekötők előre létrehozott összekötőként működnek, így az egyéb összekötőkhöz hasonlóan használhatók a logikai alkalmazásokban.


## <a name="prerequisites"></a>Előfeltételek

A SOAP-összekötők regisztrálásához a következőkre van szükség:

* Azure-előfizetés. Ha nem rendelkezik előfizetéssel, kezdhet egy [ingyenes Azure-fiókkal](https://azure.microsoft.com/free/). Egyéb esetben regisztráljon egy [használatalapú fizetéses előfizetésre](https://azure.microsoft.com/pricing/purchase-options/).

* Bármelyik a következők közül:
  * Egy URL-cím, amely a SOAP-szolgáltatást és az API-kat definiáló WSDL-leírásra mutat
  * Egy, a SOAP-szolgáltatást és az API-kat definiáló WSDL-fájl

  Ebben az oktatóanyagban használhatja a [Megrendelések SOAP-szolgáltatás](http://fazioapisoap.azurewebsites.net/FazioService.svc?singleWsdl) példafájlt.

* Nem kötelező: Egy, az egyéni összekötőhöz ikonként használható kép


## <a name="1-create-your-connector"></a>1. Saját összekötő létrehozása

1. Az Azure Portalon válassza az **Új** lehetőséget a főmenüben. Írja be a „logic apps összekötő” kifejezést szűrőként a keresőmezőbe, majd nyomja le az ENTER billentyűt.

   ![A „logic apps összekötő” kifejezés keresése az Új menüpont alatt](./media/logic-apps-soap-connector-create-register/create-logic-apps-connector.png)

2. Az eredmények listájában válassza a **Logic Apps-összekötő** > **Létrehozás** lehetőséget.

   ![Logic Apps-összekötő létrehozása](./media/logic-apps-soap-connector-create-register/choose-logic-apps-connector.png)

3. Adja meg az összekötő regisztrálásához szükséges adatokat a táblázatban leírtak szerint. Ha elkészült, válassza a **Rögzítés az irányítópulton** > **Létrehozás** lehetőséget.

   ![Egyéni Logic Apps-összekötő adatai](./media/logic-apps-soap-connector-create-register/logic-apps-soap-connector-details.png)

   | Tulajdonság | Ajánlott érték | Leírás | 
   | -------- | --------------- | ----------- | 
   | **Name (Név)** | *soap-összekötő-neve* | Adja meg az összekötő nevét. | 
   | **Előfizetés** | *Azure-előfizetés-neve* | Válassza ki az Azure-előfizetését. | 
   | **Erőforráscsoport** | *Azure-erőforráscsoport-neve* | Hozzon létre vagy válasszon ki egy Azure-erőforráscsoportot az Azure-erőforrások rendszerezéséhez. | 
   | **Hely** | *üzembehelyezési-régió* | Válassza ki a régiót az összekötő üzembe helyezéséhez. | 
   |||| 

   Miután az Azure üzembe helyezte az összekötőt, automatikusan megnyílik a Logic Apps-összekötő menüje. 
   Ha mégsem nyílna meg, válassza ki a SOAP-összekötőt az Azure irányítópultján.

## <a name="2-define-your-connector"></a>2. Összekötő definiálása

Most adja meg a WSDL-fájlt vagy URL-címet az összekötő létrehozásához, az összekötő által használt hitelesítést, valamint a SOAP-összekötő által biztosított műveleteket és eseményindítókat.


### <a name="2a-specify-the-wsdl-file-or-url-for-your-connector"></a>2a. Az összekötő WSDL-fájljának vagy URL-címének megadása

1. Ha még nem lenne kiválasztva, az összekötő menüjében válassza a **Logic Apps-összekötő** lehetőséget. Az eszköztáron válassza a **Szerkesztés** elemet.

   ![Egyéni összekötő szerkesztése](./media/logic-apps-soap-connector-create-register/edit-soap-connector.png)

2. Az **Általános** lehetőséget választva a táblázatokban megadott adatok segítségével hozhatja létre és biztosíthatja a SOAP-összekötőt, valamint definiálhatja annak műveleteit és eseményindítóit.

   1. Az **Egyéni összekötők** területen válassza a **SOAP** lehetőséget **API-végpontként**, így a WSDL-fájl megadásával definiálhatja az API-t.

      ![Az API WSDL-fájljának megadása](./media/logic-apps-soap-connector-create-register/provide-wsdl-file.png)

      | Beállítás | Formátum |Leírás | 
      | ------ | ------ | ----------- | 
      | **WSDL feltöltése fájlból** | *WSDL-fájl* | Keresse meg a WSDL-fájl helyét, és válassza ki a fájlt. | 
      | **WSDL feltöltése URL-címből** | http://*wsdl-fájl-elérési-útja* | Adja meg a szolgáltatás WSDL-fájljának URL-címét. | 
      | **SOAP–REST** |   | A SOAP-szolgáltatás API-jai átalakíthatók REST API-kká. | 
      |||| 

   2. Az **Általános információk** területen töltsön fel egy ikont az összekötőhöz. 
   A **Leírás**, a **Gazdagép** és a **Kiindulási URL-cím** mezőket a rendszer általában automatikusan kitölti a WSDL-fájlból. 
   Ha mégsem lennének kitöltve, adja meg az adatokat a táblázat szerint, majd kattintson a **Folytatás** gombra. 

      ![Összekötő adatai](./media/logic-apps-soap-connector-create-register/add-general-details.png)

      | Beállítás | Formátum | Leírás | 
      | ----------------- | ------ | ----------- | 
      | **Ikon feltöltése** | *png-vagy-jpg-fájl-1-MB-alatt* | Az összekötőt jelölő ikon <p>Szín: Ideális esetben egy fehér embléma színes háttér előtt. <p>Méretek: Egy ~160 pixeles embléma egy 230 pixeles négyzetben. | 
      | **Ikon háttérszíne** | *ikon-arculati-színének-hexadecimális-kódja* | <p>Az ikon mögötti szín, amely egyezik az ikonfájl háttérszínével. <p>Formátum: hexadecimális. A #007ee5 kód például a kék színt jelenti. | 
      | **Leírás** | *összekötő-leírása* | Adja meg az összekötő rövid leírását. | 
      | **Gazdagép** | *összekötő-gazdagépe* | Adja meg a SOAP-szolgáltatás gazdagép-tartományát. | 
      | **Kiindulási URL-cím** | *összekötő-kiindulási-URL-címe* | Adja meg a SOAP-szolgáltatás kiindulási URL-címét. | 
      |||| 

### <a name="2b-describe-the-authentication-that-your-connector-uses"></a>2b. Az összekötő által használt hitelesítés leírása

1. Most válassza a **Biztonság** lehetőséget, hogy áttekinthesse vagy megadhassa az összekötő által használt hitelesítési módot. A hitelesítés segítségével biztosíthatja, hogy a rendszer zökkenőmentesen kommunikálja a felhasználók azonosságait a szolgáltatás és az ügyfelek közt.

   Alapértelmezés szerint az összekötő **Hitelesítési típus** beállításának értéke **Nincs hitelesítés**.
   
   ![Hitelesítés típusa](./media/logic-apps-soap-connector-create-register/security-authentication-options.png)

   A hitelesítési típus módosításához válassza a **Szerkesztés** lehetőséget. Választhatja az **Alapszintű hitelesítés** beállítást. Az alapértelmezett értékektől eltérő paramétercímkék használatához frissítse azokat a **Paramétercímke** területen.

   ![Az egyszerű hitelesítés](./media/logic-apps-soap-connector-create-register/security.png)

   
2. Ha szeretné menteni az összekötőt a biztonsági adatok megadása után, válassza a lap tetején lévő **Összekötő frissítése**, majd a **Folytatás** lehetőséget. 

### <a name="2c-review-update-or-define-actions-and-triggers-for-your-connector"></a>2c. Összekötő műveleteinek és eseményindítóinak áttekintése, frissítése és definiálása

1. A **Definíció** elemre kattintva áttekintheti, szerkesztheti és definiálhatja a műveleteket és eseményindítókat, amelyeket a felhasználók hozzáadhatnak munkafolyamataikhoz.

   A műveletek és eseményindítók a WSDL-fájlban definiált műveleteken alapulnak, amely automatikusan feltölti a **Definíció** lapot, valamint a kérés és a válasz értékeit is megadja. Így, ha a szükséges műveletek már megjelennek itt, az oldal módosítása nélkül léphet tovább a regisztrációs folyamat következő lépésére.

   ![Összekötő definíciója](./media/logic-apps-soap-connector-create-register/definition.png)

2. Ha szükséges, ha azt szeretné, meglévő műveletek és eseményindítók szerkesztéséhez vagy újak, hozzáadásával [folytassa a következő lépéseket](logic-apps-custom-connector-register.md#add-action-or-trigger).


## <a name="3-finish-creating-your-connector"></a>3. Az összekötő létrehozásának befejezése

Ha elkészült, válassza az **Összekötő frissítése** lehetőséget az összekötő üzembe helyezéséhez. 

Gratulálunk! Amikor létrehoz egy logikai alkalmazást, mostantól megtalálja az összekötőt a Logic Apps Designerben, és hozzáadhatja a logikai alkalmazáshoz.

![Összekötő keresése a Logic Apps Designerben](./media/logic-apps-soap-connector-create-register/soap-connector-created.png)

## <a name="share-your-connector-with-other-logic-apps-users"></a>Összekötő megosztása más Logic Apps-felhasználókkal

A regisztrált, de nem tanúsított egyéni összekötők a Microsoft által felügyelt összekötőként működnek, azonban *kizárólag* a szerző és az olyan felhasználók számára láthatók és elérhetők, akik ugyanazzal az Azure Active Directory-bérlővel és Azure-előfizetéssel rendelkeznek a logikai alkalmazásokhoz abban a régióban, ahol az alkalmazások üzembe vannak helyezve. Bár a megosztás nem kötelező, valószínűleg adódnak olyan alkalmazási helyzetek, amikor meg kívánja osztani az összekötőit más felhasználókkal. 

> [!IMPORTANT]
> Miután megoszt egy összekötőt, lehet, hogy mások egy idő után számítani fognak rá. 
> ***Az összekötő törlésével az összekötővel való összes kapcsolatot is törli.***
 
Az összekötő kívül a határok, például külső felhasználókkal való megosztása senki Logic Apps [küldje el a Microsoft-tanúsítvány-összekötő](../logic-apps/custom-connector-submit-certification.md).

## <a name="faq"></a>GYIK

**K:** A SOAP-összekötő általánosan elérhető? </br>
**V:** A SOAP-összekötő **előzetes verziójú** szolgáltatás, így általánosan még nem érhető el.

**K:** Rendelkezik a SOAP-összekötő valamilyen korlátozással vagy ismert hibákkal? </br>
**V:** Igen, lásd: [A SOAP összekötők korlátozásai és ismert hibái](../api-management/api-management-api-import-restrictions.md#wsdl).

**K:** Rendelkeznek az egyéni összekötők valamilyen korlátozással? </br>
**A:** Igen. [Az egyéni összekötők korlátait itt tekintheti meg](../logic-apps/logic-apps-limits-and-config.md#custom-connector-limits).

## <a name="get-support"></a>Támogatás kérése

* A fejlesztéssel és a bevezetéssel kapcsolatos támogatásért, illetve a regisztrációs varázslóban nem elérhető funkciók igénylésével kapcsolatban írjon a következő címre: [condevhelp@microsoft.com](mailto:condevhelp@microsoft.com). A Microsoft figyelemmel kíséri az erre a címre küldött, fejlesztéssel kapcsolatos kérdéseket és problémákat, és továbbítja azokat az illetékes csapatoknak.

* Látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps), ahol kérdéseket tehet fel és válaszolhat meg, valamint megtudhatja, mivel foglalkoznak az Azure Logic Apps más felhasználói.

* Ha szeretne segíteni a Logic Apps fejlesztésében, szavazzon vagy küldje el javaslatait a [Logic Apps felhasználói visszajelzések oldalon](http://aka.ms/logicapps-wish). 

## <a name="next-steps"></a>További lépések

* [Választható lehetőség: Az összekötő tanúsítás](../logic-apps/custom-connector-submit-certification.md)
* [Egyéni összekötő – gyakori kérdések](../logic-apps/custom-connector-faq.md)