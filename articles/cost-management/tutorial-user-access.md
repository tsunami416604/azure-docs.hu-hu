---
title: Oktatóanyag – Hozzáférések hozzárendelése a Cloudynban | Microsoft Docs
description: Ebben az oktatóanyagban megtudhatja, hogyan rendelhet hozzáféréseket a Cloudyn-adatokhoz olyan felhasználói fiókokkal, amelyek meghatározzák az entitások hozzáférési szintjeit.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/18/2019
ms.topic: tutorial
ms.service: cost-management
ms.custom: seodec18
manager: benshy
ms.openlocfilehash: e9bd7fb147f9789120776bc603768b4ace59e693
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58181078"
---
# <a name="tutorial-assign-access-to-cloudyn-data"></a>Oktatóanyag: A Cloudyn-adatokhoz való hozzáférés hozzárendelése

A Cloudyn-adatokhoz való hozzáférés felhasználó- vagy entitáskezeléssel biztosítható. A Cloudyn felhasználói fiókok határozzák meg az *entitásokhoz* és az adminisztratív funkciókhoz való hozzáférést. Két típusú hozzáférés létezik: rendszergazdai és felhasználói. Hacsak az adott felhasználónál nincs máshogy beállítva, a rendszergazdai hozzáférés lehetővé teszi a Cloudyn portál összes funkciójának korlátlan használatát, beleértve a felhasználókezelést, a címzettlisták kezelését és a gyökérszintű entitás-hozzáférést az entitások összes adatához. A felhasználói hozzáférés a végfelhasználóknak van szánva, hogy jelentéseket tekinthessenek meg és állíthassanak össze az általuk elérhető entitásadatok alapján.

Az entitások az üzleti szervezet hierarchikus struktúráját hivatottak leképezni. A cég részlegeit, osztályait és csapatait jelölik a Cloudynban. Az entitáshierarchia segítségével pontosan nyomon követheti az entitások kiadásait.

Az Azure-szerződés vagy -fiók regisztrálásakor a rendszer létrehozott egy rendszergazdai jogosultságokkal rendelkező fiókot a Cloudynban, így az oktatóanyag összes lépését végrehajthatja. Az oktatóanyag a Cloudyn-adatokhoz való hozzáférést ismerteti, beleértve a felhasználó- és entitáskezelést. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Rendszergazdai hozzáféréssel rendelkező felhasználó létrehozása
> * Felhasználói hozzáféréssel rendelkező felhasználó létrehozása
> * Felhasználó törlése
> * Személyes adatok törlése vagy exportálása
> * Entitások létrehozása és felügyelete


Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

- Rendelkeznie kell egy Azure-fiókkal.
- Rendelkeznie kell a Cloudyn próbaverziójával vagy fizetett előfizetésével.

## <a name="create-a-user-with-admin-access"></a>Rendszergazdai hozzáféréssel rendelkező felhasználó létrehozása

Bár Ön eleve rendelkezik rendszergazdai hozzáféréssel, előfordulhat, hogy erre a cég más munkatársainak is szüksége lesz. A Cloudyn portálon kattintson a fogaskerék szimbólumra a jobb felső sarokban, és válassza a **User Management** (Felhasználókezelés) lehetőséget. Új felhasználó hozzáadásához kattintson az **Add New User** (Új felhasználó hozzáadása) gombra.

Adja meg a felhasználóra vonatkozó kötelező adatokat. A **bejelentkezési azonosítónak** egy érvényes e-mail-címnek kell lennie. Adjon felhasználókezelési jogosultságokat, ha azt szeretné, hogy a felhasználó létrehozhasson és módosíthasson más felhasználókat. A címzettlisták kezelésére vonatkozó jogosultsággal a felhasználó szerkesztheti a címzettlistákat. A **Notify user by email** (Felhasználó értesítése e-mailben) lehetőségre kattintva a Cloudyn egy e-mailt küld a felhasználónak a bejelentkezési információkkal. Az első bejelentkezéskor a felhasználó beállít egy jelszót.

A **User has admin access** (A felhasználó rendszergazdai hozzáféréssel rendelkezik) alatt a cég gyökérszintű entitása van kiválasztva. Hagyja kiválasztva a gyökérszintű entitást, és mentse a felhasználói adatokat. A gyökérszintű entitás kiválasztásával a felhasználó nem csupán a fa legfelső szintű entitásához fér majd hozzá, hanem az össze az alá tartozóhoz is.  
  ![Új felhasználó hozzáadása mezőbe a rendszergazdai hozzáférés megjelenítő példa](./media/tutorial-user-access/new-admin-access.png)

