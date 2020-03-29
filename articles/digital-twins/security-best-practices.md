---
title: A biztonsági tanácsok ismertetése – Azure Digital Twins | Microsoft dokumentumok
description: Ismerje meg az Azure Digital Twins és a dolgok internete biztonsági gyakorlati tanácsait.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: 5fc5ba447557aa89e8f0870c576d6d4c439f3353
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122559"
---
# <a name="azure-digital-twins-security-best-practices"></a>Az Azure Digital Twins biztonsági gyakorlati tanácsok

Az Azure Digital Twins biztonság a pontos hozzáférést biztosít az IoT-grafikon on adott erőforrásokhoz és műveletekhez. Ezt a szerepkör- és engedélykezelésnek nevezett [részletes szerepkör-](./security-role-based-access-control.md)és engedélykezelésen keresztül teszi.

Az Azure Digital Twins más, az Azure IoT-ben is jelen lévő biztonsági funkciókat is használ, beleértve az Azure Active Directoryt (Azure AD). Ezért az Azure Digital Twins-re épülő alkalmazások konfigurálása és biztonságossá tétele a jelenleg ajánlott [Azure IoT-biztonsági eljárások sok ahasználatára vonatkozik.](../iot-fundamentals/iot-security-best-practices.md)

Ez a cikk összefoglalja a követendő ajánlott eljárásokat.

> [!IMPORTANT]
> Az IoT-tárhely maximális biztonságának biztosítása érdekében tekintse át a további biztonsági erőforrásokat. Győződjön meg arról, hogy az eszköz forgalmazói.

> [!TIP]
> Az [Azure Security Center for IoT](https://docs.microsoft.com/azure/asc-for-iot/) segítségével észlelheti az IoT biztonsági fenyegetéseit és biztonsági réseit.

## <a name="iot-security-best-practices"></a>Ajánlott biztonsági eljárások az IoT-hez

Az IoT-eszközök biztonságos védelmének néhány kulcsfontosságú gyakorlata a következő:

> [!div class="checklist"]
> * Hamisíthatatlan módon biztosíthat minden olyan eszközt, amely az IoT-területhez csatlakozik.
> * Korlátozza az egyes eszközök, érzékelők és személyek szerepét az IoT-tárhelyen belül. Ha kompromittálódott, a hatás minimálisra csökken.
> * Vegye figyelembe az eszköz IP-címszűrésének és portkorlátozásának lehetséges használatát.
> * Korlátozza az I/O és az eszköz sávszélességét a teljesítmény javítása érdekében. A sebességkorlátozás a szolgáltatásmegtagadási támadások megakadályozásával növelheti a biztonságot.
> * Tartsa naprakészen az eszköz belső vezérlőprogramját, az operációs rendszert és a szoftvert.
> * Rendszeresen naplózza és felülvizsgálja az eszközök, szoftverek, hálózatok és átjárók biztonsági bevált módszereit, ahogy azok tovább fejlődnek és fejlődnek.
> * Megbízható, tanúsított és megfelelő biztonsági rendszereket, szoftvereket és eszközöket használhat. Tekintse át például az Azure Cloud [megfelelőségi ajánlatait.](https://azure.microsoft.com/overview/trusted-cloud/compliance/)

Az IoT-terület biztonságos biztonságos védelmének néhány kulcsfontosságú gyakorlata a következő:

> [!div class="checklist"]
> * Mentett, tárolt vagy állandó adatok titkosítása.
> * A jelszavak vagy kulcsok rendszeres módosításának vagy frissítésének megkövetelése.
> * Gondosan korlátozza a hozzáférést és az engedélyeket szerepkör szerint. Olvassa el az alábbi [Szerepköralapú hozzáférés-vezérlési gyakorlati tanácsok](#role-based-access-control-best-practices) című részt.
> * Fontolja meg a megosztott hálózati topológia, hogy az eszközök minden hálózaton el vannak különítve a többitől.
> * Használjon hatékony titkosítást. Hosszú jelszavak megkövetelése, biztonságos protokollok és [többtényezős hitelesítés](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)használata.

[Figyelő](./how-to-configure-monitoring.md) IoT-erőforrásokat a kiugró értékek, fenyegetések vagy erőforrás-paraméterek, amelyek kívül esnek a szokásos művelet tartományán. Használja az Azure Analytics figyelési felügyelet.

> [!IMPORTANT]
> Olvassa el az Azure [IoT biztonsági gyakorlati tanácsait](../iot-fundamentals/iot-security-best-practices.md) egy átfogó IoT-biztonsági stratégia megkezdéséhez.

> [!NOTE]
> Az események feldolgozásával és figyelésével kapcsolatos további információkért olvassa el [az Útvonalesemények és üzenetek az Azure Digital Twins segítségével](./concepts-events-routing.md)című részt.

## <a name="azure-active-directory-best-practices"></a>Az Azure Active Directory gyakorlati tanácsok

Az Azure Digital Twins az [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/) használatával hitelesíti a felhasználókat és védi az alkalmazásokat. Az Azure Active Directory számos modern architektúra hitelesítését támogatja. Ezek mindegyike az iparági szabványnak megfelelő protokollokon alapul, például az OAuth 2.0 vagy az OpenID Connect protokollon. Az Azure Active Directory IoT-tárhelyének biztonságossá tétele a következő:

> [!div class="checklist"]
> * Az Azure Active Directory alkalmazás titkos kulcsait és kulcsait biztonságos helyen, például [az Azure Key Vaultban](https://azure.microsoft.com/services/key-vault/)tárolhatja.
> * A hitelesítéshez ne az alkalmazástitkos kulcsok, hanem egy megbízható [hitelesítésszolgáltató](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) által kiállított tanúsítványt használjon.
> * Korlátozza az OAuth 2.0 hozzáférési tartományt egy jogkivonathoz.
> * Ellenőrizze, hogy egy token mennyi ideig érvényes, és hogy egy token érvényes marad-e.
> * Állítsa be a megfelelő időtartamokat, amelyek tokenek érvényesek. A lejárt jogkivonatok frissítése.
> * A nem használt **átirányítási URI-k** és engedélyek eltávolítása [szerepköralapú hozzáférés-vezérlési gyakorlati tanácsok](#role-based-access-control-best-practices)szerint.

## <a name="role-based-access-control-best-practices"></a>A szerepköralapú hozzáférés-vezérlés gyakorlati tanácsai

[!INCLUDE [digital-twins-rbac-best-practices](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="next-steps"></a>További lépések

* Ha többet szeretne megtudni az Azure IoT gyakorlati tanácsokról, olvassa el az [IoT biztonsági gyakorlati tanácsait.](../iot-fundamentals/iot-security-best-practices.md)

* A szerepköralapú hozzáférés-vezérlésről a [Szerepköralapú hozzáférés-vezérlés](./security-role-based-access-control.md)című olvasnivalóban olvashat.

* A hitelesítésről a Hitelesítés API-kkal című olvasni tudó című olvasni tudó című [olvasnitudó című olvasnitudó című olvasnitudó](./security-authenticating-apis.md)ban.
