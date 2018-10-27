---
title: Felügyelheti az Azure IoT Central alkalmazáshoz |} A Microsoft Docs
description: Egy rendszergazdaként az Azure IoT központi alkalmazás felügyelete
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 25b4777be4257933b84d58d0f10cf12571de9590
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50155320"
---
# <a name="administer-your-iot-central-application"></a>Az IOT központi alkalmazás felügyelete

Miután létrehozott egy Microsoft Azure IoT Central-alkalmazást, használhatja a **felügyeleti** felügyelheti az Azure IoT Central felhasználói felületén szakaszában. Ugrás a **felügyeleti** szakaszban jelölje be **felügyeleti** a bal oldali navigációs menüben.

A **felügyeleti** szakaszban:

- Felhasználók kezelése

- Szerepkörök kezelése

- Számlázási adatok megtekintése

- Alkalmazásbeállítások kezelése

- Ingyenes próbaidőszakot

Az a **felügyeleti** a szakaszban egy másodlagos navigációs menü a különböző felügyeleti feladatokra mutató hivatkozásokat tartalmaz.

Hogy eléri és használja a **felügyeleti** szakaszban kell lennie a a **rendszergazda** szerepkört az Azure IoT Central alkalmazáshoz. Azure IoT Central alkalmazást hoz létre, ha automatikusan kapott a **rendszergazda** szerepkör az adott alkalmazáshoz. A [felhasználók kezelésével](#manage-users) szakasz ebben a cikkben a hozzárendelése több ismerteti a **rendszergazda** szerepkör más felhasználók számára.

## <a name="change-application-name"></a>Alkalmazás nevének módosítása

Ha módosítani szeretné az alkalmazás nevét, a másodlagos navigációs menü használatával nyissa meg a **Alkalmazásbeállítások** lap a **felügyeleti** szakaszban.

Az a **Alkalmazásbeállítások** lap, adja meg a választott nevét a **alkalmazásnév** mező. Ezután válassza a **Save** (Mentés) lehetőséget.

## <a name="change-the-application-url"></a>Az alkalmazás URL-Címének módosítása

Az alkalmazás URL-Címének módosításához használja a másodlagos navigációs menü, keresse meg a **Alkalmazásbeállítások** lapját a **felügyeleti** szakaszban.

![Alkalmazás-Beállítások lap](media\howto-administer\image0-a.png)

Az a **Alkalmazásbeállítások** lap, adja meg az URL-címet a választott a **URL-cím** mezőben, majd válassza ki **mentése**. Az URL-cím legfeljebb 200 karakter hosszúságú lehet. Ha az URL-cím nem érhető el, egy érvényesítési hibaüzenet jelenik meg.

> [!Note]
> Ha megváltoztatja az URL-CÍMÉT, a régi URL-CÍMÉT egy másik Azure IoT Central ügyfél elvégezhet. Ha ez történik, már nem érhető el, amelyet használhat. Ha megváltoztatja az URL-CÍMÉT, a régi URL-cím már nem működik, és értesíti a felhasználókat arról, hogy az új URL-cím használatára kell.

## <a name="change-the-application-image"></a>Az alkalmazás-lemezkép módosítása

További információ a rendszerképek használata az Azure IoT Central alkalmazáshoz: [előkészítése és a feltöltés rendszerképek az Azure IoT Central alkalmazásnak](howto-prepare-images.md).

## <a name="copy-an-application"></a>Alkalmazás másolása

Minden olyan alkalmazás, bármely eszköz példányok, az adatok eszközelőzmények és a felhasználói adatok mínusz egy másolatát is létrehozhat. A példány lesz a fizetős alkalmazásra, amely díjkötelesek. Próbaverziós alkalmazás másolása egy másik alkalmazás nem hozható létre.

Másolja egy alkalmazást, nyissa meg a **Alkalmazásbeállítások** lapot. Válassza ki a **másolási** gombra.

![Alkalmazás-Beállítások lap](media\howto-administer\appCopy1.png)

Válassza a **másolási** gombra kattintva megnyílik egy párbeszédpanel, ahol kiválaszthatja a neve, URL-cím, az Azure AD directory, előfizetés és az új alkalmazás létrejönnek másolása az alkalmazás Azure-régióban. Válassza ki az értékeket az egyes mezők. Válassza ki a **másolási** gombra annak megerősítéséhez, hogy végre szeretné hajtani. További információ meg ezekhez az értékekhez ebben a cikkben arról [hogyan hozhat létre egy alkalmazást](howto-create-application.md).

![Alkalmazás-Beállítások lap](media\howto-administer\appCopy2.png)

Miután az alkalmazás másolási művelet sikeres, az új alkalmazás másolása az alkalmazás által létrehozott léphet. Nyissa meg az alkalmazást, válassza a megjelenő hivatkozásra a **Alkalmazásbeállítások** lapot.

![Alkalmazás-Beállítások lap](media\howto-administer\appCopy3.png)

> [!Note]
> Alkalmazás másolása is átmásolja a szabályok és műveletek definíciója. De az eredeti alkalmazáshoz hozzáféréssel rendelkező felhasználók nem másolja át a másolt alkalmazást, mert a felhasználók manuális hozzáadása a műveleteket, például e-mail, amelyhez felhasználók olyan előfeltételt.

## <a name="delete-an-application"></a>Alkalmazás törlése

Az alkalmazás törléséhez a másodlagos navigációs menü használatával nyissa meg a **Alkalmazásbeállítások** lapját a **felügyeleti** szakaszban.

Válasszon **törlése**.

> [!Note]
> Egy alkalmazás törlésével véglegesen törli a az adott alkalmazáshoz társított összes adatot.  Egy alkalmazás törléséhez, jogosultnak kell lennie is törölje az erőforrást az Azure-előfizetésében választotta, az alkalmazás létrehozásakor. További tudnivalókért lásd: [az Azure-előfizetések erőforrásaihoz való hozzáférés kezelése szerepköralapú hozzáférés-vezérlés használatával](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).

## <a name="roles-in-azure-iot-central"></a>Szerepkörök az Azure IoT-központ

Szerepkörök lehetővé teszik az Azure IoT Central különböző feladatok végrehajtására a szervezeten belül ki. Az Azure IoT-központ rendelkezik három szerepkört hozzárendelheti az alkalmazást. Minden alkalmazás hozzárendelve. Ugyanaz a felhasználó különböző alkalmazások különböző szerepköröket is lehet. Az alkalmazáson belül több szerepkört rendelhet ugyanahhoz a felhasználóhoz.

### <a name="administrator"></a>Rendszergazda

A felhasználók a **rendszergazda** szerepkör rendelkezik minden funkciókhoz való hozzáférést az Azure IoT Central alkalmazáshoz.

A felhasználó, aki alkalmazást hoz létre automatikusan hozzá van rendelve a **rendszergazda** szerepkör. Mindig kell legalább egy felhasználója a **rendszergazda** szerepkör.

### <a name="application-builder"></a>Alkalmazásszerkesztő

A felhasználók a **alkalmazás Builder** szerepkör teheti meg mindent az Azure IoT Central alkalmazások kivételével az alkalmazás felügyeletét.

### <a name="application-operator"></a>Alkalmazás-operátor

A felhasználók a **alkalmazást üzemeltető** szerepkör nem rendelkezik hozzáféréssel a **alkalmazás Builder** lapot. Az alkalmazás, nem kezelheti.

## <a name="manage-users"></a>Felhasználók kezelése

Alkalmazás-rendszergazdák felhasználókat rendelhet a szerepkörök az alkalmazásban.

### <a name="add-users"></a>Felhasználók hozzáadása

Minden rendelkezniük kell egy felhasználói fiókkal jelentkezzen be, és hozzáférést az Azure IoT Central alkalmazáshoz. A Microsoft Accounts (msa-k) és az Azure Active Directory (Azure AD) az Azure IoT Central támogatottak. Az Azure Active Directory-csoportok jelenleg nem támogatottak az Azure IoT Central.

További információkért lásd: [Microsoft-fiók súgó](https://support.microsoft.com/products/microsoft-account?category=manage-account) és [a rövid útmutató: új felhasználók hozzáadása az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

1. Adjon hozzá egy felhasználót az Azure IoT Central alkalmazáshoz, a másodlagos navigációs menü használatával nyissa meg a **felhasználók** lapját a **felügyeleti** szakaszban.

    ![Felhasználók listája](media\howto-administer\image1.png)

1. A felhasználó hozzáadásához a **felhasználók** lapon a **+ Hozzáadás felhasználó**.

    ![Felhasználó hozzáadása](media\howto-administer\image2.png)

1. Válassza ki a felhasználót egy szerepkört a **szerepkör** legördülő menüből. További információ a szerepkörökről az *szerepkörök az Azure IoT Central* című szakaszát.

    ![Szerepkör kiválasztása](media\howto-administer\image3.png)

    > [!NOTE]
    >  Felhasználók tömeges hozzáadása, a felhasználó az összes olyan a hozzáadni kívánt felhasználó azonosítóját adja meg pontosvesszővel elválasztva. A munkakör kiválasztása a **szerepkör** legördülő menüből. Ezután válassza a **Save** (Mentés) lehetőséget.

1. Miután hozzáadott egy felhasználó, egy bejegyzés jelenik meg, hogy a felhasználó számára a **felhasználók** lapot.

    ![Felhasználólista](media\howto-administer\image4.png)

### <a name="edit-the-roles-that-are-assigned-to-users"></a>A felhasználókhoz rendelt szerepkörök szerkesztése

Szerepkörök nem módosítható, miután hozzá vannak rendelve. A felhasználóhoz hozzárendelt szerepkör módosítása, törölje a felhasználót, és adja hozzá a felhasználót újra egy másik szerepkörrel rendelkező.

### <a name="delete-users"></a>Felhasználók törlése

Törli a felhasználókat, válassza ki egy vagy több jelölőnégyzetet az **felhasználók** lap. Ezután válassza a **Törlés** elemet.

## <a name="view-your-bill"></a>A számla megtekintése

A számla megtekintéséhez nyissa meg a **számlázási** lapját a **felügyeleti** szakaszban. Válassza ki **számlázási**. Az Azure számlázási oldalán, ahol megtekintheti a számla minden, az Azure IoT Central alkalmazások egy új lapon nyílik meg.

## <a name="convert-your-trial-to-a-paid-application"></a>A próbaidőszak átalakítása fizetős alkalmazásra

Miután kiértékelte az IoT-központ, a próbaidőszak átválthat a fizetős alkalmazásra. Az önkiszolgáló folyamat befejezéséhez kövesse az alábbi lépéseket:

1. A másodlagos navigációs menü használatával nyissa meg a **számlázási** lapját a **felügyeleti** szakaszban. Ha a próbaidőszak még nem lett kiterjesztve, az oldal a következő képernyőképhez hasonlóan néz ki:

    ![Ingyenes próba állapota](media/howto-administer/freetrial.png)

2. Válassza ki **konvertálása fizetős szolgáltatáshoz hozzáadandó**. Ha a próbaidőszak még nem lett kiterjesztve, az előugró ablakban az alábbi képernyőképhez hasonlóan néz ki:

    ![Ingyenes próbaidőszak meghosszabbítása](media/howto-administer/extend.png)

3. Az előugró ablakban válassza ki a megfelelő Azure Active Directory-bérlővel, majd az Azure-előfizetés használata az IoT Central alkalmazáshoz.

3. Miután kiválasztotta **konvertálása**, a próbaverziós összeállítása a fizetős alkalmazásra, és indítsa el a számlázandó.

## <a name="extend-your-free-trial"></a>Az ingyenes próbaidőszak meghosszabbítása

Alapértelmezés szerint az összes ingyenes próbalehetőséget érhetők el hét napja. Ha szeretné növelni a próbaidőszak 30 napra, kövesse az alábbi lépéseket:

1. A másodlagos navigációs menü használatával nyissa meg a **számlázási** lapját a **felügyeleti** szakaszban.

1. Válassza ki **próbaidőszak meghosszabbítása**. Az előugró ablakban válassza ki a megfelelő Azure Active Directory-bérlővel, majd az Azure-előfizetés használata az IoT Central alkalmazáshoz.

1. Válassza ki **kiterjesztése**. A próbaidőszak már érvényes 30 napig.

## <a name="use-the-azure-sdks-for-control-plane-operations"></a>Az Azure SDK-k használata vezérlési síkjával végzett műveletek

A Node, Python, C#, Ruby, a Java és Go IoT Central Azure Resource Manager SDK-csomagok érhetők el. E kódtárak támogatási vezérlési síkjával végzett műveletek az IoT-központ, így lehetővé teszi a létrehozása, listázása, frissítésére, vagy törölje az IoT Central-alkalmazást. Hitelesítési többé vesződnie a sérült segítők is rendelkeznek, és a hibakezelési, amely csak a különböző nyelveken. 

Példa bemutatja, hogyan használhatja az Azure Resource Manager SDK-k, annak [ https://github.com/emgarten/iotcentral-arm-sdk-examples ](https://github.com/emgarten/iotcentral-arm-sdk-examples).

További tudnivalókért tekintse meg ezeket a csomagokat a Githubon.

| Nyelv | Tárház | Csomag |
| ---------| ---------- | ------- |
| Csomópont | [https://github.com/Azure/azure-sdk-for-node](https://github.com/Azure/azure-sdk-for-node) | [https://www.npmjs.com/package/azure-arm-iotcentral](https://www.npmjs.com/package/azure-arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Indítás | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>További lépések

Most, hogy megismerte az Azure IoT központi alkalmazás felügyelete, Íme a javasolt következő lépésre:

> [!div class="nextstepaction"]
> [Az eszköz-sablon beállítása](howto-set-up-template.md)
