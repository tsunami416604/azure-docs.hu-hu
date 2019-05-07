---
title: Hitelesíti a hozzáférést az Azure-blobok és üzenetsorok az Azure Active Directory |} A Microsoft Docs
description: Azure-blobok és üzenetsorok az Azure Active Directoryval való hozzáférés hitelesítéséhez.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 66051bd0f8be349f748c72218d538bba273be8f6
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65147266"
---
# <a name="authenticate-access-to-azure-blobs-and-queues-using-azure-active-directory"></a>Hitelesíti a hozzáférést az Azure-blobok és üzenetsorok az Azure Active Directoryval

Az Azure Storage támogatja a hitelesítés és engedélyezés az Azure Active Directory (AD) a Blob és üzenetsor-szolgáltatások. Az Azure ad-vel szerepköralapú hozzáférés-vezérlés (RBAC) használhatja hozzáférést a felhasználók, csoportok és alkalmazások szolgáltatásnevének. 

Felhasználók vagy az Azure AD hitelesítő adatait használó alkalmazások hitelesítése készít felső szintű biztonság és a könnyű használat más engedélyezési módot. Miközben továbbra is megosztott kulcsos engedélyezési használata az alkalmazások, Azure AD-vel megkerüli ügyféladataik tárolásának a kód a hozzáférési kulcsára. Közös hozzáférésű jogosultságkódok (SAS) használata a minden részletre kiterjedő hozzáférést biztosítani a tárfiókban lévő erőforrásokhoz folytathatja, de az Azure AD kezelése SAS-tokeneket vagy sérült biztonságú SAS visszavonása foglalkoznia kellene hasonló funkciókat kínál. A Microsoft javasolja, hogy az Azure AD-hitelesítés használata az Azure Storage-alkalmazásokhoz, amikor csak lehetséges.

Hitelesítés és engedélyezés az Azure AD-beli hitelesítő érhető el az összes általános célú és a Blob storage-fiókok az összes nyilvános régióban és az országos felhők. Csak a tárfiókok az Azure Resource Manager üzembe helyezési modell támogatása az Azure AD engedélyezési hoz létre.

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>Blobok és üzenetsorok az Azure ad-ben – áttekintés

Amikor egy blob és üzenetsor-erőforrás eléréséhez megpróbál egy rendszerbiztonsági tag (egy felhasználó, csoport vagy alkalmazás), a kérelem kell engedélyezni, kivéve, ha egy blob érhető el a névtelen hozzáférés. Az Azure AD-erőforrásokhoz való hozzáférést két lépésből áll. Először a rendszerbiztonsági tag identitás hitelesítve van, és a egy OAuth 2.0 jogkivonat adja vissza. Ezután a jogkivonatot egy kérés részeként továbbítja a Blob és üzenetsor-szolgáltatás és a szolgáltatás a megadott erőforráshoz való hozzáférés engedélyezésére használja.

A hitelesítési lépés szükséges, hogy egy alkalmazás kérése futásidőben az OAuth 2.0 hozzáférési jogkivonatot. Ha egy alkalmazás például egy Azure virtuális gép, virtuálisgép-méretezési csoportot vagy egy Azure Functions-alkalmazás egy Azure entitáson belül fut, használhat egy [identitás](../../active-directory/managed-identities-azure-resources/overview.md) való hozzáférés blobok vagy üzenetsorok. Megtudhatja, hogyan engedélyezheti a kérést hoz létre egy felügyelt identitás az Azure Blob- vagy várólista szolgáltatáshoz, talál [hitelesíti a blobok és üzenetsorok az Azure Active Directory és a felügyelt identitásokból hozzáférést az Azure-erőforrások](storage-auth-aad-msi.md).

Az engedélyezési lépés szükséges, hogy egy vagy több RBAC-szerepkört kell rendelni a rendszerbiztonsági tagot. Az Azure Storage blob és üzenetsor adatok közös jogosultságkészletek építőelemekkel RBAC-szerepkörök biztosít. A hozzárendelt rendszerbiztonsági szerepköröket határozza meg, hogy az engedélyeket, az egyszerű lesz. RBAC-szerepkörök hozzárendelése az Azure Storage kapcsolatos további információkért lásd: [kezelés hozzáférési jogosultsága ahhoz, hogy az RBAC tárolási adatok](storage-auth-aad-rbac.md).

