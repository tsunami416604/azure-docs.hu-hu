---
title: Létrehozása a Dynamics 365 for Operations Cloud Partner Portalon ajánlat |} A Microsoft Docs
description: Létrehozása a Dynamics 365 for Operations ajánlat Cloud Partner Portalon keresztül
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 41f3fd55be02364b4028642db9db1d3f47043afa
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51245086"
---
# <a name="how-to-create-dynamics-365-for-operations-offer-via-cloud-partner-portal"></a>Létrehozása a Dynamics 365 for Operations ajánlat Cloud Partner Portalon keresztül

Közzétételi portál a portálon, így több személyek számára, hogy azokra az ajánlat közzétételi dolgozhatnak együtt szerepköralapú hozzáférést biztosít. Lásd: [felhőalapú portál Felhasználók kezelése](./cloud-partner-portal-manage-users.md) további információ.

Mielőtt ajánlat nevében a közzétevő tehetők közzé fiókra, egyéni felhasználók számára az egyik \"tulajdonosa\" szerepkör szükséges ahhoz, hogy elfogadja a [használati feltételek](https://azure.microsoft.com/support/legal/website-terms-of-use/), [Microsoft adatvédelmi nyilatkozatát](https://www.microsoft.com/privacystatement/default.aspx), és [Microsoft Azure Certified Program szerződése](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).

## <a name="how-to-create-a-new-dynamics-365-for-operations-offer"></a>Hogyan hozhat létre egy új Dynamics 365 for Operations ajánlat

Miután az összes az előfeltételek teljesülnek, készen áll a Dynamics 365 for Operations ajánlat szerzői műveleteket.

