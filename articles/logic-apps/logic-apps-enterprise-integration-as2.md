---
title: AS2-üzenetek B2B vállalati integráció – Azure Logic Apps |} A Microsoft Docs
description: AS2-üzenetek váltása a B2B vállalati integráció az Azure Logic Apps Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: c9b7e1a9-4791-474c-855f-988bd7bf4b7f
ms.date: 06/08/2017
ms.openlocfilehash: 3413b235d9202530eb1a3129637e3746bbe6585b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57872565"
---
# <a name="exchange-as2-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>AS2-üzenetek váltása a B2B vállalati integráció az Azure Logic Apps Enterprise Integration Pack

AS2-üzenetek is cserél az Azure Logic Apps, mielőtt kell létrehozni egy AS2-egyezményt és az integrációs fiókban lévő megállapodás tárolja. AS2-egyezményt létrehozásának lépései a következők.

## <a name="before-you-start"></a>Előkészületek

A következő szükséges elemek:

* Egy [integrációs fiók](../logic-apps/logic-apps-enterprise-integration-accounts.md) , amely már definiált és az Azure-előfizetéséhez társított
* Legalább két [partnerek](logic-apps-enterprise-integration-partners.md) , amely már definiálva az integrációs fiókban lévő és az AS2-minősítő a konfigurált **üzleti identitások**

> [!NOTE]
> Amikor egy szerződést hoz létre, a tartalmat a szerződés fájlban meg kell egyeznie a szerződés típusának.    

Miután [integrációs fiók létrehozásáról](../logic-apps/logic-apps-enterprise-integration-accounts.md) és [partnerek felvétele](logic-apps-enterprise-integration-partners.md), AS2-egyezményt a következő lépésekkel hozhat létre.

## <a name="create-an-as2-agreement"></a>Az AS2-egyezmény létrehozása

