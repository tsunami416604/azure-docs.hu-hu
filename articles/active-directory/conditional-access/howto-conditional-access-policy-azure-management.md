---
title: Feltételes hozzáférés – Többszintű szolgáltatás megkövetelése az Azure-kezeléshez – Azure Active Directory
description: Egyéni feltételes hozzáférési szabályzat létrehozása az Azure felügyeleti feladatainak többtényezős hitelesítéséhez
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: c90566006868c817d977699c35f2213895f3fe70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295238"
---
# <a name="conditional-access-require-mfa-for-azure-management"></a>Feltételes hozzáférés: Többéves kortól megkövetelése az Azure-kezeléshez

A szervezetek számos Azure-szolgáltatást használnak, és az Azure Resource Manager-alapú eszközökből kezelik őket, például:

* Azure portál
* Azure PowerShell
* Azure CLI

Ezek az eszközök magas szintű jogosultsággal rendelkező hozzáférést biztosíthatnak az erőforrásokhoz, amelyek módosíthatják az előfizetésegészének konfigurációit, a szolgáltatásbeállításokat és az előfizetési számlázást. Ezek a kiemelt erőforrások védelme érdekében a Microsoft azt javasolja, hogy az erőforrásokhoz hozzáférő felhasználók nak többtényezős hitelesítést kell jenük.

## <a name="user-exclusions"></a>Felhasználói kizárások

A feltételes hozzáférési szabályzatok hatékony eszközök, javasoljuk, hogy zárja ki a következő fiókokat a szabályzatból:

* **Vészelérési** vagy **törésüveg-fiókok** a bérlői fiók zárolásának megakadályozása érdekében. Abban a valószínűtlen esetben az összes rendszergazda ki van zárva a bérlőből, a vészelérési felügyeleti fiók segítségével jelentkezzen be a bérlőbe lépéseket a hozzáférés helyreállítása érdekében.
   * További információ a cikkben található, [a vészelérési fiókok kezelése az Azure AD-ben.](../users-groups-roles/directory-emergency-access.md)
* **Szolgáltatásfiókok** és **szolgáltatásalapelvek,** például az Azure AD Connect szinkronizálási fiók. A szolgáltatásfiókok olyan nem interaktív fiókok, amelyek nem kötődnek egy adott felhasználóhoz. Ezeket általában a háttérszolgáltatások használják, és lehetővé teszik az alkalmazások hoz való programozott hozzáférést. A szolgáltatásfiókokat ki kell zárni, mivel az MFA nem hajtható végre programozott módon.
   * Ha a szervezet ezeket a fiókokat parancsfájlokban vagy kódokban használja, fontolja meg, hogy lecseréli őket [felügyelt identitásokra.](../managed-identities-azure-resources/overview.md) Ideiglenes megoldásként ezeket a konkrét fiókokat kizárhatja az alaptervi házirendből.

## <a name="create-a-conditional-access-policy"></a>Feltételes hozzáférési házirend létrehozása

A következő lépések segítségével hozzon létre egy feltételes hozzáférési szabályzatot, amely megköveteli a hozzárendelt felügyeleti szerepköröktől a többtényezős hitelesítés elvégzéséhez.

1. Jelentkezzen be az **Azure Portalon** globális rendszergazdaként, biztonsági rendszergazdaként vagy feltételes hozzáférés-rendszergazdaként.
1. Tallózással keresse meg az **Azure Active Directory** > **biztonsági** > **feltételes hozzáférését.**
1. Válassza az **Új házirend lehetőséget.**
1. Adjon nevet a szabályzatának. Azt javasoljuk, hogy a szervezetek hozzanak létre egy értelmes szabvány a házirendek nevét.
1. A **Hozzárendelések**csoportban válassza a **Felhasználók és csoportok lehetőséget.**
   1. A **Belefoglalás**csoportban válassza a **Minden felhasználó lehetőséget.**
   1. A **Kizárás csoportban**válassza a **Felhasználók és csoportok** lehetőséget, és válassza ki a szervezet vészelérési vagy törésüvegfiókjait. 
   1. Válassza a **Done** (Kész) lehetőséget.
1. A **Felhőalapú alkalmazások és műveletek** > **felvétele csoportban**válassza **az Alkalmazások kiválasztása**lehetőséget, válassza a Microsoft Azure **Management**lehetőséget, és válassza **a Kijelölés,** majd **a Kész**lehetőséget.
1. A **Feltételek** > **az ügyfélalkalmazások (előzetes verzió)** csoportban állítsa a **Konfigurálás** beállítást **Igen**értékre, és válassza a **Kész gombot.**
1. A**Grant** **access-vezérlők csoportban** > válassza a **Hozzáférési támogatás**lehetőséget, válassza a **Többtényezős hitelesítés megkövetelése**lehetőséget, majd a **Kijelölés**lehetőséget.
1. Erősítse meg a beállításokat, és állítsa a **Házirend engedélyezése** **be**beállítást.
1. A létrehozás gombra a házirend engedélyezéséhez válassza a **Létrehozás** gombot.

## <a name="next-steps"></a>További lépések

[Feltételes hozzáférés közös házirendjei](concept-conditional-access-policy-common.md)

[Hatás meghatározása csak feltételes hozzáférésű jelentésmódhasználatával](howto-conditional-access-report-only.md)

[Bejelentkezési viselkedés szimulálása a Feltételes hozzáférés Mi ha eszközzel](troubleshoot-conditional-access-what-if.md)
