---
title: "Az Azure Active Directory feltételes hozzáférést vezérlő |} Microsoft Docs"
description: "Ismerje meg, az Azure Active Directory feltételes hozzáférést vezérlő működése."
services: active-directory
keywords: "alkalmazások, a feltételes hozzáférés az Azure ad-vel, a biztonságos hozzáférés a vállalati erőforrásokhoz, a feltételes hozzáférési házirendekkel a feltételes hozzáférés"
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2017
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 4cb225266a45808e5fda271e901749bf03c636e2
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="controls-in-azure-active-directory-conditional-access"></a>A vezérlők Azure Active Directory feltételes hozzáférés beállítása 

A [Azure Active Directory (Azure AD) feltételes hozzáférés](active-directory-conditional-access-azure-portal.md), szabályozhatja a hogyan engedéllyel rendelkező felhasználók hozzáférést a felhőalapú alkalmazásokat. A feltételes hozzáférési szabályzatot akkor határozza meg a válaszát ("Ehhez") egy adott feltételnek ("Ez akkor fordul elő"). Az a környezetben, a feltételes hozzáférés 

- "**Ha ez történik**" nevezik **utasítás feltétel**

- "**Majd ehhez**" nevezik **vezérlők**

![vezérlő](./media/active-directory-conditional-access-controls/11.png)

Egy feltétel utasítást a vezérlőkkel kombinációja a feltételes hozzáférési házirend jelöli.

![vezérlő](./media/active-directory-conditional-access-controls/12.png)

Minden vezérlő követelmény, hogy teljesülnek a személy által vagy rendszer jelentkezik be, vagy a bejelentkezés után a felhasználó a korlátozását teheti meg. 

A vezérlők két típusa van: 

- **Támogatás szabályozza** - kapu eléréséhez

- **Munkamenet-vezérlők** - munkameneten belül a hozzáférés korlátozása

Ez a témakör ismerteti a különböző vezérlők elérhető Azure AD feltételes hozzáférésével. 

## <a name="grant-controls"></a>Támogatás vezérlők

Támogatás vezérlőkkel teljesen letiltja a hozzáférést, vagy a kívánt vezérlők kiválasztásával további követelményekkel rendelkező hozzáférést. Több vezérlő is van szüksége:

- Az összes kijelölt vezérlők teljesítendő (*és*) 
- Egy kijelölt teljesítendő vezérlő (*vagy*)

![vezérlő](./media/active-directory-conditional-access-controls/17.png)



### <a name="multi-factor-authentication"></a>Többtényezős hitelesítés

Ez a vezérlő segítségével a többtényezős hitelesítést a megadott felhő-alkalmazás eléréséhez. Ez a vezérlő a következő multi-factor Authentication-szolgáltatók támogatja: 

- Azure Multi-Factor Authentication 

- Egy helyszíni többtényezős hitelesítési szolgáltató az Active Directory összevonási szolgáltatások (AD FS) együtt.
 
A multi-factor authentication segítségével erőforrások védelme a fent konfigurált érvényes felhasználó elsődleges hitelesítő adatai hozzáférést szerzett előfordulhat, hogy jogosulatlan felhasználók.



### <a name="compliant-device"></a>Szabályzatnak megfelelő eszköz

Konfigurálhatja, amelyek az eszközalapú feltételes hozzáférési szabályzatokat. Eszközalapú feltételes hozzáférési házirend célja a hozzáférési jogot a konfigurált erőforrások megbízható eszközökről. Megfelelő eszközökre használata egy beállítást meg kell adnia a megbízható eszközök. További információkért lásd: [Azure Active Directory eszközalapú feltételes hozzáférés szabályzatainak beállítása](active-directory-conditional-access-policy-connected-applications.md).

### <a name="domain-joined-device"></a>Tartományhoz csatlakozó eszközök

A tartományhoz csatlakoztatott eszközön egy másik lehetőség igénylő be kell állítania eszközalapú feltételes hozzáférési szabályzatokat. Ez a követelmény a Windows asztali számítógépek, laptopok és a helyszíni Active Directory tartományhoz csatlakoztatott vállalati táblagépek hivatkozik. További információkért lásd: [Azure Active Directory eszközalapú feltételes hozzáférés szabályzatainak beállítása](active-directory-conditional-access-policy-connected-applications.md).





