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
ms.openlocfilehash: a43febf1e78f80451b6aeed19e095b2c313d3216
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39284853"
---
# <a name="how-to-administer-your-application"></a>Az alkalmazás felügyelete

Miután létrehozott egy Microsoft Azure IoT Central-alkalmazást, használhatja a **felügyeleti** felügyelheti az Azure IoT Central felhasználói felületén szakaszában. Navigáljon a **felügyeleti** válassza **felügyeleti** a bal oldali navigációs menüben.

A **felügyeleti** szakaszban:

- Felhasználók kezelése

- Szerepkörök kezelése

- Számlázási adatok megtekintése

- Alkalmazásbeállítások kezelése

- Ingyenes próbaidőszak meghosszabbítása

Az a **felügyeleti** szakaszt, és van egy másodlagos navigációs menü a különböző felügyeleti feladatok mutató hivatkozásokkal.

Hogy eléri és használja a **felügyeleti** szakaszban kell lennie a a **rendszergazda** szerepkörhöz az Azure IoT Central alkalmazáshoz. Ha az Azure IoT Central alkalmazást hoz létre, akkor automatikusan megkapja a a **rendszergazda** szerepkör az adott alkalmazáshoz. A *felhasználók kezelésével* szakasz ebben a cikkben a hozzárendelése több ismerteti a **rendszergazda** szerepkör más felhasználók számára.

## <a name="change-application-name"></a>Alkalmazás nevének módosítása

Ha módosítani szeretné az alkalmazás nevét, a másodlagos navigációs menü használatával nyissa meg a **Alkalmazásbeállítások** lap a **felügyeleti** szakaszban.

A a **Alkalmazásbeállítások** lap, adja meg a választott nevét a **alkalmazásnév** mezőben, majd válassza a **mentése**.

## <a name="change-the-application-url"></a>Az alkalmazás URL-Címének módosítása

Az alkalmazás URL-Címének módosításához használja a másodlagos navigációs menü, keresse meg a **Alkalmazásbeállítások** lapját a **felügyeleti** szakaszban.

![Alkalmazás-Beállítások lap](media\howto-administer\image0-a.png)

Az a **Alkalmazásbeállítások** lap, adja meg az URL-címet a választott a **URL-cím** mezőben, majd válassza a **mentése**. Az URL-cím legfeljebb 200 karakter hosszúságú lehet. Az URL-cím nem érhető el, ha látja-e érvényesítési hiba

> [!Note]
> Ha megváltoztatja az URL-CÍMÉT, a régi URL-CÍMÉT egy másik Azure IoT Central ügyfél elvégezhet. Ebben az esetben az már nem érhető el, amelyet használhat. Ha megváltoztatja az URL-CÍMÉT, a régi URL-cím már nem működik, és értesítenie kell a felhasználókat arról, hogy az új URL-cím használatára.

## <a name="change-the-application-image"></a>Az alkalmazás-lemezkép módosítása

További információ a rendszerképek használata az Azure IoT Central alkalmazáshoz: [előkészítése és a feltöltés rendszerképek az Azure IoT Central alkalmazásnak](howto-prepare-images.md).

## <a name="copy-an-application"></a>Alkalmazás másolása

Minden olyan alkalmazás, bármely eszköz példányok, az adatok eszközelőzmények és a felhasználói adatok mínusz egy másolatát is létrehozhat. A példány lesz a fizetős alkalmazásra, amely akkor kell fizetnie. Próbaverziós alkalmazás másolása egy másik alkalmazás nem hozható létre.

Másolja egy alkalmazást, lépjen a **Alkalmazásbeállítások** lapon, majd kattintson a **másolási** gombra.

![Alkalmazás-Beállítások lap](media\howto-administer\appCopy1.png)

Kattintson a **másolási** gomb megnyílik egy párbeszédpanel, amelyben kiválaszthatja a nevét, URL-címe, AAD könyvtár, előfizetés és az új alkalmazás létrejönnek másolása az alkalmazás Azure-régióban. Válassza ki az értékeket az egyes mezők, és kattintson a **másolási** gombra annak megerősítéséhez, hogy végre szeretné hajtani. További információkat kapcsolatos adja meg ezeket az értékeket a cikkben [hogyan hozhat létre egy alkalmazást](howto-create-application.md).

