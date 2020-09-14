---
title: Példány és hitelesítés beállítása (portál)
titleSuffix: Azure Digital Twins
description: 'Lásd: az Azure Digital Twins szolgáltatás példányának beállítása a Azure Portal használatával'
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 12b0dd957cb89fc7114c752312b08f1e490499da
ms.sourcegitcommit: c94a177b11a850ab30f406edb233de6923ca742a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89280026"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-portal"></a>Azure digitális Twins-példány és-hitelesítés beállítása (portál)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Ez a cikk az **új Azure Digital Twins-példány beállításának**lépéseit ismerteti, beleértve a példány létrehozását és a hitelesítés beállítását. A cikk elvégzése után egy Azure Digital Twins-példánnyal kell elkezdenie a programozást.

A cikk jelen verziója ezeket a lépéseket manuálisan, egyenként, a Azure Portal használatával hajtja végre. Az Azure Portal egy egységesített felületet biztosító webes konzol, amely alternatívát biztosít a parancssori eszközökkel szemben.
* Ha ezeket a lépéseket manuálisan szeretné használni a parancssori felületről, tekintse meg a jelen cikk CLI-verzióját: [*útmutató: példány és hitelesítés (CLI) beállítása*](how-to-set-up-instance-cli.md).
* Ha az üzembe helyezési parancsfájl mintájának használatával szeretne automatikus telepítést végezni, tekintse meg a jelen cikk parancsfájlokkal ellátott verzióját: [*útmutató: példány és hitelesítés beállítása (megírt)*](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-steps-prereq.md](../../includes/digital-twins-setup-steps-prereq.md)]

## <a name="create-the-azure-digital-twins-instance"></a>Az Azure Digital Twins-példány létrehozása

