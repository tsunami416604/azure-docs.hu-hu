---
title: Blobok és várólisták elérésének engedélyezése az Active Directory használatával
titleSuffix: Azure Storage
description: Engedélyezze a hozzáférést az Azure-blobokhoz és várólistákhoz az Azure Active Directory használatával.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 2/23/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: b8a42723a9b56665160e660c0ea1451253c3d185
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255365"
---
# <a name="authorize-access-to-blobs-and-queues-using-azure-active-directory"></a>Blobok és várólisták elérésének engedélyezése az Azure Active Directory használatával

Az Azure Storage támogatja az Azure Active Directory (Azure AD) használatát a Blob és a Queue storage-ba vonatkozó kérelmek engedélyezéséhez. Az Azure AD használatával szerepköralapú hozzáférés-vezérlés (RBAC) segítségével engedélyeket adhat egy rendszerbiztonsági tagnak, amely lehet egy felhasználó, csoport vagy egyszerű alkalmazásszolgáltatás. A rendszerbiztonsági tag az Azure AD által hitelesített egy OAuth 2.0-s jogkivonat ot ad vissza. A jogkivonat ezután blob vagy várólista-tároló elleni kérelem engedélyezésére használható.

Az Azure Storage-szal szembeni kérelmek engedélyezése az Azure AD-vel kiváló biztonságot és egyszerű használatot biztosít a megosztott kulcs engedélyezése helyett. A Microsoft azt javasolja, hogy az Azure AD-hitelesítést a blob és a várólista-alkalmazások, ha lehetséges, a megosztott kulcsban rejlő potenciális biztonsági rések minimalizálása érdekében.

Az Azure AD-vel való engedélyezés minden általános célú és Blob storage-fiókhoz elérhető az összes nyilvános régióban és a nemzeti felhőkben. Csak az Azure Resource Manager telepítési modellel létrehozott tárfiókok támogatják az Azure AD-hitelesítést.

A Blob Storage emellett támogatja az Azure AD-hitelesítő adatokkal aláírt megosztott hozzáférés-aláírások (SAS) létrehozását. További információ: [Korlátozott hozzáférés megadása megosztott hozzáférésű hozzáféréssel rendelkező adatokhoz.](storage-sas-overview.md)

Az Azure Files támogatja az AD (előzetes verzió) vagy az Azure AD DS (GA) csak a tartományhoz csatlakozott virtuális gépek en keresztül id. Az AD (előzetes verzió) vagy az Azure AD DS (GA) SMB-n keresztüli használatáról az SMB-fájlok használatának áttekintése című témakörben olvashat az [SMB-hozzáférés identitásalapú hitelesítési támogatásának áttekintése](../files/storage-files-active-directory-overview.md)című témakörben.

Az Azure AD-vel történő engedélyezés nem támogatott az Azure Table storage esetében. A Megosztott kulcs használatával engedélyezheti a table storage-ba irányuló kérelmeket.

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>Az Azure AD áttekintése a blobok és várólisták

Amikor egy rendszerbiztonsági tag (egy felhasználó, csoport vagy alkalmazás) megpróbál hozzáférni egy blob- vagy várólista-erőforráshoz, a kérelmet engedélyezni kell, kivéve, ha névtelen hozzáféréshez elérhető blob. Az Azure AD-vel az erőforráshoz való hozzáférés két lépésből áll. Először a rendszerbiztonsági tag identitása hitelesítve van, és egy OAuth 2.0-s jogkivonatot ad vissza. Ezután a jogkivonat a Blob vagy a Várólista-szolgáltatáskérés részeként kerül átadásra, és a szolgáltatás a megadott erőforráshoz való hozzáférés engedélyezéséhez.

A hitelesítési lépés megköveteli, hogy egy alkalmazás futásidőben oauth 2.0-s hozzáférési jogkivonatot kérjen. Ha egy alkalmazás egy Azure-entitáson belül, például egy Azure virtuális gép, egy virtuális gép méretezési csoport vagy egy Azure Functions-alkalmazás, [használhatja](../../active-directory/managed-identities-azure-resources/overview.md) a felügyelt identitás blobok vagy várólisták eléréséhez. Ha meg szeretné tudni, hogyan engedélyezheti a felügyelt identitás által az Azure Blob vagy a Queue szolgáltatás számára tett kérelmeket, olvassa [el a Blobok és várólisták hozzáférésének engedélyezése az Azure Active Directoryval és az Azure Resources felügyelt identitásaival című témakört.](storage-auth-aad-msi.md)

