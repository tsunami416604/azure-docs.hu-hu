---
title: Az Azure Active Directoryval (előzetes verzió) Azure Storage hozzáférés hitelesítéséhez |} Microsoft Docs
description: Az Azure Active Directoryval (előzetes verzió) Azure Storage hozzáférés hitelesítéséhez.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 06/01/2018
ms.author: tamram
ms.openlocfilehash: 9a0782b96b45d27c9b7e603959ecadf5b2632064
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34737646"
---
# <a name="authenticate-access-to-azure-storage-using-azure-active-directory-preview"></a>Az Azure Active Directoryval (előzetes verzió) Azure Storage hozzáférés hitelesítéséhez

Az Azure Storage támogatja a Blob és a Queue szolgáltatások hitelesítési és engedélyezési az Azure Active Directory (AD). Az Azure AD segítségével szerepköralapú hozzáférés-vezérlést (RBAC) hozzáférést biztosíthat a felhasználók, csoportok vagy alkalmazás szolgáltatásnevekről. 

Azure Storage használata az Azure AD elérhető alkalmazásokat engedélyező keresztül biztosítja a felső szintű biztonsági és könnyen használható egyéb engedélyezési beállítások. Továbbra is megosztott kulcsos engedélyezési használja az alkalmazásokkal, amíg az Azure AD megkerüli a kód a hívóbetűre tárolásának szükségességét. Hasonlóképpen továbbra is használja a közös hozzáférésű jogosultságkód (SAS) részletes a tárfiókban lévő erőforrások eléréséhez, de az Azure AD nem SAS-tokenje kezelése vagy visszavonni a sérült biztonságú SAS foglalkoznia kell hasonló képességeket biztosít.

## <a name="about-the-preview"></a>Előzetes tudnivalók

Előzetes tudnivalók a következő szempontokat vegye figyelembe:

- Az Azure AD-integrációs a Blob és a várólista szolgáltatások csak az előzetes kiadásban érhető el.
- Minden nyilvános régióban GPv1 GPv2 és Blob storage-fiókok Azure AD-integrációs érhető el. 
- Csak a Resource Manager telepítési modellel készült tárfiókok támogatottak. 
- Hívó azonosító adatok Azure Storage Analytics naplózása támogatása hamarosan elérhető.
- Standard szintű tárfiókokban lévő erőforrásokhoz való hozzáférés az Azure AD engedélyezési jelenleg támogatott. Prémium szintű storage-fiókok a lapblobokat hozzáférési engedély hamarosan támogatott.
- Az Azure Storage mind beépített és egyéni RBAC-szerepkörök támogatja. Az előfizetés, az erőforráscsoport, a tárfiók, vagy egy egyéni tároló vagy várólista hatóköre szerepköröket rendelhet.
- Az Azure Storage ügyfélkódtáraival, amelyek jelenleg támogatják az Azure AD-integrációs a következők:
    - [.NET](https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0)
    - [Java](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage) (7.1.x-Preview használata)
    - Python
        - [Blob](https://github.com/Azure/azure-storage-python/releases/tag/v1.2.0rc1-blob)
        - [Várólista](https://github.com/Azure/azure-storage-python/releases/tag/v1.2.0rc1-queue)
    - [Node.js](https://www.npmjs.com/package/azure-storage)
    - [JavaScript](https://aka.ms/downloadazurestoragejs))

> [!IMPORTANT]
> Ez az előzetes kiadás csak nem üzemi használatra készült. Éles szolgáltatásiszint-szerződések (SLA) nem lesz elérhető, amíg az Azure AD integrálása az Azure Storage általánosan elérhető van deklarálva. Ha az Azure AD-integrációs jelenleg nem támogatott a forgatókönyvnek, továbbra is használhatja a megosztott kulcsos engedélyezési vagy SAS-tokenje az alkalmazásokban.
>
> Az előzetes RBAC szerepkör-hozzárendelések terjesztése akár öt percet is eltarthat.
>
> Az Azure AD integrálása az Azure Storage Azure Storage műveletekhez HTTPS használatát igényli.

## <a name="get-started-with-azure-ad-for-storage"></a>Ismerkedés az Azure ad-val tároló

Az Azure AD integrálása révén az Azure Storage első lépése, hogy rendelje hozzá a tárolási adatok az egyszerű szolgáltatásnév RBAC-szerepkörök (olyan felhasználó, csoport vagy alkalmazás egyszerű szolgáltatásnév) vagy felügyelt szolgáltatás identitásának (MSI). Az RBAC-szerepkörök tartalmazzák a közös készleteinek tárolók és a várólisták engedélyeit. További információt az RBAC-szerepkörök az Azure Storage, lásd: [kezelése hozzáférési jogosultsága ahhoz, hogy az RBAC (előzetes verzió) storage-adatokkal](storage-auth-aad-rbac.md).

Az alkalmazások tárolási erőforrásokhoz való hozzáférés engedélyezése az Azure AD használatához szüksége olyan OAuth 2.0 hozzáférési jogkivonatot kérhet a kódot. Olyan hozzáférési jogkivonatot kérésére, és engedélyezze a kérelmek Azure Storage használatával, lásd: [hitelesítés az Azure AD egy Azure Storage-alkalmazás (előnézet)](storage-auth-aad-app.md). Ha egy Azure felügyelt szolgáltatás identitásának (MSI) használ, tekintse meg [hitelesítés az Azure virtuális gép felügyelt szolgáltatás identitásának (előzetes verzió) Azure AD-val](storage-auth-aad-msi.md).

Azure CLI és PowerShell mostantól támogatják az Azure AD identity bejelentkezik. Az Azure AD identity bejelentkezés után a munkamenet identitás alatt fut. További tudnivalókért lásd: [CLI vagy a PowerShell használatával (előzetes verzió) Azure Storage eléréséhez használja az Azure AD identity](storage-auth-aad-script.md).

## <a name="next-steps"></a>További lépések

Az Azure AD integrálása az Azure-blobokat és üzenetsorokat kapcsolatos további információkért lásd a következő Azure Storage csapat blogbejegyzésben, [az Azure betekintő AD hitelesítés az Azure Storage bejelentése](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
