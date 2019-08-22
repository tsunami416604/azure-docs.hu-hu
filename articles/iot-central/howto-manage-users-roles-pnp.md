---
title: Felhasználók és szerepkörök kezelése az Azure IoT Central alkalmazásban | Microsoft Docs
description: Rendszergazdaként, hogyan kezelheti az Azure IoT Central-alkalmazás felhasználóit és szerepköreit
author: v-krghan
ms.author: v-krghan
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 0c1a6b7370a4d1f8e01b22bfd52caa6cb6973947
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69880643"
---
# <a name="manage-users-and-roles-in-your-iot-central-application"></a>Felhasználók és szerepkörök kezelése a IoT Central alkalmazásban

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Ez a cikk bemutatja, hogyan adhat hozzá, szerkeszthet és törölhet felhasználókat az Azure IoT Central alkalmazásban, és hogyan kezelheti az Azure IoT Central-alkalmazásban található szerepköröket.

Az **Adminisztráció** szakasz eléréséhez és használatához **rendszergazdai** szerepkörrel kell rendelkeznie egy Azure IoT Central-alkalmazáshoz. Ha létrehoz egy Azure IoT Central alkalmazást, a rendszer automatikusan hozzárendeli az adott alkalmazáshoz tartozó **rendszergazdai** szerepkörhöz.


## <a name="add-users"></a>Felhasználók hozzáadása

Minden felhasználónak rendelkeznie kell egy felhasználói fiókkal, mielőtt bejelentkeznek, és hozzá tudnak férni egy Azure IoT Central alkalmazáshoz. Az Azure IoT Central támogatja a Microsoft-fiókok (MSAs) és a Azure Active Directory (Azure AD) fiókjait. Azure Active Directory csoportok jelenleg nem támogatottak az Azure IoT Centralban.

További információ: [Microsoft-Fiók Súgó](https://support.microsoft.com/products/microsoft-account?category=manage-account) és [gyors útmutató: Új felhasználók hozzáadása a Azure Active Directoryhoz](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

1. Ha felhasználót szeretne felvenni egy IoT Central alkalmazásba, lépjen a **felhasználók** lapra az **Adminisztráció** szakaszban.

    ![Felhasználók listája](media/howto-manage-users-roles-pnp/image1.png)

1. Felhasználó hozzáadásához a **felhasználók** lapon válassza a **+ felhasználó hozzáadása**elemet.

1. Válasszon egy szerepkört a felhasználó számára a **szerepkör** legördülő menüből. További információ a jelen cikk szerepkörök [kezelése](#manage-roles) című szakaszában található szerepkörökről.

    ![Szerepkör kiválasztása](media/howto-manage-users-roles-pnp/image3.png)

    > [!NOTE]
    >  Ha tömegesen szeretné felvenni a felhasználókat, adja meg az összes olyan felhasználó azonosítóját, amelyet pontosvesszővel kell elválasztani. Válasszon egy szerepkört a **szerepkör** legördülő menüből. Ezután válassza a **Save** (Mentés) lehetőséget.

### <a name="edit-the-roles-that-are-assigned-to-users"></a>A felhasználókhoz rendelt szerepkörök szerkesztése

A szerepkörök hozzárendelése után nem módosíthatók. A felhasználóhoz rendelt szerepkör módosításához törölje a felhasználót, majd adja hozzá újra a felhasználót egy másik szerepkörrel.

> [!NOTE]
> A hozzárendelt szerepkörök a IoT Central alkalmazásra vonatkoznak, és az Azure Portalról nem kezelhetők.

## <a name="delete-users"></a>Felhasználók törlése

A felhasználók törléséhez jelöljön ki egy vagy több jelölőnégyzetet a **felhasználók** lapon. Ezután válassza a **Törlés** elemet.

## <a name="manage-roles"></a>Szerepkörök kezelése

A szerepkörök lehetővé teszik annak szabályozását, hogy a szervezeten belül kik végezhetnek el különböző feladatokat IoT Centralban. Három szerepkört rendelhet hozzá az alkalmazás felhasználóihoz.

### <a name="administrator"></a>Rendszergazda

A **rendszergazdai** szerepkör felhasználói hozzáférhetnek az alkalmazás összes funkciójának eléréséhez.

Az alkalmazást létrehozó felhasználó automatikusan hozzá lesz rendelve a **rendszergazdai** szerepkörhöz. A **rendszergazdai** szerepkörnek mindig legalább egy felhasználónak kell lennie.

### <a name="application-builder"></a>Alkalmazáskészítő

Az **Application Builder** szerepkör felhasználói mindent elvégezhetnek egy alkalmazásban, kivéve az alkalmazás felügyeletét. Az építők létrehozhatják, szerkeszthetik és törölhetik az eszközök sablonjait és eszközeit, kezelhetik az eszközök készleteit, és futtathatják az elemzéseket és a feladatokat. Az építők nem férhetnek hozzá az alkalmazás adminisztrációs szakaszához.

### <a name="application-operator"></a>Alkalmazásoperátor

Az alkalmazás- **kezelő** szerepkör felhasználói nem módosíthatják az eszközök sablonjait, és nem felügyelhetik az alkalmazást. Az operátorok hozzáadhatnak és törölhetnek eszközöket, kezelhetik az eszközök készleteit, és futtathatják az elemzéseket és a feladatokat. Az operátorok nem férnek hozzá az **Application Builder** és az **adminisztrációs** lapokhoz.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte, hogyan kezelheti a felhasználókat és a szerepköröket az Azure-IoT Centralban, a javasolt következő lépés az, hogy megtudja, hogyan [tekintheti meg a számláját](howto-view-bill-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) az Azure IoT Centralban.
