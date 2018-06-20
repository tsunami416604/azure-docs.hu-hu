---
title: Kezelheti a fiók beállításait LUIS |} Microsoft Docs
description: LUIS webhely segítségével kezelheti a fiók beállításait.
titleSuffix: Azure
services: cognitive-services
author: v-geberr
manager: Kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/04/2018
ms.author: v-geberr
ms.openlocfilehash: 76300b307c8e87a3dd108fc53d3b30a53d3276dd
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36263924"
---
# <a name="manage-your-luis-account"></a>A LUIS fiók kezelése
A két kulcsfontosságú adatokat LUIS fiók olyan felhasználói fiók és a szerzői műveletekhez kulcs. A bejelentkezési adatok felügyelt [account.microsoft.com](https://account.microsoft.com). A szerzői műveletekhez kulcs felügyelete a [LUIS][LUIS] webhely **beállítások** lap. 

## <a name="authoring-key"></a>Szerzői kulcs

Az egyetlen, régióspecifikus szerzői a kulcs a **beállítások** lap lehetővé teszi, hogy az alkalmazások készíthet a [LUIS][LUIS] webhelyet, valamint a [ API-k szerzői](https://aka.ms/luis-authoring-api). A könnyebb a szerzői műveletekhez kulcs engedélyezett létrehozása egy [korlátozott](luis-boundaries.md) végpont száma havonta lekérdezi. 

![LUIS beállítások lap](./media/luis-how-to-account-settings/account-settings.png)

A szerzői műveletekhez kulccsal minden alkalmazás saját, valamint minden olyan alkalmazás, közreműködő jelennek meg.

## <a name="authoring-key-regions"></a>Szerzői kulcs régiók
A szerzői műveletekhez kulcs csak az a [régió szerzői](luis-reference-regions.md#publishing-regions). A kulcs nem működik egy másik régióban. 

## <a name="reset-authoring-key"></a>Szerzői kulcs visszaállítása
A szerzői műveletekhez kulcs biztonsága sérül, ha alaphelyzetbe állítja a kulcsot. A kulcs alaphelyzetbe áll, az alkalmazások a [LUIS] webhelyet. Ha szerzőként a szerzői műveletekhez API-k segítségével az alkalmazások, szeretné-e a értékének módosítása `Ocp-Apim-Subscription-Key` az új kulcshoz. 

## <a name="delete-account"></a>Fiók törlése
Lásd: [adattárolás és eltávolítási](luis-concept-data-storage.md#accounts) milyen adatok akkor törlődnek, ha a fiók törlése kapcsolatos információkat. 

## <a name="azure-active-directory-tenant-user"></a>Az Azure Active Directory-bérlő felhasználó
LUIS szabványos Azure Active Directory (Azure AD) hozzájárulási folyamat használja. 

A bérlői rendszergazda közvetlenül a felhasználókat, akiknek hozzáférést biztosít az Azure AD-ben LUIS használandó együtt kell működnie. 

A felhasználó először jelentkezik be LUIS, és látja a rendszergazdai jóváhagyást igénylő előugró párbeszédpanelen. A felhasználó felveszi a kapcsolatot a bérlői rendszergazda a folytatás előtt. 

A bérlői rendszergazda ezután LUIS bejelentkezik, és egy hozzájárulási folyamat felugró párbeszédpanel látja. Ez az a párbeszédpanelen, a rendszergazdának hozzá kell engedélyt a felhasználó számára. A rendszergazda elfogadja az engedélyt, ha a felhasználó nem tudja LUIS folytatásához.

A bérlői rendszergazda nem fog bejelentkezni LUIS, ha a rendszergazda hozzáférhet [hozzájárulás](https://account.activedirectory.windowsazure.com/Consent.aspx?ClientID=65920ba3-ab61-4a9b-9b10-505e5ce61b58) LUIS számára. 

Ha a bérlői rendszergazda csak bizonyos felhasználók LUIS használni fájlvédelemre, tekintse meg a [identitás blogja](https://blogs.technet.microsoft.com/tfg/2017/10/15/english-tips-to-manage-azure-ad-users-consent-to-applications-using-azure-ad-graph-api/).

### <a name="user-accounts-with-multiple-emails-for-collaborators"></a>Több e-maileket közreműködők rendelkező felhasználói fiókok
Közreműködők LUIS alkalmazásokhoz való hozzáadásakor meg a pontos e-mail címet, közreműködő, közreműködő LUIS használatára van szüksége. Azure Active Directory (Azure AD) lehetővé teszi, hogy egy felhasználó felcserélhetők egynél több e-mail fiókot, amíg LUIS a felhasználónak a közreműködő listában megadott e-mail cím bejelentkezni. 


## <a name="next-steps"></a>További lépések

További információ a [kulcs szerzői](luis-concept-keys.md#authoring-key). 

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions
