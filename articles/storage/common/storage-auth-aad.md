---
title: Blobokhoz és várólistákhoz való hozzáférés engedélyezése Active Directory használatával
titleSuffix: Azure Storage
description: Az Azure-blobokhoz és-várólistákhoz való hozzáférés engedélyezése Azure Active Directory használatával.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 445d98ab07a91b056d4cf747f7c0f4cf1cdf9d53
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74891813"
---
# <a name="authorize-access-to-blobs-and-queues-using-azure-active-directory"></a>Blobokhoz és várólistákhoz való hozzáférés engedélyezése Azure Active Directory használatával

Az Azure Storage támogatja a Azure Active Directory (AD) használatát a blob-és üzenetsor-tárolásra vonatkozó kérések engedélyezéséhez. Az Azure AD-vel szerepköralapú hozzáférés-vezérlés (RBAC) használatával adhat meg engedélyeket egy rendszerbiztonsági tag számára, amely lehet egy felhasználó, egy csoport vagy egy egyszerű szolgáltatásnév. A rendszerbiztonsági tag hitelesítése az Azure AD által OAuth 2,0 token visszaküldésével történik. A jogkivonat használatával engedélyezhető az erőforrás elérésére irányuló kérelem a blob vagy a várólista-tárolóban.

Az Azure AD által visszaadott OAuth 2,0 tokent használó felhasználók vagy alkalmazások engedélyezése kiváló biztonságot és egyszerű használatot biztosít a megosztott kulcs engedélyezési és közös hozzáférési aláírásai (SAS) számára. Az Azure AD-ben nem szükséges a fiókhoz tartozó hozzáférési kulcs tárolása a kóddal és a potenciális biztonsági rések kockázatával. Habár továbbra is használhatja a megosztott kulcsos engedélyezést az alkalmazásokkal, az Azure AD megkerülésével megkerüli a fiók hozzáférési kulcsának a kóddal való tárolásának szükségességét. Továbbra is használhatja a közös hozzáférésű aláírásokat (SAS) a Storage-fiók erőforrásaihoz való részletes hozzáférés biztosításához, de az Azure AD hasonló képességeket kínál, anélkül, hogy az SAS-tokeneket kellene kezelnie, vagy nem kell aggódnia a sérült SAS visszavonásával kapcsolatban. A Microsoft az Azure AD-hitelesítés használatát javasolja az Azure Storage-alkalmazásokhoz, ha lehetséges.

Az Azure AD-vel való engedélyezés az összes általános célú és blob Storage-fiókhoz elérhető az összes nyilvános régióban és nemzeti felhőben. Csak az Azure Resource Manager üzembehelyezési modellel létrehozott Storage-fiókok támogatják az Azure AD-hitelesítést. Az Azure AD-vel való engedélyezés nem támogatott az Azure Table Storage szolgáltatásban.

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>A blobok és a várólisták Azure AD-áttekintése

Ha egy rendszerbiztonsági tag (felhasználó, csoport vagy alkalmazás) megpróbál hozzáférni egy blob-vagy várólista-erőforráshoz, a kérést engedélyezni kell, kivéve, ha a névtelen hozzáféréshez elérhető blob. Az Azure AD-vel az erőforrásokhoz való hozzáférés kétlépéses folyamat. Először a rendszerbiztonsági tag identitása hitelesítve van, és a rendszer OAuth 2,0 tokent ad vissza. Ezt követően a tokent a rendszer a Queue szolgáltatás blobnak küldött kérelem részeként továbbítja, és a szolgáltatás azt használja, hogy engedélyezze a hozzáférést a megadott erőforráshoz.

A hitelesítési lépés megköveteli, hogy egy alkalmazás OAuth 2,0 hozzáférési tokent igényeljen futásidőben. Ha egy alkalmazás egy Azure-entitáson, például egy Azure-beli virtuális gépen, egy virtuálisgép-méretezési csoporton vagy egy Azure Functions alkalmazáson belül fut, akkor [felügyelt identitást](../../active-directory/managed-identities-azure-resources/overview.md) használhat a blobokhoz vagy várólistákhoz való hozzáféréshez. Ha meg szeretné tudni, hogyan engedélyezheti a felügyelt identitások által az Azure-Blobhoz vagy a Queue szolgáltatáshoz intézett kéréseket, tekintse meg az [Azure-erőforrások Azure Active Directory és felügyelt identitásokkal való hozzáférésének engedélyezése a blobokhoz és a várólistákhoz](storage-auth-aad-msi.md)

Az engedélyezési lépés megköveteli, hogy egy vagy több RBAC-szerepkört hozzá lehessen rendelni a rendszerbiztonsági tag számára. Az Azure Storage olyan RBAC-szerepköröket biztosít, amelyek a blob-és üzenetsor-adatkezelési engedélyek közös készleteit ölelik fel. A rendszerbiztonsági tag számára hozzárendelt szerepkörök határozzák meg, hogy a résztvevő milyen engedélyeket fog tartalmazni. Ha többet szeretne megtudni az Azure Storage-hoz készült RBAC-szerepkörök hozzárendeléséről, tekintse meg a [hozzáférési jogosultságok kezelése a RBAC](storage-auth-aad-rbac.md)szolgáltatással című témakört.

Az Azure blobnak vagy Queue szolgáltatásnak kéréseket kérő natív alkalmazások és webalkalmazások is engedélyezhetik az Azure AD-vel való hozzáférést. Ha meg szeretné tudni, hogyan kérhet hozzáférési jogkivonatot, és hogyan engedélyezheti a blob-vagy üzenetsor-adatokra vonatkozó kérelmeket, tekintse meg az Azure [Storage-hoz való hozzáférés engedélyezése Azure Storage-alkalmazásból](storage-auth-aad-app.md)című témakört.

## <a name="assign-rbac-roles-for-access-rights"></a>Hozzáférési jogosultságok RBAC-szerepköreinek kiosztása

Azure Active Directory (Azure AD) a [szerepköralapú hozzáférés-vezérlés (RBAC)](../../role-based-access-control/overview.md)segítségével engedélyezi a hozzáférési jogokat a biztonságos erőforrásokhoz. Az Azure Storage olyan beépített RBAC-szerepköröket határoz meg, amelyek a blob-és üzenetsor-adateléréshez használt engedélyek közös készleteit foglalják magukban. Egyéni szerepköröket is meghatározhat a blob-és üzenetsor-adataihoz való hozzáféréshez.