Az Azure ad-vel is hitelesítheti a natív alkalmazások és webalkalmazások, amelyek a kéréseket küld az Azure Blob vagy a Queue szolgáltatás. Hozzáférési jogkivonat kérése, és engedélyezze az adatok blob és üzenetsor használatával kapcsolatban lásd: [hitelesítés az Azure Storage-alkalmazás Azure AD-vel](storage-auth-aad-app.md).

## <a name="assigning-rbac-roles-for-access-rights"></a>RBAC-szerepkörök hozzáférési engedélyek hozzárendelése

Az Azure Active Directory (Azure AD) keresztül biztonságos erőforrásokhoz való hozzáférési jogosultságok engedélyezi [szerepköralapú hozzáférés-vezérlés (RBAC)](../../role-based-access-control/overview.md). Az Azure Storage blob és üzenetsor adatok eléréséhez használt gyakori jogosultságkészletek építőelemekkel beépített RBAC-szerepkör egy csoportját határozza meg. Egyéni szerepkörök hozzáférési adatok blob és üzenetsor is definiálhat.

Az RBAC szerepkör van rendelve egy Azure AD rendszerbiztonsági tag, amikor az Azure biztosít hozzáférést az ezeket az erőforrásokat, hogy a rendszerbiztonsági tag esetében. Hozzáférés az előfizetéshez, az erőforráscsoport, a storage-fiókban vagy egy tároló vagy üzenetsor szintjét hatóköre. Az Azure AD rendszerbiztonsági tag lehet, hogy egy felhasználó, csoport, egy alkalmazás egyszerű szolgáltatást, vagy egy [-identitás az Azure-erőforrások](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="built-in-rbac-roles-for-blobs-and-queues"></a>A blobok és üzenetsorok beépített RBAC-szerepkörök

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

Egy beépített RBAC-szerepkör hozzárendelése egy rendszerbiztonsági tag, lásd: egyet az alábbi cikkeket:

- [Hozzáférés engedélyezése az Azure blob és üzenetsor adatok RBAC használata az Azure Portalon](storage-auth-aad-rbac-portal.md)
- [Az RBAC Azure CLI-vel az Azure blob és üzenetsor adatokhoz való hozzáférés engedélyezése](storage-auth-aad-rbac-cli.md)
- [Az RBAC a PowerShell-lel az Azure blob és üzenetsor adatokhoz való hozzáférés engedélyezése](storage-auth-aad-rbac-powershell.md)

További információ a beépített szerepkörök határozza meg az Azure Storage, lásd: [megismerheti a szerepkör-definíciók](../../role-based-access-control/role-definitions.md#management-and-data-operations-preview). Egyéni RBAC-szerepkörök létrehozásával kapcsolatos információkért lásd: [egyéni szerepkörök létrehozása az Azure szerepköralapú hozzáférés-vezérlés](../../role-based-access-control/custom-roles.md).

### <a name="access-permissions-for-data-operations"></a>Hozzáférési engedélyek műveletekhez

További információ az adott Blob és üzenetsor-szolgáltatás műveletek meghívásához szükséges engedélyekről: [hívása blob és üzenetsor Adatműveletek engedélyeinek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

## <a name="resource-scope"></a>Erőforrás-hatókör

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="access-data-with-an-azure-ad-account"></a>Az Azure AD-fiókkal érheti el adatait

Az Azure Portalon, PowerShell, a blob és üzenetsor adatokhoz való hozzáférést, vagy az Azure CLI-vel jogosult-e a felhasználó Azure AD-fiók használatával, vagy a fiók hozzáférési kulcsait (megosztott kulcsos hitelesítés) használatával.

### <a name="data-access-from-the-azure-portal"></a>Az Azure Portalról adatelérés

Az Azure portal használatával vagy az Azure AD-fiókot, vagy a hozzáférési kulcsainak az Azure storage-fiók blob és üzenetsor adatok eléréséhez. Melyik engedélyezési sémát, használja az Azure Portalon az RBAC-szerepkörök Önhöz rendelt függ.

Amikor megpróbálja blob és üzenetsor-adatok elérése, az Azure Portalon először ellenőrzi e van hozzájuk rendelve egy RBAC szerepkör- **Microsoft.Storage/storageAccounts/listkeys/action**. Ha Ön egy szerepkört rendeltek ezt a műveletet, ezután az Azure Portalon a fiókkulcs keresztül megosztott kulcsos engedélyezési blob és üzenetsor adatainak eléréséhez. Ha rendelkezik nincs hozzárendelve egy szerepkörhöz, ezt a műveletet, majd az Azure Portalon megpróbál hozzáférni adatokat az Azure AD-fiók használatával.

Blob- vagy várólista eléréséhez blob és üzenetsor-adatokhoz való hozzáféréshez engedélyek szükségesek az Azure portal használata az Azure AD-fiók adatait, és nyissa meg a tárfiók erőforrásainak az Azure Portalon keresztül engedélyek is szükségesek. Az Azure Storage által biztosított beépített szerepkörök blob és üzenetsor-erőforrásokhoz való hozzáférés biztosítása, de azok nem adja meg a tárfiók erőforrásainak engedélyeket. Ebből kifolyólag a portáljához való hozzáférésre is szükséges a hozzárendelés egy Azure Resource Manager-szerepkör például a [olvasó](../../role-based-access-control/built-in-roles.md#reader) szerepkör hatóköre a storage-fiók vagy magasabb szintre. A **olvasó** szerepkör engedélyt ad a legtöbb korlátozott, de egy másik Azure Resource Manager-szerepkör, amely hozzáférést biztosít a tárfiók kezelési erőforrásainak is fogadható el. Engedélyek hozzárendelése a felhasználók számára az adatok eléréséhez az Azure Portalon az Azure AD-fiókkal kapcsolatos további információkért lásd: [hozzáférést biztosít az Azure blob és üzenetsor az adatokat az RBAC az Azure Portalon](storage-auth-aad-rbac-portal.md).

Az Azure Portalon azt jelzi, hogy mely hitelesítési séma a tároló vagy üzenetsor lépve használatban. Az adatok elérése a portálon további információkért lásd: [az Azure Portallal blob és üzenetsor adatok elérését](storage-access-blobs-queues-portal.md).

### <a name="data-access-from-powershell-or-azure-cli"></a>Adatelérési Powershellből vagy az Azure CLI-vel

Az Azure CLI és PowerShell támogatja a bejelentkezés az Azure AD hitelesítő adatait. Miután bejelentkezett, a munkamenet ezeket a hitelesítő adatokat alatt fut. További tudnivalókért lásd: [futtatása az Azure parancssori felület vagy PowerShell-parancsokat az Azure AD-beli hitelesítő adatok blob vagy a várólista eléréséhez](storage-auth-aad-script.md).

## <a name="azure-ad-authentication-over-smb-for-azure-files"></a>Az Azure Files számára az Azure AD-hitelesítés SMB-n keresztül

Az Azure Files SMB-n keresztül hitelesítést az Azure AD-tartományhoz csatlakoztatott virtuális gépek csak (előzetes verzió) támogatja. Azure AD SMB-n keresztül az Azure Files-vel kapcsolatos további információkért lásd: [áttekintése az Azure Active Directory hitelesítési SMB-n keresztül az Azure Files (előzetes verzió)](../files/storage-files-active-directory-overview.md).

## <a name="next-steps"></a>További lépések

- [Blobok és üzenetsorok az Azure Active Directory és a felügyelt identitásokból való hozzáférés hitelesítése az Azure-erőforrások](storage-auth-aad-msi.md)
- [Hitelesítés az Azure Active Directoryval egy alkalmazásból blobok és üzenetsorok eléréséhez](storage-auth-aad-app.md)
- [Az Azure Storage-támogatás az Azure Active Directory szerepköralapú hozzáférés-vezérlés általánosan elérhető](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)
