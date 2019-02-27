---
title: Az Azure AD hozzáférési jogosultságokat az RBAC (előzetes verzió) – Azure Storage üzenetsorok és tárolók kezelése az Azure portal használatával |} A Microsoft Docs
description: Szerepköralapú hozzáférés-vezérlés (RBAC) az Azure Portal használatával hozzáférés hozzárendelése a tárolókhoz és a rendszerbiztonsági tagok üzenetsorok. Az Azure Storage támogatja a beépített és egyéni RBAC-szerepkörök az Azure AD-n keresztül hitelesítést.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/25/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 622a7bc870aba58205c1811de2fcdcabffd177e5
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56869683"
---
# <a name="grant-access-to-azure-containers-and-queues-with-rbac-in-the-azure-portal-preview"></a>Hozzáférés engedélyezése az Azure-tárolók és a várólisták RBAC használata az Azure Portalon (előzetes verzió)

Az Azure Active Directory (Azure AD) keresztül biztonságos erőforrásokhoz való hozzáférési jogosultságok engedélyezi [szerepköralapú hozzáférés-vezérlés (RBAC)](../../role-based-access-control/overview.md). Az Azure Storage határozza meg, amely magában foglalja a tárolók és -várólisták eléréséhez használt gyakori jogosultságkészletek beépített RBAC-szerepkör. 

Az RBAC szerepkör van rendelve egy Azure AD rendszerbiztonsági tag, amikor az Azure biztosít hozzáférést az ezeket az erőforrásokat, hogy a rendszerbiztonsági tag esetében. Hozzáférés az előfizetéshez, az erőforráscsoport, a storage-fiókban vagy egy tároló vagy üzenetsor szintjét hatóköre. Az Azure AD rendszerbiztonsági tag lehet, hogy egy felhasználó, csoport, egy alkalmazás egyszerű szolgáltatást, vagy egy [-identitás az Azure-erőforrások](../../active-directory/managed-identities-azure-resources/overview.md).

Ez a cikk ismerteti, hogyan használhatja az Azure Portalon rendeljen RBAC-szerepköröket. Az Azure Portalon egy egyszerű felületet biztosít az RBAC-szerepkörök hozzárendelése és a tárolási erőforrásokhoz való hozzáférés kezelése. RBAC-szerepkörök az Azure parancssori eszközeivel vagy az Azure Storage felügyeleti API-k blob és üzenetsor erőforrásokhoz is hozzárendelhetők. A tárolási erőforrások RBAC-szerepkörök kapcsolatos további információkért lásd: [hitelesítés hozzáférés az Azure-blobok és üzenetsorok az Azure Active Directory (előzetes verzió) használatával](storage-auth-aad.md). 

## <a name="rbac-roles-for-blobs-and-queues"></a>A blobok és üzenetsorok RBAC-szerepkörök

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Erőforrás-hatókör meghatározása 

Az RBAC-szerepkört rendel egy rendszerbiztonsági tag, mielőtt határozza meg, hogy a rendszerbiztonsági tagot kell hozzáférés hatókörét. Ajánlott eljárások szabályozzák, hogy minden esetben célszerű csak a minimális lehetséges hatókör megadása.

Az alábbi lista ismerteti a szintek milyen gazdagépcsoportjaira az Azure blob és üzenetsor erőforrásokhoz való hozzáférés a minimális hatókör kezdve:

- **Tároló.** Ebben a hatókörben egy rendszerbiztonsági tag hozzáfér az összes, a tárolóba, valamint a tároló tulajdonságainak és metaadatainak blobok.
- **Az egyes üzenetsorába.** Ebben a hatókörben egy rendszerbiztonsági tag üzeneteket az üzenetsor, valamint a várólista-tulajdonságok és metaadatok hozzáféréssel rendelkezik.
- **A storage-fiók.** Ebben a hatókörben egy rendszerbiztonsági tag hozzáfér a blobok és az összes tárolót, vagy valamennyi üzenetsorok és az üzenetek.
- **Az erőforráscsoport.** Ebben a hatókörben egy rendszerbiztonsági tag hozzáfér az összes, a tárolók és a tárfiókot az erőforráscsoportban lévő összes üzenetsor.
- **Az előfizetés.** Ebben a hatókörben egy rendszerbiztonsági tag hozzáfér az összes, a tárolók és az üzenetsorok, az összes, az erőforráscsoport az előfizetésben található összes tárfiókot.

Ha megadta, hogy a kívánt hatókörhöz a szerepkör-hozzárendelés, navigáljon az Azure Portalon a megfelelő erőforrás. Megjelenítés a **hozzáférés-vezérlés (IAM)** az erőforrás beállításait, és kövesse az ezt követő szakaszok a szerepkör-hozzárendelések kezeléséhez.

## <a name="assign-rbac-roles-using-the-azure-portal"></a>Rendeljen RBAC-szerepköröket az Azure portal használatával

