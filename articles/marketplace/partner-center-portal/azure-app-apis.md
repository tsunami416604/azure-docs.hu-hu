---
title: Bevezetési API azure-alkalmazásokhoz a kereskedelmi piactéren
description: Az Azure-alkalmazások API-előfeltételei a Microsoft Partner Center kereskedelmi piacterén.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/10/2019
ms.openlocfilehash: cc4d56058ce3985ec3a1d9124ef4ec73ff6be1a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80279759"
---
# <a name="api-for-onboarding-azure-apps-in-partner-center"></a>API az Azure-alkalmazások partnerközpontban való bevezetéséhez

A *Partnerközpont beküldési API-jával* programozott módon lekérdezheti, elküldheti és közzéteheti az Azure-ajánlatokat.  Ez az API akkor hasznos, ha a fiók sok ajánlatot kezel, és automatizálni és optimalizálni szeretné az ajánlatok beküldési folyamatát.

## <a name="api-prerequisites"></a>API-előfeltételek

Van néhány programozott eszköz, amely az Azure-termékek partnerközpont API-jának használatához szükséges: 

- egy Azure Active Directory-alkalmazás.
- egy Azure Active Directory (Azure AD) hozzáférési jogkivonat.

### <a name="step-1-complete-prerequisites-for-using-the-partner-center-submission-api"></a>1. lépés: A Partnerközpont beküldési API-jának teljes előfeltételei

Mielőtt elkezdené írni a Partnerközpont beküldési API-jának hívásához szükséges kódot, győződjön meg arról, hogy teljesítette az alábbi előfeltételeket.

