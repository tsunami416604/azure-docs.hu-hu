---
title: Azure IoT központi megrongáltak |} Microsoft Docs
description: Egy rendszergazdaként az Azure IoT központi alkalmazás felügyelete
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 0bdd798f9bc8fdaef54abd721a986c607c6323a5
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34628555"
---
# <a name="how-to-administer-your-application"></a>Az alkalmazás felügyelete

Miután létrehozott egy Microsoft Azure IoT központi alkalmazáshoz, használhatja a **felügyeleti** felügyelheti úgy, hogy az Azure IoT központi felhasználói felületének része. Navigáljon a **felügyeleti** területen válasszon **felügyeleti** a bal oldali navigációs menü.

A **felügyeleti** szakasz lehetővé teszi:

- Felhasználók kezelése

- Szerepkörök kezelése

- Megtekintheti a számlázási adatokat

- Alkalmazásbeállítások kezelése

- Ingyenes próbaverzió kiterjesztése

Az a **felügyeleti** szakaszt, és van egy másodlagos navigációs menüben, a különböző felügyeleti feladatok mutató hivatkozásokkal.

Eléréséről és használatáról a **felügyeleti** csoportban kell lennie a a **rendszergazda** szerepkört az Azure IoT központi alkalmazásban. Ha létrehoz egy Azure IoT központi alkalmazást, akkor automatikusan hozzárendelni a **rendszergazda** szerepkört az adott alkalmazáshoz. A *felhasználók kezelése* szakasz ebben a cikkben magyarázó hozzárendelése a **rendszergazda** szerepkör más felhasználók számára.

## <a name="change-application-name"></a>Alkalmazás nevének módosítása

Ha módosítani szeretné az alkalmazás nevét, a másodlagos navigációs menü segítségével keresse meg a **Alkalmazásbeállítások** lapját a **felügyeleti** szakasz.

Az a **Alkalmazásbeállítások** lapján adjon meg egy nevet az Ön által választott, a a **alkalmazásnév** mezőben, majd válassza a **mentése**.

## <a name="change-the-application-url"></a>Az alkalmazás URL-Címének módosítása

Az alkalmazás URL-Címének módosításához használja a másodlagos navigációs menü lehetőségre, és navigáljon a **Alkalmazásbeállítások** lapját a **felügyeleti** szakasz.

![Alkalmazás beállításait tartalmazó lap](media\howto-administer\image0-a.png)

Az a **Alkalmazásbeállítások** lapján adja meg a kiválasztott URL-CÍMÉT a **URL-cím** mezőben, majd válassza a **mentése**. Az URL-cím legfeljebb 200 karakter hosszúságú lehet. Az URL-cím nem érhető el, ha látja-e érvényesítési hiba

> [!Note]
> Ha módosítja az URL-cím, a régi URL-címet egy másik Azure IoT központi ügyfél lehessen állítani. Ebben az esetben az már nem érhető el, hogy használja. Ha megváltoztatja az URL-cím, a régi URL-cím nem működik, és értesítenie kell a felhasználókat arról, hogy az új URL-cím használatára.

## <a name="change-the-application-image"></a>Az alkalmazás-lemezkép módosítása

Egy Azure IoT központi alkalmazásban lemezképek használatával kapcsolatos további információkért lásd: [előkészítése és feltöltése az Azure IoT központi alkalmazáshoz képek](howto-prepare-images.md).

## <a name="delete-an-application"></a>Alkalmazás törlése

Az alkalmazás törléséhez használja a másodlagos navigációs menü lehetőségre, és navigáljon a **Alkalmazásbeállítások** lapját a **felügyeleti** szakasz.

Válasszon **törlése**.

> [!Note]
> Egy alkalmazás végleg törli az alkalmazáshoz kapcsolódó összes adatot törli. Egy alkalmazás törléséhez is jogosultsággal kell rendelkeznie a törli az erőforrást az Azure-előfizetést választja, az alkalmazás létrehozásakor. További tudnivalókért lásd: [Use Role-Based hozzáférés-vezérlés kezelése az Azure-előfizetés erőforrásokhoz való hozzáférés](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).

## <a name="roles-in-azure-iot-central"></a>Szerepkörök az Azure IoT-közép-India

A szerepkörök teszik lehetővé, hogy ki, a szervezeten belül a különböző Azure IoT központi feladatokat hajthat végre. Az Azure IoT központi három szerepkört hozzárendelheti a felhasználók számára az alkalmazás rendelkezik. Minden alkalmazás szerepkörök rendeli hozzá. Ugyanaz a felhasználó különböző alkalmazások különböző szerepkörök is lehet. Az azonos felhasználói is rendelhet a több szerepkört az alkalmazáson belül.

### <a name="administrator"></a>Rendszergazda

A felhasználók a **rendszergazda** szerepkör elérheti az összes funkciót az Azure IoT központi alkalmazásban.

A felhasználó az alkalmazás létrehozása automatikusan hozzárendelni a **rendszergazda** szerepkör. Mindig kell legalább egy felhasználónak a **rendszergazda** szerepkör.

### <a name="application-builder"></a>Alkalmazásszerkesztő

A felhasználók a **alkalmazás jelentéskészítő** szerepkör elérhető műveletek mindegyikét, az Azure IoT központi alkalmazásban kivételével az alkalmazás felügyeletét.

### <a name="application-operator"></a>Alkalmazás operátor

