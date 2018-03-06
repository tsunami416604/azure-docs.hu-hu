---
title: "Oktatóanyag – Hozzáférések hozzárendelése az Azure Cost Managementben | Microsoft Docs"
description: "Ebben az oktatóanyagban megtudhatja, hogyan rendelhet hozzáféréseket a költségkezelési adatokhoz olyan felhasználói fiókokkal, amelyek meghatározzák az entitások hozzáférési szintjeit."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 02/27/2018
ms.topic: tutorial
ms.service: cost-management
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 0e2edc946c5d6ada1049fbd6a960ec138f7088f2
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
---
# <a name="tutorial-assign-access-to-cost-management-data"></a>Oktatóanyag: Hozzáférések hozzárendelése költségkezelési adatokhoz

A költségkezelési adatokhoz való hozzáférés felhasználó- vagy entitáskezeléssel biztosítható. A Cloudyn felhasználói fiókok határozzák meg az *entitásokhoz* és az adminisztratív funkciókhoz való hozzáférést. Két típusú hozzáférés létezik: rendszergazdai és felhasználói. Hacsak az adott felhasználónál nincs máshogy beállítva, a rendszergazdai hozzáférés lehetővé teszi a Cloudyn portál összes funkciójának korlátlan használatát, beleértve a felhasználókezelést, a címzettlisták kezelését és a gyökérszintű entitás-hozzáférést az entitások összes adatához. A felhasználói hozzáférés a végfelhasználóknak van szánva, hogy jelentéseket tekinthessenek meg és állíthassanak össze az általuk elérhető entitásadatok alapján.

Az entitások az üzleti szervezet hierarchikus struktúráját hivatottak leképezni. A cég részlegeit, osztályait és csapatait jelölik a Cloudynban. Az entitáshierarchia segítségével pontosan nyomon követheti az entitások kiadásait.

Az Azure-szerződés vagy -fiók regisztrálásakor a rendszer létrehozott egy rendszergazdai jogosultságokkal rendelkező fiókot a Cloudynban, így az oktatóanyag összes lépését végrehajthatja. Az oktatóanyag a költségkezelési adatokhoz való hozzáférést ismerteti, beleértve a felhasználó- és entitáskezelést. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Rendszergazdai hozzáféréssel rendelkező felhasználó létrehozása
> * Felhasználói hozzáféréssel rendelkező felhasználó létrehozása
> * Entitások létrehozása

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

- Rendelkeznie kell egy Azure-fiókkal.
- Rendelkeznie kell az Azure Cost Management próbaverziójával vagy fizetett előfizetésével.

## <a name="create-a-user-with-admin-access"></a>Rendszergazdai hozzáféréssel rendelkező felhasználó létrehozása

Bár Ön eleve rendelkezik rendszergazdai hozzáféréssel, előfordulhat, hogy erre a cég más munkatársainak is szüksége lesz. A Cloudyn portálon kattintson a fogaskerék szimbólumra a jobb felső sarokban, és válassza a **User Management** (Felhasználókezelés) lehetőséget. Új felhasználó hozzáadásához kattintson az **Add New User** (Új felhasználó hozzáadása) gombra.

Adja meg a felhasználóra vonatkozó kötelező adatokat. A jelszó mezőt üresen hagyhatja, így a felhasználó állíthatja be a jelszót az első bejelentkezésekor. A **Notify user by email** (Felhasználó értesítése e-mailben) lehetőségre kattintva a Cloudyn egy e-mailt küld a felhasználónak a bejelentkezési információkkal. Adjon felhasználókezelési jogosultságokat, ha azt szeretné, hogy a felhasználó létrehozhasson és módosíthasson más felhasználókat. A címzettlisták kezelésére vonatkozó jogosultsággal a felhasználó szerkesztheti a címzettlistákat.

A **User has admin access** (A felhasználó rendszergazdai hozzáféréssel rendelkezik) alatt a cég gyökérszintű entitása van kiválasztva. Hagyja kiválasztva a gyökérszintű entitást, és mentse a felhasználói adatokat. A gyökérszintű entitás kiválasztásával a felhasználó nem csupán a fa legfelső szintű entitásához fér majd hozzá, hanem az össze az alá tartozóhoz is.  
  ![rendszergazdai hozzáféréssel rendelkező új felhasználó hozzáadása](.\media\tutorial-user-access\new-admin-access.png)

## <a name="create-a-user-with-user-access"></a>Felhasználói hozzáféréssel rendelkező felhasználó létrehozása
A költségvetési adatokhoz irányítópult- és jelentésszintű hozzáférést igénylő felhasználóknak felhasználói jogosultságra van szükségük ezek megtekintéséhez. Hozzon létre egy új felhasználót a rendszergazdai hozzáféréssel rendelkező felhasználóhoz hasonló felhasználói hozzáféréssel, a következő eltérésekkel:

