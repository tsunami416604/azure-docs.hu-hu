---
title: Feltételes hozzáférés – Hozzáférés letiltása – Azure Active Directory
description: Egyéni feltételes hozzáférési házirend létrehozása
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb,
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2834fd3d4901b6394eabe000f9efc572c2efd497
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80755079"
---
# <a name="conditional-access-block-access"></a>Feltételes hozzáférés: Hozzáférés letiltása

A konzervatív felhőbehaszeresztő megközelítéssel rendelkező szervezetek esetében az összes házirend letiltása használható. 

> [!CAUTION]
> A blokkszabályzat helytelen konfigurálása azt eredményezheti, hogy a szervezetek ki vannak zárva az Azure Portalról.

Politikák, mint ezek lehetnek nem kívánt mellékhatások. A megfelelő tesztelés és érvényesítés elengedhetetlen, mielőtt engedélyezné. A rendszergazdáknak olyan eszközöket kell használniuk, mint a [feltételes hozzáférésjelentés csak,](concept-conditional-access-report-only.md) és [a Mi van ha eszköz a feltételes hozzáférésben](what-if-tool.md) a módosítások végrehajtásakor.

## <a name="user-exclusions"></a>Felhasználói kizárások

A feltételes hozzáférési szabályzatok hatékony eszközök, javasoljuk, hogy zárja ki a következő fiókokat a szabályzatból:

* **Vészelérési** vagy **törésüveg-fiókok** a bérlői fiók zárolásának megakadályozása érdekében. Abban a valószínűtlen esetben az összes rendszergazda ki van zárva a bérlőből, a vészelérési felügyeleti fiók segítségével jelentkezzen be a bérlőbe lépéseket a hozzáférés helyreállítása érdekében.
   * További információ a cikkben található, [a vészelérési fiókok kezelése az Azure AD-ben.](../users-groups-roles/directory-emergency-access.md)
* **Szolgáltatásfiókok** és **egyszerű szolgáltatás,** például az Azure AD Connect szinkronizálási fiók. A szolgáltatásfiókok olyan nem interaktív fiókok, amelyek nem kötődnek egy adott felhasználóhoz. Ezeket általában az alkalmazásokhoz programozott hozzáférést lehetővé tevő háttérszolgáltatások használják, de a rendszerekbe való rendszergazdai célú bejelentkezésre is használják őket. Az ilyen szolgáltatásfiókokat ki kell zárni, mivel az MFA nem hajtható végre programozott módon. A szolgáltatásnévi szolgáltatások hívásait a feltételes hozzáférés nem blokkolja.
   * Ha a szervezet ezeket a fiókokat parancsfájlokban vagy kódokban használja, fontolja meg, hogy lecseréli őket [felügyelt identitásokra.](../managed-identities-azure-resources/overview.md) Ideiglenes megoldásként ezeket a konkrét fiókokat kizárhatja az alaptervi házirendből.

## <a name="create-a-conditional-access-policy"></a>Feltételes hozzáférési házirend létrehozása

