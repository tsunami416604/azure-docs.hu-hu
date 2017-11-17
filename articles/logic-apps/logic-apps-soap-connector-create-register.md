---
title: "Létrehozni és regisztrálni az SOAP összekötők - Azure Logic Apps |} Microsoft Docs"
description: "Az Azure Logic Apps használatra SOAP-összekötők beállítása"
author: divyaswarnkar
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
ms.author: LADocs; divswa
ms.openlocfilehash: 0323b0f7ee03dce209d5a71c6711988a34ba7633
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2017
---
# <a name="create-and-register-soap-connectors-in-azure-logic-apps"></a>Létrehozni és regisztrálni az Azure Logic Apps a SOAP-összekötők

A logic app munkafolyamatok integrálását a SOAP-szolgáltatások, hozhat létre és regisztrálja egy egyéni egyszerű SOAP Object Access Protocol ()-összekötő Web Services Description Language (WSDL), amely leírja a SOAP-szolgáltatás használatával. A SOAP-összekötők úgy működik, mint előre elkészített összekötők, hogy használhassa őket a ugyanúgy, mint a többi összekötőt a logic Apps alkalmazásait.


## <a name="prerequisites"></a>Előfeltételek

A SOAP-összekötő regisztrálásához kell ezeket az elemeket:

* Azure-előfizetés. Ha nem rendelkezik előfizetéssel, Kezdésként használhatja az egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/). Ellenkező esetben regisztráljon egy [használatalapú fizetés előfizetés](https://azure.microsoft.com/pricing/purchase-options/).

* Bármely elem itt:
  * A WSDL, amely meghatározza a SOAP-szolgáltatás és az API-k URL-CÍMÉT
  * A WSDL-fájl, amely meghatározza a SOAP-szolgáltatás és az API-k

  Ebben az oktatóanyagban használhatja a fenti példában [rendelések SOAP szolgáltatás](http://fazioapisoap.azurewebsites.net/FazioService.svc?singleWsdl).

* Választható lehetőség: Egy kép ikonként az egyéni összekötő használatára


## <a name="1-create-your-connector"></a>1. Az összekötő létrehozása

1. Az Azure portálon, a fő Azure menüben válasszon **új**. A keresési mezőbe írja be a "logic apps-összekötők" szűrőként, és nyomja le az ENTER billentyűt.

   ![Új keressen a "logic apps-összekötők"](./media/logic-apps-soap-connector-create-register/create-logic-apps-connector.png)

2. Az eredmények listájában válassza ki a **Logic Apps-összekötők** > **létrehozása**.

   ![A Logic Apps-összekötő létrehozása](./media/logic-apps-soap-connector-create-register/choose-logic-apps-connector.png)

3. Adja meg a részleteket az az összekötő regisztrálása a táblázatban ismertetett módon. Amikor elkészült, válassza ki a **rögzítés az irányítópulton** > **létrehozása**.

   ![Logic App egyéni connector részletei](./media/logic-apps-soap-connector-create-register/logic-apps-soap-connector-details.png)

   | Tulajdonság | Ajánlott érték | Leírás | 
   | -------- | --------------- | ----------- | 
   | **Name (Név)** | *SOAP-összekötő-neve* | Adja meg az összekötő nevét. | 
   | **Előfizetés** | *Azure-előfizetés-neve* | Válassza ki az Azure-előfizetéshez. | 
   | **Erőforráscsoport** | *Azure-erőforrás-csoportnév* | Hozzon létre vagy válassza ki az Azure-csoportok a az Azure-erőforrások rendszerezéséhez. | 
   | **Hely** | *központi telepítés-terület* | Az összekötő telepítési régiót. | 
   |||| 

   Miután az Azure az összekötőt telepíti, a logic apps összekötő menü automatikusan megnyílik. 
   Ha nem, az Azure-irányítópultjának lehetőségek közül választhat a soap-összekötőt.

## <a name="2-define-your-connector"></a>2. Adja meg az összekötő

Most adja meg a WSDL-fájl vagy URL-címe az összekötő, a hitelesítés, amely az összekötőt használja, és a műveletek és eseményindítók, amely a soap-összekötő létrehozása


### <a name="2a-specify-the-wsdl-file-or-url-for-your-connector"></a>2a. Adja meg a WSDL-fájl vagy URL-címe az összekötőhöz

1. Az összekötő menü, ha nincs bejelölve, válasszon **Logic Apps-összekötők**. Az eszköztáron válassza **szerkesztése**.

   ![Egyéni összekötő szerkesztése](./media/logic-apps-soap-connector-create-register/edit-soap-connector.png)

2. Válasszon **általános** , hogy a táblázatokban adatainak megadása létrehozása, biztonságossá tétele és a műveletek és a SOAP-összekötő eseményindítók meghatározása.

   1. A **egyéni összekötők**, jelölje be **SOAP** a a **API-végpont** , megadhatja a WSDL-fájl, mely leírja az API-t.

      ![Adja meg a WSDL-fájl az API-hoz](./media/logic-apps-soap-connector-create-register/provide-wsdl-file.png)

      | Beállítás | Formátum |Leírás | 
      | ------ | ------ | ----------- | 
      | **Töltse fel a WSDL-fájlból** | *WSDL-fájl* | Keresse meg a helyet, a WSDL-fájl, és válassza ki a fájlt. | 
      | **Töltse fel az URL-címről WSDL** | http://*útvonal-wsdl-fájl –* | Adja meg az URL-címet a szolgáltatás WSDL-fájl. | 
      | **SOAP-többi** |   | Alakítsa át API-k SOAP szolgáltatás REST API-k. | 
      |||| 

   2. A **általános információkat**, töltse fel az összekötő ikont. 
   Általában a **leírás**, **állomás**, és **alap URL-cím** mezőket a rendszer automatikusan feltölti a WSDL-fájlból. 
   De ha még nem, adja hozzá ezeket az információkat a táblázatban ismertetett módon, és válassza a **Folytatás**. 

      ![Connector részletei](./media/logic-apps-soap-connector-create-register/add-general-details.png)

      | A beállítás vagy beállítás | Formátum | Leírás | 
      | ----------------- | ------ | ----------- | 
      | **Töltse fel az ikon** | *PNG-or-jpg-File-under-1-MB* | Az összekötő jelölő ikon <p>Szín: Lehetőleg fehér szín háttér emblémát. <p>Dimenziók: ~ 160 képpontos embléma 230 képpontos négyzet belül | 
      | **Ikon háttérszíne** | *ikon-márka-szín-hexadecimális-kód* | <p>Az ikon, amely megfelel az ikonfájl háttérszíne hátterének színét. <p>Formátum: hexadecimális. #007ee5 például a kék szín jelöli. | 
      | **Leírás** | *összekötő-leírása* | Adjon meg egy rövid leírást a Connector. | 
      | **Állomás** | *összekötő-állomás* | A SOAP-a gazdagép tartományhoz adni. | 
      | **Alap URL-cím** | *összekötő-alap-URL-cím* | A SOAP-adni az alap URL-címet. | 
      |||| 

### <a name="2b-describe-the-authentication-that-your-connector-uses"></a>2b. A hitelesítés az összekötőt használó leírása

1. Válasszon **biztonsági** , tekintse át, vagy a hitelesítés az összekötőt használó ismertetik. Hitelesítési gondoskodik arról, hogy a felhasználók identitását megfelelően a szolgáltatás és az ügyfelek közötti áramlását.

   Alapértelmezés szerint az összekötő által **hitelesítési típus** értéke **nincs hitelesítés**.
   
   ![Hitelesítés típusa](./media/logic-apps-soap-connector-create-register/security-authentication-options.png)

   A hitelesítési típus módosításához válassza **szerkesztése**. Kiválaszthatja **az egyszerű hitelesítés**. Paraméter címkék eltérő alapértelmezett értékek használatához frissítse azokat a **paraméter címke**.

   ![Az egyszerű hitelesítés](./media/logic-apps-soap-connector-create-register/security.png)

   
2. Az összekötő mentéséhez a biztonsági adatokat a lap tetején megadása után válassza **összekötő frissítése**, majd válassza a **Folytatás**. 

### <a name="2c-review-update-or-define-actions-and-triggers-for-your-connector"></a>2c. Tekintse át, a frissítést, vagy a műveletek és a Connector eseményindítók definiálása

1. Válasszon **Definition** áttekintheti, szerkesztése vagy új műveletek és eseményindítókat, amelyek felhasználókat adhat hozzá a munkafolyamatok megadása.

   Műveletek és eseményindítók alapján a WSDL-fájl megadott műveleteket, amelyek automatikusan feltölti a **Definition** lapon, és a kérés- és értékeket tartalmaznak. Ezért ha a szükséges műveletek már itt jelenik meg, megnyithatja a regisztrációs folyamat során a következő lépéssel ezen az oldalon módosítások nélkül.

   ![Összekötő meghatározása](./media/logic-apps-soap-connector-create-register/definition.png)

2. Ha szükséges, ha azt szeretné, meglévő műveletek és eseményindítók szerkesztéséhez vagy újak, hozzáadásával [folytassa a következő lépéseket](logic-apps-custom-connector-register.md#add-action-or-trigger).


## <a name="3-finish-creating-your-connector"></a>3. Az összekötő létrehozásának befejezéséhez

Ha elkészült, válassza ki a **frissítés összekötő** , hogy az összekötőt. 

Gratulálunk! Most logikai alkalmazás létrehozása, amikor is megkeresi a connector Logic Apps-tervezőben, és adja hozzá ezt az összekötőt a Logic Apps alkalmazást.

![Logic Apps Designer keresse meg az összekötő](./media/logic-apps-soap-connector-create-register/soap-connector-created.png)

## <a name="share-your-connector-with-other-logic-apps-users"></a>Az összekötő megosztása más Logic Apps felhasználókkal

Regisztrált, de a nem hitelesített egyéni összekötők úgy működik, mint a Microsoft által felügyelt összekötők, de látható és *csak* az összekötő Szerző és azonos Azure Active Directory-bérlőt és Azure-előfizetéssel rendelkező felhasználók a logic apps a régióban, ahol az alkalmazások vannak telepítve. Bár megosztása nem kötelező, lehetséges, hogy a forgatókönyvek az összekötők megosztása más felhasználókkal helyét. 

> [!IMPORTANT]
> Ha megosztott egy összekötőt, mások is elindíthatják összekötőt függ. 
> ***Az összekötő törlése az összes kapcsolatot az összekötőt.***
 
Az összekötő kívül a határok, például külső felhasználókkal való megosztása senki Logic Apps [küldje el a Microsoft-tanúsítvány-összekötő](../logic-apps/custom-connector-submit-certification.md).

## <a name="faq"></a>GYIK

**K:** a SOAP összekötő általánosan elérhető (GA)? </br>
**V:** a SOAP-összekötő **előzetes**, és még nincs GA szolgáltatás.

**K:** bármely korlátozások és a SOAP-összekötővel kapcsolatos ismert problémák vannak-e? </br>
**V:** Igen, tekintse meg a [SOAP összekötő korlátozások és ismert problémákat](../api-management/api-management-api-import-restrictions.md#wsdl).

**K:** vannak-e bármilyen egyéni összekötők korlátai? </br>
**V:** Igen, tekintse meg a [egyéni összekötő korlátozza Itt](../logic-apps/logic-apps-limits-and-config.md#custom-connector-limits).

## <a name="get-support"></a>Támogatás kérése

* Forduljon a támogatási fejlesztési és bevezetési, vagy a kérelem-szolgáltatások, amelyek nem érhetők el a regisztrációs varázslót a, [ condevhelp@microsoft.com ](mailto:condevhelp@microsoft.com). A Microsoft developer kérdések és problémák ehhez a fiókhoz figyeli, és továbbítja a dokumentumokat a megfelelő csapat.

* Kérje meg vagy kérdésekre, vagy más Azure Logic Apps felhasználók tevékenységeit megtekintéséhez keresse fel a [Azure Logic Apps-fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* A Logic Apps javítása érdekében, szavazhatnak, vagy küldje el a következő ötleteket a [Logic Apps felhasználói visszajelzési webhelyet](http://aka.ms/logicapps-wish). 

## <a name="next-steps"></a>Következő lépések

* [Választható lehetőség: Az összekötő tanúsítás](../logic-apps/custom-connector-submit-certification.md)
* [Egyéni összekötő – gyakori kérdések](../logic-apps/custom-connector-faq.md)