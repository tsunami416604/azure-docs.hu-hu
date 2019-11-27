---
title: Felügyeleti eszköz üzembe helyezése – Azure
description: Felhasználói felületi eszköz telepítése a Windows rendszerű virtuális asztali erőforrások kezeléséhez.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 11/09/2019
ms.author: helohr
ms.openlocfilehash: ad0c67cea6a5a9b487cd47aa7c10d10da1438050
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384282"
---
# <a name="tutorial-deploy-a-management-tool"></a>Oktatóanyag: felügyeleti eszköz üzembe helyezése

A felügyeleti eszköz felhasználói felületet (UI) biztosít a Microsoft virtuális asztali erőforrásainak kezeléséhez. Ebből az oktatóanyagból megtudhatja, hogyan helyezheti üzembe és csatlakozhat a felügyeleti eszközhöz.

>[!NOTE]
>Ezek az utasítások egy olyan Windows rendszerű virtuális asztali konfigurációra vonatkoznak, amelyet a szervezet meglévő folyamataihoz használhat.

## <a name="important-considerations"></a>Fontos szempontok

Mivel az alkalmazásnak hozzá kell járulnia a Windows virtuális asztal használatához, az eszköz nem támogatja a vállalatközi (B2B) forgatókönyveket. Minden Azure Active Directory (HRE) bérlő előfizetéséhez a felügyeleti eszköz saját külön üzembe helyezésére lesz szükség.

Ez a felügyeleti eszköz egy minta. A Microsoft fontos biztonsági és minőségi frissítéseket biztosít. A [forráskód a githubon érhető el](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy). Az ügyfelek és a partnerek számára ajánlott az eszköz testreszabása az üzleti igényeknek megfelelően.

A következő böngészők kompatibilisek a felügyeleti eszközzel:
- Google Chrome 68 vagy újabb verzió
- Microsoft Edge 40,15063 vagy újabb verzió
- Mozilla Firefox 52,0 vagy újabb verzió
- Safari 10 vagy újabb (csak macOS)

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>Mire van szükség a Azure Resource Manager sablon futtatásához

A Azure Resource Manager sablon telepítése előtt szüksége lesz egy Azure Active Directory felhasználóra a felügyeleti felhasználói felület telepítéséhez. A felhasználónak a következőket kell tennie:

- Az Azure Multi-Factor Authentication (MFA) le van tiltva
- Engedéllyel rendelkezik erőforrások létrehozásához az Azure-előfizetésében
- Engedéllyel rendelkezik Azure AD-alkalmazás létrehozásához. A következő lépésekkel ellenőrizheti, hogy a felhasználó rendelkezik-e a [szükséges engedélyekkel](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

A Azure Resource Manager sablon üzembe helyezése után el kell indítania a felügyeleti felhasználói felületet az ellenőrzéshez. A felhasználónak a következőket kell tennie:
- Szerepkör-hozzárendeléssel megtekintheti vagy szerkesztheti a Windows rendszerű virtuális asztali bérlőt

## <a name="run-the-azure-resource-manager-template-to-provision-the-management-ui"></a>A felügyeleti felhasználói felület kiépítéséhez futtassa a Azure Resource Manager sablont

Mielőtt elkezdené, győződjön meg arról, hogy a kiszolgáló és az ügyfélalkalmazások beleegyeznek a Azure Active Directory (HRE) által képviselt [Windows virtuális asztali engedélyezési oldalára](https://rdweb.wvd.microsoft.com) .

Az Azure Resource Management-sablon üzembe helyezéséhez kövesse az alábbi utasításokat:

1. Nyissa meg a [GitHub Azure RDS-templates oldalt](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy).
2. A sablon üzembe helyezése az Azure-ban.
    - Ha vállalati előfizetésben végez üzembe helyezést, görgessen le, és válassza **az üzembe helyezés az Azure**-ban lehetőséget. Lásd: [útmutató a sablon paramétereinek](#guidance-for-template-parameters)megtekintéséhez.
    - Ha felhőalapú megoldás-szolgáltatói előfizetést használ, kövesse az alábbi utasításokat az Azure-ba történő üzembe helyezéshez:
        1. Görgessen le, és kattintson **a jobb gombbal az Azure**-ba, majd válassza a **hivatkozás helyének másolása**lehetőséget.
        2. Nyisson meg egy szövegszerkesztőt, például a jegyzettömböt, és illessze be a hivatkozást.
        3. Közvetlenül a <https://portal.azure.com/> után és a hashtag (#) előtt adjon meg egy kukac (@) jelet, majd a bérlői tartománynevet. Íme egy példa a következő formátumra: <https://portal.azure.com/@Contoso.onmicrosoft.com#create/>.
        4. Jelentkezzen be a Azure Portal felhasználóként rendszergazdai/közreműködői engedélyekkel a felhőalapú megoldás-szolgáltató előfizetéséhez.
        5. Illessze be a szövegszerkesztőbe másolt hivatkozást a címsorba.

### <a name="guidance-for-template-parameters"></a>Útmutató a sablon paramétereinek megadásához
A következő módon adhatja meg az eszköz konfigurálásához szükséges paramétereket:

- A **isServicePrincipal** paraméternél válassza a **false (hamis**) lehetőséget.
- A hitelesítő adatok esetében adja meg a Azure Active Directory hitelesítő adatait a multi-Factor Authentication szolgáltatás letiltásával. Ezek a hitelesítő adatok az Azure-ba való bejelentkezéshez és az Azure AD-alkalmazás és az Azure-webalkalmazás-erőforrások létrehozásához használhatók. További információ: [Mi szükséges a Azure Resource Manager sablon futtatásához](#what-you-need-to-run-the-azure-resource-manager-template).
- Az **applicationName**esetében használjon egy egyedi nevet az alkalmazás számára, amely regisztrálva lesz a Azure Active Directoryban. Ezt a nevet a rendszer a webalkalmazás URL-címére is felhasználja. Használhatja például a "Apr3UX" nevet.

## <a name="provide-consent-for-the-management-tool"></a>Adja meg a felügyeleti eszköz beleegyezikét

A GitHub-Azure Resource Manager sablon befejezése után egy olyan erőforráscsoportot talál, amely a Azure Portal egy app Service-csomaggal együtt két app Services-csomagot tartalmaz.

A bejelentkezés és a felügyeleti eszköz használata előtt meg kell adnia a felügyeleti eszközhöz társított új Azure Active Directory alkalmazáshoz való hozzájárulásukat. A beleegyezik, hogy lehetővé teszi a felügyeleti eszköz számára, hogy a Windows virtuális asztali felügyeleti hívásokat az eszközbe bejelentkezett felhasználó nevében végezze el.

![Képernyőkép, amely a felhasználói felületi felügyeleti eszközhöz való hozzáféréskor megadott engedélyeket mutatja.](media/management-ui-delegated-permissions.png)

Az eszközre való bejelentkezéshez használható felhasználó meghatározásához lépjen a [Azure Active Directory felhasználói beállítások lapra](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) , és jegyezze fel, hogy a felhasználók milyen értékkel férhetnek hozzá a **vállalati adatokhoz a nevükben**.

![Képernyőkép, amely azt mutatja, hogy a felhasználók megadhatnak-e jóváhagyást az alkalmazásoknak csak a felhasználó számára.](media/management-ui-user-consent-allowed.png)

- Ha az érték értéke **Igen**, akkor a Azure Active Directory bármely felhasználói fiókjával bejelentkezhet, és csak az adott felhasználó beleegyezett. Ha azonban később egy másik felhasználóval jelentkezik be a felügyeleti eszközre, akkor újra kell végrehajtania ugyanezt a hozzájárulásukat.
- Ha a **nem**értékre van állítva, akkor a Azure Active Directory globális rendszergazdájaként kell bejelentkeznie, és rendszergazdai jogosultsággal kell rendelkeznie a címtár összes felhasználója számára. Más felhasználók nem fognak megjelenni a jóváhagyásban.


Ha eldöntötte, hogy melyik felhasználót fogja használni a beleegyezikés megadásához, kövesse az alábbi utasításokat az eszköz beleegyezikének biztosításához:

1. Nyissa meg az Azure-erőforrásokat, válassza ki az Azure App Services erőforrást a sablonban megadott névvel (például Apr3UX), és keresse meg a hozzá társított URL-címet. például <https://rdmimgmtweb-210520190304.azurewebsites.net>.
2. Jelentkezzen be a megfelelő Azure Active Directory felhasználói fiókkal.
3. Ha globális rendszergazdai jogosultsággal rendelkezik, most bejelölheti a **szervezet nevében**való engedélyezéshez szükséges jelölőnégyzetet. Az **elfogadás** lehetőséget választva adja meg a beleegyező értéket.
   
   ![A felhasználó vagy a rendszergazda által megjelenő teljes körű beleegyezikés oldalra mutató képernyőkép.](media/management-ui-consent-page.png)

Ekkor a felügyeleti eszközre kerül.

## <a name="use-the-management-tool"></a>A felügyeleti eszköz használata

Miután beleegyezett a szervezetbe vagy egy adott felhasználóhoz, bármikor hozzáférhet a felügyeleti eszközhöz.

Az eszköz elindításához kövesse az alábbi utasításokat:

1. Válassza ki az Azure App Services erőforrást a sablonban megadott névvel (például Apr3UX), és navigáljon a hozzá társított URL-címhez. például <https://rdmimgmtweb-210520190304.azurewebsites.net>.
2. Jelentkezzen be a Windows rendszerű virtuális asztali hitelesítő adataival.
3. Amikor a rendszer kéri, hogy válasszon ki egy bérlői csoportot, válassza ki az **alapértelmezett bérlői csoportot** a legördülő listából.
4. Amikor kiválasztja az alapértelmezett bérlői csoportot, az ablak jobb oldalán megjelenik egy menü. Ezen a menüben keresse meg a bérlői csoport nevét, és jelölje ki.

> [!NOTE]
> Ha egyéni bérlői csoporttal rendelkezik, a legördülő listából válassza a nevet manuálisan.

## <a name="report-issues"></a>Problémák jelentése

Ha problémák merülnek fel a felügyeleti eszközzel vagy más Windowsos virtuális asztali eszközökkel kapcsolatban, kövesse az [ARM-sablonok](https://github.com/Azure/RDS-Templates/blob/master/README.md) című témakör utasításait a githubon való jelentéséhez távoli asztali szolgáltatások.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan helyezheti üzembe és csatlakozhat a felügyeleti eszközhöz, megtudhatja, hogyan használhatja a Azure Service Healtht a szolgáltatási problémák és az állapot-útmutatók figyelésére.

> [!div class="nextstepaction"]
> [A szolgáltatási riasztások oktatóanyagának beállítása](./set-up-service-alerts.md)
