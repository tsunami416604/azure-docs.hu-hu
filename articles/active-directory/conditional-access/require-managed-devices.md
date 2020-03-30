---
title: Feltételes hozzáférés hez felügyelt eszköz szükséges – Azure Active Directory
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directory (Azure AD) eszközalapú feltételes hozzáférés-szabályzatokat, amelyek felügyelt eszközöket igényelnek a felhőalapú alkalmazások eléréséhez.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/22/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a3c71534febc3cdb6429d3092225ebc73f6cbe7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481483"
---
# <a name="how-to-require-managed-devices-for-cloud-app-access-with-conditional-access"></a>Útmutató: Felügyelt eszközök megkövetelése a felhőalapú alkalmazások eléréséhez feltételes hozzáféréssel

A mobilos, felhőalapú világban az Azure Active Directory (Azure AD) bárhonnan egyszeri bejelentkezést tesz lehetővé az alkalmazásokba és a szolgáltatásokba. A jogosult felhasználók a felhőalapú alkalmazásokat számos eszközről érhetik el, beleértve a mobil- és a személyes eszközöket is. Azonban számos környezetben legalább néhány olyan alkalmazás sal rendelkezik, amelyet csak a biztonsági és megfelelőségi szabványoknak megfelelő eszközöknek kell elérniük. Ezeket az eszközöket felügyelt eszközöknek is nevezik. 

Ebből a cikkből megtudhatja, hogyan konfigurálhatja a feltételes hozzáférésű szabályzatokat, amelyek felügyelt eszközöket igényelnek a környezetbizonyos felhőalkalmazások eléréséhez. 

## <a name="prerequisites"></a>Előfeltételek

Felügyelt eszközök felügyelete a felhőbeli alkalmazás-hozzáféréshez az **Azure AD feltételes hozzáférést** és az Azure **AD-eszközkezelést** együtt kapcsolja ki. Ha még nem ismeri az alábbi területek egyikét, először olvassa el a következő témaköröket:

- **[Feltételes hozzáférés az Azure Active Directoryban](../active-directory-conditional-access-azure-portal.md)** – Ez a cikk a feltételes hozzáférés és a kapcsolódó terminológia fogalmi áttekintést nyújt.
- **[Bevezetés az Azure Active Directory eszközfelügyeletébe](../devices/overview.md)** – Ez a cikk áttekintést nyújt a szervezeti vezérlés alá helyezett eszközök különböző lehetőségeiről. 
- A Windows **10 Alkotók frissítéséhez (1703-as** vagy újabb verzió) telepített Chrome-támogatásért telepítse a [Windows 10-fiókok bővítményt.](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji) Erre a bővítményre akkor van szükség, ha egy feltételes hozzáférési szabályzat eszközspecifikus részleteket igényel.

>[!NOTE] 
> Azt javasoljuk, hogy az Azure AD-eszköz alapú feltételes hozzáférési szabályzat a legjobb kényszerítés a kezdeti eszköz hitelesítése után. Ez magában foglalja a munkamenetek lezárását, ha az eszköz kiesik a megfelelőségi és az eszköz kódáramlását.


## <a name="scenario-description"></a>Forgatókönyv leírása

A biztonság és a termelékenység közötti egyensúly elsajátítása kihívást jelent. A támogatott eszközök elterjedése a felhőbeli erőforrások eléréséhez segít a felhasználók termelékenységének növelésében. A másik oldalon valószínűleg nem szeretné, hogy a környezet bizonyos erőforrásait ismeretlen védelmi szintű eszközök érjék el. Az érintett erőforrások esetében meg kell követelnie, hogy a felhasználók csak felügyelt eszközzel férhessenek hozzájuk. 

Az Azure AD feltételes hozzáférés, akkor ezt a követelményt egyetlen szabályzat, amely hozzáférést biztosít:

- A kijelölt felhőalapú alkalmazásokra
- Kijelölt felhasználók és csoportok számára
- Felügyelt eszköz megkövetelése

## <a name="managed-devices"></a>Felügyelt eszközök  

Egyszerűen fogalmazva, a felügyelt eszközök olyan eszközök, amelyek *valamilyen* szervezeti ellenőrzés alatt állnak. Az Azure AD-ben a felügyelt eszköz előfeltétele, hogy az Azure AD-vel regisztrálva van. Az eszköz regisztrálása eszközazonosítót hoz létre az eszközhöz egy eszközobjektum formájában. Ezt az objektumot az Azure egy eszköz állapotinformációinak nyomon követésére használja. Azure AD-rendszergazdaként már használhatja ezt az objektumot az eszköz állapotának váltásához (engedélyezéséhez/letiltásához).
  
![Eszközalapú feltételek](./media/require-managed-devices/32.png)

Az Azure AD-ben regisztrált eszköz lekért két lehetősége van: 