## <a name="create-a-user-with-user-access"></a>Felhasználói hozzáféréssel rendelkező felhasználó létrehozása
A Cloudyn-adatokhoz irányítópult- és jelentésszintű hozzáférést igénylő felhasználóknak felhasználói jogosultságra van szükségük ezek megtekintéséhez. Hozzon létre egy új felhasználót a rendszergazdai hozzáféréssel rendelkező felhasználóhoz hasonló felhasználói hozzáféréssel, a következő eltérésekkel:

- Törölje az **Allow User Management** (Felhasználókezelés engedélyezése) és az **Allow Recipient lists Management** (Címzettlisták kezelésének engedélyezése) beállítás, valamint a **User has admin access** (A felhasználó rendszergazdai hozzáféréssel rendelkezik) lista összes beállításának jelölését.
- Jelölje be a **User has user access** (A felhasználó felhasználói hozzáféréssel rendelkezik) listában azokat az entitásokat, amelyekhez a felhasználónak hozzáféréssel kell rendelkeznie.
- Emellett igény szerint egyes entitásokhoz rendszergazdai hozzáférést is adhat.

![Új felhasználó hozzáadása jelölőnégyzetet a felhasználói hozzáférés megjelenítő példa](./media/tutorial-user-access/new-user-access.png)

