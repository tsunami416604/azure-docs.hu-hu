---
title: Példány és hitelesítés beállítása (szkriptelt)
titleSuffix: Azure Digital Twins
description: 'Lásd: az Azure Digital Twins szolgáltatás példányának beállítása automatikus telepítési parancsfájl futtatásával'
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 63bc46f679b71f6965cda8f9db800a125683c093
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89298283"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-scripted"></a>Azure digitális Twins-példány és-hitelesítés beállítása (parancsfájlba foglalt)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Ez a cikk az **új Azure Digital Twins-példány beállításának**lépéseit ismerteti, beleértve a példány létrehozását és a hitelesítés beállítását. A cikk elvégzése után egy Azure Digital Twins-példánnyal kell elkezdenie a programozást.

A jelen cikk ezen verziója a lépéseket egy [ **automatizált üzembehelyezési parancsfájl** ](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples/) futtatásával hajtja végre, amely egyszerűsíti a folyamatot. 
* A parancsfájlnak a háttérben való futtatásához szükséges manuális parancssori lépések megtekintéséhez tekintse meg a jelen cikk CLI-verzióját: [*útmutató: példány és hitelesítés (CLI) beállítása*](how-to-set-up-instance-cli.md).
* A Azure Portal szerinti manuális lépések megtekintéséhez tekintse meg a jelen cikk portáljának verzióját: [*útmutató: példány és hitelesítés beállítása (portál)*](how-to-set-up-instance-portal.md).