- Önnek (vagy a szervezetének) rendelkeznie kell egy Azure AD-könyvtárral, és [globális rendszergazdai](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) engedéllyel kell rendelkeznie a címtárhoz. Ha már használja az Office 365-öt vagy a Microsoft más üzleti szolgáltatásait, akkor már rendelkezik Azure AD-könyvtárral. Ellenkező esetben további díj nélkül [létrehozhat egy új Azure AD-t a Partnerközpontban.](https://docs.microsoft.com/windows/uwp/publish/associate-azure-ad-with-partner-center#create-a-brand-new-azure-ad-to-associate-with-your-partner-center-account)

- Egy [Azure AD-alkalmazást kell társítania a Partnerközpont-fiókkal,](https://docs.microsoft.com/windows/uwp/monetize/create-and-manage-submissions-using-windows-store-services#associate-an-azure-ad-application-with-your-windows-partner-center-account) és be kell szereznie a bérlői azonosítót, az ügyfélazonosítót és a kulcsot. Ezekre az értékekre az Azure AD-hozzáférési jogkivonat beszerzéséhez, amelyet a Microsoft Store beküldési API-hívásokhoz fog használni.

#### <a name="how-to-associate-an-azure-ad-application-with-your-partner-center-account"></a>Azure AD-alkalmazás társítása partnerközpont-fiókkal

A Microsoft Store beküldési API használatához hozzá kell rendelnie egy Azure AD-alkalmazást a Partnercenter-fiókhoz, le kell kérnie az alkalmazás bérlői azonosítóját és ügyfélazonosítóját, és létre kell hoznia egy kulcsot. Az Azure AD alkalmazás azt az alkalmazást vagy szolgáltatást jelöli, amelyből meg szeretné hívni a Partnerközpont beküldési API-ját. A bérlői azonosító, az ügyfél-azonosító és a kulcs az Azure AD-hozzáférési jogkivonat, amely átaz API-t kell beszereznie.

>[!Note]
>Ezt a feladatot csak egyszer kell elvégeznie. Miután rendelkezik a bérlői azonosítóval, az ügyfélazonosítóval és a kulccsal, bármikor újra felhasználhatja őket, amikor új Azure AD-hozzáférési jogkivonatlétrehozásához szüksége van.

1. A Partnerközpontban [társítsa a szervezet Partnerközpont-fiókját a szervezet Azure AD-könyvtárához.](https://docs.microsoft.com/windows/uwp/publish/associate-azure-ad-with-partner-center)
1. Ezután a **Partnerközpont** **Fiókbeállítások szakaszában** a Felhasználók lapon [adja hozzá az Azure AD-alkalmazást,](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#add-azure-ad-applications-to-your-partner-center-account) amely a Partnerközpont-fiók beküldött anyagainak eléréséhez használni fog. Győződjön meg arról, hogy az alkalmazáshoz a **Kezelő** szerepkört rendelte hozzá. Ha az alkalmazás még nem létezik az Azure AD-címtárban, [létrehozhat egy új Azure AD-alkalmazást a Partner Centerben.](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#create-a-new-azure-ad-application-account-in-your-organizations-directory-and-add-it-to-your-partner-center-account)
1. Térjen vissza a **Felhasználók** lapra, kattintson az Azure AD-alkalmazás nevére az alkalmazásbeállításainak megugrásához, és másolja lefelé a **bérlői azonosító** és az **ügyfélazonosító** értékeit.
1. Kattintson **az Új kulcs hozzáadása gombra.** A következő képernyőn másolja lefelé a **Kulcs** értéket. Az oldal elhagyása után nem fogja tudni újra elérni ezeket az adatokat. További információ: [Kulcsok kezelése egy Azure AD-alkalmazáshoz.](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#manage-keys)

### <a name="step-2-obtain-an-azure-ad-access-token"></a>2. lépés: Azure AD-hozzáférési jogkivonat beszerzése

Mielőtt meghívja a partnerközpont beküldési API-ban a metódusok bármelyikét, először be kell szereznie egy Azure AD-hozzáférési jogkivonatot, amelyet átkell adnia az API-ban lévő egyes metódusok **engedélyezési** fejlécének. Miután beszerezte a hozzáférési jogkivonatot, 60 perce van annak használatára, mielőtt lejár. A jogkivonat lejárta után frissítheti a jogkivonatot, így továbbra is használhatja azt az API jövőbeli hívásaiban.

A hozzáférési jogkivonat beszerzéséhez kövesse a [Service to Service Calls using Client Credentials (Szolgáltatás szolgáltatáshoz szolgáltatásra irányuló hívások ügyfélhitelesítő adatok használatával)](https://azure.microsoft.com/documentation/articles/active-directory-protocols-oauth-service-to-service/) című útmutató utasításait, `HTTP POST` `https://login.microsoftonline.com/<tenant_id>/oauth2/token` hogy küldjön egy a végpontra. Itt van egy minta kérés:

JSONCOPY
```Json
POST https://login.microsoftonline.com/<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded; charset=utf-8

grant_type=client_credentials
&client_id=<your_client_id>
&client_secret=<your_client_secret>
&resource= https://api.partner.microsoft.com
```

A *tenant_id* érték `POST URI` a és a *client_id* és *client_secret* paraméterek, adja meg a bérlői azonosító, ügyfél-azonosító és a kulcs az alkalmazás, amely beolvasni a Partner Center az előző szakaszban. Az *erőforrásparaméterhez* meg `https://api.partner.microsoft.com`kell adnia a paramétert.

### <a name="step-3-use-the-microsoft-store-submission-api"></a>3. lépés: A Microsoft Store beküldési API-jának használata

Miután rendelkezik egy Azure AD-hozzáférési jogkivonattal, metódusokat hívhat meg a Partnerközpont beküldési API-jában. A beküldött anyagok létrehozásához vagy frissítéséhez általában több metódust hív meg a Partnerközpont beküldési API-jában egy adott sorrendben. Az egyes forgatókönyvekről és az egyes módszerek szintaxisáról a Betöltési API-swagger című témakörben talál további információt.

https://apidocs.microsoft.com/services/partneringestion/