![Alkalmazás-Beállítások lap](media\howto-administer\appCopy2.png)

Után az alkalmazás másolási művelet sikeres, akkor fogja tudni az új alkalmazást, amely hozta létre az alkalmazás másolása a megjelenő hivatkozásra kattintva nyissa meg a **Alkalmazásbeállítások** lapot.

![Alkalmazás-Beállítások lap](media\howto-administer\appCopy3.png)

> [!Note]
> Alkalmazás másolása másolja a szabályok és műveletek definíciója. Azonban az eredeti alkalmazáshoz hozzáféréssel rendelkező felhasználók nem másolja át a másolt alkalmazást, mert kell felhasználók manuális hozzáadása a műveleteket, például e-mail, amelyhez felhasználók olyan előfeltételt.

## <a name="delete-an-application"></a>Alkalmazás törlése

Az alkalmazás törléséhez a másodlagos navigációs menü használatával nyissa meg a **Alkalmazásbeállítások** lapját a **felügyeleti** szakaszban.

Válasszon **törlése**.

> [!Note]
> Az alkalmazáshoz kapcsolódó összes adat visszavonhatatlanul törli egy alkalmazás törlése. Egy alkalmazás törléséhez, is jogosultságaival kell rendelkeznie, törölje az erőforrást az Azure-előfizetésében választotta, az alkalmazás létrehozásakor. További tudnivalókért lásd: [Use Role-Based hozzáférés-vezérlés az Azure-előfizetések erőforrásaihoz való hozzáférés kezelése](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).

## <a name="roles-in-azure-iot-central"></a>Szerepkörök az Azure IoT-központ

Szerepkörök lehetővé teszi annak vezérléséhez, a szervezeten belül a különböző Azure IoT Central feladatokat hajthat végre. Az Azure IoT-központ rendelkezik három szerepkört hozzárendelheti az alkalmazást. Minden alkalmazás hozzárendelve. Ugyanaz a felhasználó különböző alkalmazások különböző szerepköröket is lehet. Az azonos felhasználói is hozzárendelheti az alkalmazáson belül több szerepkörhöz.

### <a name="administrator"></a>Rendszergazda

A felhasználók a **rendszergazda** szerepkör rendelkezik minden funkciókhoz való hozzáférést az Azure IoT Central alkalmazáshoz.

Az alkalmazás létrehozása a felhasználó automatikusan hozzá van rendelve a **rendszergazda** szerepkör. Mindig kell legalább egy felhasználója a **rendszergazda** szerepkör.

### <a name="application-builder"></a>Alkalmazásszerkesztő

A felhasználók a **alkalmazás Builder** szerepkör teheti meg mindent az Azure IoT Central alkalmazások kivételével az alkalmazás felügyeletét.

### <a name="application-operator"></a>Alkalmazás-operátor

A felhasználók a **alkalmazást üzemeltető** szerepkör nem rendelkezik hozzáféréssel a **alkalmazás Builder** lapot. Az alkalmazás, nem kezelheti.

## <a name="manage-users"></a>Felhasználók kezelése

Alkalmazás-rendszergazdák felhasználókat rendelhet a szerepkörök az alkalmazásban.

### <a name="add-users"></a>Felhasználók hozzáadása

Minden rendelkezniük kell egy felhasználói fiókkal jelentkezzen be, és hozzáférést az Azure IoT Central alkalmazáshoz. A Microsoft Accounts (msa-k) és az Azure Active Directory (AAD) az Azure IoT Central támogatottak. Az Azure Active Directory-csoportok jelenleg nem támogatottak az Azure IoT Central.

