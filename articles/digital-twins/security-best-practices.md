---
title: Ajánlott biztonsági eljárások az Azure digitális Ikerállapotának megismerése |} A Microsoft Docs
description: Az Azure digitális Twins ajánlott biztonsági eljárások
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: adgera
ms.openlocfilehash: a17fe8ed47384ed248b339643be11269b8b9cdc0
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50092185"
---
# <a name="security-best-practices"></a>Ajánlott biztonsági eljárások

Az Azure digitális Twins biztonsági lehetővé teszi a meghatározott erőforráshoz és művelethez az IoT-Graph pontos hozzáférést. Ezért nevezik szerepköralapú hozzáférés-vezérlés részletes szerepkör és a jogosultsági management szolgáltatáson keresztül hajtja végre.

Az Azure digitális Twins más biztonsági funkciókat az Azure IoT, beleértve az Azure Active Directory jelenlegi előnyöket is kihasználja. Éppen ezért az Azure digitális Twins alkalmazás konfigurálásához keretein belül a azonos számos [Azure IoT biztonsági eljárások](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-best-practices?context=azure/iot-hub/) jelenleg ajánlott.

Ez a cikk a főbb vonatkozó ajánlott eljárásokat foglalja össze.

> [!IMPORTANT]
> Tekintse át a további biztonsági erőforrások (beleértve az eszközforgalmazók) maximális biztonsági IoT-tárhely biztosításához.

## <a name="iot-security-best-practices"></a>Ajánlott biztonsági eljárások az IoT-hez

Néhány kulcsfontosságú eljárásokat, és biztonságosan biztonságos az IoT-eszközökről a következők:

> [!div class="checklist"]
> * Minden eszköz, amely csatlakozik az IoT-terület hamisíthatatlan módon biztonságossá tételéhez.
> * A szerepkör az egyes eszközök, érzékelők és az IoT-címtéren belüli személy korlátozza. Ha megsérül, a hatás minimálisra csökken.
> * Lehetséges használata az eszköz IP címe szűrési és portszáma korlátozás.
> * A teljesítmény javítása i/o- és eszköz sávszélesség korlátozása. A sebességhatárolt javíthatja a biztonsági-szolgáltatásmegtagadási támadások megelőzésével.
> * Eszköz belső vezérlőprogramjának frissítéséhez.

Néhány kulcsfontosságú eljárásokat, és biztonságosan biztonságos az IoT-terület a következők:

> [!div class="checklist"]
> * Mentett, a tárolt vagy a perzisztens adatok titkosításához.
> * Jelszavak vagy -kulcsok rendszeresen módosítani vagy frissíteni kell.
> * Alaposan a szerepkör által korlátozzák a hozzáférést és engedélyeket (lásd az alábbi ajánlott eljárások szerepköralapú hozzáférés-vezérlés).
> * Erős titkosítást használjon. Ez azt jelenti, hogy hosszú jelszavak biztonságos protokollokkal, és kéttényezős hitelesítést igénylő.

Figyelheti a kiugró értékek, a fenyegetések vagy kívül esne a szokásos műveletet az erőforrás-paraméterek az IoT-erőforrások figyelése az Azure-analitika segítségével kezeli.

> [!NOTE]
> További információ az esemény feldolgozása és -monitoring, tekintse meg a cikk [esemény > útválasztási](./concepts-events-routing.md).

## <a name="azure-active-directory-best-practices"></a>Az Azure Active Directory – gyakorlati tanácsok

Az Azure digitális Twins az Azure Active Directory felhasználók hitelesítéséhez és alkalmazások védelmét. Az Azure Active Directory hitelesítési modern architektúrák, mindegyiket alapján szabványos protokollok, mint például az OAuth 2.0 vagy OpenID Connect többféle támogatja. Biztonságos IoT tárhely az Azure Active Directory néhány kulcsfontosságú eljárások a következők:

> [!div class="checklist"]
> * Azure Active Directory-alkalmazás titkos kódok és kulcsok biztonságos helyen Store például [Key Vault](https://azure.microsoft.com/services/key-vault/).
> * Egy megbízható által kibocsátott tanúsítványt használja [hitelesítésszolgáltató](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-certificate-based-authentication-get-started) helyett Alkalmazáskulcs hitelesítéséhez.
> * OAuth 2.0 hozzáférési jogkivonat hatóköre korlátozza.
> * Mennyi ideig jogkivonat érvényességét. Ellenőrizze, és hogy-e a jogkivonat érvényes marad.
> * Állítsa be a megfelelő hosszúságú, ameddig a jogkivonatok érvényesek.
> * Frissítse a lejárt.

## <a name="role-based-access-control-best-practices"></a>Szerepköralapú hozzáférés-vezérlés ajánlott eljárások

[!INCLUDE [digital-twins-rbac-best-practices](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="next-steps"></a>További lépések

További tudnivalók az Azure IoT ajánlott eljárásairól, olvassa el [IoT ajánlott biztonsági eljárások](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-best-practices?context=azure/iot-hub/).

Szerepköralapú hozzáférés-vezérléssel kapcsolatos tudnivalókért olvassa el [szerepköralapú hozzáférés-vezérlés](./security-role-based-access-control.md).

A hitelesítéshez, olvassa el a [API-kkal való hitelesítés közben](./security-authenticating-apis.md).
