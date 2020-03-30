---
title: Feltételes hozzáférés – Az Azure Active Directory használatával – feltételes hozzáférés – hibaelhárítása
description: Hol található, hogy milyen feltételes hozzáférési házirendeket alkalmaztak, és miért
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
ms.openlocfilehash: 661afc08c76c6cde61b02a29a55b4a8bec932e21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73175799"
---
# <a name="troubleshooting-conditional-access-using-the-what-if-tool"></a>Feltételes hozzáférés hibaelhárítása a Mi lenne, ha eszközzel

A [Mi van ha eszköz](what-if-tool.md) feltételes hozzáférés hatékony, amikor megpróbálja megérteni, hogy miért egy szabályzat volt, vagy nem alkalmazták a felhasználó egy adott körülmények között, vagy ha a házirend vonatkozna egy ismert állapotban.

A Mi van, ha eszköz az Azure Active Directory > **feltételes hozzáférése** > az **Azure Portalon** > **Azure Active Directory****található.**

![Feltételes hozzáférés , ha az eszköz alapértelmezett állapotban](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-tool.png)

> [!NOTE]
> A Mi ha eszköz jelenleg nem értékeli ki a házirendeket csak jelentésmódban.

## <a name="gathering-information"></a>Információgyűjtés

A Mi van, ha eszköz csak egy **felhasználót** igényel az első lépésekhez. 

A következő kiegészítő információk nem kötelezőek, de segítenek a konkrét esetek hatókörének szűkítésében.

* Felhőalkalmazások vagy műveletek
* IP-cím 
* Ország
* Eszközplatform
* Ügyfélalkalmazások (előzetes verzió)
* Eszköz állapota (előzetes verzió) 
* Bejelentkezési kockázat

Ezeket az információkat a felhasználótól, az eszköztől vagy az Azure AD bejelentkezési bejelentkezési naplóból lehet összegyűjteni.

## <a name="generating-results"></a>Eredmények létrehozása

Adja meg az előző szakaszban összegyűjtött feltételeket, és válassza a **Mi lenne, ha** létrehoz egy eredménylistát. 

Bármikor kiválaszthatja az **Alaphelyzet** lehetőséget a feltételbevitel törléséhez és az alapértelmezett állapotba való visszatéréshez.

## <a name="evaluating-results"></a>Az eredmények értékelése

### <a name="policies-that-will-apply"></a>A alkalmazni kívánt politikák

Ez a lista megmutatja, hogy a feltételeknek adott feltételek nek tekintettel mely feltételes hozzáférési házirendeket kellene alkalmazni. A lista tartalmazza az alkalmazandó támogatási és munkamenet-vezérlőket is. Ilyenek például a többtényezős hitelesítés megkövetelése egy adott alkalmazás eléréséhez.

### <a name="policies-that-will-not-apply"></a>Nem alkalmazandó házirendek

Ez a lista olyan feltételes hozzáférési házirendeket jelenít meg, amelyek nem lennének érvényesek, ha a feltételek et alkalmaznák. A lista tartalmazza a szabályzatokat és annak okát, hogy miért nem vonatkoznak. Ilyenek például a felhasználók és csoportok, amelyek kizárhatók a házirendből.

## <a name="use-case"></a>Használati eset

Számos szervezet hálózati helyek alapján hoz létre házirendeket, lehetővé téve a megbízható helyeket és a blokkolt helyeket, ahol a hozzáférés nem fordulhat elő.

Annak ellenőrzéséhez, hogy a konfiguráció megfelelően lett-e elkészülve, a rendszergazda a Mi lenne, ha eszközt használhatja a hozzáférés utánzására, egy olyan helyről, amelyet engedélyezni kell, és egy olyan helyről, amelyet meg kell tagadni.

![Mi a következő, ha a blokkhozzáféréssel találatokat megjelenítő eszköz](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-results.png)

Ebben az esetben a felhasználó le lenne tiltva az észak-koreai utazássorán a felhőalkalmazások elérésében, mivel a Contoso letiltotta a hozzáférést az adott helyről.

Ez a teszt kiterjeszthető más adatpontok rakterének szűkítésére.

## <a name="next-steps"></a>További lépések

* [Mi a feltételes hozzáférés?](overview.md)
* [Mi az az Azure Active Directory Identity Protection?](../identity-protection/overview-v2.md)
* [Mi az az eszközidentitás?](../devices/overview.md)
* [Az Azure Multi-Factor Authentication működése](../authentication/concept-mfa-howitworks.md)