Ebben a szakaszban az **Azure Digital Twins új példányát fogja létrehozni** a [Azure Portal](https://ms.portal.azure.com/)használatával. Lépjen a portálra, és jelentkezzen be a hitelesítő adataival.

A portálon először kattintson az _erőforrás létrehozása_ elemre az Azure-szolgáltatások Kezdőlap menüjében.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-resource.png" alt-text="Válassza az erőforrás létrehozása lehetőséget a Azure Portal kezdőlapján.":::

Keressen az *Azure Digital Twins* kifejezésre a keresőmezőbe, majd az eredmények közül válassza ki az **Azure Digital Twins (előzetes verzió)** szolgáltatást. Válassza a _Létrehozás_ gombot a szolgáltatás új példányának létrehozásához.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins.png" alt-text="A Create (létrehozás) elem kiválasztása az Azure Digital Twins szolgáltatás oldaláról":::

A következő *Erőforrás-létrehozási* oldalon adja meg az alább megadott értékeket:
* **Előfizetés**: az Ön által használt Azure-előfizetés
  - **Erőforráscsoport**: az az erőforráscsoport, amelyben a példányt telepíteni kell. Ha még nem rendelkezik meglévő erőforráscsoporthoz, itt létrehozhat egyet az *új csoport létrehozása* hivatkozásra kattintva és egy új erőforráscsoport nevének megadásával.
* **Hely**: egy Azure digitális Twins-kompatibilis régió az üzembe helyezéshez. A regionális támogatással kapcsolatos további információkért látogasson el a [*régió (Azure Digital Twins) által elérhető Azure-termékek területére*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
* **Erőforrás neve**: az Azure Digital Twins-példány neve. Az új példány nevének a régión belül egyedinek kell lennie az előfizetéshez (ami azt jelenti, hogy ha az előfizetés egy másik Azure Digital Twins-példánnyal rendelkezik abban a régióban, amely már használja a választott nevet), a rendszer kérni fogja, hogy válasszon másik nevet.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins-2.png" alt-text="A leírt értékek kitöltése Azure digitális Twins-erőforrások létrehozásához":::

Ha elkészült, válassza a _felülvizsgálat + létrehozás_elemet. Ekkor megjelenik egy összefoglaló oldal, ahol áttekintheti a megadott példányok részleteit, és elvégezheti a _létrehozást_. 

### <a name="verify-success-and-collect-important-values"></a>Sikeres ellenőrzés és fontos értékek összegyűjtése

A *create (létrehozás*) gombra kattintva megtekintheti a példány üzembe helyezésének állapotát az Azure-értesítésekben a portál ikon sávján keresztül. Az értesítés akkor jelenik meg, ha az üzembe helyezés sikeres volt, és a létrehozott példány megtekintéséhez válassza az _Ugrás az erőforráshoz_ gombot.

:::image type="content" source="media/how-to-set-up-instance/portal/notifications-deployment.png" alt-text="A sikeres telepítést mutató Azure-értesítések, valamint az Ugrás erőforráshoz gomb kiemelése":::

Ha az üzembe helyezés meghiúsul, az értesítésben az is látható, hogy miért. Figyelje meg a hibaüzenetben szereplő tanácsokat, és próbálja meg újra létrehozni a példányt.

>[!TIP]
>A példány létrehozása után bármikor visszatérhet a lapjához, ha a Azure Portal keresési sávban keresi a példány nevét.

A példány *Áttekintés* lapján jegyezze fel a *nevét*, az *erőforráscsoport*és az *állomásnév*nevét. Ezek mind olyan fontos értékek, amelyekre szüksége lehet az Azure Digital Twins-példánnyal folytatott munka folytatásához. Ha más felhasználók is programozást végeznek a példányon, ezeket az értékeket meg kell osztani velük.

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="A példány Áttekintés oldalának fontos értékeinek kiemelése":::

Most már rendelkezik egy Azure Digital Twins-példánnyal, amely készen áll a használatra. Ezután meg kell adnia a megfelelő Azure-felhasználói engedélyeket a kezeléséhez.

## <a name="set-up-user-access-permissions"></a>Felhasználói hozzáférési engedélyek beállítása

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

Először nyissa meg az Azure Digital Twins-példány oldalát a Azure Portal. A példány menüjében válassza a *hozzáférés-vezérlés (iam)* lehetőséget. Válassza a  *Hozzáadás* gombot a *szerepkör-hozzárendelés hozzáadása*területen.

:::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-1.png" alt-text="Szerepkör-hozzárendelés hozzáadásának kiválasztása a hozzáférés-vezérlés (IAM) lapról":::

Az alábbi *szerepkör-hozzárendelés hozzáadása* lapon adja meg az értékeket (az Azure-előfizetéshez [megfelelő engedélyekkel](#prerequisites-permission-requirements) rendelkező felhasználónak kell elvégeznie):
* **Szerepkör**: válassza az *Azure Digital Twins-tulajdonos (előzetes verzió)* lehetőséget a legördülő menüből
* **Hozzáférés kiosztása**: válassza ki az *Azure ad-felhasználó,-csoport vagy-szolgáltatásnév* elemet a legördülő menüből
* **Válassza**a következőt: keresse meg a hozzárendelni kívánt felhasználó nevét vagy e-mail-címét. Az eredmény kiválasztásakor a felhasználó megjelenik a *kiválasztott tagok* szakaszban.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-2.png" alt-text="A felsorolt mezők kitöltése a szerepkör-hozzárendelés hozzáadása párbeszédpanelen":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Ha végzett a részletek beírásával, nyomja meg a *Save (Mentés* ) gombot.

### <a name="verify-success"></a>Sikeres ellenőrzés

Megtekintheti a *hozzáférés-vezérlés (iam) > szerepkör-* hozzárendelések alatt beállított szerepkör-hozzárendelést. A felhasználónak az *Azure Digital Twins-tulajdonos (előzetes verzió)* szerepkörrel kell megjelennie a listában. 

:::image type="content" source="media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Az Azure Digital Twins-példány szerepkör-hozzárendeléseinek megtekintése Azure Portal":::

Most már rendelkezik egy Azure Digital Twins-példánnyal, amely készen áll a használatra, és hozzárendelt engedélyekkel rendelkezik a kezeléséhez. Ezután be kell állítania egy ügyfélalkalmazás engedélyeit, hogy hozzáférjenek az alkalmazáshoz.

## <a name="set-up-access-permissions-for-client-applications"></a>Ügyfélalkalmazások hozzáférési engedélyeinek beállítása

[!INCLUDE [digital-twins-setup-app-registration.md](../../includes/digital-twins-setup-app-registration.md)]

Először navigáljon a [Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) a Azure Portalban (ezt a hivatkozást használhatja, vagy megkeresheti a portálon található keresősáv használatával). Válassza a szolgáltatás menü *Alkalmazásregisztrációk* elemét, majd az *+ új regisztráció*lehetőséget.

:::image type="content" source="media/how-to-set-up-instance/portal/new-registration.png" alt-text="Tekintse meg az Azure AD szolgáltatás oldalát a Azure Portal, és válassza ki a Alkalmazásregisztrációk menüpontot és a + új regisztráció gombot.":::

A következő *alkalmazás regisztrálása* lapon adja meg a kért értékeket:
* **Név**: a regisztrációhoz TÁRSÍTANDÓ Azure ad-alkalmazás megjelenítendő neve
* **Támogatott fióktípus**: *csak az ebben a szervezeti könyvtárban lévő fiókok kijelölése (csak alapértelmezett címtár – egyetlen bérlő)*
* **Átirányítási URI**: Azure *ad-alkalmazás válaszának URL-címe* az Azure ad-alkalmazáshoz. Adjon hozzá egy *nyilvános ügyfél/natív (mobile & Desktop) URI-* t a következőhöz: `http://localhost` .

Ha elkészült, kattintson a *regisztráció* gombra.

:::image type="content" source="media/how-to-set-up-instance/portal/register-an-application.png" alt-text="Az alkalmazás regisztrálása oldal megtekintése a leírt értékekkel":::

A regisztráció befejezését követően a portál átirányítja a részleteket tartalmazó lapjára.

### <a name="provide-azure-digital-twins-api-permission"></a>Azure digitális Twins API-engedély megadása

Ezután konfigurálja a létrehozott alkalmazás-regisztrációt az Azure Digital Twins API-k alapkonfigurációs engedélyeivel.

Az alkalmazás regisztrációjának portál lapján válassza az API- *engedélyek* lehetőséget a menüből. A következő engedélyek lapon kattintson a *+ engedély hozzáadása* gombra.

:::image type="content" source="media/how-to-set-up-instance/portal/add-permission.png" alt-text="Tekintse meg az alkalmazás regisztrációját a Azure Portalban, és válassza ki az API-engedélyek menüpontot és az engedély hozzáadása gombot":::

Az alábbi *API-engedélyek kérése* lapon váltson a *saját szervezet által használt API* -k lapra, és keressen rá az *Azure Digital Twins*kifejezésre. Válassza az *Azure Digital Twins* lehetőséget a keresési eredmények közül, és folytassa az engedélyek hozzárendelését az Azure Digital Twins API-khoz.

:::image type="content" source="media/how-to-set-up-instance/portal/request-api-permissions-1.png" alt-text="Az API-engedélyek kérése oldal keresési eredményének megjelenítése az Azure digitális Twins":::

>[!NOTE]
> Ha már rendelkezik Azure Digital Twins-példánnyal a szolgáltatás előző nyilvános előzetes verziójából (az 2020. július előtt) még az előfizetésében, akkor az *Azure Smart Spaces szolgáltatást* kell keresnie. Ez az API-k ugyanazon készletének régebbi neve, és ez a lépés nem változtatja meg a felhasználói élményt.

Ezután kiválaszthatja, hogy mely engedélyeket kell megadni ezekhez az API-khoz. Bontsa ki az **olvasási (1)** engedélyt, és jelölje be az *olvasás. írás* az alkalmazás regisztrációs olvasójának és az író engedélyének megadására szolgáló jelölőnégyzetet.

:::image type="content" source="media/how-to-set-up-instance/portal/request-api-permissions-2.png" alt-text="A kérelmek API-engedélyei oldal megtekintése az Azure Digital Twins API-khoz tartozó Read. Write engedélyek kiválasztásával":::

Ha elkészült, kattintson a *hozzáadási engedélyek* elemre.

### <a name="verify-success"></a>Sikeres ellenőrzés

Az API- *engedélyek* lapon ellenőrizze, hogy már van-e olyan bejegyzés az Azure digitális Twins számára, amely az olvasási/írási jogosultságokat tükrözi:

:::image type="content" source="media/how-to-set-up-instance/portal/verify-api-permissions.png" alt-text="Az Azure AD-alkalmazás regisztrálásához szükséges API-engedélyek portál nézete, amely az Azure Digital Twins olvasási/írási hozzáférését mutatja":::

Azt is ellenőrizheti, hogy az Azure Digital Twins-hoz kapcsolódott-e az alkalmazás regisztrációjának *manifest.jsján*, amely automatikusan frissült az Azure digitális Twins információi között az API-engedélyek hozzáadásakor.

Ehhez válassza ki a *jegyzékfájlt* a menüből az alkalmazás regisztrációs jegyzékfájljának megtekintéséhez. Görgessen a Code (kód) ablak aljára, és keresse meg a következő mezőket `requiredResourceAccess` . Az értékeknek meg kell egyezniük az alábbi képernyőképen láthatókkal:

:::image type="content" source="media/how-to-set-up-instance/portal/verify-manifest.png" alt-text="Az Azure AD-alkalmazás regisztrálási jegyzékfájljának portál nézete. A requiredResourceAccess alá ágyazva van egy resourceAppId érték a 0b07f429-9f4b-4714-9392-cc5e8e80c8b0 és egy resourceAccess > id értékkel 4589bd03-58cb-4e6c-b17f-b580e39652f8":::

### <a name="collect-important-values"></a>Fontos értékek gyűjtése

Ezután válassza az *Áttekintés* lehetőséget a menüsávon az alkalmazás regisztrációjának részleteinek megtekintéséhez:

:::image type="content" source="media/how-to-set-up-instance/portal/app-important-values.png" alt-text="Az alkalmazás regisztrációjának fontos értékeit a portálon tekintheti meg":::

Jegyezze **fel a lapon** megjelenő *alkalmazás (ügyfél) azonosítóját* és *KÖNYVTÁRát (bérlői azonosítóját)* . Ezekre az értékekre később szükség lesz az [ügyfélalkalmazás hitelesítéséhez az Azure digitális Twins API](how-to-authenticate-client.md)-kon. Ha Ön nem az a személy, aki az ilyen alkalmazások kódját fogja írni, meg kell osztania ezeket az értékeket a felhasználóval.

### <a name="other-possible-steps-for-your-organization"></a>A szervezet egyéb lehetséges lépései

[!INCLUDE [digital-twins-setup-additional-requirements.md](../../includes/digital-twins-setup-additional-requirements.md)]

## <a name="next-steps"></a>Következő lépések

Tesztelje az egyes REST API hívásokat a példányon az Azure Digital Twins CLI parancsaival: 
* [az DT Reference](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt?view=azure-cli-latest)
* [*Útmutató: az Azure digitális Twins parancssori felületének használata*](how-to-use-cli.md)

Azt is megtudhatja, hogyan csatlakoztatható az ügyfélalkalmazás a példányhoz az ügyfélalkalmazás hitelesítési kódjának megírásával:
* [*Útmutató: az alkalmazás-hitelesítési kód írása*](how-to-authenticate-client.md)
