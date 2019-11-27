---
title: A feltételes hozzáféréshez felügyelt eszköz szükséges – Azure Active Directory
description: Megtudhatja, hogyan konfigurálhat olyan Azure Active Directory (Azure AD) eszköz-alapú feltételes hozzáférési szabályzatokat, amelyek felügyelt eszközöket igényelnek a felhőalapú alkalmazásokhoz való hozzáféréshez.
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
ms.openlocfilehash: 0caf8e8d5e18efc0a7332f97acccc394051ed360
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74452394"
---
# <a name="how-to-require-managed-devices-for-cloud-app-access-with-conditional-access"></a>Útmutató: felügyelt eszközök megkövetelése a Cloud app Accesshez feltételes hozzáféréssel

A mobil-első, Felhőbeli első világában Azure Active Directory (Azure AD) lehetővé teszi az alkalmazások és a szolgáltatások egyszeri bejelentkezését, bárhonnan. A jogosult felhasználók számos eszközről, például mobilról és személyes eszközökről is hozzáférhetnek a felhőalapú alkalmazásaihoz. Azonban számos környezetben van legalább néhány alkalmazás, amelyet csak olyan eszközök férhetnek hozzá, amelyek megfelelnek a biztonsági és megfelelőségi szabványoknak. Ezeket az eszközöket felügyelt eszközöknek is nevezzük. 

Ez a cikk azt ismerteti, hogyan konfigurálhat olyan feltételes hozzáférési szabályzatokat, amelyek a felügyelt eszközöket igénylik bizonyos felhőalapú alkalmazások eléréséhez a környezetben. 

## <a name="prerequisites"></a>Előfeltételek

A felügyelt eszközök Felhőbeli alkalmazásokhoz való hozzáférésének megkövetelése az **Azure ad feltételes hozzáférés** és az **Azure ad-eszközök felügyeletével** együtt. Ha még nem ismeri ezen területek valamelyikét, először olvassa el a következő témaköröket:

- **[Feltételes hozzáférés Azure Active Directoryban](../active-directory-conditional-access-azure-portal.md)** – ez a cikk a feltételes hozzáférés és a kapcsolódó terminológia fogalmi áttekintését tartalmazza.
- Az **[eszközkezelés bemutatása Azure Active Directoryban](../devices/overview.md)** – ez a cikk áttekintést nyújt azokról a különböző lehetőségekről, amelyekkel az eszközöket a szervezeti vezérlés alatt szerezheti be. 

## <a name="scenario-description"></a>Forgatókönyv leírása

A biztonság és a termelékenység egyensúlyának elsajátítása kihívást jelent. A támogatott eszközöknek a Felhőbeli erőforrások eléréséhez való elterjedése segíti a felhasználók termelékenységének növelését. A flip oldalon valószínűleg nem szeretné, hogy a környezet bizonyos erőforrásaihoz olyan eszközök férhessenek hozzá, amelyek ismeretlen védelmi szinttel rendelkeznek. Az érintett erőforrások esetében meg kell követelnie, hogy a felhasználók csak felügyelt eszköz használatával férhessenek hozzájuk. 

Az Azure AD feltételes hozzáférésével ezt a követelményt egyetlen, hozzáférést biztosító szabályzattal kezelheti:

- A kiválasztott felhőalapú alkalmazások
- A kiválasztott felhasználók és csoportok esetében
- Felügyelt eszköz megkövetelése

## <a name="managed-devices"></a>Felügyelt eszközök  

Egyszerű feltételek esetén a felügyelt eszközök olyan eszközök, amelyek *valamilyen* szervezeti szabályozás alatt állnak. Az Azure AD-ben a felügyelt eszköz előfeltétele, hogy regisztrálva legyen az Azure AD-ben. Az eszköz regisztrálása eszköz-objektum formájában hozza létre az eszköz identitását. Ezt az objektumot az Azure használja az eszköz állapotával kapcsolatos információk nyomon követésére. Azure AD-rendszergazdaként ezt az objektumot már használhatja az eszköz állapotának bekapcsolásához (engedélyezéséhez vagy letiltásához).
  
![Eszköz alapú feltételek](./media/require-managed-devices/32.png)

Az Azure AD-ben regisztrált eszköz beszerzéséhez három lehetősége van: 

- **Azure ad-beli regisztrált eszközök** – az Azure ad-ben regisztrált személyes eszközök beszerzése
- **Azure ad-hez csatlakoztatott eszközök** – olyan szervezeti Windows 10-es eszközök beszerzéséhez, amelyek nem csatlakoznak az Azure ad-ben regisztrált helyszíni ad-hez. 
- **Hibrid Azure ad-hez csatlakoztatott eszközök** – a Windows 10 vagy az Azure ad-ben regisztrált helyszíni ad-hez csatlakoztatott, régebbi verziójú eszköz beszerzése.