- **Azure AD regisztrált eszközök** - az Azure AD-vel regisztrált személyes eszköz beszerezéséhez
- **Az Azure AD-hez csatlakozott eszközök** – egy olyan szervezeti Windows 10-eszköz lekért, amely nincs csatlakoztatva az Azure AD-hez regisztrált helyszíni AD-hez. 
- **Hibrid Azure AD-s csatlakozott eszközök** – lekért egy Windows 10 vagy támogatott down-level eszköz, amely csatlakozik egy helyszíni AD regisztrált Az Azure AD.

Ezt a három lehetőséget a cikk tárgyalja [Mi az eszköz identitása?](../devices/overview.md)

Felügyelt eszközké válásához a regisztrált eszköznek **hibrid Azure AD-hez csatlakozott eszköznek** vagy **megfelelőként megjelölt eszköznek**kell lennie.  

![Eszközalapú feltételek](./media/require-managed-devices/47.png)
 
## <a name="require-hybrid-azure-ad-joined-devices"></a>Hibrid Azure AD-hez csatlakozó eszközök megkövetelése

A feltételes hozzáférési szabályzatban **kiválaszthatja a Hibrid Azure AD-hez csatlakozott eszköz megkövetelése** lehetőséget, amely azt szeretné, hogy a kiválasztott felhőalapú alkalmazások csak felügyelt eszközzel érhetők el. 

![Eszközalapú feltételek](./media/require-managed-devices/10.png)

Ez a beállítás csak azokra a Windows 10-es vagy down-szintű eszközökre vonatkozik, mint például a Windows 7 vagy a Windows 8, amelyek egy helyszíni AD-hez csatlakoznak. Ezeket az eszközöket csak hibrid Azure AD-csatlakozással regisztrálhatja az Azure AD-vel, amely egy [automatikus folyamat](../devices/hybrid-azuread-join-plan.md) a Windows 10-es eszközök regisztrálásához. 

![Eszközalapú feltételek](./media/require-managed-devices/45.png)

Mi teszi a hibrid Azure AD-hez csatlakozott eszközt felügyelt eszközzé?  A helyszíni AD-hez csatlakozó eszközök esetében feltételezhető, hogy az ilyen eszközök vezérlése felügyeleti megoldások, például **a Configuration Manager** vagy a **csoportházirend (GP)** használatával kényszeríti ki őket. Mivel az Azure AD-nek nincs módszere annak megállapítására, hogy ezek a módszerek valamelyike egy eszközre lett-e alkalmazva, a hibrid Azure AD-hez csatlakozott eszköz megkövetelése viszonylag gyenge mechanizmus egy felügyelt eszköz megköveteléséhez. Rendszergazdaként ön dönti el, hogy a helyszíni tartományhoz csatlakozó eszközökre alkalmazott módszerek elég erősek-e ahhoz, hogy felügyelt eszközt képezzenek, ha egy ilyen eszköz egyhibrid Azure AD-hez csatlakozott eszköz is.

## <a name="require-device-to-be-marked-as-compliant"></a>Az eszköz megfelelőként való megjelölése

A felügyelt eszköz igényléséhez a legerősebb űrlap az a lehetőség, *amely megköveteli, hogy egy eszközt megfelelőként jelöljék meg.*

![Eszközalapú feltételek](./media/require-managed-devices/11.png)

Ehhez a beállításhoz egy eszköz regisztrálásához az Azure AD-hez kell regisztrálni, és a következőknek is megfelelőként kell megjelölni:
         
- Intune
- Külső gyártótól származó mobileszköz-felügyeleti (MDM) rendszer, amely az Azure AD-integráción keresztül kezeli a Windows 10-es eszközöket. A Windows 10-től eltérő eszközoperációsrendszer-típusokhoz nem támogatottak a külső gyártótól származó MDM-rendszerek.
 
![Eszközalapú feltételek](./media/require-managed-devices/46.png)

A megfelelőként megjelölt eszközök esetében a következőket feltételezheti: 

- A vállalati adatok eléréséhez a munkaerő által használt mobileszközök kezelése
- A munkaerő által használt mobilalkalmazások kezelése
- A vállalati adatok védelme azáltal történik, hogy szabályozhatják, hogyan fér hozzá és osztja meg a munkaerő
- Az eszköz és alkalmazásai megfelelnek a vállalati biztonsági követelményeknek

### <a name="known-behavior"></a>Ismert viselkedés

Windows 7, iOS, Android, macOS és néhány külső webböngészők az Azure AD azonosítja az eszközt egy ügyfél-tanúsítvány, amely ki van építve, ha az eszköz regisztrálva van az Azure AD. Amikor egy felhasználó először jelentkezik be a böngészőn keresztül, a rendszer kéri, hogy válassza ki a tanúsítványt. A végfelhasználónak ki kell választania ezt a tanúsítványt, mielőtt továbbra is használhatja a böngészőt.

## <a name="next-steps"></a>További lépések

Mielőtt konfigurálna egy eszközalapú feltételes hozzáférési szabályzatot a környezetében, tekintse meg az Azure Active Directory feltételes hozzáféréssel kapcsolatos [gyakorlati tanácsait.](best-practices.md)
