---
title: Az ajánlott biztonsági eljárások ismertetése – Azure digitális Twins | Microsoft Docs
description: Ismerje meg az Azure Digital Twins és a eszközök internetes hálózata ajánlott biztonsági eljárásait.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 09/30/2019
ms.openlocfilehash: 9f3f98863f9a7cd0e6328ddc75b1154ee933fe0b
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74009250"
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
> * Az eszköz belső vezérlőprogramja naprakészen tartása.
> * Az eszközök, a hálózat és az átjáró biztonsági eljárásainak rendszeres időközönkénti ellenőrzése és áttekintése, mivel azok továbbra is javítják és fejlődnek.

A IoT-területek biztonságos biztonságossá tételéhez szükséges alapvető eljárások a következők:

> [!div class="checklist"]
> * Mentett, tárolt vagy állandó adattitkosítás.
> * A jelszavak vagy kulcsok rendszeres módosításának vagy frissítésének megkövetelése.
> * A hozzáférés és az engedélyek körültekintő korlátozása szerepkör alapján. Tekintse meg az alábbi, [szerepköralapú hozzáférés-vezérléssel kapcsolatos ajánlott eljárásokat](#role-based-access-control-best-practices) .
> * Vegye fontolóra egy megosztott hálózati topológiát, hogy az egyes hálózatokon lévő eszközök el legyenek különítve a többitől.
> * Hatékony titkosítás használata. Hosszú jelszavak megkövetelése és biztonságos protokollok és kétfaktoros hitelesítés használata.

[Figyelő](./how-to-configure-monitoring.md) A IoT a szokásos művelet tartományán kívül eső kiugró adatokat, fenyegetéseket vagy erőforrás-paramétereket figyeli. Az Azure Analytics használata figyelési felügyelethez.

> [!NOTE]
> Az események feldolgozásával és figyelésével kapcsolatos további információkért lásd: [események és üzenetek átirányítása az Azure Digital Twins szolgáltatással](./concepts-events-routing.md).

## <a name="azure-active-directory-best-practices"></a>Azure Active Directory ajánlott eljárások

Az Azure Digital Twins Azure Active Directory használatával hitelesíti a felhasználókat és gondoskodik az alkalmazások biztonságáról. Azure Active Directory támogatja a különböző modern architektúrák hitelesítését. Ezek mind az iparági szabványnak megfelelő protokollok, például a OAuth 2,0 vagy az OpenID Connect alapján működnek. Néhány kulcsfontosságú gyakorlat a IoT-terület biztonságossá tételéhez Azure Active Directory többek között a következők:

> [!div class="checklist"]
> * Egy biztonságos helyen tárolja Azure Active Directory alkalmazás titkos kulcsait, például a [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).
> * A hitelesítéshez használjon egy megbízható [hitelesítésszolgáltató](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) által kiadott tanúsítványt, nem pedig az alkalmazás titkos kulcsát.
> * Korlátozza a OAuth 2,0 hozzáférés hatókörét.
> * Ellenőrizze, hogy a jogkivonat érvényességi ideje érvényes-e, és hogy a jogkivonat érvényes marad-e.
> * Állítsa be a megfelelő időtartamot, ameddig a tokenek érvényesek.
> * Lejárt tokenek frissítése.

## <a name="role-based-access-control-best-practices"></a>Szerepköralapú hozzáférés-vezérlés – ajánlott eljárások

[!INCLUDE [digital-twins-rbac-best-practices](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="next-steps"></a>Következő lépések

* Ha többet szeretne megtudni az Azure IoT ajánlott eljárásairól, olvassa el a [IoT-biztonsággal kapcsolatos ajánlott eljárásokat](../iot-fundamentals/iot-security-best-practices.md).

* A szerepköralapú hozzáférés-vezérléssel kapcsolatos további tudnivalókért olvassa el a [szerepköralapú hozzáférés-vezérlés](./security-role-based-access-control.md)című témakört.

* A hitelesítéssel kapcsolatos további tudnivalókért olvassa el a [hitelesítés API](./security-authenticating-apis.md)-kkal című témakört.