1.  Jelentkezzen be az [Azure Portalra](https://portal.azure.com "Azure Portal")  

2. Az Azure fő menüjéből válassza **minden szolgáltatás**. A Keresés mezőbe írja be a "integráció" névre, és válassza ki **integrációs fiókok**.

   ![Keresse meg az integrációs fiók](./media/logic-apps-enterprise-integration-as2/overview-1.png)

   > [!TIP]
   > Ha nem lát **minden szolgáltatás**, lehetséges, hogy először bontsa ki a menüben. A összecsukott menü tetején válassza **feliratok megjelenítése**.

3. A **integrációs fiókok**, válassza ki az integrációs fiók, ahol szeretné létrehozni a szerződést.

   ![Válassza ki az integrációs fiók, hová hozza létre a szerződés](./media/logic-apps-enterprise-integration-overview/overview-3.png)

4. Válassza ki a **szerződések** csempére. Ha nem rendelkezik egy szerződés-csempét, először adja hozzá a csempét.

    ![Válassza a "Szerződés" csempe](./media/logic-apps-enterprise-integration-as2/agreement-1.png)

5. A **szerződések**, válassza a **Hozzáadás**.

    ![Válassza az "Add"](./media/logic-apps-enterprise-integration-as2/agreement-2.png)

6. Alatt **Hozzáadás**, adjon meg egy **neve** esetében a szerződés hatálya alá. A **Szerződéstípus**válassza **AS2**. Válassza ki a **Gazdagéppartner**, **gazdagép-identitás**, **Vendégpartner**, és **Vendégidentitás** esetében a szerződés hatálya alá.

    ![Adja meg a szerződés részletei](./media/logic-apps-enterprise-integration-as2/agreement-3.png)  

    | Tulajdonság | Leírás |
    | --- | --- |
    | Name (Név) |A Szerződés neve |
    | Egyezmény típusa | AS2 kell lennie. |
    | Gazdagéppartner |Egy szerződést kell a gazdagép és Vendég partner. A gazdagéppartner a szervezet, amely beállítja a szerződés jelöli. |
    | Gazdagép-identitás |A gazdagéppartner azonosítója |
    | Vendégpartner |Egy szerződést kell a gazdagép és Vendég partner. A vendégpartner a szervezet, amely a fogadó partner üzleti állapotát jelöli. |
    | Vendégidentitás |A vendégpartner azonosítója |
    | Fogadási beállítások |Ezek a tulajdonságok vonatkoznak az összes, a szerződés által fogadott üzeneteket. |
    | Küldési beállítások |Ezek a tulajdonságok a szerződés által küldött összes üzenet vonatkozik. |

## <a name="configure-how-your-agreement-handles-received-messages"></a>Hogyan fogadja a szerződés kezeli az üzenetek konfigurálása

Most, hogy beállította a megállapodás tulajdonságai, konfigurálhatja, hogyan azonosítja a jelen szerződés, és kezeli a partner a jelen szerződés keretében kapott bejövő üzenetek.

1.  A **Hozzáadás**válassza **fogadási beállítások**.
Konfigurálja ezeket a tulajdonságokat a partnerrel, amely az üzeneteket, a szerződés alapján. Vlastnost leírásáért lásd: Ebben a szakaszban a táblában.

    ![Konfigurálja a "Kapják meg a beállításokat"](./media/logic-apps-enterprise-integration-as2/agreement-4.png)

2. Igény szerint, felülbírálhatja a bejövő üzenetek tulajdonságainak kiválasztásával **üzenettulajdonságok felülbírálása**.

3. Minden bejövő üzenetet alá kell írni szükséges, jelölje be **üzenetet alá kell írni**. Az a **tanúsítvány** válasszon ki egy meglévő [Vendég partner nyilvános tanúsítvány](../logic-apps/logic-apps-enterprise-integration-certificates.md) esetében az üzenetek az aláírás ellenőrzése. Vagy ha nem rendelkezik ilyennel, hozzon létre a tanúsítványt.

4.  Az összes bejövő üzenetek titkosításához szükséges, jelölje be **üzenetnek titkosítottnak kell lennie**. Az a **tanúsítvány** válasszon ki egy meglévő [fogadó partner személyes tanúsítvány](../logic-apps/logic-apps-enterprise-integration-certificates.md) bejövő üzenetek tartalomkulcsot. Vagy ha nem rendelkezik ilyennel, hozzon létre a tanúsítványt.

5. Tömörítendő üzenetek szükséges, jelölje be **üzenetnek tömörítettnek kell lennie**.

6. Szinkron disposition értesítő üzenetet (MDN) érkező üzenetek küldéséhez válassza **MDN küldése**.

7. Aláírt visszaigazolással a kapott üzeneteket küldeni, válassza ki a **aláírt MDN küldése**.

8. A fogadott üzenetek aszinkron visszaigazolással küldeni, válassza ki a **aszinkron MDN küldésének cél**.

9. Miután elkészült, ügyeljen arra, hogy a beállítások mentéséhez kiválasztásával **OK**.

Most a szerződés elkészült kezelje a bejövő üzenetek, amelyek megfelelnek a kiválasztott beállításokat.

| Tulajdonság | Leírás |
| --- | --- |
| Üzenettulajdonságok felülbírálása |Azt jelzi, hogy a fogadott üzenetek tulajdonságok felülbírálható lesz. |
| Az üzenetet alá kell írni |Szükséges az üzenetek digitális aláírással kell. A Vendég partner nyilvános tanúsítvány konfigurálása a aláírás-ellenőrzése.  |
| Az üzenetnek titkosítottnak kell lennie |Üzenetek titkosításához szükséges. A nem titkosított üzeneteket a rendszer elutasítja. A fogadó partner privát tanúsítvány konfigurálása az üzenetek visszafejtésére.  |
| Az üzenetnek tömörítettnek kell lennie |Üzenetek tömörítve van szükség. Nem-tömörített üzeneteket a rendszer elutasítja. |
| MDN-szöveg |Az alapértelmezett üzenet törlése értesítés (MDN) kell küldeni az üzenet küldője. |
| MDN küldése |Szükséges küldendő visszaigazolással. |
| Aláírt MDN küldése |Csak az aláírt visszaigazolással igényel. |
| MIC-algoritmus |Válassza ki a üzenetek aláírásához használt algoritmust. |
| Aszinkron MDN küldése | Üzenetek küldését aszinkron módon van szükség. |
| URL-cím | Az URL-címet adja meg, hova küldhetők a visszaigazolással. |

## <a name="configure-how-your-agreement-sends-messages"></a>Hogyan a szerződéshez küldi az üzeneteket konfigurálása

Beállíthatja, hogyan azonosítja a jelen szerződés, és kezeli a kimenő üzenetek küldését a jelen szerződés keretében a partnerek számára.

1.  A **Hozzáadás**válassza **küldési beállítások**.
Konfigurálja ezeket a tulajdonságokat a partnerrel, amely az üzeneteket, a szerződés alapján. Vlastnost leírásáért lásd: Ebben a szakaszban a táblában.

    ![Állítsa be a "Küldés" tulajdonságai](./media/logic-apps-enterprise-integration-as2/agreement-51.png)

2. Aláírt üzeneteket küldeni a partner, válassza ki a **üzenetek aláírásának engedélyezése**. Az üzenetek aláírására a a **MIC-algoritmust** listáról válassza ki a *fogadó partner személyes tanúsítvány MIC-algoritmust*. Majd a a **tanúsítvány** válasszon ki egy meglévő [fogadó partner személyes tanúsítvány](../logic-apps/logic-apps-enterprise-integration-certificates.md).

3. Válassza ki a titkosított üzeneteket küldeni a partner, **üzenettitkosítás engedélyezése**. Az üzenetek titkosításához a a **titkosítási algoritmus** listáról válassza ki a *Vendég partner nyilvános tanúsítvány algoritmus*.
Majd a a **tanúsítvány** válasszon ki egy meglévő [Vendég partner nyilvános tanúsítvány](../logic-apps/logic-apps-enterprise-integration-certificates.md).

4. Az üzenet tömöríteni, válassza ki a **üzenettömörítés engedélyezése**.

5. Válassza a HTTP-content-type fejléc kibontása egyetlen sorba, **kibontása HTTP-fejlécek**.

6. Szinkron visszaigazolással az elküldött üzenetek fogadásához, válassza ki a **MDN kérése**.

7. Aláírt visszaigazolással az elküldött üzenetek fogadásához, válassza ki a **aláírt MDN kérése**.

8. Aszinkron visszaigazolással az elküldött üzenetek fogadásához, válassza ki a **aszinkron MDN kérése**. Ha ezt a lehetőséget választja, adja meg az URL-címet, amelyre a visszaigazolással.

9. Nyugta nem hitelesítettek szükséges, jelölje be **NRR engedélyezése**.  

10. A MIC vagy az AS2-üzenet vagy az MDN kimenő fejléceiben aláírási algoritmus formátumát, adja meg **SHA2-algoritmus formátuma**.  

11. Miután elkészült, ügyeljen arra, hogy a beállítások mentéséhez kiválasztásával **OK**.

Most már a szerződés elkészült kezelésére, amelyek megfelelnek a kiválasztott beállításokat kimenő üzenetek.

| Tulajdonság | Leírás |
| --- | --- |
| Üzenetek aláírásának engedélyezése |Minden aláírt az egyezményben szereplő küldött üzenetek igényel. |
| MIC-algoritmus |Az üzenetek az aláíráshoz használandó algoritmus. Konfigurálja a fogadó partner privát tanúsítványt MIC-algoritmust az üzenetek aláírására. |
| Tanúsítvány |Válassza ki az üzeneteket az aláíráshoz használandó tanúsítványt. Az üzenetek aláírására tanúsítványt titkos fogadó partner konfigurálja. |
| Üzenettitkosítás engedélyezése |A jelen szerződés küldött összes üzenet titkosításához szükséges. Konfigurálja a Vendég partner nyilvános tanúsítvány algoritmus az üzenetek titkosítására. |
| Titkosítási algoritmus |Az üzenet titkosításhoz használandó titkosítási algoritmus. Konfigurálja a Vendég partner nyilvános tanúsítványt az üzenetek titkosítására. |
| Tanúsítvány |Üzenetek titkosítására használandó tanúsítvány. Konfigurálja a Vendég partner privát tanúsítványt az üzenetek titkosítására. |
| Üzenettömörítés engedélyezése |A jelen szerződés küldött összes üzenet tömörítési igényel. |
| HTTP-fejlécek kibontása |A HTTP-content-type fejléc egyetlen sor alakzatot helyezi. |
| MDN kérése |A jelen szerződés küldött összes üzenet MDN-igényel. |
| Aláírt MDN kérése |A jelen szerződés alá legyen írva küldött összes visszaigazolással igényel. |
| Aszinkron MDN kérése |Aszinkron visszaigazolással kell küldeni a jelen szerződés szükséges. |
| URL-cím |Az URL-címet adja meg, hova küldhetők a visszaigazolással. |
| NRR engedélyezése |Nyugta (NRR), a kommunikáció attribútum, amely bizonyítja nem hitelesítettek igényel, amely az adatok érkezett, a címzett. |
| SHA2-algoritmus formátuma |Válassza ki a MIC vagy az AS2-üzenet vagy az MDN kimenő fejléceiben aláírási algoritmus formátumát |

## <a name="find-your-created-agreement"></a>Keresse meg a létrehozott szerződés

1. A megállapodás tulajdonságai a beállítás befejezése után a **Hozzáadás** lapon a **OK** véglegesítse a szerződés hatálya alá, és térjen vissza az integrációs fiókban.

    Az újonnan hozzáadott szerződés most már megjelenik a **szerződések** listája.

2. Az integrációs fiókok áttekintése is megtekintheti a szerződéseket. Az integrációs fiók menüjében válassza a **áttekintése**, majd válassza ki a **szerződések** csempére. 

   ![Válassza a "Szerződés" csempe megtekintéséhez minden szerződés](./media/logic-apps-enterprise-integration-as2/agreement-6.png)

## <a name="view-the-swagger"></a>A swagger megtekintése
Tekintse meg a [részletek swagger](/connectors/as2/). 

## <a name="next-steps"></a>További lépések
* [További információ az Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "megismerheti a vállalati integrációs csomag")  
