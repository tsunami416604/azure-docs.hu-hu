---
title: Fizikai kiszolgálók előkészítése az értékeléshez és a Migrálás Azure Migrate
description: Ismerje meg, hogyan készítheti elő a fizikai kiszolgálók értékelését és áttelepítését Azure Migrate használatával.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 363549662a17a87513c8426347909142ee405cae
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196403"
---
# <a name="prepare-for-assessment-and-migration-of-physical-servers-to-azure"></a>Felkészülés a fizikai kiszolgálók Azure-ba történő értékelésére és áttelepítésére

Ez a cikk azt ismerteti, hogyan lehet felkészülni a helyszíni fizikai kiszolgálók értékelésére [Azure Migrate](migrate-services-overview.md)használatával.

[Azure Migrate](migrate-overview.md) olyan eszközöket biztosít, amelyek segítségével az alkalmazások, az infrastruktúra és a munkaterhelések felderíthető, mérhetők és áttelepíthetők a Microsoft Azure. A hub Azure Migrate eszközöket és külső gyártótól származó független szoftvergyártó (ISV) ajánlatokat tartalmaz. 

Ez az oktatóanyag az első egy olyan sorozatban, amely bemutatja, hogyan kell felmérni a fizikai kiszolgálókat a Azure Migrateokkal. Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Készítse elő az Azure-t. Állítsa be az Azure-fiókra és-erőforrásokra vonatkozó engedélyeket a Azure Migrate való együttműködéshez.
> * A helyszíni fizikai kiszolgálók előkészítése a kiszolgálók értékeléséhez.


> [!NOTE]
> Az oktatóanyagok bemutatják a forgatókönyvek legegyszerűbb telepítési útvonalát, így gyorsan beállíthatja a rendszer megvalósíthatóságát. Az oktatóanyagok az alapértelmezett beállításokat használják, ahol lehetséges, és nem jelennek meg az összes lehetséges beállítás és elérési út. Részletes utasításokért tekintse át a fizikai kiszolgálók értékelésének útmutatója című témakört.


Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) a virtuális gép létrehozásának megkezdése előtt.


## <a name="prepare-azure"></a>Az Azure előkészítése

### <a name="azure-permissions"></a>Azure-engedélyek

Be kell állítania a Azure Migrate telepítéséhez szükséges engedélyeket.

- Az Azure-fiók engedélyei egy Azure Migrate projekt létrehozásához.
- A fiókra vonatkozó engedélyek a Azure Migrate berendezés regisztrálásához. A készülék a Hyper-V felderítésére és áttelepítésére szolgál. A készülék regisztrálása során Azure Migrate két Azure Active Directory (Azure AD) alkalmazást hoz létre, amelyek egyedileg azonosítják a készüléket:
    - Az első alkalmazás a Azure Migrate szolgáltatási végpontokkal kommunikál.
    - A második alkalmazás a regisztráció során létrehozott Azure Key Vault fér hozzá az Azure AD-alkalmazás adatainak és a készülék konfigurációs beállításainak tárolásához.



### <a name="assign-permissions-to-create-project"></a>Engedélyek kiosztása projekt létrehozásához

Győződjön meg arról, hogy rendelkezik Azure Migrate-projekt létrehozásához szükséges engedélyekkel.

1. A Azure Portal nyissa meg az előfizetést, és válassza a **hozzáférés-vezérlés (iam)** lehetőséget.
2. A **hozzáférés engedélyezése**területen keresse meg a megfelelő fiókot, és kattintson rá az engedélyek megtekintéséhez.
3. **Közreműködői** vagy **tulajdonosi** engedélyekkel kell rendelkeznie.
    - Ha csak az ingyenes Azure-fiókot hozta létre, akkor Ön az előfizetés tulajdonosa.
    - Ha nem Ön az előfizetés tulajdonosa, a tulajdonossal társítsa a szerepkört.


### <a name="assign-permissions-to-register-the-appliance"></a>Engedélyek kiosztása a készülék regisztrálásához

A következő módszerek egyikével hozhat létre engedélyeket a Azure Migrate számára a berendezés regisztrálása során létrehozott Azure AD-alkalmazások létrehozásához:

- A bérlők/globális rendszergazdák engedélyeket adhatnak a bérlő felhasználói számára az Azure AD-alkalmazások létrehozásához és regisztrálásához.
- A bérlői/globális rendszergazda hozzárendelheti az alkalmazás fejlesztői szerepkörét (amely rendelkezik engedélyekkel) a fiókhoz.

Érdemes megjegyezni, hogy:

- Az alkalmazások nem rendelkeznek más hozzáférési engedélyekkel az előfizetéshez a fent leírtak kivételével.
- Az új berendezések regisztrálásához csak ezekre az engedélyekre van szükség. Az engedélyeket a készülék beállítása után távolíthatja el.


#### <a name="grant-account-permissions"></a>Fiók engedélyeinek megadása

A bérlő/globális rendszergazda a következőképpen adhat meg engedélyeket:

1. Az Azure AD-ben a bérlői/globális rendszergazdának **Azure Active Directory** > **felhasználó** > **felhasználói beállításokat**kell megkeresnie.
2. A rendszergazdának az **Igen**értékre kell állítania a **Alkalmazásregisztrációk** .

    ![Azure AD-engedélyek](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> Ez egy alapértelmezett beállítás, amely nem érzékeny. [Részletek](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

#### <a name="assign-application-developer-role"></a>Alkalmazás fejlesztői szerepkörének kiosztása

A bérlő/globális rendszergazda hozzárendelheti az alkalmazás fejlesztői szerepkörét egy fiókhoz. [Részletek](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).


## <a name="prepare-for-physical-server-assessment"></a>Felkészülés a fizikai kiszolgáló értékelésére

A fizikai kiszolgáló értékelésének előkészítéséhez ellenőriznie kell a fizikai kiszolgáló beállításait, és ellenőriznie kell a berendezések központi telepítésének beállításait:

### <a name="verify-physical-server-settings"></a>Fizikai kiszolgáló beállításainak ellenőrzése

1. Ellenőrizze a [fizikai kiszolgálóra vonatkozó követelményeket](migrate-support-matrix-physical.md#assessment-physical-server-requirements) a kiszolgálók értékeléséhez.
2. Győződjön meg arról, hogy a [szükséges portok](migrate-support-matrix-physical.md#assessment-port-requirements) meg vannak nyitva a fizikai kiszolgálókon.


### <a name="verify-appliance-settings"></a>Berendezés beállításainak ellenőrzése

A Azure Migrate berendezés beállítása és az értékelés megkezdése előtt a következő oktatóanyagban készítse elő a berendezés üzembe helyezését.

1. A berendezésre vonatkozó követelmények [ellenőrzése](migrate-support-matrix-physical.md#assessment-appliance-requirements) .
2. [Tekintse át](migrate-support-matrix-physical.md#assessment-appliance-url-access) azokat az Azure URL-címeket, amelyekhez a készüléknek hozzá kell férnie.
3. Tekintse át a berendezés által a felderítés és az értékelés során gyűjtött adatokat.
4. [Jegyezze](migrate-support-matrix-physical.md#assessment-port-requirements) fel a port hozzáférési követelményeit a készülékhez.


### <a name="set-up-an-account-for-physical-server-discovery"></a>Fiók beállítása a fizikai kiszolgáló felderítéséhez

Azure Migrate a helyszíni kiszolgálók felderítéséhez szükséges engedélyekkel.

- **Windows:** Állítson be egy helyi felhasználói fiókot az összes olyan Windows-kiszolgálón, amelyet fel szeretne venni a felderítésbe. A felhasználói fiókot fel kell venni a következő csoportokba:-távfelügyeleti felhasználók – Teljesítményfigyelő felhasználók – Teljesítménynapló felhasználói
- **Linux:** Szüksége lesz egy rendszergazdai fiókra a felderíteni kívánt Linux-kiszolgálókon.


## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban az alábbiakat végezte el:

> [!div class="checklist"]
> * Azure-fiók engedélyeinek beállítása.
> * Előkészített fizikai kiszolgálók az értékeléshez.

Folytassa a következő oktatóanyaggal, amely létrehoz egy Azure Migrate projektet, és felméri a fizikai kiszolgálókat az Azure-ba való áttelepítésre

> [!div class="nextstepaction"]
> [Fizikai kiszolgálók értékelése](./tutorial-assess-physical.md)
