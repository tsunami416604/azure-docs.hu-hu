---
title: Azure Monitor-munkafüzetek használata az Azure AD tartományi szolgáltatásokkal | Microsoft dokumentumok
description: Megtudhatja, hogy miként tekintheti át a biztonsági naplózásokat az Azure Active Directory tartományi szolgáltatások felügyelt tartományában az Azure Monitor munkafüzetek használatával.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/18/2020
ms.author: iainfou
ms.openlocfilehash: bdfc7d37d99dc5511f47e33d1848c3f142a9693e
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654470"
---
# <a name="review-security-audit-events-in-azure-ad-domain-services-using-azure-monitor-workbooks"></a>Biztonsági naplózási események áttekintése az Azure AD tartományi szolgáltatásokban az Azure Monitor munkafüzetek használatával

Az Azure Active Directory tartományi szolgáltatások (Azure AD DS) által felügyelt tartomány állapotának megismerése érdekében engedélyezheti a biztonsági naplózási eseményeket. Ezek a biztonsági naplózási események ezután áttekinthetők az Azure Monitor munkafüzetek használatával, amelyek a szöveget, az elemzési lekérdezéseket és a paramétereket gazdag interaktív jelentésekké kombinálják. Az Azure AD DS munkafüzetsablonokat tartalmaz a biztonsági áttekintéshez és a fióktevékenységhez, amelyek lehetővé teszik a naplózási események beásására és a környezet kezelésére.

Ez a cikk bemutatja, hogyan használhatja az Azure Monitor munkafüzetek biztonsági naplózási események az Azure AD DS-ben.

## <a name="before-you-begin"></a>Előkészületek

A cikk végrehajtásához a következő erőforrásokra és jogosultságokra van szükség:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Az előfizetéshez társított Azure Active Directory-bérlő, amely et egy helyszíni könyvtárral vagy egy csak felhőbeli könyvtárral szinkronizált.
    * Szükség esetén [hozzon létre egy Azure Active Directory-bérlőt,][create-azure-ad-tenant] vagy [társítson egy Azure-előfizetést a fiókjához.][associate-azure-ad-tenant]
* Az Azure Active Directory tartományi szolgáltatások felügyelt tartomány a konfigurált és konfigurált az Azure AD-bérlő.
    * Szükség esetén töltse ki az oktatóanyagot [az Azure Active Directory tartományi szolgáltatások példányának létrehozásához és konfigurálásához.][create-azure-ad-ds-instance]
* Az Azure Active Directory tartományi szolgáltatások által felügyelt tartományban engedélyezett biztonsági naplózási események, amelyek adatokat továbbítanak egy Log Analytics-munkaterületre.
    * Szükség esetén [engedélyezze az Azure Active Directory tartományi szolgáltatások biztonsági naplózását.][enable-security-audits]

## <a name="azure-monitor-workbooks-overview"></a>Az Azure Monitor-munkafüzetek áttekintése

Ha a biztonsági naplózási események be vannak kapcsolva az Azure AD DS-ben, nehéz lehet elemezni és azonosítani a felügyelt tartomány problémáit. Az Azure Monitor lehetővé teszi, hogy ezeket a biztonsági naplózási eseményeket összesítse, és lekérdezze az adatokat. Az Azure Monitor munkafüzetek segítségével vizualizálhatja ezeket az adatokat, hogy gyorsabban és egyszerűbben azonosíthassa a problémákat.

A munkafüzetsablonok válogatott jelentések, amelyeket több felhasználó és csoport rugalmas újrafelhasználására terveztek. Amikor megnyit egy munkafüzetsablont, az Azure Monitor környezetből származó adatok betöltődnek. A sablonokat anélkül is használhatja, hogy az hatással lenne a szervezet más felhasználóira, és a sablon alapján mentheti saját munkafüzeteit.

Az Azure AD DS a következő két munkafüzetsablont tartalmazza:

* Biztonsági áttekintő jelentés
* Partnertevékenység-jelentés

A munkafüzetek szerkesztéséről és kezeléséről az [Azure Monitor munkafüzetek – áttekintés című témakörben olvashat bővebben.](../azure-monitor/platform/workbooks-overview.md)

## <a name="use-the-security-overview-report-workbook"></a>A biztonsági áttekintő jelentés munkafüzetének használata

A használat jobb megértése és a potenciális biztonsági fenyegetések azonosítása érdekében a biztonsági áttekintő jelentés összefoglalja a bejelentkezési adatokat, és azonosítja azokat a fiókokat, amelyeket esetleg ellenőrizni szeretne. Megtekintheti az eseményeket egy adott dátumtartományban, és részletezheti az adott bejelentkezési eseményeket, például a rossz jelszókísérleteket, vagy ahol a fiók le van tiltva.

A biztonsági áttekintő jelentés munkafüzetsablonjának eléréséhez hajtsa végre az alábbi lépéseket:

1. Keresse meg és válassza ki az **Azure Active Directory tartományi szolgáltatásokat** az Azure Portalon.
1. Válassza ki a felügyelt tartományt, például *aaddscontoso.com*
1. A bal oldali menüben válassza a **Figyelés > munkafüzetek**

    ![Válassza a Munkafüzetek menüt az Azure Portalon](./media/use-azure-monitor-workbooks/select-workbooks-in-azure-portal.png)