Az Azure AD hitelesítő adatait az Azure Portalon blob és üzenetsor erőforrásokhoz való hozzáférés biztosítása az alábbi lépésekből áll: 

1. Rendelje hozzá a megfelelő Azure Storage RBAC-szerepkörök, tárolók vagy várólisták hozzáférést. Olvasási hozzáférés, rendelje hozzá a **Blobadatok olvasója (előzetes verzió)** vagy **Üzenetsorbeli adatok olvasója (előzetes verzió)** szerepkör. Olvasási, írási és törlési hozzáférésének, rendelje hozzá a **Blobadatok Közreműködője (előzetes verzió)** vagy **Üzenetsorbeli adatok Közreműködője (előzetes verzió)** szerepkör. Egy egyéni szerepkört is hozzárendelhetők.

1. Rendelje hozzá az Azure Resource Manager [olvasó](../../role-based-access-control/built-in-roles.md#reader) szerepkör tárolók vagy az Azure Portalon az Azure AD hitelesítő adataikkal üzenetsorok elérni kívánó felhasználók számára. 

A következő szakaszok ismertetik az egyes lépéseket részletesen.

### <a name="assign-a-built-in-rbac-role"></a>Beépített RBAC-szerepkör hozzárendelése

Mielőtt egy szerepkört rendel egy rendszerbiztonsági tag, mindenképp vegye figyelembe a hatókörébe tartozó engedélyeket oszt ki. Tekintse át a [erőforrás hatókörének meghatározásához](#determine-resource-scope) szakasz dönthet arról, hogy a megfelelő hatókörben.

Az itt bemutatott eljárás hozzárendel egy szerepkör hatóköre egy tároló, de várólista hatóköre szerepkör hozzárendelése ugyanazokat a lépéseket követheti: 

1. Az a [az Azure portal](https://portal.azure.com), lépjen a tárfiókhoz, és megjeleníti a **áttekintése** a fiókhoz.
1. A szolgáltatások területen válassza ki a **Blobok**. 
1. Keresse meg a helyet, amelynek meg szeretné rendelhet egy szerepkört, és megjeleníti a tároló beállításait. 
1. Válassza ki **hozzáférés-vezérlés (IAM)** a tárolóhoz a hozzáférés-vezérlési beállítások megjelenítéséhez. Válassza ki a **szerepkör-hozzárendelések** lapján megtekintheti a szerepkör-hozzárendelések listáját.

    ![Képernyő-tároló hozzáférés-vezérlési beállításokkal bemutató képernyőkép](media/storage-auth-aad-rbac/portal-access-control-container.png)

1. Kattintson a **szerepkör-hozzárendelés hozzáadása** gombra kattintva adhat hozzá egy új szerepkör.
1. Az a **szerepkör-hozzárendelés hozzáadása** ablakban válassza ki a hozzárendelni kívánt Azure Storage-szerepkört. Ezután keresse meg a rendszerbiztonsági tagot, amelyhez szeretné, hogy a szerepkör hozzárendelése.

    ![Az RBAC szerepkör hozzárendelése bemutató képernyőkép](media/storage-auth-aad-rbac/add-rbac-role.png)

1. Kattintson a **Save** (Mentés) gombra. Az identitás, akinek a szerepköre alatt a szerepkör a listán jelenik meg. Ha például az alábbi képen látható, hogy a hozzáadott felhasználó már rendelkezik olvasási engedéllyel a nevű tárolóban lévő adatok *minta-tároló*.

    ![Képernyőn egy szerepkörhöz rendelt felhasználók listáját bemutató képernyőkép](media/storage-auth-aad-rbac/container-scoped-role.png)

A storage-fiók, erőforráscsoport vagy előfizetés hatóköre szerepkör hozzárendelése hasonló lépéseket követheti.

> [!NOTE]
> Az Azure Storage-fiók tulajdonosai akkor nem lesznek automatikusan hozzárendelve engedélyeket az adatok eléréséhez. Kell explicit módon saját magának egy RBAC szerepkör hozzárendelése az Azure Storage. Az előfizetés, erőforráscsoport, tárfiók, vagy egy tároló vagy üzenetsor szintjén rendelhet.
> 
> A hatókörrel rendelkező egy tároló vagy egy üzenetsor, ha a tárfiók engedélyezve hierarchikus névtérrel rendelkező szerepkör nem rendelhető hozzá.

### <a name="assign-the-reader-role-for-portal-access"></a>A portál elérésére szolgáló olvasó szerepkör hozzárendelése

Rendel egy beépített vagy egyéni szerepkört az Azure Storage egy rendszerbiztonsági tag, amikor a tárfiókban lévő adatokat a műveletek végrehajtásához, hogy a rendszerbiztonsági tag engedélyeket oszt ki. A beépített **Adatolvasó** szerepkörök adja meg a tároló vagy üzenetsor, az adatok olvasási engedélyeket a során a beépített **Data Közreműködője** szerepkörök adja meg az olvasási, írási és törlési engedélyek tárolóba vagy üzenetsor. A megadott erőforrás engedélyek hatóköre.  

Például, ha a **Storage-Blobadatok Közreműködője (előzetes verzió)** Mary nevű tároló szintű felhasználói szerepkört **minta-tároló**, majd Mary nyújtott olvasási, írási és törlési minden a tárolóban lévő blobokat.

Azonban, ha Mary szeretne egy blob megtekintése az Azure Portalon, akkor a **Storage-Blobadatok Közreműködője (előzetes verzió)** szerepkör önmagában nem biztosít megfelelő engedélyekkel ahhoz, hogy megtekintse a blobba a portálon lépjen. További Azure AD-engedélyekről keresse meg a portálon keresztül, és láthatóvá vannak más erőforrások megtekintéséhez szükséges.

Ha a felhasználóknak képesnek kell lenniük az Azure Portal blobok elérése, majd rendelje hozzá őket egy további RBAC-szerepkör a [olvasó](../../role-based-access-control/built-in-roles.md#reader) szerepkört ezen a felhasználók számára. A **olvasó** szerepkör egy Azure Resource Manager-szerepkör, amely lehetővé teszi a felhasználók megtekinthetik a tárfiók erőforrásainak, de nem módosíthatók. Nem biztosít az adatok Azure Storage-ban, de csak a fiók felügyeleti erőforrásokhoz olvasási engedéllyel.

Rendelje hozzá a következő lépésekkel a **olvasó** szerepkört, hogy egy felhasználó hozzáférhessen a blobok az Azure Portalról. Ebben az esetben a hozzárendelés hatókörét a tárolót:

1. Az a [az Azure portal](https://portal.azure.com), lépjen a tárfiókhoz, és megjeleníti a **áttekintése** a fiókhoz.
1. A szolgáltatások területen válassza ki a **Blobok**. 
1. Keresse meg a helyet, amelynek meg szeretné rendelhet egy szerepkört, és megjeleníti a tároló beállításait. 
1. Válassza ki **hozzáférés-vezérlés (IAM)** a tárolóhoz a hozzáférés-vezérlési beállítások megjelenítéséhez. Válassza ki a **szerepkör-hozzárendelések** lapján megtekintheti a szerepkör-hozzárendelések listáját.
1. Az a **szerepkör-hozzárendelés hozzáadása** ablakban válassza ki a **olvasó** szerepkör. 
1. Az a **rendelhet hozzáféréseket** legördülő menüben válassza **az Azure AD-felhasználó, csoport vagy szolgáltatásnév**.
1. Keresse meg a rendszerbiztonsági tagot, amelyhez szeretné rendelni a szerepkört.
1. A szerepkör-hozzárendelés mentése.

> [!NOTE]
> Az Olvasó szerepkör hozzárendelése a csak blobok vagy üzenetsorok az Azure portal használatával elérni kívánó felhasználók számára szükség. 

## <a name="use-azure-ad-credentials-with-the-portal"></a>Az Azure AD hitelesítő adatait használja a portállal

Blobok vagy üzenetsorok használatával az Azure AD hitelesítő adatait az Azure Portal eléréséhez használja az előzetes verzió hivatkozásokat a következő képen látható:

![Hozzáférés blobok vagy üzenetsorok az Azure AD hitelesítő adatai a portálon](media/storage-auth-aad-rbac/access-data-azure-ad.png)

Ha éri el az előzetes verzió hivatkozások helyett az éles hivatkozások blob és üzenetsor adatokat, az Azure Portalon a fiókkulcs használatával engedélyezze a hozzáférést, nem pedig az Azure AD.

## <a name="next-steps"></a>További lépések

- RBAC kapcsolatos további információkért lásd: [Mi a szerepköralapú hozzáférés-vezérlés (RBAC)?](../../role-based-access-control/overview.md).
- Ismerje meg, hogyan hozzárendelése és kezelése az Azure PowerShell, az Azure CLI vagy a REST API az RBAC szerepkör-hozzárendeléseit, tanulmányozza a következő cikkeket:
    - [Kezelheti a szerepköralapú hozzáférés-vezérlés (RBAC) az Azure PowerShell használatával](../../role-based-access-control/role-assignments-powershell.md)
    - [Kezelheti a szerepköralapú hozzáférés-vezérlés (RBAC) az Azure CLI-vel](../../role-based-access-control/role-assignments-cli.md)
    - [Szerepköralapú hozzáférés-vezérlés (RBAC) a REST API-val kezelheti.](../../role-based-access-control/role-assignments-rest.md)
- A tárolókhoz és üzenetsorok, a storage-alkalmazásokban való elérésének hitelesítéséhez, lásd: [az Azure AD használata az Azure Storage-alkalmazások](storage-auth-aad-app.md).
- Az Azure-tárolók és -üzenetsorok az Azure AD-integrációval kapcsolatos további információkért lásd: az Azure Storage csapat blogja tenne fel, [bejelentése a megtekintése az Azure AD-hitelesítés az Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