A felhasználók a **alkalmazást üzemeltető** szerepkör nem rendelkezik hozzáféréssel a **alkalmazás jelentéskészítő** lap. Azok az alkalmazás nem kezelheti.

## <a name="manage-users"></a>Felhasználók kezelése

Alkalmazás-rendszergazdák megoldással felhasználókat rendelhet a szerepköröket, az alkalmazásban.

### <a name="add-users"></a>Felhasználók hozzáadása

Minden felhasználó felhasználói fiókkal kell rendelkeznie ahhoz, hogy jelentkezzen be és érhetik el az Azure IoT központi alkalmazást. Microsoft Accounts (msa-k) és az Azure Active Directory (AAD) fiókok az Azure IoT-központ támogatott. Az Azure Active Directory-csoportok jelenleg nem támogatottak az Azure IoT-központ.

További tudnivalókért lásd: [Microsoft-fiók súgó](https://support.microsoft.com/products/microsoft-account?category=manage-account) és [gyors üzembe helyezés: új felhasználók hozzáadása az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

1. Egy felhasználói fiókot Azure IoT központi alkalmazáshoz való hozzáadásához használja a másodlagos navigációs menü lehetőségre, és navigáljon a **felhasználók** lapját a **felügyeleti** szakasz.

    ![Azoknak a felhasználóknak](media\howto-administer\image1.png)

1. Az a **felhasználók** lapon, válassza ki **+ Hozzáadás felhasználói** hozzáadni egy felhasználót.

    ![Felhasználó hozzáadása](media\howto-administer\image2.png)

1. Amikor a felhasználó hozzá az Azure IoT központi alkalmazáshoz, válassza ki a felhasználói szerepkört a **szerepkör** legördülő listán. További információ a szerepkörök a *szerepkörök az Azure IoT központi* című szakaszát.

    ![Szerepkör kiválasztása](media\howto-administer\image3.png)

    > [!NOTE]
    >  A felhasználó hozzáadása egyszerre adja meg az összes olyan felhasználó, szeretne adni a felhasználói azonosítók pontosvesszővel elválasztva. Válassza ki a szerepkör eltávolítása a **szerepkör** legördülő listán, és válassza a **mentése**.

1. Miután egy felhasználó, egy bejegyzés megjelenik az adott felhasználó a **felhasználók** lap.

    ![Felhasználólista](media\howto-administer\image4.png)

### <a name="edit-the-roles-assigned-to-users"></a>A felhasználókhoz rendelt szerepkörök szerkesztése

Szerepkörök nem módosítható assinged. Ha módosítani szeretné a szerepkört a felhasználóhoz rendelt, törölje a felhasználót, és adja hozzá a felhasználót újra egy másik szerepkör.

### <a name="delete-users"></a>Felhasználók törlése

Törli a felhasználókat, tanulmányozza az egy vagy több jelölőnégyzetet a **felhasználók** lapon, és válassza a **törlése**.

## <a name="view-your-bill"></a>A számlázási megtekintése

A számlázási megtekintéséhez nyissa meg a **számlázási** lapját a **felügyeleti** szakaszt, és válassza a **számlázási**. Az Azure számlázási oldalon egy új lapon nyílik meg, és a számlázási láthatja az összes az Azure IoT központi alkalmazáshoz.

## <a name="convert-your-trial-to-a-paid-application"></a>A próbaverzió átalakítása fizetett alkalmazás

Miután IoT központi kiértékelni a próbaidőszak átválthat a fizetős alkalmazások. Az önkiszolgáló folyamat befejezéséhez kövesse az alábbi lépéseket:

1. A másodlagos navigációs menü használatával nyissa meg a **számlázási** lapját a **felügyeleti** szakasz. Ha a próbaidőszak még nem bővítette, a lap a következőképpen néznek:

    ![Ingyenes próba állapota](media/howto-administer/freetrial.png)

2. Kattintson a **konvertálni a fizetős**. Ha a próbaidőszak még nem bővítette, a előugró ablak a következőképpen néznek:

    Az előugró válassza ki a megfelelő Azure Active Directory-bérlőt és az IoT központi alkalmazáshoz használni kívánt Azure-előfizetést.

    ![Ingyenes próbaverzió kiterjesztése](media/howto-administer/extend.png)

3. Miután rákattintott **átalakítása**, a próbaidőszak alakítja át a fizetős alkalmazások és Számlázva első indításakor.

## <a name="extend-your-free-trial"></a>Hosszabbítani az ingyenes próbaidőszakot

Alapértelmezés szerint minden ingyenes próbaverzió érhetők el a 7 nap. Ha azt szeretné, a 30 napos próbaidőszak növelésére, tegye a következőket:

1. A másodlagos navigációs menü használatával nyissa meg a **számlázási** lapját a **felügyeleti** szakasz:

1. Kattintson a **hosszabbítani próbaidőszakot**. Az előugró jelölje ki a megfelelő Azure Active Directory-bérlő és az Azure-előfizetés IoT központi alkalmazás használatára:

1. Kattintson a **bővítése**. A próbaverzió 30 napig most is érvényes.

## <a name="next-steps"></a>További lépések

Most, hogy rendelkezik megismerte az Azure IoT központi alkalmazás felügyelete, ez a javasolt következő lépésre:

> [!div class="nextstepaction"]
> [Az eszköz-sablon beállítása](howto-set-up-template.md)