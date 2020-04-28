---
title: Az ajánlott biztonsági eljárások ismertetése – Azure digitális Twins | Microsoft Docs
description: Ismerje meg az Azure Digital Twins és a eszközök internetes hálózata ajánlott biztonsági eljárásait.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: 5fc5ba447557aa89e8f0870c576d6d4c439f3353
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "76122559"
---
# <a name="azure-digital-twins-security-best-practices"></a>Azure Digital Twins – ajánlott biztonsági eljárások

Az Azure digitális Twins biztonsági szolgáltatásával pontos hozzáférést érhet el a IoT gráf egyes erőforrásaihoz és műveleteihez. Ezt a részletes szerepkör és a [szerepköralapú hozzáférés-vezérlés](./security-role-based-access-control.md)nevű engedélyek kezelése teszi lehetővé.

Az Azure Digital Twins más biztonsági funkciókat is tartalmaz, amelyek az Azure IoT találhatók, beleértve a Azure Active Directory (Azure AD) szolgáltatást. Emiatt az Azure Digital Twins-ra épülő alkalmazások konfigurálása és biztonságossá tétele számos különböző [Azure IoT biztonsági gyakorlatot](../iot-fundamentals/iot-security-best-practices.md) használ, jelenleg ajánlott.

Ez a cikk a követendő ajánlott eljárásokat foglalja össze.

> [!IMPORTANT]
> A IoT-terület maximális biztonságának biztosításához tekintse át a további biztonsági forrásokat. Ügyeljen rá, hogy tartalmazza az eszközök gyártóit.

> [!TIP]
> A IoT biztonsági fenyegetések és biztonsági rések észleléséhez használja [a IoT Azure Security Center](https://docs.microsoft.com/azure/asc-for-iot/) .

## <a name="iot-security-best-practices"></a>Ajánlott biztonsági eljárások az IoT-hez

Néhány kulcsfontosságú eljárás a IoT-eszközök biztonságos biztonságossá tételéhez a következők:

> [!div class="checklist"]
> * Védje a IoT-területhez csatlakozó összes eszközt illetéktelen hozzáférést igazoló módon.
> * Korlátozza az egyes eszközök, érzékelők és személyek szerepkörét a IoT belül. Ha sérült, a hatás kisméretű.
> * Vegye figyelembe az eszközök IP-címek szűrésének és a portok korlátozásának lehetséges használatát.
> * A teljesítmény javítása érdekében korlátozza az I/O és az eszköz sávszélességét. A díjszabási korlátozás a szolgáltatásmegtagadási támadások megakadályozásával növelheti a biztonságot.
> * Naprakészen tarthatja az eszköz belső vezérlőprogramja, operációs rendszerét és szoftverét.
> * Az eszköz-, szoftver-, hálózat-és átjárók biztonsági eljárásainak rendszeres időközönkénti ellenőrzése és áttekintése, ahogy azok továbbra is javítják és fejlődnek.
> * Megbízható, hitelesített és megfelelő biztonsági rendszereket, szoftvereket és eszközöket használhat. Tekintse át például [Az Azure Cloud megfelelőségi ajánlatait](https://azure.microsoft.com/overview/trusted-cloud/compliance/) .

A IoT-területek biztonságos biztonságossá tételéhez szükséges alapvető eljárások a következők:

> [!div class="checklist"]
> * Mentett, tárolt vagy állandó adattitkosítás.
> * A jelszavak vagy kulcsok rendszeres módosításának vagy frissítésének megkövetelése.
> * A hozzáférés és az engedélyek körültekintő korlátozása szerepkör alapján. Olvassa el az alábbi, [szerepköralapú hozzáférés-vezérléssel kapcsolatos ajánlott eljárásokat](#role-based-access-control-best-practices) .
> * Vegye fontolóra egy megosztott hálózati topológiát, hogy az egyes hálózatokon lévő eszközök el legyenek különítve a többitől.
> * Hatékony titkosítás használata. Hosszú jelszavak megkövetelése, biztonságos protokollok és [multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)használata.

[Figyelő](./how-to-configure-monitoring.md) A IoT a szokásos művelet tartományán kívül eső kiugró adatokat, fenyegetéseket vagy erőforrás-paramétereket figyeli. Az Azure Analytics használata figyelési felügyelethez.

> [!IMPORTANT]
> Tekintse meg az Azure [IoT ajánlott biztonsági eljárásait](../iot-fundamentals/iot-security-best-practices.md) egy átfogó IoT biztonsági stratégia elindításához.

> [!NOTE]
> Az események feldolgozásával és figyelésével kapcsolatos további információkért olvassa el az [útválasztási eseményeket és üzeneteket az Azure digitális Twins](./concepts-events-routing.md)szolgáltatásban.

## <a name="azure-active-directory-best-practices"></a>Azure Active Directory ajánlott eljárások

Az Azure Digital Twins [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/) használatával hitelesíti a felhasználókat és gondoskodik az alkalmazások biztonságáról. Azure Active Directory támogatja a különböző modern architektúrák hitelesítését. Ezek mind az iparági szabványnak megfelelő protokollok, például a OAuth 2,0 vagy az OpenID Connect alapján működnek. Néhány kulcsfontosságú gyakorlat a IoT-terület biztonságossá tételéhez Azure Active Directory többek között a következők:

> [!div class="checklist"]
> * Egy biztonságos helyen tárolja Azure Active Directory alkalmazás titkos kulcsait, például a [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).
> * A hitelesítéshez használjon egy megbízható [hitelesítésszolgáltató](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) által kiadott tanúsítványt, nem pedig az alkalmazás titkos kulcsát.
> * Korlátozza a OAuth 2,0 hozzáférés hatókörét.
> * Ellenőrizze, hogy a jogkivonat érvényességi ideje érvényes-e, és hogy a jogkivonat érvényes marad-e.
> * Állítsa be a megfelelő időtartamot, ameddig a tokenek érvényesek. Lejárt tokenek frissítése.
> * A nem használt **átirányítási URI** -k és engedélyek eltávolítása a [szerepköralapú hozzáférés-vezérlés ajánlott eljárásai alapján](#role-based-access-control-best-practices).

## <a name="role-based-access-control-best-practices"></a>Szerepköralapú hozzáférés-vezérlés – ajánlott eljárások

[!INCLUDE [digital-twins-rbac-best-practices](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="next-steps"></a>További lépések

* Ha többet szeretne megtudni az Azure IoT ajánlott eljárásairól, olvassa el a [IoT-biztonsággal kapcsolatos ajánlott eljárásokat](../iot-fundamentals/iot-security-best-practices.md).

* A szerepköralapú hozzáférés-vezérléssel kapcsolatos további tudnivalókért olvassa el a [szerepköralapú hozzáférés-vezérlés](./security-role-based-access-control.md)című témakört.

* A hitelesítéssel kapcsolatos további tudnivalókért olvassa el a [hitelesítés API](./security-authenticating-apis.md)-kkal című témakört.