Az engedélyezési lépés megköveteli, hogy egy vagy több RBAC szerepkörök legyenek hozzárendelve a rendszerbiztonsági taghoz. Az Azure Storage RBAC-szerepköröket biztosít, amelyek a blob- és várólista-adatok hoz való közös engedélyeket foglalják magukban. A rendszerbiztonsági taghoz rendelt szerepkörök határozzák meg a rendszerbiztonsági tag engedélyeit. Ha többet szeretne tudni az RBAC-szerepkörök Azure Storage-hoz való hozzárendeléséről, [olvassa el a Tárolási adatokhoz való hozzáférési jogok kezelése az RBAC-mal című témakört.](storage-auth-aad-rbac.md)

Natív alkalmazások és webes alkalmazások, amelyek az Azure Blob vagy queue szolgáltatás kéréseket is engedélyezheti az Azure AD-vel. Ha meg szeretné tudni, hogyan kérhet hozzáférési jogkivonatot, és hogyan használhatja azt blob- vagy várólista-adatokra vonatkozó kérelmek engedélyezésére, olvassa [el az Azure Storage-hoz való hozzáférés engedélyezése az Azure Storage-mal egy Azure Storage-alkalmazásból.](storage-auth-aad-app.md)

## <a name="assign-rbac-roles-for-access-rights"></a>RBAC-szerepkörök hozzárendelése a hozzáférési jogokhoz

Az Azure Active Directory (Azure AD) [szerepköralapú hozzáférés-vezérlés (RBAC)](../../role-based-access-control/overview.md)révén engedélyezi a védett erőforrásokhoz való hozzáférési jogokat. Az Azure Storage a blob- és várólista-adatok eléréséhez használt közös engedélyeket tartalmazó beépített RBAC-szerepkörök készletét határozza meg. Egyéni szerepköröket is definiálhat a blob- és várólista-adatok eléréséhez.

Ha egy RBAC-szerepkör egy Azure AD rendszerbiztonsági taghoz van rendelve, az Azure hozzáférést biztosít ezekhez az erőforrásokhoz az adott rendszerbiztonsági tag számára. Az access az előfizetés, az erőforráscsoport, a tárfiók vagy egy adott tároló vagy várólista szintjére ható. Az Azure AD rendszerbiztonsági tag lehet egy felhasználó, egy csoport, egy egyszerű alkalmazás vagy egy [felügyelt identitás az Azure-erőforrások.](../../active-directory/managed-identities-azure-resources/overview.md)

### <a name="built-in-rbac-roles-for-blobs-and-queues"></a>Beépített RBAC-szerepkörök blobokhoz és várólistákhoz

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

A beépített RBAC-szerepkör rendszerbiztonsági taghoz rendeléséről az alábbi cikkekben olvashat:

- [Hozzáférés biztosítása Azure-blobok és -üzenetsorok adataihoz RBAC használatával az Azure Portalon](storage-auth-aad-rbac-portal.md)
- [Hozzáférés biztosítása Azure-blobok és -üzenetsorok adataihoz RBAC használatával az Azure CLI-vel](storage-auth-aad-rbac-cli.md)
- [Hozzáférés biztosítása Azure-blobok és -üzenetsorok adataihoz RBAC használatával a PowerShell-lel](storage-auth-aad-rbac-powershell.md)