A következő lépések segítségével feltételes hozzáférési házirendek hozhatnak létre, amelyek letiltják az összes alkalmazáshoz való hozzáférést, kivéve az [Office 365-öt,](concept-conditional-access-cloud-apps.md#office-365-preview) ha a felhasználók nem megbízható hálózaton vannak. Ezek a házirendek [csak jelentés módban](howto-conditional-access-report-only.md) indulnak el, így a rendszergazdák meghatározhatják, hogy milyen hatással lesznek a meglévő felhasználókra. Ha a rendszergazdák számára kényelmes, hogy a házirendek a kívánt módon érvényesek, bekapcsolva kapcsolhatják **őket.**

Az első házirend letiltja az összes alkalmazás elérését, kivéve az Office 365-alkalmazásokat, ha nem megbízható helyen.

1. Jelentkezzen be az **Azure Portalon** globális rendszergazdaként, biztonsági rendszergazdaként vagy feltételes hozzáférés-rendszergazdaként.
1. Tallózással keresse meg az **Azure Active Directory** > **biztonsági** > **feltételes hozzáférését.**
1. Válassza az **Új házirend lehetőséget.**
1. Adjon nevet a szabályzatának. Azt javasoljuk, hogy a szervezetek hozzanak létre egy értelmes szabvány a házirendek nevét.
1. A **Hozzárendelések** alatt válassza a **Felhasználók és csoportok** lehetőséget.
   1. A **Belefoglalás**csoportban válassza a **Minden felhasználó lehetőséget.**
   1. A **Kizárás csoportban**válassza a **Felhasználók és csoportok** lehetőséget, és válassza ki a szervezet vészelérési vagy törésüvegfiókjait. 
   1. Válassza a **Done** (Kész) lehetőséget.
1. A **Felhőalapú alkalmazások és műveletek csoportban**adja meg a következő beállításokat:
   1. A **Belefoglalás**csoportban válassza az **Összes felhőalapú alkalmazás lehetőséget.**
   1. A **Kizárás**csoportban válassza az **Office 365 (előnézet)** lehetőséget, válassza a **Kijelölés**lehetőséget, majd a **Kész**lehetőséget.
1. **Feltételek mellett:**
   1. **Feltételek szerint** > **hely**.
      1. A **Konfigurálás** beállítása **Igen-re**
      1. A **Belefoglalás**csoportban válassza **a Bármely hely lehetőséget.**
      1. A **Kizárás**csoportban válassza a **Minden megbízható hely lehetőséget.**
      1. Válassza a **Done** (Kész) lehetőséget.
   1. Az **Ügyfélalkalmazások (előnézet)** csoportban állítsa a **Konfigurálás** beállítást **Igen**értékre, majd kattintson a **Kész** **gombra, majd a Kész gombra.**
1. A**Grant** **access-vezérlők csoportban** > válassza **a Hozzáférés blokkolása**lehetőséget, majd a **Kijelölés**lehetőséget.
1. Erősítse meg a beállításokat, és állítsa be a **Házirend engedélyezése** **csak jelentést.**
1. A létrehozás gombra a házirend engedélyezéséhez válassza a **Létrehozás** gombot.

Az alábbiakban egy második szabályzat jön létre, amely többtényezős hitelesítést vagy megfelelő eszközt igényel az Office 365 felhasználói számára.

1. Válassza az **Új házirend lehetőséget.**
1. Adjon nevet a szabályzatának. Azt javasoljuk, hogy a szervezetek hozzanak létre egy értelmes szabvány a házirendek nevét.
1. A **Hozzárendelések** alatt válassza a **Felhasználók és csoportok** lehetőséget.
   1. A **Belefoglalás**csoportban válassza a **Minden felhasználó lehetőséget.**
   1. A **Kizárás csoportban**válassza a **Felhasználók és csoportok** lehetőséget, és válassza ki a szervezet vészelérési vagy törésüvegfiókjait. 
   1. Válassza a **Done** (Kész) lehetőséget.
1. A **Felhőalapú alkalmazások és műveletek** > **felvétele: Válassza**az Alkalmazások **kiválasztása**lehetőséget, válassza az **Office 365 (előnézet)** lehetőséget, majd kattintson a **Kijelölés**gombra, majd a **Kész gombra.**
1. A**Grant** **Access-vezérlők csoportban** > válassza a **Hozzáférés megadása**lehetőséget.
   1. Jelölje **be A többtényezős hitelesítés megkövetelése** jelölőnégyzetet, és **az eszköz megfelelőként való megjelölésének megkövetelése** lehetőséget **válassza a Select (Kijelölés) lehetőséget.**
   1. Annak **biztosítása, hogy az összes kijelölt vezérlő kijelölése** meg legyen jelölve.
   1. Válassza a **Kiválasztás** lehetőséget.
1. Erősítse meg a beállításokat, és állítsa be a **Házirend engedélyezése** **csak jelentést.**
1. A létrehozás gombra a házirend engedélyezéséhez válassza a **Létrehozás** gombot.

## <a name="next-steps"></a>További lépések

[Feltételes hozzáférés közös házirendjei](concept-conditional-access-policy-common.md)

[Hatás meghatározása csak feltételes hozzáférésű jelentésmódhasználatával](howto-conditional-access-report-only.md)

[Bejelentkezési viselkedés szimulálása a Feltételes hozzáférés Mi ha eszközzel](troubleshoot-conditional-access-what-if.md)