- Törölje az **Allow User Management** (Felhasználókezelés engedélyezése) és az **Allow Recipient lists Management** (Címzettlisták kezelésének engedélyezése) beállítás, valamint a **User has admin access** (A felhasználó rendszergazdai hozzáféréssel rendelkezik) lista összes beállításának jelölését.
- Jelölje be a **User has user access** (A felhasználó felhasználói hozzáféréssel rendelkezik) listában azokat az entitásokat, amelyekhez a felhasználónak hozzáféréssel kell rendelkeznie.
- Emellett igény szerint egyes entitásokhoz rendszergazdai hozzáférést is adhat.

![felhasználói hozzáféréssel rendelkező új felhasználó hozzáadása](.\media\tutorial-user-access\new-user-access.png)

A felhasználók hozzáadásáról szóló oktatóvideó: [Felhasználók felvétele az Azure Cost Management by Cloudyn rendszerébe](https://youtu.be/Nzn7GLahx30).

## <a name="create-entities"></a>Entitások létrehozása

A költségentitás-hierarchia meghatározásakor az az ajánlott eljárás, ha felméri a cég felépítését.

A fa létrehozásakor mérlegelje, miként szeretné követni vagy miként kell követnie a költségeket üzleti egységenként, költséghelyenként, környezetenként és értékesítési részlegenként elkülönítve. A Cloudyn entitásfája az entitásöröklésnek köszönhetően rugalmas. A felhőfiókok egyéni előfizetései adott entitásokhoz vannak csatolva. Az entitások tehát több-bérlősek. Az egyes felhasználókat az entitások használatával hozzárendelheti csak a vállalat őket érintő szegmenseihez. Így az adatok elkülönítve maradnak, akár kisebb, akár nagyobb egységekről, például a leányvállalatokról van szó. Az adatok elkülönítése segít abban, hogy kézben tarthassa az irányítást.  

Az Azure-szerződés vagy -fiók a Cloudynben való regisztrálásakor az előfizetett Azure-erőforrások adatait, többek között a használatra, a teljesítményre, a számlázásra és a címkékre vonatkozó adatokat a rendszer átmásolta a Cloudyn-fiókjába. Az entitásfát azonban manuálisan kell létrehoznia. Ha kihagyta az Azure Resource Manager-regisztrációt, csak a számlázási adatok és néhány adategység-jelentés érhetők el a Cloudyn portálján.

A Cloudyn portálon kattintson a **Settings** (Beállítások) gombra a jobb felső sarokban, és válassza a **Cloud Accounts** (Felhőbeli fiókok) lehetőséget. Egyetlen entitásból (a gyökérből) kell kiindulni, és ebből kell kiépíteni a teljes entitásfát. Példa egy entitáshierarchiára, amely számos informatika vállalatéhoz hasonlóan fog kinézni, amint a fa elkészül:

![entitásfa](.\media\tutorial-user-access\entity-tree.png)

Az **Entities** (Entitások) elem mellett kattintson az **Add Entity** (Entitás hozzáadása) gombra. Adja meg a hozzáadni kívánt személy vagy részleg adatait. A **Full Name** (Teljes név) és az **Email** (E-mail-cím) mezőknek nem szükséges meglévő felhasználók adatait tartalmaznia. Ha meg szeretné tekinteni a hozzáférési szintek listáját, keressen rá a Súgóban az *Adding an entity* (Entitások hozzáadása) témakörre.

![entitás hozzáadása](.\media\tutorial-user-access\add-entity.png)

Amikor elkészült, **mentse** az entitást.


A költségentitás-hierarchiák létrehozásáról szóló oktatóvideó: [Költségentitás-hierarchia létrehozása az Azure Cost Management by Cloudynben](https://youtu.be/dAd9G7u0FmU).

Ha Ön Azure Nagyvállalati Szerződés alá tartozó felhasználó, tekintse meg a fiókok és előfizetések entitásokkal való társításáról szóló videót: [Csatlakozás az Azure Resource Managerhez az Azure Cost Management by Cloudynben](https://youtu.be/oCIwvfBB6kk).

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Rendszergazdai hozzáféréssel rendelkező felhasználó létrehozása
> * Felhasználói hozzáféréssel rendelkező felhasználó létrehozása
> * Entitások létrehozása

Ha még nem engedélyezte az Azure Resource Manager API-hozzáférést a fiókokhoz, folytassa a következő cikkel.

> [!div class="nextstepaction"]
> [Azure-előfizetések és -fiókok aktiválása](activate-subs-accounts.md)
