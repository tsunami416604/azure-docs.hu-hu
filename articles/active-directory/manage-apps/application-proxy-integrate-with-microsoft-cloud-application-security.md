---
title: Helyszíni alkalmazások integrálása a Cloud App Security szolgáltatással – Azure AD
description: Konfiguráljon egy helyszíni alkalmazást az Azure Active Directoryban a Microsoft Cloud App Security (MCAS) használatával való együttműködésre. Az MCAS feltételes hozzáférésű alkalmazásvezérlővel valós időben figyelheti és szabályozhatja a munkameneteket feltételes hozzáférési házirendek alapján. Ezeket a szabályzatokat alkalmazhatja az Azure Active Directoryban (Azure AD) alkalmazásproxyt használó helyszíni alkalmazásokra.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275497"
---
# <a name="configure-real-time-application-access-monitoring-with-microsoft-cloud-app-security-and-azure-active-directory"></a>Valós idejű alkalmazáshozzáférés-figyelés konfigurálása a Microsoft Cloud App Security és az Azure Active Directory segítségével
Konfiguráljon egy helyszíni alkalmazást az Azure Active Directoryban (Azure AD) a Microsoft Cloud App Security (MCAS) valós idejű figyeléséhez. Az MCAS feltételes hozzáférésű alkalmazásvezérléssel figyeli és szabályozza a munkameneteket valós idejű, feltételes hozzáférési házirendek alapján. Ezeket a szabályzatokat alkalmazhatja az Azure Active Directoryban (Azure AD) alkalmazásproxyt használó helyszíni alkalmazásokra.

Íme néhány példa az MCAS-sel létrehozható házirendtípusokra:

- A bizalmas dokumentumok nem felügyelt eszközökön való letöltésének letiltása vagy védelme.
- Figyelje, hogy a magas kockázatú felhasználók mikor jelentkeznek be az alkalmazásokba, majd a munkameneten belül naplózza a műveleteket. Ezzel az információval elemezheti a felhasználói viselkedést, hogy meghatározza a munkamenet-házirendek alkalmazását.
- Az ügyféltanúsítványok vagy az eszközmegfelelőség használatával letilthatja a nem felügyelt eszközökről származó alkalmazásokhoz való hozzáférést.
- A nem vállalati hálózatok felhasználói munkameneteinek korlátozása. Korlátozott hozzáférést biztosíthat a vállalati hálózaton kívülről egy alkalmazáshoz hozzáférő felhasználókszámára. Ez a korlátozott hozzáférés például letilthatja a felhasználót a bizalmas dokumentumok letöltésétől.

További információt az [Alkalmazások védelme a Microsoft Cloud App Security feltételes hozzáférésű alkalmazásvezérléssel című témakörben talál.](/cloud-app-security/proxy-intro-aad)

## <a name="requirements"></a>Követelmények

Engedély:

- EMS E5 licenc, vagy 
- Az Azure Active Directory Prémium P1 és az MCAS önálló.

Helyszíni alkalmazás:

- A helyszíni alkalmazásnak Kerberos korlátozott delegálást (KCD) kell használnia.

Alkalmazásproxy konfigurálása:

- Konfigurálja az Azure AD-t az alkalmazásproxy használatára, beleértve a környezet előkészítését és az alkalmazásproxy-összekötő telepítését. Az [azure-beli AD alkalmazásproxyn keresztül i aktív alkalmazások hozzáadása](application-proxy-add-on-premises-application.md)a helyszíni alkalmazások hozzáadása című témakörben található. 

## <a name="add-on-premises-application-to-azure-ad"></a>Helyszíni alkalmazás hozzáadása az Azure AD-hez

Adjon hozzá egy helyszíni alkalmazást az Azure AD-hez. Rövid útmutató: [Helyszíni alkalmazás hozzáadása az Azure AD-hez.](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad) Az alkalmazás hozzáadásakor a következő két beállítást kell megadnia a **Helyszíni alkalmazás hozzáadása** panelen:

- **Hitelesítés előtti :** Adja meg **az Azure Active Directory**t.
- **URL-címek fordítása az alkalmazás törzsében:** Válassza az **Igen**lehetőséget.

Erre a két beállításra van szükség ahhoz, hogy az alkalmazás működjön az MCAS-szal.

## <a name="test-the-on-premises-application"></a>A helyszíni alkalmazás tesztelése

Miután hozzáadja az alkalmazást az Azure AD-hez, az [alkalmazás tesztelése](application-proxy-add-on-premises-application.md#test-the-application) című szakaszban leírt lépésekkel hozzáadhat egy felhasználót tesztelésre, és tesztelheti a bejelentkezést. 

## <a name="deploy-conditional-access-app-control"></a>Feltételes hozzáférésű alkalmazásvezérlő telepítése

Az alkalmazás feltételes hozzáférés-alkalmazásvezérléssel való konfigurálásához kövesse az [Azure AD-alkalmazások feltételes hozzáférésű alkalmazásvezérlésének telepítésére](/cloud-app-security/proxy-deployment-aad)vonatkozó utasításokat.


## <a name="test-conditional-access-app-control"></a>Feltételes hozzáférési alkalmazásvezérlő tesztelése

Az Azure AD-alkalmazások feltételes hozzáférés-alkalmazásvezérléssel történő telepítésének teszteléséhez kövesse [az Azure AD-alkalmazások üzembe helyezésének tesztelése](/cloud-app-security/proxy-deployment-aad)című útmutató utasításait.