Ha egy Azure AD-rendszerbiztonsági tag egy RBAC-szerepkört rendel hozzá, az Azure hozzáférést biztosít ezen rendszerbiztonsági tag erőforrásaihoz. A hozzáférés hatóköre az előfizetés, az erőforráscsoport, a Storage-fiók vagy egy adott tároló vagy várólista szintjére is kiterjed. Az Azure AD rendszerbiztonsági tag lehet egy felhasználó, egy csoport, egy egyszerű alkalmazás vagy egy [felügyelt identitás az Azure-erőforrásokhoz](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="built-in-rbac-roles-for-blobs-and-queues"></a>A blobok és a várólisták beépített RBAC szerepkörei

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

Ha szeretné megtudni, hogyan rendeljen hozzá egy beépített RBAC-szerepkört egy rendszerbiztonsági tag számára, tekintse meg a következő cikkek egyikét:

- [Hozzáférés biztosítása Azure-blobok és -üzenetsorok adataihoz RBAC használatával az Azure Portalon](storage-auth-aad-rbac-portal.md)
- [Hozzáférés biztosítása Azure-blobok és -üzenetsorok adataihoz RBAC használatával az Azure CLI-vel](storage-auth-aad-rbac-cli.md)
- [Hozzáférés biztosítása Azure-blobok és -üzenetsorok adataihoz RBAC használatával a PowerShell-lel](storage-auth-aad-rbac-powershell.md)

További információ arról, hogy az Azure Storage beépített szerepkörei hogyan vannak definiálva. a [szerepkör-definíciók ismertetése](../../role-based-access-control/role-definitions.md#management-and-data-operations)című témakörben talál további információt. Az egyéni RBAC-szerepkörök létrehozásával kapcsolatos információkért tekintse meg [Az Azure szerepköralapú Access Control egyéni szerepköreinek létrehozása](../../role-based-access-control/custom-roles.md)című témakört.

### <a name="access-permissions-for-data-operations"></a>Az adatműveletek hozzáférési engedélyei

Az adott blob vagy Queue szolgáltatás műveletek meghívásához szükséges engedélyekkel kapcsolatos részletekért lásd: [a blob-és üzenetsor-műveletek meghívására vonatkozó engedélyek](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

## <a name="resource-scope"></a>Erőforrás hatóköre

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="access-data-with-an-azure-ad-account"></a>Az Azure AD-fiókkal való hozzáférés

A blob vagy üzenetsor adataihoz való hozzáférés a Azure Portal, a PowerShell vagy az Azure CLI használatával engedélyezhető a felhasználó Azure AD-fiókjával vagy a fiók-hozzáférési kulcsok (megosztott kulcs engedélyezése) használatával.

### <a name="data-access-from-the-azure-portal"></a>Adatokhoz való hozzáférés a Azure Portal

A Azure Portal használhatja az Azure AD-fiókot vagy a fiók elérési kulcsait egy Azure Storage-fiókban található blob-és üzenetsor-információ eléréséhez. Az Azure Portal által használt engedélyezési séma az Ön által hozzárendelt RBAC-szerepköröktől függ.

Amikor megpróbál hozzáférni a blob-vagy üzenetsor-adathoz, a Azure Portal először ellenőrzi, hogy van-e hozzárendelve RBAC szerepkör a **Microsoft. Storage/storageAccounts/listkeys műveletének beolvasása/művelettel**. Ha ehhez a művelethez hozzárendelt egy szerepkört, akkor a Azure Portal a fiók kulcsát használja a blob-és üzenetsor-adatok megosztott kulcsos hitelesítésen keresztüli eléréséhez. Ha nem rendelt hozzá szerepkört ezzel a művelettel, akkor a Azure Portal az Azure AD-fiókjával próbál hozzáférni az adatelérési ponthoz.

Ha az Azure AD-fiókkal szeretné elérni a Azure Portal blob-vagy üzenetsor-adatait, a blob-és üzenetsor-adatok eléréséhez engedélyekre van szüksége, és a Azure Portal a Storage-fiók erőforrásain is meg kell keresnie az engedélyeket. Az Azure Storage beépített szerepkörei hozzáférést biztosítanak a blob-és üzenetsor-erőforrásokhoz, de nem biztosítanak engedélyeket a Storage-fiók erőforrásaihoz. Emiatt a portálhoz való hozzáféréshez szükség van egy olyan Azure Resource Manager szerepkör hozzárendelésére is, mint például az [olvasó](../../role-based-access-control/built-in-roles.md#reader) szerepkör, a Storage-fiók szintjére vagy magasabbra. Az **olvasó** szerepkör biztosítja a legtöbb korlátozott engedélyt, de egy másik Azure Resource Manager szerepkör is elfogadható, amely hozzáférést biztosít a Storage-fiókok felügyeleti erőforrásaihoz. Ha többet szeretne megtudni arról, hogyan rendelhet engedélyeket a felhasználók számára az Azure Portal Azure AD-fiókkal való adathozzáféréshez, tekintse meg [Az Azure Blob-és üzenetsor-hozzáférés biztosítása a RBAC](storage-auth-aad-rbac-portal.md)című témakört az Azure Portal.

A Azure Portal azt jelzi, hogy melyik engedélyezési séma van használatban, amikor egy tárolót vagy várólistát navigál. További információ a portálon elérhető adathozzáférésről: [a Azure Portal használata a blob-vagy üzenetsor-adatok eléréséhez](storage-access-blobs-queues-portal.md).

### <a name="data-access-from-powershell-or-azure-cli"></a>Adatokhoz való hozzáférés a PowerShell vagy az Azure CLI használatával

Azure CLI-és PowerShell-támogatás az Azure AD-beli hitelesítő adatokkal való bejelentkezéshez. A bejelentkezés után a munkamenet ezen hitelesítő adatok alatt fut. További információ: [Azure CLI-vagy PowerShell-parancsok futtatása Azure ad-beli hitelesítő adatokkal a blob-vagy üzenetsor-adatok eléréséhez](storage-auth-aad-script.md).

## <a name="azure-ad-authorization-over-smb-for-azure-files"></a>Azure AD-hitelesítés SMB protokollon keresztül Azure Files

Azure Files támogatja az Azure AD-t az SMB protokollon keresztül a tartományhoz csatlakoztatott virtuális gépek esetében (előzetes verzió). Ha szeretné megtudni, hogyan használhatja az Azure AD-t az SMB protokollon keresztül a Azure Fileshoz, tekintse meg [az Azure Active Directory engedélyezésének áttekintése az SMB-ben Azure Files (előzetes verzió](../files/storage-files-active-directory-overview.md)

## <a name="next-steps"></a>Következő lépések

- [Blobok és várólisták hozzáférésének engedélyezése Azure Active Directory és felügyelt identitásokkal az Azure-erőforrásokhoz](storage-auth-aad-msi.md)
- [Engedélyezés Azure Active Directory alkalmazásból a blobokhoz és várólistákhoz való hozzáféréshez](storage-auth-aad-app.md)
- [Általánosan elérhető az Azure Storage-támogatás a Azure Active Directory-alapú hozzáférés-vezérléshez](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)
