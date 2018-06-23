---
title: Hozzon létre egy egyszerű Azure verem |} Microsoft Docs
description: Ismerteti, amelyek segítségével a szerepköralapú hozzáférés-vezérlés az Azure Resource Manager erőforrásokhoz való hozzáférés kezelése szolgáltatásnevet létrehozni.
services: azure-resource-manager
documentationcenter: na
author: mattbriggs
manager: femila
ms.assetid: 7068617b-ac5e-47b3-a1de-a18c918297b6
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/21/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: 3c9f114c2844021d515765888aa19f18a0adc10b
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2018
ms.locfileid: "36320757"
---
# <a name="give-applications-access-to-azure-stack-resources-by-creating-service-principals"></a>Hozzáférést alkalmazások Azure verem erőforrások szolgáltatásnevekről létrehozásával

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Hozzon létre egy szolgáltatás egyszerű Azure Resource Managert használja, amely egy alkalmazás hozzáférést biztosíthat Azure verem erőforrások. Egy egyszerű szolgáltatás lehetővé teszi, hogy a delegált engedélyeket használ [szerepköralapú hozzáférés-vezérlés](azure-stack-manage-permissions.md).

Ajánlott eljárásként szolgáltatásnevekről kell használnia, az alkalmazások számára. Szolgáltatásnevekről előnyösek futnak, ha az alkalmazást a saját hitelesítő adatok a következők lehetnek az okai:

* Engedélyeket rendelhet az egyszerű szolgáltatásnév, amelyek eltérnek a saját fiók engedélyeit. A szolgáltatás egyszerű engedélyek általában korlátozódik, hogy mit az alkalmazás kell tennie.
* Nem kell módosítani az alkalmazás hitelesítő adatokat, ha a szerepkör vagy a felelősségekhez módosítása.
* A tanúsítvány segítségével automatizálhatja a hitelesítési mód, ha egy felügyelet nélküli parancsfájllal futtatja.

## <a name="example-scenario"></a>Példaforgatókönyv

Lehetősége van a konfigurációs felügyeleti alkalmazás, amelyet a szoftverleltár-Azure-erőforrások Azure Resource Manager használatával. Egy egyszerű szolgáltatásnév létrehozása, és rendelje hozzá az olvasó szerepkört. Ezt a szerepkört az alkalmazás csak olvasható hozzáférést ad a Azure-erőforrások.

## <a name="getting-started"></a>Első lépések

Kövesse a lépéseket a cikk útmutatójaként:

* Az alkalmazás szolgáltatásnevet létrehozni.
* Regisztrálja az alkalmazást, és a hitelesítési kulcs létrehozása.
* Az alkalmazás hozzárendelése szerepkörhöz.

Az Active Directory beállítása az Azure-verem módja határozza meg, hogyan hozzon létre egy egyszerű szolgáltatást. Válasszon az alábbi lehetőségek közül:

* Az egyszerű szolgáltatás létrehozása [Azure Active Directory (Azure AD)](azure-stack-create-service-principals.md#create-service-principal-for-azure-ad).
* Az egyszerű szolgáltatás létrehozása [Active Directory összevonási szolgáltatások (AD FS)](azure-stack-create-service-principals.md#create-service-principal-for-ad-fs).

A lépései hozzárendelése egy egyszerű szolgáltatást az adott szerepkörhöz azonos az Azure AD és az AD FS. Az egyszerű szolgáltatás létrehozása után is [engedélyek delegálása](azure-stack-create-service-principals.md#assign-role-to-service-principal) hozzá egy szerepkört.

## <a name="create-a-service-principal-for-azure-ad"></a>Az Azure AD egy egyszerű szolgáltatás létrehozása

Ha az Azure-verem használja az Azure AD használva identity, létrehozhat egy szolgáltatás egyszerű ugyanazokat a lépéseket, mint Azure-bA az Azure-portál használatával.

>[!NOTE]
Ellenőrizze, hogy a [szükséges engedélyek az Azure AD](../../azure-resource-manager/resource-group-create-service-principal-portal.md#required-permissions) egyszerű szolgáltatás létrehozása előtt.

### <a name="create-service-principal"></a>Egyszerű szolgáltatás létrehozása

Az alkalmazás egyszerű szolgáltatás létrehozása:

1. Az Azure-fiók használatával jelentkezzen be a [Azure-portálon](https://portal.azure.com).
2. Válassza ki **Azure Active Directory** > **App regisztrációk** > **Hozzáadás**.
3. Adja meg az alkalmazás nevét és URL-címét. Válassza ki vagy **Web app / API** vagy **natív** a létrehozandó alkalmazás típusától. Miután beállította az értékeket, válassza ki a **létrehozása**.

### <a name="get-credentials"></a>Hitelesítő adatok beolvasása

Bejelentkezéskor programozott módon, a azonosító használata az alkalmazás és egy hitelesítési kulcs. Ezek az értékek beolvasása:

1. A **App regisztrációk** az Active Directoryban, válassza ki az alkalmazást.

2. Másolja ki az **Alkalmazásazonosítót**, és tárolja az alkalmazás kódjában. Az alkalmazások a [mintaalkalmazást](#sample-applications) használja **ügyfél-azonosító** kontextusban való megnevezésekor a **Alkalmazásazonosító**.

     ![Az alkalmazás azonosítója](./media/azure-stack-create-service-principal/image12.png)
3. A hitelesítési kulcs létrehozásához válassza a **Kulcsok** elemet.

4. Adjon meg egy leírást és egy időtartamot a kulcshoz. Ha elkészült, kattintson a **Mentés** elemre.

>[!IMPORTANT]
A kulcs, a kulcs mentése után **érték** jelenik meg. Jegyezze fel ezt az értéket, mert később nem tudja lekérni a kulcsot. A kulcsértéket olyan helyen tárolja, ahonnan az alkalmazás le tudja kérni.

![Kulcs értéke Figyelmeztetés mentett kulcshoz.](./media/azure-stack-create-service-principal/image15.png)

A végső lépés [az alkalmazás-szerepkör hozzárendelése](azure-stack-create-service-principals.md#assign-role-to-service-principal).

## <a name="create-service-principal-for-ad-fs"></a>Az AD FS egyszerű szolgáltatás létrehozása

Ha telepítette az AD FS segítségével használva identity Azure verem, PowerShell használható a következő feladatokat:

* Hozzon létre egy egyszerű szolgáltatást.
* Szolgáltatás egyszerű hozzárendelése egy szerepkörhöz.
* Jelentkezzen be az egyszerű szolgáltatás identitás használatával.

Az egyszerű szolgáltatás létrehozása a részletekért lásd: [egyszerű szolgáltatásnév létrehozása az AD FS](../azure-stack-create-service-principals.md#create-service-principal-for-ad-fs).

## <a name="assign-the-service-principal-to-a-role"></a>A szolgáltatás egyszerű hozzárendelése egy szerepkörhöz

Az előfizetés az erőforrások eléréséhez az alkalmazást egy szerepkörhöz kell rendelni. Döntse el, melyik szerepkört jelöli a megfelelő engedélyekkel az alkalmazáshoz. A rendelkezésre álló szerepkörökkel kapcsolatos további tudnivalókért lásd: [RBAC: beépített szerepkörök](../../role-based-access-control/built-in-roles.md).

>[!NOTE]
A szerepkör hatóköre előfizetés, egy erőforráscsoport vagy egy erőforrás szinten állíthatja be. Engedélyek hatókör alacsonyabb szintre származnak. Az olvasó szerepkört erőforráscsoport alkalmazás például azt jelenti, hogy az alkalmazás elolvashatják a erőforráscsoportban található erőforrásokhoz.

Az alábbi lépésekkel útmutatóként a szerepkört rendel egy egyszerű szolgáltatást.

1. A verem Azure portálon lépjen a szintre hatókör azt szeretné, hogy az alkalmazást. Például egy szerepkört az előfizetés hatókörből rendeléséhez jelölje **előfizetések**.

2. Válassza ki az előfizetést, hogy az alkalmazást. Ebben a példában az előfizetés, a Visual Studio Enterprise.

     ![Válassza ki a Visual Studio Enterprise előfizetést a hozzárendeléshez](./media/azure-stack-create-service-principal/image16.png)

3. Válassza ki **hozzáférés-vezérlés (IAM)** az előfizetéshez.

     ![Válassza ki a hozzáférés-vezérlés](./media/azure-stack-create-service-principal/image17.png)

4. Válassza a **Hozzáadás** lehetőséget.

5. Jelölje ki az alkalmazáshoz hozzárendelni kívánt szerepkört.

6. Keresse meg az alkalmazást, és válassza ki azt.

7. Válassza ki **OK** a szerepkör hozzárendelése befejezéséhez. Az alkalmazás a listában, hogy a hatókör adott szerepkörhöz rendelt felhasználók tekintheti meg.

Most, hogy létrehozni egy egyszerű szolgáltatást és szerepkört rendeltek, az alkalmazás az Azure-verem erőforrásait is elérik.

## <a name="next-steps"></a>További lépések

[Felhasználói engedélyek kezelése](azure-stack-manage-permissions.md)
