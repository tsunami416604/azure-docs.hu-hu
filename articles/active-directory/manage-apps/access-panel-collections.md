---
title: Gyűjtemények létrehozása a Saját alkalmazások portáljaihoz az Azure Active Directoryban | Microsoft dokumentumok
description: A Saját alkalmazások gyűjtemények segítségével testreszabhatja a Saját alkalmazások oldalakat, hogy egyszerűbb encikbe használhassa a végfelhasználókat. Az alkalmazásokat külön lapokkal csoportokba rendezheti.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2020
ms.author: mimart
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: c91b9ffc9e3487e492c91cb0f5825d0b725f9410
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120096"
---
# <a name="create-collections-on-the-my-apps-portal"></a>Gyűjtemények létrehozása a Saját alkalmazások portálon

A felhasználók a My Apps portálsegítségével megtekinthetik és elindíthatják azokat a felhőalapú alkalmazásokat, amelyekhez hozzáféréssel rendelkeznek. Alapértelmezés szerint a felhasználó által elérhető összes alkalmazás egy oldalon jelenik meg. Ha azure AD Premium P1 vagy P2 licenccel rendelkezik, akkor beállíthat gyűjteményeket, hogy jobban rendszerezhesse ezt az oldalt a felhasználók számára, ha rendelkezik Egy Azure AD Premium P1 vagy P2 licenccel. Gyűjtemény esetén csoportosíthatja a kapcsolódó alkalmazásokat (például feladatszerepkör, feladat vagy projekt szerint), és külön lapon jelenítheti meg őket. A gyűjtemény lényegében egy szűrőt alkalmaz azokra az alkalmazásokra, amelyekhez a felhasználó már hozzáférhet, így a felhasználó csak a gyűjteményben lévő, hozzájuk rendelt alkalmazásokat látja.

