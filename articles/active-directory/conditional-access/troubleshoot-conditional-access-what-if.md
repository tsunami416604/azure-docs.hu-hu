---
title: A feltételes hozzáférés hibáinak megoldása a What If eszköz használatával – Azure Active Directory
description: Hol találhatók az alkalmazott feltételes hozzáférési szabályzatok, és miért
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 07/03/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9aaab58acb00305f76b10847a0417d91d724ba8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "83737639"
---
# <a name="troubleshooting-conditional-access-using-the-what-if-tool"></a>Feltételes hozzáférés hibaelhárítása a What If eszköz használatával

A feltételes hozzáférés [What if eszköze](what-if-tool.md) nagy teljesítményű, amikor megpróbálják megérteni, hogy egy adott házirend miért lett vagy nem lett alkalmazva a felhasználóra adott körülmények között, vagy ha egy házirendet ismert állapotban kellene alkalmazni.

A What if eszköz a **Azure Portal**  >  **Azure Active Directory**  >  **feltételes hozzáférési**  >  **What Ifban**található.

![Feltételes hozzáférés What If eszköz alapértelmezett állapotban](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-tool.png)

> [!NOTE]
> A What If eszköz jelenleg csak jelentési módban értékeli ki a házirendeket.

## <a name="gathering-information"></a>Adatgyűjtés

A What If eszköz csak egy **felhasználót** igényel a kezdéshez. 

A következő további információk nem kötelezőek, de bizonyos esetekben a hatókör leszűkíthető.

* Felhőalkalmazások vagy műveletek
* IP-cím 
* Ország/régió
* Eszközplatform
* Ügyfélalkalmazások (előzetes verzió)
* Eszköz állapota (előzetes verzió) 
* Bejelentkezési kockázat

Ezeket az információkat a felhasználó, az eszköz vagy az Azure AD bejelentkezési naplója alapján lehet összegyűjteni.

## <a name="generating-results"></a>Eredmények generálása

Adja meg az előző szakaszban összegyűjtött feltételeket, és válassza a **What if** lehetőséget az eredmények listájának létrehozásához. 

Az **Alaphelyzetbe állítás** lehetőség kiválasztásával törölheti a feltételek bemenetét, és visszatérhet az alapértelmezett állapotba.

## <a name="evaluating-results"></a>Eredmények kiértékelése

### <a name="policies-that-will-apply"></a>Alkalmazandó házirendek

Ez a lista azt mutatja, hogy milyen feltételes hozzáférési szabályzatok érvényesek a feltételekre. A lista tartalmazza az érvényes engedélyezési és munkamenet-vezérlőket is. Ilyenek például a többtényezős hitelesítés megkövetelése egy adott alkalmazás eléréséhez.

### <a name="policies-that-will-not-apply"></a>Szabályzatok, amelyek nem lesznek alkalmazva

A lista azokat a feltételes hozzáférési házirendeket jeleníti meg, amelyeket a rendszer nem alkalmaz, ha az alkalmazott feltételek teljesülnek. A lista tartalmazni fogja a szabályzatokat és azt, hogy miért nem alkalmazzák őket. Ilyenek például azok a felhasználók és csoportok, amelyek kizárható a szabályzatból.

## <a name="use-case"></a>Használati eset

Számos szervezet hálózati helyen alapuló házirendeket hoz létre, lehetővé téve a megbízható helyek és a blokkoló helyek elérését, ahol a hozzáférés nem fordulhat elő.

Annak ellenőrzéséhez, hogy a konfiguráció megfelelően lett-e elvégezve, a rendszergazda a What If eszközzel utánozhatja a hozzáférést, egy olyan helyről, amelyet engedélyezni kell, és olyan helyről, amelyet meg kell tagadni.

![What If eszköz, amely blokkolja a hozzáférést](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-results.png)

Ebben az esetben a felhasználónak le kell tiltania a felhőalapú alkalmazásokhoz való hozzáférését Észak-Koreában, mivel a contoso letiltotta a hozzáférést az adott helyről.

Ez a teszt kiterjeszthető úgy, hogy más adatpontokat is egyesítse a hatókör szűkítéséhez.

## <a name="next-steps"></a>További lépések

* [Mi az a feltételes hozzáférés?](overview.md)
* [Mi az az Azure Active Directory Identity Protection?](../identity-protection/overview-v2.md)
* [Mi az az eszközidentitás?](../devices/overview.md)
* [Az Azure Multi-Factor Authentication működése](../authentication/concept-mfa-howitworks.md)