1. Jelentkezzen be a [Cloud Partner Portalon](http://cloudpartner.azure.com/).
2. A bal oldali navigációs sávban kattintson a \"+ új ajánlat\" válassza \"Dynamics 365 for Operations\".
3. Egy új ajánlat \"szerkesztő\" megtekintése most megnyílik az Ön számára, és szerzői készen vagyunk.
4. A \"űrlapok\" meg kell adni, hogy szükség belül a bal oldalon láthatók a \"szerkesztő\" megtekintése. Minden egyes \"űrlap\" automatikusan kitölti a mezőket egy készlete áll. Szükséges, piros csillaggal jelölt mezők (\*).

Van egy Dynamics 365 for Operations ajánlat készítéséhez négy fő űrlapok:

- Ajánlat beállításai
- Technikai információ
- Storefront részletei
- Kapcsolatok

## <a name="how-to-fill-out-the-offer-settings-form"></a>Az ajánlat beállításainak űrlap kitöltésére.

Az ajánlat beállítások képernyő művelet egy alapszintű az ajánlat beállításainak megadásához. A különböző mezők az alábbiakban tekintheti át.

### <a name="offer-id"></a>Ajánlat azonosítója

Ez a közzétevő-profilon belül az ajánlat egyedi azonosítója. Ezt az Azonosítót meg fognak jelenni a termék URL-címeket. Csak kisbetűs alfanumerikus karakterekből és kötőjelekből (-) állhat. Az azonosító nem végződhet kötőjellel, és legfeljebb 50 karakter hosszú lehet. Ez a mező után egy ajánlatot élesíti zárolva van.

például ha egy kiadó contoso-közzétevő létrehoz egy ajánlatot az ajánlat azonosítója *minta-dynamics365 műveletek*, ez fog megjelenni az appsource-ban, \"https://appsource.microsoft.com/marketplace/apps/**contoso**.*minta – dynamics 365 for operations\*? lapon = áttekintése\"

### <a name="publisher-id"></a>Közzétevő azonosítója

A legördülő listából válassza ki ezt az ajánlatot a közzétenni kívánt közzétevő profilt teszi lehetővé. Ez a mező után egy ajánlatot élesíti zárolva van.

Name (Név)

Ez az ajánlat megjelenített neve. Ez a név fog megjelenni a [AppSource](https://appsource.microsoft.com). Legfeljebb 50 karakterből állhat.

Kattintson a \"mentése\" a folyamat mentéséhez. Töltse ki az ajánlatban technikai információ lehet a következő lépéssel.

## <a name="how-to-fill-out-the-technical-info-form"></a>Hogyan kell kitölteni a műszaki adatok képernyő

Technikai információ formájában jelenik meg az ajánlat oldalon információt tartalmazza. A különböző mezők útmutatást az alábbiakban tekintheti át.

![Új ajánlat képernyő](./media/publish_d365_new_offer/Technical_info.png)

### <a name="solution-identifier"></a>Megoldás azonosítója

A megoldás azonosító először van.

1. Ez az azonosító megkereséséhez nyissa meg életciklus-szolgáltatások, és válassza ki a megoldás felügyeleti.
2. Miután kiválasztotta a megfelelő megoldás, látni fogja a csomag áttekintésében megoldás azonosítóját. \*\*Ha azonosítója üres, válassza a Szerkesztés, és tegye közzé ismét a csomagot a megoldás azonosítójának jelenik meg.

### <a name="validation-assets"></a>Érvényesítési eszközök

Töltse fel ide az autó (testreszabási Problémaelemző jelentések).

### <a name="does-solution-enable-translations"></a>Megoldás lehetővé teszi a translation(s)?

Válassza ki \'Igen\' vagy \'nem\'.

### <a name="does-solution-include-localizations"></a>Megoldás tartalmaz Localization(s)?

Válassza ki \'Igen\' vagy \'nem\'.

### <a name="product-version"></a>Termékverzió

Válassza ki az új AX. Végül kattintson a Mentés gombra.

## <a name="how-to-fill-out-storefront-details-form"></a>Hogyan kirakat részletek képernyő kitöltéséhez.

Először is az ajánlat részletei.

1. **Ajánlat-összefoglaló**

    \- Adja meg a röviden összefoglalja, hogy a megoldás (maximális 100 karakter).

2. **Az ajánlat leírása**

    \- Adja meg a megoldás rövid leírása. A leírás kell rendelkeznie, hogy a megoldás a működési erőforrás-igényű, és közvetlenül kell igazítani a BPM könyvtárhoz. Ha például Ön tegyük fel, hogy rendelkezik-e funkciók x, y, a – z, a marketing-tartalom esetén a végső lektorálásra, azt biztosítja, hogy ezek a belül LCS BPM könyvtárban vannak dokumentálva során.

![kirakat részletek képernyő](./media/publish_d365_new_offer/offer_details.png)

### <a name="listing-details"></a>Lista részletei

![kirakat részletek képernyő](./media/publish_d365_new_offer/storefront_details.png)

1. **Iparág** – ellenőrizze a megadott lehetőségek közül két iparágak legfeljebb.
2. **Kategóriák** – ellenőrizze a maximális száma az adott közül három kategóriába.
3. **Alkalmazás típusa** – a megadott lehetőségek közül választhat.
4. **Az alkalmazás súgóhivatkozás** – adja meg a Súgó hivatkozásra a megoldáshoz.
5. **Támogatott országok/régiók** – ellenőrizze a megadott lehetőségek közül.
6. **Támogatott nyelvek** – ellenőrizze a megadott lehetőségek közül.
7. **Alkalmazásverzió** – adja meg a megoldás kiadásra. (például 1.0.0.0)
8. **Alkalmazás kiadási dátum** -kiadás adja meg a solution(mm/dd/yyyy) dátuma.
9. **Az alkalmazás együttműködik termékek** -lista-specifikus termékek, amely az alkalmazás működik együtt. Legfeljebb három termék listázhatja. Termék listázásához, kattintson a a melletti plusz jelre (új), és a egy új nyílt szövegmező jön létre egy terméket, amely együttműködik az alkalmazás nevét adhatja meg.
10. **Keresés kulcsszavak** – adja meg a felhasználók használhatják a megoldás megkeresése a keresés során gyakran használt kifejezések. \*\*Ezek a kulcsszavak nem jelenik meg a Marketplace-en.
11. **Kulcs elrejtése** – Ez az, hogy milyen kulcs, amely akkor értékkel egyesítve jön létre az ajánlat előnézeti URL-cím a nyilvánosság elől való elrejtéséhez. Akkor sem a jelszót. Bármilyen karakterlánc itt is megadhatja.

### <a name="marketing-artifacts"></a>Marketing-összetevők

1. Ezután tölti fel a **emblémák**, **képernyőképek**. \*\*Vegye figyelembe, hogy minden feltöltött méretei, és az összes rendszerkép PNG formátumúnak kell lennie.
2. **Bemutató videók** – kattintson a \"+ új\". A megoldás (YouTube vagy Vimeo hivatkozások csak) bemutató videó feltöltése. \*\*. Vegye figyelembe, hogy tanácsos feltölteni egy megadott méretű miniatűr, átmeneti jelennek meg a videót.
3. **Dokumentumok** – töltse fel a megoldáshoz kapcsolódó dokumentumokat, és adja meg a dokumentum nevét.

### <a name="legal"></a>Jogi tudnivalók

Ez az információ társítani fogja az adatvédelmi szabályzatot, és a használati feltételeket. Adja meg a megoldás adatvédelmi szabályzat URL-CÍMÉT, és a használati feltételek. \*\*Az ügyfél lesznek láthatók ezek a szabályzatok a portálon.

### <a name="customer-support"></a>Ügyfélszolgálat

A támogatási URL-cím csak akkor lesz látható a portálon a felhasználók által.

### <a name="leads-management"></a>Érdeklődők kezelése

Válassza ki a CRM-rendszerrel, ahol vezető rendszer tárolja. Válassza ki \"Azure Table\" itt, ha rendelkezik ilyennel, a következő CRM rendszereket: Salesforce, a Marketo, a Microsoft Dynamics CRM-hez. A CRM-rendszerrel, itt, ahol írunk a végfelhasználók számára, hogy kipróbálják az alkalmazását az appsource-ban (érdeklődők) részleteit. Attól függően, a CRM-rendszerrel választja kattintson a megfelelő URL-címet, végezze el a következő mezők információt.

![Érdeklődő felügyeleti részletei](./media/publish_d365_new_offer/leads.png)

1. Tekintse meg az Azure Table [Itt](https://aka.ms/leadsettingforazuretable)
2. Dynamics CRM online, tekintse meg [Itt](https://aka.ms/leadsettingfordynamicscrm)
3. A Marketo [Itt](https://aka.ms/leadsettingformarketo)
4. Tekintse meg a Salesforce [Itt](https://aka.ms/leadsettingforsalesforce)

## <a name="how-to-fill-out-the-contacts-form"></a>Útmutató az ügyfelek űrlap kitöltésekor.

Ezeket az adatokat kell használni a Microsoft és az ügyfél támogatásához. Adja meg az Engineering Contact és ügyfél-támogatási a vállalat és a megoldás támogatási URL-CÍMÉT. Ezeket az adatokat kell használni, pontként egy egyetlen, ha a Microsoft rendelkezik egy kérdést a megoldásról, valamint az ügyfélszolgálat.

![Az ajánlat partnerek képernyő](./media/publish_d365_new_offer/Contacts.png)
