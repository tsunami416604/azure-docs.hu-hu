---
title: Megismerheti az Azure digitális Twins ajánlott biztonsági eljárások |} A Microsoft Docs
description: Az Azure digitális Twins ajánlott biztonsági eljárások.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: adgera
ms.openlocfilehash: 16bb148a0b3a424c9ba3aaae422f423ebd40793b
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2019
ms.locfileid: "54358813"
---
# <a name="security-best-practices"></a>Ajánlott biztonsági eljárások

Az Azure digitális Twins biztonsági lehetővé teszi a meghatározott erőforráshoz és művelethez az IoT-Graph pontos hozzáférést. Részletes szerepkör és az engedélyek kezelését nevű ilyeneket [szerepköralapú hozzáférés-vezérlés](./security-role-based-access-control.md).

Az Azure digitális Twins is más biztonsági funkciókat, amelyek szerepelnek az Azure IoT, beleértve az Azure Active Directory (Azure AD) használja. Éppen ezért konfigurálásával és biztonságossá tétele az Azure digitális Twins épülő alkalmazások keretein belül a azonos számos [Azure IoT biztonsági eljárások](../iot-fundamentals/iot-security-best-practices.md) jelenleg ajánlott.

Ez a cikk a főbb vonatkozó ajánlott eljárásokat foglalja össze.

> [!IMPORTANT]
> IoT-tárhelyre vonatkozó maximális biztonság érdekében tekintse át a további biztonsági erőforrásokat. Ellenőrizze, hogy az eszközforgalmazók tartalmazza.

## <a name="iot-security-best-practices"></a>Ajánlott biztonsági eljárások az IoT-hez

Néhány kulcsfontosságú eljárásokat, és biztonságosan biztonságos az IoT-eszközökről a következők:

> [!div class="checklist"]
> * Minden eszköz, amely csatlakozik az IoT-terület hamisíthatatlan módon biztonságossá tételéhez.
> * A szerepkör az egyes eszközök, érzékelők és az IoT-címtéren belüli személy korlátozza. Ha megsérül, a hatás minimálisra csökken.
> * Gondolja végig a potenciális eszköz IP-cím a szűrés és korlátozás port.
> * A teljesítmény javítása i/o- és eszköz sávszélesség korlátozása. A sebességhatárolt javíthatja a biztonsági-szolgáltatásmegtagadási támadások megelőzésével.
> * Eszköz belső vezérlőprogramjának naprakészen tartása.

Néhány kulcsfontosságú eljárásokat, és biztonságosan biztonságos az IoT-terület a következők:

> [!div class="checklist"]
> * Mentett, a tárolt vagy a perzisztens adatok titkosításához.
> * Jelszavak vagy -kulcsok rendszeresen módosítani vagy frissíteni kell.
> * Gondosan korlátozzák a hozzáférést és engedélyeket szerepkör. Című témakör [szerepköralapú hozzáférés-vezérlés ajánlott eljárások](#rbac) alatt.
> * Erős titkosítást használjon. Hosszú jelszavak szükséges, és használjon biztonságos protokollok és a kétfaktoros hitelesítés.

[A figyelő](./how-to-configure-monitoring.md) figyelheti a kiugró értékek, a fenyegetések vagy kívül esne a szokásos műveletet az erőforrás-paraméterek IoT-erőforrásokat. Az Azure Analytics felügyeleti használni.

> [!NOTE]
> Az esemény feldolgozása és figyelési további információkért lásd: [irányíthatja az események és az Azure digitális Twins üzenetek](./concepts-events-routing.md).

## <a name="azure-active-directory-best-practices"></a>Az Azure Active Directory – gyakorlati tanácsok

Az Azure digitális Twins az Azure Active Directory felhasználók hitelesítéséhez és alkalmazások védelmét. Az Azure Active Directory különböző modern architektúrák támogatja a hitelesítést. Ezek még alapján szabványos protokollok, mint például az OAuth 2.0 vagy OpenID Connect. Biztonságos IoT tárhely az Azure Active Directory néhány kulcsfontosságú eljárások a következők:

> [!div class="checklist"]
> * Azure Active Directory-alkalmazás titkos kódok és kulcsok biztonságos helyen, Store például [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).
> * Egy megbízható által kibocsátott tanúsítványt használja [hitelesítésszolgáltató](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) helyett Alkalmazáskulcs hitelesítéséhez.
> * OAuth 2.0 hozzáférési jogkivonat hatóköre korlátozza.
> * Mennyi ideig jogkivonat érvényességét. Ellenőrizze, és hogy-e a jogkivonat érvényes marad.
> * Állítsa be a megfelelő hosszúságú, ameddig a jogkivonatok érvényesek.
> * Frissítse a lejárt.

<div id="rbac"></div>

## <a name="role-based-access-control-best-practices"></a>Szerepköralapú hozzáférés-vezérlés ajánlott eljárások

[!INCLUDE [digital-twins-rbac-best-practices](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="next-steps"></a>További lépések

* További tudnivalók az Azure IoT ajánlott eljárásairól, olvassa el [IoT ajánlott biztonsági eljárások](../iot-fundamentals/iot-security-best-practices.md).

* Szerepköralapú hozzáférés-vezérléssel kapcsolatos tudnivalókért olvassa el [szerepköralapú hozzáférés-vezérlés](./security-role-based-access-control.md).

* Hitelesítéssel kapcsolatban, olvassa el [hitelesítés API-kkal](./security-authenticating-apis.md).