Ezt a három lehetőséget a cikk a [Mi az eszköz identitása](../devices/overview.md) című cikkben tárgyalja?

Felügyelt eszközként a regisztrált eszköznek egy **hibrid Azure ad-hez csatlakoztatott eszköznek** vagy egy **megfelelőként megjelölt eszköznek**kell lennie.  

![Eszköz alapú feltételek](./media/require-managed-devices/47.png)
 
## <a name="require-hybrid-azure-ad-joined-devices"></a>Hibrid Azure AD-hez csatlakoztatott eszközök megkövetelése

A feltételes hozzáférési szabályzatban kiválaszthatja a **hibrid Azure ad-hez csatlakoztatott eszköz megkövetelése** lehetőséget, hogy a kiválasztott felhőalapú alkalmazások csak felügyelt eszköz használatával legyenek elérhetők. 

![Eszköz alapú feltételek](./media/require-managed-devices/10.png)

Ez a beállítás csak olyan Windows 10 vagy régebbi rendszerű eszközökre vonatkozik, mint a Windows 7 vagy a Windows 8, amelyek egy helyszíni AD-hez csatlakoznak. Ezeket az eszközöket csak hibrid Azure AD-csatlakozással regisztrálhatja az Azure AD-vel, amely egy [automatizált folyamat](../devices/hybrid-azuread-join-plan.md) , amellyel regisztrálható a Windows 10-es eszköz. 

![Eszköz alapú feltételek](./media/require-managed-devices/45.png)

Mi teszi a hibrid Azure AD-hez csatlakoztatott eszköz felügyelt eszközét?  A helyszíni AD-hez csatlakoztatott eszközök esetében feltételezhető, hogy az eszközök vezérlése a felügyeleti megoldások, például a **System Center Configuration Manager (SCCM)** vagy a **Csoportházirend (GP)** használatával kényszeríthető a felügyeletre. Mivel az Azure AD nem tudja meghatározni, hogy a módszerek bármelyikét alkalmazták-e egy eszközre, a hibrid Azure AD-hez csatlakoztatott eszközök viszonylag gyenge mechanizmust igényelnek a felügyelt eszközök megköveteléséhez. Önnek rendszergazdaként kell megítélnie, hogy a helyszíni tartományhoz csatlakoztatott eszközökön alkalmazott módszerek elég erősek-e ahhoz, hogy felügyelt eszközöket képezzenek, ha az adott eszköz hibrid Azure AD-hez csatlakoztatott eszköz is.

## <a name="require-device-to-be-marked-as-compliant"></a>Eszköz megfelelőként való megjelölésének megkövetelése

Az *eszköz megfelelőként való megjelölésének* lehetősége a felügyelt eszközök igénylésének legerősebb formája.

![Eszköz alapú feltételek](./media/require-managed-devices/11.png)

Ehhez a beállításhoz az szükséges, hogy az eszköz regisztrálva legyen az Azure AD-ben, és a következő módon legyen megjelölve:
         
- Intune
- Egy külső gyártótól származó mobileszköz-felügyeleti (MDM) rendszer, amely az Azure AD-integráción keresztül kezeli a Windows 10-es eszközöket. A harmadik féltől származó MDM rendszerek nem Windows 10 rendszerű eszközökön használhatók.
 
![Eszköz alapú feltételek](./media/require-managed-devices/46.png)

A megfelelőként megjelölt eszközök esetében feltételezhető, hogy: 

- A munkaerő által a vállalat adataihoz való hozzáféréshez használt mobileszközök kezelése
- A munkaerő által használt mobil alkalmazások kezelése
- A céges adatok védelméhez segítséget nyújt a munkatársak hozzáférésének és megosztásának szabályozása
- Az eszköz és az alkalmazásai megfelelnek a vállalat biztonsági követelményeinek

> [!NOTE]
> Ha a szabályzatot úgy konfigurálja, hogy megkövetelje a megfelelő eszközök felhasználóinak megkövetelését Mac, iOS és Android rendszerre, az eszköz tanúsítványának kiválasztásához a szabályzat kiértékelése során. Ez egy ismert viselkedés.

## <a name="next-steps"></a>További lépések

Az eszköz alapú feltételes hozzáférési szabályzatnak a környezetben való konfigurálása előtt tekintse át a [Azure Active Directory a feltételes hozzáférés ajánlott eljárásait](best-practices.md).
