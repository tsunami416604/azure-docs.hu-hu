---
title: RosettaNet üzenetek a B2B integrációhoz
description: Exchange RosettaNet-üzenetek az Azure Logic Apps alkalmazásban vállalati integrációs csomaggal
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 2cc2ac08b9624c1d1d9bee9ce91a7c91189d7f2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792418"
---
# <a name="exchange-rosettanet-messages-for-b2b-enterprise-integration-in-azure-logic-apps"></a>Exchange RosettaNet-üzenetek a B2B vállalati integrációhoz az Azure Logic Apps alkalmazásban

[A RosettaNet](https://resources.gs1us.org) egy non-profit konzorcium, amely szabványos eljárásokat hozott létre az üzleti információk megosztására. Ezeket a szabványokat általánosan használják az ellátási lánc folyamataiban, és széles körben elterjedtek a félvezető, az elektronika és a logisztikai iparágakban. A RosettaNet konzorcium létrehozza és karbantartja a Partnerinterface Processes (PIP-ket), amelyek közös üzleti folyamatdefiníciókat biztosítanak az összes RosettaNet üzenetváltáshoz. A RosettaNet xml alapú, és meghatározza az üzenetekkel kapcsolatos irányelveket, az üzleti folyamatok felületeit és a vállalatok közötti kommunikáció végrehajtási keretrendszerét.

Az [Azure Logic Apps](../logic-apps/logic-apps-overview.md)alkalmazásban a RosettaNet-összekötő segít a RosettaNet-szabványokat támogató integrációs megoldások létrehozásában. Az összekötő a RosettaNet Implementation Framework (RNIF) 2.0.01-es verzióján alapul. Az RNIF egy nyílt hálózati alkalmazáskeretrendszer, amely lehetővé teszi az üzleti partnerek számára a RosettaNet PIP-k közös működtetését. Ez a keretrendszer határozza meg az üzenetszerkezetet, a nyugták szükségességét, a többcélú internet-mail bővítmények (MIME) kódolását és a digitális aláírást.

Pontosabban az összekötő biztosítja a következő képességeket:

* RosettaNet üzenetek kódolása vagy fogadása.
* Dekódolni vagy küldeni RosettaNet üzeneteket.
* Várja meg a választ, és a generációs értesítés hiba.

Ezekhez a képességekhez az összekötő támogatja az RNIF 2.0.01 által meghatározott összes PI-t. A partnerrel való kommunikáció lehet szinkron vagy aszinkron.

## <a name="rosettanet-concepts"></a>RosettaNet fogalmak

Íme néhány olyan fogalom és kifejezés, amely egyedülálló a RosettaNet specifikációban, és fontos a RosettaNet-alapú integrációk építésekor:

* **Pip**

  A RosettaNet szervezet létrehozza és karbantartja a Partnerinterface Processes (PI-ket), amelyek közös üzleti folyamatdefiníciókat biztosítanak az összes RosettaNet üzenetváltáshoz. Minden PIP-specifikáció tartalmaz egy dtd-fájlt és egy üzenet-útmutató dokumentumot. A DTD-fájl határozza meg a szolgáltatás-tartalom üzenet szerkezetét. Az üzenet-irányelvalsor, amely egy ember által olvasható HTML-fájl, elemszintű megkötéseket határoz meg. Ezek a fájlok együttesen biztosítják az üzleti folyamat teljes meghatározását.

   A PI-k magas szintű üzleti függvény vagy fürt, valamint egy alfüggvény vagy szegmens szerint vannak kategorizálva. Például a "3A4" a beszerzési rendelés PIP-je, míg a "3" a Rendeléskezelés funkció, a "3A" pedig az Ajánlat & Rendelésbevitel alfunkció. További információt a [RosettaNet webhelyen talál.](https://resources.gs1us.org)

* **Művelet**

  A PIP része, a műveletüzenetek a partnerek között kicserélt üzleti üzenetek.

* **Jel**

   A PIP része, a jelüzenetek a műveletüzenetekre válaszul küldött nyugták.

* **Egységes fellépés és kettős fellépés**

  Az egyműveletes PIP esetében az egyetlen válasz egy nyugtázási jelüzenet. A kettős műveletpip, a kezdeményező kap egy válaszüzenetet, és a válaszok egy nyugtázás mellett az egyműveletes üzenet áramlását.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókot.](https://azure.microsoft.com/free/)

* Integrációs [fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) a szerződés és más B2B-összetevők tárolásához. Ezt az integrációs fiókot az Azure-előfizetéshez kell társtársosan kezelni.

* Legalább két [partner,](../logic-apps/logic-apps-enterprise-integration-partners.md) amely az integrációs fiókban van definiálva, és konfigurálva van a "DUNS" minősítővel az **Üzleti identitások** csoportban

* A PIP-folyamat konfigurációja, amely a RosettaNet-üzenetek küldéséhez vagy fogadásához szükséges, az integrációs fiókban. A folyamat konfigurációja tárolja az összes PIP konfigurációs jellemzőt. Ezután hivatkozhat erre a konfigurációra, amikor megállapodást hoz létre a partnerrel. Pip-folyamatkonfiguráció létrehozásához az integrációs fiókban a [PIP-folyamat konfigurációjának hozzáadása](#add-pip)című témakörben található.

* Választható [tanúsítványok](../logic-apps/logic-apps-enterprise-integration-certificates.md) az integrációs fiókba feltöltött üzenetek titkosításához, visszafejtéséhez vagy aláírásához. A tanúsítványokra csak akkor van szükség, ha aláírást vagy titkosítást használ.

<a name="add-pip"></a>

## <a name="add-pip-process-configuration"></a>PIP-folyamat konfigurációjának hozzáadása

Ha pip-folyamatkonfigurációt szeretne hozzáadni az integrációs fiókhoz, kövesse az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com)keresse meg és nyissa meg az integrációs fiókot.

1. Az **Áttekintő** ablaktáblán válassza a **RosettaNet PIP** csempét.

   ![RosettaNet csempe kiválasztása](media/logic-apps-enterprise-integration-rosettanet/select-rosettanet-tile.png)

1. A **RosettaNet PIP csoportban**válassza **a Hozzáadás**lehetőséget. Adja meg a PIP adatait.

   ![RosettaNet PIP részletek hozzáadása](media/logic-apps-enterprise-integration-rosettanet/add-rosettanet-pip.png)

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **Név** | Igen | Az Ön PIP-neve |
   | **PIP-kód** | Igen | A PIP háromjegyű kód. További információ: [RosettaNet PIPs](https://docs.microsoft.com/biztalk/adapters-and-accelerators/accelerator-rosettanet/rosettanet-pips). |
   | **PIP verzió** | Igen | A PIP verziószám, amely a kiválasztott PIP-kód alapján érhető el |
   ||||

   A PIP-tulajdonságokkal kapcsolatos további információkért látogasson el a [RosettaNet webhelyére.](https://resources.gs1us.org/RosettaNet-Standards/Standards-Library/PIP-Directory#1043208-pipsreg)

1. Ha elkészült, válassza az **OK gombot,** amely létrehozza a PIP konfigurációt.

1. A folyamatkonfiguráció megtekintéséhez vagy szerkesztéséhez jelölje ki a PIP-et, és válassza a **Szerkesztés JSON-ként**lehetőséget.

   Minden folyamatkonfigurációs beállítás a PIP specifikációiból származik. A Logic Apps a legtöbb beállítást feltölti az alapértelmezett értékekkel, amelyek a leggyakrabban használt értékek ezekhez a tulajdonságokhoz.

   ![RosettaNet PIP-konfiguráció szerkesztése](media/logic-apps-enterprise-integration-rosettanet/edit-rosettanet-pip.png)

1. Ellenőrizze, hogy a beállítások megfelelnek-e a megfelelő PIP specifikációban megadott értékeknek, és hogy megfelelnek-e az üzleti igényeinek. Szükség esetén frissítse a JSON értékeit, és mentse ezeket a módosításokat.

## <a name="create-rosettanet-agreement"></a>RosettaNet-szerződés létrehozása

1. Az [Azure Portalon](https://portal.azure.com)keresse meg és nyissa meg az integrációs fiókot, ha még nem nyitott.

1. Az **Áttekintő** ablaktáblán válassza a **Megállapodások csempét.**

   ![Megállapodások csempe kiválasztása](media/logic-apps-enterprise-integration-rosettanet/select-agreement-tile.png)

1. A **Megállapodások csoportban**válassza **a Hozzáadás**lehetőséget. Adja meg a szerződés részleteit.

   ![Szerződés részleteinek hozzáadása](media/logic-apps-enterprise-integration-rosettanet/add-agreement-details.png)

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **Név** | Igen | A megállapodás neve |
   | **Megállapodás típusa** | Igen | Válassza a **RosettaNet**lehetőséget. |
   | **Fogadó partner** | Igen | A megállapodáshoz házigazda és vendégpartner is szükséges. A gazdapartner a szerződést konfiguráló szervezetet jelöli. |
   | **Állomás identitása** | Igen | A gazdapartner azonosítója |
   | **Vendégpartner** | Igen | A megállapodáshoz házigazda és vendégpartner is szükséges. A vendégpartner képviseli azt a szervezetet, amely a fogadó partnerrel üzleti kapcsolatban áll. |
   | **Vendég identitása** | Igen | A vendégpartner azonosítója |
   | **Fogadási beállítások** | Változó | Ezek a tulajdonságok a gazdapartner által fogadott összes üzenetre vonatkoznak. |
   | **Beállítások küldése** | Változó | Ezek a tulajdonságok a gazdapartner által küldött összes üzenetre vonatkoznak. |  
   | **RosettaNet PIP referenciák** | Igen | A pip referenciák a megállapodáshoz. Minden RosettaNet-üzenethez PIP-konfiguráció szükséges. |
   ||||

1. Ha be szeretné állítani a vendégpartnertől érkező üzenetek fogadására vonatkozó szerződést, válassza a **Fogadási beállítások lehetőséget.**

   ![Fogadási beállítások fogadása](media/logic-apps-enterprise-integration-rosettanet/add-agreement-receive-details.png)

   1. A bejövő üzenetek aláírásának vagy titkosításának engedélyezéséhez az **Üzenetek**csoportban válassza az **Üzenet aláírást,** illetve az **Üzenet titkosítását.**

      | Tulajdonság | Kötelező | Leírás |
      |----------|----------|-------------|
      | **Az üzenetet alá kell írni** | Nem | Bejövő üzenetek bejelentkezése a kijelölt tanúsítvánnyal. |
      | **Tanúsítvány** | Igen, ha az aláírás engedélyezve van | Az aláíráshoz használandó tanúsítvány |
      | **Üzenettitkosítás engedélyezése** | Nem | A bejövő üzenetek titkosítása a kijelölt tanúsítvánnyal. |
      | **Tanúsítvány** | Igen, ha a titkosítás engedélyezve van | A titkosításhoz használandó tanúsítvány |
      ||||

   1. Az egyes beállítások alatt válassza ki a megfelelő [tanúsítványt](./logic-apps-enterprise-integration-certificates.md), amelyet korábban hozzáadott az integrációs fiókhoz, aláíráshoz vagy titkosításhoz.

1. Ha be szeretné állítani a vendégpartnernek küldött üzenetekre vonatkozó szerződését, válassza a **Küldési beállítások lehetőséget.**

   ![Beállítások küldése](media/logic-apps-enterprise-integration-rosettanet/add-agreement-send-details.png)

   1. A kimenő üzenetek aláírásának vagy titkosításának engedélyezéséhez az **Üzenetek**csoportban jelölje be **az Üzenetaláírás engedélyezése** vagy az **Üzenettitkosítás engedélyezése** jelölőnégyzetet. Az egyes beállítások alatt válassza ki a megfelelő algoritmust és [tanúsítványt](./logic-apps-enterprise-integration-certificates.md), amelyet korábban hozzáadott az integrációs fiókhoz, aláíráshoz vagy titkosításhoz.

      | Tulajdonság | Kötelező | Leírás |
      |----------|----------|-------------|
      | **Üzenetaláírás engedélyezése** | Nem | A kimenő üzeneteket a kijelölt aláíró algoritmussal és tanúsítvánnyal írja alá. |
      | **Aláírási algoritmus** | Igen, ha az aláírás engedélyezve van | A kiválasztott tanúsítványon alapuló aláírási algoritmus |
      | **Tanúsítvány** | Igen, ha az aláírás engedélyezve van | Az aláíráshoz használandó tanúsítvány |
      | **Üzenettitkosítás engedélyezése** | Nem | A kimenő adatok titkosítása a kijelölt titkosítási algoritmussal és tanúsítvánnyal. |
      | **Titkosítási algoritmus** | Igen, ha a titkosítás engedélyezve van | A kiválasztott tanúsítványon alapuló titkosítási algoritmus |
      | **Tanúsítvány** | Igen, ha a titkosítás engedélyezve van | A titkosításhoz használandó tanúsítvány |
      ||||

   1. A **Végpontok csoportban**adja meg a műveletüzenetek és nyugták küldéséhez szükséges URL-címeket.

      | Tulajdonság | Kötelező | Leírás |
      |----------|----------|-------------|
      | **Művelet URL-címe** |  Igen | A műveletüzenetek küldéséhez használandó URL-cím. Az URL-cím a szinkron és az aszinkron üzenetek kötelező mezője. |
      | **Nyugta URL-címe** | Igen | A nyugtázó üzenetek küldéséhez használandó URL-cím. Az URL-cím az aszinkron üzenetek kötelező mezője. |
      ||||

1. Ha be szeretné állítani a kapcsolatot a RosettaNet PIP-hivatkozásokkal a partnerek számára, válassza a **RosettaNet PIP-hivatkozások lehetőséget.** A **PIP-név csoportban**válassza ki a korábban létrehozott PIP nevét.

   ![PIP-hivatkozások](media/logic-apps-enterprise-integration-rosettanet/add-agreement-pip-details.png)

   A kijelölés feltölti a fennmaradó tulajdonságokat, amelyek az integrációs fiókban beállított PIP-en alapulnak. Szükség esetén módosíthatja a **PIP szerepkört**.

   ![Kijelölt PIP](media/logic-apps-enterprise-integration-rosettanet/add-agreement-selected-pip.png)

A lépések elvégzése után készen áll a RosettaNet-üzenetek küldésére vagy fogadására.

## <a name="rosettanet-templates"></a>RosettaNet sablonok

A fejlesztés felgyorsítása és az integrációs minták ajánlása érdekében logic app sablonokat használhat a RosettaNet üzenetek dekódolásához és kódolásához. Logikai alkalmazás létrehozásakor a Logic App Designer sablongyűjteményéből választhat. Ezeket a sablonokat az [Azure Logic Apps GitHub-tárházában](https://github.com/Azure/logicapps)is megtalálhatja.

![RosettaNet sablonok](media/logic-apps-enterprise-integration-rosettanet/decode-encode-rosettanet-templates.png)

## <a name="receive-or-decode-rosettanet-messages"></a>RosettaNet-üzenetek fogadása vagy dekódolása

1. [Hozzon létre egy üres logikai alkalmazást.](quickstart-create-first-logic-app-workflow.md)

1. [Az integrációs fiók összekapcsolása](logic-apps-enterprise-integration-create-integration-account.md#link-account) a logikai alkalmazással.

1. Mielőtt hozzáadhatna egy műveletet a RosettaNet üzenet dekódolásához, hozzá kell adnia egy eseményindítót a logikai alkalmazás indításához, például egy kérési eseményindítót.

1. Az eseményindító hozzáadása után válassza az **Új lépés lehetőséget.**

   ![Kérelem eseményindítóhozzáadása](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. A keresőmezőbe írja be a "rosettanet" kifejezést, és válassza ki ezt a műveletet: **RosettaNet Decode**

   ![A "RosettaNet Decode" művelet megkeresése és kiválasztása](media/logic-apps-enterprise-integration-rosettanet/select-decode-rosettanet-action.png)

1. Adja meg a művelet tulajdonságaira vonatkozó információkat:

   ![Művelet részleteinek biztosítása](media/logic-apps-enterprise-integration-rosettanet/decode-action-details.png)

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **Üzenetet** | Igen | A Dekódolandó RosettaNet üzenet  |
   | **Fejlécek** | Igen | A HTTP-fejlécek, amelyek a verzió értékeit adják meg, amely az RNIF-verzió, valamint a választípus, amely a partnerek közötti kommunikáció típusát jelzi, és szinkron vagy aszinkron lehet |
   | **Szerepkör** | Igen | A fogadó partner szerepe a PIP-ben |
   ||||

   A RosettaNet Decode művelet, a kimenet, valamint más tulajdonságok, tartalmazza **a kimenő jel**, amely választhat, hogy kódolja, és visszatér a partner, vagy bármilyen más műveletet az adott kimeneten.

## <a name="send-or-encode-rosettanet-messages"></a>RosettaNet-üzenetek küldése vagy kódolása

1. [Hozzon létre egy üres logikai alkalmazást.](quickstart-create-first-logic-app-workflow.md)

1. [Az integrációs fiók összekapcsolása](logic-apps-enterprise-integration-create-integration-account.md#link-account) a logikai alkalmazással.

1. A RosettaNet-üzenet kódolásához művelet hozzáadása előtt hozzá kell adnia egy eseményindítót a logikai alkalmazás indításához, például egy kérési eseményindítót.

1. Az eseményindító hozzáadása után válassza az **Új lépés lehetőséget.**

   ![Kérelem eseményindítóhozzáadása](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. A keresőmezőbe írja be a "rosettanet" kifejezést, és válassza a következő műveletet: **RosettaNet Encode**

   ![Keresse meg és válassza ki a "RosettaNet Encode" műveletet](media/logic-apps-enterprise-integration-rosettanet/select-encode-rosettanet-action.png)

1. Adja meg a művelet tulajdonságaira vonatkozó információkat:

   ![Művelet részleteinek biztosítása](media/logic-apps-enterprise-integration-rosettanet/encode-action-details.png)

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **Üzenetet** | Igen | A RosettaNet kódolandó üzenete  |
   | **Fogadó partner** | Igen | A gazdapartner neve |
   | **Vendégpartner** | Igen | A vendégpartner neve |
   | **PIP-kód** | Igen | A PIP-kód |
   | **PIP verzió** | Igen | A PIP verzió |  
   | **PIP-példány identitása** | Igen | A PIP-üzenet egyedi azonosítója |  
   | **Üzenettípus** | Igen | A kódolandó üzenet típusa |  
   | **Szerepkör** | Igen | A fogadó partner szerepe |
   ||||

   A kódolt üzenet készen áll a partnernek való elküldésre.

1. A kódolt üzenet elküldéséhez ez a példa a **HTTP-műveletet** használja, amely "HTTP - Kódolt üzenet küldése a partnernek" névre keresztelt.

   ![HTTP-művelet a RosettaNet-üzenet küldéséhez](media/logic-apps-enterprise-integration-rosettanet/send-rosettanet-message-to-partner.png)

   A RosettaNet szabványai szerint az üzleti tranzakciók csak akkor minősülnek teljesnek, ha a PIP által meghatározott összes lépés befejeződött.

1. Miután az állomás elküldi a kódolt üzenetet a partnernek, az állomás megvárja a jelet és a nyugtázást. A feladat végrehajtásához adja hozzá a **RosettaNet várakozási válasz** művelet.

   !["RosettaNet várakozás a válaszra" művelet hozzáadása](media/logic-apps-enterprise-integration-rosettanet/rosettanet-wait-for-response-action.png)

   A várakozáshoz használt időtartam és az újrapróbálkozások száma az integrációs fiók PIP-konfigurációján alapul. Ha a válasz nem érkezik meg, ez a művelet létrehoz egy értesítést a hiba. Az újrapróbálkozások kezeléséhez mindig helyezze az **Encode-ot,** és **várja meg a válaszműveleteket** egy **Until** ciklusban.

   ![Amíg hurok RosettaNet intézkedések](media/logic-apps-enterprise-integration-rosettanet/rosettanet-loop.png)

## <a name="next-steps"></a>További lépések

* A vállalati integrációs csomaggal végzett műveletek érvényesítésének, átalakításának és egyéb [üzenetműveleteinek megismerése](../logic-apps/logic-apps-enterprise-integration-overview.md)
* További információ a [Logic Apps-összekötőkről](../connectors/apis-list.md)