A beépített szerepkörök Azure Storage-hoz való definiálásáról a [Szerepkör-definíciók ismertetése című témakörben](../../role-based-access-control/role-definitions.md#management-and-data-operations)talál további információt. Az egyéni RBAC-szerepkörök létrehozásáról az [Egyéni szerepkörök létrehozása az Azure szerepköralapú hozzáférés-vezérléshez című](../../role-based-access-control/custom-roles.md)témakörben talál további információt.

### <a name="access-permissions-for-data-operations"></a>Hozzáférési engedélyek az adatműveletekhez

Az adott Blob- vagy várólista-szolgáltatás műveleteinek hívásához szükséges engedélyekről a [Blob- és várólista-adatok műveleteinek hívásához szükséges engedélyek](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)című témakörben talál további információt.

## <a name="resource-scope"></a>Erőforrás hatóköre

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="access-data-with-an-azure-ad-account"></a>Adatok elérése Azure AD-fiókkal

Blob- vagy várólista-adatokhoz való hozzáférés az Azure Portalon, a PowerShellen vagy az Azure CLI-n keresztül a felhasználó Azure AD-fiókjával vagy a fiók hozzáférési kulcsaival (megosztott kulcs engedélyezése) keresztül engedélyezhető.

### <a name="data-access-from-the-azure-portal"></a>Adathozzáférés az Azure Portalról

Az Azure Portalhasználhatja az Azure AD-fiók vagy a fiók hozzáférési kulcsok eléréséhez blob és a várólista-adatok egy Azure storage-fiókban. Az Azure Portal által használt engedélyezési séma az Önhöz rendelt RBAC-szerepköröktől függ.

Amikor megpróbál hozzáférni a blob- vagy várólista-adatokhoz, az Azure Portal először ellenőrzi, hogy rbac-szerepkört kapott-e a **Microsoft.Storage/storageAccounts/listkeys/action szolgáltatással.** Ha ezzel a művelettel szerepkört kapott, majd az Azure Portal a fiókkulcsot használja a blob- és várólista-adatok megosztott kulcs-engedélyezésen keresztüli eléréséhez. Ha nem kapott szerepkört ezzel a művelettel, majd az Azure Portal megkísérli az adatok elérését az Azure AD-fiók használatával.

A blob- vagy várólista-adatok elérése az Azure-portálról az Azure AD-fiók használatával, engedélyeket kell elérnie a blob- és várólista-adatok eléréséhez, és engedélyekre is szüksége van a tárfiók-erőforrások on-the Azure Portalon való navigáláshoz. Az Azure Storage által biztosított beépített szerepkörök hozzáférést biztosítanak a blob- és várólista-erőforrásokhoz, de nem adnak engedélyeket a tárfiók-erőforrásokhoz. Ebből az okból a portálhoz való hozzáférés hez egy Azure Resource Manager-szerepkör, például az [Olvasó](../../role-based-access-control/built-in-roles.md#reader) szerepkör hozzárendelése is szükséges, a tárfiók vagy magasabb szintű. Az **Olvasó** szerepkör a legtöbb korlátozott engedélyeket adja, de egy másik Azure Resource Manager szerepkör, amely hozzáférést biztosít a tárfiók-kezelési erőforrásokhoz, szintén elfogadható. Ha többet szeretne megtudni arról, hogyan rendelhet engedélyeket a felhasználóknak az Azure Portalon való adathozzáféréshez egy Azure AD-fiókkal, olvassa el [az Azure blob- és várólista-adatok hoz való hozzáférés megadása az RBAC használatával című témakört az Azure Portalon.](storage-auth-aad-rbac-portal.md)

Az Azure Portal jelzi, hogy melyik engedélyezési séma van használatban, amikor egy tárolóhoz vagy várólistához navigál. A portálon lévő adathozzáférésről további információt [a Blob- vagy várólistaadatok eléréséhez az Azure Portal használata című témakörben](storage-access-blobs-queues-portal.md)talál.

### <a name="data-access-from-powershell-or-azure-cli"></a>Adathozzáférés a PowerShellből vagy az Azure CLI-ből

Az Azure CLI és a PowerShell támogatja a bejelentkezést az Azure AD hitelesítő adataival. Miután bejelentkezett, a munkamenet a hitelesítő adatok alatt fut. További információ: [Azure CLI- vagy PowerShell-parancsok futtatása Azure AD-hitelesítő adatokkal a blob- vagy várólista-adatok eléréséhez.](authorize-active-directory-powershell.md)

## <a name="next-steps"></a>További lépések

- [Blobok és várólisták elérésének engedélyezése az Azure Active Directoryval és az Azure Resources felügyelt identitásaival](storage-auth-aad-msi.md)
- [Engedélyezés az Azure Active Directoryval egy alkalmazásból a blobokhoz és várólistákhoz való hozzáféréshez](storage-auth-aad-app.md)
- [Az Azure Storage általánosan elérhető Azure Directory-alapú hozzáférés-vezérléstámogatása](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)
