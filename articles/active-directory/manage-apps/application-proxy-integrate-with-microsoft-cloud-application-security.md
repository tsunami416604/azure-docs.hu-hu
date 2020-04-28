---
title: Helyszíni alkalmazások integrálása Cloud App Security-Azure AD-vel
description: Helyszíni alkalmazás konfigurálása Azure Active Directoryban a Microsoft Cloud App Security (MCAS) szolgáltatással való együttműködéshez. A MCAS feltételes hozzáférést biztosító alkalmazás-vezérlő segítségével valós időben figyelheti és irányíthatja a munkameneteket a feltételes hozzáférési szabályzatok alapján. Ezeket a házirendeket alkalmazhatja a Azure Active Directory (Azure AD) alkalmazásproxy használatát használó helyszíni alkalmazásokra.
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb97f9dd87277215a5d4708d3a6f49564c490204
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "74275497"
---
# <a name="configure-real-time-application-access-monitoring-with-microsoft-cloud-app-security-and-azure-active-directory"></a>A valós idejű alkalmazás-hozzáférés figyelésének konfigurálása Microsoft Cloud App Security és Azure Active Directory
Azure Active Directory (Azure AD) helyszíni alkalmazásának konfigurálása a Microsoft Cloud App Security (MCAS) valós idejű figyeléshez való használatához. A MCAS a feltételes hozzáférést biztosító alkalmazás-vezérlő a feltételes hozzáférési házirendek alapján valós időben figyeli és vezérli a munkameneteket. Ezeket a házirendeket alkalmazhatja a Azure Active Directory (Azure AD) alkalmazásproxy használatát használó helyszíni alkalmazásokra.

Íme néhány példa arra, hogy milyen típusú házirendeket hozhat létre a MCAS használatával:

- A bizalmas dokumentumok letöltésének tiltása vagy letiltása a nem felügyelt eszközökön.
- Figyelje meg, hogy mikor jelentkeznek be a magas kockázatú felhasználók az alkalmazásokba, majd a munkamenetből naplózzák a műveleteiket. Ezeknek az információknak a segítségével elemezheti a felhasználói viselkedést, és meghatározhatja a munkamenet-szabályzatok alkalmazását.
- Az ügyféltanúsítványok vagy az eszközök megfelelőségének használatával letilthatja a nem felügyelt eszközökről származó adott alkalmazásokhoz való hozzáférést.
- A nem vállalati hálózatokból származó felhasználói munkamenetek korlátozása. Korlátozott hozzáférést biztosíthat a vállalati hálózaton kívülről egy alkalmazáshoz hozzáférő felhasználók számára. Ez a korlátozott hozzáférés például megakadályozhatja, hogy a felhasználó bizalmas dokumentumokat töltsön le.

További információ: [alkalmazások Microsoft Cloud App Security feltételes hozzáférést biztosító alkalmazás-vezérlőekkel való védelemmel való](/cloud-app-security/proxy-intro-aad)ellátása.

## <a name="requirements"></a>Követelmények

Engedély

- EMS E5-licenc, vagy 
- Prémium szintű Azure Active Directory P1 és MCAS önálló.

Helyszíni alkalmazás:

- A helyszíni alkalmazásnak a Kerberos által korlátozott delegálást (KCD) kell használnia.

Alkalmazásproxy konfigurálása:

- Az Azure AD konfigurálása alkalmazásproxy használatára, beleértve a környezet előkészítését és az alkalmazásproxy-összekötő telepítését. Oktatóanyag: helyszíni [alkalmazások távoli eléréshez való hozzáadása az Azure ad-ben alkalmazásproxy használatával](application-proxy-add-on-premises-application.md). 

## <a name="add-on-premises-application-to-azure-ad"></a>Helyszíni alkalmazás hozzáadása az Azure AD-hez

Helyszíni alkalmazás hozzáadása az Azure AD-hez. A gyors útmutatóért lásd: helyszíni [alkalmazás hozzáadása az Azure ad-](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)hez. Az alkalmazás hozzáadásakor ügyeljen arra, hogy az alábbi két beállítást adja meg a helyszíni **alkalmazás hozzáadása** panelen:

- **Előzetes hitelesítés**: írja be a **Azure Active Directory**.
- **URL-címek fordítása az alkalmazás törzsében**: válassza az **Igen**lehetőséget.

Ezek a két beállítás szükséges ahhoz, hogy az alkalmazás működjön a MCAS.

## <a name="test-the-on-premises-application"></a>Helyszíni alkalmazás tesztelése

Miután hozzáadta az alkalmazást az Azure AD-hoz, kövesse az [alkalmazás tesztelése](application-proxy-add-on-premises-application.md#test-the-application) című témakör lépéseit a felhasználók teszteléshez való hozzáadásához és a bejelentkezés teszteléséhez. 

## <a name="deploy-conditional-access-app-control"></a>feltételes hozzáférést biztosító alkalmazás-vezérlő üzembe helyezése

Az alkalmazás feltételes hozzáférésű alkalmazás-vezérlővel való konfigurálásához kövesse az [Azure ad-alkalmazások feltételes hozzáférés-vezérlésének üzembe helyezése](/cloud-app-security/proxy-deployment-aad)című témakör utasításait.


## <a name="test-conditional-access-app-control"></a>Tesztelés feltételes hozzáférést biztosító alkalmazás-vezérlő

Az Azure AD-alkalmazások feltételes hozzáférésű alkalmazás-vezérléssel történő üzembe helyezésének teszteléséhez kövesse az [Azure ad-alkalmazások üzembe helyezésének tesztelése](/cloud-app-security/proxy-deployment-aad)című témakör utasításait.





