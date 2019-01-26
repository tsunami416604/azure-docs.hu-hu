---
title: Hogyan – megkövetelése felügyelt eszközök az Azure Active Directory feltételes hozzáféréssel felhőalapú alkalmazás eléréséhez |} A Microsoft Docs
description: Ismerje meg, amely a felügyelt eszközök igényel a felhőalapú alkalmazások elérésének Azure Active Directory (Azure AD) eszközalapú feltételes hozzáférési szabályzatok konfigurálása.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: a27862a6-d513-43ba-97c1-1c0d400bf243
ms.service: active-directory
ms.subservice: conditional-access
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 8b772f24bf86cebbee8c10526c344681dc91dac4
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2019
ms.locfileid: "55075110"
---
# <a name="how-to-require-managed-devices-for-cloud-app-access-with-conditional-access"></a>kézikönyv: A felügyelt eszközök szükségesek a feltételes hozzáféréssel felhőalapú alkalmazás-hozzáférés

Mobileszközök és a felhő-és felhőközpontú világában Azure Active Directory (Azure AD) lehetővé teszi, hogy egyszeri bejelentkezés alkalmazásokhoz és szolgáltatásokhoz bárhonnan. Jogosult felhasználók férhessenek a felhőalapú alkalmazások származó sokféle eszközt, beleértve a mobil- és személyes eszközök is. Sok környezetben azonban csak legyenek elérhetők a eszközöket, amelyek a biztonsági és megfelelőségi szabványoknak legalább néhány alkalmazások rendelkeznek. Ezek az eszközök felügyelt eszközök is ismertek. 

Ez a cikk azt ismerteti, hogy hogyan konfigurálhat feltételes hozzáférési szabályzatokat, amelyek a felügyelt eszközök férhessenek hozzá az egyes felhőalkalmazások a környezetében szükséges. 


## <a name="prerequisites"></a>Előfeltételek

A felügyelt eszközök megkövetelése a cloud app access ties **Azure AD feltételes hozzáférés** és **Azure AD eszközkezeléséről** együtt. Ha Ön még nem ismeri a következő területeken, olvassa el a következő témakörök először:

- **[Az Azure Active Directory feltételes hozzáférés](../active-directory-conditional-access-azure-portal.md)**  – Ez a cikk ismerteti a feltételes hozzáférés és a kapcsolódó terminológia fogalmi áttekintése.

- **[Az Eszközfelügyelet az Azure Active Directory bemutatása](../devices/overview.md)**  – Ez a cikk áttekintést nyújt a különböző beállítások eszközök szervezeti ellenőrzés alatt van. 


## <a name="scenario-description"></a>Forgatókönyv leírása

Biztonság és hatékonyság közötti egyensúly szakértő kezelése kihívást jelent. Támogatott eszközök hozzáférését a felhőbeli erőforrások elterjedése segítségével a felhasználók a hatékonyság növelése érdekében. A tükrözés oldalon valószínűleg nem lesz szüksége bizonyos erőforrások érnek el egy ismeretlen védelmi szintet az eszközök a környezetben. Érintett erőforrások érdemes beállítani, hogy felhasználók csak érhetik el azokat a felügyelt eszközök használata. 

Az Azure AD feltételes hozzáférés meg lehet oldani ezt a követelményt, egy egyetlen szabályzattal, amely engedélyezi a hozzáférést:

- A kiválasztott felhőalapú alkalmazások

- A kijelölt felhasználók és csoportok

- Felügyelt eszköz megkövetelése


## <a name="managed-devices"></a>A felügyelt eszközök  

Egyszerűen fogalmazva a felügyelt eszközök azok az alatt álló eszközök *valamilyen* szervezeti vezérlő. Az Azure AD-ben a felügyelt eszközök előfeltétel, hogy azt az Azure ad-ben regisztrálták. Az eszköz identitásának eszköz regisztrálásához ajánlatos hoz létre a hálózatieszköz-objektum formájában. Ez az objektum állapotát nyomon követheti a olyan eszköz, az Azure használja. Azure ad-ben a rendszergazdák már használhatja ezt az objektumot a váltógomb (engedélyezés/letiltás) egy eszköz állapotáról.
  
![Eszköz-alapú feltételek](./media/require-managed-devices/32.png)

Három lehetősége van az Azure ad-vel regisztrált eszköz lekéréséhez:

- **[Az Azure ad-ben regisztrált eszközök](../devices/overview.md#azure-ad-registered-devices)**  – az Azure ad-vel regisztrált egy személyes eszköz

- **[Az Azure AD-hez csatlakoztatott eszközök](../devices/overview.md#azure-ad-joined-devices)**  – egy szervezeti Windows 10 rendszerű eszköz, amely nem csatlakozik egy helyszíni ad-ben regisztrált az Azure ad-ben. 

- **[Hibrid Azure AD-hez csatlakoztatott eszközök](../devices/overview.md#hybrid-azure-ad-joined-devices)**  – a Windows 10-es vagy a támogatott régebbi verziójú eszközt, amely csatlakozik egy helyszíni ad-ben regisztrált az Azure ad-ben.

Felügyelt eszközzé válik, egy regisztrált eszközt kell, vagy egy **hibrid Azure AD-hez csatlakoztatott eszköz** vagy egy **megfelelőként megjelölt eszköz**.  

![Eszköz-alapú feltételek](./media/require-managed-devices/47.png)

 
## <a name="require-hybrid-azure-ad-joined-devices"></a>Szükséges a hibrid Azure AD-hez csatlakoztatott eszközök

A feltételes hozzáférési szabályzat kiválaszthatja **hibrid Azure AD-csatlakoztatott eszköz megkövetelése** , de a, hogy a kiválasztott felhőalapú alkalmazások csak érhetők el a felügyelt eszközök használata. 

![Eszköz-alapú feltételek](./media/require-managed-devices/10.png)

Ez a beállítás csak Windows 10-es vagy régebbi verziójú eszközök például a Windows 7 vagy Windows 8, amely csatlakozik egy helyszíni AD. Csak regisztrálhatja ezeket az eszközöket az Azure AD-bA egy hibrid Azure AD joinnal, amely egy [automatikus folyamat](../devices/hybrid-azuread-join-plan.md) egy regisztrált Windows 10-es eszköz. 

![Eszköz-alapú feltételek](./media/require-managed-devices/45.png)

Eszköz teszi a hibrid Azure ad-ben csatlakozott egy felügyelt eszközt?  Egy helyszíni tartományhoz csatlakoztatott eszközök AD, feltételezzük, hogy ezek az eszközök felett van érvényben, használjon, például felügyeleti megoldások **System Center Configuration Manager (SCCM)** vagy **csoportházirend (GP)** őket. Mivel nincs semmilyen metódus az Azure ad-e az alábbi módszereket írtunk egy eszközt meghatározásához, a hibrid Azure AD léptetett eszköz megkövetelése egy viszonylag egyszerű olyan mechanizmus, egy felügyelt eszköz megkövetelése. Célszerű megítélhető, hogy a módszereket, amelyek érvényesek a helyi tartományhoz csatlakoztatott eszközök-e elég erős a felügyelt eszközök jelent, ha ilyen eszközt is egy hibrid Azure AD-csatlakoztatású eszközt rendszergazdaként.


## <a name="require-device-to-be-marked-as-compliant"></a>Eszköz megfelelőként való megjelölésének megkövetelése

Lehetőség *megfelelőként megjelölt eszköz megkövetelése* a legerősebb űrlap kérése egy felügyelt eszközt.

![Eszköz-alapú feltételek](./media/require-managed-devices/11.png)

Ez utóbbi lehetőség megköveteli egy eszközt regisztrálni kell az Azure ad-vel és előírásainak megjelölni:
         
- Intune-ban.
- Egy harmadik fél mobileszköz-felügyelet (MDM) rendszer által felügyelt Windows 10-es eszközök Azure AD-integráció. Eszköz operációs rendszer típusa Windows 10-es nem harmadik fél mobileszköz-kezelési rendszerek nem támogatottak.
 
![Eszköz-alapú feltételek](./media/require-managed-devices/46.png)



Megfelelőként megjelölt eszköz akkor feltételezheti, hogy: 

- Kezeli a mobileszközöket, a munkatársak által használt céges adatok elérésére
- A munkatársak által használt mobilalkalmazásokat kezel.
- A vállalati adatok védelmét a módja a munkatársak elérik és megosztják őket szabályozva
- Az eszköz és az alkalmazások megfeleljenek a vállalat biztonsági követelményeinek




## <a name="next-steps"></a>További lépések

Eszközalapú feltételes hozzáférési szabályzat konfigurálása a környezetében, előtt meg kell vessen egy pillantást a [ajánlott eljárások az Azure Active Directory feltételes hozzáférés](best-practices.md).