További tudnivalókért lásd: [Microsoft-fiók súgó](https://support.microsoft.com/products/microsoft-account?category=manage-account) és [a rövid útmutató: új felhasználók hozzáadása az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

1. Adjon hozzá egy felhasználót az Azure IoT Central alkalmazáshoz, a másodlagos navigációs menü használatával nyissa meg a **felhasználók** lapját a **felügyeleti** szakaszban.

    ![Felhasználók listája](media\howto-administer\image1.png)

1. Az a **felhasználók** lapon a **+ Hozzáadás felhasználó** hozzáadni egy felhasználót.

    ![Felhasználó hozzáadása](media\howto-administer\image2.png)

1. Felhasználó hozzáadása az Azure IoT Central alkalmazáshoz, amikor a felhasználó a munkakör kiválasztása a **szerepkör** listából. További információ a szerepkörökről az *szerepkörök az Azure IoT Central* című szakaszát.

    ![Szerepkör kiválasztása](media\howto-administer\image3.png)

    > [!NOTE]
    >  Felhasználók tömeges hozzáadása, írja be a hozzáadni kívánt összes felhasználó felhasználói azonosítóinak pontosvesszővel elválasztva. A munkakör kiválasztása a **szerepkör** listából, és válassza a **mentése**.

1. Miután hozzáadott egy felhasználó, egy bejegyzés jelenik meg, hogy a felhasználó számára a **felhasználók** lapot.

    ![Felhasználólista](media\howto-administer\image4.png)

### <a name="edit-the-roles-assigned-to-users"></a>A felhasználókhoz rendelt szerepkörök szerkesztése

Szerepkörök nem módosítható egyszer assinged. Egy felhasználóhoz hozzárendelt szerepkör módosítása, törölje a felhasználót, és adja hozzá a felhasználót újra egy másik szerepkört.

### <a name="delete-users"></a>Felhasználók törlése

Törli a felhasználókat, ellenőrizze az egy vagy több jelölőnégyzetet az **felhasználók** lapon, majd **törlése**.

## <a name="view-your-bill"></a>A számla megtekintése

A számla megtekintése, lépjen a **számlázási** lapját a **felügyeleti** szakaszt, és válassza a **számlázási**. Az Azure számlázási oldalon új lapon nyílik meg, és megtekintheti a számla minden, az Azure IoT Central alkalmazások.

## <a name="convert-your-trial-to-a-paid-application"></a>A próbaidőszak átalakítása fizetős alkalmazásra

Miután kiértékelte az IoT-központ, a próbaidőszak átválthat a fizetős alkalmazásra. Az önkiszolgáló folyamat befejezéséhez kövesse az alábbi lépéseket:

1. A másodlagos navigációs menü használatával nyissa meg a **számlázási** lapját a **felügyeleti** szakaszban. Ha a próbaidőszak még nem lett kiterjesztve, az oldal az alábbihoz hasonló:

    ![Ingyenes próba állapota](media/howto-administer/freetrial.png)

2. Kattintson a **konvertálása fizetős szolgáltatáshoz hozzáadandó**. Ha a próbaidőszak még nem lett kiterjesztve, az előugró ablak jelenik meg a következőhöz hasonló:

    Az előugró válassza ki a megfelelő Azure Active Directory-bérlőhöz, majd az IoT Central alkalmazáshoz használni kívánt Azure-előfizetést.

    ![Ingyenes próbaidőszak meghosszabbítása](media/howto-administer/extend.png)

3. Miután rákattintott **konvertálása**, a próbaidőszak alakítja át fizetős alkalmazásra, és számlázandó megkezdése.

## <a name="extend-your-free-trial"></a>Az ingyenes próbaidőszak meghosszabbítása

Alapértelmezés szerint az összes ingyenes próbalehetőséget érhetők el 7 napon. Ha szeretné növelni a próbaidőszak 30 nap, akkor kövesse az alábbi lépéseket:

1. A másodlagos navigációs menü használatával nyissa meg a **számlázási** lapját a **felügyeleti** szakaszban:

1. Kattintson a **próbaidőszak meghosszabbítása**. Az előugró válassza ki a megfelelő Azure Active Directory-bérlővel, majd az Azure-előfizetés használata az IoT Central alkalmazáshoz:

1. Kattintson a **kiterjesztése**. A próbaidőszak már érvényes 30 napig.

## <a name="next-steps"></a>További lépések

Most, hogy megtanulhatta, hogyan felügyelheti az Azure IoT Central alkalmazáshoz, Íme a javasolt következő lépésre:

> [!div class="nextstepaction"]
> [Az eszköz-sablon beállítása](howto-set-up-template.md)