> [!NOTE]
> Ez a cikk bemutatja, hogy a rendszergazda hogyan engedélyezheti és hozhat létre gyűjteményeket. A végfelhasználó számára a Saját alkalmazások portál és gyűjtemények használatáról az [Access és a gyűjtemények használata](https://docs.microsoft.com/azure/active-directory/user-help/my-applications-portal-workspaces)című témakörben talál további információt.

## <a name="enable-the-latest-my-apps-features"></a>A Legújabb Saját alkalmazások funkció engedélyezése

1. Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be felhasználói rendszergazdaként vagy globális rendszergazdaként.

2. Nyissa meg az **Azure Active Directory** > **felhasználói beállításait.**

3. A **Felhasználói funkciók előnézete csoportban**válassza **a Felhasználói funkciók előnézeti beállításainak kezelése**lehetőséget.

4. A **Felhasználók a Saját alkalmazások előzetes verzióiban**az alábbi lehetőségek közül választhat:
   * **Kiválasztva** – Engedélyezi egy adott csoport szolgáltatásait. A **Csoport kiválasztása** beállítással jelölje ki azt a csoportot, amelyhez engedélyezni szeretné a szolgáltatásokat.  
   * **Minden** – Engedélyezi a funkciókat az összes felhasználó számára.

> [!NOTE]
> A Saját alkalmazások portál megnyitásához a `https://myapps.microsoft.com` felhasználók használhatják a szervezethez `https://myapps.microsoft.com/contoso.com`szükséges hivatkozást vagy testreszabott hivatkozást, például . Miután engedélyezte az új Saját alkalmazások felületet, a **Frissített alkalmazások szolgáltatás elérhető** szalagcím a Saját alkalmazások lap tetején jelenik meg, és a felhasználók a **Kipróbálás** lehetőség lehetőséget választva megtekinthetik az új felületet. Az új felület használatának leállításához a felhasználók az oldal tetején található **Új élmény után** szalagcím közül az **Igen** lehetőséget választhatják.

## <a name="create-a-collection"></a>Gyűjtemény létrehozása

Egy gyűjtemény létrehozásához rendelkeznie kell egy Azure AD Premium P1 vagy P2 licenc.

1. Nyissa meg az [**Azure Portalon,**](https://portal.azure.com/) és jelentkezzen be rendszergazdaként egy Azure AD Premium P1 vagy P2 licenccel.

2. Nyissa meg az **Azure Active Directory** > **Enterprise Applications alkalmazást.**

3. A **Kezelés csoportban**válassza **a Gyűjtemények**lehetőséget.

4. Válassza az **Új gyűjtemény lehetőséget.** Az **Új gyűjtemény** lapon adja meg a gyűjtemény **nevét** (azt javasoljuk, hogy ne használja a "gyűjtemény" kifejezést a nevében. Ezután adja meg a **Megnevezést.**

   ![Új gyűjteménylap](media/acces-panel-collections/new-collection.png)

5. Válassza az **Alkalmazások** **+ Add application**lapot. **Add applications** **Search**

   ![Alkalmazás hozzáadása a gyűjteményhez](media/acces-panel-collections/add-applications.png)

6. Ha befejezte az alkalmazások hozzáadását, válassza a **Hozzáadás**lehetőséget. Megjelenik a kijelölt alkalmazások listája. A felfelé mutató nyilakkal módosíthatja a listában szereplő alkalmazások sorrendjét. Ha egy alkalmazást lefelé szeretne helyezni, vagy törölni szeretné a gyűjteményből, válassza az **Egyebek** menüt (**...**).

7. Válassza a **Tulajdonosok** **+ Add users and groups**lapot. **Add users and groups** Ha befejezte a felhasználók és csoportok kiválasztását, válassza a **Kijelölés lehetőséget.**

9. Válassza a **Felhasználók és** **+ Add users and groups**csoportok lapot. **Add users and groups** Vagy használja a **Keresőmezőt** a felhasználók vagy csoportok megkereséséhez. Ha befejezte a felhasználók és csoportok kiválasztását, válassza a **Kijelölés lehetőséget.**

   ![Felhasználók és csoportok hozzáadása](media/acces-panel-collections/add-users-and-groups.png)

11. Válassza **a Véleményezés + Létrehozás lehetőséget.** Megjelennek az új gyűjtemény tulajdonságai.


## <a name="view-audit-logs"></a>Auditnaplók megtekintése

A naplózási naplók rögzítik a Saját alkalmazások gyűjtemények műveleteket, beleértve a gyűjtemény létrehozása végfelhasználói műveleteket. A következő események jönnek létre a Saját alkalmazások ból:

* Gyűjtemény létrehozása
* Gyűjtemény szerkesztése
* Gyűjtemény törlése
* Alkalmazás indítása (végfelhasználó)
* Önkiszolgáló alkalmazások hozzáadása (végfelhasználó)
* Önkiszolgáló alkalmazások törlése (végfelhasználó)

Az [Azure Portalon](https://portal.azure.com) található naplózási naplók eléréséhez válassza az **Azure Active Directory** > **vállalati alkalmazások** > **naplózási naplói a** tevékenység szakaszban. A **Szolgáltatás területen**válassza a Saját **alkalmazások**lehetőséget.

## <a name="get-support-for-my-account-pages"></a>Támogatás a Saját fiók laphoz

A Saját alkalmazások lapon a felhasználó **kiválaszthatja** > a**Fiókmegtekintése A fiókomat** a fiókbeállítások megnyitásához lehetőséget. Az Azure AD **saját fiók** lapon a felhasználók kezelhetik biztonsági adataikat, eszközeiket, jelszavukat és egyebeiket. Az Office-fiók beállításaihoz is hozzáférhetnek.

Abban az esetben, ha támogatási kérelmet kell benyújtania az Azure AD-fiók vagy az Office-fiók lapjával kapcsolatos probléma esetén, kövesse az alábbi lépéseket, hogy a kérés megfelelően legyen irányítva: 

* Az Azure **AD "Saját fiók"** lapproblémák esetén nyisson meg egy támogatási kérelmet az Azure Portalon belül. Nyissa meg az **Azure Portal** > **Azure Active Directory** > **új támogatási kérelmét.**

* A **"Saját fiók" lap office-verziójával** kapcsolatos problémákesetén nyisson meg egy támogatási kérelmet a Microsoft 365 Felügyeleti központból. Látogasson el a **Microsoft 365 Felügyeleti központ** > **támogatási szolgálatához.** 

## <a name="next-steps"></a>További lépések
[Végfelhasználói élmény az Azure Active Directoryban lévő alkalmazásokszámára](end-user-experiences.md)