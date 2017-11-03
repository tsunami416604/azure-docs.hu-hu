---
title: "Egyéni összekötők áttekintés – Azure Logic Apps |} Microsoft Docs"
description: "Egyéni összekötők támogatása és integrációs feladatokhoz bővülő létrehozása – áttekintés"
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
ms.openlocfilehash: 0515b21bc7c7cc737e14fda016606bbea1aab476
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="custom-connectors-overview"></a>Egyéni összekötők áttekintése

Programozás, nélkül sorrendben is létrehozhat munkafolyamatokat és alkalmazások [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), [Microsoft Flow](https://flow.microsoft.com), vagy [Microsoft PowerApps](https://powerapps.microsoft.com). Teszi az integrációt az adatok és az üzleti folyamatok, ezek a szolgáltatások ajánlja fel [100 + összekötők](../connectors/apis-list.md), nem csak a Microsoft-szolgáltatások és termékek, például az Azure és az SQL Server, de más szolgáltatások túl, például a Githubon, Salesforce, a Twitter és további . 

Egyes esetekben azonban célszerű hívja az API-k, a szolgáltatások és a rendszerek, amelyek nem érhetők el előre elkészített összekötők. Több szabott forgatókönyvek a felhasználók és a termelékenység igények támogatásához létrehozhat *egyéni összekötők* saját eseményindítók és műveletek.
Egyéni összekötők bontsa ki a integrációja, reach, felderítése és használata a szolgáltatás vagy termék, így könnyen növelése és a felhasználói elfogadása felgyorsítása.

Ez az ábra például egy webes API-t, hogy API, és a logikai alkalmazás, amely az adott API-t az egyéni összekötő által létrehozott egyéni Logic Apps összekötő közötti interakció látható:

![A Web API, egyéni összekötő és logikai alkalmazás elméleti áttekintése](./media/custom-connector-overview/custom-connector-conceptual.png)

Ez az áttekintés bemutatja azokat az általános magas szintű feladatok létrehozása, biztonságossá tételéhez, regisztrálása, és használatával és opcionálisan megosztása vagy az összekötők igazoló:

![Szerzői lépéseket egyéni összekötő](./media/custom-connector-overview/authoring-steps.png)

## <a name="prerequisites"></a>Előfeltételek

Az összekötő kezdetétől a végéig létrehozásához kell ezeket az elemeket:

* Azure-előfizetés. Ha nem rendelkezik előfizetéssel, Kezdésként használhatja az egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/). Ellenkező esetben regisztráljon egy [használatalapú fizetés előfizetés](https://azure.microsoft.com/pricing/purchase-options/).

* Egy RESTful API-t bizonyos típusú hitelesített hozzáférést. További információkért lásd: [hozzon létre egyéni összekötők webes API-](../logic-apps/custom-connector-build-web-api-app-tutorial.md). Használható a connector eseményindítók és műveletek kombinációját, lásd: [hozzon létre egyéni API-k, amelyek a logic app munkafolyamatok hívása](../logic-apps/logic-apps-create-api-app.md).

* Bármely elem itt:

  * Egy [OpenAPI 2.0 fájl](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md), korábban néven Swagger
  * Az OpenAPI-definíció URL-CÍMÉT
  * A [Postman gyűjtemény](../logic-apps/custom-connector-api-postman-collection.md) az API- 

  Ha ezek az elemek nem rendelkezik, nyújtunk segítséget meg.

* Választható lehetőség: Egy kép ikonként az egyéni összekötő használatára

## <a name="1-build-your-restful-api"></a>1. A RESTful API létrehozása

Technikai szempontból összekötő-e a REST API-t egy OpenAPI alapuló csomagolásának (korábbi nevén Swagger) specifikáció és lehetővé teszi, hogy a mögöttes szolgáltatás kérdezze meg a Logic Apps, a folyamat vagy a powerapps segítségével. Ezért először a teljes mértékben működő alkalmazásprogramozási Felületet az egyéni összekötő létrehozása előtt. 

A nyelvi és platform az API-használható. A Microsoft-technológiák ajánlott ezekről a platformokról egyikét:

* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Azure Web Apps](https://azure.microsoft.com/services/app-service/web/)
* [Az Azure API-alkalmazások](https://azure.microsoft.com/services/app-service/api/)

Például ez az oktatóanyag bemutatja [egyéni összekötő létrehozása egy webes API](../logic-apps/custom-connector-build-web-api-app-tutorial.md). Használható a connector eseményindítók és műveletek kombinációját, lásd: [hozzon létre egyéni API-k, amelyek a logic app munkafolyamatok hívása](../logic-apps/logic-apps-create-api-app.md).

## <a name="2-secure-your-api"></a>2. Az API védelme

Ezek a hitelesítési szabványok összekötők és API-k használhatja:

   * [OAuth 2.0](https://oauth.net/2/), többek között a következőket [Azure Active Directory](https://azure.microsoft.com/develop/identity/) vagy adott szolgáltatások, például a dropbox-ba, a Githubon és a SalesForce
   * Általános OAuth 2.0
   * [Az egyszerű hitelesítés](https://swagger.io/docs/specification/authentication/basic-authentication/)
   * [Az API-kulcs](https://swagger.io/docs/specification/authentication/api-keys/)

Az API-nak az Azure-portálon az Azure Active Directory (Azure AD) hitelesítési beállíthat így nem kell hitelesítési kód végrehajtásához. Vagy igényelnek, és kényszeríteni a hitelesítést az API-kódban keresztül. 

További információért hajtsa végre a megfelelő oktatóanyagok:

* [A Logic Apps: Az egyéni összekötő biztonságos](../logic-apps/custom-connector-azure-active-directory-authentication.md)
* [Folyamat: Biztonságos az egyéni összekötő](https://ms.flow.microsoft.com/documentation/customapi-azure-resource-manager-tutorial/)
* [PowerApps: Biztonságos az egyéni összekötő](https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/)

## <a name="3-describe-your-api"></a>3. Az API leírása 

Feltételezve, hogy az API-hitelesített hozzáférést bizonyos típusú rendelkezik, az API felület és műveletek írják le, hogy a Logic Apps, a folyamat vagy a powerapps segítségével képes kommunikálni az API-t kell.
E iparági szabványos definíciókat egyikét használja:

* Egy [OpenAPI 2.0 fájl](https://swagger.io/) elindíthatja épület egy meglévő OpenAPI fájllal.

  Ha most ismerkedik a OpenAPI, látogasson el [Ismerkedés a Swagger](http://swagger.io/getting-started/) a swagger.io helyen.
  Egy példa OpenAPI fájlt, tekintse meg a [szöveg Analytics API-JÁNAK dokumentációja](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/export?DocumentFormat=Swagger&ApiName=Azure). 

* A Postman gyűjteményt, amely automatikusan létrehoz egy OpenAPI fájlt meg. Ha még nincs egy OpenAPI fájlt, és nem szeretné, hozzon létre egyet, továbbra is könnyen hozhat létre egy egyéni összekötő Postman gyűjtemény használatával.

  Ha most ismerkedik a Postman, [a Postman alkalmazástelepítési](https://www.getpostman.com/apps) a helyükön. További információkért lásd: [írják le a Postman egyéni összekötők](../logic-apps/custom-connector-api-postman-collection.md).

> [!IMPORTANT]
> A fájlméret 1 MB-nál kevesebb lehet.

A háttérben Logic Apps, a folyamat és a powerapps segítségével végső soron OpenAPI használja, a Postman gyűjtemény elemzi és átalakítja a gyűjteményt, amelyben egy OpenAPI csomagdefiníciós fájlt. Bár OpenAPI 2.0 és Postman gyűjtemények különböző formátumokban, mindkettő nyelvtől független, géppel olvasható dokumentumok, amely az API-műveleteket és paramétereket ismerteti. Ezeket a dokumentumokat a különböző eszközök, a nyelvi alapján is létrehozhat, és platform az API-t használja. Amikor regisztrál az összekötő egy OpenAPI fájlt is hozhat létre.

Például létrehozhat egy OpenAPI fájl Postman gyűjteményét, illetve a REST API-végpont, beleértve:

* Nyilvánosan elérhető összekötők, például [Spotify](https://developer.spotify.com/), [Slackhez](https://api.slack.com/), [Rackspace](http://docs.rackspace.com/), és így tovább

* Az API-k létrehozott és telepített bármely felhőalapú szolgáltatóra, mint az Azure, Heroku, Google Cloud,

* Egy egyéni üzleti API-t, hogy telepítve van a hálózatban, de csak akkor, ha az adott API-t a nyilvános interneten van közzétéve.

## <a name="4-register-your-connector"></a>4. Az összekötő regisztrálása

A regisztrációs folyamat során a Logic Apps, folyamata, segítségével, vagy a PowerApps megérteni az API-jellemzőkkel rendelkezik, beleértve a leírás, a szükséges hitelesítés és a műveletek, beleértve a paraméterek és kimenetek minden művelethez. A regisztrációs folyamat megkezdéséhez, biztosíthatja az OpenAPI fájl vagy egy Postman gyűjteményt, amely automatikusan feltölti a metaadatmezőket, a regisztráció varázslóban. A mezők értékei bármikor módosíthatja.

![Az API leírása](./media/custom-connector-overview/choose-api-definition-file.png)

Az összekötő regisztrálásához hajtsa végre a megfelelő oktatóanyag:

* [A Logic Apps: Az összekötő regisztrálása](../logic-apps/logic-apps-custom-connector-register.md)
* [Folyamat: Az összekötő regisztrálása](https://ms.flow.microsoft.com/documentation/register-custom-api/#register-your-custom-connector)
* [PowerApps: Az összekötő regisztrálása](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector)

## <a name="5-use-your-connector-in-a-logic-app-flow-or-app"></a>5. A logic app, folyamat vagy alkalmazás-összekötő használatára 

Az összekötő használatát a Microsoft által felügyelt összekötők ugyanúgy használhatja. Például a logic app munkafolyamat adja hozzá az egyéni összekötő így kapcsolat létrehozása az API-nak, és hívja az API-t tartalmaz, amely meghívja a Microsoft által felügyelt összekötők műveleteinek ugyanúgy műveleteket.

## <a name="6-share-your-connector"></a>6. Az összekötő megosztása 

Megoszthatja a connector felhasználók a szervezetben, hogy a Logic Apps, a folyamat vagy a PowerApps erőforrások megosztása azonos módon. Bár megosztása nem kötelező, lehetséges, hogy a forgatókönyvek az összekötők megosztása más felhasználókkal helyét.

Regisztrált, de a nem hitelesített egyéni összekötők úgy működik, mint a Microsoft által felügyelt összekötők, de látható és *csak* az összekötő Szerző és azonos Azure Active Directory-bérlőt és Azure-előfizetéssel rendelkező felhasználók a logic apps a régióban, ahol az alkalmazások vannak telepítve. A következő lépés ismerteti, hogyan megoszthatja az összekötő ezeket a határokat kívüli külső felhasználók például minden Logic Apps, a folyamat és a PowerApps felhasználókkal.

> [!IMPORTANT]
> Ha megosztott egy összekötőt, mások is elindíthatják összekötőt függ. 
> ***Az összekötő törlése az összes kapcsolatot az összekötőt.***

* [A Logic Apps: Az összekötő megosztása](../logic-apps/logic-apps-custom-connector-register.md)
* [Folyamat: Az összekötő megosztása](https://ms.flow.microsoft.com/documentation/register-custom-api/#share-your-custom-connector)
* [PowerApps: Az összekötő megosztása](https://powerapps.microsoft.com/tutorials/register-custom-api/#share-your-custom-connector)

## <a name="7-certify-your-connector"></a>7. Az összekötő tanúsítás

Opcionálisan megosztani az összekötőt a Logic Apps, a folyamat és a powerapps segítségével minden felhasználó, küldje el a Microsoft-tanúsítvány-összekötő. Ez a folyamat ellenőrzi, hogy az összekötő, ellenőrzi a tartalom és műszaki megfelelőségét, és érvényesíti a funkciót a Logic Apps, folyamata, és PowerApps ahhoz a Microsoft adatokat tehessenek közzé az összekötő. Ismerje meg, [hogyan küldje el a Microsoft-tanúsítvány-összekötő](../logic-apps/custom-connector-submit-certification.md).

## <a name="get-support"></a>Támogatás kérése

* A bevezetési és fejlesztői támogatási, e-mail [ condevhelp@microsoft.com ](mailto:condevhelp@microsoft.com). A Microsoft aktívan figyeli a fiókot, fejlesztői kérdések és problémák, és továbbítja a dokumentumokat a megfelelő csoporthoz. 

* Gyakran ismételt kérdések, tekintse meg a [egyéni összekötő – gyakori kérdések](../logic-apps/custom-connector-faq.md)

## <a name="next-steps"></a>Következő lépések

* [Egyéni összekötő felépíteni a webes API](../logic-apps/custom-connector-build-web-api-app-tutorial.md)
* [Az egyéni összekötők hitelesítés beállítása](../logic-apps/custom-connector-azure-active-directory-authentication.md)
* [Egyéni összekötők Postman gyűjteményekkel leírása](../logic-apps/custom-connector-api-postman-collection.md)
* [Küldje el a Microsoft hitelesítő egyéni összekötők](../logic-apps/custom-connector-submit-certification.md)
