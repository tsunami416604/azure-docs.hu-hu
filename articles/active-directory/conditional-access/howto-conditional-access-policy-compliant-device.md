---
title: Feltételes hozzáférés – Megfelelő eszközök megkövetelése – Azure Active Directory
description: Egyéni feltételes hozzáférési szabályzat létrehozása a megfelelő eszközök megköveteléséhez
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
ms.openlocfilehash: cc6bd486c1e8338eaf875c7026764c80d49e2f05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295197"
---
# <a name="conditional-access-require-compliant-devices"></a>Feltételes hozzáférés: Megfelelő eszközök megkövetelése

A Microsoft Intune-t üzembe helyező szervezetek az eszközeikről visszaküldött adatok alapján azonosíthatják a megfelelőségi követelményeknek megfelelő eszközöket, például a következőket:

* PIN-kód feloldása
* Eszköztitkosítás megkövetelése
* Az operációs rendszer minimális vagy maximális verziójának megkövetelése
* Az eszköz megkövetelése nem feltört vagy gyökerezik

Ezt a szabályzatmegfelelőségi információkat továbbítja az Azure AD, ahol a feltételes hozzáférés hozhat döntéseket az erőforrásokhoz való hozzáférés engedélyezéséről vagy letiltásáról. Az eszközmegfelelőségi szabályzatokról további információ a cikkben, [a Szabályok beállítása az eszközökön, hogy az Intune használatával hozzáférést biztosítson a szervezet erőforrásaihoz.](/intune/protect/device-compliance-get-started)

## <a name="create-a-conditional-access-policy"></a>Feltételes hozzáférési házirend létrehozása

A következő lépések segítségével hozzon létre egy feltételes hozzáférési szabályzatot, amely megköveteli, hogy az erőforrásokhoz hozzáférő eszközök a szervezet Intune megfelelőségi szabályzatainak megfelelőként legyenek megjelölve.

1. Jelentkezzen be az **Azure Portalon** globális rendszergazdaként, biztonsági rendszergazdaként vagy feltételes hozzáférés-rendszergazdaként.
1. Tallózással keresse meg az **Azure Active Directory** > **biztonsági** > **feltételes hozzáférését.**
1. Válassza az **Új házirend lehetőséget.**
1. Adjon nevet a szabályzatának. Azt javasoljuk, hogy a szervezetek hozzanak létre egy értelmes szabvány a házirendek nevét.
1. A **Hozzárendelések**csoportban válassza a **Felhasználók és csoportok lehetőséget.**
   1. A **Belefoglalás**csoportban válassza a **Minden felhasználó lehetőséget.**
   1. A **Kizárás csoportban**válassza a **Felhasználók és csoportok** lehetőséget, és válassza ki a szervezet vészelérési vagy törésüvegfiókjait. 
   1. Válassza a **Done** (Kész) lehetőséget.
1. A **Felhőalapú alkalmazások vagy műveletek** > közé tartozik a**Include csoportban**válassza az Összes **felhőalapú alkalmazás**lehetőséget.
   1. Ha bizonyos alkalmazásokat ki kell zárnia a szabályzatból, **kiválaszthatja** őket a Kizárt **felhőalkalmazások kiválasztása** csoport Kizárás lapján, és a **Kijelölés lehetőséget.**
   1. Válassza a **Done** (Kész) lehetőséget.
1. A **Feltételek** > **az ügyfélalkalmazások (előzetes verzió)** csoportban állítsa a **Konfigurálás** beállítást **Igen**értékre, és válassza a **Kész gombot.**
1. A**Grant** **Access-vezérlők csoportban** > jelölje be Az **eszköz megfelelőként való megjelölésének megkövetelése**jelölőnégyzetet.
   1. Válassza a **Kiválasztás** lehetőséget.
1. Erősítse meg a beállításokat, és állítsa a **Házirend engedélyezése** **be**beállítást.
1. A létrehozás gombra a házirend engedélyezéséhez válassza a **Létrehozás** gombot.

> [!NOTE]
> Az új eszközöket akkor is regisztrálhatja az Intune-ba, ha a fenti lépésekkel az eszköz minden **felhasználó** számára **megfelelőként való megjelölése megkövetelése** lehetőséget választja, és minden **felhőalapú alkalmazáshoz.** **Az eszköz megfelelő vezérlőként való megjelölésének megkövetelése** nem blokkolja az Intune-regisztrációt. 

### <a name="known-behavior"></a>Ismert viselkedés

Windows 7, iOS, Android, macOS és néhány külső webböngészők az Azure AD azonosítja az eszközt egy ügyfél-tanúsítvány, amely ki van építve, ha az eszköz regisztrálva van az Azure AD. Amikor egy felhasználó először jelentkezik be a böngészőn keresztül, a rendszer kéri, hogy válassza ki a tanúsítványt. A végfelhasználónak ki kell választania ezt a tanúsítványt, mielőtt továbbra is használhatja a böngészőt.

## <a name="next-steps"></a>További lépések

[Feltételes hozzáférés közös házirendjei](concept-conditional-access-policy-common.md)

[Hatás meghatározása csak feltételes hozzáférésű jelentésmódhasználatával](howto-conditional-access-report-only.md)

[Bejelentkezési viselkedés szimulálása a Feltételes hozzáférés Mi ha eszközzel](troubleshoot-conditional-access-what-if.md)

[Az eszközmegfelelőségi szabályzatok együttműködnek az Azure AD-vel](/intune/device-compliance-get-started#device-compliance-policies-work-with-azure-ad)