1. Válassza ki a **Biztonsági áttekintés jelentést**.
1. A munkafüzet tetején található legördülő menükből válassza ki az Azure-előfizetést, majd az Azure Monitor munkaterületét. Válasszon **egy időtartományt**, például *az Elmúlt 7 napot.*

    ![Válassza a Munkafüzetek menüt az Azure Portalon](./media/use-azure-monitor-workbooks/select-query-filters.png)

    A **Mozaik nézet** és a Diagram **nézet** beállításai is módosíthatók az adatok igény szerint való elemzéséhez és megjelenítéséhez

1. Egy adott eseménytípusba való részletezéshez válassza ki a **Bejelentkezési eredménykártyák** egyikét, például a *Zárolt fiók,* akövetkező példában látható módon:

    ![Példa biztonsági áttekintő jelentés adataira az Azure Monitor munkafüzeteiben](./media/use-azure-monitor-workbooks/example-security-overview-report.png)

1. A biztonsági áttekintő jelentés alsó része a diagram alatt lebontja a kiválasztott tevékenységtípust. A jobb oldalon szereplő felhasználónevek szerint szűrhet, ahogy az a következő példajelentésben látható:

    [![](./media/use-azure-monitor-workbooks/account-lockout-details-cropped.png "Details of account lockouts in Azure Monitor Workbooks")](./media/use-azure-monitor-workbooks/account-lockout-details.png#lightbox)

## <a name="use-the-account-activity-report-workbook"></a>A fióktevékenység-jelentés munkafüzetének használata

Egy adott felhasználói fiókkal kapcsolatos problémák elhárítása érdekében a fióktevékenység-jelentés lebontja a részletes naplónapló-információkat. Tekintse át, ha egy rossz felhasználónév vagy jelszó volt megadva a bejelentkezés során, és a bejelentkezési kísérlet forrása.

A fióktevékenység-jelentés munkafüzetsablonjának eléréséhez hajtsa végre az alábbi lépéseket:

1. Keresse meg és válassza ki az **Azure Active Directory tartományi szolgáltatásokat** az Azure Portalon.
1. Válassza ki a felügyelt tartományt, például *aaddscontoso.com*
1. A bal oldali menüben válassza a **Figyelés > munkafüzetek**
1. Válassza ki a **Partnertevékenység-jelentést**.
1. A munkafüzet tetején található legördülő menükből válassza ki az Azure-előfizetést, majd az Azure Monitor munkaterületét. Válasszon egy **időtartományt**, például *az Elmúlt 30 napot,* majd azt, hogy a **Csempe nézet** hogyan képviselje az adatokat. Szűrhet **fiók felhasználóneve**, például *felix*, ahogy az a következő példajelentésben látható:

    [![](./media/use-azure-monitor-workbooks/account-activity-report-cropped.png "Account activity report in Azure Monitor Workbooks")](./media/use-azure-monitor-workbooks/account-activity-report.png#lightbox)

    A diagram alatti területen az egyes bejelentkezési események, valamint a tevékenység eredménye és a forrás munkaállomás információkat. Ez az információ segíthet meghatározni a bejelentkezési események ismétlődő forrásait, amelyek fiókzárolást okozhatnak, vagy potenciális támadást jelezhetnek.

A biztonsági áttekintő jelentéshez ugyanúgy, ha a jelentés tetején lévő különböző csempékbe is leáshat, hogy szükség szerint megjelenítse és elemezze az adatokat.

## <a name="save-and-edit-workbooks"></a>Munkafüzetek mentése és szerkesztése

Az Azure AD DS által biztosított két sablonmunkafüzet jó kiindulópont a saját adatelemzéssel. Ha részletesebb adatokat kell beolvasnia az adatlekérdezésekben és vizsgálatokban, mentheti saját munkafüzeteit, és szerkesztheti a lekérdezéseket.

1. A munkafüzetsablonok másolatának mentéséhez jelölje be a Szerkesztés **> a Mentés > megosztott jelentésekként**lehetőséget, majd adjon meg egy nevet, és mentse azt.
1. A sablon saját példányából válassza a **Szerkesztés** lehetőséget a szerkesztési módba való belépéshez. A jelentés bármely része mellett **kiválaszthatja** a kék Szerkesztés gombot, és módosíthatja azt.

Az Azure Monitor-munkafüzetek összes diagramja és táblája Kusto-lekérdezések használatával jön létre. A saját lekérdezések létrehozásáról további információt az [Azure Monitor naplólekérdezései][azure-monitor-queries] és [a Kusto-lekérdezések oktatóanyaga című témakörben talál.][kusto-queries]

## <a name="next-steps"></a>További lépések

Ha módosítania kell a jelszó- és zárolási házirendeket, olvassa el [a Felügyelt tartományok jelszó- és fiókzárolási házirendjecímű témakört.][password-policy]

A felhasználókkal kapcsolatos problémákról megtudhatja, hogy miként háríthatók el [a fiókbejelentkezési vagy][troubleshoot-sign-in] [fiókzárolási problémák.][troubleshoot-account-lockout]

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[enable-security-audits]: security-audit-events.md
[password-policy]: password-policy.md
[troubleshoot-sign-in]: troubleshoot-sign-in.md
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-monitor-queries]: ../azure-monitor/log-query/query-language.md
[kusto-queries]: https://docs.microsoft.com/azure/kusto/query/tutorial?pivots=azuredataexplorer