A felhasználók hozzáadásáról szóló oktatóvideó: [Felhasználók hozzáadása a Cloudyn rendszerébe](https://youtu.be/Nzn7GLahx30).

## <a name="delete-a-user"></a>Felhasználó törlése

Felhasználó törlésekor érintetlen marad az összes olyan entitás, amelyhez a felhasználó hozzáféréssel rendelkezik. A felhasználó törlésekor a mentett *személyes* jelentések el lesznek távolítva. Az adott felhasználó által létrehozott, mentett *nyilvános* jelentések nem lesznek törölve.

Felhasználóként saját magát nem távolíthatja el.

> [!WARNING]
> A felhasználótörlési műveletet nem lehet visszavonni.

1.  A Cloudyn portálon kattintson a fogaskerék szimbólumra a jobb felső sarokban, és válassza a **User Management** (Felhasználókezelés) lehetőséget.
2.  A felhasználók listájában jelölje ki a törölni kívánt felhasználót, majd kattintson a **Delete User** (Felhasználó törlése) lehetőségre (a kuka szimbólumra).
3.  A felhasználótörlési párbeszédpanelen kattintson a **Yes** (Igen), majd az **OK** gombra.


## <a name="delete-or-export-personal-data"></a>Személyes adatok törlése vagy exportálása

Ha személyes adatokat kíván törölni vagy exportálni a Cloudynről, akkor létre kell hoznia egy támogatási jegyet. A létrehozott támogatási jegy hivatalos kérelemnek (adatalany általi kérelemnek) minősül. A Microsoft ekkor azonnal eltávolítja a fiókot, beleértve az ügyfél- és a személyes adatokat is. Az adatai törlésére vagy exportálására irányuló kérelem létrehozásának lépéseit a [Cloudyn-adatok adatalany általi kérelmeit](https://www.cloudyn.com/cloudyn-gdpr-requests) ismertető részben tekintheti meg.

## <a name="create-and-manage-entities"></a>Entitások létrehozása és felügyelete

A költségentitás-hierarchia meghatározásakor az az ajánlott eljárás, ha felméri a cég felépítését. Az entitásokkal külön fiókonként vagy előfizetésenként szegmentálhatja a költségeket. Költségentitások létrehozásával logikai csoportokat alkothat a költségek kezelésére és nyomon követésére. A fa létrehozásakor mérlegelje, miként szeretné követni vagy miként kell követnie a költségeket üzleti egységenként, költséghelyenként, környezetenként és értékesítési részlegenként elkülönítve. A Cloudyn entitásfája az entitásöröklésnek köszönhetően rugalmas.

A felhőfiókok egyéni előfizetései adott entitásokhoz vannak csatolva. Hozzárendelhet egy entitást a felhőszolgáltatói fiókjához vagy előfizetéséhez. Az entitások tehát több-bérlősek. Az egyes felhasználókat az entitások használatával hozzárendelheti csak a vállalat őket érintő szegmenseihez. Így az adatok elkülönítve maradnak, akár kisebb, akár nagyobb egységekről, például a leányvállalatokról van szó. Az adatok elkülönítése segít abban, hogy kézben tarthassa az irányítást.  

Az Azure-szerződés vagy -fiók a Cloudynben való regisztrálásakor az előfizetett Azure-erőforrások adatait, többek között a használatra, a teljesítményre, a számlázásra és a címkékre vonatkozó adatokat a rendszer átmásolta a Cloudyn-fiókjába. Az entitásfát azonban manuálisan kell létrehoznia. Ha kihagyta az Azure Resource Manager-regisztrációt, csak a számlázási adatok és néhány adategység-jelentés érhetők el a Cloudyn portálján.

A Cloudyn portálon kattintson a fogaskerék ikonra a jobb felső sarokban, és válassza a **Cloud Accounts** (Felhőbeli fiókok) lehetőséget. Egyetlen entitásból (a gyökérből) kell kiindulni, és ebből kell kiépíteni a teljes entitásfát. Példa egy entitáshierarchiára, amely számos informatika vállalatéhoz hasonlóan fog kinézni, amint a fa elkészül:

![Az a fiókkezelés oldalán látható entitásfát – példa](./media/tutorial-user-access/entity-tree.png)

Az **Entities** (Entitások) elem mellett kattintson az **Add Entity** (Entitás hozzáadása) gombra. Adja meg a hozzáadni kívánt személy vagy részleg adatait. A **Full Name** (Teljes név) és az **Email** (E-mail-cím) mezőknek nem szükséges meglévő felhasználók adatait tartalmaznia. Ha meg szeretné tekinteni a hozzáférési szintek listáját, keressen rá a Súgóban az *Adding an entity* (Entitások hozzáadása) témakörre.

![Entitás hozzáadása mezőbe a nevet és hozzáférési szintek entitást megjelenítő példa](./media/tutorial-user-access/add-entity.png)

Amikor elkészült, **mentse** az entitást.

### <a name="entity-access-levels"></a>Entitás hozzáférési szintjei

Az entitások hozzáférési szintjeivel és a felhasználói hozzáférésekkel együtt definiálható, hogy milyen típusú műveletek legyenek elérhetők a Cloudyn portálon.

- **Vállalati** – Lehetővé teszi a gyermek költségentitások létrehozását és felügyeletét.
- **Vállalati + költséglefoglalási** – Lehetővé teszi a gyermek költségentitások létrehozását és felügyeletét, beleértve az összevont fiókok költséglefoglalását.
- **Vállalati, költségek a szülő költséglefoglalása alapján** – Lehetővé teszi a gyermek költségentitások létrehozását és felügyeletét. A fiók költségei a szülő költséglefoglalási modelljén alapulnak.
- **Csak egyéni irányítópultok** – Csak az előre definiált egyéni irányítópultok megtekintését engedi a felhasználónak.
- **Csak irányítópultok** – Csak az irányítópultok megtekintését engedi a felhasználónak.

### <a name="create-a-cost-entity-hierarchy"></a>Költségentitás-hierarchia létrehozása

Költségentitás-hierarchia létrehozásához vállalati vagy vállalati + költséglefoglalási hozzáférésű fiókkal kell rendelkeznie.

A Cloudyn portálon kattintson a fogaskerék ikonra a jobb felső sarokban, és válassza a **Cloud Accounts** (Felhőbeli fiókok) lehetőséget. A bal oldali panelen megjelenik az **Entitások** fa. Szükség esetén bontsa ki az entitásfát, ha nem látja a fiókkal társítani kívánt entitást.  A felhőszolgáltatói fiókok füleken láthatók a jobb oldali ablaktáblán. Válassza ki valamelyik fület, majd húzzon egy fiókot vagy előfizetést az entitásra, és ott engedje el. Az **Áthelyezés** mező tájékoztatja, ha a fiók sikeresen át lett helyezve. Kattintson az **OK** gombra.

Több fiókot is társíthat egy adott entitáshoz. Válassza ki a fiókokat, majd kattintson az **Áthelyezés** elemre. A Fiókok áthelyezése mezőben válassza ki az entitást, ahová a fiókot át szeretné helyezni, majd kattintson a **Mentés** elemre. A Fiókok áthelyezése mező megerősítést kér, hogy valóban át szeretné-e helyezni a fiókokat. Kattintson az **Igen**, majd az **OK** gombra.

A költségentitás-hierarchiák létrehozásáról szóló oktatóvideó: [Költségentitás-hierarchia létrehozása a Cloudynban](https://youtu.be/dAd9G7u0FmU).

Ha Ön Azure Nagyvállalati Szerződés alá tartozó felhasználó, tekintse meg a fiókok és előfizetések entitásokkal való társításáról szóló videót: [Csatlakozás az Azure Resource Managerhez a Cloudynnal](https://youtu.be/oCIwvfBB6kk).

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Rendszergazdai hozzáféréssel rendelkező felhasználó létrehozása
> * Felhasználói hozzáféréssel rendelkező felhasználó létrehozása
> * Felhasználó törlése
> * Személyes adatok törlése vagy exportálása
> * Entitások létrehozása és felügyelete


Ha még nem engedélyezte az Azure Resource Manager API-hozzáférést a fiókokhoz, folytassa a következő cikkel.

> [!div class="nextstepaction"]
> [Azure-előfizetések és -fiókok aktiválása](activate-subs-accounts.md)
