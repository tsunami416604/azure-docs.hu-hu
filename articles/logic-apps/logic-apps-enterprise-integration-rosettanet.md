---
title: B2B vállalati integráció – Azure Logic Apps RosettaNet üzenete
description: Az Azure Logic Appsben és Enterprise Integration Pack RosettaNet üzeneteket
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 88e02f3fbbca8007fdf479bb973f50c42a878d6e
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2019
ms.locfileid: "67333023"
---
# <a name="exchange-rosettanet-messages-for-b2b-enterprise-integration-in-azure-logic-apps"></a>Az Azure Logic Apps B2B-vállalati integráció RosettaNet alkalmazásközi 

[RosettaNet](https://resources.gs1us.org) van egy nonprofit consortium hozott létre az üzleti adatok megosztása a szabványos folyamatokat. Ezeknek a szabványoknak gyakran használják az ellátási lánc folyamatok és széles körben elterjedt az félvezető electronics és logisztikai iparágban. A RosettaNet consortium hozza létre, és a partneri kapcsolat folyamatok (maggal), minden RosettaNet üzenetváltásokban közös üzleti folyamatok definíciók biztosító kezeli. RosettaNet XML-alapú, és határozza meg az üzenet irányelveket, az üzleti folyamatok felületek és megvalósítási keretrendszerek vállalatok közötti kommunikációhoz.

A [Azure Logic Apps](../logic-apps/logic-apps-overview.md), a RosettaNet-összekötő segítségével létrehozhatja az integrációs megoldásokat, amelyek támogatják a RosettaNet szabványoknak. Az összekötő a RosettaNet megvalósítási keretrendszer (RNIF) verzió 2.0.01 alapul. RNIF egy megnyitott hálózati alkalmazás-keretrendszer, amely lehetővé teszi az üzleti partnerek RosettaNet magokat elősegítése érdekében futtatásához. Ezt a keretrendszert az üzenet struktúra, a visszaigazolások, Multipurpose Internet Mail Extensions (MIME) kódolás és a digitális aláírás határozza meg.

Az összekötő, ezeket a képességeket biztosítja:

* Kódolás, vagy RosettaNet üzeneteket fogadni.
* Dekódolandó vagy RosettaNet üzeneteket küldeni.
* Várjon, amíg a válasz és értesítés a hiba generációja.

Ezek a képességek az összekötő támogatja az összes magokat RNIF 2.0.01 által meghatározott. A partner folytatott kommunikáció lehet szinkron vagy aszinkron.

## <a name="rosettanet-concepts"></a>RosettaNet fogalmak

Íme, néhány fogalmakat és feltételek, amelyek egyediek a RosettaNet specifikációinak, és fontos RosettaNet-alapú Integrációk készítése során:

* **PIP**

  A RosettaNet szervezet hozza létre, és a partneri kapcsolat folyamatok (maggal), minden RosettaNet üzenetváltásokban közös üzleti folyamatok definíciók biztosító kezeli. Minden egyes PIP specifikáció biztosít egy dokumentum típusa (DTD) csomagdefiníciós fájl és a egy üzenet iránymutatás dokumentumot. A DTD-fájlt a tartalom üzenet struktúrát határozza meg. Üzenet-iránymutatás dokumentum, amely egy olyan emberek számára olvasható HTML-fájl, megadja az elem szintű megkötések. Ezeket a fájlokat, az üzleti folyamat teljes definíciókat tartalmazzák.

   Magokat egy magas szintű üzleti függvény vagy fürt, és a egy alfüggvény vagy a szegmens szerint vannak kategóriába. Például "3A4" a PIP beszerzési rendelés, "3" közben a rendelést kezelő funkció, pedig "3A" az ajánlat & sorrend bejegyzés alfüggvény. További információkért lásd: a [RosettaNet hely](https://resources.gs1us.org).

* **Művelet**

  Része a pipet, a művelet üzeneteket üzleti partnerek között továbbított üzeneteket is.

* **Signal**

   Része a pipet jel üzenetek olyan, a művelet üzeneteket válaszként küldött.

* **Egyetlen művelet, és dupla művelet**

  A single-művelet a PIP, a válasz csak az nyugtázása jel üzenet. A double-művelet PIP a kezdeményező kap egy válaszüzenetet, és egyetlen műveleti Üzenetáramlás mellett nyugtázás-válaszok.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetésem, [regisztráljon egy ingyenes Azure-fiókkal](https://azure.microsoft.com/free/).

* Egy [integrációs fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) a szerződés és más B2B összetevők tárolására. Az integrációs fiók az Azure-előfizetése társítva kell lennie.

* Legalább két [partnerek](../logic-apps/logic-apps-enterprise-integration-partners.md) , amely az integrációs fiók megadott és a "DUNS" minősítő alatt konfigurált **üzleti identitások**

* A PIP folyamat beállítása, üzeneteket küldeni vagy fogadni RosettaNet, az integrációs fiók szükséges. A folyamat konfigurációs tárolja a PIP konfigurációs jellemzőit. Ez a konfiguráció egy szerződést, a partnerrel való létrehozásakor majd hivatkozhat. Az integrációs fiókban lévő PIP folyamat konfiguráció létrehozása: [hozzáadása PIP folyamat konfigurációs](#add-pip).

* Nem kötelező [tanúsítványok](../logic-apps/logic-apps-enterprise-integration-certificates.md) titkosítása, visszafejtése vagy az integrációs fiók feltöltött üzenetek aláírását. Tanúsítványokra szükség, ha a használati aláírási vagy titkosítási.

<a name="add-pip"></a>

## <a name="add-pip-process-configuration"></a>A PIP folyamat konfiguráció hozzáadása

A PIP folyamat konfiguráció az integrációs fiók hozzáadásához kövesse az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com), keresse meg és nyissa meg az integrációs fiókot.

1. Az a **áttekintése** panelen válassza a **RosettaNet PIP** csempére.

   ![RosettaNet csempe kiválasztása](media/logic-apps-enterprise-integration-rosettanet/select-rosettanet-tile.png)

1. A **RosettaNet PIP**, válassza a **Hozzáadás**. Adja meg a PIP-adatokat.

   ![RosettaNet PIP adatainak hozzáadása](media/logic-apps-enterprise-integration-rosettanet/add-rosettanet-pip.png)

   | Tulajdonság | Szükséges | Leírás |
   |----------|----------|-------------|
   | **Name (Név)** | Igen | A PIP neve |
   | **A PIP-kód** | Igen | A PIP három számjegyű kód. További információkért lásd: [RosettaNet magokat](https://docs.microsoft.com/biztalk/adapters-and-accelerators/accelerator-rosettanet/rosettanet-pips). |
   | **A PIP verzióját** | Igen | A PIP verziószámot, amely a kijelölt PIP kód alapján |
   ||||

   Ezek a PIP-tulajdonságokkal kapcsolatos további információkért látogasson el a [RosettaNet webhely](https://resources.gs1us.org/RosettaNet-Standards/Standards-Library/PIP-Directory#1043208-pipsreg).

1. Ha elkészült, válassza ki **OK**, amely a PIP-konfigurációt hoz létre.

1. Megtekinteni vagy szerkeszteni a folyamatot konfiguráció, jelölje ki a PIP, és válassza a **Szerkesztés JSON-ként**.

   A konfigurációs beállításokat a PIP specifikációk származnak az összes folyamat. A Logic Apps a beállításokat az alapértelmezett értékekre, amelyek a leggyakrabban használt értékek ezeket a tulajdonságokat a legtöbb tölti fel.

   ![RosettaNet PIP-konfiguráció szerkesztése](media/logic-apps-enterprise-integration-rosettanet/edit-rosettanet-pip.png)

1. Győződjön meg arról, hogy a beállításokat az értékeket a megfelelő PIP specifikációban megfelelnek-e, és az üzleti igényeknek. Ha szükséges, frissítse az értékeket a JSON-ban, és menti a módosításokat.

## <a name="create-rosettanet-agreement"></a>RosettaNet-egyezmény létrehozása

1. Az a [az Azure portal](https://portal.azure.com), keresse meg és nyissa meg az integrációs fiók, ha nem már megnyitásához.

1. Az a **áttekintése** panelen válassza a **szerződések** csempére.

   ![Szerződések csempe kiválasztása](media/logic-apps-enterprise-integration-rosettanet/select-agreement-tile.png)

1. A **szerződések**, válassza a **Hozzáadás**. Adja meg a szerződés részletei.

   ![Szerződés adatainak hozzáadása](media/logic-apps-enterprise-integration-rosettanet/add-agreement-details.png)

   | Tulajdonság | Szükséges | Leírás |
   |----------|----------|-------------|
   | **Name (Név)** | Igen | A Szerződés neve |
   | **Szerződés típusa** | Igen | Válassza ki **RosettaNet**. |
   | **Gazdagéppartner** | Igen | Létrejött egy gazdagép és Vendég partner van szükség. A gazdagéppartner a szervezet, amely beállítja a szerződés jelöli. |
   | **Gazdagép-identitás** | Igen | A gazdagéppartner azonosítója |
   | **Vendégpartner** | Igen | Létrejött egy gazdagép és Vendég partner van szükség. A vendégpartner a szervezet, amely a fogadó partner üzleti állapotát jelöli. |
   | **Vendégidentitás** | Igen | A vendégpartner azonosítója |
   | **Beállítások** | Változó | Ezek a tulajdonságok vonatkoznak, a fogadó partner által fogadott összes üzenet |
   | **Küldési beállítások** | Változó | Ezek a tulajdonságok vonatkoznak, a fogadó partner által küldött összes üzenet |  
   | **RosettaNet PIP referenciák** | Igen | A PIP hivatkozásokat a szerződés. Üzenetek RosettaNet PIP konfigurációk van szükség. |
   ||||

1. A szerződés, a bejövő üzenetek fogadása a vendégpartner beállítani, válassza ki a **fogadási beállítások**.

   ![Beállítások](media/logic-apps-enterprise-integration-rosettanet/add-agreement-receive-details.png)

   1. Aláírási vagy titkosítási bejövő üzenetek engedélyezése a **üzenetek**válassza **üzenetet alá kell írni** vagy **üzenetnek titkosítottnak kell lennie** jelölik.

      | Tulajdonság | Szükséges | Leírás |
      |----------|----------|-------------|
      | **Üzenet alá kell írni** | Nem | A kiválasztott tanúsítványhoz bejövő üzenetek aláírását. |
      | **Tanúsítvány** | Igen, ha engedélyezve van a aláírása | Az aláíráshoz használandó tanúsítványt |
      | **Üzenettitkosítás engedélyezése** | Nem | A kiválasztott tanúsítványhoz bejövő üzenetek titkosítására. |
      | **Tanúsítvány** | Igen, ha a titkosítás engedélyezve | A titkosításhoz használandó tanúsítványt |
      ||||

   1. Minden elem alatt válassza ki a megfelelő [tanúsítvány](./logic-apps-enterprise-integration-certificates.md), amely korábban hozzáadott, az integrációs fiók, az aláírási vagy titkosítási.

1. A vendégpartner üzenetek küldéséhez a szerződéshez, válassza ki **küldési beállítások**.

   ![Küldési beállítások](media/logic-apps-enterprise-integration-rosettanet/add-agreement-send-details.png)

   1. Aláírási vagy titkosítási kimenő üzenetek engedélyezése a **üzenetek**válassza **üzenetek aláírásának engedélyezése** vagy **üzenettitkosítás engedélyezése** jelölik. Minden elem alatt válassza ki a megfelelő algoritmus és [tanúsítvány](./logic-apps-enterprise-integration-certificates.md), amely korábban hozzáadott, az integrációs fiók, az aláírási vagy titkosítási.

      | Tulajdonság | Szükséges | Leírás |
      |----------|----------|-------------|
      | **Üzenetek aláírásának engedélyezése** | Nem | A kiválasztott aláíró algoritmus és tanúsítvány kimenő üzenetek aláírását. |
      | **Aláíró algoritmus** | Igen, ha engedélyezve van a aláírása | A kiválasztott tanúsítvány alapján szeretne használni, az aláírási algoritmus |
      | **Tanúsítvány** | Igen, ha engedélyezve van a aláírása | Az aláíráshoz használandó tanúsítványt |
      | **Üzenettitkosítás engedélyezése** | Nem | A kiválasztott titkosítási algoritmus és tanúsítvány kimenő a titkosítást. |
      | **Titkosítási algoritmus** | Igen, ha a titkosítás engedélyezve | A titkosítási algoritmust használ, a kiválasztott tanúsítvány alapján |
      | **Tanúsítvány** | Igen, ha a titkosítás engedélyezve | A titkosításhoz használandó tanúsítványt |
      ||||

   1. A **végpontok**, adja meg a szükséges URL-címek műveleti üzenetek és nyugták küldéséhez használt.

      | Tulajdonság | Szükséges | Leírás |
      |----------|----------|-------------|
      | **A művelet URL-címe** |  Igen | A művelet üzenetek küldéséhez használt URL-címe. Az URL-cím mező kitöltése kötelező a szinkron és aszinkron üzeneteket is. |
      | **Visszaigazoló URL-címe** | Igen | Visszaigazoló üzenetek küldéséhez használt URL-címe. Az URL-cím mező kitöltése kötelező az aszinkron üzenetekkel. |
      ||||

1. A szerződés RosettaNet PIP hivatkozásokat a partnerek számára, válassza ki **RosettaNet PIP hivatkozik**. A **PIP neve**, válassza ki a korábban létrehozott PIP nevét.

   ![A PIP-hivatkozások](media/logic-apps-enterprise-integration-rosettanet/add-agreement-pip-details.png)

   A kijelölt tölti fel a többi tulajdonság, és az integrációs fiókjában állítsa be a PIP alapulnak. Ha szükséges, módosíthatja a **PIP szerepkör**.

   ![Kiválasztott PIP](media/logic-apps-enterprise-integration-rosettanet/add-agreement-selected-pip.png)

Miután végrehajtotta ezeket a lépéseket, készen áll RosettaNet üzeneteket küldeni vagy fogadni.

## <a name="rosettanet-templates"></a>RosettaNet sablonok

Gyorsabb fejlesztés és integrációs minták, logic app-sablonok használata dekódolása és kódolása RosettaNet üzeneteket. Amikor létrehoz egy logikai alkalmazást, a Logic App Designerben sablonkatalógus közül választhat. Ezeket a sablonokat a is megkeresheti a [GitHub-tárházat az Azure Logic Apps a](https://github.com/Azure/logicapps).

![RosettaNet sablonok](media/logic-apps-enterprise-integration-rosettanet/decode-encode-rosettanet-templates.png)

## <a name="receive-or-decode-rosettanet-messages"></a>Fogadni, vagy RosettaNet-üzenetek dekódolása

1. [Hozzon létre egy üres logikai alkalmazás](quickstart-create-first-logic-app-workflow.md).

1. [Az integrációs fiókot](logic-apps-enterprise-integration-create-integration-account.md#link-account) a logikai alkalmazáshoz.

1. Egy olyan műveletet, a RosettaNet-üzenet dekódolása hozzáadása előtt hozzá kell adnia egy eseményindítót a logikai alkalmazást, például a kérelem-eseményindítóval indítása.

1. Válassza az eseményindító felvett **új lépés**.

   ![Kérelem típusú trigger hozzáadása](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. A keresőmezőbe írja be a "rosettanet", és válassza a következő műveletet: **RosettaNet-dekódolást.**

   ![Keresse meg és válassza a "RosettaNet dekódolása" művelet](media/logic-apps-enterprise-integration-rosettanet/select-decode-rosettanet-action.png)

1. Adja meg az adatait a művelet tulajdonságait:

   ![Adja meg a művelet részletei](media/logic-apps-enterprise-integration-rosettanet/decode-action-details.png)

   | Tulajdonság | Szükséges | Leírás |
   |----------|----------|-------------|
   | **üzenet** | Igen | A RosettaNet üzenet dekódolása  |
   | **Fejlécek** | Igen | A HTTP-fejléceket, adja meg a verziót, amely az RNIF verziója, és a válasz típusát, amely azt jelzi, hogy a kommunikáció típusát, a partnerek között, és lehet szinkron vagy aszinkron az értékét |
   | **Szerepkör** | Igen | A fogadó partner a PIP szerepe |
   ||||

   A RosettaNet dekódolása műveletet, a kimenetet, és egyéb tulajdonságokat tartalmaz **kimenő jelzés**, amely lehet váltani, kódolása és térjen vissza a partner, vagy bármilyen más műveletet végrehajthat, hogy a kimenet.

## <a name="send-or-encode-rosettanet-messages"></a>Küldés vagy RosettaNet-üzenetek kódolása

1. [Hozzon létre egy üres logikai alkalmazás](quickstart-create-first-logic-app-workflow.md).

1. [Az integrációs fiókot](logic-apps-enterprise-integration-create-integration-account.md#link-account) a logikai alkalmazáshoz.

1. Hozzáadhat egy műveletet a RosettaNet üzenet kódolására, hozzá kell adnia egy eseményindítót a logikai alkalmazást, például a kérelem-eseményindítóval indítása.

1. Válassza az eseményindító felvett **új lépés**.

   ![Kérelem típusú trigger hozzáadása](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. A keresőmezőbe írja be a "rosettanet", és válassza a következő műveletet: **RosettaNet kódolása**

   ![Keresse meg és válassza a "RosettaNet kódolása" művelet](media/logic-apps-enterprise-integration-rosettanet/select-encode-rosettanet-action.png)

1. Adja meg az adatait a művelet tulajdonságait:

   ![Adja meg a művelet részletei](media/logic-apps-enterprise-integration-rosettanet/encode-action-details.png)

   | Tulajdonság | Szükséges | Leírás |
   |----------|----------|-------------|
   | **üzenet** | Igen | A RosettaNet üzenet kódolása  |
   | **Gazdagéppartner** | Igen | A partner neve |
   | **Vendégpartner** | Igen | A Vendég-partner neve |
   | **A PIP-kód** | Igen | A PIP kódot |
   | **A PIP verzióját** | Igen | A PIP verzióját |  
   | **A PIP rendszerszolgáltatásipéldány-identitás** | Igen | A PIP üzenet egyedi azonosítója |  
   | **Üzenet típusa** | Igen | Az üzenet kódolására típusa |  
   | **Szerepkör** | Igen | A gazdagéppartner szerepe |
   ||||

   A kódolt üzenet küldése a partnernek készen áll.

1. Ez a példa a kódolt üzenetet küldeni, használja a **HTTP** művelet, amely "a HTTP - partner üzenet küldése kódolású" átnevezték.

   ![HTTP-művelet RosettaNet üzenetek küldéséhez](media/logic-apps-enterprise-integration-rosettanet/send-rosettanet-message-to-partner.png)

   / RosettaNet szabványok üzleti tranzakciók minősülnek teljes csak akkor, ha teljesülnek a PIP által meghatározott összes lépését.

1. Miután a gazdagép a kódolt üzenetet küld a partner, a gazdagép megvárja, amíg a jel, valamint visszajelzés. Ennek a feladatnak, adja hozzá a **RosettaNet Várakozás a válaszra** művelet.

   !["Válasz RosettaNet várja" művelet hozzáadása](media/logic-apps-enterprise-integration-rosettanet/rosettanet-wait-for-response-action.png)

   Az időtartam, Várakozás, és az újrapróbálkozások számát az integrációs fiók a PIP-konfigurációt alapulnak. Ha nem érkezik válasz, ez a művelet létrehoz egy értesítés sikertelen. Az újrapróbálkozások kezelésére, mindig helyezze a **Encode** és **válaszra** műveletek egy **mindaddig, amíg** ciklus.

   ![UNTIL ciklus RosettaNet műveletekhez](media/logic-apps-enterprise-integration-rosettanet/rosettanet-loop.png)

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan érvényesítéséhez, alakítson át, és a többi üzenet műveletek a a [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
* További információk egyéb [Logic Apps-összekötők](../connectors/apis-list.md)