[!INCLUDE [digital-twins-setup-steps-prereq.md](../../includes/digital-twins-setup-steps-prereq.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="run-the-deployment-script"></a>Az üzembe helyezési parancsfájl futtatása

Ez a cikk egy Azure digitális Twins-kód minta használatával helyez üzembe egy Azure digitális Twins-példányt és a szükséges hitelesítést félig automatikusan. Kiindulási pontként is használható a saját megírt interakciók írásához.

A minta parancsfájl a PowerShellben van megírva. Az [Azure digitális Twins-minták](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples/)részét képezi, amelyeket letöltheti a gépre, ha megnyitja az adott minta hivatkozást, és a cím alatt a *zip letöltése* gombot választja.

A letöltött minta mappában a telepítési parancsfájl a _Azure_Digital_Twins_samples.zip > scripts > **deploy.ps1** _található.

Az üzembe helyezési parancsfájl Cloud Shell-ben való futtatásának lépései.
1. Nyissa meg a böngésző [Azure Cloud Shell](https://shell.azure.com/) ablakát. Jelentkezzen be a következő parancs használatával:
    ```azurecli-interactive
    az login
    ```
    Ha a parancssori felület megnyithatja az alapértelmezett böngészőt, akkor az egy Azure-beli bejelentkezési oldal betöltésével végezhető el. Ellenkező esetben nyisson meg egy böngészőt, *https://aka.ms/devicelogin* és adja meg a terminálon megjelenő engedélyezési kódot.
 
2. A bejelentkezés után keresse meg a Cloud Shell ablak ikonját. Válassza a "fájlok feltöltése/letöltése" ikont, és válassza a "feltöltés" lehetőséget.

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="A feltöltési lehetőség kiválasztását ábrázoló Cloud Shell ablak":::

    Navigáljon a _**deploy.ps1**_ fájlra a gépen, és nyomja meg a "Megnyitás" lehetőséget. Ezzel feltölti a fájlt a Cloud Shellba, hogy az Cloud Shell ablakban is futtatható legyen.

3. Futtassa a szkriptet a Cloud Shell ablakban a parancs elküldésével `./deploy.ps1` . Ahogy a parancsfájl az automatikus telepítés lépésein fut, a rendszer a következő értékek megadását kéri:
    * A példány: a használni kívánt Azure-előfizetés *előfizetés-azonosítója*
    * A példány esetében: egy *hely* , ahová a példányt telepíteni szeretné. Ha szeretné megtekinteni, hogy mely régiók támogatják az Azure Digital Twins-t, látogasson el az [*Azure-termékek területre*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
    * A példány esetében: az *erőforráscsoport* neve. Használhat egy meglévő erőforráscsoportot, vagy megadhat egy új nevet a létrehozáshoz.
    * A példány esetében: az Azure Digital Twins-példány *neve* . Az új példány nevének a régión belül egyedinek kell lennie az előfizetéshez (ami azt jelenti, hogy ha az előfizetés egy másik Azure Digital Twins-példánnyal rendelkezik abban a régióban, amely már használja a választott nevet), a rendszer kérni fogja, hogy válasszon másik nevet.
    * Az alkalmazás regisztrálása: az *Azure ad-alkalmazás megjelenített neve* , amely a regisztrációhoz társítva van. Az alkalmazás regisztrálása az [Azure Digital Twins API](how-to-use-apis-sdks.md)-khoz való hozzáférési engedélyek konfigurálására szolgál. Később az ügyfélalkalmazás hitelesíteni fogja az alkalmazás regisztrációját, és ennek eredményeképpen a konfigurált hozzáférési engedélyek lesznek elérhetők az API-khoz.
    * Az alkalmazás regisztrációja: *Azure ad-alkalmazás válaszának URL-címe* az Azure ad-alkalmazáshoz. Használja az `http://localhost` parancsot. A szkript létrehoz egy *nyilvános ügyfél/natív (mobil & asztali) URI-* t.

A szkript létrehoz egy Azure Digital Twins-példányt, hozzárendeli az Azure-felhasználót az *Azure Digital Twins tulajdonos (előzetes verzió)* szerepkörrel a példányon, és beállít egy Azure ad-alkalmazás regisztrációját a használni kívánt ügyfélalkalmazás számára.

>[!NOTE]
>Jelenleg létezik egy **ismert probléma** a parancsfájlokkal rendelkező telepítővel, amelyben egyes felhasználók (különösen a személyes [Microsoft-fiókok (MSAs)](https://account.microsoft.com/account)felhasználói) megtalálják az ** _Azure Digital Twins tulajdonosának (előzetes verzió)_ szerepkör-hozzárendelését**.
>
>A szerepkör-hozzárendelést a jelen cikk későbbi, [*felhasználói szerepkör-hozzárendelés ellenőrzése*](#verify-user-role-assignment) szakaszában ellenőrizheti, és – szükség esetén – a szerepkör-hozzárendelés manuális beállítása a [Azure Portal](how-to-set-up-instance-portal.md#set-up-user-access-permissions) vagy a [parancssori](how-to-set-up-instance-cli.md#set-up-user-access-permissions)felület használatával.
>
>A problémáról további részleteket a [*Hibaelhárítás: az Azure digitális Twins ismert problémái*](troubleshoot-known-issues.md#missing-role-assignment-after-scripted-setup)című témakörben talál.

Íme egy részlet a kimenet naplóból a szkriptből:

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png" alt-text="Cloud Shell ablak, amely az üzembe helyezési parancsfájl futtatásával mutatja be a bemeneti és a kimeneti naplót" lightbox="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png":::

Ha a parancsfájl sikeresen befejeződik, a rendszer a végső kinyomatot fogja mondani `Deployment completed successfully` . Ellenkező esetben oldja meg a hibaüzenetet, és futtassa újra a parancsfájlt. A művelet megkerüli a már elvégzett lépéseket, és ismét megkezdi a bevitelt arra a pontra, ahol abbahagyta a műveletet.

A szkript befejezésekor most már rendelkezik egy Azure Digital Twins-példánnyal, amely lehetővé teszi a kezeléséhez szükséges engedélyeket, és beállította az ügyfélalkalmazások eléréséhez szükséges engedélyeket.

> [!NOTE]
> A parancsfájl jelenleg a szükséges felügyeleti szerepkört rendeli hozzá az Azure Digital Twins-ben (*Azure Digital Twins-tulajdonos (előzetes verzió)*) ugyanahhoz a felhasználóhoz, amely a parancsfájlt a Cloud Shellról futtatja. Ha ezt a szerepkört a példányt kezelő másnak szeretné hozzárendelni, ezt most a Azure Portal ([utasítások](how-to-set-up-instance-portal.md#set-up-user-access-permissions)) vagy a CLI ([utasítások](how-to-set-up-instance-cli.md#set-up-user-access-permissions)) segítségével teheti meg.

## <a name="collect-important-values"></a>Fontos értékek gyűjtése

A parancsfájl által beállított erőforrások számos fontos értékkel rendelkeznek, amelyeket az Azure Digital Twins-példánnyal folytatott munka folytatásához lehet szükség. Ebben a szakaszban a [Azure Portal](https://portal.azure.com) fogja használni ezeket az értékeket. Mentse őket biztonságos helyre, vagy térjen vissza erre a szakaszra, hogy később megkeresse őket, amikor szüksége van rájuk.

Ha más felhasználók is programozást végeznek a példányon, ezeket az értékeket is meg kell osztani velük.

### <a name="collect-instance-values"></a>Példányok értékének gyűjtése

A [Azure Portal](https://portal.azure.com)keresse meg az Azure Digital Twins-példányát, és keressen rá a példány nevére a portál keresési sávján.

Ha kiválasztja, megnyílik a példány *Áttekintés* lapja. Jegyezze fel a *nevét*, az *erőforráscsoportot*és az *állomásnév nevét*. Erre később szükség lehet a példány azonosításához és a hozzá való kapcsolódáshoz.

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="A példány Áttekintés oldalának fontos értékeinek kiemelése":::

### <a name="collect-app-registration-values"></a>Alkalmazás regisztrációs értékeinek összegyűjtése 

Az alkalmazás regisztrációjának két fontos értékét kell megadnia, amelyeket később [Az Azure Digital Twins API-khoz tartozó ügyfélalkalmazás-hitelesítési kód írására](how-to-authenticate-client.md)lehet szükség. 

A kereséshez kövesse [ezt a hivatkozást](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) , és keresse meg az Azure ad-alkalmazás regisztrációjának áttekintés lapját a Azure Portal. Ezen az oldalon az előfizetésében létrehozott összes alkalmazás regisztrálása látható.

Ekkor megjelenik az imént létrehozott alkalmazás-regisztráció a listában. A részletek megnyitásához válassza ki a következőket:

:::image type="content" source="media/how-to-set-up-instance/portal/app-important-values.png" alt-text="Az alkalmazás regisztrációjának fontos értékeit a portálon tekintheti meg":::

Jegyezze **fel a lapon** megjelenő *alkalmazás (ügyfél) azonosítóját* és *KÖNYVTÁRát (bérlői azonosítóját)* . Ha nem az a személy, aki az ügyfélalkalmazások kódját fogja írni, meg kell osztania ezeket az értékeket a felhasználóval.

## <a name="verify-success"></a>Sikeres ellenőrzés

Ha szeretné ellenőrizni az erőforrások és a parancsfájl által beállított engedélyek létrehozását, megtekintheti őket a [Azure Portalban](https://portal.azure.com).

### <a name="verify-instance"></a>Példány ellenőrzése

A példány létrejöttének ellenőrzéséhez lépjen a Azure Portal [Azure digitális Twins oldalára](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) . Ezen az oldalon az összes Azure-beli digitális Twins-példány szerepel. Keresse meg az újonnan létrehozott példány nevét a listában.

### <a name="verify-user-role-assignment"></a>Felhasználói szerepkör-hozzárendelés ellenőrzése

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

> [!NOTE]
> Ne felejtse el, hogy a parancsfájl jelenleg hozzárendeli ezt a szükséges szerepkört ugyanahhoz a felhasználóhoz, amely a parancsfájlt a Cloud Shellról futtatja. Ha ezt a szerepkört a példányt kezelő másnak szeretné hozzárendelni, ezt most a Azure Portal ([utasítások](how-to-set-up-instance-portal.md#set-up-user-access-permissions)) vagy a CLI ([utasítások](how-to-set-up-instance-cli.md#set-up-user-access-permissions)) segítségével teheti meg.
>
> A portálon vagy a CLI-ben is megadhatja saját szerepkör-hozzárendelését, ha bármilyen probléma merült fel a parancsfájlban.

### <a name="verify-app-registration"></a>Alkalmazás regisztrációjának ellenőrzése

[!INCLUDE [digital-twins-setup-verify-app-registration-1.md](../../includes/digital-twins-setup-verify-app-registration-1.md)]

Először ellenőrizze, hogy az Azure Digital Twins engedélyeinek beállításai megfelelően vannak-e beállítva a regisztrációhoz. Ehhez a menüsávban válassza a *jegyzékfájl* lehetőséget az alkalmazás regisztrációs jegyzékfájljának megtekintéséhez. Görgessen a Code (kód) ablak aljára, és keresse meg a következő mezőket `requiredResourceAccess` . Az értékeknek meg kell egyezniük az alábbi képernyőképen láthatókkal:

[!INCLUDE [digital-twins-setup-verify-app-registration-2.md](../../includes/digital-twins-setup-verify-app-registration-2.md)]

## <a name="other-possible-steps-for-your-organization"></a>A szervezet egyéb lehetséges lépései

[!INCLUDE [digital-twins-setup-additional-requirements.md](../../includes/digital-twins-setup-additional-requirements.md)]

## <a name="next-steps"></a>Következő lépések

Tesztelje az egyes REST API hívásokat a példányon az Azure Digital Twins CLI parancsaival: 
* [az DT Reference](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt?view=azure-cli-latest)
* [*Útmutató: az Azure digitális Twins parancssori felületének használata*](how-to-use-cli.md)

Azt is megtudhatja, hogyan csatlakoztatható az ügyfélalkalmazás a példányhoz az ügyfélalkalmazás hitelesítési kódjának megírásával:
* [*Útmutató: az alkalmazás-hitelesítési kód írása*](how-to-authenticate-client.md)