### <a name="approved-client-app"></a>Jóváhagyott ügyfélalkalmazás

Mivel az alkalmazottak a mobileszközök mind a személyes és munkahelyi feladatokhoz használnak, szükség lehet az eszközhasználattal elért eszközök még abban az esetben, ha azok nem Ön által felügyelt vállalati adatok védelme érdekében.
Használhat [Intune alkalmazás adatvédelmi szabályzatok](https://docs.microsoft.com/intune/app-protection-policy) függetlenek a mobileszköz-kezelési (MDM) megoldásoktól a vállalati adatok védelme érdekében.


Jóváhagyott alkalmazások esetében egy ügyfél-alkalmazást, amely megpróbál hozzáférni a felhőalapú alkalmazások támogatására is megkövetelhető [Intune alkalmazás adatvédelmi szabályzatok](https://docs.microsoft.com/intune/app-protection-policy). Például korlátozhatja hozzáférés az Exchange Online az Outlook alkalmazást. Egy feltételes hozzáférési szabályzatot, amely megköveteli az jóváhagyott alkalmazások is nevezik [alkalmazás-alapú feltételes hozzáférési házirend](active-directory-conditional-access-mam.md). Ügyfelek támogatott jóváhagyott alkalmazások listáját lásd: [ügyfél app követelmény jóváhagyott](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement).


### <a name="terms-of-use"></a>Használati feltételek

A felhasználónak az erőforráshoz való hozzáférést megkapják előtt használati feltételeit a bérlőnél. Ha Ön rendszergazda konfigurálhatja és PDF-dokumentumokat feltöltésével testre szabhatja a használati feltételeket. Ha egy felhasználó tartozik a vezérlő alkalmazáshoz való hozzáférés hatókörét csak kap Ha született a használati feltételeket. 


### <a name="custom-controls"></a>Egyéni vezérlők 

Feltételes hozzáférés, amely átirányítja a felhasználók egy kompatibilis szolgáltatás megfelelnek a további Azure Active Directory kívül is létrehozhat egyéni vezérlők. Ez lehetővé teszi bizonyos külső többtényezős hitelesítés és ellenőrzési szolgáltatók a feltételes hozzáférési szabályok érvénybe, vagy a saját egyéni szolgáltatás létrehozásához. A vezérlő kielégítéséhez, a felhasználó böngészőben a külső szolgáltatás átirányítja hajt végre a szükséges hitelesítés vagy érvényesítési tevékenységek és van majd visszairányítja Azure Active Directory. Ha a felhasználó sikeresen hitelesített vagy érvényesítve, a felhasználó a feltételes hozzáférés folyamata folytatódik. 

## <a name="custom-controls"></a>Egyéni vezérlők

Egyéni vezérlők olyan Azure Active Directory Premium P2 kiadás egy képesség. Egyéni vezérlők használatakor a rendszer átirányítja a felhasználókat megfelelnek a további Azure Active Directory kívül kompatibilis szolgáltatás. A vezérlő kielégítéséhez, a felhasználó böngészőben a külső szolgáltatás átirányítja hajt végre a szükséges hitelesítés vagy érvényesítési tevékenységek és van majd visszairányítja Azure Active Directory. Az Azure Active Directory ellenőrzi a választ, és ha a felhasználó sikeresen hitelesített vagy érvényesítve, a felhasználó a feltételes hozzáférés folyamata a továbbra is.

Ezek az intézkedések egyes külső vagy egyéni szolgáltatást is feltételes hozzáférés-vezérlés használatának engedélyezése, és általában a feltételes hozzáférés képességekkel bővíthetik.

Jelenleg a egy kompatibilis szolgáltatást nyújtó szolgáltatók a következők:

- Duo biztonsági

- RSA

- [Trusona](https://www.trusona.com/docs/azure-ad-integration-guide)

Ezek a szolgáltatások további információkért lépjen kapcsolatba a szolgáltatók közvetlenül.

### <a name="creating-custom-controls"></a>Egyéni vezérlők létrehozását

Egyéni vezérlő létrehozására, lépjen kapcsolatba a szolgáltatót, amelyet használni kíván. Egyes nem a Microsofttól szolgáltató rendelkezik, saját folyamat és a regisztráció, előfizetés vagy más módon lesz a szolgáltatás része, és annak jelzésére, hogy szeretné-e integrációt a feltételes hozzáférési követelmények. Ezen a ponton a szolgáltató biztosít egy adatblokk JSON formátumban. Ezek az adatok lehetővé teszi a szolgáltató és a feltételes hozzáférés működjön együtt a bérlő, hoz létre az új ellenőrzését, és határozza meg a feltételes hozzáférés segítségével megállapíthatja, ha a felhasználók sikeresen végeztek el a szolgáltató a ellenőrzése.

Másolja át a JSON-adatokat, és illessze be a kapcsolódó szövegmezőbe. Ne tegye a módosításokat a JSON Ha nincs tisztában a módosítás hajt végre explicit módon. Bármilyen módosítás a sikerült bontsa a kapcsolatot a szolgáltató és a Microsoft- és potenciálisan a és a felhasználók kívül a fiókok zárolása.

Hozzon létre egy egyéni vezérlő nem található a **kezelése** szakasza a **feltételes hozzáférés** lap.

![vezérlő](./media/active-directory-conditional-access-controls/82.png)

Kattintson a **új egyéni vezérlő**, megnyílik egy panel a szövegmezőben a JSON-adatok a vezérlő.  


![vezérlő](./media/active-directory-conditional-access-controls/81.png)


### <a name="deleting-custom-controls"></a>Egyéni vezérlők törlése

Törli az egyéni vezérlőt, akkor előbb ellenőrizze, hogy nincs használatban a feltételes hozzáférési házirendben. Hiba a befejezést:

1. Nyissa meg az egyéni vezérlők listájához

2. Kattintson...  

3. Válassza a **Törlés** elemet.

### <a name="editing-custom-controls"></a>Egyéni vezérlők szerkesztése

Egyéni vezérlő szerkesztése, törlése az aktuális vezérlő, és hozzon létre egy új vezérlőelemet a frissített információk.




## <a name="session-controls"></a>Munkamenet-vezérlők

Munkamenet vezérlők a felhőalapú alkalmazások korlátozott tapasztalatok teszik lehetővé. A munkamenet vezérlők felhőalkalmazások érvényesíti, és további Azure AD-be a munkamenetre vonatkozó az alkalmazás által biztosított információk alapján.

![vezérlő](./media/active-directory-conditional-access-controls/31.png)

### <a name="use-app-enforced-restrictions"></a>Használja a kényszerített alkalmazásra vonatkozó korlátozások

A vezérlő segítségével az Azure AD át az eszköz információkat a cloud app szükséges. Ez segít a cloud app, ha a felhasználó a megfelelő eszköz vagy a tartományhoz csatlakoztatott eszköz származik. Ez a vezérlő jelenleg csak a SharePoint, a cloud app támogatott. SharePoint biztosít a felhasználók egy teljes vagy korlátozott élmény attól függően, hogy az eszköz állapota az eszközinformáció használja.
A SharePoint korlátozott hozzáférést igényelnek kapcsolatos további tudnivalókért lásd: [szabályozhatja a hozzáférést a nem felügyelt eszközökön](https://aka.ms/spolimitedaccessdocs).



## <a name="next-steps"></a>Következő lépések

- Ha meg szeretné ismerni a feltételes hozzáférési házirend konfigurálása tudnivalókat [Ismerkedés a feltételes hozzáférés az Azure Active Directoryban](active-directory-conditional-access-azure-portal-get-started.md).

- Ha készen áll a környezet feltételes hozzáférési házirend-beállításokkal, tekintse meg a [ajánlott eljárások a feltételes hozzáférés az Azure Active Directoryban](active-directory-conditional-access-best-practices.md). 
